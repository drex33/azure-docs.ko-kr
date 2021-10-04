---
title: Azure Machine Learning 데이터 세트 만들기
titleSuffix: Azure Machine Learning
description: 기계 학습 실험 실행용으로 데이터에 액세스하기 위해 Azure Machine Learning 데이터 세트를 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.custom: contperf-fy21q1, data4ml
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 07/06/2021
ms.openlocfilehash: f640165420f06a85633d4db30d6338f4bfa205c4
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428372"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning 데이터 세트 만들기

이 문서에서는 Azure Machine Learning Python SDK를 사용하여 로컬 또는 원격 실험 데이터에 액세스하기 위해 Azure Machine Learning 데이터 세트를 만드는 방법을 알아봅니다. Azure Machine Learning의 데이터 액세스 워크플로 전체에서 데이터 세트가 적합한 위치를 이해하려면 [안전하게 데이터 액세스](concept-data.md#data-workflow) 문서를 참조하세요.

데이터 세트를 만들면 데이터 원본 위치에 대한 참조와 해당 메타데이터의 복사본을 만듭니다. 데이터가 기존 위치에 남아 있기 때문에 추가 스토리지 비용이 발생하지 않으며 데이터 원본의 무결성에 대한 위험이 없습니다. 또한 데이터 세트 평가기 지연되므로 워크플로 성능 속도가 향상됩니다. 데이터 저장소, 퍼블릭 URL, [Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)에서 데이터 세트를 만들 수 있습니다.

코드를 적게 사용하려면 [Azure Machine Learning 스튜디오를 사용하여 Azure Machine Learning 데이터 세트를 만드세요](how-to-connect-data-ui.md#create-datasets).

Azure Machine Learning 데이터 세트를 사용하면 다음을 수행할 수 있습니다.

* 데이터 세트에서 참조하는 데이터의 단일 복사본을 스토리지에 보관합니다.

* 연결 문자열이나 데이터 경로와 상관없이 모델 학습 중에 데이터에 원활하게 액세스합니다. [데이터 세트를 사용하여 학습시키는 방법을 알아보세요](how-to-train-with-datasets.md).

* 데이터를 공유하고 다른 사용자와 협업합니다.

## <a name="prerequisites"></a>필수 구성 요소

데이터 세트를 만들고 작업하려면 다음이 필요합니다.

* Azure 구독 구독이 없으면 시작하기 전에 계정을 만드세요. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

* azureml-datasets 패키지가 포함된 [Azure Machine Learning SDK for Python이 설치](/python/api/overview/azure/ml/install)됩니다.

    * 통합 Notebook이 포함되어 있고 SDK가 이미 설치된 완전히 구성되어 관리되는 개발 환경인 [Azure Machine Learning 컴퓨팅 인스턴스](how-to-create-manage-compute-instance.md)를 만듭니다.

    **OR**

    * 고유한 Jupyter Notebook에서 작업하고 [직접 SDK를 설치](/python/api/overview/azure/ml/install)합니다.

> [!NOTE]
> 일부 데이터 세트 클래스는 64비트 Python과만 호환되는 [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) 패키지에 종속됩니다. __Linux__ 에서 개발하는 경우 이러한 클래스는 .NET Core 2.1을 사용하며 특정 배포에서만 지원됩니다. 지원되는 배포판에 대한 자세한 내용은 [Linux에 .NET 설치](/dotnet/core/install/linux) 문서의 .NET Core 2.1 열을 참조하세요.

> [!IMPORTANT]
> 패키지는 이전 버전의 Linux 배포판에서 작동할 수 있지만 기본 지원이 아닌 배포판을 사용하지 않는 것이 좋습니다. 기본 지원이 아닌 배포판은 최신 업데이트를 받지 못하기 때문에 보안 취약성이 있을 수 있습니다. 다음과 호환되는 최신 버전의 배포판를 사용하는 것이 좋습니다.

## <a name="compute-size-guidance"></a>컴퓨팅 크기 참고 자료

데이터 세트를 만들 때 메모리에서 컴퓨팅 처리 능력과 데이터 크기를 검토합니다. 스토리지에 있는 데이터 크기는 데이터 프레임의 데이터 크기와 같지 않습니다. 예를 들어 CSV 파일의 데이터는 데이터 프레임에서 최대 10배 확장할 수 있기 때문에, 1GB CSV 파일은 데이터 프레임에서 10GB가 될 수 있습니다. 

데이터가 압축되면 추가로 확장할 수 있습니다. 압축된 parquet 형식으로 저장된 20GB의 비교적 스파스한 데이터는 메모리에서 800GB까지 확장될 수 있습니다. Parquet 파일은 데이터를 열 형식으로 저장하므로 열의 절반만 필요하면 메모리에 ~ 400GB만 로드하면 됩니다.

[Azure Machine Learning에서 데이터 처리를 최적화하는 방법에 관해 자세히 알아봅니다](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>데이터 세트 형식

사용자가 학습에서 사용하는 방식에 따라 FileDatasets, TabularDatasets 등 두 가지 데이터 세트가 있습니다. 두 형식 모두 예측 도구, AutoML, hyperDrive 및 파이프라인과 관련된 Azure Machine Learning 학습 워크플로에서 사용할 수 있습니다. 

### <a name="filedataset"></a>FileDataset

[FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset)는 데이터 저장소와 퍼블릭 URL의 단일 파일 또는 여러 파일을 참조합니다. 데이터가 이미 정리되어 학습 실험에서 사용할 준비가 된 경우, 파일을 FileDataset 개체로 컴퓨팅에 [다운로드 또는 탑재](how-to-train-with-datasets.md#mount-vs-download)할 수 있습니다. 

원본 파일은 모든 형식이 될 수 있기 때문에 딥 러닝을 비롯한 더 광범위한 기계 학습 시나리오가 가능하기 때문에 기계 학습 워크플로에 FileDatasets를 사용하는 것이 좋습니다.

[Python SDK](#create-a-filedataset) 또는 [Azure Machine Learning 스튜디오](how-to-connect-data-ui.md#create-datasets)를 사용하여 FileDataset를 만듭니다.
### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)는 제공된 파일 또는 파일 목록을 구문 분석하여 데이터를 테이블 형식으로 나타냅니다. 그러면 데이터를 Pandas 또는 Spark DataFrame으로 구체화할 수 있기 때문에 Notebook에서 계속 익숙한 데이터 준비 및 학습 라이브러리를 사용하여 작업할 수 있습니다. csv, .tsv, [.parquet](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-), [.jsonl 파일](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--) 및 [SQL 쿼리 결과](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)에서 `TabularDataset` 개체를 만들 수 있습니다.

TabularDatasets를 사용하면 데이터의 열 또는 경로 패턴 데이터가 저장된 위치에서 타임스탬프를 지정하여 시계열 특성을 사용으로 설정할 수 있습니다. 이 사양에서는 시간을 기준으로 쉽고 효율적인 필터링을 수행할 수 있습니다. 예를 들어 [NOAA 날씨 데이터를 사용하는 테이블 형식 시계열 관련 API 데모](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)를 참조하세요.

[Python SDK](#create-a-tabulardataset) 또는 [Azure Machine Learning 스튜디오](how-to-connect-data-ui.md#create-datasets)를 사용하여 TabularDataset를 만듭니다.

>[!NOTE]
> Azure Machine Learning 스튜디오를 통해 생성된 [자동화된 ML](concept-automated-ml.md) 워크플로는 현재 TabularDatasets만 지원합니다. 

## <a name="access-datasets-in-a-virtual-network"></a>가상 네트워크의 데이터 세트에 액세스

작업 영역이 가상 네트워크에 있으면 유효성 검사를 건너뛰도록 데이터 세트를 구성해야 합니다. 가상 네트워크에서 데이터 저장소 및 데이터 세트를 사용하는 방법에 관한 자세한 내용은 [작업 영역 및 연결된 리소스 보호](how-to-secure-workspace-vnet.md#datastores-and-datasets)를 참조하세요.

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>데이터 저장소에서 데이터 세트 만들기

Azure Machine Learning에서 데이터에 액세스할 수 있도록 하려면 [Azure Machine Learning 데이터 저장소](how-to-access-data.md) 또는 웹 URL의 경로에서 데이터 세트를 만들어야 합니다. 

> [!TIP] 
> ID 기반 데이터 액세스 권한을 사용하여 스토리지 URL에서 직접 데이터 세트를 만들 수 있습니다. [ID 기반 데이터 액세스 권한을 사용하여 스토리지에 연결(미리 보기)](how-to-identity-based-data-access.md)에서 자세히 알아보기<br><br>
이 기능은 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든지 변경할 수 있습니다. 

 
Python SDK를 사용하여 데이터 저장소에서 데이터 세트를 만들려면 다음을 수행합니다.

1. 등록된 Azure Machine Learning 데이터 저장소의 기본 스토리지 서비스에 대한 `contributor` 또는 `owner` 액세스 권한이 있는지 확인합니다. [Azure Portal에서 스토리지 계정 권한을 확인](../role-based-access-control/check-access.md)합니다.

1. 데이터 저장소에서 경로를 참조하여 데이터 세트를 만듭니다. 여러 데이터 저장소의 여러 경로에서 데이터 세트를 만들 수 있습니다. 데이터 세트를 만들 수 있는 파일 수 또는 데이터 크기에는 하드 제한이 없습니다. 

> [!NOTE]
> 각 데이터 경로가 파일 또는 폴더를 가리키는지 확인하기 위해 몇 가지 요청이 스토리지 서비스로 전송됩니다. 이와 같은 오버헤드 때문에 성능이 저하되거나 오류가 발생할 수 있습니다. 1,000개의 파일이 포함된 하나의 폴더를 참조하는 데이터 세트는 하나의 데이터 경로를 참조하는 것으로 간주합니다. 최적의 성능을 위해 데이터 저장소에서 100개 미만의 경로를 참조하는 데이터 세트를 생성하는 것이 좋습니다.

### <a name="create-a-filedataset"></a>FileDataset 만들기

`FileDatasetFactory` 클래스에서 [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) 메서드를 사용하여 모든 형식의 파일을 로드하고 등록되지 않은 FileDataset을 만듭니다. 

스토리지가 가상 네트워크 또는 방화벽 뒤에 있으면 `from_files()` 메서드에서 `validate=False` 매개 변수를 설정합니다. 이렇게 하면 초기 유효성 검사 단계를 무시하고 해당 보안 파일에서 데이터 세트를 만들 수 있습니다. [가상 네트워크에서 데이터 저장소 및 데이터 세트를 사용](how-to-secure-workspace-vnet.md#datastores-and-datasets)하는 방법에 관해 자세히 알아봅니다.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
작업 영역 실험에서 데이터 세트를 재사용하고 공유하려면 [데이터 세트를 등록](#register-datasets)합니다. 

> [!TIP] 
> 로컬 디렉터리에서 파일을 업로드하고 퍼블릭 미리 보기 메서드인 [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-)를 사용하여 단일 메서드로 FileDataset을 만듭니다. 이 메서드는 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든지 변경할 수 있습니다. 
> 
>  이 메서드는 데이터를 기본 스토리지에 업로드하므로 스토리지 비용이 발생합니다. 

### <a name="create-a-tabulardataset"></a>TabularDataset 만들기

`TabularDatasetFactory` 클래스의 [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) 메서드를 사용하여 .csv 또는 .tsv 형식으로 파일을 읽고 등록되지 않은 TabularDataset을 만듭니다. .parquet 형식에서 파일에 읽어 오려면 [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 메서드를 사용합니다. 여러 파일에서 읽는 경우 결과는 하나의 테이블 형식 표시로 집계됩니다. 

지원되는 파일 형식뿐만 아니라 구문 및 디자인 패턴(예: [여러 줄 지원](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--))에 대한 정보는 [TabularDatasetFactory 참조 설명서](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory)를 참조하세요. 

스토리지가 가상 네트워크 또는 방화벽 뒤에 있으면 `from_delimited_files()` 메서드에서 `validate=False` 매개 변수를 설정합니다. 이렇게 하면 초기 유효성 검사 단계를 무시하고 해당 보안 파일에서 데이터 세트를 만들 수 있습니다. [가상 네트워크에서 데이터 저장소 및 데이터 세트를 사용](how-to-secure-workspace-vnet.md#datastores-and-datasets)하는 방법에 관해 자세히 알아봅니다.

다음 코드는 기존 작업 영역과 원하는 데이터 저장소를 이름별로 가져옵니다. 그런 다음, 데이터 저장소와 파일 위치를 `path` 매개 변수에 전달하여 새 TabularDataset, `weather_ds`를 만듭니다.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>데이터 스키마 설정

기본적으로 TabularDataset을 만들 때 열 데이터 형식이 자동으로 유추됩니다. 추론된 형식이 예상과 일치하지 않으면 다음 코드로 열 형식을 지정하여 데이터 세트 스키마를 업데이트할 수 있습니다. `infer_column_type` 매개 변수는 구분된 파일에서 만든 데이터 세트에만 적용됩니다. [지원되는 데이터 형식에 관해 자세히 알아봅니다](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Survived|Pclass|이름|성별|나이|SibSp|Parch|티켓|요금|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley(Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

작업 영역 실험에서 데이터 세트를 재사용하고 공유하려면 [데이터 세트를 등록](#register-datasets)합니다.

## <a name="wrangle-data"></a>데이터 랭글링
데이터 세트를 만들고 [등록](#register-datasets)한 다음, 모델 학습 전에 데이터 랭글링 및 [검색](#explore-data)을 위해 Notebook에 로드할 수 있습니다. 

데이터 랭글링이나 검색을 수행할 필요가 없으면 [데이터 세트를 사용한 학습](how-to-train-with-datasets.md)에서 ML 실험을 제출하기 위해 학습 스크립트에서 데이터 세트를 사용하는 방법을 참조하세요.

### <a name="filter-datasets-preview"></a>데이터 세트 필터링(미리 보기)

필터링 기능은 보유하고 있는 데이터 세트의 형식에 따라 달라집니다. 
> [!IMPORTANT]
> 미리 보기 메서드인 [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-)로 데이터 세트를 필터링하는 것은 [실험적인](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능이며, 언제든 변경될 수 있습니다. 
> 
**TabularDatasets의 경우**, [keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) 및 [drop_columns()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) 메서드로 열을 유지하거나 제거할 수 있습니다.

TabularDataset의 특정 열 값으로 행을 필터링하려면 [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) 메서드(미리 보기)를 사용합니다. 

다음 예에서는 지정된 식에 따라 등록되지 않은 데이터 세트를 반환합니다.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**FileDatasets에서** 각 행은 파일의 경로에 해당하므로, 열 값을 기준으로 필터링하는 것은 유용하지 않습니다. 그러나 CreationTime, Size 등과 같은 메타데이터를 기준으로 행을 [filter()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-)할 수 있습니다.

다음 예에서는 지정된 식에 따라 등록되지 않은 데이터 세트를 반환합니다.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

이미지 레이블 지정 프로젝트에서 만든 [레이블이 지정되는](how-to-create-image-labeling-projects.md) **데이터 세트는** 특별한 경우입니다. 이 데이터 세트는 이미지 파일로 구성된 TabularDataset 형식입니다. 해당 형식의 데이터 세트에서는 메타데이터 및 `label`과 `image_details`와 같은 열 값을 기준으로 이미지를 [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-)할 수 있습니다.

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data-preview"></a>데이터 분할(미리 보기)

TabularDataset 또는 FileDataset를 만들 때 `partitions_format` 매개 변수를 포함하여 데이터 세트를 분할할 수 있습니다. 

> [!IMPORTANT]
> 데이터 세트 분할은 [실험적](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능으로, 언제든 변경될 수 있습니다. 

데이터 세트를 분할하면 각 파일 경로의 파티션 정보가 지정된 형식에 따라 열로 추출됩니다. 형식은 첫 번째 파티션 키의 위치에서 시작하여 파일 경로의 끝까지입니다. 

예를 들어 부서 이름과 시간별 파티션인 `../Accounts/2019/01/01/data.jsonl` 경로라고 하면 `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'`은 값이 'Accounts’인 'Department' 문자열 열과, 값이 `2019-01-01`인 'PartitionDate' 날짜/시간 열을 만듭니다.

데이터에 기존 파티션이 있고 그 형식을 유지하려는 경우 [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) 메서드에 `partitioned_format` 매개 변수를 포함하여 FileDataset를 만듭니다. 

기존 파티션을 유지하는 TabularDataset를 만들려면 [from_parquet_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 또는 [from_delimited_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--) 메서드에 `partitioned_format` 매개 변수를 포함합니다.

다음 예제에서는
* 분할된 파일에서 FileDataset를 만듭니다.
* 파티션 키 가져오기
* 인덱싱된 새 FileDataset를 만드는 방법
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

[Partitions_by()](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-) 메서드를 사용하여 TabularDataset에 대해 새 파티션 구조를 만들 수도 있습니다.

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

## <a name="explore-data"></a>데이터 살펴보기

데이터 랭글링을 완료하고 나면 데이터 세트를 [등록](#register-datasets)한 다음, 모델 학습 전에 데이터 검색을 위해 Notebook에 로드할 수 있습니다.

FileDatasets의 경우 데이터 세트를 **탑재** 하거나 **다운로드** 하고 데이터 검색에 일반적으로 사용하는 Python 라이브러리를 적용할 수 있습니다. [탑재와 다운로드 비교에 관해 자세히 알아봅니다](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

TabularDatasets의 경우 [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 메서드를 사용하여 데이터 프레임의 데이터를 봅니다. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Survived|Pclass|이름|성별|나이|SibSp|Parch|티켓|요금|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley(Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Pandas 데이터 프레임에서 데이터 세트 만들기

메모리 내 pandas 데이터 프레임에서 TabularDataset을 만들려면 csv와 같은 로컬 파일에 데이터를 쓰고 해당 파일에서 데이터 세트를 만듭니다. 다음 코드에서 이 워크플로를 보여 줍니다.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> 퍼블릭 미리 보기 메서드인 [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) 및 [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods)이 있는 단일 메서드를 사용하여 메모리 내 spark 또는 pandas 데이터 프레임에서 TabularDataset을 만들고 등록합니다. 이 등록 메서드는 [실험적](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능이며 언제든지 변경될 수 있습니다. 
> 
>  이 메서드는 데이터를 기본 스토리지에 업로드하므로 스토리지 비용이 발생합니다. 

## <a name="register-datasets"></a>데이터 세트 등록

만들기 프로세스를 완료하려면 작업 영역에 데이터 세트를 등록합니다. 다른 사용자와 공유하고 작업 영역의 실험에서 재사용하도록 [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) 메서드를 사용하여 작업 영역에 데이터 세트를 등록합니다.

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 데이터 세트 만들기

[https://github.com/Azure/azure-quickstart-templates/tree/master//quickstarts/microsoft.machinelearningservices](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices)에는 데이터 세트를 만드는 데 사용할 수 있는 템플릿이 많이 있습니다.

해당 템플릿 사용에 관한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning용 작업 영역 만들기 ](how-to-create-workspace-template.md)를 참조하세요.


## <a name="create-datasets-from-azure-open-datasets"></a>Azure Open Datasets에서 데이터 세트 만들기

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)는 기계 학습 솔루션에 시나리오별 기능을 추가하여 보다 정확한 모델을 만들 수 있는 큐레이팅된 공개 데이터 세트입니다. 데이터 세트에는 기계 학습 모델을 학습시키고 예측 솔루션을 보강할 수 있는 날씨, 인구, 휴일, 공공 안전 및 위치에 대한 공개 도메인 데이터가 포함되어 있습니다. Open Datasets는 Microsoft Azure의 클라우드에 있으며 SDK와 스튜디오 둘 다에 포함되어 있습니다.

[Azure Open Datasets에서 Azure Machine Learning 데이터 세트](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)를 만드는 방법을 알아봅니다. 

## <a name="train-with-datasets"></a>데이터 세트로 학습

ML 모델 학습을 위한 기계 학습 실험에서 데이터 세트를 사용합니다. [데이터 세트를 사용하여 학습시키는 방법을 알아보세요](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>버전 데이터 세트

새 버전을 만들어 같은 이름으로 새 데이터 세트를 등록할 수 있습니다. 데이터 세트 버전을 사용하면 데이터 상태에 책갈피를 지정하여 실험 또는 향후 재현을 위해 특정 버전의 데이터 세트를 적용할 수 있습니다. [데이터 세트 버전](how-to-version-track-datasets.md)에 관해 자세히 알아봅니다.
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>다음 단계

* [데이터 세트를 사용하여 학습하는 방법](how-to-train-with-datasets.md)에 관해 알아봅니다.
* 자동화된 Machine Learning을 사용하여 [TabularDatasets로 학습](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)합니다.
* 추가 데이터 세트 학습 예제는 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)을 참조하세요.
