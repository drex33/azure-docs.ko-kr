---
title: AKS(Azure Kubernetes Service)에서 인증서 회전
description: AKS(Azure Kubernetes Service) 클러스터에서 인증서를 회전하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 11/03/2021
ms.openlocfilehash: 655aeb296c53fd3006d05f91e4200fa70e80506d
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133480989"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 인증서 회전

AKS(Azure Kubernetes Service)는 여러 구성 요소에 대한 인증을 위해 인증서를 사용합니다. 보안 또는 정책상의 이유로 인증서를 주기적으로 회전해야 할 수 있습니다. 예를 들어 90일마다 모든 인증서를 회전하는 정책이 있을 수 있습니다.

이 문서에서는 AKS 클러스터에서 인증서를 회전하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하려면 Azure CLI 버전 2.0.77 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS 인증서, 인증 기관 및 서비스 계정

AKS는 다음 인증서, 인증 기관, 서비스 계정을 생성하고 사용합니다.

* AKS API 서버는 클러스터 CA라는 CA(인증 기관)를 생성합니다.
* API 서버에는 클러스터 CA가 있으며, 클러스터 CA는 API 서버에서 Kubelet으로의 단방향 통신 인증서에 서명합니다.
* 또한 각 Kubelet은 Kubelet에서 API 서버로의 통신을 위해 클러스터 CA가 서명한 CSR(인증서 서명 요청)을 생성합니다.
* API 애그리게이터는 클러스터 CA를 사용하여 다른 API와의 통신을 위한 인증서를 발급합니다. API 애그리게이터는 해당 인증서를 발급하기 위한 자체 CA도 가질 수 있지만, 현재는 클러스터 CA를 사용하고 있습니다.
* 각 노드는 클러스터 CA에서 서명한 SA(서비스 계정) 토큰을 사용합니다.
* `kubectl` 클라이언트는 AKS 클러스터와 통신하기 위한 인증서를 가지고 있습니다.

> [!NOTE]
> 2019년 5월 이전에 생성된 AKS 클러스터에는 2년 후에 만료되는 인증서가 있습니다. 2019년 5월 이후에 생성된 클러스터 또는 인증서가 회전된 클러스터에는 30년 후에 만료되는 클러스터 CA 인증서가 있습니다. 서명에 클러스터 CA를 사용하는 다른 모든 AKS 인증서는 2년 후에 만료되며 AKS 버전 업그레이드 중에 자동으로 순환됩니다. 클러스터가 생성된 시기를 확인하려면 `kubectl get nodes`를 사용하여 노드 풀의 ‘사용 기간’을 확인하세요.
> 
> 또한 클러스터 인증서의 만료 날짜를 확인할 수 있습니다. 예를 들어 다음 bash 명령은 리소스 그룹 *rg* 의 *myAKSCluster* 클러스터에 대한 클라이언트 인증서 세부 정보를 표시합니다.
> ```console
> kubectl config view --raw -o jsonpath="{.users[?(@.name == 'clusterUser_rg_myAKSCluster')].user.client-certificate-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

* apiserver 인증서의 만료 날짜 확인
```console
curl https://{apiserver-fqdn} -k -v 2>&1 |grep expire
```

* VMAS 에이전트 노드에 있는 인증서의 만료 날짜 확인
```console
az vm run-command invoke -g MC_rg_myAKSCluster_region -n vm-name --command-id RunShellScript --query 'value[0].message' -otsv --scripts "openssl x509 -in /etc/kubernetes/certs/apiserver.crt -noout -enddate"
```

* 하나의 VMSS 에이전트 노드에 있는 인증서의 만료 날짜 확인
```console
az vmss run-command invoke -g MC_rg_myAKSCluster_region -n vmss-name --instance-id 0 --command-id RunShellScript --query 'value[0].message' -otsv --scripts "openssl x509 -in /etc/kubernetes/certs/apiserver.crt -noout -enddate"
```

## <a name="certificate-auto-rotation"></a>인증서 자동 회전

Azure Kubernetes Service는 클러스터에 대 한 가동 중지 시간 없이 만료 되기 전에 제어 평면과 에이전트 노드 모두에서 비 ca 인증서를 자동으로 회전 합니다.

AKS에서 CA가 아닌 인증서를 자동으로 회전 하려면 클러스터에 [TLS 부트스트래핑](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-tls-bootstrapping/)이 있어야 합니다. TLS 부트스트래핑은 현재 다음 지역에서 사용할 수 있습니다.

* eastus2euap
* centraluseuap
* westcentralus
* uksouth
* eastus
* australiacentral
* australiaest

#### <a name="how-to-check-whether-current-agent-node-pool-is-tls-bootstrapping-enabled"></a>현재 에이전트 노드 풀에서 TLS 부트스트래핑을 사용 하는지 여부를 확인 하는 방법
클러스터에서 TLS 부트스트래핑을 사용 하는지 확인 하려면 다음 경로를 찾습니다.  Linux 노드:/var/lib/kubelet/bootstrap-kubeconfig, Windows 노드의 c:\k\bootstrap-config.

> [!Note]
> 나중에 k8s 버전이 발전 함에 따라 파일 경로가 변경 될 수 있습니다.

> [!IMPORTANT]
>지역이 구성 된 후에는 새 클러스터를 만들거나 ' az aks upgrade-g $RESOURCE _GROUP_NAME-n $CLUSTER _NAME '로 업그레이드 하 여 해당 클러스터를 자동 인증서 회전으로 설정 합니다. 

### <a name="limitation"></a>제한 사항

비 rbac 클러스터에서는 자동 인증서 회전이 사용 되지 않습니다.


## <a name="rotate-your-cluster-certificates"></a>클러스터 인증서 회전

> [!WARNING]
> 를 사용 하 여 인증서를 회전 `az aks rotate-certs` 하면 모든 노드와 해당 OS 디스크가 다시 생성 되며 AKS 클러스터에 대해 최대 30 분의 가동 중지 시간이 발생할 수 있습니다.

AKS 클러스터에 로그인하려면 [az aks get-credentials][az-aks-get-credentials]를 사용합니다. 또한 이 명령은 로컬 머신에서 `kubectl` 클라이언트 인증서를 다운로드하고 구성합니다.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

`az aks rotate-certs`를 사용하여 클러스터의 모든 인증서, CA, SA를 회전합니다.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> `az aks rotate-certs`를 완료하는 데 최대 30분이 소요될 수 있습니다. 완료하기 전에 명령이 실패하면 `az aks show`를 사용하여 클러스터의 상태가 ‘인증서 회전’인지 확인합니다. 클러스터가 실패 상태인 경우, `az aks rotate-certs`를 다시 실행하여 인증서를 다시 회전합니다.

`kubectl` 명령을 실행하여 이전 인증서가 더 이상 유효하지 않은지 확인합니다. `kubectl`에서 사용하는 인증서를 업데이트하지 않았으므로 오류가 표시됩니다.  예를 들면 다음과 같습니다.

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

`az aks get-credentials`를 실행하여 `kubectl`에서 사용하는 인증서를 업데이트합니다.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

`kubectl` 명령을 실행하여 인증서가 업데이트되었는지 확인하고 나면 이제 성공할 것입니다. 예를 들면 다음과 같습니다.

```console
kubectl get no
```

> [!NOTE]
> AKS 위에서 실행되는 서비스가 있는 경우 해당 서비스와 관련된 인증서도 업데이트해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터의 인증서, CA 및 SA를 자동으로 회전하는 방법을 보여 주었습니다. AKS 보안 모범 사례에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)에서 클러스터 보안 및 업그레이드에 대한 모범 사례][aks-best-practices-security-upgrades]를 참조하세요.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
