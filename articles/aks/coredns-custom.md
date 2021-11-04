---
title: AKS(Azure Kubernetes Service)용 CoreDNS 사용자 지정
description: AKS(Azure Kubernetes Service)를 통해 하위 도메인을 추가하거나 사용자 지정 DNS 엔드포인트를 확장하도록 CoreDNS를 사용자 지정하는 방법을 알아봅니다.
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: c62e269ae20b8da974b4ba7ef72ac7171ee0e88d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577976"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Azure Kubernetes Service를 사용하여 CoreDNS 사용자 지정

AKS(Azure Kubernetes Service)는 모든 *1.12.x* 이상의 클러스터에서 클러스터 DNS 관리 및 확인 시 [CoreDNS][coredns] 프로젝트를 사용합니다. 이전에는 kube-dns 프로젝트가 사용되었습니다. 이 kube-dns 프로젝트는 이제 사용되지 않습니다. CoreDNS 사용자 지정 및 Kubernetes에 대한 자세한 내용은 [공식 업스트림 설명서][corednsk8s]를 참조하세요.

AKS는 관리되는 서비스이므로, CoreDNS(*CoreFile*)에 대한 기본 구성을 수정할 수 없습니다. 대신 Kubernetes *ConfigMap* 을 사용하여 기본 설정을 재정의합니다. 기본 AKS CoreDNS ConfigMaps를 보려면 `kubectl get configmaps --namespace=kube-system coredns -o yaml` 명령을 사용합니다.

이 문서에는 AKS에서 CoreDNS의 기본 사용자 지정 옵션에 ConfigMaps를 사용하는 방법이 나와 있습니다. 이 방법은 CoreFile을 사용하는 등 다른 컨텍스트에서 CoreDNS를 구성하는 것과 다릅니다. 구성 값이 버전 간에 변경될 수 있으므로 실행 중인 CoreDNS의 버전을 확인합니다.

> [!NOTE]
> `kube-dns`에서는 Kubernetes 구성 맵을 통해 다양한 [사용자 지정 옵션][kubednsblog]을 제공했습니다. CoreDNS는 kube-dns를 사용하는 이전 버전과 호환되지 **않습니다**. 이전에 사용한 모든 사용자 지정 항목은 업데이트해야 CoreDNS에서 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

아래의 예제와 같은 구성을 만들 때 *데이터* 섹션의 이름은 *.server* 또는 *.override* 로 끝나야 합니다. 이 명명 규칙은 `kubectl get configmaps --namespace=kube-system coredns -o yaml` 명령을 사용하여 볼 수 있는 기본 AKS CoreDNS Configmap에 정의되어 있습니다.

## <a name="what-is-supportedunsupported"></a>지원되는 기능/지원되지 않는 기능

기본 제공 CoreDNS 플러그인이 모두 지원됩니다. 추가 기능/타사 플러그인은 지원되지 않습니다.

## <a name="rewrite-dns"></a>DNS 다시 쓰기

한 가지 시나리오가 DNS 이름을 즉시 다시 쓰는 것입니다. 다음 예제에서 `<domain to be written>`을 사용자 고유의 정규화된 도메인 이름으로 바꿉니다. `corednsms.yaml`이라는 파일을 만들고 다음 예제 구성을 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
  <domain to be rewritten>.com:53 {
  log
  errors
  rewrite stop {
    name regex (.*)\.<domain to be rewritten>.com {1}.default.svc.cluster.local
    answer name (.*)\.default\.svc\.cluster\.local {1}.<domain to be rewritten>.com
  }
  forward . /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
}
```

> [!IMPORTANT]
> CoreDNS 서비스 IP와 같은 DNS 서버로 리디렉션하는 경우, 해당 DNS 서버에서 다시 쓴 도메인 이름을 확인할 수 있어야 합니다.

[kubectl apply configmap][kubectl-apply] 명령을 사용하여 ConfigMap을 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f corednsms.yaml
```

사용자 지정 사항이 적용되었는지 확인하려면 [kubectl get configmaps][kubectl-get]를 사용하고 *coredns-custom* ConfigMap을 지정합니다.

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

이제 CoreDNS에서 ConfigMap을 강제로 다시 로드합니다. [kubectl delete pod][kubectl delete] 명령은 파괴적이지 않으며 중단 시간이 발생하지 않습니다. `kube-dns` Pod가 삭제되면 Kubernetes Scheduler에서 Pod를 다시 만듭니다. 이러한 새 Pod에는 TTL 값의 변경 내용이 포함됩니다.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> 위의 명령은 틀린 부분이 없습니다. 를 변경하는 동안 `coredns` 배포는 **kube-dns** 레이블 아래에 있습니다.

## <a name="custom-forward-server"></a>사용자 지정 전달 서버

네트워크 트래픽에 대한 전달 서버를 지정해야 하는 경우 ConfigMap을 만들어 DNS를 사용자 지정할 수 있습니다. 다음 예제에서는 사용자 고유의 환경 값으로 `forward` 이름과 주소를 업데이트합니다. `corednsms.yaml`이라는 파일을 만들고 다음 예제 구성을 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

이전 예제와 같이 [kubectl apply configmap][kubectl-apply] 명령을 사용하여 ConfigMap을 만들고 YAML 매니페스트의 이름을 지정합니다. 그런 다음, [kubectl delete Pod][kubectl delete]를 사용해 CoreDNS에서 ConfigMap을 강제로 다시 로드하여 Kubernetes Scheduler에서 이를 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>사용자 지정 도메인 사용

내부적으로만 확인할 수 있는 사용자 지정 도메인을 구성할 수 있습니다. 예를 들면, 유효한 최상위 도메인이 아닌 사용자 지정 도메인 *puglife.local* 을 확인할 수 있습니다. 사용자 지정 도메인 ConfigMap이 없으면 AKS 클러스터에서 주소를 확인할 수 없습니다.

다음 예제에서는 사용자 지정 도메인과 IP 주소를 업데이트하여 사용자 고유의 환경 값으로 트래픽을 전달합니다. `corednsms.yaml`이라는 파일을 만들고 다음 예제 구성을 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

이전 예제와 같이 [kubectl apply configmap][kubectl-apply] 명령을 사용하여 ConfigMap을 만들고 YAML 매니페스트의 이름을 지정합니다. 그런 다음, [kubectl delete Pod][kubectl delete]를 사용해 CoreDNS에서 ConfigMap을 강제로 다시 로드하여 Kubernetes Scheduler에서 이를 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>스텁 도메인

CoreDNS를 사용하여 스텁 도메인도 구성할 수 있습니다. 다음 예제에서는 사용자 지정 도메인과 IP 주소를 사용자 고유의 환경 값으로 업데이트합니다. `corednsms.yaml`이라는 파일을 만들고 다음 예제 구성을 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

이전 예제와 같이 [kubectl apply configmap][kubectl-apply] 명령을 사용하여 ConfigMap을 만들고 YAML 매니페스트의 이름을 지정합니다. 그런 다음, [kubectl delete Pod][kubectl delete]를 사용해 CoreDNS에서 ConfigMap을 강제로 다시 로드하여 Kubernetes Scheduler에서 이를 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>호스트 플러그인

기본 제공 플러그인이 모두 지원되므로 CoreDNS [호스트][coredns hosts] 플러그인도 사용자 지정할 수 있습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts { 
              10.0.0.1 example1.org
              10.0.0.2 example2.org
              10.0.0.3 example3.org
              fallthrough
          }
```

## <a name="troubleshooting"></a>문제 해결

엔드포인트나 확인 검사와 같은 일반적인 CoreDNS 문제 해결 단계는 [DNS 확인 디버깅][coredns-troubleshooting]을 참조하세요.

DNS 쿼리 로깅을 사용하려면 coredns-custom ConfigMap에 다음 구성을 적용합니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

구성 변경 내용을 적용한 후에는 `kubectl logs` 명령을 사용하여 CoreDNS 디버그 로깅을 확인합니다. 예를 들면 다음과 같습니다.

```console
kubectl logs --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 CoreDNS를 사용자 지정하기 위한 예제 시나리오를 몇 가지 살펴보았습니다. CoreDNS 프로젝트에 대한 자세한 내용은 [CoreDNS 업스트림 프로젝트 페이지][coredns]를 참조하세요.

핵심 네트워크 개념에 대해 자세히 알아보려면 [AKS의 애플리케이션에 대한 네트워크 개념][concepts-network]을 참조하세요.

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/
[coredns-troubleshooting]: https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
