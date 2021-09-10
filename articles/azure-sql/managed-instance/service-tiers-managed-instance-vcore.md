---
title: vCore 구매 모델
description: vCore 구매 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 스케일링하고, 온-프레미스 성능과 일치시키며, Azure SQL Managed Instance의 가격을 최적화할 수 있습니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 05/18/2021
ms.openlocfilehash: 16782538918c0477b969f95b7730b38221f97f82
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415215"
---
# <a name="azure-sql-managed-instance---compute-hardware-in-the-vcore-service-tier"></a>Azure SQL Managed Instance - vCore 서비스 계층의 컴퓨팅 하드웨어
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)용 vCore 구매 모델을 검토합니다. vCore 및 DTU 구매 모델 중 선택하는 방법에 대한 자세한 내용은 [vCore 및 DTU 구매 모델 중에서 선택](../database/purchasing-models.md)을 참조하세요.

Azure SQL Managed Instance에서 사용하는 가상 코어(vCore) 구매 모델에는 다음과 같은 특징이 있습니다.

- 워크로드에 대한 컴퓨팅 및 메모리 요구 사항을 더 잘 일치시키기 위한 하드웨어 세대 제어.
- [AHB(Azure 하이브리드 혜택)](../azure-hybrid-benefit.md) 및 [RI(예약 인스턴스)](../database/reserved-capacity-overview.md)의 가격 할인.
- 컴퓨팅을 지원하는 하드웨어 세부 정보의 투명도를 향상시킴으로써 온-프레미스 배포에서의 마이그레이션 계획을 용이하게 함.
- [예약 인스턴스 가격](../database/reserved-capacity-overview.md)은 vCore 구매 모델에만 제공됩니다. 

## <a name="service-tiers"></a><a id="compute-tiers"></a>서비스 계층

vCore 구매 모델의 서비스 계층 옵션에는 범용 및 중요 비즈니스용이 포함됩니다. 서비스 계층은 일반적으로 가용성 및 재해 복구와 관련된 스토리지 아키텍처, 공간 및 I/O 제한 및 비즈니스 연속성 옵션을 정의합니다.

|**사용 사례**|**범용**|**중요 비즈니스용**|
|---|---|---|
|적합한 대상|대부분의 비즈니스 워크로드. 예산에 맞게 균형 있고 확장 가능한 컴퓨팅 및 스토리지 옵션을 제공합니다. |여러 개의 격리된 복제본을 사용하여 비즈니스 애플리케이션에서 오류에 대한 가장 높은 복원력을 제공하고 최고의 I/O 성능을 제공합니다.|
|스토리지|원격 스토리지를 사용합니다. 32GB~8TB |로컬 SSD 스토리지를 사용합니다. 32GB~4TB |
|IOPS 및 처리량(근사치)|[Azure SQL Managed Instance 리소스 제한 개요](../managed-instance/resource-limits.md#service-tier-characteristics)를 참조하세요.|[Azure SQL Managed Instance 리소스 제한 개요](../managed-instance/resource-limits.md#service-tier-characteristics)를 참조하세요.|
|가용성|1개 복제본, 읽기 크기 조정 복제본 없음|3개 복제본, 1개 [읽기 크기 조정 복제본](../database/read-scale-out.md),<br/>영역 중복 HA(고가용성)|
|Backup|[RA-GRS(읽기 액세스 지역 중복 스토리지)](../../storage/common/geo-redundant-design.md), 1-35일(기본적으로 7일)|[RA-GRS](../../storage/common/geo-redundant-design.md), 1-35일(기본값: 7일)|
|메모리 내|지원되지 않음|지원 여부|
||||

### <a name="choosing-a-service-tier"></a>서비스 계층 선택

특정 워크로드에 대한 서비스 계층을 선택하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [범용 서비스 계층을 선택하는 경우](../database/service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [중요 비즈니스용 서비스 계층을 선택하는 경우](../database/service-tier-business-critical.md#when-to-choose-this-service-tier)

## <a name="compute"></a>Compute

SQL Managed Instance 컴퓨팅은 워크로드 활동과 관계없이 지속적으로 프로비전되는 특정 분량의 컴퓨팅 리소스를 제공하고, 시간당 고정된 가격으로 프로비전된 컴퓨팅 양에 대한 요금을 청구합니다.

## <a name="hardware-generations"></a>하드웨어 세대

vCore 모델의 하드웨어 생성 옵션에는 Gen 5 하드웨어 시리즈가 포함됩니다. 하드웨어 세대는 일반적으로 컴퓨팅 및 메모리 제한과 워크로드 성능에 영향을 주는 기타 특성을 정의합니다.

### <a name="compute-and-memory-specifications"></a>컴퓨팅, 메모리 사양

|하드웨어 세대  |컴퓨팅  |메모리  |
|:---------|:---------|:---------|
|Gen4     |- Intel&reg; E5-2673 v3(Haswell) 2.4GHz 프로세서<br>- 최대 24 vCore 프로비전(1 vCore = 1 실제 코어)  |- vCore당 7GB<br>- 최대 168GB 프로비전|
|5세대     |- Intel&reg; E5-2673 v4(Broadwell) 2.3GHz, Intel&reg; SP-8160(Skylake)\* 및 Intel&reg; 8272CL(Cascade Lake) 2.5GHz\* 프로세서<br>- 최대 80 vCore 프로비전(1 vCore = 1 하이퍼 스레드)|vCore당 5.1GB<br>- 최대 408GB 프로비전|

\* [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 동적 관리 뷰에서는 Intel&reg; SP-8160(Skylake) 프로세서를 사용하는 데이터베이스에 대한 하드웨어 세대가 Gen6으로 표시되고, Intel&reg; 8272CL(Cascade Lake)을 사용하는 인스턴스에 대한 하드웨어 세대는 Gen7로 표시됩니다. 모든 Gen5 인스턴스에 대한 리소스 제한은 프로세서 유형(Broadwell, Skylake 또는 Cascade Lake)에 관계없이 동일합니다.

### <a name="selecting-a-hardware-generation"></a>하드웨어 세대 선택

Azure Portal에서 생성 시 하드웨어 세대를 선택하거나 기존 SQL Managed Instance의 하드웨어 세대를 변경할 수 있습니다.

**SQL Managed Instance를 만들 때 하드웨어 세대를 선택하려면**

자세한 내용은 [SQL Managed Instance 만들기](../managed-instance/instance-create-quickstart.md)를 참조하세요.

**기본** 탭의 **컴퓨팅 + 스토리지** 섹션에서 **데이터베이스 구성** 링크를 선택한 다음, 원하는 하드웨어 세대를 선택합니다.

:::image type="content" source="../database/media/service-tiers-vcore/configure-managed-instance.png" alt-text="SQL Managed Instance 구성"  loc-scope="azure-portal":::
  
**기존 SQL Managed Instance의 하드웨어 세대를 변경하려면**

#### <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

SQL Managed Instance 페이지에서 설정 섹션 아래에 있는 **가격 책정 계층** 링크를 선택합니다.

:::image type="content" source="../database/media/service-tiers-vcore/change-managed-instance-hardware.png" alt-text="SQL Managed Instance 하드웨어 변경"  loc-scope="azure-portal":::

가격 책정 계층 페이지에서 이전 단계에 설명된 대로 하드웨어 세대를 변경할 수 있습니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 스크립트를 사용합니다.

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

자세한 내용은 [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) 명령을 확인하세요.

#### <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 CLI 명령을 사용합니다.

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

자세한 내용은 [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) 명령을 확인하세요.

---

### <a name="hardware-availability"></a>고가용성

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> Gen4/Gen5

Gen4 하드웨어는 [단계적으로 중단](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)되며 더이상 새로운 배포에 사용할 수 없습니다. 모든 새 인스턴스를 Gen5 하드웨어에 배포해야 합니다.

Gen5는 전세계 모든 공용 지역에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Portal을 사용하여 SQL Managed Instance 만들기](instance-create-quickstart.md)를 참조하세요.
- 가격 정보는 다음을 참조하세요. 
    - [Azure SQL Managed Instance 단일 인스턴스 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Azure SQL Managed Instance 풀 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
- 범용 및 중요 비즈니스용 서비스 계층에서 사용할 수 있는 특정 컴퓨팅 및 스토리지 크기에 대한 세부 정보는 [Azure SQL Managed Instance에 대한 vCore 기반 리소스 제한](resource-limits.md)을 참조하세요.
