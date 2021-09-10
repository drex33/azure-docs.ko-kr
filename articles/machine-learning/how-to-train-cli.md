---
title: CLI(v2)를 사용한 모델 학습(작업 만들기)
titleSuffix: Azure Machine Learning
description: Machine Learning에 대한 Azure CLI 확장을 사용한 모델 학습(작업 만들기) 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 06/18/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 9f3a91f9abc472f285139bfac04af7dff5c63e9f
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122568203"
---
# <a name="train-models-create-jobs-with-the-cli-v2"></a>CLI(v2)를 사용한 모델 학습(작업 만들기)

Azure CLI 확장인 Azure Machine Learning CLI(v2)를 사용하면 추적 및 감사 가능한 모델 수명 주기를 통해 Azure 컴퓨팅을 스케일 업 및 아웃하는 동시에 모델 학습 프로세스를 가속화할 수 있습니다.

기계 학습 모델을 학습시키는 과정은 반복 프로세스입니다. 최신 도구를 사용하면 더 많은 모델에 더 많은 데이터를 전보다 훨씬 더 빠르게 학습시킬 수 있습니다. 하이퍼 매개 변수 튜닝과 알고리즘 선택 같은 이전의 지루한 수동 프로세스가 자동화되는 경우가 많습니다. Azure Machine Learning CLI를 사용하면 하이퍼 매개 변수 스윕을 통해 [작업 영역](concept-workspace.md)에서 작업(및 모델)을 추적하고, 고성능 Azure 컴퓨팅을 스케일 업하고, 분산 학습을 활용하여 스케일 아웃할 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

- CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
- [Machine Learning에 대한 Azure CLI 확장 설치 및 설정](how-to-configure-cli.md)

> [!TIP]
> 모든 기능을 갖춘 개발 환경을 위해서는 Visual Studio Code 및 [Azure Machine Learning 확장](how-to-setup-vs-code.md)을 사용하여 [Azure Machine Learning 리소스를 관리](how-to-manage-resources-vscode.md)하고 [기계 학습 모델을 학습](tutorial-train-deploy-image-classification-model-vscode.md)시킵니다.

### <a name="clone-examples-repository"></a>예제 리포지토리 복제

학습 예제를 실행하려면 먼저 예제 리포지토리를 복제하고 `cli` 디렉터리로 변경합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="git_clone":::

`--depth 1`은 리포지토리에 대한 최신 커밋만 복제하여 작업을 완료하는 시간을 줄입니다.

### <a name="create-compute"></a>컴퓨팅 만들기

명령줄에서 Azure Machine Learning 컴퓨팅 클러스터를 만들 수 있습니다. 예를 들어 다음 명령은 `cpu-cluster`라는 클러스터 하나와 `gpu-cluster`라는 클러스터 하나를 만듭니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/create-compute.sh" id="create_computes":::

이때 컴퓨팅 요금은 청구되지 않습니다. `cpu-cluster` 및 `gpu-cluster`는 작업이 제출될 때까지 0개 노드에 유지되기 때문입니다. [AmlCompute의 비용을 관리하고 최적화](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute)하는 방법에 대해 자세히 알아보세요.

이 문서의 다음 예제 작업은 `cpu-cluster` 또는 `gpu-cluster` 중 하나를 사용합니다. 필요에 따라 클러스터의 이름으로 조정합니다.

컴퓨팅 만들기 옵션에 대한 자세한 내용을 보려면 `az ml compute create -h`를 사용하세요.

[!INCLUDE [arc-enabled-kubernetes](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

## <a name="introducing-jobs"></a>작업 소개

Azure Machine Learning CLI(v2)에서 작업은 YAML 형식으로 작성됩니다. 작업은 다음을 집계합니다.

- 실행할 버전
- 실행 방법
- 실행 위치

"hello world" 작업에는 세 항목이 모두 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/misc/hello-world.yml":::

다음을 실행할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="hello_world":::

이는 로그 파일에서 한 줄만 출력하는 예제 작업입니다. 일반적으로 시스템 생성 로그 외에 모델 이진 파일 및 함께 제공되는 메타데이터와 같은 추가 아티팩트를 생성하는 것이 좋습니다.

Azure Machine Learning은 다음 아티팩트를 자동으로 캡처합니다.

- `./outputs` 및 `./logs` 디렉터리는 Azure Machine Learning에서 별도로 처리합니다. 작업 중에 이러한 디렉터리에 파일을 기록하는 경우 작업이 완료된 후에도 해당 파일에 계속 액세스할 수 있도록 작업의 실행 기록에 이러한 파일이 업로드됩니다. `./outputs` 폴더는 작업이 종료될 때 업로드되는 반면, `./logs`에 기록된 파일은 실시간으로 업로드됩니다. 작업 중에 TensorBoard 로그와 같은 로그를 스트리밍하려면 후자를 사용합니다.
- Azure Machine Learning은 MLflow의 추적 기능과 통합됩니다. `mlflow.autolog()`를 몇 가지 일반적인 ML 프레임워크에 사용하여 모델 매개 변수, 성능 메트릭, 모델 아티팩트 및 기능 중요도 그래프를 로그할 수 있습니다. 또한 `mlflow.log_*()` 메서드를 사용하여 매개 변수, 메트릭 및 아티팩트를 명시적으로 로그할 수도 있습니다. MLflow로 로그된 모든 메트릭 및 아티팩트는 작업의 실행 기록에 저장됩니다.

작업에는 로컬에서 편집 및 제어되는 일부 소스 코드를 실행하는 과정이 포함되는 경우가 많습니다. 명령이 실행될 작업에 포함할 소스 코드 디렉터리를 지정할 수 있습니다.

예를 들어 예제 리포지토리에서 `jobs/train/lightgbm/iris` 프로젝트 디렉터리를 살펴봅니다.

```tree
.
├── job-sweep.yml
├── job.yml
└── src
    └── main.py
```

이 디렉터리에는 소스 코드 하위 디렉터리인 `src`라는 두 개의 작업 파일이 포함되어 있습니다. 이 예제에서는 `src` 아래에 파일이 하나만 있지만 전체 하위 디렉터리가 재귀적으로 업로드되며 작업에 사용할 수 있습니다.

명령 작업은 `job.yml`을 통해 구성됩니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

다음을 실행할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris":::

## <a name="basic-python-training-job"></a>기본 Python 학습 작업

작업 YAML 파일을 자세히 살펴보겠습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job.yml":::

| 키 | 설명 |
| --- | ----------- |
| `$schema` | [선택 사항] YAML 스키마입니다. 위 예제의 [스키마](https://azuremlschemas.azureedge.net/latest/commandJob.schema.json)를 브라우저에서 보면 명령 작업 YAML 파일에서 사용할 수 있는 모든 옵션을 볼 수 있습니다. Azure Machine Learning VS Code 확장을 사용하여 YAML 파일을 작성하는 경우 파일 상단에 이 `$schema` 속성을 포함하면 스키마 및 리소스 완성을 호출할 수 있습니다. |
| `code.local_path` | [선택 사항] 작업과 함께 업로드되고 사용되는 YAML 파일에 상대적인 원본 디렉터리의 로컬 경로. 일관성을 위해 작업 파일과 동일한 디렉터리에서 `src`를 사용하는 것이 좋습니다. |
| `command` | 실행할 명령입니다. `>` 규칙을 사용하면 줄바꿈을 접어 공백으로 만드는 방식으로 읽을 수 있는 여러 줄의 명령을 작성할 수 있습니다. 명령줄 인수를 명령에 명시적으로 기록하거나 중괄호 표기법을 사용하여 다른 섹션(특히 `inputs` 또는 `search_space`)에서 유추할 수 있습니다. |
| `inputs` | [선택 사항] 입력 데이터 바인딩의 사전으로, 키가 입력 바인딩에 지정된 이름입니다. 각 요소의 값은 `data` 및 `mode` 필드로 구성되는 입력 바인딩입니다. `data`는 1) `azureml:` 접두사(예: "iris-url"이라는 데이터 자산의 버전 1을 가리키는 `azureml:iris-url:1`)를 사용하여 버전이 지정되는 기존 Azure Machine Learning 데이터 자산에 대한 참조이거나 2) 데이터의 인라인 정의일 수 있습니다. `data.path`를 사용하여 클라우드 위치를 지정합니다. `data.local_path`를 사용하여 기본 데이터 저장소에 업로드할 로컬 파일 시스템의 데이터를 지정합니다. `mode`는 작업의 컴퓨팅에서 데이터를 사용할 수 있는 방법을 나타냅니다. "mount" 및 "download"라는 두 가지 옵션이 지원됩니다. <br><br> 명령에서 `{inputs.my_input_name}` 같은 이름으로 입력을 참조할 수 있습니다. 그러면 Azure Machine Learning이 런타임 중에 명령의 매개 변수화된 표기법을 통해 컴퓨팅 대상의 해당 데이터 위치를 확인합니다. 예를 들어 데이터가 탑재되도록 구성된 경우 `{inputs.my_input_name}`이 탑재 지점으로 확인됩니다. |
| `environment` | 컴퓨팅 대상에서 명령을 실행할 환경. 사용할 Docker 이미지 또는 이미지를 빌드하는 데 사용할 Dockerfile을 지정하여 환경을 인라인으로 정의할 수 있습니다. `azureml:` 접두사를 사용하여 작업 영역에서 버전이 지정된 기존 환경 또는 Azure ML의 큐레이팅된 환경 중 하나를 참조할 수도 있습니다. 예를 들어 `azureml:AzureML-TensorFlow2.4-Cuda11-OpenMpi4.1.0-py36:1`은 GPU 지원이 포함된 TensorFlow에 대한 큐레이팅된 환경의 버전 1을 참조합니다. <br><br> 학습에 사용되는 환경에 Python을 설치해야 합니다. 필요한 경우 Dockerfile에서 `apt-get update -y && apt-get install python3 -y`를 실행하여 설치합니다. |
| `compute.target` | 컴퓨팅 대상. 로컬에서 실행하려면 `local`을 지정하고, 작업 영역에서 기존 컴퓨팅 리소스를 참조하려면 `azureml:` 접두사를 사용합니다. 예를 들어 `azureml:cpu-cluster`는 "cpu-cluster"라는 컴퓨팅 대상을 가리킵니다. |
| `experiment_name` | [선택 사항] Azure Machine Learning 스튜디오에서 작업에 태그를 지정하면 더 간편하게 정리할 수 있습니다. 각 작업의 실행 레코드는 스튜디오의 "실험" 탭에 있는 해당 실험 아래에 정리됩니다. 생략하면 작업이 생성된 작업 디렉터리의 이름으로 기본 설정됩니다. |
| `name` | [선택 사항] 작업 영역의 모든 작업에서 고유해야 하는 작업의 이름. YAML 파일에서 `name` 필드를 통해 또는 명령줄에서 `--name/-n`을 통해 이름을 지정하지 않으면 GUID/UUID가 자동으로 생성되어 이름에 사용됩니다. 작업 이름은 작업 실행 레코드의 스튜디오 UI에 있는 실행 ID에 해당합니다. |

이 작업을 만들면 소스 코드 디렉터리와 같은 지정된 모든 로컬 자산이 업로드되어 YAML 파일의 유효성을 검사하고 실행을 제출합니다. 필요한 경우 환경이 빌드된 후 작업 실행을 위해 컴퓨팅이 스케일 업되고 구성됩니다.

lightgbm/iri 학습 작업을 실행하려면 다음을 수행합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris":::

작업이 완료되면 출력을 다운로드할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="download_outputs":::

> [!IMPORTANT]
> `$run_id`를 콘솔 출력 또는 스튜디오의 실행 세부 정보 페이지에서 찾을 수 있는 실행 ID로 바꿉니다.

그러면 로그 및 캡처된 모든 아티팩트가 `$run_id`라는 디렉터리에 로컬로 다운로드됩니다. 이 예에서는 MLflow 로그 모델 하위 디렉터리가 다운로드됩니다.

## <a name="sweep-hyperparameters"></a>하이퍼 매개 변수 스윕

또한 Azure Machine Learning을 사용하여 기계 학습 모델의 하이퍼 매개 변수를 보다 효율적으로 튜닝할 수 있습니다. 스윕 작업이라고 하는 하이퍼 매개 변수 튜닝 작업을 구성하고 CLI를 통해 제출할 수 있습니다.

`job.yml`을 `job-sweep.yml`로 수정하여 하이퍼 매개 변수를 스윕할 수 있습니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/lightgbm/iris/job-sweep.yml":::

| 키 | 설명 |
| --- | ----------- |
| `$schema` | [선택 사항] 이제 스윕 작업 [스키마](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)를 가리키도록 변경된 YAML 스키마. |
| `type` | 작업 유형입니다. |
| `algorithm` | 샘플링 알고리즘 - "random"을 선택하는 것이 좋은 경우가 많습니다. 옵션의 열거형은 스키마를 참조하세요. |
| `trial` | 실행할 각 평가판에 대한 명령 작업 구성입니다. 이전 예제의 명령(`trial.command`)은 `{search_space.<hyperparameter_name>}` 표기법을 사용하여 `search_space`에 정의된 하이퍼 매개 변수를 참조하도록 수정되었습니다. 그러면 Azure Machine Learning은 매개 변수화된 각 표기법을 통해 각 평가판에 대해 생성되는 해당 하이퍼 매개 변수의 값을 확인합니다. |
| `search_space` | 스윕할 하이퍼 매개 변수의 사전. 키는 하이퍼 매개 변수의 이름입니다(예: `search_space.learning_rate`). 이름은 학습 스크립트의 인수 자체와 일치할 필요가 없으며, 명령에서 중괄호 표기법(예: `{search_space.learning_rate}`)의 검색 공간 참조와 일치하기만 하면 됩니다. 이 값은 하이퍼 매개 변수 분포입니다. 옵션의 열거형은 스키마를 참조하세요. |
| `objective.primary_metric` | 학습 코드에서 로그된 메트릭의 이름과 일치해야 하는 최적화 메트릭. `objective.goal`은 방향을 지정합니다("minimize"/"maximize"). 옵션의 전체 열거형은 스키마를 참조하세요. |
| `max_total_trials` | 실행할 개별 평가판의 최대 수. |
| `max_concurrent_trials` | [선택 사항] 컴퓨팅 클러스터에서 동시에 실행할 최대 평가판 수. |
| `timeout_minutes` | [선택 사항] 스윕 작업을 실행할 최대 시간(분). |
| `experiment_name` | [선택 사항] 스윕 작업을 추적할 실험. 생략하면 작업을 만들 때 기본적으로 작업 디렉터리 이름으로 지정됩니다. |

작업을 만들고 스튜디오에서 엽니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="lightgbm_iris_sweep":::

> [!TIP]
> 하이퍼 매개 변수 스윕은 분산 명령 작업과 함께 사용할 수 있습니다.

## <a name="distributed-training"></a>분산 학습

명령 작업에 `distribution` 섹션을 지정할 수 있습니다. Azure ML은 PyTorch, Tensorflow 및 MPI 호환 프레임워크에 대한 분산 학습을 지원합니다. PyTorch 및 TensorFlow는 TensorFlow용 `tf.distributed.Strategy` API와 같은 관련 프레임워크에 대해 네이티브 분산 학습을 지원합니다.

`compute.instance_count`(기본값 1인)를 작업에 대해 원하는 노드 수로 설정해야 합니다.

### <a name="pytorch"></a>PyTorch

CIFAR-10 데이터 세트에 대한 분산 PyTorch 학습을 위한 YAML 파일 예제:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/pytorch/cifar-distributed/job.yml":::

이는 복제된 예제 리포지토리에 없는 로컬 데이터를 참조합니다. 먼저 CIFAR-10 데이터 세트를 로컬에서 다운로드하고, 추출하고, 프로젝트 디렉터리의 적절한 위치로 다시 배치해야 합니다.

:::code language="bash" source="~/azureml-examples-main/cli/train.sh" id="download_cifar":::

작업을 만들고 스튜디오에서 엽니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="pytorch_cifar":::

### <a name="tensorflow"></a>TensorFlow

MNIST 데이터 세트에 대한 분산형 TensorFlow 학습용 예제 YAML 파일:

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed/job.yml":::

작업을 만들고 스튜디오에서 엽니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="tensorflow_mnist":::

### <a name="mpi"></a>MPI

Azure ML은 여러 노드 및 노드당 여러 프로세스에서 MPI 작업을 시작하도록 지원합니다. `mpirun`을 통해 작업을 시작합니다. 예를 들어 학습 코드에서 분산 학습에 Horovod 프레임워크를 사용하는 경우 이 작업 유형을 활용하여 Azure ML에 대해 학습시킬 수 있습니다.

MPI 작업을 시작하려면 유형으로 `mpi`를 지정하고 `distribution` 섹션에서 시작할 노드당 프로세스 수(`process_count_per_instance`)를 지정합니다. 이 필드가 지정되지 않으면 Azure ML에서 기본적으로 노드당 하나의 프로세스를 시작합니다.

Horovod를 사용하여 MNIST에서 TensorFlow 작업을 실행하는 YAML 사양의 예입니다.

:::code language="yaml" source="~/azureml-examples-main/cli/jobs/train/tensorflow/mnist-distributed-horovod/job.yml":::

작업을 만들고 스튜디오에서 엽니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/train.sh" id="tensorflow_mnist_horovod":::

## <a name="next-steps"></a>다음 단계

- [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)
- [REST를 사용하여 모델 학습(미리 보기)](how-to-train-with-rest.md)
