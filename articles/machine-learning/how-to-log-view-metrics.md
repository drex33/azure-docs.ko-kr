---
title: 메트릭 및 로그 파일 로그 및 보기
titleSuffix: Azure Machine Learning
description: ML 학습 실행에서 로그를 사용하도록 설정하여 실시간 실행 메트릭을 모니터링하고 오류 및 경고를 진단하는 데 도움을 줍니다.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 81f05686954e35d68d93c598978e9676652fe71f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593480"
---
# <a name="log--view-metrics-and-log-files"></a>메트릭 및 로그 파일 로그 및 보기

기본 Python 로깅 패키지와 Azure Machine Learning Python SDK 관련 기능을 사용하여 실시간 정보를 로그할 수 있습니다. 로컬로 로그인하고 로그를 포털의 작업 영역에 보낼 수 있습니다.

로그는 오류 및 경고를 진단하거나 매개 변수 및 모델 성능과 같은 성능 메트릭을 추적하는 데 도움이 됩니다. 이 문서에서는 다음 시나리오에서 로깅을 사용하도록 설정하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 실행 메트릭 로그
> * 대화형 학습 세션
> * ScriptRunConfig를 사용하여 학습 작업 제출
> * Python 네이티브 `logging` 설정
> * 추가 원본에서 로깅


> [!TIP]
> 이 문서에서는 모델 학습 프로세스를 모니터링하는 방법을 보여 줍니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용량 및 이벤트를 모니터링하는 데 관심이 있는 경우 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="data-types"></a>데이터 형식

스칼라 값, 목록, 테이블, 이미지, 디렉터리 등을 포함한 여러 데이터 형식을 기록할 수 있습니다. 다양한 데이터 형식에 대한 자세한 내용과 Python 코드 예제는 [Run 클래스 참조 페이지](/python/api/azureml-core/azureml.core.run%28class%29)를 참조하세요.

## <a name="logging-run-metrics"></a>실행 메트릭 로그 

로깅 API에서 다음 메서드를 사용하여 메트릭 시각화에 영향을 줄 수 있습니다. 로그된 메트릭의 [서비스 한도](./resource-limits-quotas-capacity.md#metrics)에 유의하세요. 

|기록된 값|예제 코드| 포털의 형식|
|----|----|----|
|숫자 값의 배열 기록| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|단일 변수 꺾은선형 차트|
|반복적으로 사용되는 동일한 메트릭 이름(for 루프 내에서와 같이)을 사용하여 단일 숫자 값 기록| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 단일 변수 꺾은선형 차트|
|2개의 숫자 열을 반복적으로 사용하여 행 기록|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|두 개의 변수 꺽은선형 차트|
|두 개의 숫자 열을 사용하여 테이블 기록|`run.log_table(name='Sine Wave', value=sines)`|두 개의 변수 꺽은선형 차트|
|이미지 로그|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|이 메서드는 이미지 파일이나 matplotlib 플롯을 실행에 로그하는 데 사용합니다. 실행 기록에서 해당 이미지를 표시하고 비교할 수 있습니다.|

## <a name="logging-with-mlflow"></a>MLflow를 사용하여 로그

MLflow는 오픈 소스이며 클라우드 이식성에 대한 로컬 모드를 지원하기 때문에 MLflow를 사용하여 모델, 메트릭 및 아티팩트를 로깅하는 것이 좋습니다. 다음 표 및 코드 예제에서는 MLflow를 사용하여 학습 실행에서 메트릭 및 아티팩트를 로그하는 방법을 보여 줍니다. 
[MLflow의 로깅 메서드 및 디자인 패턴에 대해 자세히 알아봅니다](https://mlflow.org/docs/latest/python_api/mlflow.html#mlflow.log_artifact).

`mlflow` 및 `azureml-mlflow` pip 패키지를 작업 영역에 설치해야 합니다. 

```conda
pip install mlflow
pip install azureml-mlflow
```

MLflow 추적 URI가 Azure Machine Learning 백 엔드를 가리키도록 설정하여 메트릭 및 아티팩트가 작업 영역에 로그되도록 합니다. 

```python
from azureml.core import Workspace
import mlflow
from mlflow.tracking import MlflowClient

ws = Workspace.from_config()
mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())

mlflow.create_experiment("mlflow-experiment")
mlflow.set_experiment("mlflow-experiment")
mlflow_run = mlflow.start_run()
```

|기록된 값|예제 코드| 참고|
|----|----|----|
|숫자 값 로그(int 또는 float) | `mlflow.log_metric('my_metric', 1)`| |
|부울 값 로그 | `mlflow.log_metric('my_metric', 0)`| 0 = True, 1 = False|
|문자열 로그 | `mlflow.log_text('foo', 'my_string')`| 아티팩트로 로그됨|
|numpy 메트릭 또는 PIL 이미지 개체 로그|`mlflow.log_image(img, 'figure.png')`||
|matlotlib 플롯 또는 이미지 파일 로그|` mlflow.log_figure(fig, "figure.png")`||

## <a name="view-run-metrics-via-the-sdk"></a>SDK를 통해 실행 메트릭 보기
`run.get_metrics()`를 사용하여 학습된 모델의 메트릭을 볼 수 있습니다. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping metric names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

실행 개체의 데이터 및 정보 속성을 통해 MLflow를 사용하여 실행 정보에 액세스할 수도 있습니다. 자세한 내용은 [MLflow.entities.Run object](https://mlflow.org/docs/latest/python_api/mlflow.entities.html#mlflow.entities.Run) 설명서를 참조하세요. 

실행이 완료되면 MlFlowClient()를 사용하여 검색할 수 있습니다.

```python
from mlflow.tracking import MlflowClient

# Use MlFlow to retrieve the run that was just completed
client = MlflowClient()
finished_mlflow_run = MlflowClient().get_run(mlflow_run.info.run_id)
```

실행 개체의 데이터 필드에서 실행에 대한 메트릭, 매개 변수 및 태그를 볼 수 있습니다.

```python
metrics = finished_mlflow_run.data.metrics
tags = finished_mlflow_run.data.tags
params = finished_mlflow_run.data.params
```

>[!NOTE]
> `mlflow.entities.Run.data.metrics` 아래의 메트릭 사전은 지정된 메트릭 이름에 대해 가장 최근에 로그된 값만 반환합니다. 예를 들어 순서대로 1, 2, 3, 4를 `sample_metric`이라는 메트릭에 로드하는 경우 `sample_metric`의 메트릭 사전에는 4만 있습니다.
> 
> 특정 메트릭 이름에 대해 로그된 모든 메트릭을 얻으려면 [`MlFlowClient.get_metric_history()`](https://www.mlflow.org/docs/latest/python_api/mlflow.tracking.html#mlflow.tracking.MlflowClient.get_metric_history)를 사용할 수 있습니다.

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-the-studio-ui"></a>스튜디오 UI에서 실행 메트릭 보기

[Azure Machine Learning 스튜디오](https://ml.azure.com)에서 로그된 메트릭을 비롯한 완료된 실행 기록을 찾아볼 수 있습니다.

**실험** 탭으로 이동합니다. 작업 영역에서 실험 전체의 모든 실행을 보려면 **모든 실행** 탭을 선택합니다. 맨 위 메뉴 모음에서 실험 필터를 적용하여 특정 실험에 대한 실행으로 드릴다운할 수 있습니다.

개별 실험 보기를 확인하려면 **모든 실험** 탭을 선택합니다. 실험 실행 대시보드에서 각 실행에 대한 추적된 메트릭 및 로그를 볼 수 있습니다. 

실행 목록 테이블을 편집하여 여러 실행을 선택하고 실행에 대해 로그된 마지막 값, 최솟값, 최댓값을 표시할 수도 있습니다. 여러 실행의 로그된 메트릭 값과 집계를 비교하려면 차트를 사용자 지정합니다. 차트의 y-축에 여러 메트릭을 그리고 x-축을 사용자 지정하여 로그된 메트릭을 그릴 수 있습니다. 


### <a name="view-and-download-log-files-for-a-run"></a>실행에 대한 로그 파일 보기 및 다운로드 

로그 파일은 Azure ML 워크로드를 디버그하는 데 필수적인 리소스입니다. 학습 작업을 제출한 후 특정 실행으로 드릴다운하여 해당 로그 및 출력을 봅니다.  

1. **실험** 탭으로 이동합니다.
1. 특정 실행에 대한 runID를 선택합니다.
1. 페이지 맨 위에서 **출력 및 로그** 를 선택합니다.
2. **모두 다운로드** 를 선택하여 모든 로그를 zip 폴더로 다운로드합니다.
3. 개별 로그 파일, **다운로드** 를 차례로 선택하여 개별 로그 파일을 다운로드할 수도 있습니다.

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="실행에 대한 출력 및 로그 섹션 스크린샷입니다.":::

아래 표에는 이 섹션에서 볼 수 있는 폴더의 로그 파일 콘텐츠가 있습니다.

> [!NOTE]
> 모든 실행에 대한 모든 파일이 표시되는 것은 아닙니다. 예를 들어 20_image_build_log*.txt는 새 이미지가 빌드된 경우(예: 환경을 변경하는 경우)에만 표시됩니다.

#### <a name="azureml-logs-folder"></a>`azureml-logs` 폴더

|파일  |Description  |
|---------|---------|
|20_image_build_log.txt     | 학습 환경의 Docker 이미지 빌드 로그로, 선택적 로그이며 실행당 하나입니다. 환경을 업데이트하는 경우에만 적용됩니다. 그 외의 경우에는 AML에서 캐시된 이미지를 다시 사용합니다. 성공하는 경우 해당 이미지에 대한 이미지 레지스트리 세부 정보가 포함됩니다.         |
|55_azureml-execution-<node_id>.txt     | 호스트 도구의 stdout/stderr 로그로, 노드당 하나입니다. 이미지를 컴퓨팅 대상으로 풀(pull)합니다. 이 로그는 컴퓨팅 리소스가 보호된 경우에만 표시됩니다.         |
|65_job_prep-<node_id>.txt     |   작업 준비 스크립트의 stdout/stderr 로그로, 노드당 하나입니다. 코드를 컴퓨팅 대상 및 데이터 저장소(요청된 경우)로 다운로드합니다.       |
|70_driver_log(_x).txt      |  AML 제어 스크립트 및 고객 학습 스크립트의 stdout/stderr 로그로, 프로세스당 하나입니다. **스크립트의 표준 출력입니다. 이 파일에 코드 로그(예: print 문)가 표시됩니다.** 대부분의 경우 여기에서 로그를 모니터링합니다.       |
|70_mpi_log.txt     |   MPI 프레임워크 로그로, 선택 사항이며 실행당 하나입니다. MPI 실행에만 해당됩니다.   |
|75_job_post-<node_id>.txt     |  작업 릴리스 스크립트의 stdout/stderr 로그로, 노드당 하나입니다. 로그를 보내고 컴퓨팅 리소스를 다시 Azure로 해제합니다.        |
|process_info.json      |   노드에서 실행 중인 프로세스를 나타냅니다.  |
|process_status.json      | 프로세스 상태를 프로세스가 시작되지 않음, 실행 중, 완료됨 등으로 표시합니다.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` 폴더

|파일  |Description  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   작업 준비에 대한 시스템 로그입니다.        |
|job_release_azureml.log     | 작업 릴리스에 대한 시스템 로그입니다.        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` 폴더

사이드카를 사용하도록 설정하면 사이드카 컨테이너 내에서 작업 준비 및 작업 릴리스 스크립트가 실행됩니다.  노드마다 하나의 폴더가 있습니다. 

|파일  |Description  |
|---------|---------|
|start_cms.txt     |  사이드카 컨테이너가 시작되는 경우 시작된 프로세스의 로그입니다.       |
|prep_cmd.txt      |   `job_prep.py`가 실행될 때 입력된 ContextManagers에 대한 로그입니다(이 콘텐츠 중 일부는 `azureml-logs/65-job_prep`로 스트림됨).       |
|release_cmd.txt     |  `job_release.py`가 실행될 때 종료된 ComtextManagers에 대한 로그입니다.        |

#### <a name="other-folders"></a>기타 폴더

다중 컴퓨팅 클러스터에서 학습하는 작업의 경우 노드 IP마다 로그가 있습니다. 각 노드의 구조는 단일 노드 작업과 동일합니다. 전체 실행, stderr, stdout 로그에 대한 로그 폴더가 하나 더 있습니다.

Azure Machine Learning은 학습 작업을 실행하는 AutoML이나 Docker 컨테이너와 같이 학습 중에 다양한 원본의 정보를 기록합니다. 해당 로그는 대부분 문서화되지 않습니다. 문제가 발생하여 Microsoft 지원에 문의하는 경우 이러한 로그를 사용하여 문제를 해결할 수 있습니다.


## <a name="interactive-logging-session"></a>대화형 로깅 세션

대화형 로깅 세션은 일반적으로 Notebook 환경에서 사용됩니다. [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) 메서드는 대화형 로깅 세션을 시작합니다. 세션 중에 기록된 모든 메트릭은 실험의 실행 기록에 추가됩니다. [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) 메서드는 세션을 종료하고 실행을 완료됨으로 표시합니다.

## <a name="scriptrun-logs"></a>ScriptRun 로그

이 섹션에서는 ScriptRunConfig로 구성할 때 생성된 실행 내부에 로깅 코드를 추가하는 방법을 알아봅니다. [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig) 클래스를 사용하여 반복 가능한 실행에 대한 스크립트 및 환경을 캡슐화할 수 있습니다. 또한 이 옵션을 사용하여 모니터링을 위한 시각적 Jupyter Notebook 위젯을 표시할 수 있습니다.

다음 예제에서는 [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----) 메서드를 사용하여 알파 값에 대한 매개 변수 스윕을 수행하고 결과를 캡처합니다.

1. 로깅 논리가 포함된 학습 스크립트(`train.py`)를 만듭니다.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. 사용자 관리 환경에서 실행되도록 ```train.py``` 스크립트를 제출합니다. 학습을 위해 전체 스크립트 폴더가 제출됩니다.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    `show_output` 매개 변수는 자세한 정보 로깅을 설정합니다. 그러면 학습 프로세스의 세부 정보와 원격 리소스 또는 컴퓨팅 대상에 대한 정보를 확인할 수 있습니다. 실험을 제출할 때 자세한 정보 로깅을 설정하려면 다음 코드를 사용합니다.

```python
run = exp.submit(src, show_output=True)
```

결과 실행의 `wait_for_completion` 함수에서도 동일한 매개 변수를 사용할 수 있습니다.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>네이티브 Python 로깅

SDK의 일부 로그에는 로깅 수준을 DEBUG로 설정하도록 지시하는 오류가 포함될 수 있습니다. 로깅 수준을 설정하려면 스크립트에 다음 코드를 추가합니다.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="other-logging-sources"></a>기타 로깅 원본

Azure Machine Learning은 학습 중에 자동화된 Machine Learning 실행 또는 작업을 실행하는 Docker 컨테이너와 같은 다른 원본의 정보를 기록할 수도 있습니다. 이러한 로그는 문서화되어 있지 않지만 문제가 발생하여 Microsoft 지원에 문의하는 경우 문제 해결 중에 이러한 로그를 사용할 수 있습니다.

Azure Machine Learning 디자이너의 메트릭 로깅에 대한 자세한 내용은 [디자이너에서 메트릭을 기록하는 방법](how-to-track-designer-experiments.md)을 참조하세요.

## <a name="example-notebooks"></a>노트북 예제

이 문서의 개념을 보여 주는 노트북은 다음과 같습니다.
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

Azure Machine Learning을 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* 최상의 모델을 등록하고 배포하는 방법에 대한 예제는 [Azure Machine Learning으로 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.
