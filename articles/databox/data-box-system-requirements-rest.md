---
title: Microsoft Azure Data Box Blob Storage 요구 사항 | Microsoft Docs
description: Azure Data Box Blob Storage의 API, SDK 및 클라이언트 라이브러리에 지원되는 버전을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/05/2020
ms.author: alkohli
ms.openlocfilehash: 4c62c1e854e55b8817724c78bc325badc703bc27
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250661"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Azure Data Box Blob Storage 요구 사항

이 문서에서는 Azure API, Azure 클라이언트 라이브러리 및 Data Box Blob Storage에서 지원되는 도구 버전을 나열합니다. Data Box Blob Storage는 Blob 관리 기능에 Azure 일관성이 있는 의미 체계를 제공합니다. 이 문서에서는 Azure Storage 서비스와 Azure Data Box Blob Storage의 알려진 차이점도 요약합니다.

Data Box Blob Storage에 연결하기 전에 정보를 주의 깊게 검토한 후 필요에 따라 다시 참조하는 것이 좋습니다.


## <a name="storage-differences"></a>스토리지 차이점

|     기능                                             |     Azure Storage                                     |     Data Box Blob Storage |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Azure 파일                                   |    클라우드 기반 SMB 및 NFS 파일 공유 지원됨               |    지원되지 않음      |
|    미사용 데이터에 대한 서비스 암호화                  |    256비트 AES 암호화                             |    256비트 AES 암호화 |
|    스토리지 계정 유형                                 |    범용 및 Azure Blob Storage 계정    |    범용 v1만|
|    Blob 이름                                            |    1,024자(2,048바이트)                     |    880자(1,760바이트)|
|    블록 Blob 최대 크기                              |    4.75TiB(100MB X 50,000개 블록)                   |    Azure Data Box v 3.0 이상의 경우 4.75TiB(100MB x 50,000개 블록)|
|    페이지 Blob 최대 크기                               |    8TiB                                               |    1TiB                   |
|    페이지 Blob 페이지 크기                                  |    512바이트                                          |    4KiB                   |

## <a name="supported-api-versions"></a>지원되는 API 버전

Data Box Blob Storage에서 지원되는 Azure Storage 서비스 API 버전은 다음과 같습니다.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3.0 이상

[!INCLUDE [data-box-rest-supported-api-versions](../../includes/data-box-rest-supported-api-versions.md)]

## <a name="supported-azure-client-libraries"></a>지원되는 Azure 클라이언트 라이브러리

Data Box Blob Storage에는 특정 클라이언트 라이브러리 및 특정 엔드포인트 접미사 요구 사항이 있습니다. Data Box Blob Storage 엔드포인트는 최신 버전의 Azure Blob Storage REST API와 완전히 동일하지는 않습니다. [Azure Data Box 3.0 이상에 지원되는 버전](#supported-api-versions)을 참조하세요. 스토리지 클라이언트 라이브러리의 경우 REST API와 호환되는 버전을 알아야 합니다.

### <a name="azure-data-box-30-onwards"></a>Azure Data Box 3.0 이상

다음 Azure 클라이언트 라이브러리 버전은 Data Box Blob Storage에 지원됩니다.

[!INCLUDE [data-box-rest-supported-azure-client-libraries](../../includes/data-box-rest-supported-azure-client-libraries.md)]

### <a name="install-php-client-via-composer---current"></a>작성기를 통해 PHP 클라이언트 설치 - 현재

작성기를 통해 설치하려면: (Blob을 예로 사용).
1. 다음 코드를 사용하여 프로젝트 루트에 composer.json이라는 파일을 만듭니다.

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. `composer.phar`을 프로젝트 루트에 다운로드합니다.

3. 실행: php composer.phar install.

### <a name="endpoint-declaration"></a>엔드포인트 선언

Data Box Blob Storage SDK에서 엔드포인트 접미사 - `<device serial number>.microsoftdatabox.com` -은 Data Box 도메인을 식별합니다. 해당 Blob 서비스 엔드포인트에 대한 자세한 내용은 [Data Box Blob Storage를 통해 연결](data-box-deploy-copy-data-via-rest.md)을 참조하세요.
 
## <a name="examples"></a>예

### <a name="net"></a>.NET

Data Box Blob Storage의 엔드포인트 접미사는 `app.config` 파일에 지정됩니다.

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Data Box Blob Storage의 엔드포인트 접미사는 연결 문자열 설정에 지정됩니다.

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Data Box Blob Storage의 엔드포인트 접미사는 선언 인스턴스에 지정됩니다.

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Data Box Blob Storage의 엔드포인트 접미사는 연결 문자열 설정에 지정됩니다.

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Data Box Blob Storage의 엔드포인트 접미사는 연결 문자열 설정에 지정됩니다.

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Data Box Blob Storage의 엔드포인트 접미사는 선언 인스턴스에 지정됩니다.

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Data Box Blob Storage의 엔드포인트 접미사는 연결 문자열 설정에 지정됩니다.

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>다음 단계

* [Azure Data Box 배포](data-box-deploy-ordered.md)
