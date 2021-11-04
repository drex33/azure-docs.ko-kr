---
title: Azure Video Analyzer 라이브 파이프라인 시작 - Azure Portal
description: 이 빠른 시작에서는 Azure Video Analyzer 서비스의 라이브 파이프라인을 사용하여 RTSP 카메라에서 비디오를 캡처하고 녹화하는 단계를 안내합니다.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 10/16/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f032eea5c54ac4c0d0200d9063b7f499838f2465
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053588"
---
# <a name="quickstart-get-started-with-video-analyzer-live-pipelines-in-the-azure-portal"></a>빠른 시작: Azure Portal Video Analyzer 라이브 파이프라인 시작

[!INCLUDE [header](includes/cloud-env.md)]

이 빠른 시작에서는 Azure Video Analyzer 서비스의 라이브 파이프라인을 사용하여 실시간 스트리밍 프로토콜(RTSP) 카메라에서 비디오를 캡처하고 녹화하는 단계를 안내합니다.
Azure Portal을 사용하여 Video Analyzer 계정 및 해당 리소스를 만듭니다. 실제 RTSP 카메라에 액세스할 수 없는 경우(인터넷을 통해 액세스할 수 있는) RTSP 카메라 시뮬레이터를 배포합니다. 그런 다음, 관련 Video Analyzer 리소스를 배포하여 Video Analyzer 계정에 비디오를 녹화합니다.

이 문서에 설명된 단계는 인터넷을 통해 액세스할 수 있고 방화벽 뒤에서 보호되지 않는 카메라에 적용됩니다. 다음 다이어그램은 Video Analyzer 계정에 배포할 라이브 [파이프라인](../pipeline.md)을 그래픽으로 나타냅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/live-pipeline.svg" alt-text="클라우드의 라이브 파이프라인 표현":::

## <a name="prerequisites"></a>필수 구성 요소

- 활성화된 Azure 구독. 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]
- 인터넷을 통해 액세스할 수 있는 RTSP 카메라 또는 리자 권한으로 RTSP 카메라 시뮬레이터를 호스팅하는 Azure Linux VM

## <a name="sample-architecture---recording-video-from-a-camera-over-the-internet"></a>샘플 아키텍처 - 인터넷을 통해 카메라에서 비디오 녹화
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/public-camera-to-cloud-live-pipeline-arch.png" alt-text="클라우드에서 비디오를 캡처하는 Video Analyzer의 라이브 파이프라인과 통합된 공용 카메라 비디오 피드의 샘플 아키텍처 다이어그램":::

## <a name="rtsp-camera"></a>RTSP 카메라

[지원되는 카메라](../quotas-limitations.md)에 대한 RTSP 지원 카메라 참조에 액세스해야 합니다. 최대 비트 전송률 3Mbps 미만의 비디오를 인코딩하도록 카메라를 구성해야 합니다. 이 최대 비트 전송률 설정을 기록해 둡니다. 또한 이 카메라의 RTSP 서버는 공용 인터넷을 통해 액세스할 수 있어야 합니다. 이러한 카메라를 사용할 수 있는 경우 Azure 리소스 만들기 섹션으로 건너뛸 수 있습니다. 또는 아래 섹션에 설명된 대로 RTSP 카메라 시뮬레이터를 배포할 수 있습니다.

## <a name="deploy-rtsp-camera-simulator"></a>RTSP 카메라 시뮬레이터 배포

이 섹션에서는 'Ubuntu Server 18.04' 운영 체제를 실행하는 Azure Linux VM에 RTSP 카메라 시뮬레이터를 배포하는 방법을 보여줍니다. 이 시뮬레이터는 [Live555 Media Server](http://www.live555.com/mediaServer/)를 사용합니다.

> [!NOTE]
> 타사 소프트웨어에 대한 참조는 정보 및 편의 제공 목적으로만 사용됩니다. Microsoft는 타사 소프트웨어에 대한 권한을 보증하거나 제공하지 않습니다. 자세한 내용은 [Live555 Media Server](http://www.live555.com/mediaServer/)를 참조하세요.

> [!WARNING]
> 이 RTSP 카메라 시뮬레이터 엔드포인트는 인터넷을 통해 노출되므로 RTSP URL을 알고 있는 모든 사용자가 액세스할 수 있습니다.

**배포 단계:**
1. 'Ubuntu Server 18.04' 운영 체제를 실행하는 standard_D2s_v3 시리즈 Azure Linux VM을 배포합니다. VM 만들기 단계는 [여기](../../../virtual-machines/linux/quick-create-portal.md)를 참조하세요. 연결된 문서에서 웹 서버를 설치할 필요가 없습니다. 또한 SSH 연결을 사용하여 VM에 연결할 수 있도록 배포 마법사에서 SSH 포트를 허용합니다.
1. RTSP 프로토콜에 대한 인바운드 연결을 사용하도록 설정합니다. Azure Portal에서, 위에서 만든 Linux VM에 대한 관리 창을 엽니다.

    1. 네트워킹을 클릭하면 인바운드 SSH 연결을 지원하기 위해 만든 네트워크 보안 그룹(NSG)에 대한 인바운드 포트 규칙에 대한 블레이드가 열립니다.
    1. 인바운드 포트 규칙 추가를 클릭하여 새 규칙을 추가합니다.
    1. 열리는 창에서 대상 포트 범위를 554로 변경합니다. 규칙의 이름을 선택합니다(예: "RTSP"). 다른 모든 값은 기본값으로 유지합니다. 자세한 내용은 [여기](../../../virtual-machines/windows/nsg-quickstart-portal.md)를 참조하세요.
1. [여기의](https://docs.docker.com/engine/install/ubuntu/) 지침을 사용하여 VM에 docker를 설치합니다. 'hello-world' 이미지를 실행하여 Docker 설치를 확인하는 단계만 수행합니다.
1. 예를 들어, SSH를 사용하여 VM에 연결합니다. 터미널 창에서 미디어 파일을 호스팅하는 'localmedia'와 같은 로컬 폴더를 만듭니다. 이 VM 로컬 폴더는 RTSP mediaserver 컨테이너에 매핑하는 데 사용됩니다.
1. 다음과 같이 카메라 피드를 시뮬레이션하는 데 사용되는 MKV 파일을 복사합니다.

    ```
    cd localmedia
    wget https://lvamedia.blob.core.windows.net/public/camera-1800s.mkv
    ```
1. 루트 디렉터리에서 다음과 같이 미리 빌드된 컨테이너 이미지를 사용하여 VM에서 RTSP 서버를 시작합니다.

    ```    
    sudo docker run -d -p 554:554 -v ${PWD}/localmedia:/live/mediaServer/media mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2
    ```
1. RTSP 서버가 실행되면 클라이언트는 이제 RTSP URL을 통해 연결할 수 있습니다.

    - Azure Portal VM의 '개요' 페이지로 이동하여 '공용 IP 주소' 값을 적어 둡니다.
    
        - RTSP URL은 rtsp://{공용 IP 주소}:554/media/camera-1800s.mkv이며 데스크톱의 플레이어(예: VLC)로 테스트할 수 있습니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

다음 단계는 필요한 Azure 리소스(Video Analyzer 계정, 스토리지 계정, 사용자가 할당한 관리 ID)를 만드는 것입니다.

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Azure Portal에서 Video Analyzer 계정 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 맨 위의 검색 창에서 **Video Analyzer** 를 입력합니다.
1. **서비스** 에서 **Video Analyzer** 를 선택합니다.
1. **추가** 를 선택합니다.
1. **Video Analyzer 계정 만들기** 섹션에서 다음 필수 값을 입력합니다.

   - **구독**: Video Analyzer 계정을 만드는 데 사용 중인 구독을 선택합니다.
   - **리소스 그룹**: Video Analyzer 계정을 만들 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 리소스 그룹을 만듭니다.
   - **Video Analyzer 계정 이름**: Video Analyzer 계정의 이름을 입력합니다. 이름은 공백 없이 모두 소문자이거나 숫자여야 하고 길이는 3~24자여야 합니다.
   - **위치**: Video Analyzer 계정을 배포할 위치를 선택합니다(예: **미국 서부 2**).
   - **스토리지 계정**: 스토리지 계정을 만듭니다. [표준 범용 v2](../../../storage/common/storage-account-overview.md#types-of-storage-accounts) 스토리지 계정을 선택하는 것이 좋습니다.
   - **사용자 ID**: 사용자가 할당한 관리 ID를 새로 만들고 이름을 지정합니다.
1. 양식 맨 아래에서 **검토 + 만들기** 를 선택합니다.

### <a name="deploy-a-live-pipeline"></a>라이브 파이프라인 배포

Video Analyzer 계정이 만들어지면 다음 단계를 진행하여 라이브 파이프라인 토폴로지 및 라이브 파이프라인을 만들 수 있습니다.
1. Video Analyzer 계정으로 이동하여 왼쪽 아래에서 **라이브** 메뉴 항목을 찾아 선택합니다. 
1. 토폴로지 평면 상단에서 **토폴로지 만들기** 옵션을 선택하여 라이브 토폴로지를 만듭니다. 포털 마법사 단계에 따라 라이브 파이프라인 토폴로지 만들기

    - **파이프라인 토폴로지 만들기** 마법사가 포털에 표시됩니다.
    - **샘플 토폴로지 체험**-> **RTSP 카메라에서 라이브 캡처, 기록 및 스트림** 토폴로지 선택-> **샘플 토폴로지 로드** 대화 상자에서 '계속' 선택을 진행합니다.
    - 'RTSP 원본에서 비디오 싱크로' 라이브 파이프라인 토폴로지 편집 마법사가 표시됩니다.
    - 토폴로지 만들기에 필요한 필드를 입력합니다. 
    
        - **토폴로지 이름** – 토폴로지의 이름을 입력합니다. 
        - **설명**(선택 사항) – 토폴로지에 대한 간략한 설명을 입력합니다. 
        - **종류**(‘라이브’로 미리 채워짐)
        - **Rtsp 원본** 노드의 경우: **전송** 속성 값을 TCP로 설정합니다.
        - 나머지 속성에 대해 기본 구성으로 **저장** 을 선택합니다.
1. 다음 단계는 이전 단계에서 만든 토폴로지를 사용하여 라이브 파이프라인을 만드는 것입니다. 

    - **파이프라인**-> **파이프라인 만들기** 선택 -> 이전 단계에서 만든 라이브 파이프라인 토폴로지를 선택하여 파이프라인을 만듭니다. 토폴로지 선택 후 **만들기** 를 클릭합니다.
    - **라이브 파이프라인 만들기** 마법사가 포털에 표시됩니다. 필수 필드를 입력합니다. 
    
        - **라이브 파이프라인 이름** – 고유한 이름을 사용하고 알파 숫자 및 대시를 허용합니다.
        - **비트 전송률** – 라이브 파이프라인용으로 예약된 최대 용량(Kbps)이며, 허용되는 범위는 500kbps~3000kbps입니다. RTSP 카메라 시뮬레이터 camera-1800s.mkv 파일에 대해 기본값 1000을 사용합니다(이 값은 사용된 샘플 비디오 파일과 일치해야 함). 
        - **rtspUserNameParameter**, **rtspPasswordParameter** - RTSP 카메라 시뮬레이터를 사용하는 경우 이러한 필드에 더미 값을 설정합니다. 그렇지 않으면 실제 RTSP 카메라 스트림에 대한 인증 자격 증명을 입력합니다.
        - **rtspUrlParameter** – `rtsp://<VMpublicIP>:554/media/camera-1800s.mkv`(RTSP 카메라 시뮬레이터용)를 사용하거나 그렇지 않으면 실제 RTSP 카메라 스트림 URL을 사용합니다.
        - **videoNameParameter** - 기록할 대상 비디오 리소스의 고유 이름입니다. 참고: 각 카메라(또는 MKV 파일)에 고유한 비디오 리소스 사용
    - **만들기** 를 선택하면 포털의 파이프라인 그리드에 파이프라인이 생성됩니다.
    - 그리드에서 만든 라이브 파이프라인을 선택하고 창 오른쪽에 있는 **활성화** 옵션을 선택하여 라이브 파이프라인을 활성화합니다. 그러면 라이브 파이프라인이 시작되고 비디오 녹화가 시작됩니다.
1. 이제 포털의 Video Analyzer 계정 -> **비디오** 창에서 비디오 리소스를 볼 수 있습니다. 해당 상태는 파이프라인이 활성 상태이고 기록 중이므로 **사용 중** 으로 표시됩니다.
1. 몇 초 후에 비디오를 선택하면 [짧은 대기 시간 스트림](../playback-recordings-how-to.md)을 볼 수 있습니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="클라우드의 라이브 파이프라인에서 캡처한 녹화된 비디오의 다이어그램입니다.":::

    > [!NOTE]
    > RTSP 카메라 시뮬레이터를 사용하는 경우 엔드투엔드 대기 시간을 정확하게 확인할 수 없습니다. 또한 RTSP 카메라 시뮬레이터가 MKV 파일의 끝에 도달 하면 중지됩니다. 라이브 파이프라인은 다시 연결을 시도하며, 잠시 후 시뮬레이터는 파일의 시작 부분에서 스트림을 다시 시작 합니다. 이 라이브 파이프라인이 많은 시간 동안 실행되도록 하는 경우 시뮬레이터가 중지되었다가 다시 시작될 때마다 비디오 기록에 차이가 표시됩니다.
* 필요한 경우 활동 로그를 참조하여 배포 작업을 신속하게 확인합니다. 모니터링 및 이벤트 로그는 [여기](./monitor-log-cloud.md)를 참조하세요.
* 파이프라인 기록을 비활성화하려면 Video Analyzer 계정으로 이동하고, 왼쪽 패널에서 **라이브**-> **파이프라인** 선택-> 비활성화할 파이프라인 선택 후 파이프라인 그리드에서 **비활성화** 를 선택하여 기록을 중지합니다. 
* 또한 파이프라인 및 토폴로지가 필요하지 않은 경우 계속해서 삭제할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 빠른 시작이나 자습서를 사용하려면 여기서 만든 리소스를 유지합니다. 그렇지 않으면 Azure Portal, 리소스 그룹으로 차례로 이동하고, 이 빠른 시작을 실행한 리소스 그룹을 선택한 다음, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- 비디오의 [보존 정책](../manage-retention-policy.md)을 관리하는 방법에 대해 자세히 알아보기
- [여기](https://github.com/Azure/video-analyzer/tree/main/media)에서 미디어 시뮬레이터에 다른 MKV 샘플 파일을 사용해 보세요. 샘플 파일의 비트 전송률은 파이프라인 설정과 일치해야 합니다.
- [클라우드 파이프라인에 대한 모니터링 및 로깅](./monitor-log-cloud.md)에 대해 자세히 알아보기
