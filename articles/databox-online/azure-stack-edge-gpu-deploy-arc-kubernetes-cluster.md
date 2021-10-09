---
title: Azure Stack Edge Pro GPU 디바이스의 Kubernetes에서 Azure Arc 사용 | Microsoft Docs
description: Azure Stack Edge Pro GPU 디바이스의 기존 Kubernetes 클러스터에서 Azure Arc를 사용하도록 설정하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/05/2021
ms.author: alkohli
ms.openlocfilehash: a43b3945a214f0825f1b580610dd364be446eeba
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713082"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스의 Kubernetes 클러스터에 Azure Arc 사용

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro 디바이스의 기존 Kubernetes 클러스터에서 Azure Arc를 사용하도록 설정하는 방법을 보여 줍니다. 

이 절차는 [Azure Stack Edge Pro 디바이스에서 Kubernetes 워크로드를](azure-stack-edge-gpu-kubernetes-workload-management.md) 검토하고 Azure Arc [지원 Kubernetes(미리 보기)란?](../azure-arc/kubernetes/overview.md)개념에 익숙한 사람들을 위한 것입니다.


## <a name="prerequisites"></a>필수 구성 요소

Kubernetes 클러스터에서 Azure Arc를 사용하도록 설정하기 전에 Azure Stack Edge Pro 디바이스 및 디바이스에 액세스하는 데 사용할 클라이언트에 대해 다음과 같은 필수 구성 요소를 완료했는지 확인합니다.

### <a name="for-device"></a>디바이스의 경우

1. 1노드 Azure Stack Edge Pro 디바이스에 대한 로그인 자격 증명이 있습니다.
    1. 디바이스가 활성화되어야 있습니다. [디바이스 활성화](azure-stack-edge-gpu-deploy-activate.md)를 참조하세요.
    1. 디바이스에 Azure Portal을 통해 구성된 컴퓨팅 역할이 있으며, Kubernetes 클러스터가 있습니다. [컴퓨팅 구성](azure-stack-edge-gpu-deploy-configure-compute.md)을 참조하세요.

1. 구독에 대한 소유자 액세스 권한이 있습니다. 서비스 주체에 대한 역할 할당 단계 중에 이 액세스 권한이 필요합니다.
 

### <a name="for-client-accessing-the-device"></a>디바이스에 액세스하는 클라이언트

1. Azure Stack Edge Pro 디바이스에 액세스하는 데 사용되는 Windows 클라이언트 시스템이 있습니다.
  
    - 클라이언트에서 Windows PowerShell 5.0 이상을 실행하고 있습니다. 최신 버전의 Windows PowerShell을 다운로드하려면 [Windows Powershell 설치](/powershell/scripting/install/installing-powershell-core-on-windows)로 이동합니다.
    
    - [지원되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 사용하는 다른 클라이언트도 있을 수 있습니다. 이 문서에서는 Windows 클라이언트를 사용하는 절차에 대해 설명합니다. 
    
1. [Azure Stack Edge Pro 디바이스에서 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)에 설명된 절차를 완료했습니다. 수행한 작업은 다음과 같습니다.
    
    - 클라이언트에 `kubectl`을 설치했습니다.    
    - `kubectl` 클라이언트 버전이 Azure Stack Edge Pro 디바이스에서 실행되는 Kubernetes 마스터 버전보다 두 버전 이상 차이나지 않는지 확인합니다. 
      - `kubectl version`을 사용하여 클라이언트에서 실행되는 kubectl의 버전을 확인합니다. 전체 버전을 기록해 둡니다.
      - Azure Stack Edge Pro 디바이스의 로컬 UI에서 **소프트웨어 업데이트** 로 이동한 후 Kubernetes 서버 버전 번호를 기록해 둡니다. 
    
        ![Kubernetes 서버 버전 번호 확인](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - 이러한 두 버전이 호환되는지 확인합니다. 


## <a name="register-kubernetes-resource-providers"></a>Kubernetes 리소스 공급자 등록

Kubernetes 클러스터에서 Azure Arc를 사용하도록 설정하기 전에 구독에 대해 `Microsoft.Kubernetes` 및 `Microsoft.KubernetesConfiguration`을 사용하도록 설정하고 등록해야 합니다. 

1. 리소스 공급자를 사용하도록 설정하려면 Azure Portal에서 배포에 사용할 구독으로 이동합니다. **리소스 공급자** 로 이동합니다. 
1. 오른쪽 창에서 추가하려는 공급자를 검색합니다. 이 예제에서는 `Microsoft.Kubernetes` 및 `Microsoft.KubernetesConfiguration`입니다.

    ![Kubernetes 리소스 공급자 등록](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. 리소스 공급자를 선택하고 명령 모음 맨 위에서 **등록** 을 선택합니다. 등록은 몇 분 정도 걸립니다. 

    ![Kubernetes 리소스 공급자 등록 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. 리소스 공급자가 등록되어 있는지 확인하기 전까지 UI를 새로 고칩니다. 두 리소스 공급자에 대해 이 프로세스를 반복합니다.
    
    ![Kubernetes 리소스 공급자 등록 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

`az cli`를 통해 리소스 공급자를 등록할 수도 있습니다. 자세한 내용은 [Azure Arc 지원 Kubernetes에 대한 두 공급자 등록을 참조하세요.](../azure-arc/kubernetes/quickstart-connect-cluster.md#1-register-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>서비스 주체 만들기, 역할 할당

1. Azure Stack Edge 서비스의 리소스 배포에 사용한 리소스 그룹의 `Subscription ID` 및 이름이 있는지 확인합니다. 구독 ID를 가져오려면 Azure Portal에서 Azure Stack Edge 리소스로 이동합니다. **개요 > 기본 정보** 로 이동합니다.

    ![구독 ID 가져오기](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    리소스 그룹 이름을 가져오려면 **속성** 으로 이동합니다.

    ![리소스 그룹 이름 가져오기](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. 서비스 주체를 만들려면 `az cli`를 통해 다음 명령을 사용합니다.

    `az ad sp create-for-rbac --skip-assignment --name "<Informative name for service principal>"`  

    `az cli`에 로그인하는 방법에 대한 내용을 보려면 [Azure Portal에서 Cloud Shell을 시작](../cloud-shell/quickstart-powershell.md#start-cloud-shell)하세요.

    다음은 예제입니다. 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. `appID`, `name`, `password` 및 `tenantID`는 다음 명령에서 입력으로 사용하기 때문에 기록해 둡니다.

1. 새 서비스 주체를 만든 후 새로 만든 보안 주체에 `Kubernetes Cluster - Azure Arc Onboarding` 역할을 할당합니다. 이 역할은 제한된 권한의 기본 제공 Azure 역할(명령에서 역할 ID 사용)입니다. 다음 명령을 사용합니다.

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    다음은 예제입니다.
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    서비스 주체를 만들고 역할 할당을 수행하는 방법에 대한 자세한 내용은 [Azure Arc 지원 온보딩 서비스 주체 만들기](../azure-arc/kubernetes/create-onboarding-service-principal.md)의 단계를 참조하세요.


## <a name="enable-arc-on-kubernetes-cluster"></a>Kubernetes 클러스터에서 Arc 사용

Azure Arc 관리를 위해 Kubernetes 클러스터를 구성하려면 다음 단계를 수행합니다.

1. 디바이스의 [PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. 유형:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>"` 
    
    이 명령을 실행하면 를 입력하라는 후속 메시지가 `ClientSecret` 표시됩니다. 서비스 주체 암호를 제공합니다.

    Azure 퍼블릭이 아닌 다른 클라우드를 사용하는 경우 `CloudEnvironment` 매개 변수를 추가합니다. 이 매개 변수를 , , 및 로 설정할 수 `AZUREPUBLICCLOUD` `AZURECHINACLOUD` `AZUREGERMANCLOUD` `AZUREUSGOVERNMENTCLOUD` 있습니다.

    > [!NOTE]
    > - 디바이스에 Azure Arc를 배포하려면 [Azure Arc의 지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)을 사용해야 합니다. 
    > - `az account list-locations` 명령을 사용하여 `Set-HcsKubernetesAzureArcAgent` cmdlet에 전달할 정확한 위치 이름을 파악합니다. 위치 이름 형식은 일반적으로 공백이 없습니다.
    > - `ClientId` 및 `ClientSecret` 가 필요합니다. 
    
    예를 들면 다음과 같습니다.
   
    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed&quot; -ResourceGroupName &quot;myaserg1&quot; -ResourceName &quot;myasetestresarc&quot; -Location &quot;westeurope&quot; -TenantId &quot;72f988bf-86f1-41af-91ab-2d7cd011db47&quot; -ClientId &quot;aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b"
    
    WARNING: A script or application on the remote computer 10.126.76.0 is sending a prompt request. When you are prompted,
    enter sensitive information, such as credentials or passwords, only if you trust the remote computer and the
    application or script that is requesting the data.

    cmdlet Set-HcsKubernetesAzureArcAgent at command pipeline position 1

    Supply values for the following parameters:
    ClientSecret: **********************************
    [10.100.10.10]: PS>
    ```
    
    Azure Portal에서는 이전 명령에 제공했던 이름으로 리소스를 만들어야 합니다.

    ![Azure Arc 리소스로 이동](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Azure Arc가 사용하도록 설정되었는지 확인하려면 PowerShell 인터페이스에서 다음 명령을 실행합니다.

    `kubectl get deployments -n azure-arc`

    이 명령은 Azure Arc에 해당하는 `azure-arc` 네임스페이스에 배포된 모든 애플리케이션을 찾습니다.

    다음은 `azure-arc` 네임스페이스의 Kubernetes 클러스터에 배포된 Azure Arc 에이전트를 보여 주는 샘플 출력입니다. 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    `azure-arc` 네임스페이스의 Kubernetes 클러스터에서 실행되는 pod 목록을 가져올 수도 있습니다. Pod는 Kubernetes 클러스터에서 실행되는 애플리케이션 컨테이너 또는 프로세스입니다. 

    다음 명령을 사용합니다.
    
    `kubectl get pods -n azure-arc`
    
    샘플 출력은 다음과 같습니다.
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


앞의 출력과 같이 Azure Arc 지원 Kubernetes는 네임스페이스에 배포된 클러스터에서 실행되는 몇 가지 에이전트(운영자)로 `azure-arc` 구성됩니다.

- `config-agent`: 클러스터에 적용된 원본 제어 구성 리소스에 대해 연결된 클러스터를 감시하고 호환성 상태를 업데이트합니다.
- `controller-manager`: 연산자의 연산자이며 Azure Arc 구성 요소 간의 상호 작용을 조정합니다.
- `metrics-agent`: 다른 Arc 에이전트의 메트릭을 수집하여 이러한 에이전트가 최적의 성능을 발휘하는지 확인합니다.
- `cluster-metadata-operator`: 클러스터 메타데이터 - 클러스터 버전, 노드 수 및 Azure Arc 에이전트 버전을 수집합니다.
- `resource-sync-agent`: 위에서 언급한 클러스터 메타데이터를 Azure에 동기화합니다.
- `clusteridentityoperator`: Azure Arc 사용하도록 설정된 Kubernetes는 현재 시스템 할당 ID를 지원합니다. 다른 에이전트가 Azure와 통신하기 위해 사용하는 MSI(관리 서비스 ID) 인증서를 유지 관리합니다.
- `flux-logs-agent`: 원본 제어 구성의 일부로 배포된 Flux 연산자에서 로그를 수집합니다.
- `connect-agent`: Azure Arc 리소스와 통신합니다.

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Kubernetes 클러스터에서 Arc 제거

Azure Arc 관리를 제거하려면 다음 단계를 수행합니다.

1. 1. 디바이스의 [PowerShell 인터페이스에 연결합니다](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
2. 유형:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> 기본적으로 Git 리포지토리에서 리소스 `yamls`가 삭제되면 해당 리소스가 Kubernetes 클러스터에서 삭제되지 않습니다. Git 리포지토리에서 삭제될 때 리소스 삭제를 허용하도록 Arc OperatorParams에서 `--sync-garbage-collection`을 설정해야 합니다. 자세한 내용은 [구성 삭제](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Arc 배포를 실행하는 방법을 이해하려면 [Azure Stack Edge Pro 디바이스에서 GitOps를 통해 Redis를 사용하여 상태 비저장 PHP `Guestbook` 애플리케이션 배포](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)를 참조하세요.
