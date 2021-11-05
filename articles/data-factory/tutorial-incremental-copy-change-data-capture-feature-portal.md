---
title: 변경 데이터 캡처를 사용하여 데이터 증분 복사
description: 이 자습서에서는 Azure SQL Managed Instance 데이터베이스의 테이블에서 Azure Storage로 델타 데이터를 증분 복사해 Azure Data Factory 파이프라인을 만듭니다.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 07/05/2021
ms.openlocfilehash: 1c0e6e052b9e65f02ab57a7a2c165c9ba67be0a8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031046"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>CDC(변경 데이터 캡처)를 사용하여 Azure SQL Managed Instance에서 Azure Storage로 데이터 증분 로드

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 원본 Azure SQL Managed Instance 데이터베이스의 **CDC(변경 데이터 캡처)** 정보를 기반으로 Azure Blob Storage에 델타 데이터를 로드하는 파이프라인이 있는 Azure 데이터 팩터리를 만듭니다.  

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 원본 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 연결된 서비스 만들기.
> * 원본 및 싱크 데이터 세트를 만듭니다.
> * 파이프라인을 만들고 디버그하고 실행하여 변경된 데이터를 확인합니다.
> * 원본 테이블의 데이터를 수정합니다.
> * 증분 복사 파이프라인을 생성, 실행 및 모니터링합니다.

## <a name="overview"></a>개요
Azure SQL MI(Managed Instances) 및 SQL Server 같은 데이터 저장소에서 지원되는 변경 데이터 캡처 기술은 변경된 데이터를 식별하는 데 사용할 수 있습니다.  이 자습서는 SQL 변경 데이터 캡처 기술을 통해 Azure Data Factory를 사용하여 Azure SQL Managed Instance에서 Azure Blob Storage로 델타 데이터를 증분 로드하는 방법을 설명합니다.  SQL 변경 데이터 캡처 기술에 대한 자세한 내용은 [SQL Server의 변경 데이터 캡처](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)를 참조하세요.

## <a name="end-to-end-workflow"></a>엔드투엔드 워크플로
다음은 변경 데이터 캡처 기술을 사용하여 데이터를 증분 로드하는 일반적인 엔드투엔드 워크플로 단계입니다.

> [!NOTE]
> Azure SQL MI 및 SQL Server는 모두 변경 데이터 캡처 기술을 지원합니다. 이 자습서는 Azure SQL Managed Instance를 원본 데이터 저장소로 사용합니다. 온-프레미스 SQL Server를 사용할 수도 있습니다.

## <a name="high-level-solution"></a>대략적인 솔루션
이 자습서에서는 다음 작업을 수행하는 파이프라인을 만듭니다.  

   1. SQL Database CDC 테이블에서 변경된 레코드 수를 계산하고 IF 조건 작업에 전달하는 **조회 작업** 을 만듭니다.
   2. 변경된 레코드가 있는지 여부를 확인하기 위해 **If 조건** 을 만들고, 해당하는 경우 복사 작업을 호출합니다.
   3. **복사 작업** 만들어서 CDC 테이블 사이의 삽입/업데이트/삭제된 데이터를 Azure Blob Storage에 복사합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소
* **Azure SQL Database Managed Instance** 데이터베이스를 **원본** 데이터 저장소로 사용합니다. Azure SQL Database Managed Instance가 없는 경우 [Azure SQL Database Managed Instance 만들기](../azure-sql/managed-instance/instance-create-quickstart.md) 문서를 참조하세요.
* **Azure Storage 계정**. Blob Storage를 **싱크** 데이터 스토리지로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [스토리지 계정 만들기](../storage/common/storage-account-create.md) 문서를 참조하세요. **원시** 라는 컨테이너를 만들려면: 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Azure SQL Database에 데이터 원본 테이블 만들기

1. **SQL Server Management Studio** 를 시작하고 Azure SQL Managed Instances 서버에 연결합니다.
2. **서버 탐색기** 에서 **데이터베이스** 를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.
3. Azure SQL Managed Instances 데이터베이스에 대해 다음 SQL 명령을 실행하여 데이터 원본 저장소로 `customers`이라는 테이블을 만듭니다.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. 다음 SQL 쿼리를 실행하여 데이터베이스 및 원본 테이블(고객)에서 **변경 데이터 캡처** 메커니즘을 사용하도록 설정합니다.

    > [!NOTE]
    > - &lt;원본 스키마 이름&gt;을 고객 테이블이 있는 Azure SQL MI의 스키마로 바꿉니다.
    > - 변경 데이터 캡처는 추적 중인 테이블을 변경하는 트랜잭션의 일부로써 어떤 작업도 수행하지 않습니다. 대신 삽입, 업데이트 및 삭제 작업이 트랜잭션 로그에 기록됩니다. 변경 테이블에 보관되어 있는 데이터는 주기적이며 체계적으로 정리하지 않으면 관리할 수 없을 정도로 커집니다. 자세한 내용은 [데이터베이스에 대한 변경 데이터 캡처 사용](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server#enable-change-data-capture-for-a-database)을 참조하세요.

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. 다음 명령을 실행하여 고객 테이블에 데이터를 삽입합니다.

    ```sql
     insert into customers 
         (customer_id, first_name, last_name, email, city) 
     values 
         (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
         (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > 변경 데이터 캡처를 사용하도록 설정하기 전에는 테이블에 대한 기록 변경 내용이 캡처되지 않습니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터 + 분석** > **Data Factory** 를 차례로 선택합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png" alt-text="&quot;새로 만들기&quot; 창에서 Data Factory 선택":::

2. **새 데이터 팩터리** 페이지에서 **이름** 에 대해 **ADFTutorialDataFactory** 를 입력합니다.

     :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png" alt-text="새 데이터 팩터리 페이지":::

   Azure Data Factory의 이름은 **전역적으로 고유** 해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameADFTutorialDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.

    *데이터 팩터리 이름 "ADFTutorialDataFactory"를 사용할 수 없습니다.*
3. **버전** 에 대해 **V2** 를 선택합니다.
4. 데이터 팩터리를 만들려는 위치에 Azure **구독** 을 선택합니다.
5. **리소스 그룹** 에 대해 다음 단계 중 하나를 수행합니다.

   1. **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
   2. **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
5. 데이터 팩터리의 **위치** 를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.
6. **GIT 사용** 을 선택 취소합니다.     
7. **만들기** 를 클릭합니다.
8. 배포가 완료되면 **리소스로 이동** 을 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png" alt-text="스크린샷은 배포가 완료되었음을 나타내는 메시지와 리소스로 이동하는 옵션을 보여줍니다.":::
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.

      :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure Data Factory Studio 열기 타일이 있는 Azure Data Factory 홈페이지":::

10. **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택하여 별도의 탭에서 Azure Data Factory UI(사용자 인터페이스)를 시작합니다.
11. 다음 이미지와 같이 홈페이지에서 왼쪽 패널의 **관리** 탭으로 전환합니다.

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="관리 단추 스크린샷":::

## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 섹션에서는 Azure Storage 계정과 Azure SQL MI에 연결된 서비스를 만듭니다.

### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스를 만듭니다.
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다.

1. **연결**, **+ 새로 만들기** 를 차례로 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png" alt-text="새 연결 단추":::
2. **새 연결된 서비스** 창에서 **Azure Blob Storage** 를 선택하고 **계속** 을 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png" alt-text="Azure Blob Storage 선택":::
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

   1. **이름** 에 대해 **AzureStorageLinkedService** 를 입력합니다.
   2. **스토리지 계정 이름** 에 대해 Azure Storage 계정을 선택합니다.
   3. **저장** 을 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png" alt-text="Azure Storage 계정 설정":::


### <a name="create-azure-sql-mi-database-linked-service"></a>Azure SQL MI Database 연결 서비스를 만듭니다.
이 단계에서는 Azure SQL MI 데이터베이스를 데이터 팩터리에 연결합니다.

> [!NOTE]
> SQL MI를 사용하는 경우 공용 vs 프라이빗 엔드포인트를 통해 액세스하는 방법에 대한 자세한 내용은 [여기](./connector-azure-sql-managed-instance.md#prerequisites)를 참조하세요. 프라이빗 엔드포인트를 사용하는 경우 자체 호스팅 통합 런타임을 사용하여 이 파이프라인을 실행해야 합니다. VM 또는 VNet 시나리오에서 온-프레미스를 실행하는 SQL Server에도 동일하게 적용됩니다.

1. **연결**, **+ 새로 만들기** 를 차례로 클릭합니다.
2. **새 연결된 서비스** 창에서 **Azure SQL Database Managed Instance** 를 선택하고 **계속** 을 클릭합니다.
3. **새 연결된 서비스** 창에서 다음 단계를 수행합니다.

   1. **이름** 필드에 **AzureSqlMI1** 을 입력합니다.
   2. **서버 이름** 필드에 대한 SQL 서버를 선택합니다.
   4. **데이터베이스 이름** 필드에 대한 SQL 데이터베이스를 선택합니다.
   5. **사용자 이름** 필드에 대해 사용자의 이름을 입력합니다.
   6. **암호** 필드에 대해 사용자의 암호를 입력합니다.
   7. **연결 테스트** 를 클릭하여 연결을 테스트합니다.
   8. **저장** 을 클릭하여 연결된 서비스를 저장합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png" alt-text="Azure SQL MI Database 연결 서비스 설정":::

## <a name="create-datasets"></a>데이터 세트 만들기
이 단계에서는 데이터 원본 및 데이터 대상을 나타내는 데이터 세트를 만듭니다.

### <a name="create-a-dataset-to-represent-source-data"></a>원본 데이터를 나타내는 데이터 세트 만들기
이 단계에서는 원본 데이터를 나타내는 데이터 세트를 만듭니다.

1. 트리 뷰에서 **+(더하기)** , **데이터 세트** 를 차례로 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png" alt-text="새 데이터 세트 메뉴":::
2. **Azure SQL Database Managed Instance** 를 선택하고 **계속** 을 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png" alt-text="원본 데이터 세트 유형 - Azure SQL Database":::
   
3. **Set 속성** 탭에서 데이터 세트 이름 및 연결 정보를 설정합니다.
 
   1. **연결된 서비스** 에 대해 **AzureSqlMI1** 를 선택합니다.
   2. **테이블 이름** 에 대한 **[dbo].[dbo_customers_CT]** 를 선택합니다.  참고: CDC를 고객 테이블에서 실행할 때 이 테이블이 자동으로 생성되었습니다. 변경된 데이터는 이 테이블에서 직접 쿼리하지 않지만[CDC 함수](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql)를 통해 추출됩니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png" alt-text="원본 연결":::

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>싱크 데이터 저장소에 복사된 데이터를 나타내는 데이터 세트 만들기
이 단계에서는 원본 데이터 저장소에서 복사된 데이터를 나타내는 데이터 세트를 만듭니다. 필수 구성 요소의 일부로써 데이터 레이크 컨테이너를 Azure Blob Storage에 만들었습니다. 아직 없는 경우 컨테이너를 만들거나 기존 컨테이너의 이름으로 설정합니다. 이 자습서에서 출력 파일 이름은 나중에 구성될 트리거 시간을 사용하여 동적으로 생성됩니다.

1. 트리 뷰에서 **+(더하기)** , **데이터 세트** 를 차례로 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png" alt-text="새 데이터 세트 메뉴":::
2. **Azure Blob Storage** 를 선택하고 **선택** 을 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png" alt-text="싱크 데이터 세트 유형 - Azure Blob Storage":::
3. **DelimitedText** 를 선택하고 **계속** 을 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png" alt-text="싱크 데이터 세트 형식 - DelimitedText":::
4. **Set 속성** 탭에서 데이터 세트 이름 및 연결 정보를 설정합니다.

   1. **연결된 서비스** 에 대해 **AzureStorageLinkedService** 를 선택합니다.
   2. **filePath** 의 **컨테이너** 부분에 **원시** 를 입력합니다.
   3. **첫 번째 행을 헤더** 로 사용하십시오.
   4. **확인** 을 클릭합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png" alt-text="싱크 데이터 세트 - 연결":::

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>변경된 데이터를 복사하는 파이프라인 만들기
이 단계에서는 **조회 작업** 을 사용하여 변경 테이블에 있는 변경된 레코드의 수를 먼저 확인하는 파이프라인을 만듭니다. IF 조건 작업은 변경된 레코드 수가 0보다 큰지 여부를 확인하고 **복사 작업** 을 실행하여 Azure SQL Database에서 삽입/업데이트/삭제된 데이터를 Azure Blob Storage로 복사합니다. 마지막으로, 연속 창 트리거가 구성되고 시작 및 종료 시간이 작업에 시작 및 종료 창 매개 변수로 전달됩니다. 

1. Data Factory UI에서 **편집** 탭으로 전환합니다. 왼쪽 창에서 **+(더하기)** , **파이프라인** 을 차례로 클릭합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png" alt-text="새 파이프라인 메뉴":::
2. 파이프라인을 구성하기 위한 새 탭이 표시됩니다. 또한 트리 뷰에도 파이프라인이 표시됩니다. **속성** 창에서 파이프라인 이름을 **IncrementalCopyPipeline** 으로 변경합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png" alt-text="파이프라인 이름":::
3. **활동** 도구 상자에서 **일반** 을 펼치고, **조회** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 활동 이름을 **GetChangeCount** 로 설정합니다. 이 활동은 지정된 시간 창에 대한 변경 테이블의 레코드 수를 가져옵니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png" alt-text="조회 활동 - 이름":::
4. **속성** 창의 **설정** 으로 전환합니다.

   1. **원본 데이터 세트** 필드에 대해 SQL MI 데이터 세트 이름을 지정합니다.

   2. 쿼리 옵션을 선택하고 쿼리 상자에 다음을 입력합니다.
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```

   3. **첫 행만** 사용

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png" alt-text="조회 활동 - 설정":::

5. **데이터 미리 보기** 단추를 클릭하여 조회 작업에서 유효한 출력을 가져왔는지 확인합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png" alt-text="조회 작업 - 미리 보기":::
6. **활동** 도구 상자에서 **반복 및 조건부** 를 펼치고, **If 조건부** 작업을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 작업 이름을 **HasChangedRows** 로 설정합니다. 

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png" alt-text="If 조건 작업 - 이름":::
7. **속성** 창에서 **작업** 으로 전환합니다.

   1. 다음 **식** 을 입력합니다.
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. 연필 아이콘을 클릭하여 True 조건을 편집합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png" alt-text="If 조건 작업 - 설정":::

   3. **활동** 도구 상자에서 **일반** 을 펼치고, **대기** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 이 작업은 If 조건을 디버깅하기 위한 임시 작업이며 자습서의 뒷부분에서 변경됩니다. 

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png" alt-text="If 조건 True - 대기":::

   4. IncrementalCopyPipeline 이동 경로를 클릭하여 주 파이프라인으로 돌아갑니다.

8. **디버그** 모드에서 파이프라인을 실행하여 파이프라인이 성공적으로 실행되는지 확인합니다. 

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png" alt-text="파이프라인 - 디버그":::
9. 그런 다음 True 조건 단계로 돌아가서 **대기** 작업을 삭제합니다. **활동** 도구 상자에서 **이동 & 전환** 을 펼치고, **복사** 활동을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 활동 이름을 **IncrementalCopyActivity** 로 설정합니다. 

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png" alt-text="복사 활동 - 이름":::
10. **속성** 창에서 **원본** 으로 전환하고 다음 단계를 수행합니다.

   1. **원본 데이터 세트** 필드에 대해 SQL MI 데이터 세트 이름을 지정합니다. 
   2. **쿼리 사용** 에 대해 **쿼리** 를 선택합니다.
   3. **쿼리** 에 다음을 입력합니다.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png" alt-text="복사 활동 - 원본 설정":::

11. 미리 보기를 클릭하여 쿼리가 변경된 행을 올바르게 반환하는지 확인합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png" alt-text="스크린샷은 쿼리를 확인하는 미리 보기를 보여줍니다.":::
12. **싱크** 탭으로 전환하고 **싱크 데이터 세트** 필드의 Azure Storage 데이터 세트를 지정합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png" alt-text="스크린샷은 싱크 탭을 보여줍니다.":::
13. 주 파이프라인 캔버스로 돌아가기를 클릭하고 **조회** 활동을 **If 조건** 활동에 하나씩 연결합니다. **조회** 활동에 연결된 **녹색** 단추를 **If 조건** 활동으로 끕니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png" alt-text="조회 활동 및 복사 활동 연결":::
14. 도구 모음에서 **유효성 검사** 를 클릭합니다. 유효성 검사 오류가 없는지 확인합니다. **>>** 를 클릭하여 **파이프라인 유효성 검사 보고서** 창을 닫습니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png" alt-text="유효성 검사 단추":::
15. 디버그를 클릭하여 파이프라인을 테스트하고 파일이 스토리지 위치에 생성되었는지 확인합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png" alt-text="증분 파이프라인 디버그-2":::
16. **모두 게시** 단추를 클릭하여 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)를 Data Factory 서비스에 게시합니다. **게시 성공** 메시지가 표시될 때까지 기다립니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png" alt-text="게시 단추":::    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>연속 창 트리거 및 CDC 창 매개 변수를 구성합니다. 
이 단계에서는 빈번한 일정에 따라 작업을 실행하는 연속 창 트리거를 만듭니다. 연속 창 트리거의 WindowStart 및 WindowEnd 시스템 변수를 사용하여 CDC 쿼리에 사용할 파이프라인에 매개 변수로 전달합니다.

1. **IncrementalCopyPipeline** 파이프라인의 **매개 변수** 탭으로 이동하고 **+ 새** 단추를 사용하여 파이프라인에 두 개의 매개 변수(**triggerStartTime** 및 **triggerEndTime**)를 추가합니다. 이는 연속 창 시작 및 종료 시간을 나타냅니다. 디버깅을 위해 **YYYY-MM-DD HH24:MI:SS.FFF** 형식의 기본값을 추가합니다. 하지만 테이블에서 triggerStartTime이 CDC를 사용하도록 설정되어 있지 않은지 확인합니다. 그렇지 않으면 오류가 발생합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png" alt-text="지금 트리거 메뉴":::
2. **조회** 작업의 설정 탭을 클릭하고 시작 및 종료 매개 변수를 사용하도록 쿼리를 구성합니다. 다음을 쿼리에 복사합니다.
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. **If 조건** 활동이 True이면 **복사** 작업으로 이동하고 **원본** 탭을 클릭합니다. 다음을 쿼리에 복사합니다.
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. **복사** 작업의 **싱크** 탭을 클릭하고 **열기** 를 클릭하여 데이터 세트 속성을 편집합니다. **매개 변수** 탭을 클릭하고 **triggerStart** 라는 새 매개 변수를 추가합니다.    

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png" alt-text="스크린샷은 매개 변수 탭에 새 매개 변수 추가를 보여줍니다.":::
5. 그런 다음 날짜 기반 파티션이 있는 **고객/증분** 하위 디렉터리에 데이터를 저장하도록 데이터 세트 속성을 구성합니다.
   1. 데이터 세트 속성의 **연결** 탭을 클릭하고 **디렉터리** 및 **파일** 섹션 모두에 동적 콘텐츠를 추가합니다. 
   2. 텍스트 상자 아래의 동적 콘텐츠 링크를 클릭하여 **디렉터리** 섹션에 다음 식을 입력합니다.
    
      ```sql
      @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
      ```
   3. **파일** 섹션에 다음 식을 입력합니다. 이렇게 하면 트리거 시작 날짜와 시간을 기준으로 파일 이름이 생성되고 그 뒤에 csv 확장명이 붙습니다.
    
      ```sql
      @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
      ```

      :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png" alt-text="싱크 데이터 세트 구성-3":::

   4. **IncrementalCopyPipeline** 탭을 클릭하여 **복사** 작업의 **싱크** 설정으로 다시 이동합니다. 
   5. 데이터 세트 속성을 확장하고 다음 식을 사용하여 triggerStart 매개 변수 값에 동적 콘텐츠를 입력합니다.

      ```sql
      @pipeline().parameters.triggerStartTime
      ```

     :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png" alt-text="싱크 데이터 세트 구성-4":::

6. 디버그를 클릭하여 파이프라인을 테스트하고 폴더 구조와 출력 파일이 예상대로 생성되는지 확인합니다. 콘텐츠를 확인하려면 파일을 다운로드하여 엽니다. 

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png" alt-text="증분 복사 디버그-3":::

7. 파이프라인 실행의 입력 매개 변수를 검토하여 매개 변수가 쿼리에 삽입되는지 확인합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png" alt-text="증분 복사 디버그-4":::

8. **모두 게시** 단추를 클릭하여 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)를 Data Factory 서비스에 게시합니다. **게시 성공** 메시지가 표시될 때까지 기다립니다.

9. 마지막으로 연속 창 트리거를 구성하여 일정한 간격으로 파이프라인을 실행하고 시작 및 종료 시간 매개 변수를 설정합니다. 

   1. **트리거 추가** 단추를 클릭하고 **새로 만들기/편집** 을 선택합니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png" alt-text="새 트리거 추가":::

   2. 트리거 이름을 입력하고 시작 시간을 지정합니다. 이 시간은 위의 디버그 창 종료 시간과 같습니다.

   :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png" alt-text="연속 창 트리거":::

   3. 다음 화면에서 시작 및 종료 매개 변수에 대한 다음 값을 각각 지정합니다.

      ```sql
      @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
      @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
      ```

      :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png" alt-text="연속 창 트리거-2":::

> [!NOTE]
> 트리거는 게시된 후에만 실행됩니다. 또한 연속 창의 예상 동작은 시작 날짜부터 지금까지 모든 기록 간격을 실행하는 것입니다. 연속 창 트리거에 대한 자세한 내용은 [여기](./how-to-create-tumbling-window-trigger.md)에서 찾을 수 있습니다. 

10. **SQL Server Management Studio** 를 사용하여 다음 SQL을 실행하면 고객 테이블을 약간 추가로 변경할 수 있습니다.

    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. **모두 게시** 단추를 클릭합니다. **게시 성공** 메시지가 표시될 때까지 기다립니다.  

12. 몇 분 후 파이프라인이 트리거되고 새 파일이 Azure Storage에 로드됩니다.

### <a name="monitor-the-incremental-copy-pipeline"></a>증분 복사 파이프라인 모니터링

1. 왼쪽의 **모니터** 탭을 클릭합니다. 목록에 파이프라인 실행 및 해당 상태가 표시됩니다. 목록을 새로 고치려면 **새로 고침** 을 클릭합니다. 파이프라인 이름 근처를 가리켜 다시 실행 작업 및 소비 보고서에 액세스합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png" alt-text="파이프라인 실행":::
2. 파이프라인 실행과 연결된 활동 실행을 보려면 파이프라인 이름을 클릭합니다. 변경된 데이터가 검색되면 복사 작업을 포함하는 세 가지 작업이 있습니다. 그렇지 않으면 목록에 두 개의 항목만 있습니다. 파이프라인 실행 보기로 다시 전환하려면 위쪽의 **모든 파이프라인** 링크를 클릭합니다.

    :::image type="content" source="./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png" alt-text="작업 실행":::


### <a name="review-the-results"></a>결과 검토
`raw` 컨테이너의 `customers/incremental/YYYY/MM/DD` 폴더에 두 번째 파일이 표시됩니다.

:::image type="content" source="media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png" alt-text="증분 복사의 출력 파일":::
 

## <a name="next-steps"></a>다음 단계
다음 자습서로 넘어가서 해당 LastModifiedDate를 기준으로 새로운 파일과 변경된 파일을 복사하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
>[lastmodifieddate별로 새 파일 복사](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
