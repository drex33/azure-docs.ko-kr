---
title: Azure Data Factory를 사용하여 FTP 서버에서 데이터 이동
description: Azure Data Factory를 사용하여 FTP 서버에서 데이터를 이동하는 방법에 대해 알아봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 94cb4f6a3429084fe555f1d2a54ab80f7c230a36
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260002"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 FTP 서버에서 데이터 이동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-ftp-connector.md)
> * [버전 2(현재 버전)](../connector-ftp.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 FTP 커넥터](../connector-ftp.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 FTP 서버에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

FTP 서버에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 표를 참조하세요. 현재 데이터 팩터리는 FTP 서버에서 다른 데이터 저장소로의 데이터 이동만 지원하며, 다른 데이터 저장소에서 FTP 서버로의 데이터 이동은 지원하지 않습니다. 온-프레미스 및 클라우드 FTP 서버를 지원합니다.

> [!NOTE]
> 복사 작업 시 원본 파일이 대상에 성공적으로 복사된 후 원본 파일이 삭제되지 않습니다. 성공적 복사 후 원본 파일을 삭제해야 할 경우 파일을 삭제하는 사용자 지정 작업을 만들고 파이프라인에서 해당 작업을 사용합니다.

## <a name="enable-connectivity"></a>연결 설정
**온-프레미스** FTP 서버의 데이터를 클라우드 데이터 저장소(예: Azure Blob Storage)로 이동하는 경우 데이터 관리 게이트웨이를 설치하여 사용합니다. 데이터 관리 게이트웨이는 온-프레미스 컴퓨터에 설치되는 클라이언트 에이전트로, 클라우드 서비스가 온-프레미스 리소스에 연결할 수 있습니다. 자세한 내용은 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요. 게이트웨이 설정 및 사용에 대한 단계별 지침은 [온-프레미스 위치 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요. 서버가 Azure IaaS(Infrastructure as a Service) 가상 머신(VM)인 경우에도 게이트웨이를 사용하여 FTP 서버에 연결합니다.

FTP 서버로 동일한 온-프레미스 컴퓨터 또는 IaaS VM에 게이트웨이를 설치할 수 있습니다. 그러나 리소스 경합을 방지하고 성능 향상을 꾀하려면 게이트웨이를 별도 컴퓨터 또는 IaaS VM에 설치하는 것이 좋습니다. 별도 컴퓨터에 게이트웨이를 설치하는 경우 컴퓨터가 FTP 서버에 액세스할 수 있어야 합니다.

## <a name="get-started"></a>시작
다른 도구 또는 API를 사용하여 FTP 원본의 데이터를 이동하는 복사 작업이 포함된 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **데이터 팩터리 복사 마법사** 를 사용하는 것입니다. 빠른 연습을 위해서는 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요.

또한 **Visual Studio**, **PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API** 를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하거나 API를 사용하는 경우에도 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만듭니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스** 를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트** 를 만듭니다.
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인** 을 만듭니다.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구 또는 API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 데이터 팩터리 엔터티를 정의합니다. FTP 데이터 저장소의 데이터를 복사하는 데 사용되는 데이터 팩터리 엔터티의 JSON 정의에 대한 샘플은 이 문서의 [JSON의 예: FTP 서버에서 Azure Blob으로 데이터 복사](#json-example-copy-data-from-ftp-server-to-azure-blob) 섹션을 참조하세요.

> [!NOTE]
> 지원되는 파일 및 사용할 압축 파일에 대한 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md)을 참조하세요.

다음 섹션에서는 FTP에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 테이블은 FTP 연결 서비스에만 해당하는 JSON 요소에 대해 설명합니다.

| 속성 | 설명 | 필수 | 기본값 |
| --- | --- | --- | --- |
| type |FtpServer로 설정합니다. |예 |&nbsp; |
| host |FTP 서버의 이름 또는 IP 주소를 지정합니다. |예 |&nbsp; |
| authenticationType |인증 유형을 지정합니다. |예 |기본, 익명 |
| 사용자 이름 |FTP 서버에 액세스하는 사용자를 지정합니다. |예 |&nbsp; |
| password |사용자(사용자 이름)의 암호를 지정합니다. |예 |&nbsp; |
| encryptedCredential |FTP 서버 액세스를 위한 암호화된 자격 증명을 지정합니다. |예 |&nbsp; |
| gatewayName |온-프레미스 FTP 서버에 연결하기 위한 데이터 관리 게이트웨이의 게이트웨이 이름을 지정합니다. |예 |&nbsp; |
| 포트 |FTP 서버가 수신 대기하는 포트를 지정합니다. |예 |21 |
| enableSsl |SSL/TLS 채널을 통해 FTP를 사용할지 여부를 지정합니다. |예 |true |
| enableServerCertificateValidation |SSL/TLS 채널을 통해 FTP를 사용할 때 서버 TLS/SSL 인증서 유효성 검사를 사용할지 여부를 지정합니다. |예 |true |

>[!NOTE]
>FTP 커넥터는 암호화 또는 명시적인 SSL/TLS 암호화를 사용하지 않고, FTP 서버에 액세스하도록 지원하며, 암시적인 SSL/TLS 암호화를 지원하지 않습니다.

### <a name="use-anonymous-authentication"></a>익명 인증 사용

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>일반 텍스트에 사용자 이름 및 암호를 기본 인증에 사용

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>포트, enableSsl, enableServerCertificateValidation 사용

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>인증 및 게이트웨이에 encryptedCredential 사용

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md)를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다.

**typeProperties** 섹션은 데이터 세트의 각 형식마다 다릅니다. 데이터 세트 형식에 대한 정보를 제공합니다. **FileShare** 형식의 데이터 세트에 대한 **typeProperties** 섹션에는 다음과 같은 속성이 있습니다.

| 속성 | 설명 | 필수 |
| --- | --- | --- |
| folderPath |폴더의 하위 경로입니다. 문자열의 특수 문자에 이스케이프 문자 ‘\’를 사용합니다. 예제를 살펴보려면 연결된 서비스 및 데이터 세트 정의 샘플을 참조하세요.<br/><br/>이 속성을 **partitionBy** 와 결합하여 조각 시작 및 종료 날짜-시간을 기준으로 폴더 경로를 지정할 수 있습니다. |예 |
| fileName |테이블이 폴더의 특정 파일을 참조하도록 하려는 경우 **folderPath** 에 파일 이름을 지정합니다. 이 속성에 값을 지정하지 않으면 테이블은 폴더에 있는 모든 파일을 가리킵니다.<br/><br/>출력 데이터 세트에 대한 **fileName** 이 지정되지 않은 경우 생성되는 파일의 이름 형식은 다음과 같습니다. <br/><br/>`Data.<Guid>.txt`(예: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |예 |
| fileFilter |모든 파일이 아닌 **folderPath** 의 파일 하위 집합을 선택하는데 사용할 필터를 지정합니다.<br/><br/>허용 되는 값은 `*`(여러 문자) 및 `?`(하나의 문자)입니다.<br/><br/>예 1: `"fileFilter": "*.log"`<br/>예 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** 는 FileShare 입력 데이터 세트에 적용할 수 있습니다. 이 속성은 HDFS(Hadoop Distributed File System)에서 지원되지 않습니다. |예 |
| partitionedBy |동적 **folderPath** 및 시계열 데이터에 대한 **filename** 을 지정하는 데 사용합니다. 예를 들어 매 시간 데이터에 대해 매개 변수가 있는 **folderPath** 를 지정할 수 있습니다. |예 |
| format | **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat** 과 같은 서식 유형이 지원됩니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](data-factory-supported-file-and-compression-formats.md#text-format), [Json 형식](data-factory-supported-file-and-compression-formats.md#json-format), [Avro 형식](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc 형식](data-factory-supported-file-and-compression-formats.md#orc-format) 및 [Parquet 형식](data-factory-supported-file-and-compression-formats.md#parquet-format) 섹션을 참조하세요. <br><br> 파일 기반 저장소(이진 복사) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의에서 형식 섹션을 건너뜁니다. |예 |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 지원되는 형식은 **GZip**, **Deflate**, **BZip2**, **ZipDeflate** 이고 지원되는 수준은 **최적** 및 **가장 빠름** 입니다. 자세한 내용은 [Azure Data Factory의 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md#compression-support)을 참조하세요. |예 |
| useBinaryTransfer |이진 전송 모드를 사용할지 여부를 지정합니다. 값은 이진 모드(기본값)에서만 true이며 ASCII에서는 false입니다. 이 속성은 연결된 서비스 유형이 FtpServer인 경우에만 사용할 수 있습니다. |예 |

> [!NOTE]
> **filename** 및 **fileFilter** 는 동시에 사용할 수 없습니다.

### <a name="use-the-partionedby-property"></a>partionedBy 속성 사용
이전 섹션에서 설명한 대로 **partitionedBy** 속성을 사용하여 시계열 데이터의 동적 **folderPath** 와 **fileName** 을 지정할 수 있습니다.

시계열 데이터 세트, 예약 및 조각에 대한 자세한 내용은 [데이터 세트 만들기](data-factory-create-datasets.md), [일정 예약 및 실행](data-factory-scheduling-and-execution.md) 및 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요.

#### <a name="sample-1"></a>샘플 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
이 예제에서 {Slice}는 지정된 형식(YYYYMMDDHH)의 데이터 팩터리 시스템 변수 SliceStart 값으로 대체됩니다. SliceStart는 조각의 시작 시간을 가리킵니다. 폴더 경로는 각 조각에 따라 다릅니다. (예를 들어 wikidatagateway/wikisampledataout/2014100103 또는 wikidatagateway/wikisampledataout/2014100104입니다.)

#### <a name="sample-2"></a>샘플 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
이 예제에서 SliceStart의 연도, 월, 일 및 시간은 **folderPath** 및 **fileName** 속성에서 사용하는 별도 변수로 추출됩니다.

## <a name="copy-activity-properties"></a>복사 작업 속성
작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 작업의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 작업 유형에 따라 다릅니다. 복사 작업의 경우 유형 속성은 원본 및 싱크의 형식에 따라 달라집니다.

원본이 **FileSystemSource** 형식인 복사 작업의 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| recursive |하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. |True, False(기본값) |예 |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON 예: FTP 서버에서 Azure Blob으로 데이터 복사
이 샘플은 FTP 서버에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여줍니다. 하지만 데이터 팩터리의 복사 작업을 사용하여 [지원되는 데이터 저장소 및 형식](data-factory-data-movement-activities.md#supported-data-stores-and-formats)에 지정된 싱크로 데이터를 직접 복사할 수 있습니다.

다음 예제에서는 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 통해 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다.

* [FtpServer](#linked-service-properties) 형식의 연결된 서비스
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)형식의 연결된 서비스
* [FileShare](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [FileSystemSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 1시간마다 FTP 서버의 데이터를 Azure Blob으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

### <a name="ftp-linked-service"></a>FTP 연결 서비스

이 예에서는 일반 텍스트의 사용자 이름과 암호를 기본 인증으로 사용합니다. 다음 방법 중 하나를 사용할 수도 있습니다.

* 익명 인증
* 암호화된 자격 증명으로 기본 인증
* SSL/TLS 경우 FTP(FTPS)

사용할 수 있는 다른 유형의 인증은 [FTP 연결 서비스](#linked-service-properties) 섹션을 참조하세요.

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스

```JSON
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
### <a name="ftp-input-dataset"></a>FTP 입력 데이터 세트

이 데이터 세트는 FTP 폴더 `mysharedfolder`와 `test.csv` 파일을 참조합니다. 파이프라인은 파일을 대상에 복사합니다.

**external** 을 **true** 로 설정하면 데이터 세트가 데이터 팩터리의 외부에 있으며 데이터 팩터리의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob 출력 데이터 세트

데이터는 매시간 새 blob에 기록됩니다.(빈도: 1시간, 간격:1회) Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간의 년, 월, 일 및 시 부분을 사용합니다.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>파일 시스템 원본 및 Blob 싱크를 사용하는 파이프라인의 복사 작업

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **FileSystemSource** 로 설정되고 **sink** 형식은 **BlobSink** 로 설정됩니다.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

* Data Factory에서 데이터 이동(복사 작업) 성능에 영향을 미치는 주요 요소 및 다양한 최적화 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.

* 복사 작업이 포함된 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
