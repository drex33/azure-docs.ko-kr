---
title: Azure Synapse Analytics 작업 영역을 검사하는 방법
description: Azure Purview 데이터 카탈로그에서 Azure Synapse 작업 영역을 검사하는 방법을 알아봅니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: a74e88d72d1e7109b6e0acfa81485476eed9e00b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566524"
---
# <a name="register-and-scan-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 작업 영역 등록 및 검사

이 문서에서는 Azure Purview에서 Azure Synapse Analytics 작업 영역을 등록하고 작업 영역의 검사를 설정하는 방법을 간략하게 설명합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Synapse Analytics 작업 영역 검사는 작업 영역 내에서 전용 및 서버리스 SQL 데이터베이스의 메타데이터 및 스키마 캡처를 지원합니다. 또한 작업 영역 검사는 시스템 및 사용자 지정 분류 규칙에 따라 자동으로 데이터를 분류합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 데이터 원본을 등록하려면 먼저 Azure Purview 계정을 만듭니다. 자세한 내용은 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
- Azure Purview 데이터 원본 관리자여야 합니다.
- 다음 섹션에 설명된 대로 인증을 설정합니다.

## <a name="register-and-scan-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역 등록 및 검사

> [!IMPORTANT]
> 작업 영역을 성공적으로 검사하려면 다음 섹션에 설명된 대로 정확하게 단계를 수행하고 권한을 적용하세요.

### <a name="step-1-register-your-source"></a>**1단계**: 원본 등록

> [!NOTE]
> Azure Synapse 작업 영역에서 ‘읽기 권한자’ 이상의 역할이 있고 Azure Purview에서 ‘데이터 원본 관리자’이기도 한 사용자만 이 단계를 수행할 수 있습니다. 

새 Azure Synapse 원본을 데이터 카탈로그에 등록하려면 다음을 수행합니다.

1. Azure Purview 계정으로 이동합니다.
1. 왼쪽 창에서 **원본** 을 선택합니다.
1. **등록** 을 선택합니다.
1. **원본 등록** 에서 **Azure Synapse Analytics(여러 개)** 를 선택합니다.
1. **계속** 을 선택합니다.

   :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source.png" alt-text="Azure Synapse Analytics를 포함하여 Azure Purview의 원본 선택에 대한 스크린샷":::

1. **원본 등록(Azure Synapse Analytics)** 페이지에서 다음을 수행합니다.

    a. 데이터 카탈로그에 나열할 데이터 원본의 **이름** 을 입력합니다.  
    b. 필요한 경우 필터링할 **구독** 을 선택합니다.  
    다. **작업 영역 이름** 드롭다운 목록에서 현재 작업 중인 작업 영역을 선택합니다.  
    d. 엔드포인트 드롭다운 목록에서 작업 영역 선택에 따라 SQL 엔드포인트가 자동으로 채워집니다.  
    e. **컬렉션 선택** 드롭다운 목록에서 현재 작업 중인 컬렉션을 선택하거나 필요한 경우 새로 만듭니다.  
    f. **등록** 을 선택하여 데이터 원본 등록을 완료합니다.
    
    :::image type="content" source="media/register-scan-synapse-workspace/register-synapse-source-details.png" alt-text="Azure Synapse 원본에 대한 세부 정보를 입력하는 ‘원본 등록(Azure Synapse Analytics)’ 페이지 스크린샷":::


### <a name="step-2-apply-permissions-to-enumerate-the-contents-of-the-azure-synapse-workspace"></a>**2단계**: Azure Synapse 작업 영역의 콘텐츠를 열거하기 위한 권한 적용

#### <a name="set-up-authentication-for-enumerating-dedicated-sql-database-resources"></a>전용 SQL 데이터베이스 리소스를 열거하기 위한 인증 설정

1. Azure Portal에서 Azure Synapse 작업 영역 리소스로 이동합니다.  
1. 왼쪽 창에서  **액세스 제어(IAM)** 를 선택합니다. 

   > [!NOTE]
   > 리소스와 관련된 역할을 추가하려면 ‘소유자’ 또는 ‘사용자 액세스 관리자’여야 합니다. 
   
1. **추가** 단추를 선택합니다.   
1. **읽기 권한자** 역할을 설정하고 해당 MSI(관리 서비스 ID)를 나타내는 Azure Purview 계정 이름을 입력합니다.
1. **저장** 을 선택하여 역할 할당을 완료합니다.

> [!NOTE]
> Azure Purview 계정에서 여러 Azure Synapse 작업 영역을 등록하고 검사하려는 경우 리소스 그룹 또는 구독과 같은 상위 수준에서 역할을 할당할 수도 있습니다. 

#### <a name="set-up-authentication-for-enumerating-serverless-sql-database-resources"></a>서버리스 SQL 데이터베이스 리소스를 열거하기 위한 인증 설정

Purview에서 서버리스 SQL 데이터베이스 리소스를 열거할 수 있도록 하려면 Synapse 작업 영역, 연결된 스토리지, 서버리스 데이터베이스의 세 위치에서 인증을 설정해야 합니다. 다음은 세 위치 모드에 대해 권한을 설정하는 단계입니다.

1. Azure Portal에서 Azure Synapse 작업 영역 리소스로 이동합니다.  
1. 왼쪽 창에서  **액세스 제어(IAM)** 를 선택합니다. 

   > [!NOTE]
   > 리소스와 관련된 역할을 추가하려면 ‘소유자’ 또는 ‘사용자 액세스 관리자’여야 합니다. 
   
1. **추가** 단추를 선택합니다.   
1. **읽기 권한자** 역할을 설정하고 해당 MSI(관리 서비스 ID)를 나타내는 Azure Purview 계정 이름을 입력합니다.
1. **저장** 을 선택하여 역할 할당을 완료합니다.
1. Azure Portal에서 Azure Synapse 작업 영역이 있는 **리소스 그룹** 또는 **구독** 으로 이동합니다.
1. 왼쪽 창에서  **액세스 제어(IAM)** 를 선택합니다. 

   > [!NOTE]
   > **리소스 그룹** 또는 **구독** 필드에서 역할을 추가하려면 ‘소유자’ 또는 ‘사용자 액세스 관리자’여야 합니다.  

1. **추가** 단추를 선택합니다. 
1. **Storage Blob 데이터 읽기 권한자** 역할을 설정하고 **선택** 상자에 해당 MSI를 나타내는 Azure Purview 계정 이름을 입력합니다. 
1. **저장** 을 선택하여 역할 할당을 완료합니다.
1. Azure Synapse 작업 영역으로 이동하여 Synapse Studio를 엽니다.
1. 왼쪽 메뉴에서 **데이터** 탭을 선택합니다.
1. 데이터베이스 중 하나 옆에 있는 줄임표( **...** )를 선택하고 새 SQL 스크립트를 시작합니다.
1. 서버리스 SQL 데이터베이스에서 Azure Purview 계정 MSI(계정 이름으로 표시됨)를 추가합니다. 이렇게 하려면 SQL 스크립트에서 다음 명령을 실행합니다.
    ```sql
    CREATE LOGIN [PurviewAccountName] FROM EXTERNAL PROVIDER;
    ```

### <a name="step-3-apply-permissions-to-scan-the-contents-of-the-workspace"></a>**3단계**: 작업 영역의 콘텐츠를 검사하기 위한 권한 적용

다음 두 가지 방법 중 하나로 Azure Synapse 원본에 대한 인증을 설정할 수 있습니다.

- 관리 ID 사용
- 서비스 주체 사용

> [!IMPORTANT]
> 서버리스 데이터베이스에 대한 이러한 단계는 복제된 데이터베이스에 적용되지 **않습니다**. 현재 Synapse에서, Spark 데이터베이스에서 복제된 서버리스 데이터베이스는 읽기 전용입니다. 자세한 내용은 [여기](../synapse-analytics/sql/resources-self-help-sql-on-demand.md#operation-is-not-allowed-for-a-replicated-database)를 참조하세요.

> [!NOTE]
> 등록하고 검사하려는 Azure Synapse 작업 영역의 각 전용 SQL 데이터베이스에서 인증을 설정해야 합니다. 다음 섹션에 설명된 서버리스 SQL 데이터베이스에 대한 권한은 작업 영역 내 모든 데이터베이스에 적용됩니다. 즉, 인증을 한 번만 설정하면 됩니다.

#### <a name="use-a-managed-identity-for-dedicated-sql-databases"></a>전용 SQL 데이터베이스에 관리 ID 사용

1. Azure Synapse 작업 영역으로 이동합니다.
1. **데이터** 섹션으로 이동하고 전용 SQL 데이터베이스 중 하나를 찾습니다.
1. 데이터베이스 옆에 있는 줄임표( **...** )를 선택하고 새 SQL 스크립트를 시작합니다.

   > [!NOTE]
   > 다음 절차의 명령을 실행하려면 작업 영역에서 ‘Azure Synapse 관리자’여야 합니다. Azure Synapse Analytics 권한에 대한 자세한 내용은 [Azure Synapse 작업 영역에 대한 액세스 제어 설정](../synapse-analytics/security/how-to-set-up-access-control.md)을 참조하세요.

1. 전용 SQL 데이터베이스에서 Azure Purview 계정 MSI(계정 이름으로 표시됨)를 **db_datareader** 로 추가합니다. 이렇게 하려면 SQL 스크립트에서 다음 명령을 실행합니다.

    ```sql
    CREATE USER [PurviewAccountName] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [PurviewAccountName]
    GO
    ```
#### <a name="use-a-managed-identity-for-serverless-sql-databases"></a>서버리스 SQL 데이터베이스에 관리 ID 사용

1. Azure Synapse 작업 영역으로 이동합니다.
1. **데이터** 섹션으로 이동하고 **검사하려는 각 데이터베이스** 에 대해 다음 단계를 수행합니다.
1. 데이터베이스 옆에 있는 줄임표( **...** )를 선택하고 새 SQL 스크립트를 시작합니다.
1. 서버리스 SQL 데이터베이스에서 Azure Purview 계정 MSI(계정 이름으로 표시됨)를 **db_datareader** 로 추가합니다. 이렇게 하려면 SQL 스크립트에서 다음 명령을 실행합니다.
    ```sql
    CREATE USER [PurviewAccountName] FOR LOGIN [PurviewAccountName];
    ALTER ROLE db_datareader ADD MEMBER [PurviewAccountName]; 
    ```

#### <a name="use-a-service-principal-for-dedicated-sql-databases"></a>전용 SQL 데이터베이스에 서비스 주체 사용

> [!NOTE]
> 먼저 [Azure Purview에서 원본 인증을 위한 자격 증명](manage-credentials.md)의 지침에 따라 ‘서비스 주체’ 형식의 새 ‘자격 증명’을 설정해야 합니다. 

1. **Azure Synapse 작업 영역** 으로 이동합니다.
1. **데이터** 섹션으로 이동하고 전용 SQL 데이터베이스 중 하나를 찾습니다.
1. 데이터베이스 옆에 있는 줄임표( **...** )를 선택하고 새 SQL 스크립트를 시작합니다.
1. 전용 SQL 데이터베이스에서 **서비스 주체 ID** 를 **db_datareader** 로 추가합니다. 이렇게 하려면 SQL 스크립트에서 다음 명령을 실행합니다.

    ```sql
    CREATE USER [ServicePrincipalID] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [ServicePrincipalID]
    GO
    ```
> [!NOTE]
> Synapse 작업 영역의 모든 전용 SQL 데이터베이스에 대해 이전 단계를 반복합니다. 

#### <a name="use-a-service-principal-for-serverless-sql-databases"></a>서버리스 SQL 데이터베이스에 서비스 주체 사용

1. Azure Synapse 작업 영역으로 이동합니다.
1. **데이터** 섹션으로 이동하고 서버리스 SQL 데이터베이스 중 하나를 찾습니다.
1. 데이터베이스 옆에 있는 줄임표( **...** )를 선택하고 새 SQL 스크립트를 시작합니다.
1. 서버리스 SQL 데이터베이스에서 **서비스 주체 ID** 를 추가합니다. 이렇게 하려면 SQL 스크립트에서 다음 명령을 실행합니다.
    ```sql
    CREATE LOGIN [ServicePrincipalID] FROM EXTERNAL PROVIDER;
    ```
    
1. 검사하려는 각 서버리스 SQL 데이터베이스에서 **서비스 주체 ID** 를 **db_datareader** 로 추가합니다. 이렇게 하려면 SQL 스크립트에서 다음 명령을 실행합니다.
   ```sql
    CREATE USER [ServicePrincipalID] FOR LOGIN [ServicePrincipalID];
    ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalID]; 
    ```

### <a name="step-4-set-up-azure-synapse-workspace-firewall-access"></a>**4단계**: Azure Synapse 작업 영역 방화벽 액세스 설정

1. Azure Portal에서 Azure Synapse 작업 영역으로 이동합니다. 

1. 왼쪽 창에서 **방화벽** 을 선택합니다.

1. **Azure 서비스 및 리소스가 이 작업 영역에 액세스할 수 있도록 허용** 컨트롤에서 **켜기** 를 선택합니다.

1. **저장** 을 선택합니다.

### <a name="step-5-set-up-a-scan-on-the-workspace"></a>**5단계**: 작업 영역에서 검사 설정

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. Purview Studio의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 데이터 원본을 선택합니다.

1. **세부 정보 보기** 를 선택하고 **새 검사** 를 선택합니다. 또는 원본 타일에서 **빠른 작업 검사** 아이콘을 선택할 수 있습니다.

1. **검사** 세부 정보 창에서 **이름** 상자에 검사 이름을 입력합니다.
1. **유형** 드롭다운 목록에서 이 원본 내에서 검사할 리소스의 종류를 선택합니다. **SQL Database** 는 현재 Azure Synapse 작업 영역 내에서 지원되는 유일한 종류입니다.
   
    :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-setup.png" alt-text="Azure Synapse 원본 검사의 세부 정보 창 스크린샷":::

1. **자격 증명** 드롭다운 목록에서 데이터 원본 내의 리소스에 연결할 자격 증명을 선택합니다. 
  
1. 각 종류 내에서 이름으로 모든 리소스 또는 해당 하위 집합을 검사하도록 선택할 수 있습니다.

1.  **계속** 을 선택하여 진행합니다. 

1.  **Azure Synapse SQL** 형식의 **검사 규칙 집합** 을 선택합니다. 검사 규칙 집합을 인라인으로 만들 수도 있습니다.

1. 검사 트리거를 선택합니다. **매주/매월** 또는 **한 번** 실행되도록 예약할 수 있습니다.

1. 검사를 검토하고 **저장** 을 선택하여 설정을 완료합니다.   

#### <a name="view-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

1. 원본 섹션 아래 타일에서 **세부 정보 보기** 를 선택하여 원본 세부 정보를 확인합니다. 

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-source-details.png" alt-text="Azure Synapse Analytics 원본 세부 정보 페이지 스크린샷"::: 

1. **검사 세부 정보** 페이지로 이동하여 검사 실행 세부 정보를 확인합니다.

    * **상태 표시줄** 에는 하위 리소스의 실행 상태가 간단히 요약되어 표시됩니다. 상태는 작업 영역 수준 검사에 표시됩니다.  
    * 녹색은 성공한 검사 실행을 나타내고, 빨간색은 실패한 검사 실행을 나타내며, 회색은 검사 실행이 아직 진행 중임을 나타냅니다.  
    * 검사 실행을 클릭하여 검사 실행에 대한 세부 정보를 확인할 수 있습니다.

      :::image type="content" source="media/register-scan-synapse-workspace/synapse-scan-details.png" alt-text="Azure Synapse Analytics 검사 세부 정보 페이지 스크린샷" lightbox="media/register-scan-synapse-workspace/synapse-scan-details.png"::: 

    * **원본 세부 정보** 페이지 아래쪽에서 최근 실패한 검사 실행의 요약을 볼 수 있습니다. 다시 검사 실행을 클릭하여 검사 실행에 대한 세부 정보를 확인할 수 있습니다.

#### <a name="manage-your-scans"></a>검사 관리

검사를 편집, 삭제, 취소하려면 다음을 수행합니다.

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택한 다음, 관리하려는 데이터 원본을 선택합니다.

1. 관리하려는 검사를 선택한 다음, **편집** 을 선택합니다.

   - 검사를 삭제하려면 **삭제** 를 선택합니다.
   - 검사가 현재 실행 중인 경우 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)   
