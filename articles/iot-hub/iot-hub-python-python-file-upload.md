---
title: Python을 사용하여 디바이스에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: Python용 Azure IoT 디바이스 SDK를 사용하여 디바이스에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/18/2021
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: a7f367c1301a3eb325e9dc024c518ecabe791170
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122835647"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 문서에서는 [IoT Hub의 파일 업로드 기능](iot-hub-devguide-file-upload.md)을 사용하여 [Azure Blob Storage](../storage/index.yml)에 파일을 업로드하는 방법에 대해 설명합니다. 이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 파일을 업로드하기 위해 스토리지 컨테이너를 안전하게 제공합니다.

* Python 클라이언트를 사용하여 IoT 허브를 통해 파일을 업로드합니다.

[디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) 빠른 시작에서는 IoT Hub의 기본 디바이스-클라우드 메시지 기능을 보여 줍니다. 그러나 일부 시나리오에서는 디바이스에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 디바이스-클라우드 메시지에 쉽게 매핑할 수 없습니다. 디바이스에서 파일을 업로드해야 할 때 IoT Hub의 보안 및 안정성을 여전히 사용할 수 있습니다.

이 자습서의 끝 부분에서 Python 콘솔 앱을 실행합니다.

* **FileUpload.py** 는 Python 디바이스 SDK를 사용하여 파일을 스토리지로 업로드합니다.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>디바이스 앱에서 파일 업로드

이 섹션에서는 IoT Hub에 파일을 업로드하는 디바이스 앱을 만듭니다.

1. 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 패키지를 설치합니다. 이 패키지를 사용하여 IoT 허브로 파일 업로드를 조정합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 명령 프롬프트에서 다음 명령을 실행하여 [**azure.storage.blob**](https://pypi.org/project/azure-storage-blob/) 패키지를 설치합니다. 이 패키지를 사용하여 파일 업로드를 수행합니다.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. blob storage에 업로드할 테스트 파일을 만듭니다.

1. 텍스트 편집기를 사용하여 작업 폴더에 **FileUpload.py** 파일을 만듭니다.

1. **FileUpload.py** 파일의 시작 부분에서 다음 `import` 문 및 변수를 추가합니다.

    ```python
    import os
    from azure.iot.device import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. 파일에서 `[Device Connection String]`을 IoT Hub 디바이스의 연결 문자열로 바꿉니다. `[Full path to local file]`을 작성한 테스트 파일 또는 업로드할 디바이스의 파일 경로로 바꿉니다.

1. blob storage에 파일을 업로드하는 함수를 만듭니다.

    ```python
    def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    이 함수는 이 함수에 전달된 *blob_info* 구조를 구문 분석하여 [azure.storage.blob.BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient)를 초기화하는 데 사용하는 URL을 만듭니다. 그런 다음 이 클라이언트를 사용하여 Azure blob storage에 파일을 업로드합니다.

1. 다음 코드를 추가하여 클라이언트를 연결하고 파일을 업로드합니다.

    ```python
    def run_sample(device_client):
        # Connect the client
        device_client.connect()

        # Get the storage info for the blob
        blob_name = os.path.basename(PATH_TO_FILE)
        storage_info = device_client.get_storage_info_for_blob(blob_name)

        # Upload to blob
        success, result = store_blob(storage_info, PATH_TO_FILE)

        if success == True:
            print("Upload succeeded. Result is: \n") 
            print(result)
            print()

            device_client.notify_blob_upload_status(
                storage_info["correlationId"], True, 200, "OK: {}".format(PATH_TO_FILE)
            )

        else :
            # If the upload was not successful, the result is the exception object
            print("Upload failed. Exception is: \n") 
            print(result)
            print()

            device_client.notify_blob_upload_status(
                storage_info["correlationId"], False, result.status_code, str(result)
            )

    def main():
        device_client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        try:
            print ("IoT Hub file upload sample, press Ctrl-C to exit")
            run_sample(device_client)
        except KeyboardInterrupt:
            print ("IoTHubDeviceClient sample stopped")
        finally:
            # Graceful exit
            device_client.shutdown()


    if __name__ == "__main__":
        main()
    ```

    이 코드는 **IoTHubDeviceClient** 를 만들고 다음 API를 사용하여 IoT 허브로 파일 업로드를 관리합니다.

    * **get_storage_info_for_blob** 는 IoT 허브에서 이전에 만든 연결된 스토리지 계정에 대한 정보를 가져옵니다. 이 정보에는 호스트 이름, 컨테이너 이름, blob 이름 및 SAS 토큰이 포함됩니다. 스토리지 정보가 이전 단계에서 만든 **store_blob** 함수에 전달되므로 해당 함수의 **BlobClient** 는 Azure Storage를 사용하여 인증할 수 있습니다. **get_storage_info_for_blob** 메서드는 **notify_blob_upload_status** 메서드에서 사용되는 correlation_id도 반환합니다. correlation_id는 IoT Hub에서 작업 중인 blob을 표시하는 방식입니다.

    * **notify_blob_upload_status** 는 Blob Storage 작업의 상태를 IoT Hub에 알립니다. **get_storage_info_for_blob** 메서드로 얻은 correlation_id를 전달합니다. IoT Hub에서 파일 업로드 작업의 상태에 대한 알림을 수신할 수 있는 서비스를 알리는 데 사용됩니다.

1. **FileUpload.py** 파일을 저장하고 닫습니다.

## <a name="run-the-application"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. 작업 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```cmd/sh
    python FileUpload.py
    ```

2. 다음 스크린샷은 **FileUpload** 앱의 출력을 보여 줍니다.

    ![simulated-device 앱의 출력](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. 포털을 사용하면 구성한 스토리지 컨테이너에 업로드된 파일을 볼 수 있습니다.

    ![업로드된 파일](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에서 파일 업로드를 단순화하기 위해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 알아보았습니다. 다음 문서를 사용하여 IoT Hub 기능 및 시나리오를 계속 탐색할 수 있습니다.

* [프로그래밍 방식으로 IoT Hub 만들기](iot-hub-rm-template-powershell.md)

* [C SDK 소개](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

다음 링크를 통해 Azure Blob Storage에 대해 자세히 알아보세요.

* [Azure Blob Storage 설명서](../storage/blobs/index.yml)

* [Python API용 Azure Blob Storage 설명서](/python/api/overview/azure/storage-blob-readme)