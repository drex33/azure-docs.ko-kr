---
title: 서버리스 SQL 풀에 대한 스토리지 계정 액세스 제어
description: 서버리스 SQL 풀에서 Azure Storage에 액세스하는 방법과 Azure Synapse Analytics에서 서버리스 SQL 풀에 대한 스토리지 액세스를 제어하는 방법을 설명합니다.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e610e7ec02ec16d077087dab4742721c4209bfa
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234854"
---
# <a name="control-storage-account-access-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버리스 SQL 풀에 대한 스토리지 계정 액세스 제어

서버리스 SQL 풀 쿼리는 Azure Storage에서 직접 파일을 읽습니다. Azure Storage의 파일에 액세스할 수 있는 권한은 두 가지 수준으로 제어됩니다.
- **스토리지 수준** - 사용자에게 기본 스토리지 파일에 액세스할 수 있는 권한이 있어야 합니다. 스토리지 관리자가 Azure AD 보안 주체에게 파일 읽기/쓰기를 허용하거나 스토리지에 액세스하는 데 사용할 SAS 키를 생성해야 합니다.
- **SQL 서비스 수준** - 사용자는 [외부 테이블](develop-tables-external-tables.md)을 사용하여 데이터를 읽거나 `OPENROWSET` 함수를 실행할 수 있는 권한을 부여해야 합니다. [이 섹션에서 필요한 권한](develop-storage-files-overview.md#permissions)에 대해 자세히 알아보세요.

이 문서에서는 사용할 수 있는 자격 증명의 유형과 SQL 및 Azure AD 사용자에 대한 자격 증명 조회가 적용되는 방법에 대해 설명합니다.

## <a name="storage-permissions"></a>스토리지 권한

Synapse Analytics 작업 영역의 서버리스 SQL 풀은 Azure Data Lake 스토리지에 저장된 파일의 내용을 읽을 수 있습니다. SQL 쿼리를 실행하는 사용자가 파일을 읽을 수 있도록 하려면 스토리지에 대한 사용 권한을 구성해야 합니다. 파일에 대한 액세스를 허용하는 방법에는 세 가지가 있습니다.
- **[RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)** 를 사용하면 스토리지가 배치된 테넌트의 일부 Azure AD 사용자에게 역할을 할당할 수 있습니다. 리더에는 스토리지에 대한 `Storage Blob Data Reader`, `Storage Blob Data Contributor` 또는 `Storage Blob Data Owner` RBAC 역할이 있어야 합니다. Azure Storage에 데이터를 기록하는 사용자에게는 `Storage Blob Data Writer` 또는 `Storage Blob Data Owner` 역할을 부여해야 합니다. `Storage Owner` 역할은 사용자가 `Storage Data Owner`이기도 한다는 것을 의미하지 않습니다.
- **ACL(액세스 제어 목록)** 을 사용하면 Azure Storage의 파일 및 디렉터리에 대한 세분화된 [읽기(R), 쓰기(W) 및 실행(X) 권한](../../storage/blobs/data-lake-storage-access-control.md#levels-of-permission)을 정의할 수 있습니다. Azure AD 사용자에게 ACL을 할당할 수 있습니다. 리더가 Azure Storage의 경로에 있는 파일을 읽으려면 파일 경로에 있는 모든 폴더에 대한 실행(X) ACL 및 파일에 대한 읽기(R) ACL이 있어야 합니다. [스토리지 계층에서 ACL 사용 권한을 설정하는 방법에 대해 자세히 알아보세요](../../storage/blobs/data-lake-storage-access-control.md#how-to-set-acls).
- **SAS(공유 액세스 서명)** 를 사용하면 reader가 시간 제한 토큰을 사용하여 Azure Data Lake 스토리지에 있는 파일에 액세스할 수 있습니다. reader는 Azure AD 사용자로 인증하지 않아도 됩니다. SAS 토큰에는 reader에 부여된 사용 권한뿐만 아니라 토큰의 유효 기간도 포함됩니다. SAS 토큰은 동일한 Azure AD 테넌트에 없어도 되는 모든 사용자에게 시간 제한 액세스를 부여하는 경우에 사용하면 좋습니다. SAS 토큰은 스토리지 계정 또는 특정 디렉터리에 정의할 수 있습니다. [공유 액세스 서명을 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../../storage/common/storage-sas-overview.md)에 대해 자세히 알아보세요.

또는 익명 액세스를 허용하여 파일을 공개적으로 사용할 수 있습니다. 비공개 데이터가 있는 경우 이 접근 방식을 사용하면 안 됩니다. 

## <a name="supported-storage-authorization-types"></a>지원되는 스토리지 권한 부여 유형

서버리스 SQL 풀에 로그인한 사용자는 파일을 공개적으로 사용할 수 없는 경우 Azure Storage의 파일에 액세스하고 쿼리할 수 있는 권한이 있어야 합니다. [사용자 ID](?tabs=user-identity), [공유 액세스 서명](?tabs=shared-access-signature), [서비스 사용자](?tab/service-principal) 및 [관리 ID](?tabs=managed-identity)의 네 가지 권한 부여 유형을 사용하여 비공개 스토리지에 액세스할 수 있습니다.

> [!NOTE]
> **Azure AD 통과** 는 작업 영역을 만들 때의 기본 동작입니다.

### <a name="user-identity"></a>[사용자 ID](#tab/user-identity)

**사용자 ID**("Azure AD 통과"라고도 함)는 서버리스 SQL 풀에 로그인한 Azure AD 사용자의 ID를 사용하여 데이터 액세스 권한을 부여하는 권한 부여 유형입니다. 데이터에 액세스하기 전에 Azure Storage 관리자가 Azure AD 사용자에게 권한을 부여해야 합니다. 아래 표에서 설명한 대로 SQL 사용자 유형에는 지원되지 않습니다.

> [!IMPORTANT]
> AAD 인증 토큰은 클라이언트 애플리케이션에서 캐시될 수 있습니다. 예를 들어 PowerBI는 AAD 토큰을 캐시하고 한 시간 동안 동일한 토큰을 재사용합니다. 쿼리가 실행되는 도중 토큰이 만료되면 긴 실행 쿼리가 실패할 수 있습니다. 쿼리 중간에 만료되는 AAD 액세스 토큰으로 인해 쿼리 오류가 발생하는 경우 [서비스 사용자](develop-storage-files-storage-access-control.md?tabs=service-principal#supported-storage-authorization-types), [관리 ID](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) 또는 [공유 액세스 서명](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#supported-storage-authorization-types)으로 전환하는 것이 좋습니다.

이 ID를 사용하여 데이터에 액세스하려면 Storage Blob 데이터 소유자/기여자/읽기 권한자 역할이 있어야 합니다. 또는 파일 및 폴더에 액세스하는 세분화된 ACL 규칙을 지정할 수 있습니다. 스토리지 계정의 소유자인 경우에도 Storage Blob 데이터 역할 중 하나에 자신을 추가해야 합니다.
Azure Data Lake Store Gen2의 액세스 제어에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../../storage/blobs/data-lake-storage-access-control.md) 문서를 검토하세요.


### <a name="shared-access-signature"></a>[공유 액세스 서명](#tab/shared-access-signature)

**SAS(공유 액세스 서명)** 는 스토리지 계정의 리소스에 대한 위임된 액세스를 제공합니다. SAS를 사용하면 고객이 계정 키를 공유하지 않고 스토리지 계정의 리소스에 대한 액세스 권한을 클라이언트에 부여할 수 있습니다. SAS는 유효성 간격, 부여된 권한, 허용되는 IP 주소 범위 및 허용되는 프로토콜(https/http)을 포함하여 SAS를 사용하는 클라이언트에 부여하는 액세스 유형에 대한 세부적인 제어를 제공합니다.

**Azure Portal > 스토리지 계정 > 공유 액세스 서명 > 권한 구성 > SAS 및 연결 문자열 생성** 으로 차례로 이동하여 SAS 토큰을 가져올 수 있습니다.

> [!IMPORTANT]
> SAS 토큰이 생성되면 토큰의 시작 부분에 물음표('?')가 포함됩니다. 서버리스 SQL 풀에서 토큰을 사용하려면 자격 증명을 만들 때 물음표('?')를 제거해야 합니다. 예를 들면 다음과 같습니다.
>
> SAS token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

SAS 토큰을 사용하여 액세스를 사용하도록 설정하려면 데이터베이스 범위 또는 서버 범위 자격 증명을 만들어야 합니다. 


> [!IMPORTANT]
> SAS 토큰을 사용하여 프라이빗 스토리지 계정에 액세스할 수 없습니다. 보호된 스토리지에 액세스하려면 [관리 ID](develop-storage-files-storage-access-control.md?tabs=managed-identity#supported-storage-authorization-types) 또는 [Azure AD 통과](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) 인증으로 전환하는 것이 좋습니다.


### <a name="service-principal"></a>Service Principal
**서비스 주체** 는 특정 Azure AD 테넌트에 있는 글로벌 애플리케이션 개체의 로컬 표현입니다. 이 인증 방법은 사용자 앱, 서비스 또는 자동화 도구에 대한 스토리지 액세스 권한을 부여하는 경우에 적합합니다. 

애플리케이션은 Azure Active Directory에 등록해야 합니다. 등록 프로세스는 [빠른 시작: Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../../active-directory/develop/quickstart-register-app.md)을 참조하세요. 애플리케이션을 등록한 후에는 해당 서비스 주체를 권한 부여에 사용할 수 있습니다. 

애플리케이션에서 데이터에 액세스하려면 서비스 사용자에게 Storage Blob 데이터 소유자/기여자/Reader 역할이 할당되어야 합니다. 서비스 주체가 스토리지 계정의 소유자인 경우에도 적절한 Storage Blob 데이터 역할을 부여받아야 합니다. 스토리지 파일 및 폴더에 대한 액세스 권한을 부여하는 또 다른 방법으로, 서비스 주체에 대한 세분화된 ACL 규칙을 정의할 수 있습니다. Azure Data Lake Store Gen2의 액세스 제어에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../../storage/blobs/data-lake-storage-access-control.md) 문서를 검토하세요.

### <a name="managed-identity"></a>[관리 ID](#tab/managed-identity)

**관리 ID** 는 MSI라고도 합니다. Azure 서비스를 서버리스 SQL 풀에 제공하는 Azure AD(Azure Active Directory)의 기능입니다. 또한 Azure AD에서 관리 ID를 자동으로 배포합니다. 이 ID는 Azure Storage에서 데이터 액세스에 대한 요청을 승인하는 데 사용될 수 있습니다.

데이터에 액세스하기 전에 Azure Storage 관리자가 데이터에 액세스할 수 있는 권한을 관리 ID에 부여해야 합니다. 관리 ID에 대한 권한 부여는 다른 Azure AD 사용자에게 권한을 부여하는 것과 동일한 방식으로 수행됩니다.

### <a name="anonymous-access"></a>[익명 액세스](#tab/public-access)

[익명 액세스를 허용](../../storage/blobs/anonymous-read-access-configure.md)하는 Azure 스토리지 계정에 있는 공개적으로 사용 가능한 파일에 액세스할 수 있습니다.

---

#### <a name="cross-tenant-scenarios"></a>교차 테넌트 시나리오
Synapse 서버리스 SQL 풀과 다른 테넌트에 Azure Storage가 있는 경우 **서비스 주체** 를 통한 권한 부여가 권장되는 방법입니다. **SAS** 권한 부여도 가능하지만 **관리 ID** 는 지원되지 않습니다. 

### <a name="supported-authorization-types-for-databases-users"></a>데이터베이스 사용자에 대해 지원되는 권한 부여 유형

아래 표에서 Synapse 서버리스 SQL 엔드포인트에 대한 다양한 로그인 메서드에 사용할 수 있는 권한 부여 유형을 찾을 수 있습니다.

| 권한 부여 유형                    | *SQL 사용자*    | *Azure AD 사용자*     | Service Principal |
| ------------------------------------- | ------------- | -----------    | -------- |
| [사용자 ID](?tabs=user-identity#supported-storage-authorization-types)       |  지원되지 않음 | 지원됨      | 지원됨|
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | 지원됨     | 지원됨      | 지원됨|
| Service Principal | 지원됨 | 지원됨      | 지원됨|
| [관리 ID](?tabs=managed-identity#supported-storage-authorization-types) | 지원됨 | 지원됨      | 지원됨|

### <a name="supported-storages-and-authorization-types"></a>지원되는 스토리지 및 권한 부여 유형

사용할 수 있는 권한 부여 및 Azure Storage 유형의 조합은 다음과 같습니다.

| 권한 부여 유형  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | 지원됨      | 지원되지 않음   | 지원됨     |
| Service Principal | 지원됨   | 지원됨      | 지원됨  |
| [관리 ID](?tabs=managed-identity#supported-storage-authorization-types) | 지원됨      | 지원됨        | 지원 여부     |
| [사용자 ID](?tabs=user-identity#supported-storage-authorization-types)    | 지원 여부      | 지원됨        | 지원 여부     |

## <a name="firewall-protected-storage"></a>방화벽으로 보호된 스토리지

[리소스 인스턴스 규칙](../../storage/common/storage-network-security.md?tabs=azure-portal#grant-access-from-azure-resource-instances-preview)을 만들어 특정 서버리스 SQL 풀에 대한 액세스를 허용하도록 스토리지 계정을 구성할 수 있습니다.
방화벽으로 보호되는 스토리지에 액세스하는 경우 **사용자 ID** 또는 **관리 ID** 를 사용할 수 있습니다.

> [!NOTE]
> 스토리지의 방화벽 기능은 공개 미리 보기로 제공되며 모든 공용 클라우드 지역에서 사용할 수 있습니다. 


아래 표에서 Synapse 서버리스 SQL 엔드포인트에 대한 다양한 로그인 메서드에 사용할 수 있는 권한 부여 유형을 찾을 수 있습니다.

| 권한 부여 유형                    | *SQL 사용자*    | *Azure AD 사용자*     | Service Principal |
| ------------------------------------- | ------------- | -----------    | -------- |
| [사용자 ID](?tabs=user-identity#supported-storage-authorization-types)       |  지원되지 않음 | 지원됨      | 지원됨|
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | 지원되지 않음     | 지원되지 않음      | 지원되지 않음|
| Service Principal | 지원되지 않음 | 지원되지 않음      | 지원되지 않음|
| [관리 ID](?tabs=managed-identity#supported-storage-authorization-types) | 지원됨 | 지원됨      | 지원 여부|

### <a name="user-identity"></a>[사용자 ID](#tab/user-identity)

사용자 ID를 통해 방화벽으로 보호된 스토리지에 액세스하려면 Azure Portal UI 또는 PowerShell 모듈 Az. Storage를 사용하면 됩니다.
### <a name="configuration-via-azure-portal"></a>Azure Portal을 통한 구성

1. Azure Portal에서 스토리지 계정을 검색합니다.
1. 설정 섹션에서 네트워킹으로 이동합니다.
1. "리소스 인스턴스" 섹션에서 Synapse 작업 영역에 대한 예외를 추가합니다.
1. 리소스 유형으로 Microsoft.Synapse/workspaces를 선택합니다.
1. 인스턴스 이름으로 작업 영역 이름을 선택합니다.
1. 저장을 클릭합니다.

### <a name="configuration-via-powershell"></a>PowerShell을 통한 구성

이러한 단계에 따라 스토리지 계정 방화벽을 구성하고 Synapse 작업 영역에 대한 예외를 추가합니다.

1. PowerShell 열기 또는 [PowerShell 설치](/powershell/scripting/install/installing-powershell-core-on-windows)
2. Az.Storage 3.4.0 모듈 및 Az.Synapse 0.7.0을 설치합니다. 
    ```powershell
    Install-Module -Name Az.Storage -RequiredVersion 3.4.0
    Install-Module -Name Az.Synapse -RequiredVersion 0.7.0
    ```
    > [!IMPORTANT]
    > **버전 3.4.0** 을 사용하고 있는지 확인합니다. 다음 명령을 실행하여 Az. Storage 버전을 확인할 수 있습니다.  
    > ```powershell 
    > Get-Module -ListAvailable -Name  Az.Storage | select Version
    > ```
    > 

3. Azure 테넌트에 연결: 
    ```powershell
    Connect-AzAccount
    ```
4. PowerShell에서 변수 정의: 
    - 리소스 그룹 이름 - Azure Portal의 스토리지 계정 개요에서 찾을 수 있습니다.
    - 계정 이름 - 방화벽 규칙에 의해 보호되는 스토리지 계정의 이름입니다.
    - 테넌트 ID - 테넌트 정보에 있는 Azure Active Directory의 Azure Portal에서 찾을 수 있습니다.
    - 작업 영역 이름 - Synapse 작업 영역의 이름입니다.

    ```powershell
        $resourceGroupName = "<resource group name>"
        $accountName = "<storage account name>"
        $tenantId = "<tenant id>"
        $workspaceName = "<synapse workspace name>"
        
        $workspace = Get-AzSynapseWorkspace -Name $workspaceName
        $resourceId = $workspace.Id
        $index = $resourceId.IndexOf("/resourceGroups/", 0)
        # Replace G with g - /resourceGroups/ to /resourcegroups/
        $resourceId = $resourceId.Substring(0,$index) + "/resourcegroups/" + $resourceId.Substring($index + "/resourceGroups/".Length)
        $resourceId
    ```
    > [!IMPORTANT]
    > 리소스 ID가 resourceId 변수 인쇄에서 이 템플릿과 일치하는지 확인합니다.
    >
    > **resourcegroups** 를 소문자로 작성하는 것이 중요합니다.
    > 리소스 ID의 한 예: 
    > ```
    > /subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Synapse/workspaces/{name-of-workspace}
    > ```
    > 
5. 스토리지 네트워크 규칙 추가: 
    ```powershell
        Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId
    ```
6. 규칙이 스토리지 계정에 적용되었는지 확인: 
    ```powershell
        $rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
        $rule.ResourceAccessRules | ForEach-Object { 
            if ($_.ResourceId -cmatch "\/subscriptions\/(\w\-*)+\/resourcegroups\/(.)+") { 
                Write-Host "Storage account network rule is successfully configured." -ForegroundColor Green
                $rule.ResourceAccessRules
            } else {
                Write-Host "Storage account network rule is not configured correctly. Remove this rule and follow the steps in detail." -ForegroundColor Red
                $rule.ResourceAccessRules
            }
        }
    ```

### <a name="shared-access-signature"></a>[공유 액세스 서명](#tab/shared-access-signature)

공유 액세스 서명은 방화벽으로 보호된 스토리지에 액세스하는 데 사용할 수 없습니다.

### <a name="service-principal"></a>Service Principal

서비스 주체는 방화벽으로 보호된 스토리지에 액세스하는 데 사용할 수 없습니다. 대신 관리 ID를 사용합니다.

### <a name="managed-identity"></a>[관리 ID](#tab/managed-identity)

해당 인스턴스의 [시스템 할당 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에 [신뢰할 수 있는 Microsoft 서비스 허용... 설정](../../storage/common/storage-network-security.md#trusted-microsoft-services) 및 명시적으로 [Azure 역할 할당](../../storage/blobs/authorize-access-azure-active-directory.md#assign-azure-roles-for-access-rights)이 필요합니다. 이 경우 인스턴스에 대한 액세스 범위는 관리 ID에 할당된 Azure 역할에 해당합니다.

### <a name="anonymous-access"></a>[익명 액세스](#tab/public-access)

익명 액세스를 사용하여 방화벽으로 보호된 스토리지에 액세스할 수 없습니다.

---

## <a name="credentials"></a>자격 증명

Azure Storage에 있는 파일을 쿼리하려면 서버리스 SQL 풀 엔드포인트에 인증 정보가 포함된 자격 증명이 필요합니다. 두 가지 유형의 자격 증명이 사용됩니다.
- 서버 수준 자격 증명은 `OPENROWSET` 함수를 사용하여 실행되는 임시 쿼리에 사용됩니다. 자격 증명 이름은 스토리지 URL과 일치해야 합니다.
- 데이터베이스 범위 자격 증명은 외부 테이블에 사용됩니다. 외부 테이블은 스토리지에 액세스하는 데 사용해야 하는 자격 증명으로 `DATA SOURCE`를 참조합니다.

사용자가 자격 증명을 만들거나 삭제할 수 있도록 관리자는 사용자에게 GRANT/DENY ALTER ANY CREDENTIAL 권한을 부여할 수 있습니다.

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

외부 스토리지에 액세스하는 데이터베이스 사용자는 자격 증명을 사용할 수 있는 권한이 있어야 합니다.

### <a name="grant-permissions-to-use-credential"></a>자격 증명을 사용할 수 있는 권한 부여

자격 증명을 사용하려면 사용자에게 특정 자격 증명에 대한 `REFERENCES` 권한이 있어야 합니다. specific_user의 storage_credential에 대한 `REFERENCES` 권한을 부여하려면 다음을 실행합니다.

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

## <a name="server-scoped-credential"></a>서버 범위 자격 증명

서버 범위 자격 증명은 SQL 로그인이 `DATA_SOURCE` 없이 `OPENROWSET` 함수를 호출하여 일부 스토리지 계정의 파일을 읽을 때 사용됩니다. 서버 범위 자격 증명의 이름은 **반드시** Azure Storage의 기본 URL과 일치해야 합니다(선택적으로 뒤에 컨테이너 이름이 옴). 자격 증명은 [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)을 실행하여 추가됩니다. CREDENTIAL NAME 인수를 제공해야 합니다.

> [!NOTE]
> `FOR CRYPTOGRAPHIC PROVIDER` 인수는 지원되지 않습니다.

서버 수준의 자격 증명 이름은 스토리지 계정의 전체 경로(및 선택적으로 컨테이너)와 일치해야 하며, 다음과 같은 형식이어야 합니다. `<prefix>://<storage_account_path>[/<container_name>]`. 스토리지 계정 경로는 다음 표에 설명되어 있습니다.

| 외부 데이터 원본       | 접두사 | 스토리지 계정 경로                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

서버 범위 자격 증명을 사용하면 다음 인증 유형을 사용하여 Azure 스토리지에 액세스할 수 있습니다.

### <a name="user-identity"></a>[사용자 ID](#tab/user-identity)

Azure AD 사용자는 `Storage Blob Data Owner`, `Storage Blob Data Contributor` 또는 `Storage Blob Data Reader` 역할이 있는 경우 Azure 스토리지의 모든 파일에 액세스할 수 있습니다. Azure AD 사용자는 스토리지에 액세스하는 데 자격 증명이 필요하지 않습니다. 

SQL 사용자는 Azure AD 인증을 사용하여 스토리지에 액세스할 수 없습니다.

### <a name="shared-access-signature"></a>[공유 액세스 서명](#tab/shared-access-signature)

다음 스크립트는 `OPENROWSET` 함수가 SAS 토큰을 사용하여 Azure 스토리지의 파일에 액세스하는 데 사용할 수 있는 서버 수준 자격 증명을 만듭니다. 이 자격 증명을 만들면 `OPENROWSET` 함수를 실행하는 SQL 보안 주체가 자격 증명 이름의 URL과 일치하는 Azure 스토리지에서 SAS 키로 보호된 파일을 읽을 수 있습니다.

<*mystorageaccountname*>을 실제 스토리지 계정 이름으로 교환하고, <*mystorageaccountcontainername*>을 실제 컨테이너 이름으로 교환합니다.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.dfs.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

필요에 따라 컨테이너 이름 없이 스토리지 계정의 기본 URL만 사용할 수 있습니다.

### <a name="service-principal"></a>Service Principal

다음 스크립트는 인증 및 권한 부여를 위해 서비스 주체를 사용하여 스토리지의 파일에 액세스하는 데 사용할 수 있는 서버 수준 자격 증명을 만듭니다. **AppID** 는 Azure Portal에서 앱 등록을 방문하여 스토리지 액세스를 요청하는 앱을 선택하여 찾을 수 있습니다. **비밀** 은 앱 등록 중에 얻습니다. **AuthorityUrl** 은 AAD Oauth2.0 기관의 URL입니다.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY = '<AppID>@<AuthorityUrl>' 
, SECRET = '<Secret>'
```

### <a name="managed-identity"></a>[관리 ID](#tab/managed-identity)
 
다음 스크립트는 `OPENROWSET` 함수가 작업 영역 관리 ID를 사용하여 Azure 스토리지의 파일에 액세스하는 데 사용할 수 있는 서버 수준 자격 증명을 만듭니다.

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

필요에 따라 컨테이너 이름 없이 스토리지 계정의 기본 URL만 사용할 수 있습니다.

### <a name="public-access"></a>[공용 액세스](#tab/public-access)

데이터베이스 범위 자격 증명은 공개적으로 사용 가능한 파일에 대한 액세스를 허용하는 데 필요하지 않습니다. Azure Storage에서 공개적으로 사용 가능한 파일에 액세스하려면 [데이터베이스 범위 자격 증명 없이 데이터 원본](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source)을 만듭니다.

---

## <a name="database-scoped-credential"></a>데이터베이스 범위 자격 증명

데이터베이스 범위 자격 증명은 보안 주체가 `DATA_SOURCE`를 사용하여 `OPENROWSET` 함수를 호출하거나 공용 파일에 액세스하지 않는 [외부 테이블](develop-tables-external-tables.md)에서 데이터를 선택하는 경우에 사용됩니다. 데이터베이스 범위 자격 증명은 스토리지 계정 이름과 일치하지 않아도 됩니다. 스토리지의 위치를 정의하는 데이터 원본에서 명시적으로 사용됩니다.

데이터베이스 범위 자격 증명을 사용하면 다음 인증 유형을 사용하여 Azure 스토리지에 액세스할 수 있습니다.

### <a name="azure-ad-identity"></a>[Azure AD ID](#tab/user-identity)

Azure AD 사용자는 최소한 `Storage Blob Data Owner`, `Storage Blob Data Contributor` 또는 `Storage Blob Data Reader` 역할이 있는 경우 Azure 스토리지의 모든 파일에 액세스할 수 있습니다. Azure AD 사용자는 스토리지에 액세스하는 데 자격 증명이 필요하지 않습니다.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

SQL 사용자는 Azure AD 인증을 사용하여 스토리지에 액세스할 수 없습니다.

### <a name="shared-access-signature"></a>[공유 액세스 서명](#tab/shared-access-signature)

다음 스크립트는 자격 증명에 지정된 SAS 토큰을 사용하여 스토리지의 파일에 액세스하는 데 사용되는 자격 증명을 만듭니다. 스크립트는 이 SAS 토큰을 사용하여 스토리지에 액세스하는 샘플 외부 데이터 원본을 만듭니다.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```


### <a name="service-principal"></a>Service Principal
다음 스크립트는 인증 및 권한 부여를 위해 서비스 주체를 사용하여 스토리지의 파일에 액세스하는 데 사용할 수 있는 데이터베이스 범위 자격 증명을 만듭니다. **AppID** 는 Azure Portal에서 앱 등록을 방문하여 스토리지 액세스를 요청하는 앱을 선택하여 찾을 수 있습니다. **비밀** 은 앱 등록 중에 얻습니다. **AuthorityUrl** 은 AAD Oauth2.0 기관의 URL입니다.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>

CREATE DATABASE SCOPED CREDENTIAL [<CredentialName>] WITH
IDENTITY = '<AppID>@<AuthorityUrl>' 
, SECRET = '<Secret>'
GO
CREATE EXTERNAL DATA SOURCE MyDataSource
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = CredentialName
)
```

### <a name="managed-identity"></a>[관리 ID](#tab/managed-identity)

다음 스크립트는 현재 Azure AD 사용자를 서비스의 관리 ID로 가장하는 데 사용할 수 있는 데이터베이스 범위 자격 증명을 만듭니다. 스크립트는 작업 영역 ID를 사용하여 스토리지에 액세스하는 샘플 외부 데이터 원본을 만듭니다.

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

데이터베이스 범위 자격 증명은 스토리지 위치를 정의하는 DATA SOURCE에서 명시적으로 사용되기 때문에 스토리지 계정 이름과 일치하지 않아도 됩니다.

### <a name="public-access"></a>[공용 액세스](#tab/public-access)

데이터베이스 범위 자격 증명은 공개적으로 사용 가능한 파일에 대한 액세스를 허용하는 데 필요하지 않습니다. Azure Storage에서 공개적으로 사용 가능한 파일에 액세스하려면 [데이터베이스 범위 자격 증명 없이 데이터 소스](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source)를 만듭니다.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

데이터베이스 범위 자격 증명은 외부 데이터 원본에서 이 스토리지에 액세스하는 데 사용할 인증 방법을 지정하는 데 사용됩니다.

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>예

### <a name="access-a-publicly-available-data-source"></a>**공개적으로 사용 가능한 데이터 원본에 액세스**

다음 스크립트를 사용하여 공개적으로 사용 가능한 데이터 원본에 액세스하는 테이블을 만듭니다.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

데이터베이스 사용자는 데이터 원본을 참조하는 [OPENROWSET](develop-openrowset.md) 함수나 외부 테이블을 사용하여 데이터 원본에서 파일의 콘텐츠를 읽을 수 있습니다.

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = 'mysample',
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**자격 증명을 사용하여 데이터 원본에 액세스**

다음 스크립트를 수정하여 SAS 토큰, 사용자의 Azure AD ID 또는 작업 영역의 관리 ID를 사용하여 Azure 스토리지에 액세스하는 외부 테이블을 만듭니다.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity, SAS token or Service Principal. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'
GO
CREATE DATABASE SCOPED CREDENTIAL SPNCredential WITH
IDENTITY = '**44e*****8f6-ag44-1890-34u4-22r23r771098@https://login.microsoftonline.com/**do99dd-87f3-33da-33gf-3d3rh133ee33/oauth2/token' 
, SECRET = '.7OaaU_454azar9WWzLL.Ea9ePPZWzQee~'
GO
-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
--,CREDENTIAL = SPNCredential
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

데이터베이스 사용자는 데이터 원본을 참조하는 [OPENROWSET](develop-openrowset.md) 함수나 [외부 테이블](develop-tables-external-tables.md)을 사용하여 데이터 원본에서 파일의 콘텐츠를 읽을 수 있습니다.

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = 'mysample', FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>다음 단계

아래 나열된 문서를 통해 다른 폴더 유형, 파일 형식을 쿼리하고, 보기를 만들고 사용하는 방법을 알아볼 수 있습니다.

- [단일 CSV 파일 쿼리](query-single-csv-file.md)
- [폴더 및 여러 CSV 파일 쿼리](query-folders-multiple-csv-files.md)
- [특정 파일 쿼리](query-specific-files.md)
- [Parquet 파일 쿼리](query-parquet-files.md)
- [보기 만들기 및 사용](create-use-views.md)
- [JSON 파일 쿼리](query-json-files.md)
- [Parquet 중첩 형식 쿼리](query-parquet-nested-types.md)
