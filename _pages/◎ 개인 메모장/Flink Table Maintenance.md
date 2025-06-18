---
title: "MEMO - Flink Table Maintenance"
tags:
    - Memo
date: "2000-01-01"
thumbnail: "/assets/img/thumbnail/MEMO.png"
bookmark: true
---

<br>

## Overview
---
When using **Apache Iceberg tables** in a **Flink streaming environment**, it's important to automate table maintenance operations such as `snapshot expiration`, `small file compaction`, and `orphan file cleanup`.

Previously, these maintenance tasks were only available through **Iceberg Spark Actions**, requiring a separate Spark cluster. However, maintaining **Spark infrastructure** just for table optimization adds **complexity** and **operational overhead**.

The `TableMaintenance` API in **Apache Iceberg** enables **Flink jobs** to execute these maintenance tasks **natively** within the same streaming job or as an independent Flink job, avoiding the need for external systems. This approach simplifies **architecture**, reduces **costs**, and improves **automation**.

## Quick Start
---
Here's a simple example that sets up automated maintenance for an Iceberg table:

```java
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
TableLoader tableLoader = TableLoader.fromHadoopTable("path/to/table");
TriggerLockFactory lockFactory = TriggerLockFactory.defaultLockFactory();

TableMaintenance.forTable(env, tableLoader, lockFactory)
    .uidSuffix("my-maintenance-job")
    .rateLimit(Duration.ofMinutes(10))
    .lockCheckDelay(Duration.ofSeconds(10))
    .add(ExpireSnapshots.builder()
        .scheduleOnCommitCount(10)
        .maxSnapshotAge(Duration.ofMinutes(10))
        .retainLast(5)
        .deleteBatchSize(5)
        .parallelism(8))
    .add(RewriteDataFiles.builder()
        .scheduleOnDataFileCount(10)
        .targetFileSizeBytes(128 * 1024 * 1024) // 128MB
        .partialProgressEnabled(true)
        .partialProgressMaxCommits(10))
    .append();

env.execute("Table Maintenance Job");
```