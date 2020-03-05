---
title: "Running Cortex in Production"
linkTitle: "Running Cortex in Production"
weight: 1
slug: running-in-production
---

This document builds on the [getting started guide](../getting_started.md) and specifies the steps needed to get Cortex into production.
Ensure you have completed all the steps in the getting started guide before you start this one.

## 1. Pick a storage backend

The getting started guide uses local chunk storage.
Local chunk storage is experimental and shouldn’t be used in production.
For production systems, it is recommended you use chunk storage with one of the following backend:

* DynamoDB/S3 (see [Cortex on AWS](./storage-aws.md))
* BigTable/GCS (TODO)
* Cassandra (see [Cortex on Cassandra](./storage-cassandra.md))

Cortex has an alternative to chunk storage: block storage.  Block storage is not ready for production usage at this time.

## 2. Deploy Query Frontend

The **Query Frontend** is the Cortex component which parallelizes the execution of and caches the results of queries.
The **Query Frontend** is also responsible for retries and multi-tenant QoS.

For the multi-tenant QoS algorithms to work, you should not run more than two **Query Frontends**.
The **Query Frontend** should be deployed behind a load balancer, and should only be sent queries -- writes should go straight to the Distributor component, or to the single-process Cortex.

The **Querier** component (or single-process Cortex) “pulls” queries from the queues in the **Query Frontend**.
**Queriers** discover the **Query Frontend** via DNS SRV records.
The **Queriers** should not use the load balancer to access the **Query Frontend**.
In Kubernetes, you should use a separate headless service.

To configure the **Queries** to use the **Query Frontend**, set the following flags:

```
  -querier.frontend-address string
    	Address of query frontend service.
```

The **Query Frontend** can run using an in-process cache, but should be configured with an external Memcached for production workloads.
The next section has more details.

## 3. Setup Caching

Correctly configured caching is important for a production-ready Cortex cluster.
Cortex has many opportunities for using caching to accelerate queries and reduce cost.

For more information, see the [Caching in Cortex documentation.](./caching.md)

## 4. Monitoring and Alerting

Cortex exports metrics in the Prometheus format.
We recommend you install and configure Prometheus server to monitor your Cortex cluster.

We publish a set of Prometheus alerts and Grafana dashboards as the [cortex-mixin](https://github.com/grafana/cortex-jsonnet).
We recommend you use these for any production Cortex cluster.

## 5. Write Ahead Log

Cortex uses a Write Ahead Log (WAL) to ensure data that is written to Cortex and buffered in memory is not lost should Cortex restart before it has the chance to flush.

For more information on configuring the WAL, see [Ingesters with WAL](ingesters-with-wal.md).

## 6. Authentication & Multitenancy

If you want to run Cortex as a multi-tenant system, you need to give each
tenant a unique ID - this can be any string.
Managing tenants and allocating IDs must be done outside of Cortex.
See [Authentication and Authorisation](auth.md) for more information.

## 7. Handling HA Prometheus Pairs

ha-pair-handling.md

## 8. Scalable Rules & Alerts.

TODO
