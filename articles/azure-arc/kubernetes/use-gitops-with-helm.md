---
title: Azure Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 Helm 차트 배포
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: Azure Arc 지원 클러스터 구성을 위해 Helm과 함께 GitOps 사용
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: bc0dc3f0583c346ae909bbb877a6e8a9a9d66a72
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132053296"
---
# <a name="deploy-helm-charts-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 지원 Kubernetes 클러스터에서 GitOps를 사용하여 Helm 차트 배포

Helm은 Kubernetes 애플리케이션을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. APT 및 Yum과 같은 Linux 패키지 관리자와 마찬가지로 Helm은 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리하는 데 사용합니다.

이 문서에서는 Azure Arc 지원 Kubernetes에서 Helm을 구성하고 사용하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 기존 Azure Arc 지원 Kubernetes 연결 클러스터.
    - 아직 클러스터를 연결하지 않은 경우 [Azure Arc 지원 Kubernetes 클러스터 연결 빠른 시작](quickstart-connect-cluster.md)을 살펴보세요.
- 이 기능의 이점과 아키텍처 이해. [구성 및 GitOps - Azure Arc 지원 Kubernetes 문서](conceptual-configurations.md)에서 자세히 알아보세요.
- `k8s-configuration` Azure CLI 확장 버전 >= 1.0.0을 설치합니다.
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes에서 GitOps 및 Helm 사용 개요

 Helm 연산자는 Helm 차트 릴리스를 자동화하는 Flux에 대한 확장을 제공합니다. Helm 차트 릴리스는 HelmRelease라는 Kubernetes 사용자 지정 리소스를 통해 설명됩니다. Flux는 이러한 리소스를 Git에서 클러스터로 동기화하는 반면 Helm 연산자는 리소스에 지정된 대로 Helm 차트가 릴리스되도록 합니다.

 이 문서에 사용된 [예제 리포지토리](https://github.com/Azure/arc-helm-demo)는 다음과 같은 방식으로 구성됩니다.

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

Git 리포지토리에는 두 개의 디렉터리(Helm 차트를 포함하는 디렉터리와 릴리스 구성을 포함하는 디렉터리)가 있습니다. `releases` 디렉터리의 `app.yaml`에는 다음과 같은 HelmRelease 구성이 포함됩니다.

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Helm 릴리스 구성에는 다음 필드가 포함되어 있습니다.

| 필드 | 설명 |
| ------------- | ------------- | 
| `metadata.name` | 필수 필드입니다. Kubernetes 명명 규칙을 따라야 합니다. |
| `metadata.namespace` | 선택적 필드입니다. 릴리스가 생성되는 위치를 결정합니다. |
| `spec.releaseName` | 선택적 필드입니다. 제공되지 않은 경우 릴리스 이름은 `$namespace-$name`입니다. |
| `spec.chart.path` | 차트를 포함하는 디렉터리(리포지토리 루트 기준). |
| `spec.values` | 차트 자체에서 기본 매개 변수 값의 사용자 사용자 지정. |

HelmRelease `spec.values`에 지정된 옵션은 차트 원본의 `values.yaml`에 지정된 옵션을 재정의합니다.

공식 [Helm 연산자 설명서](https://docs.fluxcd.io/projects/helm-operator/en/stable/)에서 HelmRelease에 대해 자세히 알아볼 수 있습니다.

## <a name="create-a-configuration"></a>구성 만들기

`k8s-configuration`에 대한 Azure CLI 확장을 사용하여 연결된 클러스터를 예제 Git 리포지토리에 연결합니다. 이 구성 이름을 `azure-arc-sample`로 지정하고 `arc-k8s-demo` 네임스페이스에 Flux 연산자를 배포합니다.

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>구성 매개 변수

구성 만들기를 사용자 지정하려면 [추가 매개 변수에 대해 알아보세요](./tutorial-use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>구성 유효성 검사

Azure CLI를 사용하여 구성이 성공적으로 만들어졌는지 확인합니다.

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

구성 리소스는 규정 준수 상태, 메시지 및 디버깅 정보로 업데이트됩니다.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "1.2.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>애플리케이션 유효성 검사

다음 명령을 실행하고 브라우저에서 `localhost:8080`으로 이동하여 애플리케이션이 실행 중인지 확인합니다.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>다음 단계

[Azure Policy](./use-azure-policy.md)를 사용하여 대규모 클러스터 구성을 적용합니다.
