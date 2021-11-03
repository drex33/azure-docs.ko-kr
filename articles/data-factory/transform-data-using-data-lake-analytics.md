---
title: U-SQL 스크립트를 사용하여 데이터 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics 사용하여 Azure Data Lake Analytics 컴퓨팅 서비스에서 U-SQL 스크립트를 실행하여 데이터를 처리하거나 변환하는 방법을 알아봅니다.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 3cf224f3b24d318818e53a8c6342edc9f56063e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077822"
---
# <a name="process-data-by-running-u-sql-scripts-on-azure-data-lake-analytics-with-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory 및 Synapse Analytics 사용하여 Azure Data Lake Analytics U-SQL 스크립트를 실행하여 데이터 처리
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-usql-activity.md)
> * [현재 버전](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 또는 Synapse Analytics 작업 영역의 파이프라인은 연결된 컴퓨팅 서비스를 사용하여 연결된 스토리지 서비스의 데이터를 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 이 문서는 **Azure Data Lake Analytics** 컴퓨팅 연결 서비스에서 **U-SQL** 스크립트를 실행하는 **Data Lake Analytics U-SQL 작업** 에 대해 설명합니다. 

Data Lake Analytics U-SQL 작업이 포함된 파이프라인을 만들기 전에 Azure Data Lake Analytics 계정을 만듭니다. Azure Data Lake Analytics에 대해 알아보려면 [Azure Data Lake Analytics 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)을 참조하세요.


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 연결된 서비스
**Azure Data Lake Analytics** 연결된 서비스를 만들어 Azure Data Lake Analytics 컴퓨팅 서비스를 Azure Data Factory 또는 Synapse Analytics 작업 영역에 연결합니다. 파이프라인에서 데이터 레이크 분석 U-SQL 작업은 이 연결된 서비스를 가리킵니다. 

다음 표에는 JSON 정의에서 사용하는 일반 속성에 대한 설명이 나와 있습니다. 

| 속성                 | 설명                              | 필수                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | 형식 속성은 **AzureDataLakeAnalytics** 로 설정해야 합니다. | 예                                      |
| **accountName**          | Azure 데이터 레이크 분석 계정 이름입니다.  | 예                                      |
| **dataLakeAnalyticsUri** | Azure 데이터 레이크 분석 URI입니다.           | 예                                       |
| **subscriptionId**       | Azure 구독 ID입니다.                    | 예                                       |
| **resourceGroupName**    | Azure 리소스 그룹 이름                | 예                                       |

### <a name="service-principal-authentication"></a>서비스 주체 인증
Azure Data Lake Analytics 연결된 서비스에는 Azure Data Lake Analytics 서비스에 연결하기 위해 서비스 사용자 인증이 필요합니다. 서비스 사용자 인증을 사용하려면 Azure AD(Azure Active Directory)에서 애플리케이션 엔터티를 등록하고 해당 엔터티에서 사용하는 Data Lake Analytics 및 Data Lake Store에 대한 액세스 권한을 부여합니다. 자세한 단계는 [서비스 간 인증](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

* 애플리케이션 UI
* 애플리케이션 키 
* 테넌트 ID

[사용자 추가 마법사](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user)를 사용하여 Azure Data Lake Analytics에 서비스 주체 권한을 부여합니다.

다음 속성을 지정하여 서비스 주체 인증을 사용합니다.

| 속성                | 설명                              | 필수 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | 애플리케이션의 클라이언트 ID를 지정합니다.     | 예      |
| **servicePrincipalKey** | 애플리케이션의 키를 지정합니다.           | 예      |
| **테넌트**              | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 예      |

**예: 서비스 주체 인증**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요.

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 작업
다음 JSON 조각은 Data Lake Analytics U-SQL 작업이 포함된 파이프라인을 정의합니다. 작업 정의에 앞에서 만든 Azure Data Lake Analytics 연결된 서비스에 대한 참조가 있습니다. Data Lake Analytics U-SQL 스크립트를 실행하기 위해 서비스는 지정한 스크립트를 Data Lake Analytics 제출하고, 필요한 입력 및 출력은 Data Lake Analytics 가져오고 출력하기 위해 스크립트에 정의됩니다. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

다음 표에는 이 작업과 관련된 속성 이름과 설명이 나와 있습니다. 

| 속성            | 설명                              | 필수 |
| :------------------ | :--------------------------------------- | :------- |
| name                | 파이프라인의 작업 이름입니다.     | 예      |
| description         | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다.  | 예       |
| type                | Data Lake Analytics U-SQL 작업의 경우 작업 형식은 **DataLakeAnalyticsU-SQL** 입니다. | 예      |
| linkedServiceName   | Azure Data Lake Analytics에 연결된 서비스입니다. 이 연결된 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요.  |예       |
| scriptPath          | U-SQL 스크립트가 포함된 폴더 경로입니다. 파일 이름은 대/소문자를 구분합니다. | 예      |
| scriptLinkedService | 스크립트가 포함된 **Azure Data Lake Store** 또는 **Azure Storage** 연결하는 연결된 서비스 | 예      |
| degreeOfParallelism | 작업을 실행하는 데 동시에 사용되는 최대 노드 수입니다. | 예       |
| priority            | 대기열에 있는 모든 작업 중에서 먼저 실행해야 하는 작업을 결정합니다. 번호가 낮을수록 우선 순위가 높습니다. | 예       |
| 매개 변수          | U-SQL 스크립트에 전달할 매개 변수입니다.    | 예       |
| runtimeVersion      | 사용할 U-SQL 엔진의 런타임 버전입니다. | 예       |
| compilationMode     | <p>U-SQL의 컴파일 모드 다음 값 중 하나여야 합니다. **의미 체계:** 의미 체계 검사 및 필수 온전성 검사만 수행합니다. **전체:** 구문 검사, 최적화, 코드 생성 등을 비롯하여 전체 컴파일을 수행합니다. **SingleBox:** TargetType이 SingleBox로 설정된 상태에서 전체 컴파일을 수행합니다. 이 속성에 대한 값을 지정하지 않으면 서버가 최적의 컴파일 모드를 결정합니다. | 예 |

스크립트 정의에 대해서는 [SearchLogProcessing.txt](#sample-u-sql-script)를 참조하세요. 

## <a name="sample-u-sql-script"></a>샘플 U-SQL 스크립트

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

위의 스크립트 예제에서 스크립트에 대한 입력 및 출력은 **\@in** 및 **\@out** 매개 변수에 정의됩니다. U-SQL 스크립트의 **\@ in** 및 **\@ out** 매개 변수에 대 한 값은 ' parameters ' 섹션을 사용 하 여 서비스에 의해 동적으로 전달 됩니다. 

Azure Data Lake Analytics 서비스에서 실행되는 작업에 대한 파이프라인 정의뿐 아니라 degreeOfParallelism나 우선 순위와 같은 다른 속성을 지정할 수 있습니다.

## <a name="dynamic-parameters"></a>동적 매개 변수
샘플 파이프라인 정의에서 in 및 out 매개 변수는 하드 코드된 값으로 할당됩니다. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

동적 매개 변수를 대신 사용할 수 있습니다. 예를 들면 다음과 같습니다. 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

이 경우 입력 파일은 여전히 /datalake/input 폴더에서 가져오며 출력 파일은 /datalake/output 폴더에 생성됩니다. 파일 이름은 파이프라인이 트리거될 때 전달되는 창 시작 시간을 기준으로 동적입니다.  

## <a name="next-steps"></a>다음 단계
다른 방법으로 데이터를 변환하는 방법을 설명하는 다음 문서를 참조하세요. 

* [Hive 작업](transform-data-using-hadoop-hive.md)
* [Pig 작업](transform-data-using-hadoop-pig.md)
* [MapReduce 작업](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 스트리밍 작업](transform-data-using-hadoop-streaming.md)
* [Spark 작업](transform-data-using-spark.md)
* [.NET 사용자 지정 작업](transform-data-using-dotnet-custom-activity.md)
* [저장 프로시저 작업](transform-data-using-stored-procedure.md)
