---
title: 연결 문자열 구성
titleSuffix: Azure Storage
description: Azure Storage 계정에 대한연결 문자열을 구성합니다. 연결 문자열에는 공유 키 인증을 사용하여 런타임 시 애플리케이션에서 스토리지 계정에 액세스 권한을 부여하는 데 필요한 정보가 포함되어 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 435f5376a0a84cf2d9d706e4391142814b0f0141
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593081"
---
# <a name="configure-azure-storage-connection-strings"></a>Azure Storage 연결 문자열 구성

연결 문자열에는 애플리케이션이 공유 키 인증을 사용하여 런타임에 Azure Storage 계정의 데이터에 액세스하는 데 필요한 인증 정보가 포함되어 있습니다. 다음과 같은 작업을 수행하도록 연결 문자열을 구성할 수 있습니다.

- Azurite 스토리지 에뮬레이터에 연결합니다.
- Azure의 스토리지 계정에 액세스
- SAS(공유 액세스 서명)를 통해 Azure의 지정된 리소스에 액세스

계정 액세스 키를 보고 연결 문자열을 복사하는 방법을 알아보려면 [스토리지 계정 액세스 키 관리](storage-account-keys-manage.md)를 참조하세요.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="store-a-connection-string"></a>연결 문자열 제공

Azure Storage에 대해 만들어진 요청 권한을 부여하려면 런타임에 애플리케이션이 연결 문자열에 액세스해야 합니다. 연결 문자열을 저장하기 위한 여러 가지 옵션이 있습니다.

- 환경 변수에 연결 문자열을 저장할 수 있습니다.
- 데스크톱 또는 디바이스에서 실행 중인 애플리케이션의 경우 연결 문자열을 **app.config** 또는 **web.config** 파일에 저장할 수 있습니다. 이러한 파일의 **AppSettings** 섹션에 연결 문자열을 추가합니다.
- Azure 클라우드 서비스에서 실행 중인 애플리케이션의 경우, 연결 문자열을 [Azure 서비스 구성 스키마(.cscfg) 파일](/previous-versions/azure/reference/ee758710(v=azure.100))에 저장할 수 있습니다. 연결 문자열을 서비스 구성 파일의 **ConfigurationSettings** 섹션에 추가합니다.

사용자의 연결 문자열을 구성 파일에 저장하면 연결 문자열을 업데이트하여 [Azurite 스토리지 에뮬레이터](../common/storage-use-azurite.md)와 클라우드의 Azure Storage 계정 사이에 전환하기 쉽습니다. 대상 환경을 가리키도록 연결 문자열을 편집하기만 하면 됩니다.

[Microsoft Azure 구성 관리자](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)를 사용하여 애플리케이션이 실행 중인 위치와 상관없이 런타임에 사용자의 연결 문자열에 액세스할 수 있습니다.

## <a name="configure-a-connection-string-for-azurite"></a>Azurite용 연결 문자열 구성

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Azurite에 대한 자세한 내용은 [로컬 Azure Storage 개발에 Azurite 에뮬레이터 사용](../common/storage-use-azurite.md)을 참조하세요.

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Azure Storage 계정에 대한 연결 문자열 구성

Azure Storage 계정에 연결 문자열을 만들려면 다음 형식을 사용 합니다. HTTPS(권장) 또는 HTTP를 통해 스토리지 계정에 연결할지 여부를 나타내며, `myAccountName`을 스토리지 계정의 이름으로 바꾸고, `myAccountKey`를 계정 액세스 키로 바꿉니다.

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

예를 들어 연결 문자열은 다음과 유사할 수 있습니다.

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Azure Storage는 연결 문자열에서 HTTP 및 HTTPS를 모두 지원하지만 *HTTPS를 사용하는 것이 좋습니다.*

> [!TIP]
> 스토리지 계정의 연결 문자열은 [Azure Portal](https://portal.azure.com)에서 찾을 수 있습니다. 스토리지 계정의 메뉴 블레이드에서 **설정** > **액세스 키** 로 이동하여 주 및 보조 액세스 키에 대한 연결 문자열을 모두 확인합니다.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>공유 액세스 서명을 사용하여 연결 문자열 만들기

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>명시적 스토리지 엔드포인트에 대한 연결 문자열 만들기

기본 엔드포인트를 사용하는 대신 연결 문자열에서 명시적 서비스 엔드포인트를 지정할 수 있습니다. 명시적 Blob 엔드포인트를 지정하는 연결 문자열을 만들려면 다음 형식으로 프로토콜 사양(HTTPS(권장) 또는 HTTP)을 포함하는 전체 서비스 엔드포인트를 각 서비스에 대해 지정합니다.

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

명시적 엔드포인트를 지정하고자 할 수 있는 하나의 시나리오로서 Blob Storage 엔드포인트를 [사용자 지정 도메인](../blobs/storage-custom-domain-name.md)에 매핑한 경우가 있습니다. 이 경우 연결 문자열에 있는 Blob Storage에 대해 사용자 지정 엔드포인트를 지정할 수 있습니다. 애플리케이션에서 다른 서비스에 대한 기본 엔드포인트를 사용하는 경우 선택적으로 지정할 수 있습니다.

다음은 Blob service에 대한 명시적 엔드포인트를 지정하는 연결 문자열의 예제입니다.

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

이 예제에서는 Blob service에 대한 사용자 지정 도메인을 비롯하여 모든 서비스에 대해 명시적 엔드포인트를 지정합니다.

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

연결 문자열에 있는 엔드포인트 값은 스토리지 서비스의 요청 URI를 생성하는 데 사용되며 코드로 반환되는 URI의 형식을 지정합니다.

스토리지 엔드포인트를 사용자 지정 도메인에 매핑하고 연결 문자열에서 엔드포인트를 생략하면 해당 연결 문자열을 사용할 수 없어서 코드로부터 해당 서비스의 데이터에 액세스할 수 없습니다.

Azure Storage에 대한 사용자 지정 도메인을 구성하는 방법에 대한 자세한 내용은 [사용자 지정 도메인을 Azure Blob Storage 엔드포인트에 매핑](../blobs/storage-custom-domain-name.md)을 참조하세요.

> [!IMPORTANT]
> 연결 문자열의 서비스 엔드포인트 값은 `https://`(권장) 또는 `http://`를 포함하는 올바른 형식의 URI여야 합니다.

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>엔드포인트 접미사를 사용하여 연결 문자열 만들기

Azure 중국 21Vianet 또는 Azure Government와 같이 다른 엔드포인트 접미사를 사용하여 지역이나 인스턴스에 스토리지 서비스에 대한 연결 문자열을 만들려면 다음 연결 문자열 형식을 사용합니다. HTTPS(권장) 또는 HTTP를 통해 스토리지 계정에 연결할지 여부를 표시하며, `myAccountName`을 스토리지 계정의 이름으로 바꾸고, `myAccountKey`를 계정 액세스 키로 바꾸고, `mySuffix`을 URI 접미사로 바꿉니다.

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

다음은 Azure 중국 21Vianet에서 스토리지 서비스에 대한 연결 문자열에 대한 예입니다.

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>연결 문자열 구문 분석

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>다음 단계

- [로컬 Azure Storage 개발에 Azurite 에뮬레이터 사용](../common/storage-use-azurite.md)
- [Azure Storage Explorer](storage-explorers.md)
- [SAS(공유 액세스 서명) 사용](storage-sas-overview.md)
