---
title: memory
type: buffer
status: stable
categories: ["Utility"]
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the contents of:
     lib/buffer/memory.go
-->

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';


Stores consumed messages in memory and acknowledges them at the input level.
During shutdown Benthos will make a best attempt at flushing all remaining
messages before exiting cleanly.


<Tabs defaultValue="common" values={[
  { label: 'Common', value: 'common', },
  { label: 'Advanced', value: 'advanced', },
]}>

<TabItem value="common">

```yaml
# Common config fields, showing default values
buffer:
  memory:
    limit: 524288000
    batch_policy:
      enabled: false
      count: 0
      byte_size: 0
      period: ""
      check: ""
```

</TabItem>
<TabItem value="advanced">

```yaml
# All config fields, showing default values
buffer:
  memory:
    limit: 524288000
    batch_policy:
      enabled: false
      count: 0
      byte_size: 0
      period: ""
      check: ""
      processors: []
```

</TabItem>
</Tabs>

This buffer is appropriate when consuming messages from inputs that do not
gracefully handle back pressure and where delivery guarantees aren't critical.

This buffer has a configurable limit, where consumption will be stopped with
back pressure upstream if the total size of messages in the buffer reaches this
amount. Since this calculation is only an estimate, and the real size of
messages in RAM is always higher, it is recommended to set the limit
significantly below the amount of RAM available.

## Delivery Guarantees

This buffer intentionally weakens the delivery guarantees of the pipeline and
therefore should never be used in places where data loss is unacceptable.

## Batching

It is possible to batch up messages sent from this buffer using a
[batch policy](/docs/configuration/batching#batch-policy).

## Fields

### `limit`

The maximum buffer size (in bytes) to allow before applying backpressure upstream.


Type: `int`  
Default: `524288000`  

### `batch_policy`

Optionally configure a policy to flush buffered messages in batches.


Type: `object`  

### `batch_policy.enabled`

Whether to batch messages as they are flushed.


Type: `bool`  
Default: `false`  

### `batch_policy.count`

A number of messages at which the batch should be flushed. If `0` disables count based batching.


Type: `int`  
Default: `0`  

### `batch_policy.byte_size`

An amount of bytes at which the batch should be flushed. If `0` disables size based batching.


Type: `int`  
Default: `0`  

### `batch_policy.period`

A period in which an incomplete batch should be flushed regardless of its size.


Type: `string`  
Default: `""`  

```yaml
# Examples

period: 1s

period: 1m

period: 500ms
```

### `batch_policy.check`

A [Bloblang query](/docs/guides/bloblang/about/) that should return a boolean value indicating whether a message should end a batch.


Type: `string`  
Default: `""`  

```yaml
# Examples

check: this.type == "end_of_transaction"
```

### `batch_policy.processors`

A list of [processors](/docs/components/processors/about) to apply to a batch as it is flushed. This allows you to aggregate and archive the batch however you see fit. Please note that all resulting messages are flushed as a single batch, therefore splitting the batch into smaller batches using these processors is a no-op.


Type: `array`  
Default: `[]`  

```yaml
# Examples

processors:
  - archive:
      format: lines

processors:
  - archive:
      format: json_array

processors:
  - merge_json: {}
```


