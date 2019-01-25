
.. _changes:

=======
Changes
=======

.. _release_3.0.0:

-------------
Version 3.0.0
-------------

*Release Data: 2019-01-18*

Introduction
------------

3.0.0 version is now officially released. It is backward compatible with MQTT 3 (3.1 & 3.1.1), and it also supports new features of MQTT 5.0 specification.

It also comes with some important features. Scalability and extensibility are improved significantly as well after refactoring some core components.

MQTT 5.0 Protocol specification support
---------------------------------------

- Session expiry

  Clean session flag in MQTT 3 is now split to a Clean Start Flag and a Session Expiry Interval.

- Message expiry

  Allow an expiry interval to be set when a message is published.

- Reason code on all ACKs

  All response packets contains a reason code. This allows the invoker to determine whether the requested function succeeded.

- Reason string on all ACKs

  Most packets with a reason code also allow an optional reason string.

- Server disconnect

  Allow DISCONNECT to be sent by the server.

- Request/Response

  Formalize the request/response pattern within MQTT and provide the Response Topic and Correlation Data properties to allow response messages to be routed back to the publisher of a request.

- Shared subscriptions

  EMQ X 2.x supports shared subscription on single-node as an unstandardized feature. Now in EMQ X 3.0, the shared subscription is cluster-wide.

- Subscription ID

  With a subscription ID the client is able to know from which subscription the message comes.

- Topic Alias

  Topic can have an integer alias, which reduces the communication overhead for the long topic names.

- User properties

  User properties can be added in most packets.

- Maximum Packer Size

  Allow the Client and Server to independently specify the maximum packet size they support. It is an error for the session partner to send a larger packet.

- Subscription options

  Provide subscription options primarily defined to allow for message bridge applications. These include an option to not send messages originating on this Client (noLocal), and options for handling retained messages on subscribe.

- Will Delay

  MQTT 5.0 allows to specify a delay between end of connection and sending of the will message，so it can avoid to send out the will message during temporary network problems.

- Server Keep Alive

  Allow the Server to specify the value it wishes the Client to use as a keep alive.

- Assign ClientID

  In MQTT 5.0, if ClientID is assigned by the server, then the server should return the assigned ClientID to client.

Evolved Clustering Architecture
-------------------------------

The clustering architecture is evolved. Now a single cluster is able to serve ten-millions of concurrent connections.

.. code-block:: properties

    ----------             ----------
    |  EMQX  |<--- MQTT--->|  EMQX  |
    |--------|             |--------|
    |  Ekka  |<----RPC---->|  Ekka  |
    |--------|             |--------|
    | Mnesia |<--Cluster-->| Mnesia |
    |--------|             |--------|
    | Kernel |<----TCP---->| Kernel |
    ----------             ----------

- Ekka is introduced to auto-cluster EMQ X, and to auto-heal the cluster after net-split, following clustering methods are now supported:

  - manual: nodes joining a cluster manually;

  - static: auto-clustering from a pre-defined node list;

  - mcast: auto-clustering using IP multicast;

  - dns: auto-clustering using DNS A-records;

  - etcd: auto-clustering using etcd;

  - k8s: auto-clustering using kubernetes.

- A scalable RPC is introduced to mitigate network congestion among nodes to reduce the risk of net-split.

Rate Limiting
-------------

The rate limiting is introduced to make the broker more resilient. User can configure MQTT TCP or SSL listener configuration.

- Concurrent connection numbers: max_clients

- Connection rate limitation: max_conn_rate

- Message delivery bytes limitation: rate_limit

- Message delivery number rate limitation: max_publish_rate

Other Feature improvements and bug fixs
---------------------------------------

- Upgraded esockd

- Switched to cowboy HTTP stack for higher HTTP connection performance

- Refactored the ACL caching mechanism

- Added local and remote MQTT bridge

- Introduced concept of "zone", that different zones can have different configurations

- Refactored session module, and reduced data copy among nodes, which led to higher inter-nodes communication efficiency

- Improved OpenLDAP Access Control

- Added delayed publish

- Supported new statistic and metrics to Prometheus

- Improved the hooks

- Supported storing messages with QoS 0

- Optimized server performance when clients are offline in batch

- Support for reloading plugins to import new configurations
