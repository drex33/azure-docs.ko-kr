---
title: Data Science Virtual Machine의 새로운 기능
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine의 릴리스 정보
author: michalmar
ms.service: data-science-vm
ms.author: mimarusa
ms.date: 07/16/2021
ms.topic: reference
ms.openlocfilehash: e256382b4d1e397be6d11a3660a4c4fe73eafb23
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778326"
---
# <a name="azure-data-science-virtual-machine-release-notes"></a>Azure Data Science Virtual Machine 릴리스 정보

본 문서에서는 Azure Data Science Virtual Machine 릴리스에 대해 알아봅니다. 버전 번호와 함께 포함된 도구 전체 목록은 [관련 페이지](./tools-included.md)를 확인하세요.

알려진 버그 및 해결 방법에 대해 알아보려면 [알려진 문제 목록](reference-known-issues.md)을 참조하세요.


## <a name="2021-08-11"></a>2021-08-11

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)용 새로운 이미지입니다.

버전: 21.08.11

주요 변경 사항은 다음과 같습니다.

- Windows 보안 이벤트
- NVIDIA CuDNN를 8.1.0로 업데이트
- Jupyter Lab을 3.0.16으로 업데이트
- 실험 추적을 위해 MLFLow 추가
- 안정성 개선 및 사소한 버그 수정 



## <a name="2021-07-12"></a>2021-07-12

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)용 새로운 이미지입니다.

주요 변경 사항은 다음과 같습니다.

- PyTorch 1.9.0으로 업데이트
- Azure CLI를 2.26.1로 업데이트
- Azure CLI Azure Machine Learning 확장을 1.29.0으로 업데이트
- VS 코드 버전 1.58.1 업데이트
- 안정성 개선 및 사소한 버그 수정 


## <a name="2021-06-22"></a>2021-06-22

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)용 새로운 이미지입니다.

버전: 21.06.22

주요 변경 사항은 다음과 같습니다.

- PyTorch 1.9.0으로 업데이트
- git을 사용할 수 없는 버그 수정


## <a name="2021-06-01"></a>2021-06-01

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

## <a name="2021-05-22"></a>2021-05-22

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

## <a name="2021-05-12"></a>2021-05-12

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


## <a name="2020-02-24"></a>2020-02-24

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)의 Data Science Virtual Machine 이미지 및 [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) 이미지를 이제 사용할 수 있습니다.

## <a name="2019-10-08"></a>2019-10-08

### <a name="updates-to-software-on-the-windows-dsvm"></a>Windows DSVM의 소프트웨어 업데이트

- Azure Storage Explorer 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### <a name="default-browser-for-windows-updated"></a>Windows용 기본 브라우저 업데이트

이전에는 Internet Explorer를 기본 브라우저로 설정했습니다. 이제 사용자가 처음 로그인할 때 기본 브라우저를 선택하라는 프롬프트가 표시됩니다.
