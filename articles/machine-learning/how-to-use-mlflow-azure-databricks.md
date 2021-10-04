---
title: Azure Databricks ML 실험에 대한 MLflow 추적
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 mlflow를 설정 하 여 Azure Databricks ML 실험에서 메트릭 및 아티팩트를 로깅합니다.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 62e87c2c63ade98be48977e7a8377ad66530af5d
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428828"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning"></a>MLflow 및 Azure Machine Learning을 사용하여 Azure Databricks ML 실험 추적

이 문서에서는 MLflow의 추적 URI 및 로깅 API(총칭하여 [MLflow 추적](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api))를 사용하여 ADB(Azure Databricks) 실험, MLflow 및 Azure Machine Learning에 연결하는 방법을 알아봅니다.

[MLflow](https://www.mlflow.org)는 기계 학습 실험의 수명 주기를 관리하기 위한 오픈 소스 라이브러리입니다. MLFlow 추적은 학습 실행 메트릭 및 모델 아티팩트를 로깅하고 추적하는 MLflow의 구성 요소입니다. [Azure Databricks 및 MLflow](/azure/databricks/applications/mlflow/)에 대해 자세히 알아봅니다. 

추가 MLflow 및 Azure Machine Learning 기능 통합에 대해 [MLflow 및 Azure Machine Learning](concept-mlflow.md)을 참조하세요.

Azure Machine Learning을 통해 학습할 MLflow 프로젝트가 있는 경우 [MLflow 프로젝트 및 Azure Machine Learning을 사용하여 ML 모델 학습](how-to-train-mlflow-projects.md)을 참조하세요.

> [!TIP]
> 이 문서의 정보는 주로 모델 학습 프로세스를 모니터링하려는 데이터 과학자와 개발자를 위한 것입니다. 할당량, 완료된 학습 실행 또는 완료된 모델 배포와 같이 Azure Machine Learning의 리소스 사용과 이벤트를 모니터링하는 데 관심이 있는 관리자는 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md)을 참조하세요.

## <a name="prerequisites"></a>전제 조건

* `azureml-mlflow` 패키지를 설치합니다. 
    * 이 패키지는 작업 영역에 액세스하기 위해 MLflow에 대한 연결을 제공하는 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install)의 `azureml-core`를 자동으로 가져옵니다.
* [Azure Databricks 작업 영역 및 클러스터](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)
* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).
    * [작업 영역을 사용하여 MLflow 작업을 수행하는 데 필요한 액세스 권한](how-to-assign-roles.md#mlflow-operations)을 확인하세요.

## <a name="track-azure-databricks-runs"></a>Azure Databricks 실행 추적

MLflow 추적을 Azure Machine Learning과 함께 사용하면 Azure Databricks 실행의 로깅된 메트릭 및 아티팩트를 다음에 저장할 수 있습니다. 

* Azure Databricks 작업 영역.
* Azure Machine Learning 작업 영역

Azure Databricks 작업 영역 및 클러스터를 만든 후 

1. PyPi로부터 *azureml-mlflow* 라이브러리를 설치하여 클러스터가 필요한 함수 및 클래스에 대한 액세스 권한이 있는지 확인합니다.

1. 실험 Notebook을 설정합니다.

1. Azure Databricks 작업 영역 및 Azure Machine Learning 작업 영역에 연결합니다.

이러한 단계에 대한 추가 세부 정보는 다음 섹션에서 설명하므로 Azure Databricks를 사용하여 MLflow 실험을 성공적으로 실행할 수 있습니다. 

## <a name="install-libraries"></a>라이브러리 설치

클러스터에 라이브러리를 설치하려면 **라이브러리** 탭으로 이동하여 **새로 설치** 를 선택합니다.

 ![Azure Databricks를 사용한 MLflow](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

**패키지** 필드에 azureml-mlflow를 입력한 다음, 설치를 선택합니다. 필요에 따라 이 단계를 반복하여 실험을 위한 클러스터에 다른 추가 패키지를 설치합니다.

 ![Azure DB 설치 mlflow 라이브러리](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Notebook 설정 

ADB 클러스터를 설정하면 
1. 왼쪽 탐색 창에서 **작업 영역** 을 선택합니다. 
1. 작업 영역 드롭다운 메뉴를 확장하고 **가져오기** 를 선택합니다.
1. ADB 작업 영역을 가져오려면 실험 Notebook을 끌어서 놓거나 찾아봅니다.
1. **가져오기** 를 선택합니다. 실험 Notebook이 자동으로 열립니다.
1. 왼쪽 상단의 Notebook 제목 아래에서 실험 Notebook에 연결하려는 클러스터를 선택합니다. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Azure Databricks 및 Azure Machine Learning 작업 영역 연결

ADB 작업 영역을 Azure Machine Learning 작업 영역에 연결하면 Azure Machine Learning 작업 영역에서 실험 데이터를 추적할 수 있습니다.

ADB 작업 영역을 신규 또는 기존 Azure Machine Learning 작업 영역에 연결하려면 
1. [Azure 포털](https://ms.portal.azure.com)에 로그인합니다.
1. ADB 작업 영역의 **개요** 페이지로 이동합니다.
1. 오른쪽 아래에 있는 **Azure Machine Learning 작업 영역 연결** 단추를 선택합니다. 

 ![Azure DB 및 Azure Machine Learning 작업 영역 연결](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>작업 영역의 MLflow 추적

작업 영역을 인스턴스화한 후에는 다음 모든 위치에서 MLflow 추적이 추적되도록 자동으로 설정됩니다.

* 연결된 Azure Machine Learning 작업 영역
* 원래 ADB 작업 영역 

모든 실험은 관리형 Azure Machine Learning 추적 서비스에 배치됩니다.

다음 코드는 연결된 Azure Machine Learning 작업 영역을 가져오기 위해 실험 Notebook에 있어야 합니다. 

이 코드에서 다음을 수행합니다. 

*  Azure Machine Learning 작업 영역을 인스턴스화하기 위해 Azure 구독에 대한 세부 정보를 가져옵니다. 

* 기존 리소스 그룹 및 Azure Machine Learning 작업 영역을 보유하고 있는 것으로 가정합니다. 그렇지 않으면 [만들 수 있습니다](how-to-manage-workspace.md). 

* 실험 이름을 설정합니다. `user_name` 여기서는 Azure Databricks 작업 영역과 연결된 `user_name`과 일치합니다.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에서만 추적하도록 MLflow 추적 설정

중앙 위치에서 추적된 실험을 관리하려는 경우 Azure Machine Learning 작업 영역 **에서만** 추적하도록 MLflow 추적을 설정할 수 있습니다. 

스크립트에 다음 코드를 포함합니다.

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

학습 스크립트에서 `mlflow`를 가져와 MLflow 로깅 API를 사용하고 실행 메트릭 로깅을 시작합니다. 다음 예에서는 Epoch 손실 메트릭을 로깅합니다. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>MLflow를 사용하여 모델 등록

모델을 학습한 후에는 `mlflow.<model_flavor>.log_model()` 메서드를 사용하여 백 엔드 추적 서버에 모델을 로깅하고 등록할 수 있습니다. `<model_flavor>`는 모델과 연결된 프레임워크를 나타냅니다. [지원되는 모델 종류에 대해 알아봅니다](https://mlflow.org/docs/latest/models.html#model-api). 

백 엔드 추적 서버는 기본적으로 Azure Databricks 작업 영역입니다. [Azure Machine Learning 작업 영역에서만 추적하도록 MLflow 추적 설정](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace)을 선택하지 않은 경우 백 엔드 추적 서버는 Azure Machine Learning 작업 영역입니다.   

* **이름이 있는 등록 모델이 없는 경우** 이 메서드는 새 모델을 등록하고, 버전 1을 만들고, ModelVersion MLflow 개체를 반환합니다. 

* **이름이 있는 등록 모델이 이미 있는 경우** 이 메서드는 새 모델 버전을 만들고 버전 개체를 반환합니다. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>MLflow 모델에 대한 엔드포인트 만들기

ML 모델에 대한 엔드포인트를 만들 준비가 된 경우 다음으로 배포할 수 있습니다. 

* 대화형 점수 매기기를 위한 Azure Machine Learning 요청-응답 웹 서비스입니다. 이 배포를 통해 Azure Machine Learning 모델 관리 및 데이터 드리프트 검색 기능을 활용하여 프로덕션 모델에 적용할 수 있습니다. 

* MLFlow 모델 개체는 스트리밍 또는 일괄 처리 파이프라인에서 Python 함수로 사용하거나 Azure Databricks 작업 영역에서 Pandas UDF로 사용할 수 있습니다.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Azure Machine Learning 엔드포인트에 모델 배포 
[mlflow.azureml.deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API를 활용하여 Azure Machine Learning 작업 영역에 모델을 배포할 수 있습니다. [MLflow를 사용하여 모델 등록](#register-models-with-mlflow) 섹션에 설명된 대로 Azure Databricks 작업 영역에만 모델을 등록한 경우 `model_name` 매개 변수를 지정하여 모델을 Azure Machine Learning 작업 영역에 등록합니다. 

다음 엔드포인트에 Azure Databricks 실행을 배포할 수 있습니다. 
* [Azure Container Instance](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>일괄 처리 점수 매기기를 위해 ADB 엔드포인트에 모델 배포 

일괄 처리 점수 매기기를 위해 Azure Databricks 클러스터를 선택할 수 있습니다. MLFlow 모델은 로드되어 새 데이터의 점수를 매기는 Spark Pandas UDF로 사용됩니다. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>리소스 정리

Azure Databricks 작업 영역을 유지 하지만 azure ML 작업 영역이 더 이상 필요 하지 않은 경우 azure ML 작업 영역을 삭제할 수 있습니다. 이 작업을 수행 하면 Azure Databricks 작업 영역 및 Azure ML 작업 영역 연결 해제가 발생 합니다. 

작업 영역에서 기록 된 메트릭과 아티팩트를 사용 하지 않으려는 경우에는이를 개별적으로 삭제 하는 기능을 사용할 수 없습니다. 대신, 스토리지 계정 및 작업 영역을 포함하는 리소스 그룹을 삭제하여 요금이 발생하지 않도록 합니다.

1. Azure Portal의 맨 왼쪽에서 **리소스 그룹** 을 선택합니다.

   ![Azure Portal에서 삭제](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. 목록에서 만든 리소스 그룹을 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

## <a name="example-notebooks"></a>노트북 예제

[Azure Machine Learning Notebook을 사용한 MLflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)는 이 문서에 나와 있는 개념을 시연하고 확장합니다.

## <a name="next-steps"></a>다음 단계
* [MLflow 모델을 Azure 웹 서비스로 배포](how-to-deploy-mlflow-models.md) 
* [모델 관리](concept-model-management-and-deployment.md).
* [MLflow 및 Azure Machine Learning을 사용하여 실험 실행 추적](how-to-use-mlflow.md) 
* [Azure Databricks 및 MLflow](/azure/databricks/applications/mlflow/)에 대해 자세히 알아봅니다.
