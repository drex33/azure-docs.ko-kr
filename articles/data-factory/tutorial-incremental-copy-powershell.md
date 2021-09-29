---
title: PowerShell을 사용하여 테이블 증분 복사
description: 이 자습서에서는 Azure SQL 데이터베이스에서 Azure Blob Storage로 데이터를 증분 복사하는 Azure Data Factory 파이프라인을 만듭니다.'
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 02/18/2021
ms.openlocfilehash: 9b1592f76917975e06e7d8a36cb618cbe3abadcf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757784"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-powershell"></a>PowerShell을 사용하여 Azure SQL Database에서 Azure Blob 스토리지로 데이터 증분 로드

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 Azure Data Factory를 사용하여 Azure SQL Database의 테이블에서 Azure Blob 스토리지로 델타 데이터를 로드하는 파이프라인을 만듭니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 워터마크 값을 저장할 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 연결된 서비스 만들기.
> * 원본, 싱크 및 워터마크 데이터 세트를 만듭니다.
> * 파이프라인을 만듭니다.
> * 파이프라인을 실행합니다.
> * 파이프라인 실행을 모니터링합니다.

## <a name="overview"></a>개요
대략적인 솔루션 다이어그램은 다음과 같습니다.

:::image type="content" source="media/tutorial-Incrementally-copy-powershell/incrementally-load.png" alt-text="데이터 증분 로드":::

이 솔루션을 만드는 중요한 단계는 다음과 같습니다.

1. **워터마크 열을 선택합니다**.
    원본 데이터 저장소에서 하나의 열을 선택합니다. 이 열은 모든 실행에 대해 새 레코드 또는 업데이트된 레코드를 분할하는 데 사용할 수 있습니다. 선택한 이 열의 데이터(예: last_modify_time 또는 ID)는 일반적으로 행을 만들거나 업데이트할 때 계속 증가합니다. 이 열의 최대 값은 워터마크로 사용됩니다.

2. **워터마크 값을 저장할 데이터 저장소를 준비합니다**.   
    이 자습서에서는 SQL 데이터베이스에 워터마크 값을 저장합니다.

3. **다음 워크플로를 사용하여 파이프라인을 만듭니다.**

    이 솔루션의 파이프라인에 포함되는 작업은 다음과 같습니다.

    * 두 가지 조회 작업을 만듭니다. 첫 번째 조회 작업을 사용하여 마지막 워터마크 값을 검색합니다. 두 번째 조회 작업을 사용하여 새 워터마크 값을 검색합니다. 이러한 워터마크 값은 복사 작업에 전달됩니다.
    * 이전 워터마크 값보다 크고, 새 워터마크 값보다 작은 워터마크 열 값으로 원본 데이터 저장소의 행을 복사하는 복사 작업을 만듭니다. 그런 다음 원본 데이터 스토리지의 델타 데이터를 새 파일로 Blob Storage에 복사합니다.
    * 다음에 실행되는 파이프라인에 대한 워터마크 값을 업데이트하는 StoredProcedure 작업을 만듭니다.


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure SQL Database**. 데이터베이스를 원본 데이터 저장소로 사용합니다. Azure SQL Database에 데이터베이스가 없는 경우 [Azure SQL Database에서 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md)에서 만드는 단계를 참조하세요.
* **Azure Storage**. Blob Storage를 싱크 데이터 스토리지로 사용합니다. 스토리지 계정이 없는 경우, 계정을 만드는 단계는 [스토리지 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요. adftutorial이라는 컨테이너를 만듭니다. 
* **Azure PowerShell**. [Azure PowerShell을 설치 및 구성](/powershell/azure/install-Az-ps)의 지침을 따르세요.

### <a name="create-a-data-source-table-in-your-sql-database"></a>SQL 데이터베이스에 데이터 원본 테이블 만들기
1. SQL Server Management Studio를 엽니다. **서버 탐색기** 에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리** 를 선택합니다.

2. SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 데이터 원본 저장소로 `data_source_table`이라는 테이블을 만듭니다.

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    이 자습서에서는 LastModifytime을 워터마크 열로 사용합니다. 데이터 원본 저장소의 데이터는 다음 테이블에 표시됩니다.

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>상위 워터마크 값을 저장하기 위해 SQL 데이터베이스에 다른 테이블 만들기
1. SQL 데이터베이스에 대해 다음 SQL 명령을 실행하여 워터마크 값을 저장할 `watermarktable` 테이블을 만듭니다.  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. 원본 데이터 저장소의 테이블 이름으로 상위 워터마크의 기본 값을 설정합니다. 이 자습서에서 테이블 이름은 data_source_table입니다.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. `watermarktable` 테이블의 데이터를 검토합니다.

    ```sql
    Select * from watermarktable
    ```
    출력:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>SQL 데이터베이스에 저장 프로시저 만들기

다음 명령을 실행하여 SQL 데이터베이스에 저장 프로시저를 만듭니다.

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기
1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름을 지정한 다음 명령을 실행합니다. 예제는 `"adfrg"`입니다. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 실행합니다.

2. 데이터 팩터리의 위치에 대한 변수를 정의합니다.

    ```powershell
    $location = "East US"
    ```
3. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다.

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$resourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 실행합니다.

4. 데이터 팩터리 이름에 대한 변수를 정의합니다.

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유하게 업데이트합니다. 예를 들어 ADFTutorialFactorySP1127이라는 이름을 사용합니다.

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. 데이터 팩터리를 만들려면 다음 **Set-AzDataFactoryV2** cmdlet을 실행합니다.

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

다음 사항에 유의하세요.

* 데이터 팩터리 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도합니다.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정이 참여자 또는 소유자 역할의 구성원이거나, Azure 구독의 관리자여야 합니다.
* 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics** 를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Storage, SQL Database, Azure SQL Managed Instance 등) 및 컴퓨팅(Azure HDInsight 등)은 다른 지역에 있을 수 있습니다.


## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 섹션에서는 스토리지 계정과 SQL Database에 연결된 서비스를 만듭니다.

### <a name="create-a-storage-linked-service"></a>Storage 연결된 서비스 만들기
1. C:\ADF 폴더에 다음 내용이 포함된 AzureStorageLinkedService.json이라는 JSON 파일을 만듭니다. (ADF 폴더가 없으면 해당 폴더를 만듭니다.) 파일을 저장하기 전에 스토리지 계정의 이름과 키로 `<accountName>`과 `<accountKey>`를 바꿉니다.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```
2. PowerShell에서 ADF 폴더로 전환합니다.

3. **Set-AzDataFactoryV2LinkedService** cmdlet을 실행하여 AzureStorageLinkedService라는 연결된 서비스를 만듭니다. 다음 예제에서는 *ResourceGroupName* 및 *DataFactoryName* 매개 변수에 대한 값을 전달합니다.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-a-sql-database-linked-service"></a>SQL Database 연결된 서비스 만들기
1. C:\ADF 폴더에 다음 내용이 포함된 AzureSQLDatabaseLinkedService.json이라는 JSON 파일을 만듭니다. (ADF 폴더가 없으면 해당 폴더를 만듭니다.) 파일을 저장하기 전에 &lt;server&gt;, &lt;database&gt;, &lt;user id&gt; 및 &lt;password&gt;를 서버의 이름, 데이터베이스, 사용자 ID 및 암호로 바꿉니다.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. PowerShell에서 ADF 폴더로 전환합니다.

3. **Set-AzDataFactoryV2LinkedService** cmdlet을 실행하여 연결된 서비스 AzureSQLDatabaseLinkedService를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>데이터 세트 만들기
이 단계에서는 원본 및 싱크 데이터를 나타내는 데이터 세트를 만듭니다.

### <a name="create-a-source-dataset"></a>원본 데이터 세트 만들기

1. 동일한 폴더에 다음 내용이 포함된 SourceDataset.json이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }

    ```
    이 자습서에서는 테이블 이름으로 data_source_table을 사용합니다. 다른 이름의 테이블을 사용하는 경우 이름을 바꿉니다.

2. **Set-AzDataFactoryV2Dataset** cmdlet을 실행하여 SourceDataset 데이터 세트를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>싱크 데이터 세트 만들기

1. 동일한 폴더에 다음 내용이 포함된 SinkDataset.json이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > 이 코드 조각에서는 Blob 스토리지에 `adftutorial`이라는 Blob 컨테이너가 있다고 가정합니다. 컨테이너가 없으면 만들거나 기존 컨테이너의 이름으로 설정합니다. `incrementalcopy` 출력 폴더가 컨테이너에 없는 경우 자동으로 만들어집니다. 이 자습서에서 파일 이름은 `@CONCAT('Incremental-', pipeline().RunId, '.txt')` 식을 사용하여 동적으로 생성됩니다.

2. **Set-AzDataFactoryV2Dataset** cmdlet을 실행하여 SinkDataset 데이터 세트를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>워터마크에 대한 데이터 세트 만들기
이 단계에서는 상위 워터마크 값을 저장하기 위한 데이터 세트를 만듭니다.

1. 동일한 폴더에 다음 내용이 포함된 WatermarkDataset.json이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  **Set-AzDataFactoryV2Dataset** cmdlet을 실행하여 WatermarkDataset 데이터 세트를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    cmdlet의 샘플 출력은 다음과 같습니다.

    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>파이프라인 만들기
이 자습서에서는 하나의 파이프라인에 두 개의 조회 작업, 하나의 복사 작업 및 하나의 StoredProcedure 작업이 연결되어 있는 파이프라인을 만듭니다.


1. 동일한 폴더에 다음 내용이 포함된 IncrementalCopyPipeline.json이라는 JSON 파일을 만듭니다.

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },

                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },

                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },

                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],

                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },

                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {

                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },

                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },

                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]

        }
    }
    ```


2. **Set-AzDataFactoryV2Pipeline** cmdlet을 실행하여 IncrementalCopyPipeline 파이프라인을 만듭니다.

   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   샘플 출력은 다음과 같습니다.

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```

## <a name="run-the-pipeline"></a>파이프라인 실행

1. **Invoke-AzDataFactoryV2Pipeline** cmdlet을 사용하여 IncrementalCopyPipeline 파이프라인을 실행합니다. 자리 표시자를 사용자의 리소스 그룹 및 데이터 팩터리 이름으로 바꿉니다.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
2. **Get-AzDataFactoryV2ActivityRun** cmdlet을 실행하여 모든 작업이 성공적으로 실행되었다고 표시될 때까지 파이프라인 상태를 확인합니다. 자리 표시자를 *RunStartedAfter* 및 *RunStartedBefore* 매개 변수에 대한 사용자 고유의 적절한 시간으로 바꿉니다. 이 자습서에서는 *-RunStartedAfter "2017/09/14"* 및 *-RunStartedBefore "2017/09/15"* 를 사용합니다.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    샘플 출력은 다음과 같습니다.

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>결과 검토

1. Blob Storage(싱크 스토리지)에서 SinkDataset에 정의된 파일에 데이터가 복사된 것을 확인합니다. 현재 자습서에서 파일 이름은 `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`입니다. 이 파일을 열면 SQL 데이터베이스의 데이터와 동일한 레코드를 파일에서 볼 수 있습니다.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
2. `watermarktable`에서 최신 값을 확인합니다. 워터마크 값이 업데이트된 것을 볼 수 있습니다.

    ```sql
    Select * from watermarktable
    ```

    샘플 출력은 다음과 같습니다.

    TableName | WatermarkValue
    --------- | --------------
    data_source_table | 2017-09-05 8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>데이터 원본 저장소에 데이터를 삽입하여 델타 데이터 로드 확인

1. SQL 데이터베이스(데이터 원본 저장소)에 새 데이터를 삽입합니다.

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ```

    SQL 데이터베이스에서 업데이트된 데이터는 다음과 같습니다.

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. **Invoke-AzDataFactoryV2Pipeline** cmdlet을 사용하여 IncrementalCopyPipeline 파이프라인을 다시 실행합니다. 자리 표시자를 사용자의 리소스 그룹 및 데이터 팩터리 이름으로 바꿉니다.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. **Get-AzDataFactoryV2ActivityRun** cmdlet을 실행하여 모든 작업이 성공적으로 실행되었다고 표시될 때까지 파이프라인 상태를 확인합니다. 자리 표시자를 *RunStartedAfter* 및 *RunStartedBefore* 매개 변수에 대한 사용자 고유의 적절한 시간으로 바꿉니다. 이 자습서에서는 *-RunStartedAfter "2017/09/14"* 및 *-RunStartedBefore "2017/09/15"* 를 사용합니다.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    샘플 출력은 다음과 같습니다.

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. Blob Storage에 다른 파일이 만들어진 것을 볼 수 있습니다. 이 자습서에서 새 파일 이름은 `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`입니다. 해당 파일을 열면 2개 행의 레코드가 표시됩니다.

5. `watermarktable`에서 최신 값을 확인합니다. 워터마크 값이 다시 업데이트된 것을 볼 수 있습니다.

    ```sql
    Select * from watermarktable
    ```
    샘플 출력은 다음과 같습니다.

    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000


## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다.

> [!div class="checklist"]
> * 워터마크 값을 저장할 데이터 저장소를 준비합니다.
> * 데이터 팩터리를 만듭니다.
> * 연결된 서비스 만들기.
> * 원본, 싱크 및 워터마크 데이터 세트를 만듭니다.
> * 파이프라인을 만듭니다.
> * 파이프라인을 실행합니다.
> * 파이프라인 실행을 모니터링합니다.

이 자습서에서는 파이프라인이 Azure SQL Database의 단일 테이블에서 Blob 스토리지로 데이터를 복사했습니다. SQL Server 데이터베이스의 여러 테이블에서 SQL Database로 데이터를 복사하는 방법을 알아보려면 다음 자습서로 이동하세요.

> [!div class="nextstepaction"]
>[SQL Server의 여러 테이블에서 Azure SQL Database로 데이터 증분 로드](tutorial-incremental-copy-multiple-tables-powershell.md)
