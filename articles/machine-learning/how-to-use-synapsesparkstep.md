---
title: 기계 학습 파이프라인에서 Apache Spark 사용(미리 보기)
titleSuffix: Azure Machine Learning
description: 데이터 조작을 위해 Apache Spark를 사용하려면 Azure Synapse Analytics 작업 영역을 Azure Machine Learning 파이프라인에 연결합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: how-to
ms.custom: synapse-azureml
ms.openlocfilehash: 4139b5e2a05ebf4f6355db761d0458b4f367cbaf
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425167"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>기계 학습 파이프라인에서 Apache Spark(Azure Synapse Analytics에서 제공)를 사용하는 방법(미리 보기)

이 문서에서는 Azure Synapse Analytics에서 제공하는 Apache Spark 풀을 Azure Machine Learning 파이프라인의 데이터 준비 단계에 대한 컴퓨팅 대상으로 사용하는 방법에 대해 알아봅니다. 단일 파이프라인이 특정 단계에 적합한 계산 리소스(예: 데이터 준비 또는 학습)를 사용할 수 있는 방법을 알아봅니다. Spark 단계에 대한 데이터를 준비하는 방법과 다음 단계로 전달하는 방법을 확인할 수 있습니다. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>필수 구성 요소

* 모든 파이프라인 리소스를 수용하는 [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만듭니다.

* Azure Machine Learning SDK를 설치하거나 SDK가 이미 설치된 [Azure Machine Learning 컴퓨팅 인스턴스](concept-compute-instance.md)를 사용하도록 [개발 환경을 구성](how-to-configure-environment.md)합니다.

* Azure Synapse Analytics 작업 영역 및 Apache Spark 풀을 만듭니다([빠른 시작: Synapse Studio를 사용하여 서버리스 Apache Spark 풀 만들기](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md) 참조). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Azure Machine Learning 작업 영역과 Azure Synapse Analytics 작업 영역 연결 

Azure Synapse Analytics 작업 영역에서 Apache Spark 풀을 만들고 관리합니다. Apache Spark 풀을 Azure Machine Learning 작업 영역과 통합하려면 [Azure Synapse Analytics 작업 영역에 연결](how-to-link-synapse-ml-workspaces.md)해야 합니다. 

Azure Machine Learning 작업 영역과 Azure Synapse Analytics 작업 영역이 연결되면 다음을 통해 Apache Spark 풀을 연결할 수 있습니다. 
* [Azure Machine Learning Studio](how-to-link-synapse-ml-workspaces.md#attach-a-pool-via-the-studio)
* Python SDK([아래에 자세히 설명](#attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning))
* ARM(Azure Resource Manager) 템플릿(이 [ARM 템플릿 예제](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json) 참조) 
    * 명령줄을 사용하여 ARM 템플릿을 따르고, 연결된 서비스를 추가하고, 다음 코드를 사용하여 Apache Spark 풀을 연결할 수 있습니다.
    ```azurecli
    az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
    ```

> [!Important]
> Azure Synapse Analytics 작업 영역에 성공적으로 연결하려면 Azure Synapse Analytics 작업 영역 리소스에 소유자 역할이 있어야 합니다. Azure Portal에서 액세스를 확인합니다.
>
> 연결된 서비스는 만들 때 SAI(System Assigned Identity)를 가져옵니다. Spark 작업을 제출할 수 있도록 이 연결 서비스 SAI를 Synapse Studio의 "Synapse Apache Spark 관리자" 역할에 할당해야 합니다([Synapse Studio에서 Synapse RBAC 역할 할당을 관리하는 방법](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md) 참조). 
> 
> 또한 Azure Machine Learning 작업 영역의 사용자에게 리소스 관리의 Azure Portal에서 "Contributor" 역할을 부여해야 합니다.

## <a name="retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Azure Synapse Analytics 작업 영역과 Azure Machine Learning 작업 영역 간의 링크를 검색합니다.

다음과 같은 코드를 사용하여 작업 영역에서 연결된 서비스를 검색할 수 있습니다.

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

먼저 `Workspace.from_config()`는 `config.json`에서 구성을 사용하여 Azure Machine Learning 작업 영역에 액세스합니다([작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace) 참조). 그런 다음, 코드는 작업 영역에서 사용할 수 있는 모든 연결된 서비스를 인쇄합니다. 마지막으로 `LinkedService.get()`이 `'synapselink1'`이라는 연결된 서비스를 검색합니다. 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Azure Machine Learning에 대한 컴퓨팅 대상으로 Apache Spark 풀 연결

Apache Spark 풀을 사용하여 기계 학습 파이프라인의 단계를 수행하려면 다음 코드에 표시된 대로 파이프라인 단계에 대한 `ComputeTarget`으로 연결해야 합니다.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

첫 번째 단계는 `SynapseCompute`를 만드는 것입니다. `linked_service` 인수는 이전 단계에서 만들었거나 검색한 `LinkedService` 개체입니다. `type` 인수는 `SynapseSpark`여야 합니다. `SynapseCompute.attach_configuration()`의 `pool_name` 인수는 Azure Synapse Analytics 작업 영역에 있는 기존 풀의 인수와 일치해야 합니다. Azure Synapse Analytics 작업 영역에서 Apache Spark 풀을 만드는 방법에 대한 자세한 내용은 [빠른 시작: Synapse Studio를 사용하여 서버리스 Apache Spark 풀 만들기](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)를 참조하세요. `attach_config`의 형식은 `ComputeTargetAttachConfiguration`입니다.

구성이 만들어지면 `Workspace`, `ComputeTargetAttachConfiguration` 및 기계 학습 작업 영역에서 계산을 참조할 이름을 전달하여 기계 학습 `ComputeTarget`을 생성합니다. `ComputeTarget.attach()`에 대한 호출은 비동기이므로 호출이 완료될 때까지 샘플이 차단됩니다.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>연결된 Apache Spark 풀을 사용하는 `SynapseSparkStep` 만들기

[Apache Spark 풀의 샘플 Notebook Spark 작업](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb)은 간단한 기계 학습 파이프라인을 정의합니다. 먼저 노트북은 이전 단계에서 정의된 `synapse_compute`에서 제공하는 데이터 준비 단계를 정의합니다. 그런 다음, 학습에 더 적합한 컴퓨팅 대상에서 제공하는 학습 단계를 정의합니다. 샘플 노트북은 Titanic 생존 데이터베이스를 사용하여 데이터 입력 및 출력을 보여 줍니다. 실제로 데이터를 정리하거나 예측 모델을 만들지 않습니다. 이 샘플에 대한 실제 학습은 없으므로 학습 단계에서는 저렴한 CPU 기반 컴퓨팅 리소스를 사용합니다.

데이터는 표 형식의 데이터 또는 파일 집합을 포함할 수 있는 `DatasetConsumptionConfig`개의 개체를 통해 기계 학습 파이프라인으로 흐릅니다. 데이터는 종종 작업 영역의 데이터 저장소에 있는 Blob Storage의 파일에서 제공됩니다. 다음 코드는 기계 학습 파이프라인에 대한 입력을 만들기 위한 몇 가지 일반적인 코드를 보여 줍니다.

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

위의 코드는 Blob Storage에 `Titanic.csv` 파일이 있다고 가정합니다. 이 코드에서는 `TabularDataset` 및 `FileDataset`로 파일을 읽는 방법을 보여 줍니다. 이 코드는 입력값을 복제하거나 단일 데이터 원본을 테이블 포함 리소스와 파일 모두로 해석하는 데 혼동을 줄 수 있기 때문에 데모용으로만 사용됩니다.

> [!Important]
> `FileDataset`를 입력으로 사용하려면 `azureml-core` 버전이 `1.20.0` 이상이어야 합니다. `Environment` 클래스를 사용하여 이를 지정하는 방법은 아래에 설명되어 있습니다.

단계가 완료되면 다음과 유사한 코드를 사용하여 출력 데이터를 저장하도록 선택할 수 있습니다.

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

이 경우 데이터는 `test`라는 파일의 `datastore`에 저장되며, 이름이 `registered_dataset`인 `Dataset`로 기계 학습 작업 영역 내에서 사용할 수 있습니다.

데이터 외에도 파이프라인 단계는 단계별로 Python 종속성을 가질 수 있습니다. 개별 `SynapseSparkStep` 개체는 정확한 Azure Synapse Apache Spark 구성도 지정할 수 있습니다. 이는 `azureml-core` 패키지 버전이 `1.20.0` 이상이어야 함을 지정하는 다음 코드에 나와 있습니다. (앞서 설명한 대로 `FileDataset`를 입력으로 사용하려면 `azureml-core`에 대한이 요구 사항이 필요합니다.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

위의 코드는 Azure 기계 학습 파이프라인에서 단일 단계를 지정합니다. 이 단계의 `environment`는 특정 `azureml-core` 버전을 지정하고 필요에 따라 다른 Conda 또는 pip 종속성을 추가할 수 있습니다.

`SynapseSparkStep`은 로컬 컴퓨터에서 하위 디렉터리 `./code`를 압축하고 업로드합니다. 컴퓨팅 서버에서 해당 디렉터리가 다시 만들어지고 단계에서 해당 디렉터리의 `dataprep.py` 파일을 실행합니다. 이 단계의 `inputs` 및 `outputs`는 앞서 설명한 `step1_input1`, `step1_input2` 및 `step1_output` 개체입니다. `dataprep.py` 스크립트 내에서 이러한 값에 액세스하는 가장 쉬운 방법은 명명된 `arguments`와 연결하는 것입니다.

`SynapseSparkStep` 생성자에 대한 다음 인수 집합은 Apache Spark를 제어합니다. `compute_target`은 이전에 컴퓨팅 대상으로 연결한 `'link1-spark01'`입니다. 다른 매개 변수는 사용하고자 하는 메모리 및 코어를 지정합니다.

샘플 노트북은 `dataprep.py`에 대해 다음 코드를 사용합니다.

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

이 "데이터 준비" 스크립트는 실제 데이터 변환을 수행하지는 않지만 데이터를 검색하고, Spark 데이터 프레임으로 변환하고, 몇 가지 기본적인 Apache Spark 조작을 수행하는 방법을 보여 줍니다. 다음 그림에 표시된 대로 자식 실행을 열고 **출력 + 로그** 탭을 선택하고 `logs/azureml/driver/stdout` 파일을 열어 Azure Machine Learning Studio의 출력을 찾을 수 있습니다.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="자식 실행의 stdout 탭을 보여 주는 Studio의 스크린샷":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>파이프라인에서 `SynapseSparkStep` 사용

다음 예제에서는 [이전 섹션](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool)에서 만든 `SynapseSparkStep`의 출력을 사용합니다. 파이프라인의 다른 단계는 자신의 고유한 환경을 포함하고 현재 작업에 적합한 서로 다른 컴퓨팅 리소스에서 실행됩니다. 샘플 노트북은 작은 CPU 클러스터에서 "학습 단계"를 실행합니다.

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

위의 코드는 필요한 경우 새 컴퓨팅 리소스를 만듭니다. 그런 다음 `step1_output` 결과가 학습 단계에 대한 입력으로 변환됩니다. `as_download()` 옵션은 데이터를 컴퓨팅 리소스로 이동하여 더 빠르게 액세스할 수 있음을 의미합니다. 데이터가 너무 커서 로컬 컴퓨팅 하드 드라이브에 맞지 않는 경우 `as_mount()` 옵션을 사용하여 FUSE 파일 시스템을 통해 데이터를 스트리밍할 수 있습니다. 이 두 번째 단계의 `compute_target`은 데이터 준비 단계에서 사용한 `'link1-spark01'` 리소스가 아니라 `'cpucluster'`입니다. 이 단계에서는 이전 단계에서 사용한 `dataprep.py` 대신 간단한 프로그램인 `train.py`를 사용합니다. 샘플 노트북에서 `train.py`의 세부 정보를 볼 수 있습니다.

모든 단계를 정의한 후 파이프라인을 만들고 실행할 수 있습니다. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

위의 코드는 Azure Synapse Analytics(`step_1`) 및 학습 단계(`step_2`)에 의해 제공되는 Apache Spark 풀의 데이터 준비 단계로 구성된 파이프라인을 만듭니다. Azure는 단계 간의 데이터 종속성을 검사하여 실행 그래프를 계산합니다. 이 경우 `step2_input`에 반드시 `step1_output`이 필요한 간단한 종속성만 있습니다.

필요한 경우 `pipeline.submit`에 대한 호출은 `synapse-pipeline`이라는 실험을 생성하고 그 안에서 실행을 비동기적으로 시작합니다. 파이프라인 내의 개별 단계는 이 기본 실행의 자식 실행으로 실행되며 Studio의 실험 페이지에서 모니터링 및 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [기계 학습 파이프라인 게시 및 추적](how-to-deploy-pipelines.md) 
* [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)
* [Python의 Azure Machine Learning 파이프라인에서 자동화된 ML 사용](how-to-use-automlstep-in-pipelines.md)
