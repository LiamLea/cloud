# PromQL

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

- [PromQL](#promql)
    - [概述](#概述)
      - [1.基础概念](#1基础概念)
        - [（1）vector（向量）](#1vector向量)
      - [2.PromQL结果的数据类型](#2promql结果的数据类型)
        - [（1）instant vector（瞬时向量）](#1instant-vector瞬时向量)
        - [（2）range vector（区间向量）](#2range-vector区间向量)
        - [（3）scalar（标量）](#3scalar标量)
      - [2.时间序列的选择器](#2时间序列的选择器)
        - [（1）instant vector selector](#1instant-vector-selector)
        - [（2）range vector selector](#2range-vector-selector)
      - [3.向量 修饰符](#3向量-修饰符)
        - [（1）`offset`](#1offset)
        - [（2）`@`](#2)
      - [4.operators（运算符）](#4operators运算符)
        - [（1）算数运算符](#1算数运算符)
        - [（2）比较运算符](#2比较运算符)
      - [5.聚合运算符](#5聚合运算符)
        - [（1）运算符](#1运算符)
        - [（2）语法](#2语法)
      - [6.函数](#6函数)
        - [（1）常用函数](#1常用函数)
        - [（2）速率相关](#2速率相关)
        - [（3）`<aggregation>_over_time()`](#3aggregation_over_time)
    - [Usage](#usage)
      - [1.merge two metrics](#1merge-two-metrics)
      - [2.cpu related](#2cpu-related)
        - [(1) cpu usage per container](#1-cpu-usage-per-container)
        - [(2) cpu usage / requests](#2-cpu-usage--requests)
        - [(3) cpu usage every 5 minutes in a day](#3-cpu-usage-every-5-minutes-in-a-day)
        - [(4) 95% cpu usage of every container in a day](#4-95-cpu-usage-of-every-container-in-a-day)
      - [3.memory related](#3memory-related)
        - [(1) memory usage per container](#1-memory-usage-per-container)
        - [(2) memory usage / requests](#2-memory-usage--requests)
        - [(3) memory usage in an hour](#3-memory-usage-in-an-hour)
        - [(4) max memory usage in a day](#4-max-memory-usage-in-a-day)
      - [3.list all values of a label](#3list-all-values-of-a-label)
      - [4.query deployment usage](#4query-deployment-usage)

<!-- /code_chunk_output -->

### 概述

#### 1.基础概念

##### （1）vector（向量）
时间序列 是按照 时间戳和值 顺序存放的，所以被称为向量

#### 2.PromQL结果的数据类型

##### （1）instant vector（瞬时向量）
某一个时间点的样本值，比如：`node_cpu_seconds_total{mode="idle"}`

##### （2）range vector（区间向量）
某一个时间范围内的样本值，比如：`node_cpu_seconds_total{mode="idle"}[5m]`

##### （3）scalar（标量）
跟时间戳没有关系，只有一个值，比如：`count(node_cpu_seconds_total{mode="idle"})`

#### 2.时间序列的选择器

##### （1）instant vector selector
```shell
<METRIC>{<labels>}
```

* `<labels>`完全匹配：`=`、`!=`
  * 比如：`label=value`、`label!=value`
* `<labels>`正则匹配
  * 比如：`label=~regx`、`label!~regx`

##### （2）range vector selector
```shell
(<EXPRESSION>)[<TIME>:<STEP>]
#<TIME>指定最近多长时间
#<STEP>步长可以忽略（嵌套查询时，外层的不能省略），默认为采样时间
```

#### 3.向量 修饰符

##### （1）`offset`

* 将时间向过去移
```shell
<METRIC>{<labels>} offset <TIME>
(<EXPRESSION>)[<TIME>:<STEP>] offset <TIME>
```
##### （2）`@`

* 指定某个时间点，是unix timestamp，比如
```shell
http_requests_total @ 1609746000
rate(http_requests_total[5m] @ 1609746000)
```

#### 4.operators（运算符）

##### （1）算数运算符
```shell
+
-
*
/
%
^
```

##### （2）比较运算符
```shell
==
!=
>
<
>=
<=
```

#### 5.聚合运算符

##### （1）运算符
```shell
sum (calculate sum over dimensions)
min (select minimum over dimensions)
max (select maximum over dimensions)
avg (calculate the average over dimensions)
group (all values in the resulting vector are 1)
stddev (calculate population standard deviation over dimensions)
stdvar (calculate population standard variance over dimensions)
count (count number of elements in the vector)
count_values (count number of elements with the same value)
bottomk (smallest k elements by sample value)
topk (largest k elements by sample value)
quantile (calculate φ-quantile (0 ≤ φ ≤ 1) over dimensions)
```

##### （2）语法
```shell
<aggr-operator>([parameter,] <vector expression>) [without|by (<label list>)]

#parameter是某些聚合运算需要
#(<label list>)格式：(label1, label2)
```

#### 6.函数
[参考](https://prometheus.io/docs/prometheus/latest/querying/functions/)

##### （1）常用函数

* `increate(METRIC[TIME])`
```shell
last值-first值
#比如：increase(node_cpu_seconds_total{mode="idle"}[1m])
#得到每个cpu每1分钟，cpu处于idle增长的时间
```

##### （2）速率相关
* `rate(METRIC[TIME])`（当TIME <= 采集周期 时，则rate不会返回任何结果）
```shell
(last值-first值)/时间差s
#配合counter类型数据使用
```

* `irate(METRIC[TIME])`
```shell
(last值-倒数第二个值)/时间差s
#所以这里的TIME对irate意义不大，当结合其他聚合函数才有意义，比如avg
```

* example
  ```shell
  #采用周期为1m，采样的值如下：
  0
  60
  120
  600
  720
  780
  ```
  5分钟 rate：`(780-0)/(5*60)`
  5分钟 irate：`(780-720)/1*60)`

##### （3）`<aggregation>_over_time()`
```shell
avg_over_time(range-vector): the average value of all points in the specified interval.
min_over_time(range-vector): the minimum value of all points in the specified interval.
max_over_time(range-vector): the maximum value of all points in the specified interval.
sum_over_time(range-vector): the sum of all values in the specified interval.
count_over_time(range-vector): the count of all values in the specified interval.
quantile_over_time(scalar, range-vector): the φ-quantile (0 ≤ φ ≤ 1) of the values in the specified interval.
stddev_over_time(range-vector): the population standard deviation of the values in the specified interval.
stdvar_over_time(range-vector): the population standard variance of the values in the specified interval.
last_over_time(range-vector): the most recent point value in specified interval.
```

***

### Usage

#### 1.merge two metrics

```
kube_replicaset_owner * on(replicaset,namespace)  group_right(owner_kind,owner_name) label_replace(kube_pod_info{created_by_kind="ReplicaSet"}, "replicaset", "$1", "created_by_name", "(.*)")
```

* `*` 
  * the value of metrc1 X the value of metric2
* `on(replicaset,namespace)` 
  * these labels must identiy metric1 **uniquely**
* `group_right(owner_kind,owner_name)`
  * merge metric1's `owner_kind,owner_name` to metric2 when `metric2.(replicaset,namespace) == metric1.(replicaset,namespace)`
* label_replace
  * change metric2's label name

#### 2.cpu related
##### (1) cpu usage per container

```
sum by (cluster, namespace, pod, container) (irate(container_cpu_usage_seconds_total[5m]))
```

##### (2) cpu usage / requests

```
sum by (cluster, namespace, pod, container) (irate(container_cpu_usage_seconds_total[5m])) / sum by (cluster, namespace, pod, container) (kube_pod_container_resource_requests{resource="cpu"})
```

##### (3) cpu usage every 5 minutes in a day
```
sum by (cluster, namespace, pod, container) (irate(container_cpu_usage_seconds_total[5m]))[1d:5m]
```

##### (4) 95% cpu usage of every container in a day
```
quantile_over_time(0.95, sum by (cluster, namespace, pod, container) (irate(container_cpu_usage_seconds_total[5m]))[1d:5m])
```

#### 3.memory related
##### (1) memory usage per container
```
sum by (cluster, namespace, pod, container) (container_memory_working_set_bytes)
```

##### (2) memory usage / requests
```
sum by (cluster, namespace, pod, container) (container_memory_working_set_bytes) / sum by (cluster, namespace, pod, container) (kube_pod_container_resource_requests{resource="memory"})
```

##### (3) memory usage in an hour
```
sum by (cluster, namespace, pod, container) (container_memory_working_set_bytes)[5m]
```

##### (4) max memory usage in a day
```
max_over_time(sum by (cluster, namespace, pod, container) (container_memory_working_set_bytes)[1d:5m])
```

#### 3.list all values of a label
```
count by (created_by_kind) (kube_pod_info)
```

#### 4.query deployment usage

```
quantile_over_time(0.95, sum by (cluster, namespace, owner_kind,owner_name, container) (irate(((kube_replicaset_owner * on(replicaset,namespace)  group_right(owner_kind,owner_name) label_replace(kube_pod_info{created_by_kind="ReplicaSet"}, "replicaset", "$1", "created_by_name", "(.*)")) * on(pod,namespace) group_right(owner_kind,owner_name) container_cpu_usage_seconds_total)[5m:30s]))[24h:5m])
```


```
sum by (cluster, namespace, owner_kind, owner_name, container,resource) ((kube_replicaset_owner * on(replicaset,namespace)  group_right(owner_kind,owner_name) label_replace(kube_pod_info{created_by_kind="ReplicaSet"}, "replicaset", "$1", "created_by_name", "(.*)")) * on(pod,namespace) group_right(owner_kind,owner_name) kube_pod_container_resource_requests{resource="cpu"})
```