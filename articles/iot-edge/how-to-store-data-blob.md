---
title: 디바이스에 블록 Blob 저장 - Azure IoT Edge | Microsoft Docs
description: 계층화 및 Time-to-Live 기능을 이해하고, 지원되는 Blob 스토리지 작업을 확인하고, Blob 스토리지 계정에 연결합니다.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d280570560b4d2bb991d829321764ce461bb209e
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133357870"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>IoT Edge에서 Azure Blob Storage를 사용하여 에지에 데이터 저장

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Blob Storage on IoT Edge는 에지에서 [블록 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 및 [추가 Blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 스토리지 솔루션을 제공합니다. Blob이 IoT Edge 디바이스에 로컬로 저장된다는 점을 제외하고는 IoT Edge 디바이스의 Blob 스토리지 모듈은 Azure Blob 서비스처럼 작동합니다. 이미 익숙한 것과 동일한 Azure 스토리지 SDK 메서드 또는 Blob API 호출을 사용하여 Blob에 액세스할 수 있습니다. 이 문서에서는 IoT Edge 디바이스에서 Blob 서비스를 실행하는 Azure Blob Storage on IoT Edge 컨테이너와 관련된 개념에 대해 설명합니다.

이 모듈이 유용한 시나리오는 다음과 같습니다.

* 데이터를 처리하거나 클라우드로 전송할 수 있을 때까지 로컬로 저장해야 하는 경우. 이 데이터는 비디오, 이미지, 재무 데이터, 병원 데이터 또는 기타 비정형 데이터일 수 있습니다.
* 연결이 제한되는 위치에 디바이스가 있는 경우
* 최대한 빠르게 긴급 상황에 대응할 수 있도록 데이터를 로컬로 효율적으로 처리하여 데이터에 대한 짧은 대기 시간 액세스를 갖추려는 경우
* 대역폭 비용을 줄이고 테라바이트 단위의 데이터를 클라우드로 전송하지 않도록 방지하려는 경우. 데이터를 로컬로 처리하고 처리된 데이터만 클라우드에 보낼 수 있습니다.

빠른 소개를 위한 비디오 시청
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

이 모듈은 **deviceToCloudUpload** 및 **deviceAutoDelete** 기능과 함께 제공됩니다.

**deviceToCloudUpload** 는 구성 가능한 기능입니다. 이 기능은 일시적인 인터넷 연결 지원을 사용하여 데이터를 로컬 Blob 스토리지에서 Azure로 자동으로 업로드합니다. 다음을 수행할 수 있습니다.

* deviceToCloudUpload 기능을 설정/해제합니다.
* NewestFirst 또는 OldestFirst와 같이 데이터를 Azure에 복사하는 순서를 선택합니다.
* 데이터를 업로드하려는 Azure Storage 계정을 지정합니다.
* Azure에 업로드하려는 컨테이너를 지정합니다. 이 모듈을 사용하면 원본 및 대상 컨테이너 이름을 모두 지정할 수 있습니다.
* 클라우드 스토리지에 대한 업로드가 완료되는 즉시 Blob을 삭제할 수 있는 기능을 선택합니다.
* 전체 Blob 업로드(`Put Blob` 작업 사용) 및 블록 수준 업로드(Put `Put Block`, `Put Block List` 및 `Append Block` 작업 사용)를 수행합니다.

Blob이 블록으로 구성된 경우 이 모듈은 블록 수준 업로드를 사용합니다. 일반적인 몇 가지 시나리오는 다음과 같습니다.

* 애플리케이션에서 이전에 업로드한 블록 Blob의 일부 블록을 업데이트하거나 새 블록을 추가 Blob에 추가합니다. 이 모듈은 전체 Blob이 아닌 업데이트된 블록만 업로드합니다.
* 모듈에서 Blob을 업로드하는 중이고 인터넷 연결이 끊어집니다. 다시 연결되면 전체 Blob이 아닌 나머지 블록만 업로드합니다.

Blob 업로드 중에 예기치 않은 프로세스 종료(예: 정전)가 발생하는 경우 모듈이 다시 온라인 상태가 되면 업로드할 예정이었던 모든 블록을 다시 업로드합니다.

**deviceAutoDelete** 는 구성 가능한 기능입니다. 지정된 기간(분 단위로 측정)이 만료되면 이 기능은 로컬 스토리지에서 Blob을 자동으로 삭제합니다. 다음을 수행할 수 있습니다.

* deviceAutoDelete 기능을 설정/해제합니다.
* Blob이 자동으로 삭제될 때까지 경과하는 시간(deleteAfterMinutes)을 분 단위로 지정합니다.
* deleteAfterMinutes 값이 만료되면 업로드하는 동안 Blob을 유지할 수 있는 기능을 선택합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure IoT Edge 디바이스:

* [Linux](quickstart-linux.md) 또는 [Windows 디바이스](quickstart.md)용 빠른 시작의 단계에 따라 개발 머신 또는 가상 머신을 IoT Edge 디바이스로 사용할 수 있습니다.

* 지원되는 운영 체제 및 아키텍처 목록은 [Azure IoT Edge 지원 시스템](support.md#operating-systems)을 참조하세요. Azure Blob Storage on IoT Edge에서 지원하는 아키텍처는 다음과 같습니다.
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64(미리 보기)

클라우드 리소스:

Azure의 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 및 deviceAutoDelete 속성

모듈의 desired 속성을 사용하여 **deviceToCloudUploadProperties** 및 **deviceAutoDeleteProperties** 를 설정합니다. desired 속성은 배포 중에 설정하거나 나중에 다시 배포할 필요 없이 모듈 쌍을 편집하여 변경할 수 있습니다. 값이 올바르게 전파되도록 `reported configuration` 및 `configurationValidation`에 대한 "모듈 쌍"을 확인하는 것이 좋습니다.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

이 설정의 이름은 `deviceToCloudUploadProperties`입니다. IoT Edge 시뮬레이터를 사용하는 경우 값을 설명 섹션에서 확인할 수 있는 이러한 속성의 관련 환경 변수로 설정합니다.

| 속성 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| uploadOn | true, false | 기본적으로 `false`로 설정됩니다. 기능을 설정하려면 이 필드를 `true`로 설정합니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | 데이터를 Azure에 복사하는 순서를 선택할 수 있습니다. 기본적으로 `OldestFirst`로 설정됩니다. 순서는 Blob을 마지막으로 수정한 시간에 따라 결정됩니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`는 데이터를 업로드할 스토리지 계정을 지정할 수 있는 연결 문자열입니다. `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`를 지정합니다. 데이터가 업로드될 Azure의 적절한 EndpointSuffix를 추가합니다. 이는 글로벌 Azure, 정부 Azure 및 Microsoft Azure Stack에 따라 데이터가 달라집니다. <br><br> 여기서 Azure Storage SAS 연결 문자열을 지정하도록 선택할 수 있습니다. 그러나 이 속성이 만료되면 업데이트해야 합니다. SAS 권한에는 컨테이너에 대한 만들기 액세스 권한과 Blob에 대한 만들기, 쓰기 및 추가 액세스 권한이 포함될 수 있습니다.  <br><br> 환경 변수: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure에 업로드하려는 컨테이너 이름을 지정할 수 있습니다. 이 모듈을 사용하면 원본 및 대상 컨테이너 이름을 모두 지정할 수 있습니다. 대상 컨테이너 이름을 지정하지 않으면 컨테이너 이름이 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`으로 자동으로 할당됩니다. 대상 컨테이너 이름에 대한 템플릿 문자열을 만들고, 가능한 값 열을 체크 아웃할 수 있습니다. <br>* %h -> IoT Hub 이름(3~50자) <br>* %d -> IoT Edge 디바이스 ID(1~129자) <br>* %m -> 모듈 이름(1~64자) <br>* %c -> 원본 컨테이너 이름(3~63자) <br><br>컨테이너 이름의 최대 크기는 63자이지만, 컨테이너 크기가 63자를 초과하는 경우 대상 컨테이너 이름이 자동으로 할당되어 각 섹션(IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName)이 15자로 잘립니다. <br><br> 환경 변수: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true, false | 기본적으로 `false`로 설정됩니다. `true`로 설정하면 클라우드 스토리지에 대한 업로드를 완료할 때 데이터가 자동으로 삭제됩니다. <br><br> **주의**: 추가 Blob을 사용하는 경우 이 설정은 업로드가 성공한 후 로컬 스토리지에서 추가 Blob을 삭제하고 해당 Blob에 대한 이후 블록 추가 작업이 실패합니다. 이 설정은 신중하게 사용해야 합니다. 애플리케이션에서 추가 작업을 자주 수행하지 않거나 연속 추가 작업을 지원하지 않는 경우 이 설정을 사용하도록 설정하지 마세요.<br><br> 환경 변수: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

이 설정의 이름은 `deviceAutoDeleteProperties`입니다. IoT Edge 시뮬레이터를 사용하는 경우 값을 설명 섹션에서 확인할 수 있는 이러한 속성의 관련 환경 변수로 설정합니다.

| 속성 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| deleteOn | true, false | 기본적으로 `false`로 설정됩니다. 기능을 설정하려면 이 필드를 `true`로 설정합니다. <br><br> 환경 변수: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 시간을 분 단위로 지정합니다. 이 값이 만료되면 모듈에서 로컬 스토리지로부터 Blob을 자동으로 삭제합니다. 현재 허용되는 최대 시간(분)은 35791입니다. <br><br> 환경 변수: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | 기본적으로 `true`로 설정되며, deleteAfterMinutes가 만료되는 경우 클라우드 스토리지에 업로드하는 동안 Blob이 유지됩니다. `false`로 설정할 수 있으며, deleteAfterMinutes가 만료되는 즉시 데이터가 삭제됩니다. 참고: 이 속성이 작동하려면 uploadOn을 true로 설정해야 합니다.  <br><br> **주의**: 추가 Blob을 사용하는 경우 이 설정은 값이 만료될 때 로컬 스토리지에서 추가 Blob을 삭제하고 해당 Blob에 대한 이후 블록 추가 작업이 실패합니다. 프로그램에서 수행하는 추가 작업의 예상 빈도에 맞게 만료 값이 충분히 큰지 확인하는 것이 좋습니다.<br><br> 환경 변수: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>SMB 공유를 로컬 스토리지로 사용

이 모듈의 Windows 컨테이너를 Windows 호스트에 배포하는 경우 SMB 공유를 로컬 스토리지 경로로 제공할 수 있습니다.

SMB 공유 및 IoT 디바이스가 상호 트러스트된 도메인에 있는지 확인합니다.

`New-SmbGlobalMapping` PowerShell 명령을 실행하여 Windows를 실행하는 IoT 디바이스에서 SMB 공유를 로컬로 매핑할 수 있습니다.

구성 단계는 다음과 같습니다.

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

예:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

이 명령은 자격 증명을 사용하여 원격 SMB 서버에 인증합니다. 그런 다음 원격 공유 경로를 G: 드라이브 문자에 매핑합니다(사용 가능한 다른 모든 드라이브 문자일 수 있음). 이제 IoT 디바이스에는 G: 드라이브의 경로에 매핑된 데이터 볼륨이 있습니다.

IoT 디바이스의 사용자가 원격 SMB 공유에 대한 읽기/쓰기 작업을 수행할 수 있는지 확인합니다.

배포의 경우 `<storage mount>` 값은 **G:/ContainerData:C:/BlobRoot** 일 수 있습니다.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Linux에서 컨테이너 사용자에게 디렉터리 액세스 권한 부여

Linux 컨테이너에 대한 만들기 옵션에서 [볼륨 탑재](https://docs.docker.com/storage/volumes/)를 스토리지에 사용한 경우 추가 단계를 수행할 필요가 없지만, [바인딩 탑재](https://docs.docker.com/storage/bind-mounts/)를 사용한 경우 이러한 단계가 서비스를 올바르게 실행하는 데 필요합니다.

사용자의 액세스 권한을 작업 수행에 필요한 최소 권한으로 제한하는 최소 권한 원칙에 따라 이 모듈에는 사용자(이름: absie, ID: 11000) 및 사용자 그룹(이름: absie, ID: 11000)이 포함됩니다. 컨테이너가 **root**(기본 사용자가 **root** 임)로 시작되는 경우 서비스는 낮은 권한의 **absie** 사용자로 시작됩니다.

이 동작은 서비스가 올바르게 작동하는 데 중요한 호스트 경로 바인딩에 대한 권한을 구성합니다. 그렇지 않으면 서비스가 액세스 거부 오류로 인해 중단됩니다. 디렉터리 바인딩에 사용되는 경로는 컨테이너 사용자(예: absie 11000)가 액세스할 수 있어야 합니다. 호스트에서 아래 명령을 실행하여 컨테이너 사용자에게 디렉터리에 대한 액세스 권한을 부여할 수 있습니다.

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

예:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

**absie** 이외의 사용자 권한으로 서비스를 실행해야 하는 경우 배포 매니페스트의 "User" 속성 아래에 있는 createOptions에서 사용자 지정 사용자 ID를 지정할 수 있습니다. 이 경우 기본 그룹 ID 또는 루트 그룹 ID(`0`)를 사용해야 합니다.

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

이제 컨테이너 사용자에게 디렉터리에 대한 액세스 권한을 부여합니다.

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>로그 파일 구성

모듈에 대한 로그 파일을 구성하는 방법에 대한 자세한 내용은 이러한 [프로덕션 모범 사례](./production-checklist.md#set-up-logs-and-diagnostics)를 참조하세요.

## <a name="connect-to-your-blob-storage-module"></a>Blob Storage 모듈에 연결

모듈에 대해 구성한 계정 이름 및 계정 키를 사용하여 IoT Edge 디바이스의 Blob Storage에 액세스할 수 있습니다.

만드는 스토리지 요청의 Blob 엔드포인트로 IoT Edge 디바이스를 지정합니다. 구성한 IoT Edge 디바이스 정보 및 계정 이름을 사용하여 [명시적 스토리지 엔드포인트에 대한 연결 문자열을 만들 수 있습니다](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint).

* Azure Blob Storage on IoT Edge가 실행되는 것과 동일한 디바이스에 배포된 모듈의 경우 Blob 엔드포인트는 `http://<module name>:11002/<account name>`입니다.
* 다른 디바이스에서 실행되는 모듈 또는 애플리케이션의 경우 네트워크에 적합한 엔드포인트를 선택해야 합니다. 네트워크 설정에 따라 외부 모듈 또는 애플리케이션의 데이터 트래픽이 Azure Blob Storage on IoT Edge를 실행하는 디바이스에 도달할 수 있도록 엔드포인트 형식을 선택합니다. 이 시나리오에 대한 Blob 엔드포인트는 다음 중 하나입니다.
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > Azure IoT Edge는 모듈을 호출할 때 대/소문자를 구분하며, Storage SDK도 기본적으로 소문자로 설정됩니다. [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)의 모듈 이름은 **AzureBlobStorageonIoTEdge** 이지만, 이름을 소문자로 변경하면 Azure Blob Storage on IoT Edge에 대한 연결이 중단되지 않도록 하는 데 도움이 됩니다.
 
## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage 빠른 시작 샘플

Azure Blob Storage 설명서에는 여러 언어로 작성된 빠른 시작 샘플 코드가 포함되어 있습니다. 로컬 Blob 스토리지 모듈에 연결하도록 Blob 엔드포인트를 변경하여 이러한 샘플을 실행하여 Azure Blob Storage on IoT Edge를 테스트할 수 있습니다.

다음 빠른 시작 샘플에서는 IoT Edge에서도 지원하는 언어를 사용하므로 이를 Blob 스토리지 모듈과 함께 IoT Edge 모듈로 배포할 수 있습니다.

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
  * Azure Blob Storage on IoT Edge 모듈 v1.4.0 이하는 WindowsAzure.Storage 9.3.3 SDK와 호환되며, v1.4.1은 Azure.Storage.Blobs 12.8.0 SDK도 지원합니다.
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Python SDK V2.1 이전 버전에는 모듈에서 Blob을 만든 시간을 반환하지 않는 알려진 문제가 있습니다. 이 문제로 인해 Blob 나열과 같은 일부 메서드가 작동하지 않습니다. 해결 방법으로, Blob 클라이언트의 API 버전을 '2017-04-17'로 명시적으로 설정합니다. 예제: `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [추가 Blob 샘플](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Azure Storage Explorer를 사용하여 로컬 스토리지에 연결

[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 로컬 스토리지 계정에 연결할 수 있습니다.

1. Azure Storage Explorer 다운로드 및 설치

1. 연결 문자열을 사용하여 Azure Storage에 연결합니다.

1. 연결 문자열을 제공합니다(`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`).

1. 연결 단계를 진행합니다.

1. 컨테이너를 로컬 스토리지 계정 내에 만듭니다.

1. 블록 Blob 또는 추가 Blob으로 파일 업로드를 시작합니다.
   > [!NOTE]
   > 이 모듈은 페이지 Blob을 지원하지 않습니다.

1. Storage Explorer에서도 Azure 스토리지 계정을 연결하도록 선택할 수 있습니다. 이 구성은 로컬 스토리지 계정과 Azure 스토리지 계정 모두에 대한 단일 보기를 제공합니다.

## <a name="supported-storage-operations"></a>지원되는 스토리지 작업

IoT Edge의 Blob 스토리지 모듈은 Azure Storage SDK를 사용하며, 블록 Blob 엔드포인트용 Azure Storage API 버전 2017-04-17과 일치합니다.

모든 Azure Blob Storage 작업은 Azure Blob Storage on IoT Edge에서 지원되지 않으므로 이 섹션에는 각 작업의 상태가 나와 있습니다.

### <a name="account"></a>계정

지원됨:

* 컨테이너 나열

지원되지 않음:

* Blob 서비스 속성을 가져오고 설정
* 실행 전 Blob 요청
* Blob 서비스 통계 가져오기
* 계정 정보 가져오기

### <a name="containers"></a>컨테이너

지원됨:

* 컨테이너 만들기 및 삭제
* 컨테이너 속성 및 메타데이터 가져오기
* Blob 나열
* 컨테이너 ACL 가져오기 및 설정
* 컨테이너 메타데이터 설정

지원되지 않음:

* 컨테이너 임대

### <a name="blobs"></a>Blob

지원됨:

* Blob 배치, 가져오기 및 삭제
* Blob 속성 가져오기 및 설정
* Blob 메타데이터 가져오기 및 설정

지원되지 않음:

* Blob 임대
* Blob 스냅샷
* Blob 복사 및 중단
* Blob 삭제 취소
* Blob 계층 설정

### <a name="block-blobs"></a>블록 Blob

지원됨:

* 블록 배치
* 블록 목록 배치 및 가져오기

지원되지 않음:

* URL에서 블록 배치

### <a name="append-blobs"></a>추가 Blob

지원됨:

* 블록 추가

지원되지 않음:

* URL에서 블록 추가

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid on IoT Edge 통합

> [!CAUTION]
> Event Grid on IoT Edge와의 통합은 미리 보기에 있습니다.

이 Azure Blob Storage on IoT Edge 모듈은 이제 Event Grid on IoT Edge와의 통합을 제공합니다. 이 통합에 대한 자세한 내용은 [모듈 배포, 이벤트 게시 및 이벤트 전송 확인 자습서](../event-grid/edge/react-blob-storage-events-locally.md)를 참조하세요.

## <a name="release-notes"></a>릴리스 정보

이 모듈에 대한 [docker 허브의 릴리스 정보](https://hub.docker.com/_/microsoft-azure-blob-storage)는 다음과 같습니다. 특정 버전의 릴리스 정보에서 버그 수정 및 업데이트 관리와 관련된 추가 정보를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Blob Storage on IoT Edge를 배포하는 방법](how-to-deploy-blob.md)을 알아봅니다.

[Azure Blob Storage on IoT Edge 블로그](https://aka.ms/abs-iot-blogpost)의 최신 업데이트 및 공지를 최신 상태로 유지합니다.
