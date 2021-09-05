---
title: Azure Kubernetes Service(AKS)로 마이그레이션
description: Azure Kubernetes Service(AKS)로 마이그레이션.
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c162a782965156545cf238f8f0489409433a0345
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535576"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)로 마이그레이션

이 가이드에서는 AKS(Azure Kubernetes Service)에 대한 성공적인 마이그레이션을 계획하고 실행하는 데 도움을 주기 위해 현재 권장되는 AKS 구성에 대한 세부 정보를 제공합니다. 이 문서에서는 모든 시나리오에 대해 설명하지 않지만 성공적인 마이그레이션 계획에 대한 자세한 정보를 볼 수 있는 링크를 포함하고 있습니다.

이 문서를 통해 다음과 같은 시나리오를 지원할 수 있습니다.

* [Azure Migrate](../migrate/migrate-services-overview.md)를 사용하여 특정 애플리케이션을 컨테이너화하고 AKS로 마이그레이션.
* [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md)으로 지원되는 AKS 클러스터를 [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md)로 마이그레이션.
* [표준 SKU 부하 분산 장치](./load-balancer-standard.md)를 사용하도록 AKS 클러스터 마이그레이션.
* [ACS(Azure Container Service) - 2020년 1월 31일 사용 중지](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)에서 AKS로 마이그레이션.
* [AKS 엔진](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)에서 AKS로 마이그레이션.
* 비 Azure 기반 Kubernetes 클러스터에서 AKS로 마이그레이션.
* 기존 리소스를 다른 지역으로 이동.

마이그레이션 할 때 대상 Kubernetes 버전이 AKS에 대한 지원 범위에 속하는지 확인합니다. 이전 버전은 지원되는 범위 내에 있지 않을 수 있으며 AKS에서 지원되는 버전으로 업그레이드해야 합니다. 자세한 내용은 [AKS 지원 Kubernetes 버전](./supported-kubernetes-versions.md)을 참조하세요.

최신 버전의 Kubernetes로 마이그레이션하려는 경우 [Kubernetes 버전 및 버전 기울기 지원 정책](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)을 검토하세요.

시나리오에 따라 몇 가지 오픈 소스 도구를 사용하여 쉽게 마이그레이션할 수 있습니다.

* [Velero](https://velero.io/)(Kubernetes 1.7 이상 필요)
* [Azure Kube CLI 확장](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

이 문서에서는 다음에 대한 마이그레이션 세부 정보를 요약합니다.

> [!div class="checklist"]
> * Azure Migrate를 통해 애플리케이션 컨테이너화 
> * Azure 표준 Load Balancer AKS 및 Virtual Machine Scale Sets
> * 기존에 연결된 Azure 서비스
> * 유효한 할당량 확인
> * 고가용성 및 비즈니스 연속성
> * 상태 비저장 애플리케이션에 대한 고려 사항
> * 상태 저장 애플리케이션에 대한 고려 사항
> * 클러스터 구성 배포

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Azure Migrate을 사용하여 애플리케이션을 AKS로 마이그레이션합니다

Azure Migrate는 온-프레미스 서버, 인프라, 애플리케이션 및 데이터를 평가하고 Azure로 마이그레이션할 수 있는 통합 플랫폼을 제공합니다. AKS의 경우 다음과 같은 작업에 Azure Migrate를 사용할 수 있습니다.

* [ASP.NET 애플리케이션 컨테이너화 및 AKS로 마이그레이션](../migrate/tutorial-app-containerization-aspnet-kubernetes.md)
* [Java 웹 애플리케이션 컨테이너화 및 AKS로 마이그레이션](../migrate/tutorial-app-containerization-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>Azure 표준 Load Balancer AKS 및 Virtual Machine Scale Sets

AKS는 관리 오버헤드가 낮은 고유한 기능을 제공하는 관리되는 서비스입니다. AKS는 관리되는 서비스이므로 AKS에서 지원하는 [지역](./quotas-skus-regions.md) 집합에서 선택해야 합니다. 기존 클러스터에서 AKS로 전환하는 동안 AKS 관리 제어 평면에서 기존 애플리케이션을 정상 상태로 유지하기 위해 수정해야 할 수 있습니다.

[Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml) 및 [Azure 표준 Load Balancer](./load-balancer-standard.md)로 지원되는 AKS 클러스터를 사용하여 다음과 같은 기능을 확보하는 것이 좋습니다.
* [여러 노드 풀](./use-multiple-node-pools.md),
* [가용성 영역](../availability-zones/az-overview.md),
* [권한 있는 IP 범위](./api-server-authorized-ip-ranges.md),
* [클러스터 자동 크기 조정기](./cluster-autoscaler.md),
* [AKS에 대한 Azure Policy](../governance/policy/concepts/policy-for-kubernetes.md) 및
* 출시된 다른 새로운 기능.

[가상 컴퓨터 가용성 집합](../virtual-machines/availability.md#availability-sets)으로 지원되는 AKS 클러스터는 이러한 기능 중 상당수를 지원하지 않습니다.

다음 예제에서는 VM(가상 머신) 확장 집합으로 지원되는 단일 노드 풀로 AKS 클러스터를 만듭니다. 이 클러스터는
* 표준 부하 분산 장치를 사용합니다. 
* 클러스터의 노드 풀에서 클러스터 자동 크기 조정기를 사용하도록 설정합니다.
* 최소 *1개* 에서 최대 *3* 개의 노드를 설정합니다.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>기존에 연결된 Azure 서비스

클러스터를 마이그레이션할 때 외부 Azure 서비스를 연결했을 수 있습니다. 다음 서비스에는 리소스 재작성이 필요하지 않지만 기능을 유지하려면 이전 클러스터에서 새 클러스터로의 연결을 업데이트해야 합니다.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* 스토리지 계정
* 외부 데이터베이스

## <a name="ensure-valid-quotas"></a>유효한 할당량 확인

마이그레이션 중에 다른 VM이 구독에 배포되므로 할당량과 한도가 이러한 리소스에 충분한지 확인해야 합니다. 필요한 경우 [vCPU 할당량](../azure-portal/supportability/per-vm-quota-requests.md)증가를 요청합니다.

[네트워크 할당량](../azure-portal/supportability/networking-quota-requests.md)에 대한 증가를 요청하여 ip가 고갈되지 않도록 해야 할 수도 있습니다. 자세한 내용은 [AKS의 네트워킹 및 IP 범위](./configure-kubenet.md)를 참조하세요.

자세한 내용은 [Azure 구독 및 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요. 현재 할당량을 확인하려면 Azure Portal [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동하여 구독을 선택한 다음 **사용량 + 할당량** 을 선택합니다.

## <a name="high-availability-and-business-continuity"></a>고가용성 및 비즈니스 연속성

애플리케이션에서 가동 중지 시간을 처리할 수 없는 경우 고가용성 마이그레이션 시나리오에 대한 모범 사례를 따라야 합니다. [AKS(Azure Kubernetes Service)에서 복잡한 비즈니스 연속성 계획, 재해 복구 및 작동 시간 극대화를 위한 모범 사례](./operator-best-practices-multi-region.md)에 대해 자세히 알아보세요.

복잡한 애플리케이션은 일반적으로 한 번에 전부 마이그레이션하기보다는 점차 마이그레이션을 수행합니다. 따라서 이전 환경과 새 환경이 네트워크를 통해 통신해야 할 수 있습니다. 이전에 `ClusterIP` 서비스를 사용하여 통신하던 애플리케이션은 `LoadBalancer` 유형으로 노출하고 적절하게 보호해야 할 수 있습니다.

마이그레이션을 완료하려면 클라이언트가 AKS에서 실행되는 새 서비스를 가리키도록 합니다. 트래픽을 리디렉션하는 경우 AKS 클러스터 앞에 있는 Load Balancer를 가리키도록 DNS를 업데이트하는 것이 좋습니다.

[Azure Traffic Manager](../traffic-manager/index.yml)는 고객에게 원하는 Kubernetes 클러스터 및 애플리케이션 인스턴스로 보낼 수 있습니다. Traffic Manager는 네트워크 트래픽을 여러 지역에 분산할 수 있는 DNS 기반 트래픽 부하 분산 디바이스입니다. 최상의 성능 및 중복도를 위해 AKS 클러스터로 보내기 전에 Traffic Manager를 통해 모든 애플리케이션 트래픽을 전달합니다. 

다중 클러스터 배포에서는 고객이 각 AKS 클러스터의 서비스를 가리키는 Traffic Manager DNS 이름에 연결해야 합니다. 이러한 서비스는 Traffic Manager 엔드포인트를 사용하여 정의합니다. 각 엔드포인트는 *서비스 부하 분산 장치 IP* 입니다. 이 구성을 사용하여 한 지역의 Traffic Manager 엔드포인트에서 다른 지역의 엔드포인트로 네트워크 트래픽을 보낼 수 있습니다.

![Traffic Manager와 ASK](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](../frontdoor/front-door-overview.md)는 AKS 클러스터에 대한 트래픽을 라우팅하는 또 다른 옵션입니다. Azure Front Door Service를 사용하면 최적의 성능과 고가용성을 지원하는 즉시 글로벌 장애 조치(failover)를 최적으로 구현하여 웹 트래픽의 글로벌 라우팅을 정의, 관리, 모니터링할 수 있습니다. 

### <a name="considerations-for-stateless-applications"></a>상태 비저장 애플리케이션에 대한 고려 사항

상태 비저장 애플리케이션 마이그레이션은 가장 간단한 경우입니다.
1. 리소스 정의(YAML 또는 Helm)를 새 클러스터에 적용합니다.
1. 모두 예상대로 작동하는지 확인합니다.
1. 트래픽을 리디렉션하여 새 클러스터를 활성화합니다.

### <a name="considerations-for-stateful-applications"></a>상태 저장 애플리케이션에 대한 고려 사항

데이터 손실 또는 예기치 않은 가동 중지 시간을 방지하기 위해 상태 저장 애플리케이션의 마이그레이션을 신중하게 계획합니다.

* Azure Files를 사용하는 경우 파일 공유를 새 클러스터에 볼륨으로 탑재할 수 있습니다. [정적 Azure Files를 볼륨으로 탑재](./azure-files-volume.md#mount-file-share-as-an-persistent-volume)를 참조하세요.
* Azure Managed Disks를 사용하는 경우 VM에 연결되지 않은 경우에만 디스크를 탑재할 수 있습니다. [정적 Azure Disk를 볼륨으로 탑재](./azure-disk-volume.md#mount-disk-as-volume)를 참조하세요.
* 이러한 방법이 작동하지 않는 경우 백업 및 복원 옵션을 사용할 수 있습니다. [Azure의 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)를 참조하세요.

#### <a name="azure-files"></a>Azure 파일

디스크와 달리 Azure Files는 여러 호스트에 동시에 탑재할 수 있습니다. AKS 클러스터에서 Azure 및 Kubernetes는 AKS 클러스터가 여전히 사용하는 Pod를 만드는 것을 방해하지 않습니다. 데이터 손실 및 예기치 않은 동작을 방지하려면 클러스터가 같은 파일에 동시에 쓰지 않도록 합니다.

애플리케이션에서 동일한 파일 공유를 가리키는 여러 복제본을 호스팅할 수 있는 경우, 상태 비저장 마이그레이션 단계를 수행하고 YAML 정의를 새 클러스터에 배포합니다. 

그렇지 않은 경우 한 가지 가능한 마이그레이션 방법에 다음 단계가 포함됩니다.

1. 애플리케이션이 올바르게 작동하는지 확인합니다.
1. 새 AKS 클러스터에 대한 라이브 트래픽을 가리킵니다.
1. 이전 클러스터의 연결을 끊습니다.

빈 공유로 시작한 다음, 원본 데이터의 복사본을 만들려면 [`az storage file copy`](/cli/azure/storage/file/copy) 명령을 사용하여 데이터를 마이그레이션할 수 있습니다.


#### <a name="migrating-persistent-volumes"></a>영구적 볼륨 마이그레이션

기존 영구 볼륨을 AKS로 마이그레이션하는 경우 일반적으로 다음 단계를 수행합니다.

1. 애플리케이션에 쓰기 작업을 정지합니다. 
    * 이 단계는 선택 사항이며 가동 중지 시간이 필요합니다.
1. 디스크의 스냅숏을 생성합니다.
1. 스냅샷에서 새 관리 디스크 만들기.
1. AKS에서 영구적 볼륨 만들기.
1. PersistentVolumeClaims(정적 프로비전) 대신 [기존 볼륨을 사용](./azure-disk-volume.md)하도록 Pod 사양 업데이트.
1. 애플리케이션을 AKS에 배포합니다.
1. 애플리케이션이 올바르게 작동하는지 확인합니다.
1. 새 AKS 클러스터에 대한 라이브 트래픽을 가리킵니다.

> [!IMPORTANT]
> 쓰기 작업을 정지하지 않으려면 새 배포에 데이터를 복제해야 합니다. 그렇지 않으면 디스크 스냅샷을 만든 후 작성된 데이터를 놓치게 됩니다.

일부 오픈 소스 도구는 관리 디스크를 만들고 Kubernetes 클러스터 간에 볼륨을 마이그레이션하는 데 도움이 될 수 있습니다.

* [Azure CLI Disk Copy 확장](https://github.com/noelbundick/azure-cli-disk-copy-extension)은 리소스 그룹 및 Azure 지역에서 디스크를 복사하고 변환합니다.
* [Azure Kube CLI extension](https://github.com/yaron2/azure-kube-cli)는 ACS Kubernetes 볼륨을 열거하고, 이를 AKS 클러스터로 마이그레이션합니다.


### <a name="deployment-of-your-cluster-configuration"></a>클러스터 구성 배포

기존 지속적인 통합(CI) 및 지속적인 배달(CD) 파이프라인을 사용하여 AKS에 알려진 양호한 구성을 배포하는 것이 좋습니다. Azure Pipelines를 사용하여 [애플리케이션을 빌드하고 AKS에 배포](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)할 수 있습니다. 기존 배포 작업을 복제하고 `kubeconfig`에서 새 AKS 클러스터를 가리키는지 확인합니다.

불가능한 경우 기존 Kubernetes 클러스터에서 리소스 정의를 내보낸 다음 AKS에 적용합니다. `kubectl`을 사용하여 개체를 내보낼 수 있습니다. 예를 들면 다음과 같습니다.

```console
kubectl get deployment -o yaml > deployments.yaml
```

출력을 검사하고 불필요한 라이브 데이터 필드를 제거해야 합니다.

### <a name="moving-existing-resources-to-another-region"></a>기존 리소스를 다른 영역으로 이동

AKS 클러스터를 [AKS가 지원하는 다른 지역][region-availability]으로 이동할 수도 있습니다. 다른 지역에 새 클러스터를 만든 다음, 리소스 및 애플리케이션을 새 클러스터에 배포하는 것이 좋습니다. 

또한 AKS 클러스터에서 실행 중인 서비스가 있는 경우 해당 서비스를 새 지역의 클러스터에 설치하고 구성해야 합니다.


이 문서에서는 다음에 대한 마이그레이션 세부 정보를 요약합니다.

> [!div class="checklist"]
> * Azure 표준 Load Balancer AKS 및 Virtual Machine Scale Sets
> * 기존에 연결된 Azure 서비스
> * 유효한 할당량 확인
> * 고가용성 및 비즈니스 연속성
> * 상태 비저장 애플리케이션에 대한 고려 사항
> * 상태 저장 애플리케이션에 대한 고려 사항
> * 클러스터 구성 배포


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service