---
title: AKS(Azure Kubernetes Service)에 대한 서비스 주체
description: AKS(Azure Kubernetes Service)에서 클러스터에 대한 Azure Active Directory 서비스 주체 만들기 및 관리
services: container-service
ms.topic: conceptual
ms.date: 04/22/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 332866c49470ed47f3c3de65b03ffd07003f6d13
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530435"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)를 사용하는 서비스 주체

Azure API와 상호 작용하기 위해서 AKS 클러스터는 [Azure AD(Active Directory) 서비스 주체][aad-service-principal] 또는 [관리 ID](use-managed-identity.md)가 필요합니다. Azure Load Balancer 또는 ACR(컨테이너 레지스트리)과 같은 다른 Azure 리소스를 동적으로 생성하고 관리하려면 서비스 주체 또는 관리 ID가 필요합니다.

이 문서에서는 AKS 클러스터에 대한 서비스 주체를 만들고 사용하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure AD 서비스 주체를 만들려면 Azure AD 테넌트에 애플리케이션을 등록하고 구독의 역할에 해당 애플리케이션을 할당할 수 있는 사용 권한이 있어야 합니다. 필요한 사용 권한이 없으면 필요한 사용 권한을 할당하거나 AKS 클러스터로 사용하기 위한 서비스 주체를 미리 만들도록 Azure AD 또는 구독 관리자에 요청해야 합니다.

다른 Azure AD 테넌트의 서비스 주체를 사용하는 경우 클러스터를 배포할 때 사용 가능한 사용 권한에 대한 추가 고려 사항이 있습니다. 디렉터리 정보를 읽고 쓸 수 있는 적절한 권한이 없을 수 있습니다. 자세한 내용은 [Azure Active Directory의 기본 사용자 권한이란?][azure-ad-permissions] 페이지를 참조하세요.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

또한 Azure PowerShell 버전 5.0.0 이상이 설치되어 있어야 합니다. `Get-InstalledModule -Name Az`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure Az PowerShell 모듈 설치][install-the-azure-az-powershell-module]를 참조하세요.

---

## <a name="automatically-create-and-use-a-service-principal"></a>자동으로 서비스 주체 만들기 및 사용

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Portal에서 또는 [az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만들 때 Azure는 서비스 주체를 자동으로 생성할 수 있습니다.

다음 Azure CLI 예제에서는 서비스 주체가 지정되지 않았습니다. 이 시나리오에서 Azure CLI는 AKS 클러스터에 대한 서비스 주체를 만듭니다. 작업을 성공적으로 완료하려면 Azure 계정에는 서비스 주체를 만드는 적절한 권한이 있어야 합니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure Portal에서 또는 [New-AzAksCluster][new-azakscluster] 명령을 사용하여 AKS 클러스터를 만들 때 Azure는 서비스 주체를 자동으로 생성할 수 있습니다.

다음 Azure PowerShell 예제에서는 서비스 주체가 지정되지 않았습니다. 이 시나리오에서는 Azure PowerShell이 AKS 클러스터에 대한 서비스 주체를 만듭니다. 작업을 성공적으로 완료하려면 Azure 계정에는 서비스 주체를 만드는 적절한 권한이 있어야 합니다.

```azurepowershell-interactive
New-AzAksCluster -Name myAKSCluster -ResourceGroupName myResourceGroup
```

> [!NOTE]
> 오류 "서비스 주체 clientID: 00000000-0000-0000-000000000000000을 Active Directory 테넌트 0000000-0000-0000-00000-00000-000000000000000에서 찾을 수 없음"의 경우 [추가 고려 사항](#additional-considerations)을 참조하여 `acsServicePrincipal.json` 파일을 제거하세요.

---
## <a name="manually-create-a-service-principal"></a>수동으로 서비스 주체 만들기

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 서비스 주체를 수동으로 만들려면 [az ad sp create-for-rbac][az-ad-sp-create] 명령을 사용합니다. 다음 예제에서 `--skip-assignment` 매개 변수는 추가 기본 할당이 할당되는 것을 방지합니다.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

다음 예제와 유사하게 출력됩니다. 사용자 고유의 `appId` 및 `password`를 기록해 둡니다. 이러한 값은 다음 섹션에서 AKS 클러스터를 만들 때 사용됩니다.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell에서 수동으로 서비스 주체를 만들려면 [New-AzADServicePrincipal][new-azadserviceprincipal] 명령을 사용합니다. 다음 예제에서 `-SkipAssignment` 매개 변수는 추가 기본 할당이 할당되는 것을 방지합니다.

```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName myAKSClusterServicePrincipal -SkipAssignment -OutVariable sp
```

다음 예제와 유사하게 출력됩니다. 이 값도 다음 섹션에서 AKS 클러스터를 만들 때 사용할 변수에 저장됩니다.

```Output
Secret                : System.Security.SecureString
ServicePrincipalNames : {559513bd-0c19-4c1a-87cd-851a26afd5fc, http://myAKSClusterServicePrincipal}
ApplicationId         : 559513bd-0c19-4c1a-87cd-851a26afd5fc
ObjectType            : ServicePrincipal
DisplayName           : myAKSClusterServicePrincipal
Id                    : 559513bd-0c19-4c1a-87cd-851a26afd5fc
Type                  :
```

**비밀** 보안 문자열에 저장된 값의 암호를 해독하려면 다음 예제를 사용합니다.

```azurepowershell-interactive
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
[System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

자세한 내용은 [Azure PowerShell을 사용하여 Azure 서비스 주체 만들기][create-an-azure-service-principal-with-azure-powershell]를 참조하세요.

---

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>AKS 클러스터에 대한 서비스 주체 지정

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만들 때 기존 서비스 주체를 사용하려면 `--service-principal` 및 `--client-secret` 매개 변수를 사용하여 [az ad sp create-for-rbac][az-ad-sp-create] 명령의 출력에서 `appId` 및 `password`를 지정합니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> 사용자 지정 암호로 기존 서비스 주체를 사용하는 경우에 암호가 190바이트보다 길지 않은지 확인합니다.

Azure Portal을 사용하여 AKS 클러스터를 배포하는 경우 **Kubernetes 클러스터 만들기** 대화 상자의 *인증* 페이지에서 **서비스 주체를 구성** 하도록 선택합니다. **기존 항목 사용** 을 선택하고, 다음 값을 지정합니다.

- **서비스 주체 클라이언트 ID** 는 사용자의 *appId* 입니다.
- **서비스 주체 클라이언트 비밀** 은 *암호* 값입니다.

![Azure Vote로 이동하는 이미지](media/kubernetes-service-principal/portal-configure-service-principal.png)

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

AKS 클러스터를 만들 때 기존 서비스 주체를 사용하려면 다음 예제에서 보듯 서비스 주체 `ApplicationId` 및 `Secret`를 **PSCredential** 개체로 변환해야 합니다.

```azurepowershell-interactive
$Cred = New-Object -TypeName System.Management.Automation.PSCredential ($sp.ApplicationId, $sp.Secret)
```

`New-AzAksCluster` 명령을 실행할 때, 앞서 만든 **PSCredential** 개체를 해당 값으로 사용하여 `ServicePrincipalIdAndSecret` 매개 변수를 지정합니다.

```azurepowershell-interactive
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -ServicePrincipalIdAndSecret $Cred
```

> [!NOTE]
> 사용자 지정 암호로 기존 서비스 주체를 사용하는 경우에 암호가 190바이트보다 길지 않은지 확인합니다.

Azure Portal을 사용하여 AKS 클러스터를 배포하는 경우 **Kubernetes 클러스터 만들기** 대화 상자의 *인증* 페이지에서 **서비스 주체를 구성** 하도록 선택합니다. **기존 항목 사용** 을 선택하고, 다음 값을 지정합니다.

- **서비스 주체 클라이언트 ID** 는 사용자의 *ApplicationId* 입니다.
- **서비스 주체 클라이언트 암호** 는 암호 해독된 *비밀* 값입니다.

![Azure Vote로 이동하는 이미지](media/kubernetes-service-principal/portal-configure-service-principal.png)

---

## <a name="delegate-access-to-other-azure-resources"></a>다른 Azure 리소스에 대한 액세스 권한 위임

AKS 클러스터에 대한 서비스 주체를 사용하여 다른 리소스에 액세스할 수 있습니다. 예를 들어 AKS 클러스터를 기존 Azure 가상 네트워크 서브넷에 배포하거나 ACR(Azure Container Registry)에 연결하려면 해당 리소스에 대한 액세스를 서비스 주체에 위임해야 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

권한을 위임하려면 [az role assignment create][az-role-assignment-create] 명령을 사용하여 역할 할당을 만듭니다. `appId`를 리소스 그룹 또는 가상 네트워크 리소스와 같은 특정 범위에 할당합니다. 그러면, 역할은 다음 예제에 표시된 것처럼 서비스 주체가 리소스에 대해 가진 사용 권한을 정의합니다.

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

리소스에 대한 `--scope`는 전체 리소스 ID여야 합니다(예: */subscriptions/\<guid\>/resourceGroups/myResourceGroup* 또는 */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*).

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

권한을 위임하려면 [New-AzRoleAssignment][new-azroleassignment] 명령을 사용하여 역할 할당을 만듭니다. `ApplicationId`를 리소스 그룹 또는 가상 네트워크 리소스와 같은 특정 범위에 할당합니다. 그러면, 역할은 다음 예제에 표시된 것처럼 서비스 주체가 리소스에 대해 가진 사용 권한을 정의합니다.

```azurepowershell-interactive
New-AzRoleAssignment -ApplicationId <ApplicationId> -Scope <resourceScope> -RoleDefinitionName Contributor
```

리소스에 대한 `Scope`는 전체 리소스 ID여야 합니다(예: */subscriptions/\<guid\>/resourceGroups/myResourceGroup* 또는 */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*).

---

> [!NOTE]
> 노드 리소스 그룹에서 기여자 역할 할당을 제거한 경우, 아래 작업이 실패할 수 있습니다.
> 시스템 관리 ID를 사용하는 클러스터에 대한 권한 부여는 채우는 데 최대 60분 정도 걸릴 수 있습니다.

다음 섹션에서는 만들 필요가 있는 일반적인 위임에 대해 자세히 설명합니다.

### <a name="azure-container-registry"></a>Azure Container Registry

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ACR(Azure Container Registry)을 컨테이너 이미지 스토리지로 사용하는 경우 이미지를 읽고 끌어오기 위해서는 AKS 클러스터에 대한 사용 권한을 서비스 주체에 부여해야 합니다. 현재 권장되는 구성은 [az aks create][az-aks-create] 또는 [az aks update][az-aks-update] 명령을 사용하여 레지스트리와 통합하고 서비스 주체에 적절한 역할을 할당하는 것입니다. 자세한 단계는 Azure Kubernetes Service의 Azure Container Registry를 사용하여 [인증][aks-to-acr]을 참조하세요.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

ACR(Azure Container Registry)을 컨테이너 이미지 스토리지로 사용하는 경우 이미지를 읽고 끌어오기 위해서는 AKS 클러스터에 대한 사용 권한을 서비스 주체에 부여해야 합니다. 현재 권장되는 구성은 [New-AzAksCluster][new-azakscluster] 또는 [Set-AzAksCluster][set-azakscluster] 명령을 사용하여 레지스트리와 통합하고 서비스 주체에 적절한 역할을 할당하는 것입니다. 자세한 단계는 Azure Kubernetes Service의 Azure Container Registry를 사용하여 [인증][aks-to-acr]을 참조하세요.

---

### <a name="networking"></a>네트워킹

가상 네트워크와 서브넷 또는 공용 IP 주소가 다른 리소스 그룹에 있는 고급 네트워킹을 사용할 수 있습니다. 또는 가상 네트워크 내에서 서브넷에 [네트워크 기여자][rbac-network-contributor] 기본 제공 역할을 할당합니다. 또는 해당 리소스 그룹의 네트워크 리소스에 액세스할 수 있는 권한이 있는 [사용자 지정 역할][rbac-custom-role]을 만들 수 있습니다. 자세한 내용은 [AKS 서비스 사용 권한][aks-permissions]을 참조하세요.

### <a name="storage"></a>스토리지

다른 리소스 그룹에 있는 기존 디스크 리소스에 액세스해야 할 수 있습니다. 다음 역할 권한 집합 중 하나를 할당합니다.

- [사용자 지정 역할][rbac-custom-role]을 만들고 다음 역할 권한을 정의합니다.
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- 또는 리소스 그룹에 [Storage 계정 기여자][rbac-storage-contributor] 기본 제공 역할을 할당합니다.

### <a name="azure-container-instances"></a>Azure Container Instances

Virtual Kubelet을 사용하여 AKS와 통합하고 AKS 클러스터와 별도로 리소스 그룹에서 ACI(Azure Container Instances)를 실행하도록 선택하는 경우, AKS 서비스 주체에 ACI 리소스 그룹에 대한 ‘Contributor’ 권한을 부여해야 합니다.

## <a name="additional-considerations"></a>기타 고려 사항

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

AKS와 Azure AD 서비스 주체를 사용하는 경우 다음 고려 사항을 유의하세요.

- Kubernetes에 대한 서비스 주체는 클러스터 구성의 일부입니다. 그러나 클러스터를 배포하는 데에는 이 ID를 사용하지 마세요.
- 기본적으로 서비스 주체 자격 증명은 1년 동안 유효합니다. 언제든지 [서비스 주체 자격 증명을 업데이트 또는 회전][update-credentials]할 수 있습니다.
- 모든 서비스 주체는 Azure AD 애플리케이션과 연결됩니다. Kubernetes 클러스터에 대한 서비스 주체는 유효한 모든 Azure AD 애플리케이션 이름(예: *https://www.contoso.org/example* )과 연결할 수 있습니다. 애플리케이션에 대한 URL은 실제 엔드포인트일 필요가 없습니다.
- 서비스 주체 **클라이언트 ID** 를 지정할 때 `appId` 값을 사용합니다.
- Kubernetes 클러스터의 에이전트 및 노드 VM에서 서비스 주체 자격 증명은 `/etc/kubernetes/azure.json` 파일에 저장
- [az aks create][az-aks-create] 명령을 사용하여 서비스 주체를 자동으로 생성하는 경우 서비스 주체 자격 증명은 명령을 실행하는 데 사용되는 머신의 `~/.azure/aksServicePrincipal.json` 파일에 기록됩니다.
- 추가 AKS CLI 명령에서 특정 서비스 주체를 전달하지 않으면 `~/.azure/aksServicePrincipal.json`에 있는 기본 서비스 주체가 사용됩니다.
- 선택에 따라 aksServicePrincipal.json 파일을 제거하고 AKS에서 새로운 서비스 주체를 만들 수도 있습니다.
- [az aks create][az-aks-create]로 만든 AKS 클러스터를 삭제하는 경우 자동으로 생성된 서비스 주체는 삭제되지 않습니다.
    - 서비스 주체를 삭제하려면 클러스터에 *servicePrincipalProfile.clientId* 를 쿼리한 다음, [az ad sp delete][az-ad-sp-delete]로 삭제합니다. 다음 리소스 그룹과 클러스터 이름을 고유한 값으로 바꿉니다.

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

AKS와 Azure AD 서비스 주체를 사용하는 경우 다음 고려 사항을 유의하세요.

- Kubernetes에 대한 서비스 주체는 클러스터 구성의 일부입니다. 그러나 클러스터를 배포하는 데에는 이 ID를 사용하지 마세요.
- 기본적으로 서비스 주체 자격 증명은 1년 동안 유효합니다. 언제든지 [서비스 주체 자격 증명을 업데이트 또는 회전][update-credentials]할 수 있습니다.
- 모든 서비스 주체는 Azure AD 애플리케이션과 연결됩니다. Kubernetes 클러스터에 대한 서비스 주체는 유효한 모든 Azure AD 애플리케이션 이름(예: *https://www.contoso.org/example* )과 연결할 수 있습니다. 애플리케이션에 대한 URL은 실제 엔드포인트일 필요가 없습니다.
- 서비스 주체 **클라이언트 ID** 를 지정할 때 `ApplicationId` 값을 사용합니다.
- Kubernetes 클러스터의 에이전트 및 노드 VM에서 서비스 주체 자격 증명은 `/etc/kubernetes/azure.json` 파일에 저장
- [New-AzAksCluster][new-azakscluster] 명령을 사용하여 서비스 주체를 자동으로 생성하는 경우 서비스 주체 자격 증명은 명령을 실행하는 데 사용되는 머신의 `~/.azure/acsServicePrincipal.json` 파일에 기록됩니다.
- 추가 AKS PowerShell 명령에서 특정 서비스 주체를 전달하지 않으면 `~/.azure/acsServicePrincipal.json`에 있는 기본 서비스 주체가 사용됩니다.
- 선택에 따라 acsServicePrincipal.json 파일을 제거하고 AKS에서 새로운 서비스 주체를 만들 수도 있습니다.
- [New-AzAksCluster][new-azakscluster]로 만든 AKS 클러스터를 삭제하는 경우 자동으로 생성된 서비스 주체는 삭제되지 않습니다.
    - 서비스 주체를 삭제하려면 *ServicePrincipalProfile.ClientId* 클러스터를 쿼리한 다음, [Remove-AzADServicePrincipal][remove-azadserviceprincipal]로 삭제합니다. 다음 리소스 그룹과 클러스터 이름을 고유한 값으로 바꿉니다.

        ```azurepowershell-interactive
        $ClientId = (Get-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster ).ServicePrincipalProfile.ClientId
        Remove-AzADServicePrincipal -ApplicationId $ClientId
        ```
---

## <a name="troubleshoot"></a>문제 해결

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

AKS 클러스터에 대한 서비스 주체 자격 증명은 Azure CLI에 의해 캐시됩니다. 해당 자격 증명이 만료되면, AKS 클러스터를 배포할 때 오류가 발생합니다. [az aks create][az-aks-create]를 실행할 때 나타나는 다음 오류 메시지는 캐시된 서비스 주체 자격 증명에 문제가 있음을 나타낼 수 있습니다.

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

다음 명령을 사용하여 자격 증명 파일의 유효 기간을 확인합니다.

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

서비스 주체 자격 증명은 보통 1년 뒤에 만료됩니다. *aksServicePrincipal.json* 파일을 1년 넘게 사용했다면, 파일을 삭제하고 AKS 클러스터를 다시 배포합니다.

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

AKS 클러스터에 대한 서비스 주체 자격 증명은 Azure PowerShell에 의해 캐시됩니다. 해당 자격 증명이 만료되면, AKS 클러스터를 배포할 때 오류가 발생합니다. [New-AzAksCluster][new-azakscluster]를 실행할 때 나타나는 다음 오류 메시지는 캐시된 서비스 주체 자격 증명에 문제가 있음을 나타낼 수 있습니다.

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

다음 명령을 사용하여 자격 증명 파일의 유효 기간을 확인합니다.

```azurepowershell-interactive
Get-ChildItem -Path $HOME/.azure/aksServicePrincipal.json
```

서비스 주체 자격 증명은 보통 1년 뒤에 만료됩니다. *aksServicePrincipal.json* 파일을 1년 넘게 사용했다면, 파일을 삭제하고 AKS 클러스터를 다시 배포합니다.

---

## <a name="next-steps"></a>다음 단계

Azure Active Directory 서비스 주체에 대한 자세한 내용은 [애플리케이션 및 서비스 주체 개체][service-principal]를 참조하세요.

자격 증명을 업데이트하는 방법에 대한 자세한 내용은 [AKS의 서비스 주체에 대한 자격 증명 업데이트 또는 회전][update-credentials]을 참조하세요.

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az_ad_app_list
[az-ad-app-delete]: /cli/azure/ad/app#az_ad_app_delete
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
[install-the-azure-az-powershell-module]: /powershell/azure/install-az-ps
[new-azakscluster]: /powershell/module/az.aks/new-azakscluster
[new-azadserviceprincipal]: /powershell/module/az.resources/new-azadserviceprincipal
[create-an-azure-service-principal-with-azure-powershell]: /powershell/azure/create-azure-service-principal-azureps
[new-azroleassignment]: /powershell/module/az.resources/new-azroleassignment
[set-azakscluster]: /powershell/module/az.aks/set-azakscluster
[remove-azadserviceprincipal]: /powershell/module/az.resources/remove-azadserviceprincipal
