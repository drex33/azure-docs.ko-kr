---
title: '자습서: REST API를 사용하여 Azure Data Factory 파이프라인 만들기 '
description: 이 자습서에서는 REST API를 사용하여 Azure Blob 스토리지에서 Azure SQL Database로 데이터를 복사하는 복사 작업이 있는 Azure Data Factory 파이프라인을 만듭니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
ms.custom: devx-track-azurepowershell
robots: noindex
ms.openlocfilehash: 000f412c31f154b195eb82bdebe8d90254eb2673
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680664"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>자습서: REST API를 사용하여 데이터를 복사하는 Azure Data Factory 파이프라인 만들기 
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
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [복사 작업 자습서](../quickstart-create-data-factory-rest-api.md)를 참조하세요. 

이 문서에서는 REST API를 사용하여 Azure Blob 스토리지에서 Azure SQL Database로 데이터를 복사하는 파이프라인이 있는 데이터 팩터리를 만드는 방법에 대해 알아봅니다. Azure Data Factory를 처음 사용하는 경우 이 자습서를 수행하기 전에 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.   

이 자습서에는 다음 한 가지 작업이 포함된 파이프라인을 만듭니다. 복사 작업 복사 작업은 지원되는 데이터 저장소에서 지원되는 싱크 데이터 저장소로 데이터를 복사합니다. 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고 안정적이며 확장성 있는 방법으로 복사할 수 있는 전역적으로 사용 가능한 서비스를 통해 이루어집니다. 복사 작업에 대한 자세한 내용은 [데이터 이동 작업](data-factory-data-movement-activities.md)을 참조하세요.

파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [파이프라인의 여러 작업](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요.

> [!NOTE]
> 이 문서는 모든 데이터 팩터리 REST API를 다루지 않습니다. 데이터 팩터리 REST API에 대한 포괄적인 설명서는 [데이터 팩터리 Cmdlet 참조](/rest/api/datafactory/) (영문)를 참조하세요.
>  
> 이 자습서에서 데이터 파이프라인은 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사합니다. Azure Data Factory를 사용하여 데이터를 변환하는 방법에 대한 자습서는 [자습서: Hadoop 클러스터를 사용하여 데이터를 변환하는 파이프라인 빌드](data-factory-build-your-first-pipeline.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [자습서 개요](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 를 살펴보고 **필수 구성 요소** 단계를 완료합니다.
* 컴퓨터에 [Curl](https://curl.haxx.se/dlwiz/) 을 설치합니다. REST 명령과 함께 Curl 도구를 사용하여 데이터 팩터리를 만듭니다. 
* [이 문서](../../active-directory/develop/howto-create-service-principal-portal.md) 의 지침에 따라 다음 작업을 수행합니다. 
  1. Azure Active Directory에서 **ADFCopyTutorialApp** 이라는 웹 애플리케이션을 만듭니다.
  2. **클라이언트 ID** 및 **암호 키** 를 가져옵니다. 
  3. **테넌트 ID** 를 가져옵니다. 
  4. **ADFCopyTutorialApp** 애플리케이션을 **데이터 팩터리 기여자** 역할에 할당합니다.  
* [Azure PowerShell](/powershell/azure/)을 설치합니다.  
* **PowerShell** 을 시작하고 다음 단계를 수행합니다. 이 자습서를 마칠 때까지 Azure PowerShell을 열어 두세요. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.
  
  1. 다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **&lt;NameOfAzureSubscription**&gt;을 Azure 구독의 이름으로 바꿉니다. 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. PowerShell에서 다음 명령을 실행하여 **ADFTutorialResourceGroup** 이라는 Azure 리소스 그룹을 만듭니다.  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     ```
     
      리소스 그룹이 이미 있다면 업데이트(Y)할지 또는 유지(N)할지를 지정합니다. 
     
      이 자습서의 일부 단계에서는 ADFTutorialResourceGroup이라는 리소스 그룹을 사용한다고 가정합니다. 다른 리소스 그룹을 사용하는 경우 이 자습서에서 ADFTutorialResourceGroup 대신 해당 리소스 그룹의 이름을 사용해야 합니다.

## <a name="create-json-definitions"></a>JSON 정의 만들기
curl.exe가 있는 폴더에서 다음 JSON 파일을 만듭니다. 

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> 이름은 전역적으로 고유해야 하므로 고유한 이름을 지정하기 위해 ADFCopyTutorialDF를 접두사/접미사로 지정할 수 있습니다. 
> 
> 

```JSON
{  
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> **accountname** 및 **accountkey** 를 Azure Storage 계정의 이름 및 키로 바꿉니다. 스토리지 액세스 키를 가져오는 방법을 알아보려면 [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요.

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

JSON 속성에 대한 자세한 내용은 [Azure Storage 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-linked-service)를 참조하세요.

### <a name="azuresqllinkedservicejson"></a>azuresqllinkedservice.json
> [!IMPORTANT]
> **servername**, **databasename**, **username** 및 **password** 를 Server의 이름, SQL Database의 이름, 사용자 계정 및 계정의 암호로 바꿉니다.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

JSON 속성에 대한 자세한 내용은 [Azure SQL 연결된 서비스](data-factory-azure-sql-connector.md#linked-service-properties)를 참조하세요.

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
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
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
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
| type | Azure Blob Storage에 데이터가 있기 때문에 type 속성은 **AzureBlob** 으로 설정됩니다. |
| linkedServiceName | 이전에 만든 **AzureStorageLinkedService** 를 참조합니다. |
| folderPath | 입력 Blob이 포함된 Blob **컨테이너** 와 **폴더** 를 지정합니다. 이 자습서에서 adftutorial은 Blob 컨테이너이며, 폴더는 루트 폴더입니다. | 
| fileName | 이 속성은 선택 사항입니다. 이 속성을 생략하면 folderPath의 모든 파일이 선택됩니다. 이 자습서에서는 fileName에 대해 **emp.txt** 를 지정하므로 해당 파일만 처리를 위해 선택됩니다. |
| format -> type |입력 파일은 텍스트 형식이므로 **TextFormat** 을 사용합니다. |
| columnDelimiter | 입력 파일의 열은 **쉼표(`,`)** 로 구분됩니다. |
| frequency/interval | frequency는 **Hour** 로 설정되고, interval은 **1** 로 설정됩니다. 즉 입력 조각이 **매시간** 사용될 수 있습니다. 다시 말하면, Data Factory 서비스가 지정한 Blob 컨테이너(**adftutorial**)의 루트 폴더에서 매 시간마다 입력 데이터를 찾습니다. 이러한 시간 이전 또는 이후가 아니라 파이프라인의 시작 시간과 종료 시간 내에 있는 데이터를 찾습니다.  |
| external | 이 파이프라인에 의해 데이터가 생성되지 않는 경우 이 속성은 **true** 로 설정됩니다. 이 자습서의 입력 데이터는 이 파이프라인에 의해 생성되지 않는 emp.txt 파일에 있으므로 이 속성을 true로 설정합니다. |

이러한 JSON 속성에 대한 자세한 내용은 [Azure Blob 커넥터 문서](data-factory-azure-blob-connector.md#dataset-properties)를 참조하세요.

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
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
    "linkedServiceName": "AzureSqlLinkedService",
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

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL Database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
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
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

다음 사항에 유의하세요.

- 작업 섹션에는 **형식** 이 **복사** 로 설정된 작업만 있습니다. 복사 활동에 대한 자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. Data Factory 솔루션에서 [데이터 변환 활동](data-factory-data-transformation-activities.md)을 사용할 수도 있습니다.
- 작업에 대한 입력을 **AzureBlobInput** 으로 설정하고 작업에 대한 출력을 **AzureSqlOutput** 으로 설정합니다. 
- **typeProperties** 섹션에서 **BlobSource** 를 원본 유형으로 지정하고 **SqlSink** 를 싱크 유형으로 지정합니다. 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 전체 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. 지원되는 특정 데이터 저장소를 원본/싱크로 사용하는 방법을 알아보려면 표에 나와 있는 링크를 클릭하세요.  
 
**시작** 속성 값을 현재 날짜로 바꾸고 **종료** 값을 다음 날짜로 바꿉니다. 날짜 부분만 지정하고 날짜/시간의 시간 부분은 건너뛸 수 있습니다. 예를 들어, "2017-02-03"은 "2017-02-03T00:00:00Z"와 동일합니다.
 
start 및 end 날짜/시간은 둘 다 [ISO 형식](https://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예를 들면 다음과 같습니다. 2016-10-14T16:32:41Z. **종료** 시간은 선택 사항이지만 이 자습서에서는 사용합니다. 
 
**종료** 속성 값을 지정하지 않는 경우 "**시작 + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **종료** 속성 값으로 **9999-09-09** 를 지정합니다.
 
앞의 예에서는 각 데이터 조각이 1시간마다 생성되므로 24개 데이터 조각이 있게 됩니다.

파이프라인 정의의 JSON 속성에 대한 설명은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 복사 활동 정의의 JSON 속성에 대한 설명은 [데이터 이동 활동](data-factory-data-movement-activities.md)을 참조하세요. BlobSource에서 지원하는 JSON 속성에 대한 설명은 [Azure Blob 커넥터 문서](data-factory-azure-blob-connector.md)를 참조하세요. SqlSink에서 지원하는 JSON 속성에 대한 설명은 [Azure SQL Database 커넥터 문서](data-factory-azure-sql-connector.md)를 참조하세요.

## <a name="set-global-variables"></a>전역 변수 설정
Azure PowerShell에서 값을 고유한 값으로 대체한 후에 다음 명령을 실행합니다.

> [!IMPORTANT]
> 클라이언트 ID, 클라이언트 암호, 테넌트 ID 및 구독 ID를 가져오는 지침은 [필수 구성 요소](#prerequisites) 섹션을 참조하세요.   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

사용 중인 데이터 팩터리의 이름을 업데이트한 후 다음 명령을 실행하십시오. 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>AAD로 인증
다음 명령을 실행하여 AAD(Azure Active Directory)로 인증합니다. 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>데이터 팩터리 만들기
이 단계에서는 **ADFCopyTutorialDF** 라는 Azure Data Factory를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 복사 작업은 원본에서 대상 데이터 저장소로 데이터를 복사합니다. HDInsight Hive 작업은 Hive 스크립트를 실행하여 입력 데이터를 제품 출력 데이터로 변환합니다. 다음 명령을 실행하여 데이터 팩터리를 만듭니다. 

1. 이 명령을 **cmd** 라는 변수에 할당합니다. 
   
    > [!IMPORTANT]
    > 여기(ADFCopyTutorialDF)에서 지정한 데이터 팩터리의 이름이 **datafactory.json** 에서 지정한 이름과 일치하는지 확인합니다. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. **Invoke-Command** 를 사용하여 명령을 실행합니다.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 결과를 확인합니다. 데이터 팩터리가 성공적으로 생성된 경우 데이터 팩터리에 대한 JSON이 **결과** 에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.  
   
    ```
    Write-Host $results
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 결과에 **데이터 팩터리 이름 "ADFCopyTutorialDF"를 사용할 수 없습니다** 라는 오류가 표시되는 경우 다음 단계를 수행합니다.  
  
  1. **datafactory.json** 파일에서 이름(예: yournameADFCopyTutorialDF)을 변경합니다.
  2. **$cmd** 변수가 값을 할당한 첫 번째 명령에서 ADFCopyTutorialDF를 새 이름으로 바꾸고 명령을 실행합니다. 
  3. REST API를 호출하는 다음 두 명령을 실행하여 데이터 팩터리를 만들고 작업의 결과를 인쇄합니다. 
     
     데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.
* 데이터 팩터리 인스턴스를 만들려면 Azure 구독의 참가자/관리자여야 합니다.
* 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.
* 만약 “**이 구독이 Microsoft.DataFactory 네임스페이스를 사용하도록 등록되어 있지 않습니다.** ”라는 오류를 수신하는 경우 다음 중 하나를 수행하고 다시 게시하세요. 
  
  * Azure PowerShell에서 다음 명령을 실행하여 데이터 팩터리 공급자를 등록합니다. 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    데이터 팩터리 공급자가 등록되어 있는지 확인하려면 다음 명령을 실행할 수 있습니다. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Azure 구독을 사용하여 [Azure 포털](https://portal.azure.com) 에 로그인하고 데이터 팩터리 블레이드로 이동하거나 Azure 포털에 데이터 팩터리를 만듭니다. 이 작업은 공급자를 자동으로 등록합니다.

파이프라인을 만들기 전에 먼저 몇 가지 데이터 팩터리 엔터티를 만들어야 합니다. 먼저 연결된 서비스를 만들어서 원본 및 대상 데이터 저장소를 데이터 저장소에 연결합니다. 그런 입력 및 출력 데이터 세트를 정의하여 다음 연결된 데이터 저장소에 데이터를 나타냅니다. 마지막으로 이러한 데이터 세트를 사용하는 작업이 있는 파이프라인을 만듭니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기
데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 자습서에서는 Azure HDInsight 또는 Azure Data Lake Analytics와 같은 컴퓨팅 서비스를 사용하지 않습니다. Azure Storage(원본) 및 Azure SQL Database(대상) 유형의 두 데이터 저장소를 사용합니다. 이에 따라 두 개의 연결된 서비스, 즉 AzureStorage와 AzureSqlDatabase 유형의 AzureStorageLinkedService와 AzureSqlLinkedService를 만듭니다.  

AzureStorageLinkedService는 Azure 스토리지 계정을 데이터 팩터리에 연결합니다. 이 스토리지 계정은 컨테이너를 만들고 [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 데이터를 업로드한 계정입니다.   

AzureSqlLinkedService는 Azure SQL Database를 데이터 팩터리에 연결합니다. (선택 사항) Azure File Storage를 프로젝트 리더의 DSVM(데이터 과학 Virtual Machine)에 탑재하고 여기에 프로젝트 데이터 자산을 추가합니다. [필수 구성 요소](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)의 일부로 이 데이터베이스에서 emp 테이블을 만들었습니다.  

### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기
이 단계에서는 Azure Storage 계정을 데이터 팩터리에 연결합니다. 이 섹션의 Azure Storage 계정 이름 및 키를 지정합니다. Azure Storage 연결된 서비스를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Storage 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-linked-service)를 참조하세요.  

1. 이 명령을 **cmd** 라는 변수에 할당합니다. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. **Invoke-Command** 를 사용하여 명령을 실행합니다.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 결과를 확인합니다. 연결된 서비스가 성공적으로 생성된 경우 연결된 서비스에 대한 JSON이 **결과** 에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Azure SQL 연결된 서비스 만들기
이 단계에서는 Azure SQL Database를 데이터 팩터리에 연결합니다. 이 섹션에서 논리 SQL 서버 이름, 데이터베이스 이름, 사용자 이름 및 사용자 암호를 지정합니다. Azure SQL 연결된 서비스를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure SQL 연결된 서비스](data-factory-azure-sql-connector.md#linked-service-properties)를 참조하세요.

1. 이 명령을 **cmd** 라는 변수에 할당합니다. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azuresqllinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. **Invoke-Command** 를 사용하여 명령을 실행합니다.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 결과를 확인합니다. 연결된 서비스가 성공적으로 생성된 경우 연결된 서비스에 대한 JSON이 **결과** 에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>데이터 세트 만들기
이전 단계에서는 Azure Storage 계정과 Azure SQL Database를 데이터 팩터리에 연결하는 연결된 서비스를 만들었습니다. 이 단계에서는 AzureStorageLinkedService 및 AzureSqlLinkedService에서 각각 참조하는 데이터 저장소에 저장된 입력 및 출력 데이터를 나타내는 AzureBlobInput 및 AzureSqlOutput이라는 두 개의 데이터 세트를 정의합니다.

Azure Storage 연결된 서비스는 런타임에 Data Factory 서비스에서 Azure Storage 계정에 연결하는 데 사용하는 연결 문자열을 지정합니다. 그리고 입력 Blob 데이터 세트(AzureBlobInput)는 입력 데이터가 포함된 컨테이너와 폴더를 지정합니다.  

마찬가지로 Azure SQL Database 연결 서비스는 런타임에 Data Factory 서비스에서 Azure SQL Database에 연결하는 데 사용하는 연결 문자열을 지정합니다. Azure File Storage 정보를 수동으로 입력하는 방법: 텍스트 파일에 Azure 파일 스토리지 정보가 없는 경우 다음 화면의 지침에 따라 필요한 구독, 스토리지 계정 및 Azure 파일 스토리지 정보를 입력할 수 있습니다. 

### <a name="create-input-dataset"></a>입력 데이터 세트 만들기
이 단계에서는 AzureStorageLinkedService 연결된 서비스에서 나타내는 Azure Storage의 Blob 컨테이너(adftutorial)의 루트 폴더에 있는 Blob 파일(emp.txt)을 가리키는 AzureBlobInput이라는 데이터 세트를 만듭니다. fileName 값을 지정하지 않거나 건너뛰면 입력 폴더에 있는 모든 Blob의 데이터가 대상에 복사됩니다. 이 자습서에서는 fileName 값을 지정합니다. 

1. 이 명령을 **cmd** 라는 변수에 할당합니다. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. **Invoke-Command** 를 사용하여 명령을 실행합니다.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 결과를 확인합니다. 데이터 세트가 성공적으로 생성된 경우 데이터 세트에 대한 JSON이 **결과** 에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>출력 데이터 세트 만들기
Azure SQL Database 연결 서비스는 런타임 시 Data Factory 서비스에서 Azure SQL Database에 연결하는 데 사용하는 연결 문자열을 지정합니다. 이 단계에서 만든 출력 SQL 테이블 데이터 세트(OututDataset)는 Blob Storage의 데이터가 복사되는 데이터베이스의 테이블을 지정합니다.

1. 이 명령을 **cmd** 라는 변수에 할당합니다.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. **Invoke-Command** 를 사용하여 명령을 실행합니다.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 결과를 확인합니다. 데이터 세트가 성공적으로 생성된 경우 데이터 세트에 대한 JSON이 **결과** 에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>파이프라인 만들기
이 단계에서는 **AzureBlobInput** 을 입력으로 사용하고 **AzureSqlOutput** 을 출력으로 사용하는 **복사 작업** 을 포함하는 파이프라인을 만듭니다.

현재 출력 데이터 세트는 일정을 작동하는 것입니다. 이 자습서에서는 출력 데이터 세트가 한 시간에 한 번씩 조각을 생성하도록 구성됩니다. 이 파이프라인은 하루 24시간 간격, 즉 24시간 동안에 걸친 시작 시간과 종료 시간을 갖습니다. 따라서 24개의 출력 데이터 세트가 파이프라인에 의해 생성됩니다. 

1. 이 명령을 **cmd** 라는 변수에 할당합니다.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. **Invoke-Command** 를 사용하여 명령을 실행합니다.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. 결과를 확인합니다. 데이터 세트가 성공적으로 생성된 경우 데이터 세트에 대한 JSON이 **결과** 에 표시되고, 그렇지 않으면 오류 메시지가 나타납니다.  

    ```PowerShell   
    Write-Host $results
    ```

**축하합니다.** Azure Blob Storage에서 Azure SQL Database에 데이터를 복사하는 파이프라인으로 Azure 데이터 팩터리를 성공적으로 만들었습니다.

## <a name="monitor-pipeline"></a>파이프라인 모니터링
이 단계에서는 데이터 팩터리 REST API를 사용하여 파이프라인에 의해 생성되는 조각을 모니터링합니다.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> 다음 명령에 지정된 시작 및 종료 시간이 파이프라인의 시작 및 종료 시간과 일치하는지 확인하십시오. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

조각이 **준비** 상태 또는 **실패** 상태로 표시될 때까지 Invoke-Command 및 다음 명령을 실행합니다. 조각이 준비 상태일 때 출력 데이터에 대한 Azure SQL Database에서 **emp** 테이블을 확인합니다. 

각 조각에 대해 원본 파일의 데이터 두 개의 행을 Azure SQL Database의 emp 테이블에 복사합니다. 따라서 모든 조각이 준비 상태로 성공적으로 처리되면 emp 테이블에 24개의 새 레코드가 표시됩니다. 

## <a name="summary"></a>요약
이 자습서에서는 Azure Blob에서 Azure SQL Database로 데이터를 복사하는 Azure 데이터 팩터리를 만드는 데 REST API를 사용했습니다. 이 자습서에서 수행한 단계를 요약하면 다음과 같습니다.  

1. Azure **데이터 팩터리** 를 만들었습니다.
2. **연결된 서비스** 를 만들었습니다.
   1. 입력 데이터를 보유하는 Azure Storage 계정을 연결하는 Azure Storage 연결된 서비스입니다.     
   2. 출력 데이터를 보유하는 데이터베이스를 연결하는 Azure SQL 연결 서비스입니다. 
3. 파이프라인의 입력 데이터와 출력 데이터를 설명하는 **데이터 세트** 를 만들었습니다.
4. 원본인 BlobSource와 싱크인 SqlSink를 사용하여 복사 작업으로 **파이프라인** 을 만들었습니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Blob 스토리지를 원본 데이터 저장소로 사용하고 Azure SQL Database를 복사 작업의 대상 데이터 저장소로 사용했습니다. 다음 표에서는 복사 활동에서 원본 및 싱크로 지원되는 데이터 저장소의 목록을 제공합니다. 

[!INCLUDE [data-factory-supported-data-stores](includes/data-factory-supported-data-stores.md)]

데이터 저장소간에 데이터를 복사하는 방법에 대해 알아보려면 테이블에서 데이터 저장소에 대한 링크를 클릭하세요.
