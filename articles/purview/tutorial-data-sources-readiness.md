---
title: 대규모 데이터 원본 준비 상태 확인
description: 이 자습서에서는 Azure Purview에서 등록하고 검사하기 전에 Azure 데이터 원본의 준비 상태를 확인합니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 1eaca951a5abe43bda2621043321537cee2daa13
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213416"
---
# <a name="tutorial-check-data-source-readiness-at-scale"></a>자습서: 대규모 데이터 원본 준비 상태 확인

데이터 원본을 검사하려면 Azure Purview에서 해당 원본에 대한 액세스 권한이 필요합니다. 자격 증명을 사용하여 이 액세스 권한을 얻습니다. ‘자격 증명’은 Azure Purview가 등록된 데이터 원본에 인증하는 데 사용할 수 있는 인증 정보입니다. Azure Purview에 대한 자격 증명을 설정하는 몇 가지 방법은 다음과 같습니다. 
- Azure Purview 계정에 할당된 관리 ID
- Azure Key Vault에 저장된 비밀 
- 서비스 주체

2부로 구성된 이 자습서 시리즈는 Azure 구독 전체에서 다양한 Azure 데이터 원본에 대해 필요한 Azure 역할 할당 및 네트워크 액세스를 대규모로 확인하고 구성하는 데 도움이 됩니다. 그런 다음, Azure Purview에서 Azure 데이터 원본을 등록하고 검사할 수 있습니다. 

Azure Purview 계정을 배포한 후 Azure 데이터 원본을 등록하고 검사하기 전에 [Azure Purview 데이터 원본 준비 상태 검사 목록](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) 스크립트를 실행합니다. 

이 자습서 시리즈의 1부에서 수행하는 작업은 다음과 같습니다.

> [!div class="checklist"]
>
> * 데이터 원본을 찾고 데이터 원본 구독 목록을 준비합니다.
> * 준비 상태 검사 목록 스크립트를 실행하여 Azure의 데이터 원본에서 누락된 RBAC(역할 기반 액세스 제어) 또는 네트워크 구성을 찾습니다.
> * 출력 보고서에서 Azure Purview MSI(관리 ID)에 필요한 누락된 네트워크 구성 및 역할 할당을 검토합니다. 
> * 제안된 작업을 수행할 수 있도록 데이터 Azure 구독 소유자와 보고서를 공유합니다.

## <a name="prerequisites"></a>필수 조건

* 데이터 원본이 있는 Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 계정을 만듭니다.
* [Azure Purview 계정](create-catalog-portal.md).
* Azure SQL Database, Azure Synapse Analytics 또는 Azure SQL Managed Instance와 같은 데이터 원본이 있는 각 구독의 Azure Key Vault 리소스
* [Azure Purview 데이터 원본 준비 상태 검사 목록](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) 스크립트.

> [!NOTE]
> Azure Purview 데이터 원본 준비 상태 검사 목록은 Windows에서만 사용할 수 있습니다.
> 이 준비 상태 검사 목록 스크립트는 현재 Azure Purview MSI에서 지원됩니다.

## <a name="prepare-azure-subscriptions-list-for-data-sources"></a>데이터 원본에 대한 Azure 구독 목록 준비

스크립트를 실행하기 전에 4개의 열이 있는 .csv 파일(예: C:\temp\Subscriptions.csv)을 만듭니다.

|열 이름|설명|예제|
|----|----|----|
|`SubscriptionId`|데이터 원본에 대한 Azure 구독 ID입니다.|12345678-aaaa-bbbb-cccc-1234567890ab|
|`KeyVaultName`|데이터 원본 구독에 배포된 기존 키 자격 증명 모음의 이름입니다.|ContosoDevKeyVault|
|`SecretNameSQLUserName`|Azure AD 인증을 사용하여 Azure Synapse, Azure SQL Database 또는 Azure SQL Managed Instance에 로그인할 수 있는 Azure AD(Azure Active Directory) 사용자 이름이 포함된 기존 Azure Key Vault 비밀의 이름입니다.|ContosoDevSQLAdmin|
|`SecretNameSQLPassword`|Azure AD 인증을 사용하여 Azure Synapse, Azure SQL Database 또는 Azure SQL Managed Instance에 로그인할 수 있는 Azure AD 사용자 암호가 포함된 기존 Azure Key Vault 비밀의 이름입니다.|ContosoDevSQLPassword|
   

**샘플 .csv 파일:**
    
:::image type="content" source="./media/tutorial-data-sources-readiness/subscriptions-input.png" alt-text="샘플 구독 목록 스크린샷" lightbox="./media/tutorial-data-sources-readiness/subscriptions-input.png":::

> [!NOTE] 
> 필요한 경우 코드에서 파일 이름과 경로를 업데이트할 수 있습니다.



## <a name="run-the-script-and-install-the-required-powershell-modules"></a>스크립트를 실행하고 필요한 PowerShell 모듈을 설치합니다. 

Windows 컴퓨터에서 스크립트를 실행하려면 다음 단계를 수행합니다.

1. [Azure Purview 데이터 원본 준비 상태 검사 목록](https://github.com/Azure/Purview-Samples/tree/master/Data-Source-Readiness) 스크립트를 선택한 위치에 다운로드합니다.

2. 컴퓨터의 Windows 작업 표시줄에 있는 검색 상자에 **PowerShell** 을 입력합니다. 검색 목록에서 **Windows PowerShell** 을 길게 선택(또는 마우스 오른쪽 단추로 클릭)한 다음, **관리자 권한으로 실행** 을 선택합니다.

3. PowerShell 창에서 다음 명령을 입력합니다. `<path-to-script>`를 추출된 스크립트 파일의 폴더 경로로 바꿉니다.

   ```powershell
   dir -Path <path-to-script> | Unblock-File
   ```

4. 다음 명령을 입력하여 Azure cmdlet을 설치합니다.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```
6. ‘계속하려면 NuGet 공급자가 필요합니다.’라는 메시지가 표시되면 **Y** 를 입력하고 **Enter** 키를 선택합니다.

7. ‘신뢰할 수 없는 리포지토리’ 메시지가 표시되면 **A** 를 입력하고 **Enter** 키를 선택합니다.

5. 이전 단계를 반복하여 `Az.Synapse` 및 `AzureAD` 모듈을 설치합니다.

PowerShell이 필요한 모듈을 설치하는 데 최대 1분 정도 걸릴 수 있습니다.


## <a name="collect-other-data-needed-to-run-the-script"></a>스크립트 실행에 필요한 기타 데이터 수집

PowerShell 스크립트를 실행하여 데이터 원본 구독의 준비 상태를 확인하기 전에 스크립트에서 사용할 다음 인수의 값을 가져옵니다.

- `AzureDataType`: 다음 옵션 중 하나를 데이터 원본 형식으로 선택하여 구독 전체에서 데이터 형식에 대한 준비 상태를 확인합니다. 
    
    - `BlobStorage`

    - `AzureSQLMI`

    - `AzureSQLDB`
    
    - `ADLSGen2`
    
    - `ADLSGen1`
    
    - `Synapse`
    
    - `All`

- `PurviewAccount`: 기존 Azure Purview 계정 리소스 이름입니다.

- `PurviewSub`: Azure Purview 계정이 배포된 구독 ID입니다.

## <a name="verify-your-permissions"></a>권한 확인

사용자에게 다음 역할과 권한이 있는지 확인합니다.

역할 또는 권한 | 범위 |
|-------|--------|
| **글로벌 읽기 권한자** | Azure AD 테넌트 |
| **판독기** | Azure 데이터 원본이 있는 Azure 구독 |
| **판독기** | Azure Purview 계정이 생성된 구독 |
| **SQL 관리자**(Azure AD 인증) | Azure Synapse 전용 풀, Azure SQL Database 인스턴스, Azure SQL 관리형 인스턴스 |
| Azure Key Vault에 대한 액세스 권한 | 키 자격 증명 모음의 비밀 또는 Azure Key Vault 비밀 사용자 가져오기/나열 권한 |  


## <a name="run-the-client-side-readiness-script"></a>클라이언트 쪽 준비 상태 스크립트 실행

다음 단계를 완료하여 스크립트를 실행합니다.

1. 다음 명령을 사용하여 스크립트의 폴더로 이동합니다. `<path-to-script>`를 추출된 파일의 폴더 경로로 바꿉니다.

   ```powershell
   cd <path-to-script>
   ```

2. 다음 명령을 실행하여 로컬 컴퓨터의 실행 정책을 설정합니다. 실행 정책을 변경하라는 메시지가 표시되면 ‘모두 예’를 나타내는 **A** 를 입력합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

3. 다음 매개 변수를 사용하여 스크립트를 실행합니다. `DataType` , `PurviewName` 및 `SubscriptionID` 자리 표시자를 바꿉니다.

   ```powershell
   .\purview-data-sources-readiness-checklist.ps1 -AzureDataType <DataType> -PurviewAccount <PurviewName> -PurviewSub <SubscriptionID>
   ```

   명령을 실행하면 Azure Active Directory 자격 증명을 사용하여 Azure 및 Azure AD에 로그인하라는 팝업 창이 두 번 나타날 수 있습니다.


환경의 Azure 구독 및 리소스 수에 따라 보고서를 만드는 데 몇 분 정도 걸릴 수 있습니다. 

프로세스가 완료되면 Azure 구독 또는 리소스에서 검색된 누락된 구성을 보여 주는 출력 보고서를 검토합니다. 결과는 ‘통과’, ‘미통과’ 또는 ‘인식’으로 표시될 수 있습니다.   조직의 해당 구독 관리자와 결과를 공유하여 필요한 설정을 구성할 수 있습니다.


## <a name="more-information"></a>자세한 정보

### <a name="what-data-sources-are-supported-by-the-script"></a>스크립트에서 지원하는 데이터 원본은 무엇인가요?

현재 스크립트에서 지원하는 데이터 원본은 다음과 같습니다.

- Azure Blob Storage(BlobStorage)
- Azure Data Lake Storage Gen2(ADLSGen2)
- Azure Data Lake Storage Gen1(ADLSGen1)
- Azure SQL Database(AzureSQLDB)
- Azure SQL Managed Instance(AzureSQLMI)
- Azure Synapse(Synapse) 전용 풀

스크립트를 실행할 때 이러한 데이터 원본 중 일부나 전체를 입력 매개 변수로 선택할 수 있습니다.

### <a name="what-checks-are-included-in-the-results"></a>결과에 포함되는 검사는 무엇인가요?

#### <a name="azure-blob-storage-blobstorage"></a>Azure Blob Storage(BlobStorage)

- RBAC. 선택한 범위 아래의 각 구독에서 Azure Purview MSI에 **Storage Blob 데이터 읽기 권한자** 역할이 할당되었는지 확인합니다.
- RBAC. 선택한 범위에서 Azure Purview MSI에 **읽기 권한자** 역할이 할당되었는지 확인합니다.
- 서비스 엔드포인트입니다. 서비스 엔드포인트가 켜져 있는지 확인하고, **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 이 사용하도록 설정되었는지 확인합니다.
- 네트워킹: 스토리지용 프라이빗 엔드포인트가 생성되어 Blob Storage에 사용하도록 설정되었는지 확인합니다.

#### <a name="azure-data-lake-storage-gen2-adlsgen2"></a>Azure Data Lake Storage Gen2(ADLSGen2)

- RBAC. 선택한 범위 아래의 각 구독에서 Azure Purview MSI에 **Storage Blob 데이터 읽기 권한자** 역할이 할당되었는지 확인합니다.
- RBAC. 선택한 범위에서 Azure Purview MSI에 **읽기 권한자** 역할이 할당되었는지 확인합니다.
- 서비스 엔드포인트입니다. 서비스 엔드포인트가 켜져 있는지 확인하고, **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 이 사용하도록 설정되었는지 확인합니다.
- 네트워킹: 스토리지용 프라이빗 엔드포인트가 생성되어 Blob Storage에 사용하도록 설정되었는지 확인합니다.

#### <a name="azure-data-lake-storage-gen1-adlsgen1"></a>Azure Data Lake Storage Gen1(ADLSGen1)

- 네트워킹 서비스 엔드포인트가 켜져 있는지 확인하고, **모든 Azure 서비스가 이 Data Lake Storage Gen1 계정에 액세스하도록 허용** 이 사용하도록 설정되었는지 확인합니다.
- 사용 권한. Azure Purview MSI에 읽기/실행 권한이 있는지 확인합니다.

#### <a name="azure-sql-database-azuresqldb"></a>Azure SQL Database(AzureSQLDB)

- SQL Server 인스턴스:
  - Network. 퍼블릭 엔드포인트 또는 프라이빗 엔드포인트가 사용하도록 설정되었는지 확인합니다.
  - 방화벽. **Azure 서비스와 리소스가 이 서버에 액세스하도록 허용** 이 사용하도록 설정되었는지 확인합니다.
  - Azure AD 관리. Azure SQL Server에 Azure AD 인증이 있는지 확인합니다.
  - Azure AD 관리. Azure SQL Server의 Azure AD 관리 사용자 또는 그룹을 채웁니다.

- SQL 데이터베이스:
  - SQL 역할. Azure Purview MSI에 **db_datareader** 역할이 할당되었는지 확인합니다.

#### <a name="azure-sql-managed-instance-azuresqlmi"></a>Azure SQL Managed Instance(AzureSQLMI)

- SQL Managed Instance 서버:
  - Network. 퍼블릭 엔드포인트 또는 프라이빗 엔드포인트가 사용하도록 설정되었는지 확인합니다.
  - ProxyOverride. Azure SQL Managed Instance가 프록시 또는 리디렉션으로 구성되어 있는지 확인합니다.
  - 네트워킹 NSG에 필수 포트에서 AzureCloud를 허용하는 인바운드 규칙이 있는지 확인합니다.  
    - 리디렉션: 1433 및 11000~11999  
    또는
    - 프록시: 3342
  - Azure AD 관리. Azure SQL Server에 Azure AD 인증이 있는지 확인합니다.
  - Azure AD 관리. Azure SQL Server의 Azure AD 관리 사용자 또는 그룹을 채웁니다.

- SQL 데이터베이스:
  - SQL 역할. Azure Purview MSI에 **db_datareader** 역할이 할당되었는지 확인합니다.

#### <a name="azure-synapse-synapse-dedicated-pool"></a>Azure Synapse(Synapse) 전용 풀

- RBAC. 선택한 범위 아래의 각 구독에서 Azure Purview MSI에 **Storage Blob 데이터 읽기 권한자** 역할이 할당되었는지 확인합니다.
- RBAC. 선택한 범위에서 Azure Purview MSI에 **읽기 권한자** 역할이 할당되었는지 확인합니다.
- SQL Server 인스턴스(전용 풀):
  - 네트워크: 퍼블릭 엔드포인트 또는 프라이빗 엔드포인트가 사용하도록 설정되었는지 확인합니다.
  - 방화벽: **Azure 서비스와 리소스가 이 서버에 액세스하도록 허용** 이 사용하도록 설정되었는지 확인합니다.
  - Azure AD 관리: Azure SQL Server에 Azure AD 인증이 있는지 확인합니다.
  - Azure AD 관리: Azure SQL Server의 Azure AD 관리 사용자 또는 그룹을 채웁니다.

- SQL 데이터베이스:
  - SQL 역할. Azure Purview MSI에 **db_datareader** 역할이 할당되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.
> [!div class="checklist"]
>
> * Azure Purview 준비 상태 검사 목록을 실행하여 Azure Purview에서 Azure 구독을 등록하고 검사하기 전에 Azure 구독에 누락된 구성이 있는지 대규모로 확인합니다.

다음 자습서로 이동하여 Azure 데이터 원본에서 Azure Purview에 필요한 액세스를 식별하고 필요한 인증 및 네트워크 규칙을 설정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [대규모 Azure Purview MSI의 데이터 원본에 대한 액세스 구성](tutorial-msi-configuration.md)
