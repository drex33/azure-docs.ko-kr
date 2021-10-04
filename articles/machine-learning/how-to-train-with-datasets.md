---
title: 기계 학습 데이터 세트로 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 데이터 세트를 사용하여 모델 학습용으로 로컬 또는 원격 컴퓨팅에서 데이터를 사용할 수 있게 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: yogipandey
author: ynpandey
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 1aa47d7dfbc4a4d43702522a133ae86c8bdbdd1a
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427270"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Azure Machine Learning 데이터 세트를 사용하여 모델 학습 

이 문서에서는 [Azure Machine Learning 데이터 세트](/python/api/azureml-core/azureml.core.dataset%28class%29)를 사용하여 기계 학습 모델을 학습시키는 방법을 알아봅니다.  연결 문자열이나 데이터 경로에 상관없이 로컬 또는 원격 컴퓨팅 대상에서 데이터 세트를 사용할 수 있습니다. 

Azure Machine Learning 데이터 세트는 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig), [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive) 및 [Azure Machine Learning 파이프라인](./how-to-create-machine-learning-pipelines.md)과 같은 Azure Machine Learning 교육 기능과 원활하게 통합됩니다.

모델 학습에 데이터를 사용할 준비가 되지 않았지만 데이터 검색을 위해 Notebook에 데이터를 로드하려면 [데이터 세트에서 데이터 검색](how-to-create-register-datasets.md#explore-data) 방법을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항

데이터 세트를 만들고 학습시키려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

* `azureml-datasets` 패키지가 포함된 [Azure Machine Learning SDK for Python이 설치](/python/api/overview/azure/ml/install)됩니다(1.13.0 이상).

> [!Note]
> 일부 데이터 세트 클래스는 [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) 패키지에 종속되어 있습니다. Linux 사용자의 경우 이 클래스는 Red Hat Enterprise Linux, Ubuntu, Fedora 및 CentOS 배포판에서만 지원됩니다.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>기계 학습의 학습 스크립트에서 데이터 세트 사용

아직 데이터 세트로 등록되지 않은 정형 데이터가 있으면 TabularDataset을 만들고 로컬 또는 원격 실험을 위한 학습 스크립트에서 직접 사용합니다.

이 예제에서는 등록되지 않은 [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)를 만들고 학습을 위해 [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) 개체에서 스크립트 인수로 지정합니다. 이 TabularDataset을 작업 영역의 다른 실험과 함께 재사용하려면 [작업 영역에 데이터 세트를 등록하는 방법](how-to-create-register-datasets.md#register-datasets)을 참조하세요.

### <a name="create-a-tabulardataset"></a>TabularDataset 만들기

다음 코드는 웹 URL에서 등록되지 않은 TabularDataset을 만듭니다.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset 개체는 TabularDataset의 데이터를 pandas 또는 Spark DataFrame으로 로드하는 기능을 제공하므로 Notebook에서 계속 익숙한 데이터 준비 및 학습 라이브러리로 작업할 수 있습니다.

### <a name="access-dataset-in-training-script"></a>학습 스크립트의 데이터 세트에 액세스

다음 코드는 학습 실행을 구성할 때 지정할 스크립트 인수 `--input-data`를 구성합니다(다음 섹션 참조). 테이블 형식 데이터 세트가 인수 값으로 전달되면 Azure ML은 이 값을 데이터 세트의 ID로 확인합니다. 따라서 학습 스크립트의 데이터 세트에 액세스하는 데 사용할 수 있습니다(스크립트에 있는 데이터 세트의 이름 또는 ID를 하드 코드할 필요가 없음). 그런 다음 [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 메서드를 사용하여 학습 전에 추가 데이터 검색 및 준비를 위해 해당 데이터 세트를 pandas 데이터 프레임에 로드합니다.

> [!Note]
> 원래 데이터 원본에 NaN, 빈 문자열 또는 빈 값이 포함된 경우 `to_pandas_dataframe()`을 사용하면 해당 값이 *Null* 값으로 바뀝니다.

메모리 내 pandas 데이터 프레임에서 새 데이터 세트로 준비된 데이터를 로드해야 하는 경우 데이터를 parquet와 같은 로컬 파일에 쓰고 해당 파일에서 새 데이터 세트를 만듭니다. [데이터 세트를 만드는 방법](how-to-create-register-datasets.md)에 관해 자세히 알아봅니다.

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>학습 실행 구성

[ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) 개체는 학습 실행을 구성하고 제출하는 데 사용합니다.

이 코드는 다음을 지정하는 ScriptRunConfig 개체, `src`를 만듭니다.

* 스크립트의 스크립트 디렉터리입니다. 이 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다.
* 학습 스크립트, *train_titanic.py* 입니다.
* 스크립트 인수로 사용할 학습용 입력 데이터 세트, `titanic_ds`입니다. Azure ML에서는 데이터 세트를 스크립트에 전달할 때 데이터 세트의 해당 ID로 확인합니다.
* 실행에 사용할 컴퓨팅 대상입니다.
* 실행에 사용할 환경입니다.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>원격 컴퓨팅 대상에 파일 탑재

비정형 데이터가 있는 경우 [FileDataset](/python/api/azureml-core/azureml.data.filedataset)를 만들고 데이터 파일을 탑재하거나 다운로드하여 학습용 원격 컴퓨팅 대상에서 사용할 수 있게 만들 수 있습니다. 원격 학습 실험을 위해 [탑재 및 다운로드](#mount-vs-download)를 사용하는 경우에 관해 알아봅니다. 

다음 예제에서는 

* 학습 데이터에 대한 입력 FileDataset `mnist_ds`를 만듭니다.
* 학습 결과를 작성하고 해당 결과를 FileDataset으로 승격할 위치를 지정합니다.
* 입력 데이터 세트를 컴퓨팅 대상에 탑재합니다.

> [!Note]
> 사용자 지정 Docker 기본 이미지를 사용하는 경우 데이터 세트 탑재가 작동하려면 `apt-get install -y fuse`를 종속성으로 사용하여 fuse를 설치해야 합니다. [사용자 지정 빌드 이미지를 빌드](./how-to-deploy-custom-container.md)하는 방법을 알아봅니다.

Notebook 예제는 [데이터 입력 및 출력을 사용하여 학습 실행을 구성하는 방법](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb)을 참조하세요.

### <a name="create-a-filedataset"></a>FileDataset 만들기

다음 예제에서는 웹 url에서 등록되지 않은 FileDataset, `mnist_data`를 만듭니다. 이 FileDataset은 학습 실행에 대한 입력 데이터입니다.

다른 원본에서 [데이터 세트를 만드는 방법](how-to-create-register-datasets.md)에 관해 자세히 알아보세요.

```Python

from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]

mnist_ds = Dataset.File.from_files(path = web_paths)

```
### <a name="where-to-write-training-output"></a>학습 출력을 작성하는 위치

[OutputFileDatasetConfig 개체](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)를 사용하여 학습 결과를 작성할 위치를 지정할 수 있습니다. 

OutputFileDatasetConfig 개체를 사용하면 다음을 수행할 수 있습니다. 

* 지정한 클라우드 스토리지에 실행 출력을 탑재하거나 업로드합니다.
* 출력을 다음과 같은 지원되는 스토리지 유형에 FileDataset으로 저장합니다.
    * Azure Blob
    * Azure 파일 공유
    * Azure Data Lake Storage 1세대 및 2세대
* 학습 실행 간의 데이터 계보를 추적합니다.

다음 코드는 학습 결과를 기본 Blob 데이터 저장소인 `def_blob_store`의 `outputdataset` 폴더에 FileDataset으로 저장해야 함을 지정합니다. 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>학습 실행 구성

`ScriptRunConfig` 생성자의 `arguments` 매개 변수를 통해 탑재할 때 데이터 세트를 인수로 전달하는 것이 좋습니다. 그러면 인수를 통해 학습 스크립트의 데이터 경로(탑재 지점)를 가져올 수 있습니다. 이렇게 하면 모든 클라우드 플랫폼에서 로컬 디버깅 및 원격 학습에 동일한 학습 스크립트를 사용할 수 있습니다.

다음 예에서는 `arguments`를 통해 FileDataset에 전달된 ScriptRunConfig를 만듭니다. 실행을 제출하면 데이터 세트 `mnist_ds`에서 참조하는 데이터 파일이 컴퓨팅 대상에 탑재되고 학습 결과가 기본 데이터 저장소의 지정된 `outputdataset` 폴더에 저장됩니다.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>간단한 학습 스크립트

다음 스크립트는 ScriptRunConfig를 통해 제출됩니다. `mnist_ds ` 데이터 세트를 입력으로 읽고 기본 Blob 데이터 저장소인 `def_blob_store`의 `outputdataset` 폴더에 파일을 씁니다.

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>탑재와 다운로드 비교

Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database 및 Azure Database for PostgreSQL에서 생성된 데이터 세트에 대해 모든 형식의 파일 탑재 또는 다운로드가 지원됩니다. 

데이터 세트를 **탑재** 할 때 데이터 세트가 참조하는 파일을 디렉터리(탑재 지점)에 연결하고 컴퓨팅 대상에서 사용할 수 있게 합니다. 탑재는 Azure Machine Learning 컴퓨팅, 가상 머신 및 HDInsight를 포함한 Linux 기반 컴퓨팅에 지원됩니다. 데이터 크기가 컴퓨팅 디스크 크기를 초과하는 경우 다운로드를 수행할 수 없습니다. 이 시나리오의 경우 스크립트에서 사용하는 데이터 파일만 처리 시 로드되므로 탑재하는 것이 좋습니다.

데이터 세트를 **다운로드** 하면 데이터 세트에서 참조하는 모든 파일이 컴퓨팅 대상으로 다운로드됩니다. 다운로드는 모든 컴퓨팅 형식에 지원됩니다. 스크립트가 데이터 세트에서 참조하는 모든 파일을 처리하고 컴퓨팅 디스크가 전체 데이터 세트에 적합한 경우 스토리지 서비스에서 데이터를 스트림하는 오버헤드를 방지하기 위해 다운로드하는 것이 좋습니다. 다중 노드 다운로드의 경우 [제한을 방지하는 방법](#troubleshooting)을 참조하세요. 

> [!NOTE]
> 다운로드 경로 이름은 Windows OS의 경우 255자 이하의 영숫자여야 합니다. Linux OS의 경우 다운로드 경로 이름은 4,096자 이하의 영숫자여야 합니다. 또한 Linux OS의 경우 파일 이름(다운로드 경로 `/path/to/file/{filename}`의 마지막 세그먼트)은 255자 이하의 영숫자여야 합니다.

다음 코드는 `dataset`를 `mounted_path`의 임시 디렉터리에 탑재함

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>기계 학습 스크립트에서 데이터 세트 가져오기

등록된 데이터 세트는 Azure Machine Learning 컴퓨팅과 같은 컴퓨팅 클러스터에서 로컬 및 원격으로 모두 액세스할 수 있습니다. 실험 전체에서 등록된 데이터 세트에 액세스하려면 다음 코드를 사용하여 작업 영역에 액세스하고 이전에 제출한 실행에서 사용된 데이터 세트를 가져옵니다. 기본적으로 `Dataset` 클래스의 [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) 메서드는 작업 영역에 등록된 데이터 세트의 최신 버전을 반환합니다.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>학습 도중에 소스 코드에 액세스

Azure Blob Storage는 Azure 파일 공유보다 처리 속도가 빠르며 동시에 시작되는 여러 작업으로 스케일링됩니다. 이러한 이유로 소스 코드 파일을 전송할 때 Blob Storage를 사용하도록 실행을 구성하는 것이 좋습니다.

다음 코드 예제는 실행 구성에서 소스 코드 전송에 사용할 BLOB 데이터 저장소를 지정합니다.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebook 예제

+ 추가 데이터 세트 예제 및 개념은 [데이터 세트 Notebooks](https://aka.ms/dataset-tutorial)를 참조하세요.
+ [ML 파이프라인에서 데이터 세트 매개 변수화](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) 방법을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

**데이터 세트 초기화 실패: 탑재 지점 준비 대기 시간이 초과됨**: 
  * 아웃바운드 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md) 규칙이 없고 `azureml-sdk>=1.12.0`을 사용하는 경우 `azureml-dataset-runtime` 및 해당 종속성을 특정 부 버전의 최신 버전으로 업데이트합니다. 또는 실행에서 사용하는 경우 수정 사항이 포함된 최신 패치가 있도록 환경을 다시 만듭니다. 
  * `azureml-sdk<1.12.0`을 사용하는 경우 최신 버전으로 업그레이드합니다.
  * 아웃바운드 NSG 규칙이 있는 경우 서비스 태그 `AzureResourceMonitor`의 모든 트래픽을 허용하는 아웃바운드 규칙이 있는지 확인합니다.

**데이터 세트 초기화 실패: ThrottlingException으로 인해 StreamAccessException 발생**

다중 노드 파일 다운로드의 경우, 모든 노드가 Azure Storage 서비스에서 파일 데이터 세트의 모든 파일을 다운로드하려고 시도하여 제한 오류가 발생할 수 있습니다. 제한을 방지하려면 처음에 `AZUREML_DOWNLOAD_CONCURRENCY` 환경 변수가 CPU 코어 수를 노드 수로 나눈 값의 8배가 되도록 설정합니다. 이 환경 변수의 값을 설정하려면 몇 가지 실험이 필요할 수 있으므로 앞에서 설명한 지침은 시작점입니다.

다음 예제에서는 32개 코어와 4개 노드를 가정합니다.

```python
from azureml.core.environment import Environment 
myenv = Environment(name="myenv")
myenv.environment_variables = {"AZUREML_DOWNLOAD_CONCURRENCY":64}
```

### <a name="azurefile-storage"></a>AzureFile 스토리지

**스토리지가 오버로드되어 AzureFile의 작업 디렉터리에 프로젝트 파일을 업로드할 수 없습니다**.

* 데이터 전송과 같은 다른 워크로드에 파일 공유를 사용하는 경우 실행을 제출하는 데 파일 공유를 자유롭게 사용할 수 있도록 Blob을 사용하는 것이 좋습니다.

* 또 다른 옵션은 서로 다른 두 작업 영역 간에 워크로드를 분할하는 것입니다.

**ConfigException: 자격 증명이 누락되어 AzureFileService에 대한 연결을 만들 수 없습니다. 계정 키 또는 SAS 토큰을 기본 작업 영역 Blob 저장소에 연결해야 합니다.**

스토리지 액세스 자격 증명이 작업 영역 및 관련 파일 데이터 저장소에 연결되어 있는지 확인하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://ms.portal.azure.com)에서 작업 영역으로 이동합니다.
1. 작업 영역 **개요** 페이지에서 스토리지 링크를 선택합니다.
1. 스토리지 페이지의 왼쪽 메뉴에서 **액세스 키** 를 선택합니다. 
1. 키를 복사합니다.
1. 작업 영역에 대한 [Azure Machine Learning 스튜디오](https://ml.azure.com)로 이동합니다.
1. 스튜디오에서 인증 자격 증명을 제공하려는 파일 데이터 저장소를 선택합니다. 
1. **인증 업데이트** 를 선택합니다.
1. 이전 단계에서 키를 붙여넣습니다. 
1. **저장** 을 선택합니다. 

### <a name="passing-data-as-input"></a>데이터를 입력으로 전달

**TypeError: FileNotFound: 해당 파일 또는 디렉터리가 없음**: 제공한 파일 경로가 파일이 있는 위치가 아닌 경우 이 오류가 발생합니다. 파일을 참조하는 방식이 컴퓨팅 대상에서 데이터 세트를 탑재한 위치와 일치하는지 확인해야 합니다. 결정적 상태를 보장하려면 컴퓨팅 대상에 데이터 세트를 탑재할 때 추상 경로를 사용하는 것이 좋습니다. 예를 들어, 다음 코드에서는 컴퓨팅 대상 `/tmp`의 파일 시스템 루트 아래에 데이터 세트를 탑재합니다. 
    
```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

선행 슬래시 ‘/’를 포함하지 않는 경우 컴퓨팅 대상에서 작업 디렉터리를 접두사로 지정하여(예: `/mnt/batch/.../tmp/dataset`) 데이터 세트를 탑재할 위치를 표시해야 합니다.


## <a name="next-steps"></a>다음 단계

* TabularDatasets로 [기계 학습 모델을 자동 학습](how-to-configure-auto-train.md#data-source-and-format)시킵니다.

* FileDatasets를 사용하여 [이미지 분류 모델을 학습](https://aka.ms/filedataset-samplenotebook)시킵니다.

* [파이프라인을 사용하여 데이터 세트로 학습](./how-to-create-machine-learning-pipelines.md)시킵니다.