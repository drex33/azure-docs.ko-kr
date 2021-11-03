---
title: Azure Portal을 사용하여 여러 테이블 증분 복사
description: 이 자습서에서는 SQL Server 데이터베이스의 여러 테이블에서 Azure SQL Database의 데이터베이스로 델타 데이터를 로드하는 파이프라인이 있는 Azure 데이터 팩터리를 만듭니다.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 07/05/2021
ms.openlocfilehash: e9149f25c6cc9c0aa45c62ef7b3100b8143e6c83
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031027"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-a-database-in-azure-sql-database-using-the-azure-portal"></a>Azure Portal을 사용하여 SQL Server의 여러 테이블에서 Azure SQL Database의 데이터베이스로 데이터를 증분 로드

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 SQL Server 데이터베이스의 여러 테이블에서 Azure SQL Database의 데이터베이스로 델타 데이터를 로드하는 파이프라인이 있는 Azure 데이터 팩터리를 만듭니다.    

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 원본 및 대상 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 통합 런타임을 만듭니다.
> * Integration Runtime을 설치합니다. 
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 워터마크 데이터 세트를 만듭니다.
> * 파이프라인을 만들고 실행하고 모니터링합니다.
> * 결과를 검토합니다.
> * 원본 테이블의 데이터를 추가 또는 업데이트합니다.
> * 파이프라인을 다시 실행하고 모니터링합니다.
> * 최종 결과를 검토합니다.

## <a name="overview"></a>개요
이 솔루션을 만드는 중요한 단계는 다음과 같습니다. 

1. **워터마크 열을 선택합니다**.
    
    원본 데이터 저장소에서 각 테이블에 대해 하나의 열을 선택합니다. 이 열은 모든 실행에 대해 새 레코드 또는 업데이트된 레코드를 식별하는 데 사용할 수 있습니다. 선택한 이 열의 데이터(예: last_modify_time 또는 ID)는 일반적으로 행을 만들거나 업데이트할 때 계속 증가합니다. 이 열의 최대 값은 워터마크로 사용됩니다.

1. **워터마크 값을 저장할 데이터 저장소를 준비합니다**.   
    
    이 자습서에서는 SQL 데이터베이스에 워터마크 값을 저장합니다.

1. **다음 작업을 사용하여 파이프라인을 만듭니다.** 
    
    a. 파이프라인에 매개 변수로 전달되는 원본 테이블 이름 목록을 반복하는 ForEach 작업을 만듭니다. 각 원본 테이블에 해당 테이블에 대한 델타 로드를 수행하는 다음 작업을 호출합니다.

    b. 두 가지 조회 작업을 만듭니다. 첫 번째 조회 작업을 사용하여 마지막 워터마크 값을 검색합니다. 두 번째 조회 작업을 사용하여 새 워터마크 값을 검색합니다. 이러한 워터마크 값은 복사 작업에 전달됩니다.

    다. 이전 워터마크 값보다 크고, 새 워터마크 값보다 작은 워터마크 열 값으로 원본 데이터 저장소의 행을 복사하는 복사 작업을 만듭니다. 그런 다음 원본 데이터 스토리지의 델타 데이터를 새 파일로 Azure Blob Storage에 복사합니다.

    d. 다음에 실행되는 파이프라인에 대한 워터마크 값을 업데이트하는 StoredProcedure 작업을 만듭니다. 

    대략적인 솔루션 다이어그램은 다음과 같습니다. 

    :::image type="content" source="media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png" alt-text="데이터 증분 로드":::


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소
* **SQL Server**. 이 자습서에서는 SQL Server 데이터베이스를 원본 데이터 저장소로 사용합니다. 
* **Azure SQL Database**. Azure SQL Database의 데이터베이스를 싱크 데이터 저장소로 사용합니다. SQL Database에 데이터베이스가 없는 경우 [Azure SQL Database에서 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md)에서 만드는 단계를 참조하세요. 

### <a name="create-source-tables-in-your-sql-server-database"></a>SQL Server 데이터베이스에 원본 테이블 만들기

1. SQL Server Management Studio를 열고 SQL Server 데이터베이스에 연결합니다.

1. **서버 탐색기** 에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.

1. 데이터베이스에 대해 다음 SQL 명령을 실행하여 `customer_table` 및 `project_table`(이)라는 테이블을 만듭니다.

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-database"></a>데이터베이스에 대상 테이블 만들기

1. SQL Server Management Studio를 열고 Azure SQL Database의 데이터베이스에 연결합니다.

1. **서버 탐색기** 에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.

1. 데이터베이스에 대해 다음 SQL 명령을 실행하여 `customer_table` 및 `project_table`(이)라는 테이블을 만듭니다.  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-your-database-to-store-the-high-watermark-value"></a>데이터베이스에 상위 워터마크 값을 저장할 또 다른 테이블 만들기

1. 데이터베이스에 대해 다음 SQL 명령을 실행하여 워터마크 값을 저장할 `watermarktable` 테이블을 만듭니다. 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. 두 원본 테이블의 초기 워터마크 값을 워터마크 테이블에 삽입합니다.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-your-database"></a>데이터베이스에 저장 프로시저 만들기

다음 명령을 실행하여 데이터베이스에 저장 프로시저를 만듭니다. 이 저장 프로시저는 파이프라인의 실행이 끝날 때마다 워터마크 값을 업데이트합니다. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-your-database"></a>데이터베이스에 데이터 형식 및 추가 저장 프로시저 만들기

다음 쿼리를 실행하여 데이터베이스에 두 개의 데이터 형식과 두 개의 저장 프로시저를 만듭니다. 원본 테이블의 데이터를 대상 테이블에 병합하는 데 사용됩니다.

여정을 쉽게 시작할 수 있도록 테이블 변수를 통해 델타 데이터를 전달하는 이러한 저장 프로시저를 직접 사용한 다음, 대상 저장소에 병합합니다. 테이블 변수에 "많은" 수(100개 초과)의 델타 행이 저장될 수 없으니 주의하세요.  

많은 수의 델타 행을 대상 저장소에 병합해야 하는 경우, 먼저 복사 작업을 사용하여 모든 델타 데이터를 대상 저장소의 임시 “준비” 테이블에 복사한 다음, 테이블 변수를 사용하지 않고 저장 프로시저를 직접 빌드하여 “준비” 테이블에서 “최종” 테이블로 병합하는 것이 좋습니다. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. 왼쪽 메뉴에서 **리소스 만들기** > **통합** > **Data Factory** 를 선택합니다. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="&quot;새로 만들기&quot; 창에서 Data Factory 선택":::

3. **새 데이터 팩터리** 페이지에서 **이름** 에 대해 **ADFMultiIncCopyTutorialDF** 를 입력합니다. 
 
   Azure Data Factory의 이름은 **전역적으로 고유** 해야 합니다. 다음 오류와 함께 빨간색 느낌표가 표시되면, 데이터 팩터리 이름(예: yournameADFIncCopyTutorialDF)을 변경하고 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. 데이터 팩터리를 만들려는 위치에 Azure **구독** 을 선택합니다. 
5. **리소스 그룹** 에 대해 다음 단계 중 하나를 수행합니다.
     
    - **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
    - **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다.   
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
6. **버전** 에 대해 **V2** 를 선택합니다.
7. 데이터 팩터리의 **위치** 를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.
8. **만들기** 를 클릭합니다.      
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure Data Factory Studio 열기 타일이 있는 Azure Data Factory 홈페이지":::

10. **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작합니다.

## <a name="create-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 만들기
프라이빗 네트워크(온-프레미스)의 데이터 저장소에서 Azure 데이터 저장소로 데이터를 이동할 때 온-프레미스 환경에 자체 호스팅 IR(통합 런타임)을 설치합니다. 자체 호스팅 IR은 프라이빗 네트워크와 Azure 간에 데이터를 이동합니다. 

1. Azure Data Factory UI의 홈페이지에서 맨 왼쪽 창에 있는 [관리 탭](./author-management-hub.md)을 선택합니다.

   :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="홈 페이지 관리 단추":::

1. 왼쪽 창에서 **통합 런타임** 을 선택한 다음, **+새로 만들기** 를 선택합니다.

   :::image type="content" source="media/doc-common-process/manage-new-integration-runtime.png" alt-text="Integration Runtime 만들기":::

1. **Integration Runtime 설정** 창에서 **데이터 이동을 수행하고 활동을 외부 계산으로 디스패치합니다** 를 선택하고, **계속** 을 클릭합니다. 

1. **자체 호스팅** 을 선택하고 **계속** 을 클릭합니다. 
1. **이름** 에 대해 **MySelfHostedIR** 을 입력하고 **만들기** 를 클릭합니다. 

1. 다음 위치에서 **이 컴퓨터에 대한 기본 설치를 시작하려면 여기를 클릭하세요** 를 클릭합니다. **옵션 1: 빠른 설치** 섹션 

   :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png" alt-text="빠른 설치 링크 클릭":::
1. **Integration Runtime(자체 호스팅) 빠른 설치** 창에서 **닫기** 를 클릭합니다. 

   :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png" alt-text="Integration Runtime 설정 - 성공":::
1. 웹 브라우저의 **Integration Runtime 설정** 창에서 **마침** 을 클릭합니다. 

 
1. 통합 런타임 목록에 **MySelfHostedIR** 이 표시되는지 확인합니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 섹션에서는 SQL Server 데이터베이스 및 Azure SQL Database의 데이터베이스에 연결된 서비스를 만듭니다. 

### <a name="create-the-sql-server-linked-service"></a>SQL Server에 연결된 서비스 만들기
이 단계에서는 SQL Server 데이터베이스를 데이터 팩터리에 연결합니다.

1. **연결** 창의 **통합 런타임** 탭에서 **연결된 서비스** 탭으로 전환하고 **+ 새로 만들기** 를 클릭합니다.

    :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="새 연결된 서비스.":::
1. **새 연결된 서비스** 창에서 **SQL Server** 를 선택하고 **계속** 을 클릭합니다. 

1. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름** 에 대해 **SqlServerLinkedService** 를 입력합니다. 
    1. **통합 런타임을 통해 연결** 에 대해 **MySelfHostedIR** 을 선택합니다. 이는 **중요한** 단계입니다. 기본 통합 런타임은 온-프레미스 데이터 저장소에 연결할 수 없습니다. 앞에서 만든 자체 호스팅 통합 런타임을 사용합니다. 
    1. **서버 이름** 의 경우 SQL Server 데이터베이스가 있는 컴퓨터의 이름을 입력합니다.
    1. **데이터베이스 이름** 의 경우 원본 데이터가 있는 SQL Server에 데이터베이스의 이름을 입력합니다. 테이블을 만들었고 필수 구성 요소의 일부로 이 데이터베이스에 데이터를 삽입했습니다. 
    1. **인증 유형** 의 경우 데이터베이스에 연결하는 데 사용하려는 **인증 유형** 을 선택합니다. 
    1. **사용자 이름** 의 경우 SQL Server 데이터베이스에 대한 액세스가 있는 사용자의 이름을 입력합니다. 슬래시 문자(`\`)를 사용자 계정 또는 서버 이름에 사용해야 할 경우 이스케이프 문자(`\`)를 사용합니다. 예제는 `mydomain\\myuser`입니다.
    1. **암호** 의 경우 사용자에 대한 **암호** 를 입력합니다. 
    1. 데이터 팩터리가 SQL Server 데이터베이스에 연결할 수 있는지를 테스트하려면 **연결 테스트** 를 클릭합니다. 연결이 성공할 때까지 모든 오류를 수정합니다. 
    1. 연결된 서비스를 저장하려면 **마침** 을 클릭합니다.

### <a name="create-the-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기
마지막 단계에서는 SQL Server 데이터베이스를 데이터 팩터리에 연결하기 위한 연결된 서비스를 만듭니다. 이 단계에서는 대상/싱크 데이터베이스를 데이터 팩터리에 연결합니다. 

1. **연결** 창의 **통합 런타임** 탭에서 **연결된 서비스** 탭으로 전환하고 **+ 새로 만들기** 를 클릭합니다.
1. **새 연결된 서비스** 창에서 **Azure SQL Database** 를 선택하고 **계속** 을 클릭합니다. 
1. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

    1. **이름** 에 대해 **AzureSqlDatabaseLinkedService** 를 입력합니다. 
    1. **서버 이름** 의 경우 드롭다운 목록에서 서버의 이름을 선택합니다. 
    1. **데이터베이스 이름** 의 경우 필수 구성 요소의 일부로 customer_table 및 project_table을 만든 데이터베이스를 선택합니다. 
    1. **사용자 이름** 의 경우 데이터베이스에 대한 액세스 권한이 있는 사용자의 이름을 입력합니다. 
    1. **암호** 의 경우 사용자에 대한 **암호** 를 입력합니다. 
    1. 데이터 팩터리가 SQL Server 데이터베이스에 연결할 수 있는지를 테스트하려면 **연결 테스트** 를 클릭합니다. 연결이 성공할 때까지 모든 오류를 수정합니다. 
    1. 연결된 서비스를 저장하려면 **마침** 을 클릭합니다.

1. 두 개의 연결된 서비스가 목록에 표시되는지 확인합니다. 
   
    :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png" alt-text="두 개의 연결된 서비스"::: 

## <a name="create-datasets"></a>데이터 세트 만들기
이 단계에서는 데이터 원본, 데이터 대상 및 워터마크를 저장할 위치를 나타내는 데이터 세트를 만듭니다.

### <a name="create-a-source-dataset"></a>원본 데이터 세트 만들기

1. 왼쪽 창에서 **+(더하기)** , **데이터 세트** 를 차례로 클릭합니다.

1. **새 데이터 세트** 창에서 **SQL Server** 를 선택하고 **계속** 을 클릭합니다. 

1. 웹 브라우저에 데이터 세트를 구성하기 위해 열린 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 세트가 표시됩니다. 아래쪽 속성 창의 **일반** 탭에서 **이름** 에 대해 **SourceDataset** 를 입력합니다. 

1. 속성 창에서 **연결** 탭으로 전환하고, **연결된 서비스** 에 대해 **SqlServerLinkedService** 를 선택합니다. 여기에서 테이블을 선택하지 마십시오. 파이프라인의 복사 작업은 전체 테이블을 로드하는 대신 SQL 쿼리를 사용하여 데이터를 로드합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png" alt-text="원본 데이터 세트 - 연결":::


### <a name="create-a-sink-dataset"></a>싱크 데이터 세트 만들기
1. 왼쪽 창에서 **+(더하기)** , **데이터 세트** 를 차례로 클릭합니다.

1. **새 데이터 세트** 창에서 **Azure SQL Database** 를 선택하고 **계속** 을 클릭합니다. 

1. 웹 브라우저에 데이터 세트를 구성하기 위해 열린 새 탭이 표시됩니다. 또한 트리 뷰에도 데이터 세트가 표시됩니다. 아래쪽 속성 창의 **일반** 탭에서 **이름** 에 대해 **SinkDataset** 를 입력합니다.

1. 속성 창에서 **매개 변수** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **Create/update 매개 변수** 섹션에서 **새로 만들기** 를 클릭합니다. 
    1. **이름** 에 대해 **SinkTableName** 을 입력하고 **형식** 에 대해 **문자열** 을 입력합니다. 이 데이터 세트는 매개 변수로 **SinkTableName** 을 사용합니다. SinkTableName 매개 변수는 런타임에 동적으로 파이프라인에 의해 설정됩니다. 파이프라인의 ForEach 작업은 테이블 이름 목록을 반복하고 반복할 때마다 테이블 이름을 이 데이터 세트에 전달합니다.
   
        :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png" alt-text="싱크 데이터 세트 - 속성":::
1. 속성 창에서 **연결** 탭으로 전환하고, **연결된 서비스** 에 대해 **AzureSqlDatabaseLinkedService** 를 선택합니다. **테이블** 속성에서 **동적 콘텐츠 추가** 를 클릭합니다.   
    
1. **동적 콘텐츠 추가** 창의 **매개 변수** 섹션에서 **SinkTableName** 를 선택 합니다. 
 
1. **마침** 을 클릭하면 “@dataset().SinkTableName”이 테이블 이름으로 표시됩니다.

   :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png" alt-text="싱크 데이터 세트 - 연결":::

### <a name="create-a-dataset-for-a-watermark"></a>워터마크에 대한 데이터 세트 만들기
이 단계에서는 상위 워터마크 값을 저장하기 위한 데이터 세트를 만듭니다. 

1. 왼쪽 창에서 **+(더하기)** , **데이터 세트** 를 차례로 클릭합니다.

1. **새 데이터 세트** 창에서 **Azure SQL Database** 를 선택하고 **계속** 을 클릭합니다. 

1. 아래쪽 속성 창의 **일반** 탭에서 **이름** 에 대해 **WatermarkDataset** 를 입력합니다.
1. **연결** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **연결된 서비스** 에 대해 **AzureSqlDatabaseLinkedService** 를 선택합니다.
    1. **테이블** 에 대해 **[dbo].[watermarktable]** 을 선택합니다.

        :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png" alt-text="워터마크 데이터 세트 - 연결":::

## <a name="create-a-pipeline"></a>파이프라인 만들기
파이프라인에서는 테이블 이름 목록을 매개 변수로 사용합니다. ForEach 작업은 테이블 이름 목록을 반복하고 다음 작업을 수행합니다. 

1. 조회 작업을 사용하여 이전 워터마크 값(초기 값 또는 마지막 반복에서 사용된 값)을 검색합니다.

1. 조회 작업을 사용하여 새로운 워터마크 값(원본 테이블의 워터마크 열의 최대값)을 검색합니다.

1. 복사 작업을 사용하여 원본 데이터베이스에서 대상 데이터베이스로 2개의 워터마크 값 사이에 데이터를 복사합니다.

1. StoredProcedure 작업을 사용하여 다음 반복의 첫 번째 단계에 사용할 이전 워터마크 값을 업데이트합니다. 

### <a name="create-the-pipeline"></a>파이프라인 만들기

1. 왼쪽 창에서 **+(더하기)** , **파이프라인** 을 차례로 클릭합니다.

1. **속성** 아래의 일반 패널에서 **이름** 에 **IncrementalCopyPipeline** 을 지정합니다. 그런 다음, 오른쪽 위 모서리에 있는 속성 아이콘을 클릭하여 패널을 축소합니다.  

1. **매개 변수** 탭에서 다음 단계를 수행합니다. 

    1. **+새로 만들기** 를 클릭합니다. 
    1. 매개 변수 **이름** 에 대해 **tableList** 를 입력합니다. 
    1. 매개 변수 **형식** 에 대해 **Array** 를 선택합니다.

1. **활동** 도구 상자에서 **반복 및 조건부** 를 펼치고, **ForEach** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. **속성** 창의 **일반** 탭에서 **IterateSQLTables** 를 입력합니다. 

1. **설정** 탭으로 전환하고 **항목** 에 대해 `@pipeline().parameters.tableList`를 입력합니다. ForEach 작업은 테이블 목록을 반복하고 증분 복사 작업을 수행합니다. 

    :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png" alt-text="ForEach 활동 - 설정":::

1. 아직 선택되지 않은 경우 파이프라인에서 **ForEach** 활동을 선택합니다. **편집(연필 아이콘)** 단추를 클릭합니다.

1. **활동** 도구 상자에서 **일반** 을 펼치고, **조회** 활동을 파이프라인 디자이너 화면으로 끌어서 놓고, **Name** 에 대해 **LookupOldWaterMarkActivity** 를 입력합니다.

1. **속성** 창에서 **설정** 탭으로 전환하고 다음 단계를 수행합니다. 

    1. **원본 데이터 세트** 에 대해 **WatermarkDataset** 을 선택합니다.
    1. **쿼리 사용** 에 대해 **쿼리** 를 선택합니다. 
    1. **쿼리** 에 대해 다음 SQL 쿼리를 입력합니다. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png" alt-text="첫 번째 조회 활동 - 설정":::
1. **활동** 도구 상자에서 **조회** 활동을 끌어서 놓고 **이름** 에 대해 **LookupNewWaterMarkActivity** 를 입력합니다.
        
1. **설정** 탭으로 전환합니다.

    1. **원본 데이터 세트** 에 대해 **SourceDataset** 를 선택합니다. 
    1. **쿼리 사용** 에 대해 **쿼리** 를 선택합니다.
    1. **쿼리** 에 대해 다음 SQL 쿼리를 입력합니다.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png" alt-text="두 번째 조회 활동 - 설정":::
1. **활동** 도구 상자에서 **복사** 활동을 끌어서 놓고 **이름** 에 대해 **IncrementalCopyActivity** 를 입력합니다. 

1. **조회** 활동을 하나씩 **복사** 활동에 연결합니다. 연결하려면 **조회** 활동에 연결된 **녹색** 상자를 끌어서 **복사** 활동에 놓습니다. 복사 활동의 테두리 색이 **파란색** 으로 변경되면 마우스 단추를 놓습니다.

    :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png" alt-text="조회 활동 및 복사 활동 연결":::
1. 파이프라인에서 **복사** 활동을 선택합니다. **속성** 창의 **원본** 탭으로 전환합니다. 

    1. **원본 데이터 세트** 에 대해 **SourceDataset** 를 선택합니다. 
    1. **쿼리 사용** 에 대해 **쿼리** 를 선택합니다. 
    1. **쿼리** 에 대해 다음 SQL 쿼리를 입력합니다.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png" alt-text="복사 활동 - 원본 설정":::
1. **싱크** 탭으로 전환하고, **싱크 데이터 세트** 에 대해 **SinkDataset** 을 선택합니다. 
        
1. 다음 단계를 수행합니다.

    1. **데이터 세트 속성** 에서 **SinkTableName** 매개 변수에 `@{item().TABLE_NAME}`을 입력합니다.
    1. **저장 프로시저 이름** 속성에 `@{item().StoredProcedureNameForMergeOperation}`을 입력합니다.
    1. **테이블 형식** 속성에 `@{item().TableType}`을 입력합니다.
    1. **테이블 형식 매개 변수 이름** 에 `@{item().TABLE_NAME}`을 입력합니다.

        :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png" alt-text="복사 활동 - 매개 변수":::
1. **저장 프로시저** 활동을 **활동** 도구 상자에서 파이프라인 디자이너 화면으로 끌어서 놓습니다. **복사** 활동을 **저장 프로시저** 활동에 연결합니다. 

1. 파이프라인에서 **저장 프로시저** 활동을 선택하고 **속성** 창의 **일반** 탭에서 **이름** 에 대해 **StoredProceduretoWriteWatermarkActivity** 를 입력합니다. 

1. **SQL 계정** 탭으로 전환하고, **연결된 서비스** 에 대해 **AzureSqlDatabaseLinkedService** 를 선택합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png" alt-text="저장 프로시저 활동 - SQL 계정":::
1. **저장 프로시저** 탭으로 전환하고 다음 단계를 수행합니다.

    1. **저장 프로시저 이름** 에 `[dbo].[usp_write_watermark]`를 입력합니다. 
    1. **가져오기 매개 변수** 를 선택합니다. 
    1. 매개 변수에 대해 다음 값을 지정합니다. 

        | Name | Type | 값 | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | String | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png" alt-text="저장 프로시저 활동 - 저장 프로시저 설정":::
1. 만든 엔터티를 Data Factory에 게시하려면 **모두 게시** 를 선택합니다. 

1. **게시됨** 메시지가 표시될 때까지 기다립니다. 알림을 보려면 **알림 표시** 링크를 클릭합니다. **X** 를 클릭하여 알림 창을 닫습니다.

 
## <a name="run-the-pipeline"></a>파이프라인 실행

1. 파이프라인에 대한 도구 모음에서 **트리거 추가**, **지금 트리거** 를 차례로 클릭합니다.     

1. **파이프라인 실행** 창에서 **tableList** 매개 변수에 대해 다음 값을 입력하고 **마침** 을 클릭합니다. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    :::image type="content" source="./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png" alt-text="파이프라인 실행 인수":::

## <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. **수동 트리거** 로 트리거된 파이프라인 실행이 표시됩니다. **파이프라인 이름** 열 아래의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.

1. 파이프라인 실행과 관련된 활동 실행을 보려면 **파이프라인 이름** 열에서 링크를 선택합니다. 활동 실행에 대한 자세한 내용을 보려면 **활동 이름** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 

1. 파이프라인 실행 보기로 돌아가려면 위쪽에 있는 **모든 파이프라인 실행** 을 선택합니다. 보기를 새로 고치려면 **새로 고침** 을 선택합니다.


## <a name="review-the-results"></a>결과 검토
SQL Server Management Studio에서 대상 SQL 데이터베이스에 대해 다음 쿼리를 실행하여 데이터가 원본 테이블에서 대상 테이블로 복사되었는지 확인합니다. 

**쿼리** 
```sql
select * from customer_table
```

**출력**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**쿼리**

```sql
select * from project_table
```

**출력**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**쿼리**

```sql
select * from watermarktable
```

**출력**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

두 테이블의 워터마크 값이 업데이트되었습니다. 

## <a name="add-more-data-to-the-source-tables"></a>원본 테이블에 데이터 추가

원본 SQL Server 데이터베이스에 대해 다음 쿼리를 실행하여 customer_table의 기존 행을 업데이트합니다. project_table에 새 행을 삽입합니다. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>파이프라인 다시 실행
1. 웹 브라우저 창에서 왼쪽의 **편집** 탭으로 전환합니다. 
1. 파이프라인에 대한 도구 모음에서 **트리거 추가**, **지금 트리거** 를 차례로 클릭합니다.   
1. **파이프라인 실행** 창에서 **tableList** 매개 변수에 대해 다음 값을 입력하고 **마침** 을 클릭합니다. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>파이프라인 다시 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. **수동 트리거** 로 트리거된 파이프라인 실행이 표시됩니다. **파이프라인 이름** 열 아래의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.

1. 파이프라인 실행과 관련된 활동 실행을 보려면 **파이프라인 이름** 열에서 링크를 선택합니다. 활동 실행에 대한 자세한 내용을 보려면 **활동 이름** 열에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 

1. 파이프라인 실행 보기로 돌아가려면 위쪽에 있는 **모든 파이프라인 실행** 을 선택합니다. 보기를 새로 고치려면 **새로 고침** 을 선택합니다.

## <a name="review-the-final-results"></a>최종 결과 검토
SQL Server Management Studio에서 대상 SQL 데이터베이스에 대해 다음 쿼리를 실행하여 업데이트된/새로운 데이터가 원본 테이블에서 대상 테이블로 복사되었는지 확인합니다. 

**쿼리** 
```sql
select * from customer_table
```

**출력**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

3번 **PersonID** 에 대한 **Name** 및 **LastModifytime** 의 새 값을 확인합니다. 

**쿼리**

```sql
select * from project_table
```

**출력**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

**NewProject** 항목이 project_table에 추가되었습니다. 

**쿼리**

```sql
select * from watermarktable
```

**출력**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

두 테이블의 워터마크 값이 업데이트되었습니다.
     
## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 원본 및 대상 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 자체 호스팅 IR(통합 런타임)을 만듭니다.
> * Integration Runtime을 설치합니다.
> * 연결된 서비스 만들기. 
> * 원본, 싱크 및 워터마크 데이터 세트를 만듭니다.
> * 파이프라인을 만들고 실행하고 모니터링합니다.
> * 결과를 검토합니다.
> * 원본 테이블의 데이터를 추가 또는 업데이트합니다.
> * 파이프라인을 다시 실행하고 모니터링합니다.
> * 최종 결과를 검토합니다.

Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[변경 내용 추적 기술을 사용하여 Azure SQL Database에서 Azure Blob Storage로 데이터 증분 로드](tutorial-incremental-copy-change-tracking-feature-portal.md)