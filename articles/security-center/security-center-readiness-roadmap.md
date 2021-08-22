---
title: Azure Security Center 준비 로드맵 | Microsoft Docs
description: Azure Security Center에서 시작하기 위한 준비 로드맵을 제공합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 42ca1bf538ff7a8a7479f8db041d35d4ea68692e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535866"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure Security Center 준비 로드맵
이 문서에서는 Azure Security Center를 시작하는 데 도움이 되는 준비 로드맵을 제공합니다.

## <a name="understanding-security-center"></a>Security Center 이해
Azure Security Center는 Azure, 온-프레미스 및 기타 클라우드용으로 통합 보안 관리 및 고급 위협 보호 기능을 제공합니다. 

다음 리소스를 사용하여 Security Center를 시작합니다.

아티클
- [Azure Security Center 소개](security-center-introduction.md)
- [Azure Security Center 빠른 시작 가이드](security-center-get-started.md)

동영상
- [간략한 소개 비디오](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Security Center 예방, 검색 및 응답 기능에 대한 개요](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>계획 및 작업

Security Center를 완벽하게 활용하려면 조직의 여러 개인 또는 팀에서 보안 작업, 모니터링, 거버넌스 및 인시던트 대응 요구 사항에 맞게 서비스를 사용하는 방법에 대해 이해해야 합니다.

다음 리소스를 사용하여 계획 및 작업 프로세스 중에 도움을 받습니다.

- [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Security Center에 컴퓨터 온보딩
Security Center는 Azure Defender로 보호되지 않는 모든 Azure 구독 또는 작업 영역을 자동으로 감지합니다. 여기에는 Security Center 무료 계층을 사용하는 Azure 구독과 보안 솔루션이 사용하도록 설정되지 않은 작업 영역이 포함됩니다.

다음 리소스를 사용하여 온보딩 프로세스 중에 도움을 받습니다.

- [비 Azure 컴퓨터 온보딩](quickstart-onboard-machines.md)
- [Azure Security Center 하이브리드 - 개요](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Security Center를 사용하여 보안 문제 완화
보안 센터는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다.

다음 리소스를 사용하여 보안 경고를 관리하고 리소스를 보호할 수 있습니다.

아티클    
- [Azure Security Center에서 네트워크 보호](./security-center-network-recommendations.md)
- [Azure Security Center에서 Azure SQL 서비스 및 데이터 보호](./security-center-remediate-recommendations.md)


비디오    
- [Azure Security Center를 사용하여 보안 문제 완화](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>인시던트 대응을 위한 Security Center
비용과 손상을 줄이려면 공격이 발생하기 전에 사고 대응 계획을 적절히 수립하는 것이 중요합니다. Azure Security Center는 사고에 대응하는 여러 단계에서 사용할 수 있습니다.

다음 리소스를 사용하여 Security Center를 인시던트 대응 프로세스에 통합하는 방법을 이해합니다.

동영상    
* [인시던트 대응을 위한 Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [차세대 보안 작업으로 위협에 대한 신속한 대응 및 조사](https://youtu.be/e8iFCz5RM4g)

아티클    
* [인시던트 대응에 Azure Security Center 사용](./tutorial-security-incident.md)
* [자동화를 사용하여 Security Center 트리거에 응답](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>고급 클라우드 방어

Azure VM은 Security Center의 고급 클라우드 방어 기능을 활용할 수 있습니다. 이러한 기능에는 JIT(Just-In-Time) VM(가상 머신) 액세스 및 적응형 애플리케이션 제어가 포함됩니다.

다음 리소스를 사용하여 Security Center에서 이러한 기능을 사용하는 방법을 알아봅니다.

동영상    
* [Azure Security Center – Just-In-Time VM 액세스](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center - 적응형 애플리케이션 컨트롤](https://youtu.be/wWWekI1Y9ck)

아티클    
* [Just-In-Time을 사용하여 가상 머신 액세스 관리](./security-center-just-in-time.md)
* [Azure Security Center의 적응형 애플리케이션 제어](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>실습 활동

* [Security Center 실습 교육](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Security Center의 WAF(웹 애플리케이션 방화벽) 권장 플레이 북](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center 플레이북: 보안 경고](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>추가 리소스
* [Security Center 설명서 페이지](./index.yml)
* [Security Center REST API 설명서 페이지](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Azure Security Center FAQ(질문과 대답)](./faq-general.yml)
* [Security Center 가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)
* [ID 보안 모범 사례](../security/fundamentals/identity-management-best-practices.md)
* [네트워크 보안 모범 사례](../security/fundamentals/network-best-practices.md)
* [PaaS 권장 사항](../security/fundamentals/paas-deployments.md)
* [호환성](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [이제 Log Analytics 고객은 Azure Security Center를 사용하여 하이브리드 클라우드 워크로드를 보호할 수 있습니다.](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)