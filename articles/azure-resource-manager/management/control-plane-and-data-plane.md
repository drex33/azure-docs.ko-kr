---
title: 컨트롤 플레인 및 데이터 평면 작업
description: 컨트롤 플레인과 데이터 평면 작업의 차이점을 설명합니다. 컨트롤 플레인 작업은 Azure Resource Manager가 처리합니다. 데이터 평면 작업은 서비스가 처리합니다.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: c5e72693c751086f17958c39c96d12624e478b99
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893408"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure 컨트롤 플레인과 데이터 평면

Azure 작업은 컨트롤 플레인과 데이터 평면이라는 두 가지 범주로 나눌 수 있습니다. 이 문서에서는 두 가지 유형의 작업 간의 차이점을 설명합니다.

컨트롤 플레인은 구독의 리소스를 관리하는 데 사용됩니다. 데이터 평면은 리소스 종류 인스턴스에서 노출되는 기능을 사용하는 데 사용됩니다.

예를 들면 다음과 같습니다.

* 컨트롤 플레인을 통해 가상 머신을 만듭니다. 가상 머신을 만든 후에는 RDP(원격 데스크톱 프로토콜)와 같은 데이터 평면 작업을 통해 가상 머신과 상호 작용합니다.

* 컨트롤 플레인을 통해 스토리지 계정을 만듭니다. 데이터 평면을 사용하여 스토리지 계정에서 데이터를 읽고 씁니다.

* 컨트롤 플레인을 통해 Cosmos 데이터베이스를 만듭니다. Cosmos 데이터베이스의 데이터를 쿼리하려면 데이터 평면을 사용합니다.

## <a name="control-plane"></a>제어 평면

컨트롤 플레인 작업에 대한 모든 요청은 Azure Resource Manager URL로 전송됩니다. 해당 URL은 Azure 환경에 따라 다릅니다.

* Azure 글로벌의 경우 URL은 `https://management.azure.com`입니다.
* Azure Government의 경우 URL은 `https://management.usgovcloudapi.net/`입니다.
* Azure 독일의 경우 URL은 `https://management.microsoftazure.de/`입니다.
* Microsoft Azure 중국 21Vianet의 경우 URL은 `https://management.chinacloudapi.cn`입니다.

어떤 작업이 Azure Resource Manager URL을 사용하는지 검색하려면 [Azure REST API](/rest/api/azure/)를 참조하세요. 예를 들어, MySQL에 대한 [만들기 또는 업데이트 작업](/rest/api/mysql/singleserver/databases/create-or-update)은 요청 URL이 다음과 같기 때문에 컨트롤 플레인 작업입니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager는 모든 컨트롤 플레인 요청을 처리합니다. 다음과 같이 리소스를 관리하기 위해 구현한 Azure 기능을 자동으로 적용합니다.

* [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [관리 잠금](lock-resources.md)
* [활동 로그](../../azure-monitor/essentials/activity-log.md)

요청을 인증한 후 Azure Resource Manager는 컨트롤 플레인을 리소스 공급자에게 보내고, 리소스 공급자가 작업을 완료합니다.

컨트롤 플레인에는 요청을 처리하는 두 가지 시나리오(“녹색 필드”와 “갈색 필드”)가 포함되어 있습니다. 녹색 필드는 새 리소스를 나타냅니다. 갈색 필드는 기존 리소스를 나타냅니다. 리소스를 배포할 때 Azure Resource Manager는 새 리소스를 만들 시기와 기존 리소스를 업데이트할 시기를 파악합니다. 따라서 동일한 리소스가 만들어질 염려가 없습니다.

## <a name="data-plane"></a>데이터 평면

데이터 평면 작업에 대 한 요청은 사용자 인스턴스와 관련 된 끝점으로 전송 됩니다. 예를 들어 Cognitive Services의 [언어 검색 작업](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md)은 요청 URL이 다음과 같기 때문에 데이터 평면 작업입니다.

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

데이터 평면 작업은 REST API로 제한되지 않습니다. 가상 컴퓨터 또는 데이터베이스 서버에 로그인 하는 등의 다른 자격 증명이 필요할 수 있습니다.

관리 및 관리를 적용 하는 기능은 데이터 평면 작업에 적용 되지 않을 수 있습니다. 사용자가 솔루션과 상호 작용하는 다른 방법을 고려해야 합니다. 예를 들어 사용자가 데이터베이스를 삭제하지 못하도록 하는 잠금은 사용자가 쿼리를 통해 데이터를 삭제하는 것을 막지 않습니다.

일부 정책을 사용하여 데이터 평면 작업을 제어할 수 있습니다. 자세한 내용은 [Azure Policy의 리소스 공급자 모드(미리 보기)](../../governance/policy/concepts/definition-structure.md#resource-provider-modes)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager에 대한 개요는 [Azure Resource Manager란?](overview.md)을 참조하세요.

* 새 리소스 및 기존 리소스에 대 한 정책 정의의 영향에 대해 자세히 알아보려면 [새 Azure Policy 정의의 영향 평가](../../governance/policy/concepts/evaluate-impact.md)를 참조 하세요.
