---
title: ML 파이프라인에서 데이터 이동
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 파이프라인에서 데이터를 수집하는 방법과 파이프라인 단계 간 데이터를 관리하고 이동하는 방법에 관해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: how-to
ms.custom: contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 86bdbd1588c14ad03cca6544e341599a446c35e9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767264"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>ML 파이프라인 단계로/단계 간에 데이터 이동(Python)

이 문서에서는 Azure Machine Learning 파이프라인의 단계 간 데이터를 가져오고, 변환하고, 이동하는 코드를 제공합니다. Azure Machine Learning에서 데이터의 작동 방식에 대한 개요는 [Azure Storage 서비스에서 데이터 액세스](how-to-access-data.md)를 참조하세요. Azure Machine Learning 파이프라인의 이점과 구조는 [Azure Machine Learning 파이프라인이란?](concept-ml-pipelines.md)을 참조하세요.

이 문서에서 설명하는 방법:

- 기존 데이터에 대한 `Dataset` 개체 사용
- 단계 내의 데이터 액세스
- `Dataset` 데이터를 학습 및 유효성 검사 하위 집합과 같은 하위 집합으로 분할
- 다음 파이프라인 단계로 데이터를 전송하는 `OutputFileDatasetConfig` 개체 만들기
- 파이프라인 단계에 대한 입력으로 `OutputFileDatasetConfig` 개체 사용
- 유지하려는 `OutputFileDatasetConfig`에서 새 `Dataset` 개체 만들기

## <a name="prerequisites"></a>사전 요구 사항

필요한 사항:

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

- [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) 또는 [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 대한 액세스 권한

- Azure Machine Learning 작업 영역
  
  [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만들거나 Python SDK를 통해 기존 작업 영역을 사용합니다. `Workspace` 및 `Datastore` 클래스를 가져오고, `from_config()` 함수를 사용하여 `config.json` 파일의 구독 정보를 로드합니다. 이 함수는 기본적으로 현재 디렉터리에서 JSON 파일을 찾지만 `from_config(path="your/file/path")`를 사용하여 해당 파일을 가리키도록 경로 매개 변수를 지정할 수도 있습니다.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 일부 기존 데이터. 이 문서에서는 [Azure BLOB 컨테이너](../storage/blobs/storage-blobs-overview.md) 사용에 대해 간략하게 설명합니다.

- 선택 사항: [Azure Machine Learning SDK를 사용하여 기계 학습 파이프라인 생성 및 실행](./how-to-create-machine-learning-pipelines.md)에 설명된 것과 같은 기존 기계 학습 파이프라인입니다.

## <a name="use-dataset-objects-for-pre-existing-data"></a>기존 데이터에 대한 `Dataset` 개체 사용 

파이프라인으로 데이터를 수집하는 기본 방법은 [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29) 개체를 사용하는 것입니다. `Dataset` 개체는 작업 영역 전체에서 사용할 수 있는 영구적 데이터를 나타냅니다.

`Dataset` 개체를 만들고 등록하는 방법에는 여러 가지가 있습니다. 테이블 형식 데이터 세트는 하나 이상의 파일에서 사용할 수 있는 구분된 데이터에 대한 것입니다. 파일 데이터 세트는 이미지와 같은 이진 데이터 또는 구문을 분석할 데이터에 대한 것입니다. `Dataset` 개체를 만드는 가장 간단한 프로그래밍 방식은 작업 영역 스토리지 또는 퍼블릭 URL에서 기존 BLOB을 사용하는 것입니다.

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
```

다른 옵션을 사용하여 데이터 세트를 만들거나 다른 소스에서 데이터 세트를 만들고 이를 Azure Machine Learning UI에서 등록 및 검토하고, 데이터 크기가 컴퓨팅 용량과 상호 작용하는 방식을 이해하고, 버전을 관리하는 것에 대한 더 많은 옵션은 [Azure Machine Learning 데이터 세트 만들기](how-to-create-register-datasets.md)를 참조하세요. 

### <a name="pass-datasets-to-your-script"></a>스크립트에 데이터 세트 전달

데이터 세트의 경로를 스크립트로 전달하려면 `Dataset` 개체의 `as_named_input()` 메서드를 사용합니다. 결과 `DatasetConsumptionConfig` 개체를 인수로 스크립트에 전달하거나 파이프라인 스크립트에 대한 `inputs` 인수를 사용함으로써 `Run.get_context().input_datasets[]`를 사용하여 데이터 세트를 검색할 수 있습니다.

명명된 입력을 만들면 액세스 모드 `as_mount()` 또는 `as_download()`를 선택할 수 있습니다. 스크립트가 데이터 세트의 모든 파일을 처리하고 컴퓨팅 리소스의 디스크가 데이터 세트에 충분한 크기일 경우 다운로드 액세스 모드를 선택하는 것이 좋습니다. 다운로드 액세스 모드는 런타임에 데이터를 스트리밍하는 오버헤드를 방지합니다. 스크립트가 데이터 세트의 하위 집합에 액세스하거나 크기가 너무 커서 컴퓨팅에 사용할 수 없는 경우에는 탑재 액세스 모드를 사용합니다. 자세한 내용은 [탑재 대 다운로드](./how-to-train-with-datasets.md#mount-vs-download)를 참조하세요.

파이프라인 단계에 데이터 세트를 전달하려면:

1. `TabularDataset.as_named_input()` 또는 `FileDataset.as_named_input()`(끝에 ‘s’ 없음)을 사용하여 `DatasetConsumptionConfig` 개체 만들기
1. `as_mount()` 또는 `as_download()`를 사용하여 액세스 모드 설정
1. `arguments` 또는 `inputs` 인수를 사용하여 파이프라인 단계로 데이터 세트 전달

다음 코드 조각은 `PythonScriptStep` 생성자 내에서 이러한 단계를 결합하는 일반적인 패턴을 보여 줍니다. 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> 이러한 모든 인수(즉, `"train_data"`, `"train.py"`, `cluster`, `iris_dataset`)에 대한 값을 고유한 데이터로 바꿔야 합니다. 위의 코드 조각은 호출 형식만 보여 주며 Microsoft 샘플의 일부가 아닙니다. 

`random_split()` 및 `take_sample()`과 같은 메서드를 사용하여 여러 입력을 만들거나 파이프라인 단계로 전달되는 데이터양을 줄일 수도 있습니다.

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>스크립트 내에서 데이터 세트에 액세스

파이프라인 단계 스크립트의 명명된 입력은 `Run` 개체 내에서 사전으로 사용할 수 있습니다. `Run.get_context()`를 사용하여 활성 `Run` 개체를 검색한 다음 `input_datasets`를 사용하여 명명된 입력의 사전을 검색합니다. `inputs` 인수가 아닌 `arguments` 인수를 사용하여 `DatasetConsumptionConfig` 개체를 전달한 경우 `ArgParser` 코드를 사용하여 데이터에 액세스합니다. 다음 코드 조각에서는 두 가지 방법을 모두 보여 줍니다.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()],
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

전달된 값은 데이터 세트 파일의 경로가 됩니다.

등록된 `Dataset`에 직접 액세스할 수도 있습니다. 등록된 데이터 세트는 영구적이며 작업 영역에서 공유되므로 직접 검색할 수 있습니다.

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> 이전 코드 조각은 호출의 형식을 나타내며 Microsoft 샘플의 일부가 아닙니다. 다양한 인수를 사용자 고유의 프로젝트 값으로 바꿔야 합니다.

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>중간 데이터에 `OutputFileDatasetConfig` 사용

`Dataset` 개체가 영구 데이터만 나타내지만 파이프라인 단계 **및** 영구 출력 데이터에서 임시 데이터 출력에 [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) 개체를 사용할 수 있습니다. `OutputFileDatasetConfig`에서는 Blob Storage, fileshare, adlsgen1 또는 adlsgen2에 데이터 쓰기를 지원합니다. 탑재 모드와 업로드 모드를 모두 지원합니다. 탑재 모드에서 탑재된 디렉터리에 기록된 파일은 파일을 닫을 때 영구적으로 저장됩니다. 업로드 모드에서 출력 디렉터리에 기록된 파일은 작업 종료 시 업로드됩니다. 작업이 실패하거나 취소되면 출력 디렉터리가 업로드되지 않습니다.

 `OutputFileDatasetConfig` 개체의 기본 동작은 작업 영역의 기본 데이터 저장소에 쓰는 것입니다. `arguments` 매개 변수를 사용하여 `OutputFileDatasetConfig` 개체를 `PythonScriptStep`에 전달합니다.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

> [!NOTE]
> `OutputFileDatasetConfig`에 대한 동시 쓰기가 실패하게 됩니다. 단일 `OutputFileDatasetConfig`를 동시에 사용하지 않아야 합니다. [분산 학습](how-to-train-distributed-gpu.md)을 사용하는 경우와 같이 다중 처리 상황에서는 단일 `OutputFileDatasetConfig`를 공유하지 않도록 합니다. 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>학습 단계의 출력으로 `OutputFileDatasetConfig` 사용

파이프라인의 `PythonScriptStep` 내에서 프로그램의 인수를 사용하여 사용 가능한 출력 경로를 검색할 수 있습니다. 이 단계가 첫 번째이고 출력 데이터를 초기화하는 경우 지정된 경로에 디렉터리를 만들어야 합니다. 그런 다음 `OutputFileDatasetConfig`에 포함할 파일을 쓸 수 있습니다.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>`OutputFileDatasetConfig`를 초기 단계가 아닌 단계에 대한 입력으로 읽기

초기 파이프라인 단계에서 일부 데이터를 `OutputFileDatasetConfig` 경로에 쓰고 초기 단계의 출력이 되면 이후 단계에 대한 입력으로 사용할 수 있습니다. 

다음 코드에서: 

* `step1_output_data`는 PythonScriptStep, `step1`의 출력이 업로드 액세스 모드에서 ADLS Gen 2 데이터스토어 `my_adlsgen2`에 기록되었음을 나타냅니다. ADLS Gen 2 데이터 저장소에 데이터를 다시 쓰기 위해 [역할 권한 설정](how-to-access-data.md#azure-data-lake-storage-generation-2)을 하는 방법에 관해 자세히 알아보세요. 

* `step1`이 완료되고 출력이 `step1_output_data`에 표시된 대상에 기록되면 2단계에서 `step1_output_data`를 입력으로 사용할 수 있습니다. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>다시 사용할 `OutputFileDatasetConfig` 개체 등록

`OutputFileDatasetConfig`를 실험 기간보다 오래 사용할 수 있도록 하려면 이 개체를 작업 영역에 등록하여 실험 전반에 공유하고 다시 사용하면 됩니다.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>더 이상 필요하지 않은 `OutputFileDatasetConfig` 콘텐츠 삭제

Azure는 `OutputFileDatasetConfig`로 작성된 중간 데이터를 자동으로 삭제하지 않습니다. 대량의 불필요한 데이터에 대한 스토리지 요금을 방지하려면 다음 중 하나를 선택해야 합니다.

* 파이프라인 실행 종료 시 중간 데이터가 더 이상 필요하지 않을 때 프로그래밍 방식으로 삭제
* 중간 데이터에 대한 단기 스토리지 정책과 함께 Blob Storage 사용([Azure Blob Storage 액세스 계층을 자동화하여 비용 최적화](../storage/blobs/lifecycle-management-overview.md?tabs=azure-portal) 참조) 
* 더 이상 필요하지 않은 데이터 정기적으로 검토 및 삭제

자세한 내용은 [Azure Machine Learning 비용 플랜 및 관리](concept-plan-manage-cost.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 기계 학습 데이터 세트 만들기](how-to-create-register-datasets.md)
* [Azure Machine Learning SDK를 사용하여 기계 학습 파이프라인 만들기 및 실행](./how-to-create-machine-learning-pipelines.md)