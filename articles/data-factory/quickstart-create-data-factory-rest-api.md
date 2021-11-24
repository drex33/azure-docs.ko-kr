---
title: REST API를 사용하여 Azure Data Factory 만들기
description: Azure Blob 스토리지의 한 위치에서 다른 위치로 데이터를 복사하는 Azure 데이터 팩터리 파이프라인을 만듭니다.
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/31/2021
ms.author: jingwang
ms.custom: devx-track-azurepowershell, mode-other
ms.openlocfilehash: b70091032c6801894099e366dea53c7d13177d12
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133070262"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>빠른 시작: REST API를 사용하여 Azure Data Factory 및 파이프라인 만들기

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [현재 버전](quickstart-create-data-factory-rest-api.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 컴퓨팅 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 애플리케이션의 Azure Synapse Analytics와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다.

이 빠른 시작에서는 REST API를 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리의 파이프라인은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure 구독**. 구독이 없는 경우 [평가판](https://azure.microsoft.com/pricing/free-trial/) 계정을 만들 수 있습니다.
* **Azure Storage 계정**. Blob Storage를 **원본** 및 **싱크** 데이터 저장소 모두로 사용합니다. 아직 없는 경우 Azure Storage 계정을 만드는 단계는 [스토리지 계정 만들기](../storage/common/storage-account-create.md) 문서를 참조하세요.
* Blob Storage에 **Blob 컨테이너** 를 만들고 컨테이너에 입력 **폴더** 를 만들고 폴더에 일부 파일을 업로드합니다. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)와 같은 도구를 사용하여 Azure Blob 스토리지에 연결, Blob 컨테이너 만들기, 입력 파일 업로드 및 출력 파일 확인을 수행할 수 있습니다.
* **Azure PowerShell** 을 설치합니다. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)의 지침을 따르세요. 이 빠른 시작은 PowerShell을 사용하여 REST API 호출을 호출합니다.
* [이 지침](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)에 따라 **Azure Active Directory에 애플리케이션을 만듭니다**. 이후 단계에서 사용하는 **애플리케이션 ID**, **clientSecrets** 및 **테넌트 ID** 값을 기록해 둡니다. 구독 또는 리소스 그룹 수준에서 애플리케이션을 “**기여자**” 역할에 할당합니다.
>[!NOTE]
>   소버린 클라우드의 경우 적절한 ActiveDirectoryAuthority 및 ResourceManagerUrl(BaseUri)의 클라우드 관련 엔드포인트를 사용해야 합니다. Powershell을 사용하여 각 클라우드 환경에 대한 엔드포인트 목록을 반환하는 “Get-AzEnvironment | Format-List”를 실행하여 다양한 클라우드의 엔드포인트 Url을 쉽게 가져올 수 있습니다.  
>    
## <a name="set-global-variables"></a>전역 변수 설정

1. **PowerShell** 을 시작합니다. 이 빠른 시작을 완료할 때까지 Azure PowerShell을 열어 둡니다. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다.

    다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.

    ```powershell
    Connect-AzAccount
    ```
    다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzSubscription
    ```
    다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId** 를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. 자리 표시자를 사용자의 고유한 값으로 바꾼 후 다음 명령을 실행하여 이후 단계에서 사용할 전역 변수를 설정합니다.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $clientSecrets = "<your clientSecrets for the application>"
    $subscriptionId = "<your subscription ID to create the factory>"
    $resourceGroupName = "<your resource group to create the factory>"
    $factoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Azure AD를 사용하여 인증

다음 명령을 실행하여 AAD(Azure Active Directory)로 인증합니다.

```powershell
$credentials = Get-Credential -UserName $appId
Connect-AzAccount -ServicePrincipal  -Credential $credentials -Tenant $tenantID
```
암호를 입력하라는 메시지가 표시되면 clientSecrets 변수의 값을 사용합니다.

액세스 토큰을 가져와야 하는 경우 다음을 실행합니다. 

```powershell

GetToken

```

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

다음 명령을 실행하여 데이터 팩터리를 만듭니다.

```powershell
$body = @"
{
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@

$response =   Invoke-AzRestMethod -SubscriptionId ${subscriptionId}  -ResourceGroupName ${resourceGroupName} -ResourceProviderName  Microsoft.DataFactory -ResourceType "factories" -Name  ${factoryName} -ApiVersion ${apiVersion} -Method PUT -Payload ${body}
$response.Content  

```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics** 를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

샘플 응답 콘텐츠는 다음과 같습니다.

```json

{  
    "name":"<dataFactoryName>",
    "identity":{  
        "type":"SystemAssigned",
        "principalId":"<service principal ID>",
        "tenantId":"<tenant ID>"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{  
        "provisioningState":"Succeeded",
        "createTime":"2019-09-03T02:10:27.056273Z",
        "version":"2018-06-01"
    },
    "eTag":"\"0200c876-0000-0100-0000-5d6dcb930000\"",
    "location":"East US",
    "tags":{  

    }
}
```
## <a name="create-linked-services"></a>연결된 서비스 만들기

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소를 연결하고 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서는 복사 원본 및 싱크 저장소 모두에 대해 샘플의 “AzureStorageLinkedService”라는 하나의 Azure Storage 연결된 서비스를 만들기만 하면 됩니다.

다음 명령을 실행하여 **AzureStorageLinkedService** 라는 연결된 서비스를 만듭니다.

명령을 실행하기 전에 &lt;accountName&gt; 및 &lt;accountKey&gt;를 Azure Storage 계정의 이름 및 키로 바꿉니다.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"

$body = @"
{  
    "name":"AzureStorageLinkedService",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response.content
```

샘플 출력은 다음과 같습니다.

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":"AzureStorageLinkedService",
    "type":"Microsoft.DataFactory/factories/linkedservices",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;"
        }
    },
    "etag":"07011a57-0000-0100-0000-5d6e14a20000"
}
```
## <a name="create-datasets"></a>데이터 세트 만들기

원본에서 싱크로 복사할 데이터를 나타내는 데이터 세트를 정의합니다. 이 예제에서는 다음 두 개의 데이터 세트를 만듭니다. InputDataset 및 OutputDataset. 이 데이터 집합은 이전 섹션에서 만든 Azure Storage 연결된 서비스를 참조합니다. 입력 데이터 세트는 입력 폴더의 원본 데이터를 나타냅니다. 입력 데이터 세트 정의에서 원본 데이터가 포함된 Blob 컨테이너(adftutorial), 폴더(input) 및 파일(emp.txt)을 지정합니다. 출력 데이터 세트는 대상에 복사되는 데이터를 나타냅니다. 출력 데이터 세트 정의에서 Blob 컨테이너(adftutorial), 폴더(output) 및 데이터가 복사될 파일을 지정합니다.

**InputDataset 만들기**

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/InputDataset?api-version=${apiVersion}"

$body = @"
{  
    "name":"InputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response  

```

샘플 출력은 다음과 같습니다.

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/InputDataset",
    "name":"InputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; fileName=emp.txt; folderPath=input; container=adftutorial}"
        }
    },
    "etag":"07011c57-0000-0100-0000-5d6e14b40000"
}
```
**OutputDataset 만들기**

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/OutputDataset?api-version=${apiVersion}"

$body = @"
{  
    "name":"OutputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content
```

샘플 출력은 다음과 같습니다.

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/OutputDataset",
    "name":"OutputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; folderPath=output; container=adftutorial}"
        }
    },
    "etag":"07013257-0000-0100-0000-5d6e18920000"
}
```
## <a name="create-a-pipeline"></a>파이프라인 만들기

이 예제에서 이 파이프라인은 하나의 복사 작업을 포함합니다. 복사 작업은 이전 단계에서 생성된 "InputDataset" 및 "OutputDataset"을 입력 및 출력으로 참조합니다.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"

$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "InputDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "OutputDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "annotations": []
    }
}
"@
$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content
```

샘플 출력은 다음과 같습니다.

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":"Adfv2QuickStartPipeline",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "properties":{  
        "activities":[  
            "@{name=CopyFromBlobToBlob; type=Copy; dependsOn=System.Object[]; policy=; userProperties=System.Object[]; typeProperties=; inputs=System.Object[]; outputs=System.Object[]}"
        ],
        "annotations":[  

        ]
    },
    "etag":"07012057-0000-0100-0000-5d6e14c00000"
}
```

## <a name="create-pipeline-run"></a>파이프라인 실행 만들기

이 단계에서는 파이프라인 실행을 트리거합니다. 응답 본문에 반환된 ID를 실행하는 파이프라인은 나중에 API 모니터링에 사용됩니다.

```powershell
$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"

$response =  Invoke-AzRestMethod  -Path ${path}  -Method POST 
$response.content 
```

샘플 출력은 다음과 같습니다.

```json
{  
    "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc"
}
```

다음 명령을 사용하여 runId를 가져올 수도 있습니다. 
```powershell

($response.content | ConvertFrom-Json).runId

```

## <a name="parameterize-your-pipeline"></a>파이프라인 매개 변수화 

매개 변수를 사용하여 파이프라인을 만들 수 있습니다. 다음 예제에서는 입력 및 출력 파일 이름을 파이프라인에 지정된 매개 변수로 사용할 수 있는 입력 데이터 세트 및 출력 데이터 세트를 만듭니다. 


## <a name="create-parameterized-input-dataset"></a>매개 변수화된 입력 데이터 세트 만들기 

strInputFileName이라는 매개 변수를 정의하고 데이터 세트의 파일 이름으로 사용합니다. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/ParamInputDataset?api-version=${apiVersion}"

$body = @"
{
    "name": "ParamInputDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strInputFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strInputFileName",
                    "type": "Expression"
                },
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content

```

샘플 출력은 다음과 같습니다. 

```json
{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/datasets/ParamInputDataset",
    "name": "ParamInputDataset",
    "type": "Microsoft.DataFactory/factories/datasets",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strInputFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strInputFileName",
                    "type": "Expression"
                },
                "folderPath": "input",
                "container": "adftutorial"
            }
        }
    },
    "etag": "00000000-0000-0000-0000-000000000000"
}

```


## <a name="create-parameterized-output-dataset"></a>매개 변수화된 출력 데이터 세트 만들기 


strOutputFileName이라는 매개 변수를 정의하고 데이터 세트의 파일 이름으로 사용합니다. 

```powershell


$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/ParamOutputDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "ParamOutputDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strOutPutFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strOutPutFileName",
                    "type": "Expression"
                },
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}

"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content

```

샘플 출력은 다음과 같습니다. 

```json
{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/datasets/ParamOutputDataset",
    "name": "ParamOutputDataset",
    "type": "Microsoft.DataFactory/factories/datasets",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "strOutPutFileName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "Binary",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@dataset().strOutPutFileName",
                    "type": "Expression"
                },
                "folderPath": "output",
                "container": "adftutorial"
            }
        }
    },
    "etag": "00000000-0000-0000-0000-000000000000"
}
```

## <a name="create-parameterized-pipeline"></a>매개 변수화된 파이프라인 만들기 

두 개의 파이프라인 수준 매개 변수 strParamInputFileName 및 strParamOutputFileName을 사용하여 파이프라인을 정의합니다. 그런 다음 이러한 두 매개 변수를 데이터 세트의 strInputFileName 및 strOutputFileName 매개 변수에 연결합니다. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartParamPipeline?api-version=${apiVersion}"

$body = @"
{
    "name": "Adfv2QuickStartParamPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "ParamInputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strInputFileName": {
                                "value": "@pipeline().parameters.strParamInputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "ParamOutputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strOutPutFileName": {
                                "value": "@pipeline().parameters.strParamOutputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],   

        "parameters": {
            "strParamInputFileName": {
              "type": "String"
            },
            "strParamOutputFileName": {
              "type": "String"
            }
          }
    }
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method PUT -Payload $body
$response.content


```

샘플 출력은 다음과 같습니다. 

```json

{
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelines/Adfv2QuickStartParamPipeline",
    "name": "Adfv2QuickStartParamPipeline",
    "type": "Microsoft.DataFactory/factories/pipelines",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "BinarySource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        }
                    },
                    "sink": {
                        "type": "BinarySink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging": false
                },
                "inputs": [
                    {
                        "referenceName": "ParamInputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strInputFileName": {
                                "value": "@pipeline().parameters.strParamInputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "ParamOutputDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "strOutPutFileName": {
                                "value": "@pipeline().parameters.strParamOutputFileName",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "parameters": {
            "strParamInputFileName": {
                "type": "String"
            },
            "strParamOutputFileName": {
                "type": "String"
            }
        }
    },
    "etag": "5e01918d-0000-0100-0000-60d569a90000"
}

```

## <a name="create-pipeline-run-with-parameters"></a>매개 변수를 사용하여 파이프라인 실행 만들기 

이제 파이프라인 실행을 만들 때 매개 변수의 값을 지정할 수 있습니다. 

```powershell

$path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartParamPipeline/createRun?api-version=${apiVersion}"

$body = @"
{  
        "strParamInputFileName": "emp2.txt",
        "strParamOutputFileName": "aloha.txt"
}
"@

$response =  Invoke-AzRestMethod  -Path ${path}  -Method POST -Payload $body
$response.content
$runId  = ($response.content | ConvertFrom-Json).runId

```
샘플 출력은 다음과 같습니다. 

```json
{"runId":"ffc9c2a8-d86a-46d5-9208-28b3551007d8"}
```


## <a name="monitor-pipeline"></a>파이프라인 모니터링

1. 다음 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다.

    ```powershell
        $path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
        
        
        while ($True) {
        
            $response =  Invoke-AzRestMethod  -Path ${path}  -Method GET 
            $response = $response.content | ConvertFrom-Json
        
            Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"
        
            if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") -or ($response.Status -eq "In Progress") ) {
                Start-Sleep -Seconds 10
            }
            else {
                $response | ConvertTo-Json
                break
            }
        }
    ```

    샘플 출력은 다음과 같습니다.

    ```json
        {
          "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelineruns/ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "runId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "debugRunId": null,
          "runGroupId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "pipelineName": "Adfv2QuickStartParamPipeline",
          "parameters": {
            "strParamInputFileName": "emp2.txt",
            "strParamOutputFileName": "aloha.txt"
          },
          "invokedBy": {
            "id": "9c0275ed99994c18932317a325276544",
            "name": "Manual",
            "invokedByType": "Manual"
          },
          "runStart": "2021-06-25T05:34:06.8424413Z",
          "runEnd": "2021-06-25T05:34:13.2936585Z",
          "durationInMs": 6451,
          "status": "Succeeded",
          "message": "",
          "lastUpdated": "2021-06-25T05:34:13.2936585Z",
          "annotations": [],
          "runDimension": {},
          "isLatest": true
        }
    ```

2. 복사 활동 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 스크립트를 실행합니다.

    ```powershell
         $path = "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}/queryActivityruns?api-version=${apiVersion}"
        
        
        while ($True) {
        
            $response =  Invoke-AzRestMethod  -Path ${path}  -Method POST 
            $responseContent = $response.content | ConvertFrom-Json
            $responseContentValue = $responseContent.value
        
            Write-Host  "Activity run status: " $responseContentValue.Status -foregroundcolor "Yellow"
        
            if ( ($responseContentValue.Status -eq "InProgress") -or ($responseContentValue.Status -eq "Queued") -or ($responseContentValue.Status -eq "In Progress") ) {
                Start-Sleep -Seconds 10
            }
            else {
                $responseContentValue | ConvertTo-Json
                break
            }
        }
    ```
    샘플 출력은 다음과 같습니다.

    ```json
        {
          "activityRunEnd": "2021-06-25T05:34:11.9536764Z",
          "activityName": "CopyFromBlobToBlob",
          "activityRunStart": "2021-06-25T05:34:07.5161151Z",
          "activityType": "Copy",
          "durationInMs": 4437,
          "retryAttempt": null,
          "error": {
            "errorCode": "",
            "message": "",
            "failureType": "",
            "target": "CopyFromBlobToBlob",
            "details": ""
          },
          "activityRunId": "40bab243-9bbf-4538-9336-b797a2f98e2b",
          "iterationHash": "",
          "input": {
            "source": {
              "type": "BinarySource",
              "storeSettings": "@{type=AzureBlobStorageReadSettings; recursive=True}"
            },
            "sink": {
              "type": "BinarySink",
              "storeSettings": "@{type=AzureBlobStorageWriteSettings}"
            },
            "enableStaging": false
          },
          "linkedServiceName": "",
          "output": {
            "dataRead": 134,
            "dataWritten": 134,
            "filesRead": 1,
            "filesWritten": 1,
            "sourcePeakConnections": 1,
            "sinkPeakConnections": 1,
            "copyDuration": 3,
            "throughput": 0.044,
            "errors": [],
            "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
            "usedDataIntegrationUnits": 4,
            "billingReference": {
              "activityType": "DataMovement",
              "billableDuration": ""
            },
            "usedParallelCopies": 1,
            "executionDetails": [
              "@{source=; sink=; status=Succeeded; start=06/25/2021 05:34:07; duration=3; usedDataIntegrationUnits=4; usedParallelCopies=1; profile=; detailedDurations=}"
            ],
            "dataConsistencyVerification": {
              "VerificationResult": "NotVerified"
            },
            "durationInQueue": {
              "integrationRuntimeQueue": 0
            }
          },
          "userProperties": {},
          "pipelineName": "Adfv2QuickStartParamPipeline",
          "pipelineRunId": "ffc9c2a8-d86a-46d5-9208-28b3551007d8",
          "status": "Succeeded",
          "recoveryStatus": "None",
          "integrationRuntimeNames": [
            "defaultintegrationruntime"
          ],
          "executionDetails": {
            "integrationRuntime": [
              "@{name=DefaultIntegrationRuntime; type=Managed; location=East US; nodes=}"
            ]
          },
          "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<factoryName>/pipelineruns/ffc9c2a8-d86a-46d5-9208-28b3551007d8/activityruns/40bab243-9bbf-4538-9336-b797a2f98e2b"
        }
    ```
## <a name="verify-the-output"></a>출력 확인

Azure Storage 탐색기를 사용하여 파이프라인 실행을 만들 때 지정한 대로 파일이 "inputPath"에서 "outputPath"로 복사되었는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작에서 만든 리소스는 두 가지 방법으로 정리할 수 있습니다. 리소스 그룹의 모든 리소스를 포함하고 있는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)을 삭제할 수 있습니다. 다른 리소스를 그대로 유지하려면 이 자습서에서 만든 데이터 팩터리만 삭제합니다.

다음 명령을 실행하여 전체 리소스 그룹을 삭제합니다.
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

다음 명령을 실행하여 데이터 팩터리만 삭제합니다.

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>다음 단계
이 샘플의 파이프라인은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요.
