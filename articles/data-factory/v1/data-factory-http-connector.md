---
title: HTTP 원본에서 데이터 이동 - Azure
description: Azure Data Factory를 사용하여 온-프레미스 또는 클라우드 HTTP 소스에서 데이터를 이동하는 방법을 알아봅니다.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 05/22/2018
robots: noindex
ms.openlocfilehash: 316b8cbcf056b5f26d0043d5a09d688215f1ae9f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651202"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 HTTP 소스에서 데이터 이동

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-http-connector.md)
> * [버전 2(현재 버전)](../connector-http.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Azure Data Factory 서비스를 사용 중인 경우, [V2의 HTTP 커넥터](../connector-http.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스 또는 클라우드 HTTP 엔드포인트의 데이터를 지원되는 싱크 데이터 저장소로 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md)을 기반으로 합니다. 이 문서에는 복사 작업이 원본 및 싱크로 지원하는 데이터 저장소도 나열되어 있습니다.

현재 Data Factory는 HTTP 소스에서 다른 데이터 저장소로 데이터를 이동하는 경우만 지원합니다. 다른 데이터 저장소에서 HTTP 대상으로 데이터 이동은 지원하지 않습니다.

## <a name="supported-scenarios-and-authentication-types"></a>지원되는 시나리오 및 인증 형식

이 HTTP 커넥터에서 HTTP **GET** 또는 **POST** 메서드를 사용하여 *클라우드 및 온-프레미스 HTTP/S 엔드포인트* 에서 데이터를 검색할 수 있습니다. 다음 인증 형식이 지원됩니다. **Anonymous**, **Basic**, **Digest**, **Windows** 및 **ClientCertificate**. 이 커넥터와 [웹 테이블 커넥터](data-factory-web-table-connector.md)의 차이를 알아두세요. 웹 테이블 커넥터는 HTML 웹 페이지에서 테이블 콘텐츠를 추출합니다.

온-프레미스 HTTP 엔드포인트에서 데이터를 복사할 때 온-프레미스 환경 또는 Azure VM에서 데이터 관리 게이트웨이를 설치해야 합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.

## <a name="get-started"></a>시작

다른 도구나 API를 사용하여 HTTP 소스의 데이터를 이동하는 복사 작업이 포함된 파이프라인을 만들 수 있습니다.

- 파이프라인을 만드는 가장 쉬운 방법은 데이터 복사 마법사를 사용하는 것입니다. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

- 또한 다음 도구를 사용하여 파이프라인을 만들 수도 있습니다. **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 또는 **REST API**. 복사 작업이 포함된 파이프라인을 만드는 방법에 대한 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. HTTP 소스에서 Azure Blob Storage로 데이터를 복사하는 JSON 샘플은 [JSON 예](#json-examples) 섹션을 참조하세요.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음 표는 HTTP 연결 서비스에 해당하는 JSON 요소에 대해 설명합니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| type | **type** 속성은 **Http** 로 설정해야 합니다. | 예 |
| url | 웹 서버의 기본 URL입니다. | 예 |
| authenticationType | 인증 유형을 지정합니다. 허용되는 값은 **Anonymous**, **Basic**, **Digest**, **Windows** 및 **ClientCertificate** 입니다. <br><br> 이러한 인증 형식의 더 많은 속성 및 JSON 샘플은 이 문서의 이후 섹션을 참조하세요. | 예 |
| enableServerCertificateValidation | 원본이 HTTPS 웹 서버인 경우 서버 TLS/SSL 인증서 유효성 검사를 사용할지 여부를 지정합니다. HTTPS 서버에서 자체 서명된 인증서를 사용하는 경우 **false** 로 설정합니다. | 예<br /> (기본값: **true**) |
| gatewayName | 온-프레미스 HTTP 소스에 연결하기 위한 데이터 관리 게이트웨이 인스턴스의 이름입니다. | 온-프레미스 HTTP 소스에서 데이터를 복사하는 경우에는 필수 |
| encryptedCredential | HTTP 엔드포인트 액세스를 위한 암호화된 자격 증명입니다. 이 값은 복사 마법사에서 또는 **ClickOnce** 대화 상자를 사용하여 인증 정보를 구성할 때 자동 생성됩니다. | 예<br /> (온-프레미스 HTTP 서버에서 데이터를 복사하는 경우에만 적용) |

온-프레미스 HTTP 커넥터 데이터 원본의 자격 증명 설정에 대한 자세한 내용은 [Move data between on-premises sources and the cloud by using Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md)(데이터 관리 게이트웨이를 사용하여 온-프레미스 원본과 클라우드 간 데이터 이동)를 참조하세요.

### <a name="using-basic-digest-or-windows-authentication"></a>Basic, Digest 또는 Windows 인증 사용

**authenticationType** 을 **Basic**, **Digest** 또는 **Windows** 로 설정합니다. 이전 섹션에서 설명한 일반 HTTP 커넥터 속성 외에 다음 속성을 설정합니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| userName | HTTP 엔드포인트에 액세스하는 데 사용할 사용자 이름입니다. | 예 |
| password | 사용자(**username**)의 암호입니다. | 예 |

**예: Basic, Digest 또는 Windows 인증 사용**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>ClientCertificate 인증 사용

기본 인증을 사용하려면 **authenticationType** 을 **ClientCertificate** 로 설정합니다. 이전 섹션에서 설명한 일반 HTTP 커넥터 속성 외에 다음 속성을 설정합니다.

| 속성 | Description | 필수 |
| --- | --- | --- |
| embeddedCertData | PFX 파일의 이진 데이터의 Base64 인코딩 콘텐츠입니다. | **embeddedCertData** 또는 **certThumbprint** 지정 |
| certThumbprint | 게이트웨이 컴퓨터의 인증서 저장소에 설치된 인증서의 지문입니다. 온-프레미스 HTTP 소스에서 데이터를 복사하는 경우에만 적용됩니다. | **embeddedCertData** 또는 **certThumbprint** 지정 |
| password | 인증서와 연결된 암호입니다. | 예 |

인증에 **certThumbprint** 를 사용하고 인증서가 로컬 컴퓨터의 개인 저장소에 설치된 경우 게이트웨이 서비스에 읽기 권한을 부여합니다.

1. MMC(Microsoft Management Console)를 엽니다. **로컬 컴퓨터** 를 대상으로 하는 **인증서** 스냅인을 추가합니다.
2. **인증서** > **개인** 을 확장한 후 **인증서** 를 선택합니다.
3. 개별 저장소에서 인증서를 마우스 오른쪽 단추로 클릭한 다음, **모든 작업** >**프라이빗 키 관리** 를 선택합니다.
3. **보안** 탭에서 인증서에 대한 읽기 권한으로 데이터 관리 게이트웨이 호스트 서비스를 실행 중인 사용자 계정을 추가합니다.  

**예제: 클라이언트 인증서 사용**

이 연결된 서비스는 데이터 팩터리를 온-프레미스 HTTP 웹 서버에 연결합니다. 데이터 관리 게이트웨이가 설치된 머신에 설치된 클라이언트 인증서를 사용합니다.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

**예제: 파일로 클라이언트 인증서 사용**

이 연결된 서비스는 데이터 팩터리를 온-프레미스 HTTP 웹 서버에 연결합니다. 데이터 관리 게이트웨이가 설치된 머신에 설치된 클라이언트 인증서 파일을 사용합니다.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

구조, 가용성 및 정책과 같은 데이터 세트 JSON 파일의 일부 섹션은 모든 데이터 세트 형식(예: Azure SQL Database, Azure Blob Storage 및 Azure Table Storage)에 대해 유사합니다.

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md)를 참조하세요.

**typeProperties** 섹션은 데이터 세트의 각 형식마다 다릅니다. **typeProperties** 섹션은 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **Http** 형식의 데이터 세트에 대한 **typeProperties** 섹션에는 다음과 같은 속성이 있습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 을 **Http** 로 설정해야 합니다. | 예 |
| relativeUrl | 데이터를 포함하는 리소스에 대한 상대 URL입니다. 경로를 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. <br><br> 동적 URL을 구성하려면 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md)를 사용할 수 있습니다. 예제: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**. | 예 |
| requestMethod | HTTP 메서드입니다. 허용되는 값은 **GET** 및 **POST** 입니다. | 예 <br />(기본값: **GET**) |
| additionalHeaders | 추가 HTTP 요청 헤더입니다. | 예 |
| requestBody | HTTP 요청의 본문입니다. | 예 |
| format | 데이터를 구문 분석하지 않고 HTTP 엔드포인트에서 데이터를 그대로 검색하려면 **format** 설정을 건너뜁니다. <br><br> 복사 중에 HTTP 응답 내용을 구문 분석하려면 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [JSON 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format)을 참조하세요. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식: **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**. 지원되는 수준: **Optimal** 및 **Fastest**. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |

**예제: GET(기본) 메서드 사용**

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
          "relativeUrl": "XXX/test.xml",
          "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**예제: POST 메서드 사용**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
       "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이름, 설명, 입력 및 출력 테이블, 정책과 같은 속성은 모든 형식의 작업에 사용할 수 있습니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요. 

작업의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 작업 유형에 대해 다릅니다. 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 달라집니다.

현재 복사 작업의 원본이 **HttpSource** 형식인 경우 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
| -------- | ----------- | -------- |
| httpRequestTimeout | HTTP 요청이 응답을 받을 시간 제한(**TimeSpan** 값)입니다. 응답 데이터를 읽는 시간 제한이 아니라, 응답을 받을 시간 제한입니다. | 예<br />(기본값: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>지원되는 파일 및 압축 형식

자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md)을 참조하세요.

## <a name="json-examples"></a>JSON 예

다음 예제에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. HTTP 소스에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 임의의 원본에서 [지원되는](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 싱크로 직접 데이터를 복사할 수 있습니다.

**예제: HTTP 소스에서 Azure Blob Storage로 데이터 복사**

이 샘플에 대한 Data Factory 솔루션은 다음 Data Factory 엔터티를 포함합니다.

*   [HTTP](#linked-service-properties) 형식의 연결된 서비스입니다.
*   [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
*   [Http](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
*   [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
*   [HttpSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업이 포함된 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 1시간마다 HTTP 소스의 데이터를 Azure Blob으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

### <a name="http-linked-service"></a>HTTP 연결된 서비스

이 예제에서는 익명 인증으로 HTTP 연결된 서비스를 사용합니다. 사용할 수 있는 다른 유형의 인증은 [HTTP 연결 서비스](#linked-service-properties)를 참조하세요.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP 데이터 세트

**external** 을 **true** 로 설정하면 데이터 세트가 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실을 Data Factory 서비스에 전달합니다.

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
        "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
    },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure Blob 출력 데이터 세트

데이터는 1시간마다 새 blob에 기록됩니다(**frequency**: **hour**, **interval**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>복사 작업을 사용하는 파이프라인

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 활동을 포함합니다. 복사 작업은 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **HttpSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다.

**HttpSource** 에서 지원하는 속성 목록은 [HttpSource](#copy-activity-properties)를 참조하세요.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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

> [!NOTE]
> 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝

Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 다양한 최적화 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
