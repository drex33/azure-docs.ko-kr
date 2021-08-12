---
title: Azure Kubernetes Service(AKS)에서 Istio 설치
description: Istio를 설치 및 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법을 알아봅니다.
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683831"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)에서 Istio 설치 및 사용

[Istio][istio-github]는 Kubernetes 클러스터에서 마이크로 서비스에서 기능 키 세트를 제공하는 오픈 소스 서비스 메시입니다. 이 기능에는 트래픽 관리, 서비스 ID 및 보안, 정책 적용 및 관찰성을 포함됩니다. Istio에 대한 자세한 내용은 [Istio는 무엇인가?][istio-docs-concepts] 공식 설명서를 참조하세요.

이 문서에서는 Istio를 설치하는 방법을 보여줍니다. 클라이언트 컴퓨터에 Istio `istioctl` 클라이언트 이진이 설치되고, Istio 구성 요소는 AKS의 Kubernetes 클러스터에 설치됩니다.

> [!NOTE]
> 다음 지침은 Istio 버전 `1.7.3`을 참조합니다.
>
> Istio `1.7.x` 릴리스는 Istio 팀에서 Kubernetes 버전 `1.16+`을 대상으로 테스트했습니다. 추가적인 Istio 버전은 [GitHub - Istio 릴리스][istio-github-releases]에서, 각 릴리스에 대한 정보는 [Istio 뉴스][istio-release-notes]에서, 지원되는 Kubernetes 버전은 [Istio 일반 FAQ][istio-faq]에서 찾을 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Istio istioctl 클라이언트 이진 다운로드 및 설치
> * AKS에 Istio 설치
> * 설치 유효성 검사
> * 추가 기능에 액세스
> * AKS에서 Istio 제거

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 자세히 설명된 단계에서는 AKS 클러스터(Kubernetes `1.16` 이상, Kubernetes RBAC 사용)를 만들고 클러스터와 `kubectl`을 연결했다고 가정합니다. 이 항목에 대한 도움이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

AKS 클러스터에서 Istio를 실행하기 위한 추가 리소스 요구 사항을 이해하려면 [Istio 성능 및 확장성](https://istio.io/docs/concepts/performance-and-scalability/) 설명서를 확인해야 합니다. 코어 및 메모리 요구 사항은 특정 워크로드에 따라 달라집니다. 설정에 사용할 적절한 수의 노드 및 VM 크기를 선택합니다.

이 문서에서는 몇 단계로 Istio 설치 지침을 구분합니다. 최종 결과는 공식 설치 [지침][istio-install-istioctl]과 동일한 구조입니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>AKS에 Istio 연산자 설치

Istio는 AKS 클러스터 내에서 Istio 구성 요소에 대한 설치 및 업데이트를 관리하는 [연산자][istio-install-operator] 를 제공합니다. `istioctl` 클라이언트 이진을 사용하여 Istio 연산자를 설치합니다.

```bash
istioctl operator init
```

Istio 연산자가 설치되었는지 확인하려면 다음 출력과 유사한 내용이 표시되어야 합니다.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Istio 연산자는 `istio-operator` 네임스페이스에 설치됩니다. 네임스페이스를 쿼리합니다.

```bash
kubectl get all -n istio-operator
```

다음 구성 요소가 배포된 것을 볼 수 있습니다.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

연산자 패턴 및 [kubernetes.io][kubernetes-operator]를 통해 복잡한 작업을 자동화하는 방법을 자세히 알아볼 수 있습니다.


### <a name="install-istio-components"></a>Istio 구성 요소 설치

이제 AKS 클러스터에 Istio 연산자를 성공적으로 설치했으므로 Istio 구성 요소를 설치해야 합니다. 

`default` [Istio 구성 프로필][istio-configuration-profiles]을 활용하여 [istio 연산자 사양][istio-control-plane]을 빌드합니다.

다음 `istioctl` 명령을 실행하여 `default` Istio 구성 프로필에 대한 구성을 볼 수 있습니다.

```bash
istioctl profile dump default
```

> [!NOTE]
> 현재 Istio는 Linux 노드에서 실행하도록 예약되어야 합니다. 클러스터에 Windows Server 노드가 있는 경우 Istio Pod가 Linux 노드에서만 실행되도록 예약해야 합니다. [노드 선택기][kubernetes-node-selectors]를 사용하여 Pod가 올바른 노드로 예약되었는지 확인합니다.

> [!CAUTION]
> [Istio CNI][istio-feature-cni] Istio 기능은 현재 [알파][istio-feature-stages]이므로 이 기능을 사용하도록 설정하기 전에 고려해야 합니다. 

다음 코드를 사용하여 `istio.aks.yaml`라는 파일을 만듭니다. 이 파일에는 Istio를 구성하기 위한 [Istio 연산자 사양][istio-control-plane]이 포함되어 있습니다.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

`istio-system` 네임스페이스를 만들고 Istio 연산자 사양을 해당 네임스페이스에 배포합니다. Istio 연산자는 Istio 연산자 사양을 감시하고 이를 사용하여 AKS 클러스터에 Istio를 설치하고 구성합니다.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

이 시점에서 AKS 클러스터에 Istio를 배포했습니다. Istio를 성공적으로 배포하기 위해 다음 섹션인 [Istio 설치 유효성 검사](#validate-the-istio-installation)로 넘어가겠습니다.

## <a name="validate-the-istio-installation"></a>설치 유효성 검사

Istio 연산자에서 Istio 및 추가 기능 구성 요소를 설치한 `istio-system` 네임스페이스를 쿼리합니다.

```bash
kubectl get all -n istio-system
```

다음과 같은 메시지가 표시됩니다.

- `istio*` - Istio 구성 요소
- `jaeger-*`, `tracing`, `zipkin` - 추적 추가 기능
- `prometheus` - 메트릭 추가 기능
- `grafana` - 분석 및 모니터링 대시보드 추가 기능
- `kiali` - 서비스 메시 대시보드 추가 기능

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

또한 Istio 연산자 로그를 확인하여 설치에 추가 인사이트를 얻을 수 있습니다.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

`istio-ingressgateway`에서 `<pending>`의 외부 IP를 표시하는 경우 Azure 네트워킹에서 IP 주소가 할당될 때까지 몇 분이 걸립니다.

모든 Pod는 `Running` 상태를 표시해야 합니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. Pod에서 문제를 보고하는 경우 [kubectl describe pod][kubectl-describe] 명령을 사용하여 출력과 상태를 검토합니다.

## <a name="accessing-the-add-ons"></a>추가 항목 액세스

추가 기능을 제공하는 Istio 연산자는 다수의 추가 기능을 설치합니다. 추가 기능의 웹 애플리케이션은 외부 IP 주소를 통해 공개적으로 노출되지 **않습니다**. 

추가 항목 사용자 인터페이스에 액세스하려면 `istioctl dashboard` 명령을 사용합니다. 이 명령은 [kubectl 포트 전달][kubectl-port-forward] 및 임의 포트를 사용하여 클라이언트 컴퓨터와 AKS 클러스터의 관련 Pod 간에 보안 연결을 만듭니다. 그러면 기본 브라우저에서 추가 기능 웹 애플리케이션이 자동으로 열립니다.

### <a name="grafana"></a>Grafana

Istio에 대한 분석 및 모니터링 대시보드는 [Grafana][grafana]에서 제공됩니다. 메시지가 표시되면 이전에 Grafana 암호를 통해 만든 자격 증명을 사용해야 합니다. 다음과 같이 Grafana 대시보드를 안전하게 엽니다.

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Istio에 대한 메트릭은 [Prometheus][prometheus]에서 제공합니다. 다음과 같이 안전하게 Prometheus 대시보드를 엽니다.

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 내의 추적은 [Jaeger][jaeger]에서 제공합니다. 다음과 같이 Jaeger 대시보드를 안전하게 엽니다.

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

서비스 메시 관찰성 대시보드는 [Kiali][kiali]에서 제공됩니다. 메시지가 표시되면 이전에 Kiali 암호를 통해 만든 자격 증명을 사용해야 합니다. 다음과 같이 Kiali 대시보드를 안전하게 엽니다.

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

[Envoy][envoy] 프록시에 간단한 인터페이스를 사용할 수 있습니다. 지정된 Pod에서 실행되는 Envoy 프록시에 대한 구성 정보 및 메트릭을 제공합니다. 다음과 같이 Envoy 인터페이스를 안전하게 엽니다.

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>AKS에서 Istio 제거

> [!WARNING]
> 실행 중인 시스템에서 Istio를 삭제하면 서비스 간에 트래픽 관련 문제가 발생할 수 있습니다. 계속 진행하기 전에 Istio 없이 제대로 작동하도록 시스템에 프로비전을 수행했는지 확인합니다.

### <a name="remove-istio"></a>Istio 제거

AKS 클러스터에서 Istio를 제거하려면 이전에 추가한 `istio-control-plane`라는 이름의 `IstioOperator` 리소스를 삭제합니다. Istio 연산자는 Istio 연산자 사양이 제거되었음을 인식한 다음 관련된 Istio 구성 요소를 모두 삭제합니다.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

다음을 실행하여 모든 Istio 구성 요소가 삭제되었는지 확인할 수 있습니다.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>Istio 연산자 제거

Istio가 성공적으로 제거되면 Istio 연산자를 제거할 수도 있습니다.

```bash
istioctl operator remove
```

그런 다음 마지막으로 `istio-` 네임스페이스를 제거합니다.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>다음 단계

Istio에 대한 자세한 설치 및 구성 옵션을 탐색하려면 다음 공식 Istio 참고 자료를 참조하세요.

- [Istio - 설치 가이드][istio-installation-guides]

다음을 사용하여 추가 시나리오를 따를 수도 있습니다.

- [Istio Bookinfo 애플리케이션 예제][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
