---
title: 빠른 시작 - 컨테이너 인스턴스에 Docker 컨테이너 배포 - Azure CLI
description: 이 빠른 시작에서는 Azure CLI를 사용하여, 격리된 Azure 컨테이너 인스턴스에서 실행하는 컨테이너화된 웹앱을 신속하게 배포합니다.
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom: seo-python-october2019, seodec18, mvc, devx-track-js, devx-track-azurecli, mode-api
ms.openlocfilehash: 288087db751d62ca2b51d882ebf7b22f9aad01a5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133053322"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure에서 컨테이너 인스턴스 배포

Azure Container Instances를 사용하여 Azure에서 서버리스 Docker 컨테이너를 간단하고 빠르게 실행합니다. Azure Kubernetes Service와 같은 풀 컨테이너 오케스트레이션 플랫폼이 필요하지 않을 경우 애플리케이션을 요청 시 컨테이너 인스턴스에 배포합니다.

이 빠른 시작에서는 Azure CLI를 사용하여 격리된 Docker 컨테이너를 배포하고 해당 애플리케이션을 정규화된 도메인 이름(FQDN)으로 사용 가능하게 합니다. 단일 배포 명령을 실행한 후 몇 초 내에 컨테이너에서 실행 중인 애플리케이션을 찾아볼 수 있습니다.

![브라우저에서 Azure Container Instances에 배포된 앱 보기][aci-app-browser]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 빠른 시작에는 Azure CLI 버전 2.0.55 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

모든 Azure 리소스와 마찬가지로 Azure 컨테이너 인스턴스는 리소스 그룹에 배포해야 합니다. 리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.

먼저, 다음 [az group create][az-group-create] 명령을 사용하여 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>컨테이너 만들기

이제 리소스 그룹이 있으므로 Azure에서 컨테이너를 실행할 수 있습니다. Azure CLI를 사용하여 컨테이너 인스턴스를 만들려면 [az container create][az-container-create] 명령에 리소스 그룹 이름, 컨테이너 인스턴스 이름 및 Docker 컨테이너 이미지를 제공합니다. 이 빠른 시작에서는 공용 `mcr.microsoft.com/azuredocs/aci-helloworld` 이미지를 사용합니다. 이 이미지는 고정 HTML 페이지를 제공하는 Node.js로 작성된 작은 웹앱을 패키징합니다.

열려는 하나 이상의 포트, DNS 이름 레이블 또는 둘 다를 지정하여 컨테이너를 인터넷에 공개할 수 있습니다. 이 빠른 시작에서는 DNS 이름 레이블이 있는 컨테이너를 배포하여 웹앱을 공개적으로 연결할 수 있도록 합니다.

컨테이너 인스턴스를 시작하려면 다음과 유사한 명령을 실행합니다. 인스턴스를 만드는 Azure 지역 내에서 고유한 `--dns-name-label` 값을 설정합니다. "DNS 이름 레이블을 사용할 수 없습니다"라는 오류 메시지가 표시되면 다른 DNS 이름 레이블을 사용해 보세요.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

몇 초 내에 Azure CLI로부터 배포가 완료되었음을 알려주는 응답을 받습니다. [az container show][az-container-show] 명령을 사용하여 상태를 확인합니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

명령을 실행하면 컨테이너의 FQDN(정규화된 도메인 이름) 및 해당 프로비전 상태가 표시됩니다.

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

컨테이너의 `ProvisioningState`가 **Succeeded**(성공)이면 브라우저에서 해당 FQDN으로 이동합니다. 다음과 비슷한 웹 페이지가 표시됩니다. Docker 컨테이너에서 실행되는 애플리케이션이 Azure에 성공적으로 배포되었습니다.

![브라우저에서 Azure Container Instances에 배포된 앱 보기][aci-app-browser]

처음에 애플리케이션이 표시되지 않으면 DNS가 전파되는 동안 잠시 기다린 후에 브라우저를 새로 고쳐야 할 수 있습니다.

## <a name="pull-the-container-logs"></a>컨테이너 로그 끌어오기

컨테이너 또는 컨테이너가 실행되는 애플리케이션의 문제를 해결해야 하거나 해당 문제에 대한 출력만 보려는 경우 컨테이너 인스턴스의 로그를 확인하면서 시작합니다.

[az container logs][az-container-logs] 명령을 사용하여 컨테이너 인스턴스 로그를 끌어옵니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

출력은 컨테이너의 로그를 표시하며, 브라우저에서 애플리케이션을 볼 때 생성된 HTTP GET 요청을 표시해야 합니다.

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>출력 스트림 연결

로그를 확인하는 것 외에도 로컬 표준 출력과 표준 오류 스트림을 컨테이너의 해당 표준 출력 스트림에 연결할 수 있습니다.

먼저 [az container attach][az-container-attach] 명령을 실행하여 로컬 콘솔을 컨테이너의 출력 스트림에 연결합니다.

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

연결되면 브라우저를 몇 번 새로 고쳐 추가 출력을 생성합니다. 완료되면 `Control+C`를 사용하여 콘솔을 분리합니다. 다음과 비슷한 결과가 나타나야 합니다.

```output
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>리소스 정리

컨테이너 작업을 완료했으면 [az container delete][az-container-delete] 명령을 사용하여 제거합니다.

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

컨테이너가 삭제되었는지 확인하려면 [az container list](/cli/azure/container#az_container_list) 명령을 실행합니다.

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** 컨테이너가 명령의 출력에 나타나지 않아야 합니다. 리소스 그룹에 다른 컨테이너가 없는 경우 표시되는 출력이 없습니다.

*myResourceGroup* 리소스 그룹 및 이 그룹에 포함된 모든 리소스 작업이 완료되었으면 [az group delete][az-group-delete] 명령을 사용하여 해당 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 공용 Microsoft 이미지를 사용하여 Azure 컨테이너 인스턴스를 만들었습니다. 컨테이너 이미지를 빌드하고 프라이빗 Azure 컨테이너 레지스트리에서 배포하려면 Azure Container Instances 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)

Azure에서 오케스트레이션 시스템의 컨테이너 실행 옵션을 사용하려면 [AKS(Azure Kubernetes Service)][container-service] 빠른 시작을 참조하세요.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-container-list]: /cli/azure/container#az_container_list
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
