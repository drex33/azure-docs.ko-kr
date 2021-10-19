---
title: Python 및 Jupyter Notebook을 사용하여 데이터 과학을 교육하기 위한 랩 설정 | Microsoft Docs
description: Python 및 Jupyter Notebook을 사용하여 데이터 과학을 교육하기 위한 랩을 설정하는 방법을 알아봅니다.
author: emaher
ms.topic: how-to
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: f52215fbcaf2d3a037863853366aaae9bb55a3fb
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176893"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Python 및 Jupyter Notebook을 사용하여 데이터 과학을 교육하기 위한 랩 설정
이 문서에서는 학생에게 [Jupyter Notebook](http://jupyter-notebook.readthedocs.io/) 사용 방법과 학생이 본인의 VM(가상 머신)에서 Notebook에 연결하는 방법을 교육하는 데 필요한 도구를 사용하여 Lab Services에서 템플릿 VM(가상 머신)을 설정하는 방법을 설명합니다.

Jupyter Notebook은 서식 있는 텍스트와 실행 가능한 Python 원본 코드를 노트북이라는 단일 캔버스에 쉽게 결합할 수 있는 오픈 소스 프로젝트입니다. 노트북을 실행하면 입력 및 출력의 선형 레코드가 생성됩니다. 이러한 출력에는 텍스트, 정보 테이블, 분산형 플롯 등이 포함될 수 있습니다.

## <a name="set-up-the-lab"></a>랩 설정

### <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독과 랩 계정에 대한 액세스 권한이 필요합니다. 조직의 관리자에게 문의하여 기존 Azure 구독에 액세스할 수 있는지 확인하세요. Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

Azure 구독이 있으면 자습서: [랩 계정 설정](tutorial-setup-lab-account.md)의 지침에 따라 Azure Lab Services에서 새 랩 계정을 만듭니다. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정
랩 계정에 대해 아래 표에 설명된 설정을 사용합니다. 마켓플레이스 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Marketplace 이미지 지정](specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
| Marketplace 이미지 | 랩 계정 내에서 운영 체제 요구 사항에 따라 다음 Azure Marketplace 이미지 중 하나를 사용하도록 설정합니다. <br/><ul><li>Data Science Virtual Machine – Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18.04</li></ul> |

> [!NOTE]
> 이 문서에서는 Azure Marketplace에서 제공하는 Data Science Virtual Machine 이미지를 사용합니다. 이 이미지는 Jupyter Notebook을 사용하여 미리 구성되어 있습니다. 그러나 이 이미지에는 데이터 과학을 위한 여러 가지 다른 개발 및 모델링 도구도 포함되어 있습니다. 이러한 추가 도구를 원하지 않고 Jupyter Notebook만 포함된 간단한 설정을 원하는 경우 사용자 지정 VM 이미지를 만듭니다. 예를 들어 [Azure에 JupyperHub를 설치](http://tljh.jupyter.org/en/latest/install/azure.html)합니다. 사용자 지정 이미지를 만들었으면 해당 이미지를 공유 이미지 갤러리에 업로드하여 Azure Lab Services 내에서 이 이미지를 사용할 수 있습니다. [Azure Lab Services에서 Shared Image Gallery 사용](how-to-attach-detach-shared-image-gallery.md)에 대해 자세히 알아보세요. 

### <a name="lab-settings"></a>랩 설정
클래스룸 랩을 설정할 때 다음 표와 같이 **가상 머신 크기** 및 **가상 머신 이미지** 설정을 구성합니다. 클래스룸 랩을 만드는 방법에 대한 지침은 [클래스룸 랩 설정](tutorial-setup-classroom-lab.md)을 참조하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ | 
| 가상 머신 크기 | <p>여기서 선택하는 크기는 실행하려는 워크로드에 따라 달라집니다.</p><ul><li>소형 또는 중간 – Jupyter Notebook 기본 액세스 설정에 적합</li><li>소형 GPU(컴퓨팅) - AI나 Deep Learning처럼 컴퓨팅 및 네트워크를 많이 사용하는 애플리케이션에 가장 적합합니다.</li></ul> | 
| 가상 컴퓨터 이미지 | <p>운영 체제 요구 사항에 따라 다음 이미지 중 하나를 선택합니다.</p><ul><li>[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |

**소형 GPU(컴퓨팅)** 크기의 랩을 만들 때 [GPU 드라이버 설치](./how-to-setup-lab-gpu.md#ensure-that-the-appropriate-gpu-drivers-are-installed) 옵션이 있습니다.  이 옵션은 GPU로 고성능 컴퓨팅을 사용하는 데 필요한 최신 NVIDIA 드라이버와 CUDA(컴퓨팅 통합 디바이스 아키텍처) 도구 키트를 설치합니다.  자세한 내용은 [GPU 가상 머신을 사용하여 랩 설정](./how-to-setup-lab-gpu.md) 문서를 참조하세요.

### <a name="template-virtual-machine"></a>템플릿 가상 머신
랩을 만든 후에는 선택한 가상 머신 크기 및 이미지에 따라 템플릿 VM이 생성됩니다. 이 클래스의 학생들에게 제공하려는 모든 항목을 사용하여 템플릿 VM을 구성합니다. 자세한 내용은 [템플릿 가상 머신을 관리하는 방법](how-to-create-manage-template.md)을 참조하세요. 

기본적으로 Data Science VM 이미지는 이러한 종류의 클래스에 필요한 여러 데이터 과학 프레임워크 및 도구를 함께 제공합니다. 예를 들어 이미지에 다음과 같은 것들이 포함됩니다.

- [Jupyter Notebook](http://jupyter-notebook.readthedocs.io/): 데이터 과학자가 동일한 환경에서 원시 데이터를 가져오고, 계산을 실행하고, 결과를 볼 수 있는 웹 애플리케이션입니다. 템플릿 VM에서 로컬로 실행됩니다.  
- [Visual Studio Code](https://code.visualstudio.com/): Notebook을 작성하고 테스트할 때 풍부한 대화형 환경을 제공하는 IDE(통합 개발 환경)입니다. 자세한 내용은 [Visual Studio Code에서 Jupyter Notebook 작업](https://code.visualstudio.com/docs/python/jupyter-support)을 참조하세요.

**소형 GPU(컴퓨팅)** 크기를 사용하는 경우 Data Science 프레임워크 및 라이브러리가 GPU를 사용하여 올바르게 설정되었는지 확인하는 것이 좋습니다.  프레임워크 및 라이브러리를 올바르게 설정하려면 다른 버전의 NVIDIA 드라이버 및 CUDA 도구 키트를 설치해야 할 수도 있습니다.  예를 들어 TensorFlow에 대해 GPU가 구성되었는지 확인하려면 템플릿 VM에 연결하고 Jupyter Notebook에서 다음 Python-TensorFlow 코드를 실행하면 됩니다.

```python
import tensorflow as tf
from tensorflow.python.client import device_lib

print(device_lib.list_local_devices())
```

위 코드의 출력이 다음과 같은 경우 TensorFlow에 대해 GPU가 구성되지 않은 것입니다.

```python
[name: "/device:CPU:0"
device_type: "CPU"
memory_limit: 268435456
locality {
}
incarnation: 15833696144144374634
]
```
GPU를 올바르게 구성하려면 프레임워크 또는 라이브러리 설명서를 참조해야 합니다.  위의 예제를 계속 진행하면 TensorFlow에서 다음 지침을 제공합니다.
- [TensorFlow GPU 지원](https://www.tensorflow.org/install/gpu)

해당 지침에서는 필수 [NVIDIA 드라이버](https://www.nvidia.com/drivers) 및 [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit-archive) 버전을 다룹니다.  이 지침에는 [NVIDIA cudDNN(NVIDIA CUDA Deep Neural Network) 라이브러리](https://developer.nvidia.com/cudnn) 설치에 대한 내용도 포함되어 있습니다.

TensorFlow의 단계에 따라 GPU를 구성한 후 위의 코드를 다시 실행하면 다음과 유사한 출력이 표시됩니다.

```python
[name: "/device:CPU:0"
device_type: "CPU"
memory_limit: 268435456
locality {
}
incarnation: 15833696144144374634
, name: "/device:GPU:0"
device_type: "GPU"
memory_limit: 11154792128
locality {
  bus_id: 1
  links {
  }
}
incarnation: 2659412736190423786
physical_device_desc: "device: 0, name: NVIDIA Tesla K80, pci bus id: 0001:00:00.0, compute capability: 3.7"
]
```

### <a name="provide-notebooks-for-the-class"></a>클래스에서 사용할 Notebook 제공
그 다음 작업은 학생들이 사용할 Notebook을 제공하는 것입니다. 고유의 Notebook을 제공하려면 템플릿 VM에 로컬로 Notebook을 저장하면 됩니다. 

Azure Machine Learning의 샘플 Notebook을 사용하려면 [Jupyter Notebook을 사용하여 환경을 구성하는 방법](../machine-learning/how-to-configure-environment.md#jupyter)을 참조하세요. 

### <a name="optional-enable-graphical-desktop-for-linux"></a>선택 사항: Linux용 그래픽 데스크톱 사용 
**Data Science Virtual Machine – Ubuntu** 이미지는 이미 X2GO 서버를 통해 프로비저닝되었으며 클라이언트 연결을 수락할 준비가 완료되어 있습니다. 템플릿 VM을 설정할 때 추가 단계가 필요 없습니다. 

### <a name="publish-the-template-machine"></a>템플릿 머신 게시
템플릿을 게시하면 랩에 등록된 각 학생은 여러분이 설정한 모든 로컬 도구와 Notebook이 포함된 템플릿 VM의 복사본을 얻게 됩니다.

## <a name="how-students-connect-to-jupyter-notebooks"></a>학생이 Jupyter Notebook에 연결하는 방법
템플릿을 게시하면 각 학생은 Jupyter Notebook을 포함하여 여러분이 클래스를 위해 미리 구성해 둔 모든 것이 포함된 VM에 액세스할 수 있습니다. 다음 섹션에서는 학생들이 Jupyter Notebook에 연결하는 다양한 방법을 보여줍니다. 

### <a name="for-windows-vms"></a>Windows VM의 경우
학생에게 Windows VM을 제공한 경우 학생은 자신의 VM에 연결하고, 자신의 VM에서 로컬로 사용할 수 있는 Jupyter Notebook을 사용해야 합니다. 

학생은 RDP(원격 데스크톱 연결)를 사용하여 Windows VM에 연결할 수 있습니다. 자세한 단계는 [클래스룸 랩에 액세스하는 방법](how-to-use-classroom-lab.md)을 참조하세요. 

Mac 또는 Chromebook을 사용하는 학생은 다음 문서의 지침에 따라 Data Science Windows VM에 연결할 수 있습니다. 

- [Mac에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-mac-remote-desktop.md)
- [에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Linux VM의 경우
학생에게 Linux VM을 제공한 경우 학생이 VM에서 Jupyter Notebook에 연결하는 데 사용할 수 있는 여러 가지 옵션이 있습니다.

- VM에 연결한 후 Jupyter Notebook에 로컬로 액세스
    - VM에 SSH로 연결하여 터미널 세션 설정
     - VM에 X2Go로 연결하여 그래픽 세션 설정
- SSH 터널링을 사용하여 학생의 로컬 컴퓨터에서 VM의 Jupyter 서버에 직접 연결합니다. 

다음 섹션에서는 Jupyter Notebook에 연결하는 이러한 방법에 대해 자세히 설명합니다. 

#### <a name="ssh-to-virtual-machine"></a>가상 머신에 SSH로 연결
학생은 터미널 세션에서 SSH를 통해 Linux VM에 연결할 수 있습니다. 자세한 단계는 [클래스룸 랩에 액세스하는 방법](how-to-use-classroom-lab.md)을 참조하세요. Windows 클라이언트 머신을 사용하는 학생은 [PuTTY](https://www.putty.org/)를 다운로드하거나 명령 프롬프트에서 [Windows의 OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse)를 사용하여 SSH로 연결하도록 설정하여 SSH 클라이언트를 사용해야 합니다. 

1.  VM을 시작합니다.
2.  VM이 실행되면 **연결** 을 클릭합니다. 그러면 다음 샘플과 비슷하게 생긴 SSH 명령 문자열을 제공하는 대화 상자가 표시됩니다.
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  명령 프롬프트 또는 터미널로 이동하여 이 명령에 붙여넣은 다음, **Enter** 키를 누릅니다.
4.  암호를 입력하여 VM에 로그인합니다. 

학생이 VM에 연결되면 학생은 Jupyter Notebook에 로컬로 액세스하여 실행할 수 있습니다.

#### <a name="x2go-to-virtual-machine"></a>가상 머신에 X2Go로 연결
**Data Science Virtual Machine – Ubuntu** 이미지는 이미 X2GO 서버를 통해 프로비저닝되었으며 클라이언트 연결을 수락할 준비가 완료되어 있습니다. Linux 머신의 그래픽 데스크톱에 연결하려면 학생은 다음과 같은 일회성 단계를 수행하여 클라이언트 머신에 X2Go를 설정해야 합니다.

1.  사용하는 클라이언트 플랫폼에 맞는 [X2Go 클라이언트](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)를 다운로드하여 설치합니다.
2.  [Azure Lab Services 포털](https://labs.azure.com)에서 연결하려는 Linux VM이 시작되었는지 확인합니다.
3.  VM이 실행되면 **연결** 을 클릭합니다. 그러면 다음 샘플과 비슷하게 생긴 SSH 명령 문자열을 제공하는 대화 상자가 표시됩니다.

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. 이 정보를 얻었으면 X2Go 클라이언트 앱을 열고 새 세션을 만듭니다. 
5.  **세션 기본 설정** 창에서 다음 값을 입력합니다.
    - **세션 이름**: 원하는 대로 지정할 수 있지만, 랩 VM의 이름을 사용하는 것이 좋습니다.
     - **호스트**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **로그인**: student
     - **SSH 포트**: 12345
     - **세션 유형**: XFCE
6. **확인** 을 선택합니다. 

    > [!NOTE]
     > 새 X2Go 세션을 만들 때 RDP 포트가 **아닌** SSH 포트를 사용해야 합니다.

이제 다음 단계를 수행하여 VM에 연결합니다.    

1.  X2Go 클라이언트에서 연결하려는 VM을 두 번 클릭합니다. 

    ![X2Go 클라이언트](./media/class-type-jupyter-notebook/x2go-client.png)
2. 암호를 입력하여 VM에 연결합니다. (연결을 마치려면 방화벽을 우회할 수 있는 X2Go 권한을 부여해야 할 수도 있습니다.)
3.  이제 Ubuntu Data Science VM의 그래픽 인터페이스가 표시됩니다.

#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>VM의 Jupyter 서버에 SSH 터널링
일부 학생은 로컬 컴퓨터에서 VM 내의 Jupyter 서버에 직접 연결하려고 할 수 있습니다. SSH 프로토콜을 사용하면 서버의 특정 포트에서 실행되는 애플리케이션이 로컬 컴퓨터의 매핑 포트로 **터널링** 되도록 로컬 컴퓨터와 원격 서버(이 문서에서는 학생의 랩 VM) 간에 포트 전달이 가능합니다. 학생은 다음 단계에 따라 랩 VM의 Jupyter 서버에 SSH 터널링해야 합니다.

1.  [Azure Lab Services 포털](https://labs.azure.com)에서 연결하려는 Linux VM이 시작되었는지 확인합니다.
2.  VM이 실행되면 **연결** 을 클릭합니다. 그러면 다음 문자열과 비슷하게 생긴 SSH 명령 문자열을 제공하는 대화 상자가 표시됩니다.

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. 로컬 컴퓨터에서 터미널 또는 명령 프롬프트를 시작하고 SSH 연결 문자열을 복사합니다. 그런 다음, 명령 문자열에 `-L 8888:localhost:8888`을 추가합니다. 그러면 포트 간에 **터널** 이 생성됩니다. 마지막 문자열은 다음과 비슷합니다.

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. **Enter** 키를 눌러 명령을 실행합니다. 
5.  암호를 입력하라는 메시지가 표시되면 암호를 입력하여 랩 VM에 연결합니다. 
6.  VM에 연결되면 다음 명령을 사용하여 Jupyter 서버를 시작합니다. 

    ```bash
     jupyter notebook
     ```
7. 이 명령을 실행하면 터미널 또는 명령 프롬프트에 URL이 제공됩니다. URL은 다음과 비슷합니다.

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. 이 URL을 로컬 컴퓨터의 브라우저에 붙여넣어 Jupyter Notebook 연결하고 작업합니다. 

    > [!NOTE]
    > Visual Studio Code를 사용하면 강력한 [Jupyter Notebook 편집 환경](https://code.visualstudio.com/docs/python/jupyter-support)도 사용할 수 있습니다. [원격 Jupyter 서버에 연결](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server)하고 이전 단계와 동일한 URL을 사용하여 브라우저 대신 VS Code에서 연결하는 방법에 대한 지침을 따르면 됩니다. 


## <a name="cost-estimate"></a>예상 비용
이 수업의 가능한 예상 비용을 살펴보겠습니다. 25명의 학생이 있는 수업을 예로 들겠습니다. 예약된 수업 시간은 20시간입니다. 예약된 수업 시간 외에도 숙제 또는 과제에 사용하도록 각 학생에게 10시간이 할당됩니다. 앞에서 선택한 VM 크기는 랩 단위 139개를 제공하는 소형 GPU(컴퓨팅)였습니다. 소형(랩 단위 20개) 또는 중간 크기(랩 단위 42개)를 사용하려면 아래 수식에서 랩 단위 부분을 원하는 숫자로 바꾸면 됩니다.  

이 클래스의 예상 비용은 학생 25명 *(예약된 시간 20시간 + 할당량 시간 10시간) * 랩 단위 139 * 0.01USD/시간 = 1042.5USD입니다.

가격 책정에 대한 자세한 내용은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론
이 문서에서는 Jupyter Notebook 클래스의 랩을 만드는 단계를 살펴보았습니다. 비슷한 설정을 다른 기계 학습 클래스에도 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)