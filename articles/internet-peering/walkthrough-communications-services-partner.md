---
title: Communication Services에 대한 Azure Internet 피어링 연습
titleSuffix: Azure
description: Communication Services에 대한 Azure Internet 피어링 연습
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: d17ae08067568098fffcdd443ba196c0023fbd42
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519348"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Communication Services에 대한 Azure Internet 피어링 연습

이 섹션에서는 Microsoft와 직접 상호 연결을 설정하기 위해 Communications Services 공급자가 따라야 하는 단계에 대해 설명합니다.

**Communications Services 공급자:** Communications Services 공급자는 통신 서비스(통신, 메시징, 회의 등)를 제공하고 통신 서비스 인프라(SBC/SIP 게이트웨이 등)를 Azure Communication Services 및 Microsoft Teams와 통합하려는 조직입니다. 

Azure Internet 피어링은 Communications Services 공급자를 지원하여 모든 에지 사이트(pop 위치)에서 Microsoft와 직접 상호 연결을 설정합니다. 모든 공용 에지 사이트의 목록은 [PeeringDB](https://www.peeringdb.com/net/694)에서 사용할 수 있습니다.

Azure Internet 피어링은 높은 품질 및 성능 중심 서비스를 보장하기 위해 통신 서비스에 대해 매우 안정적인 QoS(서비스 품질) 지원 상호 연결을 제공합니다.

## <a name="technical-requirements"></a>기술적인 요구 사항
Communication Services에 대한 직접 상호 연결을 설정하는 기술 요구 사항은 다음과 같습니다.
-   피어는 공용이어야 하는 고유한 ASN(익명 시스템 번호)을 제공해야 합니다.
-   로컬 중복성을 보장하려면 피어는 각 상호 연결 위치에 중복 상호 연결(PNI)이 있어야 합니다.
-   지역/메트로에서 사이트 오류 발생 시 장애 조치(failover)를 위해 피어에 지리적 중복성이 있어야 합니다.
-   높은 가용성과 신속한 수렴을 보장하기 위해 피어가 활성-활성 상태로 BGP 세션을 보유해야 하며, 기본 및 백업으로 프로비전해서는 안 됩니다.
-   피어링 라우터에서 피어링 회로에 대한 피어는 1:1 비율을 유지해야 하며 비율 제한이 적용되지 않습니다.
-   피어는 피어의 통신 서비스 엔드포인트(예: SBC)에서 사용하는 공개적으로 라우팅 가능한 IPv4 주소 공간을 제공하고 보급해야 합니다. 
-   피어는 보급된 각 서브넷에 있는 트래픽 및 엔드포인트의 클래스에 대한 세부 정보를 제공해야 합니다. 
-   피어는 BFD(양방향 전달 검색)를 통해 BGP를 실행하여 하위 두 번째 경로 일치성을 용이하게 합니다.
-   모든 통신 인프라 접두사는 Azure Portal에 등록되며 커뮤니티 문자열 8075:8007에 알려집니다.
-   피어가 상태 저장 방화벽을 실행하는 디바이스에서 피어링을 종료해서는 안 됩니다. 
-   Microsoft는 기본적으로 LAG(링크 번들)로 모든 상호 연결 링크를 구성하므로 피어가 상호 연결 링크의 LACP(링크 집계 제어 프로토콜)를 지원해야 합니다.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Communication Services에 대해 Microsoft와 직접 상호 연결을 설정합니다.

Azure Internet 피어링을 사용하여 직접 연결을 설정하려면 다음 단계를 수행하세요.

**1. 피어 공용 ASN을 Azure 구독에 연결합니다.**

피어가 이미 공용 ASN을 Azure 구독에 연결한 경우에는 이 단계를 무시하세요.

[포털을 사용하여 피어 ASN을 Azure 구독에 연결 - Azure | Microsoft Docs](./howto-subscription-association-portal.md)

다음 단계는 Peering Service에 대한 직접 피어링 연결을 만드는 것입니다.

> [!NOTE]
> ASN 연결이 승인되면 사용자의 ASN 및 구독 ID로 peeringservice@microsoft.com에서 이메일을 보내 구독을 Communication Services와 연결합니다. 

**2. Peering Service에 대한 직접 피어링 연결을 만듭니다.**

[포털을 사용하여 직접 피어링 만들기 또는 수정](./howto-direct-portal.md)에 대한 지침을 따릅니다.

고가용성 요구 사항을 충족하는지 확인합니다.

"피어링 만들기" 페이지에서 다음 옵션을 선택했는지 확인하세요.

피어링 유형: **직접**

Microsoft 네트워크:  **8075 (음성 포함)**

SKU: **프리미엄 무료**


"직접 피어링 연결 페이지"에서 다음 옵션을 선택합니다.

세션 주소 공급자: **Microsoft**

Peering Service에 사용: **사용**

> [!NOTE] 
> Peering Service를 활성화하기 위해 선택하는 동안 다음 메시지를 무시합니다.
> *맵 공급자 되기에 대해 peering@microsoft.com에 문의하지 않은 한 사용하도록 설정하지 마세요.*


  **2a. Peering Service에 기존 직접 피어링 연결 사용**

Peering Service를 지원하는 데 사용하려는 기존 직접 피어링이 있는 경우 Azure Portal에서 활성화할 수 있습니다.
1.  [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](./howto-legacy-direct-portal.md)에 대한 지침을 따릅니다.
필요에 따라 고가용성 요구 사항을 충족하기 위해 추가 회로를 주문합니다.

2.  포털을 사용하여 직접 피어링에 [Peering Service 사용](./howto-peering-service-portal.md)에 대한 단계를 따릅니다.




**3. 최적화된 라우팅에 대한 접두사 등록**

통신 서비스 인프라 접두사에 대해 최적화된 라우팅의 경우 피어링 상호 연결을 사용하여 모든 접두사를 등록해야 합니다.
[Azure Peering Service 등록 - Azure Portal | Microsoft Docs](../peering-service/azure-portal.md)

접두사 키는 통신 서비스 파트너에 대해 자동으로 채워지므로 파트너는 등록할 접두사 키를 사용할 필요가 없습니다. 

등록 중인 접두사가 해당 지역에 대해 설정된 직접 상호 연결을 통해 발표되는지 확인하세요.


## <a name="faqs"></a>FAQ:

**Q.**  내 통신 서비스에 대해 더 작은 서브넷(</24)이 있습니다. 더 작은 서브넷도 라우팅할 수 있나요?

**A.**  예, Microsoft Azure 피어링 서비스는 더 작은 접두사 라우팅도 지원합니다. 라우팅에 대한 더 작은 접두사를 등록하고 있고 상호 연결을 통해 동일하게 발표되는지 확인하세요.

**Q.**  이러한 상호 연결을 통해 받는 Microsoft 경로는 무엇인가요?

**A.** Microsoft는 이러한 상호 연결을 통해 모든 Microsoft의 공용 서비스 접두사를 발표합니다. 이렇게 하면 통신뿐만 아니라 다른 클라우드 서비스도 동일한 상호 연결에서 액세스할 수 있습니다.

**Q.**  접두사 제한을 설정해야 합니다. Microsoft에서 몇 개의 경로를 발표하나요?

**A.** Microsoft는 인터넷에서 약 280개의 접두사를 발표하며, 향후에는 10-15%까지 늘어날 수 있습니다. 따라서 "최대 접두사 수"로 400-500의 안전 제한을 설정하는 것이 좋을 수 있습니다.

**Q.** Microsoft에서 피어 접두사를 인터넷에 다시 보급하나요?

**A.** 아니요.

**Q.** 이 서비스에 대한 요금이 있나요?

**A.** 아니요, 그러나 피어는 사이트 간 연결 비용을 전달해야 합니다.

**Q.** 상호 연결에 대한 최소 링크 속도는 무엇인가요?

**A.** 10Gbps입니다.

**Q.** 피어가 SLA에 바인딩되어 있나요?

**A.** 예, 사용률이 40%에 도달하면 45-60일 LAG 확대 프로세스를 시작해야 합니다.

**Q.** 현재 직접 피어링 또는 express 경로에 대한 이 서비스의 이점은 무엇인가요?

**A.** 정착지 무료 및 전체 경로는 Microsoft WAN을 통한 음성 트래픽에 최적화되고, 수렴은 BFD를 사용하여 하위 초에 대해 조정됩니다.

**Q.** 온보딩 프로세스를 완료하는 데 얼마나 걸리나요?

**A.** 시간은 사이트 수와 위치에 따라 달라지며, 피어가 기존 개인 피어링을 마이그레이션하거나 새 케이블을 설정하는 경우에 따라 달라집니다. 이동 통신 사업자는 3+주에 대해 계획해야 합니다.

**Q.** 온보딩에 API를 사용할 수 있나요?

**A.** 현재는 API를 지원하지 않으며 웹 포털을 통해 구성을 수행해야 합니다.
