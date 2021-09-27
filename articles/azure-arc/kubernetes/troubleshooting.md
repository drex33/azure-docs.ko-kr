---
title: 일반적인 Azure Arc 사용 가능한 Kubernetes 문제 해결
services: azure-arc
ms.service: azure-arc
ms.date: 05/21/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 지원 Kubernetes 클러스터와 관련된 일반적인 문제 해결
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: f6f29b30f3a62653c032b7aae40cac5afdcf96b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546512"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure Arc 사용 가능한 Kubernetes 문제 해결

이 문서에서는 연결, 권한 및 에이전트 문제에 대한 문제 해결 가이드를 제공합니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결

### <a name="azure-cli"></a>Azure CLI

`az connectedk8s` 또는 `az k8s-configuration` CLI 명령을 사용하기 전에 Azure CLI가 올바른 Azure 구독에 대해 작동하도록 설정되어 있는지 확인합니다.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc 에이전트

Azure Arc 지원 Kubernetes에 대한 모든 에이전트는 네임스페이스에 Pod로 `azure-arc` 배포됩니다. 모든 Pod가 실행되고 상태 검사를 통과해야 합니다.

먼저, Azure Arc Helm 릴리스를 확인합니다.

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Helm 릴리스가 없거나 누락된 경우 [클러스터를 Azure Arc에 다시 연결](./quickstart-connect-cluster.md)해 보세요.

`STATUS: deployed`에 Helm 릴리스가 있는 경우 `kubectl`을 사용하여 에이전트의 상태를 확인합니다.

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

모든 Pod는 `READY` 열에 `3/3` 또는 `2/2`가 있으며 `STATUS`를 `Running`으로 표시해야 합니다. 로그를 가져오고 `Error` 또는 `CrashLoopBackOff`를 반환하는 Pod를 설명합니다. Pod가 `Pending` 상태에서 중단되면 클러스터 노드에 리소스가 부족할 수 있습니다. [클러스터를 스케일링하면](https://kubernetes.io/docs/tasks/administer-cluster/) 이러한 pod가 `Running` 상태로 전환될 수 있습니다.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Kubernetes 클러스터를 Azure Arc에 연결

클러스터를 Azure에 연결하려면 Azure 구독에 대한 액세스와 대상 클러스터에 대한 `cluster-admin` 액세스가 필요합니다. 클러스터에 연결할 수 없거나 사용 권한이 부족한 경우 클러스터를 Azure Arc에 연결하지 못합니다.

### <a name="azure-cli-is-unable-to-download-helm-chart-for-azure-arc-agents"></a>Azure CLI Azure Arc 에이전트에 대한 Helm 차트를 다운로드할 수 없습니다.

Helm 버전 >= 3.7.0을 사용하는 경우 가 `az connectedk8s connect` 실행되어 클러스터를 Azure Arc 연결할 때 다음 오류가 발생합니다.

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest

Unable to pull helm chart from the registry 'mcr.microsoft.com/azurearck8s/batch1/stable/azure-arc-k8sagents:1.4.0': Error: unknown command "chart" for "helm"
Run 'helm --help' for usage.
```

이 경우 이전 버전의 [Helm 3(버전 3.7.0)을](https://helm.sh/docs/intro/install/)설치해야 &lt; 합니다. 그 후 `az connectedk8s connect` 명령을 다시 실행하여 클러스터를 Azure Arc 연결합니다.

### <a name="insufficient-cluster-permissions"></a>클러스터 권한 부족

제공된 kubeconfig 파일에 Azure Arc 에이전트를 설치할 수 있는 충분한 권한이 없는 경우 Azure CLI 명령은 오류를 반환합니다.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

클러스터를 Azure Arc에 연결하는 사용자는 클러스터에서 `cluster-admin` 역할이 할당되어야 합니다.


### <a name="unable-to-connect-openshift-cluster-to-azure-arc"></a>OpenShift 클러스터를 Azure Arc에 연결할 수 없음

OpenShift 클러스터를 Azure Arc에 연결할 때 `az connectedk8s connect` 시간이 초과되고 실패하는 경우 다음을 확인하세요.

1. OpenShift 클러스터는 4.5.41 이상, 4.6.35 이상 또는 4.7.18 이상의 버전 필수 조건을 충족해야 합니다.

1. `az connectedk8s connnect`를 실행하기 전에 클러스터에서 다음 명령을 실행해야 합니다.

    ```console
    oc adm policy add-scc-to-user privileged system:serviceaccount:azure-arc:azure-arc-kube-aad-proxy-sa
    ```

### <a name="installation-timeouts"></a>설치 시간 초과

Kubernetes 클러스터를 Azure Arc 지원 Kubernetes에 연결하려면 클러스터에 Azure Arc 에이전트를 설치해야 합니다. 클러스터가 저속 인터넷 연결을 통해 실행되는 경우 에이전트에 대한 컨테이너 이미지 풀링은 Azure CLI 시간 제한보다 오래 걸릴 수 있습니다.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm 문제

Helm `v3.3.0-rc.1` 버전에는 Helm 설치/업그레이드(`connectedk8s` CLI 확장에 사용)로 인해 모든 후크가 실행되고 다음 오류를 야기하는 [이슈](https://github.com/helm/helm/pull/8527)가 있습니다.

```console
$ az connectedk8s connect -n AzureArcTest -g AzureArcTest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

이 문제에서 복구하려면 다음 단계를 수행합니다.

1. Azure Portal Azure Arc 사용 가능한 Kubernetes 리소스를 삭제합니다.
2. 머신에서 다음 명령을 실행합니다.
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. 릴리스 후보 버전 대신, 머신에 Helm 3의 [안정적인 버전을 설치](https://helm.sh/docs/intro/install/)합니다.
4. 적절한 값으로 `az connectedk8s connect` 명령을 실행하여 클러스터를 Azure Arc에 연결합니다.

## <a name="configuration-management"></a>구성 관리

### <a name="general"></a>일반
구성 리소스의 문제를 해결하려면 `--debug` 매개 변수를 지정하여 az 명령을 실행합니다.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>구성 만들기

Azure Arc 사용 가능한 Kubernetes 리소스()에 대한 쓰기 `Microsoft.Kubernetes/connectedClusters/Write` 권한은 해당 클러스터에서 구성을 만드는 데 필요하며 충분합니다.

### <a name="configuration-remains-pending"></a>구성을 `Pending`으로 유지

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

## <a name="monitoring"></a>모니터링

컨테이너용 Azure Monitor를 사용하려면 DaemonSet가 권한 있는 모드에서 실행되어야 합니다. 모니터링을 위해 Canonical Charmed Kubernetes 클러스터를 설정하려면 다음 명령을 실행합니다.

```console
juju config kubernetes-worker allow-privileged=true
```

## <a name="enable-custom-locations-using-service-principal"></a>서비스 주체를 사용하여 사용자 지정 위치 사용

클러스터를 Azure Arc에 연결하거나 기존 클러스터에서 사용자 지정 위치 기능을 사용하도록 설정하는 경우 다음과 같은 경고가 표시될 수 있습니다.

```console
Unable to fetch oid of 'custom-locations' app. Proceeding without enabling the feature. Insufficient privileges to complete the operation.
```

위의 경고는 서비스 주체를 사용하여 Azure에 로그인하고 이 서비스 주체에게 Azure Arc 서비스에서 사용하는 애플리케이션의 정보를 가져올 수 있는 권한이 없는 경우에 관찰됩니다. 이 오류를 방지하려면 다음 단계를 실행합니다.

1. Azure Arc 서비스에서 사용하는 Azure AD 애플리케이션의 개체 ID를 가져옵니다.

    ```console
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. 위 단계의 `<objectId>` 값을 사용하여 클러스터에서 사용자 지정 위치 기능을 사용하도록 설정합니다.
    - Arc로 클러스터 연결의 일부로 사용자 지정 위치 기능을 사용하도록 설정하는 경우 다음 명령을 실행합니다.

        ```console
        az connectedk8s connect -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId>   
        ```

    - 기존 Azure Arc 지원 Kubernetes 클러스터에서 사용자 지정 위치 기능을 사용하도록 설정하는 경우 다음 명령을 실행합니다.

        ```console
        az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
        ```

위의 사용 권한이 부여되면 이제 클러스터에서 [사용자 지정 위치 기능을 사용하도록 설정](custom-locations.md#enable-custom-locations-on-cluster)할 수 있습니다.

## <a name="azure-arc-enabled-open-service-mesh"></a>Azure Arc 사용 가능한 Open Service Mesh

다음 문제 해결 단계에서는 클러스터에 있는 모든 Open Service Mesh 확장 구성 요소의 배포 유효성 검사에 대한 지침을 제공합니다.

### <a name="1-check-osm-controller-deployment"></a>1. OSM 컨트롤러 **배포** 확인
```bash
kubectl get deployment -n arc-osm-system --selector app=osm-controller
```

OSM 컨트롤러가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

### <a name="2-check-the-osm-controller-pod"></a>2. OSM 컨트롤러 **Pod** 확인
```bash
kubectl get pods -n arc-osm-system --selector app=osm-controller
```

OSM 컨트롤러가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

특정 시점에 하나의 컨트롤러를 _제거한_ 경우에도 다시 시작한 횟수가 `0`번인 `READY 1/1` 및 `Running`인 하나의 컨트롤러가 있습니다.
`READY` 열이 `1/1`이 아닌 경우 서비스 메시는 중단된 상태에 있게 됩니다.
`0/1`인 `READY` 열은 컨트롤 플레인 컨테이너가 충돌함을 나타내는 것이며, 로그를 가져와야 합니다. 아래 `Get OSM Controller Logs from Azure Support Center` 섹션을 참조하세요.
`READY` 열의 `/` 뒤에 1보다 큰 숫자가 있으면 사이드카가 설치되어 있는 것입니다. 사이드카가 연결되어 있는 경우 OSM 컨트롤러는 작동하지 않을 가능성이 높습니다.

### <a name="3-check-osm-controller-service"></a>3. OSM 컨트롤러 **서비스** 확인
```bash
kubectl get service -n arc-osm-system osm-controller
```

OSM 컨트롤러가 정상 상태가 되면 다음과 같은 출력이 표시됩니다.
```
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> 참고: `CLUSTER-IP`는 다를 수 있습니다. 서비스 `NAME` 및 `PORT(S)`는 출력에 표시된 것과 동일해야 합니다.

### <a name="4-check-osm-controller-endpoints"></a>4. OSM 컨트롤러 **엔드포인트** 확인:
```bash
kubectl get endpoints -n arc-osm-system osm-controller
```

OSM 컨트롤러가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

사용자의 클러스터에 `osm-controller`에 대한 `ENDPOINTS`가 없는 경우에는 컨트롤 평면이 비정상 상태임을 의미합니다. 이는 OSM 컨트롤러 Pod가 충돌하거나 올바르게 배포되지 않은 경우에 발생할 수 있습니다.

### <a name="5-check-osm-injector-deployment"></a>5. OSM 인젝터 **배포** 확인
```bash
kubectl get deployments -n arc-osm-system osm-injector
```

OSM 인젝터가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
osm-injector   1/1     1            1           73m
```

### <a name="6-check-osm-injector-pod"></a>6. OSM 인젝터 **Pod** 확인
```bash
kubectl get pod -n arc-osm-system --selector app=osm-injector
```

OSM 인젝터가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

`READY` 열은 `1/1`이어야 합니다. 다른 모든 값은 비정상 osm-인젝터 Pod를 표시합니다.

### <a name="7-check-osm-injector-service"></a>7. OSM 인젝터 **서비스** 확인
```bash
kubectl get service -n arc-osm-system osm-injector
```

OSM 인젝터가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

`osm-injector` 서비스에 대해 나열된 IP 주소가 `9090`인지 확인합니다. `EXTERNAL-IP`가 없어야 합니다.

### <a name="8-check-osm-injector-endpoints"></a>8. OSM 인젝터 **엔드포인트** 확인
```bash
kubectl get endpoints -n arc-osm-system osm-injector
```

OSM 인젝터가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

OSM이 작동하려면 `osm-injector`에 대해 엔드포인트가 하나 이상 있어야 합니다. OSM 인젝터 엔드포인트의 IP 주소는 다를 수 있습니다. 포트 `9090`은 동일해야 합니다.


### <a name="9-check-validating-and-mutating-webhooks"></a>9. **유효성 검사** 및 **변경** 웹후크 확인:
```bash
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

유효성 검사 웹후크가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      81m
```

```bash
kubectl get MutatingWebhookConfiguration --selector app=osm-controller
```


변경 웹후크가 정상 상태이면 다음 출력과 유사한 출력을 얻게 됩니다.
```
NAME              WEBHOOKS   AGE
arc-osm-webhook-osm   1      102m
```

**유효성 검사** 웹후크의 서비스 및 CA 번들을 확인합니다.
```
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

잘 구성된 유효성 검사 웹후크 구성은 다음과 같이 출력됩니다.
```json
{
  "name": "osm-config-validator",
  "namespace": "arc-osm-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

**변경** 웹후크의 서비스 및 CA 번들을 확인합니다.
```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

잘 구성된 변경 웹후크 구성은 다음과 같이 출력됩니다.
```
{
  "name": "osm-injector",
  "namespace": "arc-osm-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```


다음 명령을 사용하여 OSM 컨트롤러가 CA 번들에 유효성 검사(또는 변경) 웹후크를 제공했는지 확인합니다.

```bash
kubectl get ValidatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```bash
kubectl get MutatingWebhookConfiguration arc-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

예제 출력:
```bash
1845
```
출력의 숫자는 CA 번들의 바이트 수 또는 크기를 나타냅니다. 이 값이 비어 있거나, 0 또는 1000보다 작은 숫자이면 CA 번들이 올바르게 프로비전되지 않았음을 나타내는 것입니다. 올바른 CA 번들이 없으면 ValidatingWebhook에서 오류를 throw하고 `arc-osm-system` 네임스페이스의 `osm-config` ConfigMap을 변경하지 못하도록 합니다.

CA 번들이 잘못된 경우의 샘플 오류를 살펴보겠습니다.
- `osm-config` ConfigMap을 변경하려고 한 경우:
  ```bash
  kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
  ```
- 오류 출력:
  ```bash
  Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.arc-osm-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
  ```

**유효성 검사** 웹후크 구성에 잘못된 인증서가 있는 경우 다음 해결 방법 중 하나를 사용합니다.
- 옵션 1. OSM 컨트롤러 다시 시작 - OSM 컨트롤러를 다시 시작합니다. 시작 시에는 변경 웹후크 및 유효성 검사 웹후크의 CA 번들을 덮어씁니다.
  ```bash
  kubectl rollout restart deployment -n arc-osm-system osm-controller
  ```

- 옵션 2. 유효성 검사 웹후크 삭제 - 유효성 검사 웹후크를 제거하면 `osm-config` ConfigMap의 변경은 더 이상 유효하지 않게 됩니다. 모든 패치가 진행됩니다. CA 번들을 신속하게 다시 작성하려면 OSM 컨트롤러를 다시 시작해야 할 수 있습니다.
   ```bash
   kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm
   ```

- 옵션 3. 삭제 및 패치: 다음 명령은 유효성 검사 웹후크를 삭제하고, 값을 추가하도록 허용하고, 즉시 패치 적용을 시도합니다.
  ```bash
  kubectl delete ValidatingWebhookConfiguration arc-osm-webhook-osm; kubectl patch ConfigMap osm-config -n arc-osm-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
  ```


### <a name="10-check-the-osm-config-configmap"></a>10. `osm-config` **ConfigMap** 확인

>[!Note]
>OSM 컨트롤러는 `arc-osm-system` 네임스페이스에 `osm-config` ConfigMap이 있음을 요구하지 않습니다. 컨트롤러는 구성에 대한 적절한 기본값을 가지며, 이 값이 없어도 작동할 수 있습니다.

존재 여부를 확인합니다.
```bash
kubectl get ConfigMap -n arc-osm-system osm-config
```

`osm-config` ConfigMap의 내용 확인:
```bash
kubectl get ConfigMap -n arc-osm-system osm-config -o json | jq '.data'     
```
다음 출력이 표시됩니다.
```json
{
  "egress": "false",
  "enable_debug_server": "false",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "true",
  "service_cert_validity_duration": "24h",
  "tracing_enable": "false",
  "use_https_ingress": "false",
}
```

`osm-config` ConfigMap 값을 이해하려면 [OSM ConfigMap 설명서](https://release-v0-8.docs.openservicemesh.io/docs/osm_config_map/)를 참조하세요.

### <a name="11-check-namespaces"></a>11. 네임스페이스 확인

>[!Note]
>arc-osm-system 네임스페이스는 서비스 메시에 참여하지 않으며 아래의 키/값으로 레이블이 지정되거나 주석이 추가되지 않습니다.

`osm namespace add` 명령을 사용하여 지정된 서비스 메시에 네임스페이스를 조인합니다.
kubernetes 네임스페이스가 메시의 일부인 경우 다음을 충족해야 합니다.

`bookbuyer` 네임스페이스의 주석을 봅니다.
```bash
kc get namespace bookbuyer -o json | jq '.metadata.annotations'
```

다음과 같은 주석이 있어야 합니다.
```
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```


`bookbuyer` 네임스페이스의 레이블을 봅니다.
```bash
kc get namespace bookbuyer -o json | jq '.metadata.labels'
```

다음과 같은 레이블이 있어야 합니다.
```
{
  "openservicemesh.io/monitored-by": "osm"
}
```
`osm` CLI를 사용하지 않는 경우 네임스페이스에 이러한 주석을 수동으로 추가할 수도 있습니다. 네임스페이스에 주석이 `"openservicemesh.io/sidecar-injection": "enabled"`로 지정되어 있지 않거나 레이블이 `"openservicemesh.io/monitored-by": "osm"`으로 지정되지 않은 경우 OSM 인젝터는 Envoy 사이드카를 추가하지 않습니다.

>[!Note]
>`osm namespace add`를 호출한 후 **새** Pod에는 Envoy 사이드카가 삽입됩니다. 기존 Pod는 `kubectl rollout restard deployment` 명령으로 다시 시작해야 합니다.


### <a name="12-verify-the-smi-crds"></a>12. SMI CRD 확인
클러스터에 필요한 CRD가 있는지 확인합니다.
```bash
kubectl get crds
```

CRD가 동일한 OSM 업스트림 버전에 해당하는지 확인합니다. 예를 들어 v0.8.4를 사용하는 경우 CRD가 [OSM OSS 프로젝트](https://docs.openservicemesh.io/)의 릴리스 분기 v0.8.4에서 사용할 수 있는 것과 일치하는지 확인합니다. [OSM 릴리스 정보](https://github.com/openservicemesh/osm/releases)를 참조합니다.

다음 명령을 사용하여 설치된 CRD의 버전을 가져옵니다.
```bash
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

CRD가 없으면 다음 명령을 사용하여 클러스터에 설치합니다. 명령에서 버전을 바꾸어야 합니다.
```bash
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/access.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/specs.yaml

kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8.2/charts/osm/crds/split.yaml
```
