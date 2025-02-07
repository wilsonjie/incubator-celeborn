# Metrics

We provide various metrics about memory, disk, and important procedures. These metrics could help identify performance
issue or monitor Celeborn cluster.

## Prerequisites

1.Enable Celeborn metrics. Set configuration `celeborn.metrics.enabled` to true (true by default).

2.Install Prometheus (https://prometheus.io/). We provide an example for Prometheus config file:

```yaml
# Prometheus example config
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: "Celeborn"
    metrics_path: /metrics/prometheus
    scrape_interval: 15s
    static_configs:
      - targets: [ "master-ip:9098","worker1-ip:9096","worker2-ip:9096","worker3-ip:9096","worker4-ip:9096" ]
```

3.Install Grafana server (https://grafana.com/grafana/download).

4.Import Celeborn dashboard into Grafana.

You can find the Celeborn dashboard templates under the `assets/grafana` directory.
`celeborn-dashboard.json` displays Celeborn internal metrics and `celeborn-jvm-dashboard.json` displays Celeborn JVM related metrics.

### Optional

We recommend you to install node exporter (https://github.com/prometheus/node_exporter)
on every host, and configure Prometheus to scrape information about the host.
Grafana will need a dashboard (dashboard id:8919) to display host details.

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  - job_name: "Celeborn"
    metrics_path: /metrics/prometheus
    scrape_interval: 15s
    static_configs:
      - targets: [ "master-ip:9098","worker1-ip:9096","worker2-ip:9096","worker3-ip:9096","worker4-ip:9096" ]
  - job_name: "node"
    static_configs:
      - targets: [ "master-ip:9100","worker1-ip:9100","worker2-ip:9100","worker3-ip:9100","worker4-ip:9100" ]
```

### Import Dashboard Steps
Here is an example of Grafana dashboard importing.

![g1](assets/img/g1.png)
![g2](assets/img/g2.png)
![g3](assets/img/g3.png)
<img alt="g4" height="90%" src="assets/img/g4.png" width="90%"/>
<img alt="g6" height="90%" src="assets/img/g6.png" width="90%"/>
<img alt="g5" height="90%" src="assets/img/g5.png" width="90%"/>

## Details

|               MetricName               |       Scope       |                                                   Description                                                   |
|:--------------------------------------:|:-----------------:|:---------------------------------------------------------------------------------------------------------------:|
|              WorkerCount               |      master       |                                          The count of active workers.                                           |
|          ExcludedWorkerCount           |      master       |                                     The count of workers in excluded list.                                      |
|        RunningApplicationCount         |      master       |                                The count of running applications in the cluster.                                |
|             OfferSlotsTime             |      master       |                                            The time of offer slots.                                             |
|             PartitionSize              |      master       |          The estimated partition size of last 20 flush window whose length is 15 seconds by defaults.           |
|            PartitionWritten            |      master       |                                            The active shuffle size.                                             |
|           PartitionFileCount           |      master       |                                       The active shuffle partition count.                                       |
|             diskFileCount              |      master       |                                The count of disk files consumption by each user.                                |
|            diskBytesWritten            |      master       |                               The amount of disk files consumption by each user.                                |
|             hdfsFileCount              |      master       |                                The count of hdfs files consumption by each user.                                |
|            hdfsBytesWritten            |      master       |                               The amount of hdfs files consumption by each user.                                |
|         RegisteredShuffleCount         | master and worker |                                  The value means count of registered shuffle.                                   |
|            CommitFilesTime             |      worker       |                           CommitFiles means flush and close a shuffle partition file.                           |
|            ReserveSlotsTime            |      worker       |                     ReserveSlots means acquire a disk buffer and record partition location.                     |
|             FlushDataTime              |      worker       |                                  FlushData means flush a disk buffer to disk.                                   |
|             OpenStreamTime             |      worker       |            OpenStream means read a shuffle file and send client about chunks size and stream index.             |
|             FetchChunkTime             |      worker       |                      FetchChunk means read a chunk from a shuffle file and send to client.                      |
|          PrimaryPushDataTime           |      worker       |                      PrimaryPushData means handle pushdata of primary partition location.                       |
|          ReplicaPushDataTime           |      worker       |                      ReplicaPushData means handle pushdata of replica partition location.                       |
|           WriteDataFailCount           |      worker       |                    The count of writing PushData or PushMergedData failed in current worker.                    |
|         ReplicateDataFailCount         |      worker       |                  The count of replicating PushData or PushMergedData failed in current worker.                  |
|      ReplicateDataWriteFailCount       |      worker       |       The count of replicating PushData or PushMergedData failed caused by write failure in peer worker.        |
| ReplicateDataCreateConnectionFailCount |      worker       | The count of replicating PushData or PushMergedData failed caused by creating connection failed in peer worker. |
| ReplicateDataConnectionExceptionCount  |      worker       |    The count of replicating PushData or PushMergedData failed caused by connection exception in peer worker.    |
|       ReplicateDataTimeoutCount        |      worker       |        The count of replicating PushData or PushMergedData failed caused by push timeout in peer worker.        |
|             TakeBufferTime             |      worker       |                              TakeBuffer means get a disk buffer from disk flusher.                              |
|             SlotsAllocated             |      worker       |                                          Slots allocated in last hour                                           |
|              NettyMemory               |      worker       |                         The value measures all kinds of transport memory used by netty.                         |
|                SortTime                |      worker       |                           SortTime measures the time used by sorting a shuffle file.                            |
|               SortMemory               |      worker       |                       SortMemory means total reserved memory for sorting shuffle files .                        |
|              SortingFiles              |      worker       |                              This value means the count of sorting shuffle files.                               |
|              SortedFiles               |      worker       |                               This value means the count of sorted shuffle files.                               |
|             SortedFileSize             |      worker       |                        This value means the count of sorted shuffle files 's total size.                        |
|               DiskBuffer               |      worker       | Disk buffers are part of netty used memory, means data need to write to disk but haven't been written to disk.  |
|             PausePushData              |      worker       |                   PausePushData means the count of worker stopped receiving data from client.                   |
|       PausePushDataAndReplicate        |      worker       |    PausePushDataAndReplicate means the count of worker stopped receiving data from client and other workers.    |
|           ActiveShuffleSize            |      worker       |                 The active shuffle size of a worker including master replica and slave replica.                 |
|         ActiveShuffleFileCount         |      worker       |              The active shuffle file count of a worker including master replica and slave replica.              |
|              jvm_gc_count              |        JVM        |                                     The GC count of each garbage collector.                                     |
|              jvm_gc_time               |        JVM        |                                   The GC cost time of each garbage collector.                                   |
|          jvm_memory_heap_init          |        JVM        |                                         The amount of heap init memory.                                         |
|          jvm_memory_heap_max           |        JVM        |                                         The amount of heap max memory.                                          |
|          jvm_memory_heap_used          |        JVM        |                                         The amount of heap used memory.                                         |
|       jvm_memory_heap_committed        |        JVM        |                                      The amount of heap committed memory.                                       |
|         jvm_memory_heap_usage          |        JVM        |                                      The percentage of heap memory usage.                                       |
|        jvm_memory_non_heap_init        |        JVM        |                                       The amount of non-heap init memory.                                       |
|        jvm_memory_non_heap_max         |        JVM        |                                       The amount of non-heap max memory.                                        |
|        jvm_memory_non_heap_used        |        JVM        |                                       The amount of non-heap uesd memory.                                       |
|     jvm_memory_non_heap_committed      |        JVM        |                                    The amount of non-heap committed memory.                                     |
|       jvm_memory_non_heap_usage        |        JVM        |                                    The percentage of non-heap memory usage.                                     |
|         jvm_memory_pools_init          |        JVM        |                                  The amount of each memory pool's init memory.                                  |
|          jvm_memory_pools_max          |        JVM        |                                  The amount of each memory pool's max memory.                                   |
|         jvm_memory_pools_used          |        JVM        |                                  The amount of each memory pool's used memory.                                  |
|       jvm_memory_pools_committed       |        JVM        |                               The amount of each memory pool's committed memory.                                |
|     jvm_memory_pools_used_after_gc     |        JVM        |                             The amount of each memory pool's used memory after GC.                              |
|         jvm_memory_pools_usage         |        JVM        |                               The percentage of each memory pool's memory usage.                                |
|         jvm_memory_total_init          |        JVM        |                                        The amount of total init memory.                                         |
|          jvm_memory_total_max          |        JVM        |                                         The amount of total max memory.                                         |
|         jvm_memory_total_used          |        JVM        |                                        The amount of total used memory.                                         |
|       jvm_memory_total_committed       |        JVM        |                               The amount of each memory pool's committed memory.                                |
|          jvm_direct_capacity           |        JVM        |                          An estimate of the total capacity of the buffers in this pool                          |
|            jvm_direct_count            |        JVM        |                                An estimate of the number of buffers in the pool                                 |
|            jvm_direct_used             |        JVM        |                        An estimate of the memory that JVM is using for this buffer pool                         |
|          jvm_mapped_capacity           |        JVM        |                          An estimate of the total capacity of the buffers in this pool                          |
|            jvm_mapped_count            |        JVM        |                                An estimate of the number of buffers in the pool                                 |
|            jvm_mapped_used             |        JVM        |                        An estimate of the memory that JVM is using for this buffer pool                         |
|            jvm_thread_count            |        JVM        |                                         The current number of threads.                                          |
|        jvm_thread_daemon_count         |        JVM        |                                      The current number of daemon threads.                                      |
|        jvm_thread_blocked_count        |        JVM        |                               The current number of threads having blocked state.                               |
|       jvm_thread_deadlock_count        |        JVM        |                              The current number of threads having deadlock state.                               |
|          jvm_thread_new_count          |        JVM        |                                 The current number of threads having new state.                                 |
|       jvm_thread_runnable_count        |        JVM        |                              The current number of threads having runnable state.                               |
|      jvm_thread_terminated_count       |        JVM        |                             The current number of threads having terminated state.                              |
|     jvm_thread_timed_waiting_count     |        JVM        |                            The current number of threads having timed_waiting state.                            |
|        jvm_thread_waiting_count        |        JVM        |                               The current number of threads having waiting state.                               |
|               JVMCPUTime               |      system       |                                             The JVM costs cpu time.                                             |
|          AvailableProcessors           |      system       |                                   The amount of system available processors.                                    |
|          LastMinuteSystemLoad          |      system       |                                         The last minute load of system.                                         |

## Implementation

Celeborn master metrics : `org/apache/celeborn/service/deploy/master/MasterSource.scala`.

Celeborn worker metrics : `org/apache/celeborn/service/deploy/worker/WorkerSource.scala`.

Other common metrics are implemented in `org.apache.celeborn.common.metrics.source` package.

## Dashboard Snapshots

The dashboard [Celeborn-dashboard](assets/grafana/celeborn-dashboard.json) was generated by Grafana of version 10.0.3.

Here are some snapshots:

![d1](assets/img/dashboard1.png)
![d2](assets/img/dashboard_full.webp)
