---
title: Azure SSIS Integration Runtime을 위한 Azure Active Directory 인증 사용
description: 이 문서에서는 Azure Data Factory에 대해 지정된 시스템/사용자 할당 관리 ID와 함께 Azure Active Directory 인증을 사용하도록 설정하여 Azure-SSIS 통합 런타임을 만드는 방법을 설명합니다.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 10/22/2021
ms.openlocfilehash: c800193e027821df00167930d06d9c13ad8cb67d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843657"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime을 위한 Azure Active Directory 인증 사용

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서는 다음 목적으로 ADF(Azure Data Factory)에 대한 지정된 시스템/사용자 할당 관리 ID를 통해 Azure AD(Azure Active Directory) 인증을 사용하도록 설정하는 방법 및 이를 일반적인 인증 방법(SQL 인증 등) 대신 사용하는 방법을 보여 줍니다.

- 사용자를 대신하여 Azure SQL Database 서버/Managed Instance에서 SSISDB(SSIS 카탈로그 데이터베이스)를 프로비저닝하는 Azure-SSIS IR(통합 런타임)을 만듭니다.

- Azure-SSIS IR에서 SSIS 패키지를 실행할 때 다양한 Azure 리소스에 연결합니다.

ADF에 대한 관리 ID에 대한 자세한 내용은 [Data Factory용 관리 ID](./data-factory-service-identity.md)를 참조하세요.

> [!NOTE]
> - 이 시나리오에서 ADF에 대해 지정 된 시스템/사용자 할당 관리 id를 사용 하는 Azure AD 인증은 SSISDB를 프로 비전 하 고 SSISDB에 연결 하는 Azure-SSIS IR의 프로 비전 및 후속 시작 작업 에서만 사용 됩니다. SSIS 패키지 Azure-SSIS IR 실행의 경우 ssisdb 프로 비전 중에 생성 되는 완전히 관리 되는 계정 (*AzureIntegrationServiceDbo* 및 *AzureIntegrationServiceWorker*)을 사용 하 여 SQL 인증을 사용 하 여 패키지를 인출 하기 위해 ssisdb에 계속 연결 합니다.
>
> - SQL 인증을 사용하여 Azure-SSIS IR을 이미 만들었다면, 이번에는 PowerShell을 통해 Azure AD 인증을 사용하도록 IR을 재구성할 수 없습니다. 단, Azure Portal/ADF 앱에서는 할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-authentication-on-azure-sql-database"></a>Azure SQL Database에 대해 Azure AD 인증 사용

Azure SQL Database는 Azure AD 사용자로 데이터베이스 만들기를 지원합니다. 먼저 구성원으로 ADF에 대한 지정된 시스템/사용자 할당 관리 ID를 사용하여 Azure AD 그룹을 만들어야 합니다. 다음으로, Azure SQL Database 서버에 대한 Active Directory 관리자로 Azure AD 사용자를 설정한 다음, SSMS(SQL Server Management Studio)에서 해당 사용자를 사용하여 연결합니다. 마지막으로, Azure-SSIS IR에서 ADF에 대한 지정된 시스템/사용자 할당 관리 ID를 사용하여 사용자 대신 SSISDB를 만들도록 Azure AD 그룹을 나타내는 포함된 사용자를 만들어야 합니다.

### <a name="create-an-azure-ad-group-with-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-member"></a>구성원으로 ADF에 대한 지정된 시스템/사용자 할당 관리 ID를 사용하여 Azure AD 그룹을 만듭니다.

기존 Azure AD 그룹을 사용하거나 Azure AD PowerShell을 사용하여 새로 만들 수 있습니다.

1. [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) 모듈을 설치합니다.

2. `Connect-AzureAD`을 사용하여 로그인하고, 다음 cmdlet을 실행하여 그룹을 만들어서 이를 변수에 저장합니다.

   ```powershell
   $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                             -MailEnabled $false `
                             -SecurityEnabled $true `
                             -MailNickName "NotSet"
   ```

   결과는 다음 예제와 같으며 변수 값을 표시합니다.

   ```powershell
   $Group

   ObjectId DisplayName Description
   -------- ----------- -----------
   6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
   ```

3. ADF에 대해 지정된 시스템/사용자 할당 관리 ID를 그룹에 추가합니다. [Data Factory에 대한 관리 ID](./data-factory-service-identity.md) 문서에 따라 ADF에 대해 지정된 시스템/사용자 할당 관리 ID의 개체 ID를 가져올 수 있습니다(예: 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc. 하지만 이 용도로 애플리케이션 ID를 사용하지 않아야 함).

   ```powershell
   Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
   ```

   또한 나중에 그룹 구성원을 검사할 수도 있습니다.

   ```powershell
   Get-AzureAdGroupMember -ObjectId $Group.ObjectId
   ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database"></a>Azure SQL Database에 대한 Azure AD 인증 구성

다음 단계를 사용하여 [Azure SQL Database에 대한 Azure AD 인증을 구성하고 관리](../azure-sql/database/authentication-aad-configure.md)할 수 있습니다.

1. Azure Portal의 왼쪽 탐색 영역에서 **모든 서비스** -> **SQL 서버** 를 선택합니다.

2. Azure AD 인증을 사용하여 구성할 Azure SQL Database 서버를 선택합니다.

3. 블레이드의 **설정** 섹션에서 **Active Directory 관리자** 를 선택합니다.

4. 명령 모음에서 **관리자 설정** 을 선택합니다.

5. 서버 관리자로 만들 Azure AD 사용자 계정을 선택한 다음, **선택** 을 선택합니다.

6. 명령 모음에서 **저장** 을 선택합니다.

### <a name="create-a-contained-user-in-azure-sql-database-representing-the-azure-ad-group"></a>Azure AD 그룹을 나타내는 Azure SQL Database에서 포함된 사용자 만들기

이 다음 단계에서는 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다.

1. SSMS를 시작합니다.

2. **서버에 연결** 대화 상자에서 **서버 이름** 필드에 서버 이름을 입력합니다.

3. **인증** 필드에서 **Active Directory - MFA 지원을 통한 유니버설 인증** 을 선택합니다. 다른 두 가지 Active Directory 인증 유형을 사용할 수도 있습니다([Azure SQL Database에 대한 Azure AD 인증 구성 및 관리](../azure-sql/database/authentication-aad-configure.md) 참조).

4. **사용자 이름** 필드에 서버 관리자로 설정한 Azure AD 계정의 이름(예: testuser@xxxonline.com)을 입력합니다.

5. **커넥트** 를 선택 하 고 로그인 프로세스를 완료 합니다.

6. **개체 탐색기** 에서 **데이터베이스** -> **시스템 데이터베이스** 폴더를 확장합니다.

7. **마스터** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.

8. 쿼리 창에서 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행** 을 선택합니다.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   명령이 성공적으로 완료되고 그룹을 나타내는 포함된 사용자가 만들어집니다.

9. 쿼리 창을 지우고 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행** 을 선택합니다.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   명령이 성공적으로 완료되면 포함된 사용자에게 데이터베이스(SSISDB)를 만들 수 있는 기능이 부여됩니다.

10. SQL 인증을 사용 하 여 SSISDB를 만든 경우 Azure-SSIS IR에 액세스 하기 위해 Azure AD 인증을 사용 하도록 전환 하려면 먼저 **master** 데이터베이스에 대 한 사용 권한을 부여 하는 위의 단계가 성공적으로 완료 되었는지 확인 합니다. 그런 다음, **SSISDB** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.

    1. 쿼리 창에서 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행** 을 선택합니다.

       ```sql
       CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
       ```

       명령이 성공적으로 완료되고 그룹을 나타내는 포함된 사용자가 만들어집니다.

    2. 쿼리 창을 지우고 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행** 을 선택합니다.

       ```sql
       ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
       ```

       명령이 성공적으로 완료되면 포함된 사용자에게 SSISDB에 액세스할 수 있는 기능이 부여됩니다.

## <a name="enable-azure-ad-authentication-on-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대해 Azure AD 인증 사용

Azure SQL Managed Instance는 직접 ADF에 대한 지정된 시스템/사용자 할당 관리 ID로 데이터베이스 만들기를 지원합니다. ADF에 대한 지정된 시스템/사용자 할당 관리 ID를 Azure AD 그룹에 조인하거나 Azure SQL Managed Instance에서 해당 그룹을 나타내는 포함된 사용자를 만들 필요는 없습니다.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Azure SQL Database Managed Instance에 대한 Azure AD 인증 구성

[Azure SQL Managed Instance에 대한 Azure AD 관리자 프로비저닝](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)의 단계를 따릅니다.

### <a name="add-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-user-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance에서 ADF에 대해 지정된 시스템/사용자 할당 관리 ID를 사용자로 추가

이 다음 단계에서는 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다.

1. SSMS를 시작합니다.

2. **sysadmin** SQL Server 계정을 사용하여 Azure SQL Managed Instance에 연결합니다. 이는 Azure SQL Managed Instance에 대한 Azure AD 서버 보안 주체(로그인)에 대한 지원이 일반 공급되면 제거되는 일시적인 제한입니다. Azure AD 관리자 계정을 사용하여 로그인을 만들려는 경우, 다음 오류가 표시됩니다. *메시지 15247, 수준 16, 상태 1, 줄 1 사용자는 이 작업을 수행할 수 있는 권한* 이 없습니다.

3. **개체 탐색기** 에서 **데이터베이스** -> **시스템 데이터베이스** 폴더를 확장합니다.

4. **마스터** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.

5. 쿼리 창에서 다음 T-SQL 스크립트를 실행하여 ADF에 대한 지정된 시스템/사용자 할당 관리 ID를 사용자로 추가합니다.

   ```sql
   CREATE LOGIN [{your managed identity name}] FROM EXTERNAL PROVIDER
   ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your managed identity name}]
   ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your managed identity name}]
   ```

   ADF에 대한 시스템 관리 ID를 사용하는 경우 관리 ID 이름은 ADF 이름이어야 합니다. ADF에 대한 사용자 할당 관리 ID를 사용하는 경우 관리 ID 이름은 지정된 사용자 할당 관리 ID 이름이어야 합니다.
    
   명령이 성공적으로 완료되면 ADF에 대한 시스템/사용자 할당 관리 ID에 데이터베이스(SSISDB)를 만들 수 있는 기능이 부여됩니다.

6. SQL 인증을 사용 하 여 SSISDB를 만든 경우 Azure-SSIS IR에 액세스 하기 위해 Azure AD 인증을 사용 하도록 전환 하려면 먼저 **master** 데이터베이스에 대 한 사용 권한을 부여 하는 위의 단계가 성공적으로 완료 되었는지 확인 합니다. 그런 다음, **SSISDB** 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.

   1. 쿼리 창에서 다음 T-SQL 명령을 입력한 후, 도구 모음에서 **실행** 을 선택합니다.

      ```sql
      CREATE USER [{your managed identity name}] FOR LOGIN [{your managed identity name}] WITH DEFAULT_SCHEMA = dbo
      ALTER ROLE db_owner ADD MEMBER [{your managed identity name}]
      ```

      명령이 성공적으로 완료되면 ADF에 대한 시스템/사용자 할당 관리 ID에 SSISDB에 액세스하기 위한 기능이 부여됩니다.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure Portal/ADF 앱에서 Azure-SSIS IR 프로비전

Azure Portal/ADF 앱에서 Azure-SSIS IR를 프로비저닝할 때 **배포 설정** 페이지에서 **Azure SQL Database 서버/Managed Instance에서 호스트되는 SSIS 카탈로그(SSISDB)를 만들어 프로젝트/패키지/환경/실행 로그 저장** 확인란을 선택하고 **Data Factory에 대한 시스템 관리 ID에서 AAD 인증 사용** 또는 **Data Factory에 대한 사용자 할당 관리 ID에서 AAD 인증 사용** 확인란을 선택하여 SSISDB를 호스트하는 데이터베이스 서버에 액세스하기 위한 Azure-SSIS IR에 대한 Azure AD 인증 방법을 선택합니다.

자세한 내용은 [ADF에서 Azure-SSIS IR 만들기](./create-azure-ssis-integration-runtime.md)를 참조하세요.

## <a name="provision-azure-ssis-ir-with-powershell"></a>PowerShell을 사용하여 Azure-SSIS IR 프로비전

PowerShell을 사용하여 Azure-SSIS IR을 프로비전하려면 다음 작업을 수행합니다.

1. [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 모듈을 설치합니다.

2. 스크립트에서 `CatalogAdminCredential` 매개 변수를 설정하지 마세요. 예를 들면 다음과 같습니다.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                         -DataFactoryName $DataFactoryName `
                                         -Name $AzureSSISName `
                                         -Description $AzureSSISDescription `
                                         -Type Managed `
                                         -Location $AzureSSISLocation `
                                         -NodeSize $AzureSSISNodeSize `
                                         -NodeCount $AzureSSISNodeNumber `
                                         -Edition $AzureSSISEdition `
                                         -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                         -CatalogServerEndpoint $SSISDBServerEndpoint `
                                         -CatalogPricingTier $SSISDBPricingTier

   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName
   ```

## <a name="run-ssis-packages-using-azure-ad-authentication-with-the-specified-systemuser-assigned-managed-identity-for-your-adf"></a>ADF에 대해 지정된 시스템/사용자 할당 관리 ID에서 Azure AD 인증을 사용하여 SSIS 패키지 실행

Azure-SSIS IR에서 SSIS 패키지를 실행하는 경우 ADF에 대해 지정된 시스템/사용자 할당 관리 ID에서 Azure AD 인증을 사용하여 다양한 Azure 리소스에 연결할 수 있습니다. 현재, 다음 연결 관리자에서 ADF에 대해 지정된 시스템/사용자 할당 관리 ID를 사용하는 AAD 인증을 지원합니다.

- [OLEDB 연결 관리자](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET 연결 관리자](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage 연결 관리자](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
