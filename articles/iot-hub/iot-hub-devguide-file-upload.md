---
title: Azure IoT Hub 파일 업로드 이해 | Microsoft Docs
description: 개발자 가이드 - IoT Hub의 파일 업로드 기능을 사용하여 디바이스에서 Azure Storage blob 컨테이너로 파일 업로드를 관리합니다.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/25/2021
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 36ecdb4fc1442447e1f0e80019db6039556e75e4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718209"
---
# <a name="upload-files-with-iot-hub"></a>IoT Hub를 사용하여 파일 업로드

IoT Hub 수락하는 비교적 작은 디바이스-클라우드 메시지에 디바이스 데이터를 쉽게 매핑할 수 없는 많은 시나리오가 있습니다. 예를 들어 대용량 미디어 파일을 보내거나 간헐적으로 연결된 디바이스에서 업로드하거나 대역폭을 절약하기 위해 집계 및 압축된 대량 원격 분석 일괄 처리를 보냅니다.

디바이스에서 대용량 파일을 업로드해야 하는 경우에도 IoT Hub 보안 및 안정성을 계속 사용할 수 있습니다. 그러나 자체 메시지를 조정하는 대신 IoT Hub 연결된 Azure Storage 계정에 대한 디스패처 역할을 합니다. IoT Hub 디바이스가 파일 업로드를 완료할 때 백 엔드 서비스에 알림을 제공할 수도 있습니다.

reported 속성, 디바이스-클라우드 메시지 또는 파일 업로드를 사용할 시기를 결정하는 데 도움이 필요한 경우 [디바이스-클라우드 통신 지침을 참조하세요.](iot-hub-devguide-d2c-guidance.md)

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="file-upload-overview"></a>파일 업로드 개요

IoT Hub는 허브로 미리 구성된 Blob 컨테이너 및 Azure Storage 계정에 업로드당 SAS(공유 액세스 서명) URI를 제공하여 연결된 디바이스에서 파일 업로드를 용이하게 합니다. IoT Hub 파일 업로드를 사용하는 데는 IoT Hub에서 Azure Storage 계정 및 Blob 컨테이너를 미리 구성하고, 디바이스에서 파일을 업로드하고, 필요에 따라 백 엔드 서비스에 완료된 파일 업로드를 알리는 세 가지 부분이 있습니다.

파일 업로드 기능을 사용하려면 먼저 Azure [Storage 계정](../storage/common/storage-account-overview.md) 및 [Blob 컨테이너를](../storage/blobs/storage-blobs-introduction.md) IoT Hub와 연결해야 합니다. Azure Storage를 사용하여 IoT Hub 인증하는 방법, IoT Hub가 디바이스에 배포하는 SAS UR의 TTL(Time to Live) 및 백 엔드 서비스에 파일 업로드 알림을 제어하는 설정을 구성할 수도 있습니다. 자세한 내용은 [Azure Storage 계정을 IoT Hub 참조하세요.](#associate-an-azure-storage-account-with-iot-hub)

디바이스는 3단계 프로세스를 따라 연결된 Blob 컨테이너에 파일을 업로드합니다.

1. 디바이스는 IoT Hub를 통해 파일 업로드를 시작합니다. 요청에서 Blob의 이름을 전달하고 SAS URI 및 상관 관계 ID를 반환합니다. SAS URI에는 Blob 컨테이너의 요청된 Blob에 대한 디바이스 읽기-쓰기 권한을 부여하는 Azure Storage에 대한 SAS 토큰이 포함되어 있습니다. 자세한 내용은 [디바이스: 파일 업로드 초기화를 참조하세요.](#device-initialize-a-file-upload)

1. 디바이스는 SAS URI를 사용하여 Azure Blob Storage API를 안전하게 호출하여 Blob 컨테이너에 파일을 업로드합니다. 자세한 내용은 [디바이스: Azure Storage API를 사용하여 파일 업로드 참조하세요.](#device-upload-file-using-azure-storage-apis)

1. 파일 업로드가 완료되면 디바이스는 업로드를 시작할 때 IoT Hub 받은 상관 관계 ID를 사용하여 IoT Hub에 완료 상태를 알리고, 자세한 내용은 [디바이스: 완료된 파일 업로드 IoT Hub 알림을 참조하세요.](#device-notify-iot-hub-of-a-completed-file-upload)

백 엔드 서비스는 IoT Hub의 서비스 관련 파일 업로드 알림 엔드포인트에서 파일 업로드 알림을 구독할 수 있습니다. IoT Hub에서 이러한 알림을 사용하도록 설정한 경우 디바이스가 파일 업로드를 완료했다고 허브에 알 수 있을 때마다 이 엔드포인트에서 알림을 전달합니다. 서비스는 이러한 알림을 사용하여 Blob 데이터의 추가 처리를 트리거할 수 있습니다. 자세한 내용은 [서비스: 파일 업로드 알림을 참조하세요.](#service-file-upload-notifications)

파일 업로드는 Azure IoT 디바이스 및 서비스 SDK에서 완전히 지원됩니다. 자세한 내용은 [SDK를 사용하여 파일 업로드를](#file-upload-using-an-sdk)참조하세요.

### <a name="file-upload-quotas-and-limits"></a>파일 업로드 할당량 및 제한

IoT Hub 지정된 기간 동안 시작할 수 있는 파일 업로드 수에 제한 한도를 부과합니다. 임계값은 SKU 및 IoT Hub의 단위 수를 기반으로 합니다. 또한 각 디바이스는 한 번에 10개의 동시 활성 파일 업로드로 제한됩니다. 자세한 내용은 [제한 및 할당량을 참조하세요.](iot-hub-devguide-quotas-throttling.md)

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage 계정을 IoT Hub 연결

파일 업로드 기능을 사용하려면 Azure Storage 계정 및 Blob 컨테이너를 IoT Hub와 연결해야 합니다. IoT Hub에 등록된 디바이스의 모든 파일 업로드는 이 컨테이너로 이동합니다. IoT Hub에서 스토리지 계정 및 Blob 컨테이너를 구성하려면 [Azure Portal 사용하여 파일 업로드 구성, Azure CLI](iot-hub-configure-file-upload.md)사용하여 파일 업로드 [구성](iot-hub-configure-file-upload-cli.md)또는 [PowerShell을](iot-hub-configure-file-upload-powershell.md)사용하여 파일 업로드 구성을 참조하세요. IoT Hub 관리 API를 사용하여 프로그래밍 방식으로 파일 업로드를 구성할 수도 있습니다.

포털을 사용하는 경우 구성 중에 스토리지 계정 및 컨테이너를 만들 수 있습니다. 그렇지 않으면 스토리지 계정을 만들려면 Azure Storage 설명서에서 [스토리지 계정 만들기를](../storage/common/storage-account-create.md) 참조하세요. 스토리지 계정이 있으면 Azure Blob Storage 빠른 시작 에서 [Blob](../storage/blobs/storage-quickstart-blobs-portal.md)컨테이너를 만드는 방법을 확인할 수 있습니다.

파일 업로드 및 파일 업로드 알림의 동작을 제어하는 몇 가지 다른 설정이 있습니다. 다음 섹션에서는 사용 가능한 모든 설정을 나열합니다. Azure Portal, Azure CLI, PowerShell 또는 관리 API를 사용하여 파일 업로드를 구성하는지 여부에 따라 이러한 설정 중 일부를 사용할 수 없습니다. 파일 업로드가 완료될 때 백 엔드 서비스로 알림을 보내려면 **enableFileUploadNotifications** 설정을 확인해야 합니다.

### <a name="iot-hub-storage-and-authentication-settings"></a>Ot Hub 스토리지 및 인증 설정

다음 설정은 스토리지 계정 및 컨테이너를 IoT Hub와 연결하고 허브가 Azure Storage로 인증하는 방법을 제어합니다. 이러한 설정은 디바이스가 Azure Storage를 통해 인증하는 방법에 영향을 미치지 않습니다. 디바이스는 항상 IoT Hub 검색된 SAS URI에 제공된 SAS 토큰을 사용하여 인증합니다.

| 속성 | 설명 | 범위 및 기본값 |
| --- | --- | --- |
| **storageEndpoints.$default.authenticationType** | IoT Hub Azure Storage를 인증하는 방법을 제어합니다. | 가능한 값은 keyBased 및 identityBased입니다. 기본값: keyBased. |
| **storageEndpoints.$default.connectionString** | 파일 업로드에 사용할 Azure Storage 계정에 대한 연결 문자열입니다. | 기본값: 빈 문자열입니다. |
| **storageEndpoints.$default.containerName** | 파일을 업로드할 컨테이너의 이름입니다. | 기본값: 빈 문자열입니다. |
| **storageEndpoints.$default.identity** | ID 기반 인증에 사용할 관리 ID입니다. | 가능한 값은 `[system]` 시스템 할당 관리 ID 또는 사용자 할당 관리 ID의 리소스 ID에 대한 값입니다. 값은 키 기반 인증에 사용되지 않습니다. 기본값: null입니다. |

### <a name="file-upload-settings"></a>파일 업로드 설정

다음 설정은 디바이스에서 파일 업로드를 제어합니다.

| 속성 | 설명 | 범위 및 기본값 |
| --- | --- | --- |
| **storageEndpoints.$default.ttlAsIso8601** | IoT Hub 생성된 SAS URI에 대한 기본 TTL입니다. | ISO_8601 간격은 최대 48시간(최소 1분)입니다. 기본값은 1시간입니다. |

### <a name="file-upload-notification-settings"></a>파일 업로드 알림 설정

다음 설정은 백 엔드 서비스에 대한 파일 업로드 알림을 제어합니다.

| 속성 | 설명 | 범위 및 기본값 |
| --- | --- | --- |
| **enableFileUploadNotifications** |파일 업로드 알림이 파일 알림 엔드포인트에 작성되는지를 제어합니다. |Bool. 기본값: False. |
| **fileNotifications.ttlAsIso8601** |파일 업로드 알림에 대한 기본 TTL입니다. |ISO_8601 간격은 최대 48시간(최소 1분)입니다. 기본값은 1시간입니다. |
| **fileNotifications.lockDuration** |파일 업로드 알림 큐에 대한 잠금 기간입니다. |5~300초 기본값은 60초입니다. |
| **fileNotifications.maxDeliveryCount** |파일 업로드 알림 큐에 대한 최대 배달 횟수입니다. |1에서 100까지입니다. 기본값은 100입니다. |

## <a name="file-upload-using-an-sdk"></a>SDK를 사용하여 파일 업로드

다음 방법 가이드에서는 Azure IoT 장치 및 서비스 sdk를 사용 하 여 파일을 업로드 하기 위한 전체 단계별 지침을 제공 합니다. Azure Portal 사용 하 여 저장소 계정을 IoT hub와 연결 하는 방법을 보여 주고, 코드 조각을 포함 하거나, 업로드 과정을 안내 하는 샘플을 참조 합니다.

| 방법 가이드 | 장치 SDK 예 | 서비스 SDK 예 |
|---------|--------|---------|
| [.NET](iot-hub-csharp-csharp-file-upload.md) | 예 | 예 |
| [Java](iot-hub-java-java-file-upload.md) | 예 | 예 |
| [Node.js](iot-hub-node-node-file-upload.md) | 예 | 예 |
| [Python](iot-hub-python-python-file-upload.md) | 예 | 아니요 (지원 되지 않음) |

> [!NOTE]
> C 장치 SDK는 장치 클라이언트에서 단일 호출을 사용 하 여 파일 업로드를 수행 합니다. 자세한 내용은 [IoTHubDeviceClient_UploadToBlobAsync ()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadtoblobasync) 및 [IoTHubDeviceClient_UploadMultipleBlocksToBlobAsync ()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadmultipleblockstoblobasync)를 참조 하세요. 이러한 함수는 한 번의 호출로 파일 업로드의 모든 측면을 수행 합니다. 즉, 업로드를 시작 하 고, Azure storage에 파일을 업로드 하 고, 완료 되 면 IoT Hub에 알립니다. 즉, 장치가 IoT Hub와 통신 하는 데 사용 하는 프로토콜 외에도, azure storage Api에 대 한 호출을 수행 하기 때문에 HTTPS를 통해 Azure storage와 통신할 수 있어야 합니다.

## <a name="device-initialize-a-file-upload"></a>장치: 파일 업로드 초기화

장치는 파일 업로드를 시작 하는 장치 Sdk 중 하나에서 [Create File 업로드 SAS URI](/rest/api/iothub/device/create-file-upload-sas-uri) REST API 또는 해당 API를 호출 합니다.

**지원 되는 프로토콜**: amqp, amqp-WS, MQTT, MQTT-WS 및 HTTPS <br/>
**끝점**: {iot hub}. azure-장치. n e t/장치/{deviceId}/파일 <br/>
**메서드**: POST

```json
{
    "blobName":"myfile.txt"
}

```

| 속성 | 설명 |
|----------|-------------|
| blobName | SAS URI를 생성할 blob의 이름입니다. |

 IoT Hub는 장치에서 Azure storage에 인증 하는 데 사용할 수 있는 상관 관계 ID 및 SAS URI 요소를 사용 하 여 응답 합니다. 이 응답에는 대상 IoT hub의 제한 한도 및 장치별 업로드 제한이 적용 됩니다.

```json
{
    "correlationId":"MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "hostName":"contosostorageaccount.blob.core.windows.net",
    "containerName":"device-upload-container",
    "blobName":"mydevice/myfile.txt",
    "sasToken":"?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw"
}

```

| 속성 | 설명 |
|----------|-------------|
| correlationId | IoT Hub에 파일 업로드 완료 알림을 보낼 때 사용할 장치에 대 한 식별자입니다. |
| hostName | IoT hub에 구성 된 저장소 계정의 Azure storage 계정 호스트 이름 |
| containerName | IoT hub에 구성 된 blob 컨테이너의 이름입니다. |
| blobName | Blob이 컨테이너에 저장 되는 위치입니다. 이름은 다음 형식으로 되어 있습니다. `{device ID of the device making the request}/{blobName in the request}` |
| sasToken | Azure storage를 사용 하 여 blob에 대 한 읽기/쓰기 액세스 권한을 부여 하는 SAS 토큰입니다. 토큰은 IoT Hub에 의해 생성 되 고 서명 됩니다. |

응답이 수신 되 면 장치는 다음을 수행 합니다.

* 업로드를 완료할 때 IoT hub에 파일 업로드 완료 알림에 포함할 상관 관계 ID를 저장 합니다.

* 는 다른 속성을 사용 하 여 Azure storage에 인증 하는 데 사용 하는 blob에 대 한 SAS URI를 생성 합니다. SAS URI에는 요청 된 blob 및 SAS 토큰에 대 한 리소스 URI가 포함 됩니다. 다음 형식을 사용 합니다. `https://{hostName}/{containerName}/{blobName}{sasToken}` ( `sasToken` 응답의 속성에 선행 '? ' 문자가 포함 되어 있습니다.) 중괄호는 포함 되지 않습니다.

    예를 들어 위의 샘플에서 반환 된 값의 경우 SAS URI는,입니다. `https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw`

    SAS URI 및 SAS 토큰에 대 한 자세한 내용은 Azure storage 설명서에서 [서비스 SAS 만들기](/rest/api/storageservices/create-service-sas) 를 참조 하세요.

## <a name="device-upload-file-using-azure-storage-apis"></a>장치: Azure storage Api를 사용 하 여 업로드 파일

장치는 azure [blob STORAGE REST api](/rest/api/storageservices/blob-service-rest-api) 또는 동등한 AZURE Storage SDK api를 사용 하 여 azure storage의 Blob에 파일을 업로드 합니다.

**지원 되는 프로토콜**: HTTPS

다음 예제에서는 작은 블록 blob을 만들거나 업데이트 하는 [Blob Put](/rest/api/storageservices/put-blob) 요청을 보여 줍니다. 이 요청에 사용 되는 URI는 이전 섹션의 IoT Hub에서 반환 하는 SAS URI입니다. `x-ms-blob-type`헤더는이 요청이 블록 blob에 대 한 것임을 나타냅니다. 요청이 성공 하는 경우 Azure storage는를 반환 `201 Created` 합니다.

```http
PUT https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw HTTP/1.1
Content-Length: 11
Content-Type: text/plain; charset=UTF-8
Host: contosostorageaccount.blob.core.windows.net
x-ms-blob-type: BlockBlob

hello world
```

Azure storage Api를 사용 하는 방법은이 문서의 범위를 벗어나는 것입니다. 이 섹션의 이전에 연결 된 Azure Blob storage REST Api 외에도 다음 설명서를 통해 시작 하는 데 도움이 될 수 있습니다.

* Azure storage에서 blob을 사용 하는 방법에 대해 자세히 알아보려면 [azure blob storage](../storage/blobs/index.yml) 설명서를 참조 하세요.

* Azure storage 클라이언트 sdk를 사용 하 여 blob을 업로드 하는 방법에 대 한 자세한 내용은 [Azure Blob Storage API 참조](../storage/blobs/reference.md)를 참조 하세요.  

## <a name="device-notify-iot-hub-of-a-completed-file-upload"></a>장치: 완료 된 파일 업로드 IoT Hub 알림

장치는 파일 업로드를 완료할 때 장치 Sdk 중 하나에서 [업데이트 파일 업로드 상태](/rest/api/iothub/device/update-file-upload-status) REST API 또는 해당 API를 호출 합니다. 장치는 업로드가 성공 또는 실패 여부에 관계 없이 IoT Hub를 사용 하 여 파일 업로드 상태를 업데이트 해야 합니다.

**지원 되는 프로토콜**: amqp, amqp-WS, MQTT, MQTT-WS 및 HTTPS <br/>
**끝점**: {iot hub}. azure-장치. n e t/장치/{deviceId}/파일/알림 <br/>
**메서드**: POST 

```json
{
    "correlationId": "MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "isSuccess": true,
    "statusCode": 200,
    "statusDescription": "File uploaded successfully"
}

```

 속성 | 설명 |
|----------|-------------|
| correlationId | 초기 SAS URI 요청에서 받은 상관 관계 ID입니다. |
| isSuccess | 파일 업로드에 성공 했는지 여부를 나타내는 부울입니다. |
| statusCode | 파일 업로드의 상태 코드를 나타내는 정수입니다. 일반적으로 3 자리 숫자 예를 들면 200 또는 201입니다. |
| statusDescription | 파일 업로드 상태에 대 한 설명입니다. |

장치에서 파일 업로드 완료 알림이 수신 되 면 다음을 IoT Hub 합니다.

* 파일 업로드 알림이 구성 된 경우 백엔드 서비스에 대 한 파일 업로드 알림을 트리거합니다.

* 파일 업로드와 연결 된 리소스를 해제 합니다. 알림을 받지 않으면 업로드와 연결 된 SAS URI TTL (time-to-live)이 만료 될 때까지 IoT Hub에서 리소스를 유지 관리 합니다.

## <a name="service-file-upload-notifications"></a>서비스: 파일 업로드 알림

IoT hub에서 파일 업로드 알림을 사용 하는 경우 파일 업로드가 완료 된 장치에서 알림을 받을 때 백엔드 서비스에 대 한 알림 메시지를 생성 합니다. IoT Hub은 서비스 지향 끝점을 통해 이러한 파일 업로드 알림을 제공 합니다. 파일 업로드 알림에 대한 수신 의미 체계는 클라우드-디바이스 메시지의 경우와 동일하며 동일한 [메시지 수명 주기](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)를 갖습니다. 서비스 Sdk는 파일 업로드 알림을 처리 하는 Api를 노출 합니다.

**지원 되는 프로토콜** AMQP, AMQP-WS <br/>
**끝점**: {iot hub}. azure-장치. net/messages/servicebound/fileuploadnotifications <br/>
**메서드** 가져오기

파일 업로드 알림 끝점에서 검색 된 각 메시지는 JSON 레코드입니다.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt",
    "blobName":"mydevice/myfile.txt",
    "lastUpdatedTime":"2021-07-31T00:26:50+00:00",
    "blobSizeInBytes":11,
    "enqueuedTimeUtc":"2021-07-31T00:26:51.5134008Z"
}
```

| 속성 | 설명 |
| --- | --- |
| enqueuedTimeUtc | 알림을 만든 시간을 나타내는 타임스탬프입니다. |
| deviceId | 파일을 업로드 한 장치의 장치 ID입니다. |
| blobUri | 업로드된 파일의 URI입니다. |
| blobName | 업로드된 파일의 이름입니다. 이름은 다음과 같은 형식입니다. `{device ID of the device}/{name of the blob}`|
| lastUpdatedTime |파일이 마지막으로 업데이트된 시간을 나타내는 타임스탬프입니다. |
| blobSizeInBytes | 업로드된 파일의 크기(바이트)를 나타내는 정수입니다. |

서비스는 알림을 사용하여 업로드를 관리할 수 있습니다. 예를 들어 Blob 데이터의 자체 처리를 트리거하거나, 다른 Azure 서비스를 사용하여 Blob 데이터의 처리를 트리거하거나, 나중에 검토할 수 있도록 파일 업로드 알림을 기록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [파일 업로드 방법 가이드](iot-hub-csharp-csharp-file-upload.md)

* [IoT Hub 파일 업로드를 구성하는 방법](iot-hub-configure-file-upload.md)

* [IoT Hub 관리 ID를 구성하는 방법](iot-hub-managed-identity.md)

* [Azure Blob Storage 설명서](../storage/blobs/index.yml)

* [Azure IoT 디바이스 및 서비스 SDK](iot-hub-devguide-sdks.md)는 IoT Hub와 상호 작용하는 디바이스 및 서비스 앱 모두를 개발할 때 사용할 수 있는 다양한 언어 SDK를 나열합니다.