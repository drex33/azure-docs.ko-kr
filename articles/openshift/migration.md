---
title: Azure Red Hat OpenShift 3.11에서 Azure Red Hat OpenShift 4로 마이그레이션
description: Azure Red Hat OpenShift 3.11에서 Azure Red Hat OpenShift 4로 마이그레이션
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/13/2020
keywords: 마이그레이션, ARO, OpenShift, Red Hat
ms.openlocfilehash: 9f166b393350971a48b23420daa9917b6ba7bbe1
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038043"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 3.11에서 Azure Red Hat OpenShift 4로 마이그레이션

OpenShift 4의 Azure Red Hat OpenShift는 Red Hat 핵심 OS의 Kubernetes 1.16, 프라이빗 클러스터, 사용자 고유의 가상 네트워크 지원, 전체 클러스터 관리자 역할을 제공합니다. 또한 이제는 운영자 프레임워크, 운영자 허브, OpenShift 서비스 메시에 대한 지원과 같은 많은 새로운 기능을 사용할 수 있습니다.

Azure Red Hat OpenShift 3.11에서 Azure Red Hat OpenShift 4로 전환하려면 [스토리지, 네트워킹, 로깅, 보안, 모니터링의 차이점](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html)을 검토해야 합니다.

이 문서에서는 Azure Red Hat OpenShift 3.11 클러스터에서 Azure Red Hat 4 클러스터로 마이그레이션하는 방법을 설명합니다.

> [!NOTE]
> 컨트롤 플레인 마이그레이션 지원 도구 및 CAM(클러스터 애플리케이션 마이그레이션 도구)과 같은 Red Hat OpenShift 마이그레이션 도구는 Azure Red Hat OpenShift 3.11 클러스터와 함께 사용할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 Azure Red Hat OpenShift 3.11 클러스터가 있다고 가정합니다.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>대상 Azure Red Hat OpenShift 4 클러스터 만들기

먼저 대상 클러스터로 사용할 [Azure Red Hat OpenShift 4 클러스터를 만듭니다](tutorial-create-cluster.md). 여기서는 기본 구성을 사용합니다. 다른 설정에 관심이 있다면 [Azure Red Hat OpenShift 4 클러스터 만들기 자습서](tutorial-create-cluster.md)를 참조하세요.

마스터와 작업자 노드를 위한 두 개의 빈 서브넷으로 가상 네트워크를 만듭니다.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

그런 다음, 다음 명령을 사용하여 클러스터를 만듭니다.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>대상 OpenShift 4 클러스터 구성

### <a name="authentication"></a>인증

사용자가 Azure Red Hat OpenShift와 상호 작용하려면 먼저 클러스터에 인증을 해야 합니다. 인증 레이어는 Azure Red Hat OpenShift API에 대한 요청과 연결된 사용자를 식별합니다. 그러면 권한 부여 레이어에서 요청하는 사용자에 대한 정보를 사용하여 요청이 허용되는지 확인합니다.

Azure Red Hat OpenShift 4 클러스터가 만들어지면 임시 관리자가 만들어집니다. [클러스터에 연결](tutorial-connect-cluster.md)하고, 사용자 및 그룹을 추가하고, 둘 다에 대한 [적절 한 권한을 구성](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html)합니다.

### <a name="networking"></a>네트워킹

Azure Red Hat OpenShift 4는 클러스터에서 네트워크를 설정하는 데 [클러스터 네트워크 운영자](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [DNS 운영자](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)및 [수신 운영자](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html)와 같은 몇 가지 운영자를 사용합니다. Azure Red Hat OpenShift 4 클러스터에서 네트워킹을 설정하는 방법에 대한 자세한 내용은 [네트워킹 다이어그램](concepts-networking.md)과 [네트워킹 이해](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html)를 참조하세요.

### <a name="storage"></a>스토리지
Azure Red Hat OpenShift 4는 다음 PersistentVolume 플러그 인을 지원합니다.

- AWS EBS(Elastic Block Store)
- Azure Disk
- Azure 파일
- GCE 영구적 디스크
- HostPath
- iSCSI
- 로컬 볼륨
- NFS
- Red Hat OpenShift 컨테이너 스토리지

이러한 스토리지 유형 구성에 대한 자세한 내용은 [영구적 스토리지 구성](https://access.redhat.com/documentation/en-us/openshift_container_platform/4.7/html/storage/configuring-persistent-storage)을 참조하세요.

### <a name="registry"></a>레지스트리

Azure Red Hat OpenShift 4는 소스 코드에서 이미지를 빌드하고, 배포하고, 이미지의 수명 주기를 관리할 수 있습니다. 이를 사용하기 위해 Azure Red Hat OpenShift 4는 Azure Red Hat OpenShift 환경에 배포하여 이미지를 로컬로 관리할 수 있는 [내부 통합 컨테이너 이미지 레지스트리](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html)를 제공합니다.

[Azure Container Registry](../container-registry/index.yml), [Red Hat Quay 레지스트리](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options), 또는 [인증을 사용하는 Red Hat 레지스트리](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options)와 같은 외부 레지스트리를 사용하는 경우, 클러스터에서 리포지토리에 액세스할 수 있도록 클러스터에 자격 증명을 제공하는 단계를 수행합니다.

### <a name="monitoring"></a>모니터링

Azure Red Hat OpenShift에는 Prometheus 오픈 소스 프로젝트 및 더 광범위한 에코 시스템을 기반으로 하는 미리 구성되고 사전 설치된 자체 업데이트 모니터링 스택이 포함됩니다. 클러스터 구성 요소에 대한 모니터링을 제공하며, 발생하는 모든 문제와 Grafana 대시보드의 집합에 대해 클러스터 관리자에게 즉시 알리기 위한 경고 집합을 제공합니다. 클러스터 모니터링 스택은 Azure Red Hat OpenShift 클러스터 모니터링에만 지원됩니다. 자세한 내용은 [Azure Red Hat OpenShift에 대한 클러스터 모니터링](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html)을 참조하세요.

[Azure Red Hat OpenShift 3.11용 컨테이너의 Azure Monitor](../azure-monitor/containers/container-insights-azure-redhat-setup.md)를 사용하고 있었다면, [Azure Red Hat OpenShift 4 클러스터](../azure-monitor/containers/container-insights-azure-redhat4-setup.md)용 컨테이너의 Azure Monitor를 사용하도록 설정하고 동일한 Log Analytics 작업 영역을 계속 사용할 수도 있습니다.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>DNS 또는 부하 분산 장치를 새 클러스터로 이동

Azure Traffic Manager를 이용하고 있다면 엔드포인트를 추가하여 대상 클러스터를 참조하고 이러한 엔드포인트의 우선 순위를 지정합니다.

## <a name="deploy-application-to-your-target-cluster"></a>대상 클러스터에 애플리케이션 배포

워크로드에 대상 클러스터를 적절히 구성했으면 [클러스터에 연결](tutorial-connect-cluster.md)하고 프로젝트에 필요한 애플리케이션, 구성 요소, 또는 서비스를 만듭니다. Azure Red Hat OpenShift를 사용하면 이러한 항목을 Git, 컨테이너 이미지, Red Hat 개발자 카탈로그, Dockerfile, YAML/JSON 정의에서 만들거나 카탈로그에서 데이터베이스 서비스를 선택하여 만들 수 있습니다.

## <a name="delete-your-source-cluster"></a>원본 클러스터 삭제
Azure Red Hat OpenShift 4 클러스터가 제대로 설정되었는지 확인했으면 Azure Red Hat OpenShift 3.11 클러스터를 삭제합니다.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>다음 단계
[여기](https://docs.openshift.com/container-platform/4.6/welcome/index.html)에서 Red Hat OpenShift 설명서를 확인하세요.
