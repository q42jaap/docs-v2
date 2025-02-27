---
title: date.addDuration() function
description: >
  `date.addDuration()` adds a duration to a time value and returns the resulting time.
menu:
  flux_0_x_ref:
    name: date.addDuration
    parent: date
weight: 302
flux/v0.x/tags: [date/time]
related:
  - /flux/v0.x/stdlib/date/subduration/
introduced: 0.162.0
---

`date.addDuration()` adds a duration to a time value and returns the resulting time.

```js
import "date"

date.addDuration(d: 12h, to: now())
```

## Parameters

### d {data-type="duration"}
Duration to add.

### to {data-type="time, duration"}
Time to add the [duration](#d) to.
Use an absolute time or a relative duration.
Durations are relative to [`now()`](/flux/v0.x/stdlib/universe/now/).

## Examples

### Add six hours to a timestamp
```js
import "date"

date.addDuration(d: 6h, to: 2019-09-16T12:00:00Z)

// Returns 2019-09-16T18:00:00.000000000Z
```

### Add six hours to a relative duration
```js
import "date"

option now = () => 2022-01-01T12:00:00Z

date.addDuration(d: 6h, to: 3h)

// Returns 2022-01-01T21:00:00.000000000Z
```
