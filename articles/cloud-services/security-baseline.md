---
title: Azure Cloud Services에 대한 Azure 보안 기준
description: Azure Cloud Services 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 814514ede598d0de435c767ee29ef6aa4564d003
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616577"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Azure Cloud Services에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Microsoft Azure Cloud Services에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure Security Benchmark 및 Cloud Services에 적용되는 관련 지침으로 정의된 **보안 컨트롤** 에 따라 그룹화됩니다. Cloud Services에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

 
Cloud Services가 완전히 Azure Security Benchmark에 매핑되는 방법을 보려면 [전체 Cloud Services 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 신뢰할 수 있는 포트 및 IP 범위에 따라 격리를 적용하도록 별도의 퍼블릭 및 프라이빗 서브넷을 사용하여 클래식 Azure Virtual Network를 만듭니다. 이러한 가상 네트워크 및 서브넷은 현재 Azure Resource Manager 리소스가 아니라 클래식 Virtual Network(클래식 배포) 기반 리소스여야 합니다.  

트래픽 방향, 프로토콜, 원본 주소 및 포트, 대상 주소 및 포트 기반의 액세스 제어 규칙을 포함하고 있는 네트워크 보안 그룹을 사용하여 트래픽을 허용하거나 거부합니다. 네트워크 보안 그룹의 규칙은 언제든지 변경할 수 있으며, 변경 내용은 연결된 모든 인스턴스에 적용됩니다.

Microsoft Azure Cloud Services(클래식)는 Azure Resource Manager 가상 네트워크에 배치할 수 없습니다. 그러나 Resource Manager 기반 가상 네트워크와 클래식 배포 가상 네트워크는 피어링을 통해 연결할 수 있습니다. 

- [네트워크 보안 그룹 개요](../virtual-network/network-security-groups-overview.md)

- [가상 네트워크 피어링](./cloud-services-connectivity-and-networking-faq.yml#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services-)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷, NIC의 구성과 트래픽 모니터링 및 기록

**지침**: Azure Cloud Services 구성을 문서화하고 변경 내용을 모니터링합니다. 서비스 구성 파일을 사용하여 서비스의 각 역할에 대해 배포할 역할 인스턴스의 수, 구성 설정의 값 및 역할에 연결된 인증서의 지문을 지정합니다. 

서비스가 가상 네트워크의 일부인 경우 가상 네트워킹 구성 파일 뿐 아니라 서비스 구성 파일에도 네트워크에 대한 구성 정보를 제공해야 합니다. 서비스 구성 파일의 기본 확장명은 .cscfg입니다. 클래식 배포에서는 구성 적용을 위한 Azure Policy가 지원되지 않습니다.

서비스 구성 파일(.cscfg)에서 클라우드 서비스의 구성 값을 설정하고 서비스 정의 파일(.csdef)에서 정의를 설정합니다. 서비스 정의 파일을 사용하여 애플리케이션에 대한 서비스 모델을 정의합니다. 역할을 정의합니다. 역할은 클라우드 서비스에서 사용할 수 있으며 서비스 엔드포인트를 지정합니다. 서비스 구성 파일을 사용하여 Azure Cloud Services에 대한 구성을 로깅합니다. 재구성은 ServiceConfig.cscfg 파일을 통해 수행할 수 있습니다. 

지정된 내부 엔드포인트와 통신할 수 있는 역할을 제한하는 선택적 NetworkTrafficRules 요소 서비스 정의를 모니터링합니다. 서비스 정의 파일의 선택적 요소인 NetworkTrafficRules 노드를 구성하여 역할이 서로 통신하는 방법을 지정합니다. 특정 역할의 내부 엔드포인트에 액세스할 수 있는 역할을 제한합니다.  서비스 정의는 변경할 수 없습니다. 

네트워크 보안 그룹 흐름 로그를 사용하도록 설정하고 감사를 위해 로그를 Azure Storage 계정으로 보냅니다. 흐름 로그를 Log Analytics 작업 영역으로 보내고 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 패턴에 대한 인사이트를 제공합니다. 트래픽 분석의 장점은 네트워크 활동을 시각화하고, 핫스폿 및 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해 하고, 네트워크 구성이 잘못 구성된 곳을 특정할 수 있다는 것입니다.

- [Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해](../azure-resource-manager/management/deployment-models.md)

- [Cloud Services 구성 파일](schema-cscfg-file.md)

- [Azure Policy에서 지원하는 서비스 목록](/cli/azure/azure-services-the-azure-cli-can-manage)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: Microsoft는 Azure Cloud Services와 고객이 데이터를 주고받을 때 TLS(전송 계층 보안) 프로토콜 v1.2를 사용하여 데이터를 보호합니다. Microsoft 데이터 센터는 Azure 서비스에 연결되는 클라이언트 시스템과 TLS 연결을 협상합니다. TLS는 강력한 인증, 메시지 개인 정보 및 무결성(메시지 변조, 가로채기 및 위조의 검색 가능), 상호 운용성, 알고리즘 유연성, 배포 및 사용 편의성을 제공합니다.

- [암호화 기본 사항](../security/fundamentals/encryption-overview.md)

- [TLS/SSL 인증서 구성](cloud-services-configure-ssl-certificate-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Cloud는 DDoS(분산 서비스 거부 공격) 공격으로부터 플랫폼 서비스를 보호하기 위해 다중 계층 네트워크 보안을 구현합니다. Azure DDoS Protection은 Azure Cloud의 연속 모니터링 프로세스의 일부이며 침투 테스트를 통해 지속적으로 개선됩니다. 이 DDoS Protection은 외부 공격뿐 아니라 다른 Azure 테넌트에서 이루어지는 공격에도 대응하도록 설계되었습니다. 

Azure Cloud Services 내의 플랫폼 수준 보호 외에도 통신을 차단하거나 거부하는 몇 가지 방법이 있습니다. 이러한 항목은 다음과 같습니다. 

-  일부 특정 IP 주소를 선택적으로 차단하는 시작 작업을 만듭니다.
-  IIS web.config 파일을 수정하여 지정된 IP 주소에 대한 Azure 웹 역할 액세스를 제한합니다.

기본 URL 또는 Cloud Services의 이름(예:. *.cloudapp.net)으로 들어오는 트래픽을 차단합니다. Cloud Services 정의 파일(* .csdef)의 사이트 바인딩 구성에서 호스트 헤더를 사용자 지정 DNS 이름으로 설정합니다.

클래식 구독 관리자 할당에 대한 거부 적용 규칙을 구성합니다. 기본적으로 내부 엔드포인트가 정의된 후에 어떤 역할에서도 제한 없이 역할의 내부 엔드포인트로 통신할 수 있습니다. 통신을 제한하려면 서비스 정의 파일의 ServiceDefinition 요소에 NetworkTrafficRules 요소를 추가해야 합니다.

- [내 클라우드 서비스의 기본 URL에 들어오는 트래픽을 차단하거나 사용하지 않도록 설정하려면 어떻게 해야 하나요?](./cloud-services-connectivity-and-networking-faq.yml#how-can-i-block-disable-incoming-traffic-to-the-default-url-of-my-cloud-service-)

- [Azure DDOS Protection](./cloud-services-connectivity-and-networking-faq.yml#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service-)

- [특정 IP 주소 차단](./cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Azure 네트워크를 모니터링할 수 있는 네트워크 성능 모니터링, 진단 및 분석 서비스인 Azure Network Watcher를 사용합니다. 요청 시 네트워크 트래픽을 캡처하고 Azure Virtual Machines의 기타 고급 기능을 사용하려면 Network Watcher 에이전트 가상 머신 확장이 필요합니다. Network Watcher 에이전트 가상 머신 확장을 설치하고 네트워크 보안 그룹 흐름 로그를 켭니다.

네트워크 보안 그룹에 대한 흐름 로깅을 구성합니다. 클래식 배포 모델을 통해 배포된 기존 가상 머신에 Network Watcher 가상 머신 확장을 배포하는 방법에 대한 세부 정보를 검토합니다.

- [네트워크 보안 그룹에 대한 흐름 로깅 구성](../virtual-machines/extensions/network-watcher-linux.md)

- [흐름 로그를 구성하는 방법에 대한 자세한 내용](/cli/azure/azure-services-the-azure-cli-can-manage)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Azure Cloud Services는 기본 제공 IDS 또는 IPS 기능을 제공하지 않습니다. 고객은 조직의 요구 사항에 따라 Azure Marketplace에서 네트워크 기반 IDS 또는 IPS 보조 솔루션을 선택하여 배포할 수 있습니다. 타사 솔루션을 사용하는 경우 적절한 작업 및 기능을 보장할 수 있도록 Azure Cloud Services를 사용하여 선택한 IDS 또는 IPS 솔루션을 철저하게 테스트해야 합니다.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: Azure Cloud Services에 첨부되는 서비스 인증서는 서비스와 안전하게 통신할 수 있도록 해줍니다. 이러한 인증서는 서비스 정의에 정의되며 웹 역할 인스턴스를 실행하는 가상 머신에 자동으로 배포됩니다. 예를 들어 웹 역할의 경우 노출된 HTTPS 엔드포인트를 인증할 수 있는 서비스 인증서를 사용할 수 있습니다. 

인증서를 업데이트하려면 새 인증서를 업로드하고 서비스 구성 파일의 지문 값을 변경하기만 하면 됩니다.

가장 일반적으로 사용되는 데이터 보호 방법인 TLS 1.2 프로토콜을 사용하여 기밀성과 무결성 보호를 제공합니다. 

일반적으로 웹 애플리케이션을 보호하고 OWASP Top 10과 같은 공격으로부터 지키려면 Azure Web Application Firewall을 사용하는 Azure Application Gateway를 배포하여 웹 애플리케이션을 보호하면 됩니다. 

- [서비스 인증서](cloud-services-certs-create.md)

- [Azure에서 응용 프로그램용 TLS 구성](cloud-services-configure-ssl-certificate-portal.md)

- [Application Gateway 배포 방법](../application-gateway/quick-create-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Cloud Services 구성을 확정하고 변경 내용을 모니터링합니다. 서비스 구성 파일은 서비스의 각 역할에 대해 배포할 역할 인스턴스의 수, 구성 설정의 값 및 역할에 연결된 인증서의 지문을 지정합니다. 

서비스가 가상 네트워크의 일부인 경우, 가상 네트워킹 구성 파일 뿐 아니라 서비스 구성 파일에도 네트워크에 대한 구성 정보를 제공해야 합니다. 서비스 구성 파일의 기본 확장명은 .cscfg입니다.

Azure Policy는 Azure Cloud Services에서 구성을 적용하는 데 사용할 수 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 네트워크 보안 그룹을 사용하여 Azure 가상 네트워크에서 Azure 리소스와 주고받는 네트워크 트래픽을 필터링할 수 있습니다. 네트워크 보안 그룹에는 여러 종류의 Azure 리소스에서 오는 인바운드 트래픽 또는 이러한 리소스로 나가는 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙이 포함됩니다. 규칙마다 원본 및 대상, 포트, 프로토콜을 지정할 수 있습니다.

Azure Cloud Services 내에서 개별 네트워크 보안 그룹 규칙에 대한 "설명" 필드를 사용하여 네트워크와 주고받는 트래픽을 허용하는 규칙을 문서화합니다.

- [네트워크 보안 그룹 규칙을 사용하여 네트워크 트래픽을 필터링하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure Traffic Manager의 기본 제공 엔드포인트 모니터링 및 자동 엔드포인트 장애 조치(failover) 기능을 사용합니다. 이러한 기능은 엔드포인트 및 Azure 지역 오류에 대한 복원력이 있는 고가용성 애플리케이션을 제공하는 데 도움이 됩니다. 엔드포인트 모니터링을 구성하려면 Traffic Manager 프로필에서 특정 설정을 지정해야 합니다.

Azure의 플랫폼 로그인 활동 로그에서 구독 수준 이벤트에 대한 인사이트를 얻습니다. 활동 로그에는 리소스가 수정되거나 가상 머신이 시작되는 등의 이벤트 정보가 포함됩니다. Azure Portal에서 활동 로그를 보거나 PowerShell 및 CLI를 사용하여 항목을 검색합니다. 

Azure Monitor 로그에 대한 활동 로그를 Azure Event Hubs로 보내 Azure 외부로 전달하거나 Azure Storage로 보내 보관하도록 진단 설정을 만듭니다. Azure Cloud Services의 중요한 리소스가 변경될 때 알림 경고를 제공하도록 Azure Monitor를 구성합니다. 

- [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기](../azure-monitor/alerts/alerts-activity-log.md)

- [Traffic Manager 모니터링](../traffic-manager/traffic-manager-monitoring.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft가 Azure Cloud Services용 Azure 리소스에 대한 시간 원본을 유지 관리합니다. 고객은 UDP 프로토콜을 사용하는 포트 123을 통해 환경에서 사용되는 시간 서버에 대한 액세스를 허용하는 네트워크 규칙을 만들어야 할 수도 있습니다.

- [NTP 서버 액세스](../firewall/protect-azure-virtual-desktop.md#additional-considerations)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Event Hubs를 사용하여 프로그래밍 방식으로 클라우드 서비스 스트리밍 데이터를 사용합니다. 이 모든 데이터를 통합하고 Azure Sentinel에 전송하여 로그를 모니터링하고 검토하거나 타사 SIEM을 사용합니다. 중앙에서 보안 로그를 관리할 수 있도록, 선택한 Azure Security Center 데이터를 Azure Event Hubs에 연속으로 내보내도록 구성하고 SIEM의 적절한 커넥터를 설정합니다. 다음은 타사 도구를 포함하여 Azure Sentinel에 사용되는 몇 가지 옵션입니다.

- Azure Sentinel - 기본 Security Center 경고 데이터 커넥터 사용
- Splunk - Splunk용 Azure Monitor 추가 기능 사용
- IBM QRadar - 수동으로 구성된 로그 원본 사용
- ArcSight – SmartConnector 사용

Azure Sentinel에서 사용 가능한 커넥터에 대한 자세한 내용은 Azure Sentinel 설명서를 검토하세요. 

- [데이터 원본 연결](../sentinel/connect-data-sources.md)

- [SIEM과 통합](../security-center/continuous-export.md)

- [진단 데이터 저장](diagnostics-extension-to-storage.md)

- [Azure Event Hubs를 통해 SIEM 통합 구성](../security-center/continuous-export.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Cloud Services를 실행하는 가상 머신 인스턴스를 포함하여 가상 머신의 시스템 및 로깅 데이터를 캡처하는 Azure Cloud Services 문제를 해결하기 위한 Azure Diagnostics를 설정하도록 Visual Studio를 구성합니다. 진단 데이터는 고객이 선택하는 스토리지 계정으로 전송됩니다. Azure Cloud Services 프로젝트에서 진단을 켠 후 배포합니다.

 
Azure Monitor 내의 활동 로그에서 일부 이벤트의 변경 기록을 살펴봅니다. 이벤트 기간 중에 발생한 변경 내용을 감사합니다. 변경 기록(미리 보기) 탭을 사용하여 자세히 검사하려면 활동 로그에서 이벤트를 선택합니다. Visual Studio에서 Azure Cloud Services를 게시할 때 Application Insights에 진단 데이터를 보냅니다. 이때 Application Insights Azure 리소스를 만들거나 기존 Azure 리소스에 데이터를 보냅니다. 

Application Insights를 사용하여 Azure Cloud Services의 가용성, 성능, 실패 및 사용량을 모니터링할 수 있습니다. 사용자 지정 차트를 Application Insights에 추가하여 가장 중요한 데이터를 볼 수 있습니다. Application Insights SDK를 사용하여 Azure Cloud Services 프로젝트에서 역할 인스턴스 데이터를 수집할 수 있습니다. 

- [Visual Studio에서 진단을 켠 후에 배포](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [변경 기록 보기](../azure-monitor/essentials/activity-log.md#view-change-history)

- [Azure Cloud 서비스용 Application Insights(클래식)](../azure-monitor/app/cloudservices.md)

- [Azure Cloud 서비스(클래식) 및 가상 머신에 대한 진단 설정](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: 5분, 1시간 및 12시간 간격으로 추가 메트릭을 샘플링하고 수집할 수 있는 고급 모니터링을 Azure Cloud Services와 함께 사용할 수 있습니다. 집계된 데이터는 스토리지 계정의 테이블에 저장되고, 10일 후에 삭제됩니다. 그러나 사용되는 스토리지 계정은 역할별로 구성되므로 역할마다 다른 스토리지 계정을 사용할 수 있습니다. 이러한 내용은 .csdef 및 .cscfg 파일에 연결 문자열로 구성됩니다.

고급 모니터링에서는 모니터링하려는 역할에 Azure Diagnostics 확장(Application Insights SDK는 선택 사항)을 사용합니다. 진단 확장은 diagnostics.wadcfgx라는 구성 파일(역할당)을 사용하여 모니터링되는 진단 메트릭을 구성합니다. Azure 진단 확장은 데이터를 수집하고 Azure Storage 계정에 저장합니다. 이러한 설정은 .wadcfgx, .csdef 및 .cscfg 파일에서 구성됩니다.

- [클라우드 서비스 모니터링 소개](cloud-services-how-to-monitor.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure Cloud Services에 기본 또는 고급 모니터링 모드를 사용할 수 있습니다. Azure Cloud Services는 호스트 가상 머신에서 기본 모니터링 데이터(CPU 비율, 네트워크 입력/출력 및 디스크 읽기/쓰기)를 자동으로 수집합니다. Azure Portal에 있는 클라우드 서비스의 개요 및 메트릭 페이지에서 수집된 모니터링 데이터를 살펴봅니다. 

Azure Diagnostics 확장을 사용하는 동안 애플리케이션 로그, 성능 카운터 등과 같은 진단 데이터를 수집하도록 Azure Cloud Services에서 진단을 사용합니다. Set-AzureServiceDiagnosticsExtension cmdlet을 사용하여 이미 실행 중인 클라우드 서비스에서 진단 구성을 사용 또는 업데이트하거나 진단 확장을 사용하여 클라우드 서비스를 자동으로 배포합니다. 필요에 따라 Application Insights SDK를 설치합니다. Azure Monitor로 성능 카운터를 보냅니다.

Azure 진단 확장은 데이터를 수집하고 Azure Storage 계정에 저장합니다. 진단 데이터는 영구적으로 저장되지 않으므로 진단 데이터를 Microsoft Azure Storage 에뮬레이터 또는 Azure Storage에 전송합니다. 스토리지에 저장한 후에는 Visual Studio의 서버 탐색기, Microsoft Azure Storage Explorer, Azure Management Studio 등과 같은 사용 가능한 여러 도구 중 하나를 사용하여 진단 데이터를 볼 수 있습니다. 진단 확장의 diagnostics.wadcfgx라는 구성 파일(역할당)을 사용하여 모니터링할 진단 메트릭을 구성합니다. 

- [클라우드 서비스 모니터링 소개](cloud-services-how-to-monitor.md)

- [작업자 역할에서 진단을 사용하도록 설정하는 방법 - SIEM과 통합](../security-center/continuous-export.md)

- [PowerShell을 사용하여 Azure Cloud Services에 진단 사용](cloud-services-diagnostics-powershell.md)

- [Azure Storage에서 진단 데이터 저장 및 보기](./diagnostics-extension-to-storage.md?preserve-view=)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure Sentinel 또는 타사 SIEM과 통합하고 비정상적인 활동에 대해 경고를 사용하도록 설정하여 Azure Cloud Services 로그 데이터를 모니터링할 수 있습니다.

- [SIEM과 통합](../security-center/continuous-export.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: Azure용 Microsoft 맬웨어 방지 프로그램은 Azure Cloud Services 및 가상 머신을 보호합니다. 웹 애플리케이션 방화벽, 네트워크 방화벽, 맬웨어 방지 프로그램, 침입 감지 및 방지 시스템(IDS 또는 IPS) 등과 같은 타사 보안 솔루션을 배포하는 옵션이 있습니다.

- [Azure 기본 IPS/IDS 및 DDOS에서 제공하는 기능](./cloud-services-configuration-and-management-faq.yml#what-are-the-features-and-capabilities-that-azure-basic-ips-ids-and-ddos-provides-)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 리소스에 대한 액세스를 관리하는 것이 좋습니다. 그러나 Azure Cloud Services는 Azure Resource Manager 기반 서비스가 아니라서 Azure RBAC 모델을 지원하지 않으며 클래식 구독을 사용해야 합니다.

기본적으로 계정 관리자, 서비스 관리자 및 공동 관리자는 Azure의 세 가지 클래식 구독 관리자 역할입니다. 

클래식 구독 관리자는 Azure 구독에 대한 모든 권한을 보유합니다. 이들은 Azure Portal, Azure Resource Manager API 및 클래식 배포 모델 API를 사용하여 리소스를 관리할 수 있습니다. Azure에 등록하는 데 사용된 계정은 자동으로 계정 관리자와 서비스 관리자로 설정됩니다. 나중에 공동 관리자를 추가할 수 있습니다. 

서비스 관리자 및 공동 관리자는 구독 범위에서 소유자 역할(Azure 역할)이 할당된 사용자와 동일한 액세스 권한을 갖습니다. Azure Portal에서 클래식 관리자 탭을 사용하여 공동 관리자를 관리하거나 서비스 관리자를 봅니다. 

PowerShell에서 다음 명령을 사용하여 클래식 서비스 관리자 및 공동 관리자에 대한 역할 할당을 나열합니다.

Get-AzRoleAssignment -IncludeClassicAdministrators

클래식 구독 관리 역할 간의 차이점을 검토합니다. 

- [세 가지 클래식 구독 관리 역할의 차이점](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 사용 가능한 역할 및 Azure Cloud Services 리소스를 운영하고 관리하는 데 필요한 권한에 따라 전용 관리 계정 사용에 대한 표준 운영 절차를 만드는 것이 좋습니다.

- [클래식 구독 관리 역할의 차이점](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure Cloud Services에서 실행되는 애플리케이션의 별도 ID를 관리하지 않습니다. 사용자가 여러 ID와 자격 증명을 관리할 필요가 없도록 Single Sign-On을 구현합니다.

- [SSO(Single Sign-On)란?](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 상승된 권한이 필요한 관리 작업에는 안전한 Azure 관리형 워크스테이션(Privileged Access Workstation이라고도 함)을 사용하는 것이 좋습니다.

- [안전한 Azure 관리형 워크스테이션 이해](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: Azure Cloud Service REST API를 사용하여 중요한 정보에 대한 Azure Cloud Service 리소스 목록을 작성합니다. 배포된 클라우드 서비스 리소스를 폴링하여 구성 및 .pkg 리소스를 가져옵니다.

 예를 들어 몇 가지 API가 아래에 나열되어 있습니다.

- 배포 가져오기 - 배포 가져오기 작업은 배포의 구성 정보, 상태 및 시스템 속성을 반환합니다.
- 패키지 가져오기 - 패키지 가져오기 작업은 배포에 대한 클라우드 서비스 패키지를 검색하고 패키지 파일을 Microsoft Azure Blob 스토리지에 저장합니다.
- 클라우드 서비스 속성 가져오기 - 클라우드 서비스 속성 가져오기 작업은 지정된 클라우드 서비스에 대한 속성을 검색합니다.

Azure Cloud Service REST API 설명서를 검토하고 조직의 요구 사항에 따라 중요한 정보의 데이터 보호를 위한 프로세스를 만듭니다.

- [배포 가져오기](/rest/api/compute/cloudservices/rest-get-deployment)

- [클라우드 서비스 속성 가져오기](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Get Package](/rest/api/compute/cloudservices/rest-get-package)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: Azure Cloud Services의 환경 형식 및 데이터 민감도 수준과 같은 개별 보안 도메인에 별도의 구독 및 관리 그룹을 사용하여 격리를 구현합니다.

Azure Cloud Service의 Certificate 요소에서 "permissionLevel"을 편집하여 역할 프로세스에 부여되는 액세스 권한을 지정할 수도 있습니다. 관리자 권한 프로세스만 프라이빗 키에 액세스할 수 있도록 하려면 관리자 권한을 지정합니다. limitedOrElevated 권한을 사용하면 모든 역할 프로세스가 프라이빗 키에 액세스할 수 있습니다. 가능한 값은 limitedOrElevated 또는 관리자 권한입니다. 기본값은 limitedOrElevated입니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [WebRole 스키마](./schema-csdef-webrole.md#Certificate)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 네트워크 경계에서 Azure Marketplace의 타사 솔루션을 사용하여 중요한 정보의 무단 전송 여부를 모니터링하고 그러한 전송을 차단하며 정보 보안 전문가에게 경고하는 것이 좋습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure Cloud Services에 대한 TLS v2를 구성합니다. Azure Portal을 사용하여 스테이징된 Azure Cloud Services 배포에 인증서를 추가하고 서비스의 CSDEF 및 CSCFG 파일에 인증서 정보를 추가합니다. 애플리케이션을 다시 패키징하고, 새 패키지를 사용하도록 스테이징된 배포를 업데이트합니다. 

Azure Cloud Services에 첨부되는 Azure의 서비스 인증서를 사용하여 서비스와 안전하게 통신할 수 있도록 합니다. 노출된 HTTPS 엔드포인트를 인증할 수 있는 인증서를 제공합니다. 클라우드 서비스의 서비스 정의에서 서비스 인증서를 정의하고, 가상 머신에 자동으로 배포하고, 역할 인스턴스를 실행합니다.

관리 인증서를 사용하여 관리 API로 인증합니다. 관리 인증서를 사용하면 클래식 배포 모델로 인증할 수 있습니다. Visual Studio 또는 Azure SDK와 같은 많은 프로그램 및 도구에서 이러한 인증서를 사용하여 다양한 Azure 서비스의 구성 및 배포를 자동화합니다. 

추가 참조를 위해 클래식 배포 모델 API는 Azure Portal을 통해 클래식 배포 모델 기능에 프로그래밍 방식으로 액세스할 수 있습니다. Python용 Azure SDK를 사용하여 Azure Cloud Services 및 Azure Storage 계정을 관리할 수 있습니다. Python용 Azure SDK는 클래식 배포 모델 API인 REST API 래핑합니다. 모든 API 작업은 TLS를 통해 수행되고 X.509 v3 인증서를 사용하여 서로 인증됩니다. 관리 서비스는 Azure에서 실행되는 서비스 내에서 액세스할 수 있습니다. HTTPS 요청을 보내고 HTTPS 응답을 받을 수 있는 애플리케이션에서 인터넷을 통해 직접 액세스할 수도 있습니다.

- [Azure에서 애플리케이션에 대한 TLS 구성](cloud-services-configure-ssl-certificate-portal.md)

- [Python의 클래식 배포 모델 사용](cloud-services-python-how-to-use-service-management.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 타사 활성 검색 도구를 사용하여 현장 또는 원격 서비스 공급자를 비롯하여 조직의 기술 시스템에서 저장, 처리 또는 전송하는 중요한 정보를 모두 식별하고 조직의 중요한 정보 인벤토리를 업데이트하는 것이 좋습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 클라우드 서비스(클래식)에는 해당되지 않습니다. 데이터 손실 방지를 적용하지 않습니다.

시스템에서 데이터를 복사하는 경우에도 데이터에 대한 액세스 제어를 적용하도록 자동화된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현하는 것이 좋습니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 투자합니다. Azure의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Cloud Services는 미사용 암호화를 지원하지 않습니다. Azure Cloud Services는 상태 비저장으로 설계되었기 때문입니다. Azure Cloud Services는 외부 스토리지(예: 기본적으로 미사용 암호화를 사용하는 Azure Storage)를 지원합니다.  

임시 디스크에 저장된 애플리케이션 데이터는 암호화되지 않습니다. 고객은 필요에 따라 이 데이터를 관리하고 암호화할 책임이 있습니다.  

- [Azure의 저장 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor에서 클래식 메트릭 경고를 사용하여 중요한 리소스에 적용된 메트릭 중 하나가 임계값을 초과할 때 알림을 받을 수 있습니다. 클래식 메트릭 경고는 무차원 메트릭에 대해서만 경고하는 이전 기능입니다. 클래식 메트릭 경고보다 향상된 기능을 가진 메트릭 경고라는 최신 기능도 있습니다. 

뿐만 아니라 Application Insights는 Azure Cloud Services 앱의 가용성, 성능, 실패 및 사용량을 모니터링할 수 있습니다. 여기에는 Application Insights SDK의 데이터와 Azure Cloud Services의 Azure Diagnostics 데이터를 결합한 데이터가 사용합니다.

- [Azure Monitor를 사용하여 클래식 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-classic-portal.md)

- [메트릭 경고 개요](../azure-monitor/alerts/alerts-metric-overview.md) 

- [Azure Cloud 서비스용 Application Insights(클래식)](../azure-monitor/app/cloudservices.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 이 정보는 PaaS(Platform as a Service)를 사용하는 Azure Cloud Services 작업자 및 웹 역할에 대한 Azure 게스트 운영 체제와 관련이 있습니다. 그러나 IaaS(서비스 제공 인프라)를 사용하는 Virtual Machines에는 적용되지 않습니다.

기본적으로 Azure는 Windows Server 2016 같은 고객의 게스트 운영 체제를 서비스 구성(.cscfg)에 지정한 운영 체제 제품군 내에서 지원되는 최신 이미지로 주기적으로 업데이트합니다.

고객이 Azure Cloud Services 배포에 사용할 특정 운영 체제 버전을 선택하면 자동 운영 체제 업데이트가 해제되며 고객은 패치를 적용할 책임이 있습니다. 고객은 자신의 역할 인스턴스가 업데이트를 수신하고 있는지 확인해야 합니다. 그렇지 않으면 애플리케이션이 보안 취약점에 노출될 수 있습니다.

- [Azure 게스트 OS](cloud-services-guestos-msrc-releases.md)

- [Azure 게스트 OS 지원 가능성 및 사용 중지 정책](cloud-services-guestos-retirement-policy.md)

- [클라우드 서비스(클래식) 구성 방법](cloud-services-how-to-configure-portal.md)

- [게스트 OS 버전 관리](./cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀을 위한 자동화된 패치 관리 솔루션 배포

**지침**: 타사 패치 관리 솔루션을 사용합니다. 현재 환경에서 이미 Configuration Manager를 사용 중인 고객은 System Center Updates Publisher를 사용하여 사용자 지정 업데이트를 Windows Server Update Service에 게시할 수 있습니다. 

이렇게 하면 업데이트 관리에서 타사 소프트웨어를 통해 Configuration Manager를 업데이트 리포지토리로 사용하는 머신을 패치할 수 있습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 고객이 지속적으로 DDoS 공격의 위험 범위를 이해하는 것이 좋습니다. 

다음과 같은 시나리오를 통해 생각해 보세요.

- 보호가 필요한 새로 공개된 Azure 리소스는 무엇인가요?
- 서비스에 단일 실패 지점이 있나요?
- 서비스를 격리하여 공격의 영향을 제한하면서도 유효한 고객에게 서비스를 계속 제공하려면 어떻게 해야 할까요?
- DDoS Protection Standard를 사용하도록 설정해야 하는데 설정하지 않은 가상 네트워크가 있나요?
- 내 서비스가 여러 지역에서 활성화/장애 조치(failover)와 함께 활성화됩니까?

지원 설명서

- [Azure 리소스의 위험 평가](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Cloud Services에는 해당되지 않습니다. 이 권장 사항은 IaaS 컴퓨팅 리소스에 적용됩니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 고객은 컴퓨팅 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어를 정의해야 합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Security Center에서 사용할 수 있는 적응형 애플리케이션 제어 기능을 사용합니다. 적응형 애플리케이션 제어는 Windows 및 Linux, Azure 및 비 Azure 머신에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Security Center의 자동화된 인텔리전트 엔드투엔드 솔루션입니다. 또한 맬웨어로부터 머신을 보호하는 데 유용합니다. 

이 기능은 Azure 및 비 Azure Windows(모든 버전, 클래식 또는 Azure Resource Manager)와 Linux 머신에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용하여 머신에서 실행 중인 애플리케이션을 분석하고 인텔리전스를 활용하여 허용 목록을 만듭니다. 이 기능은 애플리케이션 허용 목록 정책의 구성 및 유지 관리 프로세스를 대폭 단순화하며, 다음을 수행할 수 있도록 합니다.
- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.
- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.
- 오래되고 지원되지 않는 앱을 실행하지 않습니다.
- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.
- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

자세한 내용은 참조 링크에서 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure Security Center에서 사용할 수 있는 적응형 애플리케이션 제어 기능을 사용합니다. 적응형 애플리케이션 제어는 Windows 및 Linux, Azure 및 비 Azure 머신에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Security Center의 자동화된 인텔리전트 엔드투엔드 솔루션입니다. 또한 맬웨어로부터 머신을 보호하는 데 유용합니다. 

이 기능은 Azure 및 비 Azure Windows(모든 버전, 클래식 또는 Azure Resource Manager)와 Linux 머신에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용하여 머신에서 실행 중인 애플리케이션을 분석하고 인텔리전스를 활용하여 허용 목록을 만듭니다. 이 기능은 애플리케이션 허용 목록 정책의 구성 및 유지 관리 프로세스를 대폭 단순화하며, 다음을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.

- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.

- 오래되고 지원되지 않는 앱을 실행하지 않습니다.

- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.

- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

자세한 내용은 참조 링크에서 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Security Center에서 사용할 수 있는 적응형 애플리케이션 제어 기능을 사용합니다. 적응형 애플리케이션 제어는 Windows 및 Linux, Azure 및 비 Azure 머신에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Security Center의 자동화된 인텔리전트 엔드투엔드 솔루션입니다. 또한 맬웨어로부터 머신을 보호하는 데 유용합니다. 

이 기능은 Azure 및 비 Azure Windows(모든 버전, 클래식 또는 Azure Resource Manager)와 Linux 머신에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용하여 머신에서 실행 중인 애플리케이션을 분석하고 인텔리전스를 활용하여 허용 목록을 만듭니다. 이 기능은 애플리케이션 허용 목록 정책의 구성 및 유지 관리 프로세스를 대폭 단순화하며, 다음을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.

- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.

- 오래되고 지원되지 않는 앱을 실행하지 않습니다.

- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.

- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

자세한 내용은 참조 링크에서 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Azure Security Center에서 사용할 수 있는 적응형 애플리케이션 제어 기능을 사용합니다. 적응형 애플리케이션 제어는 Windows 및 Linux, Azure 및 비 Azure 머신에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Security Center의 자동화된 인텔리전트 엔드투엔드 솔루션입니다. 또한 맬웨어로부터 머신을 보호하는 데 유용합니다. 

이 기능은 Azure 및 비 Azure Windows(모든 버전, 클래식 또는 Azure Resource Manager)와 Linux 머신에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용하여 머신에서 실행 중인 애플리케이션을 분석하고 인텔리전스를 활용하여 허용 목록을 만듭니다. 이 기능은 애플리케이션 허용 목록 정책의 구성 및 유지 관리 프로세스를 대폭 단순화하며, 다음을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.

- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.

- 오래되고 지원되지 않는 앱을 실행하지 않습니다.

- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.

- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

자세한 내용은 참조 링크에서 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: Azure Security Center에서 사용할 수 있는 적응형 애플리케이션 제어 기능을 사용합니다. 적응형 애플리케이션 제어는 Windows 및 Linux, Azure 및 비 Azure 머신에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Security Center의 자동화된 인텔리전트 엔드투엔드 솔루션입니다. 또한 맬웨어로부터 머신을 보호하는 데 유용합니다. 

이 기능은 Azure 및 비 Azure Windows(모든 버전, 클래식 또는 Azure Resource Manager)와 Linux 머신에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용하여 머신에서 실행 중인 애플리케이션을 분석하고 인텔리전스를 활용하여 허용 목록을 만듭니다. 이 기능은 애플리케이션 허용 목록 정책의 구성 및 유지 관리 프로세스를 대폭 단순화하며, 다음을 수행할 수 있도록 합니다.
- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.
- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.
- 오래되고 지원되지 않는 앱을 실행하지 않습니다.
- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.
- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

자세한 내용은 참조 링크에서 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: Azure Security Center에서 사용할 수 있는 적응형 애플리케이션 제어 기능을 사용합니다. 적응형 애플리케이션 제어는 Windows 및 Linux, Azure 및 비 Azure 머신에서 실행할 수 있는 애플리케이션을 제어하는 데 도움이 되는 Security Center의 자동화된 인텔리전트 엔드투엔드 솔루션입니다. 또한 맬웨어로부터 머신을 보호하는 데 유용합니다. 

이 기능은 Azure 및 비 Azure Windows(모든 버전, 클래식 또는 Azure Resource Manager)와 Linux 머신에서 사용할 수 있습니다.

Security Center는 기계 학습을 사용하여 머신에서 실행 중인 애플리케이션을 분석하고 인텔리전스를 활용하여 허용 목록을 만듭니다. 이 기능은 애플리케이션 허용 목록 정책의 구성 및 유지 관리 프로세스를 대폭 단순화하며, 다음을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.

- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.

- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.

- 오래되고 지원되지 않는 앱을 실행하지 않습니다.

- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.

- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

자세한 내용은 참조 링크에서 확인할 수 있습니다.

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: Azure Cloud Services를 사용하는 중요한 애플리케이션 또는 위험도가 높은 애플리케이션의 경우 별도의 구독 또는 격리를 제공하는 관리 그룹을 구현합니다.

네트워크 보안 그룹을 사용하여 인바운드 보안 규칙을 만들고, http와 같은 서비스를 선택하고, 사용자 지정 포트를 선택하고, 우선 순위 및 이름을 지정합니다. 이 우선 순위는 규칙이 적용되는 순서에 영향을 줍니다. 숫자 값이 작을수록 규칙이 먼저 적용됩니다. 비즈니스 요구 사항에 따라 네트워크 트래픽을 분리하거나 분할하려면 네트워크 보안 그룹을 서브넷 또는 특정 네트워크 인터페이스에 연결해야 합니다.

자세한 내용은 참조 링크에서 확인할 수 있습니다.

- [자습서 - Azure Portal을 사용하여 네트워크 보안 그룹을 통해 네트워크 트래픽 필터링](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Security Center의 권장 사항을 Azure Cloud Services 리소스에 대한 보안 구성 기준으로 사용합니다. 

Azure Portal에서 Security Center, 컴퓨팅 및 앱, Azure Cloud Services를 차례로 선택하여 서비스 리소스에 적용되는 권장 사항을 확인합니다.

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Cloud Services에는 해당되지 않습니다. 클래식 배포 모델을 기반으로 합니다. 보안 Azure 리소스 구성을 유지하려면 타사 솔루션을 사용하는 것이 좋습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure Cloud Service의 구성 파일은 리소스의 운영 특성을 저장합니다. 구성 파일의 복사본을 보안 스토리지 계정에 저장할 수 있습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: Azure Cloud Services에는 해당되지 않습니다. 클래식 배포 모델을 기반으로 하며 Azure Resource Manager 배포 기반 구성 도구를 통해 관리할 수 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구 배포

**지침**: Azure Cloud Services에는 해당되지 않습니다. 이 권장 사항은 IaaS(서비스 제공 인프라) 기반 컴퓨팅 리소스에 적용됩니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center를 사용하여 Azure 리소스에 대한 기준 검사를 수행합니다.  

- [Azure Security Center에서 권장 사항을 수정하는 방법](../security-center/security-center-remediate-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center에서 컴퓨팅 및 앱 기능을 선택하고 가상 머신, 서버 및 컨테이너에 대한 권장 사항을 따릅니다.

- [Azure Security Center 컨테이너 권장 사항 이해](../security-center/container-security.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Cloud Services는 클래식 배포 모델 기반이며 Azure Key Vault와 통합되지 않습니다.

Azure Cloud Services에 사용되는 자격 증명과 같은 비밀을 보호하면 매번 암호를 입력하지 않아도 됩니다. 시작하려면 먼저 일반 텍스트 암호를 지정하고, PowerShell 명령 ConvertTo-SecureString을 사용하여 보안 문자열로 변환합니다. 다음으로, ConvertFrom-SecureString을 사용하여 이 보안 문자열을 암호화된 표준 문자열로 변환합니다.  이제 이 암호화된 표준 문자열은 Set-Content를 사용하여 파일에 저장할 수 있습니다.

또한 Azure Cloud Services에 사용되는 인증서의 프라이빗 키를 안전한 스토리지에 저장하는 것이 좋습니다.

- [PowerShell에서 원격 데스크톱 구성](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 매번 암호를 입력하지 않아도 되도록 Azure Cloud Services에 사용되는 자격 증명과 같은 비밀을 보호합니다. 
 

먼저 일반 텍스트 암호를 지정하고, PowerShell 명령 ConvertTo-SecureString을 사용하여 보안 문자열로 변경합니다. 다음으로, ConvertFrom-SecureString을 사용하여 이 보안 문자열을 암호화된 표준 문자열로 변환합니다. 이제 이 암호화된 표준 문자열은 Set-Content 명령을 사용하여 파일에 저장할 수 있습니다.

Azure Cloud Services에 사용되는 인증서의 프라이빗 키를 안전한 스토리지 위치에 저장합니다.

- [PowerShell에서 원격 데스크톱 구성](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용

**지침**: Azure용 Microsoft Antimalware는 Azure Cloud Services 및 Virtual Machines에 사용할 수 있습니다. 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하는 데 도움이 되는 평가판 실시간 보호 기능입니다. 알려진 악성 또는 원치 않는 소프트웨어가 Azure 시스템에서 스스로의 설치나 실행을 시도할 때 경고를 생성합니다. 

PowerShell 기반 Antimalware cmdlet "Get-AzureServiceAntimalwareConfig"를 사용하여 맬웨어 방지 구성을 가져옵니다.

Azure Cloud Services에서 시작 작업의 PowerShell 스크립트를 사용하여 Antimalware 확장을 사용하도록 설정합니다.

자동화된 인텔리전트 엔드투엔드 솔루션인 Azure Security Center에서 적응형 애플리케이션 제어 기능을 선택합니다. 맬웨어로부터 머신을 보호하는 데 도움이 되며 맬웨어 방지 솔루션에서 놓칠 수 있는 애플리케이션을 포함하여 악성 애플리케이션을 실행하려는 시도를 차단하거나 경고할 수 있습니다.

- [내 Azure Cloud Services에 대한 맬웨어 방지 확장을 자동화된 방식으로 추가하려면 어떻게 해야 하나요?](./cloud-services-configuration-and-management-faq.yml#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way-)

- [맬웨어 방지 프로그램 배포 시나리오](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [적응 애플리케이션 컨트롤](../security-center/security-center-adaptive-application.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 사용자 고유의 인시던트 대응 계획을 만들 수도 있음](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다. 

구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고, Azure 리소스를 명확하게 식별하고 분류할 수 있는 명명 시스템을 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다. 

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md) 

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 침투 테스트가 Microsoft 정책을 위반하지 않도록 Microsoft Cloud 침투 테스트 참여 규칙을 따릅니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
