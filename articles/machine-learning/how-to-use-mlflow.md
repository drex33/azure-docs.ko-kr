---
title: ML 실험을 위한 MLflow 추적
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 사용해 MLflow 추적을 설정하여 ML 모델에서 메트릭 및 아티팩트를 로깅합니다.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: nibaccam
ms.date: 05/25/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 01b0d2223bd56fd9b5a06666b82b89dd0a4c837d
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428790"
---
# <a name="track-ml-models-with-mlflow-and-azure-machine-learning"></a>MLflow 및 Azure Machine Learning을 사용하여 ML 모델 추적

이 문서에서는 MLflow의 추적 URI 및 로깅 API(총칭하여 [MLflow 추적](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api))를 사용하여 Azure Machine Learning을 MLflow 실험의 백 엔드로 연결하는 방법을 알아봅니다. 

지원되는 기능은 다음과 같습니다. 

+ [Azure Machine Learning 작업 영역](./concept-azure-machine-learning-architecture.md#workspace)에서 실험 메트릭과 아티팩트를 추적하고 로깅합니다. 실험을 위해 MLflow 추적을 이미 사용하고 있는 경우 작업 영역이 학습 메트릭 및 모델을 저장할 중앙 집중화된 안전하고 확장성 있는 위치를 제공합니다.

+ [Azure Machine Learning 백 엔드 지원을 통해 MLflow 프로젝트로 학습 작업을 제출](how-to-train-mlflow-projects.md)합니다. Azure Machine Learning 추적을 사용하여 로컬로 작업을 제출하거나 [Azure Machine Learning 컴퓨팅](how-to-create-attach-compute-cluster.md)을 통해 실행을 클라우드로 마이그레이션할 수 있습니다.

+ MLflow 및 Azure Machine Learning 모델 레지스트리에서 모델을 추적하고 관리합니다.

[MLflow](https://www.mlflow.org)는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 실험 환경에 관계없이(컴퓨터에서 로컬로, 원격 컴퓨팅 대상, 가상 머신 또는 [Azure Databricks 클러스터](how-to-use-mlflow-azure-databricks.md)에서) 학습 실행 메트릭과 모델 아티팩트를 로깅하고 추적하는 MLflow의 구성 요소입니다. 

추가 MLflow 및 Azure Machine Learning 기능 통합에 대해 [MLflow 및 Azure Machine Learning](concept-mlflow.md)을 참조하세요.

다음 다이어그램에서는 MLflow 추적을 사용하여 Azure Machine Learning 작업 영역에 실험의 실행 메트릭과 모델 아티팩트를 저장하는 방법을 보여 줍니다.

![Azure Machine Learning을 사용한 MLflow 다이어그램](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하려는 데이터 과학자와 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용과 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

> [!NOTE] 
> SQL 스토리지, 서버, UI 또는 데이터 과학 종속성이 없는 경량 MLflow 패키지인 [MLflow Skinny 클라이언트](https://github.com/mlflow/mlflow/blob/master/README_SKINNY.rst)를 사용할 수 있습니다. 이는 배포를 포함한 MLflow 기능의 전체 제품군을 가져오지 않고 주로 추적 및 로깅 기능이 필요한 사용자에게 권장됩니다. 

## <a name="prerequisites"></a>전제 조건

* `azureml-mlflow` 패키지를 설치합니다. 
    * 이 패키지는 작업 영역에 액세스하기 위해 MLflow에 대한 연결을 제공하는 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)의 `azureml-core`를 자동으로 가져옵니다.
* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).
    * [작업 영역을 사용하여 MLflow 작업을 수행하는 데 필요한 액세스 권한](how-to-assign-roles.md#mlflow-operations)을 확인하세요.

## <a name="track-local-runs"></a>로컬 실행 추적

MLflow 추적을 Azure Machine Learning과 함께 사용하면 로컬 실행의 로깅된 메트릭 및 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다.

`mlflow` 및 [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) 클래스를 가져와 MLflow의 추적 URI에 액세스하고 작업 영역을 구성합니다.

다음 코드에서 `get_mlflow_tracking_uri()` 메서드는 작업 영역 `ws`에 고유한 추적 URI 주소를 할당하고, `set_tracking_uri()`는 이 주소로 MLflow 추적 URI를 가리킵니다.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>추적 URI는 최대 1시간까지 유효합니다. 유휴 시간 이후 스크립트를 다시 시작하는 경우 get_mlflow_tracking_uri API를 사용하여 새 URI를 가져옵니다.

`set_experiment()`를 사용하여 MLflow 실험 이름을 설정하고 `start_run()`을 사용하여 학습 실행을 시작합니다. 그런 다음 `log_metric()`을 사용하여 MLflow 로깅 API를 활성화하고 학습 실행 메트릭 로깅을 시작합니다.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>원격 실행 추적

원격 실행을 사용하면 GPU 사용 가상 머신 또는 Machine Learning 컴퓨팅 클러스터와 같은 보다 강력한 컴퓨팅을 통해 모델을 학습시킬 수 있습니다. 다양한 컴퓨팅 옵션에 대해 알아보려면 [모델 학습에 컴퓨팅 대상 사용](how-to-set-up-training-targets.md)을 참조하세요.

MLflow 추적을 Azure Machine Learning과 함께 사용하면 원격 실행의 로깅된 메트릭 및 아티팩트를 Azure Machine Learning 작업 영역에 저장할 수 있습니다. MLflow 추적 코드가 포함된 모든 실행에는 메트릭이 작업 영역에 자동으로 기록됩니다. 

다음 예제 conda 환경은 `mlflow` 및 `azureml-mlflow`를 pip 패키지로 포함합니다. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - mlflow
    - numpy
```

스크립트에서 [`Environment`](/python/api/azureml-core/azureml.core.environment.environment) 클래스를 사용하여 컴퓨팅 및 학습 실행 환경을 구성합니다. 그런 다음, 컴퓨팅 대상으로 원격 컴퓨팅을 사용하여 [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig)를 구성합니다.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

이 컴퓨팅 및 학습 실행 구성에서 `Experiment.submit()` 메서드를 사용하여 실행을 제출합니다. 이 메서드는 자동으로 MLflow 추적 URI를 설정하고 MLflow에서 작업 영역으로 로깅을 보냅니다.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>작업 영역에서 메트릭 및 아티팩트 보기

MLflow 로깅의 메트릭과 아티팩트는 작업 영역에 유지됩니다. 언제든지 이들을 보려면 작업 영역으로 이동하여 [Azure Machine Learning 스튜디오](https://ml.azure.com)의 작업 영역에서 이름으로 실험을 찾습니다.  또는 아래 코드를 실행합니다. 

```python
run.get_metrics()
```

## <a name="manage-models"></a>모델 관리 

MLflow 모델 레지스트리를 지원하는 [Azure Machine Learning 모델 레지스트리](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere)를 통해 모델을 등록하고 추적합니다. Azure Machine Learning 모델은 MLflow 모델 스키마에 맞게 조정되어 다양한 워크플로에서 이러한 모델을 쉽게 내보내고 가져올 수 있습니다. 실행 ID와 같은 MLflow 관련 메타데이터도 추적 가능성을 위해 등록된 모델로 태그가 지정됩니다. 사용자는 학습 실행을 제출하고 MLflow 실행에서 생성된 모델을 등록 및 배포할 수 있습니다. 

프로덕션 준비 모델을 한 단계로 배포하고 등록하려면 [MLflow 모델 배포 및 등록](how-to-deploy-mlflow-models.md)을 참조하세요.

실행에서 모델을 등록하고 보려면 다음 단계를 사용합니다.

1. 실행이 완료되면 `register_model()` 메서드를 호출합니다.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. [Azure Machine Learning 스튜디오](overview-what-is-machine-learning-studio.md)를 사용하여 작업 영역에서 등록된 모델을 봅니다.

    다음 예제에서 등록된 모델 `my-model`에는 MLflow 추적 메타데이터 태그가 지정되어 있습니다. 

    ![register-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. **아티팩트** 탭을 선택하여 MLflow 모델 스키마(conda.yaml, MLmodel, model.pkl)와 일치하는 모든 모델 파일을 확인합니다.

    ![model-schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. MLmodel을 선택하여 실행을 통해 생성된 MLmodel 파일을 확인합니다.

    ![MLmodel-schema](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>리소스 정리

작업 영역에서 기록된 메트릭과 아티팩트를 사용하지 않으려는 경우에는 개별적으로 삭제할 수 있는 기능을 현재 사용할 수 없습니다. 대신, 스토리지 계정 및 작업 영역을 포함하는 리소스 그룹을 삭제하여 요금이 발생하지 않도록 합니다.

1. Azure Portal의 맨 왼쪽에서 **리소스 그룹** 을 선택합니다.

   ![Azure Portal에서 삭제](./media/how-to-use-mlflow/delete-resources.png)

1. 목록에서 만든 리소스 그룹을 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

## <a name="example-notebooks"></a>노트북 예제

[Azure ML을 사용한 MLflow 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)은 이 문서에 나와 있는 개념을 시연하고 확장합니다.

> [!NOTE]
> MLflow를 사용하는 커뮤니티 기반 예제 리포지토리는 https://github.com/Azure/azureml-examples 에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [MLflow를 사용하여 모델을 배포](how-to-deploy-mlflow-models.md)합니다.
* [데이터 드리프트](./how-to-enable-data-collection.md)를 위한 프로덕션 모델 모니터링.
* [MLflow를 사용하여 Azure Databricks 실행 추적](how-to-use-mlflow-azure-databricks.md)
* [모델 관리](concept-model-management-and-deployment.md).
