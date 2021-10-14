---
title: 컨테이너 인사이트 문제 해결 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트의 문제를 해결하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 04fea3c36cbff4e2c8ecb315f6e3f93bc92aa2b3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003253"
---
# <a name="troubleshooting-container-insights"></a>컨테이너 인사이트 문제 해결

컨테이너 인사이트로 AKS(Azure Kubernetes Service) 클러스터 모니터링을 구성할 때 데이터 수집 또는 상태 보고를 방지하는 문제가 발생할 수 있습니다. 이 문서에서는 몇 가지 일반적인 문제 및 문제 해결 단계를 자세히 설명합니다.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>온보딩 또는 업데이트 작업 중 권한 부여 오류

컨테이너 인사이트를 사용하도록 설정하거나 메트릭을 수집하도록 클러스터를 업데이트하는 동안 다음과 유사한 오류가 발생할 수 있습니다. *개체 ID '<사용자의 objectId>'을(를) 가진 클라이언트 <사용자의 Identity>'은(는) 'Microsoft.Authorization/roleAssignments/write' 작업을 범위에 대해 수행할 수 있는 권한이 없습니다.*

온보딩 또는 업데이트 프로세스 중에 클러스터 리소스에서 **모니터링 메트릭 게시자** 역할 할당을 시도합니다. 컨테이너 인사이트를 사용하도록 설정하는 프로세스를 시작하거나 메트릭을 수집하도록 업데이트하는 프로세스를 시작하는 사용자는 AKS 클러스터 리소스 범위에 대한 **Microsoft.Authorization/roleAssignments/write** 권한에 액세스할 수 있어야 합니다. **소유자** 및 **사용자 액세스 관리자** 기본 제공 역할의 구성원만이 이 사용 권한에 대한 액세스 권한을 부여 받습니다. 보안 정책에서 세부적인 수준 권한 할당을 요구하는 경우 [사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 보고 필요한 사용자에게 할당하는 것이 좋습니다.

다음 단계를 수행하여 Azure Portal에서 이 역할을 수동으로 부여할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스** 를 클릭합니다. 리소스 목록에 **Kubernetes** 를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Azure Kubernetes** 를 선택합니다.
3. Kubernetes 클러스터 목록에서 목록 중 하나를 선택합니다.
2. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 클릭합니다.
3. **+ 추가** 를 선택하여 역할 할당을 추가하고, **모니터링 메트릭 게시자** 역할을 선택하고, **선택** 상자에서 **AKS** 를 입력하여 구독에 정의된 클러스터 서비스 주체만 결과를 필터링합니다. 해당 클러스터와 관련된 목록에서 하나를 선택합니다.
4. **저장** 을 선택하여 역할 할당을 완료합니다.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>컨테이너 인사이트가 사용하도록 설정되었지만 정보를 보고하지 않음

컨테이너 인사이트가 성공적으로 사용되고 구성되어 있지만 상태 정보를 볼 수 없거나 로그 쿼리에서 결과가 반환되지 않는 경우 다음 단계에 따라 문제를 진단합니다.

1. 다음 명령을 실행하여 에이전트의 상태를 확인합니다.

    `kubectl get ds omsagent --namespace=kube-system`

    출력은 다음 예제와 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Windows Server 노드가 있는 경우 다음 명령을 실행하여 에이전트의 상태를 확인합니다.

    `kubectl get ds omsagent-win --namespace=kube-system`

    출력은 다음 예제와 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. 다음 명령을 사용하여 에이전트 버전 *06072018* 이상의 배포 상태를 확인합니다.

    `kubectl get deployment omsagent-rs -n=kube-system`

    출력은 다음 예제와 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Pod의 상태를 확인하여 `kubectl get pods --namespace=kube-system` 명령을 통해 실행 중인지 확인합니다.

    출력은 다음 예와 비슷하며 omsagent의 상태는 *Running* 입니다.

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>오류 메시지

아래 표에서는 컨테이너 인사이트를 사용하는 동안 발생할 수 있는 알려진 오류를 요약합니다.

| 오류 메시지  | 작업 |
| ---- | --- |
| 오류 메시지: `No data for selected filters`  | 새로 만든 클러스터에 대한 모니터링 데이터 흐름을 설정하는 데는 약간의 시간이 걸릴 수 있습니다. 클러스터에 대한 데이터가 표시될 때까지 10~15분 정도 기다려 주세요. |
| 오류 메시지: `Error retrieving data` | Azure Kubernetes Service 클러스터가 상태 및 성능 모니터링을 설정하는 동안 클러스터와 Azure Log Analytics 작업 영역 간에 연결이 설정됩니다. Log Analytics 작업 영역은 클러스터에 대한 모든 모니터링 데이터를 저장하는 데 사용됩니다. Log Analytics 작업 영역이 삭제되었으면 이 오류가 발생할 수 있습니다. 작업 영역을 삭제했는지 확인하고, 작업 영역이 삭제되었으면 컨테이너 인사이트를 사용하여 클러스터 모니터링을 다시 사용하도록 설정하고 기존 작업 영역을 지정하거나 새 작업 영역을 만들어야 합니다. 다시 사용하도록 설정하려면 클러스터에 대한 모니터링을 [사용하지 않도록 설정](container-insights-optout.md)하고 컨테이너 인사이트를 다시 [사용하도록 설정](container-insights-enable-new-cluster.md)해야 합니다. |
| az aks cli를 통해 컨테이너 인사이트를 추가한 후 `Error retrieving data` | `az aks cli`를 사용하여 모니터링을 사용하도록 설정하면 컨테이너 인사이트가 제대로 배포되지 않을 수 있습니다. 솔루션이 배포되었는지 확인합니다. 확인하려면 Log Analytics 작업 영역으로 이동하여 왼쪽 창에서 **솔루션** 을 선택하여 해당 솔루션을 사용할 수 있는지 확인합니다. 이 문제를 해결하려면 [컨테이너 인사이트를 배포하는 방법](container-insights-onboard.md)의 지침에 따라 솔루션을 다시 배포해야 합니다. |

문제 진단을 지원하기 위해 [문제 해결 스크립트](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot)를 제공했습니다.

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>컨테이너 인사이트 에이전트 ReplicaSet Pod가 비 Azure Kubernetes 클러스터에 예약되어 있지 않습니다.

컨테이너 인사이트 에이전트 ReplicaSet Pod는 일정에 대해 작업자(또는 에이전트) 노드의 다음 노드 선택기에 종속됩니다.

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

작업자 노드에 노드 레이블이 연결되지 않은 경우 에이전트 ReplicaSet Pod가 예약되지 않습니다. 레이블을 연결하는 방법에 대한 지침은 [Kubernetes 할당 레이블 선택기](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/)를 참조하세요.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Azure가 아닌 클러스터에 있는 노드 및 컨테이너의 CPU 또는 메모리는 성능 차트에 표시되지 않습니다.

컨테이너 인사이트 에이전트 Pod는 노드 에이전트의 cAdvisor 엔드포인트를 사용하여 성능 메트릭을 수집합니다. 성능 메트릭을 수집하기 위해 클러스터의 모든 노드에서 `cAdvisor port: 10255`를 열수 있도록 노드의 컨테이너화된 에이전트가 구성되어 있는지 확인합니다.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>비 Azure Kubernetes 클러스터는 컨테이너 인사이트에 표시되지 않습니다.

컨테이너 인사이트에서 비 Azure Kubernetes 클러스터를 보려면 이 인사이트를 지원하는 Log Analytics 작업 영역 및 컨테이너 인사이트 솔루션 리소스 **ContainerInsights(*작업 영역*)** 에서 읽기 권한이 필요합니다.

## <a name="metrics-arent-being-collected"></a>메트릭이 수집되지 않습니다.

1. 클러스터가 [사용자 지정 메트릭에 대해 지원](../essentials/metrics-custom-overview.md#supported-regions)되는 지역에 있는지 확인합니다.

2. 다음 CLI 명령을 사용하여 **모니터링 메트릭 게시자** 역할 할당이 존재하는지 확인합니다.

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    MSI를 사용하는 클러스터의 경우에는 모니터링이 사용하도록 설정되거나 사용하지 않도록 설정될 때마다 omsagent의 사용자 할당 클라이언트 ID가 변경되므로 역할 할당이 현재 MSI 클라이언트 ID에 존재해야 합니다. 

3. Azure Active Directory Pod ID를 사용하도록 설정하고 MSI를 사용하는 클러스터의 경우:

   - 다음 명령을 사용하여 필수 레이블 **kubernetes.azure.com/managedby: aks** 가 omsagent Pod에 있는지 확인합니다.

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity 에서 지원되는 메서드 중 하나를 사용하여 Pod ID를 사용하도록 설정한 경우 예외가 사용하도록 설정되는지 확인합니다.

        다음 명령을 실행하여 확인합니다.

        `kubectl get AzurePodIdentityException -A -o yaml`

        다음과 유사한 출력을 받아야 합니다.

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```

## <a name="installation-of-azure-monitor-containers-extension-fail-with-an-error-containing-manifests-contain-a-resource-that-already-exists-on-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc 사용 Kubernetes 클러스터에서 "매니페스트가 이미 있는 리소스를 포함 하 고 있습니다." 라는 오류와 함께 Azure Monitor 컨테이너 확장 설치가 실패 합니다.
오류 매니페스트에는 이미 _존재 하는 리소스가 포함 되어_ 있습니다 .이는 컨테이너 Insights 에이전트의 리소스가 Azure Arc Enabled Kubernetes 클러스터에 이미 있음을 나타냅니다. 이는 컨테이너 insights 에이전트가 Azure Arc에 연결 된 AKS 클러스터 인 경우 azuremonitor-컨테이너 투구 차트 또는 모니터링 추가 기능을 통해 이미 설치 되었음을 나타냅니다. 이 문제에 대 한 해결 방법은 컨테이너 insights 에이전트가 있는 경우 기존 리소스를 정리 하 고 Azure Monitor 컨테이너 확장을 사용 하도록 설정 하는 것입니다.

### <a name="for-non-aks-clusters"></a>AKS 않는 클러스터의 경우 
1.  Azure Arc에 연결 된 K8s 클러스터에 대해 아래 명령을 실행 하 여 azmon 투구 차트 릴리스가 있는지 여부를 확인 합니다.

    `helm list  -A`

2.  위 명령의 출력에서 azmon가 있는 것으로 표시 되 면 투구 차트 릴리스를 삭제 합니다.

    `helm del azmon-containers-release-1`

### <a name="for-aks-clusters"></a>AKS 클러스터의 경우
1.  아래 명령을 실행 하 고 omsagent addon 프로필을 찾아 AKS monitoring 추가 기능을 사용 하도록 설정 했는지 확인 합니다.

    ```
    az  account set -s <clusterSubscriptionId>
    az aks show -g <clusterResourceGroup> -n <clusterName>
    ```

2.  위 명령의 출력에 log analytics 작업 영역 리소스 Id가 있는 omsagent addon profile 구성이 있는 경우 AKS 모니터링 추가 기능이 사용 하도록 설정 되어 있고 사용 하지 않도록 설정 해야 함을 나타냅니다.

    `az aks disable-addons -a monitoring -g <clusterResourceGroup> -n <clusterName>`

위의 단계에서 Azure Monitor 컨테이너 확장 문제의 설치를 해결 하지 못한 경우 추가 조사를 위해 Microsoft에 티켓을 만드세요.


## <a name="next-steps"></a>다음 단계

모니터링을 사용하여 AKS 클러스터 노드와 Pod에 관한 상태 메트릭을 캡처하면, Azure Portal에서 해당 상태 메트릭을 사용할 수 있습니다. 컨테이너 인사이트 사용 방법을 알아보려면 [Azure Kubernetes Service 상태 보기](container-insights-analyze.md)를 참조하세요.
