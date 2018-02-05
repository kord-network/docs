# META Service Marketplace

> An index of META Services available on the META Network

## GraphQL API

GraphQL APIs for reading and writing to a marketplace should be available on the
network at known HTTP endpoints - eg.
https://test.meta.network/marketplace/meta-id or
https://test.meta.network/marketplace/smart-content

#### Read
```
query {
  # read all services in a marketplace
  services() {
    id
  }

  # read services by id or other filters
  service(id: String, serviceType: String) {
    name
    type
  }
}
```

#### Write
```
mutation createMetaServiceMutation() {
  # add a service to a marketplace
  createMetaService(input: ServiceInput!) {
    id
  }
}

mutation removeMetaServiceMutation() {
  # remove a service from a marketplace
  removeMetaService(id: String!)
}
```

### Simple example
```
MetaService: {
  id: '0xabc...123',
  name: 'spotify-id-claims',
  type: 'IdentityClaim',
}
```

### Extended example
```
MetaService: {
  id: '0xabc...123',
  available: true,
  displayName: 'Spotify',
  displayImage: ''data:image/jpeg;base64,/9j/4AA.../9k=',
  endpoints: {
    development: 'http://localhost:7000',
    testnet: 'https://test.meta.network/claims/spotify',
    mainnet: 'https://meta.network/claims/spotify',
  },
  extraData: {
    property: 'spotify.id'
  },
  stakeContract: { ...? },
  name: 'spotify-id-claims',
  type: 'IdentityClaim',
  txPrice: '1.5',
  website: 'https://spotify.com'
}
```
