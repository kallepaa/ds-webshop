
## ABSTRACT

One of the requirements for the project was to have at least three nodes that exchange information between each other via messaging. Also, the system's global state shall be distributed to different nodes.

## INTRODUCTION

![Figure 1](communication-req.png)
**Figure 1** The system consists of 3 nodes: public front, inventory, and order. Each node exchanges information with each other synchronously and asynchronously.

Webshop implementation consists of three services. Each service plays its own role in the system. All services interoperate with each other via synchronous and asynchronous messaging. Synchronous messages are idempotent and simply read data from other services. Asynchronous messages are not idempotent and cause state change in the system.

The system's global state is persisted in the databases of the Inventory and the Order services. Because the global state is in continuous transition, the state is also distributed to clients, and partly, the state is in an intermediate state when messages are still on the wire.

### Services and roles

**Front service**

The Public Front role is to hide underlying services from client applications. The service is stateless and only routes messages from clients to services directly or via the message bus.

**Backend services**

The Inventory service keeps track of product properties, prices, and availabilities in the inventory. Additionally, it also keeps track of reserved products for the orders. The Order service keeps track of orders and their statuses. Both services are stateless, but they store information to own databases.

### Use cases

The **Figure 2** describes in more detail the different use cases supported by the system. The use cases 'Get Catalog' and 'Get Order' are idempotent and are executed synchronously. The 'Send Order' and 'Cancel Order' use cases change the system state. Messaging between nodes is defined in **Figure 2**. All interactions between nodes are initiated when a client executes one of the use cases. 

**Idempotent use cases**

When the client executes the use case 'Get Inventory', it calls the Public Front service, which then routes the request to the Inventory service. The 'Get Order Status' call also first goes to the Public Front service, which then routes the request to the Order service. 

**Non-idempotent use cases**

The 'Send Order' and 'Cancel Order' use cases also go through the Public Front service, but the Public Front routes the message to the Message Bus and returns the response to the client.

In the case of the 'Send Order' use case, both the Inventory and the Order services subscribe to the 'Send Order' topic defined by the Public Front. The Order service simply initializes the order in its own database and sets the order state as 'undefined.' Simultaneously, the Inventory service processes the given order and attempts to deduct ordered products from the inventory. If all the ordered products are found in the inventory, the Inventory service publishes the message 'In-Stock' to the message bus. The Order service subscribes to the 'In-Stock' topic defined by the Inventory and updates the order state as 'successful.'

The 'Cancel Order' use case works in a similar way as 'Send Order', but only the Order service subscribes to the 'Cancel Order' message published by the Public Front. After the Order service has processed the cancellation routines, the Order service publishes the message 'Order Canceled.' The Inventory service subscribes to these messages and returns the reserved products for the order back to the inventory.

Because messaging between the client and backend services is asynchronous, the client should poll the order status to get confirmation about operation result.
 
![Architecture](architecture.png)
**Figure 2** System architecture, use cases, and interoperation between nodes synchronously or asynchronously. Solid arrows represent synchronous message exchanges, and dashed arrows represent asynchronous. The figure also has defined server names used in the system demonstration.


### Syncronous messaging 


###



## Repositories

* [Public Front](https://github.com/bendah123/Public_Front)
* [Inventory Service](https://github.com/kallepaa/ds-inventory)
* [Order Service](https://github.com/Kanzaaaa/ORDER-DS)