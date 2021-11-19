---
title: IoT 플러그 앤 플레이 라이브러리 및 SDK
description: IoT 플러그 앤 플레이 지원 솔루션을 개발하는 데 사용할 수 있는 디바이스 및 서비스 라이브러리에 대한 정보입니다.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-develop
services: iot-develop
ms.custom: mvc
ms.openlocfilehash: 213af02dfdfd67c63dc9470218bb08dd07a1aad9
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132810143"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>IoT 플러그 앤 플레이용 Microsoft SDK

IoT 플러그 앤 플레이 라이브러리 및 SDK를 통해 개발자는 여러 플랫폼에서 다양한 프로그래밍 언어를 사용하여 IoT 솔루션을 빌드할 수 있습니다. 다음 표에는 시작하는 데 도움이 되는 샘플 및 빠른 시작에 대한 링크가 포함되어 있습니다.

## <a name="device-sdks"></a>디바이스 SDK

| 언어 | 패키지 | 코드 리포지토리 | 샘플 | 빠른 시작 | 참조 |
|---|---|---|---|---|---|
| C - 디바이스 | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [샘플](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [IoT Hub에 연결](tutorial-connect-device.md) | [참조](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET - 디바이스 | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/) | [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples) | [IoT Hub에 연결](tutorial-connect-device.md) | [참조](/dotnet/api/microsoft.azure.devices.client) |
| Java - 디바이스 | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/main/) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample) | [IoT Hub에 연결](tutorial-connect-device.md) | [참조](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python - 디바이스 | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/main/azure-iot-device/samples/pnp) | [IoT Hub에 연결](tutorial-connect-device.md) | [참조](/python/api/azure-iot-device/azure.iot.device) |
| Node - 디바이스 | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/main/device/samples/javascript/pnp) | [IoT Hub에 연결](tutorial-connect-device.md) | [참조](/javascript/api/azure-iot-device/) |
| 임베디드 C - 디바이스 | 해당 없음 | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [샘플](tutorial-connect-device.md?pivots=programming-language-embedded-c#samples) | [임베디드 C를 사용하는 방법](tutorial-connect-device.md?pivots=programming-language-embedded-c) | 해당 없음

## <a name="service-sdks"></a>서비스 SDK

| 플랫폼  | 패키지 | 코드 리포지토리 | 샘플 | 빠른 시작 | 참조 |
|---|---|---|---|---|---|
| .NET - IoT Hub 서비스 | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/service/PnpServiceSamples) | 해당 없음 | [참조](/dotnet/api/microsoft.azure.devices) |
| Java - IoT Hub 서비스 | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | 해당 없음 | [참조](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node - IoT Hub 서비스 | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/main/service/samples) | 해당 없음 | [참조](/javascript/api/azure-iothub/) |
| Python - Digital Twins 서비스 | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/main/azure-iot-hub/samples) | [IoT Hub Digital Twins API와 상호 작용](tutorial-service.md) | 해당 없음 |
| Node - Digital Twins 서비스 | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/main/service/samples/javascript) | [IoT Hub Digital Twins API와 상호 작용](tutorial-service.md) | 해당 없음 |

## <a name="next-steps"></a>다음 단계

SDK 및 라이브러리를 사용해 보려면 [개발자 가이드](concepts-developer-guide-device.md), [디바이스 자습서](tutorial-connect-device.md) 및 [서비스 자습서](tutorial-service.md)를 참조하세요.
