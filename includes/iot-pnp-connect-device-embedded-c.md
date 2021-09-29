---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: c4e0070a9647412a873953af59338b13f4c653fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908620"
---
*제한된 디바이스* 용으로 개발 중인 경우 다음을 통해 IoT 플러그 앤 플레이를 사용할 수 있습니다.

- [Embedded C IoT용 Azure SDK 클라이언트 라이브러리](https://aka.ms/embeddedcsdk).
- [Azure RTOS](/azure/rtos/overview-rtos).

이 문서에는 이러한 제한된 시나리오에 대한 링크와 리소스가 포함되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

아래 샘플은 대부분은 특정 하드웨어 디바이스를 필요로 하며 각 샘플 마다 필수 구성 요소가 다릅니다. 자세한 필수 구성 요소, 구성 및 빌드 지침은 관련 샘플에 대한 링크를 따르세요.

## <a name="use-the-sdk-for-embedded-c"></a>Embedded C용 SDK 사용

Embedded C용 SDK는 [IoT 플러그 앤 플레이 규칙](../articles/iot-develop/concepts-convention.md)을 사용하여 제한된 디바이스를 Azure IoT 서비스에 연결하는 간단한 솔루션을 제공합니다. 다음 링크에는 MCU 기반 디바이스, 교육, 디버깅을 위한 샘플이 포함되어 있습니다.

### <a name="use-an-mcu-based-device"></a>MCU 기반 디바이스 사용

MCU에서 Embedded C용 SDK, Device Provisioning Service, IoT 플러그 앤 플레이를 사용하는 전체 엔드투엔드 자습서는 [IoT Hub Device Provisioning Service를 통해 Azure에 연결할 PIC-IoT Wx 개발 보드 용도 변경](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)을 참조하세요.

### <a name="introductory-samples"></a>소개 샘플

Embedded C용 SDK 리포지토리에는 IoT 플러그 앤 플레이를 사용하는 방법을 보여주는 [몇 가지 샘플](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample)이 포함되어 있습니다.

> [!NOTE]
> 이러한 샘플은 교육 및 디버깅을 목적으로 Windows 및 Linux에서 실행되는 것으로 표시됩니다. 프로덕션 시나리오에서 샘플은 제한된 디바이스에서만 사용됩니다.

- [Embedded C용 SDK를 사용하는 자동 온도 조절기 예제](https://github.com/Azure/azure-sdk-for-c/blob/main/sdk/samples/iot/paho_iot_pnp_sample.c)

- [Embedded C용 SDK를 사용하는 온도 컨트롤러 예제](https://github.com/Azure/azure-sdk-for-c/blob/main/sdk/samples/iot/paho_iot_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Azure RTOS 사용

Azure RTOS에는 Azure IoT 클라우드 서비스에 기본 연결을 추가하는 경량 계층이 포함되어 있습니다. 이 계층은 Azure RTOS의 고급 기능을 사용하는 동안 제한된 디바이스를 Azure IoT에 연결하는 간단한 메커니즘을 제공합니다. 자세히 알아보려면 [Microsoft Azure RTOS란?](/azure/rtos/overview-rtos)을 참조하세요.

### <a name="toolchains"></a>도구 체인

Azure RTOS 샘플에는 다음과 같은 다양의 종류의 IDE 및 도구 체인 조합이 제공됩니다.

- IAR: IAR의 [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IDE
- GCC/CMake: 오픈 소스 [CMake](https://cmake.org/) 빌드 시스템 및 [GNU Arm Embedded 도구 체인](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm) 위에 빌드합니다.
- MCUExpresso: NXP의 [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE)
- STM32Cube: STMicroelectronics의 [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- MPLAB: Microchip의 [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>샘플

다음 표에는 Azure RTOS 및 IoT 플러그 앤 플레이를 사용하여 다른 디바이스에서 시작하는 방법을 보여 주는 샘플이 나와 있습니다.

제조업체 | 디바이스 | 샘플 |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](../articles/iot-develop/quickstart-devkit-mxchip-az3166.md) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
