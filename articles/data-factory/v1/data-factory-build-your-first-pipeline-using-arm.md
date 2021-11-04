---
title: 첫 번째 데이터 팩터리(Resource Manager 템플릿) 빌드
description: 이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: tutorial
ms.date: 10/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 25309de23f1a819dc26a21e486fb96c0beac5b7b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040816"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>자습서: Azure Resource Manager 템플릿을 사용하여 첫 번째 Azure Data Factory 빌드
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 템플릿](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
 
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [빠른 시작: Azure Data Factory를 사용하여 데이터 팩터리 만들기](../quickstart-create-data-factory-dot-net.md)를 참조하세요.

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 첫 번째 Azure Data Factory를 만듭니다. 다른 도구/SDK를 사용하여 이 자습서를 수행하려면 드롭다운 목록에서 옵션 중 하나를 선택합니다.

이 자습서의 파이프라인에는 **HDInsight Hive 작업** 이라는 하나의 작업이 있습니다. 이 작업은 Azure HDInsight 클러스터에서 입력 데이터를 변환하여 출력 데이터를 생성하는 Hive 스크립트를 실행합니다. 파이프라인은 지정된 시작 및 종료 시간 사이, 한 달에 한 번 실행되도록 예약됩니다. 

> [!NOTE]
> 이 자습서의 데이터 파이프라인은 출력 데이터를 생성하는 입력 데이터를 변환합니다. Azure Data Factory를 사용하여 데이터를 복사하는 방법에 대한 자습서는 [자습서: Blob Storage에서 SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
> 
> 이 자습서의 파이프라인에는 활동 유형이 HDInsightHive 하나뿐입니다. 파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [Data Factory에서 예약 및 실행](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [자습서 개요](data-factory-build-your-first-pipeline.md) 문서를 살펴보고 **필수 구성 요소** 단계를 완료합니다.
* [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/) 문서의 지침을 수행하여 컴퓨터에 Azure PowerShell의 최신 버전을 설치합니다.
* Azure Resource Manager 템플릿에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/templates/syntax.md) 을 참조하세요. 

## <a name="in-this-tutorial"></a>자습서 내용

| 엔터티 | Description |
| --- | --- |
| Azure Storage 연결된 서비스 |Azure Storage 계정을 데이터 팩터리에 연결합니다. Azure Storage 계정은 이 샘플의 파이프라인에 대한 입력 및 출력 데이터를 가집니다. |
| HDInsight 주문형 연결된 서비스 |주문형 HDInsight 클러스터를 데이터 팩터리에 연결합니다. 이 클러스터는 사용자가 데이터를 처리할 수 있도록 자동으로 생성되며 처리 작업이 완료되면 삭제됩니다. |
| Azure Blob 입력 데이터 세트 |Azure Storage 연결된 서비스를 참조하세요. 연결된 서비스는 Azure Storage 계정을 말하며 Azure Blob 데이터 세트은 입력 데이터를 가진 스토리지의 컨테이너, 폴더, 파일 이름을 지정합니다. |
| Azure Blob 출력 데이터 세트 |Azure Storage 연결된 서비스를 참조하세요. 연결된 서비스는 Azure Storage 계정을 말하며 Azure Blob 데이터 세트은 출력 데이터를 가진 스토리지의 컨테이너, 폴더, 파일 이름을 지정합니다. |
| 데이터 파이프라인 |파이프라인에는 HDInsightHive가 입력 데이터 세트를 사용하여 출력 데이터 세트를 생성하는 활동 유형이 하나 있습니다. |

데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 이러한 두 가지 유형의 활동은 [데이터 이동 활동](data-factory-data-movement-activities.md) 및 [데이터 변환 활동](data-factory-data-transformation-activities.md)입니다. 이 자습서에는 한 가지 활동(Hive 활동)이 있는 파이프라인을 만듭니다.

다음 섹션에서는 신속하게 자습서를 살펴보고 템플릿을 테스트할 수 있도록 데이터 팩터리 엔터티를 정의하기 위한 완전한 Resource Manager 템플릿을 제공합니다. 각 데이터 팩터리 엔터티를 정의하는 방법을 알아보려면 [템플릿의 데이터 팩터리 엔터티](#data-factory-entities-in-the-template) 섹션을 참조하세요. 템플릿에서 Data Factory 리소스의 JSON 구문 및 속성에 대해 알아보려면 [Microsoft.DataFactory 리소스 종류](/azure/templates/microsoft.datafactory/allversions)를 참조하세요.

## <a name="data-factory-json-template"></a>데이터 팩터리 JSON 템플릿
데이터 팩터리 정의를 위한 최상위 Resource Manager 템플릿은 다음과 같습니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/factories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
**C:\ADFGetStarted** 폴더에 다음과 같은 내용으로 **ADFTutorialARM.json** 이라는 JSON 파일을 만듭니다.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "version": "3.5",
                    "clusterSize": 1,
                    "timeToLive": "00:05:00",
                    "osType": "Linux",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2017-07-01T00:00:00Z",
                  "end": "2017-07-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> 다음 위치에서 Azure Data Factory를 만드는 Resource Manager 템플릿의 또 다른 예를 찾을 수 있습니다. [자습서: Azure Resource Manager 템플릿을 사용하여 복사 작업이 있는 파이프라인 만들기](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)  
> 
> 

## <a name="parameters-json"></a>매개 변수 JSON
Azure Resource Manager 템플릿에 대한 매개 변수를 포함하는 **ADFTutorialARM-Parameters.json** 이라는 JSON 파일을 만듭니다.  

> [!IMPORTANT]
> 이 매개 변수 파일의 **storageAccountName** 및 **storageAccountKey** 매개 변수에 대한 Azure Storage 계정 이름과 키를 지정합니다. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> 개발, 테스트 및 프로덕션 환경에 별도의 매개 변수 JSON 파일을 두고 동일한 데이터 팩터리 JSON 템플릿을 사용할 수 있습니다. Power Shell 스크립트를 사용하여 이러한 환경에서 데이터 팩터리 엔터티 배포를 자동화할 수 있습니다.

## <a name="create-data-factory"></a>데이터 팩터리 만들기

1. **Azure PowerShell** 을 시작하고 다음 명령을 실행합니다.

   * 다음 명령을 실행하고 Azure 포털에 로그인하는 데 사용할 사용자 이름 및 암호를 입력합니다.

     ```powershell
     Connect-AzAccount
     ```

   * 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

     ```powershell
     Get-AzSubscription
     ```

   * 다음 명령을 실행하여 사용하려는 구독을 선택합니다. 이 구독은 Azure 포털에서 사용한 것과 같아야 합니다.

     ```powershell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```

2. 1단계에서 만든 Resource Manager 템플릿을 사용하여 데이터 팩터리 엔터티를 배포하려면 다음 명령을 실행합니다. 

   ```powershell
   New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
   ```

## <a name="monitor-pipeline"></a>파이프라인 모니터링

1. [Azure Portal](https://portal.azure.com/)에 로그인한 후 **찾아보기** 를 클릭하고 **데이터 팩터리** 를 선택합니다.
     :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png" alt-text="찾아보기->데이터 팩터리":::
2. **데이터 팩터리** 블레이드에서 만든 데이터 팩터리(**TutorialFactoryARM**)를 클릭합니다.    
3. 데이터 팩터리의 **데이터 팩터리** 블레이드에서 **다이어그램** 을 클릭합니다.

     :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png" alt-text="다이어그램 타일":::
4. **다이어그램 보기** 에 파이프라인의 개요와 이 자습서에 사용된 데이터 세트가 표시됩니다.
   
   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png" alt-text="다이어그램 뷰"::: 
5. 다이어그램 보기에서 **AzureBlobOutput** 데이터 세트를 두 번 클릭합니다. 현재 처리 중인 조각이 표시됩니다.
   
    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png" alt-text="AzureBlobOutput 데이터 세트를 보여주는 스크린샷.":::
6. 처리가 완료되면 **준비** 상태인 조각이 표시됩니다. 주문형 HDInsight 클러스터 만들기는 일반적으로 시간이 소요됩니다.(대략 20분) 따라서 파이프라인이 조각을 처리하는 데 **약 30분** 이 걸릴 수 있습니다.
   
    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png" alt-text="데이터 세트":::    
7. 조각이 **준비** 상태에 있으면 출력 데이터에 대한 Blob Storage의 **adfgetstarted** 컨테이너에 있는 **partitioneddata** 폴더를 확인합니다.  

Azure 포털 블레이드를 사용하여 이 자습서에서 만든 파이프라인 및 데이터 세트를 모니터링하는 방법에 대한 지침은 [데이터 세트 및 파이프라인 모니터링](data-factory-monitor-manage-pipelines.md) 을 참조하세요.

앱 모니터링 및 관리를 사용하여 데이터 파이프라인을 모니터링할 수도 있습니다. 애플리케이션을 사용하는 방법에 대한 자세한 내용은 [앱 모니터링을 사용하여 Azure Data Factory 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요. 

> [!IMPORTANT]
> 조각이 성공적으로 처리될 때 입력된 파일이 삭제됩니다. 따라서 조각을 다시 실행하거나 자습서를 다시 수행하려는 경우 adfgetstarted 컨테이너의 inputdata 폴더에 입력 파일(input.log)을 업로드합니다.
> 
> 

## <a name="data-factory-entities-in-the-template"></a>템플릿의 데이터 팩터리 엔터티
### <a name="define-data-factory"></a>데이터 팩터리 정의
다음 예제에서처럼 Resource Manager 템플릿에서 데이터 팩터리를 정의합니다.  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/factories",
    "location": "West US"
}
```
dataFactoryName은 다음과 같이 정의됩니다. 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
리소스 그룹 ID를 기반으로 하는 고유 문자열입니다.  

### <a name="defining-data-factory-entities"></a>데이터 팩터리 엔터티 정의
다음 데이터 팩터리 엔터티는 JSON 템플릿에 정의됩니다. 

* [Azure Storage 연결된 서비스](#azure-storage-linked-service)
* [HDInsight 주문형 연결된 서비스](#hdinsight-on-demand-linked-service)
* [Azure Blob 입력 데이터 세트](#azure-blob-input-dataset)
* [Azure Blob 출력 데이터 세트:](#azure-blob-output-dataset)
* [복사 작업을 포함하는 데이터 파이프라인](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
이 섹션의 Azure Storage 계정 이름 및 키를 지정합니다. Azure Storage 연결된 서비스를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Storage 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-linked-service)를 참조하세요. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** 은 storageAccountName 및 storageAccountKey 매개 변수를 사용합니다. 이러한 매개 변수의 값은 구성 파일을 사용하여 전달됩니다. 정의 또한 템플릿에 정의된 azureStorageLinkedService 및 dataFactoryName 변수를 사용합니다. 

#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight 주문형 연결된 서비스
HDInsight 주문형 연결된 서비스를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [컴퓨팅 연결 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) 문서를 참조하세요.  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```

다음 사항에 유의하세요. 

* 데이터 팩터리는 위의 JSON으로 사용자에게 **Linux 기반** HDInsight 클러스터를 만들어 줍니다. 자세한 내용은 [주문형 HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) 를 참조하세요. 
* 주문형 HDInsight 클러스터를 사용하는 대신 **고유의 HDInsight 클러스터** 를 사용할 수 있습니다. 자세한 내용은 [HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 를 참조하세요.
* HDInsight 클러스터는 JSON(**linkedServiceName**)에서 지정한 Blob Storage에 **기본 컨테이너** 를 만듭니다. HDInsight는 클러스터가 삭제될 때 이 컨테이너를 삭제하지 않습니다. 이 동작은 의도된 것입니다. 주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(**timeToLive**)가 없는 한 슬라이스를 처리해야 할 때마다 HDInsight 클러스터가 만들어지며 처리가 완료되면 삭제됩니다.
  
    온-프레미스 응용 프로그램은 File Storage REST API를 호출하여 파일 공유의 데이터에 액세스할 수 있습니다. 작업의 문제 해결을 위해 이 항목들이 필요하지 않다면 스토리지 비용을 줄이기 위해 삭제할 수 있습니다. 이 컨테이너의 이름은 "adf **yourdatafactoryname**-**linkedservicename**-datetimestamp" 패턴을 따릅니다. [Microsoft Azure Storage Explorer](https://storageexplorer.com/) 같은 도구를 사용하여 Azure Blob 스토리지에서 컨테이너를 삭제합니다.

자세한 내용은 [주문형 HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) 를 참조하세요.

#### <a name="azure-blob-input-dataset"></a>Azure Blob 입력 데이터 세트
입력 데이터를 포함하는 Blob 컨테이너, 폴더 및 파일의 이름을 지정할 수 있습니다. Azure Blob 데이터 세트를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Blob 데이터 세트 속성](data-factory-azure-blob-connector.md#dataset-properties)을 참조하세요. 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
이 정의는 매개 변수 템플릿에 정의된 매개 변수 blobContainer, inputBlobFolder 및 inputBlobName을 사용합니다. 

#### <a name="azure-blob-output-dataset"></a>Azure Blob 출력 데이터 세트
출력 데이터를 포함하는 Blob 컨테이너 및 폴더의 이름을 지정합니다. Azure Blob 데이터 세트를 정의하는 데 사용되는 JSON 속성에 대한 자세한 내용은 [Azure Blob 데이터 세트 속성](data-factory-azure-blob-connector.md#dataset-properties)을 참조하세요.  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

이 정의는 매개 변수 템플릿에 정의된 매개 변수 blobContainer 및 outputBlobFolder를 사용합니다. 

#### <a name="data-pipeline"></a>데이터 파이프라인
주문형 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하여 데이터를 변환하는 파이프라인을 정의합니다. 이 예에서 파이프라인을 정의하는 데 사용된 JSON 요소에 대한 자세한 설명은 [파이프라인 JSON](data-factory-create-pipelines.md#pipeline-json)을 참조하세요. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                    "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                        "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                    }
                },
                "inputs": [
                    {
                        "name": "[variables('blobInputDatasetName')]"
                    }
                ],
                "outputs": [
                    {
                        "name": "[variables('blobOutputDatasetName')]"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
            }
        ],
        "start": "2017-07-01T00:00:00Z",
        "end": "2017-07-02T00:00:00Z",
        "isPaused": false
    }
}
```

## <a name="reuse-the-template"></a>템플릿 재사용
이 자습서에서는 데이터 팩터리 엔터티를 정의하는 템플릿과 매개 변수 값을 전달하는 템플릿을 만들었습니다. 같은 템플릿을 사용하여 데이터 팩터리 엔터티를 다른 환경에 배포하는 데 사용하려면 각 환경에 대한 매개 변수 파일을 만들고 해당 환경에 배포할 때 사용합니다.

예제:

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

첫 번째 명령은 개발 환경에 대한 매개 변수 파일, 두 번째 명령은 테스트 환경에 대한 매개 변수 파일, 세 번째 명령은 프로덕션 환경에 대한 매개 변수 파일을 사용합니다.  

또한 이 템플릿을 재사용하여 반복 작업을 수행할 수 있습니다. 예를 들어 동일한 논리를 구현하는 하나 이상의 파이프라인으로 여러 데이터 팩터리를 만들어야 하지만 각 데이터 팩터리가 서로 다른 Azure 스토리지 및 SQL Database 계정을 사용합니다. 이 경우 매개 변수가 서로 다른 동일한 환경(개발, 테스트 또는 프로덕션)에서 동일한 템플릿을 사용합니다. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>게이트웨이를 만드는 Resource Manager 템플릿
나중에 논리 게이트웨이를 만드는 샘플 Resource Manager 템플릿입니다. 온-프레미스 컴퓨터 또는 Azure IaaS VM에 게이트웨이 설치하고 키를 사용하여 데이터 팩터리 서비스에 게이트웨이를 등록합니다. 자세한 내용은 [온-프레미스 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 을 참조하세요.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/factories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/factories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
이 템플릿은 GatewayUsingARM이라는 게이트웨이를 포함한 GatewayUsingArmDF라는 데이터 팩터리를 만듭니다. 

## <a name="see-also"></a>참고 항목

| 항목 | Description |
|:--- |:--- |
| [파이프라인](data-factory-create-pipelines.md) |이 문서는 Azure Data Factory의 파이프라인 및 시나리오 또는 비즈니스를 위한 활동과 엔드투엔드 데이터 기반 워크플로 활용하는 방법을 이해하는 데 도움이 됩니다. |
| [데이터 세트](data-factory-create-datasets.md) |이 문서는 Azure Data Factory의 데이터 세트를 이해하는 데 도움이 됩니다. |
| [예약 및 실행](data-factory-scheduling-and-execution.md) |이 문서에서는 Azure Data Factory 애플리케이션 모델의 예약 및 실행에 대한 내용을 설명합니다. |
| [모니터링 앱을 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) |이 문서는 모니터링 및 관리 앱을 사용하여 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. |