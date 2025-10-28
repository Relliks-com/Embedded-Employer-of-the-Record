
# C4 Level 4: Code Diagram

## Saga Orchestrator Implementation (TypeScript)
```typescript
enum SagaState {
  INITIATED = 'INITIATED',
  PENDING_REMOTE_CREATE = 'PENDING_REMOTE_CREATE',
  REMOTE_CREATE_SUCCESS = 'REMOTE_CREATE_SUCCESS',
  REMOTE_CREATE_FAILED = 'REMOTE_CREATE_FAILED',
  COMPENSATING = 'COMPENSATING',
  COMPLETED = 'COMPLETED',
  FAILED = 'FAILED'
}

interface SagaContext {
  sagaId: string;
  employmentId: string;
  employmentData: CreateEmploymentDTO;
  remoteId?: string;
  attempts: number;
  errors: string[];
  startedAt: Date;
}

class SagaOrchestrator {
  constructor(
    private readonly employmentRepo: EmploymentRepository,
    private readonly sagaStateRepo: SagaStateRepository,
    private readonly remoteClient: RemoteAPIClient,
    private readonly stateMachine: StateMachineEngine,
    private readonly logger: Logger
  ) {}

  async execute(definition: SagaDefinition): Promise<SagaResult> {
    const context: SagaContext = {
      sagaId: generateUUID(),
      employmentId: generateUUID(),
      employmentData: definition.data,
      attempts: 0,
      errors: [],
      startedAt: new Date()
    };

    try {
      await this.executeStep({
        name: 'create_local_employment',
        execute: async (ctx) => {
          await this.employmentRepo.create({
            id: ctx.employmentId,
            status: EmploymentStatus.PENDING_REMOTE_CREATE,
            ...ctx.employmentData
          });
        },
        compensate: async (ctx) => {
          await this.employmentRepo.softDelete(ctx.employmentId);
        }
      }, context);

      await this.executeStep({
        name: 'create_remote_employment',
        execute: async (ctx) => {
          const idempotencyKey = this.generateIdempotencyKey(ctx);
          const remoteEmployment = await this.remoteClient.createEmployment(
            ctx.employmentData,
            { idempotencyKey }
          );
          ctx.remoteId = remoteEmployment.id;
        },
        compensate: async (ctx) => {
          if (ctx.remoteId) {
            await this.remoteClient.deleteEmployment(ctx.remoteId);
          }
        }
      }, context);

      await this.executeStep({
        name: 'link_remote_id',
        execute: async (ctx) => {
          await this.employmentRepo.update(ctx.employmentId, {
            remoteEmploymentId: ctx.remoteId,
            status: EmploymentStatus.PENDING_EMPLOYEE_DETAILS
          });
        }
      }, context);

      await this.sagaStateRepo.update(context.sagaId, {
        state: SagaState.COMPLETED
      });

      return { success: true, employmentId: context.employmentId };

    } catch (error) {
      this.logger.error('Saga failed', { sagaId: context.sagaId, error });
      await this.compensate(context);
      return { success: false, error: error.message, sagaId: context.sagaId };
    }
  }
}
