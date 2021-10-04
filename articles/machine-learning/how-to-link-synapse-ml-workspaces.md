---
title: Synapse 및 Azure Machine Learning 작업 영역을 사용하여 연결된 서비스 만들기(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Synapse 및 Azure Machine Learning 작업 영역을 연결하고 통합 데이터 랭글링 환경을 위해 Apache Spark 풀을 연결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: devx-track-python, data4ml, synapse-azureml, contperf-fy21q4
ms.openlocfilehash: 812475b1a3b3029971dcc440ca6e26af95fdd389
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425357"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-and-attach-apache-spark-poolspreview"></a>Azure Synapse Analytics 및 Azure Machine Learning 작업 영역을 연결하고 Apache Spark 풀 연결(미리 보기)

이 문서에서는 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 작업 영역 및 [Azure Machine Learning 작업 영역](concept-workspace.md)을 연결하는 연결된 서비스를 만드는 방법을 알아봅니다. 

Azure Synapse 작업 영역과 연결된 Azure Machine Learning 작업 영역을 사용하면 Azure Synapse Analytics에서 제공하는 Apache Spark 풀을 규모에 맞게 데이터 랭글링 전용 컴퓨팅으로 연결하거나 동일한 Python Notebook에서 모두 모델 학습을 수행할 수 있습니다.

[Python SDK](#link-sdk) 또는 [Azure Machine Learning 스튜디오](#link-studio)를 통해 ML 작업 영역 및 Synapse 작업 영역을 연결할 수 있습니다.

작업 영역을 연결하고 단일 [Azure Resource Manager(ARM) 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json)을 사용하여 Synapse Spark 풀을 연결할 수도 있습니다.

>[!IMPORTANT]
> Azure Machine Learning 및 Azure Synapse Analytics 통합은 공개 미리 보기 상태입니다. `azureml-synapse` 패키지에서 제공되는 기능은 [실험적](/python/api/overview/azure/ml/#stable-vs-experimental) 미리 보기 기능이며 언제든지 변경할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md?tabs=python).

* [Azure Portal에서 Synapse 작업 영역을 만듭니다.](../synapse-analytics/quickstart-create-workspace.md)

* [Azure Portal, 웹 도구 또는 Synapse Studio를 사용하여 Apache Spark 풀을 만듭니다.](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)

* [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)를 설치합니다.

* [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 액세스합니다.

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Python SDK를 사용하여 작업 영역 연결

> [!IMPORTANT]
> Synapse 작업 영역에 성공적으로 연결하려면 Synapse 작업 영역의 **소유자** 역할을 부여해야 합니다. [Azure Portal](https://ms.portal.azure.com/)에서 액세스를 확인합니다.
>
> **소유자** 가 아니고 Synapse 작업 영역에 대한 **기여자** 인 경우에는 기존 연결된 서비스만 사용할 수 있습니다. [기존 연결된 서비스를 검색하고 사용](#get-an-existing-linked-service)하는 방법을 참조하세요.

다음 코드는 [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) 및 [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) 클래스를 채택합니다.

* Azure Synapse 작업 영역을 사용하여 기계 학습 작업 영역인 `ws`를 연결합니다.
* Azure Machine Learning에 연결된 서비스로 Synapse 작업 영역을 등록합니다.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> 각 연결된 서비스에 대해 관리 ID인 `system_assigned_identity_principal_id`가 생성됩니다. Synapse 세션을 시작하기 전에 이 관리 ID에 Synapse 작업 영역의 **Synapse Apache Spark 관리자** 역할을 부여해야 합니다. [Synapse Studio에서 관리 ID에 Synapse Apache Spark 관리자 역할을 할당합니다](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> 특정 연결된 서비스의 `system_assigned_identity_principal_id`를 찾으려면 `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')`을 사용합니다.

### <a name="manage-linked-services"></a>연결된 서비스 관리

기계 학습 작업 영역과 연결된 모든 연결된 서비스를 봅니다.

```python
LinkedService.list(ws)
```

작업 영역 연결을 해제하려면 `unregister()` 메서드를 사용합니다.

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Studio를 통해 작업 영역 연결

다음 단계를 사용하여 Azure Machine Learning 스튜디오를 통해 기계 학습 작업 영역 및 Synapse 작업 영역을 연결합니다. 

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인합니다.
1. 왼쪽 창의 **관리** 섹션에서 **연결된 서비스** 를 선택합니다.
1. **통합 추가** 를 선택합니다.
1. **작업 영역 연결** 양식에서 필드를 작성합니다.

    |필드| Description    
    |---|---
    |이름| 연결된 서비스의 이름을 제공합니다. 이 이름은 이 특정 연결된 서비스를 참조하는 데 사용됩니다.
    |구독 이름 | 기계 학습 작업 영역과 연결된 구독의 이름을 선택합니다. 
    |Synapse 작업 영역 | 연결하려는 Synapse 작업 영역을 선택합니다.
    
1. **다음** 을 선택하여 **Spark 풀 선택(선택 사항)** 양식을 엽니다. 이 양식에서 작업 영역에 연결할 Synapse Spark 풀을 선택합니다.

1. **다음** 을 선택하여 **검토** 양식을 열고 선택 내용을 확인합니다.
1. **만들기** 를 선택하여 연결된 서비스 만들기 프로세스를 완료합니다.

## <a name="get-an-existing-linked-service"></a>연결된 기존 서비스 가져오기

데이터 랭글링을 위한 전용 컴퓨팅을 연결하기 전에 Azure Synapse Analytics 작업 영역에 연결된 ML 작업 영역이 있어야 합니다. 이것을 연결된 서비스라 합니다. 

연결된 기존 서비스를 검색 및 사용하려면 **Azure Synapse Analytics 작업 영역** 에 대한 **사용자 또는 기여자** 권한이 필요합니다.

이 예제에서는 [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) 메서드를 사용하여 `ws` 작업 영역에서 기존의 연결된 서비스 `synapselink1`을 검색합니다.
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```

## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark 풀을 컴퓨팅으로 연결

연결된 서비스를 검색한 후에는 Synapse Apache Spark 풀을 데이터 랭글링 작업을 위한 전용 컴퓨팅 리소스로 연결합니다. 

다음을 통해 Apache Spark 풀을 연결할 수 있습니다.
* Azure Machine Learning Studio
* [ARM(Azure Resource Manager) 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-linkedservice-create/azuredeploy.json)
* Azure Machine Learning Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>스튜디오를 통해 풀 연결
다음 단계를 수행합니다. 

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인합니다.
1. 왼쪽 창의 **관리** 섹션에서 **연결된 서비스** 를 선택합니다.
1. Synapse 작업 영역을 선택합니다.
1. 왼쪽 위에서 **연결된 Spark 풀** 을 선택합니다. 
1. **연결** 을 선택합니다. 
1. 목록에서 Apache Spark 풀을 선택하고 이름을 입력합니다.  
    1. 이 목록은 컴퓨팅에 연결할 수 있는 사용 가능한 Synapse Spark 풀을 식별합니다. 
    1. 새 Synapse Spark 풀을 만들려면 [Synapse Studio를 사용하여 Apache Spark 풀 만들기](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)를 참조하세요.
1. **선택한 항목 연결** 을 선택합니다. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Python SDK를 사용하여 풀 연결

**Python SDK** 를 사용하여 Apache Spark 풀을 연결할 수도 있습니다. 

다음 코드 
1. [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute)를 다음과 같이 구성합니다.

   1. 이전 단계에서 만들었거나 검색한 [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice), `linked_service`입니다. 
   1. 연결하려는 컴퓨팅 대상의 유형 `SynapseSpark`
   1. Apache Spark 풀의 이름. 이는 Azure Synapse Analytics 작업 영역에 있는 기존의 Apache Spark 풀과 일치해야 합니다.
   
1. 전달하여 기계 학습 [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget)을 만듭니다. 
   1. 사용하려는 기계 학습 작업 영역 `ws`
   1. Azure Machine Learning 작업 영역 내에서 컴퓨팅을 참조하려는 이름입니다. 
   1. Synapse Compute를 구성할 때 지정한 attach_configuration.
       1. ComputeTarget.attach()에 대한 호출은 비동기이므로 호출이 완료될 때까지 샘플이 차단됩니다.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Apache Spark 풀이 연결되었는지 확인합니다.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="next-steps"></a>다음 단계

* [Azure Synapse(미리 보기)를 사용하여 데이터를 랭글링하는 방법](how-to-data-prep-synapse-spark-pool.md).
* [Azure Synapse를 사용하여 기계 학습 파이프라인에서 Apache Spark를 사용하는 방법(미리 보기)](how-to-use-synapsesparkstep.md)
* [모델을 학습합니다](how-to-set-up-training-targets.md).