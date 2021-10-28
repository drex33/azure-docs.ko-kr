---
ms.openlocfilehash: 456fc1be942ce51082cf671d77f2f24f5f9d6d3e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287487"
---
이 자습서를 사용하려면 다음과 같은 Azure 리소스가 필요합니다.

* IoT Hub
* 스토리지 계정
* Azure Media Services 계정
* [IoT Edge 런타임](../../../../../iot-edge/how-to-provision-single-device-linux-symmetric.md)이 설치된 Azure의 Linux VM

이 빠른 시작에서는 [Live Video Analytics 리소스 설치 스크립트](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)를 사용하여 Azure 구독에 필요한 리소스를 배포하는 것이 좋습니다. 이렇게 하려면 다음 단계를 따르십시오.

1. [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/)을 엽니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Cloud Shell을 처음 사용하는 경우 스토리지 계정 및 Microsoft Azure Files 공유를 만들 구독을 선택하라는 메시지가 표시됩니다. **스토리지 만들기** 를 선택하여 Cloud Shell 세션 정보에 대한 스토리지 계정을 만듭니다. 이 스토리지 계정은 Azure Media Services 계정에서 사용하기 위해 스크립트에서 만드는 계정과는 다릅니다.
1. Cloud Shell 창 왼쪽의 드롭다운 메뉴에서 사용자 환경으로 **Bash** 를 선택합니다.

    ![환경 선택기](../../../media/quickstarts/env-selector.png)
1. 다음 명령을 실행합니다.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    스크립트가 성공적으로 완료되면 구독에 필요한 모든 리소스가 표시됩니다. 다음 스크립트를 통해 총 12개의 리소스를 설정할 수 있습니다.
    1. **스트리밍 엔드포인트** - 기록된 AMS 자산을 재생하는 데 도움이 됩니다.
    1. **가상 머신** - 에지 다비이스로 작동하는 가상 머신입니다.
    1. **디스크** - 미디어와 아티팩트를 저장하기 위해 가상 머신에 연결된 스토리지 디스크입니다.
    1. **네트워크 보안 그룹** - Azure 가상 네트워크에서 Azure 리소스와 주고받는 네트워크 트래픽을 필터링하는 데 사용됩니다.
    1. **네트워크 인터페이스** - 이를 통해 Azure Virtual Machine이 인터넷, Azure 및 기타 리소스와 통신할 수 있습니다.
    1. **Azure Bastion** - 이를 통해 브라우저와 Azure Portal을 사용하여 가상 머신에 연결할 수 있습니다.
    1. **공용 IP 주소** - 이를 통해 Azure 리소스에서 인터넷 및 공용 Azure 서비스에 통신할 수 있습니다.
    1. **가상 네트워크** - 이를 통해 가상 머신과 같은 다양한 형식의 Azure 리소스가 서로, 인터넷 및 특정 온-프레미스 네트워크와 안전하게 통신할 수 있습니다. [가상 네트워크](../../../../../virtual-network/virtual-networks-overview.md)에 대한 자세한 정보
    1. **IoT Hub** - IoT 애플리케이션, IoT Edge 및 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다.
    1. **미디어 서비스 계정** - Azure에서 미디어 콘텐츠를 관리하고 스트리밍하는 데 도움이 됩니다.
    1. **스토리지 계정** - 하나의 기본 스토리지 계정이 있어야 하며 Media Services 계정과 연결된 보조 스토리지 계정은 여러 개 있을 수 있습니다. 자세한 내용은 [Azure Media Services 계정이 있는 Azure Storage 계정](../../../../latest/storage-account-concept.md)을 참조하세요.
    1. **컨테이너 레지스트리** - 프라이빗 Docker 컨테이너 이미지 및 관련 아티팩트를 저장하고 관리하는 데 도움이 됩니다.

1. 스크립트가 완료되면 중괄호를 선택하여 폴더 구조를 표시합니다. *~/clouddrive/lva-sample* 디렉터리에 몇 개의 파일이 표시됩니다. 이 빠른 시작에서 유의해야 할 사항은 다음과 같습니다.

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - 이 파일에는 Visual Studio Code가 에지 디바이스에 모듈을 배포하는 데 사용하는 속성이 포함되어 있습니다.
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code에서 샘플 코드를 실행하는 데 이 파일을 사용합니다.
     
    다음 섹션의 Visual Studio Code에서 개발 환경을 설정할 때 이러한 파일이 필요합니다. 지금은 로컬 파일에 복사하는 것이 좋습니다.
    
    ![앱 설정](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> 만든 Azure 리소스와 관련된 문제가 발생하는 경우 **[문제 해결 가이드](../../../troubleshoot-how-to.md#common-error-resolutions)** 를 참조하여 일반적으로 발생하는 문제를 해결하세요.