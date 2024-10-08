---
name: Asset and Relation Management Protocol
shortname: 26/ARMP
status: raw
editor: Andrew Pasika <apasika@kaaiot.io>
---


<!-- toc -->


# Introduction

Asset and Relation Management Protocol (ARMP) is designed for managing asset and their relation between Kaa services.

ARMP complies with the [Inter-Service Messaging](/0003/README.md) guidelines.


# Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

The following terms and definitions are used in this RFC.

- **Asset repository (repository)**: any service that exposes the ARMP interface to other services for managing assets.

- **Asset client (client)**: any service that uses exposed ARMP interface.


# Design

## Getting relation

### Relation get request

*Relation get request* message is a [targeted message](/0003/README.md#targeted-messaging) that the client sends to the repository to retrieve relation.

The client MUST send relation get request messages using the following NATS subject:
```
kaa.v1.service.{repository-service-instance-name}.armp.relation-get-request
```
where:
- `{repository-service-instance-name}` is the asset repository service instance name.

Relation get request payload MUST be an [Avro-encoded](https://avro.apache.org/) object with the following schema ([0026-relation-get-request.avsc](./0026-relation-get-request.avsc)):

```json
{
  "namespace": "org.kaaproject.ipc.armp.gen.v1",
  "name": "RelationGetRequest",
  "type": "record",
  "doc": "Interservice relation get request",
  "fields": [
    {
      "name": "correlationId",
      "type": "string",
      "doc": "Message ID primarily used to track message processing across services"
    },
    {
      "name": "timestamp",
      "type": "long",
      "doc": "Message creation UNIX timestamp in milliseconds"
    },
    {
      "name": "timeout",
      "type": "long",
      "default": 0,
      "doc": "Amount of milliseconds since the timestamp until the message expires. Value of 0 is reserved to indicate no expiration."
    },
    {
      "name": "tenantId",
      "type": "string",
      "doc": "Tenant ID"
    },
    {
      "name": "entityType",
      "type": "string",
      "doc": "Entity type"
    },
    {
      "name": "entityId",
      "type": "string",
      "doc": "Entity ID"
    },
    {
      "name": "relationType",
      "type": ["null", "string"],
      "doc": "Relation type. Examples: CONTAINS, IS_CONTAINED_BY, MANAGES, IS_MANAGED_BY, etc.",
      "default": null
    }
  ]
}
```


### Relation get response

*Relation get response* message MUST be sent by the repository in response to [Relation get request message](#relation-get-request).
Repository MUST publish response message to the subject provided in the NATS `replyTo` field of the request.

Relation get response message payload MUST be an Avro-encoded object with the following schema ([0026-relation-get-response.avsc](./0026-relation-get-response.avsc)):

```json
{
  "namespace": "org.kaaproject.ipc.armp.gen.v1",
  "name": "RelationGetResponse",
  "type": "record",
  "doc": "Interservice relation get request",
  "fields": [
    {
      "name": "correlationId",
      "type": "string",
      "doc": "Message ID primarily used to track message processing across services"
    },
    {
      "name": "timestamp",
      "type": "long",
      "doc": "Message creation UNIX timestamp in milliseconds"
    },
    {
      "name": "timeout",
      "type": "long",
      "default": 0,
      "doc": "Amount of milliseconds since the timestamp until the message expires. Value of 0 is reserved to indicate no expiration."
    },
    {
      "name": "statusCode",
      "type": "int",
      "doc": "HTTP status code of the request processing"
    },
    {
      "name": "reasonPhrase",
      "type": [
        "null",
        "string"
      ],
      "default": null,
      "doc": "Human-readable status reason phrase"
    },
    {
      "name": "relations",
      "doc": "Array of relations",
      "type": {
        "type": "array",
        "items": {
          "type": "org.kaaproject.ipc.armp.gen.v1.Relation"
        }
      },
      "default": []
    }
  ]
}
```


### Relation tree get request

*Relation tree get request* message is a [targeted message](/0003/README.md#targeted-messaging) that the client sends to the repository to retrieve relation tree.

The client MUST send relation get request messages using the following NATS subject:
```
kaa.v1.service.{repository-service-instance-name}.armp.relation-tree-get-request
```
where:
- `{repository-service-instance-name}` is the asset repository service instance name.

Relation tree get request payload MUST be an [Avro-encoded](https://avro.apache.org/) object with the following schema ([0026-relation-tree-get-request.avsc](./0026-relation-tree-get-request.avsc)):

```json
{
  "namespace": "org.kaaproject.ipc.armp.gen.v1",
  "name": "RelationTreeGetRequest",
  "type": "record",
  "doc": "Interservice relation get request",
  "fields": [
    {
      "name": "correlationId",
      "type": "string",
      "doc": "Message ID primarily used to track message processing across services"
    },
    {
      "name": "timestamp",
      "type": "long",
      "doc": "Message creation UNIX timestamp in milliseconds"
    },
    {
      "name": "timeout",
      "type": "long",
      "default": 0,
      "doc": "Amount of milliseconds since the timestamp until the message expires. Value of 0 is reserved to indicate no expiration."
    },
    {
      "name": "tenantId",
      "type": "string",
      "doc": "Tenant ID"
    },
    {
      "name": "entityType",
      "type": "string",
      "doc": "Entity type"
    },
    {
      "name": "entityId",
      "type": "string",
      "doc": "Entity ID"
    }
  ]
}
```


### Relation tree get response

*Relation tree get response* message MUST be sent by the repository in response to [Relation tree get request message](#relation-tree-get-request).
Repository MUST publish response message to the subject provided in the NATS `replyTo` field of the request.

Relation tree get response message payload MUST be an Avro-encoded object with the following schema ([0026-relation-tree-get-response.avsc](./0026-relation-tree-get-response.avsc)):

```json
{
  "namespace": "org.kaaproject.ipc.armp.gen.v1",
  "name": "RelationTreeGetResponse",
  "type": "record",
  "doc": "Interservice relation get request",
  "fields": [
    {
      "name": "correlationId",
      "type": "string",
      "doc": "Message ID primarily used to track message processing across services"
    },
    {
      "name": "timestamp",
      "type": "long",
      "doc": "Message creation UNIX timestamp in milliseconds"
    },
    {
      "name": "timeout",
      "type": "long",
      "default": 0,
      "doc": "Amount of milliseconds since the timestamp until the message expires. Value of 0 is reserved to indicate no expiration."
    },
    {
      "name": "statusCode",
      "type": "int",
      "doc": "HTTP status code of the request processing"
    },
    {
      "name": "reasonPhrase",
      "type": [
        "null",
        "string"
      ],
      "default": null,
      "doc": "Human-readable status reason phrase"
    },
    {
      "name": "relationTree",
      "type": [
        "null",
        "string"
      ],
      "default": null,
      "doc": "Representation of relation tree in JSON format. Empty JSON object in case no tree exists"
    }
  ]
}
```


## Entity relation tree updated event

Repository MUST publish an entity relation tree updated event on any changes to entity's relation tree.
The `{target-entity-type}` is `entity`
The `{event-group}` is `relation-tree`.
The `{event-type}` is `updated`.

Repositories MUST publish events to the following NATS subjects:

```
kaa.v1.events.{repository-service-instance-name}.entity.relation-tree.updated
```
where `{repository-service-instance-name}` is the name of the repository service instance.
It allows listeners to subscribe to events from a specific repository.

The NATS message payload is an Avro object with the following schema ([0026-relation-tree-updated.avsc](./0026-relation-tree-updated.avsc)):
```json
{
  "namespace": "org.kaaproject.ipc.armp.gen.v1",
  "name": "RelationTreeUpdated",
  "type": "record",
  "doc": "Relation tree updated event",
  "fields": [
    {
      "name": "correlationId",
      "type": "string",
      "doc": "Message ID primarily used to track message processing across services"
    },
    {
      "name": "timestamp",
      "type": "long",
      "doc": "Message creation UNIX timestamp in milliseconds"
    },
    {
      "name": "timeout",
      "type": "long",
      "default": 0,
      "doc": "Amount of milliseconds since the timestamp until the message expires. Value of 0 is reserved to indicate no expiration."
    },
    {
      "name": "relationTree",
      "type": "string",
      "doc": "Representation of relation tree in JSON format. Empty JSON object in case no tree exists"
    }
  ]
}
```
