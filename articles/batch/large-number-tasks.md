---
title: Batch 작업으로 많은 수의 작업 제출
description: 단일 Azure Batch 작업에서 매우 많은 수의 작업을 효율적으로 제출하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: c3b9612dd485c531c3a25bfe80758cf73c17123d
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965245"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>Batch 작업으로 많은 수의 작업 제출

대규모 Azure Batch 워크로드를 실행할 때 수천 수만, 수십 만 또는 더 많은 작업을 단일 작업에 제출할 수 있습니다.

이 문서에서는 처리량이 많은 작업을 단일 일괄 작업으로 전송하는 방법을 보여줍니다. 작업이 제출된 후 작업에 대해 지정한 풀에서 처리를 위해 Batch 큐를 입력합니다.

## <a name="use-task-collections"></a>작업 컬렉션 사용

많은 수의 작업을 추가하는 경우, 일괄 처리 Api에서 제공하는 적절한 메서드 또는 오버 로드를 사용하여 한 번에 하나의 *컬렉션* 으로 작업을 추가합니다. 일반적으로 작업(job)에 대해 입력 파일 또는 매개 변수의 집합을 반복함으로써 작업(task)을 정의하여 작업(task) 컬렉션을 생성합니다.

단일 호출에 추가할 수 있는 작업 컬렉션의 최대 크기는 사용하는 Batch API에 따라 달라집니다.

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>최대 100개 작업의 컬렉션을 허용하는 Api

다음 Batch API는 컬렉션을 100개의 작업으로 제한합니다. 한도는 작업의 크기에 따라 더 작을 수 있습니다(예: 작업에 많은 수의 리소스 파일 또는 환경 변수가 있는 경우).

- [REST API](/rest/api/batchservice/task/addcollection)
- [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Node.js API](/javascript/api/@azure/batch/task)

이러한 Api를 사용하는 경우 태스크 수를 컬렉션 제한에 맞게 나누고, 태스크 추가 오류가 발생할 경우 오류 및 재시도를 처리하는 논리를 제공해야 합니다. 작업 컬렉션이 추가하기에 너무 큰 경우 요청은 오류를 생성하고 더 적은 작업으로 다시 시도되어야 합니다.

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>많은 수의 작업의 컬렉션을 허용하는 Api

다른 Batch API는 클라이언트 제출에서 RAM 가용성에 의해서만 제한되는 훨씬 더 큰 작업 컬렉션을 지원합니다. 이러한 Api는 작업 컬렉션을 하위 수준 Api에 대한 "청크"로 분리하여 작업 추가 오류에 대한 재시도를 투명하게 처리합니다.

- [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- Batch CLI 템플릿이 포함된 [Azure Batch CLI 확장](batch-cli-templates.md)
- [Python SDK 확장](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>작업 제출의 처리량 늘리기

많은 작업 컬렉션을 추가하는 데 다소 시간이 걸릴 수 있습니다. 예를 들어 .NET API를 통해 2만개의 작업을 추가하는 데 최대 1분이 걸릴 수 있습니다. Batch API 및 워크로드에 따라, 다음 중 하나 이상을 수정하여 작업 처리량을 향상 시킬 수 있습니다.

### <a name="task-size"></a>작업 크기

대규모 작업을 추가하는 것은 작은 작업을 추가하는 것보다 시간이 오래 걸립니다. 컬렉션에서 각 작업의 크기를 줄이려면 태스크 명령줄을 간소화하고, 환경 변수의 수를 줄이거나 태스크 실행에 대한 요구 사항을 보다 효율적으로 처리할 수 있습니다.

예를 들어 많은 수의 리소스 파일을 사용하는 대신, 풀에서 [작업 시작](jobs-and-tasks.md#start-task)을 사용하여 작업 종속성을 설치하거나, [응용 프로그램 패키지](batch-application-packages.md) 또는 [Docker 컨테이너](batch-docker-container-workloads.md)를 사용합니다.

### <a name="number-of-parallel-operations"></a>병렬 작업 수

Batch API에 따라 Batch 클라이언트에서 동시 실행 작업의 최대 수를 늘려서 처리량을 증가시킵니다. .NET API의 [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) 속성 또는 Batch Python SDK 확장의 [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations)과 같은 메서드의 `threads` 매개 변수를 사용하여 이 설정을 구성합니다. (이 속성은 네이티브 Batch Python SDK에서 사용할 수 없습니다.)

기본적으로 이 속성은 1로 설정되지만 작업의 처리량을 개선하도록 더 높게 설정할 수 있습니다. 네트워크 대역폭 및 일부 CPU 성능을 사용하여 향상된 처리량의 균형을 유지합니다. 작업 처리량은 `MaxDegreeOfParallelism` 또는 `threads`의 최대 100배까지 증가합니다. 실제로 동시 작업 수를 100 미만으로 설정해야 합니다.

 Batch 템플릿을 사용하여 Azure Batch CLI 확장은 사용 가능한 코어 수에 따라 자동으로 동시 실행 작업 수를 증가시키지만 이 속성은 CLI에서 구성할 수 없습니다.

### <a name="http-connection-limits"></a>HTTP 연결 제한

많은 작업을 추가하는 경우 동시 HTTP 연결 수가 많으면 Batch 클라이언트의 성능을 제한할 수 있습니다. 일부 Api는 HTTP 연결 수를 제한합니다. .NET API를 사용하여 개발하는 경우, 예를 들어 [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 속성은 기본적으로 2로 설정됩니다. 병렬 작업의 수에 근접하거나 해당 수보다 큰 수로 값을 늘리는 것이 좋습니다.

## <a name="example-batch-net"></a>예제: Batch .NET

다음 C# 코드 조각은 Batch .NET API를 사용하여 많은 수의 작업을 추가하는 경우에 구성하는 설정을 보여줍니다.

작업 처리량을 늘리려면 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)의 [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) 속성 값을 늘립니다. 다음은 그 예입니다.

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

[AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) 또는 [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) 메서드의 적절한 오버로드를 사용하여 작업(job)에 작업(task) 컬렉션을 추가합니다. 다음은 그 예입니다.

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>예제: Batch CLI 확장

[Batch CLI 템플릿](batch-cli-templates.md)을 포함하는 Azure Batch CLI 확장을 사용하여 [작업 팩터리](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)를 포함하는 작업 템플릿 JSON 파일을 만듭니다. 작업 팩터리는 단일 작업 정의에서 작업에 대한 관련된 작업의 컬렉션을 구성합니다.  

다음은 많은 수의 작업(이 경우 250,000개)이 있는 1차원 매개 변수 스윕 작업에 대한 샘플 작업 템플릿입니다. 작업 명령줄은 단순 `echo` 명령입니다.

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

템플릿을 사용하여 작업을 실행하려면 [Azure Batch CLI 템플릿 및 파일 전송 사용](batch-cli-templates.md)을 참조하세요.

## <a name="example-batch-python-sdk-extension"></a>예제: Batch Python SDK 확장

Azure Batch Python SDK 확장을 사용하려면 먼저 Python SDK 및 확장을 설치합니다.

```
pip install azure-batch
pip install azure-batch-extensions
```

`import azext.batch as batch`를 사용하여 패키지를 가져온 후 SDK 확장을 사용하여 `BatchExtensionsClient`를 설정합니다.

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

작업(task)의 컬렉션을 만들어 작업(job)에 추가합니다. 다음은 그 예입니다.

```python
tasks = list()
# Populate the list with your tasks
...
```

[task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations)을 사용하여 작업 컬렉션을 추가합니다. 동시 실행 작업 수를 늘리도록 `threads` 매개 변수를 설정합니다.

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

또한 Batch Python SDK 확장은 작업 팩터리에 대한 JSON 사양을 사용하여 작업(job)에 작업(task) 매개 변수 추가를 지원합니다. 예를 들어 이전 Batch CLI 템플릿 예제와 비슷한 매개 변수 스윕용 작업 매개 변수를 구성합니다.

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

작업에 작업 매개 변수를 추가합니다. 동시 실행 작업 수를 늘리도록 `threads` 매개 변수를 설정합니다.

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>다음 단계

- [Batch CLI 템플릿](batch-cli-templates.md)이 포함된 Azure Batch CLI 확장 사용에 대해 자세히 알아봅니다.
- [Batch Python SDK 확장](https://pypi.org/project/azure-batch-extensions/)에 대해 자세히 알아봅니다.
- [Azure Batch에 대한 모범 사례를](best-practices.md)참조하세요.
