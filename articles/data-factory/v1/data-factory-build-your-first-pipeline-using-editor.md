---
title: 첫 번째 데이터 팩터리(Azure Portal) 빌드
description: 이 자습서에서는 Azure Portal의 Data Factory Editor를 사용하여 샘플 Azure Data Factory 파이프라인을 만듭니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: a13309631d2fbd971021c3307f8ebe129e6a3a3e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592416"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 첫 번째 데이터 팩터리 빌드
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Azure Resource Manager 템플릿](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> 이 문서는 일반 공급되는 Azure Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용하는 경우, [빠른 시작: Data Factory를 사용하여 데이터 팩터리 만들기](../quickstart-create-data-factory-dot-net.md)를 참조하세요.

> [!WARNING]
> ADF v1 파이프라인 작성 및 배포에 사용되는 Azure Portal의 JSON 편집기는 2019년 7월 31일에 꺼집니다. 2019년 7월 31일 이후에는 [ADF v1 Powershell cmdlet](/powershell/module/az.datafactory/), [ADF v1 .Net SDK](/dotnet/api/microsoft.azure.management.datafactories.models), [ADF v1 REST API](/rest/api/datafactory/)를 사용하여 ADF v1 파이프라인을 계속 작성 및 배포할 수 있습니다.

이 문서에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 첫 번째 데이터 팩터리를 만드는 방법을 알아봅니다. 다른 도구/SDK를 사용하여 이 자습서를 수행하려면 드롭다운 목록에서 옵션 중 하나를 선택합니다. 

이 자습서의 파이프라인에는 Azure HDInsight Hive 작업이라는 하나의 작업이 있습니다. 이 작업은 HDInsight 클러스터에서 입력 데이터를 변환하여 출력 데이터를 생성하는 Hive 스크립트를 실행합니다. 파이프라인은 지정된 시작 및 종료 시간 사이, 한 달에 한 번 실행되도록 예약됩니다. 

> [!NOTE]
> 이 자습서의 데이터 파이프라인은 출력 데이터를 생성하는 입력 데이터를 변환합니다. Data Factory를 사용하여 데이터를 복사하는 방법에 대한 자습서는 [자습서: Azure Blob Storage에서 Azure SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
> 
> 파이프라인 하나에는 활동이 둘 이상 있을 수 있습니다. 한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정함으로써 두 활동을 연결하여 활동을 하나씩 차례로 실행할 수 있습니다. 자세한 내용은 [Data Factory에서 예약 및 실행](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소
[자습서 개요](data-factory-build-your-first-pipeline.md)를 읽고 “필수 구성 요소” 섹션의 단계를 수행합니다.

이 문서는 Data Factory 서비스에 대한 개념적 개요를 제공하지 않습니다. 서비스에 대한 자세한 내용은 [Azure Data Factory 소개](data-factory-introduction.md)를 참조하세요.  

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 예를 들어 복사 작업은 원본에서 대상 데이터 저장소로 데이터를 복사합니다. 또 다른 예는 Hive 스크립트를 실행하여 입력 데이터를 제품 출력 데이터로 변환하는 HDInsight Hive 작업입니다. 

데이터 팩터리를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **새로 만들기** > **데이터 + 분석** > **Data Factory** 를 차례로 선택합니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png" alt-text="블레이드 만들기":::

1. **새 데이터 팩터리** 블레이드에서 **이름** 에 **GetStartedDF** 를 입력합니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png" alt-text="새 데이터 팩터리 블레이드":::

   > [!IMPORTANT]
   > 데이터 팩터리 이름은 전역적으로 고유해야 합니다. “데이터 팩터리 이름 GetStartedDF를 사용할 수 없습니다”라는 오류를 수신하는 경우 데이터 팩터리의 이름을 변경합니다. 예를 들어 yournameGetStartedDF를 사용하여 데이터 팩터리를 다시 만듭니다. 명명 규칙에 대한 자세한 내용은 [Data Factory: 명명 규칙](data-factory-naming-rules.md)을 참조하세요.
   >
   > 데이터 팩터리의 이름은 나중에 DNS 이름으로 등록되므로 공개적으로 표시될 수도 있습니다.
   >
   >
1. **구독** 아래에서 데이터 팩터리를 만들려는 Azure 구독을 선택합니다.

1. 기존 리소스 그룹을 선택하거나 리소스 그룹을 만듭니다. 이 자습서의 경우 **ADFGetStartedRG** 라는 이름의 리소스 그룹을 만듭니다.

1. **위치** 에서 데이터 팩터리의 위치를 선택합니다. Data Factory 서비스에서 지원하는 지역만 드롭다운 목록에 표시됩니다.

1. **대시보드에 고정** 확인란을 선택합니다.

1. **만들기** 를 선택합니다.

   > [!IMPORTANT]
   > Data Factory 인스턴스를 만들려면 구독/리소스 그룹 수준에서 [Data Factory 참가자](../../role-based-access-control/built-in-roles.md#data-factory-contributor) 역할의 구성원이어야 합니다.
   >
   >
1. 대시보드에서 **데이터 팩터리 배포 중** 상태의 다음과 같은 타일이 표시됩니다.    

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png" alt-text="Data Factory 배포 중 상태":::

1. 데이터 팩터리가 만들어지면 **데이터 팩터리** 페이지가 표시되며, 여기에 데이터 팩터리의 내용이 표시됩니다.     

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png" alt-text="데이터 팩터리 블레이드":::

데이터 팩터리에서 파이프라인을 만들기 전에 먼저 몇 가지 데이터 팩터리 엔터티를 만들어야 합니다. 먼저 연결된 서비스를 만들어서 데이터 저장소/컴퓨터를 데이터 저장소에 연결합니다. 그런 다음, 입력 및 출력 데이터 세트를 정의하여 연결된 데이터 저장소에 입력/출력 데이터를 나타냅니다. 마지막으로 이러한 데이터 세트를 사용하는 작업이 있는 파이프라인을 만듭니다.

## <a name="create-linked-services"></a>연결된 서비스 만들기
이 단계에서는 Azure Storage 계정 및 주문형 HDInsight 클러스터를 데이터 팩터리에 연결합니다. 스토리지 계정은 이 샘플의 파이프라인에 대한 입력 및 출력 데이터를 가집니다. HDInsight 연결된 서비스는 이 샘플에서 파이프라인의 활동에 지정된 Hive 스크립트를 실행하는 데 사용됩니다. 어떤 [데이터 저장소](data-factory-data-movement-activities.md)/[계산 서비스](data-factory-compute-linked-services.md)가 시나리오에서 사용되는지 식별합니다. 그런 다음 연결된 서비스를 만들어 해당 서비스를 데이터 팩터리에 연결합니다.  

### <a name="create-a-storage-linked-service"></a>Storage 연결된 서비스 만들기
이 단계에서는 스토리지 계정을 데이터 팩터리에 연결합니다. 이 자습서에서는 동일한 스토리지 계정을 사용하여 입력/출력 데이터 및 HQL 스크립트 파일을 저장합니다.

1. **GetStartedDF** 에 대한 **데이터 팩터리** 블레이드에서 **작성자 및 배포** 를 선택합니다. 데이터 팩터리 편집기가 표시됩니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png" alt-text="작성 및 배포 타일":::

1. **새 데이터 저장소** 를 선택하고 **Azure Storage** 를 선택합니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png" alt-text="새 데이터 저장소 블레이드":::

1. 편집기에 Storage 연결된 서비스를 만들기 위한 JSON 스크립트가 표시됩니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png" alt-text="Storage 연결된 서비스":::

1. **account name** 을 스토리지 계정 이름으로 바꿉니다. **account key** 를 스토리지 계정의 액세스 키로 바꿉니다. 스토리지 액세스 키를 가져오는 방법을 알아보려면 [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요.

1. 명령 모음에서 **배포** 를 선택하여 연결된 서비스를 배포합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png" alt-text="배포 단추":::

   연결된 서비스가 성공적으로 배포되면 후 초안 1 창이 사라집니다. 왼쪽의 트리 뷰에서 **AzureStorageLinkedService** 가 표시됩니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png" alt-text="AzureStorageLinkedService":::    

### <a name="create-an-hdinsight-linked-service"></a>HDInsight 연결된 서비스 만들기
이 단계에서는 데이터 팩터리에 주문형 HDInsight 클러스터를 연결합니다. HDInsight 클러스터가 런타임에 자동으로 만들어집니다. 클러스터의 처리가 완료되고 지정된 시간 동안 유휴 상태를 유지한 후에 삭제됩니다.

1. Data Factory Editor에서 **자세히** > **새 계산** > **주문형 HDInsight 클러스터** 를 선택합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png" alt-text="새 컴퓨팅":::

1. 다음 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다. JSON 코드 조각은 주문형 HDInsight 클러스터를 만드는 데 사용되는 속성을 설명합니다.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

   | 속성 | Description |
   |:--- |:--- |
   | clusterSize |HDInsight 클러스터의 크기를 지정합니다. |
   | timeToLive | HDInsight 클러스터가 삭제되기 전 유휴 시간을 지정합니다. |
   | linkedServiceName | HDInsight에 의해 생성되는 로그를 저장하는데 사용될 스토리지 계정을 지정합니다. |

    다음 사항에 유의하세요.

     a. 데이터 팩터리는 JSON 속성으로 사용자에게 Linux 기반 HDInsight 클러스터를 만들어 줍니다. 자세한 내용은 [주문형 HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 참조하세요.

     b. 주문형 HDInsight 클러스터를 사용하는 대신 고유의 HDInsight 클러스터를 사용할 수 있습니다. 자세한 내용은 [HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)를 참조하세요.

     다. HDInsight 클러스터는 JSON 속성(**linkedServiceName**)에서 지정한 Blob Storage에 기본 컨테이너를 만듭니다. HDInsight는 클러스터가 삭제될 때 이 컨테이너를 삭제하지 않습니다. 이 동작은 의도된 것입니다. 주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(**timeToLive**)가 없는 경우 조각을 처리할 때마다 HDInsight 클러스터가 만들어집니다. 클러스터는 처리가 완료되면 자동으로 삭제됩니다.

     많은 조각이 처리될수록 Blob Storage에 컨테이너가 많아집니다. 작업의 문제 해결에 이 항목들이 필요하지 않다면 스토리지 비용을 줄이기 위해 삭제할 수 있습니다. 이러한 컨테이너의 이름은 “adf **yourdatafactoryname**-**linkedservicename**-datetimestamp” 패턴을 따릅니다. [Azure Storage Explorer](https://storageexplorer.com/)와 같은 도구를 사용하여 Blob Storage에서 컨테이너를 삭제합니다.

     자세한 내용은 [주문형 HDInsight 연결된 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 참조하세요.

1. 명령 모음에서 **배포** 를 선택하여 연결된 서비스를 배포합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png" alt-text="배포 옵션":::

1. 왼쪽의 트리 뷰에서 **AzureStorageLinkedService** 및 **HDInsightOnDemandLinkedService** 가 모두 표시되는지 확인합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png" alt-text="AzureStorageLinkedService와 HDInsightOnDemandLinkedService가 함께 연결되어 있음을 보여주는 스크린샷.":::

## <a name="create-datasets"></a>데이터 세트 만들기
이 단계에서는 Hive 처리에 대한 입력 및 출력 데이터를 나타내는 데이터 세트를 만듭니다. 이러한 데이터 세트는 이 자습서의 앞부분에서 만든 AzureStorageLinkedService를 참조합니다. 연결된 서비스는 스토리지 계정으로 연결됩니다. 데이터 세트는 입력 및 출력 데이터가 있는 스토리지의 컨테이너, 폴더 및 파일 이름을 지정합니다.   

### <a name="create-the-input-dataset"></a>입력 데이터 세트 만들기
1. 데이터 팩터리 편집기에서 **자세히** > **새 데이터 세트** > **Azure Blob Storage** 를 차례로 선택합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png" alt-text="새 데이터 세트":::

1. 다음 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다. JSON 코드 조각에서 파이프라인의 활동에 대한 입력 데이터를 나타내는 **AzureBlobInput** 이라는 데이터 세트를 만듭니다. 또한 입력 데이터가 **adfgetstarted** 라는 Blob 컨테이너 및 **inputdata** 라는 폴더에 있도록 지정합니다.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
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
    다음 테이블은 코드 조각에 사용된 JSON 속성에 대한 설명을 제공합니다.

   | 속성 | 중첩 | Description |
   |:--- |:--- |:--- |
   | type | properties |Blob Storage에 데이터가 있기 때문에 형식 속성은 **AzureBlob** 으로 설정됩니다. |
   | linkedServiceName | format |이전에 만든 AzureStorageLinkedService를 참조합니다. |
   | folderPath | typeProperties | 입력 Blob이 포함된 Blob 컨테이너와 폴더를 지정합니다. | 
   | fileName | typeProperties |이 속성은 선택 사항입니다. 이 속성을 생략하면 folderPath의 모든 파일이 선택됩니다. 이 자습서에서는 input.log 파일만 처리됩니다. |
   | type | format |로그 파일이 텍스트 형식이므로 **TextFormat** 을 사용합니다. |
   | columnDelimiter | format |로그 파일의 열은 쉼표(`,`)로 구분됩니다. |
   | frequency/interval | availability |**Month** 및 간격을 설정한 빈도가 **1** 인 경우 입력 조각은 매월 제공됩니다. |
   | external | properties | 이 파이프라인에 의해 입력 데이터가 생성되지 않는 경우 이 속성은 **true** 로 설정됩니다. 이 자습서에서는 이 파이프라인에 의해 input.log 파일이 생성되지 않으므로 이 속성이 **true** 로 설정됩니다. |

    이러한 JSON 속성에 대한 자세한 내용은 [Azure Blob 커넥터](data-factory-azure-blob-connector.md#dataset-properties)를 참조하세요.

1. 명령 모음에서 **배포** 를 선택하여 새로 만든 데이터 세트를 배포합니다. 왼쪽의 트리 뷰에서 데이터 세트를 표시합니다.

### <a name="create-the-output-dataset"></a>출력 데이터 세트 만들기
이제 Blob Storage에 저장된 출력 데이터를 나타내는 출력 데이터 세트를 만듭니다.

1. 데이터 팩터리 편집기에서 **자세히** > **새 데이터 세트** > **Azure Blob Storage** 를 차례로 선택합니다.

1. 다음 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다. JSON 코드 조각에서 **AzureBlobOutput** 이라는 데이터 세트를 만들고 Hive 스크립트에 의해 생성되는 데이터의 구조를 지정합니다. 또한 결과가 **adfgetstarted** 라는 Blob 컨테이너와 **partitioneddata** 라는 폴더에 저장되도록 지정합니다. **availability** 섹션에서는 출력 데이터 세트를 매일 생성하도록 지정합니다.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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
    이러한 속성에 대한 설명은 “입력 데이터 세트 만들기” 섹션을 참조하세요. Data Factory 서비스에서 데이터 세트가 생성되므로 출력 데이터 세트에서 외부 속성을 설정하지 않습니다.

1. 명령 모음에서 **배포** 를 선택하여 새로 만든 데이터 세트를 배포합니다.

1. 데이터 세트가 성공적으로 만들어졌는지 확인합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png" alt-text="연결된 서비스와 트리 뷰":::

## <a name="create-a-pipeline"></a>파이프라인 만들기
이 단계에서는 HDInsight Hive 작업을 사용하여 첫 번째 파이프라인을 만듭니다. 입력 조각은 매월 사용할 수 있습니다(주파수가 월, 간격은 1). 출력 조각은 매월 생성됩니다. 작업에 대한 스케줄러 속성도 매월로 설정됩니다. 출력 데이터 세트 및 작업 스케줄러에 대한 설정이 일치해야 합니다. 현재 출력 데이터 세트가 일정을 결정하므로 작업이 출력을 생성하지 않는 경우에도 출력 데이터 세트를 만들어야 합니다. 활동이 입력을 가져오지 않으면 입력 데이터 세트 만들기를 건너뛸 수 있습니다. 다음 JSON 코드 조각에서 사용되는 속성은 이 섹션의 마지막에 설명되어 있습니다.

1. 데이터 팩터리 편집기에서 **자세히** > **새 파이프라인** 을 차례로 선택합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png" alt-text="새 파이프라인 옵션":::

1. 다음 코드 조각을 복사하여 Draft-1 창에 붙여넣습니다.

   > [!IMPORTANT]
   > **storageaccountname** 을 JSON 코드 조각의 스토리지 계정 이름으로 바꿉니다.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
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
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    JSON 코드 조각에서 Hive를 사용하여 HDInsight 클러스터에서 데이터를 처리하는 단일 작업으로 구성되는 파이프라인을 만듭니다.

    Hive 스크립트 파일 **partitionweblogs.hql** 은 스토리지 계정(**AzureStorageLinkedService** 이라고 하는 scriptLinkedService에 의해 지정됨)에 저장됩니다. 컨테이너 **adfgetstarted** 의 **스크립트** 폴더에서 찾을 수 있습니다.

    **defines** 섹션은 Hive 스크립트에 Hive 구성 값으로 전달되는 런타임 설정을 지정하는 데 사용됩니다. 예는 ${hiveconf:inputtable} 및 ${hiveconf:partitionedtable}입니다.

    파이프라인의 **start** 및 **end** 속성은 파이프라인의 활성 기간을 지정합니다.

    작업 JSON에서 **linkedServiceName**: **HDInsightOnDemandLinkedService** 에서 지정한 컴퓨팅에 대해 Hive 스크립트가 실행되도록 지정합니다.

   > [!NOTE]
   > 예제에서 사용되는 JSON 속성에 대한 자세한 내용은 [Data Factory의 파이프라인 및 활동](data-factory-create-pipelines.md)에서 “파이프라인 JSON” 섹션을 참조하세요.
   >
   >
1. 다음을 확인합니다.

   a. **input.log** 파일은 Blob Storage에서 **adfgetstarted** 컨테이너의 **inputdata** 폴더에 있습니다.

   b. **partitionweblogs.hql** 파일은 Blob Storage에서 **adfgetstarted** 컨테이너의 **script** 폴더에 있습니다. 파일이 표시되지 않는 경우 [자습서 개요](data-factory-build-your-first-pipeline.md)의 “필수 구성 요소” 섹션의 단계를 완료합니다.

   다. **storageaccountname** 을 파이프라인 JSON의 스토리지 계정 이름으로 변경했습니다.

1. 명령 모음에서 **배포** 를 선택하여 파이프라인을 배포합니다. **시작** 및 **종료** 시간이 과거로 설정되고 **isPaused** 가 **false** 로 설정되었기 때문에 파이프라인(파이프라인의 작업)은 배포한 후에 즉시 실행됩니다.

1. 트리 뷰에 파이프라인이 표시되는지 확인합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png" alt-text="파이프라인과 트리 뷰":::



## <a name="monitor-a-pipeline"></a>파이프라인 모니터링
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>다이어그램 보기를 사용하여 파이프라인 모니터링
1. **Data Factory** 블레이드에서 **다이어그램** 을 선택합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png" alt-text="다이어그램 타일":::

1. **다이어그램** 보기에 이 자습서에서 사용된 파이프라인 및 데이터 세트의 개요가 표시됩니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png" alt-text="다이어그램 뷰":::

1. 파이프라인의 모든 작업을 보려면 다이어그램에서 파이프라인을 마우스 오른쪽 단추로 클릭하고 **파이프라인 열기** 를 선택합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png" alt-text="파이프라인 열기 메뉴":::

1. 파이프라인에서 **Hive 작업** 이 표시되는지 확인합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png" alt-text="파이프라인 보기 열기":::

    이전 보기로 돌아가려면 맨 위의 메뉴에서 **데이터 팩터리** 를 선택합니다.

1. **다이어그램** 보기에서 **AzureBlobInput** 데이터 세트를 두 번 클릭합니다. 조각이 **준비** 상태인지 확인합니다. 조각이 **Ready** 상태로 표시되려면 몇 분이 걸릴 수 있습니다. 잠시 기다려도 표시되지 않는 경우 오른쪽 컨테이너(**adfgetstarted**) 및 폴더(**inputdata**)에 배치된 입력 파일(**input.log**)이 있는지 확인합니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png" alt-text="준비 상태인 입력 조각":::

1. **AzureBlobInput** 블레이드를 닫습니다.

1. **다이어그램** 보기에서 **AzureBlobOutput** 데이터 세트를 두 번 클릭합니다. 현재 처리 중인 조각이 표시됩니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png" alt-text="진행 중인 데이터 세트 처리":::

1. 처리가 완료된 후 조각이 **준비** 상태로 표시됩니다.

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png" alt-text="준비 상태인 데이터 세트":::  

   > [!IMPORTANT]
   > 주문형 HDInsight 클러스터 만들기는 일반적으로 약 20분이 소요됩니다. 파이프라인이 조각을 처리하는 데 약 30분이 걸릴 수 있습니다.
   >
   >

1. 조각이 **준비** 상태에 있으면 출력 데이터에 대한 Blob Storage의 **adfgetstarted** 컨테이너에 있는 **partitioneddata** 폴더를 확인합니다.  

   :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png" alt-text="출력 데이터":::

1. 조각을 선택하여 **데이터 조각** 블레이드에서 자세한 내용을 확인합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png" alt-text="데이터 조각 정보":::

1. **활동 실행** 목록에서 활동 실행을 선택하여 항목에 대한 자세한 내용을 확인합니다. (이 시나리오에서는 Hive 작업입니다.) 정보가 **활동 실행 세부 정보** 블레이드에 표시됩니다.   

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png" alt-text="활동 실행 세부 정보 창":::    

   로그 파일에서 실행되는 Hive 쿼리 및 상태 정보를 볼 수 있습니다. 이러한 로그는 문제를 해결하는 데 유용합니다.
   자세한 내용은 [Azure Portal 블레이드를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md)를 참조하세요.

> [!IMPORTANT]
> 조각이 성공적으로 처리될 때 입력된 파일이 삭제됩니다. 따라서 조각을 다시 실행하거나 자습서를 다시 수행하려는 경우 **adfgetstarted** 컨테이너의 **inputdata** 폴더에 입력 파일(**input.log**)을 업로드합니다.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>앱 모니터링 및 관리를 사용하여 파이프라인 모니터링
애플리케이션 모니터링 및 관리를 사용하여 파이프라인을 모니터링할 수도 있습니다. 이 애플리케이션 사용 방법에 대한 자세한 내용은 [앱 모니터링 및 관리를 사용하여 데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

1. 데이터 팩터리의 홈페이지에서 **모니터링 및 관리** 타일을 선택합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png" alt-text="타일 모니터링 및 관리":::

1. 애플리케이션 모니터링 및 관리에서 **시작 시간** 및 **종료 시간** 을 파이프라인 시작 및 종료 시간에 맞게 변경합니다. **적용** 을 선택합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png" alt-text="앱 모니터링 및 관리":::

1. 자세한 내용을 보려면 **활동 창** 목록에서 활동 창을 선택합니다.

    :::image type="content" source="./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png" alt-text="활동 기간 목록":::

## <a name="summary"></a>요약
이 자습서에서는 HDInsight Hadoop 클러스터에서 Hive 스크립트를 실행하여 데이터를 처리하는 데 데이터 팩터리를 만들었습니다. Azure Portal에서 다음 단계를 수행하기 위해 Data Factory Editor를 사용했습니다.  

* 데이터 팩터리를 만듭니다.
* 두 개의 연결된 서비스를 만듭니다.
   * 입력/출력 파일이 있는 Blob Storage를 데이터 팩터리에 연결하는 Storage 연결된 서비스입니다.
   * HDInsight Hadoop 클러스터를 데이터 팩터리에 연결하는 HDInsight 주문형 연결된 서비스입니다. Data Factory는 입력 데이터를 처리하고 출력 데이터를 생성하기 위해 적시에 HDInsight Hadoop 클러스터를 만듭니다.
* 파이프라인에서 HDInsight Hive 작업에 대한 입력 및 출력 데이터를 설명하는 두 개의 데이터 세트를 만듭니다.
* HDInsight Hive 작업으로 파이프라인을 만듭니다.

## <a name="next-steps"></a>다음 단계
이 문서에서 파이프라인과 함께 주문형 HDInsight 클러스터에서 Hive 스크립트를 실행하는 변환 작업(HDInsight 작업)을 만들었습니다. 복사 작업을 사용하여 Blob 스토리지에서 Azure SQL Database로 데이터를 복사하는 방법은 [자습서: Blob Storage에서 SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

## <a name="see-also"></a>참고 항목
| 항목 | Description |
|:--- |:--- |
| [파이프라인](data-factory-create-pipelines.md) |이 문서는 Data Factory의 파이프라인 및 시나리오 또는 비즈니스를 위한 활동과 엔드투엔드 데이터 기반 워크플로 활용하는 방법을 이해하는 데 도움이 됩니다. |
| [데이터 세트](data-factory-create-datasets.md) |이 문서는 Data Factory의 데이터 세트를 이해하는 데 도움이 됩니다. |
| [예약 및 실행](data-factory-scheduling-and-execution.md) |이 문서에서는 Data Factory 애플리케이션 모델의 예약 및 실행에 대한 내용을 설명합니다. |
| [앱 모니터링 및 관리를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-app.md) |이 문서는 앱 모니터링 및 관리를 사용하여 파이프라인을 모니터링, 관리 및 디버그하는 방법을 설명합니다. |