---
title: 학습 실행 구성
titleSuffix: Azure Machine Learning
description: 다양한 학습 환경(컴퓨팅 대상)에서 기계 학습 모델을 학습시킵니다. 학습 환경을 쉽게 전환할 수 있습니다.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 06/18/2021
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1
ms.openlocfilehash: 052e82f0bb1aa7c5b0b3dad7808bd46839fb95ad
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129387794"
---
# <a name="configure-and-submit-training-runs"></a>학습 실행 구성 및 제출

이 문서에서는 Azure Machine Learning 실행을 구성하고 제출하여 모델을 학습시키는 방법에 대해 알아봅니다. 학습 스크립트의 구성 및 제출에 대한 주요 부분은 코드 조각에서 설명합니다.  그런 다음, [Notebook 예제](#notebooks) 중 하나를 사용하여 전체 엔드투엔드 작업 예제를 찾습니다.

학습하는 경우 일반적으로 로컬 컴퓨터에서 시작한 다음, 나중에 클라우드 기반 클러스터로 스케일 아웃합니다. Azure Machine Learning을 사용하면 스크립트를 변경할 필요 없이 다양한 컴퓨팅 대상에서 학습 스크립트를 실행할 수 있습니다.

**스크립트 실행 구성** 내에서 각 컴퓨팅 대상에 대한 환경을 정의하기만 하면 됩니다.  그런 다음 다른 컴퓨팅 대상에서 학습 실험을 실행하려는 경우에 해당 컴퓨팅에 대한 실행 구성을 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/install)(1.13.0 이상)
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)(`ws`)
* 컴퓨팅 대상(`my_compute_target`).  [컴퓨팅 대상 만들기](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>스크립트 실행 구성이란?
[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)는 실험의 일환으로 학습 실행을 제출하는 데 필요한 정보를 구성하는 데 사용됩니다.

ScriptRunConfig 개체를 사용하여 학습 실험을 제출합니다.  이 개체는 다음을 포함합니다.

* **source_directory**: 학습 스크립트를 포함하는 원본 디렉터리
* **script**: 실행할 학습 스크립트
* **compute_target**: 실행할 컴퓨팅 대상
* **environment**: 스크립트를 실행할 때 사용할 환경
* 몇 가지 구성 가능한 추가 옵션(자세한 내용은 [참조 설명서](/python/api/azureml-core/azureml.core.scriptrunconfig) 참조)

## <a name="train-your-model"></a><a id="submit"></a>모델 학습

학습 실행을 제출하는 코드 패턴은 모든 유형의 컴퓨팅 대상에서 동일합니다.

1. 실행할 실험 만들기
1. 스크립트가 실행되는 환경 만들기
1. 컴퓨팅 대상 및 환경을 지정하는 ScriptRunConfig 만들기
1. 실행 제출
1. 실행이 완료될 때까지 대기

또는

* [하이퍼 매개 변수 튜닝](how-to-tune-hyperparameters.md)에 대한 HyperDrive 실행을 제출합니다.
* [VS Code 확장](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)을 통해 실험을 제출합니다.

## <a name="create-an-experiment"></a>실험 만들기

작업 영역에서 [실험](concept-azure-machine-learning-architecture.md#experiments) 을 만듭니다. Experiemnt는 실행 제출을 구성 하 고 코드를 추적 하는 데 도움이 되는 경량 컨테이너입니다.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>컴퓨팅 대상 선택

학습 스크립트가 실행되는 컴퓨팅 대상을 선택합니다. 컴퓨팅 대상이 ScriptRunConfig에 지정되지 않았거나 `compute_target='local'`인 경우 Azure ML에서 스크립트를 로컬로 실행합니다. 

이 문서의 예제 코드에서는 "사전 요구 사항" 섹션에서 `my_compute_target`이라는 컴퓨팅 대상을 이미 만들었다고 가정합니다.

>[!Note]
>Azure Databricks는 모델 학습을 위한 컴퓨팅 대상으로 지원되지 않습니다. Azure Databricks는 데이터 준비 및 배포 작업에 사용할 수 있습니다.

[!INCLUDE [arc-enabled-kubernetes](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

## <a name="create-an-environment"></a>환경 만들기
Azure Machine Learning [환경](concept-environments.md)은 기계 학습이 수행되는 환경을 캡슐화한 것입니다. 학습 및 채점 스크립트와 관련된 Python 패키지, Docker 이미지, 환경 변수 및 소프트웨어 설정을 지정합니다. 또한 런타임(Python, Spark 또는 Docker)을 지정합니다.

사용자 고유의 환경을 정의하거나 Azure ML 큐레이팅된 환경을 사용할 수 있습니다. [큐레이팅된 환경](./how-to-use-environments.md#use-a-curated-environment)은 기본적으로 작업 영역에서 사용할 수 있는 미리 정의된 환경입니다. 이러한 환경은 실행 준비 비용을 줄일 수 있는 캐시된 Docker 이미지에서 지원됩니다. 사용 가능한 큐레이팅된 환경의 전체 목록은 [Azure Machine Learning 큐레이팅된 환경](./resource-curated-environments.md)을 참조하세요.

원격 컴퓨팅 대상의 경우 이러한 인기 있는 큐레이팅된 환경 중 하나를 사용하여 시작할 수 있습니다.

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

환경에 대한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용](how-to-use-environments.md)을 참조하세요.
  
### <a name="local-compute-target"></a><a name="local"></a>로컬 컴퓨팅 대상

컴퓨팅 대상이 **로컬 컴퓨터** 인 경우 스크립트가 실행되는 Python 환경에서 필요한 모든 패키지를 사용할 수 있는지 확인해야 합니다.  `python.user_managed_dependencies`를 사용하여 현재 Python 환경(또는 지정한 경로의 Python)을 사용합니다.

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>스크립트 실행 구성 만들기

이제 컴퓨팅 대상(`my_compute_target`)과 환경(`myenv`)이 있으므로 `project_folder` 디렉터리에 있는 학습 스크립트(`train.py`)를 실행하는 스크립트 실행 구성을 만듭니다.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

환경을 지정하지 않으면 기본 환경이 만들어집니다.

학습 스크립트에 전달하려는 명령줄 인수가 있는 경우 ScriptRunConfig 생성자의 **`arguments`** 매개 변수를 통해 인수를 지정할 수 있습니다(예: `arguments=['--arg1', arg1_val, '--arg2', arg2_val]`).

실행에 허용되는 기본 최대 시간을 재정의하려면 **`max_run_duration_seconds`** 매개 변수를 통해 이 작업을 수행할 수 있습니다. 이 값보다 오래 걸리면 시스템에서 자동으로 실행을 취소하려고 시도합니다.

### <a name="specify-a-distributed-job-configuration"></a>분산 작업 구성 지정
[분산 학습](how-to-train-distributed-gpu.md) 작업을 실행하려면 분산 작업별 구성을 **`distributed_job_config`** 매개 변수에 제공합니다. 지원되는 구성 유형으로 [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) 및 [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)이 있습니다. 

분산 Horovod, TensorFlow 및 PyTorch 작업 실행에 대한 자세한 내용과 예제는 다음을 참조하세요.

* [TensorFlow 모델 학습](./how-to-train-tensorflow.md#distributed-training)
* [PyTorch 모델 학습](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>실험 제출

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> 학습 실행을 제출하면 학습 스크립트가 포함된 디렉터리의 스냅샷이 생성되어 컴퓨팅 대상으로 전송됩니다. 또한 작업 영역에 실험의 일부로 저장됩니다. 파일을 변경하고 실행을 다시 제출하면 변경된 파일만 업로드됩니다.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 스냅샷에 대한 자세한 내용은 [스냅샷](concept-azure-machine-learning-architecture.md#snapshots)을 참조하세요.

> [!IMPORTANT]
> 두 개의 **특수 폴더** 인 *outputs* 및 *logs* 는 Azure Machine Learning에서 별도로 처리합니다. 학습하는 동안 루트 디렉터리(각각 `./outputs` 및 `./logs`) 기준의 *outputs* 및 *logs* 라는 폴더에 파일을 쓰면, 이러한 파일이 실행 기록에 자동으로 업로드되므로 실행이 완료되면 해당 파일에 액세스할 수 있습니다.
>
> 모델 파일, 검사점, 데이터 파일 또는 그려진 이미지와 같이 학습 중에 아티팩트를 만들려면 이러한 파일을 `./outputs` 폴더에 씁니다.
>
> 마찬가지로, 학습 실행의 로그는 `./logs` 폴더에 쓸 수 있습니다. Azure Machine Learning의 [TensorBoard 통합](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb)을 활용하려면 TensorBoard 로그를 이 폴더에 기록해야 합니다. 실행이 진행되는 동안 TensorBoard를 시작하고 이러한 로그를 스트림할 수 있습니다.  나중에 이전 실행의 로그를 복원할 수도 있습니다.
>
> 예를 들어 원격 학습을 실행한 후 *outputs* 폴더에 기록된 파일을 로컬 머신으로 다운로드하는 코드는 `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`입니다.

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리인 학습 실행을 시작하면 리포지토리에 대한 정보가 실행 기록에 저장됩니다. 자세한 내용은 [Azure Machine Learning에 대한 Git 통합](concept-train-model-git-integration.md)을 참조하세요.

## <a name="notebook-examples"></a><a name="notebooks"></a>Notebook 예제

다양한 학습 시나리오에 대한 실행 구성의 예제는 다음 Notebook을 참조하세요.
* [다양한 컴퓨팅 대상에 대한 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [ML 프레임워크를 사용한 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>문제 해결

* **AttributeError: 'RoundTripLoader' 개체에 'comment_handling' 특성이 없습니다.** 이 오류는 `azureml-core` 종속성인 `ruamel-yaml`의 새 버전(v0.17.5)에서 발생합니다. 이 버전에는 `azureml-core`에 대한 호환성이 손상되는 변경이 도입되었습니다. 이 오류를 수정하려면 `pip uninstall ruamel-yaml`을 실행하고 다른 버전의 `ruamel-yaml`을 설치하여 `ruamel-yaml`을 제거합니다. 지원되는 버전은 v0.15.35 ~ v0.17.4(포함)입니다. `pip install "ruamel-yaml>=0.15.35,<0.17.5"`를 실행하여 이 작업을 수행할 수 있습니다.


* **`jwt.exceptions.DecodeError`로 인한 실행 실패**: 정확한 오류 메시지: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()`. 
    
    최신 버전의 azureml-core로 업그레이드하는 것이 좋습니다(`pip install -U azureml-core`).
    
    로컬 실행에 대해 이 문제가 발생하는 경우 실행을 시작하는 환경에 설치된 PyJWT 버전을 확인합니다. 지원되는 PyJWT 버전은 2.0.0 미만입니다. 버전이 2.0.0 이상인 경우 환경에서 PyJWT를 제거합니다. 다음과 같이 PyJWT 버전을 확인하고, 제거하고, 적절한 버전을 설치할 수 있습니다.
    1. 명령 셸을 시작하고 azureml-core가 설치된 conda 환경을 활성화합니다.
    2. `pip freeze`를 입력하고 `PyJWT`를 찾습니다. 찾은 경우 나열된 버전은 2.0.0 미만입니다.
    3. 나열된 버전이 지원되는 버전이 아닌 경우 명령 셸에서 `pip uninstall PyJWT`를 수행하고 확인을 위해 y를 입력합니다.
    4. `pip install 'PyJWT<2.0.0'`를 사용하여 설치
    
    실행과 함께 사용자가 만든 환경을 제출하는 경우 해당 환경에서 최신 버전의 azureml-core를 사용하는 것이 좋습니다. 1\.18.0 이상 버전의 azureml-core는 이미 2.0.0보다 높은 PyJWT 버전으로 고정되어 있습니다. 제출하는 환경에서 azureml-core 버전 1.18.0미만을 사용해야 하는 경우 pip 종속성에서 PyJWT를 2.0.0 미만으로 지정해야 합니다.


 * **ModuleErrors(명명된 모듈이 없음)** : Azure ML에서 실험을 제출하는 동안 ModuleErrors가 발생하는 경우 학습 스크립트에서 패키지가 설치될 것으로 예상하지만 추가되지 않습니다. 패키지 이름이 제공되면 Azure ML에서 패키지를 학습 실행에 사용되는 환경에 설치합니다.

    Estimator를 사용하여 실험을 제출하는 경우 패키지를 설치하려는 원본을 기반으로 하여 추정기에서 `pip_packages` 또는 `conda_packages` 매개 변수를 통해 패키지 이름을 지정할 수 있습니다. 또한 `conda_dependencies_file`을 사용하여 모든 종속성이 있는 yml 파일을 지정하거나, `pip_requirements_file` 매개 변수를 사용하여 모든 pip 요구 사항을 txt 파일에 나열할 수 있습니다. 추정기에서 사용하는 기본 이미지를 재정의하려는 사용자 고유의 Azure ML Environment 개체가 있는 경우 추정기 생성자의 `environment` 매개 변수를 통해 해당 환경을 지정할 수 있습니다.
    
    Azure ML에서 유지 관리되는 docker 이미지 및 해당 콘텐츠는 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 볼 수 있습니다.
    프레임워크 관련 종속성은 해당 프레임워크 설명서에 나와 있습니다.
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [Tensorflow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > 특정 패키지가 일반적으로 Azure ML에서 유지 관리되는 이미지 및 환경에 추가되는 데 충분히 적합하다고 생각하는 경우 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 GitHub 문제를 제기하세요. 
 
* **NameError(이름이 정의되지 않음), AttributeError(개체에 특성이 없음)** : 이 예외는 학습 스크립트에서 발생해야 합니다. Azure Portal에서 로그 파일을 검토하여 정의되지 않은 특정 이름 또는 특성 오류에 대한 자세한 정보를 얻을 수 있습니다. SDK에서 `run.get_details()`를 사용하여 오류 메시지를 볼 수 있습니다. 또한 여기서는 실행을 위해 생성된 모든 로그 파일도 나열합니다. 실행을 다시 제출하기 전에 학습 스크립트를 검토하고 오류를 수정했는지 확인합니다. 


* **실행 또는 실험 삭제**: 실험은 [Experiment.archive](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) 메서드를 사용하거나 "실험 보관" 단추를 통해 Azure Machine Learning 스튜디오 클라이언트의 [실험] 탭 보기에서 보관할 수 있습니다. 이 작업은 목록 쿼리 및 보기에서 실험을 숨기지만 삭제하지는 않습니다.

    개별 실험 또는 실행의 영구 삭제는 현재 지원되지 않습니다. 작업 영역 자산을 삭제하는 방법에 대한 자세한 내용은 [Machine Learning Service 작업 영역 데이터 내보내기 또는 삭제](how-to-export-delete-data.md)를 참조하세요.

* **메트릭 문서가 너무 큼**: Azure Machine Learning에는 학습 실행에서 한 번에 기록할 수 있는 메트릭 개체의 크기에 대한 내부 제한이 있습니다. 목록 값 메트릭을 로깅할 때 "메트릭 문서가 너무 큼" 오류가 발생하면 목록을 더 작은 청크로 분할해보세요. 예를 들면 다음과 같습니다.

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    내부적으로 Azure ML은 동일한 메트릭 이름을 가진 블록을 연속된 목록에 연결합니다.

* **컴퓨팅 대상을 시작하는 데 시간이 오래 걸림**: 컴퓨팅 대상의 Docker 이미지는 ACR(Azure Container Registry)에서 로드됩니다. 기본적으로 Azure Machine Learning은 *기본* 서비스 계층을 사용하는 ACR을 만듭니다. 작업 영역에 대한 ACR을 표준 또는 프리미엄 계층으로 변경하면 이미지를 빌드하고 로드하는 데 걸리는 시간을 줄일 수 있습니다. 자세한 내용은 [Azure Container Registry 서비스 계층](../container-registry/container-registry-skus.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [자습서: 모델 학습](tutorial-train-models-with-aml.md)은 모델 학습에 관리되는 컴퓨팅 대상을 사용합니다.
* [Scikit-learn](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md) 및 [PyTorch](how-to-train-pytorch.md)와 같은 특정 ML 프레임워크를 사용하여 모델을 학습시키는 방법을 알아봅니다.
* [하이퍼 매개 변수를 효율적으로 튜닝](how-to-tune-hyperparameters.md)하여 보다 나은 모델을 빌드하는 방법을 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [ScriptRunConfig 클래스](/python/api/azureml-core/azureml.core.scriptrunconfig) SDK 참조를 살펴봅니다.
* [Azure Virtual Networks에서 Azure Machine Learning 사용](./how-to-network-security-overview.md)
