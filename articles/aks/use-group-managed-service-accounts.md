---
title: AKS (Azure Kubernetes Service) 클러스터에서 서버 노드를 Windows (GMSA) 그룹 관리 서비스 계정 (미리 보기)을 사용 하도록 설정 합니다.
description: pod 보안을 위해 AKS (Azure Kubernetes Service) 클러스터에서 서버 노드를 Windows 하는 데 GMSA (그룹 관리 서비스 계정)를 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 11/01/2021
ms.openlocfilehash: f0acd8cc19ab8a0994b6218a171d8343eef21096
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482893"
---
# <a name="enable-group-managed-service-accounts-gmsa-for-you-windows-server-nodes-on-your-azure-kubernetes-service-aks-cluster-preview"></a>AKS (Azure Kubernetes Service) 클러스터에서 서버 노드를 Windows (GMSA) 그룹 관리 서비스 계정 (미리 보기)을 사용 하도록 설정 합니다.

[GMSA (그룹 관리 서비스 계정)][gmsa-overview] 는 자동 암호 관리, 간소화 된 SPN (서비스 사용자 이름) 관리 및 다른 관리자에 게 관리를 위임 하는 기능을 제공 하는 여러 서버에 대 한 관리 되는 도메인 계정입니다. AKS는 Windows 서버 노드에서 GMSA을 사용 하도록 설정 하는 기능을 제공 합니다 .이 기능을 사용 하면 Windows server 노드에서 실행 중인 컨테이너를에 통합 하 고 GMSA를 통해 관리할 수 있습니다.

AKS에서 Windows Server 노드로 GMSA를 사용 하도록 설정 하는 것이 미리 보기 상태입니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="pre-requisites"></a>필수 구성 요소

AKS에서 Windows 서버 노드로 GMSA를 사용 하도록 설정 하려면 다음이 필요 합니다.

* Kubernetes 1.19 이상.
* `aks-preview`확장 버전 0.5.37 이상입니다.
* 현재 기본값 인 Docker 컨테이너 런타임
* AKS 클러스터를 사용 하 [는 관리 id][aks-managed-id]
* Azure Key Vault를 만들거나 업데이트할 수 있는 권한입니다.
* Active Directory 도메인 서비스 또는 온-프레미스 Active Directory에서 GMSA을 구성할 수 있는 권한.
* 도메인 컨트롤러는 Active Directory 웹 서비스를 사용 하도록 설정 해야 하며, AKS 클러스터에 의해 9389 포트에서 연결할 수 있어야 합니다.

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI 설치

*aks-preview* Azure CLI 확장이 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-akswindowsgmsapreview-preview-feature"></a>`AKSWindowsGmsaPreview` 미리 보기 기능 등록

기능을 사용하려면 구독에서도 `AKSWindowsGmsaPreview` 기능 플래그를 사용하도록 설정해야 합니다.

`AKSWindowsGmsaPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKSWindowsGmsaPreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSWindowsGmsaPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configure-gmsa-on-active-directory-domain-controller"></a>Active Directory 도메인 컨트롤러에서 GMSA 구성

AKS와 함께 GMSA를 사용 하려면 도메인 컨트롤러에 구성 된 GMSA 자격 증명에 액세스 하기 위해 GMSA와 표준 도메인 사용자 자격 증명이 모두 필요 합니다. 도메인 컨트롤러에서 GMSA를 구성 하려면 [그룹 관리 서비스 계정 시작][gmsa-getting-started]을 참조 하세요. 표준 도메인 사용자 자격 증명의 경우 GMSA 자격 증명에 대 한 액세스 권한이 있으면 기존 사용자를 사용 하거나 새 사용자를 만들 수 있습니다.

> [!IMPORTANT]
> Active Directory 도메인 서비스 또는 온-프레미스 Active Directory를 사용 해야 합니다. 지금은 Azure Active Directory를 사용 하 여 AKS 클러스터로 GMSA를 구성할 수 없습니다.

## <a name="store-the-standard-domain-user-credentials-in-azure-key-vault"></a>Azure Key Vault에 표준 도메인 사용자 자격 증명을 저장 합니다.

AKS 클러스터는 표준 도메인 사용자 자격 증명을 사용 하 여 도메인 컨트롤러에서 GMSA 자격 증명에 액세스 합니다. AKS 클러스터에 대 한 자격 증명에 대 한 보안 액세스를 제공 하기 위해 이러한 자격 증명을 Azure Key Vault에 저장 해야 합니다. 새 key vault를 만들거나 기존 key vault를 사용할 수 있습니다.

`az keyvault secret set`를 사용 하 여 표준 도메인 사용자 자격 증명을 키 자격 증명 모음에 암호로 저장 합니다. 다음 예에서는 *MyAKSGMSAVault* key Vault에 *GMSADomainUserCred* 키가 있는 도메인 사용자 자격 증명을 저장 합니다. 매개 변수를 사용자 고유의 키 자격 증명 모음, 키 및 도메인 사용자 자격 증명으로 바꾸어야 합니다.

```azurecli
az keyvault secret set --vault-name MyAKSGMSAVault --name "GMSADomainUserCred" --value "$Domain\\$DomainUsername:$DomainUserPassword"
```

## <a name="optional-use-a-custom-vnet-with-custom-dns"></a>선택 사항: 사용자 지정 DNS와 함께 사용자 지정 VNET 사용

AKS 클러스터에서 연결할 수 있도록 DNS를 통해 도메인 컨트롤러를 구성 해야 합니다. AKS 클러스터 외부에서 네트워크와 DNS를 구성 하 여 클러스터에서 도메인 컨트롤러에 액세스할 수 있도록 할 수 있습니다. 또는 AKS 클러스터에서 Azure CNI를 사용 하 여 사용자 지정 DNS로 사용자 지정 VNET을 구성 하 여 도메인 컨트롤러에 대 한 액세스를 제공할 수 있습니다. 자세한 내용은 azure [Kubernetes 서비스에서 azure CNI 네트워킹 구성 (AKS)][aks-cni]을 참조 하세요.

## <a name="optional-use-your-own-kubelet-identity-for-your-cluster"></a>선택 사항: 클러스터에 대 한 사용자 고유의 kubelet id를 사용 합니다.

AKS 클러스터에 키 자격 증명 모음에 대 한 액세스를 제공 하려면 cluster kubelet identity에 키 자격 증명 모음에 대 한 액세스 권한이 있어야 합니다. 기본적으로 관리 되는 id를 사용 하는 클러스터를 만들 때 kubelet id가 자동으로 만들어집니다. 클러스터를 만든 후에이 id에 대해 키 자격 증명 모음에 대 한 액세스 권한을 부여할 수 있습니다 .이는 이후 단계에서 수행 됩니다.

또는 이후 단계에서 클러스터를 만들 때 고유한 id를 만들고이 id를 사용할 수 있습니다. 제공 된 관리 되는 id에 대 한 자세한 내용은 [관리 되는 Id 요약][aks-managed-id-kubelet]을 참조 하세요.

고유한 id를 만들려면를 사용 `az identity create` 하 여 id를 만듭니다. 다음 예에서는 *Myidentity* 리소스 그룹에 *myidentity* id를 만듭니다.

```azurecli
az identity create --name myIdentity --resource-group myResourceGroup
```

클러스터를 만들기 전이나 후에 주요 자격 증명 모음에 대 한 kubelet id 액세스 권한을 부여할 수 있습니다. 다음 예에서는를 사용 하 여 id `az identity list` id를 가져온 다음를 사용 하  여 `az keyvault set-policy` *MyAKSGMSAVault* key vault에 대 한 id 액세스 권한을 부여 MANAGED_ID로 설정 합니다.

```azurecli
MANAGED_ID=$(az identity list --query "[].id" -o tsv)
az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="create-aks-cluster"></a>AKS 클러스터 만들기

AKS 클러스터와 함께 GMSA를 사용 하려면 *GMSA*, *GMSA*, *GMSA,* 및- *id* 매개 변수를 사용 해야 합니다.

> [!NOTE]
> Windows Server 노드 풀을 사용 하 여 클러스터를 만드는 경우 클러스터를 만들 때 관리자 자격 증명을 지정 해야 합니다. 다음 명령은 사용자 이름을 묻는 프롬프트를 표시하고 나중에 사용할 수 있도록 사용자 이름을 WINDOWS_USERNAME으로 설정합니다(이 문서의 명령은 BASH 셸에 입력됨).
> 
> ```azurecli
> echo "Please enter the username to use as administrator credentials for Windows Server nodes on your cluster: " && read WINDOWS_USERNAME
> ```

`az aks create`를 사용 하 여 AKS 클러스터를 만든 다음 `az aks nodepool add` Windows 서버 노드 풀을 추가 합니다. 다음 예제에서는 *Myresourcegroup* 리소스 그룹에 *MyAKS* 클러스터를 만들고, GMSA를 사용 하도록 설정한 다음 *npwin* 라는 새 노드 풀을 추가 합니다.

> [!NOTE]
> 사용자 지정 vnet을 사용 하는 경우에는 vnet *서브넷 id* 를 사용 하 여 vnet의 id를 지정 해야 하며, 구성에 따라 docker- *주소*, *dns 서비스 ip* 및 *서비스 cidr* 도 추가 해야 할 수도 있습니다.
> 
> Kubelet id에 대 한 고유한 id를 만든 경우 *kubelet* 매개 변수를 사용 하 여 id를 지정 합니다.

```azurecli
DNS_SERVER=<IP address of DNS server>
ROOT_DOMAIN_NAME="contoso.com"

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure \
    --load-balancer-sku standard \
    --windows-admin-username $WINDOWS_USERNAME \
    --enable-managed-identity \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME

az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKS \
    --os-type Windows \
    --name npwin \
    --node-count 1    
```

을 사용 하 여 이미 Windows 서버 노드와 관리 id를 사용 하도록 설정한 기존 클러스터에서 GMSA을 사용 하도록 설정할 수도 있습니다 `az aks update` . 예를 들어:

```azurecli
az aks update \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME
```

클러스터를 만들거나 클러스터를 업데이트 한 후에는를 사용 `az keyvault set-policy` 하 여 키 자격 증명 모음에 대 한 id 액세스 권한을 부여 합니다. 다음 예에서는 클러스터에서 만든 kubelet id에 *MyAKSGMSAVault* key vault에 대 한 액세스 권한을 부여 합니다.

> [!NOTE]
> Kubelet id에 대 한 고유한 id를 제공한 경우이 단계를 건너뜁니다.

```azurecli
MANAGED_ID=$(az aks show -g MyResourceGroup -n MyAKS --query "identityProfile.kubeletidentity.objectId" -o tsv)

az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="install-gmsa-cred-spec"></a>GMSA 자격 증명 사양 설치

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][] 명령을 사용합니다. 다음 예제에서는 *Myresourcegroup* 에서 *MyAKS* 라는 AKS 클러스터에 대 한 자격 증명을 가져옵니다.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

다음을 사용 하 여 *gmsa* 를 만들고 자리 표시자를 사용자 고유의 값으로 바꿉니다.

```yml
apiVersion: windows.k8s.io/v1alpha1
kind: GMSACredentialSpec
metadata:
  name: aks-gmsa-spec  # This name can be changed, but it will be used as a reference in the pod spec
credspec:
  ActiveDirectoryConfig:
    GroupManagedServiceAccounts:
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $NETBIOS_DOMAIN_NAME
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $DNS_DOMAIN_NAME
    HostAccountConfig:
      PluginGUID: '{CCC2A336-D7F3-4818-A213-272B7924213E}'
      PortableCcgVersion: "1"
      PluginInput: ObjectId=$MANAGED_ID;SecretUri=$SECRET_URI  # SECRET_URI takes the form https://$akvName.vault.azure.net/secrets/$akvSecretName
  CmsPlugins:
  - ActiveDirectory
  DomainJoinConfig:
    DnsName: $DNS_DOMAIN_NAME
    DnsTreeName: $DNS_ROOT_DOMAIN_NAME
    Guid:  $AD_DOMAIN_OBJECT_GUID
    MachineAccountName: $GMSA_ACCOUNT_USERNAME
    NetBiosName: $NETBIOS_DOMAIN_NAME
    Sid: $GMSA_SID
```

다음을 사용 하 여 gmsa를 만듭니다 *.*

```yml
#Create the Role to read the credspec
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: aks-gmsa-role
rules:
- apiGroups: ["windows.k8s.io"]
  resources: ["gmsacredentialspecs"]
  verbs: ["use"]
  resourceNames: ["aks-gmsa-spec"]
```

다음을 사용 하 여 gmsa를 만듭니다 *.*

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: allow-default-svc-account-read-on-aks-gmsa-spec
  namespace: default
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
roleRef:
  kind: ClusterRole
  name: aks-gmsa-role
  apiGroup: rbac.authorization.k8s.io
```

`kubectl apply` *Gmsa*, *gmsa* 및 *gmsa* 의 변경 내용을 적용 하려면를 사용 하 고, 그렇지 않으면를 사용 합니다.

```azurecli
kubectl apply -f gmsa-spec.yaml
kubectl apply -f gmsa-role.yaml
kubectl apply -f gmsa-role-binding.yaml
```

## <a name="verify-gmsa-is-installed-and-working"></a>GMSA가 설치 되어 있고 작동 하는지 확인 합니다.

다음을 사용 하 여 gmsa를 만듭니다 *.*

```yml
---
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
   app: gmsa-demo
  name: gmsa-demo
  namespace: default
data:
  run.ps1: |
   $ErrorActionPreference = "Stop"

   Write-Output "Configuring IIS with authentication."

   # Add required Windows features, since they are not installed by default.
   Install-WindowsFeature "Web-Windows-Auth", "Web-Asp-Net45"

   # Create simple ASP.Net page.
   New-Item -Force -ItemType Directory -Path 'C:\inetpub\wwwroot\app'
   Set-Content -Path 'C:\inetpub\wwwroot\app\default.aspx' -Value 'Authenticated as <B><%=User.Identity.Name%></B>, Type of Authentication: <B><%=User.Identity.AuthenticationType%></B>'

   # Configure IIS with authentication.
   Import-Module IISAdministration
   Start-IISCommitDelay
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/windowsAuthentication').Attributes['enabled'].value = $true
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/anonymousAuthentication').Attributes['enabled'].value = $false
   (Get-IISServerManager).Sites[0].Applications[0].VirtualDirectories[0].PhysicalPath = 'C:\inetpub\wwwroot\app'
   Stop-IISCommitDelay

   Write-Output "IIS with authentication is ready."

   C:\ServiceMonitor.exe w3svc
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gmsa-demo
  template:
    metadata:
      labels:
        app: gmsa-demo
    spec:
      securityContext:
        windowsOptions:
          gmsaCredentialSpecName: aks-gmsa-spec
      containers:
      - name: iis
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019
        imagePullPolicy: IfNotPresent
        command:
         - powershell
        args:
          - -File
          - /gmsa-demo/run.ps1
        volumeMounts:
          - name: gmsa-demo
            mountPath: /gmsa-demo
      volumes:
      - configMap:
          defaultMode: 420
          name: gmsa-demo
        name: gmsa-demo
      nodeSelector:
        kubernetes.io/os: windows
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: gmsa-demo
  type: LoadBalancer
```

`kubectl apply`Gmsa에서 변경 내용을 적용 하는 데 사용 *합니다.*

```azurecli
kubectl apply -f gmsa-demo.yaml
```

`kubectl get service`을 사용 하 여 예제 응용 프로그램의 IP 주소를 표시 합니다.

```console
kubectl get service gmsa-demo --watch
```

처음에는 *gmsa* 서비스에 대 한 *외부 IP* 가 *보류 중* 으로 표시 됩니다.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
gmsa-demo          LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* 주소가 *보류 중* 에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

```output
gmsa-demo  LoadBalancer   10.0.37.27   EXTERNAL-IP   80:30572/TCP   2m
```

GMSA가 정상적으로 작동 하 고 구성 되었는지 확인 하려면 *GMSA* service의 외부 IP 주소에 대 한 웹 브라우저를 엽니다. 및 암호를 사용 하 여 인증 하 `$NETBIOS_DOMAIN_NAME\$AD_USERNAME` 고 확인 `Authenticated as $NETBIOS_DOMAIN_NAME\$AD_USERNAME, Type of Authentication: Negotiate` 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="no-authentication-is-prompted-when-loading-the-page"></a>페이지를 로드할 때 인증 메시지가 표시 되지 않음

페이지가 로드 되지만 인증 하 라는 메시지가 표시 되지 않으면를 사용 하 여 `kubelet logs POD_NAME` pod의 로그를 표시 하 고 *인증 된 IIS가 준비 되어* 있는지 확인 합니다.

### <a name="connection-timeout-when-trying-to-load-the-page"></a>페이지를 로드 하는 동안 연결 시간이 초과 되었습니다.

페이지를 로드 하려고 할 때 연결 시간 제한이 발생 하면 샘플 앱이로 실행 중인지 확인 `kubectl get pods --watch` 합니다. 샘플 앱 pod가 실행 되기 전에 샘플 app service에 대 한 외부 IP 주소를 사용할 수 있는 경우가 있습니다.

### <a name="pod-fails-to-start-and-an-winapi-error-shows-in-the-pod-events"></a>Pod가 시작 되지 않고 pod 이벤트에 *winapi 오류가* 표시 됩니다.

실행 `kubectl get pods --watch` 하 고 몇 분 동안 기다린 후 pod가 시작 되지 않으면를 실행 `kubectl describe pod POD_NAME` 합니다. Pod 이벤트에 *winapi 오류가* 표시 되 면 GMSA 자격 증명 사양 구성에서 오류가 발생할 수 있습니다. *Gmsa* 의 모든 교체 값이 올바른지 확인 하 고, 다시 실행 하 `kubectl apply -f gmsa-spec.yaml` 고, 샘플 응용 프로그램을 다시 배포 합니다.


[aks-cni]: configure-azure-cni.md
[aks-managed-id]: use-managed-identity.md
[aks-managed-id-kubelet]: use-managed-identity.md#summary-of-managed-identities
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[gmsa-getting-started]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[rdp]: rdp.md