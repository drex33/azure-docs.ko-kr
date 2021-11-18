---
title: 가상 네트워크를 사용하여 추론 환경 보호
titleSuffix: Azure Machine Learning
description: 격리된 Azure Virtual Network를 사용하여 Azure Machine Learning 추론 환경을 보호합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/05/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: df790ee9480333b806ff1903d5e81d81a83177cd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723007"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>가상 네트워크에서 Azure Machine Learning 추론 환경 보호

이 문서에서는 Azure Machine Learning의 가상 네트워크를 사용하여 추론 환경을 보호하는 방법을 알아봅니다.

> [!TIP]
> 이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.
>
> * [Virtual Network 개요](how-to-network-security-overview.md)
> * [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
> * [학습 환경 보호](how-to-secure-training-vnet.md)
> * [스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)
> * [사용자 지정 DNS 사용](how-to-custom-dns.md)
> * [방화벽 사용](how-to-access-azureml-behind-firewall.md)

이 문서에서는 가상 네트워크에서 다음 추론 리소스를 보호하는 방법을 알아봅니다.
> [!div class="checklist"]
> - 기본 AKS(Azure Kubernetes Service) 클러스터
> - 프라이빗 AKS 클러스터
> - 프라이빗 링크를 사용하는 AKS 클러스터
> - ACI(Azure Container Instances)

## <a name="prerequisites"></a>필수 구성 요소

+ 일반적인 가상 네트워크 시나리오 및 전반적인 가상 네트워크 구조를 이해하려면 [네트워크 보안 개요](how-to-network-security-overview.md) 문서를 참조하세요.

+ 컴퓨팅 리소스에 사용할 기존 가상 네트워크 및 서브넷입니다.

+ 가상 네트워크 또는 서브넷에 리소스를 배포하려면 사용자 계정에 Azure RBAC(Azure 역할 기반 액세스 제어)에서 다음 작업에 대한 사용 권한이 있어야 합니다.

    - 가상 네트워크 리소스에 대한 "Microsoft.Network/virtualNetworks/join/action"
    - 서브넷 리소스에 대한 "Microsoft.Network/virtualNetworks/subnet/join/action"

    네트워킹과 Azure RBAC에 대한 자세한 내용은 [네트워킹 기본 제공 역할](../role-based-access-control/built-in-roles.md#networking)을 참조하세요.

## <a name="limitations"></a>제한 사항

### <a name="azure-container-instances"></a>Azure Container Instances

* 가상 네트워크에서 Azure Container Instances를 사용하는 경우 가상 네트워크는 Azure Machine Learning 작업 영역과 동일한 리소스 그룹에 있어야 합니다. 그렇지 않은 경우 가상 네트워크가 다른 리소스 그룹에 있을 수 있습니다.
* 작업 영역에 __프라이빗 엔드포인트__ 가 있는 경우 Azure Container Instances에 사용되는 가상 네트워크는 작업 영역 프라이빗 엔드포인트에서 사용하는 것과 동일해야 합니다.
* 가상 네트워크 내에서 Azure Container Instances를 사용하는 경우 작업 영역에 대한 ACR(Azure Container Registry)은 가상 네트워크에 있을 수 없습니다.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

* 작업 영역에 프라이빗 __엔드포인트__ 가 있는 경우 Azure Kubernetes Service 클러스터는 작업 영역과 동일한 Azure 지역에 있어야 합니다.
* 프라이빗 [AKS 클러스터와 함께 공용 FQDN(정규화된 도메인 이름)을](../aks/private-clusters.md#create-a-private-aks-cluster-with-a-public-fqdn) 사용하는 것은 Azure Machine Learning에서 __지원되지 않습니다.__

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

> [!IMPORTANT]
> 가상 네트워크에서 AKS 클러스터를 사용하려면 먼저 [AKS(Azure Kubernetes Service)에서 고급 네트워킹 구성](../aks/configure-azure-cni.md#prerequisites)의 필수 구성 요소를 따릅니다.


가상 네트워크의 AKS를 작업 영역에 추가하려면 다음 단계를 수행합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인한 다음, 구독과 작업 영역을 선택합니다.
1. 왼쪽에서 __컴퓨팅__ 을 선택하고 중앙에서 __유추 클러스터__ 를 선택한 다음 __+ 새로 만들기__ 를 선택합니다.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference.png" alt-text="유추 클러스터 만들기 대화 상자의 스크린샷":::

1. __유추 클러스터 만들기__ 대화 상자에서 __새로 만들기__ 및 클러스터에 사용할 VM 크기를 선택합니다. 마지막으로 __다음__ 을 선택합니다.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference-vm.png" alt-text="VM 설정의 스크린샷":::

1. __설정 구성__ 섹션에서 __컴퓨팅 이름__ 을 입력하고 __클러스터 용도__, __노드 수__ 를 선택한 다음 __고급__ 을 선택하면 네트워크 설정이 표시됩니다. __가상 네트워크 구성__ 영역에서 다음 값을 설정합니다.

    * 사용할 __가상 네트워크__ 를 설정합니다.

        > [!TIP]
        > 작업 영역이 프라이빗 엔드포인트를 사용하여 가상 네트워크에 연결하는 경우 __가상 네트워크__ 선택 필드가 회색으로 표시됩니다.

    * 클러스터를 만들 __서브넷__ 을 설정합니다.
    * __Kubernetes 서비스 주소 범위__ 필드에 Kubernetes 서비스 주소 범위를 입력합니다. 이 주소 범위는 CIDR(Classless Inter-Domain Routing) 표기법 IP 범위를 사용하여 클러스터에 사용할 수 있는 IP 주소를 정의합니다. 서브넷 IP 범위(예: 10.0.0.0/16)와 겹치지 않아야 합니다.
    * __Kubernetes DNS 서비스 IP 주소__ 필드에 Kubernetes DNS 서비스 IP 주소를 입력합니다. 이 IP 주소는 Kubernetes DNS 서비스에 할당됩니다. 이 주소는 Kubernetes 서비스 주소 범위(예: 10.0.0.10)에 속해야 합니다.
    * __Docker 브리지 주소__ 필드에 Docker 브리지 주소를 입력합니다. 이 IP 주소는 Docker 브리지에 할당됩니다. 서브넷 IP 범위 또는 Kubernetes 서비스 주소 범위(예: 172.18.0.1/16)에 속하지 않아야 합니다.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-inference-settings.png" alt-text="네트워크 설정 구성의 스크린샷":::

1. AKS에 웹 서비스로 모델을 배포하면 추론 요청을 처리하는 점수 매기기 엔드포인트가 만들어집니다. 가상 네트워크 외부에서 호출하려는 경우 가상 네트워크를 제어하는 NSG(네트워크 보안 그룹)에 점수 매기기 엔드포인트의 IP 주소에 대해 사용하도록 설정된 인바운드 보안 규칙이 있는지 확인합니다.

    점수 매기기 엔드포인트의 IP 주소를 찾으려면 배포된 서비스에 대한 점수 매기기 URI를 확인합니다. 점수 매기기 URI를 보는 방법에 대한 자세한 내용은 [웹 서비스로 배포된 모델 이용](how-to-consume-web-service.md#connection-information)을 참조하세요.

   > [!IMPORTANT]
   > NSG에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](../virtual-network/network-security-groups-overview.md#default-security-rules)의 기본 보안 규칙을 참조하세요.

   [![인바운드 보안 규칙](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

    > [!IMPORTANT]
    > 점수 매기기 엔드포인트의 이미지에 표시되는 IP 주소는 배포에 따라 다릅니다. 하나의 AKS 클러스터에 대한 모든 배포에서 동일한 IP를 공유하지만 각 AKS 클러스터는 서로 다른 IP 주소를 갖습니다.

Azure Machine Learning SDK를 사용하여 가상 네트워크에 Azure Kubernetes Service를 추가할 수도 있습니다. 가상 네트워크에 AKS 클러스터가 이미 있는 경우 [AKS에 배포하는 방법](how-to-deploy-and-where.md)의 설명에 따라 작업 영역에 연결할 수 있습니다. 다음 코드는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 AKS 인스턴스를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

만들기 프로세스가 완료되면 가상 네트워크 뒤에서 AKS 클러스터에 유추 또는 모델 채점을 수행할 수 있습니다. 자세한 내용은 [AKS에 배포하는 방법](how-to-deploy-and-where.md)을 참조하세요.

Kubernetes와 함께 역할 기반 액세스 제어를 사용하는 방법에 대한 자세한 내용은 [Kubernetes 권한 부여에 Azure RBAC 사용](../aks/manage-azure-rbac.md)을 참조하세요.

## <a name="network-contributor-role"></a>네트워크 참가자 역할

> [!IMPORTANT]
> 이전에 만든 가상 네트워크를 제공하여 AKS 클러스터를 만들거나 연결하는 경우 AKS 클러스터에 대한 SP(서비스 주체) 또는 관리 ID에 가상 네트워크를 포함하는 리소스 그룹에 대한 _네트워크 참가자_ 역할을 부여해야 합니다.
>
> 네트워크 참가자로 ID를 추가하려면 다음 단계를 수행합니다.

1. AKS에 대한 서비스 주체 또는 관리 ID를 찾으려면 다음 Azure CLI 명령을 사용합니다. `<aks-cluster-name>`을 클러스터의 이름으로 바꿉니다. `<resource-group-name>`을 _AKS 클러스터가 포함_ 된 리소스 그룹의 이름으로 바꿉니다.

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    이 명령이 `msi` 값을 반환하는 경우 다음 명령을 사용하여 관리 ID의 주체 ID를 식별합니다.

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. 가상 네트워크가 포함된 리소스 그룹의 ID를 찾으려면 다음 명령을 사용합니다. `<resource-group-name>`을 _가상 네트워크가 포함_ 된 리소스 그룹의 이름으로 바꿉니다.

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. 서비스 주체 또는 관리 ID를 네트워크 참가자로 추가하려면 다음 명령을 사용합니다. `<SP-or-managed-identity>`를 서비스 주체 또는 관리 ID에 대해 반환된 ID로 바꿉니다. `<resource-group-id>`를 가상 네트워크가 포함된 리소스 그룹에 대해 반환된 ID로 바꿉니다.

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
AKS에서 내부 부하 분산 장치를 사용하는 방법에 대한 자세한 내용은 [Azure Kubernetes Service에서 내부 부하 분산 장치 사용](../aks/internal-lb.md)을 참조하세요.

## <a name="secure-vnet-traffic"></a>VNet 트래픽 보호

AKS 클러스터와 가상 네트워크 간에 트래픽을 격리하는 방법에는 두 가지가 있습니다.

* __프라이빗 AKS 클러스터__: 이 방법은 Azure Private Link를 사용하여 배포/관리 작업을 위한 클러스터와의 통신을 보호합니다.
* __내부 AKS 부하 분산 장치__: 이 방법은 가상 네트워크 내에서 개인 IP를 사용하도록 배포하기 위한 엔드포인트를 AKS에 구성합니다.

### <a name="private-aks-cluster"></a>프라이빗 AKS 클러스터

기본적으로 AKS 클러스터에는 공용 IP 주소를 사용하는 API 서버 또는 컨트롤 플레인이 있습니다. 프라이빗 AKS 클러스터를 만들어 프라이빗 컨트롤 플레인을 사용하도록 AKS를 구성할 수 있습니다. 자세한 내용은 [프라이빗 Azure Kubernetes Service 클러스터 만들기](../aks/private-clusters.md)를 참조하세요.

프라이빗 AKS 클러스터를 만든 후에는 [클러스터를 가상 네트워크에 연결](how-to-create-attach-kubernetes.md)하여 Azure Machine Learning에 사용합니다.

### <a name="internal-aks-load-balancer"></a>내부 AKS 부하 분산 장치

기본적으로 AKS 배포는 [공용 부하 분산 장치](../aks/load-balancer-standard.md)를 사용합니다. 이 섹션에서는 내부 부하 분산 장치를 사용하도록 AKS를 구성하는 방법을 알아봅니다. 내부(또는 개인) 부하 분산 장치는 개인 IP만 프런트 엔드로 허용되는 경우에 사용됩니다. 내부 부하 분산 장치는 가상 네트워크 내부의 트래픽 부하를 분산하는 데 사용됩니다.

개인 IP 주소는 AKS가 _내부 부하 분산 장치_ 를 사용하도록 구성하여 활성화합니다. 

#### <a name="enable-private-load-balancer"></a>개인 부하 분산 장치 사용

> [!IMPORTANT]
> Azure Machine Learning 스튜디오에서 Azure Kubernetes Service 클러스터를 만들 때는 개인 IP를 사용할 수 없습니다. 기계 학습을 위해 Python SDK 또는 Azure CLI 확장을 사용할 때 내부 부하 분산 장치를 사용하여 클러스터를 만들 수 있습니다.

다음 예제에서는 SDK 및 CLI를 사용하여 __개인 IP/내부 부하 분산 장치로 새 AKS 클러스터를 만드는__ 방법을 보여 줍니다.

# <a name="python"></a>[Python](#tab/python)

```python
import azureml.core
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config=AksCompute.provisioning_configuration(load_balancer_type="InternalLoadBalancer")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.load_balancer_subnet = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks --load-balancer-type InternalLoadBalancer
```

내부 부하 분산 장치를 사용하도록 기존 AKS 클러스터를 업그레이드하려면 다음 명령을 사용합니다.

```azurecli
az ml computetarget update aks \
                           -n myaks \
                           --load-balancer-subnet mysubnet \
                           --load-balancer-type InternalLoadBalancer \
                           --workspace-name myworkspace \
                           -g myresourcegroup
```


자세한 내용은 [az ml computetarget create aks](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_aks) 및 [az ml computetarget update aks](/cli/azure/ml(v1)/computetarget/update#az_ml_computetarget_update_aks) 참조를 참조하세요.

---

작업 영역에 __기존 클러스터를 연결__ 하는 경우에는 연결 작업 후에 부하 분산 장치를 구성할 때까지 기다려야 합니다. 클러스터 연결에 대한 자세한 내용은 [기존 AKS 클러스터 연결](how-to-create-attach-kubernetes.md)을 참조하세요.

기존 클러스터를 연결한 후에는 내부 부하 분산 장치/개인 IP를 사용하도록 클러스터를 업데이트할 수 있습니다.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>ACI(Azure Container Instances) 사용

Azure Container Instances는 모델을 배포할 때 동적으로 생성됩니다. Azure Machine Learning이 가상 네트워크 내에 ACI를 만들 수 있도록 설정하려면 배포에 사용되는 서브넷에 __서브넷 위임__ 을 활성화해야 합니다. 가상 네트워크의 ACI를 작업 영역으로 사용하려면 다음 단계를 사용하세요.

1. 가상 네트워크에서 서브넷 위임을 사용하도록 설정하려면 [서브넷 위임 추가 또는 제거](../virtual-network/manage-subnet-delegation.md) 문서의 정보를 참조하세요. 가상 네트워크를 만들 때 위임을 사용하도록 설정하거나 기존 네트워크에 추가할 수 있습니다.

    > [!IMPORTANT]
    > 위임을 사용하도록 설정하는 경우 `Microsoft.ContainerInstance/containerGroups`을 __서비스에 서브넷 위임__ 값으로 사용합니다.

2. [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)을 사용하여 모델을 배포하고 `vnet_name` 및 `subnet_name` 매개 변수를 사용합니다. 이 매개 변수를 위임을 사용하도록 설정한 가상 네트워크 이름 및 서브넷으로 설정합니다.

## <a name="limit-outbound-connectivity-from-the-virtual-network"></a>가상 네트워크에서 아웃바운드 연결 제한

기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려면 Azure Container Registry에 대한 액세스를 허용해야 합니다. 예를 들어 NSG(네트워크 보안 그룹)에 __AzureContainerRegistry.RegionNam__ 서비스 태그에 대한 액세스를 허용하는 규칙이 포함되어 있어야 합니다. 여기서 '{RegionName}'은 Azure 지역의 이름입니다.

## <a name="next-steps"></a>다음 단계

이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.

* [Virtual Network 개요](how-to-network-security-overview.md)
* [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
* [학습 환경 보호](how-to-secure-training-vnet.md)
* [스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)
* [사용자 지정 DNS 사용](how-to-custom-dns.md)
* [방화벽 사용](how-to-access-azureml-behind-firewall.md)