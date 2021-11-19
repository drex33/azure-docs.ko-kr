---
title: 빠른 시작 - Microsoft Azure Portal에서 IoT Hub Device Provisioning Service 설정
description: 빠른 시작 - Microsoft Azure Portal에서 Azure IoT Hub DPS(Device Provisioning Service) 설정
author: wesmc7777
ms.author: wesmc
manager: lizross
ms.date: 08/06/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 06a4455252feea86e9748ac6acf3080584c318f3
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755897"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>빠른 시작: Azure Portal에서 IoT Hub Device Provisioning Service 설정

IoT Hub Device Provisioning Service는 모든 IoT 허브에 대한 제로 터치 Just-In-Time 디바이스 프로비저닝을 지원합니다. Device Provisioning Service를 사용하면 고객이 사용자의 개입 없이도 안전하고 확장 가능한 방식으로 수백만 개의 IoT 디바이스를 프로비전할 수 있습니다. Azure IoT Hub 디바이스 프로비저닝 서비스는 TPM, 대칭 키 및 X.509 인증서 인증을 사용하여 IoT 디바이스를 지원합니다. 자세한 내용은 [IoT Hub Device Provisioning Service 개요](./about-iot-dps.md)를 참조하세요.

이 빠른 시작에서는 Azure Portal에서 IoT Hub Device Provisioning Service를 설정하는 방법을 알아봅니다.

디바이스를 프로비전하려면 다음을 수행합니다.

* Azure Portal을 사용하여 IoT Hub 만들기
* Azure Portal을 사용하여 IoT Hub Device Provisioning Service 만들기
* Device Provisioning Service에 IoT Hub 연결

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 시작하려면 Azure 구독이 필요합니다. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만들 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="create-a-new-iot-hub-device-provisioning-service"></a>새 IoT Hub Device Provisioning Service 만들기

1. Azure Portal에서 **+ 리소스 만들기** 를 선택합니다.

2. *Marketplace* 에서 **Device Provisioning Service** 를 검색합니다. **IoT Hub Device Provisioning Service** 를 선택합니다.

3. **생성** 를 선택합니다.

4. 다음 정보를 입력합니다.

    * **이름:** 새 Device Provisioning Service 인스턴스의 고유한 이름을 입력합니다. 입력한 이름을 사용할 수 있으면 녹색 확인 표시가 나타납니다.
    * **구독:** 이 Device Provisioning Service 인스턴스를 만드는 데 사용할 구독을 선택합니다.
    * **리소스 그룹:** 이 필드를 통해 새 리소스 그룹을 만들거나 새 인스턴스를 포함하도록 기존 항목을 선택할 수 있습니다. 이전 단계에서 만든 Iot 허브를 포함하는 동일한 리소스 그룹을 선택합니다. 모든 관련 리소스를 한 그룹에 배치하여 다 함께 관리할 수 있습니다. 예를 들어 리소스 그룹을 삭제하면 해당 그룹에 들어 있는 모든 리소스가 삭제됩니다. 자세한 내용은 [Azure Resource Manager 리소스 그룹 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.
    * **위치**: 디바이스에 가장 가까운 위치를 선택합니다.

        :::image type="content" source="./media/quick-setup-auto-provision/create-iot-dps-portal.png" alt-text="포털 블레이드에서 Device Provisioning Service 인스턴스에 대한 기본 정보 입력":::

5. **검토 + 만들기** 를 선택하여 프로비저닝 서비스의 유효성을 검사합니다.

6. **생성** 를 선택합니다.

7. 배포가 성공적으로 완료되면 **리소스로 이동** 을 선택하여 Device Provisioning Service 인스턴스를 확인합니다.

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>IoT 허브와 Device Provisioning Service 연결

이 섹션에서는 Device Provisioning Service 인스턴스에 구성을 추가합니다. 이 구성은 디바이스가 프로비전될 IoT 허브를 설정합니다.

1. *설정* 메뉴에서 **연결된 IoT 허브** 를 선택합니다.

2. **+추가** 를 선택합니다.

3. **IoT 허브에 대한 연결 추가** 패널에서 다음 정보를 입력합니다. 

    * **구독:** 새 Device Provisioning Service 인스턴스와 연결하려는 IoT 허브가 들어 있는 구독을 선택합니다.
    * **IoT 허브:** 새 Device Provisioning Service 인스턴스와 연결할 IoT 허브를 선택합니다.
    * **액세스 정책:** IoT 허브로 연결을 설정하기 위한 자격 증명으로 **iothubowner** 를 선택합니다.  

        :::image type="content" source="./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png" alt-text="포털 블레이드에서 Device Provisioning Service 인스턴스에 연결하도록 허브 이름 연결"::: 

4. **저장** 을 선택합니다.

5. **새로 고침** 을 선택합니다. 이제 선택한 허브가 **연결된 IoT Hub** 블레이드 아래에 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Device Provisioning Service의 나머지 빠른 시작 및 자습서에서는 이 빠른 시작에서 만든 리소스를 사용합니다. 그러나 더 이상 빠른 시작 또는 자습서를 수행하지 않으려는 경우 해당 리소스를 삭제하는 것이 좋습니다.

Azure Portal에서 리소스를 정리하려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다.

2. Device Provisioning Service를 선택합니다.

3. 디바이스 세부 정보 창의 위쪽에서 **삭제** 를 선택합니다.  

4. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다.

5. IoT Hub를 선택합니다.

6. 허브 세부 정보 창의 위쪽에서 **삭제** 를 선택합니다.  

## <a name="next-steps"></a>다음 단계

IoT 허브 및 Device Provisioning Service를 통해 시뮬레이션된 디바이스를 프로비전합니다.

> [!div class="nextstepaction"]
> [빠른 시작: 시뮬레이션된 대칭 키 디바이스 프로비전](./quick-create-simulated-device-symm-key.md)
