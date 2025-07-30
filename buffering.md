## Buffering

Fluentd collects log lines and puts them in chunks. These chunks are then buffered before being sent to the output destination.

Fluentd buffers data in memory (by default) or on disk (file) before sending it to the output destination. The bufer size can be configured, and can be flushed to the output destination when it reaches a certain size or after a certain time period. 

Buffering is done using buffer plugins. These buffers are configurable parameters [here](https://docs.fluentd.org/output#buffering-retrying-parameters):

    - chunk has a maximum size (configured by `chunk_limit_size`)
    - interval flushes per `flush_interval` (the time interval in which the buffer will be sent to the destination)
    - in prod envs, it’s recommended to use the file buffer type to prevent data losing upon fluentd restarts