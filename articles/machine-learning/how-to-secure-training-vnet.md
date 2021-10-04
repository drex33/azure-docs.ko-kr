---
title: 가상 네트워크를 사용하여 학습 환경 보호
titleSuffix: Azure Machine Learning
description: 격리된 Azure 가상 네트워크를 사용하여 Azure Machine Learning 학습 환경을 보호합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 09/24/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, references_regions
ms.openlocfilehash: f4eb2b35b04951a11519a6d5340ba32a3d0c0744
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399739"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>가상 네트워크에서 Azure Machine Learning 학습 환경 보호

이 문서에서는 Azure Machine Learning의 가상 네트워크를 사용하여 학습 환경을 보호하는 방법을 알아봅니다.

> [!TIP]
> 이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.
>
> * [Virtual Network 개요](how-to-network-security-overview.md)
> * [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
> * [보안 유추 환경](how-to-secure-inferencing-vnet.md)
> * [스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)
> * [사용자 지정 DNS 사용](how-to-custom-dns.md)
> * [방화벽 사용](how-to-access-azureml-behind-firewall.md)
>
> 보안 작업 영역, 컴퓨팅 클러스터 및 컴퓨팅 인스턴스를 만드는 자습서는 [자습서: 보안 작업 영역 만들기](tutorial-create-secure-workspace.md)를 참조하세요.

이 문서에서는 가상 네트워크에서 다음 학습 컴퓨팅 리소스를 보호하는 방법을 알아봅니다.
> [!div class="checklist"]
> - Azure Machine Learning 컴퓨팅 클러스터
> - Azure Machine Learning 컴퓨팅 인스턴스
> - Azure Databricks
> - Virtual Machine
> - HDInsight 클러스터

## <a name="prerequisites"></a>필수 구성 요소

+ 일반적인 가상 네트워크 시나리오 및 전반적인 가상 네트워크 구조를 이해하려면 [네트워크 보안 개요](how-to-network-security-overview.md) 문서를 참조하세요.

+ 컴퓨팅 리소스에 사용할 기존 가상 네트워크 및 서브넷입니다.

+ 가상 네트워크 또는 서브넷에 리소스를 배포하려면 사용자 계정에 Azure RBAC(Azure 역할 기반 액세스 제어)에서 다음 작업에 대한 사용 권한이 있어야 합니다.

    - 가상 네트워크 리소스의 "Microsoft.Network/virtualNetworks/*/read" ARM (Azure Resource Manager) 템플릿 배포에는 필요 하지 않습니다.
    - 서브넷 리소스에 대한 "Microsoft.Network/virtualNetworks/subnet/join/action"

    네트워킹과 Azure RBAC에 대한 자세한 내용은 [네트워킹 기본 제공 역할](../role-based-access-control/built-in-roles.md#networking)을 참조하세요.

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning 컴퓨팅 클러스터/인스턴스

* 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 구독에 있어야 합니다.
* 컴퓨팅 인스턴스나 클러스터에 사용되는 서브넷에는 할당되지 않은 IP 주소가 충분히 있어야 합니다.

    * 컴퓨팅 클러스터는 동적으로 스케일링될 수 있습니다. 할당되지 않은 IP 주소가 부족하면 클러스터가 부분적으로 할당됩니다.
    * 컴퓨팅 인스턴스에는 IP 주소 하나만 필요합니다.

* [공용 IP 주소](#no-public-ip) (미리 보기 기능) 없이 계산 인스턴스를 만들려면 작업 영역에서 개인 끝점을 사용 하 여 VNet에 연결 해야 합니다. 자세한 내용은 [Azure Machine Learning 작업 영역에 대 한 개인 끝점 구성](how-to-configure-private-link.md)을 참조 하세요.
* 가상 네트워크를 관리할 수 있는 권한을 제한하는 보안 정책이나 잠금이 없는지 확인합니다. 정책이나 잠금을 확인할 때 가상 네트워크에 대한 구독과 리소스 그룹을 모두 확인합니다.
* 가상 네트워크의 구독 또는 리소스 그룹에 대한 보안 정책이나 잠금이 가상 네트워크를 관리하는 사용자 권한을 제한하는지 확인합니다. 
* 트래픽을 제한하여 가상 네트워크를 보호하려면 [필수 공용 인터넷 액세스](#required-public-internet-access) 섹션을 참조하세요.
* 컴퓨팅 클러스터/인스턴스를 배포하는 데 사용되는 서브넷을 다른 서비스에 위임해서는 안 됩니다. 예를 들어 ACI에 위임하면 안 됩니다.

### <a name="azure-databricks"></a>Azure Databricks

* 가상 네트워크가 Azure Machine Learning 작업 영역과 동일한 구독 및 지역에 있어야 합니다.
* 작업 영역의 Azure Storage 계정도 가상 네트워크에서 보호되는 경우, Azure Databricks 클러스터와 동일한 가상 네트워크에 있어야 합니다.

## <a name="limitations"></a>제한 사항

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning 컴퓨팅 클러스터/인스턴스

* 가상 네트워크 하나에 여러 컴퓨팅 인스턴스나 클러스터를 배치하는 경우 리소스 하나 이상에 할당량 증가를 요청해야 할 수 있습니다. Machine Learning 컴퓨팅 인스턴스나 클러스터는 __가상 네트워크를 포함하는 리소스 그룹__ 에 추가 네트워킹 리소스를 자동으로 할당합니다. 각 컴퓨팅 인스턴스 또는 클러스터에 대해 서비스는 다음 리소스를 할당합니다.

    * NSG(네트워크 보안 그룹) 하나. 이 NSG에는 컴퓨팅 클러스터와 컴퓨팅 인스턴스에 적용되는 다음 규칙이 포함되어 있습니다.

        * `BatchNodeManagement` 서비스 태그에서 포트 29876-29877의 인바운드 TCP 트래픽을 허용합니다.
        * `AzureMachineLearning` 서비스 태그에서 포트 44224의 인바운드 TCP 트래픽을 허용합니다.

        다음 스크린샷에서는 이러한 규칙의 예를 보여줍니다.

        :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="NSG 스크린샷":::


        > [!TIP]
        > 계산 인스턴스가 공용 IP 주소 (미리 보기 기능)를 사용 하지 않는 경우 이러한 인바운드 NSG 규칙은 필요 하지 않습니다. 또한 계산 클러스터를 사용 하는 경우 클러스터에는 여전히 이러한 규칙이 필요 합니다.
    * 계산 클러스터의 경우 하나의 공용 IP 주소입니다. Azure Policy 할당으로 인해 공용 IP 만들기가 금지 된 경우 계산 배포가 실패 합니다.

    * 계산 인스턴스의 경우 이제 공용 IP 주소 (미리 보기 기능)를 제거할 수 있습니다. Azure Policy 할당으로 인해 공용 IP 만들기가 금지 된 경우 계산 인스턴스의 배포가 성공 합니다.

    * 부하 분산 장치 1개

    컴퓨팅 클러스터의 경우 이러한 리소스는 클러스터가 노드 0개로 스케일 다운될 때마다 삭제되어 스케일 업할 때 생성됩니다.

    컴퓨팅 인스턴스의 경우 이러한 리소스는 인스턴스가 삭제될 때까지 유지됩니다. 인스턴스를 중지해도 리소스는 제거되지 않습니다. 

    > [!IMPORTANT]
    > 이러한 리소스는 구독의 [리소스 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)으로 제한됩니다. 가상 네트워크 리소스 그룹이 잠겨 있으면 컴퓨팅 클러스터/인스턴스 삭제가 실패합니다. 컴퓨팅 클러스터/인스턴스를 삭제할 때까지 Load Balancer를 삭제할 수 없습니다. 또한 네트워크 보안 그룹 생성을 금지하는 Azure Policy 할당이 없는지 확인하세요.

* 또한 작업 영역에 대한 Azure Storage 계정이 가상 네트워크에 있으면 서브넷 제한 사항에 대한 다음 참고 자료를 사용합니다.

    * Azure Machine Learning __스튜디오__ 를 사용하여 데이터를 시각화하거나 디자이너를 사용하려는 경우 스토리지 계정이 __컴퓨팅 인스턴스나 클러스터와 동일한 서브넷에 있어야__ 합니다.
    * __SDK__ 를 사용하려는 경우 스토리지 계정이 다른 서브넷에 있을 수 있습니다.

    > [!NOTE]
    > "신뢰할 수 있는 Microsoft 서비스가 이 계정에 액세스하도록 허용" 확인란을 선택하는 것만으로는 컴퓨팅에서 통신을 허용할 수 없습니다.

* 작업 영역에서 프라이빗 엔드포인트를 사용하는 경우 가상 네트워크 내부에서만 컴퓨팅 인스턴스에 액세스할 수 있습니다. 사용자 지정 DNS나 호스트 파일을 사용하는 경우 `<instance-name>.<region>.instances.azureml.ms`에 대한 항목을 추가합니다. 이 항목을 작업 영역 프라이빗 엔드포인트의 개인 IP 주소에 매핑합니다. 자세한 내용은 [사용자 지정 DNS](./how-to-custom-dns.md) 문서를 참조하세요.
* 가상 네트워크 서비스 엔드포인트 정책은 컴퓨팅 클러스터/인스턴스 시스템 스토리지 계정에서 작동하지 않습니다.
* 스토리지와 컴퓨팅 인스턴스가 다른 지역에 있으면 간헐적인 시간 초과가 표시될 수 있습니다.
* 작업 영역에 대 한 Azure Container Registry에서 개인 끝점을 사용 하 여 가상 네트워크에 연결 하는 경우 계산 인스턴스에 대해 관리 되는 id를 사용할 수 없습니다. 관리 id를 계산 인스턴스와 함께 사용 하려면 컨테이너 레지스트리를 VNet에 배치 하지 마십시오.
* 컴퓨팅 인스턴스에서 Jupyter Notebook을 사용하려는 경우:

    * websocket 통신을 사용하지 마세요. 네트워크에서 `*.instances.azureml.net` 및 `*.instances.azureml.ms`에 대한 websocket 통신을 허용하는지 확인합니다.
    * Notebook이 데이터와 동일한 가상 네트워크와 서브넷 뒤에 있는 컴퓨팅 리소스에서 실행 중인지 확인합니다. 컴퓨팅 인스턴스를 만들 때 **고급 설정** > **가상 네트워크 구성** 을 사용하여 네트워크와 서브넷을 선택합니다.

* __컴퓨팅 클러스터__ 를 작업 영역과 다른 지역에 만들 수 있습니다. 이 기능은 __미리 보기__ 로 제공되며 컴퓨팅 인스턴스가 아닌 __컴퓨팅 클러스터__ 에서만 사용할 수 있습니다. 클러스터에 다른 지역을 사용하는 경우 다음 제한 사항이 적용됩니다.

    * 스토리지와 같은 작업 영역 관련 리소스가 클러스터와 다른 가상 네트워크에 있으면 네트워크 간에 전체 가상 네트워크 피어링을 설정합니다. 자세한 내용은 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.
    * 네트워크 대기 시간과 데이터 전송 비용이 증가할 수 있습니다. 클러스터를 생성할 때와 클러스터에서 작업을 실행할 때 대기 시간과 비용이 발생할 수 있습니다.

    NSG 규칙 사용, 사용자 정의 경로 및 입출력 요구 사항과 같은 참고 자료는 작업 영역과 다른 지역을 사용하는 경우에 정상적으로 적용됩니다.

    > [!WARNING]
    > __개인 끝점 사용 작업 영역__ 을 사용 하는 경우 다른 지역에서 클러스터를 만드는 것은 __지원 되지 않습니다__.

### <a name="azure-databricks"></a>Azure Databricks

* Azure Databricks에 사용되는 __databricks-private__ 및 __databricks-public__ 서브넷 외에 가상 네트워크에 대해 생성된 __기본__ 서브넷도 필요합니다.
* Azure Databricks는 가상 네트워크와 통신하는 데 프라이빗 엔드포인트를 사용하지 않습니다.

가상 네트워크에서 Azure Databricks를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network에서 Azure Databricks 배포](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)를 참조하세요.

### <a name="azure-hdinsight-or-virtual-machine"></a>Azure HDInsight 또는 가상 머신

* Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다.

## <a name="required-public-internet-access"></a>필수 공용 인터넷 액세스

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

방화벽 솔루션을 사용하는 방법에 대한 자세한 내용은 [Azure Machine Learning에서 방화벽 사용](how-to-access-azureml-behind-firewall.md)을 참조하세요.

## <a name="compute-clusters"></a><a name="compute-cluster"></a>계산 클러스터

다음 탭을 사용하여 컴퓨팅 클러스터를 만드는 방법을 선택합니다.

# <a name="studio"></a>[스튜디오](#tab/azure-studio)

다음 단계를 수행하여 Azure Machine Learning 스튜디오에서 컴퓨팅 클러스터를 만듭니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인한 다음, 구독과 작업 영역을 선택합니다.
1. 왼쪽에서 __컴퓨팅__ 을 선택하고 가운데에서 __클러스터 컴퓨팅__ 을 선택한 다음 __+ 새로 만들기__ 를 선택합니다.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster.png" alt-text="클러스터 만들기 스크린샷":::

1. __컴퓨팅 클러스터 만들기__ 대화 상자에서 필요한 VM 크기와 구성을 선택한 후 __다음__ 을 선택합니다.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-vm.png" alt-text="VM 구성 설정 스크린샷":::

1. __설정 구성__ 섹션에서 __컴퓨팅 이름__, __가상 네트워크__ 및 __서브넷__ 을 설정합니다.

    :::image type="content" source="media/how-to-enable-virtual-network/create-compute-cluster-config.png" alt-text="계산 이름, 가상 네트워크 및 서브넷 설정을 보여 주는 스크린샷":::

    > [!TIP]
    > 작업 영역이 프라이빗 엔드포인트를 사용하여 가상 네트워크에 연결하는 경우 __가상 네트워크__ 선택 필드가 회색으로 표시됩니다.
    > 

1. 컴퓨팅 클러스터를 만들려면 __만들기__ 를 선택합니다.

# <a name="python"></a>[Python](#tab/python)

다음 코드에서는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 Machine Learning 컴퓨팅 클러스터를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           location="westus2",
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

---

만들기 프로세스가 완료되면 실험에서 클러스터를 사용하여 모델을 학습시킵니다. 자세한 내용은 [학습의 컴퓨팅 대상 선택 및 사용](how-to-set-up-training-targets.md)을 참조하세요.

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="compute-instance"></a>컴퓨팅 인스턴스

가상 네트워크에 배포 된 계산 인스턴스를 만드는 방법에 대 한 단계는 [Azure Machine Learning 계산 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md)를 참조 하세요.

### <a name="no-public-ip-for-compute-instances-preview"></a><a name="no-public-ip"></a>계산 인스턴스에 대 한 공용 IP 없음 (미리 보기)

**공용 ip를 사용 하지 않도록** 설정 하는 경우 compute 인스턴스는 종속성과의 통신에 공용 ip를 사용 하지 않습니다. 대신, Azure 개인 링크 에코 시스템 및 서비스/개인 끝점을 사용 하 여 가상 네트워크 내 에서만 통신 하므로 공용 IP에 대 한 필요성을 완전히 없앨 수 있습니다. 공용 IP 없음 인터넷에서 계산 인스턴스 노드의 액세스 및 검색 기능을 제거 하 여 심각한 위협 벡터를 제거 합니다. 또한 계산 인스턴스는 외부 가상 네트워크에서 트래픽을 거부 하는 패킷 필터링을 수행 합니다. Azure Machine Learning 작업 영역에 대 한 [Azure 개인 링크](how-to-configure-private-link.md) 에 의존 하는 **공용 IP 인스턴스가 없습니다** . 

**아웃 바운드 연결이** 작동 하려면 사용자 정의 경로를 사용 하 여 Azure 방화벽과 같은 송신 방화벽을 설정 해야 합니다. 예를 들어 [invound/아웃 바운드 구성](how-to-access-azureml-behind-firewall.md) 으로 설정 된 방화벽을 사용 하 고 계산 인스턴스가 배포 되는 서브넷에서 경로 테이블을 정의 하 여 트래픽을 라우팅할 수 있습니다. 경로 테이블 항목은 주소 접두사가 0.0.0.0/0 인 방화벽의 개인 IP 주소에 대 한 다음 홉을 설정할 수 있습니다.

공용 ip를 사용 **하지 않는** 계산 인스턴스에는 공용 ip 계산 인스턴스와 비교 하 여 공용 인터넷에서 **들어오는 인바운드 통신 요구 사항이 없습니다** . 특히 인바운드 NSG 규칙 ( `BatchNodeManagement` , `AzureMachineLearning` )은 필요 하지 않습니다. 여전히 **VirtualNetwork** 의 원본, 모든 포트 원본, **VirtualNetwork** 의 대상 포트 및 **29876, 29877, 44224** 의 대상 포트에서 인바운드를 허용 해야 합니다.

**공용 IP가 없는** 계산 인스턴스는 또한 개인 끝점 네트워크 정책 및 개인 링크 서비스 네트워크 정책을 사용 하지 않도록 설정 해야 합니다. 이러한 요구 사항은 Azure 개인 링크 서비스 및 개인 끝점에서 제공 되며 Azure Machine Learning 특정 하지 않습니다. [개인 링크 서비스 원본 IP에 대해 네트워크 정책 사용 안 함](../private-link/disable-private-link-service-network-policy.md) 의 지침에 따라 `disable-private-endpoint-network-policies` `disable-private-link-service-network-policies` 가상 네트워크 서브넷에서 및 매개 변수를 설정 합니다.

스튜디오에서 공용 ip 주소 없음 계산 인스턴스 (미리 보기 기능)를 만들려면 가상 네트워크 섹션에서 **공용 Ip 없음** 확인란을 선택 합니다.
ARM 템플릿을 통해 공용 IP 계산 인스턴스를 만들 수도 있습니다. ARM 템플릿의 set enableNodePublicIP 매개 변수를 false로 설정 합니다.

[!INCLUDE [no-public-ip-info](../../includes/machine-learning-no-public-ip-availibility.md)]

## <a name="inbound-traffic"></a>인바운드 트래픽

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

Azure Machine Learning에 대한 입력 및 출력 트래픽 요구 사항에 대한 자세한 내용은 [방화벽 뒤에서 작업 영역 사용](how-to-access-azureml-behind-firewall.md)을 참조하세요.

## <a name="azure-databricks"></a>Azure Databricks

가상 네트워크에서 Azure Databricks를 사용하는 방법에 대한 자세한 내용은 [Azure Virtual Network에서 Azure Databricks 배포](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)를 참조하세요.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>가상 머신 또는 HDInsight 클러스터

이 섹션에서는 작업 영역이 있는 가상 네트워크의 가상 머신 또는 Azure HDInsight 클러스터를 사용하는 방법에 관해 알아봅니다.

### <a name="create-the-vm-or-hdinsight-cluster"></a>VM 또는 HDInsight 클러스터 만들기

Azure Portal 또는 Azure CLI를 사용하여 VM 또는 HDInsight 클러스터를 만들고 이 클러스터를 Azure 가상 네트워크에 배치합니다. 자세한 내용은 다음 문서를 참조하세요.
* [Linux VM용 Azure 가상 네트워크 만들기 및 관리](../virtual-machines/linux/tutorial-virtual-network.md)

* [Azure 가상 네트워크를 사용하여 HDInsight 확장](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>네트워크 포트 구성 

Azure Machine Learning이 VM 또는 클러스터에서 SSH 포트와 통신할 수 있도록 하려면 네트워크 보안 그룹의 원본 항목을 구성합니다. SSH 포트는 일반적으로 포트 22입니다. 이 원본의 트래픽을 허용하려면 다음 작업을 수행합니다.

1. __원본__ 드롭다운 목록에서 __서비스 태그__ 를 선택합니다.

1. __원본 서비스 태그__ 드롭다운 목록에서 __AzureMachineLearning__ 을 선택합니다.

    ![가상 네트워크 내의 VM 또는 HDInsight 클러스터에서 실험을 수행하기 위한 인바운드 규칙](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. __원본 포트 범위__ 드롭다운 목록에서 __*__ 를 선택합니다.

1. __대상__ 드롭다운 목록에서 __임의__ 를 선택합니다.

1. __대상 포트 범위__ 드롭다운 목록에서 __22__ 를 선택합니다.

1. __프로토콜__ 에서 __임의__ 를 선택합니다.

1. __동작__ 에서 __허용__ 을 선택합니다.

네트워크 보안 그룹에 대해 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](../virtual-network/network-security-groups-overview.md#default-security-rules)의 기본 보안 규칙을 참조하세요.

기본 아웃바운드 규칙을 사용하지 않고 가상 네트워크의 아웃바운드 액세스를 제한하려면 [필수 공용 인터넷 액세스](#required-public-internet-access) 섹션을 참조하세요.

### <a name="attach-the-vm-or-hdinsight-cluster"></a>VM 또는 HDInsight 클러스터 연결

VM 또는 HDInsight 클러스터를 Azure Machine Learning 작업 영역에 연결합니다. 자세한 내용은 [모델 학습의 컴퓨팅 대상 설정](how-to-set-up-training-targets.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.

* [Virtual Network 개요](how-to-network-security-overview.md)
* [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
* [보안 유추 환경](how-to-secure-inferencing-vnet.md)
* [스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)
* [사용자 지정 DNS 사용](how-to-custom-dns.md)
* [방화벽 사용](how-to-access-azureml-behind-firewall.md)
