---
title: Azure IoT Hub에 대한 디바이스 업데이트에서 디바이스 업데이트 계정 만들기 | Microsoft Docs
description: Azure IoT Hub에 대한 디바이스 업데이트에서 디바이스 업데이트 계정을 만듭니다.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.custom: subject-rbac-steps
ms.openlocfilehash: 5ad119017f4fe1dda0703547480c7444978d1878
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129056"
---
# <a name="device-update-for-iot-hub-resource-management"></a>IoT Hub 리소스 관리용 디바이스 업데이트

디바이스 업데이트를 시작하려면 디바이스 업데이트 계정과 인스턴스를 만들고 액세스 제어 역할을 설정해야 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

* IoT Hub에 대한 액세스 권한. S1(표준) 계층 이상을 사용하는 것이 좋습니다. 
* 지원되는 브라우저:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>디바이스 업데이트 계정 만들기

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

2. **리소스 생성하기** 를 클릭하고 “IoT Hub에 대한 디바이스 업데이트”를 검색합니다

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="IoT Hub 리소스에 대한 디바이스 업데이트 스크린샷" lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. **생성하기** -> **IoT Hub용 디바이스 업데이트** 를 클릭합니다

4. 디바이스 업데이트 계정 및 리소스 그룹에 연결할 Azure 구독을 지정합니다.

5. 디바이스 업데이트 계정의 이름 및 위치 지정

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="계정 세부 정보의 스크린샷" lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > [Azure 제품-지역별 페이지로](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) 이동하여 IoT Hub에 대한 디바이스 업데이트를 사용할 수 있는 지역을 발견할 수 있습니다. IoT Hub에 대한 디바이스 업데이트를 사용자의 지역에서 사용할 수 없는 경우 가장 가까운 사용 가능한 지역에서 계정을 만들도록 선택할 수 있습니다. 

6. 필요에 따라 확인란을 선택하여 디바이스 업데이트 관리자 역할을 자신에게 할당할 수 있습니다. "액세스 제어 역할 구성" 섹션에 나열된 단계를 사용하여 사용자와 애플리케이션에 적절한 수준의 액세스를 위한 역할 조합을 제공할 수도 있습니다.

8. **다음: 검토+생성>** 을 클릭합니다

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="계정 세부 정보 검토의 스크린샷" lightbox="media/create-device-update-account/account-review.png":::

7. 세부 정보를 검토한 다음, **만들기** 를 선택합니다. 배포가 진행되고 있는 것을 볼 수 있습니다. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="진행 중인 계정 배포 스크린샷" lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. 몇 분 안에 배포 상태가 “완료”로 변경되는 것을 볼 수 있습니다. **리소스로 이동** 을 클릭합니다

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="계정 배포의 완료의 스크린샷" lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>디바이스 업데이트 인스턴스 만들기 

디바이스 업데이트 인스턴스는 단일 IoT Hub에 연결됩니다. 디바이스 업데이트에 사용할 IoT Hub를 선택합니다. 이 단계를 수행하는 동안 새 공유 액세스 정책을 만들어 디바이스 업데이트에서 IoT Hub 사용하는 데 필요한 권한만 사용하는지 확인합니다(레지스트리 쓰기 및 서비스 연결). 이 정책은 액세스 권한이 디바이스 업데이트로만 제한되도록 합니다.

계정을 만든 후에 디바이스 업데이트 인스턴스를 만듭니다.

1. 새로 생성한 계정 리소스에 들어오면,인스턴스 관리 **인스턴스** 블레이드로 이동합니다

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="계정 내의 인스턴스 관리 스크린샷" lightbox="media/create-device-update-account/instance-blade.png":::

2. **생성하기** 및 인스턴스 이름 지정을 클릭하고 IoT Hub를 선택합니다

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="인스턴스 세부 정보의 스크린샷" lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > 디바이스 업데이트 리소스에 연결하는 IoT Hub는 디바이스 업데이트 계정과 동일한 지역에 있을 필요는 없습니다. 그러나 성능을 향상시키려면 디바이스 업데이트 계정의 지역에 가까운 지역에 IoT Hub가 위치하는 것이 좋습니다. 

3. **만들기** 를 클릭합니다. 인스턴스가 “만드는 중” 상태로 표시됩니다. 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="인스턴스를 만드는 스크린샷" lightbox="media/create-device-update-account/instance-creating.png":::

4. 인스턴스 배포가 완료 될 때까지 5-10분을 대기합니다. “프로비전 상태”가 “성공”으로 표시될 때까지 상태를 새로 고칩니다.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="인스턴스 만들기 성공의 스크린샷" lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>IoT Hub 구성 

디바이스 업데이트는 IoT Hub에서 변경 알림을 받도록 하기 위해 “기본 제공” 이벤트 허브와 통합됩니다. “IoT Hub 구성” 단추를 클릭하면 IoT 디바이스와 통신하는 데 필요한 메시지 경로 및 액세스 정책이 구성됩니다. 

IoT Hub를 구성하려면

1. “프로비전 상태” 인스턴스가 “성공”으로 전환되면 인스턴스 관리 블레이드에서 인스턴스를 선택합니다. **IoT Hub 구성** 을 클릭합니다

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="인스턴스에 대한 IoT Hub를 구성하는 스크린샷" lightbox="media/create-device-update-account/instance-configure.png":::

2. **이 변경을 수행하는 데 동의함** 을 선택합니다

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="인스턴스에 대한 IoT Hub를 구성하는 것에 동의하는 스크린샷" lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. **업데이트** 를 클릭합니다.

   > [!NOTE] 
   > Azure IoT Hub의 무료 계층을 사용하는 경우 허용되는 메시지 경로 수는 5개로 제한됩니다. Device Update for IoT Hub가 예상대로 작동하려면 4개의 메시지 경로를 구성해야 합니다. 

[구성된 메시지 경로에 대해 알아봅니다.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>액세스 제어 역할 구성

다른 사용자가 디바이스 업데이트에 액세스할 수 있도록 하려면 이 리소스에 대한 액세스 권한을 사용자에게 부여해야 합니다. 계정을 생성하는 동안 디바이스 업데이트 관리자 역할을 자신에게 할당했으며 추가 사용자 또는 애플리케이션에 대한 액세스를 제공할 필요가 없는 경우,이 단계를 건너뛸 수 있습니다. 

1. 디바이스 업데이트 계정 내에서 IAM(액세스 제어)으로 이동합니다.

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="디바이스 업데이트 계정 내의 액세스 제어 스크린샷" lightbox="media/create-device-update-account/account-access-control.png":::

2. **역할 할당 추가** 를 클릭합니다

3. 역할 탭 아래의 지정된 옵션에서 디바이스 업데이트 역할을 선택합니다
     - 디바이스 업데이트 관리자
     - 디바이스 업데이트 읽기 권한자
     - 디바이스 업데이트 콘텐츠 관리자
     - 디바이스 업데이트 콘텐츠 읽기 권한자
     - 디바이스 업데이트 배포 관리자
     - 디바이스 업데이트 배포 읽기 권한자
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="디바이스 업데이트 계정 내에서 액세스 제어 역할 할당의 스크린샷" lightbox="media/create-device-update-account/role-assignment.png":::
    
    [IoT Hub에 대한 디바이스 업데이트의 역할 기반 액세스 제어에 대해 알아봅니다.](device-update-control-access.md) 
    
4. **다음** 을 클릭합니다.
5. 사용자 또는 Azure AD 그룹에 대한 액세스 권한을 할당합니다.
6. 구성원을 선택합니다
   
   :::image type="content" source="media/create-device-update-account/role-assignment-2.png" alt-text="디바이스 업데이트 계정 내에서 액세스 제어 구성원 선택 스크린샷." lightbox="media/create-device-update-account/role-assignment-2.png":::

6. **검토 + 할당** 을 클릭합니다
7. 새 역할 할당을 검토하고 **검토 + 할당** 을 다시 클릭합니다
8. 이제 IoT Hub 내에서 디바이스 업데이트 환경을 사용할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

다음 빠른 자습서 중 하나를 사용하여 디바이스를 업데이트해 보세요.

 - [시뮬레이터에서 디바이스 업데이트](device-update-simulator.md)
 - [Raspberry Pi에서 디바이스 업데이트](device-update-raspberry-pi.md)
 - [Ubuntu Server 18.04 x64 패키지 에이전트에서 디바이스 업데이트](device-update-ubuntu-agent.md)

[디바이스 업데이트 계정 및 인스턴스에 대해 알아봅니다.](device-update-resources.md) 

[디바이스 업데이트 액세스 제어 역할에 대해 알아봅니다. ](device-update-control-access.md) 

