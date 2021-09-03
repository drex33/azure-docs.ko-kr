---
title: 프라이빗 컨테이너 레지스트리의 퍼블릭 콘텐츠 관리
description: Docker Hub 및 기타 퍼블릭 콘텐츠에서 퍼블릭 이미지에 대한 종속성을 관리하기 위한 Azure Container Registry의 사례 및 워크플로
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 06/17/2021
ms.openlocfilehash: 806cb6b49824db65744bc653c6c467c7a816a21e
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112983701"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Azure Container Registry를 사용하여 퍼블릭 콘텐츠 관리

이 문서에서는 Docker Hub의 컨테이너 이미지와 같은 퍼블릭 콘텐츠의 복사본을 유지 관리하기 위해 [Azure Container Registry](container-registry-intro.md)와 같은 로컬 레지스트리를 사용하는 방법 및 워크플로를 대략적으로 설명합니다. 


## <a name="risks-with-public-content"></a>퍼블릭 콘텐츠를 사용할 때의 위험

사용자 환경에는 퍼블릭 컨테이너 이미지, [Helm 차트](https://helm.sh/), OPA([Open Policy Agent](https://www.openpolicyagent.org/)) 정책 또는 기타 아티팩트와 같은 퍼블릭 콘텐츠에 대한 종속성이 있을 수 있습니다. 예를 들어, Docker Hub 또는 다른 퍼블릭 레지스트리에서 직접 이미지를 끌어와 서비스 라우팅 또는 `docker build FROM alpine`에 대해 [nginx](https://hub.docker.com/_/nginx)를 실행할 수 있습니다. 

적절한 컨트롤이 없는 경우 퍼블릭 레지스트리 콘텐츠에 대한 종속성이 있으면 이미지 개발 및 배포 워크플로에 위험을 초래할 수 있습니다. 위험을 완화하려면 가능한 경우 퍼블릭 콘텐츠의 로컬 복사본을 유지합니다. 자세한 내용은 [Open Container Initiative 블로그](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/)를 참조하세요. 

## <a name="authenticate-with-docker-hub"></a>Docker Hub로 인증

첫 번째 단계로, 현재 빌드 또는 배포 워크플로의 일부로 Docker Hub에서 퍼블릭 이미지를 끌어오는 경우 익명 끌어오기 요청을 수행하는 대신 [Docker Hub 계정을 사용하여 인증](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests)하는 것이 좋습니다.

자주 익명 끌어오기 요청을 수행하는 경우 `ERROR: toomanyrequests: Too Many Requests.` 또는 `You have reached your pull rate limit.`와 비슷한 Docker 오류가 표시될 수 있습니다. 이러한 오류를 방지하려면 Docker Hub에서 인증을 받으세요.

> [!NOTE]
> 2020년 11월 2일부터 Docker Free Plan 계정에서 각각 IP 주소 및 Docker ID에 의해 Docker Hub에 대한 익명 및 인증된 요청에 [다운로드 속도 제한이 적용](https://docs.docker.com/docker-hub/download-rate-limit)됩니다. 
>
> 끌어오기 요청 수를 예측하는 경우 클라우드 공급자 서비스를 사용하거나 회사 NAT 뒤에서 작업하는 경우 여러 사용자가 집계의 Docker Hub에 IP 주소 하위 세트로 표시됩니다. Docker Hub에 대한 요청에 Docker 유료 계정 인증을 추가하면 요금 제한으로 인한 잠재적인 서비스 중단이 발생하지 않습니다.
>
> 자세한 내용은 [Docker 가격 책정 및 구독](https://www.docker.com/pricing)과 [Docker 서비스 약관](https://www.docker.com/legal/docker-terms-service)을 참조하세요.

### <a name="docker-hub-access-token"></a>Docker Hub 액세스 토큰

Docker Hub는 Docker Hub에서 인증을 받을 때 Docker 암호의 대안으로 [개인 액세스 토큰](https://docs.docker.com/docker-hub/access-tokens/)을 사용하도록 지원합니다. 토큰은 Docker Hub에서 이미지를 끌어오는 자동화된 서비스에 권장됩니다. 다른 사용자 또는 서비스를 위해 여러 토큰을 생성하고 더 이상 필요하지 않은 경우 토큰을 해지할 수 있습니다.

토큰을 사용하여 `docker login`으로 인증을 받으려면 명령줄에서 암호를 생략합니다. 암호를 입력하라는 메시지가 표시되면 토큰을 대신 입력합니다. Docker Hub 계정에 대해 2단계 인증을 사용하도록 설정한 경우 Docker CLI에서 로그인할 때 개인용 액세스 토큰을 사용해야 합니다.

### <a name="authenticate-from-azure-services"></a>Azure 서비스에서 인증

App Service 및 Azure Container Instances를 비롯한 몇 가지 Azure 서비스는 컨테이너 배포를 위해 Docker Hub와 같은 퍼블릭 레지스트리에서 이미지를 끌어올 수 있도록 지원합니다. Docker Hub에서 이미지를 배포해야 하는 경우 Docker Hub 계정을 사용하여 인증을 받도록 설정을 구성하는 것이 좋습니다. 예:

**App Service**

* **이미지 원본**: Docker Hub
* **리포지토리 액세스**: 프라이빗
* **로그인**: \<Docker Hub username>
* **암호**: \<Docker Hub token>

자세한 내용은 [App Service에서 Docker Hub 인증 끌어오기](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html)를 참조하세요.

**Azure Container Instances**

* **이미지 원본**: Docker Hub 또는 기타 레지스트리
* **이미지 형식**: 프라이빗
* **이미지 레지스트리 로그인 서버**: docker.io
* **이미지 레지스트리 사용자 이름**: \<Docker Hub username>
* **이미지 레지스트리 암호**: \<Docker Hub token>
* **이미지**: docker.io/\<repo name\>:\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Azure Container Registry에 이미지 가져오기
 
퍼블릭 이미지의 복사본을 관리하기 시작하려면 아직 없는 경우 Azure Container Registry를 만들 수 있습니다. [Azure CLI](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md) 또는 기타 도구를 사용하여 레지스트리를 만듭니다. 

권장되는 일회성 단계로, 기본 이미지 및 기타 퍼블릭 콘텐츠를 Azure Container Registry로 [가져옵니다](container-registry-import-images.md). Azure CLI의 [az acr import](/cli/azure/acr#az_acr_import) 명령은 Docker Hub, Microsoft Container Registry와 같은 퍼블릭 레지스트리와 다른 프라이빗 컨테이너 레지스트리에서 이미지를 가져오도록 지원합니다. 

`az acr import`를 사용하기 위해 로컬 Docker 설치가 필요하지 않습니다. Azure CLI를 로컬로 설치하여 또는 Azure Cloud Shell에서 직접 실행할 수 있습니다. 모든 OS 유형, 다중 아키텍처 이미지 또는 OCI 아티팩트(예: Helm 차트)의 이미지를 지원합니다.

예:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

조직의 요구 사항에 따라 전용 레지스트리 또는 공유 레지스트리의 리포지토리로 가져올 수 있습니다.

## <a name="update-image-references"></a>이미지 참조 업데이트

애플리케이션 이미지 개발자는 코드에서 로컬 콘텐츠를 참조하는 과정을 제어할 수 있어야 합니다.

* 프라이빗 레지스트리를 사용하도록 이미지 참조를 업데이트합니다. 예를 들어, Dockerfile의 `FROM baseimage:v1` 문을 `FROM myregistry.azurecr.io/mybaseimage:v1`로 업데이트합니다.
* 프라이빗 레지스트리를 사용하도록 자격 증명이나 인증 메커니즘을 구성합니다. 정확한 메커니즘은 레지스트리에 액세스하는 데 사용하는 도구와 사용자 액세스 관리 방법에 따라 달라집니다.
    * Kubernetes 클러스터나 Azure Kubernetes Service를 사용하여 레지스트리에 액세스하는 경우 [인증 시나리오](authenticate-kubernetes-options.md)를 참조하세요.
    * Azure Container Registry로 [인증하는 옵션](container-registry-authentication.md)에 관해 자세히 알아보세요.

## <a name="automate-application-image-updates"></a>애플리케이션 이미지 업데이트 자동화

이미지 가져오기를 확장한 후, 기본 이미지가 업데이트될 때 애플리케이션 이미지 빌드를 자동화하도록 [Azure Container Registry 작업](container-registry-tasks-overview.md)을 설정합니다. 자동화된 빌드 작업은 [기본 이미지 업데이트](container-registry-tasks-base-images.md)와 [소스 코드 업데이트](container-registry-tasks-overview.md#trigger-task-on-source-code-update)를 모두 추적할 수 있습니다.

자세한 예제는 [Azure Container Registry 작업을 사용하여 퍼블릭 콘텐츠를 사용하고 유지 관리하는 방법](tasks-consume-public-content.md)을 참조하세요. 

> [!NOTE]
> 하나의 미리 구성된 작업을 통해 종속된 기본 이미지를 참조하는 모든 애플리케이션 이미지를 자동으로 다시 빌드할 수 있습니다. 
 
## <a name="next-steps"></a>다음 단계
* Azure에서 컨테이너 이미지를 빌드, 실행, 푸시 및 패치하는 [ACR 작업](container-registry-tasks-overview.md)에 대해 자세히 알아봅니다.
* 환경에 기본 이미지를 업데이트하는 자동화된 제어 워크플로에 대해서는 [Azure Container Registry 작업을 사용하여 퍼블릭 콘텐츠를 사용하고 유지 관리하는 방법](tasks-consume-public-content.md)을 참조하세요. 
* 이미지 빌드 및 업데이트를 자동화하는 더 많은 예제는 [ACR 작업 자습서](container-registry-tutorial-quick-task.md)를 참조하세요.
