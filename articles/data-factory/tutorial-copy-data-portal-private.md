---
title: 프라이빗 엔드포인트를 사용하여 Azure Data Factory 파이프라인 만들기
description: 이 자습서에서는 Azure Portal을 사용하여 파이프라인이 있는 데이터 팩터리를 만드는 방법에 대한 단계별 지침을 제공합니다. 파이프라인은 복사 작업을 사용하여 Azure Blob 스토리지에서 Azure SQL 데이터베이스로 데이터를 복사합니다.
author: jianleishen
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.author: jianleishen
ms.openlocfilehash: 7aee146ea06e15696b1e52e701d8d32b476d0570
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798335"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>프라이빗 엔드포인트를 사용하여 Azure Blob 스토리지에서 SQL 데이터베이스로 안전하게 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 Azure Data Factory UI(사용자 인터페이스)를 사용하여 데이터 팩터리를 만듭니다. *이 데이터 팩터리의 파이프라인은 [Azure Data Factory Managed Virtual Network](managed-virtual-network-private-endpoint.md)의 프라이빗 엔드포인트를 사용하여 데이터를 Azure Blob 스토리지에서 Azure SQL 데이터베이스로 안전하게 복사합니다(둘 다 선택한 네트워크에만 액세스할 수 있음).* 이 자습서의 구성 패턴은 파일 기반 데이터 저장소에서 관계형 데이터 저장소로 복사하는 데 적용됩니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](./copy-activity-overview.md) 표를 참조하세요.

> [!NOTE]
> Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](./introduction.md)를 참조하세요.

이 자습서에서 수행하는 단계는 다음과 같습니다.

* 데이터 팩터리를 만듭니다.
* 복사 활동이 있는 파이프라인 만들기


## <a name="prerequisites"></a>필수 구성 요소
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. Blob Storage를 *원본* 데이터 스토리지로 사용합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)를 참조하세요. *스토리지 계정이 선택한 네트워크에서만 액세스를 허용하는지 확인합니다.* 
* **Azure SQL Database**. 데이터베이스를 *싱크* 데이터 저장소로 사용합니다. Azure SQL 데이터베이스가 없는 경우 만드는 단계를 [SQL 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md)에서 참조하세요. *SQL Database 계정이 선택한 네트워크에서만 액세스를 허용하는지 확인합니다.* 

### <a name="create-a-blob-and-a-sql-table"></a>Blob 및 SQL 테이블 만들기

이제 다음 단계를 수행하여 자습서에서 사용할 Blob Storage 및 SQL 데이터베이스를 준비합니다.

#### <a name="create-a-source-blob"></a>원본 Blob 만들기

1. 메모장을 엽니다. 다음 텍스트를 복사하고 디스크에 **emp.txt** 파일로 저장합니다.

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Blob Storage에 **adftutorial** 이라는 컨테이너를 만듭니다. 이 컨테이너에 **input** 이라는 폴더를 만듭니다. 그런 다음 **emp.txt** 파일을 **input** 폴더에 업로드합니다. 이러한 작업을 수행하려면 Azure Portal 또는 [Azure Storage Explorer](https://storageexplorer.com/)와 같은 도구를 사용합니다.

#### <a name="create-a-sink-sql-table"></a>싱크 SQL 테이블 만들기

다음 SQL 스크립트를 사용하여 SQL 데이터베이스에 **dbo.emp** 테이블을 만듭니다.

```sql
CREATE TABLE dbo.emp
(
    ID int IDENTITY(1,1) NOT NULL,
    FirstName varchar(50),
    LastName varchar(50)
)
GO

CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
```

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기
이 단계에서는 데이터 팩터리를 만들고, Data Factory UI를 시작하여 파이프라인을 데이터 팩터리에 만듭니다.

1. Microsoft Edge 또는 Google Chrome을 엽니다. 현재 Microsoft Edge 및 Google Chrome 웹 브라우저만 Data Factory UI를 지원합니다.

1. 왼쪽 메뉴에서 **리소스 만들기** > **분석** > **Data Factory** 를 차례로 선택합니다.

1. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory** 를 입력합니다.

   Azure Data Factory의 이름은 *전역적으로 고유* 해야 합니다. 이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름(예: yournameADFTutorialDataFactory)을 입력합니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](./naming-rules.md)을 참조하세요.

1. 데이터 팩터리를 만들 Azure **구독** 을 선택합니다.

1. **리소스 그룹** 에 대해 다음 단계 중 하나를 사용합니다.

    - **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
    - **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다. 
     
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 

1. **버전** 에서 **V2** 를 선택합니다.

1. **위치** 에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 나타납니다. 데이터 팩터리에서 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 계산(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.

1. **만들기** 를 선택합니다.

1. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동** 을 선택하여 **Data Factory** 페이지로 이동합니다.

1. **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택하여 별도의 탭에서 Data Factory UI를 시작합니다.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Data Factory Managed Virtual Network에서 Azure 통합 런타임 만들기
이 단계에서는 Azure 통합 런타임을 만들고 Data Factory Managed Virtual Network를 사용하도록 설정합니다.

1. Data Factory 포털에서 **관리** 로 이동하고, **새로 만들기** 를 선택하여 새 Azure Integration Runtime을 만듭니다.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/create-new-azure-ir.png" alt-text="새 Azure Integration Runtime 만들기를 보여주는 스크린샷":::
1. **통합 런타임 설정** 페이지에서 필요한 기능을 기반으로 만들 통합 런타임을 선택합니다. 이 자습서에서는 **Azure, 자체 호스팅** 을 선택한 다음, **계속** 을 클릭합니다. 
1. **Azure** 를 선택한 다음, **계속** 을 클릭하여 Azure 통합 런타임을 만듭니다.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/azure-ir.png" alt-text="새 Azure Integration Runtime을 보여주는 스크린샷":::
1. **가상 네트워크 구성(미리 보기)** 에서 **사용** 을 선택합니다.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/enable-managed-vnet.png" alt-text="새 Azure Integration Runtime을 사용하도록 설정하는 내용을 보여주는 스크린샷":::
1. **만들기** 를 선택합니다.

## <a name="create-a-pipeline"></a>파이프라인 만들기
이 단계에서는 복사 활동이 있는 파이프라인을 데이터 팩터리에 만듭니다. 복사 활동은 Blob Storage에서 SQL Database로 데이터를 복사합니다. [빠른 시작 자습서](./quickstart-create-data-factory-portal.md)에서 다음 단계에 따라 파이프라인을 만들었습니다.

1. 연결된 서비스를 만듭니다.
1. 입력 및 출력 데이터 세트를 만듭니다.
1. 파이프라인을 만듭니다.

이 자습서에서는 파이프라인을 만드는 것으로 시작합니다. 그런 다음, 파이프라인을 구성해야 할 때 연결된 서비스와 데이터 세트를 만듭니다.

1. 홈페이지에서 **오케스트레이션** 을 선택합니다.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF 홈페이지를 보여 주는 스크린샷":::
1. 파이프라인의 속성 창에서 파이프라인 이름에 **CopyPipeline** 을 입력합니다.

1. **활동** 도구 상자에서 **이동 및 변환** 범주를 펼치고, **데이터 복사** 활동을 도구 상자에서 파이프라인 디자이너 화면으로 끌어옵니다. 이름에 **CopyFromBlobToSql** 을 입력합니다.

    :::image type="content" source="./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png" alt-text="복사 작업을 보여주는 스크린샷":::

### <a name="configure-a-source"></a>원본 구성

>[!TIP]
>이 자습서에서는 **계정 키** 를 원본 데이터 저장소의 인증 형식으로 사용합니다. 필요한 경우 지원되는 다른 인증 방법(예: **SAS URI**, **서비스 주체** 및 **Managed Identity**)을 선택할 수도 있습니다. 자세한 내용은 [Azure Data Factory를 사용하여 Azure Blob Storage에서 데이터 복사 및 변환](./connector-azure-blob-storage.md#linked-service-properties)의 해당 섹션을 참조하세요.
>
>데이터 저장소에 대한 비밀을 안전하게 저장하려면 Azure Key Vault를 사용하는 것도 좋습니다. 자세한 내용과 그림은 [Azure Key Vault에 자격 증명 저장](./store-credentials-in-key-vault.md)을 참조하세요.

#### <a name="create-a-source-dataset-and-linked-service"></a>원본 데이터 세트 및 연결된 서비스 만들기

1. **원본** 탭으로 이동합니다. **+ 새로 만들기** 를 선택하여 원본 데이터 세트를 만듭니다.

1. **새 데이터 세트** 대화 상자에서 **Azure Blob Storage** 를 선택한 다음, **계속** 을 선택합니다. 원본 데이터는 Blob 스토리지에 있으므로 원본 데이터 세트으로 **Azure Blob Storage** 를 선택합니다.

1. **형식 선택** 대화 상자에서 데이터의 형식 유형을 선택한 다음, **계속** 을 선택합니다.

1. **속성 설정** 대화 상자에서 **이름** 에 **SourceBlobDataset** 를 입력합니다. **첫 행을 머리글로** 의 확인란을 선택합니다. **연결된 서비스** 텍스트 상자에서 **+ 새로 만들기** 를 선택합니다.

1. **새로 연결된 서비스(Azure Blob Storage)** 대화 상자에서 **AzureStorageLinkedService** 를 **이름** 으로 입력하고, **스토리지 계정** 이름 목록에서 스토리지 계정을 선택합니다. 

1. **대화형 작성** 을 사용하도록 설정해야 합니다. 사용하도록 설정하는 데 약 1분 정도 걸릴 수 있습니다.

    :::image type="content" source="./media/tutorial-copy-data-portal-private/interactive-authoring.png" alt-text="대화형 작성을 보여주는 스크린샷":::

1. **연결 테스트** 를 클릭합니다. 스토리지 계정이 **선택한 네트워크** 의 액세스만 허용하고 Data Factory에서 사용하기 전에 승인해야 하는 프라이빗 엔드포인트를 만들어야 하는 경우 실패합니다. 오류 메시지에는 관리형 프라이빗 엔드포인트를 만들기 위해 따를 수 있는 프라이빗 엔드포인트 만들기에 대한 링크가 표시됩니다. 또 다른 방법은 **관리** 탭으로 직접 이동하여 [다음 섹션](#create-a-managed-private-endpoint)의 지침에 따라 관리형 프라이빗 엔드포인트를 만드는 것입니다.

   > [!NOTE]
   > 일부 데이터 팩터리 인스턴스에는 **관리** 탭을 사용할 수 없습니다. 표시되지 않으면 **작성자** > **연결** > **프라이빗 엔드포인트** 를 선택하여 프라이빗 엔드포인트에 액세스할 수 있습니다.
1. 대화 상자를 열어 둔 채 스토리지 계정으로 이동합니다.

1. [이 섹션](#approval-of-a-private-link-in-a-storage-account)의 지침에 따라 프라이빗 링크를 승인합니다.

1. 대화 상자로 돌아갑니다. **연결 테스트** 를 다시 선택하고 **만들기** 를 선택하여 연결된 서비스를 배포합니다.

1. 연결된 서비스가 생성되면 **속성 설정** 페이지로 다시 이동합니다. **파일 경로** 옆에 있는 **찾아보기** 를 선택합니다.

1. **adftutorial/input** 폴더로 이동하고, **emp.txt** 파일을 선택한 다음, **확인** 을 선택합니다.

1. **확인** 을 선택합니다. 자동으로 파이프라인 페이지로 이동합니다. **원본** 탭에서 **SourceBlobDataset** 가 선택되어 있는지 확인합니다. 이 페이지에서 데이터를 미리 보려면 **데이터 미리 보기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-copy-data-portal-private/source-dataset-selected.png" alt-text="원본 데이터 세트를 보여주는 스크린샷":::

#### <a name="create-a-managed-private-endpoint"></a>관리형 프라이빗 엔드포인트 만들기

연결을 테스트할 때 하이퍼링크를 선택하지 않은 경우 경로를 따릅니다. 이제 만든 연결된 서비스에 연결할 관리형 프라이빗 엔드포인트를 만들어야 합니다.

1. **관리** 탭으로 이동합니다.

   > [!NOTE]
   > 일부 Data Factory 인스턴스에는 **관리** 탭을 사용할 수 없습니다. 표시되지 않으면 **작성자** > **연결** > **프라이빗 엔드포인트** 를 선택하여 프라이빗 엔드포인트에 액세스할 수 있습니다.

1. **관리형 프라이빗 엔드포인트** 섹션으로 이동합니다.

1. **관리형 프라이빗 엔드포인트** 아래에서 **+ 새로 만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png" alt-text="관리형 프라이빗 엔드포인트 새로 만들기 단추를 보여주는 스크린샷"::: 

1. 목록에서 **Azure Blob Storage** 타일, **계속** 을 차례로 선택합니다.

1. 만든 스토리지 계정의 이름을 입력합니다.

1. **만들기** 를 선택합니다.

1. 몇 초 후면 만든 프라이빗 링크에 승인이 필요하다고 표시됩니다.

1. 만든 프라이빗 엔드포인트를 선택합니다. 스토리지 계정 수준에서 프라이빗 엔드포인트를 승인하도록 안내하는 하이퍼링크가 표시됩니다.

    :::image type="content" source="./media/tutorial-copy-data-portal-private/manage-private-endpoint.png" alt-text="관리형 프라이빗 엔드포인트 창을 보여주는 스크린샷"::: 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>스토리지 계정에서 프라이빗 링크 승인
1. 스토리지 계정의 **설정** 섹션 아래에서 **프라이빗 엔드포인트 연결** 로 이동합니다.

1. 만든 프라이빗 엔드포인트의 확인란을 선택하고 **승인** 을 선택합니다.

    :::image type="content" source="./media/tutorial-copy-data-portal-private/approve-private-endpoint.png" alt-text="프라이빗 엔드포인트의 승인 단추를 보여주는 스크린샷":::

1. 설명을 추가하고 **예** 를 선택합니다.
1. Data Factory에서 **관리** 탭의 **관리형 프라이빗 엔드포인트** 섹션으로 돌아갑니다.
1. 약 1~2분 후에 프라이빗 엔드포인트의 승인이 Data Factory UI에 나타나는 것이 보입니다.


### <a name="configure-a-sink"></a>싱크 구성
>[!TIP]
>이 자습서에서는 **SQL 인증** 을 싱크 데이터 저장소의 인증 형식으로 사용합니다. 필요한 경우 지원되는 다른 인증 방법(예: **서비스 주체** 및 **Managed Identity**)을 선택할 수도 있습니다. 자세한 내용은 [Azure Data Factory를 사용하여 Azure SQL Database에서 데이터 복사 및 변환](./connector-azure-sql-database.md#linked-service-properties)의 해당 섹션을 참조하세요.
>
>데이터 저장소에 대한 비밀을 안전하게 저장하려면 Azure Key Vault를 사용하는 것도 좋습니다. 자세한 내용과 그림은 [Azure Key Vault에 자격 증명 저장](./store-credentials-in-key-vault.md)을 참조하세요.

#### <a name="create-a-sink-dataset-and-linked-service"></a>싱크 데이터 세트 및 연결된 서비스 만들기
1. **싱크** 탭으로 이동하고, **+ 새로 만들기** 를 선택하여 싱크 데이터 세트를 만듭니다.

1. **새 데이터 세트** 대화 상자의 검색 상자에 **SQL** 을 입력하여 커넥터를 필터링합니다. **Azure SQL Database** 를 선택한 다음, **계속** 을 선택합니다. 이 자습서에서는 데이터를 SQL 데이터베이스에 복사합니다.

1. **속성 설정** 대화 상자에서 **이름** 에 **OutputSqlDataset** 를 입력합니다. **연결된 서비스** 드롭다운 목록에서 **+ 새로 만들기** 를 선택합니다. 데이터 세트는 연결된 서비스와 연결되어야 합니다. 연결된 서비스에는 런타임에 Data Factory에서 SQL 데이터베이스에 연결하는 데 사용하는 연결 문자열 있습니다. 데이터 세트는 데이터가 복사될 컨테이너, 폴더 및 파일(선택 사항)을 지정합니다.

1. **새로 연결된 서비스(Azure SQL Database)** 대화 상자에서 다음 단계를 수행합니다.

    1. **이름** 아래에서 **AzureSqlDatabaseLinkedService** 를 입력합니다.
    1. **서버 이름** 아래에서 SQL Server 인스턴스를 선택합니다.
    1. **대화형 작성** 을 사용하도록 설정해야 합니다.
    1. **데이터베이스 이름** 아래에서 SQL 데이터베이스를 선택합니다.
    1. **사용자 이름** 아래에서 사용자의 이름을 입력합니다.
    1. **암호** 아래에서 사용자의 암호를 입력합니다.
    1. **연결 테스트** 를 클릭합니다. SQL 서버에서 **선택한 네트워크** 로부터의 액세스만 허용하고 Data Factory에서 사용하기 전에 승인해야 하는 프라이빗 엔드포인트를 만들어야 하므로 실패합니다. 오류 메시지에는 관리형 프라이빗 엔드포인트를 만들기 위해 따를 수 있는 프라이빗 엔드포인트 만들기에 대한 링크가 표시됩니다. 또 다른 방법은 **관리** 탭으로 직접 이동하여 다음 섹션의 지침에 따라 관리형 프라이빗 엔드포인트를 만드는 것입니다.
    1. 대화 상자를 열어 둔 채 선택한 SQL 서버로 이동합니다.
    1. [이 섹션](#approval-of-a-private-link-in-sql-server)의 지침에 따라 프라이빗 링크를 승인합니다.
    1. 대화 상자로 돌아갑니다. **연결 테스트** 를 다시 선택하고 **만들기** 를 선택하여 연결된 서비스를 배포합니다.

1. 자동으로 **속성 설정** 대화 상자로 이동합니다. **테이블** 에서 **[dbo].[emp]** 를 선택합니다. 그런 다음, **확인** 을 선택합니다.

1. 파이프라인이 있는 탭으로 이동하고, **싱크 데이터 세트** 에서 **OutputSqlDataset** 가 선택되어 있는지 확인합니다.

    :::image type="content" source="./media/tutorial-copy-data-portal-private/pipeline-tab-2.png" alt-text="파이프라인 탭을 보여주는 스크린샷":::

[복사 작업의 스키마 매핑](./copy-activity-schema-and-type-mapping.md)을 따라 선택적으로 원본 스키마를 대상의 해당 스키마에 매핑할 수 있습니다.

#### <a name="create-a-managed-private-endpoint"></a>관리형 프라이빗 엔드포인트 만들기

연결을 테스트할 때 하이퍼링크를 선택하지 않은 경우 경로를 따릅니다. 이제 만든 연결된 서비스에 연결할 관리형 프라이빗 엔드포인트를 만들어야 합니다.

1. **관리** 탭으로 이동합니다.
1. **관리형 프라이빗 엔드포인트** 섹션으로 이동합니다.
1. **관리형 프라이빗 엔드포인트** 아래에서 **+ 새로 만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png" alt-text="관리형 프라이빗 엔드포인트 새로 만들기 단추를 보여주는 스크린샷"::: 

1. 목록에서 **Azure SQL Database** 타일, **계속** 을 차례로 선택합니다.
1. 선택한 SQL 서버의 이름을 입력합니다.
1. **만들기** 를 선택합니다.
1. 몇 초 후면 만든 프라이빗 링크에 승인이 필요하다고 표시됩니다.
1. 만든 프라이빗 엔드포인트를 선택합니다. SQL 서버 수준에서 프라이빗 엔드포인트를 승인하도록 안내하는 하이퍼링크가 표시됩니다.


#### <a name="approval-of-a-private-link-in-sql-server"></a>SQL Server에서 프라이빗 링크 승인
1. SQL 서버의 **설정** 섹션 아래에서 **프라이빗 엔드포인트 연결** 로 이동합니다.
1. 만든 프라이빗 엔드포인트의 확인란을 선택하고 **승인** 을 선택합니다.
1. 설명을 추가하고 **예** 를 선택합니다.
1. Data Factory에서 **관리** 탭의 **관리형 프라이빗 엔드포인트** 섹션으로 돌아갑니다.
1. 프라이빗 엔드포인트에 대한 승인이 표시되려면 1~2분 정도 걸립니다.

#### <a name="debug-and-publish-the-pipeline"></a>파이프라인 디버그 및 게시

아티팩트(연결된 서비스, 데이터 세트 및 파이프라인)를 Data Factory 또는 고유한 Azure Repos Git 리포지토리에 게시하기 전에 파이프라인을 디버그할 수 있습니다.

1. 파이프라인을 디버그하려면 도구 모음에서 **디버그** 를 선택합니다. 창의 아래쪽에 있는 **출력** 탭에서 파이프라인 실행 상태가 표시됩니다.
1. 파이프라인이 성공적으로 실행되면 위쪽 도구 모음에서 **모두 게시** 를 선택합니다. 이 작업은 사용자가 만든 엔터티(데이터 세트 및 파이프라인)를 Data Factory에 게시합니다.
1. **게시됨** 메시지가 표시될 때까지 기다립니다. 알림 메시지를 보려면 오른쪽 위 모서리(종 모양)에서 **알림 표시** 를 선택합니다.


#### <a name="summary"></a>요약
이 샘플의 파이프라인은 Data Factory Managed Virtual Network의 프라이빗 엔드포인트를 사용하여 Blob Storage에서 SQL Database로 데이터를 복사합니다. 구체적으로 다음 작업 방법을 알아보았습니다.

* 데이터 팩터리를 만듭니다.
* 복사 활동이 있는 파이프라인 만들기