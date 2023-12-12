

## Requirements

Webshop implementation consists of three services. Each service plays its own role in the system. All services interoperate with each other via synchronous and asynchronous messaging. Synchronous messages are idempotent and simply read data from other services. Asynchronous messages are not idempotent and cause state change in the system when the messages are handled.

The system's global state is persisted to two services' databases. Because the global state is in continuous transition, the state is also somehow distributed to clients, and partly, the state is in an intermediate state when messages are still on the wire. 

### Consensus

System do not have central controller or leader and therefore system do not need election in service failures.


## Repositories

* [Public Front](https://github.com/bendah123/Public_Front)
* [Inventory Service](https://github.com/kallepaa/ds-inventory)
* [Order Service](https://github.com/Kanzaaaa/ORDER-DS)