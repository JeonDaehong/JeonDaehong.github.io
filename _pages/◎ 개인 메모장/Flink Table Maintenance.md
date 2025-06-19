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
        .targetFileSizeBytes(128 * 1024 * 1024)
        .partialProgressEnabled(true)
        .partialProgressMaxCommits(10))
    .append();

env.execute("Table Maintenance Job");
```

## Configuration Options

### TableMaintenance Builder

| Method | Description | Default |
|--------|-------------|---------|
| `uidSuffix(String)` | Unique identifier suffix for the job | Random UUID |
| `rateLimit(Duration)` | Minimum interval between task executions | 60 seconds |
| `lockCheckDelay(Duration)` | Delay for checking lock availability | 30 seconds |
| `parallelism(int)` | Default parallelism for maintenance tasks | System default |
| `maxReadBack(int)` | Max snapshots to check during initialization | 100 |

### ExpireSnapshots Configuration

| Method | Description | Default Value | Type |
|--------|-------------|---------------|------|
| `maxSnapshotAge(Duration)` | Maximum age of snapshots to retain | No limit | Duration |
| `retainLast(int)` | Minimum number of snapshots to retain | 1 | int |
| `deleteBatchSize(int)` | Number of files to delete in each batch | 1000 | int |
| `scheduleOnCommitCount(int)` | Trigger after N commits | No automatic scheduling | int |
| `scheduleOnDataFileCount(int)` | Trigger after N data files | No automatic scheduling | int |
| `scheduleOnDataFileSize(long)` | Trigger after total data file size (bytes) | No automatic scheduling | long |
| `scheduleOnIntervalSecond(long)` | Trigger after time interval (seconds) | No automatic scheduling | long |
| `parallelism(int)` | Parallelism for this specific task | Inherits from TableMaintenance | int |



### RewriteDataFiles Configuration

| Method | Description | Default Value | Type |
|--------|-------------|---------------|------|
| `targetFileSizeBytes(long)` | Target size for rewritten files | Table property or 512MB | long |
| `partialProgressEnabled(boolean)` | Enable partial progress commits | false | boolean |
| `partialProgressMaxCommits(int)` | Maximum commits for partial progress | 10 | int |
| `scheduleOnCommitCount(int)` | Trigger after N commits | 10 | int |
| `scheduleOnDataFileCount(int)` | Trigger after N data files | 1000 | int |
| `scheduleOnDataFileSize(long)` | Trigger after total data file size (bytes) | 100GB | long |
| `scheduleOnIntervalSecond(long)` | Trigger after time interval (seconds) | 600 (10 minutes) | long |
| `maxRewriteBytes(long)` | Maximum bytes to rewrite per execution | Long.MAX_VALUE | long |
| `parallelism(int)` | Parallelism for this specific task | Inherits from TableMaintenance | int |

## Flink Configuration Options

You can also configure maintenance behavior through Flink configuration:

| Configuration Key | Description | Default Value | Type |
|-------------------|-------------|---------------|------|
| `iceberg.maintenance.rate-limit-seconds` | Rate limit in seconds | 60 | long |
| `iceberg.maintenance.lock-check-delay-seconds` | Lock check delay in seconds | 30 | long |
| `iceberg.maintenance.rewrite.max-bytes` | Maximum rewrite bytes | Long.MAX_VALUE | long |
| `iceberg.maintenance.rewrite.schedule.commit-count` | Schedule on commit count | 10 | int |
| `iceberg.maintenance.rewrite.schedule.data-file-count` | Schedule on data file count | 1000 | int |
| `iceberg.maintenance.rewrite.schedule.data-file-size` | Schedule on data file size | 100GB | long |
| `iceberg.maintenance.rewrite.schedule.interval-second` | Schedule interval in seconds | 600 | long |


## Complete Example

```java
public class TableMaintenanceJob {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.enableCheckpointing(60000); // Enable checkpointing
        
        // Configure table loader
        TableLoader tableLoader = TableLoader.fromCatalog(
            CatalogLoader.hive("my_catalog", configuration),
            TableIdentifier.of("database", "table")
        );
        
        // Set up maintenance with comprehensive configuration
        TableMaintenance.forTable(env, tableLoader, TriggerLockFactory.defaultLockFactory())
            .uidSuffix("production-maintenance")
            .rateLimit(Duration.ofMinutes(15))
            .lockCheckDelay(Duration.ofSeconds(30))
            .parallelism(4)
            
            // Daily snapshot cleanup
            .add(ExpireSnapshots.builder()
                .maxSnapshotAge(Duration.ofDays(7))
                .retainLast(10)
                .deleteBatchSize(1000)
                .scheduleOnCommitCount(50)
                .parallelism(2))
            
            // Continuous file optimization
            .add(RewriteDataFiles.builder()
                .targetFileSizeBytes(256 * 1024 * 1024) // 256MB
                .minFileSizeBytes(32 * 1024 * 1024)     // 32MB  
                .scheduleOnDataFileCount(20)
                .partialProgressEnabled(true)
                .partialProgressMaxCommits(5)
                .maxRewriteBytes(2L * 1024 * 1024 * 1024) // 2GB
                .parallelism(6))
                
            .append();
        
        env.execute("Iceberg Table Maintenance");
    }
}
```

## Scheduling Options

Maintenance tasks can be triggered based on various conditions:

### Time-based Scheduling
```java
ExpireSnapshots.builder()
    .scheduleOnIntervalSecond(3600) // Every hour
```

### Commit-based Scheduling
```java
RewriteDataFiles.builder()
    .scheduleOnCommitCount(50) // After 50 commits
```

### Data Volume-based Scheduling
```java
RewriteDataFiles.builder()
    .scheduleOnDataFileCount(500) // After 500 data files
    .scheduleOnDataFileSize(50L * 1024 * 1024 * 1024) // After 50GB
```

## Best Practices

### Resource Management
- Use dedicated slot sharing groups for maintenance tasks
- Set appropriate parallelism based on cluster resources
- Enable checkpointing for fault tolerance

### Scheduling Strategy
- Avoid too frequent executions with `rateLimit`
- Use `scheduleOnCommitCount` for write-heavy tables
- Use `scheduleOnDataFileCount` for fine-grained control

### Performance Tuning
- Adjust `deleteBatchSize` based on storage performance
- Enable `partialProgressEnabled` for large rewrite operations
- Set reasonable `maxRewriteBytes` limits

## Monitoring

The maintenance jobs expose metrics through Flink's metric system:

- `maintenance.triggered` - Number of triggered maintenance tasks
- `maintenance.succeededTasks` - Number of successful tasks
- `maintenance.failedTasks` - Number of failed tasks
- `maintenance.lastRunDurationMs` - Duration of last execution

## Troubleshooting

### Common Issues

**OutOfMemoryError during file deletion:**
```java
.deleteBatchSize(500)  // Reduce batch size
```

**Lock conflicts:**
```java
.lockCheckDelay(Duration.ofMinutes(1))  // Increase delay
.rateLimit(Duration.ofMinutes(10))      // Reduce frequency
```

**Slow rewrite operations:**
```java
.partialProgressEnabled(true)
.partialProgressMaxCommits(3)
.maxRewriteBytes(1024 * 1024 * 1024)  // 1GB limit
```