---
title: "Storage Schemas"
linkTitle: "Storage Schemas"
weight: 103
slug: schemas
---

## Schema periodic table

The periodic table from argument (`-dynamodb.periodic-table.from=<date>` if
using command line flags, the `from` field for the first schema entry if using
YAML) should be set to the date the oldest metrics you will be sending to
Cortex. Generally that means set it to the date you are first deploying this
instance. If you use an example date from years ago table-manager will create
hundreds of tables. You can also avoid creating too many tables by setting a
reasonable retention in the table-manager
(`-table-manager.retention-period=<duration>`).

## Schema version

Choose schema version 9 in most cases; version 10 if you expect
hundreds of thousands of timeseries under a single name.  Anything
older than v9 is much less efficient.