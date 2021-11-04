---
title: ML 파이프라인에서 자동화된 ML 사용
titleSuffix: Azure Machine Learning
description: AutoMLStep을 사용하면 파이프라인에서 자동화된 Machine Learning을 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, automl
ms.openlocfilehash: a3b1adb3a60b40b027b844230d7c6fca64d8cbe4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553307"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Python의 Azure Machine Learning 파이프라인에서 자동화된 ML 사용


Azure Machine Learning의 자동화된 ML 기능을 사용하면 가능한 모든 방법을 다시 구현하지 않고도 고성능 모델을 검색할 수 있습니다. Azure Machine Learning 파이프라인과 결합하여 데이터에 가장 적합한 알고리즘을 빠르게 검색할 수 있는 배포 가능한 워크플로를 만들 수 있습니다. 이 문서에서는 데이터 준비 단계를 자동화된 ML 단계에 효율적으로 조인하는 방법을 보여 줍니다. 자동화된 ML은 데이터에 가장 적합한 알고리즘을 빠르게 검색하는 동시에 파이프라인을 통해 MLOps 및 수명 주기 운영화로 이동할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.  

* Azure의 [자동화된 Machine Learning](concept-automated-ml.md) 및 [기계 학습 파이프라인](concept-ml-pipelines.md) 기능 및 SDK에 대한 지식

## <a name="review-automated-mls-central-classes"></a>자동화된 ML의 중앙 클래스 검토

파이프라인의 자동화된 ML은 `AutoMLStep` 개체로 표현됩니다. `AutoMLStep` 클래스는 `PipelineStep`의 서브클래스입니다. `PipelineStep` 개체의 그래프는 `Pipeline`을 정의합니다.

여기에는 `PipelineStep`의 여러 하위 클래스가 있습니다. 이 문서에서는 `AutoMLStep` 외에도 데이터 준비를 위한 `PythonScriptStep` 및 모델 등록을 위한 다른 단계를 보여 줍니다.

처음에 데이터를 ML 파이프라인 _으로_ 이동하는 기본 방법은 `Dataset` 개체를 사용하는 것입니다. 단계 _간_ 에 데이터를 이동하고 실행에서 데이터 출력을 저장할 수 있도록 하려면 [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) 및 [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig) 개체를 사용하는 것이 좋습니다. `AutoMLStep`에서 사용하려면 `PipelineData` 개체를 `PipelineOutputTabularDataset`으로 변환해야 합니다. 자세한 내용은 [ML 파이프라인의 입력 및 출력 데이터](how-to-move-data-in-out-of-pipelines.md)를 참조하세요.

`AutoMLStep`은 `AutoMLConfig` 개체를 통해 구성됩니다. `AutoMLConfig`는 [Python에서 자동화된 ML 실험 구성](./how-to-configure-auto-train.md#configure-your-experiment-settings)에서 설명한 대로 유연한 클래스입니다. 

`Pipeline`은 `Experiment`에서 실행됩니다. `Run` 파이프라인에는 각 단계에 대한 자식(`StepRun`)이 있습니다. `StepRun` 자동화된 ML의 출력은 학습 메트릭 및 최고 성능의 모델입니다.

구체적으로 설명하기 위해 이 문서에서는 분류 작업에 대한 간단한 파이프라인을 만듭니다. 이 작업은 Titanic의 생존을 예측하는 것이지만, 여기서는 데이터 또는 작업이 아니라 전달에 대해서만 설명합니다.

## <a name="get-started"></a>시작

### <a name="retrieve-initial-dataset"></a>초기 데이터 세트 검색

ML 워크플로는 기존의 기준 데이터로 시작하는 경우가 많습니다. 이는 등록된 데이터 세트에 적합한 시나리오입니다. 데이터 세트는 작업 영역 전체에 표시되고, 버전 관리를 지원하며, 대화형으로 검색할 수 있습니다. [Azure Machine Learning 데이터 세트 만들기](how-to-create-register-datasets.md)에서 설명한 대로 데이터 세트를 만들고 채우는 여러 가지 방법이 있습니다. Python SDK를 사용하여 파이프라인을 만들므로 SDK를 사용하여 기준 데이터를 다운로드하고 'titanic_ds'라는 이름으로 등록합니다.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

코드에서는 먼저 **config.json** 에 정의된 Azure Machine Learning 작업 영역에 로그인합니다(설명은 [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace) 참조). `'titanic_ds'`라는 데이터 세트가 아직 등록되지 않은 경우 새로 만듭니다. 이 코드는 웹에서 CSV 데이터를 다운로드하고, 이를 사용하여 `TabularDataset`를 인스턴스화한 다음, 데이터 세트를 작업 영역에 등록합니다. 마지막으로 `Dataset.get_by_name()` 함수에서 `Dataset`를 `titanic_ds`에 할당합니다. 

### <a name="configure-your-storage-and-compute-target"></a>스토리지 및 컴퓨팅 대상 구성

파이프라인에 필요한 추가 리소스는 스토리지 및 일반적으로 Azure Machine Learning 컴퓨팅 리소스입니다. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

데이터 준비와 자동화된 ML 단계 사이의 중간 데이터는 작업 영역의 기본 데이터 저장소에 저장할 수 있으므로 `Workspace` 개체에서 `get_default_datastore()`를 호출하는 것 이외의 추가 작업을 수행할 필요가 없습니다. 

그런 다음, 코드는 AML 컴퓨팅 대상인 `'cpu-cluster'`가 이미 있는지 확인합니다. 그렇지 않은 경우 작은 CPU 기반 컴퓨팅 대상이 필요하다고 지정합니다. 자동화된 ML의 딥 러닝 기능(예: DNN 지원을 통한 텍스트 기능화)을 사용하려는 경우 [GPU 최적화 가상 머신 크기](../virtual-machines/sizes-gpu.md)에서 설명한 대로 강력한 GPU를 지원하는 컴퓨팅을 선택해야 합니다. 

이 코드는 대상이 프로비전될 때까지 차단한 다음, 방금 만든 컴퓨팅 대상에 대한 일부 세부 정보를 출력합니다. 마지막으로, 지정된 컴퓨팅 대상이 작업 영역에서 검색되고 `compute_target`에 할당됩니다. 

### <a name="configure-the-training-run"></a>학습 실행 구성

AutoMLStep은 작업을 제출하는 동안 해당 종속성을 자동으로 구성합니다. 런타임 컨텍스트는 `RunConfiguration` 개체를 만들고 구성하여 설정합니다. 여기서는 컴퓨팅 대상을 설정합니다.

```python
from azureml.core.runconfig import RunConfiguration

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
```

## <a name="prepare-data-for-automated-machine-learning"></a>자동화된 Machine Learning을 위한 데이터 준비

### <a name="write-the-data-preparation-code"></a>데이터 준비 코드 작성

기준 Titanic 데이터 세트는 일부 값이 누락된 숫자와 텍스트가 혼합된 데이터로 구성됩니다. 자동화된 Machine Learning을 준비하기 위해 데이터 준비 파이프라인 단계에서 다음을 수행합니다.

- 누락된 데이터를 임의 데이터 또는 "알 수 없음"에 해당하는 범주로 채우기
- 범주 데이터를 정수로 변환
- 사용하지 않으려는 열 삭제
- 데이터를 학습 세트 및 테스트 세트로 분할
- 변환된 데이터를 `OutputFileDatasetConfig` 출력 경로에 쓰기

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

df.to_csv(os.path.join(args.output_path,"prepped_data.csv"))

print(f"Wrote prepped data to {args.output_path}/prepped_data.csv")
```

위의 코드 조각은 Titanic 데이터에 대한 데이터 준비의 완전하지만 최소한의 예제입니다. 이 코드 조각에서는 코드를 파일에 출력하는 Jupyter "매직 명령"으로 시작합니다. Jupyter Notebook을 사용하지 않는 경우 해당 줄을 제거하고 수동으로 파일을 만듭니다.

위 코드 조각의 여러 `prepare_` 함수는 입력 데이터 세트의 관련 열을 수정합니다. 이러한 함수는 Pandas `DataFrame` 개체로 변경된 데이터에 대해 작동합니다. 각각의 경우에서 누락된 데이터는 대표적인 임의 데이터 또는 "알 수 없음"을 나타내는 범주 데이터로 채워집니다. 텍스트 기반 범주 데이터는 정수에 매핑됩니다. 더 이상 필요하지 않은 열은 덮어쓰이거나 삭제됩니다. 

코드에서 데이터 준비 함수가 정의되면 코드는 데이터를 쓰려는 경로인 입력 인수를 구문 분석합니다.  (이러한 값은 다음 단계에서 설명하는 `OutputFileDatasetConfig` 개체에서 결정됩니다.) 코드에서 등록된 `'titanic_cs'` `Dataset`를 검색하고, 이를 Pandas `DataFrame`으로 변환하고, 여러 데이터 준비 함수를 호출합니다. 

`output_path`는 디렉터리이므로 `to_csv()`에 대한 호출에서 `prepped_data.csv`라는 파일 이름을 지정합니다.

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>데이터 준비 파이프라인 단계(`PythonScriptStep`) 작성

위에서 설명한 데이터 준비 코드는 파이프라인에서 사용할 `PythonScripStep` 개체와 연결되어야 합니다. CSV 출력이 기록되는 경로는 `OutputFileDatasetConfig` 개체에서 생성됩니다. `ComputeTarget`, `RunConfig` 및 `'titanic_ds' Dataset`와 같이 앞에서 준비한 리소스는 사양을 완성하는 데 사용됩니다.

```python
from azureml.data import OutputFileDatasetConfig
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input('titanic_ds')],
    allow_reuse=True
)
```

`prepped_data_path` 개체는 디렉터리를 가리키는 `OutputFileDatasetConfig` 형식입니다.  이는 `arguments` 매개 변수에 지정됩니다. 이전 단계를 검토하면 데이터 준비 코드 내에서 `'--output_path'` 인수의 값이 CSV 파일이 작성된 디렉터리 경로임을 알 수 있습니다. 

## <a name="train-with-automlstep"></a>AutoMLStep을 사용하여 학습

자동화된 ML 파이프라인 단계 구성은 `AutoMLConfig` 클래스를 사용하여 수행됩니다. 이 유연한 클래스는 [Python에서 자동화된 ML 실험 구성](./how-to-configure-auto-train.md)에서 설명하고 있습니다. 데이터 입력 및 출력은 ML 파이프라인에서 특별한 주의가 필요한 구성의 유일한 측면입니다. 파이프라인의 `AutoMLConfig` 입력 및 출력은 아래에서 자세히 설명합니다. 데이터 외에도 ML 파이프라인의 장점은 서로 다른 컴퓨팅 대상을 각 단계에 사용할 수 있다는 것입니다. 자동화된 ML 프로세스에만 더 강력한 `ComputeTarget`을 사용하도록 선택할 수 있습니다. 이렇게 하는 것은 더 강력한 `RunConfiguration`을 `AutoMLConfig` 개체의 `run_configuration` 매개 변수에 할당하는 것만큼 간단합니다.

### <a name="send-data-to-automlstep"></a>`AutoMLStep`에 데이터 보내기

ML 파이프라인에서 입력 데이터는 `Dataset` 개체여야 합니다. 성능이 가장 높은 방법은 입력 데이터를 `OutputTabularDatasetConfig` 개체 형식으로 제공하는 것입니다. `prepped_data_path` 개체의 `prepped_data_path`와 같이 `OutputFileDatasetConfig`에서 `read_delimited_files()`를 사용하여 해당 형식의 개체를 만듭니다.

```python
# type(prepped_data) == OutputTabularDatasetConfig
prepped_data = prepped_data_path.read_delimited_files()
```

또 다른 옵션은 작업 영역에 등록된 `Dataset` 개체를 사용하는 것입니다.

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

다음 두 가지 기술을 비교합니다.

| 방법 | 이점 및 단점 | 
|-|-|
|`OutputTabularDatasetConfig`| 성능 향상 | 
|| `OutputFileDatasetConfig`의 일반 경로 | 
|| 파이프라인 실행 후 데이터가 유지되지 않음 |
||  |
| 등록된 `Dataset` | 낮은 성능 |
| | 다양한 방법으로 생성할 수 있음 | 
| | 데이터가 유지되고 작업 영역 전체에서 표시됨 |
| | [등록된 `Dataset` 기술을 보여 주는 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>자동화된 ML 출력 지정

`AutoMLStep`의 출력은 고성능 모델과 해당 모델 자체의 최종 메트릭 점수입니다. 추가 파이프라인 단계에서 이러한 출력을 사용하려면 출력을 받을 `OutputFileDatasetConfig` 개체를 준비합니다.

```python
from azureml.pipeline.core import TrainingOutput, PipelineData

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

위의 코드 조각에서는 메트릭 및 모델 출력에 대한 두 개의 `PipelineData` 개체를 만듭니다. 각각 이름이 지정되고, 이전에 검색된 기본 데이터 저장소에 할당되며, `AutoMLStep`에서 `TrainingOutput`의 특정 `type`과 연결됩니다. `pipeline_output_name`을 이러한 `PipelineData` 개체에 할당하므로 해당 값은 아래의 "파이프라인 결과 검사" 섹션에서 설명한 대로 개별 파이프라인 단계뿐만 아니라 전체 파이프라인에서도 사용할 수 있습니다. 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>자동화된 ML 파이프라인 단계 구성 및 만들기

입력 및 출력이 정의되면 `AutoMLConfig` 및 `AutoMLStep`을 만들어야 합니다. 구성에 대한 세부 정보는 [Python에서 자동화된 ML 실험 구성](./how-to-configure-auto-train.md)에서 설명한 대로 작업에 따라 달라집니다. Titanic 생존 분류 작업의 경우 다음 코드 조각에서 간단한 구성을 보여 줍니다.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
이 코드 조각에서는 `AutoMLConfig`에서 일반적으로 사용되는 관용구를 보여 줍니다. 더 유동적인 인수(하이퍼 매개 변수와 비슷함)는 별도의 사전에 지정되고, 변경 가능성이 낮은 값은 `AutoMLConfig` 생성자에서 직접 지정됩니다. 이 경우 `automl_settings`는 간단한 실행을 지정합니다. 2회 반복 후 또는 15분 후 중 먼저 수행된 실행이 중지됩니다.

`automl_settings` 사전은 kwargs로 `AutoMLConfig` 생성자에 전달됩니다. 다른 매개 변수는 복잡하지 않습니다.

- 이 예제에서는 `task`가 `classification`으로 설정됩니다. 다른 유효한 값은 `regression` 및 `forecasting`입니다.
- `path` 및 `debug_log`는 프로젝트의 경로와 디버그 정보가 기록되는 로컬 파일을 설명합니다. 
- `compute_target`은 이 예제에서 저렴한 CPU 기반 컴퓨터인 이전에 정의된 `compute_target`입니다. AutoML의 딥 러닝 기능을 사용하는 경우 컴퓨팅 대상을 GPU 기반으로 변경하는 것이 좋습니다.
- `featurization`이 `auto`로 설정됩니다. 자세한 내용은 자동화된 ML 구성 문서의 [데이터 기능화](./how-to-configure-auto-train.md#data-featurization)섹션에서 확인할 수 있습니다. 
- `label_column_name`은 예측하려는 열을 나타냅니다. 
- `training_data`는 데이터 준비 단계의 출력에서 만든 `OutputTabularDatasetConfig` 개체로 설정됩니다. 

`AutoMLStep` 자체는 `AutoMLConfig`를 사용하고, 메트릭과 모델 데이터를 저장하기 위해 만든 `PipelineData` 개체를 출력으로 포함합니다. 

>[!Important]
> `AutoMLStepRun`을 사용하는 경우에만 `enable_default_model_output` 및 `enable_default_metrics_output`을 `True`로 설정해야 합니다.

이 예제에서는 자동화된 ML 프로세스에서 `training_data`에 대한 교차 유효성 검사를 수행합니다. `n_cross_validations` 인수를 사용하여 교차 유효성 검사 수를 제어할 수 있습니다. 데이터 준비 단계의 일부로 학습 데이터를 이미 분할한 경우 `validation_data`를 자체의 `Dataset`로 설정할 수 있습니다.

데이터 기능에는 `X`를 사용하고 데이터 레이블에는 `y`를 사용하는 경우가 있습니다. 이 기술은 더 이상 사용되지 않으며, `training_data`를 입력에 사용해야 합니다. 

## <a name="register-the-model-generated-by-automated-ml"></a>자동화된 ML에서 생성된 모델 등록 

간단한 ML 파이프라인의 마지막 단계는 만든 모델을 등록하는 것입니다. 모델을 작업 영역의 모델 레지스트리에 추가하면 포털에서 모델을 사용할 수 있으며 버전을 지정할 수 있습니다. 모델을 등록하려면 `AutoMLStep`의 `model_data` 출력을 사용하는 다른 `PythonScriptStep`을 작성합니다.

### <a name="write-the-code-to-register-the-model"></a>모델을 등록하는 코드 작성

모델은 `Workspace`에 등록됩니다. `Workspace.from_config()`를 사용하여 로컬 컴퓨터의 작업 영역에 로그온하는 데 익숙할 수 있지만, 실행되는 ML 파이프라인 내에서 작업 영역을 가져오는 또 다른 방법이 있습니다. `Run.get_context()`는 활성 `Run`을 검색합니다. 이 `run` 개체는 여기서 사용되는 `Workspace`를 포함하여 많은 중요한 개체에 대한 액세스를 제공합니다.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>PythonScriptStep 코드 작성

모델 등록 `PythonScriptStep`은 `PipelineParameter`를 해당 인수 중 하나에 사용합니다. 파이프라인 매개 변수는 실행 제출 시간에 쉽게 설정할 수 있는 파이프라인에 대한 인수입니다. 선언되면 일반 인수로 전달됩니다. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>자동화된 ML 파이프라인 만들기 및 실행

`AutoMLStep`이 포함된 파이프라인을 만들고 실행하는 것은 일반 파이프라인과 다르지 않습니다. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

위의 코드에서는 데이터 준비, 자동화된 ML 및 모델 등록 단계를 `Pipeline` 개체에 결합합니다. 그런 다음, `Experiment` 개체를 만듭니다. `Experiment` 생성자는 명명된 실험을 검색하거나(있는 경우), 필요한 경우 만듭니다. `Pipeline`을 `Experiment`에 제출하여 파이프라인을 비동기적으로 실행하는 `Run` 개체를 만듭니다. `wait_for_completion()` 함수는 실행이 완료될 때까지 차단됩니다.

### <a name="examine-pipeline-results"></a>파이프라인 결과 검사 

`run`이 완료되면 `pipeline_output_name`이 할당된 `PipelineData` 개체를 검색할 수 있습니다. 추가 처리를 위해 결과를 다운로드하여 로드할 수 있습니다.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

다운로드한 파일은 `azureml/{run.id}/` 하위 디렉터리에 기록됩니다. 메트릭 파일은 JSON 형식이며, 검사를 위해 Pandas 데이터 프레임으로 변환할 수 있습니다.

로컬 처리를 위해 Panda, Pickle, AzureML SDK 등과 같은 관련 패키지를 설치해야 할 수 있습니다. 다음 예제에서는 자동화된 ML에서 찾은 최상의 모델이 XGBoost에 종속될 가능성이 높습니다.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

위의 코드 조각에서는 Azure 데이터 저장소의 해당 위치에서 로드되는 메트릭 파일을 보여 줍니다. 주석과 같이 다운로드한 파일에서 로드할 수도 있습니다. 이 파일이 역직렬화되고 Pandas DataFrame으로 변환되면 자동화된 ML 단계의 각 반복에 대한 자세한 메트릭을 확인할 수 있습니다.

모델 파일은 추론, 추가 메트릭 분석 등에 사용할 수 있는 `Model` 개체로 역직렬화할 수 있습니다. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

기존 모델을 로드하고 사용하는 방법에 대한 자세한 내용은 [ Azure Machine Learning에서 기존 모델 사용](how-to-deploy-and-where.md)을 참조하세요.

### <a name="download-the-results-of-an-automated-ml-run"></a>자동화된 ML 실행 결과 다운로드

이 문서를 따른 경우 인스턴스화된 `run` 개체가 있습니다. 그러나 `Experiment` 개체를 통해 `Workspace`에서 완료된 `Run` 개체를 검색할 수도 있습니다.

작업 영역에는 모든 실험 및 실행에 대한 전체 기록이 포함됩니다. 포털을 사용하여 실험의 출력을 찾아서 다운로드하거나 코드를 사용할 수 있습니다. 기록 실행의 레코드에 액세스하려면 Azure Machine Learning을 사용하여 관심 있는 실행의 ID를 찾습니다. 이 ID를 사용하면 `Workspace` 및 `Experiment`를 통해 특정 `run`을 선택할 수 있습니다.

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

위 코드의 문자열을 기록 실행의 세부 정보로 변경해야 합니다. 위의 코드 조각에서는 일반적인 `from_config()`를 사용하여 `ws`를 관련 `Workspace`에 할당했다고 가정합니다. 관심 있는 실험을 직접 검색한 다음, 코드에서 `run.id` 값을 일치시켜 관심 있는 `Run`을 찾습니다.

`Run` 개체가 있으면 메트릭과 모델을 다운로드할 수 있습니다. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

각 `Run` 개체에는 개별 파이프라인 단계 실행에 대한 정보가 포함된 `StepRun` 개체가 포함되어 있습니다. `run`에서 `AutoMLStep`에 대한 `StepRun` 개체를 검색합니다. 메트릭과 모델은 기본 이름을 사용하여 검색되며, `PipelineData` 개체를 `AutoMLStep`의 `outputs` 매개 변수에 전달하지 않아도 사용할 수 있습니다. 

마지막으로 위의 "파이프라인 결과 검사" 섹션에서 설명한 대로 실제 메트릭과 모델이 로컬 컴퓨터에 다운로드됩니다.

## <a name="next-steps"></a>다음 단계

- 회귀를 사용하여 택시 요금을 예측하는 [파이프라인에서 자동화된 ML의 전체 예제](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)를 보여 주는 이 Jupyter Notebook을 실행합니다.
- [코드를 작성하지 않고 자동화된 ML 실험을 만듭니다.](how-to-use-automated-ml-for-ml-models.md)
- [자동화된 ML을 보여 주는 다양한 Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)을 살펴봅니다.
- 파이프라인을 [엔드투엔드 MLOps](./concept-model-management-and-deployment.md#automate-the-ml-lifecycle)에 통합하는 방법을 참조하거나 [MLOps GitHub 리포지토리](https://github.com/Microsoft/MLOpspython)를 조사합니다.
