---
title: '빠른 시작: Azure Portal을 사용하여 AKS 클러스터 배포'
titleSuffix: Azure Kubernetes Service
description: Azure Portal을 사용하여 Kubernetes 클러스터를 빠르게 만들고 애플리케이션을 배포하고 AKS(Azure Kubernetes Service)의 성능을 모니터링하는 방법을 알아봅니다.
services: container-service
ms.topic: quickstart
ms.date: 07/01/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3, mode-portal
ms.openlocfilehash: 4c2aa9973ea95f88209a74d5090a3ba2ccdf3112
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133076522"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 AKS(Azure Kubernetes Service) 클러스터 배포

AKS(Azure Kubernetes Service)는 클러스터를 빠르게 배포하고 관리할 수 있는 관리형 Kubernetes 서비스입니다. 이 빠른 시작에서는 다음을 수행합니다.
* Azure Portal을 사용하여 AKS 클러스터를 배포합니다. 
* 클러스터에서 웹 프런트 엔드 및 Redis 인스턴스를 사용하여 다중 컨테이너 애플리케이션을 실행합니다. 
* 애플리케이션을 실행하는 클러스터와 Pod의 상태를 모니터링합니다.

![Azure Vote 애플리케이션 예제로 이동하는 이미지](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

이 빠른 시작에서는 Kubernetes 기본 개념을 이해하고 있다고 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

2. **컨테이너** > **Kubernetes Service** 를 선택합니다.

3. **기본** 페이지에서 다음 옵션을 구성합니다.
    - **프로젝트 세부 정보**: 
        * Azure **구독** 을 선택합니다.
        * Azure **리소스 그룹**(예: *myResourceGroup*)을 선택하거나 만듭니다.
    - **클러스터 세부 정보**: 
        * **사전 설정 구성** 이 ‘표준($$)’인지 확인합니다. 사전 설정 구성에 관한 자세한 내용은 [Azure Portal의 클러스터 구성 사전 설정][preset-config]을 참조하세요.
        * *myAKSCluster* 같은 **Kubernetes 클러스터 이름** 을 입력합니다. 
        * AKS 클러스터에 대한 **지역** 및 **Kubernetes 버전** 을 선택합니다.
    - **주 노드 풀**: 
        * 선택된 기본값을 유지합니다.
    
    ![AKS 클러스터 만들기 - 기본 정보를 입력합니다.](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    > [!NOTE]
    > ‘모든 사전 설정 구성 보기’를 선택하고 다른 옵션을 선택하여 클러스터를 만들면 사전 설정 구성을 변경할 수 있습니다.
    > ![AKS 클러스터 만들기 - 포털 사전 설정 옵션](media/kubernetes-walkthrough-portal/cluster-preset-options.png)

4. 완료되면 **다음: 노드 풀** 을 선택합니다.

5. 기본 **노드 풀** 옵션을 유지합니다. 화면 아래쪽에서 **다음: 인증** 을 클릭합니다.
    > [!CAUTION]
    > 새로 생성된 Azure AD 서비스 주체가 전파되고 사용할 수 있게 되기까지 몇 분이 걸릴 수 있으며, 이로 인해 Azure Portal에서 "서비스 주체를 찾을 수 없음" 오류와 유효성 검사 오류가 발생할 수 있습니다. 이 범프에 도달하면 문제 완화를 위해 [문제 해결 문서](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster)를 참조하세요.

6. **인증** 페이지에서 다음 옵션을 구성합니다.
    - 다음 중 하나를 수행하여 새 클러스터 ID를 만듭니다.
        * **인증** 필드를 **시스템 할당 관리 ID** 로 유지하거나
        * **서비스 주체** 를 선택하여 서비스 주체를 사용합니다. 
            * *(새) 기본 서비스 주체* 를 선택하여 기본 서비스 주체를 만들거나
            * *서비스 주체 구성* 을 선택하여 기존 서비스 주체를 사용합니다. 기존 보안 주체의 SPN 클라이언트 ID와 비밀을 제공해야 합니다.
    - Kubernetes RBAC(Kubernetes 역할 기반 액세스 제어) 옵션을 활성화하여 AKS 클러스터에 배포된 Kubernetes 리소스에 대한 액세스를 보다 세밀하게 제어할 수 있습니다.

    기본적으로 *기본* 네트워킹이 사용되며 컨테이너에 대한 Azure Monitor가 활성화됩니다. 

7. **검토 + 만들기** 를 클릭한 후 유효성 검사가 완료되면 **만들기** 를 선택합니다. 


8. AKS 클러스터를 만드는 데 몇 분이 걸립니다. 배포가 완료되면 다음 중 하나를 수행하여 리소스로 이동합니다.
    * **리소스로 이동** 을 클릭하거나
    * AKS 클러스터 리소스 그룹으로 이동하고 AKS 리소스를 선택합니다. 
        * 아래 클러스터 대시보드의 예: *myResourceGroup* 을 검색하고 *myAKSCluster* 리소스를 선택합니다.

        ![Azure Portal의 AKS 대시보드 예제](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. 

1. Azure Portal 위쪽에 있는 `>_` 단추를 사용하여 Cloud Shell을 엽니다.

    ![포털에서 Azure Cloud Shell을 엽니다.](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > 로컬 셸 설치에서 이러한 작업을 수행하려면 다음을 수행합니다.
    > 1. Azure CLI가 설치되어 있는지 확인합니다.
    > 2. `az login` 명령을 통해 Azure에 연결합니다.

2. [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 다음 명령은 자격 증명을 다운로드하고 해당 자격 증명을 사용하도록 Kubernetes CLI를 구성합니다.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. `kubectl get`을 사용해 클러스터에 대한 연결을 확인하여 클러스터 노드 목록을 반환합니다.

    ```console
    kubectl get nodes
    ```

    출력은 이전 단계에서 만든 단일 노드를 보여줍니다. 노드 상태가 *준비* 인지 확인합니다.

    ```output
    NAME                                STATUS   ROLES   AGE   VERSION
    aks-agentpool-12345678-vmss000000   Ready    agent   23m   v1.19.11
    aks-agentpool-12345678-vmss000001   Ready    agent   24m   v1.19.11
    ```

## <a name="run-the-application"></a>애플리케이션 실행

Kubernetes 매니페스트 파일은 실행할 컨테이너 이미지와 같은 클러스터에 대해 원하는 상태를 정의합니다. 

이 빠른 시작에서는 매니페스트를 사용하여 Azure Vote 애플리케이션을 실행하는 데 필요한 모든 개체를 만듭니다. 이 매니페스트에는 다음과 같은 두 개의 Kubernetes 배포가 포함됩니다.
* 샘플 Azure Vote Python 애플리케이션.
* Redis 인스턴스. 

다음과 같은 두 개의 Kubernetes 서비스도 생성됩니다.
* Redis 인스턴스에 대한 내부 서비스.
* 인터넷에서 Azure Vote 애플리케이션에 액세스하기 위한 외부 서비스.

1. Cloud Shell에서 편집기를 사용하여 다음과 같이 `azure-vote.yaml`이라는 파일을 만듭니다.
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml` 또는 
    * `vi azure-vote.yaml`. 

1. 다음 YAML 정의에 복사합니다.

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. `kubectl apply` 명령을 사용하여 애플리케이션을 배포하고 YAML 매니페스트의 이름을 지정합니다.

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    출력은 성공적으로 생성된 배포 및 서비스를 보여 줍니다.

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 `kubectl get service` 명령을 사용합니다.

```console
kubectl get service azure-vote-front --watch
```

`azure-vote-front` 서비스의 **EXTERNAL-IP** 출력은 처음에 *보류 중* 으로 표시됩니다.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**EXTERNAL-IP** 주소가 *보류 중* 에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.


```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Azure Vote 앱이 실제로 작동하는 모습을 보려면 웹 브라우저를 서비스의 외부 IP 주소로 엽니다.

![Azure Vote 애플리케이션 예제로 이동하는 이미지](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>상태 및 로그 모니터링

클러스터를 만들 때 컨테이너에 대한 Azure Monitor가 활성화되었습니다. 컨테이너용 Azure Monitor는 클러스터에서 실행되는 AKS 클러스터와 Pod 모두에 대한 상태 메트릭을 제공합니다.

메트릭 데이터는 Azure Portal에 채우는 데 몇 분 정도 걸립니다. Azure Vote Pod의 현재 상태, 작동 시간 및 리소스 사용량을 보려면 다음을 수행합니다.

1. Azure Portal에서 AKS 리소스로 다시 이동합니다.
1. 왼쪽에 있는 **모니터링** 에서 **인사이트** 를 선택합니다.
1. 상단에서 **+ 필터 추가** 를 선택합니다.
1. **네임스페이스** 를 속성으로 선택한 다음, *\<All but kube-system\>* 을 선택합니다.
1. 보려는 **컨테이너** 를 선택합니다.

다음 예제와 같이 `azure-vote-back` 및 `azure-vote-front` 컨테이너가 표시됩니다.

![AKS에서 실행 중인 컨테이너의 상태 보기](media/kubernetes-walkthrough-portal/monitor-containers.png)

`azure-vote-front` Pod의 로그를 보려면 오른쪽에 있는 ‘azure-vote-front | 개요’ 영역 위쪽에서 **Log Analytics에서 보기** 를 선택합니다. 이러한 로그는 컨테이너의 *stdout* 및 *stderr* 스트림을 포함합니다.

![AKS에서 컨테이너 로그 보기](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>클러스터 삭제

Azure 요금이 부과되지 않도록 불필요한 리소스를 정리하세요. AKS 클러스터 대시보드에서 **삭제** 단추를 선택합니다. Cloud Shell에서 [az aks delete][az-aks-delete] 명령을 사용할 수도 있습니다.

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요.
> 
> 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="get-the-code"></a>코드 가져오기

이 빠른 시작에서는 Kubernetes 배포를 만드는 데 미리 생성된 컨테이너 이미지를 사용했습니다. 관련된 애플리케이션 코드, Dockerfile 및 Kubernetes 매니페스트 파일을 [GitHub에서 사용][azure-vote-app]할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Kubernetes 클러스터를 배포한 다음, 해당 클러스터에 다중 컨테이너 애플리케이션을 배포했습니다. AKS 클러스터에 대한 Kubernetes 웹 대시보드에 액세스합니다.


애플리케이션 빌드, Azure Container Registry에서 배포, 실행 중인 애플리케이션 업데이트, 클러스터 크기 조정 및 업그레이드를 비롯한 전체 예제를 통해 AKS에 대해 자세히 알아보려면 Kubernetes cluster 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-delete]: /cli/azure/aks#az_aks_delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[preset-config]: ./quotas-skus-regions.md#cluster-configuration-presets-in-the-azure-portal
[sp-delete]: kubernetes-service-principal.md#additional-considerations
