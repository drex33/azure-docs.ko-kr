---
title: Azure IoT 디바이스 SDK 옵션 개요
description: 개발 역할 및 작업에 따라 사용할 Azure IoT 디바이스 SDK에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: 2374755dc1874b1dcd421daa97fc77535ef99286
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455440"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Azure IoT 디바이스 SDK 개요

Azure IoT 디바이스 SDK는 디바이스 클라이언트 라이브러리, 개발자 가이드, 샘플 및 설명서의 세트입니다. 디바이스 SDK를 사용하면 디바이스를 Azure IoT 서비스에 프로그래밍 방식으로 연결할 수 있습니다.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="다양한 Azure IoT SDK를 보여 주는 다이어그램":::

다이어그램에서 보여 주듯이 디바이스 및 프로그래밍 언어 요구 사항에 맞게 사용할 수 있는 몇 가지 디바이스 SDK가 있습니다. 적절한 디바이스 SDK를 선택하는 방법에 대한 지침은 [어떤 SDK를 사용해야 하나요?](#which-sdk-should-i-use)에서 확인할 수 있습니다. 또한 백 엔드에서 클라우드 기반 애플리케이션을 Azure IoT 서비스와 연결하는 데 사용할 수 있는 Azure IoT 서비스 SDK도 있습니다. 이 문서에서는 디바이스 SDK에 대해 집중적으로 설명하지만, Azure 서비스 SDK는 [여기](#service-sdks)서 자세히 알아볼 수 있습니다.

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Azure IoT 디바이스 SDK를 사용해야 하는 이유는 무엇인가요?

디바이스를 Azure IoT에 연결하려면 사용자 지정 연결 계층을 빌드하거나 Azure IoT 디바이스 SDK를 사용할 수 있습니다. Azure IoT 디바이스 SDK를 사용하면 다음과 같은 몇 가지 이점이 있습니다.

| 개발 비용 &nbsp; &nbsp; &nbsp; &nbsp; | 사용자 지정 연결 계층 | Azure IoT 디바이스 SDK |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| 지원 | 빌드하는 모든 항목을 지원하고 문서화해야 합니다. | Microsoft 지원(GitHub, Microsoft Q&A, Microsoft Docs, 고객 지원 팀)에 액세스할 수 있습니다. |
| 새로운 기능 | 새 Azure 기능을 사용자 지정 미들웨어에 추가해야 합니다. | Microsoft에서 IoT SDK에 지속적으로 추가하는 새로운 기능을 즉시 활용할 수 있습니다. |
| 투자 | 사용자 지정 버전을 설계, 빌드, 테스트 및 유지 관리하기 위해 많은 시간을 포함 개발에 투자합니다. | 무료 오픈 소스 도구를 활용할 수 있습니다. SDK와 관련된 유일한 비용은 학습 곡선입니다. |

## <a name="which-sdk-should-i-use"></a>어떤 SDK를 사용해야 하나요?

Azure IoT 디바이스 SDK는 C, C#, Java, Node.js 및 Python을 포함하여 널리 사용되는 프로그래밍 언어로 사용할 수 있습니다. SDK를 선택할 때 고려해야 할 두 가지 주요 사항은 디바이스 기능 및 프로그래밍 언어에 대한 팀의 친숙도입니다.

### <a name="device-capabilities"></a>장치 접근 권한 값

SDK를 선택하는 경우 사용하는 디바이스의 제한을 고려해야 합니다. 제한된 디바이스는 MCU(단일 마이크로 컨트롤러)와 제한된 메모리가 있는 디바이스입니다. 제한된 디바이스를 사용하는 경우 [Embedded C SDK](#embedded-c-sdk)를 사용하는 것이 좋습니다. 이 SDK는 Azure IoT에 연결하는 데 필요한 최소한의 기능 세트를 제공하도록 설계되었습니다. 또한 포함 디바이스에 가장 최적화된 구성 요소(MQTT 클라이언트, TLS 및 소켓 라이브러리)를 선택할 수도 있습니다. 제한된 디바이스에서 Azure RTOS도 실행하는 경우 Azure RTOS 미들웨어를 사용하여 Azure IoT에 연결할 수 있습니다. Azure RTOS 미들웨어는 Embedded C SDK를 추가 기능으로 래핑하여 Azure RTOS 디바이스를 클라우드에 간단하게 연결합니다.

제한되지 않은 디바이스는 운영 체제를 실행하여 언어 런타임(예: .NET 또는 Python)을 지원할 수 있는 더 강력한 CPU가 있는 디바이스입니다. 제한되지 않은 디바이스를 사용하는 경우 주요 고려 사항은 언어에 대한 친숙도입니다.

### <a name="your-teams-familiarity-with-the-programming-language"></a>프로그래밍 언어에 대한 팀의 친숙도

Azure IoT 디바이스 SDK는 여러 언어로 구현되므로 선호하는 언어를 선택할 수 있습니다. 디바이스 SDK는 다른 친숙한 언어별 도구와도 통합됩니다. 친숙한 개발 언어 및 도구를 사용할 수 있으므로 팀에서 연구, 프로토타입, 제품 개발 및 지속적인 유지 관리의 개발 주기를 최적화할 수 있습니다.

가능하면 개발 팀에 친숙한 SDK를 선택합니다. 모든 Azure IoT SDK는 오픈 소스이며, 특정 SDK에 커밋하기 전에 팀에서 평가하고 테스트할 수 있는 몇 가지 샘플을 제공합니다.

## <a name="how-can-i-get-started"></a>어떻게 시작하나요?

먼저 Azure 디바이스 SDK의 GitHub 리포지토리를 검색합니다. SDK를 사용하여 원격 분석을 Azure IoT에 빠르게 보내는 방법을 보여 주는 [빠른 시작](quickstart-send-telemetry-central.md)을 사용해 볼 수도 있습니다.

시작 옵션은 사용하는 디바이스의 종류에 따라 달라집니다.
- 제한된 디바이스의 경우 [Embedded C SDK](#embedded-c-sdk)를 사용합니다. 
- Azure RTOS에서 실행되는 디바이스의 경우 [Azure RTOS 미들웨어](#azure-rtos-middleware)를 사용하여 개발할 수 있습니다. 
- 제한되지 않은 디바이스의 경우 선택한 언어의 [SDK](#unconstrained-device-sdks)를 선택할 수 있습니다. 

### <a name="constrained-device-sdks"></a>제한된 디바이스 SDK
이러한 SDK는 컴퓨팅 또는 메모리 리소스가 제한된 디바이스에서 실행되도록 특수화되어 있습니다. 일반적인 디바이스 유형에 대한 자세한 내용은 [Azure IoT 디바이스 유형 개요](concepts-iot-device-types.md)를 참조하세요.

#### <a name="embedded-c-sdk"></a>Embedded C SDK
* [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [샘플](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [참조 설명서](https://azure.github.io/azure-sdk-for-c/)
* [Embedded C SDK를 빌드하는 방법](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [제한된 디바이스 크기 차트](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS 미들웨어

* [GitHub 리포지토리](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [시작 가이드](https://github.com/azure-rtos/getting-started) 및 [추가 샘플](https://github.com/azure-rtos/samples)
* [참조 설명서](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>제한되지 않은 디바이스 SDK
이러한 SDK는 상위 언어 런타임을 지원할 수 있는 모든 디바이스에서 실행할 수 있습니다. 여기에는 PC, Raspberry Pi 및 스마트폰과 같은 디바이스가 포함됩니다. 주로 언어별로 차별화되어 있으므로 팀 및 시나리오에 가장 적합한 라이브러리를 선택할 수 있습니다.

#### <a name="c-device-sdk"></a>C 디바이스 SDK
* [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-c)
* [샘플](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [패키지](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [참조 설명서](/azure/iot-hub/iot-c-sdk-ref/)
* [Edge 모듈 참조 설명서](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [C 디바이스 SDK 컴파일](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [다른 플랫폼에 C SDK 이식](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* 다양한 플랫폼에서 교차 컴파일을 수행하고 시작하는 방법에 대한 [개발자 설명서](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc)
* [Azure IoT Hub C SDK 리소스 사용 정보](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C# 디바이스 SDK

* [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-csharp)
* [샘플](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [패키지](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [참조 설명서](/dotnet/api/microsoft.azure.devices)
* [Edge 모듈 참조 설명서](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java 디바이스 SDK

* [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-java)
* [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples)
* [패키지](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-device-sdk)
* [참조 설명서](/java/api/com.microsoft.azure.sdk.iot.device)
* [Edge 모듈 참조 설명서](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js 디바이스 SDK

* [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-node)
* [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [패키지](https://www.npmjs.com/package/azure-iot-device)
* [참조 설명서](/javascript/api/azure-iot-device/)
* [Edge 모듈 참조 설명서](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python 디바이스 SDK

* [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-python)
* [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [패키지](https://pypi.org/project/azure-iot-device/)
* [참조 설명서](/python/api/azure-iot-device)
* [Edge 모듈 참조 설명서](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>서비스 SDK
Azure IoT도 디바이스를 관리하고, 인사이트를 얻고, 데이터를 시각화하는 등의 솔루션 쪽 애플리케이션을 빌드할 수 있는 서비스 SDK를 제공합니다. 이러한 SDK는 각 Azure IoT 서비스에 고유하며, C#, Java, JavaScript 및 Python에서 개발 환경을 간소화하는 데 사용할 수 있습니다. 

#### <a name="iot-hub"></a>IoT Hub

IoT Hub 서비스 SDK를 사용하면 IoT Hub와 쉽게 상호 작용하여 디바이스 및 보안을 관리하는 애플리케이션을 빌드할 수 있습니다. 이러한 SDK를 사용하여 클라우드-디바이스 메시지를 보내고, 디바이스에서 직접 메서드를 호출하고, 디바이스 속성을 업데이트하는 등의 작업을 수행할 수 있습니다.

[**IoT Hub에 대한 자세한 정보**](https://azure.microsoft.com/services/iot-hub/) | [**디바이스 제어 사용해 보기**](../iot-hub/quickstart-control-device.md)

**C# IoT Hub 서비스 SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/iothub/service) | [패키지](https://www.nuget.org/packages/Microsoft.Azure.Devices/) | [샘플](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/iothub/service/samples) | [참조 설명서](/dotnet/api/microsoft.azure.devices)

**Java IoT Hub 서비스 SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-java/tree/main/service) | [패키지](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-service-sdk) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples) | [참조 설명서](/java/api/com.microsoft.azure.sdk.iot.service)

**JavaScript IoT Hub 서비스 SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-node/tree/master/service) | [패키지](https://www.npmjs.com/package/azure-iothub) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [참조 설명서](/javascript/api/azure-iothub/)

**Python IoT Hub 서비스 SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub) | [패키지](https://pypi.python.org/pypi/azure-iot-hub/) | [샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [참조 설명서](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins는 전체 환경의 디지털 모델을 기반으로 기술 자료 그래프를 만들 수 있는 PaaS(Platform as a Service) 제품입니다. 이러한 환경에는 건물, 공장, 농장, 에너지 네트워크, 철로, 경기장 - 심지어 도시 전체가 포함될 수 있습니다. 이러한 디지털 모델을 사용하 여 더 나은 제품, 최적화된 작업, 비용 절감 및 혁신적인 고객 환경을 구동하는 정보를 얻을 수 있습니다. Azure IoT는 Azure Digital Twins의 강력한 기능을 사용하는 애플리케이션을 쉽게 빌드할 수 있는 서비스 SDK를 제공합니다.

[**Azure Digital Twins에 대한 자세한 정보**](https://azure.microsoft.com/services/digital-twins/) | [**ADT 애플리케이션 코딩**](../digital-twins/tutorial-code.md)

**C# ADT 서비스 SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) | [패키지](https://www.nuget.org/packages/Azure.DigitalTwins.Core) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples) | [참조 설명서](/dotnet/api/overview/azure/digitaltwins/client)

**Java ADT 서비스 SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [패키지](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples) | [참조 설명서](/java/api/overview/azure/digitaltwins/client)

**Node.js ADT 서비스 SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core) | [패키지](https://www.npmjs.com/package/@azure/digital-twins-core) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples) | [참조 설명서](/javascript/api/@azure/digital-twins-core/)

**Python ADT 서비스 SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [패키지](https://pypi.org/project/azure-digitaltwins-core/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples) | [참조 설명서](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub DPS(Device Provisioning Service)는 IoT Hub용 도우미 서비스로, 사용자 개입 없이 적합한 IoT 허브에 자동 Just-In-Time 프로비저닝을 수행할 수 있습니다. DPS를 사용하면 수백만 개의 디바이스를 안전하고 확장 가능한 방식으로 프로비저닝할 수 있습니다. DPS 서비스 SDK를 사용하면 등록 그룹을 만들고 대량 작업을 수행하여 디바이스를 안전하게 관리할 수 있는 애플리케이션을 빌드할 수 있습니다.

[**Device Provisioning Service에 대한 자세한 정보**](../iot-dps/index.yml) | [**X.509 디바이스에 대한 등록 그룹 만들기 사용해 보기**](../iot-dps/quick-enroll-device-x509.md)

**C# Device Provisioning Service SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/provisioning/service) | [패키지](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) | [샘플](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/provisioning/service/samples) | [참조 설명서](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Java Device Provisioning Service SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-java/tree/main/provisioning/provisioning-service-client/src) | [패키지](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) | [샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client) | [참조 설명서](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Node.js Device Provisioning Service SDK**: [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service) | [패키지](https://www.npmjs.com/package/azure-iot-provisioning-service) | [샘플](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples) | [참조 설명서](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>다음 단계

* [빠른 시작: IoT Central로 원격 분석 전송](quickstart-send-telemetry-central.md)
* [빠른 시작: IoT Hub로 원격 분석 전송](quickstart-send-telemetry-iot-hub.md)
* [빠른 시작: MXCHIP AZ3166 DevKit를 IoT Central에 연결](quickstart-devkit-mxchip-az3166.md)
* [Azure IoT SDK를 사용한 개발의 이점](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)에 대해 자세히 알아보기