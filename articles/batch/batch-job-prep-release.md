---
title: 컴퓨팅 노드에서 작업을 준비 및 완료하는 태스크 만들기
description: 작업 수준 준비 태스크를 사용하여 Azure Batch 컴퓨팅 노드로의 데이터 전송을 최소화하고 작업 완료 시 태스크를 해제하여 노드를 정리합니다.
ms.topic: how-to
ms.date: 09/10/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 66be694cc05655973afac088066e95c6d7033bb2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659743"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Batch 컴퓨팅 노드에서 작업 준비 및 작업 릴리스 태스크 실행

 Azure Batch 작업은 태스크를 실행하기 전에 일종의 설정이 필요한 경우가 많습니다. 태스크가 완료되면 작업 후 유지 관리가 필요할 수도 있습니다. 예를 들어 일반적인 태스크 입력 데이터를 컴퓨팅 노드에 다운로드하거나 작업이 완료된 후 Azure Storage 태스크 출력 데이터를 업로드해야 할 수 있습니다. **작업 준비** 및 **작업 해제** 를 사용하면 이러한 작업을 수행할 수 있습니다.

## <a name="what-are-job-preparation-and-release-tasks"></a>작업 준비 및 해제 태스크에 대한 정의

작업 태스크가 실행되기 전에 하나 이상의 태스크를 실행하도록 예약된 모든 컴퓨팅 노드에서 작업 준비 태스크가 실행됩니다. 작업이 완료되면 하나 이상의 태스크를 실행한 풀의 각 노드에서 작업 릴리스 태스크가 실행됩니다. 일반 Batch 태스크처럼 작업 준비 또는 해제 태스크가 실행될 때 호출되는 명령줄을 지정할 수 있습니다.

작업 준비 및 해제 태스크는 파일([리소스 파일](/dotnet/api/microsoft.azure.batch.jobpreparationtask.resourcefiles)) 다운로드, 관리자 권한 실행, 사용자 지정 환경 변수, 최대 실행 기간, 재시도 횟수 및 파일 보존 시간과 같은 익숙한 Batch 태스크 기능을 제공합니다.

다음 섹션에서는 [Batch .NET](/dotnet/api/microsoft.azure.batch) 라이브러리에 있는 [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask)와 [JobReleaseTask](/dotnet/api/microsoft.azure.batch.jobreleasetask) 클래스를 사용하는 방법을 알아봅니다.

> [!TIP]
> 컴퓨팅 노드 풀이 작업 실행 간에 지속되고 여러 작업에서 사용되는 "공유 풀" 환경에서는 작업 준비 및 해제 태스크가 특히 유용합니다.

## <a name="when-to-use-job-preparation-and-release-tasks"></a>작업 준비 및 해제 태스크를 사용하는 시점

작업 준비 및 작업 해제 태스크가 적합한 경우는 다음과 같습니다.

- **일반적인 태스크 데이터 다운로드:** Batch 작업에는 작업의 태스크에 대한 입력으로 공통 데이터 집합이 필요한 경우가 많습니다. 예를 들어 일별 위험 분석 계산에서 시장 데이터는 작업 특정적이지만 작업의 모든 태스크에는 공통적입니다. 종종 수 기가바이트 크기인 시장 데이터는 각각의 컴퓨팅 노드에 한 번만 다운로드되어 노드에서 실행하는 모든 태스크에서 사용할 수 있어야 합니다. **작업 준비 태스크** 를 사용하여 작업의 다른 태스크를 실행하기 전에 각 노드에 이 데이터를 다운로드합니다.

- **작업 및 태스크 출력 삭제:** 풀의 컴퓨팅 노드가 작업 간에 해제되지 않는 "공유 풀" 환경에서는 실행 간에 작업 데이터를 삭제해야 할 수 있습니다. 노드의 디스크 공간을 절약하거나 조직의 보안 정책을 충족시킬 필요도 있습니다. 작업 **해제 태스크를** 사용하여 작업 준비 태스크에서 다운로드했거나 태스크 실행 중에 생성된 데이터를 삭제합니다.

- **로그 보존:** 태스크에서 생성하는 로그 파일의 복사본을 유지하거나 실패한 애플리케이션에서 생성할 수 있는 크래시 덤프 파일을 유지할 수 있습니다. 이러한 경우 **작업 릴리스 태스크를** 사용하여 이 데이터를 압축하고 [Azure Storage 계정에](accounts.md#azure-storage-accounts)업로드합니다.

## <a name="job-preparation-task"></a>작업 준비 태스크

작업에서 태스크를 실행하기 전에 Batch는 태스크를 실행하도록 예약된 각 컴퓨팅 노드에서 작업 준비 태스크를 실행합니다. 기본적으로 Batch는 노드에서 실행하도록 예약된 태스크를 실행하기 전에 작업 준비 태스크가 완료되기를 대기합니다. 그러나 서비스를 대기하지 않도록 구성할 수 있습니다. 노드가 다시 시작되면 작업 준비 태스크도 다시 실행되지만, 이 동작을 사용하지 않도록 설정할 수도 있습니다. 작업 준비 태스크와 작업 관리자 태스크가 구성된 작업이 있는 경우 다른 모든 태스크의 경우와 마찬가지로 작업 준비 태스크가 작업 관리자 태스크보다 먼저 실행됩니다. 작업 준비 태스크는 항상 먼저 실행됩니다.

작업 준비 태스크는 태스크를 실행하도록 예약된 노드에서만 실행됩니다. 이렇게 하면 노드에 태스크가 할당되지 않은 경우 준비 태스크가 불필요하게 실행되지 않습니다. 이는 작업에 대한 태스크 수가 풀의 노드 수보다 작은 경우에 발생할 수 있습니다. 이 방식은 [동시 태스크 실행](batch-parallel-node-tasks.md) 을 활성화할 때도 적용되며, 이는 태스크 개수가 가능한 총 동시 태스크 개수보다 작으면 노드 일부를 유휴 상태로 남겨둡니다.

> [!NOTE]
> JobPreparationTask는 각 작업을 시작할 때 실행되지만 StartTask는 먼저 컴퓨팅 노드를 풀과 조인하거나 다시 시작할 때만 실행된다는 점에서 [JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)는 [CloudPool.StartTask](/dotnet/api/microsoft.azure.batch.cloudpool.starttask)와 다릅니다.

## <a name="job-release-task"></a>작업 해제 태스크

작업이 완료된 것으로 표시되면 작업 릴리스 태스크는 하나 이상의 태스크를 실행한 풀의 각 노드에서 실행됩니다. 종료 요청을 발행하여 작업을 완료로 표시합니다. 이 요청은 작업 상태를 *종료로* 설정하고, 작업과 연결된 활성 또는 실행 중인 태스크를 종료하고, 작업 릴리스 태스크를 실행합니다. 작업은 *완료* 상태로 이동합니다.

> [!NOTE]
> 작업을 삭제해도 작업 릴리스 태스크가 실행됩니다. 그러나 작업이 이미 종료되었고 나중에 삭제되는 경우에는 해제 태스크가 다시 실행되지 않습니다.

작업 해제 태스크는 Batch 서비스에 의해 종료되기 전까지 최대 15분 동안 실행될 수 있습니다. 자세한 내용은 [REST API 참조 문서](/rest/api/batchservice/job/add#jobreleasetask)를 참조하세요.

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Batch .NET을 사용한 작업 준비 및 릴리스 태스크

작업 준비 태스크를 사용하려면 [JobPreparationTask](/dotnet/api/microsoft.azure.batch.jobpreparationtask) 개체를 작업의 [CloudJob.JobPreparationTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) 속성에 할당합니다. 마찬가지로 작업 릴리스 태스크를 사용하려면 [JobReleaseTask를](/dotnet/api/microsoft.azure.batch.jobreleasetask) 초기화하고 작업의 [CloudJob.JobReleaseTask](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)에 할당합니다.

이 코드 조각에서 `myBatchClient`는 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient)의 인스턴스이고 `myPool`은 Batch 계정에 있는 기존 풀입니다.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

위에서 설명했듯이 작업이 종료되거나 삭제될 때 해제 태스크가 실행됩니다. [JobOperations.TerminateJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.terminatejobasync)를 통해 작업을 종료합니다. [JobOperations.DeleteJobAsync](/dotnet/api/microsoft.azure.batch.joboperations.deletejobasync)를 통해 작업을 삭제합니다. 일반적으로 작업의 태스크들이 완료되거나 정의한 시간 제한에 도달했을 때 작업을 종료하거나 삭제합니다.

```csharp
// Terminate the job to mark it as completed; this will initiate the
// job release task on any node that executed job tasks. Note that the
// job release task is also executed when a job is deleted, so you don't
// have to call Terminate if you delete jobs after task completion.

await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>GitHub의 코드 샘플

작동 중인 작업 준비 및 해제 태스크를 보려면 GitHub의 [JobPrepRelease](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease) 샘플 프로젝트를 확인합니다. 이 콘솔 애플리케이션은 다음을 수행합니다.

1. 노드가 두 개인 풀을 만듭니다.
1. 작업 준비, 릴리스 및 표준 태스크를 사용하여 작업을 만듭니다.
1. 먼저 노드 "공유" 디렉터리의 텍스트 파일에 노드 ID를 기록하는 작업 준비 태스크를 실행합니다.
1. 동일한 텍스트 파일에 해당 태스크 ID를 기록하는 각 노드에서 태스크를 실행합니다.
1. 모든 태스크가 완료되면(또는 시간 제한에 도달하면) 콘솔에 각 노드의 텍스트 파일에 있는 내용을 인쇄합니다.
1. 작업이 완료되면 작업 릴리스 태스크를 실행하여 노드에서 파일을 삭제합니다.
1. 실행된 각 노드에 대한 작업 준비 및 릴리스 태스크의 종료 코드를 인쇄합니다.
1. 실행을 일시 중지하여 작업 및/또는 풀 삭제를 확인합니다.

샘플 애플리케이션에서 출력은 다음과 비슷합니다.

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> 새 풀에 있는 노드의 변수 생성 및 시작 시간으로 인해(일부 노드는 다른 노드 전에 태스크에 대해 준비됨) 다른 출력이 표시될 수 있습니다. 특히, 태스크가 신속하게 완료되기 때문에 풀의 노드 중 하나에서 작업의 태스크를 모두 실행할 수 있습니다. 이러한 경우 작업을 실행하지 않은 노드에는 작업 준비 및 릴리스 태스크가 존재하지 않습니다.

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Azure 포털에서 작업 준비 및 해제 태스크 검사

[Azure Portal](https://portal.azure.com) 사용하여 작업 및 해당 태스크의 속성을 볼 수 있습니다. 샘플 애플리케이션을 실행한 후 작업의 태스크에 의해 수정된 공유 텍스트 파일을 다운로드할 수도 있습니다.

아래 스크린샷은 Azure Portal **준비 작업 블레이드를** 보여줍니다. 작업이 완료되었지만 작업과 풀을 삭제하기 전에 *JobPrepReleaseSampleJob* 속성으로 이동하여 **준비 태스크** 또는 **해제 태스크** 를 클릭하여 그 속성을 확인합니다.

:::image type="content" source="media/batch-job-prep-release/portal-jobprep-01.png" alt-text="Azure Portal 작업 준비 태스크 속성을 보여주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

- [작업 및 태스크에 대한 오류 검사](batch-job-task-error-checking.md)애 대해 알아봅니다.
- [애플리케이션 패키지를](batch-application-packages.md) 사용하여 태스크 실행을 위해 Batch 컴퓨팅 노드를 준비하는 방법을 알아봅니다.
- 데이터 및 [애플리케이션을 Batch 컴퓨팅 노드에 복사하는](batch-applications-to-pool-nodes.md)다양한 방법을 살펴봅니다.
- Azure Batch 파일 [규칙 라이브러리를](batch-task-output.md#use-the-batch-file-conventions-library-for-net) 사용하여 로그 및 기타 작업 및 태스크 출력 데이터를 유지하는 방법을 알아봅니다.
