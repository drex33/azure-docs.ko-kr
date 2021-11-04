---
title: Apache Spark 풀을 사용한 데이터 랭글링(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Synapse Analytics 및 Azure Machine Learning에서의 데이터 랭글링을 위해 Apache Spark 풀을 연결하고 시작하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: 711521554e30b2905184150fa631ce92390534dd
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564388"
---
# <a name="data-wrangling-with-apache-spark-pools-preview"></a>Apache Spark 풀을 사용한 데이터 랭글링(미리 보기) 

이 문서에서는 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/)를 사용하여 Jupyter Notebook에서 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md)가 제공하는 전용 Synapse 세션 내 데이터 랭글링 작업을 대화형으로 수행하는 방법을 알아봅니다. 

Azure Machine Learning 파이프라인을 사용하려면 [기계 학습 파이프라인에서 Apache Spark(Azure Synapse Analytics에서 제공)를 사용하는 방법(미리 보기)](how-to-use-synapsesparkstep.md)을 참조하세요.

Synapse 작업 영역에서 Azure Synapse Analytics를 사용하는 방법에 대한 지침은 [Azure Synapse Analytics 시작 시리즈](../synapse-analytics/get-started.md)를 참조하세요.

>[!IMPORTANT]
> Azure Machine Learning 및 Azure Synapse Analytics 통합은 미리 보기 상태입니다. 이 문서에 제공된 기능은 언제든지 변경될 수 있는 [실험적](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능을 포함하는 `azureml-synapse` 패키지를 사용합니다.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration"></a>Azure Machine Learning 및 Azure Synapse Analytics 통합

Azure Machine Learning과 Azure Synapse Analytics를 통합(미리 보기)하면 대화형 데이터 탐색 및 준비를 위해 Azure Synapse에서 지원하는 Apache Spark 풀을 연결할 수 있습니다. 이 통합을 통해 기계 학습 모델 학습에 사용하는 동일한 Python Notebook 내에서 대규모 데이터 랭글링을 위한 전용 컴퓨팅을 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [설치된 Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install) 

* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md?tabs=python).

* [Azure Portal에서 Azure Synapse Analytics 작업 영역을 만듭니다](../synapse-analytics/quickstart-create-workspace.md).

* [Azure Portal, 웹 도구 또는 Synapse Studio를 사용하여 Apache Spark 풀을 만듭니다](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

* Azure Machine Learning SDK를 설치하거나 SDK가 이미 설치된 [Azure Machine Learning 컴퓨팅 인스턴스](concept-compute-instance.md#create)를 사용하도록 [개발 환경을 구성](how-to-configure-environment.md)합니다. 

* 다음 코드를 사용하여 `azureml-synapse` 패키지(미리 보기)를 설치합니다.

  ```python
  pip install azureml-synapse
  ```

* [Azure Machine Learning Python SDK](how-to-link-synapse-ml-workspaces.md#link-sdk)를 사용하거나 [Azure Machine Learning 스튜디오](how-to-link-synapse-ml-workspaces.md#link-studio)를 통해 Azure Machine Learning 작업 영역과 Azure Synapse Analytics 작업 영역을 연결합니다.

* [Synapse Spark 풀을 컴퓨팅 대상으로 연결](how-to-link-synapse-ml-workspaces.md#attach-synapse-spark-pool-as-a-compute)합니다.

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>데이터 랭글링 작업을 위해 Synapse Spark 풀을 시작합니다.

Apache Spark 풀을 사용하여 데이터 준비를 시작하려면 연결된 Spark Synapse 컴퓨팅 이름을 지정합니다. 이 이름은 **연결된 컴퓨팅** 탭에서 Azure Machine Learning 스튜디오를 통해 찾을 수 있습니다. 

![연결된 컴퓨팅 이름 가져오기](media/how-to-data-prep-synapse-spark-pool/attached-compute.png)

> [!IMPORTANT]
> Apache Spark 풀을 계속 사용하려면 단일 코드 줄의 경우 `%synapse`, 여러 줄의 경우 `%%synapse`를 사용하여 데이터 랭글링 전체에서 사용할 컴퓨팅 리소스를 표시해야 합니다. [% Synapse 매직 명령에 대해 자세히 알아보세요](/python/api/azureml-synapse/azureml.synapse.magics.remotesynapsemagics(class)).

```python
%synapse start -c SynapseSparkPoolAlias
```

세션이 시작되면 세션의 메타데이터를 확인할 수 있습니다.

```python
%synapse meta
```

Apache Spark 세션 중에 사용할 [Azure Machine Learning 환경](concept-environments.md)을 지정할 수 있습니다. 환경에 지정된 Conda 종속성만 적용됩니다. Docker 이미지는 지원되지 않습니다.

>[!WARNING]
>  환경 Conda 종속성에 지정된 Python 종속성은 Apache Spark 풀에서 지원되지 않습니다. 현재는 고정 Python 버전만 지원됩니다. 스크립트에 `sys.version_info`를 포함하여 Python 버전을 확인합니다.

다음 코드에서는 세션을 시작하기 전에 `azureml-core` 버전 1.20.0 및 `numpy` 버전 1.17.0을 설치하는 `myenv` 환경을 만듭니다. 그런 다음, Apache Spark 세션 `start` 문에 이 환경을 포함할 수 있습니다.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Apache Spark 풀 및 사용자 지정 환경에서 데이터 준비를 시작하려면 Apache Spark 풀 이름과 Apache Spark 세션 중에 사용할 환경을 지정합니다. 또한 구독 ID, 기계 학습 작업 영역 리소스 그룹 및 기계 학습 작업 영역의 이름을 제공할 수 있습니다.

>[!IMPORTANT]
> 연결된 Synapse 작업 영역에서 [세션 수준 패키지 허용](../synapse-analytics/spark/apache-spark-manage-python-packages.md#session-scoped-packages)이 사용하도록 설정되었는지 확인합니다.
>
>![세션 수준 패키지 사용](media/how-to-data-prep-synapse-spark-pool/enable-session-level-package.png)

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```

## <a name="load-data-from-storage"></a>스토리지에서 데이터 로드

Apache Spark 세션이 시작되면 준비하려는 데이터를 읽습니다. 데이터 로드는 Azure Blob Storage 및 Azure Data Lake Storage Generation 1 및 2에서 지원됩니다.

이러한 스토리지 서비스에서 데이터를 로드하는 방법에는 두 가지가 있습니다. 

* HDFS(Hadoop Distributed Files System) 경로를 사용하여 스토리지에서 데이터를 직접 로드합니다.

* 기존의 [Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md)에서 데이터를 읽습니다.

이러한 스토리지 서비스에 액세스하려면 **Storage Blob 데이터 읽기 권한자** 권한이 필요합니다. 이러한 스토리지 서비스에 데이터를 다시 쓰려면 **Storage Blob 데이터 기여자** 권한이 필요합니다. [스토리지 권한 및 역할에 대해 자세히 알아보세요](../storage/blobs/assign-azure-role-data-access.md).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>HDFS(Hadoop Distributed Files System) 경로를 사용하여 데이터 로드

해당 HDFS 경로를 사용하여 스토리지에서 데이터를 로드하고 읽으려면 데이터 액세스 인증 자격 증명을 즉시 사용할 수 있게 준비해야 합니다. 이 자격 증명은 스토리지 유형에 따라 다릅니다.  

다음 코드에서는 SAS(공유 액세스 서명) 토큰 또는 액세스 키를 사용하여 **Azure Blob Storage** 에서 Spark 데이터 프레임으로 데이터를 읽는 방법을 보여 줍니다. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

다음 코드에서는 서비스 주체 자격 증명을 사용하여 **ADLS Gen 1(Azure Data Lake Storage Generation 1)** 에서 데이터를 읽는 방법을 보여 줍니다. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

다음 코드에서는 서비스 주체 자격 증명을 사용하여 **ADLS Gen 2(Azure Data Lake Storage Generation 2)** 에서 데이터를 읽는 방법을 보여 줍니다. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>등록된 데이터 세트에서 데이터 읽기

작업 영역에서 등록된 기존 데이터 세트를 가져오고 Spark 데이터 프레임으로 변환하여 데이터 준비를 수행할 수도 있습니다.

다음 예제에서는 작업 영역에 인증하고 Blob Storage의 파일을 참조하는 등록된 TabularDataset `blob_dset`를 가져온 다음, Spark 데이터 프레임으로 변환합니다. 데이터 세트를 Spark 데이터 프레임으로 변환할 때 `pyspark` 데이터 탐색 및 준비 라이브러리를 활용할 수 있습니다.  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>데이터 랭글링 작업 수행

데이터를 검색하고 탐색한 후에는 데이터 랭글링 작업을 수행할 수 있습니다.

다음 코드는 이전 섹션의 HDFS 예제를 확장하고 **Survivor** 열 및 **Age** 별로 나열된 그룹을 기준으로 Spark 데이터 프레임 `df`의 데이터를 필터링합니다.

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>스토리지에 데이터 저장 및 Spark 세션 중지

데이터 탐색 및 준비가 완료되면 준비된 데이터를 나중에 사용할 수 있게 Azure의 스토리지 계정에 저장합니다.

다음 예제에서 준비된 데이터는 Azure Blob Storage에 다시 기록되고 `training_data` 디렉터리의 원본 `Titanic.csv` 파일을 덮어씁니다. 스토리지에 다시 쓰려면 **Storage Blob 데이터 기여자** 권한이 필요합니다. [스토리지 권한 및 역할에 대해 자세히 알아보세요](../storage/blobs/assign-azure-role-data-access.md).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

데이터 준비를 완료하고 준비된 데이터를 스토리지에 저장했으면 다음 명령을 사용하여 Apache Spark 풀 사용을 중지합니다.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>준비된 데이터를 나타내는 데이터 세트 만들기

모델 학습을 위해 준비된 데이터를 사용할 준비가 되면 [Azure Machine Learning 데이터 저장소](how-to-access-data.md)를 사용하여 스토리지에 연결하고 [Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md)와 함께 사용할 파일을 지정합니다.

다음 코드 예제에서는

* 준비된 데이터를 저장한 스토리지 서비스에 연결하는 데이터 저장소를 이미 만들었다고 가정합니다.  
* get() 메서드를 사용하여 작업 영역 `ws`에서 기존 데이터 저장소 `mydatastore`를 가져옵니다.
* `mydatastore`의 `training_data` 디렉터리에 있는 준비된 데이터 파일을 참조하는 [FileDataset](how-to-create-register-datasets.md#filedataset) `train_ds`를 만듭니다.  
* 학습 작업을 위한 컴퓨팅 대상에서 `train_ds` 데이터 세트의 데이터 파일을 사용할 수 있도록 나중에 사용할 수 있는 `input1` 변수를 만듭니다.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>`ScriptRunConfig`를 사용하여 Synapse Spark 풀에 실험 실행을 제출합니다.

데이터 랭글링 작업을 자동화하고 프로덕션화할 준비가 되었으면 [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 개체를 사용하여 [연결된 Synapse Spark 풀](how-to-link-synapse-ml-workspaces.md#attach-a-pool-with-the-python-sdk)에 실험 실행을 제출할 수 있습니다.  

마찬가지로, Azure Machine Learning 파이프라인이 있으면 파이프라인에서 [SynapseSparkStep을 사용하여 Synapse Spark 풀을 데이터 준비 단계의 컴퓨팅 대상으로 지정](how-to-use-synapsesparkstep.md)할 수 있습니다.

Synapse Spark 풀에 데이터를 제공하는 것은 데이터 세트 형식에 따라 달라집니다. 

* FileDataset의 경우 [`as_hdfs()`](/python/api/azureml-core/azureml.data.filedataset#as-hdfs--) 메서드를 사용할 수 있습니다. 실행이 제출되면 데이터 세트가 Synapse Spark 풀에 HFDS(Hadoop 분산 파일 시스템)로 제공됩니다. 
* [TabularDataset](how-to-create-register-datasets.md#tabulardataset)의 경우 [`as_named_input()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#as-named-input-name-) 메서드를 사용할 수 있습니다. 

코드는 다음과 같습니다. 

* 이전 코드 예제에서 생성된 FileDataset `train_ds`로부터 `input2` 변수를 만듭니다.
* HDFSOutputDatasetConfiguration 클래스로 `output` 변수를 만듭니다. 실행이 완료된 다음 이 클래스는 `mydatastore` 데이터 저장소에서 `test` 데이터 세트로 실행 출력을 저장할 수 있게 해줍니다. Azure Machine Learning 작업 영역에서 `test` 데이터 세트는 `registered_dataset` 이름으로 등록됩니다. 
* Synapse Spark 풀에서 수행하기 위해 실행에 사용되어야 하는 설정을 구성합니다. 
* 다음을 목적으로 ScriptRunConfig 매개변수를 정의합니다. 
  * 실행에 대해 `dataprep.py`를 사용합니다. 
  * 입력으로 사용할 데이터 및 이를 Synapse Spark 풀에 제공하는 방법을 지정합니다.
  * 출력 데이터 `output`을 저장할 위치를 지정합니다.  

```Python
from azureml.core import Dataset, HDFSOutputDatasetConfig
from azureml.core.environment import CondaDependencies
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

input2 = train_ds.as_hdfs()
output = HDFSOutputDatasetConfig(destination=(datastore, "test").register_on_complete(name="registered_dataset")

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

conda_dep = CondaDependencies()
conda_dep.add_pip_package("azureml-core==1.20.0")

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

`ScriptRunConfig` 개체가 설정되었으면 실행을 제출할 수 있습니다.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```

이 예제에 사용된 `dataprep.py` 스크립트와 같은 추가 세부정보는 [예제 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb)을 참조하세요.

데이터가 준비된 후 이를 학습 작업의 입력으로 사용할 수 있습니다. 앞에서 언급한 코드 예제에서 `registered_dataset`는 학습 작업을 위해 입력 데이터로 지정되는 항목입니다. 

## <a name="example-notebooks"></a>노트북 예제

Azure Synapse Analytics 및 Azure Machine Learning 통합 기능에 대한 자세한 개념 및 데모는 예제 Notebook을 참조하세요.
* [Azure Machine Learning 작업 영역의 Notebook에서 대화형 Spark 세션 실행](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb)
* [컴퓨팅 대상으로 Synapse Spark 풀을 사용하여 Azure Machine Learning 실험 실행 제출](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb)

## <a name="next-steps"></a>다음 단계

* [모델을 학습합니다](how-to-set-up-training-targets.md).
* [Azure Machine Learning 데이터 세트로 학습](how-to-train-with-datasets.md)
