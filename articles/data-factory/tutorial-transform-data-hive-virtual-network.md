---
title: Azure Virtual Network에서 Hive를 사용하여 데이터 변환
description: Azure PowerShell을 사용하여 Azure VNet(Virtual Network)에 있는 HDInsight 클러스터에서 Hive 작업을 통해 데이터를 변환하는 Data Factory 파이프라인을 만듭니다.
ms.service: data-factory
ms.subservice: tutorials
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.date: 01/22/2018
ms.openlocfilehash: c82a3fda3eb43fa73a46f934087bfb84e3aa2d1b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769696"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Azure Data Factory에서 Hive 작업을 사용하여 Azure Virtual Network에서 데이터 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 자습서에서는 Azure PowerShell을 사용하여 Azure VNet(Virtual Network)에 있는 HDInsight 클러스터에서 Hive 작업을 통해 데이터를 변환하는 Data Factory 파이프라인을 만듭니다. 이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * 자체 호스팅 통합 런타임을 작성하고 설정합니다.
> * 연결된 서비스를 작성하고 배포합니다.
> * Hive 작업이 포함된 파이프라인을 작성하고 배포합니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다. 
> * 출력을 확인합니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Storage 계정**. Hive 스크립트를 만들어 Azure Storage에 업로드합니다. Hive 스크립트의 출력은 이 스토리지 계정에 저장됩니다. 이 샘플에서 HDInsight 클러스터는 이 Azure Storage 계정을 기본 스토리지로 사용합니다. 
- **Azure Virtual Network** - 아직 없는 경우 [이 지침](../virtual-network/quick-create-portal.md)에 따라 Azure Virtual Network를 만듭니다. 이 샘플에서 HDInsight는 Azure Virtual Network에 있습니다. 다음은 Azure Virtual Network의 샘플 구성입니다. 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png" alt-text="가상 네트워크 만들기":::
- **HDInsight 클러스터** - 다음 문서에 따라 HDInsight 클러스터를 만들고 이전 단계에서 만든 가상 네트워크에 조인합니다. [Azure Virtual Network를 사용하여 Azure HDInsight 확장](../hdinsight/hdinsight-plan-virtual-network-deployment.md) 다음은 가상 네트워크에 속한 HDInsight의 샘플 구성입니다. 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png" alt-text="가상 네트워크의 HDInsight":::
- **Azure PowerShell**. [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)의 지침을 따르세요.

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Blob Storage 계정에 Hive 스크립트 업로드

1. 다음 내용이 포함된 **hivescript.hql** 이라는 Hive SQL 파일을 만듭니다.

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Azure Blob Storage에 아직 없는 경우 **adftutorial** 이라는 컨테이너를 만듭니다.
3. **hivescripts** 라는 폴더를 만듭니다.
4. **hivescript.hql** 파일을 **hivescripts** 하위 폴더로 업로드합니다.

  

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기


1. 리소스 그룹 이름을 설정합니다. 이 자습서의 일부로 리소스 그룹을 만듭니다. 단, 원하는 경우 기존 리소스 그룹을 사용할 수 있습니다. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```
2. 데이터 팩터리 이름을 지정합니다. 전역적으로 고유해야 합니다.

    ```powershell
    $dataFactoryName = "MyDataFactory09142017"
    ```
3. 파이프라인의 이름을 지정합니다. 

    ```powershell
    $pipelineName = "MyHivePipeline" # 
    ```
4. 자체 호스팅 통합 런타임의 이름을 지정합니다. Data Factory가 VNet 내부의 리소스(예: Azure SQL Database)에 액세스해야 하는 경우 자체 호스팅 통합 런타임이 필요합니다. 
    ```powershell
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" 
    ```
2. **PowerShell** 을 시작합니다. 이 빠른 시작을 완료할 때까지 Azure PowerShell을 열어 둡니다. 닫은 후 다시 여는 경우 명령을 다시 실행해야 합니다. 현재 Data Factory를 사용할 수 있는 Azure 지역 목록을 보려면 다음 페이지에서 관심 있는 지역을 선택한 다음, **Analytics** 를 펼쳐서 **Data Factory**: [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 찾습니다. 데이터 팩터리에서 사용되는 데이터 저장소(Azure Storage, Azure SQL Database 등) 및 계산(HDInsight 등)은 다른 지역에 있을 수 있습니다.

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
3. 리소스 그룹을 만듭니다. 구독에 아직 없는 경우 ADFTutorialResourceGroup 리소스 그룹을 만듭니다. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. 데이터 팩터리를 만듭니다. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    출력을 보려면 다음 명령을 실행합니다. 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>자체 호스팅 IR 만들기
이 섹션에서는 자체 호스팅 통합 런타임을 만들고 HDInsight 클러스터가 있는 동일한 Azure Virtual Network의 Azure VM과 연결합니다.

1. 자체 호스팅 통합 런타임을 만듭니다. 동일한 이름의 다른 통합 런타임이 있는 경우 고유한 이름을 사용합니다.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    이 명령은 자체 호스팅 통합 런타임의 논리적 등록을 만듭니다. 
2. PowerShell을 사용하여 자체 호스팅 통합 런타임을 등록하기 위한 인증 키를 검색합니다. 자체 호스팅 통합 런타임을 등록하기 위한 키 중 하나를 복사합니다.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   샘플 출력은 다음과 같습니다. 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    **AuthKey1** 값을 따옴표 없이 적어 둡니다. 
3. Azure VM을 만들고 HDInsight 클러스터가 포함된 동일한 가상 네트워크에 연결합니다. 자세한 내용은 [가상 머신을 만드는 방법](../virtual-network/quick-create-portal.md#create-virtual-machines)을 참조하세요. Azure Virtual Network에 조인합니다. 
4. Azure VM에서 [자체 호스팅 통합 런타임](https://www.microsoft.com/download/details.aspx?id=39717)을 다운로드합니다. 이전 단계에서 얻은 인증 키를 사용하여 자체 호스팅 통합 런타임을 수동으로 등록합니다. 

   :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png" alt-text="통합 런타임 등록":::

   자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 메시지가 표시됩니다. :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png" alt-text="성공적으로 등록됨":::

   노드가 클라우드 서비스에 연결되면 다음 페이지가 표시됩니다. :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png" alt-text="노드가 연결됨":::

## <a name="author-linked-services"></a>연결된 서비스 작성

이 섹션에서는 두 개의 연결된 서비스를 작성하고 배포합니다.
- Azure Storage 계정을 데이터 팩터리에 연결하는 Azure Storage 연결된 서비스 - 이 스토리지는 HDInsight 클러스터에서 사용하는 기본 스토리지입니다. 여기서는 Azure Storage 계정을 사용하여 Hive 스크립트와 이 스크립트의 출력을 유지합니다.
- HDInsight 연결된 서비스 - Azure Data Factory에서 Hive 스크립트를 이 HDInsight 클러스터에 제출하여 실행합니다.

### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스

원하는 편집기를 사용하여 JSON 파일을 만들고, 다음과 같은 Azure Storage 연결된 서비스의 JSON 정의를 복사하고, 파일을 **MyStorageLinkedService.json** 으로 저장합니다.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

**&lt;accountname&gt; 및 &lt;accountkey&gt;** 를 Azure Storage 계정의 이름과 키로 바꿉니다.

### <a name="hdinsight-linked-service"></a>HDInsight 연결된 서비스

원하는 편집기를 사용하여 JSON 파일을 만들고, 다음과 같은 Azure HDInsight 연결된 서비스의 JSON 정의를 복사하고, 파일을 **MyHDInsightLinkedService.json** 으로 저장합니다.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

연결된 서비스 정의에서 다음 속성 값을 업데이트합니다.

- **userName** - 클러스터를 만들 때 지정한 클러스터 로그인 사용자의 이름입니다. 
- **password** - 사용자의 암호입니다.
- **clusterUri** - HDInsight 클러스터의 URL을 `https://<clustername>.azurehdinsight.net` 형식으로 지정합니다.  이 문서에서는 인터넷을 통해 클러스터에 액세스할 수 있다고 가정합니다. 예를 들어 `https://clustername.azurehdinsight.net`에 있는 클러스터에 연결할 수 있습니다. 이 주소는 NSG(네트워크 보안 그룹) 또는 UDR(사용자 정의 경로)을 사용하여 인터넷 액세스를 제한한 경우 사용할 수 없는 공용 게이트웨이를 사용합니다. Data Factory에서 Azure Virtual Network의 HDInsight 클러스터에 작업을 제출하려면, URL을 HDInsight에서 사용하는 게이트웨이의 개인 IP 주소로 확인할 수 있도록 Azure Virtual Network를 구성해야 합니다.

  1. Azure Portal에서 HDInsight가 있는 Virtual Network를 엽니다. 이름이 `nic-gateway-0`으로 시작하는 네트워크 인터페이스를 엽니다. 개인 IP 주소를 적어 둡니다. 예를 들어 10.6.0.15입니다. 
  2. Azure Virtual Network에 DNS 서버가 있는 경우 `https://<clustername>.azurehdinsight.net` HDInsight 클러스터 URL을 `10.6.0.15`로 확인할 수 있도록 DNS 레코드를 업데이트합니다. 이 방법을 사용하는 것이 좋습니다. Azure Virtual Network에 DNS 서버가 없는 경우, 자체 호스팅 통합 런타임 노드로 등록된 모든 VM의 호스트 파일(C:\Windows\System32\drivers\etc)을 편집하여 다음과 같은 항목을 추가함으로써 이 문제를 일시적으로 해결할 수 있습니다. 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-linked-services"></a>연결된 서비스 만들기
PowerShell에서 JSON 파일을 만든 폴더로 전환하고 다음 명령을 실행하여 연결된 서비스를 배포합니다. 

1. PowerShell에서 JSON 파일을 만든 폴더로 전환합니다.
2. 다음 명령을 실행하여 Azure Storage 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
3. 다음 명령을 실행하여 Azure HDInsight 연결된 서비스를 만듭니다. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDInsightLinkedService" -File "MyHDInsightLinkedService.json"
    ```

## <a name="author-a-pipeline"></a>파이프라인 작성
이 단계에서는 Hive 작업이 있는 새 파이프라인을 만듭니다. 이 작업은 Hive 스크립트를 실행하여 샘플 테이블의 데이터를 반환하고 사용자가 정의한 경로에 저장합니다. 원하는 편집기에서 JSON 파일을 만들고, 다음과 같은 파이프라인 정의에 대한 JSON 정의를 복사하고, **MyHivePipeline.json** 으로 저장합니다.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}

```

다음 사항에 유의하세요.

- **scriptPath** 는 MyStorageLinkedService에 사용한 Azure Storage 계정의 Hive 스크립트 경로를 가리킵니다. 경로는 대/소문자를 구분합니다.
- **output** 은 Hive 스크립트에서 사용되는 인수입니다. `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` 형식을 사용하여 Azure Storage의 기존 폴더를 가리킵니다. 경로는 대/소문자를 구분합니다. 

JSON 파일을 만든 폴더로 전환하고 다음 명령을 실행하여 파이프라인을 배포합니다. 


```powershell
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>파이프라인 시작 

1. 파이프라인 실행을 시작합니다. 또한 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. 다음 스크립트를 실행하여 완료될 때까지 파이프라인 실행 상태를 계속 확인합니다.

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

   샘플 실행의 출력은 다음과 같습니다.

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. `outputfolder` 폴더에서 Hive 쿼리 결과로 만든 새 파일을 확인하면 다음과 같은 샘플 출력이 표시됩니다. 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>다음 단계
이 자습서에서 다음 단계를 수행했습니다. 

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다. 
> * 자체 호스팅 통합 런타임을 작성하고 설정합니다.
> * 연결된 서비스를 작성하고 배포합니다.
> * Hive 작업이 포함된 파이프라인을 작성하고 배포합니다.
> * 파이프라인 실행을 시작합니다.
> * 파이프라인 실행을 모니터링합니다. 
> * 출력을 확인합니다. 

Azure에서 Spark 클러스터를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
>[Data Factory 제어 흐름 분기 및 연결](tutorial-control-flow.md)
