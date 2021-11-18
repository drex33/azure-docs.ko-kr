---
title: Azure CLI에서 Azure 비정상 스튜디오를 사용 하 여 AKS 비정상 메시 오류를 사용 하는 실험을 만듭니다.
description: Azure CLI에서 AKS 비정상 메시 오류를 사용 하는 실험을 만듭니다.
author: johnkemnetz
ms.topic: how-to
ms.date: 11/11/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ab19c00739fea50b17ab885ffccbb397c115867e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722320"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-with-the-azure-cli"></a>Azure CLI에서 비정상 메시 오류를 사용 하는 비정상 실험 만들기

비정상 실험을 사용 하 여 제어 되는 환경에서 오류를 발생 시켜 응용 프로그램이 오류에 탄력적으로 대처할 수 있는지 확인할 수 있습니다. 이 가이드에서는 비정상 실험 및 Azure 비정상 스튜디오를 사용 하 여 네임 스페이스에서 주기적인 Azure Kubernetes Service pod 오류가 발생 합니다. 이 실험을 실행 하면 간헐적으로 오류가 발생 하는 경우 서비스를 사용할 수 없도록 방지할 수 있습니다.

Azure 비정상 스튜디오는 Kubernetes에 대 한 무료 오픈 소스 비정상 엔지니어링 플랫폼인 비정상 [메시](https://chaos-mesh.org/)를 사용 하 여 AKS 클러스터에 오류를 삽입 합니다. 혼란 스러운 메시 오류는 AKS 클러스터에 비정상 메시를 설치 해야 하는 [서비스 직접](chaos-studio-tutorial-aks-portal.md) 오류입니다. 이러한 동일한 단계를 사용 하 여 AKS 비정상 메시 오류에 대 한 실험을 설정 하 고 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- AKS 클러스터 AKS 클러스터가 없는 경우 [다음 단계를 수행 하 여 만들](../aks/kubernetes-walkthrough-portal.md)수 있습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 열 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, **Enter** 를 선택하여 실행합니다.

이 자습서에서 CLI를 로컬로 설치하여 사용하려면 Azure CLI 버전 2.0.30 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

> [!NOTE]
> 이러한 지침은 Azure Cloud Shell에서 Bash 터미널을 사용 합니다. CLI를 로컬로 실행 하거나 PowerShell 터미널에서 실행 하는 경우에 설명 된 대로 일부 명령은 작동 하지 않을 수 있습니다.

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>AKS 클러스터에 비정상 상태 메시 설정

비정상 스튜디오에서 비정상 메시 오류를 실행 하려면 AKS 클러스터에 비정상 메시를 설치 해야 합니다.

1. 활성 구독이 AKS 클러스터가 배포 된 구독으로 설정 된 [Azure Cloud Shell](../cloud-shell/overview.md) 창에서 다음 명령을 실행 합니다. `$RESOURCE_GROUP`및를 `$CLUSTER_NAME` 클러스터 리소스의 리소스 그룹 및 이름으로 바꿉니다.

   ```azurecli-interactive
   az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
   helm repo add chaos-mesh https://charts.chaos-mesh.org
   helm repo update
   kubectl create ns chaos-testing
   helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
   ```

2. 다음 명령을 실행 하 여 비정상 메시 pod 설치 되었는지 확인 합니다.

   ```azurecli-interactive
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

1. `$RESOURCE_ID`온 보 딩 하는 AKS 클러스터의 리소스 ID로 대체 하 여 대상을 만듭니다.

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. `$RESOURCE_ID`온 보 딩 하는 AKS 클러스터의 리소스 ID와 사용 `$CAPABILITY` [하도록 설정 하는 오류 기능의 이름을](chaos-studio-fault-library.md)바꿔서 대상의 기능을 만듭니다.
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/$CAPABILITY?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    예를 들어 PodChaos 기능을 사용 하도록 설정 하는 경우:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh/capabilities/PodChaos-2.1?api-version=2021-09-15-preview"  --body "{\"properties\":{}}"
    ```

    클러스터에서 사용 하도록 설정할 각 기능에 대해이 작업을 수행 해야 합니다.

이제 AKS 클러스터를 비정상 스튜디오로 등록 했습니다.

## <a name="create-an-experiment"></a>실험 만들기
이제 AKS 클러스터가 등록를 사용 하 여 실험을 만들 수 있습니다. 비정상 실험은 순서 대로 실행 되는 단계, 병렬로 실행 되는 분기, 병렬로 실행 되는 대상 리소스에 대해 수행 하려는 작업을 정의 합니다.

1. 비정상 메시 jsonSpec을 만듭니다.
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
          mode: all
          duration: '600s'
          selector:
            namespaces:
              - default
        ```
    3. `spec`(사양 속성 이름 포함) 외부의 YAML을 제거 하 고 사양 세부 정보의 들여쓰기를 제거 합니다.

        ```yaml
        action: pod-failure
        mode: all
        duration: '600s'
        selector:
          namespaces:
            - default
        ```
    4. [이와 같은 Yaml-json 변환기](https://www.convertjson.com/yaml-to-json.htm) 를 사용 하 여 비정상 메시 YAML을 json으로 변환 하 고 최소화 합니다.

        ```json
        {"action":"pod-failure","mode":"all","duration":"600s","selector":{"namespaces":["default"]}}
        ```
    5. [이와 같은 json 문자열 이스케이프 도구](https://www.freeformatter.com/json-escape.html) 를 사용 하 여 json 사양을 이스케이프 합니다.
    
        ```json
        {\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}
        ```

2. 아래 JSON 샘플부터 실험 JSON을 만듭니다. [Create 실험 API](/rest/api/chaosstudio/experiments/create-or-update), [오류 라이브러리](chaos-studio-fault-library.md)및 이전 단계에서 만든 jsonSpec를 사용 하 여 실행 하려는 실험에 해당 하는 JSON을 수정 합니다.

    ```json
    {
      "location": "centralus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "AKS pod kill",
            "branches": [
              {
                "name": "AKS pod kill",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                          "key": "jsonSpec",
                          "value": "{\"action\":\"pod-failure\",\"mode\":\"all\",\"duration\":\"600s\",\"selector\":{\"namespaces\":[\"default\"]}}"
                      }
                    ],
                    "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.ContainerService/managedClusters/myCluster/providers/Microsoft.Chaos/targets/Microsoft-AzureKubernetesServiceChaosMesh"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` 및를 `$EXPERIMENT_NAME` 실험의 속성으로 바꿔 Azure CLI를 사용 하 여 실험을 만듭니다. JSON 파일 이름과 함께 실험 JSON 및 업데이트를 저장 하 고 업로드 했는지 확인 `experiment.json` 합니다.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    각 실험은 해당 하는 시스템 할당 관리 id를 만듭니다. `principalId`다음 단계에 대 한 응답에서이 id의를 확인 합니다.

## <a name="give-experiment-permission-to-your-aks-cluster"></a>AKS 클러스터에 대 한 실험 권한 부여
비정상 실험을 만들 때 비정상 스튜디오는 대상 리소스에 대해 오류를 실행 하는 시스템 할당 관리 id를 만듭니다. 실험을 성공적으로 실행 하려면이 id에 대상 리소스에 대 [한 적절 한 권한을](chaos-studio-fault-providers.md) 부여 해야 합니다.

아래의 명령을 사용 하 여 리소스에 대 한 실험 액세스를 제공 하 고,를 `$EXPERIMENT_PRINCIPAL_ID` 이전 단계의 principalId로 바꾸고,을 `$RESOURCE_ID` 대상 리소스의 리소스 id로 바꿉니다 (이 경우 AKS 클러스터 리소스 id). 실험에서 대상으로 지정 된 각 리소스에 대해이 명령을 실행 합니다. 

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Cluster User Role" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>실험 실행
이제 실험을 실행할 준비가 되었습니다. 영향을 확인 하려면 AKS 클러스터 개요를 열고 별도의 브라우저 탭에서 **Insights** 하는 것이 좋습니다. **활성 Pod 수** 에 대 한 라이브 데이터는 실험 실행의 영향을 보여 줍니다.

1. Azure CLI를 사용 하 여 실험을 시작 하 고, `$SUBSCRIPTION_ID` `$RESOURCE_GROUP` 및를 `$EXPERIMENT_NAME` 실험의 속성으로 바꿉니다.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. 응답에는 실험을 실행할 때 실험 상태를 쿼리 하는 데 사용할 수 있는 상태 URL이 포함 되어 있습니다.

## <a name="next-steps"></a>다음 단계
AKS 비정상 메시 서비스 직접 실험을 실행 했으므로 다음을 수행할 준비가 되었습니다.
- [에이전트 기반 오류를 사용 하는 실험 만들기](chaos-studio-tutorial-agent-based-portal.md)
- [실험 관리](chaos-studio-run-experiment.md)
