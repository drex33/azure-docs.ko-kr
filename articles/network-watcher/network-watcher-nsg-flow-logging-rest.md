---
title: NSG 흐름 로그 관리 - Azure REST API
titleSuffix: Azure Network Watcher
description: 이 페이지에서는 REST API를 사용하여 Azure Network Watcher의 네트워크 보안 그룹 흐름 로그를 관리하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: cede3018f8922c6771f81470a714eed430cd5cdf
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291875"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>REST API를 사용하여 네트워크 보안 그룹 흐름 로그 구성

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

네트워크 보안 그룹 흐름 로그는 네트워크 보안 그룹을 통해 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있는 Network Watcher의 기능입니다. 이러한 흐름 로그는 json 형식으로 작성되고 트래픽이 허용되거나 거부된 경우 각 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개의 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)를 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

PowerShell을 사용하여 REST API를 호출하는 데 ARMclient가 사용됩니다. ARMClient는 [Chocolatey의 ARMClient](https://chocolatey.org/packages/ARMClient)에서 chocolatey에 있습니다. NSG 흐름 로그 REST API에 대한 자세한 사양은 [여기](/rest/api/network-watcher/flowlogs)에서 찾을 수 있습니다. 

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다.

> [!Important]
> Network Watcher REST API 호출의 경우 요청 URI에 있는 리소스 그룹 이름은 진단 작업이 수행되는 리소스가 아니라, Network Watcher를 포함하는 리소스 그룹입니다.

## <a name="scenario"></a>시나리오

이 문서에서 다루는 시나리오는 REST API를 사용하여 흐름 로그를 사용하거나 사용하지 않도록 설정하고 쿼리하는 방법을 보여줍니다. 네트워크 보안 그룹 흐름 로깅에 대한 자세한 내용은 [네트워크 보안 그룹 흐름 로깅 - 개요](network-watcher-nsg-flow-logging-overview.md)를 참조하세요.

이 시나리오에서는 다음을 수행합니다.

* 흐름 로그 사용(버전 2)
* 흐름 로그를 사용하지 않도록 설정
* 흐름 로그 상태 쿼리

## <a name="log-in-with-armclient"></a>ARMClient에 로그인

Azure 자격 증명으로 armclient에 로그인합니다.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Insights 공급자 등록

흐름 로깅이 성공적으로 작동하기 위해서 **Microsoft.Insights** 공급자를 등록해야 합니다. **Microsoft.Insights** 공급자가 등록되어 있는지 확실하지 않은 경우 다음 스크립트를 실행합니다.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>네트워크 보안 그룹 흐름 로그 사용

다음 예제에서는 흐름 로그 버전 2를 사용하도록 설정하는 명령이 표시됩니다. 버전 1의 경우 'version' 필드를 '1'로 바꿉니다.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

이전 예제에서 반환한 응답은 다음과 같습니다.

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```
> [!NOTE]
> - 위에서 사용된 API [Network Watchers - 흐름 로그 구성 설정](/rest/api/network-watcher/network-watchers/set-flow-log-configuration)은 오래되었으며 곧 사용되지 않을 수 있습니다.
> - 대신 새 [흐름 로그 - 만들기 또는 업데이트](/rest/api/network-watcher/flow-logs/create-or-update) REST API를 사용하는 것이 좋습니다.

## <a name="disable-network-security-group-flow-logs"></a>네트워크 보안 그룹 흐름 로그를 사용하지 않도록 설정

다음 예제를 사용하여 흐름 로그를 사용하지 않도록 설정합니다. 호출은 흐름 로그를 사용하도록 설정하는 것과 같습니다(단, 활성화된 속성에 대해 **false** 가 설정된 경우 제외).

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

이전 예제에서 반환한 응답은 다음과 같습니다.

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

> [!NOTE]
> - 위에서 사용된 API [Network Watchers - 흐름 로그 구성 설정](/rest/api/network-watcher/network-watchers/set-flow-log-configuration)은 오래되었으며 곧 사용되지 않을 수 있습니다.
> - 새 [흐름 로그 - 만들기 또는 업데이트](/rest/api/network-watcher/flow-logs/create-or-update) REST API를 사용하여 흐름 로그를 사용하지 않도록 설정하고 [흐름 로그 - 삭제](/rest/api/network-watcher/flow-logs/delete)를 사용하여 흐름 로그 리소스를 삭제하는 것이 좋습니다.

## <a name="query-flow-logs"></a>흐름 로그 쿼리

다음 REST 호출은 네트워크 보안 그룹에서 흐름 로그의 상태를 쿼리합니다.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

다음은 반환된 응답의 예제입니다.

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

> [!NOTE]
> - 위에서 사용된 API [Network Watchers - 로그 상태 가져오기](/rest/api/network-watcher/network-watchers/get-flow-log-status)에는 Network Watcher의 리소스 그룹에 추가 "리더" 권한이 필요합니다. 또한 이 API는 오래되었으며 곧 사용되지 않을 수 있습니다.
> - 대신 새 [흐름 로그 - 가져오기](/rest/api/network-watcher/flow-logs/get) REST API를 사용하는 것이 좋습니다.

## <a name="download-a-flow-log"></a>흐름 로그 다운로드

흐름 로그의 스토리지 위치를 만들 때 정의합니다. 스토리지 계정에 저장되는 이러한 흐름 로그에 액세스하는 편리한 도구는 Microsoft Azure Storage Explorer이며 https://storageexplorer.com/에서 다운로드할 수 있습니다.

스토리지 계정이 지정되어 있으면 패킷 캡처 파일은 다음 위치에서 스토리지 계정에 저장됩니다.

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>다음 단계

[PowerBI를 사용하여 NSG 흐름 로그를 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)하는 방법 알아보기

[오픈 소스 도구를 사용하여 NSG 흐름 로그를 시각화](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)하는 방법 알아보기