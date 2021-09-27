---
title: Data Factory를 사용하여 Salesforce로부터 데이터 이동하기
description: Azure Data Factory를 사용하여 Salesforce에서 데이터를 이동하는 방법에 대해 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f25329389952546007ddf6365fa7b40c9870e264
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610386"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Salesforce에서 데이터 이동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-salesforce-connector.md)
> * [버전 2(현재 버전)](../connector-salesforce.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 Salesforce 커넥터](../connector-salesforce.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 활동을 사용하여 Salesforce의 데이터를 [지원되는 원본 및 싱크](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블에 있는 싱크 열에 나열된 데이터 저장소에 복사하는 방법을 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소 조합을 사용하여 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 Azure Data Factory는 Salesforce에서 [지원되는 싱크 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)로 데이터를 이동하도록 지원하며 다른 데이터 저장소에서 Salesforce로 데이터를 이동하도록 지원하지 않습니다.

## <a name="supported-versions"></a>지원되는 버전
이 커넥터는 Salesforce Developer Edition, Professional Edition, Enterprise Edition 또는 Unlimited Edition을 지원합니다. 그리고 Salesforce 프로덕션, 샌드박스 및 사용자 지정 도메인에서 복사를 지원합니다.

## <a name="prerequisites"></a>필수 조건
* API 권한을 사용하도록 설정해야 합니다. [권한 집합에 따라 Salesforce에서 API 액세스를 사용하도록 설정하는 방법](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Salesforce에서 온-프레미스 데이터 저장소로 데이터를 복사하려면 온-프레미스 환경에 데이터 관리 게이트웨이 2.0이 설치되어 있어야 합니다.

## <a name="salesforce-request-limits"></a>Salesforce 요청 제한
Salesforce에는 총 API 요청 수와 동시 API 요청 수에 대한 제한이 있습니다. 다음 사항에 유의하세요.

- 동시 요청 수가 이 제한을 초과하면 제한이 발생하며 임의 오류가 표시됩니다.
- 총 요청 수가 이 제한을 초과하면 Salesforce 계정은 24시간 동안 차단됩니다.

두 시나리오 모두에서 ‘REQUEST_LIMIT_EXCEEDED’ 오류가 나타날 수 있습니다. 자세한 내용은 [Salesforce 개발자 제한](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) 문서의 “API 요청 제한” 섹션을 참조하세요.

## <a name="getting-started"></a>시작
다른 도구/API를 사용하여 Salesforce의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사** 를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

또한 **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API**, **REST API** 를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스** 를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트** 를 만듭니다.
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인** 을 만듭니다.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. Salesforce의 데이터를 복사하는 데 사용되는 데이터 팩터리 엔터티의 JSON 정의가 포함된 샘플은 이 문서의 [JSON 예: Salesforce에서 Azure Blob으로 데이터 복사](#json-example-copy-data-from-salesforce-to-azure-blob) 섹션을 참조하세요.

다음 섹션에서는 Salesforce에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 테이블에서는 Salesforce 연결된 서비스에 지정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| type |형식 속성은 **Salesforce** 로 설정되어야 합니다. |예 |
| environmentUrl | Salesforce 인스턴스의 URL을 지정합니다. <br><br> -기본값은 "https:\//login.salesforce.com"입니다. <br> - 샌드박스에서 데이터를 복사하려면 "[https://test.salesforce.com](https://test.salesforce.com)"을 지정합니다. <br> -사용자 지정 도메인에서 데이터를 복사하려면 예를 들어 "https://[domain].my.salesforce.com"을 지정합니다. |예 |
| 사용자 이름 |사용자 계정의 사용자 이름을 지정합니다. |예 |
| password |사용자 계정으로 password를 지정합니다. |예 |
| securityToken |사용자 계정에 대한 보안 토큰을 지정합니다. 보안 토큰을 재설정하거나 가져오는 방법에 대한 자세한 내용은 [보안 토큰 가져오기](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) 를 참조하세요. 일반적인 보안 토큰에 대해 자세히 알아보려면 [보안 및 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)를 참조하세요. |예 |

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 데이터 세트 형식의 데이터 세트에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| tableName |Salesforce에 있는 테이블의 이름입니다. |아니요(**RelationalSource** 의 **query** 가 지정된 경우) |

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 API 이름에 "__c" 부분이 필요합니다.

:::image type="content" source="media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png" alt-text="스크린샷에서 사용자 지정 개체의 API 이름을 볼 수 있는 사용자 지정 개체 정의의 세부 정보를 확인할 수 있습니다.":::

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력과 출력 테이블 및 다양한 정책과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

원본이 **RelationalSource** 형식인 복사 작업에서(Salesforce 포함) typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL-92 쿼리 또는 [SOQL(Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 쿼리입니다. 예: `select * from MyTable__c` |아니요(**데이터 세트** 의 **tableName** 이 지정된 경우) |

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 API 이름에 "__c" 부분이 필요합니다.

:::image type="content" source="media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png" alt-text="스크린샷 사용자 지정 개체의 A P I 이름을 볼 수 있는 사용자 지정 필드 & 관계를 보여 줍니다.":::

## <a name="query-tips"></a>쿼리 팁
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>DateTime 열에서 where 절을 사용하여 데이터 검색
SOQL 또는 SQL 쿼리를 지정할 때 DateTime 형식 차이에 주의해야 합니다. 예를 들면 다음과 같습니다.

* **SOQL 샘플**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL 샘플**:
    * **복사 마법사를 사용하여 쿼리 지정:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **JSON 편집을 사용하여 쿼리 지정(적절하게 문자 이스케이프):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Salesforce 보고서에서 데이터 검색
`{call "<report name>"}`과 같이 쿼리를 지정하여 Salesforce 보고서에서 데이터를 검색할 수 있으며 그 예는 다음과 같습니다. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Salesforce 휴지통에서 삭제된 레코드 검색
임시 삭제된 쿼리를 Salesforce 휴지통에서 쿼리하려면 쿼리에 **"IsDeleted = 1"** 을 지정하면 됩니다. 예제:

* 삭제된 레코드만 쿼리하려면 "select * from MyTable__c **where IsDeleted= 1**"을 지정합니다.
* 기존 레코드와 삭제된 레코드를 포함하여 모든 레코드를 쿼리하려면 "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**"을 지정합니다.

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON 예: Salesforce에서 Azure Blob으로 데이터 복사
다음 예제에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 이 샘플은 Salesforce에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 에 설명한 싱크로 데이터를 복사할 수 있습니다.

시나리오를 구현하도록 만들어야 하는 데이터 팩터리 아티팩트는 다음과 같습니다. 목록 다음에 나오는 섹션에서는 이러한 단계에 대한 세부 정보를 제공합니다.

* [Salesforce](#linked-service-properties)
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* [RelationalTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [RelationalSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

**Salesforce 연결된 서비스**

이 예제에서는 **Salesforce** 연결된 서비스를 사용합니다. 이 연결된 서비스에서 지원하는 속성 목록은 [Salesforce 연결된 서비스](#linked-service-properties) 섹션을 참조하세요. 보안 토큰을 재설정하거나 가져오는 방법에 대한 자세한 내용은 [보안 토큰 가져오기](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) 를 참조하세요.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Azure Storage 연결된 서비스**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**Salesforce 입력 데이터 세트**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**external** 을 **true** 로 설정하면 데이터 세트가 Data Factory의 외부에 있고 Data Factory의 활동으로 생성되지 않는다고 Data Factory 서비스에 전달됩니다.

> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 API 이름에 "__c" 부분이 필요합니다.

:::image type="content" source="media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png" alt-text="스크린샷에서 단일 레이블, 복수 레이블, 개체 이름 및 API 이름을 볼 수 있는 사용자 지정 개체 정의 세부 정보를 확인할 수 있습니다.":::

**Azure Blob 출력 데이터 세트:**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회)

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**복사 작업을 포함하는 파이프라인**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하며, 매시간 실행되도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다.

RelationalSource에서 지원하는 속성 목록은 [RelationalSource 형식 속성](#copy-activity-properties) 을 참조하세요.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> 모든 사용자 지정 개체에 대해 API 이름에 "__c" 부분이 필요합니다.

:::image type="content" source="media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png" alt-text="스크린샷은 A P I 이름이 호출된 사용자 지정 필드 & 관계를 보여줍니다.":::


### <a name="type-mapping-for-salesforce"></a>Salesforce에 대한 형식 매핑

| Salesforce 형식 | .NET 기반 형식 |
| --- | --- |
| 자동 번호 |String |
| 확인란 |부울 |
| 통화 |Decimal |
| Date |DateTime |
| 날짜/시간 |DateTime |
| Email |String |
| Id |String |
| 관계 조회 |String |
| 다중 선택 선택 목록 |String |
| 숫자 |Decimal |
| 백분율 |Decimal |
| Phone |String |
| 선택 목록 |String |
| 텍스트 |문자열 |
| 텍스트 영역 |String |
| 텍스트 영역(Long) |String |
| 텍스트 영역(Rich) |String |
| 텍스트(암호화됨) |String |
| URL |String |

> [!NOTE]
> 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

[!INCLUDE [data-factory-structure-for-rectangular-datasets](includes/data-factory-structure-for-rectangular-datasets.md)]

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md) 를 참조하세요.
