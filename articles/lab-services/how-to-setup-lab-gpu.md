---
title: Azure Lab Services의 GPU로 랩 설정 | Microsoft Docs
description: GPU(그래픽 처리 장치) 가상 머신으로 랩을 설정하는 방법에 대해 알아봅니다.
author: nicolela
ms.topic: how-to
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: e79038f7e05fe2605f9a861e7599fc942fdf9fff
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180080"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>GPU 가상 머신을 사용하여 랩 설정

이 문서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

- *시각화* 및 *컴퓨팅* GPU(그래픽 처리 장치) 중에서 선택합니다.
- 적절한 GPU 드라이버가 설치되어 있는지 확인합니다.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>시각화 및 컴퓨팅 GPU 크기 중에서 선택
랩 만들기 마법사의 첫 번째 페이지의 **어떤 크기의 가상 머신이 필요한가요?** 드롭다운 목록에서 클래스에 필요한 VM의 크기를 선택합니다.  

![VM 크기를 선택하기 위한 “새 랩” 창의 스크린샷](./media/how-to-setup-gpu/lab-gpu-selection.png)

이 프로세스에는 **시각화** 또는 **컴퓨팅** GPU를 선택할 수 있는 옵션이 있습니다.  학생들이 사용하는 소프트웨어를 기반으로 하는 GPU 유형을 선택하는 것이 중요합니다.  

다음 표에서 설명하는 것처럼 *컴퓨팅* GPU 크기는 컴퓨팅 집약적인 애플리케이션을 위한 것입니다.  예를 들어 [자연어 처리 클래스 형식의 딥 러닝](./class-type-deep-learning-natural-language-processing.md)은 **작은 GPU(컴퓨팅)** 크기를 사용합니다.  학생들은 [Data Science Virtual Machine 이미지](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)가 제공하는 딥 러닝 프레임워크와 도구를 사용하여 딥 러닝 모델에게 방대한 데이터 세트를 학습시키기 때문에 컴퓨팅 GPU는 이러한 종류의 클래스에 적합합니다.

| 크기 | 코어 수 | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| 소형 GPU(컴퓨팅) | -&nbsp;6&nbsp;코어<br>-&nbsp;56&nbsp;GB&nbsp;RAM  | [Standard_NC6](../virtual-machines/nc-series.md) |이 크기는 AI(인공 지능)와 딥 러닝 같은 컴퓨팅을 많이 사용하는 애플리케이션에 가장 적합합니다. |

*시각화* GPU 크기는 그래픽을 많이 사용하는 애플리케이션을 위한 것입니다.  예를 들어 [SOLIDWORKS 엔지니어링 클래스 형식](./class-type-solidworks.md)은 **작은 GPU(시각화)** 크기를 사용하여 표시합니다.  시각화 GPU에는 이러한 종류의 클래스가 적합합니다. 학생들은 솔리드 개체를 모델링하고 시각화하기 위해 CAD(SOLIDWORKS 3D 컴퓨터 보조 설계) 환경과 상호 작용하기 때문입니다.

| 크기 | 코어 수 | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| 소형 GPU(시각화) | -&nbsp;6&nbsp;코어<br>-&nbsp;56&nbsp;GB&nbsp;RAM  | [Standard_NV6](../virtual-machines/nv-series.md) | 이 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. |
| 중간 GPU(시각화) | -&nbsp;12&nbsp;코어<br>-&nbsp;112&nbsp;GB&nbsp;RAM  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | 이 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. |

> [!NOTE]
> 클래스룸 랩을 만들 때 이러한 VM 크기 중 일부가 목록에 표시되지 않을 수 있습니다. 이 목록은 랩 위치의 현재 용량을 기준으로 채워집니다. 랩 계정 작성자가 [랩 작성자가 랩의 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)할 경우 랩에 다른 위치를 선택하고 VM 크기를 사용할 수 있는지 확인할 수 있습니다. VM의 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/?products=virtual-machines)을 참조하세요.

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>적절한 GPU 드라이버가 설치되어 있는지 확인합니다.
랩 VM의 GPU 기능을 활용하려면 적절한 GPU 드라이버가 설치되어 있는지 확인합니다.  랩 만들기 마법사에서 GPU VM 크기를 선택할 때 **GPU 드라이버 설치** 옵션을 선택할 수 있습니다.  

![“GPU 드라이버 설치” 옵션을 보여주는 “새 랩”의 스크린샷](./media/how-to-setup-gpu/lab-gpu-drivers.png)

위의 그림에 표시된 것처럼 이 옵션은 기본값으로 사용하도록 설정되어 있으며, 이 옵션은 선택한 GPU 및 이미지 유형에 대해 최근에 릴리스된 드라이버가 설치되어 있는지 확인합니다.
- *컴퓨팅* GPU 크기를 선택하는 경우 랩 VM은 [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU로 구동됩니다.  이 경우 고성능 컴퓨팅을 가능하게 하는 최근의 [CUDA(컴퓨팅 통합 디바이스 아키텍처)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) 드라이버가 설치되어 있습니다.
- *시각화* GPU 크기를 선택하는 경우 랩 VM은 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 [GRID 기술](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)로 구동됩니다.  이 경우 그래픽을 많이 사용하는 애플리케이션도 사용할 수 있게 해주는 최근 GRID 드라이버가 설치됩니다.

> [!IMPORTANT]
> **GPU 드라이버 설치** 옵션은 랩의 이미지에 없는 드라이버만 설치합니다.  예를 들어 GPU 드라이버는 Azure Marketplace의 [데이터 과학 이미지](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm)에 이미 설치되어 있습니다.  데이터 과학 이미지를 사용하여 랩을 만들고 **GPU 드라이버 설치** 를 선택하면 드라이버가 최신 버전으로 업데이트되지 않습니다.  드라이버를 업데이트하려면 다음 섹션에 설명된 대로 드라이버를 수동으로 설치해야 합니다.  

### <a name="install-the-drivers-manually"></a>수동으로 드라이버 설치
Azure Lab Services가 자동으로 설치하는 버전과 다른 버전의 드라이버를 설치해야 할 수도 있습니다.  이 섹션에서는 *컴퓨팅* GPU를 사용하는지 *시각화* GPU를 사용하는지에 따라 적절한 드라이버를 수동으로 설치하는 방법을 보여줍니다.

#### <a name="install-the-compute-gpu-drivers"></a>컴퓨팅 GPU 드라이버 설치

*컴퓨팅* GPU 크기에 대한 드라이버를 수동으로 설치하려면 다음을 수행합니다.

1. 랩 만들기 마법사에서 [랩을 만들](./how-to-manage-classroom-labs.md) 때 **GPU 드라이버 설치** 설정을 사용하지 않도록 설정합니다.

1. 랩을 만든 후 템플릿 VM에 연결하여 적절한 드라이버를 설치합니다.

   ![NVIDIA 드라이버 다운로드 페이지의 스크린샷](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. 브라우저에서 [NVIDIA 드라이버 다운로드 페이지](https://www.nvidia.com/Download/index.aspx)로 이동합니다.  
   b. **제품 유형** 을 **Tesla** 로 설정합니다.  
   다. **제품 시리즈** 를 **K-시리즈** 로 설정합니다.  
   d. 랩을 만들 때 선택한 기본 이미지 유형에 따라 **운영 체제** 를 설정합니다.  
   e. **CUDA 도구 키트** 를 필요한 CUDA 드라이버의 버전으로 설정합니다.  
   f. **검색** 을 선택하여 드라이버를 찾습니다.  
   g. **다운로드** 를 선택하여 설치 프로그램을 다운로드합니다.  
   h. 드라이버를 템플릿 VM에 설치하도록 설치 프로그램을 실행합니다.  
1. [설치된 드라이버 유효성 검사](how-to-setup-lab-gpu.md#validate-the-installed-drivers) 섹션의 지침에 따라 드라이버가 올바르게 설치되었는지 확인합니다. 
1. 클래스에 필요한 드라이버 및 기타 소프트웨어를 설치한 후 **게시** 를 선택하여 학생의 VM을 만듭니다.

> [!NOTE]
> Linux 이미지를 사용하는 경우 설치 프로그램을 다운로드한 후 [Linux에서 CUDA 드라이버 설치](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#install-cuda-drivers-on-n-series-vms)의 지침에 따라 드라이버를 설치합니다.

#### <a name="install-the-visualization-gpu-drivers"></a>시각화 GPU 드라이버 설치

*시각화* GPU 크기에 대한 드라이버를 수동으로 설치하려면 다음을 수행합니다.

1. 랩 만들기 마법사에서 [랩을 만들](./how-to-manage-classroom-labs.md) 때 **GPU 드라이버 설치** 설정을 사용하지 않도록 설정합니다.
1. 랩을 만든 후 템플릿 VM에 연결하여 적절한 드라이버를 설치합니다.
1. 운영 체제에 대한 지침에 따라 Microsoft에서 제공하는 GRID 드라이버를 템플릿 VM에 설치합니다.
   -  [Windows NVIDIA 그리드 드라이버](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID 드라이버](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#nvidia-grid-drivers)
  
1. 템플릿 VM을 다시 시작합니다.
1. [설치된 드라이버 유효성 검사](how-to-setup-lab-gpu.md#validate-the-installed-drivers) 섹션의 지침에 따라 드라이버가 올바르게 설치되었는지 확인합니다.
1. 클래스에 필요한 드라이버 및 기타 소프트웨어를 설치한 후 **게시** 를 선택하여 학생의 VM을 만듭니다.

### <a name="validate-the-installed-drivers"></a>설치된 드라이버의 유효성 검사
이 섹션은 GPU 드라이버가 제대로 설치되었는지 확인하는 방법을 설명합니다.

#### <a name="windows-images"></a>Windows 이미지
1.  [Windows를 실행하는 N 시리즈 VM에 NVIDIA GPU 드라이버 설치](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation)의 “드라이버 설치 확인” 섹션의 지침을 따릅니다.
1.  *시각화* GPU를 사용하는 경우 다음을 수행할 수도 있습니다.
    - NVIDIA 제어판에서 GPU 설정을 보고 조정합니다. 이렇게 하려면 **Windows 제어판** 에서 **하드웨어** 를 선택한 다음 **NVIDIA 제어판** 을 선택합니다.

      ![NVIDIA 제어판 링크를 보여 주는 Windows 제어판의 스크린샷](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - **작업 관리자** 를 사용하여 GPU 성능을 확인합니다.  이렇게 하려면 **성능** 탭을 선택한 다음 **GPU** 옵션을 선택합니다.

       ![작업 관리자 GPU 성능 탭을 보여 주는 스크린샷](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > NVIDIA 제어판 설정은 *시각화* GPU에만 액세스할 수 있습니다.  컴퓨팅 GPU에 대한 NVIDIA 제어판을 열려고 하면 다음과 같은 오류 메시지가 표시됩니다. “NVIDIA 표시 설정을 사용할 수 없습니다.  현재 NVIDIA GPU에 연결된 디스플레이를 사용하고 있지 않습니다.”  마찬가지로, 작업 관리자의 GPU 성능 정보는 시각화 GPU에 대해서만 제공됩니다.

 시나리오에 따라 GPU가 제대로 구성되었는지 확인하기 위해 추가 유효성 검사를 수행해야 할 수도 있습니다.  특정 버전의 드라이버가 필요한 예제를 설명하는 [Python 및 Jupyter Notebook](./class-type-jupyter-notebook.md#template-virtual-machine)에 대한 클래스 형식을 읽어 보세요.

#### <a name="linux-images"></a>Linux 이미지
[Linux를 실행하는 N 시리즈 VM에 NVIDIA GPU 드라이버 설치](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation)의 “드라이버 설치 확인” 섹션의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [SOLIDWORKS CAD(컴퓨터 보조 디자인) 클래스 형식](class-type-solidworks.md)
- [MATLAB(매트릭스 실험실) 클래스 유형](class-type-matlab.md)