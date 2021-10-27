---
title: Azure IoT 디바이스 및 애플리케이션 개발 소개
description: Azure IoT를 사용하여 임베디드 디바이스 개발을 수행하고 디바이스 지원 클라우드 애플리케이션을 빌드하는 방법을 알아봅니다.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: 128bea34a2e391a44b4dba68d844c68f7331cb06
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994540"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Azure IoT 디바이스 및 애플리케이션 개발이란?

Azure IoT는 IoT 디바이스를 연결, 모니터링 및 제어하는 관리형 서비스 및 플랫폼 서비스의 모음입니다. Azure IoT는 개발자에게 포괄적인 옵션 세트를 제공합니다. 이러한 옵션에는 디바이스 플랫폼, 지원 클라우드 서비스, SDK 및 디바이스 사용 클라우드 애플리케이션을 빌드하기 위한 도구가 포함됩니다.

이 문서에서는 Azure IoT를 시작하는 개발자를 위한 몇 가지 주요 고려 사항을 간략하게 설명합니다. 이러한 개념은 IoT 디바이스 개발자에게 Azure IoT 옵션 및 이를 시작하는 방법을 안내합니다. 특히 이 문서에서는 다음과 같은 개념에 대해 간략하게 설명합니다.
- [디바이스 개발 역할 이해](#device-development-roles)
- [하드웨어 선택](#choosing-your-hardware)
- [SDK 선택](#choosing-an-sdk)
- [연결 옵션 선택](#selecting-connection-options)

## <a name="device-development-roles"></a>디바이스 개발 역할
이 문서에서는 디바이스 개발자로부터 관찰할 수 있는 일반적인 두 가지 역할에 대해 설명합니다. 여기서 사용되는 것처럼 역할은 관련된 개발 작업의 모음입니다. 현재 작업 중인 개발 역할의 유형을 이해하는 것이 유용합니다. 사용자의 역할은 사용자가 선택하는 많은 개발에 영향을 줍니다.

* **디바이스 애플리케이션 개발:** 최신 개발 사례에 맞추고, 많은 상위 언어를 대상으로 하며, Windows 또는 Linux와 같은 범용 운영 체제에서 실행됩니다.

* **포함 디바이스 개발:** 리소스가 제한된 디바이스를 대상으로 하는 개발을 설명합니다. 리소스가 제한된 디바이스는 단가, 전력 소비 또는 디바이스 크기를 줄이는 데 사용되는 경우가 많습니다. 이러한 디바이스는 실행되는 하드웨어 플랫폼을 직접 제어합니다.

### <a name="device-application-development"></a>디바이스 애플리케이션 개발
디바이스 애플리케이션 개발자는 클라우드에 연결하고 IoT 솔루션에 통합하도록 기존 디바이스를 조정하고 있습니다. 이러한 디바이스는 C# 또는 Python과 같은 상위 언어를 지원할 수 있으며, Windows 또는 Linux와 같은 강력한 범용 운영 체제를 지원하는 경우가 많습니다. 일반적인 대상 디바이스에는 PC, 컨테이너, Raspberry Pi 및 모바일 디바이스가 포함됩니다. 

이러한 개발자는 제한된 디바이스를 규모에 맞게 개발하는 대신, 클라우드 솔루션에 필요한 특정 IoT 시나리오를 사용하도록 설정하는 데 중점을 둡니다. 이러한 개발자 중 일부는 클라우드 솔루션에 맞는 제한된 디바이스에 집중하고 있습니다. 리소스가 제한된 디바이스를 사용하는 개발자의 경우 아래의 [임베디드 디바이스 개발](#embedded-device-development) 경로를 참조하세요.

> [!IMPORTANT]
> 시작하려면 [디바이스 SDK](about-iot-sdks.md#device-sdks)를 참조하세요.

### <a name="embedded-device-development"></a>포함 디바이스 개발
포함 개발은 메모리와 처리가 제한되는 제한된 디바이스를 대상으로 합니다. 제한된 디바이스는 기존 개발 플랫폼과 비교하여 달성할 수 있는 항목을 제한합니다.

포함 디바이스는 일반적으로 RTOS(실시간 운영 체제)를 사용하거나 운영 체제를 전혀 사용하지 않습니다. 범용 운영 체제가 없으므로 포함 디바이스는 하드웨어를 완전히 제어할 수 있습니다. 이러한 사실로 인해 포함 디바이스가 실시간 시스템에 적합합니다.

현재 포함 SDK는 **C** 언어를 대상으로 합니다. 포함 SDK는 운영 체제 또는 Azure RTOS 지원을 제공하지 않습니다. 이러한 SDK는 포함 대상을 고려하여 설계됩니다. 설계 고려 사항에는 최소 메모리 공간에 대한 필요성 및 메모리를 할당하지 않는 디자인이 포함됩니다.

디바이스에서 범용 운영 체제를 실행할 수 있는 경우 [디바이스 애플리케이션 개발](#device-application-development) 경로를 따르는 것이 좋습니다. 다양한 개발 옵션 세트를 제공합니다.

> [!IMPORTANT]
> 시작하려면 [임베디드 디바이스 SDK](about-iot-sdks.md#embedded-device-sdks)를 참조하세요.

## <a name="choosing-your-hardware"></a>하드웨어 선택
Azure IoT 디바이스는 IoT 솔루션의 기본 구성 요소이며, 환경을 관찰하고 상호 작용하는 역할을 수행합니다. 다양한 유형의 IoT 디바이스가 있으며, 존재하는 디바이스의 종류와 이러한 디바이스가 개발 프로세스에 미치는 영향을 이해하는 것이 좋습니다.

이 문서에서 설명하는 디바이스 유형 간의 차이점에 대한 자세한 내용은 [IoT 디바이스 유형 개요](concepts-iot-device-types.md)를 참조하세요.

## <a name="choosing-an-sdk"></a>SDK 선택
Azure IoT 디바이스 개발자는 디바이스 사용 클라우드 애플리케이션을 빌드하는 데 도움이 되는 디바이스 SDK 및 Azure 서비스 SDK의 다양한 세트를 사용할 수 있습니다. SDK는 개발 작업을 간소화하고 디바이스 연결 및 관리의 복잡성을 크게 간소화합니다. 

[디바이스 개발 역할](#device-development-roles) 섹션에서 설명한 대로 세 가지 종류의 디바이스 개발용 IoT SDK가 있습니다.
- 포함 디바이스 SDK(제한된 디바이스용)
- 디바이스 SDK(상위 언어를 사용하여 IoT 애플리케이션에 기존 디바이스 연결용)
- 서비스 SDK(디바이스를 서비스에 연결하는 Azure IoT 솔루션 빌드용)

Azure IoT 디바이스 또는 서비스 SDK를 선택하는 방법에 대한 자세한 내용은 [Azure IoT 디바이스 SDK 개요](about-iot-sdks.md)를 참조하세요.

## <a name="selecting-connection-options"></a>연결 옵션 선택
개발 프로세스에서 중요한 단계는 디바이스를 연결하고 관리하는 데 사용할 옵션 세트를 선택하는 것입니다. 고려해야 할 두 가지 중요한 측면은 다음과 같습니다.
- 디바이스를 호스팅할 IoT 애플리케이션 플랫폼 선택. Azure IoT의 경우 IoT Hub 또는 IoT Central을 선택하는 것을 의미합니다.
- 디바이스를 연결, 관리 및 모니터링하는 데 도움이 되는 개발자 도구 선택

애플리케이션 플랫폼 및 도구를 선택하는 방법에 대한 자세한 내용은 [개요: Azure IoT 디바이스 개발자를 위한 연결 옵션](concepts-overview-connection-options.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
개발 역할에 가장 적합한 포함된 개발 또는 애플리케이션 개발 빠른 시작 시리즈 중 하나를 선택합니다. 이러한 문서에서는 디바이스를 호스팅할 Azure IoT 애플리케이션을 만들고, SDK를 사용하고, 디바이스를 연결하고, 원격 분석을 보내는 작업의 기본 사항에 대해 설명합니다.  
- [Azure IoT 디바이스 개발](index.yml)
