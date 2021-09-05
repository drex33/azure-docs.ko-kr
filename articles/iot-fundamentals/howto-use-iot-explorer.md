---
title: Azure IoT 탐색기 설치 및 사용 | Microsoft Docs
description: Azure IoT 탐색기 도구를 설치하고 사용하여 IoT 허브에 연결된 IoT 플러그 앤 플레이 디바이스와 상호 작용합니다. 이 문서에서는 IoT 플러그 앤 플레이 디바이스를 사용하는 데 중점을 두지만 허브에 연결된 모든 디바이스에서 도구를 사용할 수 있습니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2020
ms.topic: how-to
ms.service: iot-develop
services: iot-develop
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0b4745a2f279dae515f6b9b085679562b0fcec33
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2021
ms.locfileid: "114406668"
---
# <a name="install-and-use-azure-iot-explorer"></a>Azure IoT 탐색기 설치 및 사용

Azure IoT 탐색기는 IoT 허브에 연결된 디바이스와 상호 작용하는 그래픽 도구입니다. 이 문서에서는 도구를 사용하여 IoT 플러그 앤 플레이 디바이스를 테스트하는 방법을 집중적으로 설명합니다. 로컬 머신에 도구를 설치한 후 허브에 연결하는 데 사용할 수 있습니다. 도구를 사용하여 디바이스에서 보내는 원격 분석을 보고, 디바이스 속성을 사용하고, 명령을 호출할 수 있습니다.

이 문서에서는 다음 방법을 보여 줍니다.

- Azure IoT 탐색기 도구를 설치하고 구성합니다.
- 도구를 사용하여 IoT 플러그 앤 플레이 디바이스와 상호 작용하고 테스트합니다.

도구 사용에 대한 일반적인 정보는 GitHub [추가 정보](https://github.com/Azure/azure-iot-explorer/blob/master/README.md)를 참조하세요.

Azure IoT 탐색기 도구를 사용하려면 다음이 필요합니다.

- Azure IoT Hub - Azure 구독에 IoT 허브를 추가하는 방법에는 [Azure CLI를 사용하여 IoT 허브 만들기](../iot-hub/iot-hub-create-using-cli.md) 등을 비롯한 여러 가지가 있습니다. Azure IoT 탐색기 도구를 실행하려면 IoT 허브 연결 문자열이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- IoT Hub에 등록된 디바이스. IoT Explorer를 사용하여 IoT Hub에서 디바이스 등록을 만들고 관리할 수 있습니다.

## <a name="install-azure-iot-explorer"></a>Azure IoT 탐색기 설치

[Azure IoT 탐색기 릴리스](https://github.com/Azure/azure-iot-explorer/releases)로 이동하여 최신 릴리스의 자산 목록을 확장하세요. 최신 버전의 애플리케이션을 다운로드하여 설치합니다.

>[!Important]
> [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)를 기반으로 하는 모든 리포지토리에서 모델을 확인하려면 버전 0.13.x로 업데이트합니다.

## <a name="use-azure-iot-explorer"></a>Azure IoT 탐색기 사용

디바이스의 경우 디바이스를 연결하거나 시뮬레이션된 샘플 디바이스 중 하나를 사용할 수 있습니다. 다른 언어로 작성된 일부 시뮬레이션된 디바이스 예제는 [IoT Hub에 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션 연결](../iot-develop/tutorial-connect-device.md) 자습서를 참조하세요.

### <a name="connect-to-your-hub"></a>허브에 연결

Azure IoT 탐색기를 처음 실행하면 IoT 허브의 연결 문자열을 입력하라는 메시지가 표시됩니다. 연결 문자열을 추가한 후 **연결** 을 선택합니다. 도구의 설정을 사용하여 연결 문자열을 업데이트하여 다른 IoT 허브로 전환할 수 있습니다.

IoT 플러그 앤 플레이 디바이스에 대한 모델 정의는 공용 리포지토리, 연결된 디바이스 또는 로컬 폴더에 저장됩니다. 기본적으로 이 도구는 공용 리포지토리와 연결된 디바이스에서 모델 정의를 찾습니다. 원본을 추가 및 제거하거나 **설정** 에서 원본의 우선 순위를 구성할 수 있습니다.

원본을 추가하려면 다음을 수행합니다.

1. **홈/IoT 플러그 앤 플레이 설정** 으로 이동합니다.
2. **추가** 를 선택하고 리포지토리 또는 로컬 폴더에서 원본을 선택합니다.

원본을 제거하려면 다음을 수행합니다.

1. **홈/IoT 플러그 앤 플레이 설정** 으로 이동합니다.
2. 제거하려는 원본을 찾습니다.
3. **X** 를 선택하여 제거합니다.

원본 우선 순위를 변경하려면 다음을 수행합니다.

모델 정의 원본 중 하나를 목록에서 다른 순위에 끌어서 놓을 수 있습니다.

### <a name="view-devices"></a>디바이스 보기

도구는 IoT 허브에 연결된 후 IoT 허브에 등록된 디바이스 ID를 나열하는 **디바이스** 목록 페이지를 표시합니다. 목록에서 항목을 선택하여 자세한 정보를 볼 수 있습니다.

**디바이스** 목록 페이지에서 다음을 수행할 수 있습니다.

- 새 디바이스를 허브에 등록하려면 **새로 만들기** 를 선택합니다. 그런 다음, 디바이스 ID를 입력합니다. 기본 설정을 사용하여 인증 키를 자동으로 생성하고 허브에 대한 연결을 사용하도록 설정합니다.
- 디바이스를 선택한 다음, **삭제** 를 선택하여 디바이스 ID를 삭제합니다. 이 작업을 완료하기 전에 디바이스 세부 정보를 검토하여 올바른 디바이스 ID를 삭제하고 있는지 확인합니다.

## <a name="interact-with-a-device"></a>디바이스와 상호 작용

**디바이스** 목록 페이지의 **디바이스 ID** 열에서 값을 선택하여 등록된 디바이스에 대한 세부 정보 페이지를 표시합니다. 각 디바이스에는 **디바이스** 및 **디지털 트윈** 의 두 섹션이 있습니다.

### <a name="device"></a>디바이스

이 섹션에는 **디바이스 ID**, **디바이스 쌍,** , **원격 분석**, **직접 메서드**, **클라우드-디바이스 메시지**, **모듈 ID** 탭이 포함되어 있습니다.

- **디바이스 ID** 탭에서 [디바이스 ID](../iot-hub/iot-hub-devguide-identity-registry.md) 정보를 확인하고 업데이트할 수 있습니다.
- [디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 정보는 **디바이스 쌍** 탭에서 액세스할 수 있습니다.
- 디바이스가 연결되어 있으며 데이터를 적극적으로 전송하는 경우 **원격 분석** 탭에서 [원격 분석](../iot-hub/iot-hub-devguide-messages-read-builtin.md)을 볼 수 있습니다.
- **직접 메서드** 탭에서 디바이스에 대한 [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md)를 호출할 수 있습니다.
- **클라우드-디바이스 메시지** 탭에서 [클라우드-디바이스 메시지](../iot-hub/iot-hub-devguide-messages-c2d.md)를 보낼 수 있습니다.
- [모듈 쌍](../iot-hub/iot-hub-devguide-module-twins.md) 정보에 액세스할 수 있습니다.

### <a name="iot-plug-and-play-components"></a>IoT 플러그 앤 플레이 구성 요소

디바이스가 **모델 ID** 를 사용하여 허브에 연결된 경우 이 도구는 **모델 ID** 를 볼 수 있는 **IoT 플러그 앤 플레이 구성 요소** 탭을 표시합니다.

구성된 원본-공용 리포지토리 또는 로컬 폴더 중 하나에서 **모델 ID** 를 사용할 수 있는 경우 구성 요소 목록이 표시됩니다. 구성 요소를 선택하면 사용할 수 있는 속성, 명령 및 원격 분석이 표시됩니다.

**구성 요소** 페이지에서 읽기 전용 속성을 보고, 쓰기 가능한 속성을 업데이트하고, 명령을 호출하고, 이 구성 요소에서 생성한 원격 분석 메시지를 볼 수 있습니다.

:::image type="content" source="media/howto-use-iot-explorer/components-iot-explorer.png" alt-text="Azure IoT 탐색기에서 구성 요소 보기":::

#### <a name="properties"></a>속성

:::image type="content" source="media/howto-use-iot-explorer/properties-iot-explorer.png" alt-text="Azure IoT 탐색기에서 속성 보기":::

**속성(읽기 전용)** 탭의 인터페이스에 정의된 읽기 전용 속성을 볼 수 있습니다. **속성(쓰기 가능)** 탭의 인터페이스에 정의된 쓰기 가능한 속성을 업데이트할 수 있습니다.

1. **속성(쓰기 가능)** 탭으로 이동합니다.
1. 업데이트하려는 속성을 클릭합니다.
1. 속성의 새 값을 입력합니다.
1. 디바이스에 전송할 페이로드를 미리 봅니다.
1. 변경 내용을 제출합니다.

변경 내용을 제출한 후 업데이트 상태(**동기화**, **성공** 또는 **오류**)를 추적할 수 있습니다. 동기화가 완료되면 **reported 속성** 열에 속성의 새 값이 표시됩니다. 동기화가 완료되기 전에 다른 페이지로 이동하는 경우에도 업데이트가 완료되면 도구에서 사용자에게 알려줍니다. 도구의 알림 센터를 사용하여 알림 기록을 볼 수도 있습니다.

#### <a name="commands"></a>명령

디바이스에 명령을 보내려면 **명령** 탭으로 이동합니다.

1. 명령 목록에서 트리거할 명령을 확장합니다.
1. 명령에 필요한 값을 입력합니다.
1. 디바이스에 전송할 페이로드를 미리 봅니다.
1. 명령을 제출합니다.

#### <a name="telemetry"></a>원격 분석

선택한 인터페이스에 대한 원격 분석을 보려면 해당 **원격 분석** 탭으로 이동합니다.

#### <a name="known-issues"></a>알려진 문제

최신 버전의 도구에서 지원하는 IoT 기능 목록은 [기능 목록](https://github.com/Azure/azure-iot-explorer/wiki)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 Azure IoT 탐색기를 설치 및 사용하여 IoT 플러그 앤 플레이 디바이스와 상호 작용하는 방법을 배웠습니다. 제안된 다음 단계는 [IoT 플러그 앤 플레이 디지털 트윈을 관리](../iot-develop/howto-manage-digital-twin.md)하는 방법을 알아보는 것입니다.
