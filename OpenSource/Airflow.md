# 1. Introduction

## 1.1. Airflow란 무엇인가?

> Airflow is a platform to programmatically author, schedule and monitor workflows

![../_images/arch-diag-basic.png](https://airflow.apache.org/docs/apache-airflow/stable/_images/arch-diag-basic.png)

Airbnb에서 개발한 워크플로우 관리 플랫폼!



## 1.2. 구성 요소

### Webserver

- A *webserver*, which presents a handy user interface to inspect, trigger and debug the behaviour of DAGs and tasks.

### Scheduler

* A [scheduler](https://airflow.apache.org/docs/apache-airflow/stable/concepts/scheduler.html), which handles both triggering scheduled workflows, and submitting [Tasks](https://airflow.apache.org/docs/apache-airflow/stable/concepts/tasks.html) to the executor to run.

### Executor

- An [executor](https://airflow.apache.org/docs/apache-airflow/stable/executor/index.html), which handles running tasks. In the default Airflow installation, this runs everything *inside* the scheduler, but most production-suitable executors actually push task execution out to *workers*.

### Worker

실제 Task를 처리하는 컴포넌트
Executor는 어떻게 실행될지에 대한 정의를 담당, Worker는 실제 프로세스 작업

### Database

- A *metadata database*, used by the scheduler, executor and webserver to store state.

Airflow의 DAG, Task 등의 메타데이터가 저장되는 데이터베이스



## 1.3. 특징

- **Dynamic**: Airflow pipelines are configuration as code (Python), allowing for dynamic pipeline generation. This allows for writing code that instantiates pipelines dynamically.
- **Extensible**: Easily define your own operators, executors and extend the library so that it fits the level of abstraction that suits your environment.
- **Elegant**: Airflow pipelines are lean and explicit. Parameterizing your scripts is built into the core of Airflow using the powerful **Jinja** templating engine.
- **Scalable**: Airflow has a modular architecture and uses a message queue to orchestrate an arbitrary number of workers. Airflow is ready to scale to infinity



## 1.4. 장점과 단점





# 2. Installation

## 2.1. Python Package

> https://github.com/apache/airflow

### 2.1.1. Requirements

|            | Main version (dev)           | Stable version (2.3.4)       |
| ---------- | ---------------------------- | ---------------------------- |
| Python     | 3.7, 3.8, 3.9, 3.10          | 3.7, 3.8, 3.9, 3.10          |
| Platform   | AMD64/ARM64(*)               | AMD64/ARM64(*)               |
| Kubernetes | 1.21, 1.22, 1.23, 1.24, 1.25 | 1.20, 1.21, 1.22, 1.23, 1.24 |
| PostgreSQL | 10, 11, 12, 13, 14           | 10, 11, 12, 13, 14           |
| MySQL      | 5.7, 8                       | 5.7, 8                       |
| SQLite     | 3.15.0+                      | 3.15.0+                      |
| MSSQL      | 2017(*), 2019 (*)            | 2017(*), 2019 (*)            |

### 2.1.2. Install with PIP

```shell
# default directory = ~/airflow
# if you want to install specific directory then
# export AIRFLOW_HOME=path/to/directory

# Install Airflow
pip install apache-airflow
```



## 2.2. Kubernetes

### 2.2.1 Requirements

- Kubernetes 1.20+ cluster
- Helm 3.0+
- PV provisioner support in the underlying infrastructure (optionally)

### 2.2.2. Install With Helm Chart

> https://airflow.apache.org/docs/helm-chart/stable/index.html

* Install with helm

```she
helm repo add apache-airflow https://airflow.apache.org
helm upgrade --install airflow apache-airflow/airflow --namespace airflow --create-namespace
```

* Install with **ArgoCD**

```yaml
# Example of overrides-values.yaml
createUserJob.useHelmHooks: false
migrateDatabaseJob.useHelmHooks: false
```



# 3. 용어 정리

## 3.1. DAG (Directed Acyclic Graph)

![../_images/basic-dag.png](https://airflow.apache.org/docs/apache-airflow/stable/_images/basic-dag.png)

* Task들의 의존성 및 관계를 설정하여 구성된다.
* Task 내부에서 어떤일이 일어나는지와 관계없이 어느 순서로 Task를 실행하고, 몇번을 재시도할 것인지등 Task 실행 및 관리만 신경씀



## 3.2. Task

Airflow의 기본 실행 단위이자 DAG을 구성하는 요소이다. 

Task간의 `upstream`, `downstream` dependencies를 통해 실행 순서를 정의한다.  





## 3.3. Executor



## 3.4. Sensor



## 3.5. Control flow





# 4. Configuration

## 4.1. Airflow config



## 4.2. values.yaml (Helm Chart)









# 5. Advanced

## DAG

### Parameters

* schedule_interval
* start_date
* catchup
* tags
* 

* default_args

  * 모든 Task에서 사용할 args를 정의

  * Task의 Base 참조

### Branching

### Dependencies

https://airflow.apache.org/docs/apache-airflow/stable/concepts/dags.html#dag-dependencies



## Task

### BaseOperator Parameters

> [Link](https://airflow.apache.org/docs/apache-airflow/stable/_api/airflow/models/baseoperator/index.html#airflow.models.baseoperator.BaseOperator)

- **task_id** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – a unique, meaningful id for the task

- **owner** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – the owner of the task. Using a meaningful description (e.g. user/person/team/role name) to clarify ownership is recommended.

- **email** (*Optional**[**Union**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*,* *Iterable**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*]**]**]*) – the ‘to’ email address(es) used in email alerts. This can be a single email or multiple ones. Multiple addresses can be specified as a comma or semi-colon separated string or by passing a list of strings.

- **email_on_retry** ([*bool*](https://docs.python.org/3/library/functions.html#bool)) – Indicates whether email alerts should be sent when a task is retried

- **email_on_failure** ([*bool*](https://docs.python.org/3/library/functions.html#bool)) – Indicates whether email alerts should be sent when a task failed

- **retries** (*Optional**[*[*int*](https://docs.python.org/3/library/functions.html#int)*]*) – the number of retries that should be performed before failing the task

- **retry_delay** (*Union**[*[*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta)*,* [*float*](https://docs.python.org/3/library/functions.html#float)*]*) – delay between retries, can be set as `timedelta` or `float` seconds, which will be converted into `timedelta`, the default is `timedelta(seconds=300)`.

- **retry_exponential_backoff** ([*bool*](https://docs.python.org/3/library/functions.html#bool)) – allow progressively longer waits between retries by using exponential backoff algorithm on retry delay (delay will be converted into seconds)

- **max_retry_delay** (*Optional**[**Union**[*[*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta)*,* [*float*](https://docs.python.org/3/library/functions.html#float)*]**]*) – maximum delay interval between retries, can be set as `timedelta` or `float` seconds, which will be converted into `timedelta`.

- **start_date** (*Optional**[*[*datetime.datetime*](https://docs.python.org/3/library/datetime.html#datetime.datetime)*]*) – The `start_date` for the task, determines the `execution_date` for the first task instance. The best practice is to have the start_date rounded to your DAG’s `schedule_interval`. Daily jobs have their start_date some day at 00:00:00, hourly jobs have their start_date at 00:00 of a specific hour. Note that Airflow simply looks at the latest `execution_date` and adds the `schedule_interval` to determine the next `execution_date`. It is also very important to note that different tasks’ dependencies need to line up in time. If task A depends on task B and their start_date are offset in a way that their execution_date don’t line up, A’s dependencies will never be met. If you are looking to delay a task, for example running a daily task at 2AM, look into the `TimeSensor` and `TimeDeltaSensor`. We advise against using dynamic `start_date` and recommend using fixed ones. Read the FAQ entry about start_date for more information.

- **end_date** (*Optional**[*[*datetime.datetime*](https://docs.python.org/3/library/datetime.html#datetime.datetime)*]*) – if specified, the scheduler won’t go beyond this date

- **depends_on_past** ([*bool*](https://docs.python.org/3/library/functions.html#bool)) – when set to true, task instances will run sequentially and only if the previous instance has succeeded or has been skipped. The task instance for the start_date is allowed to run.

- **wait_for_downstream** ([*bool*](https://docs.python.org/3/library/functions.html#bool)) – when set to true, an instance of task X will wait for tasks immediately downstream of the previous instance of task X to finish successfully or be skipped before it runs. This is useful if the different instances of a task X alter the same asset, and this asset is used by tasks downstream of task X. Note that depends_on_past is forced to True wherever wait_for_downstream is used. Also note that only tasks *immediately* downstream of the previous task instance are waited for; the statuses of any tasks further downstream are ignored.

- **dag** (*Optional**[*[*airflow.models.dag.DAG*](https://airflow.apache.org/docs/apache-airflow/stable/_api/airflow/models/dag/index.html#airflow.models.dag.DAG)*]*) – a reference to the dag the task is attached to (if any)

- **priority_weight** ([*int*](https://docs.python.org/3/library/functions.html#int)) – priority weight of this task against other task. This allows the executor to trigger higher priority tasks before others when things get backed up. Set priority_weight as a higher number for more important tasks.

- **weight_rule** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – weighting method used for the effective total priority weight of the task. Options are: `{ downstream | upstream | absolute }` default is `downstream` When set to `downstream` the effective weight of the task is the aggregate sum of all downstream descendants. As a result, upstream tasks will have higher weight and will be scheduled more aggressively when using positive weight values. This is useful when you have multiple dag run instances and desire to have all upstream tasks to complete for all runs before each dag can continue processing downstream tasks. When set to `upstream` the effective weight is the aggregate sum of all upstream ancestors. This is the opposite where downstream tasks have higher weight and will be scheduled more aggressively when using positive weight values. This is useful when you have multiple dag run instances and prefer to have each dag complete before starting upstream tasks of other dags. When set to `absolute`, the effective weight is the exact `priority_weight` specified without additional weighting. You may want to do this when you know exactly what priority weight each task should have. Additionally, when set to `absolute`, there is bonus effect of significantly speeding up the task creation process as for very large DAGs. Options can be set as string or using the constants defined in the static class `airflow.utils.WeightRule`

- **queue** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – which queue to target when running this job. Not all executors implement queue management, the CeleryExecutor does support targeting specific queues.

- **pool** (*Optional**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*]*) – the slot pool this task should run in, slot pools are a way to limit concurrency for certain tasks

- **pool_slots** ([*int*](https://docs.python.org/3/library/functions.html#int)) – the number of pool slots this task should use (>= 1) Values less than 1 are not allowed.

- **sla** (*Optional**[*[*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta)*]*) – time by which the job is expected to succeed. Note that this represents the `timedelta` after the period is closed. For example if you set an SLA of 1 hour, the scheduler would send an email soon after 1:00AM on the `2016-01-02` if the `2016-01-01` instance has not succeeded yet. The scheduler pays special attention for jobs with an SLA and sends alert emails for SLA misses. SLA misses are also recorded in the database for future reference. All tasks that share the same SLA time get bundled in a single email, sent soon after that time. SLA notification are sent once and only once for each task instance.

- **execution_timeout** (*Optional**[*[*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta)*]*) – max time allowed for the execution of this task instance, if it goes beyond it will raise and fail.

- **on_failure_callback** (*Optional**[**airflow.models.abstractoperator.TaskStateChangeCallback**]*) – a function to be called when a task instance of this task fails. a context dictionary is passed as a single parameter to this function. Context contains references to related objects to the task instance and is documented under the macros section of the API.

- **on_execute_callback** (*Optional**[**airflow.models.abstractoperator.TaskStateChangeCallback**]*) – much like the `on_failure_callback` except that it is executed right before the task is executed.

- **on_retry_callback** (*Optional**[**airflow.models.abstractoperator.TaskStateChangeCallback**]*) – much like the `on_failure_callback` except that it is executed when retries occur.

- **on_success_callback** (*Optional**[**airflow.models.abstractoperator.TaskStateChangeCallback**]*) – much like the `on_failure_callback` except that it is executed when the task succeeds.

- **pre_execute** (*Optional**[**TaskPreExecuteHook**]*) –

  a function to be called immediately before task execution, receiving a context dictionary; raising an exception will prevent the task from being executed.

  This is an [experimental feature](https://airflow.apache.org/docs/apache-airflow/stable/release-process.html#experimental).

  

- **post_execute** (*Optional**[**TaskPostExecuteHook**]*) –

  a function to be called immediately after task execution, receiving a context dictionary and task result; raising an exception will prevent the task from succeeding.

  This is an [experimental feature](https://airflow.apache.org/docs/apache-airflow/stable/release-process.html#experimental).

  

- **trigger_rule** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – defines the rule by which dependencies are applied for the task to get triggered. Options are: `{ all_success | all_failed | all_done | all_skipped | one_success | one_failed | none_failed | none_failed_min_one_success | none_skipped | always}` default is `all_success`. Options can be set as string or using the constants defined in the static class `airflow.utils.TriggerRule`

- **resources** (*Optional**[**Dict**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*,* *Any**]**]*) – A map of resource parameter names (the argument names of the Resources constructor) to their values.

- **run_as_user** (*Optional**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*]*) – unix username to impersonate while running the task

- **max_active_tis_per_dag** (*Optional**[*[*int*](https://docs.python.org/3/library/functions.html#int)*]*) – When set, a task will be able to limit the concurrent runs across execution_dates.

- **executor_config** (*Optional**[**Dict**]*) –

  Additional task-level configuration parameters that are interpreted by a specific executor. Parameters are namespaced by the name of executor.

  **Example**: to run this task in a specific docker container through the KubernetesExecutor

  ```
  MyOperator(...,
      executor_config={
          "KubernetesExecutor":
              {"image": "myCustomDockerImage"}
      }
  )
  ```

  

  

- **do_xcom_push** ([*bool*](https://docs.python.org/3/library/functions.html#bool)) – if True, an XCom is pushed containing the Operator’s result

- **task_group** (*Optional**[**airflow.utils.task_group.TaskGroup**]*) – The TaskGroup to which the task should belong. This is typically provided when not using a TaskGroup as a context manager.

- **doc** (*Optional**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*]*) – Add documentation or notes to your Task objects that is visible in Task Instance details View in the Webserver

- **doc_md** (*Optional**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*]*) – Add documentation (in Markdown format) or notes to your Task objects that is visible in Task Instance details View in the Webserver

- **doc_rst** (*Optional**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*]*) – Add documentation (in RST format) or notes to your Task objects that is visible in Task Instance details View in the Webserver

- **doc_json** (*Optional**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*]*) – Add documentation (in JSON format) or notes to your Task objects that is visible in Task Instance details View in the Webserver

- **doc_yaml** (*Optional**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str)*]*) – Add documentation (in YAML format) or notes to your Task objects that is visible in Task Instance details View in the Webserver





## Operator

### LatestOnlyOperator



### PythonOperator



### BranchDateTimeOperator



### BranchDayOfWeekOperator







## Sensors

## Triggers

### List of trigger rules

- `all_success` (default): All upstream tasks have succeeded
- `all_failed`: All upstream tasks are in a `failed` or `upstream_failed` state
- `all_done`: All upstream tasks are done with their execution
- `all_skipped`: All upstream tasks are in a `skipped` state
- `one_failed`: At least one upstream task has failed (does not wait for all upstream tasks to be done)
- `one_success`: At least one upstream task has succeeded (does not wait for all upstream tasks to be done)
- `none_failed`: All upstream tasks have not `failed` or `upstream_failed` - that is, all upstream tasks have succeeded or been skipped
- `none_failed_min_one_success`: All upstream tasks have not `failed` or `upstream_failed`, and at least one upstream task has succeeded.
- `none_skipped`: No upstream task is in a `skipped` state - that is, all upstream tasks are in a `success`, `failed`, or `upstream_failed` state
- `always`: No dependencies at all, run this task at any time

## XComs



## Edge Labels

As well as grouping tasks into groups, you can also label the *dependency edges* between different tasks in the Graph view - this can be especially useful for branching areas of your DAG, so you can label the conditions under which certain branches might run.

To add labels, you can use them directly inline with the `>>` and `<<` operators:

```
from airflow.utils.edgemodifier import Label

my_task >> Label("When empty") >> other_task
```



Or, you can pass a Label object to `set_upstream`/`set_downstream`:

```
from airflow.utils.edgemodifier import Label

my_task.set_downstream(other_task, Label("When empty"))
```



Here’s an example DAG which illustrates labeling different branches:

![../_images/edge_label_example.png](https://airflow.apache.org/docs/apache-airflow/stable/_images/edge_label_example.png)





# 6. CLI





# API



# Security









# 9. Trouble Shooting



