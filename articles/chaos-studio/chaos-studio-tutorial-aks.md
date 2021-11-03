---
title: Azure 비정상 스튜디오에서 AKS 비정상 메시 오류를 사용 하는 실험 만들기
description: AKS 비정상 메시 오류를 사용 하는 실험 만들기
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: eb042f6a1e12a114cf60137c2e1ce0aa3e5b48b8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053071"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-to-kill-aks-pods"></a>비정상 메시 오류를 사용 하 여 AKS pod를 중지 하는 비정상 실험 만들기

비정상 실험을 사용 하 여 제어 되는 환경에서 오류를 발생 시켜 응용 프로그램이 오류에 탄력적으로 대처할 수 있는지 확인할 수 있습니다. 이 가이드에서는 비정상 실험 및 Azure 비정상 스튜디오를 사용 하 여 네임 스페이스에서 주기적인 Azure Kubernetes Service pod 오류가 발생 합니다. 이 실험을 실행 하면 간헐적으로 오류가 발생 하는 경우 서비스를 사용할 수 없도록 방지할 수 있습니다.

Azure 비정상 스튜디오는 Kubernetes에 대 한 무료 오픈 소스 비정상 엔지니어링 플랫폼인 비정상 [메시](https://chaos-mesh.org/)를 사용 하 여 AKS 클러스터에 오류를 삽입 합니다. 혼란 스러운 메시 오류는 AKS 클러스터에 비정상 메시를 설치 해야 하는 [서비스 직접](chaos-studio-tutorial-aks.md) 오류입니다. 이러한 동일한 단계를 사용 하 여 AKS 비정상 메시 오류에 대 한 실험을 설정 하 고 실행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- AKS 클러스터 AKS 클러스터가 없는 경우 [다음 단계를 수행 하 여 만들](../aks/kubernetes-walkthrough-portal.md)수 있습니다.

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>AKS 클러스터에 비정상 상태 메시 설정

비정상 스튜디오에서 비정상 메시 오류를 실행 하려면 AKS 클러스터에 비정상 메시를 설치 해야 합니다.

1. 활성 구독이 AKS 클러스터가 배포 된 구독으로 설정 된 [Azure Cloud Shell](../cloud-shell/overview.md) 창에서 다음 명령을 실행 합니다. `$RESOURCE_GROUP`및를 `$CLUSTER_NAME` 클러스터 리소스의 리소스 그룹 및 이름으로 바꿉니다.

```bash
az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
kubectl create ns chaos-testing
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
```

2. 다음 명령을 실행 하 여 비정상 메시 pod 설치 되었는지 확인 합니다.

```bash
kubectl get po -n chaos-testing
```

다음과 유사한 출력이 표시 됩니다 (비정상 컨트롤러 관리자 및 하나 이상의 비정상 상황-디먼).

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

또한 비정상 [메시 웹 사이트의 설치 지침을 사용할](https://chaos-mesh.org/docs/production-installation-using-helm/)수 있습니다.


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>AKS 클러스터에서 비정상 스튜디오 사용

비정상 스튜디오는 리소스를 비정상 스튜디오로 등록 하지 않는 한 리소스에 대 한 오류를 주입할 수 없습니다. 리소스에 대 한 [대상 및 기능](chaos-studio-targets-capabilities.md) 을 만들어 비정상 스튜디오에 리소스를 등록 합니다. AKS 클러스터에는 하나의 대상 유형 (서비스 직접)만 있지만 다른 리소스에는 최대 두 개의 대상 유형 (서비스 직접 오류 및 에이전트 기반 오류에 대해 하나)이 있을 수 있습니다. 비정상 메시 오류의 각 유형은 기능 (PodChaos, NetworkChaos, IOChaos 등)으로 표현 됩니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. 검색 창에서 비정상 상태 **스튜디오 (미리 보기)** 를 검색 합니다.
3. **대상** 을 클릭 하 고 AKS 클러스터로 이동 합니다.
![Azure Portal의 대상 뷰](images/tutorial-aks-targets.png)
4. AKS 클러스터 옆의 확인란을 선택 하 고 **대상 사용** 을 클릭 한 다음 드롭다운 메뉴에서 **서비스 직접 대상 사용** 을 클릭 합니다.
![Azure Portal에서 대상 사용](images/tutorial-aks-targets-enable.png)
5. 선택한 리소스가 성공적으로 사용 하도록 설정 되었음을 나타내는 알림이 표시 됩니다.
![대상을 보여 주는 알림 설정 성공](images/tutorial-aks-targets-enable-confirm.png)

이제 AKS 클러스터를 비정상 스튜디오로 등록 했습니다. **대상** 보기에서이 리소스에 대해 사용 하도록 설정 된 기능을 관리할 수도 있습니다. 리소스 옆에 있는 **작업 관리** 링크를 클릭 하면 해당 리소스에 대해 사용 하도록 설정 된 기능이 표시 됩니다.

## <a name="create-an-experiment"></a>실험 만들기
이제 AKS 클러스터가 등록를 사용 하 여 실험을 만들 수 있습니다. 비정상 실험은 순서 대로 실행 되는 단계, 병렬로 실행 되는 분기, 병렬로 실행 되는 대상 리소스에 대해 수행 하려는 작업을 정의 합니다.

1. 비정상 스튜디오 탐색에서 **실험** 탭을 클릭 합니다. 이 보기에서는 모든 비정상 실험을 보고 관리할 수 있습니다.  
 ![ Azure Portal에서 실험 실험 보기 추가를 클릭 합니다.](images/tutorial-aks-add.png)
2. 비정상 실험을 배포 하려는 **구독**, **리소스 그룹** 및 **위치** 를 입력 합니다. 실험에 **이름을** 지정 합니다. 다음을 클릭 합니다 **. >실험 디자이너를** 클릭 하 여 
 ![ 기본 실험 정보를 추가 합니다.](images/tutorial-aks-add-basics.png)
3. 이제 비정상 스튜디오 실험 디자이너에 있습니다. 실험 디자이너를 사용 하 여 단계, 분기 및 오류를 추가 하 여 실험을 빌드할 수 있습니다. **단계** 및 **분기** 에 친숙 한 이름을 지정 하 고 **오류 추가** 를 클릭 합니다.
![실험 디자이너](images/tutorial-aks-add-designer.png)
4. 드롭다운에서 비정상 상태 **메시 Pod** 비정상 상태를 선택 **하 고,** 다음 정보를 사용 하 여 마지막으로 실패 한 시간 (분)으로 **기간** 을 AKS 합니다.

    비정상 메시 jsonSpec을 작성 하려면:
    1. 오류 유형 ( [예: PodChaos](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files))에 대 한 비정상 메시 설명서를 참조 하세요.
    2. 비정상 메시 설명서를 사용 하 여 해당 오류 유형에 대 한 YAML 구성을 공식화 합니다.

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
    3. `spec`(사양 속성 이름 포함) 외부의 YAML을 제거 하 고 사양 세부 정보의 들여쓰기를 제거 합니다.

        ```yaml
        action: pod-failure
        mode: one
        duration: '30s'
        selector:
          namespaces:
            - default
        ```
    4. [이와 같은 Yaml-json 변환기](https://www.convertjson.com/yaml-to-json.htm) 를 사용 하 여 비정상 메시 YAML을 json으로 변환 하 고 최소화 합니다.

        ```json
        {"action":"pod-failure","mode":"one","duration":"30s","selector":{"namespaces":["default"]}}
        ```
    5. 최소화 된 JSON을 포털의 **json** 필드에 붙여넣습니다.




**다음: 대상 리소스 >** 
 오류 속성을 클릭 합니다. ![](images/tutorial-aks-add-fault.png)
5. AKS 클러스터를 선택 하 고 **다음** 
 대상 추가를 클릭 합니다. ![](images/tutorial-aks-add-targets.png)
6. 실험이 정확한 지 확인 하 고 **검토 + 만들기**, 만들기를 차례로 클릭 **합니다.** 
 ![ 실험 검토 및 만들기](images/tutorial-aks-add-review.png)

## <a name="give-experiment-permission-to-your-aks-cluster"></a>AKS 클러스터에 대 한 실험 권한 부여
비정상 실험을 만들 때 비정상 스튜디오는 대상 리소스에 대해 오류를 실행 하는 시스템 할당 관리 id를 만듭니다. 실험을 성공적으로 실행 하려면이 id에 대상 리소스에 대 [한 적절 한 권한을](chaos-studio-fault-providers.md) 부여 해야 합니다.

1. AKS 클러스터로 이동 하 고 **Access control (IAM)** 을 클릭 합니다.
![AKS 개요 페이지](images/tutorial-aks-access-resource.png)
2. **추가** 를 클릭 한 후 **역할 할당 추가** 를 클릭 합니다.
![액세스 제어 개요](images/tutorial-aks-access-iam.png)
3. **Azure Kubernetes Service 클러스터 관리자 역할** 을 검색 하 고 역할을 선택 합니다. **다음** 을 클릭 하 여 
 ![ AKS 클러스터 관리자 역할 할당을 클릭 합니다.](images/tutorial-aks-access-role.png)
4. **멤버 선택** 을 클릭 하 고 실험 이름을 검색 합니다. 실험을 선택 하 고 **선택** 을 클릭 합니다. 동일한 테 넌 트에 동일한 이름의 실험이 여러 개 있는 경우 실험 이름은 임의의 문자를 추가 하 여 잘립니다.
![역할에 실험 추가](images/tutorial-aks-access-experiment.png)
5. **검토 + 할당** , **검토 + 할당** 을 차례로 클릭 합니다.

## <a name="run-your-experiment"></a>실험 실행
이제 실험을 실행할 준비가 되었습니다. 영향을 확인 하려면 AKS 클러스터 개요를 열고 별도의 브라우저 탭에서 **Insights** 하는 것이 좋습니다. **활성 Pod 수** 에 대 한 라이브 데이터는 실험 실행의 영향을 보여 줍니다.

1. **실험** 보기에서 실험을 클릭 하 고 **시작** 을 클릭 한 다음 **확인** 을 클릭 합니다.
![실험 시작](images/tutorial-aks-start.png)
2. **상태** 가 **실행 중** 으로 변경 되 면 **기록** 에서 최신 실행에 대 한 **세부 정보** 를 클릭 하 여 실행 중인 실험의 세부 정보를 확인 합니다.

## <a name="next-steps"></a>다음 단계
AKS 비정상 메시 서비스 직접 실험을 실행 했으므로 다음을 수행할 준비가 되었습니다.
- [에이전트 기반 오류를 사용 하는 실험 만들기](chaos-studio-tutorial-agent-based.md)
- [실험 관리](chaos-studio-run-experiment.md)
