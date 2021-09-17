---
title: Azure SQL 리소스를 사용 하 여 용량 오류 해결
description: Azure SQL Database 또는 Azure SQL Managed Instance 리소스를 배포 하거나 크기를 조정 하려고 할 때 가능한 용량 오류를 해결 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 09/03/2021
ms.custom: references_regions
ms.openlocfilehash: 885be735055eaf65d67466694e65d6cf7fdf00da
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123482213"
---
# <a name="resolve-capacity-errors-with-azure-sql-database-or-azure-sql-managed-instance"></a>Azure SQL Database 또는 Azure SQL를 사용 하 여 용량 오류를 해결 Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

이 문서에서는 Azure SQL Database 또는 Azure SQL Managed Instance 리소스를 배포할 때 용량 오류를 해결 하는 방법에 대해 알아봅니다. 

## <a name="exceeded-quota"></a>할당량 초과 

Azure SQL 리소스를 배포 하려고 할 때 다음 오류가 발생 하는 경우 [할당량 증가를 요청](database/quota-increase-request.md)하세요. 

- `Server quota limit has been reached for this location. Please select a different location with lower server count.`
- `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx.`
- 크기 조정 작업을 수행 하는 동안 다음 오류가 표시 될 수 있습니다.    
  `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx. `. 

## <a name="subscription-access"></a>구독 액세스

구독이 RP (SQL 리소스 공급자)에 등록 되지 않은 경우 구독에는 선택한 지역에 서버를 만들기 위한 액세스 권한이 없을 수 있습니다.  

다음 오류가 표시 되 면 [구독을 SQL RP에 등록](#register-with-sql-rp)하세요.
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`


## <a name="enable-region"></a>영역 사용 

해당 지역이 사용 하도록 설정 되지 않은 경우 선택한 지역에 서버를 만들 수 있는 권한이 구독에 없을 수 있습니다. 이 문제를 해결 하려면 구독에 [특정 지역을 사용 하도록 설정 하는 지원 요청](database/quota-increase-request.md#region) 을 파일에 만듭니다. 

다음 오류가 표시 되는 경우 특정 지역을 사용 하도록 지원 티켓을 제출 합니다. 
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`



## <a name="register-with-sql-rp"></a>SQL RP에 등록

Azure SQL 리소스를 배포 하려면 RP (SQL 리소스 공급자)를 사용 하 여 구독을 등록 합니다. 

Azure Portal, [Azure CLI](/cli/azure/install-azure-cli)또는 [Azure PowerShell](/powershell/azure/install-az-ps)를 사용 하 여 구독을 등록할 수 있습니다. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 구독을 등록 하려면 다음 단계를 수행 합니다. 

1. Azure Portal을 열고 **모든 서비스** 로 이동합니다.
1. **구독** 으로 이동하여 원하는 구독을 선택합니다.
1. **구독** 페이지의 **설정** 에서 **리소스 공급자** 를 선택합니다.
1. 필터에 **sql** 을 입력하여 SQL 관련 리소스 확장을 표시합니다.
1. 원하는 작업에 따라 **Microsoft .sql** 공급자의 **등록**, **다시 등록** 또는 등록 **취소** 를 선택 합니다.

   ![공급자 수정](./media/capacity-errors-troubleshoot/register-with-sql-rp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

[Azure CLI](/cli/azure/install-azure-cli)를 사용 하 여 구독을 등록 하려면 다음 cmdlet을 실행 합니다.

```azurecli-interactive
# Register the SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMac 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Azure PowerShell](/powershell/azure/install-az-ps)를 사용 하 여 구독을 등록 하려면 다음 cmdlet을 실행 합니다. 

```powershell-interactive
# Register the SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.Sql

```

---

## <a name="additional-provisioning-issues"></a>추가 프로 비전 문제

프로 비전 문제가 계속 발생 하는 경우 SQL Database의 지원 항목에서 **지역** 액세스 요청을 열고 Azure SQL Database 또는 Azure SQL Managed Instance에서 사용할 DTU 또는 vcores를 지정 하세요. 

## <a name="azure-program-regions"></a>Azure 프로그램 지역 

azure 프로그램 제공 (Azure Pass, Imagine, 학생용 azure, MPN, BizSpark, BizSpark Plus, Microsoft for/후원 제품, Visual Studio 구독/MSDN)은 제한 된 지역 집합에 액세스할 수 있습니다. 

구독이 Azure 프로그램 제공의 일부이 고 다음 지역에 대 한 액세스를 요청 하려는 경우 대신 대체 지역을 사용해 보세요. 

_오스트레일리아 중부, 오스트레일리아 중부 2, 오스트레일리아 남동쪽, 브라질 남동쪽, 캐나다 동부, 중국 동부, 중국 북부, 중국 북부 2, 프랑스 남부, 독일 북부, 일본 서 부, JIO 인도 중부, JIO 인도 서 부, 대한민국 남부, 노르웨이 서 부, 남아프리카 공화국, 인도, 스위스 서부, 아랍에미리트 중부, 영국 서부, US DoD 중부, US DoD 동부, US Gov 애리조나, US Gov 텍사스,  미국 중부, 인도 서 부._ 

## <a name="next-steps"></a>다음 단계

요청을 제출하면 검토됩니다. 양식에 제공한 정보를 바탕으로 답변을 받게 됩니다.

Azure 한도에 대한 자세한 내용은 [Azure 구독 및 서비스 한도, 할당량, 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
