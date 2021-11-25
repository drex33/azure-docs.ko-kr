---
title: 빠른 시작 - AKS(Azure Kubernetes Service) 클러스터 만들기
description: Azure Resource Manager 템플릿을 사용하여 Kubernetes 클러스터를 신속하고 만들고 AKS(Azure Kubernetes Service)에 애플리케이션을 배포하는 방법을 알아봅니다.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, subject-armqs, devx-track-azurecli, mode-arm
ms.openlocfilehash: f313ed868cf86ab648db8d9a21095b951d7a7859
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133060946"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 AKS(Azure Kubernetes Service) 클러스터 배포

AKS(Azure Kubernetes Service)는 클러스터를 빠르게 배포하고 관리할 수 있는 관리형 Kubernetes 서비스입니다. 이 빠른 시작에서는 다음을 수행합니다.
* Azure Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포합니다. 
* 클러스터에서 웹 프런트 엔드 및 Redis 인스턴스를 사용하여 다중 컨테이너 애플리케이션을 실행합니다. 

![Azure Vote로 이동하는 이미지](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

이 빠른 시작에서는 Kubernetes 기본 개념을 이해하고 있다고 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.kubernetes%2Faks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.61 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

- Resource Manager 템플릿을 사용하여 AKS 클러스터를 만들려면 SSH 공개 키를 제공합니다. 이 리소스가 필요한 경우 다음 섹션을 참조하세요. 그렇지 않으면 [템플릿 검토](#review-the-template) 섹션으로 건너뜁니다.

### <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

AKS 노드에 액세스하려면 `ssh-keygen` 명령을 사용해 생성하는 SSH 키 쌍(퍼블릭 및 프라이빗)을 사용하여 연결합니다. 기본적으로 이러한 파일은 *~/.ssh* 디렉터리에 만들어집니다. `ssh-keygen` 명령을 실행하면 지정된 위치에 이미 존재하는 동일한 이름의 SSH 키 쌍을 덮어씁니다.

1. [https://shell.azure.com](https://shell.azure.com)으로 이동하여 브라우저에서 Cloud Shell을 엽니다.

1. `ssh-keygen` 명령을 실행합니다. 다음 예제에서는 RSA 암호화 및 2048비트 길이를 사용하여 SSH 키 쌍을 만듭니다.

    ```console
    ssh-keygen -t rsa -b 2048
    ```

SSH 생성에 대한 자세한 내용은 [Azure에서 인증용 SSH 키 생성 및 관리][ssh-keys]를 참조하세요.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/aks/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.kubernetes/aks/azuredeploy.json":::

AKS 샘플을 더 보려면 [AKS 빠른 시작 템플릿][aks-quickstart-templates] 사이트를 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 단추를 선택하여 Azure에 로그인하고 템플릿을 엽니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.kubernetes%2Faks%2Fazuredeploy.json)

2. 다음 값을 선택하거나 입력합니다.

    이 빠른 시작에서는 *OS 디스크 크기 GB*, *에이전트 수*, *에이전트 VM 크기*, *OS 종류* 및 *Kubernetes 버전* 에 대한 기본 값을 유지합니다. 다음 템플릿 매개 변수에 대해서는 직접 값을 제공합니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기** 를 선택합니다. 리소스 그룹에 대한 고유 이름(예: *myResourceGroup*)을 입력한 다음, **확인** 을 선택합니다.
    * **위치**: 위치(예: **미국 동부**)를 선택합니다.
    * **클러스터 이름**: AKS 클러스터에 대한 고유 이름(예: *myAKSCluster*)을 입력합니다.
    * **DNS 접두사**: 클러스터에 대한 고유 DNS 접두사(예: *myakscluster*)를 입력합니다.
    * **Linux 관리자 사용자 이름**: SSH를 사용하여 연결할 사용자 이름(예: *azureuser*)을 입력합니다.
    * **SSH RSA 공개 키**: SSH 키 쌍의 *public* 부분(기본적으로 *~/.ssh/id_rsa.pub* 의 콘텐츠)을 복사하여 붙여넣습니다.

    ![포털에서 Azure Kubernetes Service 클러스터를 만드는 Resource Manager 템플릿](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. **검토 + 만들기** 를 선택합니다.

AKS 클러스터를 만드는 데 몇 분이 걸립니다. 다음 단계로 넘어가기 전에 클러스터가 성공적으로 배포될 때까지 기다립니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

### <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. 

1. [az aks install-cli][az-aks-install-cli] 명령을 사용하여 `kubectl`을 로컬로 설치합니다.

    ```azurecli
    az aks install-cli
    ```

2. [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. [kubectl get][kubectl-get] 명령을 사용하여 클러스터에 대한 연결을 확인합니다. 이 명령은 클러스터 노드 목록을 반환합니다.

    ```console
    kubectl get nodes
    ```

    출력은 이전 단계에서 만든 노드를 보여줍니다. 모든 노드의 상태가 *준비됨* 인지 확인합니다.

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>애플리케이션 실행

[Kubernetes 매니페스트 파일][kubernetes-deployment]은 실행할 컨테이너 이미지와 같은 클러스터에 대해 원하는 상태를 정의합니다. 

이 빠른 시작에서는 매니페스트를 사용하여 [Azure Vote 애플리케이션][azure-vote-app]을 실행하는 데 필요한 모든 개체를 만듭니다. 이 매니페스트에는 다음과 같은 두 개의 [Kubernetes 배포][kubernetes-deployment]가 포함됩니다.
* 샘플 Azure Vote Python 애플리케이션.
* Redis 인스턴스. 

다음과 같은 두 개의 [Kubernetes 서비스][kubernetes-service]도 생성됩니다.
* Redis 인스턴스에 대한 내부 서비스.
* 인터넷에서 Azure Vote 애플리케이션에 액세스하기 위한 외부 서비스.

1. `azure-vote.yaml`라는 파일을 만듭니다.
    * Azure Cloud Shell을 사용하는 경우 이 파일은 가상 또는 실제 시스템에서 작업하고 있는 것처럼 `vi` 또는 `nano`를 사용하여 만들 수 있습니다.
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

1. [kubectl apply][kubectl-apply] 명령을 사용하여 애플리케이션을 배포하고 YAML 매니페스트의 이름을 지정합니다.

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

### <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

`--watch` 인수와 함께 [kubectl get service][kubectl-get] 명령을 사용하여 진행 상황을 모니터링합니다.

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

![Azure Vote로 이동하는 이미지](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure 요금이 부과되지 않도록 불필요한 리소스를 정리하세요. [az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요.
> 
> 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="get-the-code"></a>코드 가져오기

이 빠른 시작에서는 Kubernetes 배포를 만드는 데 미리 생성된 컨테이너 이미지를 사용했습니다. 관련된 애플리케이션 코드, Dockerfile 및 Kubernetes 매니페스트 파일을 [GitHub에서 사용][azure-vote-app]할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Kubernetes 클러스터를 배포한 다음, 해당 클러스터에 다중 컨테이너 애플리케이션을 배포했습니다. AKS 클러스터에 대한 [Kubernetes 웹 대시보드에 액세스][kubernetes-dashboard]합니다.

AKS에 대해 자세히 알아보고 배포 예제에 대한 전체 코드를 연습해 보려면 Kubernetes 클러스터 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: /previous-versions/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
