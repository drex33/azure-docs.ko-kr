---
title: '빠른 시작: PowerShell을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅'
description: Azure Event Grid를 사용하여 Azure Cache for Redis 이벤트를 구독하고, 이벤트를 Webhook에 보내고, 웹 애플리케이션에서 이벤트를 처리합니다.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 1eb11aec829e110e5d607988939d56e338a555a8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133042488"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure Cache for Redis 이벤트를 구독하고, 이벤트를 트리거하고, 결과를 볼 수 있습니다. 

일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 그러나 이 빠른 시작을 간소화하기 위해 메시지를 수집하고 표시하는 웹앱으로 이벤트를 보냅니다. 이 빠른 시작에서 설명하는 단계를 완료하면 이벤트 데이터가 웹앱으로 보내졌음을 알 수 있습니다.

## <a name="setup"></a>설치 프로그램

이 빠른 시작에서는 최신 버전의 Azure PowerShell을 실행해야 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성](/powershell/azure/install-Az-ps)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

```powershell
Connect-AzAccount
```

이 예제에서는 **westus2** 를 사용하고, 선택한 항목을 전체적으로 사용할 수 있게 변수에 저장합니다.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 개별 Azure 리소스로 배포되며 Azure 리소스 그룹으로 프로비저닝되어야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 **westus2** 위치에 **gridResourceGroup** 이라는 리소스 그룹을 만듭니다.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis 인스턴스 만들기 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
PowerShell에서 캐시 인스턴스를 만드는 방법에 대한 자세한 내용은 [Azure PowerShell 참조](/powershell/module/az.rediscache/new-azrediscache)를 참조하세요. 

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

토픽을 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 빠른 시작을 간소화하기 위해 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

`<your-site-name>`을 웹앱의 고유한 이름으로 바꿉니다. 웹앱 이름은 DNS 항목의 일부이므로 고유해야 합니다.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

현재 표시된 메시지가 없는 사이트가 표시되어야 합니다.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="빈 Event Grid 뷰어 사이트.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Azure Cache for Redis 이벤트에 대한 구독

이 단계에서는 토픽을 구독하여 Event Grid에 추적하려는 이벤트를 알립니다. 다음 예제에서는 사용자가 만든 캐시 인스턴스를 구독하고 웹앱의 URL을 이벤트 알림에 대한 엔드포인트로 전달합니다. 웹앱에 대한 엔드포인트는 접미사 `/api/updates/`를 포함해야 합니다.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 웹앱은 구독의 유효성을 검사하는 코드를 포함합니다.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid 뷰어.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Azure Cache for Redis에서 이벤트 트리거

이제 이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴 보겠습니다.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
PowerShell에서 가져오기에 대한 자세한 내용은 [Azure PowerShell 참조](/powershell/module/az.rediscache/import-azrediscache)를 참조하세요. 

이벤트를 트리거했고 Event Grid가 구독할 때 구성한 엔드포인트로 메시지를 보냈습니다. 웹앱을 확인하여 방금 전송한 이벤트를 봅니다.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>리소스 정리
이 Azure Cache for Redis 인스턴스 및 이벤트 구독을 계속 사용할 계획이라면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속 사용하지 않으려면 다음 명령을 사용하여 이 빠른 시작에서 만든 리소스를 삭제합니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

토픽과 이벤트 구독을 만드는 방법을 알아보았으므로, 이번에는 Azure Cache for Redis 이벤트 및 Event Grid로 수행할 수 있는 작업에 대해 자세히 알아보세요.

- [Azure Cache for Redis 이벤트에 대한 대응](cache-event-grid.md)
- [Event Grid 정보](../event-grid/overview.md)
