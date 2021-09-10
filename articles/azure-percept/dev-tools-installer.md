---
title: Azure Percept 개발 도구 설치
description: 개발 도구 팩 설치 프로그램을 사용하여 Azure Percept로 고급 개발을 가속화하는 방법에 대해 자세히 알아보세요.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f445170d3cd2b2cfeb45a85371409a539368ba22
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225005"
---
# <a name="install-azure-percept-development-tools"></a>Azure Percept 개발 도구 설치

개발 도구 팩 설치 프로그램은 고급 인텔리전트 에지 솔루션을 개발하는 데 필요한 모든 도구를 설치하고 구성하는 원스탑 솔루션입니다.

## <a name="mandatory-tools"></a>필수 도구

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 이상](https://www.python.org/)
* [Docker 20.10](https://www.docker.com/)
* [PIP3 21.1](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 2.0](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1.2](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>선택적 도구

* [NVIDIA DeepStream SDK 5](https://developer.nvidia.com/deepstream-sdk)(NVIDIA 가속기의 솔루션을 개발하는 도구 키트)
* [Intel OpenVINO Toolkit 2021.3](https://docs.openvinotoolkit.org/)(Intel Accelerators용 솔루션 개발 도구 키트)
* [Lobe.ai 0.9](https://lobe.ai/)  
* [Streamlit 0.8](https://www.streamlit.io/)
* [Pytorch 1.4.0(Windows) 또는 1.2.0(Linux)](https://pytorch.org/)
* [Miniconda 4.5](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 7.7](https://chainer.org/)
* [Caffe 1.0](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 11.2](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>알려진 문제

- Docker가 제대로 실행되지 않으면 선택적 Caffe, NVIDIA DeepStream SDK 및 Intel OpenVINO Toolkit 설치가 실패할 수 있습니다. 이러한 선택적 도구를 설치하려면 Dev Tools Pack 설치 관리자를 통해 설치를 시도하기 전에 Docker가 설치되어 실행 중인지 확인합니다.

- Mac 버전에 설치된 선택적 CUDA Toolkit은 10.0.130입니다. CUDA Toolkit 11은 macOSity에서 애플리케이션 개발 또는 실행을 더 이상 지원하지 않습니다.

## <a name="docker-minimum-requirements"></a>Docker 최소 요구 사항

### <a name="windows"></a>Windows

- Windows 10 64비트: Pro, Enterprise 또는 교육용(빌드 16299 이상).

- Hyper-V 및 컨테이너 Windows 기능을 사용하도록 설정해야 합니다. Windows 10에서 Hyper-V를 성공적으로 실행하려면 다음 하드웨어 필수 조건이 필요합니다.

    - [SLAT(두 번째 수준 주소 변환)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)를 사용하는 64비트 프로세서
    - 4GB 시스템 RAM
    - BIOS 수준 하드웨어 가상화 지원은 BIOS 설정에서 활성화해야 합니다. 자세한 내용은 가상화를 참조하세요.

> [!NOTE]
> Docker는 Windows 10 운영 체제에 대한 Microsoft의 지원 수명 주기를 기반으로 Windows의 Docker Desktop을 지원합니다. 자세한 내용은 [Windows 수명 주기 팩트 시트](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet)를 참조하세요.

[Windows에 Docker Desktop을 설치](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows)하는 방법에 대해 자세히 알아보세요.

### <a name="mac"></a>Mac

- Mac은 다음 특성이 있는 2010 이상의 최신 모델이어야 합니다.
    - Intel 프로세서
    - EPT(확장 페이지 테이블) 및 무제한 모드를 비롯한 MMU(메모리 관리 단위) 가상화에 대한 Intel의 하드웨어 지원. 터미널에서 다음 명령을 실행하여 머신에 이 지원이 있는지 확인할 수 있습니다. ```sysctl kern.hv_support```. Mac에서 하이퍼바이저 프레임워크를 지원하는 경우, 명령에서 ```kern.hv_support: 1```을 인쇄합니다.

- macOS 버전 10.14 이상(Mojave, Catalina.properties 또는 Big Sur). 최신 버전의 macOS로 업그레이드하는 것이 좋습니다. macOS를 버전 10.15로 업그레이드한 후 문제가 발생하는 경우 이 버전의 macOS와 호환되도록 최신 버전의 Docker Desktop을 설치해야 합니다.

- RAM 4GB 이상

- 4\.3.30 이전 버전으로 VirtualBox를 설치하지 마세요. Docker Desktop과 호환되지 않습니다.

- 설치 프로그램은 Apple M1에서 지원되지 않습니다.

[Mac에 Docker Desktop을 설치](https://docs.docker.com/docker-for-mac/install/#system-requirements)하는 방법에 대해 자세히 알아보세요.

## <a name="launch-the-installer"></a>설치 관리자를 시작합니다.

[Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)또는 [Mac](https://go.microsoft.com/fwlink/?linkid=2132296)용 개발 도구 팩 설치 프로그램을 다운로드합니다. 아래에 설명된 대로 플랫폼에 따라 설치 프로그램을 시작합니다.

### <a name="windows"></a>Windows

1. **개발-도구-팩-설치 프로그램** 을 클릭하여 설치 마법사를 엽니다.

### <a name="mac"></a>Mac

1. 다운로드한 후 **Dev-Tools-Pack-Installer.app** 파일을 **Applications** 폴더로 이동합니다.

1. **Dev-Tools-Pack-Installer.app** 을 클릭하여 설치 마법사를 엽니다.

1. "미확인 개발자" 보안 대화 상자가 표시되는 경우:

    1. **시스템 기본 설정**  ->  **보안 및 개인 정보**  ->  **일반** 으로 이동하고 **Dev-Tools-Pack-Installer.app** 옆의 **열기** 를 클릭합니다.
    1. Electron 아이콘을 클릭합니다.
    1. 보안 대화 상자에서 **열기** 를 클릭합니다.

### <a name="linux"></a>Linux

1. 브라우저에서 메시지가 표시되면 **저장** 을 클릭하여 설치 프로그램 다운로드를 완료합니다.

1. **.appimage** 파일에 실행 권한 추가:

    1. Linux 터미널을 엽니다.

    1. **다운로드** 폴더로 이동하려면 터미널에서 다음을 입력합니다.

        ```bash
        cd ~/Downloads/
        ```

    1. AppImage 실행 파일 만들기:

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. 설치 프로그램을 실행합니다.

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. **.appimage** 파일에 실행 권한 추가:

    1. .appimage 파일을 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.
    1. **권한** 탭을 엽니다.
    1. **프로그램으로 실행 파일 허용** 옆의 상자를 선택합니다.
    1. **속성** 을 닫고 **.appimage** 파일을 엽니다.

## <a name="run-the-installer"></a>설치 관리자 실행

1. **개발 도구 팩 설치 프로그램 설치** 페이지에서 **라이선스 보기** 를 클릭하여 설치 프로그램에 포함된 각 소프트웨어 패키지의 사용권 계약을 확인합니다. 사용권 계약의 조건에 동의하면 상자를 선택하고 **다음** 을 클릭합니다.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="설치 프로그램의 사용권 계약 화면입니다.":::

1. **개인정보처리방침** 을 클릭하여 Microsoft 개인정보처리방침을 검토합니다. 개인정보처리방침 약관에 동의하고 Microsoft에 진단 데이터를 보내려면 **예** 를 선택하고 **다음** 을 클릭합니다. 그렇지 않은 경우 **아니요** 를 선택하고 **다음** 을 클릭합니다.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="설치 프로그램의 개인정보처리방침 계약 화면입니다.":::

1. **구성 요소 구성** 페이지에서 설치하려는 선택적 도구를 선택합니다 (필수 도구는 기본적으로 설치됨).

    1. Azure Percept DK에 포함된 Azure Percept Audio SoM을 사용하는 경우 Intel OpenVino Toolkit 및 Miniconda3를 설치해야 합니다.

    1. **설치** 를 클릭하여 설치를 계속합니다.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="사용 가능한 소프트웨어 패키지를 보여 주는 설치 프로그램 화면.":::

1. 선택한 모든 구성 요소를 성공적으로 설치하면 마법사가 **설치 마법사 완료** 페이지로 진행됩니다. **완료** 를 클릭하여 마법사를 종료합니다.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="설치 프로그램 완료 화면.":::

## <a name="docker-status-check"></a>Docker 상태 확인

설치 프로그램에서 Docker Desktop이 정상 실행 상태인지 확인하는 알림을 표시하는 경우 다음 단계를 참조하세요.

### <a name="windows"></a>Windows

1. 시스템 트레이 숨김 아이콘을 확장합니다.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="시스템 트레이.":::

1. Docker Desktop 아이콘에서 **Docker Desktop이 실행 중** 임을 표시하는지 확인합니다.

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker 상태.":::

1. 시스템 트레이에 위에 나열된 아이콘이 표시되지 않으면 시작 메뉴에서 Docker Desktop을 시작합니다.

1. Docker에서 다시 부팅하라는 메시지가 표시되면 설치 프로그램을 닫고 재부팅이 완료되고 Docker가 실행 중 상태가 된 후 다시 시작할 수 있습니다. 성공적으로 설치된 타사 애플리케이션이 검색되며 자동으로 다시 설치되지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure Percept DK의 고급 개발을 시작하려면 [Azure Percept 고급 개발 리포지토지](https://github.com/microsoft/azure-percept-advanced-development)를 확인하세요.
