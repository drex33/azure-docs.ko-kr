---
title: Azure IoT SDK를 사용하여 모바일 디바이스용 솔루션 개발 | Microsoft Docs
description: 개발자 가이드 - Azure IoT Hub SDK를 사용하여 모바일 디바이스용 솔루션을 개발하는 방법을 알아봅니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: robinsh
ms.openlocfilehash: 6b086e92f36873c28f4e1d88c9a73f54fd4b20d3
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287787"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Azure IoT SDK를 사용하여 모바일 디바이스용 솔루션 개발

사물 인터넷에는 센서, 마이크로컨트롤러, 스마트 디바이스, 산업용 게이트웨이, 모바일 디바이스 등 여러 가지 용량의 다양한 디바이스가 사용될 수 있습니다.  모바일 디바이스는 디바이스-클라우드 원격 분석 데이터를 전송하고 클라우드를 통해 관리되는 IoT 디바이스가 될 수 있습니다.  백 엔드 서비스 애플리케이션을 실행하고 다른 IoT 디바이스를 관리하는 디바이스가 될 수도 있습니다.  두 경우 모두 [Azure IoT Hub SDK](./iot-hub-devguide-sdks.md)를 사용하여 모바일 디바이스에서 작동하는 애플리케이션을 개발할 수 있습니다.  

## <a name="develop-for-native-ios-platform"></a>네이티브 iOS 플랫폼용 개발

Azure IoT Hub SDK는 Azure IoT Hub C SDK를 통해 네이티브 iOS 플랫폼 지원을 제공합니다.  Swift 또는 Objective C XCode 프로젝트에 통합할 수 있는 iOS SDK라고 생각하셔도 좋습니다.  iOS에서 C SDK를 사용하는 두 가지 방법이 있습니다.

* XCode 프로젝트에서 CocoaPod 라이브러리를 직접 사용합니다.  
* C SDK에 대한 소스 코드를 다운로드하고 macOS에 대한 [빌드 지침](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)에 따라 iOS 플랫폼용으로 빌드합니다.  

Azure IoT Hub C SDK는 다양한 플랫폼으로 이식할 수 있도록 C99로 작성됩니다.  이식 프로세스에는 플랫폼별 구성 요소에 대한 씬 채택 레이어를 작성하는 과정이 포함되며, 자세한 내용은 [iOS](https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx)에서 확인할 수 있습니다.  지원되는 Azure IoT Hub 기본 요소와 네트워크 안정성을 위한 재시도 정책 같은 SDK 관련 기능을 포함하여 C SDK의 기능을 iOS 플랫폼에서 활용할 수 있습니다.  iOS SDK의 인터페이스는 Azure IoT Hub C SDK의 인터페이스와도 유사합니다.  

다음 설명서는 iOS 디바이스에서 디바이스 애플리케이션 또는 서비스 애플리케이션을 개발하는 방법을 안내합니다.

* [빠른 시작: 디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md)  
* [IoT Hub를 사용하여 클라우드에서 디바이스로 메시지 보내기](iot-hub-ios-swift-c2d.md) 

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Azure IoT Hub CocoaPod 라이브러리를 사용하여 개발

Azure IoT Hub SDK는 iOS 개발을 위한 Objective-C CocoaPod 라이브러리 집합을 릴리스합니다.  최신 CocoaPod 라이브러리 목록은 [Microsoft Azure IoT용 CocoaPod](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md)를 참조하세요.  관련 라이브러리가 XCode 프로젝트에 통합되면 두 가지 방법으로 IoT Hub 관련 코드를 작성할 수 있습니다.

* Objective C 함수: 프로젝트가 Objective-C로 작성되는 경우 Azure IoT Hub C SDK에서 직접 API를 호출할 수 있습니다.  프로젝트가 Swift로 작성되는 경우 함수를 만들기 전에 `@objc func`를 호출하고, C 또는 Objective-C 코드를 사용하여 Azure IoT Hub와 관련된 모든 논리를 작성합니다.  두 가지 방법을 보여주는 샘플 집합은 [샘플 리포지토리](https://github.com/Azure-Samples/azure-iot-samples-ios)에서 찾을 수 있습니다.  

* C 샘플 통합: C 디바이스 애플리케이션을 작성한 경우 XCode 프로젝트에서 해당 애플리케이션을 직접 참조할 수 있습니다.
    * XCode에서 sample.c 파일을 XCode 프로젝트에 추가합니다.  
    * 헤더 파일을 종속성에 추가합니다.  헤더 파일은 [샘플 리포지토리](https://github.com/Azure-Samples/azure-iot-samples-ios)에 예제로 포함됩니다. 자세한 내용을 보려면 [Objective-C](https://developer.apple.com/documentation/objectivec)에 대한 Apple의 설명서 페이지를 방문하세요.

## <a name="develop-for-android-platform"></a>Android 플랫폼용 개발
Azure IoT Hub Java SDK는 Android 플랫폼을 지원합니다.

다음 설명서는 Android 디바이스에서 Gradle 및 Android Studio를 사용하여 디바이스 애플리케이션 또는 서비스 애플리케이션을 개발하는 방법을 안내합니다.

* [빠른 시작: 디바이스에서 IoT Hub로 원격 분석 데이터 보내기](../iot-develop/quickstart-send-telemetry-iot-hub.md)  
* [빠른 시작: 디바이스 제어](quickstart-control-device-android.md) 

## <a name="next-steps"></a>다음 단계

* [IoT Hub REST API 참조](/rest/api/iothub/)
* [Azure IoT C SDK 소스 코드](https://github.com/Azure/azure-iot-sdk-c)