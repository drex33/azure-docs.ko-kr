---
title: Azure Data Factory를 사용하여 Blob Storage 데이터 복사
description: PowerShell을 사용하여 Azure Data Factory를 만들어 Azure Blob 스토리지의 한 위치에서 다른 위치로 데이터를 복사합니다.
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.devlang: powershell
ms.topic: quickstart
ms.date: 04/10/2020
ms.author: jingwang
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: bc56de487b5c83f07bd3c71de7f79a9702084317
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133050781"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Data Factory 만들기

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [현재 버전](quickstart-create-data-factory-powershell.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 빠른 시작에서는 PowerShell을 사용하여 Azure Data Factory를 만드는 방법을 설명합니다. 이 데이터 팩터리에서 만든 파이프라인은 Azure Blob Storage의 한 폴더에서 다른 폴더로 데이터를 **복사합니다**. Azure Data Factory를 사용하여 데이터를 **변환** 하는 방법에 대한 자습서는 [자습서: Spark를 사용하여 데이터 변환](transform-data-using-spark.md)을 참조하세요.

> [!NOTE]
> 이 문서는 Data Factory 서비스의 자세한 소개를 제공하지 않습니다. Azure Data Factory 서비스 소개는 [Azure Data Factory 소개](introduction.md)를 참조하세요.

[!INCLUDE [data-factory-quickstart-prerequisites](includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

>[!WARNING]
>최신 버전의 PowerShell 및 Data Factory 모듈을 사용하지 않는 경우 명령을 실행하는 동안 역직렬화 오류가 발생할 수 있습니다. 

#### <a name="log-in-to-powershell"></a>PowerShell에 로그인

1. 컴퓨터에서 **PowerShell** 을 시작합니다. 이 빠른 시작을 완료할 때까지 PowerShell을 열어 둡니다. 닫은 후 다시 여는 경우 이러한 명령을 다시 실행해야 합니다.

2. 다음 명령을 실행하고 Azure Portal에 로그인하는 데 사용할 동일한 Azure사용자 이름 및 암호를 입력합니다.

    ```powershell
    Connect-AzAccount
    ```

3. 다음 명령을 실행하여 이 계정의 모든 구독을 확인합니다.

    ```powershell
    Get-AzSubscription
    ```

4. 계정과 연결된 구독이 여러 개인 경우 다음 명령을 실행하여 사용하려는 구독을 선택합니다. **SubscriptionId** 를 Azure 구독의 ID로 바꿉니다.

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 나중에 PowerShell 명령에서 사용할 리소스 그룹 이름에 대한 변수를 정의합니다. PowerShell에 다음 명령 텍스트를 복사하고, 큰따옴표에 있는 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름을 지정하고, 명령을 실행합니다. 예: `"ADFQuickStartRG"`

    ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$ResourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.

2. 새 리소스 그룹을 만들려면 다음 명령을 실행합니다.

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    리소스 그룹이 이미 있는 경우 덮어쓰지 않는 것이 좋습니다. `$ResourceGroupName` 변수에 다른 값을 할당하고 명령을 다시 시도하세요.

3. 데이터 팩터리 이름에 대한 변수를 정의합니다. 

    > [!IMPORTANT]
    >  데이터 팩터리 이름을 전역적으로 고유한 이름으로 업데이트합니다. 예를 들어 ADFTutorialFactorySP1127이라는 이름을 사용합니다.

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. 데이터 팩터리를 만들려면 $ResGrp 변수의 Location 및 ResourceGroupName 속성을 사용하여 다음 **Set-AzDataFactoryV2** cmdlet을 실행합니다.

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

다음 사항에 유의하세요.

* Azure Data Factory 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 이름을 변경하고 다시 시도하세요.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정은 **참여자** 또는 **소유자** 역할의 구성원이거나, 또는 Azure 구독의 **관리자** 이어야 합니다.

* 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics** 를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.


## <a name="create-a-linked-service"></a>연결된 서비스 만들기

데이터 팩터리에서 연결된 서비스를 만들어 데이터 저장소 및 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. 이 빠른 시작에서 원본 및 싱크 저장소로 사용되는 Azure Storage 연결된 서비스를 만듭니다. 연결된 서비스에는 Data Factory 서비스가 런타임에 연결하는 데 사용하는 연결 정보가 있습니다.

>[!TIP]
>이 빠른 시작에서는 *계정 키* 를 데이터 저장소의 인증 형식으로 사용하지만 지원되는 다른 인증 방법을 선택할 수 있습니다. 필요한 경우 *SAS URI*, *서비스 주체* 및 *관리 ID* 를 선택합니다. 자세한 내용은 [이 문서](./connector-azure-blob-storage.md#linked-service-properties)의 해당 섹션을 참조하세요.
>데이터 저장소에 대한 비밀을 안전하게 저장하려면 Azure Key Vault를 사용하는 것도 좋습니다. 자세한 그림은 [이 문서](./store-credentials-in-key-vault.md)를 참조하세요.

1. 다음 콘텐츠가 포함된 **AzureStorageLinkedService.json** 이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다. (아직 없는 경우 ADFv2QuickStartPSH 폴더를 만듭니다.)

    > [!IMPORTANT]
    > 파일을 저장하기 전에 &lt;accountName&gt;과 &lt;accountKey&gt;를 Azure Storage 계정의 이름과 키로 바꿉니다.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
    ```

    메모장을 사용하는 경우 **다른 이름으로 저장** 대화 상자의 **파일 형식** 필드에서 **모든 파일** 을 선택합니다. 선택하지 않으면 파일에 `.txt` 확장이 추가됩니다. `AzureStorageLinkedService.json.txt`)을 입력합니다. 메모장에서 파일을 열기 전에 파일 탐색기에서 파일을 만들면 **알려진 파일 형식의 확장명 숨기기** 옵션이 기본적으로 설정되어 `.txt` 확장이 보이지 않을 수 있습니다. `.txt` 확장을 제거한 후 다음 단계로 넘어갑니다.

2. **PowerShell** 에서 **ADFv2QuickStartPSH** 폴더로 전환합니다.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. **Set-AzDataFactoryV2LinkedService** cmdlet을 실행하여 연결된 서비스를 만듭니다. **AzureStorageLinkedService** 연결된 서비스를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

## <a name="create-datasets"></a>데이터 세트 만들기

이 프로시저에서는 두 개의 데이터 세트를 만듭니다. **InputDataset** 및 **OutputDataset** 이러한 데이터 세트는 **Binary** 유형입니다. 이 데이터 집합은 이전 섹션에서 만든 Azure Storage 연결된 서비스를 참조합니다.
입력 데이터 세트는 입력 폴더의 원본 데이터를 나타냅니다. 입력 데이터 세트 정의에서 원본 데이터가 포함된 Blob 컨테이너(**adftutorial**), 폴더(**input**) 및 파일(**emp.txt**)을 지정합니다.
출력 데이터 세트는 대상에 복사되는 데이터를 나타냅니다. 출력 데이터 세트 정의에서 Blob 컨테이너(**adftutorial**), 폴더(**output**) 및 데이터가 복사될 파일을 지정합니다. 
1. 다음 콘텐츠가 포함된 **InputDataset.json** 이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "fileName": "emp.txt",
                    "folderPath": "input",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

2. 데이터 세트 **InputDataset** 을 만들려면 **Set-AzDataFactoryV2Dataset** cmdlet을 실행합니다.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "InputDataset" `
        -DefinitionFile ".\InputDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```console
    DatasetName       : InputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```

3. 단계를 반복하여 출력 데이터 세트를 만듭니다. 다음 콘텐츠가 포함된 **OutputDataset.json** 이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "folderPath": "output",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

4. **Set-AzDataFactoryV2Dataset** cmdlet을 실행하여 **OutDataset** 를 만듭니다.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "OutputDataset" `
        -DefinitionFile ".\OutputDataset.json"
    ```

    샘플 출력은 다음과 같습니다.

    ```console
    DatasetName       : OutputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```
## <a name="create-a-pipeline"></a>파이프라인 만들기

이 절차에서는 입력 및 출력 데이터 세트를 사용하는 복사 작업이 포함된 파이프라인을 만듭니다. 복사 작업은 입력 데이터 세트 설정에 지정된 파일의 데이터를 출력 데이터 세트 설정에 지정된 파일로 복사합니다.  

1. 다음 콘텐츠가 포함된 **Adfv2QuickStartPipeline.json** 이라는 JSON 파일을 **C:\ADFv2QuickStartPSH** 폴더에 만듭니다.

    ```json
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
    ```

2. 파이프라인 **Adfv2QuickStartPipeline** 을 만들려면 **Set-AzDataFactoryV2Pipeline** cmdlet을 실행합니다.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>파이프라인 실행 만들기

이 단계에서는 파이프라인 실행을 만듭니다.

**Invoke-AzDataFactoryV2Pipeline** cmdlet을 실행하여 파이프라인 실행을 만듭니다. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

  ```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline `
    -DataFactoryName $DataFactory.DataFactoryName `
    -ResourceGroupName $ResGrp.ResourceGroupName `
    -PipelineName $DFPipeLine.Name 
```

## <a name="monitor-the-pipeline-run"></a>파이프라인 실행을 모니터링합니다.

1. 다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사/붙여넣기하고 ENTER 키를 누릅니다.

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ( ($Run.Status -ne "InProgress") -and ($Run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output ("Pipeline is running...status: " + $Run.Status)
        }

        Start-Sleep -Seconds 10
    }
    ```

    파이프라인 실행의 샘플 출력은 다음과 같습니다.

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    RunId             : 00000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 8/27/2019 7:23:07 AM
    Parameters        : {}
    RunStart          : 8/27/2019 7:22:56 AM
    RunEnd            : 8/27/2019 7:23:07 AM
    DurationInMs      : 11324
    Status            : Succeeded
    Message           : 
    ```
2. 복사 활동 실행 세부 정보(예: 읽고/쓴 데이터의 크기)를 검색하는 다음 스크립트를 실행합니다.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. 활동 실행 결과의 다음 샘플 출력과 유사한 출력이 표시되는지 확인합니다.

    ```console
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    ActivityRunId     : 00000000-0000-0000-0000-000000000000
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink, enableStaging}
    Output            : {dataRead, dataWritten, filesRead, filesWritten...}
    LinkedServiceName :
    ActivityRunStart  : 8/27/2019 7:22:58 AM
    ActivityRunEnd    : 8/27/2019 7:23:05 AM
    DurationInMs      : 6828
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 20
    "dataWritten": 20
    "filesRead": 1
    "filesWritten": 1
    "sourcePeakConnections": 1
    "sinkPeakConnections": 1
    "copyDuration": 4
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (Central US)"
    "usedDataIntegrationUnits": 4
    "usedParallelCopies": 1
    "executionDetails": [
      {
        "source": {
          "type": "AzureBlobStorage"
        },
        "sink": {
          "type": "AzureBlobStorage"
        },
        "status": "Succeeded",
        "start": "2019-08-27T07:22:59.1045645Z",
        "duration": 4,
        "usedDataIntegrationUnits": 4,
        "usedParallelCopies": 1,
        "detailedDurations": {
          "queuingDuration": 3,
          "transferDuration": 1
        }
      }
    ]
    
    Activity 'Error' section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "CopyFromBlobToBlob"
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 샘플의 파이프라인은 Azure Blob Storage의 한 위치에서 다른 위치로 데이터를 복사합니다. [자습서](tutorial-copy-data-dot-net.md)를 통해 더 많은 시나리오에서의 데이터 팩터리 사용에 관해 알아보세요.
