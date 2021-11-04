---
title: 파이프라인 로그 파일 모니터링 및 수집
titleSuffix: Azure Machine Learning
description: 학습 및 일괄 처리 채점 파이프라인에 로깅을 추가하고 Application Insights에서 기록된 결과를 확인합니다.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: mlops
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 682bfc9e5707ec8b6d3993616f354a591fc835f3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562393"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>경고 및 디버깅을 위해 Application Insights에서 기계 학습 파이프라인 로그 파일 수집


[OpenCensus](https://opencensus.io/quickstart/python/) python 라이브러리를 사용하여 로그를 스크립트에서 Application Insights로 라우팅할 수 있습니다. 파이프라인 실행 로그를 한 곳에서 집계하여 쿼리를 빌드하고 문제를 진단할 수 있습니다. Application Insights를 사용하면 시간 경과에 따른 로그를 추적하고 실행 간에 파이프라인 로그를 비교할 수 있습니다.

로그를 한 곳에 배치하면 예외 및 오류 메시지에 대한 기록이 제공됩니다. Application Insights는 Azure Alerts와 통합되기 때문에 Application Insights 쿼리를 기반으로 경고를 만들 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 단계에 따라 [Azure Machine Learning](./how-to-manage-workspace.md) 작업 영역을 만들고 [첫 번째 파이프라인을 만듭니다](./how-to-create-machine-learning-pipelines.md).
* [개발 환경을 구성](./how-to-configure-environment.md)하여 Azure Machine Learning SDK를 설치합니다.
* [OpenCensus Azure Monitor Exporter](https://pypi.org/project/opencensus-ext-azure/) 패키지를 로컬로 설치합니다.
  ```python
  pip install opencensus-ext-azure
  ```
* [Application Insights 인스턴스](../azure-monitor/app/opencensus-python.md)를 만듭니다(이 문서에는 리소스에 대한 연결 문자열을 가져오는 방법에 대한 정보도 수록되어 있음).

## <a name="getting-started"></a>시작하기

이 섹션에서는 Azure Machine Learning 파이프라인에서 OpenCensus를 사용하는 방법에 대해 소개합니다. 자세한 자습서는 [OpenCensus Azure Monitor Exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)를 참조하세요.

Azure ML 파이프라인에 PythonScriptStep을 추가합니다. opencensus-ext-azure에 대한 종속성을 사용하여 [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration)을 구성합니다. `APPLICATIONINSIGHTS_CONNECTION_STRING` 환경 변수를 구현합니다.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

`sample_step.py`라는 파일을 만듭니다. AzureLogHandler 클래스를 가져와서 로그를 Application Insights로 라우팅합니다. Python 로깅 라이브러리도 가져와야 합니다.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

그런 다음 AzureLogHandler를 python 로거에 추가합니다.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>사용자 지정 차원을 사용하여 로그
 
기본적으로 Application Insights에 전달된 로그에는 실행 또는 실험을 역추적하는 데 충분한 컨텍스트가 없습니다. 문제 진단에 로그를 사용하려면 추가 필드가 필요합니다. 

필드를 추가하기 위해 로그 메시지에 컨텍스트를 제공하도록 사용자 지정 차원을 추가할 수 있습니다. 한 가지 예는 누군가가 동일한 파이프라인 실행의 여러 단계에 대한 로그를 보려는 경우입니다.

사용자 지정 차원은 키-값(문자열, 문자열로 저장됨) 쌍으로 이루어진 사전을 구성합니다. 그러면 사전이 Application Insights로 전송되고 쿼리 결과에서 열로 표시됩니다. 개별 차원은 [쿼리 매개 변수](#additional-helpful-queries)로 사용할 수 있습니다.

### <a name="helpful-context-to-include"></a>포함할 유용한 컨텍스트

| 필드                          | 추론/예제                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 개별 단계를 살펴볼 필요 없이 모든 단계에서 시간이 지남에 따라 로그를 볼 수 있도록 동일한 parent_run_id가 있는 로그를 쿼리할 수 있습니다.                                        |
| step_id                        | 동일한 step_id를 통해 로그를 쿼리하여 좁은 개별 단계 범위에서만 문제가 발생한 위치를 확인할 수 있습니다.                                                        |
| step_name                      | 시간 경과에 따른 단계별 성능을 볼 수 있도록 로그를 쿼리할 수 있습니다. 포털 UI를 살펴볼 필요 없이 최근 실행에 대한 step_id를 찾을 수 있습니다.                                          |
| experiment_name                | 시간 경과에 따른 실험 성능을 볼 수 있도록 로그를 쿼리할 수 있습니다. 또한 포털 UI를 살펴볼 필요 없이 최근 실행에 대한 parent_run_id 또는 step_id를 찾을 수 있습니다.                   |
| run_url                 | 조사를 위해 실행에 바로 다시 링크를 제공할 수 있습니다. |

**기타 유용한 필드**

필드는 추가 코드 계측을 필요로 할 수 있으며 실행 컨텍스트에서 제공되지 않습니다.

| 필드                   | 추론/예제                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | CI/CD를 사용하여 배포하는 경우 이 필드는 단계 및 파이프라인 논리를 제공하는 코드 버전과 로그의 상관관계를 지정할 수 있습니다. 이 링크를 통해 문제를 진단하거나 특정 특성(로그/메트릭 값)을 사용하여 모델을 식별할 수 있습니다. |
| run_type                       | 서로 다른 모델 유형 또는 학습 실행과 채점 실행을 구분할 수 있습니다. |

### <a name="creating-a-custom-dimensions-dictionary"></a>사용자 지정 차원 사전 만들기

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python 로깅 고려 사항

OpenCensus AzureLogHandler는 Python 로그를 Application Insights로 라우팅하는 데 사용됩니다. 따라서 Python 로깅의 의미를 고려해야 합니다. 로거를 만들면 기본 로그 수준이 제공되며 해당 수준보다 크거나 같은 로그가 표시됩니다. Python 로깅 기능 사용에 대한 좋은 참조 자료는 [Logging Cookbook](https://docs.python.org/3/howto/logging-cookbook.html)입니다.

OpenCensus 라이브러리에는 `APPLICATIONINSIGHTS_CONNECTION_STRING` 환경 변수가 필요합니다. 이 환경 변수를 파이프라인 매개 변수로 전달하는 대신 일반 텍스트 연결 문자열이 전달되지 않도록 설정하는 것이 좋습니다.

## <a name="querying-logs-in-application-insights"></a>Application Insights에서 로그 쿼리

Application Insights로 라우팅되는 로그는 ‘추적’ 또는 ‘예외’ 아래에 표시됩니다. 파이프라인 실행을 포함하도록 기간을 조정해야 합니다.

![Application Insights 쿼리 결과](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Application Insights 결과에는 로그 메시지 및 수준, 파일 경로, 코드 줄 번호가 표시됩니다. 또한 포함된 사용자 지정 차원이 표시됩니다. 이 이미지에서 customDimensions 사전은 이전 [코드 샘플](#creating-a-custom-dimensions-dictionary)의 키/값 쌍을 보여 줍니다.

### <a name="additional-helpful-queries"></a>유용한 추가 쿼리

아래 쿼리에서는 ‘customDimensions.Level’을 사용합니다. 심각도 수준은 Python 로그가 원래 전송된 수준에 해당합니다. 추가 쿼리 정보는 [Azure Monitor 로그 쿼리](/azure/data-explorer/kusto/query/)를 참조하세요.

| 사용 사례                                                               | 쿼리                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 특정 사용자 지정 차원에 대한 로그 결과(예: ‘parent_run_id’) | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 지난 7일 동안 실행된 모든 학습에 대한 로그 결과                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 지난 7일 동안 발생한 severityLevel 오류에 대한 로그 결과              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 지난 7일 동안 발생한 severityLevel 오류에 대한 로그 결과 개수     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>다음 단계

Application Insights 인스턴스에 로그가 있으면 쿼리 결과에 따라 [Azure Monitor 경고](../azure-monitor/alerts/alerts-overview.md#what-you-can-alert-on)를 설정하는 데 이를 사용할 수 있습니다.

추가 정보를 얻기 위해 쿼리 결과를 [Azure 대시보드](../azure-monitor/app/tutorial-app-dashboards.md#add-logs-query)에 추가할 수도 있습니다.