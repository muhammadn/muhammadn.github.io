---
layout: default
title:  "Jaeger S3 without the need for Tempo/Loki"
date:   2021-11-11 14:03:00 +0800
---

Before working on [Jaeger-S3](https://github.com/muhammadn/jaeger-s3), not only was i looking for storing Jaeger tracing on S3 but also a way to view my traces better but i cannot find something that is not operationally expensive and expensive on renting the infrastructure so S3 was the way to go and scalable (Jaeger-S3 now supports Google GCS and Azure BlobStorage).

I had looked in Tempo/Loki but going this path would make me have more infrastructure requirements, that means THREE services: loki,tempo and grafana and finding the traces are pretty [manual](https://grafana.com/go/webinar/getting-started-with-tracing-and-grafana-tempo/). Tempo is a product that achieves to support all of the opensource Tracing Platforms (including OpenTracing) but Jaeger-S3 is focused on Jaeger only.

Eversince Jaeger-S3 was released, i had been speeding up with optimizations and even loki data format compatibility (will be a breaking change in Jaeger-S3 v2 but this will take some time) if you guys want to actually use loki to read Jaeger-S3 generated data. But you don't need to upgrade if it available to stay on v1 if loki is not really important.

With Jaeger-S3, you don't need loki or tempo and grafana is optional when Jaeger-UI having HTTP "basic-auth" is not really an option.

Personally i had been using Grafana with Jaeger (and Jaeger-S3) and i just need to focus on Grafana to check the traces. Again, No tempo, no loki. Only Jaeger and Grafana.
