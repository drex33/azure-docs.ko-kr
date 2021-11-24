---
title: 빠른 시작 - 컨테이너 인스턴스에 Docker 컨테이너 배포 - Docker CLI
description: 이 빠른 시작에서는 Docker CLI를 사용하여 격리된 Azure 컨테이너 인스턴스에서 실행하는 컨테이너화된 웹앱을 신속하게 배포합니다.
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: 1218022a6810be44a98ab20eab3e542e5cb659eb
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133063853"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>빠른 시작: Docker CLI를 사용하여 Azure에서 컨테이너 인스턴스 배포

Azure Container Instances를 사용하여 Azure에서 서버리스 Docker 컨테이너를 간단하고 빠르게 실행합니다. 클라우드 네이티브 앱을 개발할 때 주문형 컨테이너 인스턴스에 배포하고 로컬 개발에서 클라우드 배포로 원활하게 전환하려고 합니다.

이 빠른 시작에서는 기본 Docker CLI 명령을 사용하여 Docker 컨테이너를 배포하고 Azure Container Instances에서 해당 애플리케이션을 사용할 수 있도록 합니다. 이 기능은 [Docker와 Azure 간의 통합](https://docs.docker.com/engine/context/aci-integration/)을 통해 사용하도록 설정됩니다. `docker run` 명령을 실행한 후 몇 초 내에 컨테이너에서 실행 중인 애플리케이션을 찾아볼 수 있습니다.

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨":::

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][azure-account]을 만듭니다.

이 빠른 시작에서는 [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) 또는 [macOS](https://desktop.docker.com/mac/edge/Docker.dmg)에서 사용할 수 있는 Docker Desktop 버전 2.3.0.5 이상이 필요합니다. 또는 [Linux용 Docker ACI 통합 CLI](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)를 설치합니다. 

> [!IMPORTANT]
> Azure Container Instances의 모든 기능이 지원되는 것은 아닙니다. [aci-integration-beta](https://github.com/docker/aci-integration-beta) GitHub 리포지토리에서 문제를 만들어 Docker Azure 통합에 대한 피드백을 제공합니다.

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="create-a-container"></a>컨테이너 만들기

Docker 컨텍스트를 만들면 Azure에서 컨테이너를 만들 수 있습니다. 이 빠른 시작에서는 공용 `mcr.microsoft.com/azuredocs/aci-helloworld` 이미지를 사용합니다. 이 이미지는 고정 HTML 페이지를 제공하는 Node.js로 작성된 작은 웹앱을 패키징합니다.

먼저 ACI 컨텍스트를 변경합니다. 모든 후속 Docker 명령은 이 컨텍스트에서 실행됩니다.

```
docker context use myacicontext
```

다음 `docker run` 명령을 실행하여 인터넷에 노출된 포트 80을 사용하여 Azure container instance를 만듭니다.

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

성공적인 배포를 위한 샘플 출력:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

`docker ps`을(를) 실행하여 공용 IP 주소를 포함한 실행 중인 컨테이너에 대한 세부 정보를 가져옵니다.

```
docker ps
```


샘플 출력은 공용 IP 주소를 보여 줍니다(이 경우 *52.230.225.232*).

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 이제 브라우저에서 해당 IP 주소로 이동합니다. 다음과 비슷한 웹 페이지가 표시됩니다. Docker 컨테이너에서 실행되는 애플리케이션이 Azure에 성공적으로 배포되었습니다.

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨":::

## <a name="pull-the-container-logs"></a>컨테이너 로그 끌어오기

컨테이너 또는 컨테이너가 실행되는 애플리케이션의 문제를 해결해야 하거나 해당 문제에 대한 출력만 보려는 경우 컨테이너 인스턴스의 로그를 확인하면서 시작합니다.

예를 들어, ACI 컨텍스트에서 *hungry-kirch* 컨테이너의 로그를 보려면 `docker logs` 명령을 실행합니다.

```azurecli-interactive
docker logs hungry-kirch
```

출력은 컨테이너의 로그를 표시하며, 브라우저에서 애플리케이션을 볼 때 생성된 HTTP GET 요청을 표시해야 합니다.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>리소스 정리

컨테이너 작업을 완료하면 `docker rm`을(를) 실행하여 제거합니다. 이 명령은 Azure Container Instance를 중지하고 삭제합니다.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Docker와 Azure 간의 통합을 사용하여 공용 이미지에서 Azure Container Instance를 만들었습니다. [Docker 설명서](https://docs.docker.com/engine/context/aci-integration/)의 통합 시나리오에 대해 자세히 알아보세요. 

[Visual Studio Code용 Docker 확장](https://aka.ms/VSCodeDocker)은 컨테이너, 이미지 및 컨텍스트를 개발, 실행 및 관리하는 통합 환경에 사용할 수도 있습니다.

Azure 도구를 사용하여 컨테이너 인스턴스를 만들고 관리하려면 [Azure CLI](container-instances-quickstart.md), [Azure PowerShell](container-instances-quickstart-powershell.md), [Azure Portal](container-instances-quickstart-portal.md) 및 [Azure Resource Manager 템플릿](container-instances-quickstart-template.md)을 사용하여 다른 빠른 시작을 참조하세요.

Docker Compose를 사용하여 다중 컨테이너 애플리케이션을 로컬로 정의하고 실행한 다음, Azure Container Instances로 전환하려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Docker Compose 자습서](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
