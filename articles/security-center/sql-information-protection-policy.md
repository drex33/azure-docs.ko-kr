---
title: 클라우드용 Microsoft Defender의 SQL 정보 보호 정책
description: Microsoft Defender for Cloud에서 정보 보호 정책을 사용자 지정하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 83db40485e425618637b8dea75a7e3cae254637c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102797"
---
# <a name="sql-information-protection-policy-in-microsoft-defender-for-cloud"></a>클라우드용 Microsoft Defender의 SQL 정보 보호 정책

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

SQL 정보 보호의 [데이터 검색 및 분류 메커니즘](../azure-sql/database/data-discovery-and-classification-overview.md)은 데이터베이스의 중요한 데이터를 검색, 분류, 레이블 지정, 보고하기 위한 고급 기능을 제공합니다. [Azure SQL Database,](../azure-sql/database/sql-database-paas-overview.md)[Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md), [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 기본 제공됩니다.

분류 메커니즘은 다음 두 요소를 기반으로 합니다.

- **레이블** - 열에 저장된 *데이터의 민감도 수준* 을 정의하는 데 사용되는 주 분류 특성입니다. 
- **정보 형식** – 열에 저장된 *데이터의 형식* 에 추가 세분성을 제공합니다.

Defender for Cloud 내의 정보 보호 정책 옵션은 분류 엔진의 기본값 역할을 하는 미리 정의된 레이블 및 정보 유형 집합을 제공합니다. 아래 설명된 대로 조직의 필요에 따라 정책을 사용자 지정할 수 있습니다.

:::image type="content" source="./media/sql-information-protection-policy/sql-information-protection-policy-page.png" alt-text="SQL 정보 보호 정책을 보여주는 페이지.":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>SQL 정보 보호 정책에 액세스하려면 어떻게 하나요?

다음 세 가지 방법으로 정보 보호 정책에 액세스할 수 있습니다.

- **(권장)** Defender for Cloud의 가격 책정 및 설정 페이지에서
- 보안 권장 사항의 "SQL 데이터베이스의 중요한 데이터를 분류해야 합니다"
- Azure SQL DB 데이터 검색 페이지

각 내용은 아래 관련 탭에 표시됩니다.



### <a name="from-defender-for-clouds-settings"></a>[**Defender for Cloud 설정에서**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-defender-for-clouds-pricing-and-settings-page"></a>Defender for Cloud의 가격 책정 및 설정 페이지에서 정책에 액세스 <a name="sqlip-tenant"></a>

Defender for Cloud의 **가격 책정 및 설정** 페이지에서 SQL 정보 **보호를** 선택합니다.

> [!NOTE]
> 이 옵션은 테넌트 수준 권한이 있는 사용자에게만 표시됩니다. [자신에게 테넌트 전체 권한을 부여합니다](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).

:::image type="content" source="./media/sql-information-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Microsoft Defender for Cloud의 가격 책정 및 설정 페이지에서 SQL Information Protection 정책에 액세스합니다.":::



### <a name="from-defender-for-clouds-recommendation"></a>[**Defender for Cloud 권장 사항에서**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-defender-for-cloud-recommendation"></a>Defender for Cloud 권장 사항에서 정책에 액세스 <a name="sqlip-db"></a>

Defender for Cloud의 권장 사항인 "SQL 데이터베이스의 중요한 데이터를 분류해야 합니다."를 사용하여 데이터베이스에 대한 데이터 검색 및 분류 페이지를 봅니다. 또한 분류하는 것이 권장되는 정보를 포함하는 것으로 검색된 열도 표시됩니다.

1. Defender for **Cloud의 권장 사항** 페이지에서 **SQL 데이터베이스의 중요한 데이터를 분류해야 한다는** 권장 사항을 검색합니다.

    :::image type="content" source="./media/sql-information-protection-policy/sql-sensitive-data-recommendation.png" alt-text="SQL 정보 보호 정책에 대한 액세스를 제공하는 권장 사항 찾기.":::

1. 권장 사항 세부 정보 페이지의 **정상** 또는 **비정상** 탭에서 데이터베이스를 선택합니다.

1. **데이터 검색 및 분류** 페이지가 열립니다. **구성** 을 선택합니다.

    :::image type="content" source="./media/sql-information-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Microsoft Defender for Cloud의 관련 권장 사항에서 SQL 정보 보호 정책 열기":::



### <a name="from-azure-sql"></a>[**Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Azure SQL에서 정책에 액세스합니다<a name="sqlip-azuresql"></a>

1. Azure Portal에서 Azure SQL을 엽니다.

    :::image type="content" source="./media/sql-information-protection-policy/open-azure-sql.png" alt-text="Azure Portal에서 Azure SQL 열기.":::

1. 데이터베이스를 선택합니다.

1. 메뉴의 **보안** 영역에서 **데이터 검색 및 분류** 페이지 (1)을 열고 **구성** (2)를 선택합니다.

    :::image type="content" source="./media/sql-information-protection-policy/access-policy-from-azure-sql.png" alt-text="Azure SQL에서 SQL 정보 보호 정책 열기.":::

--- 


## <a name="customize-your-information-types"></a>정보 유형 사용자 지정

정보 유형을 관리하고 사용자 지정하려면 다음을 수행합니다.

1. **정보 유형 관리** 를 선택합니다.

    :::image type="content" source="./media/sql-information-protection-policy/manage-types.png" alt-text="정보 보호 정책에 대한 정보 형식을 관리합니다.":::

1. 새 형식을 추가하려면 **정보 유형 생성하기** 를 선택합니다. 정보 형식에 대한 이름, 설명, 검색 패턴 문자열을 구성할 수 있습니다. 필요에 따라 검색 패턴 문자열은 열의 메타데이터에 따라 와일드 카드 문자('%' 문자 사용)와 함께 키워드를 사용할 수 있습니다. 이 항목은 자동화된 검색 엔진이 데이터베이스에서 중요한 데이터를 식별하는 데 사용합니다.
 
    :::image type="content" source="./media/sql-information-protection-policy/configure-new-type.png" alt-text="정보 보호 정책에 대한 새 정보 유형을 구성합니다.":::

1. 검색 패턴 문자열을 추가하거나, 기존 문자열 중 일부를 사용하지 않도록 하거나, 설명을 변경하여 기본 제공 형식을 수정할 수도 있습니다. 

    > [!TIP]
    > 기본 제공 형식을 삭제하거나 이름을 변경할 수 없습니다. 

1. **정보 형식** 은 검색 순위 오름차순으로 나열됩니다. 즉, 목록의 위에 있는 형식을 먼저 일치시킵니다. 정보 형식 간의 순위를 변경하려면 형식을 테이블의 오른쪽으로 끌어오거나 **위로 이동** 및 **아래로 이동** 단추를 사용하여 순서를 변경합니다. 

1. 완료되면 **확인** 을 선택합니다.

1. 정보 형식 관리를 완료하면 특정 레이블에 대한 **구성** 을 클릭하고, 적절하게 정보 형식을 추가하거나 삭제하여 적절한 레이블과 관련 형식을 연결하도록 합니다.

1. 변경 내용을 적용하려면, 기본 **레이블** 페이지에서 **저장** 을 선택합니다.
 

## <a name="exporting-and-importing-a-policy"></a>정책 내보내기 및 가져오기

정의된 레이블 및 정보 형식이 있는 JSON 파일을 다운로드하고 원하는 편집기에서 파일을 편집한 다음 업데이트된 파일을 가져올 수 있습니다. 

:::image type="content" source="./media/sql-information-protection-policy/export-import.png" alt-text="정보 보호 정책 내보내기 및 가져오기.":::

> [!NOTE]
> 정책 파일을 가져오려면 테넌트 수준 권한이 필요합니다. 


## <a name="permissions"></a>사용 권한

Azure 테넌트에 대한 정보 보호 정책을 사용자 지정하려면, 테넌트의 루트 관리 그룹에 대해 다음을 수행해야 합니다.
  - Microsoft.Security/informationProtectionPolicies/read
  - Microsoft.Security/informationProtectionPolicies/write 

[테넌트 전체 표시 권한 부여 및 요청](tenant-wide-permissions-management.md)에서 자세히 알아보세요.

## <a name="manage-sql-information-protection-using-azure-powershell"></a>Azure PowerShell을 사용하여 SQL 정보 보호 관리

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): 실행 중인 테넌트 SQL 정보 보호 정책을 검색합니다.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): 실행 중인 테넌트 SQL 정보 보호 정책을 설정합니다.
 

## <a name="next-steps"></a>다음 단계
 
이 문서에서는 Microsoft Defender for Cloud에서 정보 보호 정책을 정의하는 방법을 배웠습니다. SQL Information Protection을 사용하여 SQL 데이터베이스에서 중요한 데이터를 분류하고 보호하는 방법에 대해 자세히 알아보려면 [Azure SQL Database 데이터 검색 및 분류](../azure-sql/database/data-discovery-and-classification-overview.md)를 참조하세요.

Defender for Cloud의 보안 정책 및 데이터 보안에 대한 자세한 내용은 다음 문서를 참조하세요.
 
- [Microsoft Defender for Cloud에서 보안 정책 설정:](tutorial-security-policy.md)Azure 구독 및 리소스 그룹에 대한 보안 정책을 구성하는 방법을 알아봅니다.
- [Microsoft Defender for Cloud 데이터 보안:](data-security.md)Defender for Cloud에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
