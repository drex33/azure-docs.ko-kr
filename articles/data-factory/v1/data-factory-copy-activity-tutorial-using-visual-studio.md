---
title: '자습서: Visual Studio를 사용하여 복사 작업이 있는 파이프라인 만들기 '
description: 이 자습서에서는 Visual Studio를 사용하여 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.custom: vs-azure, devx-track-azurepowershell
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5bd8bef3b45ef634577de90656c9452c44399386
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629524"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>자습서: Visual Studio를 사용하여 복사 작업이 있는 파이프라인 만들기
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [복사 마법사](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [복사 작업 자습서](../quickstart-create-data-factory-dot-net.md)를 참조하세요. 

이 문서에서는 Microsoft Visual Studio를 사용하여 Azure Blob 스토리지에서 Azure SQL Database로 데이터를 복사하는 파이프라인이 있는 데이터 팩터리를 만드는 방법을 학습합니다. Azure Data Factory를 처음 사용하는 경우 이 자습서를 수행하기 전에 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.   

이 자습서에는 다음 한 가지 작업이 포함된 파이프라인을 만듭니다. 복사 작업 복사 작업은 지원되는 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사합니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md)을 참조하세요.

파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [파이프라인의 여러 작업](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요.

> [!NOTE] 
> 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하는 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. [자습서 개요](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 문서를 살펴보고 **필수 구성 요소** 단계를 완료합니다.       
2. 데이터 팩터리 인스턴스를 만들려면 구독/리소스 그룹 수준에서 [데이터 팩터리 참여자](../../role-based-access-control/built-in-roles.md#data-factory-contributor) 역할의 구성원이어야 합니다.
3. 다음 항목이 컴퓨터에 설치되어 있어야 합니다. 
   * Visual Studio 2013 또는 Visual Studio 2015
   * Visual Studio 2013 또는 Visual Studio 2015용 Azure SDK를 다운로드합니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)로 이동하고 **.NET** 섹션에서 **VS 2013** 또는 **VS 2015** 를 클릭합니다.
   * Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 또는 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)용 최신 Azure Data Factory 플러그 인을 다운로드합니다. 다음 단계를 수행하여 플러그 인을 업데이트할 수도 있습니다. 메뉴에서 **도구** -> **확장 및 업데이트** -> **온라인** -> **Visual Studio 갤러리** -> **Visual Studio용 Microsoft Azure Data Factory 도구** -> **업데이트** 를 클릭합니다.

## <a name="steps"></a>단계

이 자습서의 일부로 수행하는 단계는 다음과 같습니다.

1. 데이터 팩터리에서 **연결된 서비스** 를 만듭니다. 이 단계에서는 두 가지 유형의 연결된 서비스 즉, Azure Storage 및 Azure SQL Database를 만듭니다. 

    AzureStorageLinkedService는 Azure 스토리지 계정을 데이터 팩터리에 연결합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 컨테이너를 만들고 이 스토리지 계정에 데이터를 업로드했습니다.   

    AzureSqlLinkedService는 Azure SQL Database를 데이터 팩터리에 연결합니다. (선택 사항) Azure File Storage를 프로젝트 리더의 DSVM(데이터 과학 Virtual Machine)에 탑재하고 여기에 프로젝트 데이터 자산을 추가합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 SQL 테이블을 만들었습니다.     

2. 데이터 팩터리에서 입력 및 출력 **데이터 세트** 를 만듭니다.  

    Azure Storage 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure Storage 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 입력 Blob 데이터 세트는 데이터가 포함된 컨테이너와 폴더를 지정합니다.  

    마찬가지로 Azure SQL Database 연결 서비스는 런타임에 Data Factory 서비스에서 Azure SQL Database에 연결하는 데 사용하는 연결 문자열을 지정합니다. (선택 사항) 프로젝트의 데이터 자산을 저장하는 데 사용할 Azure File Storage를 만듭니다.

3. 데이터 팩터리에서 **파이프라인** 을 만듭니다. 이 단계에서는 복사 활동을 사용하여 파이프라인을 만듭니다.   

    복사 작업은 Azure Blob 스토리지의 Blob에서 Azure SQL Database의 테이블로 데이터를 복사합니다. 파이프라인의 복사 활동을 사용하여 지원되는 모든 원본의 데이터를 지원되는 모든 대상으로 복사할 수 있습니다. 지원되는 데이터 저장소 목록은 [데이터 이동 활동](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 문서를 참조하세요.

4. Data Factory 엔터티(연결된 서비스, 데이터 세트/테이블 및 파이프라인)를 배포할 때 Azure **데이터 팩터리** 를 만듭니다. 

## <a name="create-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. **Visual Studio 2015** 를 시작합니다. **File** 을 클릭하고 **New** 를 가리킨 다음 **프로젝트** 를 클릭합니다. **새 프로젝트** 대화 상자가 나타납니다.  

2. **새 프로젝트** 대화 상자에서 **DataFactory** 템플릿을 선택하고 **빈 데이터 팩터리 프로젝트** 를 클릭합니다.  

    :::image type="content" source="./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png" alt-text="새 프로젝트 대화 상자":::

3. 프로젝트 이름, 솔루션 위치 및 솔루션 이름을 지정한 다음 **확인** 을 클릭합니다.

    :::image type="content" source="./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png" alt-text="솔루션 탐색기":::    

## <a name="create-linked-services"></a>연결된 서비스 만들기

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 자습서에서는 Azure HDInsight 또는 Azure Data Lake Analytics와 같은 컴퓨팅 서비스를 사용하지 않습니다. Azure Storage(원본) 및 Azure SQL Database(대상) 유형의 두 데이터 저장소를 사용합니다. 

따라서 두 가지 유형의 연결된 서비스 AzureStorageLinkedService와 AzureSqlLinkedService를 만듭니다.  

Azure Storage 연결된 서비스는 Azure Storage 계정을 데이터 팩터리에 연결합니다. 이 스토리지 계정은 컨테이너를 만들고 [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 데이터를 업로드한 계정입니다.   

Azure SQL 연결된 서비스는 Azure SQL Database를 데이터 팩터리에 연결합니다. (선택 사항) Azure File Storage를 프로젝트 리더의 DSVM(데이터 과학 Virtual Machine)에 탑재하고 여기에 프로젝트 데이터 자산을 추가합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 emp 테이블을 만들었습니다.

연결된 서비스는 데이터 저장소 또는 컴퓨팅 서비스를 Azure Data Factory에 연결합니다. 복사 작업에서 지원하는 모든 원본 및 싱크는 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 를 참조하세요. 데이터 팩터리에서 지원하는 컴퓨팅 서비스 목록은 [연결된 컴퓨팅 서비스](data-factory-compute-linked-services.md)를 참조하세요. 이 자습서에서는 컴퓨팅 서비스를 사용하지 않습니다. 

### <a name="create-the-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기

1. **솔루션 탐색기** 에서 **연결된 서비스** 를 마우스 오른쪽 단추로 클릭하고, **추가** 를 가리킨 다음, **새 항목** 을 클릭합니다.      

2. **새 항목 추가** 대화 상자의 목록에서 **Azure Storage 연결된 서비스** 를 선택한 다음 **추가** 를 클릭합니다. 

    :::image type="content" source="./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png" alt-text="새 연결된 서비스":::

3. `<accountname>` 및 `<accountkey>`*를 Azure Storage 계정 이름 및 해당 키로 바꿉니다. 

    :::image type="content" source="./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png" alt-text="Azure Storage 연결된 서비스":::

4. **AzureStorageLinkedService1.json** 파일을 저장합니다.

    연결된 서비스 정의의 JSON 속성에 대한 자세한 내용은 [Azure Blob Storage 커넥터](data-factory-azure-blob-connector.md#linked-service-properties) 문서를 참조하세요.

### <a name="create-the-azure-sql-linked-service"></a>Azure SQL 연결된 서비스 만들기

1. **솔루션 탐색기** 에서 다시 **연결된 서비스** 노드를 마우스 오른쪽 단추로 다시 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다. 
2. 이번에는 **Azure SQL 연결된 서비스** 를 선택하고 **추가** 를 클릭합니다. 
3. **AzureSqlLinkedService1.json 파일** 에서 `<servername>`, `<databasename>`, `<username@servername>` 및 `<password>`를 서버의 이름, 데이터베이스, 사용자 계정 및 암호로 바꿉니다.    
4. **AzureSqlLinkedService1.json** 파일을 저장합니다. 

    이러한 JSON 속성에 대한 자세한 내용은 [Azure SQL Database 커넥터](data-factory-azure-sql-connector.md#linked-service-properties)를 참조하세요.

## <a name="create-datasets"></a>데이터 세트 만들기

이전 단계에서는 Azure Storage 계정과 Azure SQL Database를 데이터 팩터리에 연결하는 연결된 서비스를 만들었습니다. 이 단계에서는 AzureStorageLinkedService1 및 AzureSqlLinkedService1에서 각각 참조하는 데이터 저장소에 저장된 입력 및 출력 데이터를 나타내는 InputDataset 및 OutputDataset이라는 두 개의 데이터 세트를 정의합니다.

Azure Storage 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure Storage 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 입력 Blob 데이터 세트(InputDataset)는 입력 데이터가 포함된 컨테이너와 폴더를 지정합니다.  

마찬가지로 Azure SQL Database 연결 서비스는 런타임에 Data Factory 서비스에서 Azure SQL Database에 연결하는 데 사용하는 연결 문자열을 지정합니다. Azure File Storage 정보를 수동으로 입력하는 방법: 텍스트 파일에 Azure 파일 스토리지 정보가 없는 경우 다음 화면의 지침에 따라 필요한 구독, 스토리지 계정 및 Azure 파일 스토리지 정보를 입력할 수 있습니다. 

### <a name="create-input-dataset"></a>입력 데이터 세트 만들기

이 단계에서는 AzureStorageLinkedService1 연결된 서비스에서 나타내는 Azure Storage의 Blob 컨테이너(adftutorial)의 루트 폴더에 있는 Blob 파일(emp.txt)을 가리키는 InputDataset이라는 데이터 세트를 만듭니다. fileName 값을 지정하지 않거나 건너뛰면 입력 폴더에 있는 모든 Blob의 데이터가 대상에 복사됩니다. 이 자습서에서는 fileName 값을 지정합니다. 

여기서는 "데이터 세트" 대신 "테이블"이라는 용어를 사용합니다. 테이블은 사각형 데이터 세트이며 현재 지원되는 유일한 데이터 세트 유형입니다. 

1. **솔루션 탐색기** 에서 **테이블** 을 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.
2. **새 항목 추가** 대화 상자에서 **Azure Blob** 을 선택하고 **추가** 를 클릭합니다.   
3. JSON 텍스트를 다음 텍스트로 바꾸고 **AzureBlobLocation1.json** 파일을 저장합니다. 

   ```json   
   {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
   }
   ``` 
    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

    | 속성 | Description |
    |:--- |:--- |
    | type | 데이터가 Azure Blob 스토리지에 있으므로 형식 속성은 **AzureBlob** 으로 설정됩니다. |
    | linkedServiceName | 이전에 만든 **AzureStorageLinkedService** 를 참조합니다. |
    | folderPath | 입력 Blob이 포함된 Blob **컨테이너** 와 **폴더** 를 지정합니다. 이 자습서에서 adftutorial은 Blob 컨테이너이며, 폴더는 루트 폴더입니다. | 
    | fileName | 이 속성은 선택 사항입니다. 이 속성을 생략하면 folderPath의 모든 파일이 선택됩니다. 이 자습서에서는 fileName에 대해 **emp.txt** 를 지정하므로 해당 파일만 처리를 위해 선택됩니다. |
    | format -> type |입력 파일은 텍스트 형식이므로 **TextFormat** 을 사용합니다. |
    | columnDelimiter | 입력 파일의 열은 **쉼표(`,`)** 로 구분됩니다. |
    | frequency/interval | 빈도는 **시간** 으로 설정되고, 간격은 **1** 로 설정됩니다. 즉, 입력 조각이 **매시간** 사용될 수 있습니다. 다시 말하면, Data Factory 서비스가 지정한 Blob 컨테이너(**adftutorial**)의 루트 폴더에서 매 시간마다 입력 데이터를 찾습니다. 이러한 시간 전후가 아니라 파이프라인의 시작 시간과 종료 시간 내에 있는 데이터를 찾습니다.  |
    | external | 이 파이프라인에 의해 데이터가 생성되지 않는 경우 이 속성은 **true** 로 설정됩니다. 이 자습서의 입력 데이터는 이 파이프라인에 의해 생성되지 않는 emp.txt 파일에 있으므로 이 속성을 true로 설정합니다. |

    이러한 JSON 속성에 대한 자세한 내용은 [Azure Blob 커넥터 문서](data-factory-azure-blob-connector.md#dataset-properties)를 참조하세요.   

### <a name="create-output-dataset"></a>출력 데이터 세트 만들기

이 단계에서는 **OutputDataset** 이라는 출력 데이터 세트를 만듭니다. 이 데이터 세트는 **AzureSqlLinkedService1** 로 표시되는 Azure SQL Database의 SQL 테이블을 가리킵니다. 

1. **솔루션 탐색기** 에서 다시 **테이블** 을 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.
2. **새 항목 추가** 대화 상자에서 **Azure SQL** 을 선택하고 **추가** 를 클릭합니다. 
3. JSON 텍스트를 다음 JSON으로 바꾸고 **AzureSqlTableLocation1.json** 파일을 저장합니다.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
        }
    }
    ```

    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

    | 속성 | Description |
    |:--- |:--- |
    | type | Azure SQL Database의 테이블에 데이터가 복사되기 때문에 type 속성은 **AzureSqlTable** 로 설정됩니다. |
    | linkedServiceName | 이전에 만든 **AzureSqlLinkedService** 를 참조합니다. |
    | tableName | 데이터가 복사되는 **테이블** 을 지정했습니다. | 
    | frequency/interval | frequency는 **Hour** 로 설정되고, interval은 **1** 입니다. 즉 출력 조각이 이러한 시간 이전 또는 이후가 아니라 파이프라인의 시작 시간과 종료 시간 사이에서 **매시간** 생성됩니다.  |

    데이터베이스의 emp 테이블에 **ID**, **FirstName** 및 **LastName** 이라는 세 개의 열이 있습니다. ID는 ID 열이므로 여기서 **FirstName** 및 **LastName** 만 지정해야 합니다.

    이러한 JSON 속성에 대한 자세한 내용은 [Azure SQL 커넥터 문서](data-factory-azure-sql-connector.md#dataset-properties)를 참조하세요.

## <a name="create-pipeline"></a>파이프라인 만들기

이 단계에서는 **InputDataset** 을 입력으로 사용하고 **OutputDataset** 을 출력으로 사용하는 **복사 활동** 을 포함한 파이프라인을 만듭니다.

현재 출력 데이터 세트는 일정을 작동하는 것입니다. 이 자습서에서는 출력 데이터 세트가 한 시간에 한 번씩 조각을 생성하도록 구성됩니다. 이 파이프라인은 하루 24시간 간격, 즉 24시간 동안에 걸친 시작 시간과 종료 시간을 갖습니다. 따라서 24개의 출력 데이터 세트가 파이프라인에 의해 생성됩니다. 

1. **솔루션 탐색기** 에서 **파이프라인** 을 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.  
2. **새 항목 추가** 대화 상자에서 **데이터 파이프라인 복사** 를 선택하고 **추가** 를 클릭합니다. 
3. JSON을 다음 JSON으로 바꾸고 **CopyActivity1.json** 파일을 저장합니다.

   ```json
  {
   "name": "ADFTutorialPipeline",
   "properties": {
     "description": "Copy data from a blob to Azure SQL table",
     "activities": [
       {
         "name": "CopyFromBlobToSQL",
         "type": "Copy",
         "inputs": [
           {
             "name": "InputDataset"
           }
         ],
         "outputs": [
           {
             "name": "OutputDataset"
           }
         ],
         "typeProperties": {
           "source": {
             "type": "BlobSource"
           },
           "sink": {
             "type": "SqlSink",
             "writeBatchSize": 10000,
             "writeBatchTimeout": "60:00:00"
           }
         },
         "Policy": {
           "concurrency": 1,
           "executionPriorityOrder": "NewestFirst",
           "style": "StartOfInterval",
           "retry": 0,
           "timeout": "01:00:00"
         }
       }
     ],
     "start": "2017-05-11T00:00:00Z",
     "end": "2017-05-12T00:00:00Z",
     "isPaused": false
   }
  }
    ```   
   - 작업 섹션에는 **형식** 이 **복사** 로 설정된 작업만 있습니다. 복사 활동에 대한 자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. Data Factory 솔루션에서 [데이터 변환 활동](data-factory-data-transformation-activities.md)을 사용할 수도 있습니다.
   - 작업에 대한 입력을 **InputDataset** 으로 설정하고 작업에 대한 출력을 **OutputDataset** 으로 설정합니다. 
   - **typeProperties** 섹션에서 **BlobSource** 를 원본 유형으로 지정하고 **SqlSink** 를 싱크 유형으로 지정합니다. 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 전체 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 지원되는 특정 데이터 저장소를 원본/싱크로 사용하는 방법을 알아보려면 표에 나와 있는 링크를 클릭하세요.  
     
     **시작** 속성 값을 현재 날짜로 바꾸고 **종료** 값을 다음 날짜로 바꿉니다. 날짜 부분만 지정하고 날짜/시간의 시간 부분은 건너뛸 수 있습니다. 예를 들어, "2016-02-03"은 "2016-02-03T00:00:00Z"과 동일합니다.
     
     start 및 end 날짜/시간은 둘 다 [ISO 형식](https://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예를 들면 다음과 같습니다. 2016-10-14T16:32:41Z. **종료** 시간은 선택 사항이지만 이 자습서에서는 사용합니다. 
     
     **종료** 속성 값을 지정하지 않는 경우 "**시작 + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **종료** 속성 값으로 **9999-09-09** 를 지정합니다.
     
     앞의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.

     파이프라인 정의의 JSON 속성에 대한 설명은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 복사 활동 정의의 JSON 속성에 대한 설명은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. BlobSource에서 지원하는 JSON 속성에 대한 설명은 [Azure Blob 커넥터 문서](data-factory-azure-blob-connector.md)를 참조하세요. SqlSink에서 지원하는 JSON 속성에 대한 설명은 [Azure SQL Database 커넥터 문서](data-factory-azure-sql-connector.md)를 참조하세요.

## <a name="publishdeploy-data-factory-entities"></a>데이터 팩터리 엔터티 게시/배포
이 단계에서는 이전에 만든 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)를 게시합니다. 이러한 엔터티를 저장하기 위해 만들어진 새 데이터 팩터리의 이름을 지정할 수도 있습니다.  

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 클릭합니다. 
2. **Microsoft 계정에 로그인** 대화 상자가 표시되면 Azure 구독이 있는 계정의 자격 증명을 입력하고 **로그인** 을 클릭합니다.
3. 다음 대화 상자가 표시됩니다.
   
   :::image type="content" source="./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png" alt-text="게시 대화 상자":::
4. 데이터 팩터리 구성 페이지에서 다음 단계를 수행합니다. 
   
   1. **새 데이터 팩터리 만들기** 옵션을 선택합니다.
   2. **이름** 에 **VSTutorialFactory** 를 입력합니다.  
      
      > [!IMPORTANT]
      > Azure Data Factory 이름은 전역적으로 고유해야 합니다. 게시할 때 데이터 팩터리의 이름에 대한 오류를 받은 경우 데이터 팩터리의 이름(예: yournameVSTutorialFactory)을 변경하고 다시 게시하도록 시도합니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.        
      > 
      > 
   3. **구독** 필드의 Azure 구독을 선택합니다.
      
      > [!IMPORTANT]
      > 모든 구독이 표시되지 않으면 구독의 관리자 또는 공동 관리자인 계정을 사용하여 로그인했는지 확인합니다.  
      > 
      > 
   4. 생성되는 데이터 팩터리의 **리소스 그룹** 을 선택합니다. 
   5. 데이터 팩터리의 **하위 지역** 을 선택합니다. Data Factory 서비스에서 지원하는 지역만 드롭다운 목록에 표시됩니다.
   6. **다음** 을 클릭하여 **항목 게시** 페이지로 전환합니다.
      
       :::image type="content" source="media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png" alt-text="데이터 팩터리 페이지 구성":::   
5. **항목 게시** 페이지에서 모든 데이터 팩터리 엔터티가 선택되었는지 확인하고 **다음** 을 클릭하여 **요약** 페이지로 전환합니다.
   
   :::image type="content" source="media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png" alt-text="항목 페이지 게시":::     
6. 요약을 검토한 후 **다음** 을 클릭하여 배포 프로세스를 시작하고 **배포 상태** 를 봅니다.
   
   :::image type="content" source="media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png" alt-text="요약 페이지 게시":::
7. **배포 상태** 페이지에 배포 프로세스의 상태가 표시됩니다. 배포가 완료되면 마침을 클릭합니다.
 
   :::image type="content" source="media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png" alt-text="배포 상태 페이지":::

다음 사항에 유의하세요. 

* 만약 “이 구독이 Microsoft.DataFactory 네임스페이스를 사용하도록 등록되어 있지 않습니다.”라는 오류를 수신하는 경우 다음 중 하나를 수행하고 다시 게시하세요. 
  
  * Azure PowerShell에서 다음 명령을 실행하여 Data Factory 공급자를 등록합니다. 

  ```powershell    
  Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
  데이터 팩터리 공급자가 등록되어 있는지 확인하려면 다음 명령을 실행할 수 있습니다. 
    
  ```powershell
  Get-AzResourceProvider
    ```
  * Azure 구독을 사용하여 [Azure 포털](https://portal.azure.com) 에 로그인하고 데이터 팩터리 블레이드로 이동하거나 Azure 포털에 데이터 팩터리를 만듭니다. 이 작업은 공급자를 자동으로 등록합니다.
* 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.

> [!IMPORTANT]
> Data Factory 인스턴스를 만들려면 Azure 구독의 관리자 또는 공동 관리자여야 합니다.

## <a name="monitor-pipeline"></a>파이프라인 모니터링
데이터 팩터리의 홈 페이지로 이동합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **추가 서비스** 를 클릭하고 **데이터 팩터리** 를 클릭합니다.

  :::image type="content" source="media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png" alt-text="데이터 팩터리 찾아보기":::
3. 데이터 팩터리의 이름을 입력하기 시작합니다.

  :::image type="content" source="media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png" alt-text="데이터 팩터리의 이름"::: 
4. 데이터 팩터리의 홈 페이지를 보려면 결과 목록에서 데이터 팩터리를 클릭합니다.

  :::image type="content" source="media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png" alt-text="데이터 팩터리 홈페이지":::
5. 이 자습서에서 만든 파이프라인과 데이터 세트를 모니터링하려면 [데이터 세트 및 파이프라인 모니터링](data-factory-monitor-manage-pipelines.md)의 지침을 참조하세요. Visual Studio는 현재 Data Factory 파이프라인 모니터링을 지원하지 않습니다. 

## <a name="summary"></a>요약
이 자습서에서는 Azure Blob에서 Azure SQL Database로 데이터를 복사하는 Azure 데이터 팩터리를 만들었습니다. Visual Studio를 사용하여 데이터 팩터리, 연결된 서비스, 데이터 세트 및 파이프라인을 만들었습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.  

1. Azure **데이터 팩터리** 를 만들었습니다.
2. **연결된 서비스** 를 만들었습니다.
   1. 입력 데이터를 보유하는 Azure Storage 계정을 연결하는 **Azure Storage** 연결된 서비스입니다.     
   2. 출력 데이터를 보유하는 데이터베이스를 연결하는 **Azure SQL** 연결 서비스입니다. 
3. 파이프라인의 입력 데이터와 출력 데이터를 설명하는 **데이터 세트** 를 만들었습니다.
4. 원본으로 **BlobSource** 를 사용하고 싱크로 **SqlSink** 를 사용하는 **복사 작업** 으로 **파이프라인** 을 만들었습니다. 

Azure HDInsight 클러스터를 사용하여 HDInsight Hive 활동을 통해 데이터를 변환하는 방법을 알아보려면 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하는 첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정하여 두 활동을 연결하면 해당 활동을 차례로 실행할 수 있습니다. 자세한 정보는 [데이터 팩터리의 예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요. 

## <a name="view-all-data-factories-in-server-explorer"></a>서버 탐색기에서 모든 데이터 팩터리 보기
이 섹션에서는 Visual Studio의 [서버 탐색기]를 사용하여 Azure 구독의 모든 데이터 팩터리를 보고 기존 데이터 팩터리에 기반한 Visual Studio 프로젝트를 만드는 방법에 대해 설명합니다. 

1. **Visual Studio** 의 메뉴에서 **보기** 를 클릭한 다음 **서버 탐색기** 를 클릭합니다.
2. 서버 탐색기 창에서 **Azure** 를 확장한 다음 **Data Factory** 를 확장합니다. **Visual Studio에 로그인** 이 표시되면 Azure 구독과 연결된 **계정** 을 입력하고 **계속** 을 클릭합니다. **암호** 를 입력하고 **로그인** 을 클릭합니다. Visual Studio에서는 구독에 있는 모든 Azure Data Factory에 대한 정보를 가져오려고 시도합니다. **데이터 팩터리 작업 목록** 창에 이 작업의 상태가 표시됩니다.

    :::image type="content" source="./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png" alt-text="서버 탐색기":::

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>기존 데이터 팩터리에 대한 Visual Studio 프로젝트 만들기

- 서버 탐색기에서 데이터 팩터리를 마우스 오른쪽 단추로 클릭하고 **새 프로젝트로 데이터 팩터리 내보내기** 를 선택하여 기존 데이터 팩터리에 기반한 Visual Studio 프로젝트를 만듭니다.

  :::image type="content" source="./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png" alt-text="VS 프로젝트로 데이터 팩터리 내보내기":::  

## <a name="update-data-factory-tools-for-visual-studio"></a>Visual Studio용 데이터 팩터리 도구 업데이트
Visual Studio용 Azure Data Factory 도구를 업데이트하려면 다음 단계를 수행합니다.

1. 메뉴에서 **도구** 를 클릭하고 **확장 및 업데이트** 를 선택합니다. 
2. 왼쪽 창에서 **업데이트** 를 선택한 다음 **Visual Studio 갤러리** 를 선택합니다.
3. **Visual Studio용 Azure Data Factory 도구** 를 선택하고 **업데이트** 를 클릭합니다. 이 항목이 표시되지 않으면 이미 최신 버전의 도구가 있는 것입니다. 

## <a name="use-configuration-files"></a>구성 파일 사용
각 환경마다 다르게 연결된 서비스/테이블/파이프라인에 대한 속성을 구성하기 위해 Visual Studio의 구성 파일을 사용할 수 있습니다.

Azure Storage 연결 서비스에 대한 다음 JSON 정의를 고려해야 합니다. 데이터 팩터리 엔터티를 배포하는 환경(개발/테스트/프로덕션)에 따라 서로 다른 accountname 및 accountkey에 대한 값으로 **connectionString** 을 지정하려면 각 환경에 대한 별도의 구성 파일을 사용하여 이 동작을 수행할 수 있습니다.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>구성 파일 추가
다음 단계를 수행하여 각 환경에 대한 구성 파일을 추가합니다.   

1. Visual Studio 솔루션의 데이터 팩터리 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.
2. 왼쪽에 있는 설치된 템플릿 목록에서 **구성** 을 선택하고 **구성 파일** 을 선택한 다음, 구성 파일의 **이름** 을 입력하고 **추가** 를 클릭합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png" alt-text="구성 파일 추가":::
3. 다음 형식으로 구성 매개 변수와 해당 값을 추가합니다.

  ```json
  {
      "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
      "AzureStorageLinkedService1": [
          {
              "name": "$.properties.typeProperties.connectionString",
              "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
          }
      ],
      "AzureSqlLinkedService1": [
          {
              "name": "$.properties.typeProperties.connectionString",
              "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
          }
      ]
  }
    ```

    이 예제에서는 Azure Storage 연결된 서비스 및 Azure SQL 연결된 서비스의 connectionString 속성을 구성합니다. 이름을 지정하는 구문은 [JsonPath](https://goessner.net/articles/JsonPath/)입니다.   

    JSON에 다음 코드와 같은 값의 배열을 가진 속성이 있는 경우:  

  ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    다음 구성 파일(0부터 시작되는 인덱스 사용)과 같은 속성을 구성합니다.

  ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>공백이 포함된 속성 이름
속성 이름에 공백이 있으면 다음 예제(데이터베이스 서버 이름)와 같이 대괄호를 사용합니다.

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>구성을 사용하여 솔루션 배포
VS에서 Azure 데이터 팩터리 엔터티를 게시하는 경우 해당 게시 작업에 사용하려는 구성을 지정할 수 있습니다.

구성 파일을 사용하여 Azure 데이터 팩터리 프로젝트에서 엔터티를 게시하려면   

1. Data Factory 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 클릭하여 **게시 항목** 대화 상자를 확인합니다.
2. 기존 데이터 팩터리를 선택하거나 **데이터 팩터리 구성** 페이지에서 데이터 팩터리를 만드는 값을 지정하고 **다음** 을 클릭합니다.   
3. **항목 게시** 페이지에서 **배포 구성 선택** 필드에 사용 가능한 구성이 있는 드롭다운 목록이 표시됩니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png" alt-text="구성 파일 선택":::
4. 사용하려는 **구성 파일** 을 선택하고 **다음** 을 클릭합니다.
5. **요약** 페이지에서 JSON 파일의 이름이 표시되는지 확인하고 **다음** 을 클릭합니다.
6. 배포 작업이 완료되면 **마침** 을 클릭합니다.

배포할 때 구성 파일의 값은 엔터티가 Azure Data Factory 서비스에 배포되기 전에 JSON 파일에서 속성 값을 설정하는 데 사용됩니다.   

## <a name="use-azure-key-vault"></a>Azure Key Vault 사용
연결 문자열과 같은 중요한 데이터를 코드 리포지토리에 커밋하는 것은 보안 정책에 위배되는 경우가 종종 있습니다. Azure Key Vault에 중요 정보를 저장하고 Data Factory 엔터티를 게시하면서 사용하는 방법에 대한 자세한 내용은 GitHub의 [ADF 보안 게시](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish)(영문) 샘플을 참조하세요. Visual Studio에 대한 보안 게시 확장을 통해 비밀이 Key Vault에 저장되도록 하고 이에 대한 참조만 연결된 서비스/배포 구성에 지정하도록 할 수 있습니다. 이러한 참조는 Data Factory 엔터티를 Azure에 게시할 때 확인됩니다. 그런 다음 이러한 파일을 비밀을 노출하지 않고 원본 리포지토리에 커밋할 수 있습니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Blob 스토리지를 원본 데이터 저장소로 사용하고 Azure SQL Database를 복사 작업의 대상 데이터 저장소로 사용했습니다. 다음 표에서는 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 목록을 제공합니다. 

[!INCLUDE [data-factory-supported-data-stores](includes/data-factory-supported-data-stores.md)]

데이터 저장소간에 데이터를 복사하는 방법에 대해 알아보려면 테이블에서 데이터 저장소에 대한 링크를 클릭하세요.
