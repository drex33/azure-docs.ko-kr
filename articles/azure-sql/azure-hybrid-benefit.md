---
title: Azure 하이브리드 혜택
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database 및 SQL Managed Instance 할인에 기존 SQL Server 라이선스를 사용합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=4
ms.topic: conceptual
author: LitKnd
ms.author: kendralittle
ms.reviewer: sashan, moslake
ms.date: 11/09/2021
ms.openlocfilehash: 96d426b69dc15afc02c555c0e49b51b79002be10
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133539462"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure 하이브리드 혜택 - Azure SQL Database 및 SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 사용 하면 Azure SQL Database 및 Azure SQL Managed Instance에서 할인 요금으로 기존 라이선스를 교환할 수 있습니다. Azure에서 소프트웨어 보증 사용 SQL Server 라이선스를 사용 하 여 SQL Database 및 SQL Managed Instance에서 최대 30%까지 절약할 수 있습니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 페이지에 절감액을 확인하실 수 있는 계산기가 있습니다.  

Azure 하이브리드 혜택으로 변경하는 경우 가동 중지 시간이 필요하지 않습니다.

## <a name="overview"></a>개요

![vCore 가격 책정 구조](./media/azure-hybrid-benefit/pricing.png)

Azure 하이브리드 혜택를 사용 하면 SQL Server 데이터베이스 엔진 자체 (기본 계산 가격 책정)에 기존 SQL Server 라이선스를 사용 하 여 기본 Azure 인프라에 대해서만 비용을 지불 합니다. Azure 하이브리드 혜택 사용 하지 않는 경우 기본 인프라와 SQL Server 라이선스 (라이선스 포함 가격) 모두에 대 한 비용을 지불 합니다.

Azure SQL Database에 대 한 Azure 하이브리드 혜택은 [vcore 기반 구매 모델](database/service-tiers-vcore.md)의 프로 비전 된 계산 계층을 사용 하는 경우에만 사용할 수 있습니다. [DTU 기반 구매 모델](database/service-tiers-dtu.md) 또는 [서버 리스 계산 계층](database/serverless-tier-overview.md)에는 Azure 하이브리드 혜택 적용 되지 않습니다.

## <a name="enable-azure-hybrid-benefit"></a>Azure 하이브리드 혜택 사용 

### <a name="azure-sql-database"></a>Azure SQL Database

선택한 Azure Portal 또는 API를 사용 하 여 Azure SQL Database에 대 한 라이선스 모델을 선택 하거나 변경할 수 있습니다.

VCore 기반 구매 모델 및 Azure SQL Database 프로 비전 된 계산 계층을 선택 하는 경우에만 Azure 하이브리드 라이선스 모델을 적용할 수 있습니다. DTU 기반 구매 모델 또는 서버 리스 계산 계층의 서비스 계층에는 Azure 하이브리드 혜택 사용할 수 없습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- 새 데이터베이스의 경우 만드는 동안 **기본 사항** 탭에서 **데이터베이스 구성** 을 선택 하 고 **비용** 을 절감 하는 옵션을 선택 합니다.
- 기존 데이터베이스의 경우 **설정** 메뉴에서 **Compute + storage** 를 선택 하 고 **비용** 을 절감 하는 옵션을 선택 합니다.

Azure Portal에 **비용 절약** 옵션이 표시 되지 않으면 vcore 기반 구매 모델 및 프로 비전 된 계산 계층을 사용 하 여 서비스 계층을 선택 했는지 확인 합니다.
#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 라이선스 형식을 설정하거나 업데이트하려면

- [AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) 를 사용 하는-LicenseType 매개 변수
- -LicenseType 매개 변수를 사용 하 여 [AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- [az sql db create](/cli/azure/sql/db#az_sql_db_create) --라이선스 형식 매개 변수

#### <a name="rest-api"></a>[REST API](#tab/rest)

REST API를 사용하여 라이선스 형식을 설정하거나 업데이트하려면

- LicenseType 매개 변수를 사용 하 여 [만들거나 업데이트](/rest/api/sql/databases/createorupdate) 합니다.
- LicenseType 매개 변수를 사용 하 여 [업데이트](/rest/api/sql/databases/update)

---

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

원하는 Azure Portal 또는 API를 사용 하 여 Azure SQL Managed Instance의 라이선스 모델을 선택 하거나 변경할 수 있습니다.
#### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- 새 관리 되는 인스턴스의 경우에는 **기본** 설정 탭에서 **Managed Instance 구성** 을 선택 하 고 **Azure 하이브리드 혜택** 에 대 한 옵션을 선택 합니다.
- 기존 관리 되는 인스턴스의 경우 **설정** 메뉴에서 **Compute + storage** 를 선택 하 고 **Azure 하이브리드 혜택** 에 대 한 옵션을 선택 합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 라이선스 형식을 설정하거나 업데이트하려면

- [AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) 를 사용 하는-LicenseType 매개 변수
- -LicenseType 매개 변수를 사용 하 여 [AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 라이선스 유형을 설정 하거나 업데이트 하려면 다음을 수행 합니다.

- --라이선스 형식 매개 변수를 사용 하 여 [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)
- --라이선스 형식 매개 변수를 사용 하 여 [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)

#### <a name="rest-api"></a>[REST API](#tab/rest)

REST API를 사용하여 라이선스 형식을 설정하거나 업데이트하려면

- LicenseType 매개 변수를 사용 하 여 [만들거나 업데이트](/rest/api/sql/managedinstances/createorupdate) 합니다.
- LicenseType 매개 변수를 사용 하 여 [업데이트](/rest/api/sql/managedinstances/update)

---
## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택에 이중 사용 권한이 있나요?

마이그레이션이 원활하게 실행되도록 하려면 180일간의 이중 사용 권한 라이선스가 있어야 합니다. 180 일이 지난 후에는 Azure에서 SQL Server 라이선스를 사용할 수 있습니다. 온-프레미스와 Azure에는 더 이상 이중 사용 권한이 없습니다.

### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server에 대한 Azure 하이브리드 혜택은 라이선스 이동과 어떻게 다른가요?

Software Assurance를 통해 SQL Server 고객들에게 라이선스 이동 혜택을 제공합니다. 라이선스 이동성은 파트너의 공유 서버에 대 한 라이선스 재할당을 허용 합니다. 이 혜택은 Azure IaaS 및 AWS EC2에서 사용할 수 있습니다.

SQL Server에 대한 Azure 하이브리드 혜택은 두 가지 주요 영역에서 라이선스 이동과 다릅니다.

- 높은 수준으로 가상화된 워크로드를 Azure로 이동할 수 있는 경제적 이점을 제공합니다. SQL Server Enterprise Edition 고객은 높은 수준으로 가상화된 애플리케이션을 위해 온-프레미스에서 소유하는 모든 코어에 대해 Azure의 범용 SKU에서 4개의 코어를 얻을 수 있습니다. 라이선스 이동은 가상화된 워크로드를 클라우드로 이동하는 데 특별한 비용상의 혜택을 허용하지 않습니다.
- Azure SQL Managed Instance에서 SQL Server와 항상 호환되는 PaaS 대상을 제공합니다.

### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택의 특정 권한은 무엇인가요?

SQL Server에 대한 Azure 하이브리드 혜택과 관련하여 SQL Database 및 SQL Managed Instance 고객이 보유하는 권한은 다음과 같습니다.

|라이선스 공간|SQL Server에 대한 Azure 하이브리드 혜택의 내용|
|---|---|
|SA가 있는 SQL Server Enterprise Edition 핵심 고객|<li>하이퍼스케일, 범용 또는 중요 비즈니스 SKU 중 하나에 대한 기본 요금을 지불할 수 있음</li><br><li>1 코어 온-프레미스 = 하이퍼 확장 SKU에서 4 개 vCores</li><br><li>단일 코어 온-프레미스 = 범용 SKU의 4 개 vCores</li><br><li>단일 코어 온-프레미스 = 중요 비즈니스용 SKU에서 1 개 vCore</li>|
|SA가 있는 SQL Server Standard Edition 핵심 고객|<li>하이퍼스케일, 범용 또는 중요 비즈니스 SKU 중 하나에 대한 기본 요금을 지불할 수 있음</li><br><li>단일 코어 온-프레미스 = 단일 vCore (하이퍼 확장 SKU)</li><br><li>단일 코어 온-프레미스 = 범용 SKU의 단일 vCore</li><br><li>온-프레미스의 4 개 코어 = 중요 비즈니스용 SKU에서 하나의 vCore</li>|
|||

## <a name="next-steps"></a>다음 단계

- Azure SQL 배포 옵션 선택에 대 한 도움말은 [서비스 비교](azure-sql-iaas-vs-paas-what-is-overview.md)를 참조 하세요.
- SQL Database 및 SQL Managed Instance 기능을 비교 하려면 [SQL Database 기능 및 SQL Managed Instance](database/features-comparison.md)을 참조 하세요.