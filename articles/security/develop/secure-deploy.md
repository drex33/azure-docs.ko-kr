---
title: Microsoft Azure에서 보안 애플리케이션 배포
description: 이 문서에서는 웹 애플리케이션 프로젝트의 릴리스 및 응답 단계에서 고려할 모범 사례를 설명합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ab2188e0a59216ab01b54f430506003529129bb7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342987"
---
# <a name="deploy-secure-applications-on-azure"></a>Azure에서 보안 애플리케이션 배포
이 문서에서는 클라우드용 애플리케이션을 배포할 때 고려해야 할 보안 및 제어 작업에 관해 설명합니다. Microsoft [SDL(보안 개발 수명 주기)](/previous-versions/windows/desktop/cc307891(v=msdn.10))의 릴리스 및 응답 단계에서 고려해야 하는 보안 질문과 개념을 다룹니다. 목표는 더 안전한 애플리케이션을 배포하는 데 사용할 수 있는 작업과 Azure 서비스를 정의하도록 돕는 것입니다.

이 문서에서 다루는 SDL 단계는 다음과 같습니다.

- 해제
- 응답

## <a name="release"></a>해제
릴리스 단계의 초점은 일반 릴리스에 대한 프로젝트를 준비하는 것입니다. 여기에는 릴리스 후 서비스 작업을 효과적으로 수행하고 나중에 발생할 수 있는 보안 취약성을 해결하는 계획 방법이 포함 됩니다.

### <a name="check-your-applications-performance-before-you-launch"></a>시작하기 전에 애플리케이션의 성능을 확인

시작하거나 프로덕션에 업데이트를 배포하기 전에 애플리케이션의 성능을 확인합니다. 애플리케이션에서 성능 문제를 찾기 위해 Visual Studio를 사용하여 클라우드 기반 [부하 테스트](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)를 실행하고, 배포 품질을 향상하고 애플리케이션이 항상 사용 가능하며 애플리케이션에서 시작에 트래픽을 처리할 수 있는지 확인합니다.

### <a name="install-a-web-application-firewall"></a>웹 애플리케이션 방화벽 설치

웹 애플리케이션의 널리 알려진 취약점을 악용하는 악의적인 공격이 점점 많아지고 있습니다. 이러한 익스플로잇 중 일반적인 것은 SQL 삽입 공격과 교차 사이트 스크립팅 공격입니다. 애플리케이션 코드에서 이러한 공격을 방지하는 것은 어려울 수 있습니다. 애플리케이션 토폴로지의 여러 계층에서 엄격한 유지 관리, 패치 및 모니터링이 필요할 수 있습니다. 중앙 집중식 WAF를 사용하면 보안을 보다 간단하게 관리할 수 있습니다. 또한 WAF 솔루션은 각각의 웹 애플리케이션을 보호하는 대신 중앙 위치에서 알려진 취약성에 패치를 적용하여 보다 신속하게 보안 위협에 대응할 수 있습니다.

[Azure Application Gateway WAF](../../web-application-firewall/ag/ag-overview.md)는 일반적인 익스플로잇 및 취약성으로부터 웹 애플리케이션의 중앙 집중식으로 보호합니다. WAF는 [OWASP 핵심 규칙 집합](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 또는 2.2.9의 규칙에 기반합니다.

### <a name="create-an-incident-response-plan"></a>인시던트 대응 계획 만들기

인시던트 대응 계획을 준비하는 것은 시간이 지남에 따라 발생할 수 있는 새로운 위협을 해결하는 데 도움이 됩니다. 인시던트 대응 계획 준비에는 적절한 보안 긴급 연락처 식별과 조직의 기타 그룹에서 상속되는 코드 및 라이선스가 부여된 제3자 코드에 대한 보안 서비스 계획 수립이 포함됩니다.

### <a name="conduct-a-final-security-review"></a>최종 보안 검토 수행

수행된 모든 보안 활동을 의도적으로 검토하면 소프트웨어 릴리스 또는 애플리케이션에 대한 준비 상태를 보장할 수 있습니다. FSR(최종 보안 검토)에는 일반적으로 요구 사항 단계에서 정의된 품질 게이트 및 버그 바에 대한 위협 모델, 도구 출력 및 성능 검사가 포함 되어 있습니다.

### <a name="certify-release-and-archive"></a>릴리스 및 보관 인증

릴리스 전에 소프트웨어를 인증 하면 보안 및 개인 정보 요구 사항이 충족됩니다. 릴리스 후 서비스 작업을 수행 하기 위해서는 모든 관련 데이터를 보관하는 것이 필수적입니다. 또한 보관을 통해 지속적인 소프트웨어 엔지니어링과 관련된 장기 비용을 낮출 수 있습니다.

## <a name="response"></a>응답

개발 팀의 릴리스 후 단계 센터는 새로운 소프트웨어 위협 및 취약성에 대한 모든 보고서에 적절하게 대응하고 사용할 수 있습니다.

### <a name="execute-the-incident-response-plan"></a>인시던트 응답 계획 실행

릴리스 단계에서 수립한 인시던트 대응 계획을 구현할 수 있는 것은 소프트웨어 보안 또는 개인 정보 보호 취약성으로부터 고객을 보호 하는 데 필요적입니다.

### <a name="monitor-application-performance"></a>애플리케이션 성능 모니터링

애플리케이션을 배포한 후 지속적으로 모니터링하면 성능 문제 및 보안 취약성을 감지하는 데 도움이 될 수 있습니다.

애플리케이션 모니터링을 지원하는 Azure 서비스는 다음과 같습니다.

  - Azure Application Insights
  - Microsoft Defender for Cloud

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md)는 여러 플랫폼의 웹 개발자를 위한 확장성 있는 APM(Application Performance Management) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. Application Insights는 자동으로 성능 이상을 검색합니다. 문제를 진단하고 앱을 사용하여 실제로 수행할 작업을 이해할 수 있도록 돕는 강력한 분석 도구를 포함합니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

#### <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud

[Microsoft Defender for Cloud를](../../security-center/security-center-introduction.md) 사용하면 웹 애플리케이션을 비롯한 Azure 리소스의 보안에 대한 가시성을 높이고 제어하여 위협을 방지, 감지 및 대응할 수 있습니다. 클라우드용 Microsoft Defender는 달리 발견되지 않을 수 있는 위협을 검색하는 데 도움이 됩니다. 다양한 보안 솔루션에서 작동합니다.

Defender for Cloud의 무료 계층은 Azure 리소스에 대해서만 제한된 보안을 제공합니다. [Defender for Cloud Standard 계층은](../../security-center/security-center-get-started.md) 이러한 기능을 온-프레미스 리소스 및 기타 클라우드로 확장합니다.
Defender for Cloud Standard를 사용하면 다음을 수행할 수 있습니다.

  - 보안 취약성을 찾아서 수정합니다.
  - 액세스 및 애플리케이션 제어를 적용하여 악의적인 활동을 차단 합니다.
  - 분석 및 인텔리전스를 사용하여 위협을 검색합니다.
  - 공격을 받을 때 신속하게 대응합니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 애플리케이션을 디자인하고 개발하는 데 도움이 되는 보안 제어 및 작업을 권장합니다.

- [보안 애플리케이션 디자인](secure-design.md)
- [보안 애플리케이션 개발](secure-develop.md)
