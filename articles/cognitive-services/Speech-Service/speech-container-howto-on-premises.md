---
title: Kubernetes 및 Helm을 사용하는 Speech Service 컨테이너
titleSuffix: Azure Cognitive Services
description: Kubernetes 및 Helm을 사용하여 음성 텍스트 변환 및 텍스트 음성 변환 컨테이너 이미지를 정의하면서 Kubernetes 패키지를 만듭니다. 이 패키지는 Kubernetes 클러스터 온-프레미스에 배포됩니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: eur
ms.openlocfilehash: 80e734581982f0c33cda07fbf28cb3e35c9610c5
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500774"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Kubernetes 및 Helm을 사용하는 Speech Service 컨테이너

음성 컨테이너 온-프레미스를 관리하는 한 가지 옵션은 Kubernetes 및 Helm을 사용하는 것입니다. Kubernetes 및 Helm을 사용하여 음성 텍스트 변환 및 텍스트 음성 변환 컨테이너 이미지를 정의하면서 Kubernetes 패키지를 만듭니다. 이 패키지는 Kubernetes 클러스터 온-프레미스에 배포됩니다. 마지막으로, 배포된 서비스와 다양한 구성 옵션을 테스트하는 방법을 살펴보겠습니다. Kubernetes 오케스트레이션을 사용하지 않고 Docker 컨테이너를 실행하는 방법에 대한 자세한 내용은 [Speech Service 컨테이너 설치 및 실행](speech-container-howto.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

음성 컨테이너 온-프레미스를 사용하기 전에 다음 필수 조건을 충족해야 합니다.

| 필수 | 목적 |
|----------|---------|
| Azure 계정 | Azure 구독이 없는 경우 시작하기 전에 [체험 계정][free-azure-account]을 만듭니다. |
| 컨테이너 레지스트리 액세스 | Kubernetes가 Docker 이미지를 클러스터로 가져오기 위해 컨테이너 레지스트리에 액세스해야 합니다. |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli]는 컨테이너 레지스트리에서 공유 자격 증명을 관리하는 데 필요합니다. Kubernetes는 Kubernetes 패키지 관리자인 Helm 이전에도 필요합니다. |
| Helm CLI | Helm 차트(컨테이너 패키지 정의)를 설치하는 데 사용되는 [Helm CLI][helm-install]를 설치합니다. |
|음성 리소스 |이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 청구 키 및 청구 엔드포인트 URI를 가져오기 위한 _Speech_ Azure 리소스입니다. 두 값은 모두 Azure Portal의 **음성** 개요 및 키 페이지에서 제공되며 컨테이너를 시작하는 데 필요합니다.<br><br>**{API_KEY}** : 리소스 키<br><br>**{ENDPOINT_URI}** : 엔드포인트 URI 예제: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>권장 호스트 컴퓨터 구성

참조로 [Speech Service 컨테이너 호스트 컴퓨터][speech-container-host-computer] 세부 정보를 참조하세요. 이 *Helm 차트* 는 사용자가 지정하는 디코드(동시 요청) 수를 기준으로 CPU 및 메모리 요구 사항을 자동으로 계산합니다. 또한 오디오/텍스트 입력에 대한 최적화가로 `enabled`로 구성되었는지 여부에 따라 조정됩니다. Helm 차트는 기본적으로, 두 개의 동시 요청 및 최적화 비활성화로 설정합니다.

| 서비스 | CPU/컨테이너 | 메모리/컨테이너 |
|--|--|--|
| **음성 텍스트 변환** | 하나의 디코더에는 최소 1,150 밀리코어가 필요합니다. `optimizedForAudioFile`을 사용하도록 설정한 경우 1,950 밀리코어가 필요합니다. (기본값: 디코더 2개) | 필수: 2GB<br>최대: 4GB |
| **텍스트 음성 변환** | 하나의 동시 요청에는 최소 500 밀리코어가 필요합니다. `optimizeForTurboMode`을 사용하도록 설정한 경우 1,000 밀리코어가 필요합니다. (기본값: 동시 요청 2개) | 필수: 1GB<br> 최대: 2GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes 클러스터에 연결

호스트 컴퓨터에 사용 가능한 Kubernetes 클러스터가 있어야 합니다. Kubernetes 클러스터를 호스트 컴퓨터에 배포하는 방법에 대한 개념을 이해하려면 [Kubernetes 클러스터 배포](../../aks/tutorial-kubernetes-deploy-cluster.md)에 대한 이 자습서를 참조하세요.

## <a name="configure-helm-chart-values-for-deployment"></a>배포에 대한 Helm 차트 값 구성

Microsoft에서 제공하는 모든 공개적으로 사용 가능한 Helm 차트를 보려면 [Microsoft Helm Hub][ms-helm-hub]를 방문하세요. Microsoft Helm Hub에서 **Cognitive Services 음성 온-프레미스 차트** 를 찾을 수 있습니다. **Cognitive Services 음성 온-프레미스** 는 설치할 차트이지만, 먼저 명시적 구성으로 `config-values.yaml` 파일을 만들어야 합니다. 먼저 Microsoft 리포지토리를 Helm 인스턴스에 추가해 보겠습니다.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

다음으로 Helm 차트 값을 구성합니다. 다음 YAML을 복사하여 `config-values.yaml` 파일에 붙여넣습니다. **Cognitive Services 음성 온-프레미스 Helm 차트** 를 사용자 지정하는 방법에 대한 자세한 내용은 [Helm 차트 사용자 지정](#customize-helm-charts)을 참조하세요. `# {ENDPOINT_URI}` 및 `# {API_KEY}` 주석을 원하는 값으로 바꿉니다.

```yaml
# These settings are deployment specific and users can provide customizations
# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` 및 `apikey` 값이 제공되지 않은 경우 서비스는 15분 후에 만료됩니다. 마찬가지로, 서비스를 사용할 수 없게 되면 확인이 실패합니다.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 패키지(Helm 차트)

*Helm 차트* 에는 `mcr.microsoft.com` 컨테이너 레지스트리에서 끌어올 Docker 이미지의 구성이 포함되어 있습니다.

> [Helm 차트][helm-charts]는 Kubernetes 리소스의 관련 집합을 설명하는 파일 컬렉션입니다. 단일 차트를 사용하여 Memcached Pod와 같이 단순한 항목이나, HTTP 서버, 데이터베이스, 캐시 등이 포함된 전체 웹 앱 스택과 같이 복잡한 항목을 배포할 수 있습니다.

제공된 *Helm 차트* 는 텍스트 음성 변환 및 음성 텍스트 변환 서비스 둘 다 Speech Service의 Docker 이미지를 `mcr.microsoft.com` 컨테이너 레지스트리에서 끌어옵니다.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes 클러스터에 Helm 차트 설치

*Helm 차트* 를 설치하려면 [`helm install`][helm-install-cmd] 명령을 실행하여 `<config-values.yaml>`을 적절한 경로 및 파일 이름 인수로 바꿉니다. 아래에서 참조하는 `microsoft/cognitive-services-speech-onpremise` Helm 차트는 [Microsoft Helm 허브][ms-helm-hub-speech-chart]에서 사용할 수 있습니다.

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

성공적인 설치 실행에서 볼 수 있는 출력의 예는 다음과 같습니다.

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Kubernetes 배포를 완료하는 데 몇 분 정도 걸릴 수 있습니다. Pod와 서비스가 올바르게 배포되고 사용 가능한지 확인하려면 다음 명령을 실행합니다.

```console
kubectl get all
```

다음과 비슷하게 표시됩니다.

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Helm 테스트를 사용하여 Helm 배포 확인

설치된 Helm 차트는 인증에 대한 편의를 제공하는 *투구 테스트* 를 정의합니다. 이러한 테스트는 서비스 준비 상태에 대한 유효성을 검사합니다. **음성 텍스트 변환** 및 **텍스트 음성 변환** 서비스를 모두 확인하기 위해 [Helm 테스트][helm-test] 명령을 실행합니다.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> POD 상태가 `Running`이 아니거나 배포가 `AVAILABLE` 열 아래에 나열되지 않은 경우 이러한 테스트는 실패합니다. 이 작업을 완료하는 데 10분 이상 걸릴 수 있으므로 조금만 기다려 주세요.

이러한 테스트는 다양한 상태 결과를 출력합니다.

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

*Helm 테스트* 를 실행하는 대신 `kubectl get all` 명령에서 *외부 IP* 주소 및 해당 포트를 수집할 수 있습니다. IP 및 포트를 사용하여 웹 브라우저를 열고 `http://<external-ip>:<port>:/swagger/index.html`로 이동하여 API Swagger 페이지를 봅니다.

## <a name="customize-helm-charts"></a>Helm 차트 사용자 지정

Helm 차트는 계층 구조입니다. 계층적으로 차트를 상속하는 경우에는 보다 구체적인 설정이 상속된 규칙을 재정의하는 특이성의 개념도 충족합니다.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>다음 단계

AKS(Azure Kubernetes Service)에서 Helm을 사용하여 애플리케이션을 설치하는 방법에 대한 자세한 내용은 [여기를 참조][installing-helm-apps-in-aks]하세요.

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: /cli/azure/install-azure-cli
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#host-computer-requirements-and-recommendations
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
