---
title: Azure Kubernetes Service에서 관리 ID 사용
description: AKS(Azure Kubernetes Service)에서 관리 ID를 사용하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 05/12/2021
ms.openlocfilehash: dbc02f8b65235a47fc523665ea6337774a6eb557
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530575"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service에서 관리 ID 사용

현재 AKS(Azure Kubernetes Service) 클러스터(특히 Kubernetes 클라우드 공급자)가 Azure의 부하 분산 장치, 관리 디스크처럼 추가 리소스를 만들려면 ID가 필요합니다. 이 ID는 *관리 ID* 또는 *서비스 주체* 일 수 있습니다. [서비스 주체](kubernetes-service-principal.md)를 사용하는 경우 서비스 주체를 하나 제공해야 합니다. 아니면 AKS에서 사용자를 대신하여 서비스 주체를 하나 만듭니다. 관리 ID를 사용하는 경우 AKS에서 자동으로 생성합니다. 서비스 주체를 사용하는 클러스터는 결국 클러스터 작동을 유지하기 위해 서비스 주체를 갱신해야 하는 상태가 됩니다. 서비스 주체 관리는 더 복잡하므로 대신 관리 ID를 사용하기가 더 쉽습니다. 서비스 주체와 관리 ID에는 둘 다 동일한 권한 요구 사항이 적용됩니다.

*관리 ID* 는 기본적으로 서비스 주체의 래퍼이며 관리를 간소화합니다. Azure Active Directory 기본값에 따라 46일마다 MI의 자격 증명이 자동으로 순환됩니다. AKS는 시스템 할당 및 사용자 할당 관리 ID 유형을 모두 사용합니다. 이러한 ID는 현재 변경할 수 없습니다. 자세한 정보는 [Azure 리소스의 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스가 설치되어 있어야 합니다.

- Azure CLI 버전 2.23.0 이상

## <a name="limitations"></a>제한 사항

* 관리 ID를 지원하는 클러스터의 테넌트 이동/마이그레이션은 지원되지 않습니다.
* 클러스터에 `aad-pod-identity`가 설정된 경우 NMI(Node Managed Identity) Pod는 Azure Instance Metadata 엔드포인트에 대한 호출을 가로채는 노드의 iptables를 수정합니다. 이 구성은 Pod가 `aad-pod-identity`을(를) 사용하지 않는 경우에도 Metadata 엔드포인트에 대한 모든 요청을 NMI가 가로챈다는 의미입니다. AzurePodIdentityException CRD는 CRD에 정의된 레이블과 일치하는 Pod에서 Metadata 엔드포인트로 전송되는 모든 요청을 NMI에서 처리하지 않고 프록시되어야 함을 `aad-pod-identity`에 알리도록 구성할 수 있습니다. AzurePodIdentityException CRD를 구성하여 _kube-system_ 네임스페이스에 `kubernetes.azure.com/managedby: aks` 레이블이 지정된 시스템 Pod를 `aad-pod-identity`에서 제외해야 합니다. 자세한 내용은 [특정 Pod 또는 애플리케이션에 대한 aad-pod-identity 사용 안 함](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)을 참조하세요.
  예외를 구성하려면 [mic-exception YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)을 설치합니다.

## <a name="summary-of-managed-identities"></a>관리 ID 요약

AKS는 기본 제공 서비스 및 추가 항목에 대해 여러 관리 ID를 사용합니다.

| ID                       | 이름    | 사용 사례 | 기본 권한 | 사용자 고유의 ID 가져오기
|----------------------------|-----------|----------|
| 제어 평면 | AKS 클러스터 이름 | AKS 컨트롤 플레인 구성 요소에서 수신 부하 분산 장치 및 AKS 관리 공용 IP, 클러스터 자동 크기 조정기, Azure 디스크 및 파일 CSI 드라이버를 관리하는 데 사용합니다. | 노드 리소스 그룹에 대한 기여자 역할 | 지원 여부
| kubelet | AKS Cluster Name-agentpool | ACR(Azure Container Registry)에 인증 | 해당 없음(kubernetes v1.15 이상의 경우) | 지원 여부
| 추가 기능 | AzureNPM | ID가 필요 없음 | 해당 없음 | 아니요
| 추가 기능 | AzureCNI network monitoring | ID가 필요 없음 | 해당 없음 | 아니요
| 추가 기능 | azure-policy(gatekeeper) | ID가 필요 없음 | 해당 없음 | 아니요
| 추가 기능 | azure-policy | ID가 필요 없음 | 해당 없음 | 아니요
| 추가 기능 | Calico | ID가 필요 없음 | 해당 없음 | 아니요
| 추가 기능 | 대시보드 | ID가 필요 없음 | 해당 없음 | 아니요
| 추가 기능 | HTTPApplicationRouting | 필요한 네트워크 리소스 관리 | 노드 리소스 그룹에 대한 읽기 권한자 역할, DNS 영역에 대한 기여자 역할 | 아니요
| 추가 기능 | 수신 애플리케이션 게이트웨이 | 필요한 네트워크 리소스 관리| 노드 리소스 그룹에 대한 기여자 역할 | 아니요
| 추가 기능 | omsagent | Azure Monitor에 AKS 메트릭을 전송하는 데 사용 | 모니터링 메트릭 게시자 역할 | 아니요
| 추가 기능 | Virtual-Node(ACIConnector) | ACI(Azure Container Instances)에 필요한 네트워크 리소스를 관리합니다. | 노드 리소스 그룹에 대한 기여자 역할 | 아니요
| OSS 프로젝트 | aad-pod-identity | 애플리케이션이 AAD(Azure Active Directory)를 사용하여 클라우드 리소스에 안전하게 액세스할 수 있도록 합니다. | 해당 없음 | 권한을 부여하는 단계(https://github.com/Azure/aad-pod-identity#role-assignment )

## <a name="create-an-aks-cluster-with-managed-identities"></a>관리 ID로 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용하여 관리 ID로 AKS 클러스터를 만들 수 있습니다.

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

그런 다음, AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

클러스터를 만든 후에는 애플리케이션 워크로드를 새 클러스터에 배포하고, 서비스 주체 기반 AKS 클러스터에서 하는 것처럼 상호 작용할 수 있습니다.

마지막으로, 클러스터에 액세스하는 데 사용할 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities"></a>AKS 클러스터를 관리되는 ID로 업데이트

이제 다음 CLI 명령을 사용하여 현재 서비스 주체와 함께 작동하는 AKS 클러스터를 관리 ID와 함께 작동하도록 업데이트할 수 있습니다.

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```
> [!NOTE]
> 업데이트 후에는 클러스터의 컨트롤 플레인과 추가 기능 pod가 관리 ID를 사용하도록 전환되지만 kubelet은 agentpool을 업그레이드할 때까지 서비스 주체를 계속 사용합니다. 노드에서 `az aks nodepool upgrade --node-image-only`를 수행하여 관리 ID에 대한 업데이트를 완료합니다. 


## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>AKS 클러스터용 시스템 할당 관리 ID 가져오기 및 사용

다음 CLI 명령을 사용하여 AKS 클러스터에서 관리 ID를 사용 중인지 확인합니다.

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

클러스터에서 관리 ID를 사용하는 경우 "msi"의 `clientId` 값이 표시됩니다. 대신 서비스 주체를 사용하는 클러스터는 개체 ID를 표시합니다. 예를 들면 다음과 같습니다. 

```output
{
  "clientId": "msi"
}
```

클러스터에서 관리 ID를 사용하고 있는지 확인한 후 다음 명령을 사용하여 컨트롤 플레인 시스템 할당 ID의 개체 ID를 찾을 수 있습니다.

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> 고유한 VNet, 고정 IP 주소 또는 리소스가 작업자 노드 리소스 그룹의 외부에 있는, 연결된 Azure 디스크를 만들고 사용하려면 클러스터 시스템 할당 관리 ID의 PrincipalID를 사용하여 역할 할당을 수행합니다. 역할 할당에 대한 자세한 내용은 [다른 Azure 리소스에 대한 액세스 권한 위임](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)을 참조하세요.
>
> Azure 클라우드 공급자가 사용하는 클러스터 관리 ID에 대한 권한 부여를 채우는 데 최대 60분 정도 걸릴 수 있습니다.


## <a name="bring-your-own-control-plane-mi"></a>사용자 고유의 컨트롤 평면 MI 가져오기
사용자 지정 컨트롤 평면 ID를 사용하면 클러스터를 만들기 전에 기존 ID에 액세스 권한을 부여할 수 있습니다. 이 기능은 미리 만든 관리 ID로 사용자 지정 VNET 또는 outboundType의 UDR을 사용하는 등의 시나리오를 지원합니다.

Azure CLI 버전 2.15.1 이상이 설치되어 있어야 합니다.

### <a name="limitations"></a>제한 사항
* Azure Government의 미국 국방부 중부, 미국 국방부 동부, 미 정부 아이오와는 현재 지원되지 않습니다.

아직 관리 ID가 없는 경우에는 [az identity CLI][az-identity-create] 등을 사용하여 새로 만들어야 합니다.

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
결과는 다음과 같습니다.

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

관리 ID가 구독의 일부인 경우 [az identity CLI 명령][az-identity-list]을 사용하여 쿼리할 수 있습니다.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

이제 다음 명령을 사용하여 기존 ID로 클러스터를 만들 수 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id>
```

사용자 고유의 관리 ID를 사용하여 클러스터를 성공적으로 만들면 다음 userAssignedIdentities 프로필 정보가 포함됩니다.

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="bring-your-own-kubelet-mi"></a>사용자 고유의 kubelet MI 가져오기

Kubelet ID를 사용하면 클러스터를 만들기 전에 기존 ID에 대한 액세스 권한을 부여할 수 있습니다. 이 기능을 사용하면 미리 만든 관리 ID를 사용하여 ACR에 연결과 같은 시나리오를 사용할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

- Azure CLI 버전 2.26.0 이상이 설치되어 있어야 합니다.

### <a name="limitations"></a>제한 사항

- User-Assigned 관리 클러스터에서만 작동합니다.
- Azure China 21Vianet의 중국 동부, 중국 북부는 현재 지원되지 않습니다.

### <a name="create-or-obtain-managed-identities"></a>관리 ID 만들기 또는 가져오기

아직 컨트롤 플레인에서 관리되는 ID가 없는 경우 계속 진행하여 새로 만들어야 합니다. 다음 예는 [az identity create][az-identity-create] 명령을 사용합니다.

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```

결과는 다음과 같습니다.

```output
{                                  
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Kubelet 관리 ID가 아직 없는 경우 계속해서 만들어야 합니다. 다음 예는 [az identity create][az-identity-create] 명령을 사용합니다.

```azurecli-interactive
az identity create --name myKubeletIdentity --resource-group myResourceGroup
```

결과는 다음과 같습니다.

```output
{
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity", 
  "location": "westus2",
  "name": "myKubeletIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

기존 관리 ID가 구독의 일부인 경우 [az identity list][az-identity-list] 명령을 사용하여 쿼리할 수 있습니다.

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

### <a name="create-a-cluster-using-kubelet-identity"></a>Kubelet ID를 사용하여 클러스터 만들기

이제 다음 명령을 사용하여 기존 ID를 사용하여 클러스터를 만들 수 있습니다. `assign-identity`를 통해 컨트롤 플레인 ID를 제공하고 `assign-kublet-identity`를 통해 kubelet 관리 ID를 제공합니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
    --assign-kubelet-identity <kubelet-identity-id>
```

사용자 고유의 kubelet 관리 ID를 사용하여 성공적으로 클러스터를 만들려면 다음 출력이 포함됩니다.

```output
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
        "clientId": "<client-id>",
        "principalId": "<principal-id>"
      }
    }
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "<client-id>",
      "objectId": "<object-id>",
      "resourceId": "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity"
    }
  },
```

## <a name="next-steps"></a>다음 단계
* [Azure Resource Manager 템플릿][aks-arm-template]을 사용하여 관리 ID 사용 클러스터를 만듭니다.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-list]: /cli/azure/identity#az_identity_list
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
