---
title: Azure IoT 디바이스 SDK 옵션 개요
description: 개발 역할 및 작업에 따라 사용할 Azure IoT 디바이스 SDK에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: e4f5d99caadaedf6389215fccb753aedd6620917
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129983972"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Azure IoT 디바이스 SDK 개요

Azure IoT 디바이스 SDK에는 디바이스 클라이언트 라이브러리, 샘플 및 설명서 세트가 포함되어 있습니다. 디바이스 SDK는 프로그래밍 방식으로 디바이스를 Azure IoT에 연결하는 프로세스를 간소화합니다. SDK는 임베디드 디바이스를 위한 여러 RTOS를 지원하는 다양한 프로그램 언어로 제공됩니다.

## <a name="why-use-an-azure-iot-device-sdk"></a>Azure IoT 디바이스 SDK를 사용하는 이유는 무엇인가요?

사용자 지정 연결 계층을 빌드하는 것에 비해 Azure IoT 디바이스 SDK를 사용할 경우의 이점은 다음과 같습니다.

| | 사용자 지정 연결 계층 | Azure IoT 디바이스 SDK |
| :-- | :-- | :-- |
| **지원** | 솔루션을 지원하고 문서화해야 합니다. | Microsoft 지원(GitHub, Microsoft Q&A, Microsoft Docs, 고객 지원 팀)에 액세스 |
| **새 기능** | 새 Azure 기능을 수동으로 추가해야 합니다. | 추가된 새 기능을 즉시 활용할 수 있습니다. |
| **투자** | 사용자 지정 버전을 설계, 빌드, 테스트 및 유지 관리하기 위해 많은 시간을 포함 개발에 투자합니다. | 무료 오픈 소스 도구를 활용할 수 있습니다. SDK와 관련된 유일한 비용은 학습 곡선입니다. |

## <a name="which-sdk-should-i-use"></a>어떤 SDK를 사용해야 하나요?

SDK를 선택할 때의 주요 고려 사항은 디바이스 고유의 하드웨어입니다. PC 및 휴대폰과 같은 일반 컴퓨팅 디바이스는 MPU(마이크로프로세서 단위)를 포함하며 컴퓨팅 및 메모리 리소스가 비교적 큽니다. 센서 또는 기타 특수 용도 역할로 사용되는 특수한 클래스의 디바이스는 MCU(마이크로 컨트롤러 단위)를 포함하며 컴퓨팅 및 메모리 리소스가 상대적으로 제한되어 있습니다. 리소스가 제한된 이러한 디바이스에는 특수 개발 도구 및 SDK가 필요합니다. 다음 표에는 다양한 디바이스 클래스와 디바이스 개발에 사용할 SDK가 요약되어 있습니다.

|디바이스 클래스|Description|예제|SDK|
|-|-|-|-|
|Embedded 디바이스|컴퓨팅 및 메모리 제한이 있는 특수 용도 MCU 기반 디바이스|센서|[Embedded 디바이스 SDK](#embedded-device-sdks)|
|기타|대용량 컴퓨팅 및 메모리 리소스가 있는 범용 MPU 기반 디바이스 포함|PC, 스마트폰, Raspberry Pi|[디바이스 SDK](#device-sdks)|

> [!Note] 
> 디바이스에 가장 적합한 SDK를 선택할 수 있도록 다양한 디바이스 범주에 대한 자세한 내용은 [Azure IoT 디바이스 유형](concepts-iot-device-types.md)을 참조하세요.

## <a name="device-sdks"></a>디바이스 SDK

이러한 SDK는 PC, 태블릿, 스마트폰 또는 Raspberry Pi와 같은 일반적인 MPU 기반 컴퓨팅 디바이스에서 실행할 수 있습니다. SDK는 C 및 C#, Node.JS, Python, Java를 포함한 최신 관리 언어로 개발을 지원합니다.

SDK는 **여러 언어** 로 제공되며 팀 및 시나리오에 가장 적합한 것을 유연하게 선택할 수 있습니다.

| 언어 | 패키지 | 원본 | 빠른 시작 | 샘플 | 참조 |
| :-- | :-- | :-- | :-- | :-- | :-- |
| **.NET** | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-csharp) | [샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [참조](/dotnet/api/microsoft.azure.devices.client) |
| **Python** | [pip](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-python) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | [참조](/python/api/azure-iot-device) |
| **Node.JS** | [npm](https://www.npmjs.com/package/azure-iot-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-nodejs) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) | [참조](/javascript/api/azure-iot-device/) |
| **Java** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-java) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples) | [참조](/java/api/com.microsoft.azure.sdk.iot.device) |
| **C** | [packages](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#getting-the-sdk) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-ansi-c) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-ansi-c) | [샘플](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) | [참조](/azure/iot-hub/iot-c-sdk-ref/) |

> [!WARNING]
> 위에 나열된 **C SDK** 는 메모리 관리 및 스레딩 모델로 인해 포함된 애플리케이션에 적합하지 **않습니다**. 임베디드 디바이스의 경우 [Embedded 디바이스 SDK](#embedded-device-sdks)를 참조하세요.

## <a name="embedded-device-sdks"></a>Embedded 디바이스 SDK

이러한 SDK는 컴퓨팅 및 메모리 리소스가 제한된 디바이스에서 실행되도록 설계 및 생성되었으며 C 언어를 사용하여 구현됩니다.

임베디드 디바이스 SDK는 팀 및 시나리오에 가장 적합한 것을 선택할 수 있는 유연성을 제공하는 **여러 운영 체제** 에서 사용할 수 있습니다.

| RTOS | SDK) | 원본 | 샘플 | 참조 |
| :-- | :-- | :-- | :-- | :-- | 
| **Azure RTOS** | Azure RTOS 미들웨어 | [GitHub](https://github.com/azure-rtos/netxduo) | [빠른 시작](quickstart-devkit-mxchip-az3166.md) | [참조](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot) | 
| **FreeRTOS** | FreeRTOS 미들웨어 | [GitHub](https://github.com/Azure/azure-iot-middleware-freertos) | [샘플](https://github.com/Azure-Samples/iot-middleware-freertos-samples) | [참조](https://azure.github.io/azure-iot-middleware-freertos) |
| **운영 체제 미설치** | Embedded C용 Azure SDK | [GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot) | [샘플](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md) | [참조](https://azure.github.io/azure-sdk-for-c) |

## <a name="next-steps"></a>다음 단계

Azure IoT 디바이스 SDK를 사용하여 일반적인 MPU 기반 디바이스를 연결하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [빠른 시작: IoT Central로 원격 분석 전송](quickstart-send-telemetry-central.md)
* [빠른 시작: IoT Hub로 원격 분석 전송](quickstart-send-telemetry-iot-hub.md)

Azure IoT 임베디드 디바이스 SDK를 사용하여 리소스 제한된 MCU 기반 디바이스를 연결하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.
* [빠른 시작: MXCHIP AZ3166 DevKit를 IoT Central에 연결](quickstart-devkit-mxchip-az3166.md)
