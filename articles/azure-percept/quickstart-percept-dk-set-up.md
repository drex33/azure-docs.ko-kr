---
title: Azure Percept DK 디바이스 설정
description: Azure Percept DK 설정 및 Azure IoT Hub에 연결
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 4e37be9e121d634e0d4a83fdd5f786805dedf132
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427213"
---
# <a name="set-up-the-azure-percept-dk-device"></a>Azure Percept DK 디바이스 설정

Azure Percept DK 설정 환경을 완료하여 개발 키트를 구성합니다. Azure 계정이 Azure Percept와 호환되는지 확인한 후 다음을 수행합니다.

- Azure Percept DK 설정 환경 시작
- Wi-Fi 네트워크에 개발 키트 연결
- 개발 키트에 원격으로 액세스할 수 있도록 SSH 로그인 설정
- Azure IoT Hub에서 새 디바이스 만들기

이 과정에서 문제가 발생하는 경우 [설정 문제 해결 가이드](./how-to-troubleshoot-setup.md)에서 가능한 해결 방법을 참조하세요.

> [!NOTE]
> 설정 환경 웹 서비스는 30분 동안 사용하지 않으면 종료됩니다. 개발 키트에 연결할 수 없거나 Wi-Fi 액세스 지점이 보이지 않으면 디바이스를 다시 시작합니다.

## <a name="prerequisites"></a>필수 조건

- Azure Percept DK(개발 키트)
- Wi-Fi 기능과 웹 브라우저를 지원하는 Windows, Linux 또는 OS X 기반 호스트 컴퓨터
- 활성화된 Azure 구독. [체험 계정을 만드세요.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 사용자에게 구독 내 **소유자** 또는 **기여자** 역할이 있어야 합니다. 아래 단계에 따라 Azure 계정 역할을 확인하세요. Azure 역할 정의에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어 설명서](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)를 참조하세요.

    > [!CAUTION]
    > 설정 환경을 시작하기 전 모든 브라우저 창을 닫고 [Azure Portal](https://portal.azure.com/)을 통해 구독에 로그인합니다. 올바른 계정으로 로그인했는지 확인하는 방법에 대한 자세한 내용은 [설정 문제 해결 가이드](./how-to-troubleshoot-setup.md)를 참조하세요.

### <a name="check-your-azure-account-role"></a>Azure 계정 역할 확인

Azure 계정이 구독 내에서 "소유자" 또는 "기여자"인지 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동한 다음, Azure Percept에서 사용하려는 Azure 계정으로 로그인합니다.

1. 노란색 키처럼 생긴 **구독** 아이콘을 선택합니다.

1. 목록에서 구독을 선택합니다. 구독이 보이지 않으면 올바른 Azure 계정으로 로그인했는지 확인합니다. 새 구독을 만들려면 [다음 단계](../cost-management-billing/manage/create-subscription.md)를 수행합니다.

1. 구독 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
1. **내 액세스 보기** 를 선택합니다.
1. 다음과 같이 역할을 확인합니다.
    - 역할이 **읽기 권한자** 로 표시되거나 역할을 볼 수 있는 권한이 없다는 메시지가 표시되는 경우 조직 내에서 계정 역할을 승격하기 위해 필요한 프로세스를 수행해야 합니다.
    - 역할이 **소유자** 또는 **기여자** 로 표시되는 경우 해당 계정은 Azure Percept와 함께 작동하며, 설정 환경을 계속 진행할 수 있습니다.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK 설정 환경 시작

1. 호스트 컴퓨터를 개발 키트의 Wi-Fi 액세스 지점에 연결합니다. 다음 네트워크를 선택하고 메시지가 표시되면 Wi-Fi 암호를 입력합니다.

    - **네트워크 이름**: **scz-xxxx** 또는 **apd-xxxx**(여기서 **xxxx** 는 개발 키트의 MAC 주소의 마지막 4자리 숫자)
    - **암호**: 개발 키트와 함께 제공된 시작 카드에서 찾을 수 있습니다.

    > [!WARNING]
    > Azure Percept DK의 Wi-Fi 액세스 지점에 연결된 동안 호스트 컴퓨터의 인터넷 연결이 일시적으로 끊깁니다. 활성 영상 회의 통화, 웹 스트리밍 또는 기타 네트워크 기반 환경이 중단됩니다.

1. 개발 키트의 Wi-Fi 액세스 지점에 연결되면 호스트 컴퓨터는 주소 표시줄에 **your.new.device/** 가 입력된 새 브라우저 창에서 자동으로 설정 환경을 시작합니다. 탭이 자동으로 열리지 않으면 웹 브라우저에서 [http://10.1.1.1](http://10.1.1.1)로 이동하여 설정 환경을 시작합니다. Azure Percept에서 사용하려는 계정과 동일한 Azure 계정 자격 증명으로 브라우저에 로그인해야 합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-welcome.png" alt-text="[시작] 페이지":::

    > [!NOTE]
    > **Mac 사용자** - Mac에서 설치 환경을 진행하는 경우 처음에는 설치 환경을 완료할 수 없는 Captive Portal 창이 열립니다. 이 창을 닫고 웹 브라우저를 열고 https://10.1.1.1 로 이동하면 설치 환경을 완료할 수 있습니다.

## <a name="connect-your-dev-kit-to-a-wi-fi-network"></a>Wi-Fi 네트워크에 개발 키트 연결

1. **시작** 화면에서 **다음** 을 선택합니다.

1. **네트워크 연결** 페이지에서 **새 WiFi 네트워크에 연결** 을 선택합니다.

    개발 키트를 이미 Wi-Fi 네트워크에 연결한 경우 **건너뛰기** 를 선택합니다.

1. 사용 가능한 네트워크 목록에서 Wi-Fi 네트워크를 선택하고 **연결** 을 선택합니다. 암호를 입력하라는 메시지가 표시되면 암호를 입력합니다.

    > [!NOTE]
    > 이 네트워크를 “기본 네트워크”(Mac)로 설정하거나 “자동으로 연결”(Windows) 확인란을 선택하는 것이 좋습니다.  이렇게 하면 프로세스 중에 연결이 중단되었을 때 호스트 컴퓨터가 개발 키트의 Wi-Fi 액세스 지점에 다시 연결할 수 있습니다.

1. 개발 키트가 성공적으로 연결되면 페이지에 개발 키트에 할당된 IPv4 주소가 표시됩니다. **이 페이지에 표시되는 IPv4 주소를 기록해 둡니다.** 문제를 해결하고 디바이스를 업데이트하기 위해 SSH를 통해 개발 키트에 연결할 때 이 IP 주소가 필요합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-success-wi-fi.png" alt-text="IP 주소 복사":::

    > [!NOTE]
    > 각 디바이스가 부팅될 때 IP 주소가 변경될 수 있습니다.

1. 사용권 계약을 모두 읽고(계약의 맨 아래까지 스크롤해야 함), **사용권 계약을 읽었으며 이에 동의합니다** 를 선택하고, **다음** 을 선택합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-eula.png" alt-text="EULA 동의":::

## <a name="set-up-an-ssh-login-for-remote-access-to-your-dev-kit"></a>개발 키트에 원격으로 액세스할 수 있도록 SSH 로그인 설정

1. SSH 계정 이름과 공개 키/암호를 만들고 **다음** 을 선택합니다.

    SSH 계정을 이미 만들었으면 이 단계를 건너뛸 수 있습니다.

    **나중에 사용할 수 있도록 로그인 정보를 기록해 둡니다**.

    > [!NOTE]
    > SSH(Secure Shell)는 호스트 컴퓨터를 통해 개발 키트에 원격으로 연결할 수 있도록 하는 네트워크 프로토콜입니다.

##  <a name="create-a-new-device-in-azure-iot-hub"></a>Azure IoT Hub에서 새 디바이스 만들기

1. **새 디바이스로 설치** 를 선택하여 Azure 계정 내에서 새 디바이스를 만듭니다.

    이제 Azure에서 디바이스 코드를 가져옵니다.

1. **복사** 를 선택합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-copy-code.png" alt-text="디바이스 코드 복사":::

    > [!NOTE]
    > 다음 단계에서 디바이스 코드를 사용할 때 오류가 발생하거나 디바이스 코드가 표시되지 않는 경우 [문제 해결 단계](./how-to-troubleshoot-setup.md)를 참조하세요. 

1. **Azure에 로그인** 을 선택합니다.

1. 새 브라우저 탭이 열리고 **코드 입력** 이라는 창이 표시됩니다. 이 창에 코드를 붙여넣고 **다음** 을 선택합니다. 설정 환경에서 **시작** 탭을 닫지 마세요.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-enter-code.png" alt-text="디바이스 코드 입력":::

1. 개발 키트와 함께 사용할 Azure 계정 자격 증명으로 Azure Percept에 로그인합니다. 모두 마쳤으면 브라우저 탭을 열어 둡니다.

    > [!CAUTION]
    > 브라우저에서 다른 자격 증명을 자동으로 캐시할 수 있습니다. 올바른 계정으로 로그인하고 있는지 다시 확인합니다.

    디바이스에서 Azure Percent에 성공적으로 로그인한 후 **허용** 을 선택합니다. 
    
    **시작** 탭으로 돌아가서 설치 환경을 계속합니다.

1. **Azure IoT Hub에 디바이스 할당** 페이지가 **시작** 탭에 표시되면 다음 작업 중 하나를 수행합니다.

    - IoT Hub가 이 페이지에 나열된 경우 **Azure IoT Hub 선택** 으로 바로 이동합니다.
    - IoT Hub가 없거나 새로 만들려는 경우 **새 Azure IoT Hub 만들기** 를 선택합니다.

    > [!IMPORTANT]
    > IoT Hub가 있지만 목록에 나타나지 않는 경우에는 잘못된 자격 증명으로 Azure Percept에 로그인했을 수 있습니다. 도움말은 [설정 문제 해결 가이드](./how-to-troubleshoot-setup.md)를 참조하세요.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-iot-hub-select.png" alt-text="IoT Hub 선택":::

1. 새 IoT Hub를 만들려면 다음 안내를 따르세요.

    - Azure Percept에서 사용할 Azure 구독을 선택합니다.
    - 기존 리소스 그룹을 선택합니다. 없는 경우 **새로 만들기** 를 선택하고 프롬프트의 지시를 따릅니다.
    - 실제 위치와 가장 가까운 Azure 지역을 선택합니다.
    - 새 IoT Hub 이름을 지정합니다.
    - **S1(표준) 가격 책정 계층** 을 선택합니다.

    > [!NOTE]
    > IoT Hub 배포가 완료되려면 몇 분 정도 걸릴 수 있습니다. edge AI 애플리케이션에 대해 더 높은 [메시지 처리량](../iot-hub/iot-hub-scaling.md#message-throughput)이 필요하면 언제든지 Azure Portal에서 [IoT Hub를 더 높은 표준 계층으로 업그레이드](../iot-hub/iot-hub-upgrade.md)할 수 있습니다. B 및 F 계층은 Azure Percept를 지원하지 않습니다.

1. 배포가 완료되면 **등록** 을 선택합니다.

1. Azure IoT Hub 선택

1. 개발 키트의 디바이스 이름을 입력하고 **다음** 을 선택합니다.

1. 이제 디바이스 모듈이 디바이스에 배포됩니다. \- 몇 분 정도 걸릴 수 있습니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-finalize.png" alt-text="설정 완료":::

1. **디바이스 설정 완료!** 개발 키트가 IoT Hub에 성공적으로 연결되었고 모든 모듈을 배포했습니다.

    > [!NOTE]
    > 완료 후 개발 키트의 Wi-Fi 액세스 지점이 자동으로 연결을 해제하고 설정 환경 웹 서비스가 종료되고 2개의 알림이 표시됩니다.

    > [!NOTE]
    > 이 설정 프로세스의 일부로 구성된 IoT Edge 컨테이너는 90일 후에 만료될 인증서를 사용합니다. IoT Edge를 다시 시작하여 인증서를 자동으로 재생성할 수 있습니다. 자세한 내용은 [IoT Edge 디바이스에서 인증서 관리](../iot-edge/how-to-manage-device-certificates.md)를 참조하세요.

1. 호스트 컴퓨터를 개발 키트가 연결된 Wi-Fi 네트워크에 연결합니다.

1. **Azure Portal 계속 진행** 을 선택합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-Azure-portal-continue.png" alt-text="Azure Percept Studio로 이동":::

### <a name="video-walk-through"></a>동영상 연습 
위에 설명된 단계에 대한 연습을 시각적으로 보려면 아래 동영상을 참조하세요.
> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>다음 단계

이제 개발 키트가 설정되었으므로 비전 AI 작동을 확인할 수 있습니다.
- [개발 키트 동영상 스트림 보기](./how-to-view-video-stream.md)
- [개발 키트에 비전 AI 모델 배포](./how-to-deploy-model.md)
