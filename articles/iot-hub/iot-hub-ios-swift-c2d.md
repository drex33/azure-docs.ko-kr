---
title: Azure IoT Hub(iOS)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: iOS용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.custom: mqtt
ms.openlocfilehash: 1175c7e7a6e1cd2ce62045328b2590fd82694e00
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133128566"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>IoT Hub(iOS)를 사용하여 클라우드-디바이스 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md) 빠른 시작에서는 IoT Hub를 만들고 그 안에 디바이스 ID를 프로비저닝하고 디바이스-클라우드 메시지를 보내는 시뮬레이션된 디바이스 앱을 코딩하는 방법을 보여 줍니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

[IoT Hub 개발자 가이드의 메시징 섹션](iot-hub-devguide-messaging.md)에서 클라우드-디바이스 메시지에 대한 자세한 내용을 찾아볼 수 있습니다.

이 문서의 끝 부분에서 다음 Swift iOS 프로젝트를 실행 합니다.

* **sample-device** - [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md)에서 만든 앱과 동일한 앱으로, IoT Hub에 연결하고 클라우드-디바이스 메시지를 수신합니다.

> [!NOTE]
> IoT Hub는 Azure IoT 디바이스 SDK를 통해 많은 디바이스 플랫폼 및 언어(C, Java, Python 및 Javascript 포함)를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://www.azure.com/develop/iot)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

* Azure의 활성 IoT Hub.

* [Azure 샘플](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)의 코드 샘플.

* 최신 버전의 iOS SDK를 실행 중인 최신 버전의 [XCode](https://developer.apple.com/xcode/). 이 빠른 시작은 XCode 9.3 및 iOS 11.3에서 테스트되었습니다.

* 최신 버전의 [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="simulate-an-iot-device"></a>IoT 디바이스 시뮬레이션

이 섹션에서는 IoT Hub에서 클라우드-디바이스 메시지를 수신하는 Swift 애플리케이션을 실행하는 iOS 디바이스를 시뮬레이션합니다. 

### <a name="install-cocoapods"></a>CocoaPods 설치

CocoaPods는 타사 라이브러리를 사용 하는 iOS 프로젝트에 대 한 종속성을 관리 합니다.

터미널 창에서 필수 구성 요소에 다운로드한 Azure-IoT-Samples-iOS 폴더로 이동합니다. 그런 다음, 샘플 프로젝트로 이동합니다.

```sh
cd quickstart/sample-device
```

XCode가 닫혀 있는지 확인한 후, 다음 명령을 실행하여 **podfile** 파일에 선언된 CocoaPods를 설치합니다.

```sh
pod install
```

설치 명령은 프로젝트에 필요한 포드를 설치하고, 종속성에 Pod를 사용하도록 이미 구성된 XCode 작업 영역 파일도 만들었습니다.

### <a name="run-the-sample-device-application"></a>샘플 디바이스 애플리케이션 실행

1. 디바이스의 연결 문자열을 검색합니다. 디바이스 세부 정보 블레이드에서 [Azure Portal](https://portal.azure.com)을 통해 이 문자열을 복사하거나 다음 CLI 명령을 사용하여 검색할 수 있습니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

2. XCode에서 샘플 작업 영역을 엽니다.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

3. **MQTT 클라이언트 샘플** 프로젝트를 확장한 다음, 같은 이름의 폴더를 확장합니다.  

4. XCode에서 편집을 위해 **ViewController.swift** 를 엽니다.

5. **connectionString** 변수를 검색하고 첫 번째 단계에서 복사해 둔 디바이스 연결 문자열로 값을 업데이트합니다.

6. 변경 내용을 저장합니다.

7. **빌드 및 실행** 단추 또는 **명령 + r** 키 콤보를 사용하여 디바이스 에뮬레이터에서 프로젝트를 실행합니다.

   ![디바이스 에뮬레이터의 빌드 및 실행 단추를 보여 주는 스크린샷](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이제 클라우드-장치 메시지를 받을 준비가 되었습니다. Azure Portal를 사용 하 여 시뮬레이션 된 IoT 장치로 테스트 클라우드-장치 메시지를 보냅니다.

1. 시뮬레이션된 IoT 디바이스에서 실행되는 **iOS 앱 샘플** 에서 **시작** 을 클릭합니다. 애플리케이션이 디바이스-클라우드 메시지 송신을 시작하고, 그와 동시에 클라우드-디바이스 메시지 수신 대기도 시작합니다.

   ![샘플 IoT 디바이스 앱 보기](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.

3. **장치** 페이지에서 시뮬레이션 된 IoT 장치에 대 한 장치 ID를 선택 합니다.

4. **장치에 메시지** 를 선택 하 여 클라우드-장치 메시지 인터페이스를 엽니다.

5. **메시지 본문** 텍스트 상자에 일반 텍스트 메시지를 작성 한 다음 **메시지 보내기** 를 선택 합니다.

6. 시뮬레이션 된 IoT 장치에서 실행 중인 앱을 시청 하세요. IoT Hub에서 메시지를 확인 하 고 화면에 있는 가장 최근 항목의 텍스트를 인쇄 합니다. 출력은 다음 예제와 비슷합니다.

   ![클라우드-디바이스 메시지 보기](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.