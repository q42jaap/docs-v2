---
title: rows.map() function
description: >
  The `rows.map()` function is an alternate implementation of [`map()`](/flux/v0.x/stdlib/universe/map/)
  that is faster, but more limited than `map()`.
aliases:
  - /influxdb/v2.0/reference/flux/stdlib/contrib/rows/map/
  - /influxdb/cloud/reference/flux/stdlib/contrib/rows/map/
menu:
  flux_0_x_ref:
    name: rows.map
    parent: rows
weight: 301
flux/v0.x/tags: [transformations]
related:
  - /flux/v0.x/stdlib/universe/map/
introduced: 0.77.0
---

The `rows.map()` function is an alternate implementation of [`map()`](/flux/v0.x/stdlib/universe/map/)
that is faster, but more limited than `map()`.
`rows.map()` cannot modify [groups keys](/flux/v0.x/get-started/data-model/#group-key) and,
therefore, does not need to regroup tables.
**Attempts to change columns in the group key are ignored.**


```js
import "contrib/jsternberg/rows"

rows.map( fn: (r) => ({_value: r._value * 100.0}))
```

## Parameters

### fn {data-type="function"}

A single argument function to apply to each record.
The return value must be a record.

{{% note %}}
Use the `with` operator to preserve columns **not** in the group and **not**
explicitly mapped in the operation.
{{% /note %}}

### tables {data-type="stream of tables"}
Input data.
Default is piped-forward data ([`<-`](/flux/v0.x/spec/expressions/#pipe-expressions)).

## Examples

- [Perform mathemtical operations on column values](#perform-mathemtical-operations-on-column-values)
- [Preserve all columns in the operation](#preserve-all-columns-in-the-operation)
- [Attempt to remap columns in the group key](#attempt-to-remap-columns-in-the-group-key)

---

### Perform mathematical operations on column values
The following example returns the square of each value in the `_value` column:

```js
import "contrib/jsternberg/rows"

data
    |> rows.map(fn: (r) => ({ _value: r._value * r._value }))
```

{{% note %}}
#### Important notes
The `_time` column is dropped because:

- It's not in the group key.
- It's not explicitly mapped in the operation.
- The `with` operator was not used to include existing columns.
{{% /note %}}

{{< flex >}}
{{% flex-content %}}
#### Input tables

**Group key:** `tag,_field`

| tag  | _field | _time | _value |
| ---  |:------ |:----- | ------:|
| tag1 | foo    | 0001  | 1.9    |
| tag1 | foo    | 0002  | 2.4    |
| tag1 | foo    | 0003  | 2.1    |

| tag  | _field | _time | _value |
|:---  |:------ |:----- | ------:|
| tag2 | bar    | 0001  | 3.1    |
| tag2 | bar    | 0002  | 3.8    |
| tag2 | bar    | 0003  | 1.7    |
{{% /flex-content %}}

{{% flex-content %}}
#### Output tables

**Group key:** `tag,_field`

| tag  | _field | _value |
| ---  |:------ | ------:|
| tag1 | foo    | 3.61   |
| tag1 | foo    | 5.76   |
| tag1 | foo    | 4.41   |

| tag  | _field | _value |
|:---  |:------ | ------:|
| tag2 | bar    | 9.61   |
| tag2 | bar    | 14.44  |
| tag2 | bar    | 2.89   |
{{% /flex-content %}}
{{< /flex >}}

---

### Preserve all columns in the operation
Use the `with` operator in your mapping operation to preserve all columns,
including those not in the group key, without explicitly remapping them.

```js
import "contrib/jsternberg/rows"

data
    |> rows.map(fn: (r) => ({ r with _value: r._value * r._value }))
```

{{% note %}}
#### Important notes
- The mapping operation remaps the `_value` column.
- The `with` operator preserves all other columns not in the group key (`_time`).
{{% /note %}}

{{< flex >}}
{{% flex-content %}}
#### Input tables

**Group key:** `tag,_field`

| tag  | _field | _time | _value |
|:---  |:------ |:----- | ------:|
| tag1 | foo    | 0001  | 1.9    |
| tag1 | foo    | 0002  | 2.4    |
| tag1 | foo    | 0003  | 2.1    |

| tag  | _field | _time | _value |
| ---  |:------ |:----- | ------:|
| tag2 | bar    | 0001  | 3.1    |
| tag2 | bar    | 0002  | 3.8    |
| tag2 | bar    | 0003  | 1.7    |
{{% /flex-content %}}

{{% flex-content %}}
#### Output tables

**Group key:** `tag,_field`

| tag  | _field | _time | _value |
|:---  |:------ |:----- | ------:|
| tag1 | foo    | 0001  | 3.61   |
| tag1 | foo    | 0002  | 5.76   |
| tag1 | foo    | 0003  | 4.41   |

| tag  | _field | _time | _value |
| ---  |:------ |:----- | ------:|
| tag2 | bar    | 0001  | 9.61   |
| tag2 | bar    | 0002  | 14.44  |
| tag2 | bar    | 0003  | 2.89   |
{{% /flex-content %}}
{{< /flex >}}

---

### Attempt to remap columns in the group key

```js
import "contrib/jsternberg/rows"

data
    |> rows.map(fn: (r) => ({ r with tag: "tag3" }))
```

{{% note %}}
#### Important notes
- Remapping the `tag` column to `"tag3"` is ignored because `tag` is part of the group key.
- The `with` operator preserves columns not in the group key (`_time` and `_value`).
{{% /note %}}

{{< flex >}}
{{% flex-content %}}
#### Input tables

**Group key:** `tag,_field`

| tag  | _field | _time | _value |
| ---  |:------ |:----- | ------:|
| tag1 | foo    | 0001  | 1.9    |
| tag1 | foo    | 0002  | 2.4    |
| tag1 | foo    | 0003  | 2.1    |

| tag  | _field | _time | _value |
|:---  |:------ |:----- | ------:|
| tag2 | bar    | 0001  | 3.1    |
| tag2 | bar    | 0002  | 3.8    |
| tag2 | bar    | 0003  | 1.7    |
{{% /flex-content %}}

{{% flex-content %}}
#### Output tables

**Group key:** `tag,_field`

| tag  | _field | _time | _value |
| ---  |:------ |:----- | ------:|
| tag1 | foo    | 0001  | 1.9    |
| tag1 | foo    | 0002  | 2.4    |
| tag1 | foo    | 0003  | 2.1    |

| tag  | _field | _time | _value |
|:---  |:------ |:----- | ------:|
| tag2 | bar    | 0001  | 3.1    |
| tag2 | bar    | 0002  | 3.8    |
| tag2 | bar    | 0003  | 1.7    |
{{% /flex-content %}}
{{< /flex >}}
