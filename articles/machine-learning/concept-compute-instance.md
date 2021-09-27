---
title: Azure Machine Learning 컴퓨팅 인스턴스란?
titleSuffix: Azure Machine Learning
description: 완전 관리형 클라우드 기반 워크스테이션인 Azure Machine Learning 컴퓨팅 인스턴스에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2021
ms.openlocfilehash: 96147d75a5bc12ebbaae9e9f9bd685c6f193fa0b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649308"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning 컴퓨팅 인스턴스란?

Azure Machine Learning 컴퓨팅 인스턴스는 데이터 과학자를 위한 관리형 클라우드 기반 워크스테이션입니다.

컴퓨팅 인스턴스를 사용하면 Azure Machine Learning 개발을 손쉽게 시작할 수 있을 뿐만 아니라 IT 관리자에게 관리 및 엔터프라이즈 준비 기능을 제공할 수 있습니다.

기계 학습을 위해 클라우드에서 완전히 구성되고 관리된 개발 환경으로 컴퓨팅 인스턴스를 사용합니다. 또한 개발 및 테스트 목적으로 학습 및 추론을 위한 컴퓨팅 대상으로도 사용할 수 있습니다.

프로덕션 등급 모델 학습의 경우 다중 노드 크기 조정 기능이 포함된 [Azure Machine Learning 컴퓨팅 클러스터](how-to-create-attach-compute-cluster.md)를 사용합니다. 프로덕션 등급 모델 배포의 경우 [Azure Kubernetes Service 클러스터](how-to-deploy-azure-kubernetes-service.md)를 사용합니다.

컴퓨팅 인스턴스 Jupyter 기능이 작동하려면 웹 소켓 통신이 비활성화되어 있지 않아야 합니다. 네트워크에서 *.instances.azureml.net 및 *.instances.azureml.ms에 대한 WebSocket 연결이 허용되는지 확인합니다.

> [!IMPORTANT]
> 이 문서에 표시된 항목(미리 보기)은 현재 퍼블릭 미리 보기에서 확인할 수 있습니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="why-use-a-compute-instance"></a>컴퓨팅 인스턴스를 사용하는 이유

컴퓨팅 인스턴스는 기계 학습 개발 환경에 최적화된 완전 관리형 클라우드 기반 워크스테이션입니다. 다음과 같은 이점을 제공합니다.

|주요 이점|설명|
|----|----|
|생산성|Azure Machine Learning 스튜디오에서 통합 Notebook 및 다음 도구를 사용하여 모델을 빌드 및 배포할 수 있습니다.<br/>-  Jupyter<br/>-  JupyterLab<br/>- VS Code(미리 보기)<br/>-  RStudio(미리 보기)<br/>컴퓨팅 인스턴스는 Azure Machine Learning 작업 영역 및 스튜디오와 완전히 통합됩니다. 작업 영역의 다른 데이터 과학자와 Notebook 및 데이터를 공유할 수 있습니다.<br/> 또한 컴퓨팅 인스턴스와 [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630)를 사용할 수도 있습니다.
|관리 및 보안|보안 공간을 줄이고 엔터프라이즈 보안 요구 사항에 따라 규정 준수를 추가합니다. 컴퓨팅 인스턴스는 다음과 같은 강력한 관리 정책과 보안 네트워킹 구성을 제공합니다.<br/><br/>- Resource Manager 템플릿 또는 Azure Machine Learning SDK에서 자동 프로비저닝<br/>- [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)<br/>- [가상 네트워크 지원](./how-to-secure-training-vnet.md#compute-cluster)<br/> - SSH 액세스를 사용하거나 사용하지 않도록 설정하는 SSH 정책<br/>- [공용 IP는 공용 IP](./how-to-secure-training-vnet.md#no-public-ip) 주소에 종속되지 않고 보안 연결 솔루션을 사용하도록 설정하는 기능을 제공하지 않습니다.</br>- TLS 1.2 사용 |
|ML&nbsp;용&nbsp;으로 미리 구성|사전 구성된 최신 ML 패키지, 딥 러닝 프레임워크 및 GPU 드라이버를 통해 설치 작업에 드는 시간을 절약할 수 있습니다.|
|완전한 사용자 지정 기능|GPU를 비롯한 Azure VM 형식에 대한 광범위한 지원과 패키지 및 드라이버 설치와 같은 지속형의 하위 수준 사용자 지정을 통해 고급 시나리오를 간편하게 만들 수 있습니다. |

* 컴퓨팅 인스턴스는 컴퓨팅 클러스터와 유사한 보안 학습 컴퓨팅 대상이기도 하지만 단일 노드입니다.
* 사용자가 직접 [컴퓨팅 인스턴스 만들거나](how-to-create-manage-compute-instance.md?tabs=python#create) 관리자가 **[사용자를 대신하여 컴퓨팅 인스턴스 만들 수 있습니다](how-to-create-manage-compute-instance.md?tabs=python#on-behalf)** .
* 또한 **[설정 스크립트(미리 보기)를 사용](how-to-create-manage-compute-instance.md#setup-script)** 하여 필요에 따라 컴퓨팅 인스턴스를 사용자 지정하고 구성할 수 있습니다.
* 비용을 절감하려면 컴퓨팅 인스턴스를 자동으로 시작하고 중지하는 **[일정을 만듭니다(미리 보기)](how-to-create-manage-compute-instance.md#schedule)** .


## <a name="tools-and-environments"></a><a name="contents"></a>도구 및 환경

Azure Machine Learning 컴퓨팅 인스턴스를 사용하면 작업 영역의 완전 통합형 Notebook 환경에서 모델을 작성, 학습 및 배포할 수 있습니다.

SSH가 필요 없는 컴퓨팅 인스턴스를 원격 서버로 사용하여 [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630)에서 Jupyter Notebook을 실행할 수 있습니다. [원격 SSH 확장](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/)을 통해 VS Code 통합을 사용하도록 설정할 수도 있습니다.

컴퓨팅 인스턴스에 [패키지 설치](how-to-access-terminal.md#install-packages) 및 [커널 추가](how-to-access-terminal.md#add-new-kernels)를 수행할 수 있습니다.

다음과 같은 도구 및 환경이 컴퓨팅 인스턴스에 이미 설치되어 있습니다.

|일반적인 도구 및 환경|세부 정보|
|----|:----:|
|드라이버|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI 라이브러리||
|Azure CLI ||
|Azure Machine Learning 샘플 ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf||

|**R** 도구 및 환경|세부 정보|
|----|:----:|
|RStudio Server 오픈 소스 버전(미리 보기)||
|R 커널||

|**PYTHON** 도구 및 환경|세부 정보|
|----|----|
|Anaconda Python||
|Jupyter 및 확장||
|Jupyterlab 및 확장||
[Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)</br>PyPI에서|대부분의 azureml 추가 패키지가 포함됩니다.  전체 목록을 보려면 [컴퓨팅 인스턴스에서 터미널 창을 열고](how-to-access-terminal.md) 다음을 실행합니다. <br/> `conda list -n azureml_py36 azureml*` |
|기타 PyPI 패키지|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 패키지|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|딥 러닝 패키지|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 패키지|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python 샘플||

Python 패키지는 **Python 3.8 - AzureML** 환경에 모두 설치됩니다. 컴퓨팅 인스턴스는 Ubuntu 18.04를 기본 OS로 포함합니다.

## <a name="accessing-files"></a>파일에 액세스

Notebook 및 R 스크립트는 Azure 파일 공유에서 작업 영역의 기본 스토리지 계정에 저장됩니다.  이러한 파일은 “사용자 파일” 디렉터리 아래에 있습니다. 이 스토리지를 사용하면 컴퓨팅 인스턴스 간에 Notebook을 쉽게 공유할 수 있습니다. 스토리지 계정은 컴퓨팅 인스턴스를 중지하거나 삭제하는 경우에도 Notebook을 안전하게 유지합니다.

작업 영역의 Azure 파일 공유 계정은 컴퓨팅 인스턴스에 드라이브로 탑재됩니다. 이 드라이브는 Jupyter, Jupyter Labs 및 RStudio의 기본 작업 디렉터리입니다. 따라서 Jupyter, JupyterLab 또는 RStudio에서 만든 Notebook 및 기타 파일은 파일 공유에 자동으로 저장되고 다른 컴퓨팅 인스턴스에서도 사용할 수 있습니다.

파일 공유의 파일은 동일한 작업 영역의 모든 컴퓨팅 인스턴스에서 액세스할 수 있습니다. 컴퓨팅 인스턴스에서 이러한 파일에 대한 모든 변경 내용은 안정적으로 파일 공유에 다시 저장됩니다.

또한 작업 영역 파일 공유의 사용자 파일 디렉터리 아래에 있는 폴더에 최신 Azure Machine Learning 샘플을 복제할 수 있습니다.

네트워크 드라이브에서 작은 파일을 작성하는 것이 컴퓨팅 인스턴스 로컬 디스크 자체에 작성하는 것에 비해 느릴 수 있습니다.  많은 작은 파일을 작성하는 경우 `/tmp` 디렉터리와 같이 컴퓨팅 인스턴스에서 직접 디렉터리를 사용해보세요. 이러한 파일은 다른 컴퓨팅 인스턴스에서 액세스할 수 없습니다.

Notebooks 파일 공유에 학습 데이터를 저장하지 마세요. 임시 데이터에 대해 컴퓨팅 인스턴스의 `/tmp` 디렉터리를 사용할 수 있습니다.  그러나 컴퓨팅 인스턴스의 OS 디스크에 매우 큰 데이터 파일은 작성하지 마세요. 컴퓨팅 인스턴스의 OS 디스크 용량은 128GB입니다. /mnt에 탑재된 임시 디스크에 임시 학습 데이터를 저장할 수도 있습니다. 임시 디스크 크기는 선택한 VM 크기에 따라 구성할 수 있으며 더 큰 크기의 VM을 선택한 경우 더 많은 양의 데이터를 저장할 수 있습니다. [데이터 저장소 및 데이터 세트](concept-azure-machine-learning-architecture.md#datasets-and-datastores)를 탑재할 수도 있습니다. 설치하는 모든 소프트웨어 패키지는 컴퓨팅 인스턴스의 OS 디스크에 저장됩니다. 고객 관리 키 암호화는 현재 OS 디스크에 대해 지원되지 않습니다. 컴퓨팅 인스턴스용 OS 디스크는 Microsoft 관리형 키로 암호화됩니다. 

### <a name="create-a-compute-instance"></a><a name="create"></a>컴퓨팅 인스턴스 생성

관리자는 **[작업 영역의 다른 사용자를 위한 컴퓨팅 인스턴스를 만들 수 있습니다(미리 보기)](how-to-create-manage-compute-instance.md#on-behalf)** .

**[설정 스크립트(미리 보기)를 사용](how-to-create-manage-compute-instance.md#setup-script)** 하여 컴퓨팅 인스턴스를 자동으로 사용자 지정하고 구성할 수도 있습니다.

스스로 계산 인스턴스를 만들려면 Azure Machine Learning studio에서 작업 영역을 사용 하 고, 노트북 중 하나를 실행할 준비가 되 면 **계산** 섹션 또는 **노트북** 섹션에서 [새 계산 인스턴스를 만듭니다](how-to-create-manage-compute-instance.md?tabs=azure-studio#create) .

인스턴스를 만들 수도 있습니다.
* [통합 Notebook 환경](tutorial-train-models-with-aml.md#azure)에서 직접 만들기
* Azure Portal에서
* Azure Resource Manager 템플릿에서 만들기. 예제 템플릿의 경우 [Azure Machine Learning 컴퓨팅 인스턴스 만들기 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance)을 참조하세요.
* [Azure Machine Learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md) 사용하여 만들기
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md#computeinstance)에서 만들기

컴퓨팅 인스턴스 만들기에 적용되는 VM 제품군 할당량 및 총 지역 할당량당 지역별 전용 코어는 Azure Machine Learning 학습 컴퓨팅 클러스터 할당량과 통합 및 공유됩니다. 컴퓨팅 인스턴스를 중지해도 컴퓨팅 인스턴스를 다시 시작할 수 있도록 할당량이 해제되지 않습니다. OS 터미널을 통해 sudo 종료를 수행하여 컴퓨팅 인스턴스를 중지하지 마세요.

컴퓨팅 인스턴스는 P10 OS 디스크와 함께 제공됩니다. 임시 디스크 유형은 선택한 VM 크기에 따라 달라 집니다. 현재 OS 디스크 유형은 변경할 수 없습니다.


## <a name="compute-target"></a>컴퓨팅 대상

컴퓨팅 인스턴스는 Azure Machine Learning 컴퓨팅 학습 클러스터와 유사하게 [학습 컴퓨팅 대상](concept-compute-target.md#train)으로 사용할 수 있습니다.

컴퓨팅 인스턴스:
* 작업 큐가 있습니다.
* 기업에서 SSH 포트를 열지 않고도 가상 네트워크 환경에서 안전하게 작업을 실행합니다. 작업은 컨테이너화된 환경에서 실행되며 모델 종속성을 Docker 컨테이너로 패키지합니다.
* 여러 작은 작업을 병렬로 실행할 수 있습니다(미리 보기).  나머지 작업이 큐에 있는 동안 코어당 두 개의 작업을 병렬로 실행할 수 있습니다.
* 단일 노드 다중 GPU [분산 학습](how-to-train-distributed-gpu.md) 작업 지원

컴퓨팅 인스턴스는 테스트/디버그 시나리오에 대한 로컬 추론 배포 대상으로 사용할 수 있습니다.

> [!TIP]
> 컴퓨팅 인스턴스에 120GB OS 디스크가 있습니다. 디스크 공간이 부족하여 사용할 수 없는 상태가 되면 파일/폴더를 제거하여 컴퓨팅 인스턴스 터미널을 통해 OS 디스크(/에 탑재됨)에서 최소 5GB의 디스크 공간을 지운 다음 `sudo reboot`를 수행합니다. 터미널에 액세스하려면 컴퓨팅 목록 페이지 또는 컴퓨팅 인스턴스 세부 정보 페이지로 이동하여 **터미널** 링크를 클릭합니다. 터미널에서 `df -h`를 실행하여 사용 가능한 디스크 공간을 확인할 수 있습니다. `sudo reboot`을 수행하기 전에 5GB 이상의 공간을 지우세요. 5GB의 디스크 공간을 지울 때까지 스튜디오를 통해 컴퓨팅 인스턴스를 중지하거나 다시 시작하지 마세요.

## <a name="next-steps"></a>다음 단계

* [컴퓨팅 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md)
* [자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md)에서는 통합 Notebook으로 컴퓨팅 인스턴스를 사용하는 방법을 보여 줍니다.
