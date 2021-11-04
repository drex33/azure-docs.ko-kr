---
title: 기계 학습 파이프라인이란?
titleSuffix: Azure Machine Learning
description: 기계 학습 파이프라인이 기계 학습 워크플로의 빌드, 최적화 및 관리에 어떻게 도움이 되는지 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 10/21/2021
ms.custom: devx-track-python
ms.openlocfilehash: 80c31cc0675be216e21010daaee32964f1969fab
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555194"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Azure Machine Learning 파이프라인이란?

이 문서에서는 기계 학습 파이프라인이 기계 학습 워크플로의 빌드, 최적화 및 관리에 어떻게 도움이 되는지 알아봅니다. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>어떤 Azure 파이프라인 기술을 사용해야 하나요?

Azure 클라우드는 각기 용도가 다른 몇 가지 형식의 파이프라인을 제공합니다. 다음 표에는 다양한 파이프라인과 그 용도가 나열되어 있습니다.

| 시나리오 | 주 가상 사용자 | Azure 제품 | OSS 제품 | Canonical 파이프 | 강점 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| 모델 오케스트레이션(기계 학습) | 데이터 과학자 | Azure Machine Learning 파이프라인 | Kubeflow 파이프라인 | 데이터 -> 모델 | 배포, 캐싱, 코드 우선, 다시 사용 | 
| 데이터 오케스트레이션(데이터 준비) | 데이터 엔지니어 | [Azure Data Factory 파이프라인](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | 데이터 -> 데이터 | 강력한 형식의 이동, 데이터 중심 활동 |
| 코드 및 앱 오케스트레이션(CI/CD) | 앱 개발자/Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | 코드 + 모델 -> 앱/서비스 | 가장 개방적이고 유연한 활동 지원, 승인 큐, 게이팅 단계 | 

## <a name="what-can-machine-learning-pipelines-do"></a>기계 학습 파이프라인은 무엇을 할 수 있나요?

Azure Machine Learning 파이프라인은 기계 학습 작업 전체에 대한 독립 실행 워크플로입니다. 하위 작업은 파이프라인 내에서 일련의 단계로 캡슐화됩니다. Azure Machine Learning 파이프라인은 Python 스크립트를 호출하는 것처럼 간단할 수 있으므로 거의 모든 작업을 수행할 수 _있습니다_. 파이프라인은 다음과 같은 기계 학습 작업에 중점을 _두어야 합니다_.

+ 데이터 준비(가져오기, 유효성 검사 및 정리, 마이닝/변환, 정규화 및 준비 포함)
+ 학습 구성(인수 매개 변수화, 파일 경로 및 구성 로깅/보고 포함)
+ 효율적 및 반복적인 학습 및 유효성 검사. 특정 데이터 하위 집합, 다른 하드웨어 컴퓨팅 리소스, 분산 처리 및 진행 상황 모니터링 지정을 통해 효율성을 확보할 수 있습니다.
+ 배포(버전 관리, 크기 조정, 프로비저닝 및 액세스 제어 포함)

독립적인 단계를 통해 여러 데이터 과학자가 과도한 컴퓨팅 리소스 사용 없이 동시에 동일한 파이프라인에서 작업할 수 있습니다. 또한 별도의 단계를 통해 각 단계에 서로 다른 컴퓨팅 형식/크기를 쉽게 사용할 수 있습니다.

파이프라인 설계 후에는 종종 파이프라인의 학습 루프를 미세 조정합니다. 파이프라인을 다시 실행하면 업데이트된 학습 스크립트와 같이 다시 실행해야 하는 단계로 이동합니다. 다시 실행할 필요가 없는 단계는 건너뜁니다. 

파이프라인을 사용하여 작업마다 다른 하드웨어를 사용하도록 선택할 수 있습니다. Azure는 사용하는 다양한 [컴퓨팅 대상](concept-azure-machine-learning-architecture.md)을 조정하므로 중간 데이터가 다운스트림 컴퓨팅 대상으로 원활하게 흐릅니다.

Azure Portal 또는 [작업 영역 방문 페이지(미리 보기)](https://ml.azure.com)에서 직접 [파이프라인 실험의 메트릭을 추적](./how-to-log-view-metrics.md)할 수 있습니다. 파이프라인이 게시된 후에는 모든 플랫폼 또는 스택에서 파이프라인을 다시 실행할 수 있는 REST 엔드포인트를 구성할 수 있습니다.

간단히 말해서, 기계 학습 수명 주기의 모든 복잡한 작업은 파이프라인을 통해 도움을 받을 수 있습니다. 다른 Azure 파이프라인 기술에는 고유의 강점이 있습니다. [Azure Data Factory 파이프라인](../data-factory/concepts-pipelines-activities.md)은 데이터 작업에 탁월하며, [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)는 연속 통합과 지속적인 배포에 적합한 도구입니다. 그러나 기계 학습에 초점을 맞추고 있다면 Azure Machine Learning 파이프라인이 워크플로 요구에 가장 적합한 선택이 될 수 있습니다. 

### <a name="analyzing-dependencies"></a>종속성 분석

많은 프로그래밍 에코시스템에는 리소스, 라이브러리 또는 컴파일 종속성을 오케스트레이션하는 도구가 있습니다. 일반적으로 이러한 도구는 파일 타임스탬프를 사용하여 종속성을 계산합니다. 파일이 변경되면 그 파일과 해당 종속 파일만 업데이트(다운로드, 다시 컴파일 또는 패키지화)됩니다. Azure Machine Learning 파이프라인은 이 개념을 확장합니다. 기존 빌드 도구와 마찬가지로 파이프라인은 단계 간의 종속성을 계산하고 필요한 재계산만 수행합니다. 

그러나 Azure Machine Learning 파이프라인에서 종속성 분석은 단순한 타임스탬프보다 더 정교합니다. 모든 단계는 서로 다른 하드웨어 및 소프트웨어 환경에서 실행될 수 있습니다. 데이터 준비는 시간이 많이 소요되는 프로세스지만, 강력한 GPU가 있는 하드웨어에서 실행할 필요는 없습니다. 특정 단계에는 OS 관련 소프트웨어가 필요할 수 있으며, [분산 학습](how-to-train-distributed-gpu.md) 등을 사용하고자 할 수 있습니다. 

Azure Machine Learning은 파이프라인 단계 간의 모든 종속성을 자동으로 오케스트레이션합니다. 이 오케스트레이션에는 Docker 이미지를 위/아래로 회전, 컴퓨팅 리소스 연결 및 분리, 일관된 자동 방식의 단계 간 데이터 이동이 포함될 수 있습니다.

### <a name="coordinating-the-steps-involved"></a>관련 단계 조정

`Pipeline` 개체를 만들고 실행할 때 다음과 같은 개략적인 단계가 발생합니다.

+ 각 단계에서 서비스는 다음에 대한 요구 사항을 계산합니다.
    + 하드웨어 컴퓨팅 리소스
    + OS 리소스(Docker 이미지)
    + 소프트웨어 리소스(Conda/virtualenv 종속성)
    + 데이터 입력 
+ 서비스는 단계 간 종속성을 확인하여 동적 실행 그래프를 생성합니다.
+ 실행 그래프의 각 노드가 실행되는 경우:
    + 서비스는 필요한 하드웨어 및 소프트웨어 환경을 구성합니다(기존 리소스를 다시 사용하는 경우).
    + 단계가 실행되어 포함된 `Experiment` 개체에 로깅 및 모니터링 정보를 제공합니다.
    + 단계가 완료되면 해당 출력이 다음 단계에 대한 입력으로 준비되고 스토리지에 기록됩니다.
    + 더 이상 필요하지 않은 리소스는 종료 및 분리됩니다.

![파이프라인 단계](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Python SDK를 사용하여 파이프라인 빌드

[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)에서 파이프라인은 `azureml.pipeline.core` 모듈에 정의된 Python 개체입니다. [파이프라인](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) 개체는 하나 이상의 [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) 개체의 순서가 지정된 시퀀스를 포함합니다. `PipelineStep` 클래스는 추상 클래스이고, 실제 단계는 [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep) 또는 [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)과 같은 서브클래스입니다. [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) 클래스는 파이프라인 간에 공유할 수 있는 재사용 가능한 단계 시퀀스를 포함합니다. `Pipeline`은 `Experiment`의 일부로 실행됩니다.

Azure Machine Learning 파이프라인은 Azure Machine Learning 작업 영역과 연결되고, 파이프라인 단계는 해당 작업 영역 내에서 사용할 수 있는 컴퓨팅 대상과 연결됩니다. 자세한 내용은 [Azure Portal에서 Azure Machine Learning 작업 영역 만들기 및 관리](./how-to-manage-workspace.md) 또는 [Azure Machine Learning에서 컴퓨팅 대상이란?](./concept-compute-target.md)을 참조하세요.

### <a name="a-simple-python-pipeline"></a>간단한 Python 파이프라인

이 코드 조각에서는`Pipeline` 만들기 및 실행에 필요한 개체 및 호출을 보여 줍니다.

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

이 코드 조각은 일반적인 Azure Machine Learning 개체, `Workspace`, `Datastore`, [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget) 및 `Experiment`로 시작됩니다. 그런 다음, 코드는 `input_data` 및 `prepped_data_path`를 저장할 개체를 만듭니다. `input_data`는 [FileDataset](/python/api/azureml-core/azureml.data.filedataset)의 인스턴스이고 `prepped_data_path`는 [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)의 인스턴스입니다. `OutputFileDatasetConfig`의 경우 기본 동작은 `/dataset/{run-id}/{output-name}` 경로 아래의 `workspaceblobstore` 데이터 저장소에 출력을 복사하는 것입니다. 여기서 `run-id`는 실행 ID이며, `output-name`은 개발자가 지정하지 않은 경우 자동 생성된 값입니다.

데이터 준비 코드(표시되지 않음)는 구분된 파일을 `prepped_data_path`에 씁니다. 데이터 준비 단계의 이러한 출력은 `prepped_data`로 학습 단계에 전달됩니다. 

배열 `steps`에는 `dataprep_step` 및 `train_step`이라는 두 개의 `PythonScriptStep`이 있습니다. Azure Machine Learning은 `prepped_data`의 데이터 종속성을 분석하고 `train_step`에 앞서 `dataprep_step`을 실행합니다. 

그런 다음, 코드가 `Pipeline` 개체 자체를 인스턴스화하여 작업 영역 및 단계 배열을 전달합니다. `experiment.submit(pipeline)`에 대한 호출은 Azure ML 파이프라인 실행을 시작합니다. `wait_for_completion()`에 대한 호출은 파이프라인이 완료될 때까지 차단됩니다. 

파이프라인을 데이터에 연결하는 방법에 대한 자세한 정보는 [Azure Machine Learning의 데이터 액세스](concept-data.md) 및 [ML 파이프라인 단계 간 데이터 이동(Python)](how-to-move-data-in-out-of-pipelines.md) 문서를 참조하세요. 

## <a name="building-pipelines-with-the-designer"></a>디자이너를 사용하여 파이프라인 빌드

시각적 디자인 화면을 선호하는 개발자는 Azure Machine Learning 디자이너를 사용하여 파이프라인을 만들 수 있습니다. 작업 영역 홈페이지의 **디자이너** 선택 영역에서 이 도구에 액세스할 수 있습니다.  디자이너를 사용하여 단계를 디자인 화면으로 끌어서 놓을 수 있습니다. 

파이프라인을 시각적으로 디자인하는 경우 단계의 입력 및 출력이 눈에 띄게 표시됩니다. 데이터 연결을 끌어서 놓을 수 있으므로 파이프라인의 데이터 흐름을 빠르게 이해하고 수정할 수 있습니다.

![Azure Machine Learning 디자이너 예](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>주요 장점

기계 학습 워크플로에서 파이프라인을 사용하여 얻을 수 있는 주요 장점은 다음과 같습니다.

|주요 장점|Description|
|:-------:|-----------|
|**무인&nbsp;실행**|안정적인 무인 방식을 통해 병렬로 또는 순차적으로 실행되도록 단계를 예약합니다. 데이터 준비 및 모델링은 며칠 또는 몇 주 동안 지속될 수 있으며 프로세스가 실행 중인 동안 파이프라인을 통해 다른 작업에 집중할 수 있습니다. |
|**이종 컴퓨팅**|확장성 있는 이종 컴퓨팅 리소스 및 스토리지 위치에서 안정적으로 조정되는 여러 파이프라인을 사용합니다. 개별 파이프라인 단계를 HDInsight, GPU Data Science VM, Databricks 등의 서로 다른 컴퓨팅 대상에서 실행하여 가용 컴퓨팅 리소스를 효율적으로 사용합니다.|
|**재사용 가능**|다시 학습, 일괄 처리 채점 등의 특정 시나리오에 맞게 파이프라인 템플릿을 만듭니다. 간단한 REST 호출을 통해 외부 시스템에서 게시된 파이프라인을 트리거합니다.|
|**추적 및 버전 관리**|반복하면서 데이터 및 결과 경로를 수동으로 추적하는 대신, Pipelines SDK를 사용하여 데이터 원본, 입력 및 출력의 이름과 버전을 명시적으로 지정합니다. 생산성 향상을 위해 스크립트와 데이터를 별도로 관리할 수도 있습니다.|
| **모듈화** | 관심 영역을 분리하고 변경 내용을 격리하면 소프트웨어가 더 빠른 속도와 더 높은 품질로 발전할 수 있습니다. | 
|**협업**|파이프라인을 사용하면 데이터 과학자들이 기계 학습 디자인 프로세스의 모든 영역에서 협업하는 동시에 파이프라인 단계에서 동시에 작업할 수 있습니다.|

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 파이프라인은 초기 개발 단계부터 가치를 제공하는 강력한 기능을 갖추고 있습니다. 팀과 프로젝트가 커질수록 그 가치가 커집니다. 이 문서에서는 Azure에서 Azure Machine Learning Python SDK 및 오케스트레이션을 사용하여 파이프라인을 지정하는 방법에 대해 설명했습니다. 몇 가지 간단한 소스 코드를 살펴보았고 사용할 수 있는 몇 가지 `PipelineStep` 클래스를 소개했습니다. Azure Machine Learning 파이프라인을 사용하는 시기와 Azure에서 실행하는 방법을 파악했을 것입니다. 

+ [첫 번째 파이프라인을 만드는](./how-to-create-machine-learning-pipelines.md) 방법을 알아봅니다.

+ [대용량 데이터에서 일괄 처리 예측을 실행](tutorial-pipeline-batch-scoring-classification.md )하는 방법을 알아봅니다.

+ [파이프라인 코어](/python/api/azureml-pipeline-core/) 및 [파이프라인 단계](/python/api/azureml-pipeline-steps/)는 SDK 참조 문서를 참조하세요.

+ [Azure Machine Learning 파이프라인](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)을 보여주는 예제 Jupyter Notebook을 사용해 봅니다. [Notebook을 사용하여 이 서비스를 검색](samples-notebooks.md)하는 방법을 알아봅니다.