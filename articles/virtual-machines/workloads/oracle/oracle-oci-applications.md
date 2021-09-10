---
title: Azure Virtual Machines에서 Oracle 앱을 배포하는 아키텍처 | Microsoft Docs
description: Microsoft Azure 가상 머신에서 Azure 또는 OCI(Oracle Cloud Infrastructure)의 데이터베이스와 함께 Oracle 앱(E-Business Suite, JD Edwards EnterpriseOne 및 PeopleSoft 포함)을 배포하는 애플리케이션 아키텍처입니다.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.openlocfilehash: ac9c699addf996178b1d6aabdf010267bda75964
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690071"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Azure에서 Oracle 애플리케이션을 배포하는 아키텍처

**적용 대상:** :heavy_check_mark: Linux VM 

Microsoft와 Oracle은 고객이 Oracle E-Business Suite, JD Edwards EnterpriseOne 및 PeopleSoft와 같은 Oracle 애플리케이션을 클라우드에 배포할 수 있도록 협력했습니다. Microsoft Azure와 OCI(Oracle Cloud Infrastructure) 간의 [개인 네트워크 상호 연결](configure-azure-oci-networking.md) 미리 보기가 도입됨에 따라 이제 Azure에서 Oracle 애플리케이션을 Azure 또는 OCI의 백 엔드 데이터베이스와 함께 배포할 수 있습니다. 또한 Oracle 애플리케이션은 Azure Active Directory와 통합할 수 있으므로 사용자가 Azure AD(Azure Active Directory) 자격 증명을 사용하여 Oracle 애플리케이션에 로그인할 수 있도록 Single Sign-On을 설정할 수 있습니다.

OCI는 DBaaS, Exadata Cloud Service, Oracle RAC 및 IaaS(Infrastructure as a Service)를 포함하여 Oracle 애플리케이션에 대한 여러 Oracle 데이터베이스 옵션을 제공합니다. 현재 Autonomous Database는 Oracle 애플리케이션에 지원되는 백 엔드가 아닙니다.

고가용성 및 보안 방식을 포함하여 Azure에서 Oracle 애플리케이션을 배포하는 [여러 옵션](oracle-overview.md)이 있습니다. 또한 Azure는 Oracle 애플리케이션을 Azure에서 완전히 실행하도록 선택한 경우 배포할 수 있는 [Oracle 데이터베이스 VM 이미지](oracle-vm-solutions.md)를 제공합니다.

다음 섹션에서는 Oracle E-Business Suite, JD Edwards EnterpriseOne 및 PeopleSoft를 클라우드 간 구성에서 배포하거나 Azure에서 완전하게 배포하기 위한 Microsoft 및 Oracle의 아키텍처 권장 사항에 대해 간략히 설명합니다. Microsoft와 Oracle은 이러한 애플리케이션을 테스트했으며, 성능이 Oracle에서 이러한 애플리케이션에 대해 설정한 표준을 충족한다는 것을 확인했습니다.

## <a name="architecture-considerations"></a>아키텍처 고려 사항

Oracle 애플리케이션은 Azure 및 선택적으로 OCI의 동일한 가상 머신 또는 여러 가상 머신에서 호스팅할 수 있는 여러 서비스로 구성됩니다. 

애플리케이션 인스턴스는 프라이빗 또는 퍼블릭 엔드포인트를 사용하여 설정할 수 있습니다. Microsoft 및 Oracle은 애플리케이션을 관리하기 위해 공용 IP 주소를 사용하여 *베스천 호스트 VM* 을 별도의 서브넷에 설정하도록 권장합니다. 그런 다음, 데이터베이스 계층을 포함하여 다른 컴퓨터에 개인 IP 주소만 할당합니다. 

클라우드 간 아키텍처에서 애플리케이션을 설정하는 경우 Azure 가상 네트워크의 IP 주소 공간이 OCI 가상 클라우드 네트워크의 개인 IP 주소 공간과 겹치지 않도록 계획해야 합니다.

보안을 강화하기 위해 서브넷 수준에서 네트워크 보안 그룹을 설정하여 특정 포트 및 IP 주소의 트래픽만 허용하도록 합니다. 예를 들어 중간 계층의 컴퓨터는 가상 네트워크 내에서만 트래픽을 받아야 합니다. 외부 트래픽이 중간 계층 컴퓨터에 직접 도달하면 안 됩니다.

고가용성을 위해 동일한 가용성 집합 또는 다른 가용성 영역에 여러 서버의 중복 인스턴스를 설정할 수 있습니다. 가용성 영역을 사용하면 99.99%의 작동 시간 SLA를 달성할 수 있으며, 가용성 집합을 사용하면 지역 내에서 99.95%의 작동 시간 SLA를 달성할 수 있습니다. 이 문서에 나와 있는 샘플 아키텍처는 두 개의 가용성 영역에 배포됩니다.

클라우드 간 상호 연결을 사용하여 애플리케이션을 배포하는 경우 기존 ExpressRoute 회로를 계속 사용하여 Azure 환경을 온-프레미스 네트워크에 연결할 수 있습니다. 그러나 OCI와의 상호 연결에는 온-프레미스 네트워크에 연결하는 회로가 아닌 별도의 ExpressRoute 회로가 필요합니다.

## <a name="e-business-suite"></a>E-Business Suite

EBS(Oracle E-Business Suite)는 SCM(공급망 관리) 및 CRM(고객 관계 관리)을 포함하는 애플리케이션 제품군입니다. OCI의 관리형 데이터베이스 포트폴리오를 활용하기 위해 Microsoft Azure와 OCI 간의 클라우드 간 상호 연결을 사용하여 EBS를 배포할 수 있습니다. 이 구성의 경우 다음 아키텍처 다이어그램(그림 1)에서 표시한 대로 프레젠테이션 및 애플리케이션 계층은 Azure에서 실행되고, 데이터베이스 계층은 OCI에서 실행됩니다.

![E-Business Suite 클라우드 간 아키텍처](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*그림 1: E-Business Suite 클라우드 간 아키텍처* 

이 아키텍처에서 Azure의 가상 네트워크는 클라우드 간 상호 연결을 사용하여 OCI의 가상 클라우드 네트워크에 연결됩니다. 애플리케이션 계층은 Azure에 설정되고, 데이터베이스는 OCI에 설정됩니다. 특정 포트의 특정 서브넷에서만 트래픽을 허용하도록 각 구성 요소를 네트워크 보안 그룹이 있는 자체 서브넷에 배포하는 것이 좋습니다.

또한 이 아키텍처는 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용하여 구성된 고가용성 Oracle 데이터베이스를 사용하여 Azure에서 완전히 배포하도록 조정할 수 있습니다. 다음 다이어그램(그림 2)은 이 아키텍처 패턴의 예입니다.

![E-Business Suite Azure 전용 아키텍처](media/oracle-oci-applications/ebs-arch-azure.png)

*그림 2: E-Business Suite Azure 전용 아키텍처*

다음 섹션에서는 다양한 구성 요소에 대해 개략적으로 설명합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>애플리케이션(중간) 계층

애플리케이션 계층은 자체 서브넷에서 격리됩니다. 내결함성 및 간편한 패치 관리를 위해 여러 가상 머신이 설정됩니다. 이러한 VM은 Azure NetApp Files 및 Ultra SSD에서 제공하는 공유 스토리지에서 지원할 수 있습니다. 이 구성을 사용하면 가동 중지 시간 없이 패치를 더 쉽게 배포할 수 있습니다. 하나의 컴퓨터가 오프라인 상태인 경우에도 EBS 애플리케이션 계층에 대한 요청이 처리되도록 애플리케이션 계층의 컴퓨터는 퍼블릭 부하 분산 장치 앞에 있어야 합니다.

### <a name="load-balancer"></a>부하 분산 장치

Azure 부하 분산 장치를 사용하면 트래픽을 워크로드의 여러 인스턴스에 분산하여 고가용성을 보장할 수 있습니다. 이 경우 사용자가 웹을 통해 EBS 애플리케이션에 액세스할 수 있으므로 퍼블릭 부하 분산 장치가 설정됩니다. 부하 분산 장치는 부하를 중간 계층의 두 컴퓨터 모두에 분산시킵니다. 보안을 강화하기 위해 사이트 간 VPN 또는 ExpressRoute 및 네트워크 보안 그룹을 사용하여 회사 네트워크에서 시스템에 액세스하는 사용자의 트래픽만 허용합니다.

### <a name="database-tier"></a>데이터베이스 계층

이 계층은 Oracle 데이터베이스를 호스팅하고 자체 서브넷으로 구분됩니다. 1521 Oracle 특정 데이터베이스 포트를 통해 애플리케이션 계층에서 데이터베이스 계층으로의 트래픽만 허용하는 네트워크 보안 그룹을 추가하는 것이 좋습니다.

Microsoft 및 Oracle은 고가용성 설정을 권장합니다. Azure의 고가용성은 Oracle Data Guard를 사용하여 두 개의 Oracle 데이터베이스를 두 개의 가용성 영역에 설정하거나 OCI에서 Oracle Database Exadata Cloud Service를 사용하여 달성할 수 있습니다. Oracle Database Exadata Cloud Service를 사용하는 경우 데이터베이스는 두 개의 서브넷에 배포됩니다. 또한 Oracle Data Guard를 사용하는 두 개의 가용성 도메인에서 Oracle Database를 OCI의 VM에 설정할 수 있습니다.


### <a name="identity-tier"></a>ID 계층

ID 계층에는 EBS Asserter VM이 포함됩니다. EBS Asserter를 사용하면 IDCS(Oracle Identity Cloud Service) 및 Azure AD의 ID를 동기화할 수 있습니다. EBS는 SAML 2.0 또는 OpenID Connect와 같은 Single Sign-On 프로토콜을 지원하지 않으므로 EBS Asserter가 필요합니다. EBS Asserter는 IDCS에서 생성된 OpenID Connect 토큰을 사용하고, 유효성을 검사한 다음, EBS에서 사용자에 대한 세션을 생성합니다. 

이 아키텍처에서는 IDCS 통합을 보여 주지만, Azure AD 통합 액세스 및 Single Sign-On은 Oracle Internet Directory 또는 Oracle Unified Directory를 사용하는 Oracle Access Manager를 통해 사용하도록 설정할 수 있습니다. 자세한 내용은 [Deploying Oracle EBS with IDCS Integration(IDCS 통합을 사용하여 Oracle EBS 배포)](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) 또는 [Deploying Oracle EBS with OAM Integration(OAM 통합을 사용하여 Oracle EBS 배포)](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf) 백서를 참조하세요.

고가용성을 위해 EBS Asserter의 중복 서버를 부하 분산 장치가 앞에 있는 여러 가용성 영역에 배포하는 것이 좋습니다.

인프라가 설정되면 Oracle에서 제공하는 설치 가이드에 따라 E-Business Suite를 설치할 수 있습니다.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle의 JD Edwards EnterpriseOne은 포괄적인 엔터프라이즈 리소스 계획 소프트웨어의 통합 애플리케이션 제품군입니다. 이는 Oracle 또는 SQL Server 데이터베이스 백 엔드를 통해 설정할 수 있는 다중 계층 애플리케이션입니다. 이 섹션에서는 OCI 또는 Azure에서 Oracle 데이터베이스 백 엔드를 사용하여 JD Edwards EnterpriseOne을 배포하는 방법에 대해 자세히 설명합니다.

다음 권장 아키텍처(그림 3)에서는 관리, 프레젠테이션 및 중간 계층이 Azure의 가상 네트워크에 배포됩니다. 데이터베이스는 OCI의 가상 클라우드 네트워크에 배포됩니다.

E-Business Suite와 마찬가지로 보안 관리를 위해 선택적 베스천 계층을 설정할 수 있습니다. 베스천 VM 호스트를 점프 서버로 사용하여 애플리케이션 및 데이터베이스 인스턴스에 액세스합니다.

![JD Edwards EnterpriseOne 클라우드 간 아키텍처](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*그림 3: JD Edwards EnterpriseOne 클라우드 간 아키텍처입*

이 아키텍처에서 Azure의 가상 네트워크는 클라우드 간 상호 연결을 사용하여 OCI의 가상 클라우드 네트워크에 연결됩니다. 애플리케이션 계층은 Azure에 설정되고, 데이터베이스는 OCI에 설정됩니다. 특정 포트의 특정 서브넷에서만 트래픽을 허용하도록 각 구성 요소를 네트워크 보안 그룹이 있는 자체 서브넷에 배포하는 것이 좋습니다.

또한 이 아키텍처는 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용하여 구성된 고가용성 Oracle 데이터베이스를 사용하여 Azure에서 완전히 배포하도록 조정할 수 있습니다. 다음 다이어그램(그림 4)은 이 아키텍처 패턴의 예입니다.

![JD Edwards EnterpriseOne Azure 전용 아키텍처](media/oracle-oci-applications/jdedwards-arch-azure.png)

*그림 4: JD Edwards EnterpriseOne Azure 전용 아키텍처*

다음 섹션에서는 다양한 구성 요소에 대해 개략적으로 설명합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>관리 계층

이름에서 알 수 있듯이 이 계층은 관리 작업에 사용됩니다. 관리 계층에 대해 별도의 서브넷을 만들 수 있습니다. 이 계층의 서비스 및 서버는 주로 애플리케이션을 설치하고 관리하는 데 사용됩니다. 따라서 이러한 서버의 단일 인스턴스로 충분합니다. 애플리케이션의 고가용성을 위해 중복 인스턴스가 필요하지 않습니다.

이 계층의 구성 요소는 다음과 같습니다.
    
 - **프로비전 서버** - 이 서버는 애플리케이션의 여러 구성 요소를 엔드투엔드 방식으로 배포하는 데 사용됩니다. 22 포트를 통해 데이터베이스 계층의 인스턴스를 포함하여 다른 계층의 인스턴스와 통신합니다. JD Edwards EnterpriseOne용 서버 관리자 콘솔을 호스팅합니다.
 - **배포 서버** - 이 서버는 주로 JD Edwards EnterpriseOne을 설치하는 데 필요합니다. 설치 프로세스 중에 이 서버는 필수 파일 및 설치 패키지의 중앙 리포지토리 역할을 합니다. 소프트웨어는 이 서버에서 다른 서버 및 클라이언트에 분산되거나 배포됩니다.
 - **개발 클라이언트** - 이 서버에는 웹 브라우저와 기본 애플리케이션에서 실행되는 구성 요소가 포함됩니다.

### <a name="presentation-tier"></a>프레젠테이션 계층

이 계층에는 AIS(Application Interface Services), ADF(Application Development Framework), JAS(Java Application Servers)와 같은 다양한 구성 요소가 포함됩니다. 이 계층의 서버는 중간 계층의 서버와 통신합니다. 이러한 서버의 앞에는 트래픽이 수신되는 포트 번호 및 URL에 따라 필요한 서버로 트래픽을 라우팅하는 부하 분산 장치가 있습니다. 고가용성을 위해 각 서버 유형의 여러 인스턴스를 배포하는 것이 좋습니다.

이 계층의 구성 요소는 다음과 같습니다.
    
- **AIS(Application Interface Services)** - AIS 서버는 JD Edwards EnterpriseOne 모바일 엔터프라이즈 애플리케이션과 JD Edwards EnterpriseOne 간의 통신 인터페이스를 제공합니다.
- **JAS(Java Application Server)** - JAS는 부하 분산 장치에서 요청을 받고 이를 중간 계층으로 전달하여 복잡한 작업을 실행합니다. JAS에는 간단한 비즈니스 논리를 실행할 수 있는 기능이 있습니다.
- **BIP(BI Publisher Server)** - 이 서버는 JD Edwards EnterpriseOne 애플리케이션에서 수집한 데이터를 기반으로 하는 보고서를 제공합니다. 보고서에서 다양한 템플릿을 기반으로 하여 데이터를 표시하는 방법을 설계하고 제어할 수 있습니다.
- **BSS(Business Services Server)** - BSS는 다른 Oracle 애플리케이션과의 정보 교환 및 상호 운용성을 지원합니다.
- **RTE(Real-Time Events Server)** - RTE 서버를 사용하면 JDE EnterpriseOne 시스템에서 발생하는 트랜잭션에 대한 알림을 외부 시스템에 설정할 수 있습니다. 구독자 모델을 사용하고, 타사 시스템에서 이벤트를 구독할 수 있습니다. 두 RTE 서버에 대한 요청의 부하를 분산하려면 서버가 클러스터에 있어야 합니다.
- **ADF(Application Development Framework) 서버** - ADF 서버는 Oracle ADF를 사용하여 개발된 JD Edwards EnterpriseOne 애플리케이션을 실행하는 데 사용됩니다. ADF 런타임을 사용하는 Oracle WebLogic 서버에 배포됩니다.

### <a name="middle-tier"></a>중간 계층

중간 계층에는 논리 서버 및 일괄 처리 서버가 포함됩니다. 이 경우 두 서버는 동일한 가상 머신에 설치됩니다. 그러나 프로덕션 시나리오의 경우 논리 서버 및 일괄 처리 서버를 별도의 서버에 배포하는 것이 좋습니다. 고가용성을 위해 여러 서버가 중간 계층의 두 개의 가용성 영역에 배포됩니다. Azure 부하 분산 장치를 만들고 이러한 서버를 백 엔드 풀에 배치하여 두 서버가 모두 활성 상태이고 요청을 처리하도록 해야 합니다.

중간 계층의 서버는 프레젠테이션 계층의 서버 및 퍼블릭 부하 분산 장치에서만 요청을 받습니다. 프레젠테이션 계층 서브넷 및 부하 분산 장치가 아닌 다른 주소의 트래픽을 거부하도록 네트워크 보안 그룹 규칙을 설정해야 합니다. 또한 관리를 위해 베스천 호스트의 22 포트에서 트래픽을 허용하도록 NSG 규칙을 설정할 수 있습니다. 퍼블릭 부하 분산 장치를 사용하여 요청의 부하를 중간 계층의 VM 간에 분산할 수 있습니다.

다음 두 가지 구성 요소는 중간 계층에 있습니다.

- **논리 서버** - 비즈니스 논리 또는 비즈니스 기능을 포함합니다.
- **일괄 처리 서버** - 일괄 처리에 사용됩니다.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle의 PeopleSoft 애플리케이션 제품군에는 인적 자원 및 재무 관리를 위한 소프트웨어가 포함됩니다. 애플리케이션 제품군은 여러 계층으로 구성되며, 애플리케이션에는 HRMS(인적 자원 관리 시스템), CRM(고객 관계 관리), FSCM(금융 및 공급망 관리) 및 EPM(기업 성과 관리)이 포함됩니다.

소프트웨어 제품군의 각 계층을 자체 서브넷에 배포하는 것이 좋습니다. 애플리케이션의 백 엔드 데이터베이스로 Oracle 데이터베이스 또는 Microsoft SQL Server가 필요합니다. 이 섹션에서는 Oracle 데이터베이스 백 엔드를 사용하여 PeopleSoft를 배포하는 방법에 대해 자세히 설명합니다.

다음은 클라우드 간 아키텍처에서 PeopleSoft 애플리케이션 제품군을 배포하는 정식 아키텍처입니다(그림 5).

![PeopleSoft 클라우드 간 아키텍처](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*그림 5: PeopleSoft 클라우드 간 아키텍처*

이 샘플 아키텍처에서 Azure의 가상 네트워크는 클라우드 간 상호 연결을 사용하여 OCI의 가상 클라우드 네트워크에 연결됩니다. 애플리케이션 계층은 Azure에 설정되고, 데이터베이스는 OCI에 설정됩니다. 특정 포트의 특정 서브넷에서만 트래픽을 허용하도록 각 구성 요소를 네트워크 보안 그룹이 있는 자체 서브넷에 배포하는 것이 좋습니다.

또한 이 아키텍처는 한 지역의 두 가용성 영역에서 Oracle Data Guard를 사용하여 구성된 고가용성 Oracle 데이터베이스를 사용하여 Azure에서 완전히 배포하도록 조정할 수 있습니다. 다음 다이어그램(그림 6)은 이 아키텍처 패턴의 예입니다.

![PeopleSoft Azure 전용 아키텍처](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*그림 6: PeopleSoft Azure 전용 아키텍처*

다음 섹션에서는 다양한 구성 요소에 대해 개략적으로 설명합니다.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>애플리케이션 계층

애플리케이션 계층에는 PeopleSoft 애플리케이션 서버, PeopleSoft 웹 서버, 탄력적 검색 및 PeopleSoft Process Scheduler의 인스턴스가 포함됩니다. Azure 부하 분산 장치는 애플리케이션 계층의 해당 서버로 라우팅되는 사용자의 요청을 수락하도록 설정됩니다.

고가용성을 위해 애플리케이션 계층에 있는 각 서버의 중복 인스턴스를 여러 가용성 영역에 설정하는 것이 좋습니다. Azure 부하 분산 장치를 여러 백 엔드 풀로 설정하여 각 요청을 올바른 서버로 보낼 수 있습니다.

### <a name="peopletools-client"></a>PeopleTools Client

PeopleTools Client는 개발, 마이그레이션 및 업그레이드와 같은 관리 작업을 수행하는 데 사용됩니다. PeopleTools Client는 애플리케이션의 고가용성을 달성하는 데 필요하지 않으므로 PeopleTools Client의 중복 서버가 필요하지 않습니다.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>다음 단계

[Terraform 스크립트](https://github.com/microsoft/azure-oracle)를 사용하여 Azure에서 Oracle 앱을 설정하고, OCI와 클라우드 간의 연결을 설정합니다.

OCI에 대한 자세한 내용 및 백서는 [Oracle 클라우드](https://docs.cloud.oracle.com/iaas/Content/home.htm) 설명서를 참조하세요.
