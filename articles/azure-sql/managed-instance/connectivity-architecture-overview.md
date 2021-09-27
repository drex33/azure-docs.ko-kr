---
title: 연결 아키텍처
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 통신과 연결 아키텍처 및 구성 요소가 트래픽을 관리되는 인스턴스로 전송하기 위해 작동하는 방식에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova
ms.date: 04/29/2021
ms.openlocfilehash: 0a9775691780a855824569f77a0bf4a1d3bf295b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657769"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 연결 아키텍처
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance의 통신에 관해 설명합니다. 또한 연결 아키텍처 및 구성 요소가 트래픽을 관리되는 인스턴스로 전송하기 위해 작동하는 방식을 설명합니다.  

SQL Managed Instance는 Azure Virtual Network 및 관리되는 인스턴스 전용 서브넷 내부에 있습니다. 이 배포는 다음을 제공합니다.

- 안전한 프라이빗 IP 주소
- 온-프레미스 네트워크를 SQL Managed Instance에 연결하는 기능.
- 연결된 서버 또는 다른 온-프레미스 데이터 저장소에 SQL Managed Instance를 연결하는 기능.
- Azure 리소스에 SQL Managed Instance를 연결하는 기능.

## <a name="communication-overview"></a>통신 개요

다음 다이어그램은 SQL Managed Instance에 연결하는 엔터티를 보여 줍니다. 또한 관리되는 인스턴스와 통신해야 하는 리소스도 보여 줍니다. 다이어그램 맨 아래의 통신 프로세스는 데이터 원본으로서 SQL Managed Instance에 연결되는 고객 애플리케이션 및 도구를 나타냅니다.  

![연결 아키텍처의 엔터티](./media/connectivity-architecture-overview/connectivityarch001.png)

SQL Managed Instance는 PaaS(Platform as a Service) 제품입니다. Azure는 자동화된 에이전트(관리, 배포 및 유지 관리)를 사용하여 원격 분석 데이터 스트림을 기반으로 이 서비스를 관리합니다. Azure는 관리를 담당하므로 고객은 RDP(원격 데스크톱 프로토콜)를 통해 SQL Managed Instance 가상 클러스터 머신에 액세스할 수 없습니다.

최종 사용자 또는 애플리케이션이 시작하는 일부 작업을 수행하려면 SQL Managed Instance가 플랫폼과 상호 작용해야 할 수도 있습니다. 한 가지 사례는 SQL Managed Instance 데이터베이스를 만드는 것입니다. 이 리소스는 Azure Portal, PowerShell, Azure CLI 및 REST API를 통해 노출됩니다.

SQL Managed Instance는 백업용 Azure Storage, 원격 분석용 Azure Event Hubs, 인증용 Azure AD(Azure Active Directory), TDE(투명한 데이터 암호화)용 Azure Key Vault, 보안 및 지원 기능을 제공하는 몇 가지 Azure 플랫폼 서비스와 같은 Azure 서비스를 사용합니다. SQL Managed Instance는 해당 서비스에 연결합니다.

모든 통신은 인증서를 사용하여 암호화되고 서명됩니다. 통신 당사자의 신뢰성을 확인하기 위해 SQL Managed Instance는 인증서 해지 목록을 통해 해당 인증서를 지속해서 확인합니다. 인증서가 해지되면 SQL Managed Instance에서 데이터 보호를 위해 연결을 닫습니다.

## <a name="high-level-connectivity-architecture"></a>고급 연결 아키텍처

높은 수준에서 SQL Managed Instance는 일련의 서비스 구성 요소입니다. 구성 요소는 고객의 가상 네트워크 서브넷 내에서 실행되는 격리된 전용 가상 머신 집합에서 호스트됩니다. 이 컴퓨터는 가상 클러스터를 형성합니다.

가상 클러스터는 여러 개의 관리되는 인스턴스를 호스트할 수 있습니다. 필요한 경우 클러스터는 고객이 서브넷에서 프로비저닝된 인스턴스 수를 변경하면 자동으로 확장 또는 축소됩니다.

고객 애플리케이션은 SQL Managed Instance에 연결하고 가상 네트워크, 피어링된 가상 네트워크, VPN 또는 Azure ExpressRoute로 연결된 네트워크 내의 데이터베이스를 쿼리하고 업데이트할 수 있습니다. 이 네트워크는 엔드포인트와 프라이빗 IP 주소를 사용해야 합니다.  

![연결 아키텍처 다이어그램](./media/connectivity-architecture-overview/connectivityarch002.png)

Azure 관리 및 배포 서비스는 가상 네트워크 외부에서 실행됩니다. SQL Managed Instance와 Azure 서비스는 공용 IP 주소가 있는 엔드포인트를 연결됩니다. SQL Managed Instance가 아웃바운드 연결을 만드는 경우 끝 NAT(Network Address Translation)를 수신하면 연결이 이 공용 IP 주소에서 시작되는 것처럼 보입니다.

관리 트래픽은 고객 가상 네트워크를 통해 흐릅니다. 즉, 가상 네트워크 인프라의 요소가 인스턴스 장애를 초래해 사용할 수 없도록 하여 관리 트래픽을 손상시킬 수 있습니다.

> [!IMPORTANT]
> Azure는 고객 경험 및 서비스 가용성을 향상시키기 위해 Azure Virtual Network 인프라 요소에 네트워크 의도 정책을 적용합니다. 정책은 SQL Managed Instance의 작동 방식에 영향을 줄 수 있습니다. 이 플랫폼 메커니즘은 네트워킹 요구 사항을 사용자에게 투명하게 전달합니다. 이 정책의 주요 목표는 네트워크 구성 오류를 방지하고 정상적인 SQL Managed Instance 작업을 보장하는 것입니다. 관리되는 인스턴스를 삭제하면 네트워크 의도 정책도 제거됩니다.

## <a name="virtual-cluster-connectivity-architecture"></a>가상 클러스터 연결 아키텍처

SQL Managed Instance의 연결 아키텍처에 대해 좀 더 자세히 살펴보겠습니다. 다음 다이어그램은 가상 클러스터의 개념적 레이아웃을 보여 줍니다.

![가상 클러스터의 연결 아키텍처](./media/connectivity-architecture-overview/connectivityarch003.png)

클라이언트는 `<mi_name>.<dns_zone>.database.windows.net` 형식을 갖는 호스트 이름을 사용하여 SQL Managed Instance에 연결합니다. 이 호스트 이름은 퍼블릭 DNS(Domain Name System) 영역에 등록되고 공개적으로 확인할 수 있지만, 개인 IP 주소로 확인됩니다. `zone-id`는 클러스터를 만들 때 자동으로 생성됩니다. 새로 만든 클러스터는 보조 관리되는 인스턴스를 호스트하는 경우 영역 ID를 주 클러스터와 공유합니다. 자세한 내용은 [자동 장애 조치(failover) 그룹을 통해 여러 데이터베이스의 투명하고 조정된 장애 조치(failover) 사용](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)을 참조하세요.

이 개인 IP 주소는 SQL Managed Instance의 내부 부하 분산 장치에 속합니다. 부하 분산 장치는 트래픽을 SQL Managed Instance 게이트웨이로 보냅니다. 여러 관리되는 인스턴스가 같은 클러스터 내에서 실행될 수 있으므로 게이트웨이는 SQL Managed Instance 호스트 이름을 사용하여 트래픽을 올바른 SQL 엔진 서비스로 리디렉션합니다.

관리 및 배포 서비스는 외부 부하 분산 장치에 매핑되는 [관리 엔드포인트](#management-endpoint)를 사용하여 SQL Managed Instance에 연결됩니다. 트래픽은 SQL Managed Instance의 관리 구성 요소가 사용하는 미리 정의된 포트 세트에서 수신될 때만 노드로 라우팅됩니다. 노드의 기본 제공 방화벽은 Microsoft IP 범위에서만 트래픽을 허용하도록 설정됩니다. 인증서는 관리 구성 요소와 관리 평면 간의 모든 통신을 상호 인증합니다.

## <a name="management-endpoint"></a>관리 엔드포인트

Azure는 관리 엔드포인트를 사용하여 SQL Managed Instance를 관리합니다. 이 엔드포인트는 인스턴스의 가상 클러스터 내부에 있습니다. 관리 엔드포인트 네트워크 수준에서 기본 제공 방화벽으로 보호됩니다. 애플리케이션 수준에서는 상호 인증서 확인을 통해 보호됩니다. 엔드포인트의 IP 주소를 찾으려면 [관리 엔드포인트의 IP 주소 확인](management-endpoint-find-ip-address.md)을 참조하세요.

백업 및 감사 로그와 같이 SQL Managed Instance 내에서 연결이 시작되면 관리 엔드포인트의 공용 IP 주소에서 시작된 것으로 트래픽이 나타납니다. SQL Managed Instance의 IP 주소만 허용하도록 방화벽 규칙을 설정하여 SQL Managed Instance에서 퍼블릭 서비스에 대한 액세스를 제한할 수 있습니다. 자세한 내용은 [SQL Managed Instance의 기본 제공 방화벽 확인](management-endpoint-verify-built-in-firewall.md)을 참조하세요.

> [!NOTE]
> SQL Managed Instance의 지역 내에 있는 Azure 서비스로 이동하는 트래픽은 최적화되며 해당 이유로 관리 엔드포인트의 공용 IP 주소에는 NAT되지 않습니다. 따라서 가장 일반적으로 스토리지에 IP 기반 방화벽 규칙을 사용해야 하는 경우 서비스는 SQL Managed Instance와 다른 지역에 있어야 합니다.

## <a name="service-aided-subnet-configuration"></a>서비스 지원 서브넷 구성

고객 보안 및 관리 효율성 요구 사항에 따라 SQL Managed Instance가 수동에서 서비스 지원 서브넷 구성으로 전환됩니다.

서비스 지원 서브넷 구성을 통해 고객은 데이터(TDS) 트래픽을 완전히 제어할 수 있지만, SQL Managed Instance 컨트롤 플레인은 SLA 준수를 위해 중단 없는 관리 트래픽 흐름을 보장해야 합니다.

서비스 지원 서브넷 구성은 가상 네트워크 [서브넷 위임](../../virtual-network/subnet-delegation-overview.md) 기능 위에 구축되어 자동 네트워크 구성 관리를 제공하고 서비스 엔드포인트를 사용하도록 설정합니다. 

서비스 엔드포인트는 백업/감사 로그를 보관하는 스토리지 계정에서 가상 네트워크 방화벽 규칙을 구성하는 데 사용할 수 있습니다. 서비스 엔드포인트가 사용으로 설정된 경우에도 고객은 서비스 엔드포인트에 대한 추가 보안을 제공하는 [프라이빗 링크](../../private-link/private-link-overview.md)를 사용하는 것이 좋습니다.

> [!IMPORTANT]
> 컨트롤 플레인 구성 특정성 때문에 서비스 지원 서브넷 구성은 국가별 클라우드에서 서비스 엔드포인트를 사용으로 설정하지 않습니다. 

### <a name="network-requirements"></a>네트워크 요구 사항

가상 네트워크 내의 전용 서브넷에 SQL Managed Instance를 배포합니다. 서브넷에는 다음과 같은 특징이 있어야 합니다.

- **전용 서브넷:** 관리되는 인스턴스의 서브넷은 연결된 다른 클라우드 서비스를 포함할 수 없지만 다른 관리되는 인스턴스는 허용되며, 게이트웨이 서브넷이 될 수 없습니다. 서브넷에는 관리되는 인스턴스 이외의 리소스가 포함될 수 없으며 나중에 서브넷에 다른 유형의 리소스를 추가할 수 없습니다.
- **서브넷 위임:** SQL Managed Instance 서브넷을 `Microsoft.Sql/managedInstances` 리소스 공급자에게 위임해야 합니다.
- **NSG(네트워크 보안 그룹):** NSG를 SQL Managed Instance의 서브넷과 연결해야 합니다. SQL Managed Instance가 리디렉션 연결에 대해 구성된 경우 NSG를 통해 포트 1433 및 포트 11000~11999에서 트래픽을 필터링하여 SQL Managed Instance의 데이터 엔드포인트에 대한 액세스를 제어할 수 있습니다. 서비스는 중단 없는 관리 트래픽 흐름을 허용하는 데 필요한 현재 [규칙](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)을 자동으로 프로비저닝하고 유지합니다.
- **UDR(사용자 정의 경로) 테이블:** UDR 테이블은 SQL Managed Instance의 서브넷과 연결해야 합니다. 경로 테이블에 항목을 추가하여 가상 네트워크 게이트웨이 또는 NVA(가상 네트워크 어플라이언스)를 통해 온-프레미스 프라이빗 IP 범위를 대상으로 하는 트래픽을 라우팅할 수 있습니다. 서비스는 중단 없는 관리 트래픽 흐름을 허용하는 데 필요한 현재 [항목](#mandatory-user-defined-routes-with-service-aided-subnet-configuration)을 자동으로 프로비닝하고 유지합니다.
- **충분한 IP 주소:** SQL Managed Instance 서브넷에는 최소 32개의 IP 주소가 있어야 합니다. 자세한 내용은 [SQL Managed Instance의 서브넷 크기 결정](vnet-subnet-determine-size.md)을 참조하세요. [SQL Managed Instance의 네트워킹 요구 사항](#network-requirements)을 충족하도록 구성한 후 [기존 네트워크](vnet-existing-add-subnet.md)에 관리되는 인스턴스를 배포할 수 있습니다. 그러지 않으면 [새 네트워크 및 서브넷](virtual-network-subnet-create-arm-template.md)을 만듭니다.
- **잠금 해제 된 리소스:** SQL Managed Instance 위임 된 서브넷이 포함 된 가상 네트워크는 가상 네트워크 리소스, 해당 부모 리소스 그룹 또는 구독에 대 한 [쓰기 또는 삭제 잠금을](../../azure-resource-manager/management/lock-resources.md) 설정 하지 않아야 합니다. 가상 네트워크 또는 부모 리소스에 대 한 잠금을 설정 하면 SQL Managed Instance 정기 유지 관리를 완료 하지 못할 수 있으며 성능 저하, 지연 된 버그 수정, 규정 준수의 손실, slo 외부의 작업, 인스턴스를 사용할 수 없게 됩니다.
- **Azure 정책에서 허용:** [Azure Policy](../../governance/policy/overview.md) 를 활용 하 여 서브넷이 Managed Instance SQL에 위임 된 가상 네트워크를 포함 하는 범위에서 거부 효과를 통해 리소스의 생성, 수정 및 삭제를 제어 하는 경우 이러한 정책이 SQL Managed Instance에서 정기적으로 유지 관리를 배포 하거나 수행할 수 없도록 하는 단계를 수행 해야 합니다. 이러한 리소스 종류의 리소스를 SQL Managed Instance에서 만들거나 관리할 수 없는 경우에는 유지 관리 작업을 수행 하는 동안 배포 되지 않거나 사용할 수 없게 될 수 있습니다. 거부 효과에서 제외 해야 하는 리소스 유형은 다음과 같습니다.  
  - Microsoft. Network/serviceEndpointPolicies
  - Microsoft. Network/networkIntentPolicies
  - Microsoft. Network/virtualNetworks/서브넷/contextualServiceEndpointPolicies

> [!IMPORTANT]
> 관리되는 인스턴스가 만들어지면 네트워크 설정에 대한 호환되지 않는 변경을 방지하기 위해 네트워크 의도 정책이 서브넷에 적용됩니다. 마지막 인스턴스가 서브넷에서 제거되면 네트워크 의도 정책도 제거됩니다. 아래 규칙은 정보 제공 전용이며, ARM 템플릿 / PowerShell / CLI를 사용하여 배포하지 않아야 합니다. 최신 공식 템플릿을 사용하려는 경우 언제든지 [포털에서 검색](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)할 수 있습니다.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>서비스 보조 서브넷 구성을 사용하는 필수 인바운드 보안 규칙
이러한 규칙은 인바운드 관리 트래픽 흐름을 보장하는 데 필요합니다. 연결 아키텍처 및 관리 트래픽에 대한 자세한 내용은 [위 단락](#high-level-connectivity-architecture)을 참조하세요.

| Name       |포트                        |프로토콜|원본           |대상|작업|
|------------|----------------------------|--------|-----------------|-----------|------|
|관리  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI SUBNET  |허용 |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI SUBNET  |허용 |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI SUBNET  |허용 |
|mi_subnet   |모두                         |모두     |MI SUBNET        |MI SUBNET  |허용 |
|health_probe|모두                         |모두     |AzureLoadBalancer|MI SUBNET  |허용 |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>서비스 보조 서브넷 구성을 사용하는 필수 아웃바운드 보안 규칙
이러한 규칙은 아웃바운드 관리 트래픽 흐름을 보장하는 데 필요합니다. 연결 아키텍처 및 관리 트래픽에 대한 자세한 내용은 [위 단락](#high-level-connectivity-architecture)을 참조하세요.

| Name       |포트          |프로토콜|원본           |대상|작업|
|------------|--------------|--------|-----------------|-----------|------|
|관리  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |허용 |
|mi_subnet   |모두           |모두     |MI SUBNET        |MI SUBNET  |허용 |

### <a name="mandatory-user-defined-routes-with-service-aided-subnet-configuration"></a>서비스 보조 서브넷 구성을 사용하는 필수 사용자 정의 경로
관리 트래픽이 대상으로 직접 라우팅되도록 하려면 이러한 경로가 필요합니다. 연결 아키텍처 및 관리 트래픽에 대한 자세한 내용은 [위 단락](#high-level-connectivity-architecture)을 참조하세요.

|이름|주소 접두사|다음 홉|
|----|--------------|-------|
|subnet-to-vnetlocal|MI SUBNET|가상 네트워크|
|mi-azurecloud-REGION-internet|AzureCloud.REGION|인터넷|
|mi-azurecloud-REGION_PAIR-internet|AzureCloud.REGION_PAIR|인터넷|
|mi-azuremonitor-internet|AzureMonitor|인터넷|
|mi-corpnetpublic-internet|CorpNetPublic|인터넷|
|mi-corpnetsaw-internet|CorpNetSaw|인터넷|
|mi-eventhub-REGION-internet|EventHub.REGION|인터넷|
|mi-eventhub-REGION_PAIR-internet|EventHub.REGION_PAIR|인터넷|
|mi-sqlmanagement-internet|SqlManagement|인터넷|
|mi-storage-internet|스토리지|인터넷|
|mi-storage-REGION-internet|Storage.REGION|인터넷|
|mi-storage-REGION_PAIR-internet|Storage.REGION_PAIR|인터넷|
|mi-azureactivedirectory-internet|AzureActiveDirectory|인터넷|
||||

\* MI SUBNET은 x.x.x.x/y 형식의 서브넷용 IP 주소 범위를 지칭합니다. 이 정보는 Azure Portal의 서브넷 속성에서 찾을 수 있습니다.

\** 대상 주소가 Azure 서비스 중 하나에 대한 주소인 경우 Azure는 트래픽을 인터넷으로 라우팅하지 않고 Azure의 백본 네트워크를 통해 트래픽을 해당 서비스로 직접 라우팅합니다. Azure 서비스 간 트래픽은 가상 네트워크가 있는 Azure 지역 또는 Azure 서비스 인스턴스가 배포된 Azure 지역과 관계없이 인터넷을 거치지 않습니다. 자세한 내용은 [UDR 설명서 페이지](../../virtual-network/virtual-networks-udr-overview.md)를 참조하세요.

또한 경로 테이블에 항목을 추가하여 가상 네트워크 게이트웨이 또는 NVA(가상 네트워크 어플라이언스)를 통해 온-프레미스 프라이빗 IP 범위를 대상으로 하는 트래픽을 라우팅할 수 있습니다.

가상 네트워크에 사용자 지정 DNS가 포함되어 있으면 사용자 지정 DNS 서버가 퍼블릭 DNS 레코드를 확인할 수 있어야 합니다. Azure AD 인증과 같은 추가 기능을 사용하는 경우 추가 FQDN을 확인해야 할 수도 있습니다. 자세한 내용은 [사용자 지정 DNS 설정](custom-dns-configure.md)을 참조하세요.

### <a name="networking-constraints"></a>네트워킹 제약 조건

**TLS 1.2는 아웃바운드 연결에 적용됨**: 2020년 1월 Microsoft는 모든 Azure 서비스에서 서비스 간 트래픽에 TLS 1.2를 적용했습니다. Azure SQL Managed Instance의 경우 복제에 사용되는 아웃바운드 연결 및 SQL Server에 연결된 서버 연결에 TLS 1.2가 적용되었습니다. SQL Managed Instance에서 2016 이전의 SQL Server 버전을 사용하는 경우 please ensure that [TLS 1.2 관련 업데이트](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)가 적용되었는지 확인하세요.

다음 가상 네트워크 기능은 현재 SQL Managed Instance에서 *지원되지 않습니다*.

- **Microsoft 피어링**: SQL Managed Instance가 있는 가상 네트워크와 직접 또는 타동적으로 피어링된 고속 경로 회로에서 [Microsoft 피어링](../../expressroute/expressroute-faqs.md#microsoft-peering)을 사용하면 가상 네트워크 내부의 SQL Managed Instance 구성 요소와 가용성 문제의 원인이 되는 서비스 간의 트래픽 흐름에 영향을 미칩니다. Microsoft 피어링이 이미 사용하도록 설정된 가상 네트워크에 대한 SQL Managed Instance 배포는 실패할 것으로 예상됩니다.
- **글로벌 가상 네트워크 피어링**: Azure 지역 간 [가상 네트워크 피어링](../../virtual-network/virtual-network-peering-overview.md) 연결은 2020년 9월 22일 이전에 만든 서브넷에 배치된 SQL Managed Instance에 대해 작동하지 않습니다.
- **AzurePlatformDNS**: AzurePlatformDNS [서비스 태그](../../virtual-network/service-tags-overview.md)를 사용하여 플랫폼 DNS 확인을 차단하면 SQL Managed Instance를 사용할 수 없게 됩니다. SQL Managed Instance는 엔진 내에서 DNS 확인을 위해 고객 정의 DNS를 지원하지만, 플랫폼 작업을 위해 플랫폼 DNS를 사용합니다.
- **NAT 게이트웨이**: [Azure Virtual Network NAT](../../virtual-network/nat-gateway/nat-overview.md)를 사용하여 특정 공용 IP 주소와의 아웃바운드 연결을 제어하면 SQL Managed Instance를 사용할 수 없게 됩니다. 현재 SQL Managed Instance 서비스는 가상 네트워크 NAT와 인바운드 및 아웃바운드 흐름을 동시 사용을 지원하지 않는 기본 부하 분산 장치를 사용하도록 제한되어 있습니다.
- **Azure Virtual Network의 IPv6**: SQL Managed Instance를 [이중 스택 IPv4/IPv6 가상 네트워크](../../virtual-network/ipv6-overview.md)에 배포하는 작업은 실패할 것으로 예상됩니다. IPv6 주소 접두사가 포함된 NSG(네트워크 보안 그룹) 또는 UDR(라우팅 테이블)을 SQL Managed Instance 서브넷에 연결하거나 이미 Managed Instance 서브넷과 연결된 NSG 또는 UDR에 IPv6 주소 접두사를 추가하면 SQL Managed Instance를 사용할 수 없게 됩니다. 이미 IPv6 접두사가 있는 NSG와 UDR이 포함된 서브넷에 SQL Managed Instance 배포는 실패할 것으로 예상됩니다.
- **Microsoft 서비스용으로 예약된 이름을 갖는 Azure DNS 프라이빗 영역**: 다음은 예약된 이름 목록입니다. windows.net, database.windows.net, core.windows.net, blob.core.windows.net, table.core.windows.net, management.core.windows.net, monitoring.core.windows.net, queue.core.windows.net, graph.windows.net, login.microsoftonline.com, login.windows.net, servicebus.windows.net, vault.azure.net. Microsoft 서비스용으로 예약된 이름을 갖는 연결된 [Azure DNS 프라이빗 영역](../../dns/private-dns-privatednszone.md)을 포함하는 가상 네트워크로는 SQL Managed Instance를 배포할 수 없습니다. 예약된 이름을 갖는 Azure DNS 프라이빗 영역을 관리되는 인스턴스를 포함하는 가상 네트워크에 연결하면 SQL Managed Instance를 사용할 수 없게 됩니다. 적절한 Private Link 구성을 위해서는 [Azure 프라이빗 엔드포인트 DNS 구성](../../private-link/private-endpoint-dns.md)을 따르세요.
- **Azure Storage에 대한 서비스 엔드포인트 정책**: 연결된 [서비스 엔드포인트 정책](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)이 있는 서브넷에 SQL Managed Instance를 배포할 수 없습니다. 서비스 엔드포인트 정책을 Managed Instance를 호스트하는 서브넷에 연결할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- 개요는  [Azure SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- SQL Managed Instance를 배포할 수 있는 [새 Azure Virtual Network](virtual-network-subnet-create-arm-template.md) 또는 [기존 Azure Virtual Network](vnet-existing-add-subnet.md)를 설정하는 방법을 알아봅니다.
- SQL Managed Instance를 배포하려는 [서브넷의 크기를 계산](vnet-subnet-determine-size.md)합니다.
- 관리되는 인스턴스를 만드는 방법을 알아봅니다.
  - [Azure Portal](instance-create-quickstart.md)
  - [PowerShell](scripts/create-configure-managed-instance-powershell.md) 사용
  - [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet/) 사용
  - [Azure Resource Manager 템플릿(SSMS가 포함된 JumpBox 사용)](https://azure.microsoft.com/resources/templates/sqlmi-new-vnet-w-jumpbox/) 사용
