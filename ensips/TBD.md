---

description: Enhances ENSIP-16 with improved GraphQL schema for off-chain ENS metadata retrieval
contributors:

- @pikonha
- @alextnetto

ensip: TBD
status: draft
created: 2024-11-04

---

# Enhanced Off-chain Domain Metadata Schema

## Abstract

This ENSIP proposes improvements to the ENSIP-16 GraphQL schema for retrieving metadata from off-chain ENS domains. The enhancements provide a more efficient and comprehensive structure for querying domain information, including text records, address records, subdomain information, and ownership details.

## Motivation

While ENSIP-16 established a foundation for off-chain metadata retrieval, implementation experience has revealed opportunities for optimization. This proposal addresses these opportunities by introducing dedicated types for text and address records, improving subdomain handling, and streamlining data retrieval to reduce query complexity.

## Specification

### Schema Structure

The enhanced GraphQL schema defines the following types:

```graphql
type Domain {
    id: ID!
    context: Bytes
    owner: Bytes
    name: String
    node: Bytes
    label: String
    labelhash: Bytes
    resolvedAddress: Bytes
    parent: String
    parentNode: Bytes
    subdomains: [Domain!]
    subdomainCount: Int!
    resolver: Resolver!
    expiryDate: BigInt!
    registerDate: BigInt
}

type Text {
    key: String
    value: String
}

type Address {
    address: Bytes
    coin: BigInt
}

type Resolver {
    id: ID!
    node: Bytes
    context: Bytes
    address: Bytes
    addr: Bytes
    contentHash: Bytes
    texts: [Text!]
    addresses: [Address!]
}
```

### Key Improvements

1. **Enhanced Domain Type**
    - Includes comprehensive metadata fields like `context`, `owner`, and `registerDate`
    - Maintains both `parent` and `parentNode` for flexible hierarchy navigation
2. **Text and Address Types**
    - Dedicated types for managing text and address records
    - Simplified structure with key-value pairs for text records to avoid the N+1 problem on the client-side
    - Standardized address representation with coin type support

### Example Query

```graphql
query Domain($name: String!) {
  domain(name: $name) {
    id
    context
    owner
    name
    node
    label
    labelhash
    resolvedAddress
    parent
    parentNode
    subdomains {
      id
      context
      owner
      name
      node
      label
      parent
      resolvedAddress
      resolver {
        address
        texts {
          key
          value
        }
        addresses {
          address
          coin
        }
      }
      expiryDate
      registerDate
    }
    subdomainCount
    resolver {
      id
      node
      context
      address
      addr
      contentHash
      texts {
        key
        value
      }
      addresses {
        address
        coin
      }
    }
    expiryDate
    registerDate
  }
}
```

## Backwards Compatibility

This specification introduces breaking changes to the ENSIP-16 GraphQL schema.
Implementations will need to update their GraphQL resolvers and clients to support the new schema structure.

## Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
