---
description: The expiration enum used by many of our ADOs to set a time limit.
---

# Expiration

### Definition

Expiration represents a point in time when some event happens. It can compare with a BlockInfo and will return is\_expired() == true once the condition is hit (and for every block in the future).

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Expiration {
    AtHeight(u64),
    AtTime(Timestamp),
    Never {},
}
```
{% endtab %}
{% endtabs %}

**AtHeight:** AtHeight will expire when `env.block.height` >= height.

**AtTime:** AtTime will expire when `env.block.time` >= time.

**Never:** Never will never expire. Used to express the empty variant.

#### Timestamp

A point in time in nanosecond precision.

```rust
pub struct Timestamp(Uint64)
```

### JSON Implementation

```json
{
"expiration":{
  "at_height": 500
  }

or

{
"expiration":{
  "at_time":"124655832"
}

```
