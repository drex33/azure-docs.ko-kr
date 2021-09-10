---
title: Azure IoT Hub 에이전트의 디바이스 업데이트 이해 | Microsoft Docs
description: Azure IoT Hub 에이전트의 디바이스 업데이트 이해
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6502779c4d301e760e10be8cf1a1766834ec2038
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122604153"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>IoT Hub 에이전트에 대한 디바이스 업데이트 개요

디바이스 업데이트 에이전트는 다음과 같은 두 가지 개념적 계층으로 구성됩니다.

* 인터페이스 계층은 디바이스가 디바이스 업데이트 에이전트와 디바이스 업데이트 서비스 간에 전달될 수 있도록 하는 [Azure IoT 플러그 앤 플레이](../iot-develop/overview-iot-plug-and-play.md)를 기반으로 빌드됩니다.
* 플랫폼 계층은 플랫폼 또는 디바이스별 다운로드, 설치 및 적용의 상위 수준 업데이트 작업을 담당합니다.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="에이전트 구현." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>인터페이스 계층

인터페이스 계층은 [ADU Core 인터페이스](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) 및 [디바이스 정보 인터페이스](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)로 구성됩니다.

이러한 인터페이스는 기본값에 대한 구성 파일을 사용합니다. 기본값에는 AzureDeviceUpdateCore 인터페이스에 대한 aduc_manufacturer 및 aduc_model과 DeviceInformation 인터페이스의 모델 및 제조업체가 포함됩니다. 구성 파일에 대해 [자세히 알아보세요](device-update-configuration-file.md).

### <a name="adu-core-interface"></a>ADU Core 인터페이스

'ADU Core' 인터페이스는 디바이스 업데이트 에이전트와 서비스 간의 기본 통신 채널입니다. 이 인터페이스에 대해 [자세히 알아보세요](device-update-plug-and-play.md#adu-core-interface).

### <a name="device-information-interface"></a>디바이스 정보 인터페이스

디바이스 정보 인터페이스는 `Azure IoT PnP DeviceInformation` 인터페이스를 구현하는 데 사용됩니다. 이 인터페이스에 대해 [자세히 알아보세요](device-update-plug-and-play.md#device-information-interface).

## <a name="the-platform-layer"></a>플랫폼 계층

플랫폼 계층에는 두 가지 구현이 있습니다. 시뮬레이터 플랫폼 계층은 업데이트 작업을 간단하게 구현하며 Device Update for IoT Hub 서비스 및 설치를 신속하게 테스트하고 평가하는 데 사용됩니다. 시뮬레이터 플랫폼 계층을 사용하여 디바이스 업데이트 에이전트를 빌드하는 경우, 이를 디바이스 업데이트 시뮬레이터 에이전트 또는 시뮬레이터로 지칭합니다. 시뮬레이터 에이전트를 사용하는 방법에 대해 [자세히 알아보세요](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md). Linux 플랫폼 계층은 다운로드를 위해 [전송 최적화](https://github.com/microsoft/do-client) 프로그램과 통합되며, Raspberry Pi 참조 이미지 및 Linux 시스템에서 실행되는 모든 클라이언트에 사용됩니다.

### <a name="simulator-platform-layer"></a>시뮬레이터 플랫폼 계층

시뮬레이터 플랫폼 계층 구현은 `src/platform_layers/simulator_platform_layer`에서 찾을 수 있으며, Device Update for IoT Hub를 테스트하고 평가하는 데 사용할 수 있습니다.  "시뮬레이터" 구현의 많은 작업은 Device Update for IoT Hub를 실험하기 위한 물리적 변경 내용을 줄이기 위해 모의됩니다.  이 플랫폼 계층을 사용하여 엔드투엔드 "시뮬레이션된" 업데이트를 수행할 수 있습니다. 시뮬레이터 에이전트 실행에 대해 [자세히 알아보세요](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md).

### <a name="linux-platform-layer"></a>Linux 플랫폼 계층

Linux 플랫폼 계층 구현은 `src/platform_layers/linux_platform_layer`에서 찾을 수 있으며 다운로드를 위해 [전송 최적화 클라이언트](https://github.com/microsoft/do-client/releases)와 통합되며 Raspberry Pi 참조 이미지 및 Linux 시스템에서 실행되는 모든 클라이언트에서 사용됩니다.

이 계층은 다른 업데이트 처리기와 통합하여 설치 프로그램을 구현할 수 있습니다. 예를 들어 `SWUpdate`업데이트 처리기는 셸 스크립트를 호출하여`SWUpdate` 업데이트를 수행하기 위해 실행 파일을 호출합니다.

## <a name="update-handlers"></a>업데이트 처리기

업데이트 처리기는 업데이트의 콘텐츠 또는 설치 프로그램 관련 부분을 처리하는 구성 요소입니다. 업데이트 처리기 구현은 `src/content_handlers`에 있습니다.

### <a name="simulator-update-handler"></a>시뮬레이터 업데이트 처리기

시뮬레이터 업데이트 처리기는 시뮬레이터 플랫폼 계층에서 사용되며 Linux 플랫폼 계층과 함께 사용하여 콘텐츠 처리기와 가짜 상호 작용을 할 수 있습니다. 시뮬레이터 업데이트 처리기는 주로 no-ops를 통해 업데이트 처리기 API를 구현합니다. 시뮬레이터 업데이트 처리기의 구현은 아래에서 찾을 수 있습니다.
* [이미지 업데이트 시뮬레이터](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [패키지 업데이트 APT 시뮬레이터](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

참고: AzureDeviceUpdateCore PnP 인터페이스의 InstalledCriteria 필드는 콘텐츠의 sha256 해시여야 합니다. 이 해시는 [매니페스트 개체 가져오기](import-update.md#create-a-device-update-import-manifest)에 있는 것과 동일한 해시입니다. `installedCriteria` 및 `AzureDeviceUpdateCore` 인터페이스에 대해 [자세히 알아보세요](device-update-plug-and-play.md).

### <a name="swupdate-update-handler"></a>`SWUpdate` 업데이트 처리기

`SWUpdate`업데이트 처리기"는 `SWUpdate` 명령줄 실행 파일 및 기타 셸 명령과 통합되어 Raspberry Pi 참조 이미지에 대한 A/B 업데이트를 구현합니다. [여기](https://github.com/Azure/iot-hub-device-update/releases)에서 최신 Raspberry Pi 참조 이미지를 찾습니다. `SWUpdate` 업데이트 처리기는 [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler)에서 구현됩니다.

### <a name="apt-update-handler"></a>APT 업데이트 처리기

APT 업데이트 처리기는 APT 관련 업데이트 매니페스트를 처리하고 APT를 호출하여 지정된 Debian 패키지를 설치하거나 업데이트합니다.

## <a name="self-update-device-update-agent"></a>디바이스 업데이트 에이전트 자동 업데이트

디바이스 업데이트 에이전트 및 해당 종속성은 Device Update for IoT Hub 파이프라인을 통해 업데이트할 수 있습니다. 이미지 기반 업데이트를 사용하는 경우 새 이미지에 최신 디바이스 업데이트 에이전트를 포함합니다. 패키지 기반 업데이트를 사용하는 경우 다른 패키지와 마찬가지로 APT 매니페스트에 디바이스 업데이트 에이전트와 원하는 버전을 포함합니다. APT 매니페스트에 대해 [자세히 알아보세요](device-update-apt-manifest.md). [IoT 디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md)의 디바이스 속성 섹션에서 설치된 버전의 디바이스 업데이트 에이전트와 전송 최적화 에이전트를 확인할 수 있습니다. [ADU Core 인터페이스에서 디바이스 속성에 대해 자세히 알아보세요](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>다음 단계
[디바이스 업데이트 에이전트 구성 파일 이해](device-update-configuration-file.md)