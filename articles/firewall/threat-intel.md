---
title: Azure Firewall 위협 인텔리전스 기반 필터링
description: 방화벽에서 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있도록 하기 위해 위협 인텔리전스 기반 필터링을 사용하도록 설정할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/04/2021
ms.author: victorh
ms.openlocfilehash: 90a988e302d3997156dc643f8bacbe6ed6ec786a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852658"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall 위협 인텔리전스 기반 필터링

방화벽에서 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있도록 하기 위해 위협 인텔리전스 기반 필터링을 사용하도록 설정할 수 있습니다. IP 주소 및 도메인은 Microsoft Cyber Security 팀을 비롯한 여러 원본을 포함하는 Microsoft Threat Intelligence 피드에서 제공됩니다. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="방화벽 위협 인텔리전스" border="false":::

위협 인텔리전스 기반 필터링을 사용하도록 설정한 경우 관련 규칙이 NAT 규칙, 네트워크 규칙 또는 애플리케이션 규칙보다 먼저 처리됩니다.

규칙이 트리거되는 경우에만 경고를 기록하도록 선택하거나 경고 및 거부 모드를 선택할 수 있습니다.

기본적으로 위협 인텔리전스 기반 필터링은 경고 모드에서 사용하도록 설정됩니다. 해당 지역에서 포털 인터페이스를 사용할 수 있을 때까지 이 기능을 끄거나 모드를 변경할 수 없습니다.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="위협 인텔리전스 기반 필터링 포털 인터페이스":::

## <a name="logs"></a>로그

다음 로그 발췌문에서는 트리거된 규칙을 보여줍니다.

```
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

- [Azure Firewall Log Analytics 샘플](./firewall-workbook.md)을 참조하세요.
- [Azure Firewall을 배포 및 구성](tutorial-firewall-deploy-portal.md)하는 방법에 대해 알아봅니다.
- [Microsoft Security 인텔리전스 보고서](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) 검토