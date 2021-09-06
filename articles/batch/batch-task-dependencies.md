---
title: 작업을 실행할 작업 종속성 만들기
description: Azure Batch에서 MapReduce 스타일과 비슷한 빅 데이터 워크로드를 처리하기 위해 다른 작업의 완료에 종속된 작업을 만듭니다.
ms.topic: how-to
ms.date: 06/29/2021
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 0cd5e0bc97d37e4daf76bee66c8b4de4698b7aca
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126521"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>작업 의존 관계를 만들어 다른 작업에 종속된 작업 실행

Batch 태스크 종속성을 통해 하나 이상의 상위 태스크를 완료한 후에 컴퓨팅 노드에서 실행하기 위해 예약된 태스크를 만듭니다. 예를 들어, 별도의 병렬 태스크를 포함한 3D 동영상의 각 프레임을 렌더링하는 작업을 만들 수 있습니다. 최종 작업은 전체 프레임이 성공적으로 렌더링된 경우에만 렌더링된 프레임을 완전한 영화로 함께 병합합니다. 즉, 최종 작업은 이전 부모 작업에 따라 달라집니다.

태스크 종속성이 유용한 몇 가지 시나리오는 다음과 같습니다.

- 클라우드에서 MapReduce 스타일의 컴퓨팅 워크로드
- DAG(방향성 비순환 그래프)으로 표현할 수 있는 태스크의 데이터 처리 작업
- 프로세스를 미리 렌더링하거나 사후 렌더링하는 경우 다음 태스크를 시작하기 전에 각 태스크를 완료해야 합니다.
- 다운스트림 태스크가 업스트림 태스크의 출력에 따라 달라지는 다른 작업

기본적으로 종속 태스크는 상위 태스크가 완료된 후에만 실행하기 위해 예약됩니다. 필요에 따라 부모 작업이 실패하더라도 기본 동작을 재정의하고 종속 작업을 실행하도록 [종속성 동작](#dependency-actions)을 지정할 수 있습니다.

이 문서에서는 [Batch .NET](/dotnet/api/microsoft.azure.batch) 라이브러리를 사용하여 작업 종속성을 구성하는 방법을 설명합니다. 먼저는 작업에서 [태스크 종속성을 사용](#enable-task-dependencies)하는 방법을 보여 주고 [종속성을 사용하여 태스크를 구성](#create-dependent-tasks)하는 방법을 설명합니다. 또한 상위 태스크가 실패하는 경우 종속 태스크를 실행하는 종속성 작동을 지정하는 방법을 설명합니다. 마지막으로 Batch에서 지원되는 [종속성 시나리오](#dependency-scenarios)를 설명합니다.

## <a name="enable-task-dependencies"></a>태스크 종속성 사용

Batch 애플리케이션에서 태스크 종속성을 사용하려면 먼저 작업이 태스크 종속성을 사용하도록 구성해야 합니다. Batch .NET에서 해당 [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) 속성을 `true`로 설정하여 [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob)에서 이를 사용합니다.

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

앞의 코드 조각에서 “batchClient”는 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 클래스의 인스턴스입니다.

## <a name="create-dependent-tasks"></a>종속성 태스크 만들기

하나 이상의 상위 태스크를 완료하는 데 따라 달라지는 태스크를 만들려면 태스크가 다른 태스크"에 종속"되도록 지정할 수 있습니다. Batch .NET에서 [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) 클래스의 인스턴스를 사용하여 [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) 속성을 구성합니다.

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

이 코드 조각은 태스크 ID가 "Flowers"인 종속 태스크를 만듭니다. "Flowers" 태스크는 "Rain" 및 "Sun" 태스크에 따라 달라집니다. "Rain" 및 "Sun" 태스크를 성공적으로 완료한 후에 "Flowers" 태스크를 컴퓨팅 노드에서 실행되도록 예약합니다.

> [!NOTE]
> 기본적으로 태스크가 완료 상태이고 해당 종료 코드가 `0`인 경우 성공적으로 완료되었다고 간주됩니다. 즉, Batch .NET에서 [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state) 속성 값은 `Completed`이고, CloudTask의 [TaskExecutionInformation.ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) 속성 값은 `0`입니다. 변경 방법에 대해 알아보려면 [종속성 작업](#dependency-actions) 섹션을 참조하세요.

## <a name="dependency-scenarios"></a>종속성 시나리오

Azure Batch에서 사용할 수 있는 세 가지 기본 태스크 종속성 시나리오는 일대일, 일대다 및 태스크 ID 범위 종속성입니다. 이러한 세 가지 시나리오를 결합하여 네 번째 시나리오인 다대다를 제공할 수 있습니다.

| 시나리오&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 예제 | 그림 |
|:---:| --- | --- |
|  [일대일](#one-to-one) |*taskB* 가 *taskA* 에 종속됨 <p/> *taskB* 는 *taskA* 가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="일대일 작업 종속성 시나리오를 보여 주는 다이어그램"::: |
|  [일 대 다](#one-to-many) |*taskC* 는 *taskA* 및 *taskB* 에 종속됨 <p/> *taskC* 는 *taskA* 및 *taskB* 가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="일대다 작업 종속성 시나리오를 보여 주는 다이어그램"::: |
|  [태스크 ID 범위](#task-id-range) |*taskD* 가 태스크의 범위에 종속됨 <p/> *taskD* 는 ID *1*-*10* 을 가진 태스크가 성공적으로 완료될 때까지 실행하도록 예약되지 않음 |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="작업 ID 범위 태스크 종속성 시나리오를 보여 주는 다이어그램"::: |

> [!TIP]
> 태스크 C, D, E 및 F는 각각 태스크 A 및 B에 종속되는 경우 **다대다** 관계를 만들 수 있습니다. 예를 들어, 다운스트림 태스크가 여러 업스트림 태스크의 출력에 따라 달라지는 병렬화된 전처리 시나리오에서 유용합니다.
> 
> 이 섹션의 예제에서는 부모 태스크가 성공적으로 완료된 후에 종속 태스크가 실행됩니다. 이 동작은 종속 태스크에 대한 기본 동작입니다. 기본 동작을 재정의하는 [종속성 작업](#dependency-actions)을 지정하여 부모 작업에 실패한 후에 종속 작업을 실행할 수 있습니다.

### <a name="one-to-one"></a>일 대 일

일대일 관계의 경우 태스크는 상위 태스크를 성공적으로 완료했는지에 따라 달라집니다. 종속성을 만들려면 [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) 속성을 채우는 경우 단일 작업 ID를 [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) 고정 메서드에 제공합니다.

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>일대다

일대다 관계의 경우 태스크는 여러 상위 태스크를 완료했는지에 따라 달라집니다. 종속성을 만들려면 [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) 속성을 채우는 경우 특정 작업 ID 컬렉션을 [TaskDependencies.OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) 고정 메서드에 제공합니다.

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

> [!IMPORTANT]
> 부모 작업 ID의 결합된 길이가 64000자를 초과하면 종속 작업 만들기가 실패합니다. 많은 수의 부모 작업을 지정하려면 작업 ID 범위를 대신 사용하는 것이 좋습니다.

### <a name="task-id-range"></a>태스크 ID 범위

부모 작업의 범위에 대한 종속성에서 작업은 지정하는 범위 내에 있는 ID를 가진 작업을 완료했는지에 따라 달라집니다.

종속성을 만들려면 [CloudTask.DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) 속성을 채우는 경우 범위의 첫 번째 작업 ID와 마지막 작업 ID를 [TaskDependencies.OnIdRange](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) 고정 메서드에 제공합니다.

> [!IMPORTANT]
> 종속성에 대해 태스크 ID 범위를 사용할 경우 ID가 정수 값을 나타내는 태스크만 범위에 따라 선택됩니다. 예를 들어, 범위 `1..10`에서 태스크 `3` 및 `7`이 선택되지만 `5flamingoes`는 선택되지 않습니다.
>
> 범위 종속성을 평가할 때 앞에 오는 0은 의미가 없으므로 문자열 식별자가 `4`, `04` 및 `004`인 작업은 모두 범위 *내* 에 포함됩니다. 따라서 `4` 작업으로 처리되고, 처음 완료되는 작업에서 종속성이 충족됩니다.
>
> 종속 작업을 실행하려면 **Satisfy** 로 설정된 [종속성 작업](#dependency-actions)에 매핑되는 오류를 성공적으로 완료하거나 해결하여 범위에 있는 모든 작업이 종속성을 충족해야 합니다.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>종속성 작업

기본적으로 상위 태스크가 성공적으로 완료된 후에만 (일련의) 종속 태스크가 실행됩니다. 일부 시나리오에서는 상위 태스크가 실패하더라도 종속 태스크를 실행하려고 할 수 있습니다. 종속 작업을 실행할 수 있는지 여부를 나타내는 *종속성 작업* 을 지정하여 기본 동작을 재정의할 수 있습니다.

예를 들어, 종속 태스크는 데이터가 업스트림 태스크를 완료하도록 대기 중입니다. 업스트림 태스크가 실패할 경우 종속 태스크는 오래된 데이터를 사용하여 실행될 수 있습니다. 이 경우에 종속성 작업은 상위 태스크가 실패하더라도 종속 태스크를 실행할 수 있는지 여부를 지정할 수 있습니다.

종속성 작업은 상위 태스크에 대한 종료 조건을 기반으로 합니다. 다음과 같은 종료 조건에 대한 종속성 작업을 지정할 수 있습니다.

- 전처리 오류가 발생할 때
- 파일을 업로드 오류가 발생할 때 태스크가 **exitCodes** 또는 **exitCodeRanges** 를 통해 지정된 종료 코드로 인해 종료된 다음 파일 업로드 오류가 발생하면 해당 종료 코드로 지정된 작업이 우선적으로 적용됩니다.
- 태스크가 **ExitCodes** 속성으로 정의된 종료 코드로 인해 종료되는 경우
- 태스크가 **ExitCodeRanges** 속성에서 지정한 범위 내에서 실패한 종료 코드로 인해 종료되는 경우
- 기본적인 경우(태스크가 **ExitCodes** 또는 **ExitCodeRanges** 로 정의되지 않은 종료 코드로 인해 종료되거나 태스크가 전처리 오류와 함께 종료되고 **PreProcessingError** 속성이 설정되지 않았거나, 태스크가 파일 업로드 오류로 인해 실패하고 **FileUploadError** 속성이 설정되지 않은 경우) 

.NET의 경우 이러한 조건은 [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) 클래스의 속성으로 정의됩니다.

종속성 작업을 지정하려면 종료 조건에 대한 [ExitOptions.DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) 속성을 다음 중 하나로 설정합니다.

- **Satisfy**: 지정된 오류로 인해 상위 태스크를 종료하는 경우 종속 태스크를 실행할 수 있음을 나타냅니다.
- **Block**: 종속 태스크를 실행할 수 없음을 나타냅니다.

**DependencyAction** 속성에 대한 기본 설정은 종료 코드 0의 경우 **충족** 이고 다른 모든 종료 조건의 경우 **차단** 입니다.

다음 코드 조각에서는 상위 태스크에 대한 **DependencyAction** 속성을 설정합니다. 전처리 오류 또는 지정된 오류 코드로 인해 상위 태스크가 종료되면 종속 태스크가 차단됩니다. 상위 태스크가 다른 0이 아닌 오류로 인해 종료되면 종속 태스크를 실행할 수 있습니다.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>코드 샘플

GitHub의 [TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) 샘플 프로젝트는 다음을 보여 줍니다.

- 작업에 대한 태스크 종속성을 사용하는 방법
- 다른 태스크에 종속된 태스크를 만드는 방법
- 컴퓨팅 노드의 풀에서 해당 태스크를 실행하는 방법.

## <a name="next-steps"></a>다음 단계

- 컴퓨팅 노드에서 태스크를 실행하는 애플리케이션을 배포하고 버전을 관리하는 쉬운 방법을 제공하는 Batch의 [애플리케이션 패키지](batch-application-packages.md) 기능에 대해 알아봅니다.
- [작업 및 태스크에 대한 오류 검사](batch-job-task-error-checking.md)애 대해 알아봅니다.
