---
title: Azure Firewall 위협 인텔리전스 구성
description: Azure Firewall 정책의 위협 인텔리전스 기반 필터링을 구성하여 알려진 악성 IP 주소 및 도메인의 트래픽을 경고하고 거부하는 방법에 대해 알아 봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 819509e08a5604957f01452e5c6ec33c20de2b2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297490"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure Firewall 위협 인텔리전스 구성

Azure Firewall 정책에 위협 인텔리전스 기반 필터링을 구성하여 악성 IP 주소와 도메인에서 보내고 받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) microsoft 위협 인텔리전스를 구동 하 고 클라우드 용 microsoft Defender를 비롯 한 여러 서비스에서 사용 됩니다.<br>

위협 인텔리전스 기반 필터링을 구성한 경우 관련 규칙이 NAT 규칙, 네트워크 규칙 또는 애플리케이션 규칙보다 먼저 처리됩니다.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="위협 인텔리전스 정책":::

## <a name="threat-intelligence-mode"></a>위협 인텔리전스 모드

위협 인텔리전스를 아래 표에서 설명된 세 가지 모드 중 하나로 설정할 수 있습니다. 기본적으로 위협 인텔리전스 기반 필터링은 경고 모드에서 사용하도록 설정됩니다.

|Mode |Description  |
|---------|---------|
|`Off`     | 방화벽에는 위협 인텔리전스 기능을 사용할 수 없습니다. |
|`Alert only`     | 방화벽을 통과하여 알려진 악성 IP 주소 및 도메인에서 보내거나 받으려는 트래픽에 대해 신뢰도 높은 경고를 수신합니다. |
|`Alert and deny`     | 방화벽을 통과하여 알려진 악성 IP 주소 및 도메인에서 보내거나 받으려는 트래픽이 감지되면, 트래픽이 차단되고 신뢰도 높은 경고를 수신합니다. |

> [!NOTE]
> 위협 인텔리전스 모드는 부모 정책에서 자식 정책으로 상속됩니다. 자식 정책은 부모 정책과 동일하거나 더 높은 strict 모드로 구성되어야 합니다.

## <a name="allowlist-addresses"></a>허용 목록 주소

위협 인텔리전스는 가양성을 트리거하거나 실제로 유효한 트래픽을 차단할 수도 있습니다. 위협 인텔리전스가 지정된 주소, 범위 또는 서브넷을 전혀 필터링하지 않도록, 허용된 IP 주소 목록을 구성할 수 있습니다.  

![허용 목록 주소](media/threat-intelligence-settings/allow-list.png)

CSV 파일을 업로드하여 한 번에 여러 항목으로 허용 목록을 업데이트할 수 있습니다. CSV 파일에는 IP 주소 및 범위만 포함할 수 있습니다. 이 파일은 제목을 포함할 수 없습니다.

> [!NOTE]
> 위협 인텔리전스 허용 목록 주소는 부모 정책에서 자식 정책으로 상속됩니다. 부모 정책에 추가되는 모든 IP 주소 또는 범위는 모든 자식 정책에도 적용됩니다.

## <a name="logs"></a>로그

다음 로그 발췌는 악성 사이트로의 아웃바운드 트래픽에 대해 트리거된 규칙을 보여 줍니다.

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>테스트

- **아웃바운드 테스트** - 환경이 손상되었음을 의미하는 아웃바운드 트래픽 경고는 드물게 발생합니다. 아웃바운드 경고가 작동하는지 테스트하는 데 도움이 되도록 경고를 트리거하는 테스트 FQDN이 생성되었습니다. 아웃바운드 테스트에 **testmaliciousdomain.eastus.cloudapp.azure.com** 을 사용합니다.

- **인바운드 테스트** - DNAT 규칙이 방화벽에 구성된 경우 들어오는 트래픽에 대한 경고를 볼 수 있습니다. DNAT 규칙에서 특정 원본만 허용되고 트래픽이 거부되는 경우에도 마찬가지입니다. Azure Firewall은 알려진 모든 포트 스캐너에 대해 경고하지 않습니다. 악의적인 작업에도 참여하는 것으로 알려진 스캐너에만 경고합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Security 인텔리전스 보고서](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) 검토
