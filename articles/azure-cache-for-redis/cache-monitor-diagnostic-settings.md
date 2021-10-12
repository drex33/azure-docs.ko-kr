---
title: 진단 설정을 사용하여 Azure Cache for Redis 데이터 모니터링
titleSuffix: Azure Cache for Redis
description: 진단 설정을 사용하여 Azure Cache for Redis 연결된 IP 주소를 모니터링하는 방법을 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: e62215649a79f16fcb9cbfc20cfe16bc98f2251f
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812666"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>진단 설정을 사용하여 Azure Cache for Redis 데이터 모니터링

Azure의 진단 설정은 리소스 로그를 수집하는 데 사용됩니다. Azure 리소스 로그를 리소스에서 내보내고, 해당 리소스의 작업에 대한 풍부하고 빈도 높은 데이터를 제공합니다. 이러한 로그는 요청당 캡처되며 "데이터 평면 로그"라고도 합니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다.

Azure Cache for Redis Azure 진단 설정을 사용하여 캐시에 대한 모든 클라이언트 연결에 대한 정보를 기록합니다. 이 진단 설정을 로깅한 다음 분석하면 캐시에 연결하는 사용자와 해당 연결의 타임스탬프를 이해하는 데 도움이 됩니다. 이 데이터는 보안 위반의 범위를 식별하고 보안 감사를 위해 사용할 수 있습니다.

구성되면 캐시가 IP 주소로 들어오는 클라이언트 연결을 기록하기 시작합니다. 또한 각 고유 IP 주소에서 시작된 연결 수를 기록합니다. 로그는 누적되지 않습니다. 10초 간격으로 수행된 시점 스냅샷을 나타냅니다.

Azure Cache for Redis 인스턴스에 대한 진단 설정을 켜고 리소스 로그를 다음 대상으로 보낼 수 있습니다.

- **이벤트 허브** - 가상 네트워크를 사용하도록 설정하면 진단 설정이 이벤트 허브 리소스에 액세스할 수 없습니다. 이벤트 허브에서 신뢰할 수 있는 **Microsoft 서비스 이 방화벽을 무시하도록 허용?** 설정을 사용하도록 설정하여 이벤트 허브 리소스에 대한 액세스 권한을 부여합니다. 이벤트 허브는 캐시와 동일한 지역에 있어야 합니다.
- **Storage 계정** - 캐시와 동일한 지역에 있어야 합니다.

진단 요구 사항에 대한 자세한 내용은 [진단 설정을 참조하세요.](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD)

두 대상에 진단 로그를 보낼 때 스토리지 계정 및 이벤트 허브 사용량에 대한 일반 데이터 요금이 청구됩니다. Azure Cache for Redis 않은 Azure Monitor 요금이 청구됩니다.
자세한 가격 책정 정보는 [가격 책정을 Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Azure Portal을 통해 진단 설정 생성하기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Cache for Redis 계정으로 이동합니다. 왼쪽의 **모니터링 섹션** 아래에서 **진단 설정** 창을 엽니다. 그런 다음, **진단 설정 추가를** 선택합니다.

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="진단 선택":::

1. 진단 **설정** 창의 **범주 세부 정보** 에서 **ConnectedClientList를** 선택합니다.

   |범주  | 정의  | 키 속성   |
   |---------|---------|---------|
   |ConnectedClientList |  정기적으로 기록된 캐시에 연결된 클라이언트의 IP 주소 및 개수입니다. | `connectedClients` 및 중첩: `ip` , `count` , `privateLinkIpv6` |
  
1. 범주 세부 **정보를** 선택하면 원하는 대상으로 로그를 보냅니다. 오른쪽의 정보를 선택합니다.

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="리소스별 사용을 선택합니다":::

## <a name="create-diagnostic-setting-via-rest-api"></a> REST API를 통해 진단 설정 만들기

대화형 콘솔을 통해 진단 설정을 만드는 데 Azure Monitor REST API를 사용합니다. 자세한 내용은 [만들기 또는 업데이트](/rest/api/monitor/diagnostic-settings/create-or-update.md)를 참조하세요.

### <a name="request"></a>요청

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

### <a name="headers"></a>헤더

   | 매개 변수/헤더 | 값/설명 |
   |---------|---------|
   | name | 진단 설정의 이름입니다. |
   | resourceUri | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | api-version | 2017-05-01-미리 보기 |
   | 콘텐츠 형식 | application/json |

### <a name="body"></a>본문

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
      "logs": [
        {
          "category": "ConnectedClientList",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Azure CLI를 통해 진단 설정 만들기

명령을 `az monitor diagnostic-settings create` 사용하여 Azure CLI 진단 설정을 만듭니다. 이 명령 및 매개 변수 설명에 대한 자세한 내용은 [플랫폼 로그 및 메트릭을 다른 대상으로 보내는 진단 설정 만들기를 참조하세요.](../azure-monitor/essentials/diagnostic-settings.md)

```azurecli

az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace


```

## <a name="next-steps"></a>다음 단계

Azure Portal, CLI 또는 PowerShell을 사용하여 진단 설정을 만드는 방법에 대한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md) 문서를 참조하세요.
