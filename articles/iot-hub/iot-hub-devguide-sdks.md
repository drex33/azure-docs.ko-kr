---
title: Azure IoT Hub SDK | Microsoft Docs
description: 디바이스 앱 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT Hub SDK에 대한 링크입니다.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: b943680f95f4b45b36343ce8e10a7599744b1d0b
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132811204"
---
# <a name="azure-iot-hub-sdks"></a>Azure IoT Hub SDK

IoT Hub를 사용하기 위한 SDK(소프트웨어 개발 키트)의 범주는 두 가지입니다.

* [**IoT Hub Service SDK**](#azure-iot-hub-service-sdks)를 사용하면 백 엔드 애플리케이션을 빌드하여 IoT 허브를 관리하고 필요에 따라 메시지를 보내거나, 작업을 예약하고, 직접 메서드를 호출하거나, IoT 디바이스 또는 모듈에 원하는 속성 업데이트를 보낼 수 있습니다.

* [**IoT Hub 디바이스 SDK**](../iot-develop/about-iot-sdks.md)를 사용하면 디바이스 클라이언트나 모듈 클라이언트를 사용하여 IoT 디바이스에서 실행되는 앱을 빌드할 수 있습니다. 이러한 앱은 IoT Hub로 원격 분석 데이터를 보내고, 필요에 따라 IoT Hub에서 메시지, 작업, 메서드, 쌍 업데이트를 받습니다. 해당 SDK를 사용하여 [Azure IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md) 규칙 및 모델을 사용하는 디바이스 앱을 빌드하여 IoT 플러그 앤 플레이 지원 애플리케이션에 해당 기능을 보급할 수 있습니다. 모듈 클라이언트를 사용하여 [Azure IoT Edge 런타임](../iot-edge/about-iot-edge.md)용 [모듈](../iot-edge/iot-edge-modules.md)을 작성할 수도 있습니다.

또한 [디바이스 프로비저닝 서비스](../iot-dps/about-iot-dps.md)와 함께 작동하는 SDK 세트도 제공됩니다.

* **프로비저닝 디바이스 SDK** 를 사용하면 IoT 디바이스에서 실행되는 앱을 빌드하여 디바이스 프로비저닝 서비스와 통신할 수 있습니다.

* **프로비저닝 서비스 SDK** 를 사용하면 백엔드 애플리케이션을 빌드하여 디바이스 프로비저닝 서비스의 등록을 관리할 수 있습니다.

[Azure IoT SDK를 사용한 개발의 이점](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)을 알아봅니다.

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub 서비스 SDK

Azure IoT 서비스 SDK에는 디바이스와 보안을 관리하기 위해 IoT Hub 서비스를 직접 조작하는 애플리케이션의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

| 플랫폼  | 패키지 | 코드 리포지토리 | 샘플 |  참조 |
|---|---|---|---|---|
| .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [참조](/dotnet/api/microsoft.azure.devices) |
| Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | [참조](/java/api/com.microsoft.azure.sdk.iot.service) |
| 노드 | [npm](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/main/service/samples) | [참조](/javascript/api/azure-iothub/) |
| Python | [pip](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/main/azure-iot-hub/samples) | [참조](/python/api/azure-iot-hub) |
| Node.js | [npm](https://www.npmjs.com/package/azure-iot-common) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/main/service/samples/javascript) | [참조](/javascript/api/azure-iothub/) |

iOS용 Azure IoT Hub 서비스 SDK:

* [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)에서 설치
* [샘플](https://github.com/Azure-Samples/azure-iot-samples-ios)

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure 프로비저닝 SDK

**Microsoft Azure 프로비저닝 SDK** 를 사용하면 [디바이스 프로비저닝 서비스](../iot-dps/about-iot-dps.md)를 통해 디바이스를 IoT Hub에 프로비전할 수 있습니다.

| 플랫폼 | 패키지 | 소스 코드 | 참조 |
| -----|-----|-----|-----|
| .NET|[디바이스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/), [서비스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) |[GitHub](https://github.com/Azure/azure-iot-sdk-csharp/)|[참조](/dotnet/api/microsoft.azure.devices.provisioning.client) |
| C|[apt-get, MBED, Arduino IDE 또는 iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)|[GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning\_client)|[참조](/azure/iot-hub/iot-c-sdk-ref/) |
| Java|[Maven](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-service-sdk)|[GitHub](https://github.com/Azure/azure-iot-sdk-java/blob/main/provisioning)|[참조](/java/api/com.microsoft.azure.sdk.iot.provisioning.device) |
| Node.js|[디바이스 SDK](https://badge.fury.io/js/azure-iot-provisioning-device), [서비스 SDK](https://badge.fury.io/js/azure-iot-provisioning-service) |[GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/main/provisioning)|[참조](/javascript/api/overview/azure/iothubdeviceprovisioning) |
| Python|[디바이스 SDK](https://pypi.org/project/azure-iot-device/), [서비스 SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/)|[GitHub](https://github.com/Azure/azure-iot-sdk-python)|[디바이스 참조](/python/api/azure-iot-device/azure.iot.device.provisioningdeviceclient), [서비스 참조](/python/api/azure-mgmt-iothubprovisioningservices) |

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub 디바이스 SDK

Microsoft Azure IoT 디바이스 SDK에는 Azure IoT Hub 서비스에 연결되고 Azure IoT Hub 서비스에서 관리하는 애플리케이션의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

[IoT 디바이스 개발 설명서](../iot-develop/about-iot-sdks.md)에서 IoT Hub 디바이스 SDK에 대해 자세히 알아보세요.

## <a name="sdk-and-hardware-compatibility"></a>SDK 및 하드웨어 호환성

디바이스 SDK를 선택하는 방법에 대한 자세한 내용은 [Azure IoT 디바이스 SDK 개요](../iot-develop/about-iot-sdks.md)를 참조하세요.

특정 하드웨어 디바이스와 SDK 호환성에 대한 자세한 내용은 [IoT용 Azure Certified 디바이스 카탈로그](https://devicecatalog.azure.com/) 또는 개별 리포지토리를 참조하세요.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="next-steps"></a>다음 단계

Azure IoT SDK를 사용한 개발과 관련된 문서

* IoT Hub 디바이스 SDK를 사용하여 [연결 및 신뢰할 수 있는 메시지를 관리하는 방법](iot-hub-reliability-features-in-sdks.md)에 대해 알아보세요.
* iOS 및 Android와 같은 [모바일 플랫폼용 개발](iot-hub-how-to-develop-for-mobile-devices.md) 방법에 대해 알아보세요.
* [IoT 디바이스 개발 설명서](../iot-develop/about-iot-sdks.md)

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)
* [디바이스 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)
* [할당량 및 제한](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT 지원](iot-hub-mqtt-support.md)
* [IoT Hub REST API 참조](/rest/api/iothub/)
