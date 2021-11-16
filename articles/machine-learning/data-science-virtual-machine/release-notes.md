---
title: Data Science Virtual Machine의 새로운 기능
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine의 릴리스 정보
author: michalmar
ms.service: data-science-vm
ms.author: mimarusa
ms.date: 10/11/2021
ms.topic: reference
ms.openlocfilehash: 97b95b37367d1cede3326307602a19ffdc5df8d5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517143"
---
# <a name="azure-data-science-virtual-machine-release-notes"></a>Azure Data Science Virtual Machine 릴리스 정보

본 문서에서는 Azure Data Science Virtual Machine 릴리스에 대해 알아봅니다. 버전 번호와 함께 포함된 도구 전체 목록은 [관련 페이지](./tools-included.md)를 확인하세요.

알려진 버그 및 해결 방법에 대해 알아보려면 [알려진 문제 목록](reference-known-issues.md)을 참조하세요.

## <a name="november-4-2021"></a>2021년 11월 4일

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)용 새로운 이미지입니다.

버전: 21.11.04

주요 변경 사항은 다음과 같습니다.
* .NET Framework 버전 3.1.414로 변경
* Azcopy를 버전 10.13.0으로 변경
* Azure CLI 버전 2.30.0으로 변경
* CUDA를 버전 11.5로 변경
* Docker를 버전 20.10.10으로 변경
* Intellijidea를 버전 2021.2.3으로 변경
* NVIDIA 드라이버를 버전 495.29.05로 변경
* NVIDIA SMI가 버전 495.29.05로 변경되었습니다.
* Nodejs를 v16.13.0 버전으로 변경
* Pycharm을 버전 2021.2.3으로 변경
* VS Code 버전 1.61.2로 변경
* Conda
  * *azureml_py36_automl*
    * azureml-core를 버전 1.35.0으로 변경
  * *py38_default*
    * Jupyter Lab/jupyterlab이 버전 3.2.1로 변경되었습니다.
    * Jupyter Notebook/Notebook을 버전 6.4.5로 변경
    * Jupyter 서버/jupyter_server 버전 1.11.2로 변경
    * PyTorch Profiler TensorBoard 플러그 인/torch-tb-profiler를 버전 0.3.1로 변경
    * azure-core를 버전 1.19.1로 변경
    * matplotlib를 버전 3.4.3으로 변경
    * mkl을 버전 2021.4.0으로 변경
    * onnx가 버전 1.10.2로 변경되었습니다.
    * opencv-python을 버전 4.5.4.58로 변경
    * pandas를 버전 1.3.4로 변경
    * pytorch를 버전 1.10.0으로 변경
    * scikit-learn을 버전 1.0.1로 변경
    * tensorflow-gpu를 버전 2.6.2로 변경


## <a name="october-7-2021"></a>2021년 10월 7일

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)용 새로운 이미지입니다.

버전: 21.10.07

주요 변경 사항은 다음과 같습니다.
 - pytorch를 버전 1.9.1로 변경
 - Docker를 버전 20.10.9로 변경
 - Intellijidea를 버전 2021.2.2로 변경
 - Nodejs를 v14.18.0 버전으로 변경
 - Pycharm을 버전 2021.2.2로 변경
 - VS Code 버전 1.60.2로 변경
 - AutoML 환경(azureml_py36_automl) 수정
 - Azure Storage Explorer 안정성 수정
 - 안정성 개선 및 사소한 버그 수정 


## <a name="august-11-2021"></a>2021년 8월 11일

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)용 새로운 이미지입니다.

버전: 21.08.11

주요 변경 사항은 다음과 같습니다.

- Windows 보안 이벤트
- NVIDIA CuDNN를 8.1.0로 업데이트
- Jupyter Lab을 3.0.16으로 업데이트
- 실험 추적을 위해 MLFLow 추가
- 안정성 개선 및 사소한 버그 수정 



## <a name="july-12-2021"></a>2021년 7월 12일

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)용 새로운 이미지입니다.

주요 변경 사항은 다음과 같습니다.

- PyTorch 1.9.0으로 업데이트
- Azure CLI를 2.26.1로 업데이트
- Azure CLI Azure Machine Learning 확장을 1.29.0으로 업데이트
- VS 코드 버전 1.58.1 업데이트
- 안정성 개선 및 사소한 버그 수정 


## <a name="june-22-2021"></a>2021년 6월 22일

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)용 새로운 이미지입니다.

버전: 21.06.22

주요 변경 사항은 다음과 같습니다.

- PyTorch 1.9.0으로 업데이트
- git을 사용할 수 없는 버그 수정


## <a name="june-1-2021"></a>2021년 6월 1일

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)용 새로운 이미지입니다.

버전: 21.06.01

주요 변경 사항은 다음과 같습니다.

- Docker는 기본적으로 사용됩니다.
- JupyterHub는 기본적으로 JupyterLab을 사용합니다.
- [CVE-2020-15523](https://nvd.nist.gov/vuln/detail/CVE-2020-15523)을 수정하기 위해 Python 버전이 업데이트되었습니다.
- [CVE-2021-25758](https://nvd.nist.gov/vuln/detail/CVE-2021-25758)을 수정하기 위해 IntelliJ IDEA가 버전 2021.1로 업데이트되었습니다.
- PyCharm Community가 2021.1로 업데이트되었습니다.
- TensorFlow가 버전 2.5.0으로 업데이트되었습니다.

<br/>
바탕 화면에서 여러 아이콘이 제거되었습니다.

## <a name="may-22-2021"></a>2021년 5월 22일

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)용 새로운 이미지입니다.

버전: 21.05.22

선택한 버전 업데이트는 다음과 같습니다.
- CUDA 11.1
- Python 3.8
- PyTorch 1.8.1
- TensorFlow 2.5.0
- Spark 3.1.1
- Java 11
- R 4.1.0
- Julia 1.0.5
- NodeJS 16.2.0
- Visual Studio Code 1.56.2(Azure ML 확장 포함)
- PyCharm Community Edition 2021.1.1
- Jupyter Lab 2.2.6
- RStudio 1.4.1106
- Visual Studio Community Edition 2019(버전 16.9.6)
- Azure CLI 2.23.0
- Storage Explorer 1.19.1
- AzCopy 10.10.0
- Power BI Desktop 2.93.641.0 64비트(2021년 5월)
- Azure Data Studio 1.28.0
- Microsoft Edge 브라우저

<br/>
Firefox, Apache Drill, Microsoft Integration Runtime이 제거되었습니다.

<br/>
다크 모드, 바탕 화면의 아이콘, 배경 화면이 변경되었습니다.

## <a name="may-12-2021"></a>2021년 5월 12일

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)용 새로운 이미지입니다.

선택한 버전 업데이트는 다음과 같습니다.
- CUDA 11.3, cuDNN 8, NCCL2
- Python 3.8
- R 4.0.5
- Spark 3.1(mmlspark, Blob Storage 커넥터, Data Lake, CosmosDB 포함)
- Java 11(OpenJDK)
- Jupyter Lab 3.0.14
- PyTorch 1.8.1(torchaudio torchtext torchvision, torch-tb-profiler 포함)
- TensorFlow 2.4.1(TensorBoard 포함)
- dask 2021.01.0
- VS.Code 1.56
- Azure Data Studio 1.22.1
- Azure CLI 2.23.0
- Azure Storage Explorer 1.19.1
- azcopy 10.10
- Microsoft Edge 브라우저(베타)

<br/>
Docker가 추가되었습니다. 리소스를 저장하기 위해 Docker 서비스는 기본적으로 시작되지 않습니다. Docker 서비스를 시작하려면 다음 명령줄 명령을 실행합니다.

```
sudo systemctl start docker
```

> [!NOTE]
> 머신에 GPU가 있는 경우 Docker 명령에 `--gpus` 매개 변수를 추가하여 컨테이너 내에서 GPU를 사용할 수 있습니다.
>
> 예를 들어 를 실행하면
>
> `sudo docker run --gpus all -it --rm -v local_dir:container_dir nvcr.io/nvidia/pytorch:18.04-py3`
>
> PyTorch가 미리 설치되고 모든 GPU가 사용하도록 설정된 Ubuntu 18.04 컨테이너가 실행됩니다. 또한 *container_dir* 의 컨테이너에서 *local_dir* 로컬 폴더를 사용할 수 있습니다.
>


## <a name="february-24-2020"></a>2020년 2월 24일

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)의 Data Science Virtual Machine 이미지 및 [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) 이미지를 이제 사용할 수 있습니다.

## <a name="october-8-2019"></a>2019년 10월 8일

### <a name="updates-to-software-on-the-windows-dsvm"></a>Windows DSVM의 소프트웨어 업데이트

- Azure Storage Explorer 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### <a name="default-browser-for-windows-updated"></a>Windows용 기본 브라우저 업데이트

이전에는 Internet Explorer를 기본 브라우저로 설정했습니다. 이제 사용자가 처음 로그인할 때 기본 브라우저를 선택하라는 메시지가 표시됩니다.
