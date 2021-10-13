---
title: Azure SQL Managed Instance란?
description: Azure SQL Managed Instance가 어떻게 최신 SQL Server(Enterprise Edition) 데이터베이스 엔진과 거의 100% 호환되는지 알아보세요.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: niko-neugebauer
ms.author: nneugebauer
ms.reviewer: mathoma, vanto
ms.date: 01/14/2021
ms.openlocfilehash: 4073a78b9dcab9d6e9ce4b11c839c0c9785d3285
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667348"
---
# <a name="what-is-azure-sql-managed-instance"></a>Azure SQL Managed Instance란?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 확장 가능한 지능형 클라우드 데이터베이스 서비스로, 완전히 관리되는 서비스형 에버그린 플랫폼의 모든 이점에 가장 광범위한 SQL Server 데이터베이스 엔진 호환성을 결합했습니다. SQL Managed Instance는 최신 SQL Server(Enterprise Edition) 데이터베이스 엔진과 거의 100% 호환되며, 일반적인 보안 문제를 해결하는 원시 [VNet(가상 네트워크)](../../virtual-network/virtual-networks-overview.md) 구현과 기존 SQL Server 고객에게 친숙한 [비즈니스 모델](https://azure.microsoft.com/pricing/details/sql-database/)을 제공합니다. SQL Managed Instance를 통해 기존 SQL Server 고객은 애플리케이션 및 데이터베이스 변경을 최소화하고 온-프레미스 애플리케이션을 클라우드로 이동할 수 있습니다. 이와 동시에 SQL Managed Instance는 모든 PaaS 기능(자동 패치 및 버전 업데이트, [자동화된 백업](../database/automated-backups-overview.md), [고가용성](../database/high-availability-sla.md))을 유지하므로 관리 오버헤드와 TCO를 크게 줄일 수 있습니다.

Azure SQL Managed Instance를 처음 사용하는 경우에는 심층 [Azure SQL 비디오 시리즈](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)에서 *Azure SQL Managed Instance* 비디오를 참조하세요.
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> SQL Managed Instance를 현재 사용할 수 있는 지역의 목록은 [지원되는 지역](resource-limits.md#supported-regions)을 참조하세요.

다음 다이어그램은 SQL Managed Instance의 주요 기능을 설명합니다.

![주요 특징](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL Managed Instance는 최대한 적은 마이그레이션 작업을 통해 많은 수의 앱을 온-프레미스 또는 IaaS, 자체 구축 또는 ISV 제공 환경에서 완전 관리형 PaaS 클라우드 환경으로 마이그레이션하려는 고객을 위해 설계되었습니다. 완전히 자동화된 [Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)를 사용하면 기존 SQL Server 인스턴스를 SQL Managed Instance로 옮길 수 있습니다. 여기에서는 SQL Server와의 호환성이 제공되고 네이티브 VNet 지원을 통해 고객 인스턴스를 완벽하게 격리합니다. 마이그레이션 옵션 및 도구에 대한 자세한 내용은 [마이그레이션 개요: SQL Server에서 Azure SQL Managed Instance로 마이그레이션](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md)을 참조하세요.</br> Software Assurance를 사용하면 [SQL Server에 대한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 SQL Managed Instance에서 기존 라이선스를 할인된 가격으로 교환할 수 있습니다. SQL Managed Instance는 클라우드에서 높은 보안과 풍부한 프로그래밍 기능 영역이 필요한 SQL Server 인스턴스를 위한 최상의 마이그레이션 대상입니다.

> [!TIP]
> Azure SQL을 더 잘 활용하려면 어떻게 해야 하나요? [설문 조사를 수행하세요](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="key-features-and-capabilities"></a>주요 특징 및 기능

SQL Managed Instance는 Azure SQL Database와 SQL Server 데이터베이스 엔진 모두에서 사용할 수 있는 최상의 기능을 결합했습니다.

> [!IMPORTANT]
> SQL Managed Instance에는 온라인 작업, 자동 계획 수정 및 기타 엔터프라이즈 성능 향상을 비롯한 SQL Server 최신 버전의 모든 기능이 실행됩니다. 제공되는 기능에 대한 비교는 [기능 비교: Azure SQL Managed Instance와 SQL Server 비교](../database/features-comparison.md)를 참조하세요.

| **PaaS의 이점** | **비즈니스 연속성** |
| --- | --- |
|하드웨어를 구입하고 관리할 필요가 없음 <br>기본 인프라 관리를 위한 오버헤드가 없음 <br>신속한 프로비전 및 서비스 크기 조정 <br>자동 패치 적용 및 버전 업그레이드 <br>다른 PaaS 데이터 서비스와 통합 |99.99% 작동 시간 SLA  <br>기본 제공되는 [고가용성](../database/high-availability-sla.md) <br>[자동화된 백업](../database/automated-backups-overview.md)으로 보호되는 데이터 <br>고객이 구성 가능한 백업 보존 기간 <br>사용자가 시작하는 [백업](/sql/t-sql/statements/backup-transact-sql?preserve-view=true&view=azuresqldb-mi-current) <br>[특정 시점 데이터베이스 복원](../database/recovery-using-backups.md#point-in-time-restore) 기능 |
|**보안 및 규정 준수** | **관리**|
|격리된 환경([VNet 통합](connectivity-architecture-overview.md), 단일 테넌트 서비스, 전용 컴퓨팅 및 스토리지) <br>[TDE(투명한 데이터 암호화)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD(Azure Active Directory) 인증](../database/authentication-aad-overview.md), Single Sign-On 지원 <br> [Azure AD 서버 보안 주체(로그인)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) <br>Azure SQL Database와 동일한 표준 준수 <br>[SQL 감사](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |서비스 프로비전 및 크기 조정을 자동화하는 Azure Resource Manager API <br>수동 서비스 프로비전 및 크기 조정을 위한 Azure Portal 기능 <br>데이터 마이그레이션 서비스

> [!IMPORTANT]
> Azure SQL Managed Instance는 다양한 규정 준수 표준에 따라 인증을 받았습니다. 자세한 내용은 [Microsoft Azure 규정 준수 제안](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers)을 참조하세요. 여기서 **SQL Database** 아래에 최신 SQL Managed Instance 규정 준수 인증서 목록이 있습니다.

SQL Managed Instance의 주요 기능을 다음 표에서 볼 수 있습니다.

|기능 | 설명|
|---|---|
| SQL Server 버전/빌드 | SQL Server 데이터베이스 엔진(안정적인 최신 버전) |
| 관리되는 자동 백업 | Yes |
| 기본 제공 인스턴스/데이터베이스 모니터링 및 메트릭 | Yes |
| 자동 소프트웨어 패치 | Yes |
| 최신 데이터베이스 엔진 기능 | Yes |
| 데이터베이스당 데이터 파일(행) 수 | 여러 |
| 데이터베이스당 로그 파일(로그) 수 | 1 |
| VNet - Azure Resource Manager 배포 | Yes |
| VNet - 클래식 배포 모델 | No |
| 포털 지원 | Yes|
| 기본 제공 통합 서비스(SSIS) | 아니요 - SSIS는 [Azure Data Factory PaaS](../../data-factory/tutorial-deploy-ssis-packages-azure.md)의 일부입니다. |
| 기본 제공 분석 서비스(SSAS) | 아니요 - SSAS는 별도의 [PaaS](../../analysis-services/analysis-services-overview.md)입니다. |
| 기본 제공 보고 서비스(SSRS) | 아니요 - Azure VM에서 SSRS를 호스트하는 대신, [Power BI의 페이지 매김 보고서](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)를 사용합니다. SQL Managed Instance는 SSRS를 서비스로 실행할 수는 없지만 SQL Server 인증을 사용하여 Azure Virtual Machine에 설치된 보고 서버용 [SSRS 카탈로그 데이터베이스](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements)를 호스트할 수 있습니다. |
|||

## <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

SQL Managed Instance의 [vCore 기반 구매 모델](../database/service-tiers-vcore.md)은 유연성, 제어 기능, 투명성 및 온-프레미스 워크로드 요구 사항을 클라우드로 전환하는 직관적인 방법을 제공합니다. 이 모델을 사용하면 워크로드 요구 사항에 따라 컴퓨팅, 메모리 및 스토리지를 선택할 수 있습니다. 또한 vCore 모델은 [SQL Server에 대한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 통해 최대 55%까지 비용을 절약할 수 있습니다.

vCore 모델에서는 하드웨어 세대를 선택할 수 있습니다.

- **Gen4** 논리 CPU는 Intel&reg; E5-2673 v3(Haswell) 2.4GHz 프로세서, SSD 장착, 물리적 코어, 코어당 7GB RAM 및 vCore가 8~24개인 컴퓨팅 크기를 기반으로 합니다.
- **Gen5** 논리 CPU는 Intel&reg; E5-2673 v4(Broadwell) 2.3GHz, Intel&reg; SP-8160(Skylake) 및 Intel&reg; 8272CL(Cascade Lake) 2.5GHz 프로세서, 고속 NVMe SSD, 하이퍼 스레드 논리 코어 및 코어가 4~80개인 컴퓨팅 크기를 기반으로 합니다.

하드웨어 세대 간의 차이점에 대한 자세한 내용은 [SQL Managed Instance 리소스 제한](resource-limits.md#hardware-generation-characteristics)을 참조하세요.

## <a name="service-tiers"></a>서비스 계층

SQL Managed Instance는 두 개의 서비스 계층에서 사용할 수 있습니다.

- **범용**: 일반적인 성능 및 I/O 대기 시간 요구 사항이 있는 애플리케이션용으로 설계되었습니다.
- **중요 비즈니스용**: I/O 대기 시간 요구 사항이 낮고 기본 유지 관리 작업이 워크로드에 미치는 영향이 최소화된 애플리케이션용으로 설계되었습니다.

두 서비스 계층은 모두 99.99% 가용성을 보장하며 스토리지 크기와 컴퓨팅 용량을 독립적으로 선택할 수 있습니다. Azure SQL Managed Instance의 고가용성 아키텍처에 대한 자세한 내용은 [고가용성 및 Azure SQL Managed Instance](../database/high-availability-sla.md)를 참조하세요.

### <a name="general-purpose-service-tier"></a>범용 서비스 계층

다음 목록에서는 범용 서비스 계층의 주요 특징을 설명합니다.

- 일반적인 성능 요구 사항이 있는 대부분의 비즈니스 애플리케이션에 적합한 디자인
- 고성능 Azure Blob Storage(8TB)
- 신뢰할 수 있는 Azure Blob Storage 및 [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)에 기반한 [고가용성](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) 기본 제공

자세한 내용은 [범용 계층의 스토리지 레이어](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) 및 [SQL Managed Instance(범용)의 스토리지 성능 모범 사례 및 고려 사항](/archive/blogs/sqlcat/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose)을 참조하세요.

서비스 계층 간의 차이점에 대한 자세한 내용은 [SQL Managed Instance 리소스 제한](resource-limits.md#service-tier-characteristics)을 참조하세요.

### <a name="business-critical-service-tier"></a>중요 비즈니스 서비스 계층

중요 비즈니스용 서비스 계층은 I/O 요구 사항이 높은 애플리케이션용으로 빌드됩니다. 여러 개의 격리된 복제본을 사용하여 장애에 대한 최고의 복원력을 제공합니다.

다음 목록에서는 중요 비즈니스용 서비스 계층의 주요 특징을 설명합니다.

- 최고의 성능과 HA 요구 사항을 가진 대부분의 비즈니스 애플리케이션용으로 설계됨
- 초고속 로컬 SSD 스토리지(4세대의 최대 1TB 및 5세대의 최대 4TB)와 함께 제공됨
- [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) 및 [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)을 기반으로 [고가용성](../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) 기본 제공
- 보고 및 기타 읽기 전용 워크로드에 사용할 수 있는 추가적인 [읽기 전용 데이터베이스 복제본](../database/read-scale-out.md) 기본 제공
- 고성능 요구 사항이 있는 워크로드에 사용할 수 있는 [메모리 내 OLTP](../in-memory-oltp-overview.md)  

서비스 계층 간의 차이점에 대한 자세한 내용은 [SQL Managed Instance 리소스 제한](resource-limits.md#service-tier-characteristics)을 참조하세요.

## <a name="management-operations"></a>관리 작업

Azure SQL Managed Instance에서는 새로운 관리형 인스턴스를 자동으로 배포하고, 인스턴스 속성을 업데이트하고, 더 이상 필요 없는 인스턴스를 삭제하는 데 사용할 수 있는 관리 작업을 제공합니다. 관리 작업에 대한 자세한 설명은 [관리되는 인스턴스 관리 작업 개요](management-operations-overview.md) 페이지에서 확인할 수 있습니다.

## <a name="advanced-security-and-compliance"></a>고급 보안 및 규정 준수

SQL Managed Instance에는 Azure 플랫폼과 SQL Server 데이터베이스 엔진에서 제공하는 고급 보안 기능이 기본 제공됩니다.

### <a name="security-isolation"></a>보안 격리

SQL Managed Instance는 Azure 플랫폼의 다른 테넌트와 추가로 격리되는 보안을 제공합니다. 보안 격리에는 다음이 포함됩니다.

- Azure ExpressRoute 또는 VPN Gateway를 사용하여 [기본 가상 네트워크 구현](connectivity-architecture-overview.md) 및 온-프레미스 환경에 연결
- 기본 배포에서 SQL 엔드포인트가 개인 IP 주소를 통해서만 노출되므로 프라이빗 Azure 또는 하이브리드 네트워크에서 안전하게 연결
- 전용 기본 인프라(컴퓨팅, 스토리지)를 제공하는 단일 테넌트

다음 다이어그램은 애플리케이션에 대한 다양한 연결 옵션을 설명합니다.

![고가용성](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

서브넷 수준의 VNet 통합 및 네트워킹 정책 적용에 대한 자세한 내용은 [Managed Instance에 대한 VNet 아키텍처](connectivity-architecture-overview.md) 및 [애플리케이션을 Managed Instance에 연결](connect-application-instance.md)을 참조하세요.

> [!IMPORTANT]
> 동일한 서브넷에서 보안 요구 사항에 따라 허용되는 곳마다 여러 개의 관리되는 인스턴스를 배치하면 추가적인 이점을 얻을 수 있습니다. 인스턴스를 동일한 서브넷에 배치하면 네트워킹 인프라 유지 관리가 크게 단순화되고 인스턴스 프로비저닝 시간이 단축됩니다. 긴 프로비저닝 기간은 첫 번째로 관리되는 인스턴스를 서브넷에 배포하는 비용과 관련되기 때문입니다.

### <a name="security-features"></a>보안 기능

Azure SQL Managed Instance는 데이터를 보호하는 데 사용할 수 있는 고급 보안 기능 세트를 제공합니다.

- [SQL Managed Instance 감사](auditing-configure.md)는 데이터베이스 이벤트를 추적하고 Azure Storage 계정에 있는 감사 로그 파일에 이를 기록합니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.
- 이동 중인 데이터 암호화 - SQL Managed Instance는 전송 계층 보안을 사용하여 이동 중인 데이터를 암호화함으로써 데이터를 보호합니다. 전송 계층 보안 외에도, SQL Managed Instance는 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용하여 전송 중인 데이터, 저장 데이터, 쿼리를 처리 중인 데이터를 보호하는 기능을 제공합니다. Always Encrypted는 중요한 데이터의 도용과 관련한 위반에 대해 데이터 보안을 제공합니다. 예를 들어 Always Encrypted를 사용하여 쿼리를 처리하는 동안에도 신용 카드 번호가 데이터베이스에 암호화되어 저장됩니다. 또한 해당 데이터를 처리해야 하는 권한이 부여된 직원 또는 애플리케이션에 의해 사용 시점에 암호를 해독할 수 있습니다.
- [Advanced Threat Protection](threat-detection-configure.md)은 데이터베이스를 액세스하거나 악용하려는 비정상적이고 잠재적으로 해로운 시도를 감지하는 서비스에 내장된 추가적인 보안 인텔리전스 계층을 제공하여 [감사](auditing-configure.md) 기능을 보완합니다. 의심스러운 활동, 잠재적 취약성 및 SQL 삽입 공격은 물론 비정상적인 데이터베이스 액세스 패턴에 대해 경고합니다. Advanced Threat Protection 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)에서 확인할 수 있습니다. 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.  
- [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)은 권한이 없는 사용자에게 중요한 데이터를 마스킹하여 데이터 노출을 제한합니다. 동적 데이터 마스킹을 수행하면 애플리케이션 계층에 대한 영향을 최소화하면서 표시할 중요한 데이터의 양을 지정할 수 있도록 하여 중요한 데이터에 대한 무단 액세스를 방지할 수 있습니다. 데이터베이스의 데이터는 변경되지 않으면서 지정된 데이터베이스 필드에 대한 쿼리의 결과 집합에서 중요한 데이터를 숨기는 정책 기반 보안 기능입니다.
- [행 수준 보안](/sql/relational-databases/security/row-level-security)(RLS)을 통해 쿼리를 실행하는 사용자의 특성(예: 그룹 멤버 자격 또는 실행 컨텍스트)을 기반으로 하여 데이터베이스 테이블의 행에 대한 액세스를 제어할 수 있습니다. RLS는 애플리케이션에서 보안의 설계 및 코딩을 간소화합니다. RLS를 사용하면 데이터 행 액세스에 대한 제한을 구현할 수 있습니다. 예를 들어 작업자가 소속 부서와 관련된 데이터 행에만 액세스하게 하거나 데이터 액세스를 관련 데이터로 제한할 수 있습니다.
- [TDE(투명한 데이터 암호화)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)는 SQL Managed Instance 데이터 파일을 암호화하며, 이를 저장 데이터 암호화라고 합니다. TDE는 데이터 및 로그 파일에 대한 실시간 I/O 암호화 및 암호 해독을 수행합니다. 암호화에는 복구 중에 사용 가능하도록 데이터베이스 부트 레코드에 저장된 DEK(데이터베이스 암호화 키)가 사용됩니다. 투명한 데이터 암호화를 사용하여 Managed Instance의 모든 데이터베이스를 보호할 수 있습니다. TDE는 스토리지 미디어의 도용으로부터 보호하기 위해 많은 규정 준수 표준에 필요한 정지된 암호화 기술로써 SQL Server에서 입증되었습니다.

암호화된 데이터베이스를 SQL Managed Instance로 마이그레이션하는 작업은 Azure Database Migration Service 또는 네이티브 복원을 통해 지원됩니다. 네이티브 복원을 사용하여 암호화된 데이터베이스를 마이그레이션하려면 SQL Server 인스턴스에서 SQL Managed Instance로 기존 TDE 인증서를 마이그레이션해야 합니다. 마이그레이션 옵션에 대한 자세한 내용은 [SQL Server를 Azure SQL Managed Instance로 마이그레이션 가이드](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)를 참조하세요.

## <a name="azure-active-directory-integration"></a>Azure Active Directory 통합

SQL Managed Instance는 기존 SQL Server 데이터베이스 엔진 로그인 및 Azure AD와 통합된 로그인을 지원합니다. Azure AD 서버 보안 주체(로그인)(**공개 미리 보기**)는 온-프레미스 환경에서 사용 중인 온-프레미스 데이터베이스 로그인의 Azure 클라우드 버전입니다. Azure AD 서버 보안 주체(로그인)를 사용하면 동일한 Managed Instance 내에서 데이터베이스 간 쿼리를 비롯한 모든 인스턴스 수준 작업을 수행할 수 있는 실제 인스턴스 범위의 보안 주체로 Azure AD 테넌트의 사용자 및 그룹을 지정할 수 있습니다.

Azure AD 서버 보안 주체(로그인)를 만들기 위한 새 구문인 **FROM EXTERNAL PROVIDER** 가 도입되었습니다. 구문에 대한 자세한 내용은 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)을 참조하고 [SQL Managed Instance에 대한 Azure Active Directory 관리자 프로비저닝](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)을 살펴보세요.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 다단계 인증

SQL Managed Instance를 사용하면 [Azure Active Directory 통합](../database/authentication-aad-overview.md)에서 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure Active Directory는 MFA([Multi-Factor Authentication](../database/authentication-mfa-ssms-configure.md))를 제공하여 단일 로그온 프로세스를 지원하는 동시에 데이터 및 애플리케이션 보안을 향상시킵니다.

### <a name="authentication"></a>인증

SQL Managed Instance 인증은 사용자가 데이터베이스에 연결할 때 자신의 ID를 증명하는 방법을 나타냅니다. SQL Managed Instance는 두 가지 인증 유형을 지원합니다.  

- **SQL 인증**:

  이 인증 방법은 사용자 이름과 암호를 사용합니다.
- **Azure Active Directory 인증**:

  이 인증 방법은 Azure Active Directory에서 관리하는 ID를 사용하며, 관리되는 도메인과 통합된 도메인에 대해 지원됩니다. [가능한 경우](/sql/relational-databases/security/choose-an-authentication-mode) Active Directory 인증(통합 보안)을 사용합니다.

### <a name="authorization"></a>권한 부여

권한 부여는 사용자가 Azure SQL Managed Instance 내 데이터베이스에서 수행할 수 있는 작업을 나타내며, 사용자 계정의 데이터베이스 역할 멤버 자격과 개체 수준 권한으로 제어합니다. SQL Managed Instance는 SQL Server 2017과 동일한 권한 부여 기능을 갖고 있습니다.

## <a name="database-migration"></a>데이터베이스 마이그레이션

SQL Managed Instance는 온-프레미스 또는 IaaS 데이터베이스 구현에서 대량의 데이터베이스를 마이그레이션하는 사용자 시나리오를 대상으로 합니다. SQL Managed Instance는 마이그레이션 가이드에 설명된 여러 데이터베이스 마이그레이션 옵션을 지원합니다. 자세한 내용은 [마이그레이션 개요: SQL Server에서 Azure SQL Managed Instance로 마이그레이션](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md)을 참조하세요.

### <a name="backup-and-restore"></a>백업 및 복원  

마이그레이션 방식에서는 Azure Blob Storage에 SQL을 백업합니다. Azure Storage Blob에 저장된 백업은 [T-SQL RESTORE 명령](/sql/t-sql/statements/restore-statements-transact-sql?preserve-view=true&view=azuresqldb-mi-current)을 사용하여 Managed Instance에 직접 복원할 수 있습니다.

- Wide World Importers 표준 데이터베이스 백업 파일을 복원하는 방법을 보여주는 빠른 시작은 [Managed Instance에 백업 파일 복원](restore-sample-database-quickstart.md)을 참조하세요. 이 빠른 시작에서는 백업 파일을 Azure Blob 스토리지에 업로드하고 SAS(보안 공유 액세스 서명) 키를 사용하여 보호해야 한다는 것을 보여 줍니다.
- URL에서 복원하는 방법에 대한 자세한 내용은 [URL에서 네이티브 복원](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md#backup-and-restore)을 참조하세요.

> [!IMPORTANT]
> Managed Instance에서 백업은 다른 Managed Instance로 복원될 수만 있습니다. SQL Server 인스턴스 또는 Azure SQL Database로 복원할 수는 없습니다.

### <a name="database-migration-service"></a>Database Migration Service

Azure Database Migration Service는 가동 중지 시간을 최소화하면서 여러 데이터베이스 소스에서 Azure 데이터 플랫폼으로 원활하게 마이그레이션할 수 있도록 설계된 완벽하게 관리되는 서비스입니다. 이 서비스는 기존 타사 및 SQL Server 데이터베이스를 Azure VM의 Azure SQL Database, Azure SQL Managed Instance 및 SQL Server로 이동하는 데 필요한 작업을 간소화합니다. [Database Migration Service를 사용하여 온-프레미스 데이터베이스를 SQL Managed Instance로 마이그레이션하는 방법](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.

## <a name="sql-features-supported"></a>지원되는 SQL 기능

SQL Managed Instance의 목표는 단계별 릴리스 계획을 통해 최신 SQL Server 버전과 거의 100% 호환되는 노출 영역 호환성을 제공하는 것입니다. 기능 및 비교 목록은 [SQL Managed Instance 기능 비교](../database/features-comparison.md)를 참조하고, SQL Managed Instance와 SQL Server 간의 T-SQL 차이점 목록은 [SQL Managed Instance 및 SQL Server 간의 T-SQL 차이점](transact-sql-tsql-differences-sql-server.md)을 참조하세요.

SQL Managed Instance는 SQL Server 2008 데이터베이스와 호환됩니다. SQL Server 2005 데이터베이스 서버에서 직접 마이그레이션할 수 있으며, 마이그레이션된 SQL Server 2005 데이터베이스의 호환성 수준은 SQL Server 2008로 업데이트됩니다.
  
다음 다이어그램은 SQL Managed Instance의 노출 영역 호환성을 설명합니다.  

![노출 영역 호환성](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>SQL Server 온-프레미스와 Server Managed Instance의 주요 차이점

Server Managed Instance는 클라우드에서 항상 최신 상태로 유지되는 이점이 있습니다. 즉, SQL Server의 일부 기능이 사용되지 않거나 사용 중지되거나 대체될 수 있습니다. 특정 기능이 약간 다른 방식으로 작동하거나 사용자에게 전체 제어 권한이 없는 환경에서 서비스가 실행되면 이 사실을 도구에서 인식해야 하는 경우가 있습니다.

주요 차이점:

- [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 고가용성이 기본 제공되고 미리 구성됩니다.
- 자동 백업 및 특정 시점 복원만 있습니다. 고객은 자동 백업 체인을 방해하지 않는 `copy-only` 백업을 시작할 수 있습니다.
- 전체 실제 경로 지정은 지원되지 않으므로 해당하는 모든 시나리오를 약간씩 다르게 지원해야 합니다. RESTORE DB는 WITH MOVE를 지원하지 않고, CREATE DB는 실제 경로를 허용하지 않고, BULK INSERT는 Azure Blob에서만 작동합니다.
- SQL Managed Instance는 Windows 인증의 클라우드 대안으로 [Azure AD 인증](../database/authentication-aad-overview.md)을 지원합니다.
- SQL Managed Instance는 메모리 내 OLTP 개체가 포함된 데이터베이스의 XTP 파일 그룹 및 파일을 자동으로 관리합니다.
- SQL Managed Instance는 SSIS(SQL Server Integration Services)를 지원하며 SSIS 패키지를 저장하는 SSIS 카탈로그(SSISDB)를 호스트할 수 있지만, Azure Data Factory의 관리형 Azure-SSIS IR(Integration Runtime)에서 실행됩니다. [Data Factory에서 Azure-SSIS IR 만들기](../../data-factory/create-azure-ssis-integration-runtime.md)를 참조하세요. SSIS 기능을 비교하려면 [SQL Managed Instance와 SQL Database 비교](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)를 참조하세요.

### <a name="administration-features"></a>관리 기능

SQL Managed Instance 서비스는 사용자를 위해 작업을 수행하거나 이러한 작업을 크게 간소화하므로 SQL Managed Instance를 사용하여 시스템 관리자가 관리 작업 시간을 단축할 수 있습니다. [OS/RDBMS 설치 및 패치](../database/high-availability-sla.md), [동적 인스턴스 크기 조정 및 구성](../database/single-database-scale.md), [백업](../database/automated-backups-overview.md), [데이터베이스 복제](replication-between-two-instances-configure-tutorial.md)(시스템 데이터베이스 포함), [고가용성 구성](../database/high-availability-sla.md), 상태 및 [성능 모니터링](../../azure-monitor/insights/azure-sql.md) 데이터 스트림 구성 등을 예로 들 수 있습니다.

자세한 내용은 [지원되거나 지원되지 않는 SQL Managed Instance 기능 목록](../database/features-comparison.md)및 [SQL Managed Instance와 SQL Server 간의 T-SQL 차이점](transact-sql-tsql-differences-sql-server.md)을 참조하세요.

### <a name="programmatically-identify-a-managed-instance"></a>관리되는 인스턴스를 프로그래밍 방식으로 식별

다음 표는 Transact-SQL을 통해 액세스할 수 있으며 애플리케이션이 SQL Managed Instance와 함께 작동 중인지 감지하고 중요한 속성을 검색하는 데 사용할 수 있는 여러 속성을 보여 줍니다.

|속성|값|의견|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure(RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|이 값은 SQL Database와 같습니다. 이것이 SQL 엔진 버전 12(SQL Server 2014)를 나타내는 것은 **아닙니다**. SQL Managed Instance는 항상 안정적인 최신 SQL 엔진 버전을 실행합니다(사용 가능한 최신 RTM 버전 이상).  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|이 값은 SQL Database와 같습니다.|
|`SERVERPROPERTY('EngineEdition')`|8|이 값은 Managed Instance를 고유하게 식별합니다.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|`<instanceName>`.`<dnsPrefix>`.database.windows.net 형식의 전체 인스턴스 DNS 이름. 여기서 `<instanceName>`은 고객이 제공한 이름이고, `<dnsPrefix>`는 글로벌 DNS 이름의 고유성을 보장하기 위해 이름에서 자동으로 생성되는 부분(예: “wcus17662feb9ce98”)입니다.|예: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>다음 단계

- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](../database/features-comparison.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [SQL Managed Instance VNet 구성](connectivity-architecture-overview.md)을 참조하세요.
- 백업 파일에서 관리형 인스턴스를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Azure Database Migration Service를 사용하여 마이그레이션하는 방법에 대한 자습서는 [Database Migration Service를 사용한 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
- 기본 제공 문제 해결 인텔리전스를 사용하는 SQL Managed Instance 데이터베이스의 고급 성능 모니터링에 대해 자세히 알아보려면 [Azure SQL 분석을 사용하여 Azure SQL Managed Instance 모니터링](../../azure-monitor/insights/azure-sql.md)을 참조하세요.
- 가격 책정 정보는 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.