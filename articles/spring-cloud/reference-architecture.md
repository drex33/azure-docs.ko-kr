---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: karlerickson
title: Azure Spring Cloud 참조 아키텍처
ms.author: akaleshian
ms.service: spring-cloud
description: 이 참조 아키텍처는 Azure Spring Cloud를 사용하기 위한 일반적인 엔터프라이즈 허브 및 스포크 설계를 사용하는 기반입니다.
ms.openlocfilehash: d3124f9e9eac913a333e4ab186c871297e955d1e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469089"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Azure Spring Cloud 참조 아키텍처

이 참조 아키텍처는 Azure Spring Cloud를 사용하기 위한 일반적인 엔터프라이즈 허브 및 스포크 설계를 사용하는 기반입니다. 설계에서 Azure Spring Cloud는 허브에서 호스트되는 공유 서비스에 종속된 단일 스포크에 배포됩니다. 아키텍처는 [Microsoft Azure Well-Architected Framework][16]에서 개념을 얻기 위해 구성 요소를 사용하여 빌드됩니다.

이 아키텍처의 구현에 대한 자세한 내용은 GitHub의 [Azure Spring Cloud 참조 아키텍처][10] 리포지토리를 참조하세요.

이 아키텍처의 배포 옵션에는 ARM(Azure Resource Manager), Terraform, Azure CLI, Bicep 등이 있습니다. 이 리포지토리의 아티팩트는 환경에 맞게 사용자 지정할 수 있는 토대를 제공합니다. Azure Firewall 또는 Application Gateway와 같은 리소스를 다른 리소스 그룹 또는 구독으로 그룹화할 수 있습니다. 이 그룹화를 사용하면 IT 인프라, 보안, 비즈니스 애플리케이션 팀 등 다양한 기능을 별도로 유지할 수 있습니다.

## <a name="planning-the-address-space"></a>주소 공간 계획

Azure Spring Cloud에는 다음과 같은 두 가지 전용 서브넷이 필요합니다.

* 서비스 런타임
* Spring Boot 애플리케이션

이러한 각 서브넷에는 전용 Azure 스프링 클라우드 클러스터가 필요 합니다. 여러 클러스터가 동일한 서브넷을 공유할 수 없습니다. 각 서브넷의 최소 크기는/28입니다. Azure Spring Cloud가 지원할 수 있는 애플리케이션 인스턴스의 수는 서브넷 크기에 따라 달라집니다. [가상 네트워크에서 Azure Spring Cloud 배포][17]의 [가상 네트워크 요구 사항][11] 섹션에서 자세한 VNET(Virtual Network) 요구 사항을 찾을 수 있습니다.

> [!WARNING]
> 선택한 서브넷 크기는 기존 VNET 주소 공간과 겹칠 수 없으며 피어링 또는 온-프레미스 서브넷 주소 범위와 겹칠 수 없습니다.

## <a name="use-cases"></a>사용 사례

이 아키텍처의 일반적인 용도는 다음과 같습니다.

* 개인 응용 프로그램: 하이브리드 클라우드 환경에 배포 된 내부 응용 프로그램
* 공용 응용 프로그램: 외부적으로 연결 되는 응용 프로그램

이러한 사용 사례는 보안과 네트워크 트래픽 규칙을 제외하고 유사합니다. 이 아키텍처는 각각의 미묘한 차이를 지원하도록 설계되었습니다.

## <a name="private-applications"></a>개인 애플리케이션

다음 목록에서는 개인 애플리케이션의 인프라 요구 사항에 대해 설명합니다. 이러한 요구 사항은 매우 규제된 환경에서 일반적입니다.

* 서브넷은 Azure Spring Cloud의 인스턴스를 하나만 포함해야 합니다.
* 하나 이상의 보안 벤치마크에 대한 준수를 적용해야 합니다.
* 애플리케이션 호스트 DNS(Domain Name Service) 레코드는 Azure 프라이빗 DNS에 저장되어야 합니다.
* Azure 서비스 종속성은 서비스 엔드포인트나 프라이빗 링크를 통해 통신해야 합니다.
* 미사용 데이터를 암호화해야 합니다.
* 전송 중인 데이터를 암호화해야 합니다.
* DevOps 배포 파이프라인(예: Azure DevOps)을 사용하고 Azure Spring Cloud에 네트워크를 연결해야 합니다.
* 송신 트래픽은 중앙 NVA(네트워크 가상 어플라이언스)(예: Azure Firewall)를 통해 이동해야 합니다.
* [Azure Spring Cloud 구성 서버][8]를 사용하여 리포지토리에서 구성 속성을 로드하는 경우에는 리포지토리가 비공개여야 합니다.
* Microsoft의 제로 트러스트 보안 방법에는 보안 자격 증명 모음에 저장되는 비밀, 인증서 및 자격 증명이 필요합니다. 권장되는 서비스 Azure Key Vault입니다.
* 온-프레미스 및 클라우드의 호스트 이름 확인은 양방향이어야 합니다.
* 제어 평면 트래픽을 제외하고 공용 인터넷에 직접 송신하지 않습니다.
* Azure Spring Cloud 배포에서 관리하는 리소스 그룹은 수정하면 안 됩니다.
* Azure Spring Cloud 배포에서 관리하는 서브넷은 수정하면 안 됩니다.

다음 목록에서는 설계를 구성하는 구성 요소를 보여 줍니다.

* 온-프레미스 네트워크
  * DNS(Domain Name Service)
  * 게이트웨이
* 허브 구독
  * Application Gateway 서브넷
  * Azure Firewall 서브넷
  * 공유 서비스 서브넷
* 연결된 구독
  * Azure Bastion 서브넷
  * 가상 네트워크 피어

다음 목록에서는 이 참조 아키텍처의 Azure 서비스에 대해 설명합니다.

* [Azure Key Vault][2]: Microsoft ID 서비스 및 컴퓨팅 리소스와 긴밀하게 통합된 하드웨어 지원 자격 증명 관리 서비스입니다.

* [Azure Monitor][3]: Azure 및 온-프레미스에 모두 배포하는 애플리케이션에 대한 모니터링 서비스를 모두 포괄하는 제품군입니다.

* [Azure Pipelines][5]: 업데이트된 Spring Boot 앱을 Azure Spring Cloud에 자동으로 배포할 수 있는 완전한 기능을 갖춘 CI/CD(연속 통합/지속적인 개발) 서비스입니다.

* [Azure Security Center][4]: 온-프레미스, 여러 클라우드 및 Azure에서 워크로드에 대한 통합 보안 관리 및 위협 방지 시스템입니다.

* [Azure Spring Cloud][1]: Java 기반 Spring Boot 애플리케이션 및 .NET 기반 [Steeltoe][9] 애플리케이션용으로 특별히 설계되고 최적화된 관리되는 서비스입니다.

다음 다이어그램은 위의 요구 사항을 해결하는 잘 구성된 허브 및 스포크 설계를 나타냅니다.

![프라이빗 애플리케이션에 대한 참조 아키텍처 다이어그램](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>공용 애플리케이션

다음 목록에서는 공용 애플리케이션의 인프라 요구 사항에 대해 설명합니다. 이러한 요구 사항은 매우 규제된 환경에서 일반적입니다. 이러한 요구 사항은 이전 섹션의 상위 집합입니다. 추가 항목은 기울임꼴로 표시 됩니다.

* 서브넷은 Azure Spring Cloud의 인스턴스를 하나만 포함해야 합니다.
* 하나 이상의 보안 벤치마크에 대한 준수를 적용해야 합니다.
* 애플리케이션 호스트 DNS(Domain Name Service) 레코드는 Azure 프라이빗 DNS에 저장되어야 합니다.
* _Azure DDoS Protection standard를 사용 하도록 설정 해야 합니다._
* Azure 서비스 종속성은 서비스 엔드포인트나 프라이빗 링크를 통해 통신해야 합니다.
* 미사용 데이터를 암호화해야 합니다.
* 전송 중인 데이터를 암호화해야 합니다.
* DevOps 배포 파이프라인(예: Azure DevOps)을 사용하고 Azure Spring Cloud에 네트워크를 연결해야 합니다.
* 송신 트래픽은 중앙 NVA(네트워크 가상 어플라이언스)(예: Azure Firewall)를 통해 이동해야 합니다.
* _수신 트래픽은 최소한 Application Gateway 또는 Azure Front Door를 통해 관리되어야 합니다._
* _인터넷 라우팅 가능 주소는 Azure 공용 DNS에 저장되어야 합니다._
* Microsoft의 제로 트러스트 보안 방법에는 보안 자격 증명 모음에 저장되는 비밀, 인증서 및 자격 증명이 필요합니다. 권장되는 서비스 Azure Key Vault입니다.
* 온-프레미스 및 클라우드의 호스트 이름 확인은 양방향이어야 합니다.
* 제어 평면 트래픽을 제외하고 공용 인터넷에 직접 송신하지 않습니다.
* Azure Spring Cloud 배포에서 관리하는 리소스 그룹은 수정하면 안 됩니다.
* Azure Spring Cloud 배포에서 관리하는 서브넷은 수정하면 안 됩니다.

다음 목록에서는 설계를 구성하는 구성 요소를 보여 줍니다.

* 온-프레미스 네트워크
  * DNS(Domain Name Service)
  * 게이트웨이
* 허브 구독
  * Application Gateway 서브넷
  * Azure Firewall 서브넷
  * 공유 서비스 서브넷
* 연결된 구독
  * Azure Bastion 서브넷
  * 가상 네트워크 피어

다음 목록에서는 이 참조 아키텍처의 Azure 서비스에 대해 설명합니다.

* _[Azure 애플리케이션 방화벽][7]: 일반적인 악용 및 취약성 으로부터 응용 프로그램의 중앙 집중식 보호를 제공 하는 Azure 애플리케이션 게이트웨이의 기능입니다._

* _[Azure 애플리케이션 Gateway][6]: 계층 7에서 작동 하는 TLS (전송 계층 보안) 오프 로드를 사용 하는 응용 프로그램 트래픽을 담당 하는 부하 분산 장치입니다._

* [Azure Key Vault][2]: Microsoft ID 서비스 및 컴퓨팅 리소스와 긴밀하게 통합된 하드웨어 지원 자격 증명 관리 서비스입니다.

* [Azure Monitor][3]: Azure 및 온-프레미스에 모두 배포하는 애플리케이션에 대한 모니터링 서비스를 모두 포괄하는 제품군입니다.

* [Azure Pipelines][5]: 업데이트된 Spring Boot 앱을 Azure Spring Cloud에 자동으로 배포할 수 있는 완전한 기능을 갖춘 CI/CD(연속 통합/지속적인 개발) 서비스입니다.

* [Azure Security Center][4]: 온-프레미스, 여러 클라우드 및 Azure에서 워크로드에 대한 통합 보안 관리 및 위협 방지 시스템입니다.

* [Azure Spring Cloud][1]: Java 기반 Spring Boot 애플리케이션 및 .NET 기반 [Steeltoe][9] 애플리케이션용으로 특별히 설계되고 최적화된 관리되는 서비스입니다.

다음 다이어그램은 위의 요구 사항을 해결 하는 잘 설계 된 허브 및 스포크 설계를 나타냅니다.  허브-가상 네트워크만 인터넷과 통신 합니다.

![공용 애플리케이션에 대한 참조 아키텍처 다이어그램](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Azure Spring Cloud 온-프레미스 연결

Azure Spring Cloud에서 실행되는 애플리케이션은 다양한 Azure, 온-프레미스 및 외부 리소스와 통신할 수 있습니다. 허브 및 스포크 설계를 사용하여 애플리케이션은 Express Route 또는 사이트 간 VPN(가상 프라이빗 네트워크)을 사용하여 외부 또는 온-프레미스 네트워크로 트래픽을 라우팅할 수 있습니다.

## <a name="azure-well-architected-framework-considerations"></a>Azure Well-Architected Framework 고려 사항

[Azure Well-Architected Framework][16]는 강력한 인프라 기반을 설정하는 데 따르는 지침 개념 집합입니다. 프레임워크에는 비용 최적화, 운영 효율성, 성능 효율성, 안정성 및 보안 등의 범주가 포함되어 있습니다.

### <a name="cost-optimization"></a>비용 최적화

분산 시스템 설계의 특성상 인프라 확산은 현실입니다. 이로 인해 예기치 않은 제어할 수 없는 비용이 발생합니다. Azure Spring Cloud는 수요를 충족하고 비용을 최적화할 수 있도록 크기를 조정하는 구성 요소를 사용하여 빌드됩니다. 이 아키텍처의 핵심은 AKS(Azure Kubernetes Service)입니다. 서비스는 클러스터의 운영 비용 효율성을 포함하는 Kubernetes 관리의 복잡성 및 운영 오버헤드를 줄이도록 설계되었습니다.

Azure Spring Cloud의 단일 인스턴스에 여러 애플리케이션 및 애플리케이션 유형을 배포할 수 있습니다. 서비스는 사용률과 비용 효율성을 향상시킬 수 있는 메트릭 또는 일정에 의해 트리거되는 애플리케이션의 자동 크기 조정을 지원합니다.

Application Insights 및 Azure Monitor를 사용하여 운영 비용을 낮출 수도 있습니다. 포괄적인 로깅 솔루션에서 제공하는 표시 유형을 사용하여 시스템의 구성 요소를 실시간으로 크기 조정하는 자동화를 구현할 수 있습니다. 또한 로그 데이터를 분석하여 시스템의 전반적인 비용과 성능을 향상시키기 위해 해결할 수 있는 애플리케이션 코드의 비효율성을 표시할 수 있습니다.

### <a name="operational-excellence"></a>운영 우수성

Azure Spring Cloud는 운영 우수성에 대한 다양한 측면을 다룹니다. 이러한 측면을 결합하여 다음 목록에 설명된 대로 프로덕션 환경에서 서비스가 효율적으로 실행되도록 할 수 있습니다.

* Azure Pipelines를 사용하여 배포의 안정성과 일관성을 유지하는 동시에 사용자의 오류를 방지할 수 있습니다.
* Azure Monitor 및 Application Insights를 사용하여 로그 및 원격 분석 데이터를 저장할 수 있습니다.
  수집된 로그 및 메트릭 데이터를 평가하여 애플리케이션의 상태 및 성능을 확인할 수 있습니다. APM(애플리케이션 성능 모니터링)은 Java 에이전트를 통해 서비스에 완벽하게 통합됩니다. 이 에이전트는 추가 코드를 요구하지 않고 배포된 모든 애플리케이션 및 종속성에 대한 가시성을 제공합니다. 자세한 내용은 블로그 게시물 [Azure Spring Cloud의 애플리케이션 및 종속성을 간편하게 모니터링][15]을 참조하세요.
* Azure Security Center를 사용하여 제공된 데이터를 분석하고 평가하는 플랫폼을 제공하여 애플리케이션이 보안을 유지하도록 할 수 있습니다.
* 서비스는 다양한 배포 패턴을 지원합니다. 자세한 내용은 [Azure Spring Cloud에서 스테이징 환경 설정][14]을 참조하세요.

### <a name="reliability"></a>안정성

Azure 스프링 클라우드는 AKS을 기반으로 합니다. AKS는 클러스터링을 통해 복원 력을 제공 하는 반면,이 참조 아키텍처는 구성 요소가 실패할 경우 응용 프로그램의 가용성을 높이기 위해 서비스 및 아키텍처 고려 사항을 통합 하 여 더욱 강화 되었습니다.

잘 정의된 허브 및 스포크 설계를 기반으로 구축하여 이 아키텍처 기반을 사용하면 여러 지역에 배포할 수 있습니다. 프라이빗 애플리케이션 사용 사례의 경우 아키텍처는 Azure Private DNS를 사용하여 지리적으로 장애 발생 시 지속적으로 가용성을 보장합니다. 공용 애플리케이션 사용 사례의 경우 Azure Front Door 및 Azure Application Gateway는 가용성을 보장합니다.

### <a name="security"></a>보안

이 아키텍처의 보안은 업계에서 정의한 제어 및 벤치마크를 준수하여 해결됩니다. 이 컨텍스트에서 "제어"는 정보 시스템 액세스를 구현할 때 최소 권한 원칙을 사용 하는 것과 같이 간결 하 고 잘 정의 된 모범 사례를 의미 합니다. IAM-05 "이 아키텍처의 컨트롤은 [Microsoft Azure][20] CSA ( [cloud Control Matrix][19] )에 의해 CCM (cloud Control Matrix)에서 시작 하 여 MAFB ( [클라우드 보안 동맹][18] )에서 CIS ( [인터넷 보안][21] )를 중심으로 하는 것입니다. 적용된 제어에서는 거버넌스, 네트워킹 및 애플리케이션 보안의 주요 보안 설계 원리에 중점을 두고 있습니다. 대상 인프라와 관련된 ID, 액세스 관리 및 스토리지의 설계 원리를 처리하는 것은 사용자의 책임입니다.

#### <a name="governance"></a>거버넌스

이 아키텍처에서 해결하는 거버넌스의 주요 측면은 네트워크 리소스의 격리를 통한 분리입니다. CCM에서 DCS-08은 데이터 센터에 대한 수신 및 송신 제어를 권장합니다. 이 제어를 충족하기 위해 아키텍처는 NSG(네트워크 보안 그룹)를 사용하는 허브 및 스포크 설계를 통해 리소스 간에 동-서 트래픽을 필터링합니다. 또한 이 아키텍처는 허브의 중앙 서비스와 스포크의 리소스 간에 트래픽을 필터링합니다. 아키텍처는 Azure 방화벽의 인스턴스를 사용 하 여 아키텍처 내의 리소스와 인터넷 간의 트래픽을 관리 합니다.

다음 목록에서는 이 참조의 데이터 센터 보안을 처리하는 제어를 보여 줍니다.

| CSA CCM 제어 ID | CSA CCM 제어 도메인 |
| :----------------- | :----------------------|
| DCS-08 | 데이터 센터 보안 권한이 없는 사람 항목 |

#### <a name="network"></a>네트워크

이 아키텍처를 지원하는 네트워크 설계는 기존 허브 및 스포크 모델에서 파생됩니다. 이 결정은 네트워크 격리가 기본 구조임을 보장합니다. CCM 제어 IV-06은 신뢰할 수 있고 신뢰할 수 없는 환경 간에 네트워크와 가상 컴퓨터 간의 트래픽을 제한하고 모니터링하는 것을 권장합니다. 이 아키텍처는 동-서 트래픽 ("데이터 센터" 내)의 경우 NSGs를 구현 하 여 제어 하 고 북부 남부 트래픽 ("데이터 센터" 외부)에 대 한 Azure 방화벽을 구현 합니다. CCM 제어 IPY-04는 인프라에서 서비스 간 데이터 교환에 대해 보안 네트워크 프로토콜을 사용하도록 권장합니다. 이 아키텍처를 지원하는 Azure 서비스는 모두 HTTP 및 SQL에 대한 TLS와 같은 표준 보안 프로토콜을 사용합니다.

다음 목록에서는 이 참조의 네트워크 보안을 다루는 CCM 제어를 보여 줍니다.

| CSA CCM 제어 ID | CSA CCM 제어 도메인 |
| :----------------- | :----------------------|
| IPY-04             | 네트워크 프로토콜      |
| IVS-06             | 네트워크 보안       |

네트워크 구현은 MAFB에서 컨트롤을 정의하여 더 안전하게 보호됩니다. 제어는 환경에 대한 트래픽이 공용 인터넷으로부터 제한되도록 합니다.

다음 목록에서는 이 참조의 네트워크 보안을 다루는 CIS 제어를 보여 줍니다.

| CIS 제어 ID | CIS 제어 설명 |
| :------------- | :---------------------- |
| 6.2 | 인터넷의 SSH 액세스가 제한되는지 확인합니다. |
| 6.3 | SQL 데이터베이스가 0.0.0.0/0(ANY IP)의 수신을 허용하지 않는지 확인합니다. |
| 6.5 | Network Watcher가 '사용'으로 설정되어 있는지 확인합니다. |
| 6.6 | UDP를 사용하는 수신이 인터넷에서 제한되는지 확인합니다. |

Azure Spring Cloud는 보안 환경에 배포될 때 Azure에서 송신하는 관리 트래픽이 필요합니다. 이를 위해서는 [VNET에서 Azure Spring Cloud를 실행하기 위해 고객 책임](./vnet-customer-responsibilities.md)에 나열된 네트워크 및 애플리케이션 규칙을 허용해야 합니다.

#### <a name="application-security"></a>애플리케이션 보안

이 설계 주체는 ID, 데이터 보호, 키 관리 및 애플리케이션 구성의 기본 구성 요소를 다룹니다. 기본적으로 Azure Spring Cloud에 배포된 애플리케이션은 작동하는 데 필요한 최소 권한으로 실행됩니다. 권한 부여 제어 집합은 서비스를 사용하는 경우 데이터 보호와 직접적으로 관련이 있습니다. 키 관리는 이 계층화된 애플리케이션 보안 방법을 강화합니다.

다음 목록에서는 이 참조의 키 관리를 다루는 CCM 제어를 보여 줍니다.

| CSA CCM 제어 ID | CSA CCM 제어 도메인 |
| :----------------- | :--------------------- |
| EKM-01 | 암호화 및 키 관리 자격 |
| EKM-02 | 암호화 및 키 관리 키 생성 |
| EKM-03 | 암호화 및 키 관리 중요한 데이터 보호 |
| EKM-04 | 암호화 및 키 관리 스토리지 및 액세스 |

CCM, EKM-02 및 EKM-03에서는 키를 관리하고 암호화 프로토콜을 사용하여 중요한 데이터를 보호하는 정책과 절차를 권장합니다. EKM-01은 모든 암호화 키가 관리될 수 있도록 식별 가능한 소유자를 가질 것을 권장합니다. EKM-04는 표준 알고리즘을 사용하는 것을 권장합니다.

다음 목록에서는 이 참조의 키 관리를 다루는 CIS 제어를 보여 줍니다.

| CIS 제어 ID | CIS 제어 설명 |
| :------------- | :---------------------- |
| 8.1 | 모든 키에 만료 날짜가 설정되어 있는지 확인합니다. |
| 8.2 | 모든 비밀에 만료 날짜가 설정되어 있는지 확인합니다. |
| 8.4 | 키 자격 증명 모음을 복구할 수 있는지 확인합니다. |

CIS 제어 8.1 및 8.2에서는 회전이 시행되도록 자격 증명의 만료 날짜를 설정할 것을 권장합니다. CIS 제어 8.4는 비즈니스 연속성을 유지하기 위해 키 자격 증명 모음의 콘텐츠를 복원할 수 있는지 확인합니다.

애플리케이션 보안의 측면은 Azure에서 Spring 워크로드를 지원하기 위한 이 참조 아키텍처를 사용할 수 있는 기반을 마련했습니다.

## <a name="next-steps"></a>다음 단계

[Azure Spring Cloud 참조 아키텍처][10] 리포지토리에서 사용할 수 있는 ARM, Terraform 및 Azure CLI 배포를 통해 이 참조 아키텍처를 살펴보세요.

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./how-to-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./how-to-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/
