---
title: Node.js를 사용하여 디바이스에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: Node.js용 Azure IoT 디바이스 SDK를 사용하여 디바이스에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: a95737cd9d15b0ee21249f0db8d1bbb96fb8a3b2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710088"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 파일을 업로드하기 위한 Azure blob URI를 디바이스에 안전하게 제공합니다.

* IoT Hub 파일 업로드 알림을 사용하여 앱 백 엔드에서 파일 처리를 트리거합니다.

[디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) 빠른 시작에서는 IoT Hub의 기본 디바이스-클라우드 메시지 기능을 보여 줍니다. 그러나 일부 시나리오에서는 디바이스에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 디바이스-클라우드 메시지에 쉽게 매핑할 수 없습니다. 예를 들면 다음과 같습니다.

* 이미지가 포함된 대형 파일
* 동영상
* 자주 샘플링되는 진동 데이터
* 특정 형태의 전처리된 데이터

이러한 파일은 일반적으로 [Azure Data Factory](../data-factory/introduction.md) 또는 [Hadoop](../hdinsight/index.yml) 스택과 같은 도구를 사용하여 클라우드에서 배치 방식으로 처리됩니다. 디바이스에서 파일을 업로드해야 할 때 IoT Hub의 보안 및 안정성을 여전히 사용할 수 있습니다.

이 문서의 끝부분에서 다음 두 개의 Node.js 콘솔 앱을 실행합니다.

* **FileUpload.js** - IoT 허브에서 제공하는 SAS URI를 사용하여 스토리지에 파일을 업로드합니다.

* **FileUploadNotification.js** - IoT 허브에서 파일 업로드 알림을 받습니다.

> [!NOTE]
> IoT Hub는 Azure IoT 디바이스 SDK를 통해 많은 디바이스 플랫폼 및 언어(C, Java, Python 및 JavaScript 포함)를 지원합니다. Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot)를 참조하세요.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Node.js 버전 10.0.x 이상. LTS 버전을 사용하는 것이 좋습니다. [nodejs.org](https://nodejs.org)에서 Node.js를 다운로드할 수 있습니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>디바이스 앱에서 파일 업로드

이 섹션에서는 IoT 허브에 파일을 업로드하는 디바이스 앱을 만듭니다. 코드는 [Azure IoT node.js SDK](https://github.com/Azure/azure-iot-sdk-node) 디바이스 샘플의 [upload_to_blob_advanced.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/javascript/upload_to_blob_advanced.js) 샘플에서 사용할 수 있는 코드를 기반으로 합니다.

1. `fileupload`라는 빈 폴더를 만듭니다.  `fileupload` 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

1. `fileupload` 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 디바이스 SDK, **azure-iot-device-mqtt**, **@azure/storage-blob** 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt @azure/storage-blob --save
    ```

1. 텍스트 편집기를 사용하여 `fileupload` 폴더에 **FileUpload.js** 파일을 만들고 다음 코드를 파일에 복사합니다.

    ```javascript
    'use strict';

    const Client = require('azure-iot-device').Client;
    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    const errors = require('azure-iot-common').errors;
    const path = require('path');

    const {
      AnonymousCredential,
      BlockBlobClient,
      newPipeline
    } = require('@azure/storage-blob');

    // make sure you set these environment variables prior to running the sample.
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    const localFilePath = process.env.PATH_TO_FILE;
    const storageBlobName = path.basename(localFilePath);

    async function uploadToBlob(localFilePath, client) {
      const blobInfo = await client.getBlobSharedAccessSignature(storageBlobName);
      if (!blobInfo) {
        throw new errors.ArgumentError('Invalid upload parameters');
      }

      const pipeline = newPipeline(new AnonymousCredential(), {
        retryOptions: { maxTries: 4 },
        telemetry: { value: 'HighLevelSample V1.0.0' }, // Customized telemetry string
        keepAliveOptions: { enable: false }
      });

      // Construct the blob URL to construct the blob client for file uploads
      const { hostName, containerName, blobName, sasToken } = blobInfo;
      const blobUrl = `https://${hostName}/${containerName}/${blobName}${sasToken}`;

      // Create the BlockBlobClient for file upload to the Blob Storage Blob
      const blobClient = new BlockBlobClient(blobUrl, pipeline);

      // Setup blank status notification arguments to be filled in on success/failure
      let isSuccess;
      let statusCode;
      let statusDescription;

      try {
        const uploadStatus = await blobClient.uploadFile(localFilePath);
        console.log('uploadStreamToBlockBlob success');

        // Save successful status notification arguments
        isSuccess = true;
        statusCode = uploadStatus._response.status;
        statusDescription = uploadStatus._response.bodyAsText;

        // Notify IoT Hub of upload to blob status (success)
        console.log('notifyBlobUploadStatus success');
      }
      catch (err) {
        isSuccess = false;
        statusCode = err.code;
        statusDescription = err.message;

        console.log('notifyBlobUploadStatus failed');
        console.log(err);
      }

      await client.notifyBlobUploadStatus(blobInfo.correlationId, isSuccess, statusCode, statusDescription);
    }

    // Create a client device from the connection string and upload the local file to blob storage.
    const deviceClient = Client.fromConnectionString(deviceConnectionString, Protocol);
    uploadToBlob(localFilePath, deviceClient)
      .catch((err) => {
        console.log(err);
      })
      .finally(() => {
        process.exit();
      });
    ```

1. **FileUpload.js** 파일을 저장하고 닫습니다.

1. 이미지 파일을 `fileupload` 폴더에 복사하고 `myimage.png`와 같은 이름을 지정합니다.

1. 디바이스 연결 문자열에 대한 환경 변수와 업로드할 파일의 경로를 추가합니다. 디바이스 연결 문자열은 [IoT 허브에 디바이스를 등록](#register-a-new-device-in-the-iot-hub)할 때 얻은 것입니다.
    
    - Windows의 경우:

        ```cmd
        set DEVICE_CONNECTION_STRING={your device connection string}
        set PATH_TO_FILE={your image filepath}
        ```

    - Linux/Bash의 경우:

        ```bash
        export DEVICE_CONNECTION_STRING="{your device connection string}"
        export PATH_TO_FILE="{your image filepath}"
        ```

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

이 문서에서는 사용자가 만든 IoT 허브에서 파일 업로드 알림 메시지를 수신하는 백 엔드 서비스를 만듭니다. 파일 업로드 알림 메시지를 수신하려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub는 이 사용 권한을 부여하는 **service** 라는 공유 액세스 정책을 사용하여 만듭니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>파일 업로드 알림 수신

이 섹션에서는 IoT Hub로부터 파일 업로드 알림 메시지를 수신하는 Node.js 콘솔 앱을 만듭니다.

1. `fileuploadnotification`라는 빈 폴더를 만듭니다.  `fileuploadnotification` 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다.  모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

1. `fileuploadnotification` 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** SDK 패키지를 설치합니다.

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. 텍스트 편집기를 사용하여 `fileuploadnotification` 폴더에 **FileUploadNotification.js** 파일을 만듭니다.

1. **FileUploadNotification.js** 파일의 시작 부분에 다음 `require` 문을 추가합니다.

    ```javascript
    'use strict';

    const Client = require('azure-iothub').Client;
    ```

1. 환경에서 IoT 허브에 대한 연결 문자열을 읽습니다.

    ```javascript
    const connectionString = process.env.IOT_HUB_CONNECTION_STRING;
    ```

1. 다음 코드를 추가하여 연결 문자열에서 서비스 클라이언트를 만듭니다.

    ```javascript
    const serviceClient = Client.fromConnectionString(connectionString);
    ```

1. 클라이언트를 열고 상태 업데이트를 수신하는 **getFileNotificationReceiver** 함수를 사용합니다.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. **FileUploadNotification.js** 파일을 저장하고 닫습니다.

1. IoT Hub 연결 문자열의 환경 변수를 추가합니다. 이 문자열은 이전에 [IoT 허브 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 것입니다.
    
    - Windows의 경우:

        ```cmd
        set IOT_HUB_CONNECTION_STRING={your iot hub connection string}
        ```

    - Linux/Bash의 경우:

        ```bash
        export IOT_HUB_CONNECTION_STRING="{your iot hub connection string}"
        ```

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

`fileuploadnotification` 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

```cmd/sh
node FileUploadNotification.js
```

`fileupload` 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

```cmd/sh
node FileUpload.js
```

업로드가 완료되면 **FileUpload** 앱에서 다음 출력이 생성됩니다.

```output
uploadStreamToBlockBlob success
notifyBlobUploadStatus success
```

업로드가 완료되면 **FileUploadNotification** 앱에서 다음 샘플 출력이 생성됩니다.

```output
Service client connected
File upload from device:
{"deviceId":"myDeviceId","blobUri":"https://{your storage account name}.blob.core.windows.net/device-upload-container/myDeviceId/image.png","blobName":"myDeviceId/image.png","lastUpdatedTime":"2021-07-23T23:27:06+00:00","blobSizeInBytes":26214,"enqueuedTimeUtc":"2021-07-23T23:27:07.2580791Z"}
```

## <a name="verify-the-file-upload"></a>파일 업로드 확인

포털을 사용하면 구성한 스토리지 컨테이너에 업로드된 파일을 볼 수 있습니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. 스토리지 계정의 왼쪽 창에서 **컨테이너** 를 선택합니다.
1. 파일을 업로드한 컨테이너를 선택합니다.
1. 디바이스 이름을 딴 폴더를 선택합니다.
1. 파일을 업로드한 Blob을 선택합니다. 이 문서에서는 파일과 동일한 이름의 Blob입니다.  

    :::image type="content" source="./media/iot-hub-node-node-file-upload/view-uploaded-file.png" alt-text="Azure Portal에 표시된, 업로드된 파일 스크린샷":::

1. 열리는 페이지에서 Blob 속성을 확인합니다. **다운로드** 를 선택하여 파일을 다운로드하고 로컬에서 파일 내용을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에서 파일 업로드를 단순화하기 위해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 알아보았습니다. 다음 문서를 사용하여 IoT Hub 기능 및 시나리오를 계속 탐색할 수 있습니다.

* [프로그래밍 방식으로 IoT Hub 만들기](iot-hub-rm-template-powershell.md)

* [C SDK 소개](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
