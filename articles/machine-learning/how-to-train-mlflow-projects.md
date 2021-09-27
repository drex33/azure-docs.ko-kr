---
title: MLflow 프로젝트를 사용하여 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 사용해 MLflow를 설정하여 ML 모델에서 메트릭 및 아티팩트를 로깅합니다.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 06/16/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 11358869e727948627e76c0f56c049b95c0abbb9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784784"
---
# <a name="train-ml-models-with-mlflow-projects-and-azure-machine-learning"></a>MLflow 프로젝트 및 Azure Machine Learning을 사용하여 ML 모델 학습

이 문서에서는 [MLflow 프로젝트](https://www.mlflow.org/docs/latest/projects.html) 및 Azure Machine Learning 백 엔드 지원을 사용하여 학습 작업을 제출하기 위해 MLflow의 추적 URI 및 로깅 API(총칭하여 [MLflow 추적](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api))를 사용하는 방법을 알아봅니다. Azure Machine Learning 추적을 사용하여 로컬로 작업을 제출하거나 [Azure Machine Learning 컴퓨팅](./how-to-create-attach-compute-cluster.md)을 통해 실행을 클라우드로 마이그레이션할 수 있습니다.

[MLflow 프로젝트](https://mlflow.org/docs/latest/projects.html)를 사용하면 다른 데이터 과학자(또는 자동화된 도구)가 코드를 실행할 수 있도록 코드를 구성하고 설명할 수 있습니다. Azure Machine Learning을 사용하는 MLflow 프로젝트를 사용하면 작업 영역에서 학습 실행을 추적하고 관리할 수 있습니다.

[MLflow](https://www.mlflow.org)는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 실험 환경에 관계없이(컴퓨터에서 로컬로, 원격 컴퓨팅 대상, 가상 머신 또는 [Azure Databricks 클러스터](how-to-use-mlflow-azure-databricks.md)에서) 학습 실행 메트릭과 모델 아티팩트를 로깅하고 추적하는 MLflow의 구성 요소입니다.

[MLflow 및 Azure Machine Learning 통합에 대해 자세히 알아보세요](how-to-use-mlflow.md).

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하려는 데이터 과학자와 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용과 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="prerequisites"></a>전제 조건

* `azureml-mlflow` 패키지를 설치합니다.
    * 이 패키지는 작업 영역에 액세스하기 위해 MLflow에 대한 연결을 제공하는 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)의 `azureml-core`를 자동으로 가져옵니다.
* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).
    * [작업 영역을 사용하여 MLflow 작업을 수행하는 데 필요한 액세스 권한](how-to-assign-roles.md#mlflow-operations)을 확인하세요.

## <a name="train-mlflow-projects-on-local-compute"></a>로컬 컴퓨팅에서 MLflow 프로젝트 학습

이 예제에서는 Azure Machine Learning 추적을 사용하여 MLflow 프로젝트를 로컬로 제출하는 방법을 보여줍니다.

`azureml-mlflow` 패키지를 설치하여 로컬 실험에서 Azure Machine Learning과 함께 MLflow 추적을 사용합니다. 실험은 Jupyter Notebook 또는 코드 편집기를 통해 실행할 수 있습니다.

```shell
pip install azureml-mlflow
```

`mlflow` 및 [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) 클래스를 가져와 MLflow의 추적 URI에 액세스하고 작업 영역을 구성합니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

`set_experiment()`를 사용하여 MLflow 실험 이름을 설정하고 `start_run()`을 사용하여 학습 실행을 시작합니다. 그런 다음, `log_metric()`을 사용하여 MLflow 로깅 API를 활성화하고 학습 실행 메트릭 로깅을 시작합니다.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

백 엔드 구성 개체를 만들어 컴퓨팅 대상 및 사용할 관리형 환경 유형과 같은 통합에 필요한 정보를 저장합니다.

```python
backend_config = {"USE_CONDA": False}
```

작업 영역에서 메트릭 및 주요 아티팩트를 추적하기 위해 `azureml-mlflow` 패키지를 환경 구성 파일에 pip 종속성으로 추가합니다. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

로컬 실행을 제출하고 매개 변수 `backend = "azureml" `을 설정했는지 확인합니다. 이 설정을 사용하면 로컬로 실행을 제출하고 작업 영역에서 자동 출력 추적, 로그 파일, 스냅샷 및 인쇄 오류에 대한 추가 지원을 받을 수 있습니다.

[Azure Machine Learning 스튜디오](overview-what-is-machine-learning-studio.md)에서 실행 및 메트릭을 확인합니다.

```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="train-mlflow-projects-with-remote-compute"></a>원격 컴퓨팅을 통해 MLflow 프로젝트 학습

이 예제에서는 Azure Machine Learning 추적을 사용하여 원격 컴퓨팅에서 MLflow 프로젝트를 제출하는 방법을 보여줍니다.

`azureml-mlflow` 패키지를 설치하여 로컬 실험에서 Azure Machine Learning과 함께 MLflow 추적을 사용합니다. 실험은 Jupyter Notebook 또는 코드 편집기를 통해 실행할 수 있습니다.

```shell
pip install azureml-mlflow
```

`mlflow` 및 [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) 클래스를 가져와 MLflow의 추적 URI에 액세스하고 작업 영역을 구성합니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

`set_experiment()`를 사용하여 MLflow 실험 이름을 설정하고 `start_run()`을 사용하여 학습 실행을 시작합니다. 그런 다음, `log_metric()`을 사용하여 MLflow 로깅 API를 활성화하고 학습 실행 메트릭 로깅을 시작합니다.

```Python
experiment_name = 'train-mlflow-project-amlcompute'
mlflow.set_experiment(experiment_name)
```

백 엔드 구성 개체를 만들어 컴퓨팅 대상 및 사용할 관리형 환경 유형과 같은 통합에 필요한 정보를 저장합니다.

통합에서는 "COMPUTE" 및 "USE_CONDA"를 매개 변수로 사용합니다. 여기서 "COMPUTE"는 원격 컴퓨팅 클러스터의 이름으로 설정되고, "USE_CONDA"는 환경 구성 파일에서 프로젝트에 대한 새 환경을 만듭니다. 개체에 "COMPUTE"가 있는 경우 프로젝트가 자동으로 원격 컴퓨팅에 제출되고 "USE_CONDA"는 무시됩니다. MLflow는 사전 개체 또는 JSON 파일을 허용합니다.

```python
# dictionary
backend_config = {"COMPUTE": "cpu-cluster", "USE_CONDA": False}
```

작업 영역에서 메트릭 및 주요 아티팩트를 추적하기 위해 `azureml-mlflow` 패키지를 환경 구성 파일에 pip 종속성으로 추가합니다. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```

mlflow 프로젝트 실행을 제출하고 매개 변수 `backend = "azureml" `을 설정해야 합니다. 이 설정을 사용하면 원격 컴퓨팅에 실행을 제출하고 작업 영역에서 자동 출력 추적, 로그 파일, 스냅샷 및 인쇄 오류에 대한 추가 지원을 받을 수 있습니다.

[Azure Machine Learning 스튜디오](overview-what-is-machine-learning-studio.md)에서 실행 및 메트릭을 확인합니다.

```python
remote_mlflow_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    backend_config = backend_config, 
                                    )

```


## <a name="clean-up-resources"></a>리소스 정리

작업 영역에서 기록된 메트릭과 아티팩트를 사용하지 않으려는 경우에는 개별적으로 삭제할 수 있는 기능을 현재 사용할 수 없습니다. 대신, 스토리지 계정 및 작업 영역을 포함하는 리소스 그룹을 삭제하여 요금이 발생하지 않도록 합니다.

1. Azure Portal의 맨 왼쪽에서 **리소스 그룹** 을 선택합니다.

   ![Azure Portal에서 삭제](./media/how-to-use-mlflow/delete-resources.png)

1. 목록에서 만든 리소스 그룹을 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

## <a name="example-notebooks"></a>노트북 예제

[Azure ML을 사용한 MLflow 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)은 이 문서에 나와 있는 개념을 시연하고 확장합니다.

  * [로컬 컴퓨팅에서 MLflow 프로젝트 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-local/train-projects-local.ipynb)
  * [원격 컴퓨팅에서 MLflow 프로젝트를 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow/train-projects-remote/train-projects-remote.ipynb)합니다.

> [!NOTE]
> MLflow를 사용하는 커뮤니티 기반 예제 리포지토리는 https://github.com/Azure/azureml-examples 에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [MLflow를 사용하여 모델을 배포](how-to-deploy-mlflow-models.md)합니다.
* [데이터 드리프트](./how-to-enable-data-collection.md)를 위한 프로덕션 모델 모니터링.
* [MLflow를 사용하여 Azure Databricks 실행 추적](how-to-use-mlflow-azure-databricks.md)
* [모델 관리](concept-model-management-and-deployment.md).
