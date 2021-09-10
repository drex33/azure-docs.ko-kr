---
title: VNet 및 방화벽 뒤의 스토리지 계정에 대한 감사
description: 가상 네트워크 및 방화벽 뒤의 스토리지 계정에서 데이터베이스 이벤트를 쓰도록 감사 구성
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 2431ba1b59ae1f9891affe510e098d41fe166797
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114711866"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>VNet 및 방화벽 뒤의 스토리지 계정에 대한 감사 작성
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


[Azure SQL Database](sql-database-paas-overview.md) 및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에 대한 감사는 가상 네트워크 및 방화벽 뒤의 [Azure Storage 계정](../../storage/common/storage-account-overview.md)에 데이터베이스 이벤트를 쓰도록 지원합니다.

이 문서에서는 이 옵션에 대해 Azure SQL Database 및 Azure Storage 계정을 구성하는 두 가지 방법을 설명합니다. 첫 번째 방법은 Azure Portal을 사용하고, 두 번째 방법은 REST를 사용합니다.

## <a name="background"></a>배경

[Azure Virtual Network(VNet)](../../virtual-network/virtual-networks-overview.md)는 Azure의 프라이빗 네트워크의 기본 구성 요소입니다. VNet을 사용하면 Azure VM(Virtual Machines)과 같은 다양한 형식의 Azure 리소스가 서로, 인터넷 및 특정 온-프레미스 네트워크와 안전하게 통신할 수 있습니다. VNet은 자체 데이터 센터의 기존 네트워크와 유사하지만, 확장, 가용성 및 격리와 같은 Azure 인프라 이점을 추가로 활용할 수 있습니다.

VNet 개념, 모범 사례 등에 대한 자세한 정보는 [Azure Virtual Network란](../../virtual-network/virtual-networks-overview.md)을 참조하세요.

가상 네트워크를 만드는 방법에 대한 자세한 정보는 [빠른 시작: Azure Portal을 사용하여 가상 네트워크 만들기](../../virtual-network/quick-create-portal.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

VNet 또는 방화벽 뒤의 스토리지 계정에 쓰도록 하는 감사의 경우 다음 필수 구성 요소가 필요합니다.

> [!div class="checklist"]
>
> * 범용 v2 스토리지 계정. 범용 v1 또는 Blob Storage 계정이 있는 경우 [범용 v2 스토리지 계정으로 업그레이드](../../storage/common/storage-account-upgrade.md)합니다. 자세한 정보는 [스토리지 계정 유형](../../storage/common/storage-account-overview.md#types-of-storage-accounts)을 참조하세요.
> * 스토리지 계정은 [논리적 SQL Server](logical-servers.md)와 동일한 테넌트 및 동일한 위치에 있어야 합니다(다른 구독에 있어도 무방함).
> * Azure Storage 계정을 사용하려면 `Allow trusted Microsoft services to access this storage account`해야 합니다. 스토리지 계정의 **방화벽 및 가상 네트워크** 아래에서 이 설정을 수행합니다.
> * 선택한 스토리지 계정에 대한 `Microsoft.Authorization/roleAssignments/write` 권한이 있어야 합니다. 자세한 정보는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="configure-in-azure-portal"></a>Azure Portal에서 구성

사용자의 구독으로 [Azure Portal](https://portal.azure.com)에 연결합니다. 리소스 그룹 및 서버로 이동합니다.

1. 보안 제목 아래에서 **감사** 를 클릭합니다. **켜기** 를 선택합니다.

2. **스토리지** 를 선택합니다. 로그를 저장할 스토리지 계정을 선택합니다. 스토리지 계정은 [필수 구성 요소](#prerequisites)에 나열된 요구 사항을 준수해야 합니다.

3. **스토리지 세부 정보** 를 엽니다.

  > [!NOTE]
  > 선택한 스토리지 계정이 VNet 뒤에 있으면 다음과 같은 메시지가 표시됩니다.
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >이 메시지가 표시되지 않으면 스토리지 계정이 VNet 뒤에 있지 않은 것입니다.

4. 보존 기간에 대한 일 수를 선택합니다. 그런 후 **OK** 를 클릭합니다. 보존 기간 보다 오래된 로그는 삭제됩니다.

5. 감사 설정에서 **저장** 을 선택합니다.

VNet 또는 방화벽 뒤의 스토리지 계정에 쓰도록 감사를 구성했습니다.

## <a name="configure-with-rest-commands"></a>REST 명령을 사용하여 구성

Azure Portal을 사용하는 대신 REST 명령을 사용하여 VNet 및 방화벽 뒤의 스토리지 계정에서 데이터베이스 이벤트를 쓰도록 감사를 구성할 수 있습니다.

이 섹션의 샘플 스크립트를 실행하기 전에 해당 스크립트를 업데이트해야 합니다. 스크립트에서 다음 값을 바꿉니다.

|샘플 값|샘플 설명|
|:-----|:-----|
|`<subscriptionId>`| Azure 구독 ID입니다.|
|`<resource group>`| Resource group|
|`<logical SQL Server>`| 서버 이름|
|`<administrator login>`| 관리자 계정 |
|`<complex password>`| 관리자 계정에 대한 복잡한 암호|

VNet 또는 방화벽 뒤의 스토리지 계정에 이벤트를 쓰도록 SQL 감사를 구성하려면

1. Azure AD(Azure Active Directory)에 서버를 등록합니다. PowerShell 또는 REST API 중 하나를 사용합니다.

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**REST API**](/rest/api/sql/servers/createorupdate):

   샘플 요청

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   요청 본문

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
     }
   }
   ```

2. [Azure Portal](https://portal.azure.com)을 엽니다. 스토리지 계정으로 이동합니다. **액세스 제어(IAM)** 를 찾은 후, **역할 할당 추가** 를 클릭합니다. 이전 단계처럼 Azure AD(Azure Active Directory)에 등록한 데이터베이스를 호스트하는 서버에 **Storage Blob 데이터 기여자** Azure 역할을 할당합니다.

   > [!NOTE]
   > 소유자 권한이 있는 멤버만 이 단계를 수행할 수 있습니다. 다양한 Azure 기본 제공 역할에 관해서는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

3. *storageAccountAccessKey* 를 지정하지 않고 [서버의 Blob 감사 정책](/rest/api/sql/server%20auditing%20settings/createorupdate)을 구성합니다.

   샘플 요청

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
   ```

   요청 본문

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Azure PowerShell 사용

- [데이터베이스 감사 정책 만들기 또는 업데이트(Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [서버 감사 정책 만들기 또는 업데이트(Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

다음 예제와 같이 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 템플릿을 사용하여 가상 네트워크 및 방화벽으로 보호되는 스토리지 계정에 데이터베이스 이벤트를 쓰도록 감사를 구성할 수 있습니다.

> [!IMPORTANT]
> 가상 네트워크 및 방화벽으로 보호되는 스토리지 계정을 사용하려면 **isStorageBehindVnet** 매개 변수를 true로 설정해야 합니다.

- [감사가 사용하도록 설정된 Azure SQL Server를 배포하여 Blob Storage에 감사 로그 쓰기](https://azure.microsoft.com/resources/templates/sql-auditing-server-policy-to-blob-storage/)

> [!NOTE]
> 연결된 샘플은 외부 퍼블릭 리포지토리에 있고 보증 없이 ‘있는 그대로’ 제공되며, Microsoft 지원 프로그램/서비스에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 가상 네트워크 서비스 엔드포인트를 만든 다음, Azure SQL Database에 대한 가상 네트워크 규칙 만들기](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [Virtual Network 규칙: REST API에서 작업](/rest/api/sql/virtualnetworkrules)
* [서버에 대한 가상 네트워크 서비스 엔드포인트 및 규칙 사용](vnet-service-endpoint-rule-overview.md)
