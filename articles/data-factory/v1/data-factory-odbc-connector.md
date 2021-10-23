---
title: ODBC 데이터 저장소에서 데이터 이동
description: Azure 데이터 팩터리를 사용하여 ODBC 데이터 저장소에서 데이터를 이동하는 방법에 대해 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
ms.custom: devx-track-azurepowershell
robots: noindex
ms.openlocfilehash: b0bf20ef43fe828a1bdd78c28a99484a758bf583
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264031"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Azure 데이터 팩터리를 사용하여 ODBC 데이터 저장소에서 데이터 이동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-odbc-connector.md)
> * [버전 2(현재 버전)](../connector-odbc.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 ODBC 커넥터](../connector-odbc.md)를 참조하세요.


이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스 ODBC 데이터 저장소에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

ODBC 데이터 저장소에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블을 참조하세요. 현재 데이터 팩터리는 다른 데이터 저장소에서 ODBC 데이터 저장소로 데이터 이동이 아닌 ODBC 데이터 저장소에서 다른 데이터 저장소로 데이터 이동만을 지원합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>연결 사용
데이터 팩터리 서비스는 데이터 관리 게이트웨이를 사용하여 온-프레미스 ODBC 원본에 연결을 지원합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요. Azure IaaS VM에서 호스팅되는 경우 ODBC 데이터 저장소에 연결하려면 게이트웨이를 사용합니다.

게이트웨이를 동일한 온-프레미스 컴퓨터 또는 ODBC 데이터 저장소로 Azure VM에 설치할 수 있습니다. 그러나 리소스 경합을 방지하고 성능 향상을 꾀하려면 게이트웨이를 별도 컴퓨터/Azure IaaS VM에 설치하는 것이 좋습니다. 별도 컴퓨터에 게이트웨이를 설치하는 경우 컴퓨터는 ODBC 데이터 저장소를 사용하여 컴퓨터에 액세스할 수 있어야 합니다.

데이터 관리 게이트웨이 외에도 게이트웨이 컴퓨터에서 데이터 저장소에 대한 ODBC 드라이버를 설치해야 합니다.

> [!NOTE]
> 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) 을 참조하세요.

## <a name="getting-started"></a>시작
다른 도구/API를 사용하여 ODBC 데이터 저장소의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사** 를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

또한 **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API**, **REST API** 를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스** 를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트** 를 만듭니다.
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인** 을 만듭니다.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. ODBC 데이터 저장소의 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의에 대한 샘플은 이 문서의 [JSON의 예: ODBC 데이터 저장소에서 Azure Blob으로 데이터 복사](#json-example-copy-data-from-odbc-data-store-to-azure-blob) 섹션을 참조하세요.

다음 섹션에서는 ODBC 데이터 저장소에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 테이블은 ODBC 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| type |형식 속성은 다음으로 설정해야 함: **OnPremisesOdbc** |예 |
| connectionString |선택적 암호화된 자격 증명 및 연결 문자열의 비 액세스 자격 증명 부분입니다. 다음 섹션의 예제를 참조하십시오. <br/><br/>`"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`와 같은 패턴으로 연결 문자열을 지정하거나 `"DSN=<name of the DSN>;"`을 사용하여 게이트웨이 컴퓨터에서 설정한 시스템 DSN(데이터 원본 이름)을 사용할 수 있습니다(그에 따라 연결된 서비스에 자격 증명 부분을 지정해야 함). |예 |
| 자격 증명(credential) |드라이버 관련 속성 값 형식에 지정된 연결 문자열의 액세스 자격 증명 부분입니다. 예: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |예 |
| authenticationType |ODBC 데이터 저장소에 연결하는 데 사용되는 인증 형식입니다. 가능한 값은 익명 및 기본입니다. |예 |
| userName |기본 인증을 사용하는 경우 사용자 이름을 지정합니다. |예 |
| password |userName에 지정한 사용자 계정의 암호를 지정합니다. |예 |
| gatewayName |데이터 팩터리 서비스가 ODBC 데이터 저장소에 연결하는 데 사용해야 하는 게이트웨이의 이름. |예 |

### <a name="using-basic-authentication"></a>기본 인증 사용

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>암호화된 자격 증명으로 기본 인증 사용
[New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue)(Azure PowerShell의 1.0 버전) cmdlet 또는 [New-AzureDataFactoryEncryptValue](/previous-versions/azure/dn834940(v=azure.100))(Azure PowerShell의 0.9 이전 버전)를 사용하여 자격 증명을 암호화할 수 있습니다.

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>익명 인증 사용

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 형식(ODBC 데이터 세트를 포함)의 데이터 세트에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| tableName |ODBC 데이터 저장소에 있는 테이블의 이름입니다. |예 |

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

원본이 **RelationalSource** 형식인 복사 작업(ODBC 포함)에서 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| Query |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: select * from MyTable. |예 |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON 예: ODBC 데이터 저장소에서 Azure Blob으로 데이터 복사
다음 예제에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 JSON 정의를 제공합니다. 이 샘플은 ODBC 원본에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 에 설명한 싱크로 데이터를 복사할 수 있습니다.

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1. [OnPremisesOdbc](#linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [RelationalTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
5. [RelationalSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 ODBC 데이터 저장소의 쿼리 결과에서 Blob에 매시간 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 데이터 관리 게이트웨이를 설정합니다. 해당 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서에 나와 있습니다.

**ODBC 연결된 서비스** 이 예제에서는 기본 인증을 사용합니다. 사용할 수 있는 다른 유형의 인증은 [ODBC 연결된 서비스](#linked-service-properties) 섹션을 참조하세요.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
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

**ODBC 입력 데이터 세트**

이 샘플은 ODBC 데이터베이스에서 만든 테이블 “MyTable”에 시계열 데이터에 대한 “timestampcolumn”이라는 열이 포함되었다고 가정합니다.

"external": "true" 설정은 데이터 세트가 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실을 Data Factory 서비스에 전달합니다.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
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

**Azure Blob 출력 데이터 세트**

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**ODBC 원본(RelationalSource) 및 Blob 싱크 (BlobSink)를 사용하는 파이프라인의 복사 작업**

파이프라인은 이러한 입력 및 출력 데이터 세트를 사용하도록 구성되며 매시간 실행되도록 예약되는 복사 활동을 포함합니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다. **query** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>ODBC에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

ODBC 데이터 저장소에서 데이터를 이동할 때 [ODBC 데이터 형식 매핑](/dotnet/framework/data/adonet/odbc-data-type-mappings) 토픽에서 설명된 대로 ODBC 데이터 형식은 .NET 형식에 매핑됩니다.

## <a name="map-source-to-sink-columns"></a>원본을 싱크 열로 매핑
원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하는 방법은 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="repeatable-read-from-relational-sources"></a>관계형 원본에서 반복 가능한 읽기
관계형 데이터 저장소에서 데이터를 복사할 때는 의도치 않는 결과를 방지하기 위해 반복성을 염두에 두어야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 세트에 대한 재시도 정책을 구성할 수 있습니다. 어느 쪽이든 조각이 재실행되는 경우 조각이 실행되는 횟수에 관계없이 같은 데이터를 읽어야 합니다. [관계형 원본에서 반복 가능한 읽기](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)를 참조하세요.

## <a name="troubleshoot-connectivity-issues"></a>연결 문제 해결
연결 문제를 해결하려면 **데이터 관리 게이트웨이 구성 관리자** 의 **진단** 탭을 사용합니다.

1. **데이터 관리 게이트웨이 구성 관리자** 를 시작합니다. "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe"를 직접 실행하거나 다음 이미지에서처럼 **게이트웨이** 를 검색하여 **Microsoft 데이터 관리 게이트웨이** 애플리케이션에 대한 링크를 찾을 수 있습니다.

    :::image type="content" source="./media/data-factory-odbc-connector/search-gateway.png" alt-text="게이트웨이 검색":::
2. **진단** 탭으로 전환합니다.

    :::image type="content" source="./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png" alt-text="게이트웨이 진단":::
3. 데이터 저장소(연결된 서비스)의 **형식** 을 선택합니다.
4. **인증** 을 지정하고 데이터 저장소에 연결된 **자격 증명** 을 입력하거나 **연결 문자열** 을 입력합니다.
5. **연결 테스트** 를 클릭하여 데이터 저장소에 대한 연결을 테스트합니다.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
