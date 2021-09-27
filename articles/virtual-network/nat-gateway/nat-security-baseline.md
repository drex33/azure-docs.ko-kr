---
title: Virtual Network NAT에 대한 Azure 보안 기준
description: Virtual Network NAT 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5edab35023afeca68cff20a82307afaf4bbad535
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784245"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Virtual Network NAT에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../../security/benchmarks/overview-v1.md) 의 지침을 Microsoft Virtual Network NAT에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure Security Benchmark에서 정의한 **보안 제어** 및 Virtual Network NAT에 적용되는 관련 지침에 따라 그룹화됩니다. Virtual Network NAT에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

 
Virtual Network NAT가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 [전체 Virtual Network NAT 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷, NIC의 구성과 트래픽을 모니터링하고 기록

**지침**: Virtual Network NAT는 nsg (네트워크 보안 그룹)를 사용 하 여 아웃 바운드 트래픽 제어를 지원 합니다.  인바운드 트래픽은 아웃바운드로 시작된 흐름에 대한 응답으로만 허용됩니다.

또한 NSG (네트워크 보안 그룹) 흐름 로그를 NAT 게이트웨이 리소스와 함께 사용 하 여 아웃 바운드 시작 된 트래픽을 모니터링할 수 있습니다.

Azure Security Center를 사용해 네트워크 보호 권장 사항을 따라 Azure 네트워크 리소스를 보호합니다. 네트워크 보안 그룹 흐름 로그를 사용하도록 설정하고 감사를 위해 로그를 Azure Storage 계정으로 보냅니다. 또한 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점은 네트워크 활동을 시각화하고, 핫스폿 및 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해 하고, 네트워크 구성이 잘못 구성된 곳을 특정할 수 있다는 것입니다. 

- [Virtual Network NAT 개요](./nat-overview.md)

- [NAT 게이트웨이 리소스](./nat-gateway-resource.md)

- [네트워크 보안 그룹 흐름 로그를 사용하도록 설정하는 방법](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../../security-center/security-center-network-recommendations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Network Watcher 패킷 캡처를 사용하도록 설정하여 비정상적인 활동을 조사할 수 있습니다. 

- [Network Watcher 인스턴스를 만드는 방법](../../network-watcher/network-watcher-create.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: 기본 제공 또는 사용자 지정 Azure Policy 정의 및 할당을 사용하여 NAT 게이트웨이 리소스로 구성된 서브넷에 대한 표준 보안 구성을 정의하고 구현합니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

- [네트워킹을 위한 Azure Policy 샘플](../../governance/policy/samples/built-in-policies.md#network)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 리소스 구성을 모니터링하고 NAT 게이트웨이 리소스 및 가상 네트워크 리소스에 대한 변경 내용을 검색합니다. 중요 한 리소스가 변경될 때 사용자에게 알리도록 Azure Monitor에 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor를 통해 Virtual Network NAT와 관련된 로그를 수집하여 엔드포인트 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성된 보안 데이터를 집계합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리하고 수행하며 장기 및 보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.

또는 이 데이터를 Azure Sentinel 또는 타사 SIEM에 사용하도록 설정하거나 온보드할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md) 

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**참고**: 자동으로 사용할 수 있는 활동 로그에는 읽기 작업(GET)을 제외한 NAT 게이트웨이 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)이 포함됩니다. 활동 로그를 사용하여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure의 로깅 및 기타 로그 형식 이해](../../azure-monitor/essentials/platform-logs-overview.md) 

Virtual Network NAT는 현재 고객이 구성할 수 있는 추가 진단 로그를 생성하지 않습니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 보안 로그 및 이벤트에서 발견된 비정상적인 활동을 모니터링하고 경고하기 위해 Log Analytics 작업 영역에서 Azure Security Center를 사용합니다. 대신, Azure Sentinel을 사용하고 데이터를 이곳으로 온보딩할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md) 

- [Azure Security Center의 경고 관리 방법](../../security-center/security-center-managing-and-responding-alerts.md) 

- [로그 분석 로그 데이터에 대해 경고하는 방법](../../azure-monitor/alerts/alerts-log.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트, 프로토콜 등)를 쿼리 및 검색합니다. 

테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Resource Graph 쿼리](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure 리소스, 리소스 그룹, 구독에 태그를 적용하여 논리적인 분류법으로 구성합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: Azure 리소스 관리를 위한 태그를 적용합니다.

- [추가 Azure 구독을 만드는 방법](../../cost-management-billing/manage/create-subscription.md) 

- [관리 그룹을 만드는 방법](../../governance/management-groups/create-management-group-portal.md) 

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지

**지침**: 현재 사용할 수 없음

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy를 사용하여 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정합니다.
Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md) 
- [Azure Graph를 사용하여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy를 사용하여 환경에서 프로비전할 수 있는 서비스를 제한합니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../../governance/policy/samples/built-in-policies.md#general)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: 해당 없음. Virtual Network NAT에 보안 구성이 없습니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure Policy 정의, Azure Resource Manager 템플릿 및 Desired State Configuration 스크립트와 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스에 액세스하려면 Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory), 또는 TFS와 통합된 경우 Active Directory에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 하나 이상의 그룹에 권한을 부여하거나 거부할 수 있습니다. 

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow) 

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: 해당 없음. Virtual Network NAT에 보안 구성이 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 제품에 대한 인시던트 대응 절차를 만들어 적절한 인시던트 대응 프로세스를 수행할 수 있게 하고, 인시던트가 해결될 때까지 적절한 수준의 우선 순위를 배정합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: 인시던트 채점 및 우선 순위 지정 절차를 수행하여 적절한 위험 또는 위협 채점을 수행하여 인시던트 해결 시 적절한 수준의 우선 순위를 받을 수 있도록 해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 인시던트가 해결될 때까지 적절한 수준의 우선 순위로 적절한 완화가 발생할 수 있도록 인시던트 보안 대응 절차를 생성하고 제품에 대해 테스트해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: 인시던트가 해결될 때까지 적절한 수준의 우선 순위로 적절한 완화가 발생할 수 있도록 인시던트 보안 대응 절차를 생성하고 제품에 대해 테스트해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 인시던트가 해결될 때까지 적절한 수준의 우선 순위로 적절한 완화가 발생할 수 있도록 인시던트 보안 대응 절차를 생성하고 제품에 대해 테스트해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: 인시던트가 해결될 때까지 적절한 수준의 우선 순위로 적절한 완화가 발생할 수 있도록 인시던트 보안 대응 절차를 생성하고 제품에 대해 테스트해야 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 침투 테스트가 Microsoft 정책을 위반하지 않도록 Microsoft Cloud 침투 테스트 참여 규칙을 따릅니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
