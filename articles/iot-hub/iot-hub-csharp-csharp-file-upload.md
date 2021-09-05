---
title: .NET을 사용하여 디바이스에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: .NET용 Azure IoT 디바이스 SDK를 사용하여 디바이스에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/18/2021
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 41cf392fb4b50c06e6af1f20e0c53570589b4090
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528768"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 자습서에서는 .NET 파일 업로드 샘플을 사용하여 IoT Hub의 파일 업로드 기능을 사용하는 방법을 보여줍니다. 

[디바이스에서 IoT Hub로 원격 분석 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) 빠른 시작 및 [IoT Hub를 사용하여 클라우드-디바이스 메시지 보내기](iot-hub-csharp-csharp-c2d.md) 자습서는 IoT Hub의 기본적인 디바이스-클라우드 및 클라우드-디바이스 메시징 기능을 보여 줍니다. [IoT Hub로 메시지 라우팅 구성](tutorial-routing.md) 자습서에서는 디바이스-클라우드 메시지를 Microsoft Azure Blob Storage에 안정적으로 저장하는 방법에 대해 설명합니다. 그러나 일부 시나리오에서는 디바이스에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 디바이스-클라우드 메시지에 쉽게 매핑할 수 없습니다. 예를 들면 다음과 같습니다.

* 이미지가 포함된 대형 파일

* 동영상

* 자주 샘플링되는 진동 데이터

* 특정 형태의 전처리된 데이터

이러한 파일은 일반적으로 [Azure Data Factory](../data-factory/introduction.md) 또는 [Hadoop](../hdinsight/index.yml) 스택과 같은 도구를 사용하여 클라우드에서 배치 방식으로 처리됩니다. 그러나 디바이스에서 파일을 업로드해야 하는 경우에도 IoT Hub의 보안 및 안정성을 사용할 수 있습니다. 이 튜토리얼에서는 그 방법에 대해 설명합니다.

> [!NOTE]
> IoT Hub는 Azure IoT 디바이스 SDK를 통해 많은 디바이스 플랫폼 및 언어(C, Java, Python 및 JavaScript 포함)를 지원합니다. Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot)를 참조하세요.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio Code

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* 이 문서에서 실행하는 샘플 애플리케이션은 C#을 사용하여 작성되었습니다. Azure IoT C# 샘플의 경우 개발 머신에 .NET Core SDK 3.1 이상이 설치되어 있는 것이 좋습니다.

    [.NET](https://dotnet.microsoft.com/download)에서 여러 플랫폼에 대한 .NET Core SDK를 다운로드할 수 있습니다.

    다음 명령을 사용하여 개발 머신에서 .NET Core SDK의 현재 버전을 확인할 수 있습니다.

    ```cmd/sh
    dotnet --version
    ```

* [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)에서 Azure IoT C# 샘플을 다운로드하고 ZIP 보관 파일을 추출합니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="examine-the-application"></a>애플리케이션 검사

Visual Studio Code에서 .NET 샘플 다운로드에 있는 *azure-iot-samples-csharp-master\iot-hub\Samples\device\FileUploadSample* 폴더를 엽니다. 폴더에는 *parameters.cs* 라는 파일이 포함되어 있습니다. 해당 파일을 열면 매개 변수 *p* 가 필요하고 디바이스 연결 문자열이 포함되어 있음을 알 수 있습니다. 디바이스를 등록할 때 이 연결 문자열을 복사하고 저장했습니다. 전송 프로토콜을 변경하려는 경우 매개 변수 *t* 를 지정할 수 있습니다. 기본 프로토콜은 mqtt입니다. 파일 *program.cs* 에는 *main* 함수가 포함되어 있습니다. *FileUploadSample.cs* 파일에는 기본 샘플 논리가 포함되어 있습니다. *TestPayload.txt* 는 Blob 컨테이너에 업로드할 파일입니다.

## <a name="run-the-application"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. Visual Studio Code에서 터미널 창을 엽니다.
1. 다음 명령을 입력합니다.
    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```

출력은 다음과 유사합니다.

```cmd/sh
  Uploading file TestPayload.txt
  Getting SAS URI from IoT Hub to use when uploading the file...
  Successfully got SAS URI (https://contosostorage.blob.core.windows.net/contosocontainer/MyDevice%2FTestPayload.txt?sv=2018-03-28&sr=b&sig=x0G1Baf%2BAjR%2BTg3nW34zDNKs07p6dLzkxvZ3ZSmjIhw%3D&se=2021-05-04T16%3A40%3A52Z&sp=rw) from IoT Hub
  Uploading file TestPayload.txt using the Azure Storage SDK and the retrieved SAS URI for authentication
  Successfully uploaded the file to Azure Storage
  Notified IoT Hub that the file upload succeeded and that the SAS URI can be freed.
  Time to upload file: 00:00:01.5077954.
  Done.
```

## <a name="verify-the-file-upload"></a>파일 업로드 확인

*TestPayload.txt* 가 컨테이너에 업로드되었는지 확인하려면 다음 단계를 수행합니다.

1. 스토리지 계정의 왼쪽 창에서 **데이터 스토리지** 아래에 있는 **컨테이너** 를 선택합니다.
1. *TestPayload.txt* 를 업로드한 컨테이너를 선택합니다.
1. 디바이스 이름을 딴 폴더를 선택합니다.
1. *TestPayload.txt* 를 선택합니다.
1. 파일을 다운로드하여 로컬로 콘텐츠를 봅니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에서 파일 업로드를 단순화하기 위해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 알아보았습니다. 다음 문서를 사용하여 IoT Hub 기능 및 시나리오를 계속 탐색할 수 있습니다.

* [프로그래밍 방식으로 IoT Hub 만들기](iot-hub-rm-template-powershell.md)

* [C SDK 소개](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/quickstart-linux.md)