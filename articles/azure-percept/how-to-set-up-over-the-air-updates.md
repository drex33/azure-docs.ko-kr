---
title: Azure IoT Hub를 설정하여 무선 업데이트를 배포합니다.
description: Azure IoT Hub를 구성하여 Azure Percept DK에 대한 업데이트를 무선으로 배포하는 방법에 관해 알아봅니다.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 19161ea1e89cf4917442e607903a7c8f0b7c2860
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006300"
---
# <a name="set-up-azure-iot-hub-to-deploy-over-the-air-updates"></a>Azure IoT Hub를 설정하여 무선 업데이트를 배포합니다.

무선 업데이트를 사용하여 Azure Percept DK를 안전하게 최신 상태로 유지합니다. 몇 가지 간단한 단계를 통해 Device Update for IoT Hub를 사용하여 Azure 환경을 설정하고 Azure Percept DK에 최신 업데이트를 배포할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): 개발 키트를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, 개발 키트를 IoT Hub에 연결했습니다.

## <a name="create-a-device-update-account"></a>디바이스 업데이트 계정 만들기

1. [Azure Portal](https://portal.azure.com)로 이동하여 Azure Percept와 함께 사용 중인 Azure 계정으로 로그인합니다.

1. 페이지 맨 위에 있는 검색 창에 **Device Update for IoT Hubs** 를 입력합니다.

1. 검색 창에 표시되는 **Device Update for IoT Hubs** 를 선택합니다.

1. 페이지 왼쪽 위에 있는 **+추가** 단추를 선택합니다.

1. Azure Percept 디바이스 및 해당 IoT Hub와 연결된 **Azure 구독** 및 **리소스 그룹** 을 선택합니다.

1. 디바이스 업데이트 계정의 **이름** 및 **위치** 를 지정합니다.

1. **디바이스 업데이트 관리자 역할 할당** 이라는 상자를 선택합니다. 

1. 세부 정보를 검토한 다음 **검토 + 만들기** 를 선택합니다.

1. **생성** 단추를 선택합니다.

1. 배포가 완료되면 **리소스로 이동** 을 클릭합니다.

## <a name="create-a-device-update-instance"></a>디바이스 업데이트 인스턴스 만들기

1. IoT Hub 리소스에 대한 디바이스 업데이트에서 **인스턴스 관리** 아래에 있는 **인스턴스** 를 클릭합니다.

1. **+ 만들기** 를 클릭하고 인스턴스 이름을 지정하여 Azure Percept 디바이스와 연결된 IoT Hub를 선택합니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

1. **만들기** 를 클릭합니다.

## <a name="configure-iot-hub"></a>IoT Hub 구성

1. 인스턴스 관리 **인스턴스** 페이지에서 디바이스 업데이트 인스턴스가 **Succeeded** 상태로 전환될 때까지 기다립니다. **새로 고침** 아이콘을 클릭하여 상태를 업데이트합니다.

1. 만들어진 인스턴스를 선택하고 **IoT Hub 구성** 을 클릭합니다. 왼쪽 창에서 **해당 변경 내용에 동의합니다** 를 선택하고 **업데이트** 를 클릭합니다.

1. 프로세스가 성공적으로 완료될 때까지 기다립니다.

## <a name="configure-access-control-roles"></a>액세스 제어 역할 구성

마지막 단계에서는 사용자에게 업데이트 게시 및 배포 권한을 부여할 수 있습니다.

1. IoT Hub 리소스에 대한 디바이스 업데이트에서 **액세스 제어(IAM)** 를 클릭합니다.

1. **+추가** 를 클릭한 다음 **역할 할당 추가** 를 선택합니다.

1. **역할** 에 대해 **디바이스 업데이트 관리자** 를 선택합니다. **다음에 대한 액세스 권한 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다. **선택** 에서 계정을 선택하거나 업데이트를 배포할 사용자의 계정을 선택합니다. **저장** 을 클릭합니다.

> [!TIP]
> 조직에서 더 많은 사용자에게 액세스 권한을 부여하려는 경우 이 단계를 반복하고 각 사용자를 **디바이스 업데이트 관리자** 로 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Device Update for IoT Hub를 사용하여 [Azure Percept 개발 키트를 무선으로 업데이트](./how-to-update-over-the-air.md)할 준비가 되었습니다.
