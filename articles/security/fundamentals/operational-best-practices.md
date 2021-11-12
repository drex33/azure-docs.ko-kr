---
title: Azure 자산 보안 모범 사례
titleSuffix: Azure security
description: 이 문서에서는 데이터, 애플리케이션 및 그 밖의 Azure 자산을 보호하기 위한 다양한 운영 모범 사례를 안내합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 79c8fadfc7662438f5820e92cfd0c71a9b346ba9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326876"
---
# <a name="azure-operational-security-best-practices"></a>Azure 운영 보안 모범 사례
이 문서에서는 데이터, 애플리케이션 및 그 밖의 Azure 자산을 보호하기 위한 다양한 운영 모범 사례를 안내합니다.

모범 사례는 의견의 일치를 기반으로 하며 현재 Azure 플랫폼 기능 및 기능 집합과 함께 작동합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하여 주기적으로 업데이트됩니다.

## <a name="define-and-deploy-strong-operational-security-practices"></a>강력한 운영 보안 방식 정의 및 배포
Azure 운영 보안은 사용자가 Azure에서 자신의 데이터, 애플리케이션 및 기타 자산을 보호하는 데 사용할 수 있는 서비스, 제어 및 기능을 지칭합니다. Azure 운영 보안은 [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl), [Microsoft 보안 대응 센터](https://www.microsoft.com/msrc?rtc=1) 프로그램 및 사이버 보안 위협 상황에 대한 심층 인식을 포함하여 Microsoft 고유의 다양한 기능을 통해 얻은 지식을 통합한 프레임워크를 기반으로 합니다.

## <a name="manage-and-monitor-user-passwords"></a>사용자 암호 관리 및 모니터링
다음 표에는 사용자 암호 관리와 관련된 몇 가지 모범 사례가 나와 있습니다.

**모범 사례**: 클라우드에 적절한 수준의 암호 보호가 구현되었는지 확인합니다.   
**세부 정보**: [암호 가이드](https://www.microsoft.com/research/publication/password-guidance/)에 나와 있는 가이드를 따릅니다. 이 가이드는 Microsoft ID 플랫폼(Azure Active Directory, Active Directory 및 Microsoft 계정)의 사용자를 위해 작성되었습니다.

**모범 사례**: 사용자 계정과 관련하여 의심스러운 동작이 있는지 모니터링합니다.   
**세부 정보**: Azure AD 보안 보고서를 바탕으로 [위험에 노출된 사용자](../../active-directory/identity-protection/overview-identity-protection.md)와 [위험한 로그인](../../active-directory/identity-protection/overview-identity-protection.md)이 있는지 모니터링합니다.

**모범 사례**: 위험 수준이 높은 암호를 자동으로 감지하고 수정합니다.   
**세부 정보**: [Azure AD ID 보호](../../active-directory/identity-protection/overview-identity-protection.md)는 다음과 같은 기능을 제공하는 Azure AD Premium P2 에디션의 기능입니다.

- 조직의 ID에 영향을 미치는 잠재적인 취약점 검색
- 조직 ID와 관련된 검색된 의심스러운 작업에 대한 자동화된 응답 구성
- 의심스러운 인시던트 조사 및 이를 해결할 적절한 작업 수행

## <a name="receive-incident-notifications-from-microsoft"></a>Microsoft의 인시던트 알림 받기
보안 운영 팀이 Microsoft의 Azure 인시던트 알림을 받고 있는지 확인하세요. 보안 팀은 인시던트 알림을 통해 보안이 침해된 Azure 리소스가 있다는 사실을 확인하여 잠재적인 보안 위험에 따르게 대응하고 이를 수정할 수 있습니다.

Azure enrollment 등록 포털에서 관리자 연락처 정보에 보안 운영 팀에 알림을 제공하기 위한 정보가 포함되어 있는지 확인할 수 있습니다. 연락처 정보는 전자 메일 주소 및 전화 번호입니다.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure 구독을 관리 그룹으로 구성
조직에 구독이 많은 경우 해당 구독에 대한 액세스, 정책 및 규정 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. [Azure 관리 그룹](../../governance/management-groups/create-management-group-portal.md)은 구독 상위 수준의 범위를 제공합니다. 구독을 관리 그룹이라고 하는 컨테이너에 구성하고 거버넌스 조건을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 조건을 자동으로 상속합니다.

관리 그룹과 구독을 유연한 구조를 갖는 디렉터리로 구성할 수 있습니다. 각 디렉터리에는 루트 관리 그룹이라는 하나의 최상위 수준 관리 그룹이 있습니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 루트 관리 그룹을 통해 글로벌 정책 및 Azure 역할 할당을 디렉터리 수준에서 적용할 수 있습니다.

다음은 관리 그룹을 사용하는 몇 가지 모범 사례입니다.

**모범 사례**: 새로운 구독을 추가할 때 정책, 권한과 같은 거버넌스 요소를 적용합니다.   
**세부 정보**: 루트 관리 그룹을 사용하여 모든 Azure 자산에 적용되는 전사적인 보안 요소를 할당합니다. 이러한 요소의 예로 정책과 권한을 들 수 있습니다.

**모범 사례**: 세그먼트 전략을 바탕으로 최상위 수준의 관리 그룹을 일관되게 맞추어서 각 세그먼트 내에서 제어 지점을 마련하고 정책 일관성을 구현합니다.   
**세부 정보**: 루트 관리 그룹 아래에 각 세그먼트에 대한 단일 관리 그룹을 만듭니다. 루트 아래에 그 밖에 다른 관리 그룹을 만들지 않습니다.

**모범 사례**: 운영과 보안 양쪽에 좋지 않은 영향을 주는 혼란을 방지하기 위해 관리 그룹의 수준을 제한합니다.   
**세부 정보**: 계층 구조를 루트 포함 세 개 수준으로 제한합니다.

**모범 사례**: 루트 관리 그룹을 사용하여 엔터프라이즈 전체에 적용할 항목을 주의 깊게 선택합니다.   
**세부 정보**: 루트 관리 그룹 요소를 모든 리소스에 적용해야 하는 명확한 이유가 있으며 해당 요소가 미치는 영향이 크지 않은지 확인합니다.

적합한 후보는 다음과 같습니다.

- 명확한 비즈니스 영향을 갖는 규정 요구 사항(예: 데이터 주권과 관련된 제한 사항)
- 운영에 미치는 부정적인 영향이 거의 전무한 요구 사항(예: 신중한 검토를 걸쳐 선택한, 감사 영향이 있는 정책이나 Azure RBAC 권한 할당)

**모범 사례**: 루트 관리 그룹에 적용할 전사적인 변경 사항(정책, Azure RBAC 모델 등)을 신중히 계획하고 테스트한 후에 적용합니다.   
**세부 정보**: 루트 관리 그룹의 변경 사항은 Azure에 있는 모든 리소스에 영향을 줄 수 있습니다. 루트 관리 그룹은 전사적으로 일관성을 보장할 강력한 방법이 되나, 오류가 발생하거나 잘못 사용할 경우 프로덕션 운영에 부정적인 영향을 줄 수 있습니다. 루트 관리 그룹의 모든 변경 사항은 테스트 랩이나 프로덕션 파일럿에서 테스트하세요.

## <a name="streamline-environment-creation-with-blueprints"></a>청사진을 사용하여 환경 간소화
클라우드 설계자와 중앙 정보 기술 그룹은 [Azure Blueprints](../../governance/blueprints/overview.md) 서비스를 통해 조직의 표준, 패턴 및 요구 사항을 구현하고 준수하는 반복 가능한 Azure 리소스 세트를 정의할 수 있습니다. Azure Blueprints를 사용하면 개발 팀이 일련의 기본 제공되는 구성 요소를 사용하여 새로운 환경을 빠르게 빌드하고 실행할 수 있으며 조직의 규정을 준수하면서 환경을 빌드하고 있다는 확신을 가질 수 있습니다.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>스토리지 서비스에서 예기치 않은 동작 변경 모니터링
클라우드 환경에서 호스팅되는 분산 애플리케이션의 문제를 진단하고 해결하는 과정은 기존 환경보다 복잡할 수 있습니다. 애플리케이션은 PaaS 또는 IaaS 인프라, 온-프레미스, 모바일 디바이스 또는 이들 중 일부가 조합된 환경에 배포될 수 있습니다. 애플리케이션의 네트워크 트래픽은 공용 네트워크와 프라이빗 네트워크로 전송될 수 있으며, 애플리케이션이 여러 가지 스토리지 기술을 사용할 수도 있습니다.

따라서 애플리케이션이 사용하는 스토리지 서비스를 지속적으로 모니터링해 응답 속도 저하 등의 예기치 않은 동작 변경을 파악해야 합니다. 로깅을 사용하여 자세한 데이터를 수집하고 문제를 심층 분석합니다. 모니터링 및 로깅을 통해 얻은 진단 정보를 토대로 애플리케이션에 발생한 문제의 근본 원인을 확인할 수 있습니다. 그러면 문제를 해결하고 해당 문제를 완화하기 위한 적절한 단계를 결정할 수 있습니다.

[Azure 스토리지 분석](../../storage/common/storage-analytics.md)은 로깅을 수행하며 Azure Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고, 사용량 추세를 분석하고, 스토리지 계정에 대한 문제를 진단하는 것이 좋습니다.

## <a name="prevent-detect-and-respond-to-threats"></a>위협 방지, 검색 및 대응
[Microsoft Defender For Cloud](../../security-center/security-center-introduction.md) 를 사용 하면 Azure 리소스의 보안에 대 한 향상 된 가시성을 제공 하 여 위협을 예방, 감지 및 대응 하는 데 도움이 됩니다. Azure 구독 전반에 걸친 통합 보안 모니터링 및 정책 관리 기능을 제공하는 Azure Security Center를 활용하면 일반적으로는 찾아내기가 어려운 위협을 검색할 수 있으며 다양한 보안 솔루션을 활용할 수 있습니다.

클라우드 용 Defender의 무료 계층은 Azure 리소스에 대 한 제한 된 보안을 제공 합니다. 표준 계층에서는 이러한 기능이 온-프레미스 및 기타 클라우드로 확대 제공됩니다. Defender for Cloud Standard를 사용 하면 보안 취약성을 찾아 해결 하 고, 액세스 및 응용 프로그램 제어를 적용 하 여 악의적인 활동을 차단 하 고, 분석 및 인텔리전스를 사용 하 여 위협을 감지 하 고, 공격에 따라 신속 하 게 처음 60 일 동안 무료로 클라우드 표준에 대해 Defender를 사용해 볼 수 있습니다. [클라우드 표준에 대해 Azure 구독을 Defender로 업그레이드](../../security-center/security-center-get-started.md)하는 것이 좋습니다.

클라우드 용 Defender를 사용 하 여 모든 Azure 리소스의 보안 상태를 중앙에서 볼 수 있습니다. 적합한 보안 제어 기능이 마련되어 있으며 올바르게 구성되어 있는지를 한눈에 확인하고 주의가 필요한 리소스를 빠르게 확인할 수 있습니다.

또한 Defender for Cloud는 포괄적인 끝점 검색 및 응답 (EDR) 기능을 제공 하는 [Microsoft Defender ATP (Advanced Threat Protection)](../../security-center/security-center-wdatp.md)와 통합 됩니다. Microsoft Defender ATP를 통합하면 비정상적인 상태를 정확히 파악할 수 있습니다. 또한 Defender for Cloud에서 모니터링 하는 서버 끝점에 대 한 고급 공격을 감지 하 고 대응할 수 있습니다.

거의 모든 엔터프라이즈 조직이 다양한 신호 수집 디바이스에서 수집한 로그 정보를 통합하여 새로 부상하는 위협을 식별하는 SIEM(보안 정보 및 이벤트 관리) 시스템을 사용합니다. 통합된 로그는 데이터 분석 시스템에 의해 분석되어, 모든 로그 수집 및 분석 솔루션에서 발생하는 잡음으로부터 의미 있는 데이터가 가려집니다.

[Microsoft 센티널](../../sentinel/overview.md) 은 확장 가능한 클라우드 기본, siem (보안 정보 및 이벤트 관리) 및 대화 충성도 (security orchestration 자동화 된 응답) 솔루션입니다. Microsoft 센티널은 경고 검색, 위협 가시성, 자동 관리 구하기 및 자동화 된 위협 대응을 통해 지능형 보안 분석과 위협 인텔리전스를 제공 합니다.

다음은 위협을 방지하고 탐지하고 대응하기 위한 몇 가지 모범 사례입니다.

**모범 사례**: 클라우드 기반 SIEM을 사용하여 SIEM 솔루션의 속도와 스케일링 성능을 높입니다.   
**세부 정보**: [Microsoft 센티널](../../sentinel/overview.md) 의 특징과 기능을 조사 하 여 현재 온-프레미스에서 사용 하 고 있는 기능의 기능과 비교 합니다. 조직의 SIEM 요구 사항을 충족 하는 경우 Microsoft 센티널을 채택 하는 것이 좋습니다.

**모범 사례**: 가장 심각한 보안 취약점을 찾아서 조사의 우선 순위를 지정할 수 있습니다.   
**세부 정보**: [azure 보안 점수](../../security-center/secure-score-security-controls.md) 를 검토 하 여 클라우드 용 Microsoft Defender에 기본 제공 되는 azure 정책 및 이니셔티브에서 얻은 권장 사항을 확인 하세요. 권장 사항은 보안 업데이트, 엔드포인트 보호, 암호화, 보안 구성, 누락된 WAF, 인터넷에 연결된 VM을 비롯한 수많은 주요 위험에 대응할 수 있도록 도와줍니다.

CIS(Center for Internet Security) 컨트롤을 기반으로 하는 보안 점수를 활용하면 외부 소스를 기준으로 조직의 Azure 보안을 벤치마크할 수 있습니다. 외부 유효성 검사는 팀의 보안 전략의 유효성을 검사하고 보강하는 데 도움이 됩니다.

**모범 사례**: 머신, 네트워크, 스토리지 및 데이터 서비스, 애플리케이션의 보안 상태를 모니터링하여 잠재적인 보안 문제를 찾아내고 우선적으로 대응합니다.  
**세부 정보**: 우선 순위가 가장 높은 항목을 포함 하 여 클라우드를 시작 하기 위해 Defender의 [보안 권장 사항을](../../security-center/security-center-recommendations.md) 따릅니다.

**모범 사례**: 클라우드 경고에 대 한 Defender를 siem (보안 정보 및 이벤트 관리) 솔루션에 통합 합니다.   
**세부 정보**: SIEM을 시용하는 대부분의 조직에서는 이를 분석가의 대응이 필요한 중앙 보안 알림 센터로 사용합니다. Defender for Cloud에 의해 생성 된 처리 된 이벤트는 Azure 활동 로그에 게시 됩니다. Azure Monitor를 통해 사용할 수 있는 로그 중 하나입니다. Azure Monitor는 모니터링 데이터를 SIEM 도구에 라우팅하는 것에 대한 통합된 파이프라인을 제공합니다. 방법은 [SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍](../../security-center/export-to-siem.md)을 참조하세요. microsoft 센티널을 사용 하는 경우 [커넥트 microsoft Defender for Cloud](../../sentinel/connect-azure-security-center.md)를 참조 하세요.

**모범 사례**: SIEM에 Azure 로그를 통합합니다.   
**세부 정보**: [Azure Monitor를 사용하여 데이터를 수집하고 내보냅니다](../../azure-monitor/overview.md#integrate-and-export-data). 이는 보안 인시던트 조사를 위한 필수 단계이나, 온라인 로그 보존에는 제한이 있습니다. Microsoft Sentinel을 사용하는 경우 [데이터 원본 커넥트 참조하세요.](../../sentinel/connect-data-sources.md)

**모범 사례**: 공격 조사에 EDR(엔드포인트 탐지 및 대응) 기능을 통합하여 조사 및 헌팅 프로세스의 속도를 높이고 가양성을 줄입니다.   
**세부 정보:** Defender for Cloud 보안 정책을 통해 [엔드포인트용 Microsoft Defender 통합을 사용하도록 설정합니다.](../../security-center/security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration) 위협 헌팅 및 인시던트 대응에 Microsoft Sentinel을 사용하는 것이 좋습니다.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>엔드투엔드 시나리오 기반 네트워크 모니터링
고객은 가상 네트워크, ExpressRoute, Application Gateway, 부하 분산 장치 등의 네트워크 리소스를 결합하여 Azure에서 엔드투엔드 네트워크를 빌드합니다. 모니터링은 각 네트워크 리소스에서 사용할 수 있습니다.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)는 지역별 서비스입니다. 이 서비스에서 제공하는 진단 및 시각화 도구를 사용하면 Azure 내/외부 통신에 사용되는 네트워크 시나리오 수준의 상태를 모니터링하고 진단할 수 있습니다.

사용 가능한 도구 및 네트워크 모니터링 관련 모범 사례는 다음과 같습니다.

**모범 사례**: 패킷 캡처를 사용하여 원격 네트워크 모니터링을 자동화합니다.  
**세부 정보**: VM에 로그인하지 않고 Network Watcher를 사용하여 네트워킹 문제를 모니터링 및 진단합니다. 경고를 설정하여 [패킷 캡처](../../network-watcher/network-watcher-alert-triggered-packet-capture.md)를 트리거하고 패킷 수준에서 실시간 성능 정보에 액세스합니다. 문제를 발견하면 자세히 조사하여 더 정확히 진단할 수 있습니다.

**모범 사례**: 흐름 로그를 사용하여 네트워크 트래픽을 파악합니다.  
**세부 정보**: [네트워크 보안 그룹 흐름 로그](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)를 사용하여 네트워크 트래픽 패턴을 심층 파악합니다. 흐름 로그의 정보를 확인하면 준수, 감사 및 네트워크 보안 프로필 모니터링에 필요한 데이터를 수집할 수 있습니다.

**모범 사례**: VPN 연결 문제를 진단합니다.  
**세부 정보**: Network Watcher를 사용하면 [자주 발생하는 VPN Gateway 및 연결 문제를 진단](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)할 수 있습니다. 문제를 파악할 수 있을 뿐 아니라 자세한 로그를 사용하여 추가로 조사할 수도 있습니다.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>검증된 DevOps 도구를 사용하여 배포 보안 유지
기업과 팀의 생산성과 효율성을 높이려면 다음의 DevOps 모범 사례를 따르세요.

**모범 사례**: 서비스 빌드 및 배포를 자동화합니다.  
**세부 정보**: [IaaC(Infrastructure as code)](/devops/deliver/what-is-infrastructure-as-code)는 IT 전문가가 일상적으로 수행하는 모듈식 인프라 빌드 및 관리 작업 부담을 없애 주는 기술 및 구성 요소 집합입니다. IT 전문가는 IaaC를 통해 소프트웨어 개발자가 애플리케이션 코드를 작성하고 유지 관리하는 것과 유사한 방식으로 최신 서버 환경을 빌드하고 유지 관리할 수 있습니다.

[Azure Resource Manager](../../azure-resource-manager/templates/syntax.md)를 사용하면 선언적 템플릿을 통해 애플리케이션을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 애플리케이션 수명 주기의 각 단계에서 애플리케이션을 반복 배포합니다.

**모범 사례**: 자동으로 Azure Web Apps 또는 Azure Cloud Services에 빌드 및 배포합니다.  
**세부 정보**: Azure DevOps Projects가 [자동으로 빌드되고 Azure 웹앱 또는 클라우드 서비스로 배포되도록](/azure/devops/pipelines/index) 구성할 수 있습니다. Azure DevOps는 코드 체크인 후 매번 Azure로 빌드한 뒤 이진 파일을 자동으로 배포합니다. 패키지 빌드 프로세스는 Visual Studio의 Package 명령과 동일하며 게시 단계는 Visual Studio의 Publish 명령과 동일합니다.

**모범 사례**: 릴리스 관리를 자동화합니다.  
**세부 정보**: [Azure Pipelines](/azure/devops/pipelines/index)는 다단계 배포 자동화 및 릴리스 프로세스 관리를 위한 솔루션입니다. 관리되는 지속적인 배포 파이프라인을 만들어 빠르고 쉽게 자주 릴리스합니다. Azure Pipelines를 통해 릴리스 프로세스를 자동화하고, 미리 정의된 승인 워크플로를 적용할 수 있습니다. 필요에 맞게 온-프레미스 및 클라우드에 배포하고 확장하며 사용자 지정합니다.

**모범 사례**: 앱을 시작하거나 프로덕션 환경에 업데이트를 배포하기 전에 앱 성능을 확인합니다.  
**세부 정보**: 클라우드 기반 [부하 테스트](/azure/devops/test/load-test/overview#alternatives)를 실행하여 다음과 같은 성과를 올립니다.

- 앱의 성능 문제를 찾습니다.
- 배포 품질을 개선합니다.
- 앱이 항상 사용 가능한 상태인지 확인합니다.
- 앱이 차기 출시 또는 마케팅 캠페인을 위한 트래픽을 처리할 수 있는지 확인합니다.

[Apache JMeter](https://jmeter.apache.org/)는 강력한 커뮤니티로 뒷받침되는 무료 인기 오픈 소스 도구입니다.

**모범 사례**: 애플리케이션 성능을 모니터링합니다.  
**세부 정보**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)는 웹 개발자들이 여러 플랫폼에서 사용할 수 있는 확가장 능한 APM(애플리케이션 성능 관리) 서비스입니다. Application Insights를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. 성능 이상을 자동으로 검색합니다. 또한 문제를 진단하고 앱을 사용하여 실제로 수행하는 작업을 파악할 수 있는 분석 도구도 포함되어 있습니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다.

## <a name="mitigate-and-protect-against-ddos"></a>DoS 완화 및 방지
DDoS(배포된 서비스 거부)는 애플리케이션 리소스를 고갈시키려는 공격 유형입니다. 공격 목표는 애플리케이션의 가용성과 합법적인 요청을 처리하는 기능을 약화시키는 것입니다. 최근에는 이러한 공격이 점점 정교해지고 그 규모와 영향도 점점 커지고 있습니다. 인터넷을 통해 공개적으로 연결 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다.

DDoS 발생 시 복원하는 기능을 설계하고 작성하려면 다양한 오류 모드에 대한 계획과 설계가 필요합니다. Azure에서 DDoS 공격 시 복원이 가능한 서비스를 구축하기 위한 모범 사례는 다음과 같습니다.

**모범 사례**: 디자인/구현에서 배포/작업까지 애플리케이션의 전체 수명 주기에서 보안을 최우선으로 고려합니다. 애플리케이션에는 양이 그다지 많지 않은 요청에서 많은 리소스를 사용하여 결국 서비스 중단으로 이어지게 만드는 버그가 있을 수 있습니다.  
**세부 정보**: Microsoft Azure에서 실행되는 서비스를 보호하려면 애플리케이션 아키텍처를 철저하게 파악하고 [소프트웨어 품질의 5가지 핵심 요소](/azure/architecture/guide/pillars)를 중점적으로 고려해야 합니다. 일반적인 트래픽 볼륨, 애플리케이션과 다른 애플리케이션 간의 연결 모델, 공용 인터넷에 노출되는 서비스 엔드포인트를 알아야 합니다.

애플리케이션 자체를 대상으로 하는 서비스 거부 공격을 처리할 수 있도록 애플리케이션의 복원력을 높이는 것이 가장 중요합니다. [SDL(Security Development Lifecycle)](https://www.microsoft.com/sdl)부터 보안 및 개인 정보 보호 기능이 Azure 플랫폼에 기본 제공됩니다. SDL은 모든 개발 단계에서 보안 문제를 해결하고 Azure를 지속적으로 업데이트하여 더욱 안전하게 보호합니다.

**모범 사례**: 부하 증가(특히 DDoS 공격 발생 시) 시의 수요를 충족하기 위해 [수평 확장](/azure/architecture/guide/design-principles/scale-out)이 가능하도록 애플리케이션을 디자인합니다. 애플리케이션이 서비스의 단일 인스턴스에 종속된 경우 단일 실패 지점이 생깁니다. 여러 인스턴스를 프로비전하면 시스템에 복원성 및 확장성이 증가하게 됩니다.  
**세부 정보**: [Azure App Service](../../app-service/overview.md)의 경우 여러 인스턴스를 제공하는 [App Service 플랜](../../app-service/overview-hosting-plans.md)을 선택합니다.

Azure Cloud Services의 경우 각각의 역할을 [여러 인스턴스](../../cloud-services/cloud-services-choose-me.md)를 사용하도록 구성합니다.

[Azure Virtual Machines](../../virtual-machines/windows/overview.md)의 경우 VM 아키텍처가 둘 이상의 VM을 포함하는지와 각 VM이 [가용성 집합](../../virtual-machines/windows/tutorial-availability-sets.md)에 포함되는지 확인합니다. 자동 크기 조정 기능을 활용할 수 있는 Virtual Machine Scale Sets를 사용하는 것이 좋습니다.

**모범 사례**: 애플리케이션에서 보안 방어를 계층화하면 공격이 성공할 가능성이 줄어듭니다. Azure 플랫폼의 기본 제공 기능을 사용하여 안전한 애플리케이션 디자인을 구현합니다.  
**세부 정보**: 애플리케이션의 크기(노출 영역)가 커지면 공격 위험도 증가합니다. 부하 분산 장치([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) 및 [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md))에 필요하지 않은 노출된 IP 주소 공간 및 수신 대기 포트를 종료하기 위해 승인 목록을 사용하여 노출 영역을 줄일 수 있습니다.

[네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md)은 공격 표면을 줄이기 위한 또 다른 방법입니다. [서비스 태그](../../virtual-network/network-security-groups-overview.md#service-tags) 및 [애플리케이션 보안 그룹](../../virtual-network/network-security-groups-overview.md#application-security-groups)을 사용하여 보안 규칙을 만드는 복잡성을 최소화하고 애플리케이션 구조의 기본 확장으로 네트워크 보안을 구성할 수 있습니다.

가능하면 [가상 네트워크](../../virtual-network/virtual-networks-overview.md)에 Azure 서비스를 배포해야 합니다. 이렇게 하면 서비스 리소스가 개인 IP 주소를 통해 통신할 수 있습니다. 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다.

[서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md) 사용 시에는 서비스 트래픽이 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 프라이빗 주소를 원본 IP 주소로 사용하도록 전환됩니다.

종종 고객의 온-프레미스 리소스가 Azure의 해댱 리소스와 함께 공격당하는 모습을 볼 수 있습니다. 온-프레미스 환경을 Azure에 연결하는 경우 공용 인터넷에 노출되는 온-프레미스 리소스를 최소화합니다.

Azure에는 네트워크 공격으로부터 리소스를 보호하는 다음의 두 가지 DDoS [서비스](../../ddos-protection/ddos-protection-overview.md)가 제공됩니다.

- 기본 보호는 기본적으로 추가 비용 없이 Azure에 통합됩니다. 전역 배포 Azure 네트워크의 규모 및 용량은 항상 작동하는 트래픽 모니터링 및 실시간 위험 완화를 통해 일반적인 네트워크 레이어 공격을 방어합니다. 사용자가 구성하거나 애플리케이션을 변경하지 않아도 사용이 가능한 기본 보호 기능을 활용하면 Azure DNS 등의 PaaS 서비스를 포함한 모든 Azure 서비스를 보호할 수 있습니다.
- 표준 보호는 네트워크 공격에 대한 고급 DDoS 완화 기능을 제공합니다. 특정 Azure 리소스를 보호하도록 자동으로 조정됩니다. 가상 네트워크를 만드는 동안 간단하게 보호를 사용하도록 설정할 수 있습니다. 생성 후에도 설정 가능하며 애플리케이션 또는 리소스를 변경할 필요가 없습니다.

## <a name="enable-azure-policy"></a>Azure Policy 사용
[Azure Policy](../../governance/policy/overview.md)는 정책을 만들고, 할당하고, 관리하는 데 사용하는 Azure의 서비스입니다. 이러한 정책은 리소스에 대해 규칙과 효과를 적용하므로 해당 리소스에서 회사 표준 및 서비스 수준 계약을 준수합니다. Azure Policy는 할당된 정책의 비준수에 대해 리소스를 평가하여 이 요구를 충족합니다.

Azure Policy를 사용하도록 설정하여 조직의 서면 정책을 모니터링하고 적용하세요. 이렇게 하면 모든 하이브리드 클라우드 작업에 걸쳐 보안 정책을 중앙에서 관리하여 회사 또는 규정 보안 요구 사항을 준수할 수 있습니다. [규정 준수를 적용하는 정책을 만들고 관리](../../governance/policy/tutorials/create-and-manage.md) 하는 방법을 알아보세요. 정책의 요소에 대해 살펴보려면 [Azure Policy 정의 구조](../../governance/policy/concepts/definition-structure.md)를 참조하세요.

다음은 Azure Policy를 도입한 후에 따르면 좋은 몇 가지 보안 모범 사례입니다.

**모범 사례**: 정책은 다양한 유형의 효과를 지원합니다. [Azure Policy 정의 구조](../../governance/policy/concepts/definition-structure.md#policy-rule)에서 자세히 알아보세요. 비즈니스 운영은 **거부** 효과와 **수정** 효과로 인해 부정적인 영향을 받을 수 있으므로 **감사** 효과로 시작하여 정책으로 인한 부정적인 영향의 위험을 줄입니다.   
**세부 정보**: [정책 배포를 감사 모드에서 시작](../../governance/policy/concepts/definition-structure.md#policy-rule)한 다음 이후 **거부** 또는 **수정** 으로 넘어갑니다. **거부** 또는 **수정** 으로 넘어가기 전에 감사 효과를 테스트하고 결과를 검토합니다.

자세한 내용은 [규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)를 참조하세요.

**모범 사례**: 정책 위반을 모니터링하고 신속하게 수정 조치가 취해졌는지 감독하는 담당 역할이 누구인지 확인합니다.   
**세부 정보**: 담당자가 [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) 또는 [명령줄](../../governance/policy/how-to/get-compliance-data.md#command-line)을 통해 규정 준수를 모니터링하도록 합니다.

**모범 사례**: Azure Policy는 조직의 서면 정책을 기술적으로 표현한 것입니다. 모든 Azure Policy 정의를 조직의 정책에 매핑하여 혼란을 줄이고 일관성을 높입니다.   
**세부 정보**: [정책 설명](../../governance/policy/concepts/definition-structure.md#display-name-and-description) 또는 [이니셔티브 정의](../../governance/policy/concepts/initiative-definition-structure.md#metadata) 설명에 조직 정책에 대한 참조를 추가하여 조직의 문서 또는 Azure Policy의 매핑을 기록으로 남깁니다.

## <a name="monitor-azure-ad-risk-reports"></a>Azure AD 위험 보고서 모니터링
대부분의 보안 침해는 공격자가 사용자의 ID를 도용하여 환경에 대한 액세스 권한을 얻을 때 발생합니다. 손상된 ID를 검색하는 것은 쉬운 작업이 아닙니다. Azure AD는 적응형 기계 학습 알고리즘 및 추론을 사용하여 사용자 계정과 관련된 의심스러운 동작을 감지합니다. 검색된 각 의심스러운 동작은 [위험 검색](../../active-directory/identity-protection/overview-identity-protection.md)이라는 레코드에 저장됩니다. 위험 검색은 Azure AD 보안 보고서에 기록됩니다. 자세한 내용은 [위험에 노출된 사용자 보안 보고서](../../active-directory/identity-protection/overview-identity-protection.md) 및 [위험한 로그인 보안 보고서](../../active-directory/identity-protection/overview-identity-protection.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure 보안 모범 사례 및 패턴](best-practices-and-patterns.md)에서 Azure를 사용하여 클라우드 솔루션을 디자인하고, 배포하고, 관리할 때 사용할 수 있는 더 많은 보안 모범 사례를 참조하세요.

Azure 보안 및 관련 Microsoft 서비스에 대한 보다 일반적인 정보를 제공하는 다음 리소스도 확인할 수 있습니다.
* [Azure 보안 팀 블로그](/archive/blogs/azuresecurity/) – Azure Security 관련 최신 정보를 확인할 수 있습니다.
* [Microsoft 보안 응답 센터](https://technet.microsoft.com/library/dn440717.aspx) - Azure와 관련된 문제를 비롯한 Microsoft 보안 취약점을 보고하거나 secure@microsoft.com으로 이메일을 보낼 수 있습니다.
