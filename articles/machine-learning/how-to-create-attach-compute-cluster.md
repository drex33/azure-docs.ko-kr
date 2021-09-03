---
title: 컴퓨팅 클러스터 만들기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 작업 영역에서 컴퓨팅 클러스터를 만드는 방법에 대해 알아봅니다. 컴퓨팅 클러스터를 학습 또는 유추를 위한 컴퓨팅 대상으로 사용합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 07/09/2021
ms.openlocfilehash: d36d7e91afc4b0bade9f3da08d1324aa7f56cba9
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113594142"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Azure Machine Learning 컴퓨팅 클러스터 만들기

Azure Machine Learning 작업 영역에서 [컴퓨팅 클러스터](concept-compute-target.md#azure-machine-learning-compute-managed)를 만들고 관리하는 방법에 대해 알아봅니다.

Azure Machine Learning 컴퓨팅 클러스터를 사용하여 클라우드의 CPU 또는 GPU 컴퓨팅 노드 클러스터에 학습 또는 일괄 처리 유추 프로세스를 배포할 수 있습니다. GPU를 포함하는 VM 크기에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](../virtual-machines/sizes-gpu.md)를 참조하세요. 

이 문서에서는 다음 방법을 알아봅니다.

* 컴퓨팅 클러스터 만들기
* 컴퓨팅 클러스터 비용 낮추기
* 클러스터에 대한 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 설정

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* [Machine Learning 서비스를 위한 Azure CLI 확장](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) 또는 [Azure Machine Learning Visual Studio Code 확장](how-to-setup-vs-code.md).

* Python SDK를 사용하는 경우 작업 영역으로 [개발 환경을 설정](how-to-configure-environment.md)합니다.  환경이 설정되면 Python 스크립트의 작업 영역에 연결합니다.

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>컴퓨팅 클러스터란?

Azure Machine Learning 컴퓨팅 클러스터는 사용자가 단일 또는 다중 노드 컴퓨팅을 간편하게 만들 수 있는 관리형 컴퓨팅 인프라입니다. 컴퓨팅 클러스터는 작업 영역의 다른 사용자와 공유할 수 있는 리소스입니다. 작업이 제출될 때 컴퓨팅이 자동으로 확장되어 Azure Virtual Network에 배치될 수 있습니다. 컴퓨팅은 컨테이너화된 환경에서 실행되며 모델 종속성을 [Docker 컨테이너](https://www.docker.com/why-docker)로 패키지합니다.

컴퓨팅 클러스터를 사용하면 기업에서 SSH 포트를 열지 않아도 [가상 네트워크 환경](how-to-secure-training-vnet.md)에서 작업을 안전하게 실행할 수 있습니다. 작업은 컨테이너화된 환경에서 실행되며 모델 종속성을 Docker 컨테이너로 패키지합니다. 

## <a name="limitations"></a>제한 사항

* 이 문서에 나열되어 있는 일부 시나리오는 __미리 보기로__ 표시되어 있습니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

* 컴퓨팅 클러스터는 작업 영역과 다른 지역에서 만들 수 있습니다. 이 기능은 __미리 보기__ 로 제공되며 컴퓨팅 인스턴스가 아닌 __컴퓨팅 클러스터__ 에서만 사용할 수 있습니다. 프라이빗 엔드포인트 지원 작업 영역을 사용하는 경우 이 미리 보기를 사용할 수 없습니다. 

    > [!WARNING]
    > 작업 영역이나 데이터 저장소와 다른 지역에서 컴퓨팅 클러스터를 사용하는 경우 네트워크 대기 시간과 데이터 전송 비용이 증가할 수 있습니다. 클러스터를 생성할 때와 클러스터에서 작업을 실행할 때 대기 시간과 비용이 발생할 수 있습니다.

* 현재 [ARM 템플릿](/azure/templates/microsoft.machinelearningservices/workspaces/computes)을 통한 클러스터 만들기만 지원되며 업데이트는 지원되지 않습니다. 컴퓨팅을 업데이트하려면 지금은 SDK, Azure CLI 또는 UX를 사용하는 것이 좋습니다.

* Azure Machine Learning 컴퓨팅에는 할당할 수 있는 코어 수와 같은 기본적인 제한이 있습니다. 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](how-to-manage-quotas.md)을 참조하세요.

* Azure를 사용하면 리소스를 삭제할 수 없도록 하거나 읽기 전용이 되도록 리소스에 _잠금_ 을 설정할 수 있습니다. __작업 영역을 포함하는 리소스 그룹에 리소스 잠금을 적용하지 마세요__. 작업 영역을 포함하는 리소스 그룹에 잠금을 적용하면 Azure ML 컴퓨팅 클러스터에 대한 스케일링 작업을 할 수 없습니다. 리소스를 잠그는 방법에 대한 자세한 내용은 [예기치 않은 변경을 방지하기 위해 리소스 잠금](../azure-resource-manager/management/lock-resources.md)을 참조하세요.

> [!TIP]
> 필요한 코어 수만큼 할당량이 있는 경우 일반적으로 클러스터를 최대 100노드까지 스케일 업할 수 있습니다. 예를 들어 기본적으로 클러스터 노드 간에는 노드 간 통신을 사용하도록 설정하여 MPI 작업을 지원합니다. 그러나 간단하게 [지원 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)하고 노드 간 통신을 사용하지 않도록 구독, 작업 영역 또는 특정 클러스터를 허용 목록에 추가해 달라고 요청하여 클러스터를 수천 개의 노드로 스케일링할 수 있습니다.


## <a name="create"></a>생성

**예상 시간**: 약 5분.

Azure Machine Learning 컴퓨팅은 실행 전반에서 다시 사용할 수 있습니다. 컴퓨팅은 작업 영역에 있는 다른 사용자와 공유할 수 있으며, 실행 간에 유지되고 제출된 실행 수와 클러스터에 설정된 max_nodes 수에 따라 노드를 자동으로 확장 또는 축소합니다. min_nodes 설정은 사용 가능한 최소 노드 수를 제어합니다.

컴퓨터 클러스터 만들기에 적용되는 VM 제품군 할당량 및 총 지역 할당량당 지역별 전용 코어는 Azure Machine Learning 학습 컴퓨팅 클러스터 할당량과 통합되고 공유됩니다. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

사용되지 않는 경우 컴퓨팅은 0개 노드로 자동 축소됩니다.   필요에 따라 작업을 실행하기 위해 전용 VM이 만들어집니다.
    
# <a name="python"></a>[Python](#tab/python)


Python에서 영구적 Azure Machine Learning 컴퓨팅 리소스를 만들려면 **vm_size** 및 **max_nodes** 속성을 지정합니다. 그런 다음, Azure Machine Learning은 다른 속성에 스마트 기본값을 사용합니다.
    
* **vm_size**: Azure Machine Learning 컴퓨팅에서 만든 노드의 VM 제품군입니다.
* **max_nodes**: Azure Machine Learning 컴퓨팅에서 작업을 실행할 때 자동으로 확장할 최대 노드 수입니다.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Azure Machine Learning 컴퓨팅을 만들 때 여러 고급 속성을 구성할 수도 있습니다. 속성을 사용하면 고정 크기로 또는 구독의 기존 Azure Virtual Network 내에서 영구적 클러스터를 만들 수 있습니다.  자세한 내용은 [AmlCompute 클래스](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)를 참조하세요.

> [!WARNING]
> `location` 매개 변수를 설정할 때 작업 영역이나 데이터 저장소와 다른 지역인 경우 네트워크 대기 시간과 데이터 전송 비용이 증가할 수 있습니다. 클러스터를 생성할 때와 클러스터에서 작업을 실행할 때 대기 시간과 비용이 발생할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2 --location westus2
```

> [!WARNING]
> 작업 영역이나 데이터 저장소와 다른 지역에서 컴퓨팅 클러스터를 사용하는 경우 네트워크 대기 시간과 데이터 전송 비용이 증가할 수 있습니다. 클러스터를 생성할 때와 클러스터에서 작업을 실행할 때 대기 시간과 비용이 발생할 수 있습니다.

자세한 내용은 [az ml computetarget create amlcompute](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_amlcompute)를 참조하세요.

# <a name="studio"></a>[Studio](#tab/azure-studio)

스튜디오에서 컴퓨팅 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Machine Learning 스튜디오에서 컴퓨팅 대상 만들기](how-to-create-attach-compute-studio.md#amlcompute)를 참조하세요.

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> 컴퓨팅 클러스터 비용 낮추기

[우선 순위가 낮은 VM](how-to-manage-optimize-cost.md#low-pri-vm)을 사용하여 워크로드의 일부 또는 전체를 실행하도록 선택할 수도 있습니다. 이러한 VM은 가용성이 보장되지 않고 사용 중에 선점될 수 있습니다. 선점된 작업을 다시 시작해야 합니다. 

이러한 방법 중 하나를 사용하여 우선 순위가 낮은 VM을 지정합니다.
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

`vm-priority`를 설정합니다.
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

스튜디오에서 VM을 만들 때 **낮은 우선 순위** 를 선택합니다.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 관리 ID 설정

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* 프로비저닝 구성에서 관리 ID를 구성:  

    * `ws`라는 작업 영역에서 만든 시스템 할당 관리 ID
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * `ws`라는 작업 영역에서 만든 사용자 할당 관리 ID
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* `cpu_cluster`라는 기존 컴퓨팅 클러스터에 관리 ID 추가
    
    * 시스템이 할당한 관리형 ID:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * 사용자가 할당한 관리형 ID:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* 관리 ID를 사용하여 새 관리형 컴퓨팅 클러스터 만들기

  * 사용자 할당 관리 ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * 시스템 할당 관리 ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* 기존 클러스터에 관리 ID를 추가:

    * 사용자 할당 관리 ID
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * 시스템 할당 관리 ID

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

[스튜디오에서 관리 ID 설정](how-to-create-attach-compute-studio.md#managed-identity)을 참조하세요.

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>관리 ID 사용량

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>문제 해결

GA 릴리스 전에 Azure Portal에서 Azure Machine Learning 작업 영역을 만든 일부 사용자가 해당 작업 영역에서 AmlCompute을 만들 수 없는 경우가 있을 수 있습니다. 서비스에 대해 지원 요청을 제기하거나 포털 또는 SDK를 통해 새 작업 영역을 만들어 즉시 차단을 직접 해제할 수 있습니다.

Azure Machine Learning 컴퓨팅 클러스터가 노드 상태에 대한 크기 조정 (0 -> 0)에서 멈춘 것처럼 보이는 경우 이는 Azure 리소스 잠금으로 인한 것일 수 있습니다.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>다음 단계

컴퓨팅 클러스터를 사용하여 다음을 수행합니다.

* [학습 실행 제출](how-to-set-up-training-targets.md) 
* [일괄 처리 유추 실행](./tutorial-pipeline-batch-scoring-classification.md).
