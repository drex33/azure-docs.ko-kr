---
title: 클러스터에 대한 자격 증명을 다시 설정합니다
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service(AKS)에 대한 서비스 주체 또는 ADD 애플리케이션 자격 증명을 업데이트하거나 다시 설정하는 방법을 알아봅니다
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 72cc0c6ab369b035df8c4a29c89be74fa9102cc5
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101520"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)에 대한 자격 증명 업데이트 및 순환

서비스 주체와 함께 만들어진 AKS 클러스터는 만료 시간이 1년입니다. 만료 날짜가 가까워지면 자격 증명을 다시 설정하여 서비스 주체를 추가 기간 동안 연장할 수 있습니다. 정의된 보안 정책의 일부로 자격 증명을 업데이트하거나 회전할 수도 있습니다. 이 문서에서는 AKS 클러스터의 이 자격 증명을 업데이트하는 방법을 자세히 설명합니다.

[AKS 클러스터를 Azure Active Directory와 통합][aad-integration]하고 클러스터에 대한 인증 공급자로 사용할 수도 있습니다. 이 경우 클러스터, AAD 서버 앱 및 AAD 클라이언트 앱에 대해 생성된 id가 2개 더 있으므로 해당 자격 증명을 재 설정할 수도 있습니다.

또는 서비스 주체 대신 사용 권한에 대해 관리 ID를 사용할 수 있습니다. 관리되는 id는 서비스 주체 보다 관리가 용이하며 업데이트 또는 순환이 필요하지 않습니다. 자세한 내용은 [관리 ID 사용](use-managed-identity.md)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.65 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>AKS 클러스터에 대한 새 서비스 주체를 업데이트하거나 만듭니다

AKS 클러스터의 자격 증명을 업데이트하려는 경우 다음을 선택할 수 있습니다.

* 기존 서비스 주체의 자격 증명을 업데이트하세요.
* 새 서비스 주체를 만들고 이 새 자격 증명을 사용하도록 클러스터를 업데이트합니다. 

> [!WARNING]
> *새* 서비스 주체를 만들기로 선택한 경우 서비스 주체 권한이 모든 지역에 전파될 때까지 약 30분 정도 기다립니다. 이러한 자격 증명을 사용하도록 큰 AKS 클러스터를 업데이트하려면 완료하는 데 시간이 오래 걸릴 수 있습니다.

### <a name="check-the-expiration-date-of-your-service-principal"></a>서비스 주체의 만료 날짜를 확인합니다

서비스 주체의 만료 날짜를 확인하려면 [az ad sp credential list][az-ad-sp-credential-list] 명령을 사용합니다. 다음 예제에서는 [az aks show][az-aks-show] 명령을 사용하여 *myResourceGroup* 이라는 리소스 그룹에서 *myAKSCluster* 라는 클러스터의 서비스 주체 ID를 가져옵니다. 서비스 사용자 ID는 [az ad sp credential list][az-ad-sp-credential-list] 명령과 함께 사용할 *SP_ID* 라는 변수로 설정됩니다.

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id "$SP_ID" --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>기존 서비스 주체 자격 증명 재설정

기존 서비스 주체의 자격 증명을 업데이트하려면 [az aks show][az-aks-show] 명령을 사용하여 클러스터의 서비스 주체 ID를 가져옵니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myAKSCluster* 클러스터의 ID를 가져옵니다. 서비스 주체 ID는 추가 명령에서 사용할 *SP_ID* 라는 변수로 설정됩니다. 이러한 명령은 Bash 구문을 사용합니다.

> [!WARNING]
> Azure Virtual Machine Scale Sets를 사용하는 AKS 클러스터에서 클러스터 자격 증명을 다시 설정하는 경우 새 자격 증명 정보를 사용하여 노드를 업데이트하기 위해 [노드 이미지 업그레이드가][node-image-upgrade] 수행됩니다.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

서비스 주체 ID가 포함된 변수 세트와 함께, 이제 [az ad sp credential reset][az-ad-sp-credential-reset]을 사용하여 자격 증명을 다시 설정합니다. 다음 예제에서는 Azure 플랫폼에서 서비스 주체의 새 보안 비밀을 생성해보겠습니다. 이 새 보안 비밀도 변수로 저장됩니다.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name "$SP_ID" --query password -o tsv)
```

이제 계속해서 [새 서비스 주체 자격 증명으로 AKS 클러스터를 업데이트](#update-aks-cluster-with-new-service-principal-credentials)합니다. 이 단계는 AKS 클러스터에 서비스 주체 변경 내용을 반영하기 위해 필요합니다.

### <a name="create-a-new-service-principal"></a>새 서비스 주체를 만들기

이전 섹션에서 기존 서비스 주체 자격 증명을 업데이트하도록 선택한 경우 이 단계를 건너뜁니다. 계속해서 [새 서비스 주체 자격 증명으로 AKS 클러스터를 업데이트](#update-aks-cluster-with-new-service-principal-credentials)합니다.

서비스 주체를 만든 후 이 새 자격 증명을 사용하도록 AKS 클러스터를 업데이트하려면 [az ad sp create-for-rbac][az-ad-sp-create] 명령을 사용합니다. 다음 예제에서 `--skip-assignment` 매개 변수는 추가 기본 할당이 할당되는 것을 방지합니다.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

다음 예제와 유사하게 출력됩니다. 사용자 고유의 `appId` 및 `password`를 기록해 둡니다. 해당 값은 다음 단계에서 사용됩니다.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

다음 예제와 같이 고유한 [az ad sp create-for-rbac][az-ad-sp-create] 명령의 출력을 사용하여 서비스 주체 ID 및 클라이언트 암호의 변수를 정의합니다. *SP_ID* 는 사용자의 ‘앱 ID’이고, *SP_SECRET* 은 사용자의 ‘암호’입니다.

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

이제 계속해서 [새 서비스 주체 자격 증명으로 AKS 클러스터를 업데이트](#update-aks-cluster-with-new-service-principal-credentials)합니다. 이 단계는 AKS 클러스터에 서비스 주체 변경 내용을 반영하기 위해 필요합니다.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>새 서비스 주체 자격 증명으로 AKS 클러스터 업데이트

> [!IMPORTANT]
> 대량 클러스터의 경우 새 서비스 주체를 사용하여 AKS 클러스터 업데이트를 완료되는데 시간이 오래 걸릴 수 있습니다. 클러스터 업데이트 및 업그레이드 중 중단을 최소화하려면 [노드 서지 업그레이드 설정][node-surge-upgrade]을 검토하고 사용자 지정하는 것이 좋습니다.

기존 서비스 주체의 자격 증명을 업데이트하거나 서비스 주체를 만들도록 선택했는지 여부에 관계없이 이제 [az aks update-credentials][az-aks-update-credentials] 명령을 사용하여 새 자격 증명으로 AKS 클러스터를 업데이트합니다. *--service-principal* 및 *--client-secret* 의 변수는 다음과 같이 사용됩니다.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal "$SP_ID" \
    --client-secret "$SP_SECRET"
```

중소 규모의 클러스터의 경우 서비스 주체 자격 증명이 AKS에서 업데이트되는데 몇 분 정도 걸립니다.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>새 AAD 애플리케이션 자격 증명으로 AKS 클러스터 업데이트

[AAD 통합 단계][create-aad-app]를 수행하여 새 AAD 서버 및 클라이언트 애플리케이션을 만들 수 있습니다. 또는 [서비스 주체 재설정에 대한 것과 동일한 방법으로](#reset-the-existing-service-principal-credential)기존 AAD 애플리케이션을 다시 설정합니다. 그 후에는 동일한 [az aks update-credentials][az-aks-update-credentials] 명령을 사용하지만 *--reset-aad* 변수를 사용하여 클러스터 AAD 애플리케이션 자격 증명을 업데이트하기만 하면 됩니다.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터 자체와 AAD 통합 애플리케이션 서비스 주체를 업데이트 했습니다. 클러스터 내에서 워크로드의 ID를 관리하는 방법에 대한 자세한 내용은 [AKS의 인증 및 권한 부여 모범 사례][best-practices-identity]를 참조하세요.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-update-credentials]: /cli/azure/aks#az_aks_update_credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az_ad_sp_credential_list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[node-image-upgrade]: ./node-image-upgrade.md
[node-surge-upgrade]: upgrade-cluster.md#customize-node-surge-upgrade