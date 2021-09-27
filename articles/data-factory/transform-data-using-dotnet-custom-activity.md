---
title: 파이프라인 내 사용자 지정 작업 사용
titleSuffix: Azure Data Factory & Azure Synapse
description: .NET을 사용하여 사용자 지정 작업을 만들고 Azure Data Factory 또는 Azure Synapse Analytics 파이프라인에서 사용하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: tutorials
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.custom: devx-track-azurepowershell, synapse
ms.date: 09/09/2021
ms.openlocfilehash: 5446bf3c2271fd31fc99e5fadb2f3ab92ca38e26
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806287"
---
# <a name="use-custom-activities-in-an-azure-data-factory-or-azure-synapse-analytics-pipeline"></a>Azure Data Factory 또는 Azure Synapse Analytics 파이프라인에서 사용자 지정 작업 사용

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-use-custom-activities.md)
> * [현재 버전](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 또는 Synapse 파이프라인에서 사용할 수 있는 두 가지 작업 유형이 있습니다.

- [데이터 이동 작업](copy-activity-overview.md)은 [지원되는 원본 및 싱크 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 간에 데이터를 이동하는 작업입니다.
- [데이터 변환 작업](transform-data.md)은 Azure HDInsight, Azure Batch, ML Studio(클래식)와 같은 Compute Services를 사용하여 데이터를 변환하는 작업입니다.

서비스에서 지원하지 않는 데이터 저장소 간에 데이터를 이동하거나, 서비스에서 지원하지 않는 방식으로 데이터를 변환/처리하려면 고유의 데이터 이동 또는 변환 논리가 포함된 **사용자 지정 작업** 을 만들어서 파이프라인에 해당 작업을 사용하면 됩니다. 사용자 지정 작업은 사용자 지정된 코드 논리를 가상 머신의 **Azure Batch** 풀에서 실행합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Batch 서비스가 처음이라면 다음 문서를 참조하세요.

* [Azure Batch 기본 사항](../batch/batch-technical-overview.md) 입니다.
* Azure Batch 계정을 만드는 [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) cmdlet 또는 Azure Portal을 사용하여 Azure Batch 계정을 만드는 [Azure Portal](../batch/batch-account-create-portal.md). 이 cmdlet 사용에 관한 자세한 지침은 [PowerShell을 사용하여 Azure Batch 계정 관리](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) 문서를 참조하세요.
* [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) cmdlet을 사용하여 Azure Batch 풀을 만듭니다.

> [!IMPORTANT]
> 새 Azure Batch 풀을 만들 때 ‘CloudServiceConfiguration’이 아니라 ‘VirtualMachineConfiguration’을 사용해야 합니다. 자세한 내용은 [Azure Batch 풀 마이그레이션 참고 자료](../batch/batch-pool-cloud-service-to-virtual-machine-configuration.md)를 참조하세요. 

## <a name="azure-batch-linked-service"></a>Azure Batch 연결된 서비스

다음 JSON은 샘플 Azure Batch 연결된 서비스를 정의합니다. 자세한 내용은 [지원되는 컴퓨팅 환경](compute-linked-services.md)을 참조하세요.

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Azure Batch 연결된 서비스에 대한 자세한 내용은 [연결된 컴퓨팅 서비스](compute-linked-services.md) 문서를 참조하세요.

## <a name="custom-activity"></a>사용자 지정 작업

다음 JSON 코드 조각은 간단한 사용자 지정 작업이 포함된 파이프라인을 정의합니다. 작업 정의에는 Azure Batch 연결된 서비스에 대한 참조가 있습니다.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

이 샘플에서 helloworld.exe는 resourceLinkedService에 사용되는 Azure Storage 계정의 customactv2/helloworld 폴더에 저장된 사용자 지정 애플리케이션입니다. 사용자 지정 작업은 Azure Batch에서 실행될 이 사용자 지정 애플리케이션을 제출합니다. Azure Batch 풀 노드의 대상 운영 체제에서 실행될 수 있는 기본 애플리케이션에 대한 명령을 바꿀 수 있습니다.

다음 표에는 이 작업과 관련된 속성 이름과 설명이 나와 있습니다.

| 속성              | 설명                              | 필수 |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | 파이프라인의 작업 이름입니다.     | 예      |
| description           | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.  | 예       |
| type                  | 사용자 지정 작업의 경우 작업 유형은 **사용자 지정** 입니다. | 예      |
| linkedServiceName     | Azure Batch에 연결된 서비스입니다. 이 연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요.  | 예      |
| 명령을 사용합니다.               | 실행할 사용자 지정 애플리케이션의 명령입니다. Azure Batch 풀 노드에 사용할 수 있는 애플리케이션이 이미 있으면 resourceLinkedService 및 folderPath를 건너뛸 수 있습니다. 예를 들어 명령을 기본적으로 Windows Batch 풀 노드에 의해 지원되는 `cmd /c dir`로 지정할 수 있습니다. | 예      |
| resourceLinkedService | 사용자 지정 애플리케이션이 저장된 스토리지 계정에 대한 Azure Storage 연결된 서비스입니다. | 아니요 &#42;       |
| folderPath            | 사용자 지정 애플리케이션 및 모든 해당 종속성 폴더에 대한 경로입니다.<br/><br/>종속성이 하위 폴더(즉, *folderPath* 아래의 계층 폴더 구조)에 저장된 경우, 해당 파일이 Azure Batch에 복사될 때 폴더 구조가 손쉽게 평면화됩니다. 즉, 모든 파일이 하위 폴더가 없는 단일 폴더에 복사됩니다. 이 동작을 해결하려면 파일을 압축하고 압축 파일을 복사한 다음, 원하는 위치에서 사용자 지정 코드로 압축을 푸세요. | 아니요 &#42;       |
| referenceObjects      | 기존 연결된 서비스 및 데이터 세트의 배열입니다. 사용자 지정 코드가 서비스의 리소스를 참조할 수 있도록 참조된 연결된 서비스 및 데이터 세트는 JSON 형식으로 사용자 지정 애플리케이션에 전달됩니다. | 예       |
| extendedProperties    | 사용자 지정 코드가 추가 속성을 참조할 수 있도록 사용자 정의 속성은 JSON 형식으로 사용자 지정 애플리케이션에 전달될 수 있습니다. | 예       |
| retentionTimeInDays | 사용자 지정 작업에 대해 제출된 파일의 보존 시간입니다. 기본값은 30일입니다. | 예 |

&#42; 속성 `resourceLinkedService` 및 `folderPath`를 둘 다 지정하거나 둘 다 생략해야 합니다.

> [!NOTE]
> 사용자 지정 작업에서 연결된 서비스를 referenceObjects로 전달하는 경우 Azure Key Vault가 활성화된 연결된 서비스를 전달하고(보안 문자열을 포함하지 않기 때문). 비밀 이름을 사용하여 코드에서 Key Vault의 자격 증명을 직접 페치하는 것이 안전합니다. AKV가 활성화된 연결된 서비스를 참조하고, Key Vault에서 자격 증명을 검색하고, 코드의 스토리지에 액세스하는 예제는 [여기](https://github.com/nabhishek/customactivity_sample/tree/linkedservice)를 참조하세요.

## <a name="custom-activity-permissions"></a>사용자 지정 활동 권한

사용자 지정 활동은 Azure Batch 자동 사용자 계정을 *태스크 범위를 사용하는 비관리자 액세스*(기본 자동 사용자 사양)로 설정합니다. 자동 사용자 계정의 권한 수준은 변경할 수 없습니다. 자세한 내용은 [Batch에서 사용자 계정으로 태스크 실행 | 자동 사용자 계정](../batch/batch-user-accounts.md#auto-user-accounts)을 참조하세요.

## <a name="executing-commands"></a>명령 실행

사용자 지정 작업을 사용하여 명령을 직접 실행할 수 있습니다. 다음 예제에서는 대상 Azure Batch 풀 노드에서 "echo hello world" 명령을 실행하고 출력을 stdout에 인쇄합니다.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>개체 및 속성 전달

이 샘플에서는 referenceObjects 및 extendedProperties를 사용하여 개체 및 사용자 지정 속성을 서비스에서 사용자 지정 애플리케이션에 전달하는 방법을 보여 줍니다.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

작업이 실행될 때 referenceObjects 및 extendedProperties는 SampleApp.exe와 동일한 실행 폴더에 배포되는 다음 파일에 저장됩니다.

- `activity.json`

  extendedProperties 및 사용자 지정 활동의 속성을 저장합니다.

- `linkedServices.json`

  referenceObjects 속성에 정의된 연결된 서비스의 배열을 저장합니다.

- `datasets.json`

  referenceObjects 속성에 정의된 데이터 세트의 배열을 저장합니다.

다음 샘플 코드는 SampleApp.exe가 JSON 파일에서 필요한 정보에 액세스할 수 있는 방법을 보여줍니다.

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>실행 출력 검색

다음 PowerShell 명령을 사용하여 파이프라인 실행을 시작할 수 있습니다.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

파이프라인을 실행하는 경우 다음 명령을 사용하여 실행 출력을 확인할 수 있습니다.

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

사용자 지정 애플리케이션의 **stdout** 및 **stderr** 은 작업의 GUID를 사용하여 Azure Batch 연결된 서비스를 만들 때 정의한 Azure Storage 연결된 서비스의 **adfjobs** 컨테이너에 저장됩니다. 다음 코드 조각에 나와 있는 것처럼 작업 실행 출력에서 자세한 경로를 얻을 수 있습니다.

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

다운스트림 작업에서 stdout.txt의 콘텐츠를 사용하려는 경우 "\@activity('MyCustomActivity').output.outputs[0]" 식에서 stdout.txt 파일의 경로를 가져올 수 있습니다.

> [!IMPORTANT]
> - activity.json, linkedServices.json 및 datasets.json은 Batch 작업의 런타임 폴더에 저장됩니다. 이 예제의 경우 activity.json, linkedServices.json, datasets.json은 `https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/` 경로에 저장됩니다. 필요한 경우 개별적으로 정리해야 합니다.
> - 자체 호스팅 통합 런타임을 사용하는 연결된 서비스의 경우 키 또는 암호와 같은 중요한 정보를 자체 호스팅 통합 런타임으로 암호화하여 사용자 정의 프라이빗 네트워크 환경에 자격 증명을 유지해야 합니다. 이러한 방식으로 사용자 지정 애플리케이션 코드에서 참조하는 경우 일부 중요한 필드가 누락될 수 있습니다. 필요한 경우 연결된 서비스 참조를 사용하는 대신 extendedProperties에서 SecureString을 사용합니다.

## <a name="pass-outputs-to-another-activity"></a>다른 활동으로 출력 전달

사용자 지정 활동의 코드에서 사용자 지정 값을 다시 서비스에 보낼 수 있습니다. 그럼으로써 사용자 애플리케이션에서 `outputs.json`에 해당 값을 작성할 수 있습니다. 서비스는 `outputs.json`의 콘텐츠를 복사하여 `customOutput` 속성 값으로 작업 출력에 추가합니다. (크기 제한은 2MB) 다운스트림 작업에서 `outputs.json`의 콘텐츠를 사용하려는 경우 `@activity('<MyCustomActivity>').output.customOutput` 식을 사용하여 값을 가져올 수 있습니다.

## <a name="retrieve-securestring-outputs"></a>SecureString 출력 검색

이 문서의 일부 예에 표시된 대로 *SecureString* 유형으로 지정된 민감한 속성 값은 사용자 인터페이스의 모니터링 탭에서 마스크 처리됩니다.  그러나 실제 파이프라인 실행에서는 *SecureString* 속성이 일반 텍스트로 `activity.json` 파일 내에서 JSON으로 serialize됩니다. 예를 들면 다음과 같습니다.

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

이 serialization은 실제로 안전하지 않으며 보안이 되지 않습니다. 의도는 모니터링 탭의 값을 마스킹하는 서비스에 대한 힌트입니다.

사용자 지정 활동에서 *SecureString* 유형의 속성에 액세스하려면 .EXE와 같은 폴더에 있는 `activity.json` 파일을 읽고 JSON을 역직렬화한 다음, JSON 특성(extendedProperties =&gt; [propertyName] =&gt; 값)에 액세스하세요.

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a>v2 사용자 지정 활동 및 버전 1(사용자 지정) DotNet 작업 비교

Azure Data Factory 버전 1에서는 `IDotNetActivity` 인터페이스의 `Execute` 메서드를 구현하는 클래스를 통해 .NET 클래스 라이브러리 프로젝트를 만들어 (사용자 지정) DotNet 작업을 구현합니다. (사용자 지정) DotNet 작업의 JSON 페이로드에 있는 연결된 서비스, 데이터 세트 및 확장된 속성은 강력한 형식의 개체로 실행 메서드에 전달됩니다. 버전 1 동작에 대한 자세한 내용은 [버전 1(사용자 지정) DotNet](v1/data-factory-use-custom-activities.md)을 참조하세요. 이 구현 때문에, 버전 1 DotNet Activity 코드의 대상이 .NET Framework 4.5.2여야 합니다. 버전 1 DotNet 활동은 Windows 기반 Azure Batch 풀 노드에서도 실행되어야 합니다.

Azure Data Factory V2 및 Synapse 파이프라인 사용자 지정 작업에서는 .NET 인터페이스를 구현할 필요가 없습니다. 이제 명령, 스크립트 및 실행 파일로 컴파일된 자체 사용자 지정 코드를 직접 실행할 수 있습니다. 이 구현을 구성하려면 `Command` 속성과 `folderPath` 속성을 함께 지정합니다. 사용자 지정 작업은 `folderpath`에서 실행 파일 및 종속성을 업로드하고 명령을 실행합니다.

Data Factory v2 및 Synapse 파이프라인 사용자 지정 작업의 JSON 페이로드에 정의된 연결된 서비스, 데이터 세트(referenceObjects에 정의됨) 및 확장된 속성은 실행 파일에서 JSON 파일로서 액세스할 수 있습니다. 위의 SampleApp.exe 코드 샘플에 나와 있는 것처럼 JSON 직렬 변환기를 사용하여 필요한 속성에 액세스할 수 있습니다.

Data Factory V2 및 Synapse 파이프라인 사용자 지정 작업이 변경되면서 이제 기본 설정 언어로 사용자 지정 코드 논리를 쓰고, Azure Batch에서 지원하는 Windows 및 Linux 운영 체제에서 해당 코드를 실행할 수 있습니다.

다음 표에서는 Data Factory V2 및 Synapse 파이프라인 사용자 지정 작업과 Data Factory 버전 1 (사용자 지정) DotNet 작업 간의 차이점을 설명합니다.

|차이점      | 사용자 지정 작업      | 버전 1 (사용자 지정) DotNet 작업      |
| ---- | ---- | ---- |
|사용자 지정 논리를 정의하는 방법      |실행 파일을 제공하여      |.NET DLL을 구현하여      |
|사용자 지정 논리의 실행 환경      |Windows 또는 Linux      |Windows(.NET Framework 4.5.2)      |
|스크립트 실행      |실행 중인 스크립트 직접 지원(예: Windows VM의 "cmd/c echo hello world")      |.NET DLL에서 구현 필요      |
|필요한 데이터 세트      |Optional      |작업을 연결하고 정보를 전달하는 데 필요      |
|작업에서 사용자 지정 논리에 정보 전달      |ReferenceObjects(LinkedServices 및 데이터 세트) 및 ExtendedProperties(사용자 지정 속성)를 통해      |ExtendedProperties(사용자 지정 속성), 입력 및 출력 데이터 세트를 통해      |
|사용자 지정 논리에서 정보 검색      |실행 파일의 동일한 폴더에 저장된 activity.json, linkedServices.json 및 datasets.json 구문 분석      |.NET SDK(.NET Frame 4.5.2)를 통해      |
|로깅      |STDOUT에 직접 작성      |.NET DLL에서 로거 구현      |

버전 1(사용자 지정) DotNet 작업용으로 작성된 기존 .NET 코드가 있는 경우 현재 버전의 사용자 지정 작업을 사용하도록 코드를 수정해야 합니다. 다음의 같은 개괄적인 지침에 따라 코드를 업데이트합니다.

  - .NET 클래스 라이브러리의 프로젝트를 콘솔 앱으로 변경합니다.
  - `Main` 메서드를 사용하여 애플리케이션을 시작합니다. `IDotNetActivity` 인터페이스의 `Execute` 메서드는 더 이상 필요하지 않습니다.
  - 연결된 서비스, 데이터 세트 및 작업을 읽고 JSON 직렬 변환기를 사용하여 강력한 형식의 개체가 아닌 다른 방식으로 구문 분석합니다. 주 사용자 지정 코드 논리에 필수 속성의 값을 전달합니다. 앞에 나온 SampleApp.exe 코드 예제를 참조하세요.
  - 로거 개체는 더 이상 지원되지 않습니다. 실행 파일의 출력은 콘솔에 출력될 수 있으며 stdout.txt에 저장됩니다.
  - Microsoft.Azure.Management.DataFactories NuGet 패키지는 더 이상 필요하지 않습니다.
  - 코드를 컴파일하고 실행 파일 및 종속성을 Azure Storage에 업로드한 후 `folderPath` 속성에 경로를 정의합니다.

Data Factory 버전 1 문서에서 설명된 엔드투엔드 DLL 및 파이프라인 샘플의 전체 샘플의 경우 [Azure Data Factory 파이프라인에서 사용자 지정 작업 사용](./v1/data-factory-use-custom-activities.md)은 Data Factory v2 및 Synapse 파이프라인의 사용자 지정 작업으로 다시 작성될 수 있습니다. [사용자 지정 작업 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample)을 참조하세요.

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch의 자동 확장

**자동 크기 조정** 기능으로 Azure Batch 풀을 만들 수 있습니다. 예를 들어 보류 중인 작업의 수에 따라 전용 VM 0개 및 자동 크기 조정 수식을 사용하여 Azure 배치 풀을 만들 수 있습니다.

여기에 나오는 샘플 수식은 다음과 같은 동작을 구현합니다. 풀이 처음 만들어질 때는 VM 1개로 시작합니다. $PendingTasks 메트릭은 실행되거나 큐에 대기 중인 활성 상태의 작업 수를 정의합니다. 이 수식은 지난 180초 동안에서 보류 중인 작업의 평균 수를 찾은 후 그에 따라 TargetDedicated를 설정합니다. 또한 TargetDedicated가 25개의 VM을 초과하지 않도록 합니다. 따라서 새 작업이 제출되면 풀이 자동으로 커지고, 작업이 완료되면 VM은 하나씩 사용 가능한 상태로 해제된 후 자동 크기 조정에 따라 해당 VM이 축소됩니다. startingNumberOfVMs 및 maxNumberofVMs은 요구에 맞게 조정될 수 있습니다.

자동 크기 조정 수식:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

자세한 내용은 [Azure Batch 풀에서 자동으로 컴퓨팅 노드 크기 조정](../batch/batch-automatic-scaling.md)을 참조하세요.

풀에 기본 [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)이 사용되는 경우, Batch 서비스가 사용자 지정 작업을 실행하기 전에 VM을 준비하는 데 15~30분이 소요될 수 있습니다. 풀에 다른 autoScaleEvaluationInterval이 사용되는 경우, Batch 서비스는 autoScaleEvaluationInterval +10분이 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요.

* [U-SQL 작업](transform-data-using-data-lake-analytics.md)
* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [ML Studio(클래식) Batch Execution 작업](transform-data-using-machine-learning.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
