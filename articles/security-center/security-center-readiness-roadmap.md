---
title: 클라우드 준비 로드맵 용 Defender | Microsoft Docs
description: 이 문서에서는 클라우드에 대해 Defender를 시작 하기 위한 준비 로드맵을 제공 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 345b1373371cc8643cb6d9c82e8a98e2eee42def
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457148"
---
# <a name="defender-for-cloud-readiness-roadmap"></a>클라우드 준비 로드맵 용 Defender

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 클라우드 용 Defender를 시작 하는 데 도움이 되는 준비 로드맵을 제공 합니다.

## <a name="understanding-defender-for-cloud"></a>클라우드 용 Defender 이해
Defender for Cloud는 Azure, 온-프레미스 및 기타 클라우드에서 실행 되는 워크 로드에 대 한 통합 보안 관리 및 고급 위협 방지 기능을 제공 합니다. 

다음 리소스를 사용 하 여 클라우드 용 Defender를 시작 합니다.

문서
- [클라우드 용 Defender 소개](defender-for-cloud-introduction.md)
- [클라우드 용 Defender 빠른 시작 가이드](get-started.md)

동영상
- [간략한 소개 비디오](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [클라우드 방지, 감지 및 대응 기능을 위한 Defender 개요](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>계획 및 작업

클라우드 용 Defender를 최대한 활용 하려면 조직의 여러 개인 또는 팀이 서비스를 사용 하 여 보안 운영, 모니터링, 관리 및 인시던트 대응 요구를 충족 하는 방법을 이해 하는 것이 중요 합니다.

다음 리소스를 사용하여 계획 및 작업 프로세스 중에 도움을 받습니다.

- [클라우드 계획 및 운영 가이드를 위한 Defender](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-defender-for-cloud"></a>클라우드 용 Defender에 컴퓨터 온 보 딩
클라우드에 대 한 defender는 azure Defender에서 보호 하지 않는 Azure 구독 또는 작업 영역을 자동으로 검색 합니다. 여기에는 클라우드 무료 용 Defender를 사용 하는 Azure 구독과 보안 솔루션이 사용 하도록 설정 되지 않은 작업 영역이 포함 됩니다.

다음 리소스를 사용하여 온보딩 프로세스 중에 도움을 받습니다.

- [비 Azure 컴퓨터 온보딩](quickstart-onboard-machines.md)
- [클라우드 하이브리드 용 Defender-개요](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-defender-for-cloud"></a>클라우드 용 Defender를 사용 하 여 보안 문제 완화
Defender for Cloud는 방화벽 및 끝점 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결 된 파트너 솔루션의 로그 데이터를 자동으로 수집, 분석 및 통합 하 여 실제 위협을 감지 하 고 가양성을 줄입니다.

다음 리소스를 사용하여 보안 경고를 관리하고 리소스를 보호할 수 있습니다.

아티클    
- [클라우드에 대 한 Defender에서 네트워크 보호](./protect-network-resources.md)
- [클라우드에 대 한 Defender의 Azure SQL 서비스 및 데이터 보호](./implement-security-recommendations.md)


동영상    
- [클라우드 용 Defender를 사용 하 여 보안 문제 완화](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="defender-for-cloud-for-incident-response"></a>인시던트 대응을 위한 클라우드 용 Defender
비용과 손상을 줄이려면 공격이 발생하기 전에 사고 대응 계획을 적절히 수립하는 것이 중요합니다. 인시던트 응답의 서로 다른 단계에서 클라우드에 Defender를 사용할 수 있습니다.

다음 리소스를 사용 하 여 클라우드 용 Defender를 인시던트 대응 프로세스에 통합 하는 방법을 이해할 수 있습니다.

동영상    
* [인시던트 응답의 클라우드 용 Defender](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [차세대 보안 작업으로 위협에 대한 신속한 대응 및 조사](https://youtu.be/e8iFCz5RM4g)

아티클    
* [인시던트 대응을 위해 Defender for Cloud 사용](./tutorial-security-incident.md)
* [자동화를 사용 하 여 클라우드 트리거에 대 한 Defender에 응답](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>고급 클라우드 방어

Azure Vm은 클라우드에 대 한 Defender의 고급 클라우드 방어 기능을 활용할 수 있습니다. 이러한 기능에는 JIT(Just-In-Time) VM(가상 머신) 액세스 및 적응형 애플리케이션 제어가 포함됩니다.

다음 리소스를 사용 하 여 Defender for Cloud에서 이러한 기능을 사용 하는 방법을 알아보세요.

동영상    
* [클라우드 용 Defender – Just-in-time VM 액세스](https://youtu.be/UOQb2FcdQnU)
* [클라우드 적응 응용 프로그램 컨트롤용 Defender](https://youtu.be/wWWekI1Y9ck)

아티클    
* [Just-In-Time을 사용하여 가상 머신 액세스 관리](./just-in-time-access-usage.md)
* [클라우드에 대 한 Defender의 적응 응용 프로그램 컨트롤](./adaptive-application-controls.md)

## <a name="hands-on-activities"></a>실습 활동

* [클라우드 실습 랩에 대 한 Defender](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Defender for Cloud의 WAF (웹 응용 프로그램 방화벽) 권장 사항 플레이 북](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Cloud 플레이 북 용 Defender: 보안 경고](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>추가 자료
* [클라우드 용 Defender 설명서 페이지](./index.yml)
* [Cloud REST API 용 Defender 설명서 페이지](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Defender for Cloud 질문과 대답 (FAQ)](./faq-general.yml)
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)
* [ID 보안 모범 사례](../security/fundamentals/identity-management-best-practices.md)
* [네트워크 보안 모범 사례](../security/fundamentals/network-best-practices.md)
* [PaaS 권장 사항](../security/fundamentals/paas-deployments.md)
* [호환성](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log analytics 고객은 이제 클라우드 용 Defender를 사용 하 여 하이브리드 클라우드 워크 로드를 보호할 수 있습니다.](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)
