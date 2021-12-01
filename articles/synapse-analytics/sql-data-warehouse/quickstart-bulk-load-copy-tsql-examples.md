---
title: COPY 문을 사용한 인증 메커니즘
description: 데이터 대량 로드를 위한 인증 메커니즘 개요를 표시합니다.
services: synapse-analytics
author: WilliamDAssafMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 07/10/2020
ms.author: wiassaf
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps, mode-other
ms.openlocfilehash: abc77f72aa6dfb596e47c64cc71ff272981e1b8f
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133365135"
---
# <a name="securely-load-data-using-synapse-sql"></a>Synapse SQL을 사용하여 안전하게 데이터 로드

이 문서에서는 [COPY 문](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)의 보안 인증 메커니즘을 설명하고 예시를 보여줍니다. COPY 문은 Synapse SQL에서 데이터를 대량 로드하기 위한 가장 유연하고 안전한 방법입니다.
## <a name="supported-authentication-mechanisms"></a>지원되는 인증 메커니즘

다음 표에서는 각 파일 유형 및 스토리지 계정에 대해 지원되는 인증 방법을 설명합니다. 원본 스토리지 위치 및 오류 파일 위치에 적용됩니다.

|                          |                CSV                |                      Parquet                       |                        ORC                         |
| :----------------------: | :-------------------------------: | :------------------------------------------------: | :------------------------------------------------: |
|  **Azure Blob 스토리지**  | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD |                      SAS/KEY                       |                      SAS/KEY                       |
| **Azure Data Lake Gen2** | SAS/MSI/SERVICE PRINCIPAL/KEY/AAD | SAS(blob<sup>1</sup>)/MSI(dfs<sup>2</sup>)/SERVICE PRINCIPAL/KEY/AAD | SAS(blob<sup>1</sup>)/MSI(dfs<sup>2</sup>)/SERVICE PRINCIPAL/KEY/AAD |

1: 이 인증 방법에는 외부 위치 경로의 .blob 엔드포인트( **.blob**.core.windows.net)가 필요합니다.

2: 이 인증 방법에는 외부 위치 경로의 .dfs 엔드포인트( **.dfs**.core.windows.net)가 필요합니다.

## <a name="a-storage-account-key-with-lf-as-the-row-terminator-unix-style-new-line"></a>A. 행 종결자로 LF가 사용되는 스토리지 계정 키(Unix 스타일의 줄 바꿈)


```sql
--Note when specifying the column list, input field numbers start from 1
COPY INTO target_table (Col_one default 'myStringDefault' 1, Col_two default 1 3)
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='<Your_Account_Key>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Storage Account Key', SECRET='x6RWv4It5F2msnjelv3H4DA80n0QW0daPdw43jM0nyetx4c6CpDkdj3986DX5AHFMIf/YN4y6kkCnU8lb+Wx0Pj+6MDw=='),
    ,ROWTERMINATOR='0x0A' --0x0A specifies to use the Line Feed character (Unix based systems)
)
```
> [!IMPORTANT]
>
> - 16진수 값(0x0A)을 사용하여 줄 바꿈 문자를 지정합니다. COPY 문은 '\n' 문자열을 '\r\n'(캐리지 리턴 줄 바꿈)으로 해석합니다.

## <a name="b-shared-access-signatures-sas-with-crlf-as-the-row-terminator-windows-style-new-line"></a>B. 행 종결자로 CRLF를 사용하는 SAS(공유 액세스 서명)(Windows 스타일 줄 바꿈)
```sql
COPY INTO target_table
FROM 'https://adlsgen2account.dfs.core.windows.net/myblobcontainer/folder1/'
WITH (
    FILE_TYPE = 'CSV'
    ,CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<Your_SAS_Token>')
    --CREDENTIAL should look something like this:
    --CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='?sv=2018-03-28&ss=bfqt&srt=sco&sp=rl&st=2016-10-17T20%3A14%3A55Z&se=2021-10-18T20%3A19%3A00Z&sig=IEoOdmeYnE9%2FKiJDSFSYsz4AkNa%2F%2BTx61FuQ%2FfKHefqoBE%3D'),
    ,ROWTERMINATOR='\n'-- COPY command automatically prefixes the \r character when \n (newline) is specified. This results in carriage return newline (\r\n) for Windows based systems.
)
```

> [!IMPORTANT]
>
> - ROWTERMINATOR를 '\r\r\n'으로 해석되고 구문 분석 문제를 일으킬 수 있는 '\r\n'으로 지정하지 마십시오.

## <a name="c-managed-identity"></a>C. 관리 ID

스토리지 계정이 VNet에 연결되었을 때는 관리 ID 인증이 필요합니다. 

### <a name="prerequisites"></a>사전 요구 사항

1. [이 가이드](/powershell/azure/install-az-ps?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용하여 Azure PowerShell을 설치합니다.
2. 범용 v1 또는 Blob Storage 계정이 있는 경우 먼저 이 [가이드](../../storage/common/storage-account-upgrade.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용하여 범용 v2로 업그레이드해야 합니다.
3. Azure Storage 계정 **방화벽 및 가상 네트워크** 설정 메뉴에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다.** 를 설정해야 합니다. 자세한 내용은 이 [가이드](../../storage/common/storage-network-security.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#exceptions)를 참조하세요.

#### <a name="steps"></a>단계

1. 독립 실행형 전용 SQL 풀이 있는 경우 PowerShell을 사용하여 AAD(Azure Active Directory)에 SQL 서버를 등록합니다. 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Synapse 작업 영역 내의 전용 SQL 풀에는 이 단계가 필요하지 않습니다.

1. Synapse 작업 영역이 있는 경우 작업 영역의 시스템 관리 ID를 등록합니다.

   1. Azure Portal에서 Synapse 작업 영역으로 이동합니다.
   2. 관리 ID 블레이드로 이동합니다. 
   3. "파이프라인 허용" 옵션을 사용하도록 설정했는지 확인합니다.
   
   ![작업 영역 시스템 msi 등록](./media/quickstart-bulk-load-copy-tsql-examples/msi-register-example.png)

1. 이 [가이드](../../storage/common/storage-account-create.md)를 사용하여 **범용 v2 스토리지 계정** 을 만듭니다.

   > [!NOTE]
   >
   > - 범용 v1 또는 Blob Storage 계정이 있는 경우 먼저 이 [가이드](../../storage/common/storage-account-upgrade.md)를 사용하여 **v2로 업그레이드** 해야 합니다.
   > - Azure Data Lake Storage Gen2의 알려진 문제에 대해서는 이 [가이드](../../storage/blobs/data-lake-storage-known-issues.md)를 참조하세요.

1. 스토리지 계정에서 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.

1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | Storage Blob 데이터 기여자 |
    | 다음에 대한 액세스 할당 | SERVICEPRINCIPAL |
    | 멤버 | AAD(Azure Active Directory)에 등록한 전용 SQL 풀을 호스팅하는 서버 또는 작업 영역  |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)


   > [!NOTE]
   > 소유자 권한이 있는 멤버만 이 단계를 수행할 수 있습니다. Azure 기본 제공 역할에 대한 자세한 내용은 이 [가이드](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조하세요.
   
    > [!IMPORTANT]
    > **Storage** **Blob 데이터** 소유자, 기여자 또는 읽기 권한자 Azure 역할을 지정합니다. 이러한 역할은 소유자, 기여자 및 읽기 권한자로 구성되는 Azure 기본 제공 역할과 다릅니다. 

    ![로드할 수 있는 Azure RBAC 권한 부여](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

4. 이제 "관리 ID"를 사용하여 COPY 문을 실행할 수 있습니다.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV',
        CREDENTIAL = (IDENTITY = 'Managed Identity'),
    )
    ```

## <a name="d-azure-active-directory-authentication"></a>D. Azure Active Directory 인증
#### <a name="steps"></a>단계

1. 스토리지 계정에서 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택하여 역할 할당 추가 페이지를 엽니다.

1. 다음 역할을 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.
    
    | 설정 | 값 |
    | --- | --- |
    | 역할 | Storage Blob 데이터 소유자, 기여자 또는 리더 |
    | 다음에 대한 액세스 할당 | USER |
    | 멤버 | Azure AD 사용자 |

    ![Azure Portal에서 역할 할당 페이지를 추가합니다.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

    > [!IMPORTANT]
    > **Storage** **Blob 데이터** 소유자, 기여자 또는 읽기 권한자 Azure 역할을 지정합니다. 이러한 역할은 소유자, 기여자 및 읽기 권한자로 구성되는 Azure 기본 제공 역할과 다릅니다.

    ![로드할 수 있는 Azure RBAC 권한 부여](./media/quickstart-bulk-load-copy-tsql-examples/rbac-load-permissions.png)

1. 다음 [문서](../../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)에 따라 Azure AD 인증을 구성합니다. 

1. Active Directory를 사용하여 SQL 풀에 연결합니다. 이제 자격 증명을 지정하지 않고 COPY 문을 실행할 수 있습니다.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder1/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
    )
    ```


## <a name="e-service-principal-authentication"></a>E. 서비스 주체 인증
#### <a name="steps"></a>단계

1. [Azure Active Directory 애플리케이션 만들기](../..//active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [애플리케이션 ID 가져오기](../..//active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)
3. [인증 키 가져오기](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)
4. [V1 OAuth 2.0 토큰 엔드포인트 가져오기](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications)
5. 스토리지 계정에서 [Azure AD 애플리케이션에 읽기, 쓰기 및 실행 권한 할당](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder)
6. 이제 COPY 문을 실행할 수 있습니다.

    ```sql
    COPY INTO dbo.target_table
    FROM 'https://myaccount.blob.core.windows.net/myblobcontainer/folder0/*.txt'
    WITH (
        FILE_TYPE = 'CSV'
        ,CREDENTIAL=(IDENTITY= '<application_ID>@<OAuth_2.0_Token_EndPoint>' , SECRET= '<authentication_key>')
        --CREDENTIAL should look something like this:
        --,CREDENTIAL=(IDENTITY= '92761aac-12a9-4ec3-89b8-7149aef4c35b@https://login.microsoftonline.com/72f714bf-86f1-41af-91ab-2d7cd011db47/oauth2/token', SECRET='juXi12sZ6gse]woKQNgqwSywYv]7A.M')
    )
    ```

> [!IMPORTANT]
>
> - OAuth 2.0 토큰 엔드포인트의 **V1** 버전 사용

## <a name="next-steps"></a>다음 단계

- 자세한 구문은 [COPY 문 문서](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)를 확인하세요.
- 모범 사례를 로드하려면 [데이터 로드 개요](./design-elt-data-loading.md#what-is-elt) 문서를 확인하세요.
