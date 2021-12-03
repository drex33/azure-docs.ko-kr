---
title: 자습서 - 컨테이너 인스턴스에 컨테이너 애플리케이션 배포
description: Azure Container Instances 자습서 3/3부 - Azure Container Instances에 컨테이너 애플리케이션 배포
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: a4fc54d8280459b1cadfe3726539abe38d6d6e75
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133519487"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>자습서: Azure Container Instances에 컨테이너 애플리케이션 배포

3부작 시리즈의 마지막 자습서입니다. 시리즈의 앞부분에서는 [컨테이너 이미지를 만들어](container-instances-tutorial-prepare-app.md)[Azure Container Registry에 푸시했습니다](container-instances-tutorial-prepare-acr.md). 이 문서에서는 Azure Container Instances에 컨테이너를 배포하여 이 시리즈를 완료합니다.

이 자습서에서는 다음과 같은 작업을 수행했습니다.

> [!div class="checklist"]
> * Azure Container Registry에서 Azure Container Instances에 컨테이너 배포
> * 브라우저에서 실행 중인 애플리케이션 보기
> * 컨테이너의 로그 표시

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI를 사용하여 컨테이너 배포

이 섹션에서는 Azure CLI를 사용하여 [첫 번째 자습서](container-instances-tutorial-prepare-app.md)에서 작성되고 [두 번째 자습서](container-instances-tutorial-prepare-acr.md)에서 Azure Container Registry에 푸시된 이미지를 배포합니다. 계속 진행하려면 먼저 이러한 자습서를 완료해야 합니다.

### <a name="get-registry-credentials"></a>레지스트리 자격 증명 가져오기

[두 번째 자습서](container-instances-tutorial-prepare-acr.md)에서 만든 이미지처럼 프라이빗 Azure 컨테이너 레지스트리에 호스트되는 이미지를 배포하는 경우 레지스트리에 액세스하기 위한 자격 증명을 제공해야 합니다. 

많은 시나리오에 대한 모범 사례는 레지스트리에 *끌어오기* 사용 권한이 있는 Azure Active Directory 서비스 주체를 만들고 구성하는 것입니다. 필요한 권한이 있는 서비스 주체를 생성하는 샘플 스크립트는 [Azure Container Instances의 Azure Container Registry를 사용하여 인증](../container-registry/container-registry-auth-aci.md)을 참조하세요. *서비스 주체 ID* 및 *서비스 주체 암호* 를 적어둡니다. 컨테이너를 배포할 때 이러한 자격 증명을 사용하여 레지스트리에 액세스합니다.

또한 컨테이너 레지스트리 로그인 서버의 전체 이름이 필요합니다(`<acrName>`을 레지스트리 이름으로 바꾸기).

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>컨테이너 배포

이제 [az container create][az-container-create] 명령을 사용하여 컨테이너를 배포합니다. `<acrLoginServer>`를 이전 명령에서 얻은 값으로 바꿉니다. `<service-principal-ID>` 및 `<service-principal-password>`를 레지스트리에 액세스하기 위해 만든 서비스 주체 ID 및 암호로 바꿉니다. `<aciDnsLabel>`을 원하는 DNS 이름으로 바꿉니다.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

몇 초 정도 지나면 Azure에서 초기 응답이 수신됩니다. `--dns-name-label` 값은 컨테이너 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 명령을 실행한 결과 **DNS 이름 레이블** 오류 메시지가 표시되는 경우에는 이전 명령의 값을 수정합니다.

### <a name="verify-deployment-progress"></a>배포 진행률 확인

배포의 상태를 확인하려면 [az container show][az-container-show]를 사용합니다.

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

상태가 *보류 중* 에서 *실행 중* 으로 변경될 때까지 [az container show][az-container-show] 명령을 1분 미만으로 반복합니다. 컨테이너가 *실행 중* 상태가 되면 다음 단계를 진행합니다.

## <a name="view-the-application-and-container-logs"></a>애플리케이션 및 컨테이너 로그 보기

배포에 성공하면 [az container show][az-container-show] 명령을 사용하여 컨테이너의 FQDN(정규화된 도메인 이름)을 표시합니다.

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

예를 들면 다음과 같습니다.
```output
"aci-demo.eastus.azurecontainer.io"
```

실행 중인 애플리케이션을 보려면 원하는 브라우저에서 표시된 DNS 이름으로 이동합니다.

![브라우저의 Hello World 앱][aci-app-browser]

또한 컨테이너의 로그 출력을 볼 수 있습니다.

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

예제 출력:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 이 자습서 시리즈에서 만든 리소스가 필요하지 않은 경우 [az group delete][az-group-delete] 명령을 실행하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. 이 명령은 실행 중인 컨테이너뿐만 아니라 생성한 컨테이너 레지스트리 및 모든 관련 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Instances에 컨테이너를 배포하는 프로세스를 완료했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure Container Registry의 컨테이너 배포
> * 브라우저에서 애플리케이션 보기
> * 컨테이너 로그 보기

기본 사항을 알아보았으니, 컨테이너 그룹의 작동 방식 등 Azure Container Instances에 대해 자세히 알아보겠습니다.

> [!div class="nextstepaction"]
> [Azure Container Instances의 컨테이너 그룹](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
