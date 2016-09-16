# Smart Oracles Specification

A specification for oracle services providers to designate what services they offer, with a complimentary way for services to be ordered by contract creators.

## Basic Workflow

An oracle service provider specifies an adapter, using the adapter schema. This schema is used to designate the needed inputs, and the corresponding outputs that will result based on the inputs.

An oracle consumer can then create an assignment request which conforms to the adapter schema, additionally specifying payment and request schedule.

Once the assignment is recieved by the oracle, they sign off on the work as part of the response to the assignment.

## Implementations

See [nayru](https://github.com/smartoracles/nayru) for a working implementation of an oracle which can have various adapters attached to it.

## Adapters
The adapter protocol allows for anyone running a Nayru instance to specify what they need to work with a service. Adapters can have as much or as little logic as is needed. There are two parts for all adapters: input and output.

### Input
There are three ways to trigger input adapters: scheduled updates, on-chain requests, and off-chain requests. All adapters support scheduled requests, and can additionally support either on-chain OR off-chain requets. The need for both on-chain and off-chain input together can often be solved by feeding one adapter into another. All adapters can also provide preset information.

#### On-Chain Input
Adapters can specify on-chain interfaces to allow for requests to be made on-chain. This can be useful for pulling off-chain information on-chain, or pushing information off-chain via on-chain logic.

Example 1: An Ethereum oracle can provide an interface to retrieve the current stock price of the stock ticker symbol provided in the request.

Example 2: A Bitcoin oracle can watch a certain address for an OP_RETURN message specifying a stock ticker symbol, and release a UTXO based on a preset price comparison.

#### Off-Chain Input
Adapters can specify off-chain interfaces, so that traditional resources like HTTP requests can affect on-chain resources.

Example 1: Push notifications can be translated to signed transactions on the blockchain.

Example 2: Configuration, like credentials, for an adapter can be reset as needed.


#### Preset Input
Preset information is set in the initial assignment for the oracle. Presets can affect later input and output requests, like with credentials, but is specified in the inputs section. Information used in scheduled requests will often be preset input.

Example 1: When interacting with a stock price adapter, you may always want the closing price of Apple stock. The preset information in this case would be the Apple stock ticker symbol, AAPL. By specifying this up front, a request does not need to be made everytime you need a price.

Example 2: When interacting with a private data feed, you may need to specify credentials. By passing these credentials in the assignment ahead of time, they are never exposed on chain.

### Output
The actions triggered by adapters is specified in the output section of an adapter. Output can take two forms: on-chain AND/OR off-chain.

#### On-Chain Output
Adapters can specify the effects that outcomes can have on blockchains, and the interfaces that the outcomes will be visible through.

Example 1: An Ethereum oracle is updated daily, and the address and method for data retrieval are specified.

Example 2: A Bitcoin payment is recorded on the Bitcoin blockchain following an update triggered on the Ethereum blockchain.

#### Off-Chain Ouput
Adapters can specify the effects that outcomes can have off of a blockchain, and the interfaces that the outcomes will be visible through.

Example 1: An HTTP notification is sent to a website after a payment is made.

Example 2: Payment via a traditional banking site is released via authentication with the bank and credentials stored with the adapter.


## TODO

- Improve Documentation: Better description, examples, clarify request sequence.
