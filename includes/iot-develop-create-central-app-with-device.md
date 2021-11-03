---
title: 포함 파일
description: 포함 파일
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 210978e3ecc817c55103267014fef9bd9e36a0e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032779"
---
## <a name="create-an-application"></a>애플리케이션 만들기
디바이스를 Azure IoT에 연결하는 방법에는 여러 가지가 있습니다. 이 섹션에서는 Azure IoT Central을 사용하여 디바이스를 연결하는 방법에 대해 알아봅니다. IoT Central은 IoT 솔루션에서 디바이스를 관리하는 데 드는 비용과 복잡성을 줄이는 IoT 애플리케이션 플랫폼입니다.

새 애플리케이션을 만들려면
1. [Azure IoT Central](https://apps.azureiotcentral.com/)로 이동하여 Microsoft 개인, 회사 또는 학교 계정으로 로그인합니다.
1. **빌드** 로 이동하여 **사용자 지정 앱** 을 선택합니다.
   :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-build.png" alt-text="IoT Central 시작 페이지":::
1. **애플리케이션 이름** 에서 고유한 이름을 입력하거나 생성된 이름을 사용합니다.
1. **URL** 에서 기억하기 쉬운 애플리케이션 URL 접두사를 입력하거나 생성된 URL 접두사를 사용합니다.
1. **애플리케이션 템플릿** 을 *사용자 지정 애플리케이션* 으로 설정된 상태로 둡니다. 
1. **가격 책정 계획** 옵션을 선택합니다. 
    - 애플리케이션을 7일 동안 무료로 사용하려면 **무료** 를 선택합니다. 만료되기 전에 무료 애플리케이션을 표준 가격 책정으로 변환할 수 있습니다.
    - 필요에 따라 표준 가격 책정 계획을 선택할 수 있습니다. 표준 가격 책정을 선택하면 더 많은 옵션이 표시되며, **디렉터리**, **Azure 구독** 및 **위치** 를 설정해야 합니다. 가격 책정에 대한 자세한 내용은 [Azure IoT Central 가격](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. 
        - **디렉터리** 는 애플리케이션을 만드는 Azure Active Directory입니다. Azure Active Directory에는 사용자 ID, 자격 증명 및 기타 조직 정보가 포함됩니다. Azure Active Directory가 없는 경우 Azure 구독을 만들 때 자동으로 만들어집니다.
        - **Azure 구독** 을 사용하면 Azure 서비스 인스턴스를 만들 수 있습니다. IoT Central은 구독에서 리소스를 프로비저닝합니다. 구독이 없는 경우 [무료](https://aka.ms/createazuresubscription)로 만들 수 있습니다. 구독이 있는 경우 드롭다운에서 선택할 수 있습니다.
        - **위치** 는 애플리케이션을 만드는 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)입니다. 최적의 성능을 얻으려면 실제로 디바이스에서 가장 가까운 위치를 선택합니다. 위치가 선택되면 애플리케이션을 다른 위치로 이동할 수 없습니다.

    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-pricing.png" alt-text="IoT Central 새 애플리케이션 대화 상자":::
1. **만들기** 를 선택합니다.
    
    IoT Central에서 애플리케이션이 만들어지면 애플리케이션 대시보드로 리디렉션됩니다.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-created.png" alt-text="IoT Central 새 애플리케이션 대시보드":::

## <a name="add-a-device"></a>디바이스 추가
이 섹션에서는 새 디바이스를 IoT Central 애플리케이션에 추가합니다. 디바이스는 애플리케이션에 연결할 디바이스를 나타내는 디바이스 템플릿의 인스턴스입니다. 

새 디바이스를 만들려면 다음을 수행합니다.
1. 왼쪽 창에서 **디바이스** 를 선택한 다음, **+ 새로 만들기** 를 선택합니다.
1. **디바이스 템플릿** 을 *할당되지 않음* 으로 설정하고 **이 디바이스를 시뮬레이션하려고 하나요?** 를 *아니요* 로 설정합니다.

1. 친숙한 **디바이스 이름** 및 **디바이스 ID** 를 설정합니다. 필요에 따라 생성된 값을 사용합니다.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-create-device.png" alt-text="IoT Central 새 디바이스 대화 상자":::

1. **만들기** 를 선택합니다.

    **모든 디바이스** 목록에 만든 디바이스가 표시됩니다.
    :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-devices-list.png" alt-text="IoT Central 모든 디바이스 목록 ":::
    
새 디바이스에 대한 연결 세부 정보를 가져오려면 다음을 수행합니다.
1. **모든 디바이스** 목록에서 연결된 디바이스 이름을 클릭하여 세부 정보를 표시합니다. 
1. 상단 메뉴에서 **연결** 을 선택합니다.

    **디바이스 연결** 대화 상자에 연결 세부 정보가 표시됩니다. :::image type="content" source="media/iot-develop-create-central-app-with-device/iot-central-device-connect.png" alt-text="IoT Central 디바이스 연결 세부 정보":::
1. **디바이스 연결** 대화 상자에서 다음 값을 안전한 위치에 복사합니다. 이러한 값을 사용하여 디바이스를 IoT Central에 연결합니다.
    * `ID scope`
    * `Device ID`
    * `Primary key`