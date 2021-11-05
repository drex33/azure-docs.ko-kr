---
title: 첫 번째 데이터 팩터리(Visual Studio) 빌드
description: 이 자습서에서는 Visual Studio를 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: tutorial
ms.custom: vs-azure, devx-track-azurepowershell
ms.date: 10/22/2021
ms.openlocfilehash: 51c421fbb6dc38dbf4de26e95b82bb455e0b35bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051176"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>자습서: Visual Studio를 사용하여 데이터 팩터리 만들기
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [개요 및 필수 구성 요소](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 템플릿](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용하는 경우 [빠른 시작: Azure Data Factory를 사용하여 데이터 팩터리 만들기](../quickstart-create-data-factory-dot-net.md)를 참조하세요.

이 자습서에서는 Visual Studio를 사용하여 Azure Data Factory를 만드는 방법을 보여줍니다. 데이터 팩터리 프로젝트 템플릿을 사용하여 Visual Studio 프로젝트를 만들고 JSON 형식으로 데이터 팩터리 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)를 정의한 다음, 이 엔터티를 이러한 클라우드에 게시하고 배포합니다. 

이 자습서의 파이프라인에는 **HDInsight Hive 작업** 이라는 하나의 작업이 있습니다. 이 작업은 Azure HDInsight 클러스터에서 입력 데이터를 변환하여 출력 데이터를 생성하는 Hive 스크립트를 실행합니다. 파이프라인은 지정된 시작 및 종료 시간 사이, 한 달에 한 번 실행되도록 예약됩니다. 

> [!NOTE]
> 이 자습서에서는 Azure Data Factory를 사용하여 데이터를 복사하는 방법을 표시하지 않습니다. Azure Data Factory를 사용하여 데이터를 복사하는 방법에 대한 자습서는 [자습서: Blob Storage에서 SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
> 
> 파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [Data Factory에서 예약 및 실행](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요.


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>연습: 데이터 팩터리 엔터티 만들기 및 게시
이 연습 일부로 수행하는 단계는 다음과 같습니다.

1. 2개의 연결된 서비스인 **AzureStorageLinkedService1** 및 **HDInsightOnDemandLinkedService1** 을 만듭니다. 
   
    이 자습서에서는 Hive 작업의 입력 및 출력 데이터가 모두 동일한 Azure Blob Storage에 있습니다. 주문형 HDInsight 클러스터를 사용하여 출력 데이터를 생성하는 기존 입력 데이터를 처리합니다. 입력 데이터를 처리할 준비가 된 경우 런타임 시 주문형 HDInsight 클러스터가 Azure Data Factory에 의해 자동으로 만들어집니다. 런타임 시 데이터 팩터리 서비스가 연결할 수 있도록 데이터 저장소 또는 계산을 데이터 팩터리에 연결해야 합니다. 따라서 AzureStorageLinkedService1을 사용하여 Azure Storage 계정을 데이터 팩터리에 연결하고 HDInsightOnDemandLinkedService1을 사용하여 주문형 HDInsight 클러스터를 연결합니다. 만들려는 데이터 팩터리 또는 기존 데이터 팩터리를 게시할 때 이름을 지정합니다.  
2. Azure Blob Storage에 저장된 입출력 데이터를 나타내는 2개의 데이터 세트인 **InputDataset** 및 **OutputDataset** 을 만듭니다. 
   
    이러한 데이터 세트 정의는 이전 단계에서 만든 Azure Storage 연결된 서비스를 참조하세요. InputDataset의 경우 Blob 컨테이너(adfgetstarted)와 입력 데이터와 함께 Blob을 포함하는 폴더(inptutdata)를 지정합니다. OutputDataset의 경우 Blob 컨테이너(adfgetstarted)와 출력 데이터를 포함하는 폴더(partitioneddata)를 지정합니다. 구조, 가용성 및 정책과 같은 기타 속성도 지정합니다.
3. **MyFirstPipeline** 이라는 파이프라인을 만듭니다. 
  
    이 자습서에서 파이프라인에는 **HDInsight Hive 작업** 이라는 하나의 작업이 있습니다. 이 작업은 주문형 HDInsight 클러스터에서 Hive 스크립트를 실행하여 출력 데이터를 생성하는 입력 데이터를 변환합니다. Hive 작업에 대한 자세한 내용은 [Hive 작업](data-factory-hive-activity.md)을 참조하세요. 
4. **DataFactoryUsingVS** 라는 데이터 팩터리를 만듭니다. 데이터 팩터리 및 모든 Data Factory 엔터티(연결된 서비스, 테이블 및 파이프라인)를 배포합니다.
5. 파이프라인을 게시한 후에 모니터링하려면 Azure Portal 블레이드 및 모니터링 및 관리 앱을 사용합니다. 
  
### <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. [자습서 개요](data-factory-build-your-first-pipeline.md) 문서를 살펴보고 **필수 구성 요소** 단계를 완료합니다. 문서를 전환하려면 맨 위에 있는 드롭다운 목록에서 **개요 및 필수 구성 요소** 옵션을 선택할 수도 있습니다. 필수 구성 요소를 완료한 후에 드롭 다운 목록에서 **Visual Studio** 옵션을 선택하여 이 문서로 다시 전환합니다.
2. 데이터 팩터리 인스턴스를 만들려면 구독/리소스 그룹 수준에서 [데이터 팩터리 참여자](../../role-based-access-control/built-in-roles.md#data-factory-contributor) 역할의 구성원이어야 합니다.  
3. 다음 항목이 컴퓨터에 설치되어 있어야 합니다.
   * Visual Studio 2013 또는 Visual Studio 2015
   * Visual Studio 2013 또는 Visual Studio 2015용 Azure SDK를 다운로드합니다. [Azure 다운로드 페이지](https://azure.microsoft.com/downloads/)로 이동하고 **.NET** 섹션에서 **VS 2013** 또는 **VS 2015** 를 클릭합니다.
   * Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 또는 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)용 최신 Azure Data Factory 플러그 인을 다운로드합니다. 메뉴에서 **도구** -> **확장 및 업데이트** -> **온라인** -> **Visual Studio 갤러리** -> **Visual Studio용 Microsoft Azure Data Factory 도구** -> **업데이트** 를 클릭하여 플러그 인을 업데이트할 수도 있습니다.

이제 Visual Studio를 사용하여 Azure Data Factory를 만들어 보겠습니다.

### <a name="create-visual-studio-project"></a>Visual Studio 프로젝트 만들기
1. **Visual Studio 2013** 또는 **Visual Studio 2015** 를 시작합니다. **File** 을 클릭하고 **New** 를 가리킨 다음 **프로젝트** 를 클릭합니다. **새 프로젝트** 대화 상자가 나타납니다.  
2. **새 프로젝트** 대화 상자에서 **DataFactory** 템플릿을 선택하고 **빈 데이터 팩터리 프로젝트** 를 클릭합니다.   

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png" alt-text="새 프로젝트 대화 상자":::
3. 프로젝트의 **이름**, **위치**, **솔루션** 의 이름을 입력한 다음 **확인** 을 클릭합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png" alt-text="솔루션 탐색기":::

### <a name="create-linked-services"></a>연결된 서비스 만들기
이 단계에서는 두 가지 연결된 서비스 **Azure Storage** 및 **주문형 HDInsight** 를 만듭니다. 

Azure Storage 연결된 서비스는 연결 정보를 제공하여 Azure Storage 계정을 데이터 팩터리에 연결합니다. 데이터 팩터리 서비스는 런타임 시 Azure storage에 연결하기 위해 연결된 서비스 설정에서 연결 문자열을 사용합니다. 이 스토리지는 파이프라인의 입력 및 출력 데이터와 Hive 작업에서 사용한 Hive 스크립트 파일을 보유합니다. 

입력 데이터를 처리할 준비가 된 경우 런타임 시 HDInsight 클러스터가 주문형 HDInsight 연결된 서비스를 사용하여 자동으로 만들어집니다. 클러스터의 처리가 완료되고 지정된 시간 동안 유휴 상태를 유지한 후에 삭제됩니다. 

> [!NOTE]
> 데이터 팩터리 솔루션을 게시할 때에 해당 이름 및 설정을 지정하여 데이터 팩터리를 만듭니다.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기
1. 솔루션 탐색기에서 **연결된 서비스** 를 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.      
2. **새 항목 추가** 대화 상자의 목록에서 **Azure Storage 연결된 서비스** 를 선택한 다음 **추가** 를 클릭합니다.
    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png" alt-text="목록에서 Azure Storage Linked Service를 강조 표시하는 스크린샷.":::
3. `<accountname>` 및 `<accountkey>`를 Azure Storage 계정 이름 및 해당 키로 바꿉니다. 스토리지 액세스 키를 가져오는 방법을 알아보려면 [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요.
    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png" alt-text="Azure Storage 연결 서비스":::
4. **AzureStorageLinkedService1.json** 파일을 저장합니다.

#### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight 연결된 서비스 만들기
1. **솔루션 탐색기** 에서 **연결된 서비스** 를 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.
2. **주문형 HDInsight 연결된 서비스** 를 선택하고 **추가** 를 클릭합니다.
3. **JSON** 을 다음 JSON으로 바꿉니다.

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

    속성 | Description
    -------- | ----------- 
    clusterSize | HDInsight Hadoop 클러스터의 크기를 지정합니다.
    timeToLive | HDInsight 클러스터가 삭제되기 전 유휴 시간을 지정합니다.
    linkedServiceName | HDInsight Hadoop 클러스터에 의해 생성되는 로그를 저장하는데 사용될 스토리지 계정을 지정합니다. 

    > [!IMPORTANT]
    > HDInsight 클러스터는 JSON(linkedServiceName)에서 지정한 Blob Storage에 **기본 컨테이너** 를 만듭니다. HDInsight는 클러스터가 삭제될 때 이 컨테이너를 삭제하지 않습니다. 이 동작은 의도된 것입니다. 주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(timeToLive)가 없는 경우 슬라이스를 처리할 때마다 HDInsight 클러스터가 만들어집니다. 클러스터는 처리가 완료되면 자동으로 삭제됩니다.
    > 
    > 온-프레미스 응용 프로그램은 File Storage REST API를 호출하여 파일 공유의 데이터에 액세스할 수 있습니다. 작업의 문제 해결을 위해 이 항목들이 필요하지 않다면 스토리지 비용을 줄이기 위해 삭제할 수 있습니다. 이러한 컨테이너의 이름은 `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp` 패턴을 따릅니다. [Microsoft Azure Storage Explorer](https://storageexplorer.com/) 같은 도구를 사용하여 Azure Blob 스토리지에서 컨테이너를 삭제합니다.

    JSON 속성에 대한 자세한 내용은 [컴퓨팅 연결 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 참조하세요. 
4. **HDInsightOnDemandLinkedService1.json** 파일을 저장합니다.

### <a name="create-datasets"></a>데이터 세트 만들기
이 단계에서는 Hive 처리에 대한 입력 및 출력 데이터를 나타내는 데이터 세트를 만듭니다. 이러한 데이터 세트는 이 자습서의 앞부분에서 만든 **AzureStorageLinkedService1** 를 참조합니다. 연결된 서비스는 Azure Storage 계정을 가리키고 데이터 세트은 입력 및 출력 데이터를 가진 스토리지의 컨테이너, 폴더, 파일 이름을 지정합니다.   

#### <a name="create-input-dataset"></a>입력 데이터 세트 만들기
1. **솔루션 탐색기** 에서 **테이블** 을 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.
2. 목록에서 **Azure Blob** 을 선택하고 파일의 이름을 **InputDataSet.json** 로 변경한 다음 **추가** 를 클릭합니다.
3. 편집기에서 **JSON** 을 다음 JSON 조각으로 바꿉니다.

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    이 JSON 코드 조각에서는 파이프라인의 Hive 작업에 대한 입력 데이터를 나타내는 **AzureBlobInput** 라는 데이터 세트를 정의합니다. 입력 데이터가 `adfgetstarted`라는 Blob 컨테이너 및 `inputdata` 폴더에 저장되도록 지정합니다.

    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

    속성 | Description |
    -------- | ----------- |
    type |Azure Blob Storage에 데이터가 있기 때문에 형식 속성은 **AzureBlob** 으로 설정됩니다.
    linkedServiceName | 이전에 만든 AzureStorageLinkedService1을 가리킵니다.
    fileName |이 속성은 선택 사항입니다. 이 속성을 생략하면 folderPath의 모든 파일을 선택합니다. 이 경우에 input.log만 처리됩니다.
    type | 로그 파일이 텍스트 형식이므로 TextFormat을 사용합니다. |
    columnDelimiter | 로그 파일의 열은 쉼표(`,`)로 구분됩니다.
    frequency/interval | 월 및 간격을 설정한 빈도가 1인 경우 입력 조각은 매월 제공됩니다.
    external | 작업의 입력 데이터가 파이프라인에서 생성되지 않는 경우 이 속성은 true로 설정됩니다. 이 속성은 입력 데이터 세트에서만 지정됩니다. 첫 번째 작업의 입력 데이터 세트의 경우 항상 true로 설정합니다.
4. **InputDataset.json** 파일을 저장합니다.

#### <a name="create-output-dataset"></a>출력 데이터 세트 만들기
이제 Azure Blob Storage에 저장된 출력 데이터를 나타내는 출력 데이터 세트를 만듭니다.

1. **솔루션 탐색기** 에서 **테이블** 을 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.
2. 목록에서 **Azure Blob** 을 선택하고 파일의 이름을 **OutputDataset.json** 로 변경한 다음 **추가** 를 클릭합니다.
3. 편집기에서 **JSON** 을 다음 JSON으로 바꿉니다.
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
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
    JSON 코드 조각에서는 파이프라인의 Hive 작업에서 생성한 출력 데이터를 나타내는 **AzureBlobOutput** 이라는 데이터 세트를 정의합니다. Hive 작업에서 생성된 출력 데이터가 `adfgetstarted`라는 Blob 컨테이너 및 `partitioneddata` 폴더에 배치되도록 지정합니다. 
    
    **가용성** 섹션은 출력 데이터 세트가 월 단위로 생성되도록 지정합니다. 출력 데이터 세트는 파이프라인의 일정을 결정합니다. 파이프라인은 해당 시작 및 종료 시간 사이에 매월 실행됩니다. 

    이러한 속성에 대한 설명은 **입력 데이터 세트 만들기** 섹션을 참조하세요. 파이프라인에서 데이터 세트가 생성되므로 출력 데이터 세트에 외부 속성을 설정하지 않습니다.
4. **OutputDataset.json** 파일을 저장합니다.

### <a name="create-pipeline"></a>파이프라인 만들기
지금까지 Azure Storage 연결된 서비스와 입력 및 출력 데이터 세트를 만들었습니다. 이제 **HDInsightHive** 작업이 포함된 파이프라인을 만듭니다. Hive 작업에 대한 **입력** 을 **AzureBlobInput** 으로 설정하고 작업에 대한 **출력** 을 **AzureBlobOutput** 으로 설정합니다. 입력 데이터 세트의 조각은 매월 사용 가능(빈도: 월, 간격: 1)하고 출력 조각도 매월 생성됩니다. 

1. **솔루션 탐색기** 에서 **파이프라인** 을 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **새 항목** 을 클릭합니다.
2. 목록에서 **Hive 변환 파이프라인** 을 선택하고 **추가** 를 클릭합니다.
3. **JSON** 을 다음 코드 조각으로 바꿉니다.

    > [!IMPORTANT]
    > `<storageaccountname>`을 스토리지 계정 이름으로 바꿉니다.

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
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
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > `<storageaccountname>`을 스토리지 계정 이름으로 바꿉니다.

    JSON 코드 조각은 단일 작업(Hive 작업)으로 구성된 파이프라인을 정의합니다. 이 작업은 주문형 HDInsight 클러스터에서 입력 데이터를 처리하여 출력 데이터를 생성하는 Hive 스크립트를 실행합니다. 파이프라인 JSON의 작업 섹션에서는 **HDInsightHive** 로 설정된 형식을 가진 배열에 하나의 작업만이 표시됩니다. 

    HDInsight Hive 작업에만 특정되는 형식 속성에서 Azure Storage 연결된 서비스에 있는 Hive 스크립트 파일, 스크립트 파일에 대한 경로 및 스크립트 파일에 대한 매개 변수를 지정합니다. 

    Hive 스크립트 파일 **partitionweblogs.hql** 은 Azure Storage 계정(scriptLinkedService에 의해 지정됨)과 `adfgetstarted`라는 컨테이너의 `script` 폴더에 저장됩니다.

    `defines` 섹션은 Hive 스크립트에 Hive 구성 값(예: `${hiveconf:inputtable}`, `${hiveconf:partitionedtable})`)으로 전달되는 런타임 설정을 지정하는 데 사용됩니다.

    파이프라인의 **start** 및 **end** 속성은 파이프라인의 활성 기간을 지정합니다. 데이터 세트가 매월 생성되도록 구성했으므로 파이프라인에서 한 번만 조각이 생성됩니다(매월 시작 및 종료 날짜가 동일하기 때문).

    작업 JSON에서 **linkedServiceName** – **HDInsightOnDemandLinkedService** 에서 지정한 컴퓨팅에 대해 Hive 스크립트가 실행되도록 지정합니다.
4. **HiveActivity1.json** 파일을 저장합니다.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>partitionweblogs.hql 및 input.log 종속성으로 추가
1. **솔루션 탐색기** 창에서 **종속성** 을 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음 **기존 항목** 을 클릭합니다.  
2. **C:\ADFGettingStarted** 로 이동하고 **partitionweblogs.hql**, **input.log** 파일을 선택한 다음 **추가** 를 클릭합니다. [자습서 개요](data-factory-build-your-first-pipeline.md)에서 필수 구성 요소의 일부로 이 두 파일을 만들었습니다.

다음 단계에서 솔루션을 게시할 때 **partitionweblogs.hql** 파일은 `adfgetstarted` Blob 컨테이너의 **스크립트** 폴더에 업로드됩니다.   

### <a name="publishdeploy-data-factory-entities"></a>데이터 팩터리 엔터티 게시/배포
이 단계에서는 Azure Data Factory 서비스에 프로젝트의 데이터 팩터리 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)를 게시합니다. 게시 과정에서 데이터 팩터리의 이름을 지정합니다. 

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 클릭합니다.
2. **Microsoft 계정에 로그인** 대화 상자가 표시되면 Azure 구독이 있는 계정의 자격 증명을 입력하고 **로그인** 을 클릭합니다.
3. 다음 대화 상자가 표시됩니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/publish.png" alt-text="게시 대화 상자":::
4. **데이터 팩터리 구성** 페이지에서 다음 단계를 수행합니다.

    :::image type="content" source="media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png" alt-text="게시 - 새 데이터 팩터리 설정":::

   1. **새 Data Factory 만들기** 옵션을 선택합니다.
   2. 데이터 팩터리의 고유한 **이름** 을 입력합니다. 예: **DataFactoryUsingVS09152016** 이름은 전역적으로 고유해야 합니다.
   3. **구독** 필드에서 올바른 구독을 선택합니다. 
      > [!IMPORTANT]
      > 모든 구독이 표시되지 않으면 구독의 관리자 또는 공동 관리자인 계정을 사용하여 로그인했는지 확인합니다.
   4. 생성되는 데이터 팩터리의 **리소스 그룹** 을 선택합니다.
   5. 데이터 팩터리의 **하위 지역** 을 선택합니다.
   6. **다음** 을 클릭하여 **항목 게시** 페이지로 전환합니다. **다음** 단추를 사용할 수 없는 경우 **TAB** 키를 눌러 이름 필드에서 나갑니다.
      > [!IMPORTANT]
      > 게시할 때 **데이터 팩터리 이름 “DataFactoryUsingVS”를 사용할 수 없습니다.** 오류가 표시되는 경우 이름을 변경합니다(예: yournameDataFactoryUsingVS). 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 - 명명 규칙](data-factory-naming-rules.md) 항목을 참조하세요.   

1. **항목 게시** 페이지에서 모든 데이터 팩터리 엔터티가 선택되었는지 확인하고 **다음** 을 클릭하여 **요약** 페이지로 전환합니다.

    :::image type="content" source="media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png" alt-text="항목 페이지 게시":::     
2. 요약을 검토한 후 **다음** 을 클릭하여 배포 프로세스를 시작하고 **배포 상태** 를 봅니다.

    :::image type="content" source="media/data-factory-build-your-first-pipeline-using-vs/summary-page.png" alt-text="요약 페이지":::
3. **배포 상태** 페이지에 배포 프로세스의 상태가 표시됩니다. 배포가 완료되면 마침을 클릭합니다.

염두해 둘 중요한 사항은 다음과 같습니다.

- **구독이 Microsoft.DataFactory 네임스페이스를 사용하도록 등록되어 있지 않습니다.** 라는 오류를 수신하는 경우 다음 중 하나를 수행하고 다시 게시하세요.

  - Azure PowerShell에서 다음 명령을 실행하여 Data Factory 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    데이터 팩터리 공급자가 등록되어 있는지 확인하려면 다음 명령을 실행할 수 있습니다.

    ```powershell
    Get-AzResourceProvider
    ```

  - Azure 구독을 사용하여 [Azure 포털](https://portal.azure.com) 에 로그인하고 데이터 팩터리 블레이드로 이동하거나 Azure 포털에 데이터 팩터리를 만듭니다. 이 작업은 공급자를 자동으로 등록합니다.

- 데이터 팩터리의 이름은 나중에 DNS 이름으로 표시되므로 공개적으로 등록될 수도 있습니다.

- 데이터 팩터리 인스턴스를 만들려면 Azure 구독의 관리자 또는 공동 관리자여야 합니다.

### <a name="monitor-pipeline"></a>파이프라인 모니터링

이 단계에서는 데이터 팩터리의 다이어그램 뷰를 사용하여 파이프라인을 모니터링합니다. 

#### <a name="monitor-pipeline-using-diagram-view"></a>다이어그램 보기를 사용하여 파이프라인 모니터링
1. [Azure Portal](https://portal.azure.com/)에 로그인하고 다음 단계를 수행합니다.
   1. **더 많은 서비스** 를 클릭하고 **데이터 팩터리** 를 클릭합니다.
       
        :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png" alt-text="데이터 팩터리 찾아보기":::
   2. 데이터 팩터리의 목록에서 데이터 팩터리의 이름을 선택합니다(예: **DataFactoryUsingVS09152016**).
   
       :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png" alt-text="데이터 팩터리 선택":::
2. 데이터 팩터리에 대한 홈페이지에서 **다이어그램** 을 클릭합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png" alt-text="다이어그램 타일":::
3. 다이어그램 보기에 파이프라인의 개요와 이 자습서에 사용된 데이터 세트가 표시됩니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png" alt-text="다이어그램 뷰":::
4. 파이프라인의 모든 작업을 보려면 다이어그램에서 파이프라인을 마우스 오른쪽 단추로 클릭하고 파이프라인 열기를 클릭합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png" alt-text="파이프라인 열기 메뉴":::
5. 파이프라인에서 HDInsightHive 활동이 표시되는지 확인합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png" alt-text="파이프라인 보기 열기":::

    이전 보기를 탐색하려면 맨 위에서 breadcrumb 메뉴의 **데이터 팩터리** 를 클릭합니다.
6. **다이어그램 보기** 에서 **AzureBlobInput** 데이터 세트를 두 번 클릭합니다. 조각이 **준비** 상태인지 확인합니다. 조각이 준비 상태로 표시되려면 몇 분이 걸릴 수 있습니다. 잠시 대기한 후에 표시되지 않는 경우 오른쪽 컨테이너(`adfgetstarted`) 및 폴더(`inputdata`)에 배치된 입력 파일(input.log)이 있는지 확인합니다. 입력 데이터 세트의 **외부** 속성을 **true** 로 설정했는지 확인합니다. 

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png" alt-text="준비 상태인 입력 조각":::
7. **X** 를 닫아서 **AzureBlobInput** 블레이드를 닫습니다.
8. **다이어그램 보기** 에서 **AzureBlobOutput** 데이터 세트를 두 번 클릭합니다. 현재 처리 중인 조각이 표시됩니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png" alt-text="AzureBlobOutput 데이터 세트를 강조 표시하는 스크린샷.":::
9. 처리가 완료되면 **준비** 상태인 조각이 표시됩니다.

   > [!IMPORTANT]
   > 주문형 HDInsight 클러스터 만들기는 일반적으로 시간이 소요됩니다.(대략 20분) 따라서 파이프라인이 조각을 처리하는 데 **약 30분** 이 걸릴 수 있습니다.  
   
    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png" alt-text="데이터 세트":::    
10. 조각이 **준비** 상태이면 Blob Storage의 `adfgetstarted` 컨테이너에 있는 `partitioneddata` 폴더에서 출력 데이터를 확인합니다.  

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png" alt-text="출력 데이터":::
11. 자세한 내용을 보려면 **데이터 조각** 블레이드에서 조각을 클릭합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png" alt-text="데이터 조각 세부 정보":::  
12. **작업 실행 목록** 에서 작업 실행을 클릭하여 **작업 실행 세부 정보** 창에서 작업 실행에 대한 세부 정보를 봅니다(이 시나리오에서 Hive 작업). 
  
    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png" alt-text="작업 실행 세부 정보":::    

    로그 파일에서 실행되는 Hive 쿼리 및 상태 정보를 볼 수 있습니다. 이러한 로그는 문제를 해결하는 데 유용합니다.  

Azure 포털을 사용하여 이 자습서에서 만든 파이프라인 및 데이터 세트를 모니터링하는 방법에 대한 지침은 [데이터 세트 및 파이프라인 모니터링](data-factory-monitor-manage-pipelines.md) 을 참조하세요.

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>앱 모니터링 및 관리를 사용하여 파이프라인 모니터링
애플리케이션 모니터링 및 관리를 사용하여 파이프라인을 모니터링할 수도 있습니다. 이 애플리케이션을 사용하는 방법에 대한 자세한 내용은 [앱 모니터링 및 관리를 사용하여 Azure Data Factory 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

1. 타일 모니터링 및 관리를 클릭합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png" alt-text="타일 모니터링 및 관리":::
2. 애플리케이션 모니터링 및 관리가 표시되어야 합니다. **시작 시간** 및 **종료 시간** 을 파이프라인 시작 시간(2016-04-01 오전 12시) 및 종료 시간(2016-04-02 오전 12시)에 맞게 변경하고 **적용** 을 클릭합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png" alt-text="앱 모니터링 및 관리":::
3. 작업 창에 대한 자세한 내용을 보려면 **작업 창 목록** 에서 작업 창을 선택합니다.
    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png" alt-text="활동 창 세부 정보":::

> [!IMPORTANT]
> 조각이 성공적으로 처리될 때 입력된 파일이 삭제됩니다. 따라서 조각을 다시 실행하거나 자습서를 다시 수행하려는 경우 `adfgetstarted` 컨테이너의 `inputdata` 폴더에 입력 파일(input.log)을 업로드합니다.

### <a name="additional-notes"></a>추가적인 참고 사항
- 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 원본에서 대상 데이터 저장소에 데이터를 복사하는 복사 작업 및 입력 데이터를 변환할 Hive 스크립트를 실행하는 HDInsight Hive 작업입니다. 복사 작업에서 지원하는 모든 원본 및 싱크는 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 를 참조하세요. 데이터 팩터리에서 지원하는 컴퓨팅 서비스 목록은 [연결된 컴퓨팅 서비스](data-factory-compute-linked-services.md)를 참조하세요.
- 연결된 서비스는 데이터 저장소 또는 컴퓨팅 서비스를 Azure Data Factory에 연결합니다. 복사 작업에서 지원하는 모든 원본 및 싱크는 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 를 참조하세요. 데이터 팩터리에서 지원하는 컴퓨팅 서비스 목록 및 여기에서 실행할 수 있는 [변환 작업](data-factory-data-transformation-activities.md)은 [연결된 컴퓨팅 서비스](data-factory-compute-linked-services.md)를 참조하세요.
- Azure Storage 연결된 서비스 정의에서 사용된 JSON 속성에 대한 자세한 내용은 [Azure Blob 간에 데이터 이동](data-factory-azure-blob-connector.md#azure-storage-linked-service)을 참조하세요.
- 주문형 HDInsight 클러스터를 사용하는 대신 고유의 HDInsight 클러스터를 사용할 수 있습니다. 자세한 내용은 [컴퓨팅 연결 서비스](data-factory-compute-linked-services.md)를 참조하세요.
-  데이터 팩터리는 앞의 JSON으로 사용자에게 **Linux 기반** HDInsight 클러스터를 만들어 줍니다. 자세한 내용은 [주문형 HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) 를 참조하세요.
- HDInsight 클러스터는 JSON(linkedServiceName)에서 지정한 Blob Storage에 **기본 컨테이너** 를 만듭니다. HDInsight는 클러스터가 삭제될 때 이 컨테이너를 삭제하지 않습니다. 이 동작은 의도된 것입니다. 주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(timeToLive)가 없는 경우 슬라이스를 처리할 때마다 HDInsight 클러스터가 만들어집니다. 클러스터는 처리가 완료되면 자동으로 삭제됩니다.
    
    온-프레미스 응용 프로그램은 File Storage REST API를 호출하여 파일 공유의 데이터에 액세스할 수 있습니다. 작업의 문제 해결을 위해 이 항목들이 필요하지 않다면 스토리지 비용을 줄이기 위해 삭제할 수 있습니다. 이러한 컨테이너의 이름은 `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp` 패턴을 따릅니다. [Microsoft Azure Storage Explorer](https://storageexplorer.com/) 같은 도구를 사용하여 Azure Blob 스토리지에서 컨테이너를 삭제합니다.
- 현재 출력 데이터 세트가 일정을 결정하므로 작업이 출력을 생성하지 않는 경우 출력 데이터 세트를 만들어야 합니다. 활동이 입력을 가져오지 않으면 입력 데이터 세트 만들기를 건너뛸 수 있습니다. 
- 이 자습서에서는 Azure Data Factory를 사용하여 데이터를 복사하는 방법을 표시하지 않습니다. Azure Data Factory를 사용하여 데이터를 복사하는 방법에 대한 자습서는 [자습서: Blob Storage에서 SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.


## <a name="use-server-explorer-to-view-data-factories"></a>서버 탐색기를 사용하여 데이터 팩터리 검토
1. **Visual Studio** 의 메뉴에서 **보기** 를 클릭한 다음 **서버 탐색기** 를 클릭합니다.
2. 서버 탐색기 창에서 **Azure** 를 확장한 다음 **Data Factory** 를 확장합니다. **Visual Studio에 로그인** 이 표시되면 Azure 구독과 연결된 **계정** 을 입력하고 **계속** 을 클릭합니다. **암호** 를 입력하고 **로그인** 을 클릭합니다. Visual Studio에서는 구독에 있는 모든 Azure Data Factory에 대한 정보를 가져오려고 시도합니다. **데이터 팩터리 작업 목록** 창에 이 작업의 상태가 표시됩니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png" alt-text="서버 탐색기":::
3. 데이터 팩터리를 마우스 오른쪽 단추로 클릭하고 **새 프로젝트로 데이터 팩터리 내보내기** 를 선택하여 기존 데이터 팩터리에 따라 Visual Studio 프로젝트를 만들 수 있습니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png" alt-text="데이터 팩터리 내보내기":::

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
                "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
    ]
    ```

    다음 구성 파일(0부터 시작되는 인덱스 사용)과 같은 속성을 구성합니다.

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    },
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    },
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    },
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

## <a name="summary"></a>요약
이 자습서에서는 HDInsight hadoop 클러스터에서 Hive 스크립트를 실행하여 데이터를 처리하는 Azure Data Factory를 만들었습니다. Azure 포털에서 다음 단계를 수행하기 위해 데이터 팩터리 편집기를 사용했습니다.  

1. Azure **데이터 팩터리** 를 만들었습니다.
2. 두 개의 **연결된 서비스** 를 만들었습니다.
   1. **Azure Storage** 연결된 서비스입니다.
   2. **Azure HDInsight** 주문형 연결된 서비스입니다. Azure 데이터 팩터리는 입력 데이터를 처리하고 출력 데이터를 생성하기 위해 적시에 HDInsight Hadoop 클러스터를 만듭니다.
3. 파이프라인에서 HDInsight Hive 작업에 대한 입력 및 출력 데이터를 설명하는 두 개의 **데이터 세트** 를 만들었습니다.
4. **HDInsight Hive** 작업으로 **파이프라인** 을 만들었습니다.  

## <a name="next-steps"></a>다음 단계
이 문서에서 파이프라인과 주문형 HDInsight 클러스터에서 Hive 스크립트를 실행하는 변환 작업(HDInsight 작업)을 만들었습니다. 복사 작업을 사용하여 Azure Blob에서 Azure SQL로 데이터를 복사하는 방법은 [자습서: Azure Blob에서 Azure SQL로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정하여 두 활동을 연결하면 해당 활동을 차례로 실행할 수 있습니다. 자세한 정보는 [데이터 팩터리의 예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요. 


## <a name="see-also"></a>참고 항목

| 항목 | Description |
|:--- |:--- |
| [파이프라인](data-factory-create-pipelines.md) |이 문서는 Azure Data Factory의 파이프라인 및 시나리오 또는 비즈니스를 위한 활동과 데이터 기반 워크플로를 활용하는 방법을 이해하는 데 도움이 됩니다. |
| [데이터 세트](data-factory-create-datasets.md) |이 문서는 Azure Data Factory의 데이터 세트를 이해하는 데 도움이 됩니다. |
| [데이터 변환 활동](data-factory-data-transformation-activities.md) |이 문서에서는 Azure Data Factory에서 지원되는 데이터 변환 활동(예: 이 자습서에 사용된 HDInsight Hive 변환)의 목록을 제공합니다. |
| [예약 및 실행](data-factory-scheduling-and-execution.md) |이 문서에서는 Azure Data Factory 애플리케이션 모델의 예약 및 실행에 대한 내용을 설명합니다. |
| [모니터링 앱을 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) |이 문서는 모니터링 및 관리 앱을 사용하여 파이프라인을 모니터링하고 관리하고 디버그하는 방법을 설명합니다. |
