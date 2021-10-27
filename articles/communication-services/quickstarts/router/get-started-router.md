---
title: 빠른 시작 - 큐 및 라우팅을 위한 작업 제출
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Services 리소스 내에서 작업 라우터 클라이언트, 배포 정책, 큐 및 작업을 만드는 방법을 알아봅니다.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/18/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ffad9a33c932300dbf52989536663215f8abb8f9
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164753"
---
# <a name="quickstart-submit-a-job-for-queuing-and-routing"></a>빠른 시작: 큐 및 라우팅을 위한 작업 제출

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

클라이언트를 설정하여 Azure Communication Services 작업 라우터를 시작합니다. 그런 다음, 큐, 정책, 작업자 및 작업과 같은 핵심 기능을 구성할 수 있습니다. 작업 라우터 개념에 대해 자세히 알아보려면 [작업 라우터 개념 설명서](../../concepts/router/concepts.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `JobRouterQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o JobRouterQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd JobRouterQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

프라이빗 미리 보기 동안 [GitHub](https://github.com/Azure/communication-preview/releases/tag/communication-job-router-net-v1.0.0-alpha.20211012.1)에서 SDK를 다운로드하세요.

> [!NOTE]
> SDK를 다운로드하려면 프라이빗 미리 보기 그룹의 구성원이어야 합니다.

다음 `using` 지시문을 **Program.cs** 위쪽에 추가하여 JobRouter 네임스페이스를 포함합니다.

```csharp
using Azure.Communication.JobRouter;
```

`Main` 함수 시그니처를 `async`로 업데이트하고 `Task`를 반환합니다.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-job-router-client"></a>작업 라우터 클라이언트 인증

작업 라우터 클라이언트는 Azure Portal의 Azure Communication Services 리소스에서 가져온 연결 문자열을 사용하여 인증할 수 있습니다.

```csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "your_connection_string";
var client = new RouterClient(connectionString);
```

## <a name="create-a-distribution-policy"></a>배포 정책 만들기

작업 라우터는 배포 정책을 사용하여 작업자에게 사용 가능한 작업에 대한 알림을 받는 방법과 알림에 대한 수명(예:**제품**)을 결정합니다. **ID**, **이름**, **offerTTL** 및 배포 **모드** 를 지정하여 정책을 만듭니다.

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);
```

## <a name="create-a-queue"></a>큐 만들기

작업은 논리적 큐로 구성됩니다. **ID**, **이름** 을 지정하여 큐를 만들고 위에서 만든 **배포 정책** 개체의 ID를 제공합니다.

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: distributionPolicy.Value.Id
);
```

## <a name="submit-a-job"></a>작업 제출
가장 빠른 시작 방법은 작업을 제출할 때 큐의 ID, 우선 순위 및 작업자 요구 사항을 지정하는 것입니다. 아래 예제에서 작업이 **XBOX 큐** 에 직접 제출됩니다. 여기서 해당 큐의 작업자는 이름 `Edmonton`과 일치하는 `Location` 레이블이 필요합니다.

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsChatChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelector: new List<LabelSelector>
    {
        new (
            key: "Location", 
            @operator: LabelOperator.Equal, 
            value: "Edmonton")
    });
```

## <a name="register-a-worker"></a>작업자 등록
**용량** 값, **레이블** 및 **채널 구성** 과 함께 이전에 만든 큐 ID를 참조해 작업자를 등록하여 `EdmontonWorker`가 'XBOX_Queue'에 할당되었는지 확인합니다.

```csharp
var edmontonWorker = await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new []{ queue.Value.Id },
    totalCapacity: 100,
    labels: new LabelCollection()
    {
        {"Location", "Edmonton"}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100)
    }
);
```

## <a name="query-the-worker-to-observe-the-job-offer"></a>작업자를 쿼리하여 작업 제안 관찰
작업 라우터 클라이언트 연결을 사용하여 작업자를 쿼리하고 ID에 대해 작업 ID를 확인합니다. 

```csharp
    // wait 500ms for the Job Router to offer the Job to the Worker
    Task.Delay(500).Wait();

    var result = await client.GetWorkerAsync(edmontonWorker.Value.Id);

    Console.WriteLine(
        $"Job ID: {job.Value.Id} offered to {edmontonWorker.Value.Id} " +
        $"should match Job ID attached to worker: {result.}");
```

`dotnet run`을 사용하여 애플리케이션을 실행하고 결과를 확인합니다.

```console
dotnet run

Job 6b83c5ad-5a92-4aa8-b986-3989c791be91 offered to EdmontonWorker should match Job ID from offer attached to worker: 6b83c5ad-5a92-4aa8-b986-3989c791be91
```

> [!NOTE]
> 애플리케이션을 두 번 이상 실행하면 매번 새 작업이 큐에 배치됩니다. 이로 인해 위의 코드를 실행할 때 만든 작업 이외의 작업이 작업자에 제공될 수 있습니다. 이렇게 하면 요청을 왜곡할 수 있기 때문에 매번 큐에서 작업을 제거하는 것이 좋습니다. 큐 또는 작업을 관리하려면 SDK 설명서를 참조하세요.