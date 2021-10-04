---
title: 데이터 세트 버전 관리
titleSuffix: Azure Machine Learning
description: 기계 학습 데이터 세트의 버전을 지정하는 방법과 기계 학습 파이프라인에서 버전 관리가 작동하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: cgronlun
author: cjgronlund
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 5711d58d8772faf724c8ea82e72356e37af7f12c
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428030"
---
# <a name="version-and-track-azure-machine-learning-datasets"></a>Azure Machine Learning 데이터 세트 버전 지정 및 추적

이 문서에서는 재현성을 위해 Azure Machine Learning 데이터 세트의 버전을 지정하고 추적하는 방법을 알아봅니다. 데이터세트 버전 관리는 향후 실험을 위해 특정 버전의 데이터 세트를 적용할 수 있도록 데이터 상태에 책갈피를 지정하는 방법입니다.

일반적인 버전 관리 시나리오는 다음과 같습니다.

* 새 데이터를 재학습에 사용할 수 있는 경우
* 다른 데이터 준비 또는 기능 엔지니어링 방식을 적용하는 경우

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 다음이 필요합니다.

- [Python용 Azure Machine Learning SDK 설치](/python/api/overview/azure/ml/install). 이 SDK는 [azureml-datasets](/python/api/azureml-core/azureml.core.dataset) 패키지를 포함합니다.
    
- [Azure Machine Learning 작업 영역](concept-workspace.md). 다음 코드를 실행하여 기존 작업 영역을 검색하거나 [새 작업 영역을 만듭니다](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md)

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>데이터 세트 버전 등록 및 검색

데이터 세트를 등록하면 실험 전체에서 버전을 지정하고 재사용하고 동료와 공유할 수 있습니다. 동일한 이름으로 여러 데이터 세트를 등록하고 이름 및 버전 번호로 특정 버전을 검색할 수 있습니다.

### <a name="register-a-dataset-version"></a>데이터 세트 버전 등록

다음 코드는 `create_new_version` 매개변수를 `True`로 설정하여 `titanic_ds` 데이터 세트의 새 버전을 등록합니다. 작업 영역에 등록된 기존 `titanic_ds` 데이터 세트가 없는 경우 코드는 이름이 `titanic_ds`인 새 데이터 세트를 만들고 해당 버전을 1로 설정합니다.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>이름으로 데이터 세트 검색

기본적으로 `Dataset` 클래스의 [get_by_name()](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) 메서드는 작업 영역에 등록된 데이터 세트의 최신 버전을 반환합니다. 

다음 코드는 `titanic_ds` 데이터 세트의 버전 1을 가져옵니다.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>버전 관리 모범 사례

데이터 세트 버전을 생성하는 경우 작업 영역을 사용하여 데이터의 추가 복사본을 생성하는 것이 아닙니다. 데이터 세트는 스토리지 서비스의 데이터에 대한 참조이므로 단일 원본이 있어서 스토리지 서비스에서 관리됩니다.

>[!IMPORTANT]
> 데이터 세트에서 참조하는 데이터를 덮어쓰거나 삭제한 경우 데이터 세트의 특정 버전을 호출해도 변경 사항이 되돌려지지 않습니다.

데이터 세트에서 데이터를 로드하면 데이터 세트에서 참조하는 현재 데이터 콘텐츠가 항상 로드됩니다. 각 데이터 세트 버전을 재현할 수 있도록 하려면 데이터 세트 버전에서 참조하는 데이터 콘텐츠를 수정하지 않는 것이 좋습니다. 새 데이터가 들어오면 새 데이터 파일을 별도의 데이터 폴더에 저장한 다음, 새 폴더의 데이터를 포함할 새 데이터 세트 버전을 만듭니다.

다음 이미지 및 샘플 코드는 데이터 폴더를 구조화하고 이 폴더를 참조하는 데이터 세트 버전을 만드는 데 권장되는 방법을 보여줍니다.

![폴더 구조](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-an-ml-pipeline-output-dataset"></a>ML 파이프라인 출력 데이터 세트 버전 지정

데이터 세트를 각 [ML 파이프라인](concept-ml-pipelines.md) 단계의 입력 및 출력으로 사용할 수 있습니다. 파이프라인을 다시 실행하면 각 파이프라인 단계의 출력이 새 데이터 세트 버전으로 등록됩니다.

ML 파이프라인은 파이프라인이 다시 실행될 때마다 각 단계의 출력을 새 폴더에 채웁니다. 이러한 동작으로 인해 버전이 지정된 출력 데이터 세트를 재현할 수 있습니다. [파이프라인의 데이터 세트](./how-to-create-machine-learning-pipelines.md#steps)에 대해 자세히 알아보세요.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>실험에서 데이터 추적

Azure Machine Learning은 실험 전반에서 데이터를 입력 및 출력 데이터 세트로 추적합니다.  

다음은 데이터가 **입력 데이터 세트** 로 추적되는 시나리오입니다. 

* 실험 실행을 제출할 때 `ScriptRunConfig` 개체의 `inputs` 또는 `arguments` 매개 변수를 통해 `DatasetConsumptionConfig` 개체로 

* get_by_name() 또는 get_by_id()와 같은 메서드가 스크립트에서 호출되는 경우. 이 시나리오의 경우 데이터 세트를 작업 영역에 등록할 때 데이터 세트에 할당된 이름이 표시되는 이름입니다. 

다음은 데이터가 **출력 데이터 세트** 로 추적되는 시나리오입니다.  

* 실험 실행을 제출할 때 `outputs` 또는 `arguments` 매개 변수를 통해 `OutputFileDatasetConfig` 개체를 전달합니다. `OutputFileDatasetConfig` 개체를 사용하여 파이프라인 단계 간에 데이터를 유지할 수도 있습니다. [ML 파이프라인 단계 간에 데이터 이동](how-to-move-data-in-out-of-pipelines.md)을 참조하세요.
  
* 스크립트에 데이터 세트를 등록합니다. 이 시나리오의 경우 데이터 세트를 작업 영역에 등록할 때 데이터 세트에 할당된 이름이 표시되는 이름입니다. 다음 예제에서 `training_ds`가 표시될 이름입니다.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* 스크립트에서 등록되지 않은 데이터 세트를 사용하여 자식 실행을 제출합니다. 그러면 익명으로 저장된 데이터 세트가 생성됩니다.

### <a name="trace-datasets-in-experiment-runs"></a>실험 실행의 데이터 세트 추적

각 Machine Learning 실험에 대해 실험 `Run` 개체를 사용하여 입력으로 사용된 데이터 세트를 쉽게 추적할 수 있습니다.

다음 코드는 [`get_details()`](/python/api/azureml-core/azureml.core.run.run#get-details--) 메서드를 사용하여 어떤 입력 데이터 세트가 실험 실행에 사용되었는지 추적합니다.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

[Azure Machine Learning 스튜디오]()를 사용하여 실험에서 `input_datasets`를 찾을 수도 있습니다. 

다음 이미지는 Azure Machine Learning 스튜디오에서 실험의 입력 데이터 세트를 찾을 수 있는 위치를 보여줍니다. 이 예에서는 **실험** 창으로 이동하여 실험의 특정 실행 `keras-mnist`에 대한 **속성** 탭을 엽니다.

![입력 데이터 세트](./media/how-to-version-track-datasets/input-datasets.png)

다음 코드를 사용하여 데이터 세트에 모델을 등록합니다.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

등록 후에는 Python을 사용하여 데이터 세트에 등록된 모델 목록을 살펴보거나 [스튜디오](https://ml.azure.com/)로 이동할 수 있습니다.

다음 보기는 **자산** 아래 **데이터 세트** 창에서 가져온 것입니다. 데이터 세트를 선택한 다음, **모델** 탭을 선택하면 데이터 세트에 등록된 모델 목록이 표시됩니다. 

![입력 데이터 세트 모델](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>다음 단계

* [데이터 세트로 학습](how-to-train-with-datasets.md)
* [더 많은 샘플 데이터 세트 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)