---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 78ad23713d1fc58d55696502dc9ff780847a8357
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414881"
---
<!-- put the ## header in the file that includes this file -->

이 섹션에서는 IoT 허브의 ID 레지스트리에 디바이스 ID를 만듭니다. ID 레지스트리에 항목이 없는 경우 디바이스를 허브에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](../articles/iot-hub/iot-hub-devguide-identity-registry.md#identity-registry-operations)를 참조하세요.

1. IoT Hub 탐색 메뉴에서 **IoT 디바이스** 를 열고 **새로 만들기** 를 선택하여 IoT Hub에 디바이스를 추가합니다.

    ![포털에서 디바이스 ID 만들기](./media/iot-hub-include-create-device/create-identity-portal-vs2019.png)

1. **디바이스 만들기** 에서 **myDeviceId** 와 같은 새 디바이스의 이름을 지정하고 **저장** 을 선택합니다. 이 작업은 IoT Hub에 대한 디바이스 ID를 만듭니다. 기본 및 보조 키가 자동으로 생성되도록 **자동 생성 키** 를 선택된 상태로 둡니다.

   ![새 디바이스 추가](./media/iot-hub-include-create-device/create-a-device-vs2019.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 디바이스가 만들어진 후 **IoT 디바이스** 창의 목록에서 디바이스를 엽니다. **기본 연결 문자열** 을 복사합니다. 이 연결 문자열은 디바이스 코드에서 허브와 통신하는 데 사용됩니다. 

    기본적으로 키와 연결 문자열은 중요한 정보이므로 마스킹됩니다. 눈 모양 아이콘을 클릭하면 아래 이미지와 같이 표시됩니다. 복사 단추를 사용하여 복사하도록 표시할 필요는 없습니다.

    ![디바이스 연결 문자열](./media/iot-hub-include-create-device/device-details-vs2019.png)

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 디바이스 ID 및 키와 개별 디바이스에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 애플리케이션이 다른 디바이스별 메타데이터를 저장해야 할 경우 애플리케이션별 스토리지를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드](../articles/iot-hub/iot-hub-devguide-identity-registry.md)를 참조하세요.
