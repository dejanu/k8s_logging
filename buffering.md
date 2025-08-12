## Buffering

BUFFERING = A mechanism to place process data into a temporary location until is ready to be shipped.

CHUNK = group of events/records/array of json objects.

A chunk's size usually is around 2 MB [fluentbit chunk size](https://docs.fluentbit.io/manual/administration/buffering-and-storage)
128 chunks up in memory in total

Fluentbit collects, parses, filters and ships logs. A central piece of this workflow is the ability to do buffering.
When an input plugin source emits records -->  Fluentbit engine groups the records together in a chunk


Fluentd collects log lines and puts them in chunks. These chunks are then buffered before being sent to the output destination.
Fluentd buffers data in memory (by default) or on disk (file) before sending it to the output destination. 
Filesystem buffering helps with backpressure and overall memory control. Enable it using storage.type filesystem


The bufer size can be configured, and can be flushed to the output destination when it reaches a certain size or after a certain time period.
Check buffer config `kubectl -n logging exec -c fluentd $(kubectl -n logging get po -l app.kubernetes.io/instance=fluentd -oname) -- cat /etc/fluent/config.d/fluent.conf`

Buffering is done using buffer plugins. These buffers are configurable parameters [here](https://docs.fluentd.org/output#buffering-retrying-parameters):
Events are grouped into chunks and placed in a buffer, the defaults fluentd  config  (memory buffer):

    - chunk_limit_size: 8MB (size of chunks)
    - total_limit_size: 512MB (buffer size)
    - interval flushes per `flush_interval` (the time interval in which the buffer will be sent to the destination)
    - in prod envs, it’s recommended to use the file buffer type to prevent data losing upon fluentd restarts
