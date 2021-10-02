---
title: Azure Kubernetes Service 만들기 및 연결
titleSuffix: Azure Machine Learning
description: Azure Machine Learning을 통해 새 Azure Kubernetes Service 클러스터를 만드는 방법 또는 기존 AKS 클러스터를 작업 영역에 연결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/16/2021
ms.openlocfilehash: 8659568081afd4357bd9977f864583ea14208fbe
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389630"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기 및 연결

Azure Machine Learning은 학습된 기계 학습 모델을 Azure Kubernetes Service에 배포할 수 있습니다. 그러나 먼저 Azure ML 작업 영역에서 AKS(Azure Kubernetes Service) 클러스터를 __만들거나__ 기존 AKS 클러스터를 __연결__ 해야 합니다. 이 문서에서는 클러스터를 만들고 연결하는 방법에 대한 정보를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

- [Machine Learning 서비스를 위한 Azure CLI 확장](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) 또는 [Azure Machine Learning Visual Studio Code 확장](how-to-setup-vs-code.md).

- Azure Virtual Network를 사용하여 Azure ML 작업 영역과 AKS 클러스터 간의 통신을 보호하려는 경우 [학습 및 추론 중 네트워크 격리](./how-to-network-security-overview.md) 문서를 참조하세요.

## <a name="limitations"></a>제한 사항

- BLB(기본 Load Balancer) 대신 클러스터에 배포된 **SLB(표준 Load Balancer)** 가 필요한 경우 AKS 포털/CLI/SDK에서 클러스터를 만든 다음, AML 작업 영역에 **연결** 합니다.

- 공용 IP 주소 만들기를 제한하는 Azure 정책이 있는 경우 AKS 클러스터를 만들 수 없습니다. AKS에는 [송신 트래픽](../aks/limit-egress-traffic.md)에 대한 공용 IP가 필요합니다. 또한 송신 트래픽 문서에서는 몇 가지 정규화된 도메인 이름을 제외하고 공용 IP를 통해 클러스터에서 송신 트래픽을 잠그는 지침을 제공합니다. 공용 IP를 사용하도록 설정하는 다음 두 가지 방법이 있습니다.
    - 클러스터는 기본적으로 BLB 또는 SLB와 함께 만들어진 공용 IP를 사용할 수 있습니다.
    - 공용 IP 없이 클러스터를 만든 다음, 사용자 정의 경로가 있는 방화벽을 사용하여 공용 IP를 구성할 수 있습니다. 자세한 내용은 [사용자 정의 경로를 사용하여 클러스터 송신 사용자 지정](../aks/egress-outboundtype.md)을 참조하세요.
    
    AML 컨트롤 플레인은 이 공용 IP와 통신하지 않습니다. 이는 배포를 위해 AKS 컨트롤 플레인과 통신합니다. 

- [API 서버에 액세스할 수 있는 권한 있는 IP 범위가 사용하도록 설정](../aks/api-server-authorized-ip-ranges.md)된 AKS 클러스터를 **연결** 하는 경우 AML 컨트롤 플레인 IP 범위를 AKS 클러스터에 사용하도록 설정합니다. AML 컨트롤 플레인은 쌍으로 연결된 지역에 배포되고, 추론 Pod를 AKS 클러스터에 배포합니다. API 서버에 대한 액세스 권한이 없으면 추론 Pod를 배포할 수 없습니다. AKS 클러스터에서 IP 범위를 사용하도록 설정하는 경우 [IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=56519)를 [쌍으로 연결된 두 지역](../best-practices-availability-paired-regions.md) 모두에 사용합니다.

    권한 있는 IP 범위는 표준 Load Balancer에서만 작동합니다.

- __다른 Azure 구독__ 에서 AKS 클러스터를 연결하려면 사용자(Azure AD 계정)에게 AKS 클러스터에 대한 **기여자** 역할이 부여되어야 합니다. [Azure Portal](https://ms.portal.azure.com/)에서 액세스를 확인합니다.

- 프라이빗 AKS 클러스터를 사용하려면(Azure Private Link 사용) 먼저 클러스터를 만든 다음, 작업 영역에 **연결** 해야 합니다. 자세한 내용은 [프라이빗 Azure Kubernetes Service 클러스터 만들기](../aks/private-clusters.md)를 참조하세요.

- AKS 클러스터에 대한 컴퓨팅 이름은 Azure ML 작업 영역 내에서 고유해야 합니다. 문자, 숫자 및 대시를 포함할 수 있습니다. 문자로 시작하여 문자 또는 숫자로 끝나야 하며, 길이는 3~24자여야 합니다.
 
 - 모델을 **GPU** 노드 또는 **FPGA** 노드(또는 특정 SKU)에 배포하려면 특정 SKU를 사용하여 클러스터를 만들어야 합니다. 보조 노드 풀을 기존 클러스터에 만들고 모델을 이 보조 노드 풀에 배포하는 것은 지원되지 않습니다.
 
- 클러스터를 만들거나 연결할 때 __개발-테스트__ 또는 __프로덕션__ 용 클러스터를 만들지 여부를 선택할 수 있습니다. 프로덕션 대신 __개발__, __유효성 검사__ 및 __테스트__ 용 AKS 클러스터를 만들려면 __클러스터 용도__ 를 __개발-테스트__ 로 설정합니다. 클러스터 용도를 지정하지 않으면 __프로덕션__ 클러스터가 만들어집니다. 

    > [!IMPORTANT]
    > __개발-테스트__ 클러스터는 프로덕션 수준 트래픽에 적합하지 않으며, 추론 시간이 늘어날 수 있습니다. 또한 개발/테스트 클러스터는 내결함성을 보장하지 않습니다.

- 클러스터를 만들거나 연결할 때 클러스터가 __프로덕션__ 에 사용될 경우 __3개__ 이상의 노드를 포함해야 합니다. 개발 __테스트__ 클러스터의 경우 노드가 1개 이상 포함되어야 합니다.

- Azure Machine Learning SDK는 AKS 클러스터 크기 조정을 지원하지 않습니다. 클러스터 노드의 크기를 조정하려면 Azure Machine Learning 스튜디오에서 AKS 클러스터에 대한 UI를 사용합니다. 클러스터의 VM 크기가 아니라 노드 수만 변경할 수 있습니다. AKS 클러스터의 노드 크기를 조정하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

    - [수동으로 AKS 클러스터의 노드 수 조정](../aks/scale-cluster.md)
    - [AKS에서 클러스터 자동 크기 조정기 설정](../aks/cluster-autoscaler.md)

- __YAML 구성을 사용하여 클러스터를 직접 업데이트하지 않습니다__. Azure Kubernetes Services는 YAML 구성을 통한 업데이트를 지원하지만, Azure Machine Learning 배포에서 변경 내용을 재정의합니다. 덮어쓰지 않는 유일한 두 가지 YAML 필드는 __요청 제한__ 및 __CPU 및 메모리__ 입니다.

- Azure Machine Learning 스튜디오 UI, SDK 또는 CLI 확장을 사용하여 AKS 클러스터를 만드는 것은 멱등원이 __아닙니다__. 리소스를 다시 만들려고 하면 동일한 이름의 클러스터가 이미 존재한다는 오류가 발생합니다.
    
    - Azure Resource Manager 템플릿 및 [Microsoft.MachineLearningServices/workspaces/computes](/azure/templates/microsoft.machinelearningservices/2019-11-01/workspaces/computes) 리소스를 사용하여 AKS 클러스터를 만드는 것도 멱등원이 __아닙니다__. 템플릿을 다시 사용하여 이미 존재하는 리소스를 업데이트하려고 하면 동일한 오류가 표시됩니다.

## <a name="azure-kubernetes-service-version"></a>Azure Kubernetes Service 버전

Azure Kubernetes Service를 사용하면 다양한 Kubernetes 버전을 사용하여 클러스터를 만들 수 있습니다. 사용 가능한 버전에 대한 자세한 내용은 [Azure Kubernetes Service에서 지원되는 Kubernetes 버전](../aks/supported-kubernetes-versions.md)을 참조하세요.

다음 방법 중 하나를 사용하여 Azure Kubernetes Service 클러스터를 **만드는** 경우 만드는 *클러스터의 버전에 대한 선택 항목이 제공되지 않습니다*.

* Azure Machine Learning 스튜디오 또는 Azure Portal의 Azure Machine Learning 섹션
* Azure CLI용 Machine Learning 확장
* Azure Machine Learning SDK

AKS 클러스터를 만드는 이러한 방법에서는 클러스터의 __기본__ 버전을 사용합니다. 새 Kubernetes 버전을 사용할 수 있게 되면 *시간이 지남에 따라 기본 버전이 변경됩니다*.

기존 AKS 클러스터를 **연결** 하는 경우 현재 지원되는 모든 AKS 버전을 지원합니다.

> [!IMPORTANT]
> Azure Kubernetes Service는 버전 1.16 이상의 [Blobfuse FlexVolume 드라이버](https://github.com/Azure/kubernetes-volume-drivers/blob/master/flexvolume/blobfuse/README.md)와 버전 1.17 이하의 [Blob CSI 드라이버](https://github.com/kubernetes-sigs/blob-csi-driver/blob/master/README.md)를 사용합니다. 따라서 클러스터 버전에 올바른 blobfuse 방법으로 배포하려면 클러스터 업그레이드 후 웹 서비스를 다시 배포하거나 [업데이트](how-to-deploy-update-web-service.md)하는 것이 중요합니다.

> [!NOTE]
> 더 이상 지원되지 않는 이전 클러스터가 있는 극단적인 경우가 있을 수 있습니다. 이 경우 연결 작업에서 오류를 반환하고 현재 지원되는 버전을 나열합니다.
>
> **미리 보기** 버전은 연결할 수 있습니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 미리 보기 버전 사용에 대한 지원이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

### <a name="available-and-default-versions"></a>사용 가능한 버전 및 기본 버전

사용 가능한 AKS 버전 및 기본 AKS 버전을 확인하려면 [az aks get-versions](/cli/azure/aks#az_aks_get_versions) [Azure CLI](/cli/azure/install-azure-cli) 명령을 사용합니다. 예를 들어 다음 명령에서는 미국 서부 지역에서 사용할 수 있는 버전을 반환합니다.

```azurecli-interactive
az aks get-versions -l westus -o table
```

이 명령의 출력은 다음 텍스트와 비슷합니다.

```text
KubernetesVersion    Upgrades
-------------------  ----------------------------------------
1.18.6(preview)      None available
1.18.4(preview)      1.18.6(preview)
1.17.9               1.18.4(preview), 1.18.6(preview)
1.17.7               1.17.9, 1.18.4(preview), 1.18.6(preview)
1.16.13              1.17.7, 1.17.9
1.16.10              1.16.13, 1.17.7, 1.17.9
1.15.12              1.16.10, 1.16.13
1.15.11              1.15.12, 1.16.10, 1.16.13
```

Azure Machine Learning을 통해 클러스터를 **만들** 때 사용되는 기본 버전을 확인하려면 `--query` 매개 변수를 사용하여 기본 버전을 선택할 수 있습니다.

```azurecli-interactive
az aks get-versions -l westus --query "orchestrators[?default == `true`].orchestratorVersion" -o table
```

이 명령의 출력은 다음 텍스트와 비슷합니다.

```text
Result
--------
1.16.13
```

**사용 가능한 버전을 프로그래밍 방식으로 확인** 하려면 컨테이너 서비스 클라이언트 - 오케스트레이터 나열 REST API를 사용합니다. 사용 가능한 버전을 확인하려면 `orchestratorType`이 `Kubernetes`인 항목을 확인합니다. 연결된 `orchestrationVersion` 항목에는 작업 영역에 **연결** 할 수 있는 사용 가능한 버전이 포함되어 있습니다.

Azure Machine Learning을 통해 클러스터를 **만들** 때 사용되는 기본 버전을 확인하려면 `orchestratorType`이 `Kubernetes`이고 `default`가 `true`인 항목을 찾습니다. 연결된 `orchestratorVersion` 값이 기본 버전입니다. 다음 JSON 코드 조각에서는 항목 예를 보여 줍니다.

```json
...
 {
        "orchestratorType": "Kubernetes",
        "orchestratorVersion": "1.16.13",
        "default": true,
        "upgrades": [
          {
            "orchestratorType": "",
            "orchestratorVersion": "1.17.7",
            "isPreview": false
          }
        ]
      },
...
```

## <a name="create-a-new-aks-cluster"></a>새 AKS 클러스터 만들기

**예상 시간**: 약 10분

AKS 클러스터를 만들거나 연결하는 작업은 작업 영역에 대한 일회성 프로세스입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다. 클러스터 또는 클러스터가 포함된 리소스 그룹을 삭제하는 경우 다음에 배포해야 할 때 새 클러스터를 만들어야 합니다. 여러 AKS 클러스터를 작업 영역에 연결할 수 있습니다.

다음 예제에서는 SDK 및 CLI를 사용하여 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

이 예제에 사용된 클래스, 메서드 및 매개 변수에 대한 자세한 내용은 다음 참조 문서를 확인하세요.

* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computetarget#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

자세한 내용은 [az ml computetarget create aks](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_aks)를 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털에서 AKS 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Machine Learning 스튜디오에서 컴퓨팅 대상 만들기](how-to-create-attach-compute-studio.md#inference-clusters)를 참조하세요.

---

## <a name="attach-an-existing-aks-cluster"></a>기존 AKS 클러스터 연결

**예상 시간**: 약 5분

Azure 구독에 AKS 클러스터가 이미 있는 경우 이 클러스터를 작업 영역에서 사용할 수 있습니다.

> [!TIP]
> 기존 AKS 클러스터는 Azure Machine Learning 작업 영역이 아니라 Azure 지역에 있을 수 있습니다.


> [!WARNING]
> 작업 영역에서 동일한 AKS 클러스터에 대한 여러 동시 연결을 만들지 마세요. 예를 들어 두 개의 다른 이름을 사용하여 하나의 AKS 클러스터를 작업 영역에 연결합니다. 각 새 첨부 파일은 이전의 기존 첨부 파일을 중단합니다.
>
> 예를 들어 TLS 또는 다른 클러스터 구성 설정을 변경하기 위해 AKS 클러스터를 다시 연결하려는 경우 먼저 [AksCompute.detach()](/python/api/azureml-core/azureml.core.compute.akscompute#detach--)를 사용하여 기존 연결을 제거해야 합니다.

Azure CLI 또는 포털을 사용하여 AKS 클러스터를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [AKS 클러스터 만들기(CLI)](/cli/azure/aks?bc=%2fazure%2fbread%2ftoc.json&toc=%2fazure%2faks%2fTOC.json#az_aks_create)
* [AKS 클러스터 만들기(포털)](../aks/kubernetes-walkthrough-portal.md)
* [AKS 클러스터 만들기(Azure 빠른 시작 템플릿의 ARM 템플릿)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.containerinstance/aks-azml-targetcompute)

다음 예제에서는 기존 AKS 클러스터를 작업 영역에 연결하는 방법을 보여 줍니다.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

이 예제에 사용된 클래스, 메서드 및 매개 변수에 대한 자세한 내용은 다음 참조 문서를 확인하세요.

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose)
* [AksCompute.attach](/python/api/azureml-core/azureml.core.compute.computetarget#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

CLI를 사용하여 기존 클러스터를 연결하려면 기존 클러스터의 리소스 ID를 가져와야 합니다. 이 값을 가져오려면 다음 명령을 사용합니다. `myexistingcluster`를 AKS 클러스터 이름으로 바꿉니다. `myresourcegroup`을 클러스터가 포함된 리소스 그룹으로 바꿉니다.

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

이 명령은 다음 텍스트와 유사한 값을 반환합니다.

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

기존 클러스터를 작업 영역에 연결하려면 다음 명령을 사용합니다. `aksresourceid`를 이전 명령에서 반환된 값으로 바꿉니다. `myresourcegroup`을 작업 영역이 포함된 리소스 그룹으로 바꿉니다. `myworkspace`를 작업 영역 이름으로 바꿉니다.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

자세한 내용은 [az ml computetarget attach aks](/cli/azure/ml(v1)/computetarget/attach#az_ml_computetarget_attach_aks) 참조를 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

포털에서 AKS 클러스터를 연결하는 방법에 대한 자세한 내용은 [Azure Machine Learning 스튜디오에서 컴퓨팅 대상 만들기](how-to-create-attach-compute-studio.md#inference-clusters)를 참조하세요.

---

## <a name="create-or-attach-an-aks-cluster-with-tls-termination"></a>TLS 종료를 사용하는 AKS 클러스터 만들기 또는 연결
[AKS 클러스터를 만들거나 연결](how-to-create-attach-kubernetes.md)할 때 **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** 및 **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 구성 개체를 사용하여 TLS 종료를 사용하도록 설정할 수 있습니다. 두 메서드는 모두 **enable_ssl** 메서드가 포함된 구성 개체를 반환하며, **enable_ssl** 메서드를 사용하여 TLS를 사용하도록 설정할 수 있습니다.

다음 예제에서는 내부적으로 Microsoft 인증서를 통해 자동 TLS 인증서 생성 및 구성을 사용하여 TLS 종료를 사용하도록 설정하는 방법을 보여 줍니다.
```python
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # Enable TLS termination when you create an AKS cluster by using provisioning_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   provisioning_config.enable_ssl(leaf_domain_label = "contoso")
   
   # Enable TLS termination when you attach an AKS cluster by using attach_config object enable_ssl method

   # Leaf domain label generates a name using the formula
   # "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
   # where "######" is a random series of characters
   attach_config.enable_ssl(leaf_domain_label = "contoso")


```
다음 예제에서는 사용자 지정 인증서 및 사용자 지정 도메인 이름을 사용하여 TLS 종료를 사용하도록 설정하는 방법을 보여 줍니다. 사용자 지정 도메인 및 인증서를 사용하는 경우 점수 매기기 엔드포인트의 IP 주소를 가리키도록 DNS 레코드를 업데이트해야 합니다. [DNS 업데이트](how-to-secure-web-service.md#update-your-dns)를 참조하세요.

```python
   from azureml.core.compute import AksCompute, ComputeTarget

   # Enable TLS termination with custom certificate and custom domain when creating an AKS cluster
   
   provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    
   # Enable TLS termination with custom certificate and custom domain when attaching an AKS cluster

   attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")


```
>[!NOTE]
> AKS 클러스터에서 모델 배포를 보호하는 방법에 대한 자세한 내용은 [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)를 참조하세요.

## <a name="create-or-attach-an-aks-cluster-to-use-internal-load-balancer-with-private-ip"></a>개인 IP를 사용하여 내부 Load Balancer를 사용하는 AKS 클러스터 만들기 또는 연결
AKS 클러스터를 만들거나 연결할 때 내부 Load Balancer를 사용하도록 클러스터를 구성할 수 있습니다. 내부 Load Balancer를 사용하면 AKS로의 배포에 대한 점수 매기기 엔드포인트에서 가상 네트워크 내의 개인 IP를 사용합니다. 다음 코드 조각에서는 AKS 클러스터에 대한 내부 Load Balancer를 구성하는 방법을 보여 줍니다.
```python
   
   from azureml.core.compute.aks import AksUpdateConfiguration
   from azureml.core.compute import AksCompute, ComputeTarget
   
   # When you create an AKS cluster, you can specify Internal Load Balancer to be created with provisioning_config object
   provisioning_config = AksCompute.provisioning_configuration(load_balancer_type = 'InternalLoadBalancer')

   # when you attach an AKS cluster, you can update the cluster to use internal load balancer after attach
   aks_target = AksCompute(ws,"myaks")

   # Change to the name of the subnet that contains AKS
   subnet_name = "default"
   # Update AKS configuration to use an internal load balancer
   update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
   aks_target.update(update_config)
   # Wait for the operation to complete
   aks_target.wait_for_completion(show_output = True)
   
   
```
>[!IMPORTANT]
> Azure Machine Learning은 내부 Load Balancer를 사용하는 TLS 종료를 지원하지 않습니다. 내부 Load Balancer에는 개인 IP가 있고, 해당 개인 IP는 다른 네트워크에 있을 수 있으며 인증서를 다시 사용할 수 있습니다. 

>[!NOTE]
> 추론 환경을 보호하는 방법에 대한 자세한 내용은 [Azure Machine Learning 추론 환경 보호](how-to-secure-inferencing-vnet.md)를 참조하세요.

## <a name="detach-an-aks-cluster"></a>AKS 클러스터 분리

작업 영역에서 클러스터를 분리하려면 다음 방법 중 하나를 사용합니다.

> [!WARNING]
> Azure Machine Learning 스튜디오, SDK 또는 Azure CLI 확장을 기계 학습에 사용하여 AKS 클러스터를 분리하더라도 **AKS 클러스터는 삭제되지 않습니다**. 클러스터를 삭제하려면 [AKS를 통해 Azure CLI 사용](../aks/kubernetes-walkthrough.md#delete-the-cluster)을 참조하세요.

# <a name="python"></a>[Python](#tab/python)

```python
aks_target.detach()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

기존 클러스터를 작업 영역으로 분리하려면 다음 명령을 사용합니다. `myaks`를 AKS 클러스터가 작업 영역에 연결된 이름으로 바꿉니다. `myresourcegroup`을 작업 영역이 포함된 리소스 그룹으로 바꿉니다. `myworkspace`를 작업 영역 이름으로 바꿉니다.

```azurecli
az ml computetarget detach -n myaks -g myresourcegroup -w myworkspace
```

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Machine Learning 스튜디오에서 __컴퓨팅__, __추론 클러스터__ 및 제거하려는 클러스터를 선택합니다. __분리__ 링크를 사용하여 클러스터를 분리합니다.

---

## <a name="troubleshooting"></a>문제 해결
### <a name="update-the-cluster"></a>클러스터 업데이트

Azure Kubernetes Service 클러스터에 설치된 Azure Machine Learning 구성 요소에 대한 업데이트는 수동으로 적용해야 합니다. 

이러한 업데이트는 Azure Machine Learning 작업 영역에서 클러스터를 분리한 다음, 클러스터를 작업 영역에 다시 연결하여 적용할 수 있습니다. 클러스터에서 TLS를 사용하도록 설정하는 경우 클러스터를 다시 연결할 때 TLS/SSL 인증서와 프라이빗 키를 제공해야 합니다. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

TLS/SSL 인증서와 프라이빗 키가 더 이상 없거나 Azure Machine Learning에서 생성된 인증서를 사용하는 경우 클러스터를 분리하기 전에 `kubectl`을 사용하여 클러스터에 연결하고 `azuremlfessl` 비밀을 검색하여 파일을 검색할 수 있습니다.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes는 비밀을 base-64로 인코딩된 형식으로 저장합니다. `attach_config.enable_ssl`에 제공하기 전에 비밀의 `cert.pem` 및 `key.pem` 구성 요소를 base-64 형식으로 디코딩해야 합니다. 

### <a name="webservice-failures"></a>웹 서비스 오류

AKS의 많은 웹 서비스 오류는 `kubectl`을 통해 클러스터에 연결하여 디버그할 수 있습니다. 이를 실행하여 AKS 클러스터에 대한 `kubeconfig.json`을 가져올 수 있습니다.

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="next-steps"></a>다음 단계

* [Kubernetes 권한 부여에 Azure RBAC 사용](../aks/manage-azure-rbac.md)
* [모델 배포 방법 및 위치](how-to-deploy-and-where.md)
* [Azure Kubernetes Service 클러스터에 모델 배포](how-to-deploy-azure-kubernetes-service.md)
