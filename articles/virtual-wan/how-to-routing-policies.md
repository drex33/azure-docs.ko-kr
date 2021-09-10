---
title: Virtual WAN 허브 라우팅 정책을 구성하는 방법
titleSuffix: Azure Virtual WAN
description: Virtual WAN 라우팅 정책 구성 방법 알아보기
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/01/2021
ms.author: wellee
ms.openlocfilehash: d61e6c3847d9ce64f9c3f17da1300b32a1a8e643
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567500"
---
# <a name="how-to-configure-virtual-wan-hub-routing-intent-and-routing-policies"></a>Virtual WAN 허브 라우팅 의도 및 라우팅 정책을 구성하는 방법

>[!NOTE] 
> 허브 라우팅 의도는 현재 제어된 퍼블릭 미리 보기로 제공됩니다. 
> 
> 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 일부 기능은 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 Microsoft Azure Preview에 대한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 
> 미리 보기에 대한 액세스 권한을 얻으려면 previewinterhub@microsoft.com에 연결하여 라우팅 의도를 구성하려는 Virtual WAN ID, 구독 ID 및 Azure 지역을 제공합니다. 기능 지원이 확인되면 업무 시간(월요일-금요일)으로 48시간 이내에 응답이 제공됩니다.

## <a name="background"></a>배경 

이제 Azure FIrewall 관리자를 사용하여 퍼블릭 및 프라이빗 트래픽에 대한 정책을 설정하는 고객은 라우팅 의도 및 라우팅 정책을 사용하여 훨씬 더 간단한 방법으로 네트워크를 설정할 수 있습니다.

라우팅 의도 및 라우팅 정책을 사용하면 Virtual WAN 허브가 인터넷 바인딩된 트래픽 및 프라이빗(지점 및 사이트 간, 사이트 간, ExpressRoute, Virtual WAN 허브 내의 네트워크 가상 어플라이언스, Virtual Network) 트래픽을 전달하는 방법을 지정할 수 있습니다. 라우팅 정책에는 인터넷 트래픽 정책 및 프라이빗 트래픽 라우팅 정책의 두 가지 유형이 있습니다. 각 Virtual WAN 허브에는 각각 다음 홉 리소스를 포함하는 최대 1개의 인터넷 트래픽 라우팅 정책과 1개의 프라이빗 트래픽 라우팅 정책이 있을 수 있습니다. 

프라이빗 트래픽은 분기 및 Virtual Network 주소 접두사를 모두 포함하지만, 라우팅 정책은 이러한 항목을 라우팅 의도 개념 내의 하나의 엔터티로 간주합니다.

>[!NOTE]
> Virtual WAN 허브 라우팅 정책의 제어된 퍼블릭 미리 보기에서는 Virtual WAN 허브가 동일한 지역에 있는 경우에만 Azure Firewall에서 허브 간 트래픽을 검사합니다. 


* **인터넷 트래픽 라우팅 정책**: 인터넷 트래픽 라우팅 정책을 Virtual WAN 허브에 대해 구성하는 경우 모든 분기(사용자 VPN(지점 및 사이트 간 VPN), 사이트 간 VPN 및 ExpressRoute) 및 해당 Virtual WAN 허브에 대한 Virtual Network 연결을 통해 인터넷 바인딩된 트래픽을 라우팅 정책의 일부로 지정된 Azure Firewall 리소스 또는 타사 보안 공급자로 전달합니다.
 

* **프라이빗 트래픽 라우팅 정책**: 프라이빗 트래픽 라우팅 정책이 Virtual WAN 허브에 대해 구성된 경우 허브 간 트래픽을 포함하여 **모든** 분기 트래픽 및 Virtual WAN 허브 내부 및 외부의 Virtual Network 트래픽이 프라이빗 트래픽 라우팅 정책에 지정된 다음 홉 Azure Firewall 리소스로 전달됩니다. 

    즉, Virtual WAN 허브에 대해 프라이빗 트래픽 라우팅 정책이 구성되어 있는 경우 모든 분기 간, 분기 및 가상 네트워크 간, 가상 네트워크 및 분기 간, 허브 간 트래픽은 Azure Firewall을 통해 전송됩니다.


## <a name="key-considerations"></a>주요 고려 사항
* 기존 사용자 지정 경로 테이블이 구성된 배포에서 또는 기본 경로 테이블에 고정 경로가 구성되어 있는 경우 라우팅 정책을 사용하도록 설정할 수 **없습니다**.
* 현재, 프라이빗 트래픽 라우팅 정책은 암호화된 ExpressRoute 연결이 있는 허브에서 지원되지 않습니다(ExpressRoute 프라이빗 연결을 통해 사이트 간 VPN 터널 실행). 
* Virtual WAN 허브 라우팅 정책의 제어된 퍼블릭 미리 보기에서는 Virtual WAN 허브가 동일한 지역에 있는 경우에만 Azure Firewall에서 허브 간 트래픽을 검사합니다.
* 라우팅 의도 및 라우팅 정책은 현재 **사전 요구 사항** 의 3단계에서 제공된 사용자 지정 포털 링크를 통해 구성해야 합니다. 라우팅 의도 및 정책은 Terraform, PowerShell 및 CLI를 통해 지원되지 않습니다. 

## <a name="prerequisites"></a>필수 구성 요소
1. Virtual WAN을 만듭니다. **동일한** 지역에 2개 이상의 가상 허브를 만들어야 합니다. 예를 들어, 미국 동부에서 두 개의 가상 허브를 사용하여 Virtual WAN을 만들 수 있습니다. 
2. 선택한 지역의 가상 허브에 Azure Firewall을 배포하여 Virtual WAN 허브를 보안 Virtual WAN 허브로 변환합니다. Virtual WAN 허브를 보안 Virtual WAN 허브로 변환하는 방법에 대한 자세한 내용은 이 [문서](howto-firewall.md)를 참조하세요.
3. **previewinterhub@microsoft.com** 에 연결하여 라우팅 정책을 구성하려는 **Virtual WAN 리소스 ID** 및 **Azure 가상 허브 지역** 을 제공합니다. Virtual WAN ID를 찾으려면 Azure Portal을 열고 Virtual WAN 리소스로 이동한 다음, 설정 > 속성 > 리소스 ID를 선택합니다. 예를 들면 다음과 같습니다. 
```
    /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>
```
4.  기능 지원이 확인되면 24-48시간 이내에 응답이 제공됩니다. 
5. 가상 허브에 defaultRouteTable에 추가했을 수 있는 사용자 지정 경로 테이블 또는 경로가 **없는지** 확인합니다. 기존 사용자 지정 경로 테이블이 구성된 배포에서 또는 기본 경로 테이블에 고정 경로가 구성되어 있는 경우 라우팅 정책을 사용하도록 설정할 수 **없습니다**. 

## <a name="configuring-routing-policies"></a>라우팅 정책 구성
1. **사전 요구 사항** 섹션의 3단계에서 확인 메일에 제공된 사용자 지정 포털 링크에서 라우팅 정책을 구성할 Virtual WAN 허브로 이동합니다.
1. 보안 아래에서 **보안 가상 허브 설정** 을 선택한 다음, **Azure Firewall Manager에서 이 보안 가상 허브에 대한 보안 공급자 및 경로 설정 관리** 를 선택합니다.
:::image type="content" source="./media/routing-policies/secured-hub-settings.png"alt-text="보안 허브 설정을 수정하는 방법을 보여 주는 스크린샷"lightbox="./media/routing-policies/secured-hub-settings.png":::
1. 메뉴에서 라우팅 정책을 구성하려는 허브를 선택합니다.
1. **설정** 에서 **보안 구성** 을 선택합니다.
1. 인터넷 트래픽 라우팅 정책을 구성하려면 **인터넷 트래픽** 드롭다운에서 **Azure Firewall** 또는 관련 인터넷 보안 공급자를 선택합니다. 그렇지 않은 경우 **없음** 을 선택합니다.
1. Azure Firewall을 통해 프라이빗 트래픽 라우팅 정책(분기 트래픽 및 Virtual Network 트래픽용)을 구성하려면 **프라이빗 트래픽** 드롭다운에서 **Azure Firewall** 을 선택합니다. 그렇지 않은 경우 **Azure Firewall 무시** 를 선택합니다.

:::image type="content" source="./media/routing-policies/configuring-intents.png"alt-text="라우팅 정책을 구성하는 방법을 보여 주는 스크린샷"lightbox="./media/routing-policies/configuring-intents.png":::

7. 프라이빗 트래픽 라우팅 정책을 구성하고 비 IANA RFC1918 접두사를 보급하는 분기 또는 가상 네트워크가 있는 경우 **프라이빗 트래픽 접두사** 를 선택하고 표시되는 텍스트 상자에 비 IANA RFC1918 접두사 범위를 지정합니다. **완료** 를 선택합니다. 

:::image type="content" source="./media/routing-policies/private-prefixes.png"alt-text="프라이빗 트래픽 접두사를 편집하는 방법을 보여 주는 스크린샷"lightbox="./media/routing-policies/private-prefixes.png":::

8. **허브 간** 을 선택하여 **사용** 으로 설정합니다. 이 옵션을 사용하도록 설정하면 라우팅 정책이 이 Virtual WAN 허브의 라우팅 의도에 적용됩니다. 
9. **저장** 을 선택합니다. 이 작업을 완료하는 데 10분 정도 걸릴 수 있습니다. 
10. 라우팅 정책을 구성하려는 다른 보안 Virtual WAN 허브에 대해 2-8단계를 반복합니다. 
 
## <a name="routing-policy-configuration-examples"></a>라우팅 정책 구성 예 

다음 섹션에서는 보안 Virtual WAN 허브에 라우팅 정책을 적용하는 두 가지 일반적인 고객 시나리오에 대해 설명합니다.

### <a name="all-virtual-wan-hubs-are-secured-deployed-with-azure-firewall"></a>모든 Virtual WAN 허브가 보호됩니다(Azure Firewall을 사용하여 배포됨).

이 시나리오에서는 모든 Virtual WAN 허브가 Azure Firewall과 함께 배포됩니다. 이 시나리오에서는 각 Virtual WAN 허브에서 인터넷 트래픽 라우팅 정책이나 프라이빗 트래픽 라우팅 정책 또는 두 정책을 모두 구성할 수 있습니다. 

:::image type="content" source="./media/routing-policies/two-secured-hubs-diagram.png"alt-text="두 개의 보안 허브가 있는 아키텍처를 보여 주는 스크린샷"lightbox="./media/routing-policies/two-secured-hubs-diagram.png":::

허브 1과 허브 2에 프라이빗 및 인터넷 트래픽 모두에 대한 라우팅 정책이 있는 다음 구성을 고려하세요. 

**허브 1 구성:**
* 다음 홉 허브 1 Azure Firewall이 있는 프라이빗 트래픽 정책
* 다음 홉 허브 1 Azure Firewall이 있는 인터넷 트래픽 정책 

**허브 2 구성:**
* 다음 홉 허브 2 Azure Firewall이 있는 프라이빗 트래픽 정책
* 다음 홉 허브 2 Azure Firewall이 있는 인터넷 트래픽 정책 

이러한 구성으로 인해 발생하는 트래픽 흐름은 다음과 같습니다. 

> [!NOTE]
> 기본 경로(0.0.0.0/0)가 허브 간에 전파되지 **않으므로** 인터넷 트래픽은 **로컬** Azure Firewall을 통해 송신되어야 합니다.

| 시작 |   대상 |  허브 1 VNet | 허브 1 분기 | 허브 2 VNet | 허브 2 분기| 인터넷|
| -------------- | -------- | ---------- | ---| ---| ---| ---|
| 허브 1 VNet     | &#8594;| 허브 1 AzFW |   허브 1 AzFW    | 허브 1 및 2 AzFW | 허브 1 및 2 AzFW | 허브 1 AzFW |
| 허브 1 분기   | &#8594;|  허브 1 AzFW  |   허브 1 AzFW    | 허브 1 및 2 AzFW | 허브 1 및 2 AzFW | 허브 1 AzFW|
| 허브 2 VNet     | &#8594;| 허브 1 및 2 AzFW |   허브 1 및 2 AzFW    | 허브 2 AzFW  | 허브 2 AzFW | 허브 2 AzFW|
| 허브 2 분기   | &#8594;|   허브 1 및 2 AzFW  |    허브 1 및 2 AzFW    | 허브 2 AzFW |  허브 2 AzFW | 허브 2 AzFW|


### <a name="mixture-of-secured-and-regular-virtual-wan-hubs"></a>보안 및 일반 Virtual WAN 허브의 혼합 

이 시나리오에서는 모든 Virtual WAN 허브가 Azure Firewall과 함께 배포되지는 않습니다. 이 시나리오에서는 보안 Virtual WAN 허브에서 인터넷 트래픽 라우팅 정책 또는 프라이빗 트래픽 라우팅 정책을 구성할 수 있습니다. 

허브 1(일반) 및 허브 2(보안)가 Virtual WAN에 배포되는 다음 구성을 고려하세요. 허브 2에는 프라이빗 및 인터넷 트래픽 모두에 대한 라우팅 정책이 있습니다. 

**허브 1 구성:**
* 해당 없음(허브가 Azure Firewall과 함께 배포되지 않은 경우 라우팅 정책을 구성할 수 없음)

**허브 2 구성:**
* 다음 홉 허브 2 Azure Firewall이 있는 프라이빗 트래픽 정책
* 다음 홉 허브 2 Azure Firewall이 있는 인터넷 트래픽 정책 


:::image type="content" source="./media/routing-policies/one-secured-one-normal-diagram.png"alt-text="하나의 보안 허브와 하나의 일반 허브가 있는 아키텍처를 보여 주는 스크린샷"lightbox="./media/routing-policies/one-secured-one-normal-diagram.png":::


 이러한 구성으로 인해 발생하는 트래픽 흐름은 다음과 같습니다. 허브 1에 연결된 분기 및 가상 네트워크는 기본 경로(0.0.0.0/0)가 허브 간에 전파되지 **않으므로** 허브의 Azure Firewall을 통해 인터넷에 액세스할 수 **없습니다**.

| 시작 |   대상 |  허브 1 VNet | 허브 1 분기 | 허브 2 VNet | 허브 2 분기| 인터넷 |
| -------------- | -------- | ---------- | ---| ---| ---| --- |
| 허브 1 VNet     | &#8594;| 직접 |   직접   | 허브 2 AzFW | 허브 2 AzFW | - |
| 허브 1 분기   | &#8594;|  직접 |   직접    | 허브,2 AzFW | 허브 2 AzFW | - |
| 허브 2 VNet     | &#8594;| 허브 2 AzFW |   허브 2 AzFW    | 허브 2 AzFW  | 허브 2 AzFW | 허브 2 AzFW|
| 허브 2 분기   | &#8594;|   허브 2 AzFW  |    허브 2 AzFW    | 허브 2 AzFW |  허브 2 AzFW | 허브 2 AzFW|


## <a name="troubleshooting"></a>문제 해결

다음 섹션에서는 Virtual WAN 허브에서 라우팅 정책을 구성할 때 발생하는 일반적인 문제에 대해 설명합니다.  아래 섹션을 읽고 문제를 해결하지 않은 경우 지원을 위해 previewinterhub@microsoft.com에 문의하세요. 업무 시간(월요일 ~ 금요일)으로 48시간 이내에 응답이 제공됩니다. 

### <a name="troubleshooting-configuration-issues"></a>구성 문제 해결
*  previewinterhub@microsoft.com에서 제어된 퍼블릭 미리 보기에 대한 액세스 권한이 구독 및 선택한 지역에 부여되었다는 확인을 받았는지 확인합니다. 미리 보기에 대한 액세스 권한이 부여되지 않은 상태에서 라우팅 정책을 구성할 수 **없습니다**.
* 배포에서 라우팅 정책 기능을 사용하도록 설정한 후에는 확인 메일의 일부로 제공된 사용자 지정 포털 링크 **만** 사용해야 합니다. PowerShell, CLI 또는 REST API 호출을 사용하여 Virtual WAN 배포를 관리하지 마세요.  여기에는 새 분기(사이트 간 VPN, 지점 및 사이트 간 VPN 또는 ExpressRoute) 연결 생성이 포함됩니다. 

>[!NOTE]
> Terraform을 사용하는 경우 라우팅 정책이 현재 지원되지 않습니다.

*  가상 허브에 사용자 지정 경로 테이블이나 defaultRouteTable의 정적 경로가 없는지 확인합니다. 구성된 사용자 지정 경로 테이블이 있거나 defaultRouteTable에 고정 경로가 있는 경우 Virtual WAN 허브의 Firewall Manager에서 **허브 간 사용** 을 선택할 수 **없습니다**. 

### <a name="troubleshooting-data-path"></a>데이터 경로 문제 해결 

* 현재, Azure Firewall을 사용한 허브 간 트래픽 검사는 **동일한** Azure 지역에 배포된 Virtual WAN 허브에 대해서만 수행할 수 있습니다. 
* 현재, 프라이빗 트래픽 라우팅 정책은 암호화된 ExpressRoute 연결이 있는 허브에서 지원되지 않습니다(ExpressRoute 프라이빗 연결을 통해 사이트 간 VPN 터널 실행). 
* DefaultRouteTable의 유효 경로를 확인하여 라우팅 정책이 제대로 적용되었는지 확인할 수 있습니다. 프라이빗 라우팅 정책이 구성된 경우 DefaultRouteTable에서 다음 홉 Azure Firewall을 사용하여 프라이빗 트래픽 접두사에 대한 경로를 확인해야 합니다. 인터넷 트래픽 라우팅 정책이 구성된 경우 DefaultRouteTable에서 다음 홉 Azure Firewall을 사용하여 기본 (0.0.0.0/0) 경로를 확인해야 합니다.

### <a name="troubleshooting-azure-firewall"></a>Azure Firewall 문제 해결

* 분기/가상 네트워크에서 비 [IANA RFC1918](https://datatracker.ietf.org/doc/html/rfc1918) 접두사를 사용하는 경우 Firewall Manager의 "프라이빗 접두사" 텍스트 상자에 해당 접두사를 지정했는지 확인합니다.
* Firewall Manager에서 **프라이빗 트래픽 접두사** 텍스트 상자의 일부로 비 RFC1918 주소를 지정한 경우 비 RFC1918 프라이빗 트래픽에 대해 SNAT를 사용하지 않도록 Firewall에서 SNAT 정책을 구성해야 할 수 있습니다. 자세한 내용은 다음 [문서](../firewall/snat-private-range.md)를 참조하세요. 
* Azure Firewall 로그를 구성하고 확인하여 네트워크 트래픽 문제를 해결하고 분석하는 데 도움을 얻을 수 있습니다. Azure Firewall에 대한 모니터링을 설정하는 방법에 대한 자세한 내용은 다음 [문서](../firewall/firewall-diagnostics.md)를 참조하세요. 다양한 유형의 Firewall 로그에 대한 개요는 [여기](../firewall/logs-and-metrics.md)에서 확인할 수 있습니다.
* Azure Firewall에 대한 자세한 내용은 [Azure Firewall 설명서](../firewall/overview.md)를 검토하세요.



## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="why-cant-i-edit-the-defaultroutetable-from-the-custom-portal-link-provided-by-previewinterhubmicrosoftcom"></a>previewinterhub@microsoft.com에서 제공하는 사용자 지정 포털 링크에서 defaultRouteTable을 편집할 수 없는 이유는 무엇인가요?

라우팅 정책의 제어된 퍼블릭 미리 보기의 일부로, Virtual WAN 허브 라우팅은 Firewall Manager에서 완전히 관리됩니다. 또한 라우팅 정책의 관리형 미리 보기는 사용자 지정 라우팅과 함께 지원되지 **않습니다**. 라우팅 정책을 사용한 사용자 지정 라우팅은 나중에 지원될 예정입니다. 

그러나 **유효 경로** 탭으로 이동하여 DefaultRouteTable의 유효 경로를 볼 수 있습니다.

### <a name="can-i-configure-a-routing-policy-for-private-traffic-and-also-send-internet-traffic-00000-via-a-network-virtual-appliance-in-a-spoke-virtual-network"></a>프라이빗 트래픽에 대한 라우팅 정책을 구성하고 스포크 Virtual Network의 네트워크 가상 어플라이언스를 통해 인터넷 트래픽(0.0.0.0/0)을 보낼 수 있나요?

이 시나리오는 제어된 퍼블릭 미리 보기에서 지원되지 않습니다. 그러나 previewinterhub@microsoft.com에 연결하여 이 시나리오의 구현에 관심이 있다는 사실을 알리세요. 

### <a name="does-the-default-route-00000-propagate-across-hubs"></a>기본 경로(0.0.0.0/0)가 허브 간에 전파되나요?

아니요. 현재, 분기 및 가상 네트워크는 분기 및 가상 네트워크가 연결된 Virtual WAN 허브 내에 배포된 Azure Firewall을 사용하여 인터넷으로 송신됩니다. 원격 허브의 Firewall을 통해 인터넷에 액세스하도록 연결을 구성할 수 없습니다.

### <a name="why-do-i-see-rfc1918-prefixes-advertised-to-my-on-premises-devices"></a>온-프레미스 디바이스에 보급된 RFC1918 접두사가 표시되는 이유는 무엇인가요?

프라이빗 트래픽 라우팅 정책을 구성하면 Virtual WAN 게이트웨이가 명시적 분기 및 Virtual Network 접두사 외에 기본 경로 테이블(RFC1918 접두사: 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)에 있는 고정 경로를 자동으로 보급합니다.

## <a name="next-steps"></a>다음 단계

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
