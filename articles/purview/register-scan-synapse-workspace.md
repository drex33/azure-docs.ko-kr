---
title: Azure Synapse Analytics 작업 영역에 대 한 커넥트 및 관리
description: 이 가이드에서는 azure 부서의 범위에서 Azure Synapse Analytics 작업 영역에 연결 하 고 부서의 범위의 기능을 사용 하 여 Azure Synapse Analytics 작업 영역 원본을 검색 하 고 관리 하는 방법을 설명 합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: a5921e2af4445ad645ce48f6102c311c9478b9e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848024"
---
# <a name="connect-to-and-manage-azure-synapse-analytics-workspaces-in-azure-purview"></a>Azure 부서의 범위에서 Azure Synapse Analytics 작업 영역에 대 한 커넥트 및 관리

이 문서에서는 azure Synapse Analytics 작업 영역을 등록 하는 방법 및 azure 부서의 범위에서 azure Synapse Analytics 작업 영역을 인증 하 고 상호 작용 하는 방법을 설명 합니다. Azure 부서의 범위에 대 한 자세한 내용은 [소개 문서](overview.md)를 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

|**메타데이터 추출**|  **전체 검사**  |**증분 검사**|**범위 검사**|**분류**|**액세스 정책**|**계보**|
|---|---|---|---|---|---|---|
| [예](#register) | [예](#scan)| [예](#scan) | [예](#scan)| [예](#scan)| 예| [예-Synapse 파이프라인](how-to-lineage-azure-synapse-analytics.md)|


<!-- 4. Prerequisites
Required. Add any relevant/source-specific prerequisites for connecting with this source. Authentication/Registration should be covered by the sections below and does not need to be covered here.
-->

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 활성 [Purview 리소스](create-catalog-portal.md).

* 원본을 등록하고 Purview Studio에서 관리하려면 데이터 원본 관리자 및 데이터 읽기 권한자여야 합니다. 자세한 내용은 [Azure Purview 권한 페이지](catalog-permissions.md)를 참조하세요.

## <a name="register"></a>등록

이 섹션에서는 [부서의 범위 Studio](https://web.purview.azure.com/)를 사용 하 여 azure 부서의 범위에 Azure Synapse Analytics 작업 영역을 등록 하는 방법을 설명 합니다.

### <a name="authentication-for-registration"></a>등록 인증

Azure 부서의 범위의 *데이터 원본 관리자* 이기도 한 azure Synapse 작업 영역에서 적어도 *읽기 권한자* 역할을 가진 사용자만 azure Synapse 작업 영역을 등록할 수 있습니다.

### <a name="steps-to-register"></a>등록 단계

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

## <a name="scan"></a>검사

아래 단계에 따라 Azure Synapse Analytics 작업 영역을 검사 하 여 자산을 자동으로 식별 하 고 데이터를 분류 합니다. 일반적으로 검색 하는 방법에 대 한 자세한 내용은 [검색 및 수집 소개](concept-scans-and-ingestion.md)를 참조 하세요.

먼저 [전용](#authentication-for-enumerating-dedicated-sql-database-resources) 또는 [서버](#authentication-for-enumerating-serverless-sql-database-resources) 를 사용 하지 않는 리소스를 열거 하기 위한 인증을 설정 해야 합니다. 이렇게 하면 부서의 범위에서 작업 영역 자산을 열거 하 고 범위 검색을 수행할 수 있습니다.

그런 다음 [작업 영역의 콘텐츠를 검색 하는 권한을 적용](#apply-permissions-to-scan-the-contents-of-the-workspace)해야 합니다.

### <a name="authentication-for-enumerating-dedicated-sql-database-resources"></a>전용 SQL 데이터베이스 리소스를 열거 하기 위한 인증

1. Azure Portal에서 Azure Synapse 작업 영역 리소스로 이동합니다.  
1. 왼쪽 창에서  **액세스 제어(IAM)** 를 선택합니다.

   > [!NOTE]
   > 리소스와 관련된 역할을 추가하려면 ‘소유자’ 또는 ‘사용자 액세스 관리자’여야 합니다. 

1. **추가** 단추를 선택합니다.
1. **읽기 권한자** 역할을 설정하고 해당 MSI(관리 서비스 ID)를 나타내는 Azure Purview 계정 이름을 입력합니다.
1. **저장** 을 선택하여 역할 할당을 완료합니다.

> [!NOTE]
> Azure Purview 계정에서 여러 Azure Synapse 작업 영역을 등록하고 검사하려는 경우 리소스 그룹 또는 구독과 같은 상위 수준에서 역할을 할당할 수도 있습니다.

### <a name="authentication-for-enumerating-serverless-sql-database-resources"></a>서버를 사용 하지 않는 SQL 데이터베이스 리소스 열거를 위한 인증

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

### <a name="apply-permissions-to-scan-the-contents-of-the-workspace"></a>작업 영역의 콘텐츠를 검색 하는 권한 적용

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

#### <a name="grant-permission-to-use-credentials-for-external-tables"></a>외부 테이블에 대 한 자격 증명을 사용할 수 있는 권한 부여

Azure Synapse 작업 영역에 외부 테이블이 있는 경우 Azure 부서의 범위 관리 id에 외부 테이블 범위 자격 증명에 대 한 References 권한이 제공 되어야 합니다. Azure 부서의 범위는 References 권한을 사용 하 여 외부 테이블에서 데이터를 읽을 수 있습니다.

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::[scoped_credential] TO [PurviewAccountName];
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

### <a name="set-up-azure-synapse-workspace-firewall-access"></a>Azure Synapse 작업 영역 방화벽 액세스 설정

1. Azure Portal에서 Azure Synapse 작업 영역으로 이동합니다. 

1. 왼쪽 창에서 **방화벽** 을 선택합니다.

1. **Azure 서비스 및 리소스가 이 작업 영역에 액세스할 수 있도록 허용** 컨트롤에서 **켜기** 를 선택합니다.

1. **저장** 을 선택합니다.

### <a name="create-and-run-scan"></a>검사 만들기 및 실행

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. [부서의 범위 Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택 합니다.

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

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>다음 단계

이제 소스를 등록했으므로 아래 가이드에 따라 Purview 및 데이터에 대해 자세히 알아봅니다.

- [Azure Purview의 데이터 인사이트](concept-insights.md)
- [Azure Purview의 계보](catalog-lineage-user-guide.md)
- [Data Catalog 검색](how-to-search-catalog.md)
