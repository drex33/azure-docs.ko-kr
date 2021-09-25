---
title: 'Data Factory 자습서: 첫 번째 데이터 파이프라인 '
description: 이 Azure 데이터 팩터리 자습서에서는 Hadoop 클러스터에서 Hive 스크립트를 사용하여 데이터를 처리하는 데이터 팩터리를 만들고 예약하는 방법을 보여 줍니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 06d5821fd6841f0fce5b657a49ecbca6fe232be1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557796"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>자습서: Hadoop 클러스터를 사용하여 데이터를 변환하는 첫 번째 파이프라인 빌드
> [!div class="op_single_selector"]
> * [개요 및 필수 구성 요소](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 템플릿](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용하는 경우 [빠른 시작: Azure Data Factory를 사용하여 데이터 팩터리 만들기](../quickstart-create-data-factory-dot-net.md)를 참조하세요.

이 자습서에서는 데이터 파이프라인을 사용하여 첫 번째 Azure Data Factory를 빌드합니다. 이 파이프라인은 Azure HDInsight(Hadoop) 클러스터에서 Hive 스크립트를 실행하여 입력 데이터를 변환하고 출력 데이터를 생성합니다.  

이 문서에서는 자습서에 대한 개요와 필수 구성 요소를 제공합니다. 필수 구성 요소를 완료한 후에는 Visual Studio, PowerShell, Resource Manager 템플릿, REST API 중 하나를 사용하여 자습서를 수행할 수 있습니다. 이러한 옵션 중 하나를 사용하여 자습서를 수행하려면 이 문서의 시작에 있는 드롭다운 목록이나 끝에 있는 링크에서 옵션 중 하나를 선택합니다.    

## <a name="tutorial-overview"></a>자습서 개요
이 자습서에서는 다음 단계를 수행합니다.

1. **데이터 팩터리** 를 만듭니다. 데이터 팩터리는 데이터를 이동 및 변환하는 하나 이상의 데이터 파이프라인을 포함할 수 있습니다.

    이 자습서에서는 데이터 팩터리에 하나의 파이프라인을 만듭니다.
2. **파이프라인** 을 만듭니다. 파이프라인에는 하나 이상의 활동(예: 복사 활동, HDInsight Hive 활동)이 포함될 수 있습니다. 이 샘플에서는 HDInsight Hadoop 클러스터에서 Hive 스크립트를 실행하는 HDInsight Hive 작업을 사용합니다. 스크립트는 먼저 Azure Blob Storage에 저장된 원시 웹 로그 데이터를 참조하는 테이블을 만든 다음 원시 데이터를 연도별 및 월별로 분할합니다.

    이 자습서에서 파이프라인은 Azure HDInsight Hadoop 클러스터에서 Hive 쿼리를 실행하여 데이터를 변환하기 위해 Hive 작업을 사용합니다.
3. **연결된 서비스** 만들기. 연결된 서비스를 만들어서 데이터 저장소 또는 컴퓨팅 서비스를 데이터 팩터리에 연결합니다. Azure Storage와 같은 데이터 저장소는 파이프라인에서 활동의 입/출력 데이터를 저장합니다. HDInsight Hadoop cluster 클러스터와 같은 컴퓨팅 서비스는 데이터를 처리/변환합니다.

    이 자습서에는 두 가지 연결된 서비스 **Azure Storage** 및 **Azure HDInsight** 를 만듭니다. Azure Storage 연결된 서비스는 데이터 팩터리에 대한 입력/출력 데이터를 보유하는 Azure Storage 계정을 연결합니다. Azure HDInsight 연결된 서비스는 데이터 팩터리에 대한 데이터를 변환하는 데 사용된 Azure HDInsight 클러스터를 연결합니다.
3. 입력 및 출력 **데이터 세트** 를 만듭니다. 입력 데이터 세트는 파이프라인의 작업에 대한 입력을 나타내고 출력 데이터 세트는 작업에 대한 출력을 나타냅니다.

    이 자습서에서 입력 및 출력 데이터 세트는 Azure Blob Storage에서 입력 및 출력 데이터의 위치를 지정합니다. Azure Storage 연결된 서비스는 어떤 Azure Storage 계정이 사용되는지를 지정합니다. 입력 데이터 세트는 입력 파일의 위치를 지정하고 출력 데이터 세트는 출력 파일이 있는 위치를 지정합니다.


Azure Data Factory에 대한 자세한 개요는 [Azure Data Factory 소개](data-factory-introduction.md) 문서를 참조하세요.

다음은 이 자습서에서 빌드한 샘플 데이터 팩터리의 **다이어그램 뷰** 입니다. **MyFirstPipeline** 에는 입력으로 **AzureBlobInput** 데이터 세트를 사용하고 **AzureBlobOutput** 데이터 세트를 출력으로 생성하는 Hive 형식의 한 가지 작업을 포함합니다.

:::image type="content" source="media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png" alt-text="데이터 팩터리 자습서에서 다이어그램 보기":::


이 자습서에서 **adfgetstarted** Azure blob 컨테이너의 **inputdata** 폴더에는 input.log라는 하나의 파일이 들어 있습니다. 이 로그 파일은 2016년 1월, 2월 및 3월 등 세 달에 항목이 있습니다. 다음은 입력 파일의 각 월에 대한 샘플 행입니다.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

HDInsight Hive 작업을 사용하여 파이프라인에서 파일이 처리될 때 작업은 입력 데이터를 연도별 및 월별로 분할하는 HDInsight 클러스터에서 Hive 스크립트를 실행합니다. 스크립트는 각 월의 항목을 가진 파일을 포함하는 세 개의 출력 폴더를 만듭니다.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

위에 표시된 샘플 줄에서 첫 번째 줄(2016-01-01)은 월=1 폴더의 000000_0 파일에 기록됩니다. 마찬가지로 두 번째 줄은 월=2 폴더의 파일에 기록되고 세 번째 줄은 월=3 폴더의 파일에 기록됩니다.  

## <a name="prerequisites"></a>사전 요구 사항
이 자습서를 시작하기 전에 다음 필수 조건이 있어야 합니다.

1. **Azure 구독** - Azure 구독이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 무료 평가판 계정을 확보하는 방법은 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/) 문서를 참조하세요.
2. **Azure 스토리지** – 이 자습서에서는 데이터 저장을 위해 Azure Storage 계정을 사용합니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../../storage/common/storage-account-create.md) 문서를 참조하세요. 스토리지 계정을 만든 후 **계정 이름** 과 **액세스 키** 를 적어둡니다. 스토리지 계정 액세스 키를 검색하는 방법에 대한 자세한 내용은 [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요.
3. [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql)에 있는 Hive 쿼리 파일(**HQL**)을 다운로드하고 검토합니다. 이 쿼리는 출력 데이터를 생성하기 위해 입력 데이터를 변환합니다.
4. [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)에 있는 샘플 입력 파일(**input.log**)을 다운로드하고 검토합니다.
5. Azure Blob Storage에 **adfgetstarted** 라는 Blob 컨테이너를 만듭니다.
6. **adfgetstarted** 컨테이너의 **script** 폴더에 **partitionweblogs.hql** 파일을 업로드합니다. [Microsoft Azure Storage Explorer](https://storageexplorer.com/)와 같은 도구를 사용합니다.
7. **adfgetstarted** 컨테이너의 **inputdata** 폴더에 **input.log** 파일을 업로드합니다.

필수 조건을 완료했으면 다음 도구/SDK 중 하나를 선택하여 자습서를 수행합니다.

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager 템플릿](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio에서는 GUI 방식으로 데이터 팩터리를 빌드할 수 있습니다. 반면, PowerShell, Resource Manager 템플릿 및 REST API 옵션은 데이터 팩터리를 빌드하는 스크립팅/프로그래밍 방식을 제공합니다.

> [!NOTE]
> 이 자습서의 데이터 파이프라인은 출력 데이터를 생성하는 입력 데이터를 변환합니다. 원본 데이터 저장소의 데이터를 대상 데이터 저장소로 복사하지 않습니다. Azure Data Factory를 사용하여 데이터를 복사하는 방법에 대한 자습서는 [자습서: Blob Storage에서 SQL Database로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
>
> 한 활동의 출력 데이터 세트를 다른 활동의 입력 데이터 세트로 설정하여 두 활동을 연결하면 해당 활동을 차례로 실행할 수 있습니다. 자세한 정보는 [데이터 팩터리의 예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요.
