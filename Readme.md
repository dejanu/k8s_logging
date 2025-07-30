# Logging chain

```
LOG COLLECTOR --> FluentBit (ds)
LOG AGGREATOR/PROCESSOR --> Fluentd (deployment/sts)
LOG STORAGE/SEARCH --> OpenSearch (sts)
```

## Motivation

Treasure Data: FluentBit is to FluentD what Beats are to Logstash...Charts [here](https://github.com/fluent/helm-charts)

* FluentBit: lightweight forwarder (450KB) translating into `memory: 128Mi` and `cpu: 100m` with a variety of input plugins. Uses [tail input plugin](https://docs.fluentbit.io/manual/data-pipeline/inputs/tail) to tail `/var/log/containers/*.log` and forwards to fluentd using [forward output plugin](https://docs.fluentbit.io/manual/data-pipeline/outputs/forward)...stream data to fluentd.
Fluentbit it uses parsers to make sense of raw log files,filters to enrich logs and outputs

```yaml
[INPUT]
  Name    tail
  Path    /var/log/myapp.log
  Tag     myapp.log
  Parser  json

[FILTER]
  Name    modify
  Match   myapp.log
  Add     hostname ${HOSTNAME}

[OUTPUT]
  Name    es
  Match   myapp.log
  Host    elasticsearch.local
  Port    9200
  Index   myapp-logs
```

* Fluentd: aggregator (40MB) translating into into `memory: 128Mi` and `cpu: 10m`. Does buffering (in-memory or file based), retries, routing, filtering.

* OpenSearch: storage and search engine

## Quick install

```bash
helm upgrade -i fluentbit charts/fluent-bit
helm upgrade -i fluentd charts/fluentd -f fluentd-values.yaml
helm upgrade -i opensearch charts/opensearch

# check opensearch cluster
kubectl port-forward svc/opensearch-cluster-master 9200:9200
```

## Notes

* A word about [buffering](https://github.com/dejanu/k8s_logging/blob/main/buffering.md) 
