---
title: 리소스 제한
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance에 대한 리소스 제한에 대한 개요를 제공합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: vladai78
ms.author: vladiv
ms.reviewer: mathoma, vladiv, sachinp
ms.date: 09/28/2021
ms.openlocfilehash: 86b823ca368223c2d789ff651fe831b76bbd5c58
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613380"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Azure SQL Managed Instance 리소스 제한 개요
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance의 기술적 특성 및 리소스 제한에 대한 개요를 제공하고 이러한 제한에 대한 증가를 요청하는 방법에 대한 정보를 제공합니다.

> [!NOTE]
> 지원되는 기능 및 T-SQL 문의 차이점은 [기능 차이](../database/features-comparison.md) 및 [T-SQL 문 지원](transact-sql-tsql-differences-sql-server.md)을 참조하세요. Azure SQL Database와 SQL Managed Instance에 대한 서비스 계층 간의 일반적인 차이점은 [서비스 계층 비교](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison)를 참조하세요.

## <a name="hardware-generation-characteristics"></a>하드웨어 세대 특성

SQL Managed Instance에는 기본 인프라 및 아키텍처에 따라 달라지는 특성과 리소스 제한이 있습니다. SQL Managed Instance는 두 가지 하드웨어 세대(Gen4 및 Gen5)에 배포될 수 있습니다. 하드웨어 세대에는 다음 표에서 설명하는 다양한 특성이 있습니다.

|   | **Gen4** | **5세대** |
| --- | --- | --- |
| **하드웨어** | Intel® E5-2673 v3(Haswell) 2.4GHz 프로세서, 연결형 SSD, vCore = 1PP(물리적 코어) | Intel® E5-2673 v4(Broadwell) 2.3GHz, Intel® SP-8160(Skylake) 및 Intel® 8272CL(Cascade Lake) 2.5GHz 프로세서, 고속 NVMe SSD, vCore=1 LP(하이퍼스레드) |
| **vCore 수** | 8, 16, 24개 vCore | vCore 4, 8, 16, 24, 32, 40, 64, 80개 |
| **최대 메모리(메모리/코어 비율)** | vCore당 7GB<br/>더 많은 메모리를 얻으려면 vCore를 더 추가합니다. | vCore당 5.1GB<br/>더 많은 메모리를 얻으려면 vCore를 더 추가합니다. |
| **메모리 내 최대 OLTP 메모리** | 인스턴스 제한: vCore당 1 ~ 1.5GB| 인스턴스 제한: vCore당 0.8 ~ 1.65GB |
| **최대 인스턴스 예약된 스토리지** |  일반 용도: 8TB <br/>중요 비즈니스용: 1TB | 일반 용도: 코어 수에 따라 8TB, 16tb (미리 보기)<br/> 코어 수에 따라 중요 비즈니스용 1TB, 2TB 또는 4TB |

> [!IMPORTANT]
> - Gen4 하드웨어는 단계적으로 중단되며 더 이상 새로운 배포에 사용할 수 없습니다. SQL Managed Instance의 모든 새 인스턴스는 Gen5 하드웨어에 배포해야 합니다.
> - 더 넓은 범위의 vCore 및 스토리지 확장성, 가속화된 네트워킹, 최고의 IO 성능 및 최소 지연 시간을 경험하려면 [SQL Managed Instance의 인스턴스를 5세대 하드웨어로 이동](../database/service-tiers-vcore.md)하는 것이 좋습니다.

### <a name="in-memory-oltp-available-space"></a>메모리 내 OLTP 사용 가능한 공간 

[중요 비즈니스용](../database/service-tier-business-critical.md) 서비스 계층의 메모리 내 OLTP 공간의 양은 vCore 및 하드웨어 생성 수에 따라 달라집니다. 다음 표에는 메모리 내 OLTP 개체에 사용할 수 있는 메모리 제한이 나열되어 있습니다.

| 메모리 내 OLTP 공간  | **5세대** | **Gen4** |
| --- | --- | --- |
| vCore 4개  | 3.14GB | |   
| vCore 8개  | 6.28GB | 8GB |
| 16개 vCore | 15.77GB | 20GB |
| 24개 vCore | 25.25GB | 36GB |
| 32개 vCore | 37.94GB | |
| 40개 vCore | 52.23GB | |
| 64 vCore | 99.9GB    | |
| 80개 vCore | 131.68GB| |

## <a name="service-tier-characteristics"></a>서비스 계층 특성

SQL Managed Instance에는 [범용](../database/service-tier-general-purpose.md) 및 [중요 비즈니스](../database/service-tier-business-critical.md)용의 두 가지 서비스 계층이 있습니다. 이러한 계층은 아래 표에서 설명한 대로 [다양한 기능](../database/service-tiers-general-purpose-business-critical.md)을 제공합니다.

> [!Important]
> 중요 비즈니스용 서비스 계층은 읽기 전용 워크로드에 사용할 수 있는 SQL Managed Instance(보조 복제본)의 추가 기본 제공 복사본을 제공합니다. 읽기-쓰기 쿼리와 읽기 전용/분석/보고 쿼리를 분리할 수 있는 경우 동일한 가격으로 두 배의 vCore 및 메모리를 얻게 됩니다. 보조 복제본은 기본 인스턴스보다 몇 초 지연될 수 있으므로 정확한 현재 데이터 상태가 필요하지 않은 보고/분석 워크로드를 오프로드하도록 설계되었습니다. 아래 표에서 **읽기 전용 쿼리** 는 보조 복제본에서 실행되는 쿼리입니다.

| **기능** | **범용** | **중요 비즈니스용** |
| --- | --- | --- |
| vCore 수\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*동일한 수의 vCore는 읽기 전용 쿼리에 전용으로 사용됩니다. |
| 최대 메모리 | Gen4: 56GB-168GB(7GB/vCore)<br/>Gen5: 20.4GB - 408GB(5.1GB/vCore)<br/>더 많은 메모리를 얻으려면 vCore를 더 추가합니다. | Gen4: 56GB-168GB(7GB/vCore)<br/>Gen5: 읽기/쓰기 쿼리를 위한 20.4GB - 408GB(5.1GB/vCore)<br/>읽기 전용 쿼리를 위한 추가 20.4GB - 408GB(5.1GB/vCore)<br/>더 많은 메모리를 얻으려면 vCore를 더 추가합니다. |
| 최대 인스턴스 스토리지 크기(예약됨) | - 4개 vCore의 경우 2TB(Gen5만 해당)<br/>- 다른 크기의 경우 8TB <br/>-16 vCores (Gen5에만 해당) 용 16TB (미리 보기) | Gen4: 1TB <br/> Gen5: <br/>- 1TB(4, 8, 16개 vCore용)<br/>- 2TB(24개 vCore용)<br/>- 4TB(32, 40, 64, 80개 vCore용) |
| 최대 데이터베이스 크기 | 현재 사용 가능한 인스턴스 크기 (vCores 수에 따라 최대 2tb, 648TB, 16tb (미리 보기)). | 현재 사용할 수 있는 최대 인스턴스 크기(vCore의 수에 따라 최대 1TB-4TB) |
| 최대 tempDB 크기 | 24GB/vCore(96 - 1,920GB) 및 현재 사용할 수 있는 인스턴스 스토리지 크기로 제한됩니다.<br/>더 많은 TempDB 공간을 얻으려면 vCore를 더 추가합니다.<br/> 로그 파일 크기는 120GB로 제한됩니다.| 현재 사용할 수 있는 최대 인스턴스 스토리지 크기입니다. |
| 인스턴스당 최대 데이터베이스 수 | 인스턴스 스토리지 크기 제한에 도달하지 않은 경우 100개의 사용자 데이터베이스. | 인스턴스 스토리지 크기 제한에 도달하지 않은 경우 100개의 사용자 데이터베이스. |
| 인스턴스당 최대 데이터베이스 파일 수 | 최대 280(인스턴스 스토리지 크기 또는 [Azure 프리미엄 디스크 스토리지 할당 공간](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) 제한에 도달하지 않은 경우) | 데이터베이스당 32,767개 파일(인스턴스 스토리지 크기 제한에 도달하지 않은 경우) |
| 최대 데이터 파일 크기 | 현재 사용할 수 있는 인스턴스 스토리지 크기(최대 2TB-8TB) 및 [Azure 프리미엄 디스크 스토리지 할당 공간](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)으로 제한됩니다. 8TB 보다 큰 데이터베이스에는 두 개 이상의 데이터 파일을 사용 합니다. | 현재 사용할 수 있는 인스턴스 스토리지 크기(최대 1TB-4TB)로 제한됩니다. |
| 최대 로그 파일 크기 | 2TB 및 현재 사용할 수 있는 인스턴스 스토리지 크기로 제한됩니다. | 2TB 및 현재 사용할 수 있는 인스턴스 스토리지 크기로 제한됩니다. |
| 데이터/로그 IOPS(근사치) | 인스턴스당 최대 30-40 K IOPS*, 파일당 500-7500<br/>\*[더 많은 IOPS를 얻기 위해 파일 크기 늘리기](#file-io-characteristics-in-general-purpose-tier)| 16K - 320K(4000 IOPS/vCore)<br/>더 나은 IO 성능을 얻으려면 vCore를 더 추가합니다. |
| 로그 쓰기 처리량 한도(인스턴스당) | vCore당 3MB/초<br/>인스턴스당 최대 120MB/초<br/>DB당 22 ~ 65MB/초<br/>\*[더 나은 IO 성능을 얻으려면 파일 크기를 늘립니다.](#file-io-characteristics-in-general-purpose-tier) | vCore당 4MB/초<br/>최대 96MB/초 |
| 데이터 처리량(근사치) | 파일당 100~250MB/초<br/>\*[더 나은 IO 성능을 얻으려면 파일 크기를 늘립니다.](#file-io-characteristics-in-general-purpose-tier) | 제한되지 않습니다. |
| 스토리지 IO 대기 시간(근사치) | 5~10ms | 1~2ms |
| 메모리 내 OLTP | 지원되지 않음 | 사용 가능, [크기는 vCore의 수에 따라 달라집니다.](#in-memory-oltp-available-space) |
| 최대 세션 | 30000 | 30000 |
| 최대 동시 작업자(요청) | Gen4: 210 * vCore 수 + 800<br>Gen5: 105 * vCore 수 + 800 | Gen4: 210 * vCore 수 + 800<br>Gen5: 105 * vCore 수 + 800 |
| [읽기 전용 복제본](../database/read-scale-out.md) | 0 | 1(가격에 포함) |
| 컴퓨팅 격리 | 범용 인스턴스는 물리적 하드웨어를 다른 인스턴스와 공유할 수 있으므로 Gen5는 지원되지 않습니다.<br/>Gen4는 사용 중단으로 인해 지원되지 않습니다.|Gen5:<br/>\- 40, 64, 80개 vCore에 지원<br/>\- 다른 크기에 대해서는 지원되지 않습니다.<br/><br/>Gen4는 사용 중단으로 인해 지원되지 않습니다.|


기타 고려 사항: 

- **현재 사용할 수 있는 인스턴스 스토리지 크기** 는 예약 인스턴스 크기와 사용된 스토리지 공간의 차이입니다.
- 사용자 및 시스템 데이터베이스의 데이터 및 로그 파일 크기는 최대 스토리지 크기 제한과 비교되는 인스턴스 스토리지 크기에 포함됩니다. [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) 시스템 뷰를 사용하여 데이터베이스에서 사용되는 총 공간을 확인합니다. 오류 로그는 영구적이지 않으며 크기에 포함되지 않습니다. 백업은 스토리지 크기에 포함되지 않습니다.
- 범용 계층의 처리량 및 IOPS는 SQL Managed Instance에 의해 명시적으로 제한되지 않는 [파일 크기](#file-io-characteristics-in-general-purpose-tier)에 따라 달라집니다.
  [자동 장애 조치(failover) 그룹](../database/auto-failover-group-configure.md)을 사용하여 다른 Azure 지역에서 읽을 수 있는 또 다른 복제본을 만들 수 있습니다.
- 최대 인스턴스 IOPS는 파일 레이아웃 및 워크로드 배포에 따라 달라집니다. 예를 들어 각각 최대 5K IOPS를 사용하여 1TB 파일을 7개 만들고, 각각 500 IOPS를 사용하여 작은 파일(128GB 미만)을 7개 만드는 경우 워크로드에서 모든 파일을 사용할 수 있는 경우 인스턴스당 38500 IOPS(7x5000+7x500)를 얻을 수 있습니다. 일부 IOPS는 자동 백업에도 사용됩니다.

[이 도움말에서 SQL Managed Instance 풀의 리소스 제한](instance-pools-overview.md#resource-limitations)에 대한 자세한 내용을 확인하세요.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>범용 계층의 파일 IO 특성

범용 서비스 계층에서 모든 데이터베이스 파일은 파일 크기에 따라 달라지는 전용 IOPS 및 처리량을 얻습니다. 더 큰 파일은 더 많은 IOPS 및 처리량을 얻습니다. 데이터베이스 파일의 IO 특성은 다음 표에 나와 있습니다.

| 파일 크기 | 0 이상 128GiB 이하 | 128 초과 512GiB 이하 | 0\.5 미만 1TiB 이상    | 1 미만 2TiB 이상    | 2 미만 4TiB 이상 | 4 미만 8TiB 이상 |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 파일당 IOPS       | 500   | 2300              | 5,000              | 7,500              | 7,500              | 12,500   |
| 파일당 처리량 | 100MiB/초 | 150MiB/초 | 200MiB/초 | 250MiB/초 | 250MiB/초 | 480MiB/초 | 

일부 데이터베이스 파일에 대해 높은 IO 대기 시간이 발생하거나 IOPS/처리량이 한도에 도달하는 것을 확인한 경우 [파일 크기를 늘려](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337) 성능을 향상시킬 수 있습니다.

또한 최대 로그 쓰기 처리량에 대한 인스턴스 수준 제한이 있으므로(값은 위 참조, 예: 22MB/초) 인스턴스 처리량 제한에 도달하기 때문에 로그 파일 전체에서 최대 파일에 도달하지 못할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

SQL Managed Instance는 [지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)에서만 만들 수 있습니다. 현재 지원되지 않는 지역에서 SQL Managed Instance를 만들려면 [Azure Portal을 통해 지원 요청을 보낼 수 있습니다](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>지원되는 구독 유형

SQL Managed Instance는 현재 다음 유형의 구독에만 배포를 지원합니다.

- [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [CSP(클라우드 서비스 공급자)](/partner-center/csp-documents-and-learning-resources)
- [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 구독자를 위한 월간 Azure 크레딧이 있는 구독](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>지역별 리소스 제한

> [!Note]
> 구독 가능한 지역에 대한 최신 정보는 먼저 [지역 선택](https://aka.ms/sqlcapacity)을 확인하세요.

지원되는 구독 유형에는 지역당 제한된 수의 리소스가 포함될 수 있습니다. SQL Managed Instance에는 Azure 지역당 두 가지 기본 제한이 있습니다(구독 유형에 따라 [Azure Portal에서 특별한 지원 요청](../database/quota-increase-request.md)을 만들어 필요에 따라 늘릴 수 있습니다.

- **서브넷 제한**: SQL Managed Instance의 인스턴스가 단일 지역에 배포되는 서브넷의 최대 수입니다.
- **vCore 단위 제한**: 단일 지역의 모든 인스턴스에 배포할 수 있는 최대 vCore 단위 수입니다. 하나의 GP vCore는 1개의 vCore 단위를 사용하고 하나의 BC vCore는 4개의 vCore 단위를 사용합니다. 총 인스턴스 수는 vCore 단위 제한 내에 있기만 하면 제한되지 않습니다.

> [!Note]
> 이러한 제한은 기술 제한이 아닌 기본 설정입니다. 현재 지역에 더 많은 인스턴스가 필요한 경우 [Azure Portal에서 특별한 지원 요청을 만들어](../database/quota-increase-request.md) 제한을 늘릴 수 있습니다. 대안으로, 지원 요청을 보내지 않고 다른 Azure 지역에 새 SQL Managed Instance의 인스턴스를 만들 수 있습니다.

다음 표에서는 지원되는 구독 유형에 대한 **기본 지역 제한** 을 보여 줍니다. 기본 제한은 아래에 설명된 지원 요청을 사용하여 확장할 수 있습니다.

|구독 유형| SQL Managed Instance 서브넷의 최대 수 | 최대 vCore 단위 수* |
| :---| :--- | :--- |
|CSP |16(일부 지역에서는 30**)|960(일부 지역의 경우 1440**)|
|EA|16(일부 지역에서는 30**)|960(일부 지역의 경우 1440**)|
|Enterprise 개발/테스트|6|320|
|종량제|6|320|
|종량제 개발/테스트|6|320|
|Azure Pass|3|64|
|BizSpark|3|64|
|BizSpark Plus|3|64|
|Microsoft Azure 스폰서쉽|3|64|
|Microsoft 파트너 네트워크|3|64|
|Visual Studio Enterprise(MPN)|3|64|
|Visual Studio Enterprise|3|32|
|Visual Studio Enterprise(BizSpark)|3|32|
|Visual Studio Professional|3|32|
|MSDN 플랫폼|3|32|

\* 배포를 계획할 때 BC(중요 비즈니스용) 서비스 계층에는 GP(범용) 서비스 계층보다 4배 많은 vCore 용량이 필요하다는 점을 고려하세요. 예: 1GP vCore = 1vCore 단위 및 1BC vCore = 4vCore 기본 제한에 대한 사용량 분석을 간소화하려면 SQL Managed Instance가 배포된 지역의 모든 서브넷에 대한 vCore 단위를 요약하고 결과를 구독 유형의 인스턴스 단위 제한과 비교합니다. **최대 vCore 단위 수** 한도는 한 지역의 각 구독에 적용됩니다. 여러 서브넷에 배포된 모든 vCore의 합계가 **최대 vCore 단위 수** 보다 작거나 같아야 한다는 점을 제외하고 개별 서브넷당 제한은 없습니다.

\*\* 더 큰 서브넷 및 vCore 제한은 오스트레일리아 동부, 미국 동부, 미국 동부 2, 북유럽, 미국 중남부, 동남아시아, 영국 남부, 서부 유럽, 미국 서부 2에서 사용할 수 있습니다.

> [!IMPORTANT]
> vCore 및 서브넷 한도가 0인 경우 구독 유형에 대한 기본 지역 제한이 설정되지 않았음을 의미합니다. 필요한 vCore 및 서브넷 값을 제공하는 동일한 프로시저에 따라 특정 지역에서 구독 액세스 권한을 얻기 위해 할당량 증가 요청을 사용할 수도 있습니다.

## <a name="request-a-quota-increase"></a>할당량 증가 요청

현재 지역에 더 많은 인스턴스가 필요한 경우 Azure Portal을 사용하여 할당량을 확장하도록 지원 요청을 보냅니다. 자세한 내용은 [Azure SQL Database에 대한 할당량 증가 요청](../database/quota-increase-request.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- SQL Managed Instance에 대한 자세한 내용은 [SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- 가격 정보는 [SQL Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
- 첫 번째 SQL Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조하세요.
