# Smart Oracles Specification

A specification for oracle providers to programatically describe the services they offer. In addition to describing the services available, this spec can be used by a consumer of oracle services to request work from an oracle provider.

At a high level oracles are issued assignments by consumers. Assignments are made up of a series of one more sequential subtasks. Subtasks are jobs gien to adapters, and an adapter's format is specified by the oracle using an the [adapter schema](https://github.com/smartoracles/spec/blob/master/schemas/adapter_schema.json). Subtasks can encapsulate as much work as necessary, but is generally more reusable if common jobs can be be broken out into small reusable adapters.

An oracle lists the adapters it has available to use, then consumers of the oracle's services can compose subtasks as needed. In addition to requiring data inputs, oracles may also require payments which can be specified along with currency type.

## Basic Workflow

An oracle specifies an adapter with a schema. This adapter schema is used to designate the required inputs, and describe the corresponding outputs that will result based on the inputs.

A consumer can then give an assignment to an oracle which conforms to the adapter schema, additionally specifying payment and request schedule.

Once the assignment is recieved by the oracle, if they accept the assignment they sign the hash of the assignment body(which includes the address the address and amount the oracle at which the is to be paid). Upon receiving a signature back from the oracle, the consumer pays the oracle.

## Implementations

See [ChainLink](https://github.com/oraclekit/chainlink) for a working implementation of an oracle with various adapters attached to it.

## Adapters
The adapter protocol allows for anyone running a smart oracle instance to specify what they need to provide a service/integration. Adapters can have as much or as little logic as is needed. There are two parts for all adapters: input and output.

### Input
There are two ways to trigger input adapters:
- scheduled updates
- on demand requests

Scheduled updates are set at the creation of an assignment. As each assignment has a set deadline, the number of updates can be determined up front, and the cost of all scheduled updates is paid with the creation of the assignment. Additionally, any upfront costs of the assignment can be covered at the time the asssignment is created.

On demand requests are also supported, but if payment is required for this it must be paid with the request, not up front. Any adapter may trigger an update to the entire assignment, but it triggers an update starting at the earliest subtask and follows a full progression through all subtasks of the associated assignment.

### Output

Each time an assignment is updated it starts with the first subtask. The result of each subtask is fed into the next subtask, until the final subtask determines the final result of each assignment update.


## TODO

- Improve Documentation: Better description, examples, clarify request sequence.
