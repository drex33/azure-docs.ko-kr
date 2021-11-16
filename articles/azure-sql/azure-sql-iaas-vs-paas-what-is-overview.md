---
title: Azure SQL란?
description: Azure SQL 서비스 제품군 내의 다양한 옵션에 대해 알아봅니다. Azure VM의 Azure SQL Database, Azure SQL Managed Instance 및 SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server 클라우드, 클라우드의 SQL Server, PaaS 데이터베이스, 클라우드 SQL Server, DBaaS, IaaS
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 2360b32a63ad5473f8dfa1f08a6d2df3e0e85fd4
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136571"
---
# <a name="what-is-azure-sql"></a>Azure SQL란? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL은 Azure 클라우드에서 SQL Server 데이터베이스 엔진을 사용하는 관리형, 보안 및 인텔리전트 제품군입니다.

- **Azure SQL Database**: 서버리스 컴퓨팅을 포함하는 인텔리전트 관리형 데이터베이스 서비스에서 최신 클라우드 애플리케이션을 지원합니다. 
- **Azure SQL Managed Instance**: SQL Server 데이터베이스 엔진과 거의 100% 동일한 기능을 제공하며 서비스로 제공되는 완전 관리형 인텔리전트 인스턴스를 통해 기존 SQL Server 애플리케이션을 대규모로 현대화합니다. 클라우드로의 마이그레이션에 대부분 적합합니다.
- **Azure VM의 SQL Server**: SQL Server 워크로드를 편리하게 리프트 앤 시프트하며 SQL Server 호환성 및 운영 체제 수준 액세스를 100% 유지합니다. 
 
Azure SQL은 친숙한 SQL Server 엔진에 기반하기 때문에 애플리케이션을 쉽게 마이그레이션할 수 있고 익숙한 도구, 언어 및 리소스를 계속 사용할 수 있습니다. 사용하던 기술과 경험이 클라우드로 이전되기 때문에 이미 가지고 있는 것으로 더 많은 작업을 수행할 수 있습니다. 

각 제품이 Microsoft의 Azure SQL 데이터 플랫폼에 어떻게 부합하는지 알아보고 비즈니스 요구 사항에 적합한 옵션을 찾아보세요. 비용 절감이 우선이든 관리 최소화가 중요하든 이 문서에서는 가장 관심 있는 비즈니스 요구 사항에 대해 어떤 접근 방식이 결과를 가져오는지 결정하도록 도울 수 있습니다.

Azure SQL을 처음 접하는 경우에는 심층 [Azure SQL 비디오 시리즈](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)에서 *Azure SQL이란?* 비디오를 확인하세요.
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

## <a name="overview"></a>개요

현재 데이터 중심 세계에서는 방대한 양의 데이터를 관리하고 그 잠재력을 활용하는 능력에 따라 디지털 혁신의 주도권이 좌우되는 경우가 점점 더 많아지고 있습니다. 하지만 최근 데이터 자산은 온-프레미스, 클라우드 또는 네트워크 에지에서 데이터가 호스트되면서 점점 더 복잡해지고 있습니다. 몰입형 인텔리전트 애플리케이션을 구축하는 개발자는 한계에 직면할 수 있으며 궁극적으로 이것은 개발자의 경험에 영향을 줄 수 있습니다. 호환되지 않는 플랫폼, 부적절한 데이터 보안, 충분하지 못한 리소스 및 가격 대비 성능 장벽으로 인해 발생하는 제한 사항은 복잡성의 원인이 되며, 앱 현대화 및 개발에 방해가 될 수 있습니다. 

Azure와 온-프레미스 SQL Server 데이터베이스를 비교 논의할 때 우선 이 둘을 모두 사용할 수 있다는 점을 이해해야 합니다. Microsoft의 데이터 플랫폼에서는 SQL Server 기술을 활용하며 물리적 온-프레미스 머신, 프라이빗 클라우드 환경, 타사 호스팅 프라이빗 클라우드 환경 및 퍼블릭 클라우드 전반에서 이 기술을 사용할 수 있습니다. 


### <a name="fully-managed-and-always-up-to-date"></a>항상 최신 및 완전 관리형 

혁신에 더 많은 시간을 투자하고 데이터베이스 패치, 업데이트 및 백업에 드는 시간을 줄이십시오. Azure는 항상 최신으로 유지되는 SQL을 사용하는 유일한 클라우드이기 때문에 최신 업데이트와 패치가 자동으로 적용되어 데이터베이스가 항상 최신 상태로 유지되며 지원 종료라는 번거로움이 없습니다. 성능 튜닝, 고가용성, 재해 복구 및 백업과 같은 복잡한 작업도 자동화되어 있기 때문에 애플리케이션에 집중할 수 있습니다.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>기본 제공 인텔리전트 보안을 통한 데이터 보호 

Azure는 위협에 대비하여 데이터를 지속적으로 모니터링합니다. Azure SQL을 사용하여 다음을 수행할 수 있습니다.

- 인텔리전트 [지능형 위협 탐지](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) 및 자동 관리 취약성 평가 경고를 통해 잠재적 위협을 실시간으로 해결합니다. 
- T-SQL, 인증, 네트워킹 및 키 관리를 포함한 [기본 제공 보안 컨트롤](https://azure.microsoft.com/overview/security/)을 통해 업계 최고의 다중 계층 보호를 받습니다. 
- 클라우드 데이터베이스 서비스의 가장 포괄적인 [규정 준수](https://azure.microsoft.com/overview/trusted-cloud/compliance/) 범위를 활용합니다. 


### <a name="business-motivations"></a>비즈니스 동기 부여

다양한 데이터 제품 중에서 선택을 하는 결정에 영향을 줄 수 있는 요소에는 몇 가지가 있습니다.

- [비용](#cost): PaaS 및 IaaS 옵션 둘 다 기본 인프라 및 라이선스를 고려하는 기본 가격을 포함합니다. 그러나 IaaS 옵션을 사용할 경우 데이터베이스를 관리하기 위한 시간과 리소스를 추가로 투자해야 하지만 PaaS에서는 이러한 관리 기능이 가격에 포함되어 있습니다. IaaS 옵션을 사용하면 사용하지 않을 때 리소스를 종료하여 비용을 절감할 수 있지만, PaaS 버전은 필요하지 않을 때 리소스를 삭제했다가 다시 만들지 않으면 항상 실행됩니다.
- [관리](#administration): PaaS 옵션은 데이터베이스를 관리하는 데 투자해야 하는 시간을 줄여줍니다. 단, 수행하거나 실행할 수 있는 사용자 지정 관리 작업 및 스크립트의 범위도 제한됩니다. 예를 들어 CLR은 SQL Database에서 지원되지 않지만 SQL Managed Instance 인스턴스에는 지원됩니다. 또한 PaaS의 배포 옵션은 추적 플래그 사용을 지원하지 않습니다.
- [서비스 수준 계약](#service-level-agreement-sla): IaaS와 PaaS 모두 높은 업계 표준 SLA를 제공합니다. PaaS 옵션은 99.99%의 SLA를 보장하지만, IaaS는 인프라에 대해 99.95%의 SLA를 보장합니다. 따라서 데이터베이스의 가용성을 보장하려면 추가 메커니즘을 구현해야 합니다. 추가로 SQL 가상 머신을 만들고 SQL Server Always On 가용성 그룹 고가용성 솔루션을 구현하여 99.99% SLA를 달성할 수 있습니다. 
- [Azure로 이동해야 하는 경우](#market): Azure VM의 SQL Server는 작업 환경과 정확히 일치하기 때문에 온-프레미스에서 Azure VM으로 마이그레이션하는 것은 데이터베이스를 한 온-프레미스 서버에서 다른 온-프레미스 서버로 이동하는 것과 다르지 않습니다. SQL Managed Instance를 사용하여 쉽게 마이그레이션할 수도 있습니다. 단, 마이그레이션하기 전에 적용해야 하는 몇 가지 변경 사항이 있을 수 있습니다. 


## <a name="service-comparison"></a>서비스 비교

   ![클라우드 SQL Server 옵션: IaaS의 SQL Server 또는 클라우드의 SaaS SQL Database](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

다이어그램에서 볼 수 있듯이 각 서비스 제품은 인프라에 대한 관리 수준과 비용 효율성 정도에 따라 특징을 설명할 수 있습니다.

Azure에서는 SQL Server 워크로드를 호스트된 서비스([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) 또는 호스트된 인프라([IaaS](https://azure.microsoft.com/overview/what-is-iaas/))로 실행할 수 있습니다. PaaS 내에는 여러 제품 옵션과 각 옵션에 포함된 서비스 계층이 있습니다. IaaS 또는 PaaS 중에서 결정할 때 확인해야 하는 핵심 질문은 직접 데이터베이스를 관리하거나, 패치를 적용하거나, 백업을 수행할 것인지 아니면 이러한 작업을 Azure에 위임할 것인가 입니다.

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md)는 *PaaS(Platform as a Service)* 산업 범주에 해당하는, Azure에 호스트된 관계형 DBaaS(Database as a Service)입니다. 
- 개발 및 마케팅에서 시간 제약 조건이 있고 안정적인 최신 SQL Server 기능을 사용하려는, 최신 클라우드 애플리케이션에 가장 적합. 
- 안정적인 최신 SQL Server Enterprise Edition을 기준으로 하는 완전 관리형 SQL Server 데이터베이스 엔진. SQL Database는 Microsoft에서 소유하고 호스트하고 유지 관리하는 표준화된 하드웨어 및 소프트웨어를 기반으로 하는 두 가지 배포 옵션을 제공합니다. 

SQL Server를 선택하면(온-프레미스 또는 Azure 가상 머신에서) 광범위한 구성이 필요한 기본 제공 기능을 사용할 수 있습니다. SQL Database를 사용할 경우 추가 성능에 맞게 중단 없이 강화 및 확장하는 옵션을 통해 사용량에 따라 지불합니다. SQL Database는 기본 제공 고가용성, 인텔리전스, 관리처럼 SQL Server에서 사용할 수 없는 추가 기능을 제공합니다.


Azure SQL Database는 다음과 같은 배포 옵션을 제공합니다.
  - [논리 SQL 서버](database/logical-servers.md)를 통해 관리되고 고유한 리소스 집합이 있는 [*단일 데이터베이스*](database/single-database-overview.md) 단일 데이터베이스는 SQL Server의 [포함된 데이터베이스](/sql/relational-databases/databases/contained-databases)와 유사합니다. 이 옵션은 새로운 클라우드 기반 애플리케이션의 최신 애플리케이션 개발에 맞게 최적화되어 있습니다. [하이퍼스케일](database/service-tier-hyperscale.md) 및 [서버리스](database/serverless-tier-overview.md) 옵션을 사용할 수 있습니다.
  - [논리 SQL 서버](database/logical-servers.md)를 통해 관리되는 공유 리소스 세트가 있는 데이터베이스 컬렉션인 [*탄력적 풀*](database/elastic-pool-overview.md). 단일 데이터베이스를 탄력적 풀로 이동하거나 탄력적 풀에서 제거할 수 있습니다. 이 옵션은 다중 테넌트 SaaS 애플리케이션 패턴을 사용하여 새로운 클라우드 기반 애플리케이션의 최신 애플리케이션 개발에 맞게 최적화되어 있습니다. 탄력적 풀은 가변 사용 패턴이 있는 여러 데이터베이스의 성능을 관리하기 위한 경제적인 솔루션입니다.

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

[Azure SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md)는 *PaaS(Platform as a Service)* 산업 범주에 속하며, 대부분의 클라우드로 마이그레이션에 가장 적합합니다. SQL Managed Instance는 리프트 앤 시프트 준비가 된 공유 리소스 세트가 있는 시스템 및 사용자 데이터베이스 컬렉션입니다.  
- 최소한의 변경으로 클라우드로 마이그레이션되며 안정적인 최신 SQL Server 기능을 사용하려는 새 애플리케이션 또는 기존 온-프레미스 애플리케이션에 가장 적합합니다. SQL Managed Instance 인스턴스는 [Microsoft SQL Server 데이터베이스 엔진](/sql/database-engine/sql-server-database-engine-overview) 인스턴스와 유사하며, 데이터베이스 및 인스턴스 범위 추가 기능에 필요한 공유 리소스를 제공합니다. 
- SQL Managed Instance에서는 데이터베이스 변경 없이 또는 최소한의 변경으로 온-프레미스에서 데이터베이스를 마이그레이션할 수 있습니다. 이 옵션은 Azure SQL Database의 모든 PaaS 혜택을 제공하지만 이전에 SQL Server VM에서만 사용할 수 있었던 기능을 추가합니다. 여기에는 기본 가상 네트워크 및 온-프레미스 SQL Server와의 거의 100% 호환성이 포함됩니다. SQL Managed Instance 인스턴스는 SQL Server를 Azure로 마이그레이션하는 데 필요한 전체 SQL Server 액세스 권한 및 기능 호환성을 제공합니다.

### <a name="sql-server-on-azure-vm"></a>Azure VM의 SQL Server

[Azure VM의 SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)는 업계 범주에 속하는 *IaaS(Infrastructure as a service)* 산업 부분으로 분류되며 Azure의 완전 관리형 VM(가상 머신) 내에서 SQL Server를 실행할 수 있도록 지원합니다. 
- 클라우드에 설치되고 호스트되는 SQL Server는 Azure에서 실행 중인 Windows Server 또는 Linux 가상 머신에서 실행되며, IaaS(infrastructure as a service)라고도 합니다. SQL 가상 머신은 데이터베이스 변경 없이 온-프레미스 SQL Server 데이터베이스 및 애플리케이션을 마이그레이션하는 좋은 옵션입니다. 모든 최신 버전의 SQL Server를 IaaS 가상 머신에 설치할 수 있습니다. 
- OS 수준의 액세스가 필요한 마이그레이션 및 애플리케이션에 적합합니다. Azure의 SQL 가상 머신은 변경 내용을 최소화하거나 변경하지 않고 클라우드로 신속하게 마이그레이션해야 하는 기존 애플리케이션을 위한 리프트 앤 시프트가 준비되어 있습니다. SQL 가상 머신은 Azure로 마이그레이션하는 데 필요한 SQL Server 인스턴스 및 기본 OS에 대한 전체 관리 제어 권한을 제공합니다. 
- SQL Database와 SQL Managed Instance의 가장 큰 차이점은 Azure Virtual Machines의 SQL Server에서는 데이터베이스 엔진을 완전히 제어할 수 있다는 점입니다. 유지 관리/패치 적용을 시작할 시기를 선택하고, 복구 모델을 단순 또는 대량 로그로 변경하고, 필요에 따라 서비스를 일시 중지하거나 시작하고, SQL Server 데이터베이스 엔진을 완전히 사용자 지정할 수 있습니다. 이러한 추가 제어 기능과 더불어 가상 머신을 관리해야 하는 책임도 추가됩니다.
- 온-프레미스 개발 및 테스팅 SQL Server 하드웨어를 구입하지 않는 빠른 개발 및 테스트 시나리오 SQL 가상 머신은 Microsoft에서 소유, 호스트 및 유지 관리하는 표준화된 하드웨어에서도 실행됩니다. SQL 가상 머신을 사용하는 경우 SQL Server 이미지에 이미 포함된 SQL Server 라이선스에 종량제를 사용하거나 기존 라이선스를 쉽게 사용할 수 있습니다. 필요에 따라 VM을 중지하거나 다시 시작할 수도 있습니다. 
- 기존 애플리케이션을 Azure로 마이그레이션하거나 기존 온-프레미스 애플리케이션을 하이브리드 배포의 클라우드로 확장하는 데 최적화됩니다. 또한 가상 머신에서 SQL Server를 사용하여 기존 SQL Server 애플리케이션을 개발하고 테스트할 수 있습니다. SQL 가상 머신을 사용하면 전용 SQL Server 인스턴스와 클라우드 기반 VM에 대한 모든 관리 권한을 갖습니다. 이 옵션은 가상 머신을 유지 관리하는 IT 리소스를 이미 갖춘 조직에게 가장 적합합니다. 이러한 기능을 사용하면 애플리케이션 관련 성능 및 가용성 요구 사항을 해결하는 고도로 사용자 지정된 시스템을 구축할 수 있습니다.


### <a name="comparison-table"></a>비교 표

다음 표에는 차이점이 추가로 나열되어 있지만 *SQL Database와 SQL Managed Instance는 모두 여러 데이터베이스를 프로비저닝하고 관리하는 전체 관리 비용을 최소로 줄이도록 최적화되어 있습니다.* 가상 머신, 운영 체제 또는 데이터베이스 소프트웨어를 관리할 필요가 없으므로 지속적인 관리 비용이 줄어듭니다. 업그레이드, 고가용성 또는 [백업](database/automated-backups-overview.md)을 관리할 필요가 없습니다. 

일반적으로 SQL Database와 SQL Managed Instance는 단일 IT 또는 개발 리소스로 관리되는 데이터베이스 수를 크게 늘립니다. 또한 [탄력적 풀](database/elastic-pool-overview.md)은 테넌트 격리, 데이터베이스 간에 리소스를 공유하여 비용을 줄이는 기능을 비롯한 기능으로 SaaS 다중 테넌트 애플리케이션 아키텍처를 지원합니다. [SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md)는 데이터베이스 간에 리소스를 공유하는 기능뿐만 아니라 기존 애플리케이션을 쉽게 마이그레이션할 수 있게 해주는 인스턴스 범위 기능을 지원합니다. 반면 [Azure VM의 SQL Server](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)는 익숙한 온-프레미스 환경과 가장 유사한 환경을 DBA에게 제공합니다. 


| Azure SQL Database | Azure SQL Managed Instance | Azure VM의 SQL Server |
| :--- | :--- | :--- |
|대부분의 온-프레미스 데이터베이스 수준 기능을 지원합니다. 가장 자주 사용되는 SQL Server 기능을 사용할 수 있습니다.<br/>99.995%의 가용성을 보장합니다.<br/>기본 제공 백업, 패치, 복구<br/>안정적인 최신 데이터베이스 엔진 버전<br/>필요한 리소스(CPU/스토리지)를 개별 데이터베이스에 할당할 수 있습니다.<br/>기본 제공 고급 인텔리전스 및 보안<br/>온라인에서 리소스(CPU/스토리지)를 변경할 수 있습니다.| 거의 모든 온-프레미스 인스턴스 수준 및 데이터베이스 수준 기능을 지원합니다. SQL Server와의 높은 호환성.<br/>99.99% 가용성 보장<br/>기본 제공 백업, 패치, 복구<br/>안정적인 최신 데이터베이스 엔진 버전<br/>SQL Server에서 쉽게 마이그레이션할 수 있습니다.<br/>Azure Virtual Network 내의 개인 IP 주소.<br/>기본 제공 고급 인텔리전스 및 보안<br/>온라인에서 리소스(CPU/스토리지)를 변경할 수 있습니다.| SQL Server 엔진을 완전히 제어할 수 있습니다. 모든 온-프레미스 기능을 지원합니다.<br/>최대 99.99%의 가용성이 보장됩니다.<br/>일치하는 온-프레미스 SQL Server의 버전과 완전히 동등합니다.<br/>고정된 잘 알려진 데이터베이스 엔진 버전입니다.<br/>SQL Server에서 쉽게 마이그레이션할 수 있습니다.<br/>Azure Virtual Network 내의 개인 IP 주소.<br/>SQL Server가 있는 호스트에 애플리케이션 또는 서비스를 배포할 수 있습니다.|
|SQL Server에서 마이그레이션하기 어려울 수 있습니다.<br/>일부 SQL Server 기능을 사용할 수 없습니다.<br/>정확한 유지 관리 시간이 보장되지 않습니다(하지만 거의 투명함).<br/>SQL Server 버전과의 호환성은 데이터베이스 호환성 수준을 사용해야만 달성될 수 있습니다.<br/>[Azure Private Link](database/private-endpoint-overview.md)를 통한 개인 IP 주소 지원.|사용할 수 없는 최소한의 SQL Server 기능이 아직 있습니다.<br/>정확한 유지 관리 시간이 보장되지 않습니다(하지만 거의 투명함).<br/>SQL Server 버전과의 호환성은 데이터베이스 호환성 수준을 사용해야만 달성될 수 있습니다.|사용자가 백업 및 패치를 관리해야 합니다.<br>사용자 고유의 고가용성 솔루션을 구현해야 합니다.<br/>리소스(CPU/스토리지)를 변경하는 동안 가동 중지 시간이 발생합니다.|
| 최대 100TB의 데이터베이스. | 최대 16TB | 최대 256TB의 스토리지가 있는 SQL Server 인스턴스 인스턴스는 필요한 만큼 많은 데이터베이스를 지원할 수 있습니다. |
| 온-프레미스 애플리케이션은 Azure SQL Database의 데이터에 액세스할 수 있습니다. | Azure ExpressRoute 또는 VPN Gateway를 사용하여 [기본 가상 네트워크 구현](managed-instance/vnet-existing-add-subnet.md) 및 온-프레미스 환경에 연결 | SQL 가상 머신을 사용하면 애플리케이션을 일부는 클라우드에서, 일부는 온-프레미스에서 실행할 수 있습니다. 예를 들어 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)를 통해 클라우드로 온-프레미스 네트워크 및 Active Directory 도메인을 확장할 수 있습니다. 하이브리드 클라우드 솔루션에 대한 자세한 내용은 [클라우드로 온-프레미스 데이터 솔루션 확장](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)을 참조하세요. |


## <a name="cost"></a>Cost

현금이 부족한 신생 기업이든 자리를 잡은 회사에서 제한된 예산으로 운영되는 팀이든 데이터베이스를 호스트하는 방법을 결정할 때 제한된 자금이 주된 동인으로 작용하는 경우가 많습니다. 이 섹션에서는 Azure SQL 서비스 제품군과 관련된 Azure의 청구 및 라이선스 기본 사항에 대해 알아봅니다.  또한 총 애플리케이션 비용을 계산하는 방법에 대해 알아봅니다.

### <a name="billing-and-licensing-basics"></a>청구 및 라이선스 기본 사항

현재 **SQL Database** 와 **SQL Managed Instance** 는 모두 서비스로 판매되며 각기 다른 리소스 요금이 책정된 여러 옵션과 여러 서비스 계층에서 사용할 수 있습니다. 모든 옵션은 고객이 선택하는 서비스 계층과 컴퓨팅 크기를 기준으로 시간당 고정 요금이 청구됩니다. 현재 지원되는 서비스 계층, 컴퓨팅 크기 및 스토리지 양에 대한 최신 정보는 [SQL Database에 대한 DTU 기반 구매 모델](database/service-tiers-dtu.md) 및 [SQL Database 및 SQL Managed Instance 모두에 대한 vCore 기반 구매 모델](database/service-tiers-vcore.md)을 참조하세요.

- SQL Database를 사용하면 필요에 맞는 서비스 계층을 기본 계층의 경우 5$/월부터 시작하는 다양한 가격에서 선택할 수 있으며 [탄력적 풀](database/elastic-pool-overview.md)을 만들어서 데이터베이스 간에 리소스를 공유하여 비용을 줄이고 사용량 급증을 수용할 수 있습니다.
- SQL Managed Instance를 선택하면 자체 라이선스를 사용할 수도 있습니다. BYOL(사용자 라이선스 필요)에 대한 자세한 내용은 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)을 참조하거나 [Azure 하이브리드 혜택 계산기](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database)를 사용하여 **최대 40%를 절감하는 방법** 을 알아보세요.

또한 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)으로 발신 인터넷 트래픽에 대해 요금이 청구됩니다. 애플리케이션의 다양한 처리량 요구에 맞게 서비스 계층과 컴퓨팅 크기를 동적으로 조정할 수 있습니다.

**SQL Database** 및 **SQL Managed Instance** 에서 데이터베이스 소프트웨어는 Azure에서 구성, 패치 적용 및 업그레이드를 자동으로 수행하며 이는 관리 비용을 줄입니다. 또한 [기본 제공 백업](database/automated-backups-overview.md) 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다.

**Azure VM의 SQL** 로 플랫폼에서 제공하는 SQL Server 이미지(라이선스 포함)를 사용하거나 사용자의 SQL Server 라이선스를 가져올 수 있습니다. 지원되는 모든 SQL Server 버전(2008R2, 2012, 2014, 2016, 2017, 2019) 및 버전(Developer, Express, Web, Standard, Enterprise)을 사용할 수 있습니다. 또한 이미지의 BYOL(사용자 라이선스 필요)를 사용할 수 있습니다. Azure에서 제공하는 이미지를 사용할 경우 VM 크기 및 선택하는 SQL Server 버전에 따라 운영 비용이 달라집니다. VM 크기 또는 SQL Server 버전과 관계없이 VM 디스크에 대한 Azure Storage 비용과 함께 SQL Server와 Windows 또는 Linux Server의 분당 라이선스 비용을 지불합니다. 분당 청구 옵션을 사용하면 추가 SQL Server 라이선스를 구입하지 않고 필요한 시간 동안만 SQL Server를 사용할 수 있습니다. SQL Server 사용자 라이선스를 Azure로 가져오는 경우, 서버 및 스토리지 비용만 청구됩니다. 고유한 라이선스 가져오기에 대한 자세한 내용은 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)을 참조하세요. 또한 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)으로 발신 인터넷 트래픽에 대해 요금이 청구됩니다.

#### <a name="calculating-the-total-application-cost"></a>총 애플리케이션 비용 계산

클라우드 플랫폼을 사용하기 시작하면 애플리케이션을 실행하는 비용에는 새 개발 및 진행 중인 관리 비용과 퍼블릭 클라우드 플랫폼 서비스 비용이 포함됩니다.

가격에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [SQL Database 및 SQL Managed Instance 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)
- [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)에 대한 [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>관리

많은 비즈니스의 경우 클라우드 서비스로 전환하는 결정은 드는 비용 만큼 관리의 복잡성을 오프로드하는 것과 관련됩니다. IaaS 및 PaaS에서는 Azure가 기본 인프라를 관리하고, 모든 데이터를 자동으로 복제하여 재해 복구를 제공하고, 데이터베이스 소프트웨어를 구성 및 업그레이드하고, 부하 분산을 관리하고, 데이터 센터 내에서 서버 오류가 발생하는 경우 장애 조치(failover)를 투명하게 수행합니다.

- **SQL Database** 및 **SQL Managed Instance** 에서는 데이터베이스를 계속 관리할 수 있지만 데이터베이스 엔진, 운영 체제 또는 하드웨어를 더 이상 관리할 필요가 없습니다. 계속 관리할 수 있는 항목의 예는 데이터베이스 및 로그인, 인덱스 및 쿼리 튜닝 및 감사 및 보안을 들 수 있습니다. 또한 다른 데이터 센터에 고가용성을 구성하는 데 필요한 구성 및 관리가 최소화됩니다.
- **Azure VM의 SQL** 에서는 운영 체제와 SQL Server 인스턴스 구성을 완벽히 제어할 수 있습니다. VM에서는 운영 체제 및 데이터베이스 소프트웨어를 업데이트/업그레이드할 시기 및 바이러스 백신 등의 추가 소프트웨어를 설치할 시기를 사용자가 결정합니다. 자동화된 일부 기능이 제공되어 패치, 백업 및 고가용성을 크게 간소화합니다. 또한 VM 크기, 디스크 수 및 해당 스토리지 구성도 사용자가 제어할 수 있습니다. Azure를 사용하면 VM이 필요한 크기를 변경할 수 있습니다. 자세한 내용은 [Azure용 Virtual Machine 및 클라우드 서비스 크기](../virtual-machines/sizes.md)를 참조하세요.

## <a name="service-level-agreement-sla"></a>SLA(서비스 수준 계약)

IT 부서의 경우 SLA(서비스 수준 계약)의 작동 시간 의무를 충족하는 일이 가장 우선합니다. 이 섹션에서는 각 데이터베이스 호스팅 옵션에 어떤 SLA를 적용하는지 살펴봅니다.

**Azure SQL Database** 및 **Azure SQL Managed Instance** 모두에 대해 Microsoft는 99.99%의 가용성 SLA를 제공합니다. 최신 정보는 [서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/azure-sql-database)을 참조하세요.

**Azure VM의 SQL** 의 경우 Microsoft는 가상 머신을 다루는 99.95%의 가용성 SLA를 제공합니다. 이 SLA는 VM에서 실행 중인 프로세스(예: SQL Server)를 다루지 않으며 가용성 집합에 두 개 이상의 VM 인스턴스를 호스트해야 합니다. 최신 정보는 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)를 참조하세요. VM 내에서 데이터베이스 고가용성(HA)을 이용하려면 SQL Server에서 지원되는 고가용성 옵션 중 하나(예: [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server))를 구성해야 합니다. 지원되는 고가용성 옵션을 사용하면 추가 SLA를 제공하지 않지만 >99.99%의 데이터베이스 가용성을 달성할 수 있습니다.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Azure로 이동해야 하는 경우

새로운 솔루션에 대한 개발자 생산성과 빠른 출시 시간이 중요한 경우 클라우드용으로 설계된 애플리케이션에 적합한 솔루션은 **Azure SQL Database** 입니다. 프로그래밍 방식 DBA와 비슷한 기능을 사용하여 기본 운영 체제 및 데이터베이스를 관리할 필요성을 낮추므로 클라우드 설계자와 개발자에게 이상적인 솔루션입니다.

**Azure SQL Managed Instance** 는 기존 애플리케이션을 Azure로 마이그레이션하는 과정을 간소화하므로 Azure에서 마이그레이션된 데이터베이스 애플리케이션을 빠르게 출시할 수 있습니다.

**Azure VM의 SQL** 은 기존 애플리케이션 또는 새 애플리케이션에 큰 데이터베이스가 필요하거나 SQL Server 또는 Windows/Linux의 모든 기능에 액세스해야 하며 새로운 온-프레미스 하드웨어를 획득하는 시간과 비용을 피하려는 경우에 유용합니다. 또한 기존 온-프레미스 애플리케이션 및 데이터베이스를 Azure로 있는 그대로 마이그레이션하기를 원하지만 SQL Database 또는 SQL Managed Instance가 적합하지 않은 경우에 적절한 선택입니다. 프레젠테이션, 애플리케이션 및 데이터 계층을 변경할 필요가 없으므로 기존 솔루션 재설계에 따른 비용과 예산이 절감됩니다. 대신 모든 솔루션을 Azure에 마이그레이션하고 Azure 플랫폼에 필요할 수 있는 일부 성능 최적화를 수행하는 데 집중할 수 있습니다. 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](./virtual-machines/windows/performance-guidelines-best-practices-checklist.md)를 참조하세요.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>다음 단계

- SQL Database를 시작하려면 [첫 Azure SQL Database](database/single-database-create-quickstart.md)를 참조하세요.
- SQL Managed Instance를 시작하려면 [첫 번째 Azure SQL Managed Instance](managed-instance/instance-create-quickstart.md)를 참조하세요. 
- [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.
- [SQL Managed Instance 가격 책정](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)을 참조하세요.
- Azure VM에서 SQL Server를 시작하려면 [Azure에서 SQL Server 가상 머신 프로비전](virtual-machines/windows/create-sql-vm-portal.md)을 참조하세요.
- [온-프레미스 데이터베이스에 적합한 SQL Database 또는 SQL Managed Instance SKU를 식별하세요](/sql/dma/dma-sku-recommend-sql-db/).
