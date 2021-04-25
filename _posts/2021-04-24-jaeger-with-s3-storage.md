---
layout: default
title:  "Jaeger with S3 storage backend"
date:   2021-04-21 14:03:00 +0800
---

Part of my work as a Site Reliablity Engineer at Setel is to collect information on the performance metrics of the services that i am my team manage.

One of it is to collect traces of the performance of the services and we use Jaeger as we move away from SaaS services that deemed expensive.

Jaeger was first developed by Uber and then opensourced and we had decided to use Jaeger since it allows us for distributed tracing across our Kubernetes Cluster and also it is supported by the Linux Foundation.

Despite that we realised that Jaeger has a dependency on ElasticSearch or Cassandra but going that route would be costly - not just as the cost of hosting these databases but also the operational costs that come with maintaining such services.

We have also looked at other options like running [Tempo](https://grafana.com/oss/tempo) that supports storing data on S3 but Tempo only allows searching only by TraceID and it requires additional infrastructure (Loki, Tempo) and we would like to go light on resources.

Since Jaeger supports "plugins" using Hashicorp's `go-plugin`, i and my team decided that i worked on to develop the plugin for S3 storage. It's available [here](https://github.com/muhammadn/jaeger-s3).

This allows us to keep our operational costs as low as possible and S3 object storage is really cheap for storing data for retention.
 
Under the hood, we leveraged on bolt-db shipper from loki's source code which stores the data (in boltdb format) in any object storage services (s3, gcs, azure blob storage) by also leveraging on cortex which is used by loki.

It's ready for production and we're shipping version 1.0 soon. So do check it out and test it out. (We've only tried using S3 which works but yet to try on GCS or Amazon Azure Storage).

You can also try configuring it using Amazon Dynamo or Google Bigtable (to store the indexes) which should theoretically work as we use Cortex to interface with the data storage backend.
