# Smart Oracles Specification

A specification for oracles to programatically describe the services they offer. In addition to describing the services available, this spec can be used by a consumer of oracle services to request work from an oracle provider.

At a high level oracles are issued Assignments by consumers. An oracle lists the types of services it offers, along with the upfront configuration details it needs to run and prices corresponding to each service. Assignments are structured in a such a way as to be composable, so consumers can build new tools with them. Consumers of the oracle's services can send in requests for services, either on demand or scheduled in the future.

Once an assignment is recieved by an oracle, the oracle signs the hash of the assignment body if they accept the assignment. Upon receiving a signature back from the oracle, the consumer pays the oracle. Once paid, the oracle begins performing the assignment.

## Assignments and Adapters

Assignments are the core of the system, they are composed of one or more sequential Subtasks, a schedule, a fee specification, and optional name and description. Subtasks are the bulk of the Assignment, as they are work specifications for distinct job types. An oracle delegates each of its Subtasks to one of its Adapters which is specialized to handle such a Subtask.

Adapters are services an oracle runs to expand the functionality they offer. An Adapter's Subtask requirements are specified by the oracle using the [adapter schema](https://github.com/smartoracles/spec/blob/master/schemas/adapter_schema.json). Subtasks can encapsulate as much work as necessary, but they tend to be more flexible and reusable when common jobs are broken out into small Adapters. By following the Adapter specification and protocol, anyone running an oracle provider can clearly specify their offered services and requirements. Adapters can have as much or as little logic as is needed. There are two parts for all adapters: input and output.

### Input

- Configuration: Adapters optionally take up front configuration for how the work is to be done, specified at the time of Assignment creation; e.g. the URL and path to parse data from. The configuration is checked against the adapter's `configuration` schema. The configuration is commonly used for Scheduled Updates, but can also be used for On Demand Updates, even as fallback `data` parameters.
- Snapshot Data: Data that is passed into an Adapter from the result of the previous subtask. This data is checked against the Adapter's `data` schema, which allows for Adapters to be chained together, and incompatibilities between an Adapter's output and the next Adapter's required input format to be detected at the time of Assignment creation.

### Output

Each time an assignment is updated it starts with the first subtask. The result of each subtask is fed into the next subtask, until the final subtask determines the final result of each assignment update. It is often helpful to make adapters that pass through the arguments they are given if the parameters do not conform to the expected format. The result of the final adapter to be updated for each snapshot is the result that is passed back for the snapshot.

## Triggering Assignments

There are two ways to trigger input adapters: scheduled or on demand.

### Scheduled Updates

Scheduled updates are set at the creation of an assignment. As each assignment has a set deadline, the number of updates can be determined up front, and the cost of all scheduled updates is paid with the creation of the assignment. Additionally, any upfront costs of the assignment can be covered at the time the asssignment is created.

### On Demand Updates

On demand requests are also supported, but if payment is required for this it must be paid with the request, not up front. Any adapter may trigger an update to the entire assignment, but it triggers an update starting at the earliest subtask and follows a full progression through all subtasks of the associated assignment. To accomodate for this, adapters often need to be stateful, storing the data that triggered their snapshot and using it when it is their turn in the pipeline.

In addition to charging per request for on demand updates, which may never materialize, oracles may choose to charge a service availability fee simliar to an SLA. The basic outline of this agreement is that the requester deposits the total fee into a contract where it can be retrieved in a pro-rated fashion by the oracle; if the oracle fails to respond to requests, the deposit is returned to the requester.

## Implementations

See [ChainLink](https://github.com/oraclekit/chainlink) and its [documentation](https://chainlink-docs.smartcontract.com) for a working implementation of an oracle with various adapters attached to it.

## Future Directions

- Allow for adapters to pull data from subtasks other than the immediate predecessor
- Allow for subtask pipeline to fan out and in to allow for subtasks to rely on multiple processes
- Create a DSL to make specifications simpler and more composable
