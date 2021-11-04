---
title: Azure Data Factory로 데이터 수집
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure Data Factory를 사용하여 데이터 수집 파이프라인을 빌드하는 데 사용할 수 있는 옵션과 각 옵션의 이점에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 3e608ffa1818ceef173336344547601ba172b609
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563077"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Azure Data Factory로 데이터 수집

이 문서에서는 [Azure Data Factory](../data-factory/introduction.md)를 사용하여 데이터 수집 파이프라인을 빌드하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이 Azure Data Factory 파이프라인은 [Azure Machine Learning](overview-what-is-azure-machine-learning.md)에 사용할 데이터를 수집하는 데 사용됩니다. Data Factory를 사용하면 (ETL) 데이터를 손쉽게 추출, 변환 및 로드할 수 있습니다. 데이터가 변환되어 스토리지에 로드되면 Azure Machine Learning에서 기계 학습 모델을 학습시키는 데 사용할 수 있습니다.

간단한 데이터 변환은 [데이터 흐름](../data-factory/control-flow-execute-data-flow-activity.md)과 같은 기본 Data Factory 활동 및 계측으로 처리할 수 있습니다. 더욱 복잡한 시나리오에서는 일부 사용자 지정 코드로 데이터를 처리할 수 있습니다. 예를 들어 Python 또는 R 코드를 사용할 수 있습니다.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Azure Data Factory 데이터 수집 파이프라인 비교 
수집 중에 데이터를 변환하기 위해 Data Factory를 사용하는 몇 가지 일반적인 기술이 있습니다. 각 기술에는 장단점이 있어 특정 사용 사례에 적합한 지 판단하는 데 도움이 됩니다.

| 방법 | 장점 | 단점 |
| ----- | ----- | ----- |
| Data Factory + Azure Functions | <li> 짧은 대기 시간, 서버리스 컴퓨팅<li>상태 저장 함수<li>재사용 가능 함수 | 단기 실행 처리에만 적합 |
| Data Factory + 사용자 지정 구성 요소 | <li>대규모 병렬 컴퓨팅<li>무거운 알고리즘에 적합 | <li>코드를 실행 파일로 래핑해야 함<li>종속성 및 IO 처리의 복잡성 |
| Data Factory + Azure Databricks Notebook |<li> Apache Spark<li>네이티브 Python 환경 |<li>비용이 많이 들 수 있음<li>처음에 클러스터를 만드는 데 시간이 걸리고 대기 시간이 추가됨

## <a name="azure-data-factory-with-azure-functions"></a>Azure Functions와 Azure Data Factory

Azure Functions를 사용하면 애플리케이션 인프라에 대한 걱정 없이 작은 코드(함수)를 실행할 수 있습니다. 이 옵션에서는 데이터가 Azure Function으로 래핑된 사용자 지정 Python 코드로 처리됩니다. 

이 함수는 [Azure Data Factory Azure Function 활동](../data-factory/control-flow-azure-function-activity.md)을 통해 호출됩니다. 이 접근 방식은 가벼운 데이터 변환에 적합한 옵션입니다. 

![이 다이어그램은 Azure Function 및 Run ML Pipeline을 함께 사용하는 Azure Data Factory 파이프라인과 Train Model을 함께 사용하는 Azure Machine Learning 파이프라인을 보여주고, 이들에서 원시 데이터 및 준비된 데이터와 상호 작용하는 방법을 보여줍니다.](media/how-to-data-ingest-adf/adf-function.png)



* 장점
    * 데이터가 서버리스 컴퓨팅으로 처리되며, 대기 시간이 비교적 짧습니다.
    * Data Factory 파이프라인은 정교한 데이터 변환 흐름을 구현하는 [지속형 Azure Function](../azure-functions/durable/durable-functions-overview.md)을 호출할 수 있습니다. 
    * 데이터 변환에 대한 세부 정보는 Azure Function에 의해 추상화되며, 이 함수는 다른 곳에서 재사용 및 호출할 수 있습니다.
* 단점:
    * ADF와 함께 사용하기 전에 먼저 Azure Functions를 만들어야 합니다.
    * Azure Functions는 단기 실행 데이터 처리에만 적합합니다.

## <a name="azure-data-factory-with-custom-component-activity"></a>사용자 지정 구성 요소 활동과 Azure Data Factory

이 옵션에서는 데이터가 실행 파일로 래핑된 사용자 지정 Python 코드로 처리됩니다. [Azure Data Factory 사용자 지정 구성 요소 활동](../data-factory/transform-data-using-dotnet-custom-activity.md)을 통해 호출됩니다. 이 접근 방식은 이전 기술보다 대용량 데이터에 더 적합합니다.

![이 다이어그램은 사용자 지정 구성 요소 및 Run ML Pipeline을 함께 사용하는 Azure Data Factory 파이프라인과 Train Model을 함께 사용하는 Azure Machine Learning 파이프라인을 보여주고, 이들에서 원시 데이터 및 준비된 데이터와 상호 작용하는 방법을 보여줍니다.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* 장점
    * 데이터가 대규모 병렬 및 고성능 컴퓨팅을 제공하는 [Azure Batch](../batch/batch-technical-overview.md) 풀에서 처리됩니다.
    * 무거운 알고리즘을 실행하고 상당량의 데이터를 처리하는 데 사용 가능합니다.
* 단점
    * Data Factory와 함께 사용하기 전에 먼저 Azure Batch 풀을 만들어야 합니다.
    * Python 코드를 실행 파일로 래핑하는 것과 관련된 과도한 엔지니어링. 종속성 및 입력/출력 매개 변수 처리의 복잡성

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Databricks Python Notebook과 Azure Data Factory

[Azure Databricks](https://azure.microsoft.com/services/databricks/)는 Microsoft 클라우드의 Apache Spark 기반 분석 플랫폼입니다.

이 기술에서 데이터 변환은 Azure Databricks 클러스터에서 실행되는 [Python Notebook](../data-factory/transform-data-using-databricks-notebook.md)에 의해 수행됩니다. 이 기술은 Azure Databricks 서비스의 모든 기능을 활용하는 가장 일반적인 접근 방식일 것입니다. 대규모 분산 데이터 처리를 위해 설계되었습니다.

![이 다이어그램은 Azure Databricks Python 및 Run ML Pipeline을 함께 사용하는 Azure Data Factory 파이프라인과 Train Model을 함께 사용하는 Azure Machine Learning 파이프라인을 보여주고, 이들에서 원시 데이터 및 준비된 데이터와 상호 작용하는 방법을 보여줍니다.](media/how-to-data-ingest-adf/adf-databricks.png)

* 장점
    * 데이터가 가장 강력한 데이터 처리 Azure 서비스에서 변환되며 Apache Spark 환경에서 백업됩니다.
    * TensorFlow, PyTorch 및 scikit-learn을 비롯해 데이터 과학 프레임워크 및 라이브러리와 함께 Python을 기본 지원합니다.
    * Python 코드를 함수나 실행 가능한 모듈로 래핑할 필요가 없습니다. 코드가 있는 그대로 작동합니다.
* 단점
    * Data Factory와 함께 사용하기 전에 먼저 Azure Databricks 인프라를 만들어야 합니다.
    * Azure Databricks 구성에 따라 비용이 많이 들 수 있습니다.
    * "콜드" 모드에서 컴퓨팅 클러스터를 로드하는 데 시간이 걸리므로 솔루션에 높은 대기 시간이 발생합니다. 
    

## <a name="consume-data-in-azure-machine-learning"></a>Azure Machine Learning에서 데이터 사용 

Data Factory 파이프라인은 준비된 데이터를 클라우드 스토리지(예: Azure Blob 또는 Azure Datalake)에 저장합니다. <br>
다음을 통해 Azure Machine Learning에서 준비된 데이터를 사용합니다. 

* Data Factory 파이프라인에서 Azure Machine Learning 파이프라인 호출.<br>**OR**
* 나중에 사용할 수 있도록 [Azure Machine Learning 데이터 저장소](how-to-access-data.md#create-and-register-datastores) 및 [Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md) 만들기.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Data Factory에서 Azure Machine Learning 파이프라인 호출

이 방법은 [MLOps(Machine Learning Operations) 워크플로](concept-model-management-and-deployment.md#what-is-mlops)에 권장됩니다. Azure Machine Learning 파이프라인을 설정하지 않으려면 [스토리지에서 직접 데이터 읽기](#read-data-directly-from-storage)를 참조하세요.

Data Factory 파이프라인이 실행될 때마다 다음 작업이 수행됩니다. 

1. 데이터가 스토리지의 다른 위치에 저장됩니다. 
1. 이 위치를 Azure Machine Learning에 전달하기 위해 Data Factory 파이프라인은 [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)을 호출합니다. ML 파이프라인을 호출할 때 이 데이터 위치와 실행 ID가 매개 변수로 전송됩니다. 
1. 그런 후 ML 파이프라인은 이 데이터 위치를 사용하여 Azure Machine Learning 데이터 저장소 및 데이터 세트를 만들 수 있습니다. [Data Factory에서 Azure Machine Learning 파이프라인 실행](../data-factory/transform-data-machine-learning-service.md)에서 자세히 알아보세요.

![이 다이어그램은 Azure Data Factory 파이프라인과 Azure Machine Learning 파이프라인을 보여주고, 이들에서 원시 데이터 및 준비된 데이터와 상호 작용하는 방법을 보여줍니다. Data Factory 파이프라인은 데이터를 준비된 데이터 데이터베이스에 공급하고, 준비된 데이터 데이터베이스는 데이터 저장소를 공급하며, 데이터 저장소는 Machine Learning 작업 영역에서 데이터 세트를 공급합니다.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> 데이터 세트는 [버전 관리를 지원](./how-to-version-track-datasets.md)하므로 ML 파이프라인이 ADF 파이프라인의 최신 데이터를 가리키는 새 버전의 데이터 세트를 등록할 수 있습니다.

데이터 저장소 또는 데이터 세트를 통해 데이터에 액세스할 수 있게 되면 이를 사용하여 ML 모델을 학습시킬 수 있습니다. 학습 프로세스는 ADF에서 호출되는 동일한 ML 파이프라인의 일부일 수 있습니다. 또는 Jupyter Notebook에서의 실험과 같은 별도의 프로세스일 수 있습니다.

데이터 세트는 버전 관리를 지원하고 파이프라인에서 실행될 때마다 새 버전이 생성되므로 모델 학습에 사용된 데이터 버전이 어느 것인지 손쉽게 파악할 수 있습니다.

### <a name="read-data-directly-from-storage"></a>스토리지에서 직접 데이터 읽기

ML 파이프라인을 만들지 않으려는 경우 준비된 데이터가 Azure Machine Learning 데이터 저장소 및 데이터 세트와 함께 저장되는 스토리지 계정에서 직접 데이터에 액세스할 수 있습니다. 

다음 Python 코드는 Azure DataLake 2세대 스토리지에 연결되는 데이터 저장소를 만드는 방법을 보여줍니다. [데이터 저장소 및 서비스 주체 권한을 확인할 수 있는 위치에 대해 자세히 알아보세요](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

다음으로 기계 학습 작업에 사용할 파일을 참조하는 데이터 세트를 만듭니다. 

다음 코드는 csv 파일인 `prepared-data.csv`에서 TabularDataset를 만듭니다. [데이터 세트 형식 및 허용되는 파일 형식](how-to-create-register-datasets.md#dataset-types)에 대해 자세히 알아보세요. 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

여기서 `prepared_dataset`를 사용하여 학습 스크립트에서와 같이 준비된 데이터를 참조합니다. [Azure Machine Learning에서 데이터 세트로 모델을 학습](./how-to-train-with-datasets.md)시키는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory에서 Databricks Notebook 실행](../data-factory/transform-data-using-databricks-notebook.md)
* [Azure Storage 서비스에서 데이터 액세스](./how-to-access-data.md#create-and-register-datastores)
* [Azure Machine Learning에서 데이터 세트를 사용하여 모델 학습](./how-to-train-with-datasets.md).
* [데이터 수집 파이프라인에 대한 DevOps](./how-to-cicd-data-ingestion.md)