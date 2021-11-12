---
title: Azure Chaos Studio에서 AKS Chaos Mesh 오류를 사용하는 실험 만들기
description: AKS Chaos Mesh 오류를 사용하는 실험 만들기
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e9a9b458343d54b9adadc07cfc009be43dc4b609
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337835"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-to-kill-aks-pods"></a>Chaos Mesh 오류를 사용하여 AKS Pod를 생성하는 비정실 실험 만들기

비정상 상황에서는 비정상 상황에서 이러한 오류를 발생시켜 애플리케이션이 오류에 대해 복원력이 있는지 확인할 수 있습니다. 이 가이드에서는 Chaos 실험 및 Azure Chaos Studio를 사용하여 네임스페이스에서 주기적인 Azure Kubernetes Service Pod 오류를 발생시킬 것입니다. 이 실험을 실행하면 간헐적인 오류가 발생할 때 서비스 사용 불가를 방어할 수 있습니다.

Azure Chaos Studio는 Kubernetes용 무료 오픈 소스 Chaos 엔지니어링 플랫폼인 [Chaos Mesh를](https://chaos-mesh.org/)사용하여 AKS 클러스터에 오류를 삽입합니다. Chaos Mesh 오류는 AKS 클러스터에 Chaos Mesh를 설치해야 하는 [서비스 직접](chaos-studio-tutorial-aks.md) 오류입니다. 이러한 동일한 단계를 사용하여 AKS Chaos Mesh 오류에 대한 실험을 설정하고 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- AKS 클러스터 AKS 클러스터가 없는 경우 다음 [단계에 따라 을 만들](../aks/kubernetes-walkthrough-portal.md)수 있습니다.

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>AKS 클러스터에서 Chaos Mesh 설정

Chaos Studio에서 Chaos Mesh 오류를 실행하려면 AKS 클러스터에 Chaos Mesh를 설치해야 합니다.

1. 활성 구독이 AKS 클러스터가 배포된 구독으로 설정된 [Azure Cloud Shell](../cloud-shell/overview.md) 창에서 다음 명령을 실행합니다. `$RESOURCE_GROUP`및 `$CLUSTER_NAME` 를 클러스터 리소스의 리소스 그룹 및 이름으로 대체합니다.

```bash
az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
kubectl create ns chaos-testing
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
```

2. 다음 명령을 실행하여 Chaos Mesh Pod가 설치되어 있는지 확인합니다.

```bash
kubectl get po -n chaos-testing
```

다음과 유사한 출력이 표시됩니다(chaos-controller-manager 및 하나 이상의 chaos-daemons).

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

[Chaos Mesh 웹 사이트 에서 설치 지침을 사용할](https://chaos-mesh.org/docs/production-installation-using-helm/)수도 있습니다.


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>AKS 클러스터에서 Chaos Studio 사용

Chaos Studio는 해당 리소스가 Chaos Studio에 먼저 온보딩되지 않은 한 리소스에 오류를 삽입할 수 없습니다. 리소스에 [대한 대상 및 기능을](chaos-studio-targets-capabilities.md) 만들어 리소스를 Chaos Studio에 온보딩합니다. AKS 클러스터에는 하나의 대상 유형(서비스 다이렉트)만 있지만 다른 리소스에는 서비스 직접 오류와 에이전트 기반 오류용으로 최대 두 가지 대상 유형이 있을 수 있습니다. 각 유형의 Chaos Mesh 오류는 기능(PodChaos, NetworkChaos, IOChaos 등)으로 표시됩니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. 검색 표시줄에서 **Chaos Studio(미리 보기)를** 검색합니다.
3. **대상을** 클릭하고 AKS 클러스터로 이동합니다.
![Azure Portal 대상 보기](images/tutorial-aks-targets.png)
4. AKS 클러스터 옆의 확인란을 선택하고 **대상 사용을** 클릭한 다음 드롭다운 메뉴에서 서비스 직접 **대상 사용을** 클릭합니다.
![Azure Portal 대상 사용](images/tutorial-aks-targets-enable.png)
5. 선택한 리소스가 사용하도록 설정되었음을 나타내는 알림이 표시됩니다.
![대상을 사용하도록 설정됨을 보여주는 알림](images/tutorial-aks-targets-enable-confirm.png)

이제 AKS 클러스터를 Chaos Studio에 성공적으로 온보딩했습니다. **대상** 보기에서 이 리소스에 사용하도록 설정된 기능을 관리할 수도 있습니다. 리소스 옆에 있는 **작업 관리** 링크를 클릭하면 해당 리소스에 대해 사용하도록 설정된 기능이 표시됩니다.

## <a name="create-an-experiment"></a>실험 만들기
이제 AKS 클러스터가 온보딩되어 있으면 실험을 만들 수 있습니다. 비정상 상황에서는 대상 리소스에 대해 수행할 작업을 순차적으로 실행되는 단계와 병렬로 실행되는 분기로 구성하여 정의합니다.

1. Chaos Studio 탐색에서 **실험** 탭을 클릭합니다. 이 보기에서는 모든 비정실적 실험을 보고 관리할 수 있습니다. Azure Portal **실험** 
 ![ 추가 보기를 클릭합니다.](images/tutorial-aks-add.png)
2. 비정상 상황에서 실험을 배포하려는 **구독,** **리소스 그룹** 및 **위치를** 입력합니다. 실험에 **이름** 을 지정합니다. **다음: 실험 디자이너 >** 기본 실험 세부 
 ![ 정보 추가를 클릭합니다.](images/tutorial-aks-add-basics.png)
3. 이제 Chaos Studio 실험 디자이너에 있습니다. 실험 디자이너를 사용하면 단계, 분기 및 오류를 추가하여 실험을 빌드할 수 있습니다. **단계** 및 **분기에** 친숙한 이름을 지정한 다음 **오류 추가를** 클릭합니다.
![실험 디자이너](images/tutorial-aks-add-designer.png)
4. 드롭다운에서 **AKS Chaos Mesh Pod Chaos를** 선택한 다음, **지속** 시간(분)을 입력하고, **jsonSpec을** 아래 정보로 채웁니다.

    Chaos Mesh jsonSpec을 작성하려면 다음을 수행합니다.
    1. 오류 [유형(예: PodChaos 형식)은](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files)Chaos Mesh 설명서를 참조하세요.
    2. Chaos Mesh 설명서를 사용하여 해당 오류 유형에 대한 YAML 구성을 작성합니다.

        ```yaml
        apiVersion: chaos-mesh.org/v1alpha1
        kind: PodChaos
        metadata:
          name: pod-failure-example
          namespace: chaos-testing
        spec:
          action: pod-failure
          mode: one
          duration: '30s'
          selector:
            namespaces:
              - default
        ```
    3. 외부의 모든 `spec` YAML(사양 속성 이름 포함)을 제거하고 사양 세부 정보의 들여쓰기 를 제거합니다.

        ```yaml
        action: pod-failure
        mode: one
        duration: '30s'
        selector:
          namespaces:
            - default
        ```
    4. 이와 [같은 YAML-JSON 변환기를](https://www.convertjson.com/yaml-to-json.htm) 사용하여 Chaos Mesh YAML을 JSON으로 변환하고 최소화합니다.

        ```json
        {"action":"pod-failure","mode":"one","duration":"30s","selector":{"namespaces":["default"]}}
        ```
    5. 최소화된 JSON을 포털의 **json** 필드에 붙여넣습니다.




**다음: 대상 리소스 >** 
 ![ 오류 속성을 클릭합니다.](images/tutorial-aks-add-fault.png)
5. AKS 클러스터를 선택하고 **다음** 
 ![ 대상 추가를 클릭합니다.](images/tutorial-aks-add-targets.png)
6. 실험이 올바른지 확인한 다음 **검토 + 만들기, 만들기를** 차례로 **클릭합니다.** 
 ![ 실험 검토 및 만들기](images/tutorial-aks-add-review.png)

## <a name="give-experiment-permission-to-your-aks-cluster"></a>AKS 클러스터에 실험 권한 부여
Chaos 실험을 만들 때 Chaos Studio는 대상 리소스에 대해 오류를 실행하는 시스템 할당 관리 ID를 만듭니다. 실험을 성공적으로 실행하려면 이 ID에 대상 리소스에 [대한 적절한 권한이](chaos-studio-fault-providers.md) 부여되어야 합니다.

1. AKS 클러스터로 이동하여 **액세스 제어(IAM)** 를 클릭합니다.
![AKS 개요 페이지](images/tutorial-aks-access-resource.png)
2. **추가를** 클릭한 다음 **역할 할당 추가를** 클릭합니다.
![액세스 제어 개요](images/tutorial-aks-access-iam.png)
3. Azure Kubernetes Service **클러스터 관리자 역할을** 검색하고 역할을 선택합니다.  
 ![ 다음 AKS 클러스터 관리자 역할 할당을 클릭합니다.](images/tutorial-aks-access-role.png)
4. **멤버 선택을** 클릭하고 실험 이름을 검색합니다. 실험을 선택하고 **선택을** 클릭합니다. 동일한 테넌트에서 동일한 이름을 가진 여러 실험이 있는 경우 임의의 문자가 추가되어 실험 이름이 잘립니다.
![역할에 실험 추가](images/tutorial-aks-access-experiment.png)
5. **검토 + 할당을** 클릭한 다음 **검토 + 할당을** 클릭합니다.

## <a name="run-your-experiment"></a>실험 실행
이제 실험을 실행할 준비가 되었습니다. 영향을 확인하려면 AKS 클러스터 개요를 열고 별도의 브라우저 탭에서 **Insights** 것이 좋습니다. **활성 Pod 수에** 대한 라이브 데이터는 실험 실행의 영향을 보여 줍니다.

1. **실험** 보기에서 실험을 클릭하고 **시작을** 클릭한 다음 **확인을** 클릭합니다.
![실험 시작](images/tutorial-aks-start.png)
2. **상태가** **실행 중으로** 변경되면 **기록** 아래의 최신 실행에 대한 **세부 정보를** 클릭하여 실행 중인 실험에 대한 세부 정보를 확인합니다.

## <a name="next-steps"></a>다음 단계
AKS Chaos Mesh 서비스 직접 실험을 실행했으므로 다음을 수행할 준비가 되었습니다.
- [에이전트 기반 오류를 사용하는 실험 만들기](chaos-studio-tutorial-agent-based-portal.md)
- [실험 관리](chaos-studio-run-experiment.md)
