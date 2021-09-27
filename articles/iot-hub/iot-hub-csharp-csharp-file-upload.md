---
title: .NET을 사용하여 디바이스에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: .NET용 Azure IoT 디바이스 SDK를 사용하여 디바이스에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 18055232714914456ec2db51891edc70ac89057e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779638"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드(.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 자습서에서는 Azure IoT .NET 디바이스와 서비스 SDK를 이용해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 보여 줍니다.

[디바이스에서 IoT Hub로 원격 분석 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) 빠른 시작 및 [IoT Hub를 사용하여 클라우드-디바이스 메시지 보내기](iot-hub-csharp-csharp-c2d.md) 자습서는 IoT Hub의 기본적인 디바이스-클라우드 및 클라우드-디바이스 메시징 기능을 보여 줍니다. [IoT Hub로 메시지 라우팅 구성](tutorial-routing.md) 자습서에서는 디바이스-클라우드 메시지를 Microsoft Azure Blob Storage에 안정적으로 저장하는 방법에 대해 설명합니다. 그러나 일부 시나리오에서는 디바이스에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 디바이스-클라우드 메시지에 쉽게 매핑할 수 없습니다. 예를 들면 다음과 같습니다.

* 이미지가 포함된 대형 파일

* 동영상

* 자주 샘플링되는 진동 데이터

* 특정 형태의 전처리된 데이터

이러한 파일은 일반적으로 [Azure Data Factory](../data-factory/introduction.md) 또는 [Hadoop](../hdinsight/index.yml) 스택과 같은 도구를 사용하여 클라우드에서 배치 방식으로 처리됩니다. 그러나 디바이스에서 파일을 업로드해야 하는 경우에도 IoT Hub의 보안 및 안정성을 사용할 수 있습니다. 이 튜토리얼에서는 그 방법에 대해 설명합니다.

이 자습서의 끝 부분에서 다음의 두 .NET 콘솔 앱을 실행합니다.

* **FileUploadSample** 이 디바이스 앱은 IoT 허브에서 제공하는 SAS URI를 사용하여 스토리지에 파일을 업로드합니다. 이 앱은 필수 구성 요소에서 다운로드하는 Azure IoT C# 샘플 리포지토리에서 실행합니다.

* **ReadFileUploadNotification**. 이 서비스 앱은 IoT 허브에서 파일 업로드 알림을 받습니다. 여러분은 이 앱을 만들게 됩니다.

> [!NOTE]
> IoT Hub는 Azure IoT 디바이스 SDK를 통해 많은 디바이스 플랫폼 및 언어(C, Java, Python 및 JavaScript 포함)를 지원합니다. Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot)를 참조하세요.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

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

## <a name="upload-file-from-a-device-app"></a>디바이스 앱의 파일 업로드

이 문서에서는 이전에 디바이스 앱으로서 다운로드한 Azure IoT C# 샘플 리포지토리에 있는 샘플을 사용합니다. Visual Studio, Visual Studio Code 또는 원하는 텍스트 편집기를 사용하여 아래 파일을 열 수 있습니다.  

샘플은 Azure IoT C# 샘플을 추출한 폴더의 **azure-iot-samples-csharp-master\iot-hub\Samples\device\FileUploadSample** 에 있습니다.

**FileUpLoadSample.cs** 에 있는 코드를 검사합니다. 이 파일에는 기본 샘플 논리가 포함되어 있습니다. 생성된 IoT Hub 디바이스 클라이언트는 디바이스의 파일을 업로드하는 표준 3단계 절차를 따릅니다.

1. 코드에서 디바이스 클라이언트의 **GetFileUploadSasUriAsync** 메서드를 호출하여 IoT 허브에서 SAS URI를 얻습니다.

    ```csharp
    var fileUploadSasUriRequest = new FileUploadSasUriRequest
    {
        BlobName = fileName
    };

    // Lines removed for clarity

    FileUploadSasUriResponse sasUri = await _deviceClient.GetFileUploadSasUriAsync(fileUploadSasUriRequest);
    Uri uploadUri = sasUri.GetBlobUri();
    ```

1. 코드에서 SAS URI를 사용하여 Azure Storage에 파일을 업로드합니다. 이 샘플에서 코드는 SAS URI를 사용하여 Azure Storage 블록 Blob 클라이언트를 만들고 파일을 업로드합니다.

    ```csharp
    var blockBlobClient = new BlockBlobClient(uploadUri);
    await blockBlobClient.UploadAsync(fileStreamSource, new BlobUploadOptions());
    ```

1. 코드에서 업로드 완료를 IoT 허브에 알립니다. IoT 허브는 업로드와 관련된 리소스(SAS URI)를 해제할 수 있음을 알게 됩니다. 파일 업로드 알림을 사용 설정한 경우 IoT 허브는 백 엔드 서비스에 알림 메시지를 보냅니다.

    ```csharp
    var successfulFileUploadCompletionNotification = new FileUploadCompletionNotification
    {
        // Mandatory. Must be the same value as the correlation id returned in the sas uri response
        CorrelationId = sasUri.CorrelationId,
    
        // Mandatory. Will be present when service client receives this file upload notification
        IsSuccess = true,
    
        // Optional, user defined status code. Will be present when service client receives this file upload notification
        StatusCode = 200,
    
        // Optional, user-defined status description. Will be present when service client receives this file upload notification
        StatusDescription = "Success"
    };
    
    await _deviceClient.CompleteFileUploadAsync(successfulFileUploadCompletionNotification);
    ```

**parameter.cs** 파일을 검사하면 다음을 확인하게 됩니다.

- 샘플에서는 사용자가 디바이스 연결 문자열을 취하는 매개 변수인 *p* 를 전달해야 합니다. 

- 기본적으로 디바이스 샘플은 MQTT 프로토콜을 사용하여 IoT Hub와 통신합니다. *t* 매개 변수를 사용하여 이 전송 프로토콜을 변경할 수 있습니다. 하지만 무엇을 선택하더라도 Azure Blob 클라이언트에서는 항상 HTTPS를 프로토콜로 사용하여 파일을 Azure Storage에 업로드합니다.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

이 문서에서는 사용자의 IoT 허브에서 파일 업로드 알림 메시지를 수신하는 백 엔드 서비스를 만듭니다. 파일 업로드 알림 메시지를 수신하려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub는 이 사용 권한을 부여하는 **service** 라는 공유 액세스 정책을 사용하여 만듭니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>파일 업로드 알림 수신

이 섹션에서는 사용자의 IoT 허브에서 파일 업로드 알림 메시지를 수신하는 C# 콘솔 앱을 만듭니다.

1. 명령 창을 열고 프로젝트를 만들 폴더로 이동합니다. **ReadFileUploadNotifications** 라는 이름의 폴더를 만들고 디렉터리를 이 폴더로 변경합니다.

    ```cmd/sh
    mkdir ReadFileUploadNotification
    cd ReadFileUploadNotification
    ```

1. 다음 명령을 실행하여 C# 콘솔 프로젝트를 만듭니다. 명령을 실행하면 폴더에는 **Program.cs** 파일과 **ReadFileUploadNotification.csproj** 파일이 포함됩니다.

    ```cmd/sh
    dotnet new console --language c#
    ```

1. 다음 명령을 실행하여 **Microsoft.Azure.Devices** 패키지를 프로젝트 파일에 추가합니다. 이 패키지는 Azure IoT .NET 서비스 SDK입니다.

    ```cmd/sh
    dotnet add package Microsoft.Azure.Devices
    ```

1. **Program.cs** 파일을 열고 파일 맨 위에 다음 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.Devices;
    ```
1. **Program** 클래스에 다음 필드를 추가합니다. `{iot hub connection string}` 자리 표시자 값을 이전에 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT Hub 연결 문자열로 바꿉니다.

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();
        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();
            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    수신 패턴은 디바이스 앱으로부터 클라우드-디바이스 메시지를 받는 데 사용되는 것과 동일합니다.

1. 마지막으로 **Main** 메서드의 줄을 다음 줄로 바꿉니다.

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```
    
## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. 먼저 서비스 앱을 실행하여 IoT 허브에서 파일 업로드 알림을 받습니다. **ReadFileUploadNotification** 폴더의 명령 프롬프트에서 다음 명령을 실행합니다. 

    ```cmd/sh
    dotnet restore
    dotnet run
    ```
    
    앱이 시작되고 IoT 허브에서 파일 업로드 알림이 도착하길 기다립니다.

    ```cmd/sh
    Receive file upload notifications


    Receiving file upload notification from service
    Press Enter to exit
    ```



1. 디바이스 앱을 실행하여 Azure Storage에 파일을 업로드합니다. Azure IoT C# 샘플을 확장한 폴더에서 새 명령 프롬프트를 열고 폴더를 **azure-iot-samples-csharp-master\iot-hub\Samples\device\FileUploadSample** 로 변경합니다. 다음 명령을 실행합니다. 두 번째 명령의 `{Your device connection string}` 자리 표시자 값을 이전에 [IoT 허브에서 새 디바이스 등록](#register-a-new-device-in-the-iot-hub)에서 복사한 디바이스 연결 문자열로 바꿉니다.

    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```
    
    업로드가 완료되면 디바이스 앱에서 다음 출력이 생성됩니다.
    
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
    
1. 파일 업로드 알림이 수신되었다는 메시지가 서비스 앱에 표시됩니다.

    ```cmd/sh
    Receive file upload notifications
    
    
    Receiving file upload notification from service
    Press Enter to exit
    
    Received file upload notification: myDeviceId/TestPayload.txt
    ```

## <a name="verify-the-file-upload"></a>파일 업로드 확인

포털을 사용하면 구성한 스토리지 컨테이너에 업로드된 파일을 볼 수 있습니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. 스토리지 계정의 왼쪽 창에서 **컨테이너** 를 선택합니다.
1. 파일을 업로드한 컨테이너를 선택합니다.
1. 디바이스 이름을 딴 폴더를 선택합니다.
1. 파일을 업로드한 Blob을 선택합니다. 이 문서에서는 **TestPayload.txt** 라는 이름의 Blob입니다.  

    :::image type="content" source="./media/iot-hub-csharp-csharp-file-upload/view-uploaded-file.png" alt-text="Azure Portal에서 업로드된 파일을 선택하는 스크린샷":::

1. 열리는 페이지에서 Blob 속성을 확인합니다. **다운로드** 를 선택하여 파일을 다운로드하고 로컬에서 파일 내용을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에서 파일 업로드를 단순화하기 위해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 알아보았습니다. 다음 문서에서 이 기능을 계속 탐색할 수 있습니다.

* [IoT Hub를 사용한 파일 업로드 개요](iot-hub-devguide-file-upload.md)

* [IoT Hub 파일 업로드 구성](iot-hub-configure-file-upload.md)

* [Azure Blob Storage 설명서](../storage/blobs/storage-blobs-introduction.md)

* [Azure Blob Storage API 참조](../storage/blobs/reference.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)