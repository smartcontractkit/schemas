# Smart Oracles Specification

A specification for oracle services providers to designate what services they offer, with a complimentary way for services to be ordered by contract creators.

## Basic Workflow

An oracle service provider specifies an adapter, using the adapter schema. This schema is used to set the needed inputs, and the corresponding outputs that will result based on the inputs.

A contract creator creates an assignment for the oracle, which conforms to the adapter schema, additionally specifying payment and request schedule.

Once the assignment is recieved by the oracle, they sign off on the work as part of the response to the assignment.

## Examples

See [nayru](https://github.com/smartoracles/nayru) for a working implementation of an oracle which can have various adapters attached to it.

## TODO

- Clarify specifaction: Better description, examples, clarify request sequence.
