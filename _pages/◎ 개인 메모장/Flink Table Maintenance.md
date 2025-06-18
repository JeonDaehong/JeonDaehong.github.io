---
title: "MEMO - Flink Table Maintenance"
tags:
    - Memo
date: "2000-01-01"
thumbnail: "/assets/img/thumbnail/MEMO.png"
bookmark: true
---

<br>

# 📝 Overview
---
When using **Apache Iceberg tables** in a **Flink streaming environment**, it's important to automate table maintenance operations such as `snapshot expiration`, `small file compaction`, and `orphan file cleanup`.

Previously, these maintenance tasks were only available through **Iceberg Spark Actions**, requiring a separate Spark cluster. However, maintaining **Spark infrastructure** just for table optimization adds **complexity** and **operational overhead**.

The `TableMaintenance` API in **Apache Iceberg** enables **Flink jobs** to execute these maintenance tasks **natively** within the same streaming job or as an independent Flink job, avoiding the need for external systems. This approach simplifies **architecture**, reduces **costs**, and improves **automation**.