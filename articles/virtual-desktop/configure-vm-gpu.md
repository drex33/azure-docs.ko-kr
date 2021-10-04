---
title: Azure Virtual Desktop용 GPU 구성 - Azure
description: Azure Virtual Desktop에서 GPU 가속 렌더링 및 인코딩을 사용하도록 설정하는 방법입니다.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 2ace40157681b250b56fcd595486260f07ec80c3
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400708"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 GPU(그래픽 처리 장치) 가속 구성

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md)를 참조하세요.

Azure Virtual Desktop은 향상된 앱 성능 및 확장성을 위해 GPU 가속 렌더링 및 인코딩을 지원합니다. GPU 가속은 특히 그래픽이 많은 앱에 매우 중요합니다.

이 문서의 지침에 따라 GPU에 최적화된 Azure 가상 머신을 만들고, 호스트 풀에 추가하고, 렌더링 및 인코딩에 GPU 가속을 사용하도록 구성합니다. 이 문서에서는 Azure Virtual Desktop 테넌트가 이미 구성되어 있다고 가정합니다.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>적절한 GPU에 최적화된 Azure 가상 머신 크기 선택

Azure의 [NV 시리즈](../virtual-machines/nv-series.md), [NVv3 시리즈](../virtual-machines/nvv3-series.md)또는 [NVv4 시리즈](../virtual-machines/nvv4-series.md) VM 크기 중 하나를 선택합니다. 이러한 기능은 앱 및 데스크톱 가상화에 맞게 조정되었으며 대부분의 앱과 Windows 사용자 인터페이스에 GPU 속도를 향상시킵니다. 호스트 풀에 적합한 선택은 특정 앱 워크로드, 원하는 사용자 환경의 품질 및 비용을 비롯한 다양한 요인에 따라 달라집니다. 일반적으로 더 많은 성능의 대규모 GPU는 주어진 사용자 밀도에서 더 나은 사용자 경험을 제공하며 적은 수의 소규모 GPU는 비용과 품질을 더 세밀하게 제어할 수 있습니다. VM을 선택할 때 NV 시리즈 VM 사용 중지를 고려 하 여 [nv](../virtual-machines/nv-series-retirement.md) 사용 중지에 대 한 세부 정보

>[!NOTE]
>Azure의 NC, NCv2, NCv3, ND 및 NDv2 시리즈 VM은 일반적으로 Azure Virtual Desktop 세션 호스트에 적합하지 않습니다. 이러한 VM은 NVIDIA CUDA로 구축된 것과 같은 특수한 고성능 컴퓨팅 또는 기계 학습 도구에 맞게 조정되었습니다. 대다수 앱 또는 Windows 사용자 인터페이스에는 GPU 가속을 지원하지 않습니다.


## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>호스트 풀 생성, 가상 머신 프로비저닝 및 앱 그룹 구성

선택한 크기의 VM을 사용하여 새 호스트 풀을 만듭니다. 자세한 내용은 [자습서: Azure Portal로 호스트 풀 만들기](./create-host-pools-azure-marketplace.md)를 참조하세요.

Azure Virtual Desktop은 다음과 같은 운영 체제에서 GPU 가속 렌더링 및 인코딩을 지원합니다.

* Windows 10 버전 1511 이상
* Windows Server 2016 이상

>[!NOTE]
>다중 세션 OS는 구체적으로 나열 되어 있지 않지만 NV 인스턴스 그리드 라이선스는 25 명의 동시 사용자를 지원 합니다. [nv 시리즈](../virtual-machines/nv-series.md) 를 참조 하세요.

또한 앱 그룹을 구성하거나 새 호스트 풀을 만들 때 자동으로 생성되는 기본 데스크톱 앱 그룹(“데스크톱 애플리케이션 그룹”이라고 함)을 사용해야 합니다. 지침은 [자습서: Azure Virtual Desktop에 대한 앱 그룹 관리](./manage-app-groups.md)를 참조하세요.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>가상 머신에서 지원되는 그래픽 드라이버 설치

Azure Virtual Desktop에서 Azure N 시리즈 VM의 GPU 기능을 활용하려면 적절한 그래픽 드라이버를 설치해야 합니다. [지원되는 운영 체제 및 드라이버](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers)의 지침에 따라 드라이버를 설치합니다. Azure에서 배포된 드라이버만 지원됩니다.

* Azure NV 시리즈 또는 NVv3 시리즈 VM의 경우 NVIDIA CUDA 드라이버가 아닌 NVIDIA GRID 드라이버만 대다수 앱 및 Windows 사용자 인터페이스에 GPU 가속을 지원합니다. 드라이버를 수동으로 설치하도록 선택하는 경우 GRID 드라이버를 설치해야 합니다. Azure VM 확장을 사용하여 드라이버를 설치하도록 선택하면 이러한 VM 크기에 대해 GRID 드라이버가 자동으로 설치됩니다.
* Azure NVv4 시리즈 VM의 경우 Azure에서 제공하는 AMD 드라이버를 설치합니다. Azure VM 확장을 사용하여 자동으로 설치하거나 수동으로 설치할 수 있습니다.

드라이버를 설치한 후에는 VM을 다시 시작해야 합니다. 위 지침의 확인 단계에 따라 그래픽 드라이버가 성공적으로 설치되었는지 확인합니다.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU 가속 앱 렌더링 구성

기본적으로 다중 세션 구성에서 실행되는 앱 및 데스크톱은 CPU를 사용하여 렌더링되며 사용 가능한 GPU를 렌더링에 활용하지 않습니다. 세션 호스트에 대한 그룹 정책을 구성하여 GPU 가속 렌더링을 사용하도록 설정합니다.

1. 로컬 관리자 권한이 있는 계정을 사용하여 VM의 데스크톱에 연결합니다.
2. 시작 메뉴를 열고 “gpedit.msc”를 입력하여 그룹 정책 편집기를 엽니다.
3. 트리를 탐색하여 **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **원격 세션 환경** 으로 이동합니다.
4. **모든 원격 데스크톱 서비스 세션에 대해 하드웨어 그래픽 어댑터 사용** 정책을 선택하고, 해당 정책을 **사용** 으로 설정하여 원격 세션에서 GPU 렌더링을 사용하도록 설정합니다.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU 가속 프레임 인코딩 구성

원격 데스크톱은 원격 데스크톱 클라이언트에 전송하기 위해 앱 및 데스크톱에 의해 렌더링된 모든 그래픽(GPU 또는 CPU를 사용하여 렌더링됨)을 인코딩합니다. 화면의 일부가 자주 업데이트되는 경우 화면의 해당 부분이 비디오 코덱(H.264/AVC)으로 인코딩됩니다. 기본적으로 원격 데스크톱은 이 인코딩에 대해 사용 가능한 GPU를 활용하지 않습니다. 세션 호스트에 대한 그룹 정책을 구성하여 GPU 가속 프레임 인코딩을 사용하도록 설정합니다. 위의 단계를 계속합니다.

>[!NOTE]
>GPU 가속 프레임 인코딩은 NVv4 시리즈 VM에서 사용할 수 없습니다.

1. **원격 데스크톱 연결에 대한 h.264/AVC 하드웨어 인코딩 구성** 정책을 선택하고 이 정책을 **사용** 하도록 설정하여 원격 세션에서 AVC/H.264에 대해 하드웨어 인코딩을 사용하도록 설정합니다.

    >[!NOTE]
    >Windows Server 2016에서는 **AVC 하드웨어 인코딩 우선** 옵션을 **항상 시도** 로 설정합니다.

2. 그룹 정책을 편집했으므로 이제 그룹 정책 업데이트를 강제로 적용합니다. 명령 프롬프트를 열고 다음을 입력합니다.

    ```cmd
    gpupdate.exe /force
    ```

3. 원격 데스크톱 세션에서 로그아웃합니다.

## <a name="configure-fullscreen-video-encoding"></a>전체 화면 비디오 인코딩 구성

3D 모델링, CAD/CAM 및 비디오 애플리케이션과 같이 높은 프레임 속도의 콘텐츠를 생성하는 애플리케이션을 자주 사용하는 경우 원격 세션에 대해 전체 화면 비디오 인코딩을 사용하도록 선택할 수 있습니다. 전체 화면 비디오 프로필은 네트워크 대역폭과 세션 호스트 및 클라이언트 리소스를 모두 사용하여 이러한 애플리케이션에 더 높은 프레임 속도와 더 나은 사용자 경험을 제공합니다. 전체 화면 비디오 인코딩에는 GPU 가속 프레임 인코딩을 사용하는 것이 좋습니다. 세션 호스트에 대한 그룹 정책을 구성하여 전체 화면 비디오 인코딩을 사용하도록 설정합니다. 위의 단계를 계속합니다.

1. **원격 데스크톱 연결에 h.264/AVC 444 그래픽 모드 우선 사용** 정책을 선택하고 이 정책을 **사용** 하도록 설정하여 원격 세션에서 h.264/AVC 444 코덱을 강제로 적용합니다.
2. 그룹 정책을 편집했으므로 이제 그룹 정책 업데이트를 강제로 적용합니다. 명령 프롬프트를 열고 다음을 입력합니다.

    ```cmd
    gpupdate.exe /force
    ```

3. 원격 데스크톱 세션에서 로그아웃합니다.
## <a name="verify-gpu-accelerated-app-rendering"></a>GPU 가속 앱 렌더링 확인

앱이 렌더링에 GPU를 사용하는지 확인하려면 다음 중 하나를 수행합니다.

* NVIDIA GPU를 사용하는 Azure VM의 경우 [드라이버 설치 확인](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation)에 설명된 대로 `nvidia-smi` 유틸리티를 사용하여 앱을 실행할 때 GPU 사용률을 확인합니다.
* 지원되는 운영 체제 버전에서는 작업 관리자를 사용하여 GPU 사용률을 확인할 수 있습니다. “성능” 탭에서 GPU를 선택하여 앱에서 GPU를 활용하는지 확인합니다.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU 가속 프레임 인코딩 확인

원격 데스크톱이 GPU 가속 인코딩을 사용하는지 확인하려면 다음을 수행합니다.

1. Azure Virtual Desktop 클라이언트를 사용하여 VM의 데스크톱에 연결합니다.
2. 이벤트 뷰어를 시작하고 다음 노드로 이동합니다. **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operational**
3. GPU 가속 인코딩이 사용되는지 확인하려면 이벤트 ID 170을 찾습니다. “AVC 하드웨어 인코더 사용: 1”가 표시된 경우 GPU 인코딩이 사용되는 것입니다.

## <a name="verify-fullscreen-video-encoding"></a>전체 화면 비디오 인코딩 확인

원격 데스크톱이 전체 화면 비디오 인코딩을 사용하는지 확인하려면 다음을 수행합니다.

1. Azure Virtual Desktop 클라이언트를 사용하여 VM의 데스크톱에 연결합니다.
2. 이벤트 뷰어를 시작하고 다음 노드로 이동합니다. **애플리케이션 및 서비스 로그** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operational**
3. 전체 화면 비디오 인코딩이 사용되는지 확인하려면 이벤트 ID 162를 찾습니다. “AVC 사용 가능: 1 초기 프로필: 2048”이 표시되면 AVC 444가 사용되는 것입니다.

## <a name="next-steps"></a>다음 단계

이러한 지침을 따르면 하나의 세션 호스트(하나의 VM)에서 GPU 가속을 실행할 수 있습니다. 더 큰 호스트 풀에서 GPU 가속을 사용하기 위한 몇 가지 추가 고려 사항은 다음과 같습니다.

* [VM 확장](../virtual-machines/extensions/overview.md)을 사용하여 여러 VM에서 드라이버 설치 및 업데이트를 간소화하는 것이 좋습니다. NVIDIA GPU를 사용하는 VM에 대해 [NVIDIA GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-gpu-windows.md)을 사용하고 AMD GPU를 사용하는 VM에 대해 [AMD GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md)을 사용합니다.
* 여러 VM에서 그룹 정책 구성을 간소화하기 위해서는 Active Directory 그룹 정책을 사용하는 것이 좋습니다. Active Directory 도메인에 그룹 정책을 배포하는 방법에 대한 자세한 내용은 [그룹 정책 개체로 작업하기](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11))를 참조하세요.