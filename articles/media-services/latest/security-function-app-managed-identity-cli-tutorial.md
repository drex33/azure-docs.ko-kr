---
title: Azure Function 앱에 Media Services 계정에 대한 액세스 권한 부여
description: 방송 스튜디오용 "On Air" 사인을 만들려고 한다고 가정합니다. Media Services API를 사용하여 Media Services 라이브 이벤트가 실행되는 시기를 확인할 수 있지만, 임베디드 디바이스에서 호출하기 어려울 수 있습니다. 이런 경우 Azure Functions를 사용하여 임베디드 디바이스의 HTTP API를 노출할 수 있습니다. 그런 다음, Azure Functions에서 Media Services를 호출하여 라이브 이벤트의 상태를 확보할 수 있습니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: inhenkel
ms.openlocfilehash: dc05d6488978004eebee68b901214ab71f0fffd4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656648"
---
# <a name="tutorial-give-an-azure-function-app-access-to-a-media-services-account"></a>자습서: Azure Function 앱에 Media Services 계정에 대한 액세스 권한 부여

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

방송 스튜디오에서 "On Air" 상태임을 애플리케이션 또는 웹 사이트 방문자에게 알리려고 한다고 가정합니다. Media Services API를 사용하여 Media Services 라이브 이벤트가 실행되는 시기를 확인할 수 있지만, 임베디드 디바이스에서 호출하기 어려울 수 있습니다. 이런 경우 Azure Functions를 사용하여 임베디드 디바이스의 HTTP API를 노출할 수 있습니다. 그런 다음, Azure Functions에서 Media Services를 호출하여 라이브 이벤트의 상태를 확보할 수 있습니다.

:::image type="content" source="media/diagrams/managed-identities-scenario-function-app-access-media-services-account.svg" alt-text="함수 앱에 Media Services 계정에 대한 액세스를 제공하는 관리 ID":::

이 자습서에서는 2020-05-01 Media Services API를 사용합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

이 문서의 명령을 사용하려면 먼저 사용하려는 구독에 로그인해야 합니다.

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>구독 설정

이 명령을 사용하여 작업할 구독을 설정합니다.

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="prerequisites"></a>사전 요구 사항

> [!IMPORTANT]
> 이 자습서를 시도하기 전에 [명령줄에서 Azure에 C# 함수 만들기 빠른 시작](../../azure-functions/create-first-function-cli-csharp.md)을 수행하는 것이 좋습니다.  그 곳에 포함된 설정 단계가 여기에 필요한 단계와 동일하기 때문입니다.  또한 이 자습서의 기반이 되는 간단한 예제를 작업할 수 있는 기회도 있습니다.

## <a name="resource-names"></a>리소스 이름

시작하기 전에 만들려는 리소스의 이름을 결정합니다.  하나의 집합으로 쉽게 식별할 수 있어야 합니다. 특히 테스트를 마친 후에 사용할 계획이 없다면 더 필요합니다. 명명 규칙은 다양한 리소스 유형에 따라 다르므로 모두 소문자를 사용하는 것이 가장 좋습니다. 예를 들어, 리소스 그룹 이름은 "mediatest1rg", 스토리지 계정 이름은 "mediatest1stor"를 사용합니다. 이 문서의 각 단계에 동일한 이름을 사용합니다.

아래 명령에서 이러한 이름이 참조된 것을 볼 수 있습니다.  필요한 리소스의 이름은 다음과 같습니다.

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-region
- your-function-name: "OnAir" 사용
- your-live-event-name: "live1" 사용
- your-ip-address: "0.0.0./32" 사용

> [!NOTE]
> 위의 하이픈은 안내 단어를 구분하는 데만 사용됩니다. Azure 서비스에서 리소스 이름 지정이 불일치하기 때문에 리소스 이름을 지정할 때는 하이픈을 사용하지 마십시오.<br/><br/>
> 00000000-0000-0000-0000000000으로 표시되는 것은 리소스의 고유 식별자입니다.  이 값은 일반적으로 JSON 응답에서 반환됩니다. 또한 JSON 응답을 복사하여 메모장이나 기타 텍스트 편집기에서 붙여넣는 것이 좋습니다. 이러한 응답에는 이후 CLI 명령에 필요한 값이 포함되기 때문입니다.<br/><br/>
> 또한 지역 이름은 사용자가 만들지 않습니다.  지역 이름은 Azure에서 결정합니다.

### <a name="list-azure-regions"></a>Azure 지역 나열

사용할 실제 지역 이름을 잘 모르는 경우 다음 명령을 사용하여 목록을 표시합니다.

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>시퀀스

아래의 각 단계는 특정 순서로 수행됩니다. JSON 응답의 값 하나 이상이 시퀀스의 다음 단계에서 사용되기 때문입니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

직접 만들 리소스는 리소스 그룹에 속해야 합니다. 먼저 리소스 그룹을 만듭니다. Media Services 계정 만들기 단계 및 후속 단계에 `your-resource-group-name`을 사용합니다.

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Storage 계정 만들기

직접 만들 Media Services 계정에는 스토리지 계정이 연결되어 있어야 합니다. 먼저 Media Services 계정에 대한 스토리지 계정을 만듭니다. 후속 단계에 `your-storage-account-name`을 사용합니다.

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Media Services 계정 만들기

이제 Media Services 계정을 만듭니다. `를 찾아봅니다.

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-cli.md)]

## <a name="set-up-the-azure-function"></a>Azure Function 설정

이 섹션에서는 Azure Function을 설정합니다.

### <a name="get-the-code"></a>코드 가져오기

Azure Functions를 사용하여 함수 프로젝트를 만들고 HTTP 템플릿에서 코드를 검색합니다.

```azurecli-interactive
func init MediaServicesLiveMonitor –dotnet
```

### <a name="change-directory"></a>디렉터리 변경

작업 디렉터리를 프로젝트 디렉터리로 변경해야 합니다.  그렇지 않으면 오류가 발생합니다.

```azurecli-interactive
cd .\MediaServicesLiveMonitor\
```

### <a name="name-your-function"></a>함수 이름 지정

```azurecli-interactive
func new --name OnAir --template "HTTP trigger" --authlevel "anonymous"
```

## <a name="configure-the-functions-project"></a>함수 프로젝트 구성

### <a name="install-media-services-and-other-extensions"></a>Media Services 및 기타 확장 설치

터미널 창에서 dotnet add package 명령을 실행하여 프로젝트에 필요한 확장 패키지를 설치합니다. 다음 명령은 Media Services 및 Azure ID 패키지를 설치합니다.

```bash
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

### <a name="edit-the-onaircs-code"></a>OnAir.cs 코드 편집

`OnAir.cs` 파일을 변경합니다. `subscriptionId`, `resourceGroup` 및 `mediaServicesAccountName` 변수를 이전에 결정한 값으로 변경합니다.

```csharp
using Azure.Core;
using Azure.Identity;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Rest;
using System.Threading.Tasks;

namespace MediaServicesLiveMonitor
{
    public static class OnAir
    {
        [FunctionName("OnAir")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            if (string.IsNullOrWhiteSpace(name))
            {
                return new BadRequestObjectResult("Missing 'name' URL parameter");
            }

            var credential = new ManagedIdentityCredential();
            var accessTokenRequest = await credential.GetTokenAsync(
                new TokenRequestContext(
                    scopes: new string[] { "https://management.core.windows.net" + "/.default" }
                    )
                );
            ServiceClientCredentials credentials = new TokenCredentials(accessTokenRequest.Token, "Bearer");

            var subscriptionId = "00000000-0000-0000-000000000000";                 // Update
            var resourceGroup = "<your-resource-group-name>";                       // Update
            var mediaServicesAccountName = "<your-media-services-account-name>";    // Update

            var mediaServices = new AzureMediaServicesClient(credentials)
            {
                SubscriptionId = subscriptionId
            };

            var liveEvent = await mediaServices.LiveEvents.GetAsync(resourceGroup, mediaServicesAccountName, name);

            if (liveEvent == null)
            {
                return new NotFoundResult();
            }

            return new OkObjectResult(liveEvent.ResourceState == LiveEventResourceState.Running ? "On air" : "Off air");
        }
    }
}
```

## <a name="create-the-function-app"></a>함수 앱 만들기

함수를 호스트할 함수 앱을 만듭니다. 이름은 이전에 다운로드한 `MediaServicesLiveMonitorApp`과 동일합니다.

```azurecli-interactive

az functionapp create --resource-group <your-resource-group-name> --consumption-plan-location your-region --runtime dotnet --functions-version 3 --name MediaServicesLiveMonitorApp --storage-account mediatest3store --assign-identity "[system]"

```

JSON 응답에서 `principalId`를 찾습니다.

```json
{
...  
"identity": {
//Note the principalId value for the following step
    "principalId": "00000000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-000000000000",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  }
...
```

## <a name="grant-the-function-app-access-to-the-media-services-account-resource"></a>함수 앱에 Media Services 계정 리소스에 대한 액세스 권한 부여

이 요청의 경우:

- `assignee`는 `az functionapp create`의 JSON 응답에 있는 `principalId`입니다.
- `scope`는 `az ams account create`의 JSON 응답에 있는 `id`입니다.  위의 예제 JSON 응답을 참조하세요.

```azurecli-interactive
az role assignment create --assignee 00000000-0000-0000-000000000000 --role "Media Services Account Administrator" --scope "/subscriptions/<the-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.Media/mediaservices/<your-media-services-account-name>"
```

## <a name="publish-the-function"></a>함수 게시

```azurecli-interactive
func azure functionapp publish MediaServicesLiveMonitorApp
```

## <a name="validation"></a>유효성 검사

브라우저에서 함수 URL로 이동합니다. 예:

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

라이브 이벤트가 아직 없으므로 404(찾을 수 없음) 오류가 반환됩니다.

### <a name="create-a-live-event"></a>라이브 이벤트 만들기

```azurecli-interactive
az ams live-event create --resource-group test3 --account-name mediatest3 --name live1 --streaming-protocol RTMP
```

브라우저에서 함수 URL로 이동합니다. 예:

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

이제 "Off Air"가 표시됩니다.

### <a name="start-the-live-event"></a>라이브 이벤트 시작

라이브 이벤트를 시작하면 함수는 "On Air"를 반환합니다.

```azurecli-interactive
az ams live-event start live1
```

이 함수는 누구나 액세스할 수 있습니다. Azure Function에 대한 액세스를 보호하고 "On Air" 조명을 연결하는 것은 이 문서의 범위에 포함되지 않습니다.

## <a name="clean-up-resources"></a>리소스 정리

직접 만든 리소스를 사용할 계획이 없으면 리소스 그룹을 삭제합니다.

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
