---
title: 리소스 제한
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance에 대한 리소스 제한에 대한 개요를 제공합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: reference
author: vladai78
ms.author: vladiv
ms.reviewer: mathoma, vladiv, sachinp, wiassaf
ms.date: 10/18/2021
ms.openlocfilehash: 0be0ef8bdf421f6ac75467ce2226915b060a5b8e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714983"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Azure SQL Managed Instance 리소스 제한 개요
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance의 기술적 특성 및 리소스 제한에 대한 개요를 제공하고 이러한 제한에 대한 증가를 요청하는 방법에 대한 정보를 제공합니다.

> [!NOTE]
> 지원되는 기능 및 T-SQL 문의 차이점은 [기능 차이](../database/features-comparison.md) 및 [T-SQL 문 지원](transact-sql-tsql-differences-sql-server.md)을 참조하세요. Azure SQL Database와 SQL Managed Instance에 대한 서비스 계층 간의 일반적인 차이점은 [서비스 계층 비교](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison)를 참조하세요.

## <a name="hardware-generation-characteristics"></a>하드웨어 세대 특성

SQL Managed Instance에는 기본 인프라 및 아키텍처에 따라 달라지는 특성과 리소스 제한이 있습니다. SQL Managed Instance를 여러 하드웨어 세대에 배포할 수 있습니다. 

> [!NOTE]
> Gen5 하드웨어 생성은 **표준 시리즈 (Gen5)** 로 이름이 바뀌고, microsoft는 두 가지 새로운 하드웨어 세대를 제한 된 미리 보기로 제공 합니다. **프리미엄 시리즈** 및 메모리 액세스에 **최적화 된 프리미엄 시리즈** 를 소개 합니다.

이전 세대 하드웨어 세대에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 [이전 세대 하드웨어 생성 정보](#previous-generation-hardware) 를 참조 하세요. 

하드웨어 세대에는 다음 표에서 설명하는 다양한 특성이 있습니다.

|    | **표준 시리즈 (Gen5)** | **Premium 시리즈 (미리 보기)** | **메모리 액세스에 최적화 된 프리미엄 시리즈 (미리 보기)** | 
|:-- |:-- |:-- |:-- |
| **CPU** |  Intel &reg; E5-2673 v4 (Broadwell) 2.3 ghz, intel &reg; SP-8160 (Skylake) 및 intel &reg; 8272Cl (케스케이드 Lake) 2.5 GHz 프로세서 | Intel &reg; 8370c (아이스 Lake) 2.8 GHz 프로세서 | Intel &reg; 8370c (아이스 Lake) 2.8 GHz 프로세서 |
| **vCore 수** <BR>vCore = 1 LP (하이퍼 스레드) | 4-80 vCores | 4-80 vCores | 4-64 vCores |
| **최대 메모리 (메모리/vCore 비율)** | vCore당 5.1GB<br/>더 많은 메모리를 얻으려면 vCore를 더 추가합니다. | vCore당 7GB | vCore 당 13.6 GB |
| **메모리 내 최대 OLTP 메모리** |  인스턴스 제한: vCore당 0.8 ~ 1.65GB | 인스턴스 제한: 1.1-vCore 당 2.3 GB | 인스턴스 제한: 2.2-vCore 당 4.5 GB |
| **최대 인스턴스 예약 된 저장소**\* | **일반 용도:** 최대 16tb<br/> **중요 비즈니스용:** 최대 4tb | **일반 용도:** 최대 16tb<br/> **중요 비즈니스용:** 최대 5.5 TB | **일반 용도:** 최대 16tb <br/> **중요 비즈니스용:** 최대 16tb |

\*[Vcores의 수](#service-tier-characteristics)에 따라 달라 집니다.

### <a name="regional-support-for-premium-series-hardware-generations-preview"></a>프리미엄 시리즈 하드웨어 세대에 대 한 지역별 지원 (미리 보기)

프리미엄 시리즈 하드웨어 세대 (공개 미리 보기)에 대 한 지원은 현재 이러한 특정 지역 에서만 사용할 수 있습니다.


| 지역 | **Premium 시리즈** | **메모리 액세스에 최적화 된 프리미엄 시리즈** | 
|:--- |:--- |:--- |
| 미국 중부 | 예 |  | 
| 미국 동부  | 예 |  | 
| 미국 동부 2 | 예 | 예 | 
| 북유럽 |  | 예 | 
| 서유럽 | 예 | 예 | 
| 미국 서부 |  | 예 |  
| 미국 서부 2 | 예 | 예 | 


### <a name="in-memory-oltp-available-space"></a>메모리 내 OLTP 사용 가능한 공간 

[중요 비즈니스용](../database/service-tier-business-critical.md) 서비스 계층의 메모리 내 OLTP 공간의 양은 vcores 및 하드웨어 생성 수에 따라 달라 집니다. 다음 표에는 메모리 내 OLTP 개체에 사용할 수 있는 메모리 한도가 정리 되어 있습니다.

| **vCore 수** | **표준 시리즈 (Gen5)** | **Premium 시리즈** | **메모리 액세스에 최적화 된 프리미엄 시리즈** | 
|:--- |:--- |:--- |:--- |
| vCore 4개    | 3.14GB | 4.39 GB | 8.79 GB | 
| vCore 8개    | 6.28GB | 8.79 GB | 22.06 GB |  
| 16개 vCore | 15.77 GB | 22.06 GB | 57.58 GB |
| 24개 vCore | 25.25 GB | 35.34 GB | 93.09 GB |
| 32개 vCore | 37.94 GB | 53.09 GB | 128.61 GB |
| 40개 vCore | 52.23 GB | 73.09 GB | 164.13 GB |
| 64 vCore | 99.9GB | 139.82 GB | 288.61 GB |
| 80개 vCore | 131.68GB| 184.30GB | 해당 없음 |

## <a name="service-tier-characteristics"></a>서비스 계층 특성

SQL Managed Instance에는 [범용](../database/service-tier-general-purpose.md) 및 [중요 비즈니스](../database/service-tier-business-critical.md)용의 두 가지 서비스 계층이 있습니다. 이러한 계층은 아래 표에서 설명한 대로 [다양한 기능](../database/service-tiers-general-purpose-business-critical.md)을 제공합니다.

> [!Important]
> 중요 비즈니스용 서비스 계층은 읽기 전용 워크로드에 사용할 수 있는 SQL Managed Instance(보조 복제본)의 추가 기본 제공 복사본을 제공합니다. 읽기-쓰기 쿼리와 읽기 전용/분석/보고 쿼리를 분리할 수 있는 경우 동일한 가격으로 두 배의 vCore 및 메모리를 얻게 됩니다. 보조 복제본은 기본 인스턴스보다 몇 초 지연될 수 있으므로 정확한 현재 데이터 상태가 필요하지 않은 보고/분석 워크로드를 오프로드하도록 설계되었습니다. 아래 표에서 **읽기 전용 쿼리** 는 보조 복제본에서 실행되는 쿼리입니다.

| **기능** | **범용** | **중요 비즈니스용** |
| --- | --- | --- |
| vCore 수\* | 4, 8, 16, 24, 32, 40, 64, 80 |  **표준 시리즈(Gen5)**: 4, 8, 16, 24, 32, 40, 64, 80 <BR> **Premium 시리즈:** 4, 8, 16, 24, 32, 40, 64, 80 <BR> **메모리 최적화 프리미엄 시리즈:** 4, 8, 16, 24, 32, 40, 64<br/>\*동일한 수의 vCore는 읽기 전용 쿼리에 전용으로 사용됩니다. |
| 최대 메모리 | **표준 시리즈(Gen5)**: 20.4GB - 408GB(5.1GB/vCore)<BR> **Premium 시리즈:** 28GB - 560GB(7GB/vCore)<BR> **메모리 최적화 프리미엄 시리즈:** 54.4GB - 870.4GB(13.6GB/vCore) | **표준 시리즈(Gen5)**: 각 복제본에서 20.4GB - 408GB(5.1GB/vCore)<BR> **Premium 시리즈:** 각 복제본에서 28GB - 560GB(7GB/vCore)<BR> **메모리 최적화 프리미엄 시리즈:** 각 복제본에서 54.4GB - 870.4GB(13.6GB/vCore) |
| 최대 인스턴스 스토리지 크기(예약됨) | - vCore 4개에 대해 2 TB<br/>- vCore 8개에 대해 8 TB<br/>- 다른 크기의 경우 16 TB <BR> | **표준 시리즈(Gen5)**: <br/>- 1TB(4, 8, 16개 vCore용)<br/>- 2TB(24개 vCore용)<br/>- 4TB(32, 40, 64, 80개 vCore용) <BR> **Premium 시리즈:** <BR>- 4개, 8개 vCore의 경우 1 TB<br/>- 16개, 24개 vCore의 경우 2 TB<br/>- 32개 vCore의 경우 4 TB<br/>- 40, 64, 80 vCore의 경우 5.5 TB<br/> **메모리 최적화 프리미엄 시리즈:** <BR>- 4개, 8개 vCore의 경우 1 TB<br/>- 16개, 24개 vCore의 경우 2 TB<br/>- 32개 vCore의 경우 4 TB<br/>- 40개 vCore의 경우 5.5 TB<br/>- 64개 vCore의 경우 16 TB<br/> |
| 최대 데이터베이스 크기 | 현재 사용 가능한 인스턴스 크기까지(vCore 수에 따라 다름) | 현재 사용 가능한 인스턴스 크기까지(vCore 수에 따라 다름) |
| 최대 tempDB 크기 | 24GB/vCore(96 - 1,920GB) 및 현재 사용할 수 있는 인스턴스 스토리지 크기로 제한됩니다.<br/>더 많은 TempDB 공간을 얻으려면 vCore를 더 추가합니다.<br/> 로그 파일 크기는 120GB로 제한됩니다.| 현재 사용할 수 있는 최대 인스턴스 스토리지 크기입니다. |
| 인스턴스당 최대 데이터베이스 수 | 인스턴스 스토리지 크기 제한에 도달하지 않은 경우 100개의 사용자 데이터베이스. | 인스턴스 스토리지 크기 제한에 도달하지 않은 경우 100개의 사용자 데이터베이스. |
| 인스턴스당 최대 데이터베이스 파일 수 | 최대 280(인스턴스 스토리지 크기 또는 [Azure 프리미엄 디스크 스토리지 할당 공간](doc-changes-updates-known-issues.md#exceeding-storage-space-with-small-database-files) 제한에 도달하지 않은 경우) | 데이터베이스당 32,767개 파일(인스턴스 스토리지 크기 제한에 도달하지 않은 경우) |
| 최대 데이터 파일 크기 | 각 데이터 파일의 최대 크기는 8 TB입니다. 8 TB보다 큰 데이터베이스에 대해 두 개 이상의 데이터 파일을 사용합니다. | 현재 사용 가능한 인스턴스 크기까지(vCore 수에 따라 다름) |
| 최대 로그 파일 크기 | 2TB 및 현재 사용할 수 있는 인스턴스 스토리지 크기로 제한됩니다. | 2TB 및 현재 사용할 수 있는 인스턴스 스토리지 크기로 제한됩니다. |
| 데이터/로그 IOPS(근사치) | 인스턴스당 최대 30-40 K IOPS*, 파일당 500-7500<br/>\*[더 많은 IOPS를 얻기 위해 파일 크기 늘리기](#file-io-characteristics-in-general-purpose-tier)| 16K - 320K(4000 IOPS/vCore)<br/>더 나은 IO 성능을 얻으려면 vCore를 더 추가합니다. |
| 로그 쓰기 처리량 한도(인스턴스당) | vCore당 3MB/초<br/>인스턴스당 최대 120MB/초<br/>DB당 22-65MB/s(로그 파일 크기에 따라 다름)<br/>\*[더 나은 IO 성능을 얻으려면 파일 크기를 늘립니다.](#file-io-characteristics-in-general-purpose-tier) | vCore당 4MB/초<br/>최대 96MB/초 |
| 데이터 처리량(근사치) | 파일당 100~250MB/초<br/>\*[더 나은 IO 성능을 얻으려면 파일 크기를 늘립니다.](#file-io-characteristics-in-general-purpose-tier) | 제한되지 않습니다. |
| 스토리지 IO 대기 시간(근사치) | 5~10ms | 1~2ms |
| 메모리 내 OLTP | 지원되지 않음 | 사용 가능, [크기는 vCore의 수에 따라 달라집니다.](#in-memory-oltp-available-space) |
| 최대 세션 | 30000 | 30000 |
| 최대 동시 작업자(요청) | 105 * vCore 수 + 800 | 105 * vCore 수 + 800 |
| [읽기 전용 복제본](../database/read-scale-out.md) | 0 | 1(가격에 포함) |
| 컴퓨팅 격리 | 범용 인스턴스가 다른 인스턴스와 물리적 하드웨어를 공유할 수 있기 때문에 지원되지 않습니다.| **표준 시리즈(Gen5)**:<br/> 40개, 64개, 80개 vCore 지원<BR> **Premium 시리즈**: 64, 80 vcores에 지원 됨 <BR> **메모리 액세스에 최적화 된 프리미엄 시리즈**: 64 vcores에 지원 됨 |


기타 고려 사항: 

- **현재 사용할 수 있는 인스턴스 스토리지 크기** 는 예약 인스턴스 크기와 사용된 스토리지 공간의 차이입니다.
- 사용자 및 시스템 데이터베이스의 데이터 및 로그 파일 크기는 최대 스토리지 크기 제한과 비교되는 인스턴스 스토리지 크기에 포함됩니다. [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) 시스템 뷰를 사용하여 데이터베이스에서 사용되는 총 공간을 확인합니다. 오류 로그는 영구적이지 않으며 크기에 포함되지 않습니다. 백업은 스토리지 크기에 포함되지 않습니다.
- 범용 계층의 처리량 및 IOPS는 SQL Managed Instance에 의해 명시적으로 제한되지 않는 [파일 크기](#file-io-characteristics-in-general-purpose-tier)에 따라 달라집니다.
  [자동 장애 조치(failover) 그룹](../database/auto-failover-group-configure.md)을 사용하여 다른 Azure 지역에서 읽을 수 있는 또 다른 복제본을 만들 수 있습니다.
- 최대 인스턴스 IOPS는 파일 레이아웃 및 워크로드 배포에 따라 달라집니다. 예를 들어, 각각 500 IOPS를 사용 하 여 최대 5 개의 작은 파일 (128 GB 미만)을 포함 하는 7 x 1TB 파일을 만드는 경우, 워크 로드에서 모든 파일을 사용할 수 있는 경우 인스턴스당 38500 IOPS (7x5000 + 7x500)를 가져올 수 있습니다. 일부 IOPS는 자동 백업에도 사용 됩니다.

[이 도움말에서 SQL Managed Instance 풀의 리소스 제한](instance-pools-overview.md#resource-limitations)에 대한 자세한 내용을 확인하세요.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>범용 계층의 파일 IO 특성

범용 서비스 계층에서 모든 데이터베이스 파일은 파일 크기에 따라 전용 IOPS 및 처리량을 가져옵니다. 더 큰 파일은 더 많은 IOPS 및 처리량을 얻습니다. 데이터베이스 파일의 IO 특성은 다음 표에 나와 있습니다.

| **파일 크기** | **>= 0 및 <= 128 GiB** | **>128 및 <= 512 GiB** | **>0.5 및 <= 1 TiB**    | **>1 및 <= 2 TiB**    | **>2 및 <= 4 TiB** | **>4 및 <= 8 TiB** | **>8 및 <= 16 TiB** |
|:--|:--|:--|:--|:--|:--|:--|:--|
| 파일당 IOPS       | 500   | 2300              | 5,000  | 7,500              | 7,500              | 12,500   | |
| 파일당 처리량 | 100MiB/초 | 150MiB/초 | 200MiB/초 | 250MiB/초| 250MiB/초 | 480MiB/초 |  |

일부 데이터베이스 파일에 대해 높은 IO 대기 시간이 발생하거나 IOPS/처리량이 한도에 도달하는 것을 확인한 경우 [파일 크기를 늘려](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337) 성능을 향상시킬 수 있습니다.

최대 로그 쓰기 처리량에 대 한 인스턴스 수준 제한도 있습니다 (값 (예: 22 m b/초). 인스턴스 처리량 제한에 도달 하 여 로그 파일의 전체에서 최대 파일에 도달 하지 못할 수 있습니다.

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
> 구독 가능한 지역에 대한 최신 정보는 먼저 [지역 선택](../capacity-errors-troubleshoot.md)을 확인하세요.

지원되는 구독 유형에는 지역당 제한된 수의 리소스가 포함될 수 있습니다. SQL Managed Instance에는 Azure 지역당 두 가지 기본 제한이 있습니다(구독 유형에 따라 [Azure Portal에서 특별한 지원 요청](../database/quota-increase-request.md)을 만들어 필요에 따라 늘릴 수 있습니다.

- **서브넷 제한**: SQL Managed Instance의 인스턴스가 단일 지역에 배포되는 서브넷의 최대 수입니다.
- **vCore 단위 제한**: 단일 지역의 모든 인스턴스에 배포할 수 있는 최대 vCore 단위 수입니다. 하나의 GP vCore는 vCore 단위 하나를 사용 하 고 하나의 BC vCore는 4 개의 vCore를 사용 합니다. 총 인스턴스 수는 vCore 단위 제한 내에 있기만 하면 제한되지 않습니다.

> [!Note]
> 이러한 제한은 기술 제한이 아닌 기본 설정입니다. 현재 지역에 더 많은 인스턴스가 필요한 경우 [Azure Portal에서 특별한 지원 요청을 만들어](../database/quota-increase-request.md) 제한을 늘릴 수 있습니다. 대안으로, 지원 요청을 보내지 않고 다른 Azure 지역에 새 SQL Managed Instance의 인스턴스를 만들 수 있습니다.

다음 표에서는 지원되는 구독 유형에 대한 **기본 지역 제한** 을 보여 줍니다. 기본 제한은 아래에 설명된 지원 요청을 사용하여 확장할 수 있습니다.

|구독 유형| SQL Managed Instance 서브넷의 최대 수 | 최대 vCore 단위 수* |
| :---| :--- | :--- |
|CSP |16 (일부 지역에서는 30 * *)|960(일부 지역의 경우 1440**)|
|EA|16 (일부 지역에서는 30 * *)|960(일부 지역의 경우 1440**)|
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

## <a name="previous-generation-hardware"></a>이전 세대 하드웨어

이 섹션에서는 이전 세대 하드웨어 세대에 대 한 세부 정보를 제공 합니다. [SQL Managed Instance 인스턴스를 표준 시리즈 (Gen5) 하드웨어로 이동](../database/service-tiers-vcore.md) 하 여 더 광범위 한 vcore 및 저장소 확장성, 가속화 된 네트워킹, 최적의 IO 성능 및 최소 대기 시간을 경험 하는 것이 좋습니다.

- Gen4는 단계적으로 진행 되 고 있으며 새 배포에 사용할 수 없습니다. 

### <a name="hardware-generation-characteristics"></a>하드웨어 세대 특성

|   | **Gen4** | 
| --- | --- | 
| **하드웨어** | Intel &reg; E5-2673 v3 (Haswell) 2.4 GHz 프로세서, 연결 된 SSD vCore = 1PP (물리적 코어) |   
| **vCore 수** | 8, 16, 24개 vCore | 
| **최대 메모리(메모리/코어 비율)** | vCore당 7GB<br/>더 많은 메모리를 얻으려면 vCore를 더 추가합니다. |  
| **메모리 내 최대 OLTP 메모리** |  인스턴스 제한: vCore당 1 ~ 1.5GB |
| **최대 인스턴스 예약된 스토리지** |  일반 용도: 8TB <br/>중요 비즈니스용: 1TB | 

### <a name="in-memory-oltp-available-space"></a>메모리 내 OLTP 사용 가능한 공간 

[중요 비즈니스용](../database/service-tier-business-critical.md) 서비스 계층의 메모리 내 OLTP 공간의 양은 vCore 및 하드웨어 생성 수에 따라 달라집니다. 다음 표에는 메모리 내 OLTP 개체에 사용할 수 있는 메모리 제한이 나열되어 있습니다.

| 메모리 내 OLTP 공간    |  **Gen4** |
| --- |  --- |
| vCore 8개    | 8GB |
| 16개 vCore |  20GB |
| 24개 vCore |  36GB |


### <a name="service-tier-characteristics"></a>서비스 계층 특성

| **기능** | **범용** | **중요 비즈니스용** |
| --- | --- | --- |
| vCore 수\* | Gen4: 8, 16, 24 | Gen4: 8, 16, 24 <BR>\*동일한 수의 vCore는 읽기 전용 쿼리에 전용으로 사용됩니다. |
| 최대 메모리 | Gen4: 56GB-168GB(7GB/vCore)<br/>더 많은 메모리를 얻으려면 vCore를 더 추가합니다. | Gen4: 56GB-168GB(7GB/vCore)<br/>읽기 전용 쿼리를 위한 추가 20.4GB - 408GB(5.1GB/vCore)<br/>더 많은 메모리를 얻으려면 vCore를 더 추가합니다. |
| 최대 인스턴스 스토리지 크기(예약됨) | Gen4:8TB | Gen4: 1TB  |
| 최대 데이터베이스 크기 | Gen4: 현재 사용할 수 있는 인스턴스 크기 (vCores의 수에 따라 최대 2tb)입니다. | Gen4: 현재 사용 가능한 인스턴스 크기 (vCores의 수에 따라 최대 1tb-4tb) |
| 최대 tempDB 크기 | Gen4:24gb/vCore (96-1920 GB) 및 현재 사용 가능한 인스턴스 저장소 크기로 제한 됩니다.<br/>더 많은 TempDB 공간을 얻으려면 vCore를 더 추가합니다.<br/> 로그 파일 크기는 120GB로 제한됩니다.| Gen4: 현재 사용할 수 있는 인스턴스 저장소 크기입니다. |
| 인스턴스당 최대 데이터베이스 수 | Gen4:100 인스턴스 저장소 크기 제한에 도달 하지 않는 한 사용자 데이터베이스를 합니다. | Gen4:100 인스턴스 저장소 크기 제한에 도달 하지 않는 한 사용자 데이터베이스를 합니다. |
| 인스턴스당 최대 데이터베이스 파일 수 | Gen4: 인스턴스 저장소 크기 또는 [Azure Premium 디스크 저장소 할당 공간](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) 제한에 도달 하지 않는 한 최대 280입니다. | Gen4: 인스턴스 저장소 크기 제한에 도달 하지 않는 한 데이터베이스당 32767 파일 |
| 최대 데이터 파일 크기 | Gen4: 현재 사용할 수 있는 인스턴스 저장소 크기 (최대 2tb tb) 및 [Azure Premium 디스크 저장소 할당 공간](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)으로 제한 됩니다. 8TB 보다 큰 데이터베이스에는 두 개 이상의 데이터 파일을 사용 합니다. | Gen4: 현재 사용할 수 있는 인스턴스 저장소 크기 (최대 1tb-4 TB)로 제한 됩니다. |
| 최대 로그 파일 크기 | Gen4:2tb 및 현재 사용 가능한 인스턴스 저장소 크기로 제한 됩니다. | Gen4:2tb 및 현재 사용 가능한 인스턴스 저장소 크기로 제한 됩니다. |
| 데이터/로그 IOPS(근사치) | Gen4: 인스턴스당 최대 30-40 K IOPS *, 500-7500<br/>\*[더 많은 IOPS를 얻기 위해 파일 크기 늘리기](#file-io-characteristics-in-general-purpose-tier)| Gen4:16 K-320 K (4000 IOPS/vCore)<br/>더 나은 IO 성능을 얻으려면 vCore를 더 추가합니다. | 
| 로그 쓰기 처리량 한도(인스턴스당) | Gen4: vCore 당 3MB/s<br/>인스턴스당 최대 120MB/초<br/>DB당 22 ~ 65MB/초<br/>\*[더 나은 IO 성능을 얻으려면 파일 크기를 늘립니다.](#file-io-characteristics-in-general-purpose-tier) | Gen4: vCore당 4MB/s<br/>최대 96MB/초 |
| 데이터 처리량(근사치) | Gen4: 파일당 100-250MB/s<br/>\*[더 나은 IO 성능을 얻으려면 파일 크기를 늘립니다.](#file-io-characteristics-in-general-purpose-tier) | Gen4: 제한되지 않습니다. |
| 스토리지 IO 대기 시간(근사치) | Gen4: 5-10ms | Gen4: 1-2ms |
| 메모리 내 OLTP | Gen4: 지원되지 않음 | Gen4: 사용 가능, [크기는 vCore 수에 따라 달라집니다.](#in-memory-oltp-available-space) |
| 최대 세션 | Gen4: 30000 | Gen4: 30000 |
| 최대 동시 작업자(요청) | Gen4: 210 * vCore 수 + 800 | Gen4: 210 * vCore 수 + 800 |
| [읽기 전용 복제본](../database/read-scale-out.md) | Gen4: 0 | Gen4: 1(가격에 포함) |
| 컴퓨팅 격리 | Gen4: 지원되지 않음 | Gen4: 지원되지 않음 |


## <a name="next-steps"></a>다음 단계

- SQL Managed Instance에 대한 자세한 내용은 [SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- 가격 정보는 [SQL Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
- 첫 번째 SQL Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조하세요.