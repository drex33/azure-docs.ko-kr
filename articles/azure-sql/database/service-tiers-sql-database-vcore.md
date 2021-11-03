---
title: vCore 구매 모델
description: vCore 구매 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 스케일링하고, 온-프레미스 성능과 일치시키며, Azure SQL Database 가격을 최적화할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 09/10/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: fb168fad05acf84fd9b64f34c929176933a1fd2f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026035"
---
# <a name="vcore-purchase-model-overview---azure-sql-database"></a>vCore 구매 모델 개요 - Azure SQL Database 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 [Azure SQL Database](sql-database-paas-overview.md)의 vCore 구매 모델을 검토합니다. vCore 및 DTU 구매 모델 중 선택하는 방법에 대한 자세한 내용은 [vCore 및 DTU 구매 모델 중에서 선택](purchasing-models.md)을 참조하세요.

Azure SQL Database에서 사용하는 vCore(가상 코어) 구매 모델은 DTU 구매 모델과 비교할 때 몇 가지 이점을 제공합니다.

- 더 높은 컴퓨팅, 메모리, I/O 및 스토리지 제한.
- 워크로드에 대한 컴퓨팅 및 메모리 요구 사항을 더 잘 일치시키기 위한 하드웨어 세대 제어.
- [AHB(Azure 하이브리드 혜택)](../azure-hybrid-benefit.md)의 가격 할인
- 컴퓨팅을 지원하는 하드웨어 세부 정보의 투명도를 향상시킴으로써 온-프레미스 배포에서의 마이그레이션 계획을 용이하게 함.
- [예약 인스턴스 가격](reserved-capacity-overview.md)은 vCore 구매 모델에만 제공됩니다. 

## <a name="service-tiers"></a>서비스 계층

vCore 구매 모델의 서비스 계층 옵션에는 범용, 중요 비즈니스용 및 하이퍼스케일이 포함됩니다. 서비스 계층은 일반적으로 가용성 및 재해 복구와 관련된 스토리지 아키텍처, 공간 및 I/O 제한 및 비즈니스 연속성 옵션을 정의합니다.

|**사용 사례**|**범용**|**중요 비즈니스용**|**하이퍼스케일**|
|---|---|---|---|
|적합한 대상|대부분의 비즈니스 워크로드. 예산에 맞게 균형 있고 확장 가능한 컴퓨팅 및 스토리지 옵션을 제공합니다. |여러 개의 격리된 복제본을 사용하여 비즈니스 애플리케이션에서 오류에 대한 가장 높은 복원력을 제공하고, 데이터베이스 복제본별로 최고 I/O 성능을 제공합니다.|확장성이 우수한 스토리지 및 읽기 크기 조정 요구 사항이 포함된 대부분의 비즈니스 워크로드.  둘 이상의 격리된 데이터베이스 복제본의 구성을 허용하여 오류에 대한 더 높은 복원력을 제공합니다. |
|스토리지|원격 스토리지를 사용합니다.<br/>**SQL Database 프로비전된 컴퓨팅**:<br/>5GB~4TB<br/>**서버리스 컴퓨팅**:<br/>5GB - 3TB|로컬 SSD 스토리지를 사용합니다.<br/>**SQL Database 프로비전된 컴퓨팅**:<br/>5GB~4TB|필요에 따라 자동으로 증가하는 유연한 스토리지. 최대 100TB의 스토리지를 지원합니다. 로컬 버퍼 풀 캐시 및 로컬 데이터 스토리지에 대한 로컬 SSD 스토리지를 사용합니다. 마지막 장기 데이터 저장소인 Azure 원격 스토리지를 사용합니다. |
|IOPS 및 처리량(근사치)|**SQL Database**: [단일 데이터베이스](resource-limits-vcore-single-databases.md) 및 [탄력적 풀](resource-limits-vcore-elastic-pools.md)에 대한 리소스 제한을 참조하세요.|[단일 데이터베이스](resource-limits-vcore-single-databases.md) 및 [탄력적 풀](resource-limits-vcore-elastic-pools.md)에 대한 리소스 제한을 참조하세요.|하이퍼스케일은 여러 수준에서 캐싱을 사용하는 다중 계층 아키텍처입니다. 효과적인 IOPS 및 처리량은 워크로드에 따라 달라집니다.|
|가용성|복제본 1개, 읽기 확장 복제본 없음, <br/>영역 중복 HA(고가용성)(미리 보기)|3개 복제본, 1개 [읽기 크기 조정 복제본](read-scale-out.md),<br/>영역 중복 HA(고가용성)|1개 읽기/쓰기 복제본 및 0-4개 [읽기 크기 조정 복제본](read-scale-out.md)|
|Backup|지역 중복, 영역 중복\* 또는 로컬 중복\* 백업 스토리지 중에서 선택, 1~35일 보존(기본값 7일)|지역 중복, 영역 중복\* 또는 로컬 중복\* 백업 스토리지 중에서 선택, 1~35일 보존(기본값 7일)|지역 중복, 영역 중복 또는 로컬 중복 \* \* \* \* 백업 스토리지, 7일 보존 중에서 선택할 수 있습니다.<p>Azure 원격 스토리지의 스냅샷 기반 백업. 복원은 빠른 복구를 위해 스냅샷을 사용합니다. 백업은 즉시 수행되며 컴퓨팅 I/O 성능에 영향을 주지 않습니다. 복원은 빠르며 데이터 크기 작업이 아닙니다(시간이 아닌 몇 분이 소요).|
|메모리 내|지원되지 않음|지원 여부|일부 지원. 메모리 최적화 테이블 형식, 테이블 변수 및 고유하게 컴파일된 모듈이 지원됩니다.|
|||

\* 미리 보기로 제공됩니다.

\*\* 미리 보기 상태, 새 하이퍼스케일 데이터베이스 전용

### <a name="choosing-a-service-tier"></a>서비스 계층 선택

특정 워크로드에 대한 서비스 계층을 선택하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [범용 서비스 계층을 선택하는 경우](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [중요 비즈니스용 서비스 계층을 선택하는 경우](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [하이퍼스케일 서비스 계층을 선택하는 경우](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>컴퓨팅 계층

vCore 모델의 컴퓨팅 계층 옵션에는 프로비전된 컴퓨팅 계층 및 서버리스 컴퓨팅 계층이 포함됩니다.


### <a name="provisioned-compute"></a>프로비저닝된 컴퓨팅

프로비전된 컴퓨팅 계층은 워크로드 활동에 관계 없이 지속적으로 프로비전되는 특정 분량의 컴퓨팅 리소스를 제공하고, 시간당 고정된 가격으로 프로비전된 컴퓨팅 양에 대한 요금을 청구합니다.


### <a name="serverless-compute"></a>서버리스 컴퓨팅

[서버리스 컴퓨팅 계층](serverless-tier-overview.md)은 워크로드 활동을 기반으로 컴퓨팅 리소스를 자동으로 스케일링하고 초당 사용된 컴퓨팅 양에 대한 요금을 청구합니다.



## <a name="hardware-generations"></a>하드웨어 세대

vCore 모델의 하드웨어 세대 옵션에는 Gen 4/5, M 시리즈, Fsv2 시리즈 및 DC 시리즈가 포함됩니다. 하드웨어 세대는 일반적으로 컴퓨팅 및 메모리 제한과 워크로드 성능에 영향을 주는 기타 특성을 정의합니다.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5 하드웨어는 균형 있는 컴퓨팅 및 메모리 리소스를 제공하며, Fsv2 시리즈 또는 M 시리즈에서 제공되는 것과 같은 더 높은 메모리, 더 높은 vCore 또는 더 빠른 단일 vCore 요구 사항이 없는 대부분의 데이터베이스 워크로드에 적합합니다.

Gen4/Gen5를 사용할 수 있는 지역의 경우 [Gen4/Gen5 가용성](#gen4gen5-1)을 참조하세요.

### <a name="fsv2-series"></a>Fsv2 시리즈

- Fsv2 시리즈는 CPU 요구량이 가장 많은 워크로드에 대해 낮은 CPU 지연 시간과 높은 클럭 속도를 제공하는 컴퓨팅 최적화 하드웨어 옵션입니다.
- 워크로드에 따라 Fsv2 시리즈는 Gen5보다 vCore당 더 많은 CPU 성능을 제공할 수 있으며, 72 vCore 크기는 Gen5에서 80 vCore보다 적은 비용으로 더 많은 CPU 성능을 제공할 수 있습니다. 
- Fsv2는 다른 하드웨어보다 vCore당 메모리와 tempdb를 더 적게 제공하므로 이러한 한도에 영향을 주는 워크로드는 Gen5 또는 M 시리즈를 대신 고려하는 것이 좋습니다.  

Fsv2 시리즈는 범용 계층에서만 지원됩니다. Fsv2 시리즈를 사용할 수 있는 지역의 경우 [Fsv2 시리즈 가용성](#fsv2-series-1)을 참조하세요.

### <a name="m-series"></a>M 시리즈

- M 시리즈는 Gen5에서 제공하는 것보다 더 많은 메모리와 더 높은 컴퓨팅 한도를 필요로 하는 워크로드를 위한 메모리 최적화 하드웨어 옵션입니다.
- M 시리즈는 vCore당 29GB 및 최대 128 vCore를 제공하여 5세대 대비 메모리 제한을 8배에서 4TB 가까이 증가시킵니다.

M 시리즈는 중요 비즈니스용 계층에서만 지원되며 영역 중복성은 지원하지 않습니다.  M 시리즈를 사용할 수 있는 지역의 경우 [M 시리즈 가용성](#m-series-1)을 참조하세요.

#### <a name="azure-offer-types-supported-by-m-series"></a>M 시리즈에서 지원되는 Azure 제품 유형

M 시리즈에 액세스하려면 구독은 종량제 또는 EA(기업계약)를 포함한 유료 제품 유형이어야 합니다.  M 시리즈에서 지원되는 Azure 제품 유형의 전체 목록은 [지출 한도가 없는 현재 제품](https://azure.microsoft.com/support/legal/offer-details)을 참조하세요.

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC 시리즈

- DC 시리즈 하드웨어는 Intel SGX(Software Guard Extensions) 기술을 사용합니다.
- DC 시리즈는 다른 하드웨어 구성에서 지원되지 않는 [보안 Enclave를 사용한 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)에 필요합니다.
- DC 시리즈는 중요한 데이터를 처리하고 보안 Enclave를 사용한 Always Encrypted에서 제공하는 기밀 쿼리 처리 기능이 요구되는 작업을 위해 설계되었습니다.
- DC 시리즈 하드웨어는 균형 있는 컴퓨팅 및 메모리 리소스를 제공합니다.

DC 시리즈는 프로비전된 컴퓨팅에 대해서만 지원되고(서버리스는 지원되지 않음) 영역 중복성을 지원하지 않습니다. DC 시리즈를 사용할 수 있는 지역의 경우 [DC 시리즈 가용성](#dc-series-1)을 참조하세요.

#### <a name="azure-offer-types-supported-by-dc-series"></a>DC 시리즈에서 지원되는 Azure 제품 유형

DC 시리즈에 액세스하려면 구독은 종량제 또는 EA(기업계약)를 포함한 유료 제품 유형이어야 합니다.  DC 시리즈에서 지원되는 Azure 제품 유형의 전체 목록은 [지출 한도가 없는 현재 제품](https://azure.microsoft.com/support/legal/offer-details)을 참조하세요.

### <a name="compute-and-memory-specifications"></a>컴퓨팅, 메모리 사양


|하드웨어 세대  |컴퓨팅  |메모리  |
|:---------|:---------|:---------|
|Gen4     |- Intel&reg; E5-2673 v3(Haswell) 2.4GHz 프로세서<br>- 최대 24 vCore 프로비전(1 vCore = 1 실제 코어)  |- vCore당 7GB<br>- 최대 168GB 프로비전|
|5세대     |**프로비저닝된 컴퓨팅**<br>- Intel&reg; E5-2673 v4(Broadwell) 2.3GHz, Intel&reg; SP-8160(Skylake)\* 및 Intel&reg; 8272CL(Cascade Lake) 2.5GHz\* 프로세서<br>- 최대 80 vCore 프로비전(1 vCore = 1 하이퍼 스레드)<br><br>**서버리스 컴퓨팅**<br>- Intel&reg; E5-2673 v4(Broadwell) 2.3GHz 및 Intel&reg; SP-8160(Skylake)* 프로세서<br>- 40 vCore 자동 스케일링(1 vCore = 1 하이퍼 스레드)|**프로비저닝된 컴퓨팅**<br>- vCore당 5.1GB<br>- 최대 408GB 프로비전<br><br>**서버리스 컴퓨팅**<br>- vCore당 최대 24GB까지 자동 스케일 업<br>- 최대 120GB까지 자동 스케일 업|
|Fsv2 시리즈     |- Intel&reg; 8168(Skylake) 프로세서<br>- 모든 코어 터보 클럭 속도가 3.4GHz이고 최대 싱글 코어 터보 클럭 속도는 3.7GHz입니다.<br>- 최대 72 vCore 프로비전(1 vCore = 1 하이퍼 스레드)|- vCore당 1.9GB<br>- 최대 136GB 프로비전|
|M 시리즈     |- Intel&reg; E7-8890 v3 2.5GHz 및 Intel&reg; 8280M 2.7GHz(Cascade Lake) 프로세서<br>- 최대 128 vCore 프로비전(1 vCore = 1 하이퍼 스레드)|- vCore당 29GB<br>- 최대 3.7TB 프로비전|
|DC 시리즈     | - Intel XEON E-2288G 프로세서<br>- Intel SGX(Software Guard Extension) 기능<br>- 최대 8 vCore 프로비전(1 vCore = 1 실제 코어) | vCore당 4.5GB |

\* [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 동적 관리 뷰에서는 Intel&reg; SP-8160(Skylake) 프로세서를 사용하는 데이터베이스에 대한 하드웨어 세대가 Gen6으로 표시되고, Intel&reg; 8272CL(Cascade Lake)을 사용하는 데이터베이스에 대한 하드웨어 세대는 Gen7로 표시됩니다. 모든 Gen5 데이터베이스에 대한 리소스 제한은 프로세서 유형(Broadwell, Skylake 또는 Cascade Lake)에 관계 없이 동일합니다.

리소스 제한에 대한 자세한 내용은 [단일 데이터베이스에 대한 리소스 제한(vCore)](resource-limits-vcore-single-databases.md) 또는 [탄력적 풀에 대한 리소스 제한(vCore)](resource-limits-vcore-elastic-pools.md)을 참조하세요.

### <a name="selecting-a-hardware-generation"></a>하드웨어 세대 선택

Azure Portal에서 생성 시 SQL Database의 데이터베이스 또는 풀에 대한 하드웨어 세대를 선택하거나 기존 데이터베이스 또는 풀의 하드웨어 세대를 변경할 수 있습니다.

**SQL Database 또는 풀을 만들 때 하드웨어 세대를 선택하려면**

자세한 내용은 [SQL Database 만들기](single-database-create-quickstart.md)를 참조하세요.

**기본** 탭의 **컴퓨팅 + 스토리지** 섹션에서 **데이터베이스 구성** 링크를 선택한 다음, **구성 변경** 링크를 선택합니다.

:::image type="content" source="./media/service-tiers-vcore/configure-sql-database.png" alt-text="SQL Database 구성" loc-scope="azure-portal":::

원하는 하드웨어 세대를 선택합니다.

:::image type="content" source="./media/service-tiers-vcore/select-hardware.png" alt-text="SQL Database에 대한 하드웨어 선택" loc-scope="azure-portal":::

**기존 SQL Database 또는 풀의 하드웨어 세대를 변경하려면**

데이터베이스의 경우 개요 페이지에서 **가격 책정 계층** 링크를 선택합니다.

:::image type="content" source="./media/service-tiers-vcore/change-hardware.png" alt-text="SQL Database에 대한 하드웨어 변경" loc-scope="azure-portal":::

풀의 경우 개요 페이지에서 **구성** 을 선택합니다.

단계에 따라 구성을 변경하고 이전 단계에서 설명한 대로 하드웨어 세대를 선택합니다.

### <a name="hardware-availability"></a>고가용성

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> Gen4/Gen5

Gen4 하드웨어는 [단계적으로 중단](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)되며 더이상 새로운 배포에 사용할 수 없습니다. 모든 새 데이터베이스는 이후 하드웨어 세대에 배포해야 합니다.

Gen5는 전세계 모든 공용 지역에서 사용할 수 있습니다.

#### <a name="fsv2-series"></a>Fsv2 시리즈

Fsv2 시리즈는 오스트레일리아 중부, 오스트레일리아 중부 2, 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 동아시아, 미국 동부, 프랑스 중부, 인도 중부, 한국 중부, 한국 남부, 북유럽, 남아프리카 공화국 북부, 동남 아시아, 영국 남부, 영국 서부, 서유럽, 미국 서부 2 지역에서 사용할 수 있습니다.

#### <a name="m-series"></a>M 시리즈

M 시리즈는 미국 동부, 북유럽, 서유럽, 미국 서부 2 지역에서 사용할 수 있습니다.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>DC 시리즈

DC 시리즈는 캐나다 중부, 캐나다 동부, 미국 동부, 북유럽, 영국 남부, 서유럽, 미국 서부 지역에서 사용할 수 있습니다.

현재 지원되지 않는 지역에서 DC 시리즈가 필요한 경우 [지원 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)합니다. **기본 사항** 페이지에서 다음 값을 제공합니다.

1. **문제 유형** 에서 **기술** 을 선택합니다.
1. **서비스 유형** 에서 **SQL Database** 를 선택합니다.
1. **문제 유형** 에서 **보안, 프라이빗 및 규정 준수** 를 선택합니다.
1. **문제 하위 유형** 에서 **Always Encrypted** 를 선택합니다.

:::image type="content" source="./media/service-tiers-vcore/request-dc-series.png" alt-text="새 지역에서 DC 시리즈 요청" loc-scope="azure-portal":::

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Portal을 사용하여 SQL Database 만들기](single-database-create-quickstart.md)를 참조하세요.
- 가격 책정에 대한 자세한 내용은 [Azure SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/)를 참조하세요.
- 사용 가능한 특정 컴퓨팅 및 저장소 크기에 대한 자세한 내용은 다음을 참조하세요.
    - [Azure SQL Database용 vCore 기반 리소스 제한](resource-limits-vcore-single-databases.md)
    - [풀링된 Azure SQL Database를 위한 vCore 기반 리소스 제한](resource-limits-vcore-elastic-pools.md)
