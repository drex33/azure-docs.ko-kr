---
title: 자습서 - Azure IoT Hub Device Provisioning Service를 사용하여 부하가 분산된 허브에서 디바이스 프로비전
description: 이 자습서에서는 DPS(Device Provisioning Service)가 Azure Portal의 부하가 분산된 IoT 허브에서 자동 디바이스 프로비저닝을 활성화하는 방법을 보여줍니다.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 090d11866f3d3605a3703fd7ca39f820bbeb7187
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273253"
---
# <a name="tutorial-provision-devices-across-load-balanced-iot-hubs"></a>자습서: 부하가 분산되는 IoT 허브에 디바이스 프로비저닝

이 자습서에서는 Device Provisioning Service를 사용하여 부하가 분산된 여러 IoT Hub에 대해 디바이스를 프로비전하는 방법을 보여 줍니다. 이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 두 번째 IoT Hub에 두 번째 디바이스를 프로비전 
> * 두 번째 디바이스에 등록 목록 항목 추가
> * Device Provisioning Service 할당 정책을 **균등하게 배포** 로 설정
> * Device Provisioning Service에 새 IoT Hub 연결

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 이전 [허브에 디바이스 프로비전](tutorial-provision-device-to-hub.md) 자습서를 토대로 작성되었습니다.

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Azure Portal을 사용하여 두 번째 IoT Hub에 두 번째 디바이스를 프로비전

[허브에 디바이스 프로비전](tutorial-provision-device-to-hub.md) 자습서의 단계를 수행하여 다른 IoT Hub에 두 번째 디바이스를 프로비전합니다.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>두 번째 디바이스에 등록 목록 항목 추가

등록 목록은 디바이스와 함께 사용 중인 증명 메서드(디바이스 ID 확인을 위한 메서드)가 무엇인지를 Device Provisioning Service에 알려줍니다. 다음 단계는 두 번째 디바이스에 대한 등록 목록 항목을 추가하는 것입니다. 

1. Device Provisioning Service에 대한 페이지에서 **관리 등록** 을 클릭합니다. **등록 목록 항목 추가** 페이지가 나타납니다. 
2. 페이지 위쪽에서 **저장** 을 클릭합니다.
2. 필드를 완료한 다음 **저장** 을 클릭합니다.

## <a name="set-the-device-provisioning-service-allocation-policy"></a>Device Provisioning Service 할당 정책 설정

할당 정책은 IoT Hub에 디바이스를 할당하는 방법을 결정하는 Device Provisioning Service 설정입니다. 세 가지의 지원되는 할당 정책이 있습니다. 

1. **최소 대기 시간**: 디바이스가 대기 시간이 가장 낮은 허브를 기준으로 IoT Hub에 프로비전됩니다.
2. **균등 가중치 배포**(기본값): 연결된 IoT Hub들은 동일하게 해당 허브에 프로비전된 디바이스를 갖게 됩니다. 이 값은 기본 설정입니다. 디바이스를 단 하나의 IoT Hub에 프로비전하려는 경우 이 설정을 유지할 수 있습니다. 
3. **등록 목록을 통한 고정 구성**: 등록 목록에서 원하는 IoT Hub의 사양을 Device Provisioning Service 수준 할당 정책보다 우선합니다.

할당 정책을 설정하려면 다음 단계를 수행합니다.

1. 할당 정책을 설정하려면 Device Provisioning Service 페이지에서 **할당 정책 관리** 를 클릭합니다.
2. 할당 정책을 **균등 가중치 배포** 로 설정합니다.
3. **저장** 을 클릭합니다.

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>Device Provisioning Service에 새 IoT Hub 연결

Device Provisioning Service가 해당 허브에 디바이스를 등록할 수 있도록 Device Provisioning Service 및 IoT Hub를 연결합니다.

1. **모든 리소스** 페이지에서 이전에 만든 Device Provisioning Service를 클릭합니다.
2. Device Provisioning Service 페이지에서 **연결된 IoT Hub** 를 클릭합니다.
3. **추가** 를 클릭합니다.
4. **IoT Hub에 링크 추가** 페이지에서 라디오 단추를 사용하여 연결된 IoT Hub가 현재 구독 또는 다른 구독 중 어디에 있는지를 지정합니다. 그런 다음 IoT Hub 이름을 **IoT Hub** 상자에서 선택합니다.
5. **저장** 을 클릭합니다.

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 두 번째 IoT Hub에 두 번째 디바이스를 프로비전 
> * 두 번째 디바이스에 등록 목록 항목 추가
> * Device Provisioning Service 할당 정책을 **균등하게 배포** 로 설정
> * Device Provisioning Service에 새 IoT Hub 연결

## <a name="next-steps"></a>다음 단계

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps]()
-->
