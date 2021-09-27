---
title: If Condition 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: If Condition 작업 사용하면 Azure Data Factory 또는 Synapse Analytics 파이프라인의 조건에 따라 처리 흐름을 제어할 수 있습니다.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.date: 09/09/2021
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: 04c0a91a7f4848f2422c0f6ed1e117686345bf78
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811689"
---
# <a name="if-condition-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Azure Data Factory 및 Synapse Analytics 파이프라인의 If Condition 작업
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

If 조건 작업은 if 문에서 프로그래밍 언어로 제공하는 것과 동일한 기능을 제공합니다. 조건이 `true`로 평가되면 작업 집합을 실행하고, 조건이 `false`로 평가되면 다른 작업 집합을 실행합니다. 

## <a name="syntax"></a>구문

```json

{
    "name": "<Name of the activity>",
    "type": "IfCondition",
    "typeProperties": {
            "expression": {
            "value": "<expression that evaluates to true or false>",
            "type": "Expression"
            },

            "ifTrueActivities": [
            {
                "<Activity 1 definition>"
            },
            {
                "<Activity 2 definition>"
            },
            {
                "<Activity N definition>"
            }
        ],

        "ifFalseActivities": [
            {
                "<Activity 1 definition>"
            },
            {
                "<Activity 2 definition>"
            },
            {
                "<Activity N definition>"
            }
            ]
    }
}
```

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | if 조건 작업의 이름입니다. | String | 예
type | **IfCondition** 으로 설정해야 함 | String | 예
식 | true 또는 false로 평가되어야 하는 식입니다. | 결과 형식이 부울인 식 | 예
ifTrueActivities | 식이 `true`로 평가될 때 실행되는 작업 집합입니다. | Array | 예
ifFalseActivities | 식이 `false`로 평가될 때 실행되는 작업 집합입니다. | Array | 예

## <a name="example"></a>예제
이 샘플의 파이프라인에서는 입력 폴더의 데이터를 출력 폴더로 복사합니다. 출력 폴더는 파이프라인 매개 변수 routeSelection의 값에 의해 결정됩니다. routeSelection의 값이 true이면 데이터가 outputPath1로 복사됩니다. 또한 routeSelection의 값이 false이면 데이터가 outputPath2로 복사됩니다. 

> [!NOTE]
> 이 섹션에서는 파이프라인을 실행하는 JSON 정의 및 샘플 PowerShell 명령을 제공합니다. Azure PowerShell 및 JSON 정의를 사용하여 파이프라인을 만드는 단계별 지침이 포함된 연습은 자습서: Azure PowerShell [사용하여 데이터 팩터리 만들기를](quickstart-create-data-factory-powershell.md)참조하세요.

### <a name="pipeline-with-if-condition-activity-adfv2quickstartpipelinejson"></a>IF 조건 작업이 포함된 파이프라인(Adfv2QuickStartPipeline.json)

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "MyIfCondition",
                "type": "IfCondition",
                "typeProperties": {
                    "expression":  {
                        "value":  "@bool(pipeline().parameters.routeSelection)", 
                        "type": "Expression"
                     },
                                           
                    "ifTrueActivities": [
                        {
                            "name": "CopyFromBlobToBlob1",
                            "type": "Copy",
                            "inputs": [
                                {
                                    "referenceName": "BlobDataset",
                                    "parameters": {
                                        "path": "@pipeline().parameters.inputPath"
                                    },
                                    "type": "DatasetReference"
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "BlobDataset",
                                    "parameters": {
                                        "path": "@pipeline().parameters.outputPath1"
                                    },
                                    "type": "DatasetReference"
                                }
                            ],
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource"
                                },
                                "sink": {
                                    "type": "BlobSink"
                                }
                            }
                        }                                   
                    ],
                    "ifFalseActivities": [
                        {
                            "name": "CopyFromBlobToBlob2",
                            "type": "Copy",
                            "inputs": [
                                {
                                    "referenceName": "BlobDataset",
                                    "parameters": {
                                        "path": "@pipeline().parameters.inputPath"
                                    },
                                    "type": "DatasetReference"
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "BlobDataset",
                                    "parameters": {
                                        "path": "@pipeline().parameters.outputPath2"
                                    },
                                    "type": "DatasetReference"
                                }
                            ],
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource"
                                },
                                "sink": {
                                    "type": "BlobSink"
                                }
                            }
                        }
                    ]                    
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath1": {
                "type": "String"
            },
            "outputPath2": {
                "type": "String"
            },
            "routeSelection": {
                "type": "String"
            }                        
        }
    }
}
```

식에 대한 다른 예제는 다음과 같습니다. 

```json
"expression":  {
    "value":  "@equals(pipeline().parameters.routeSelection,1)", 
    "type": "Expression"
}
```


### <a name="azure-storage-linked-service-azurestoragelinkedservicejson"></a>Azure Storage 연결 서비스(AzureStorageLinkedService.json)

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<Azure Storage account name>;AccountKey=<Azure Storage account key>"
        }
    }
}
```

### <a name="parameterized-azure-blob-dataset-blobdatasetjson"></a>매개 변수화된 Azure Blob 데이터 세트(BlobDataset.json)
파이프라인은 **folderPath** 를 파이프라인의 **outputPath1** 또는 **outputPath2** 매개 변수 중 하나의 값으로 설정합니다. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="pipeline-parameter-json-pipelineparametersjson"></a>파이프라인 매개 변수 JSON(PipelineParameters.json)

```json
{
    "inputPath": "adftutorial/input",
    "outputPath1": "adftutorial/outputIf",
    "outputPath2": "adftutorial/outputElse",
    "routeSelection": "false"
}
```

### <a name="powershell-commands"></a>PowerShell 명령

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이러한 명령에서는 JSON 파일이 C:\ADF 폴더에 저장되어 있다고 가정합니다. 

```powershell
Connect-AzAccount
Select-AzSubscription "<Your subscription name>"

$resourceGroupName = "<Resource Group Name>"
$dataFactoryName = "<Data Factory Name. Must be globally unique>";
Remove-AzDataFactoryV2 $dataFactoryName -ResourceGroupName $resourceGroupName -force


Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile "C:\ADF\AzureStorageLinkedService.json"
Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile "C:\ADF\BlobDataset.json"
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile "C:\ADF\Adfv2QuickStartPipeline.json"
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile C:\ADF\PipelineParameters.json
while ($True) {
    $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

    if ($run) {
        if ($run.Status -ne 'InProgress') {
            Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
            $run
            break
        }
        Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
    }

    Start-Sleep -Seconds 30
}
Write-Host "Activity run details:" -foregroundcolor "Yellow"
$result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
$result

Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

## <a name="next-steps"></a>다음 단계
지원되는 다른 제어 흐름 작업을 참조하세요. 

- [파이프라인 작업 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
