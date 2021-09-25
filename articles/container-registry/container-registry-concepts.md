---
title: 레지스트리, 리포지토리, 이미지, 아티팩트 정보
description: Azure Container Registry, 리포지토리, 컨테이너 이미지, 기타 아티팩트의 주요 개념을 소개합니다.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: add8c20de07a2d520095f257dac0356d1c21af57
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596147"
---
# <a name="about-registries-repositories-and-artifacts"></a>레지스트리, 리포지토리, 아티팩트 정보

이 문서에서는 컨테이너 레지스트리, 리포지토리, 컨테이너 이미지와 관련 아티팩트의 주요 개념을 소개합니다. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="레지스트리, 리포지토리, 아티팩트":::

## <a name="registry"></a>레지스트리

컨테이너 *레지스트리* 는 컨테이너 이미지와 관련 아티팩트를 저장하고 배포하는 서비스입니다. Docker Hub는 Docker 컨테이너 이미지의 일반 카탈로그 역할을 하는 공용 컨테이너 레지스트리의 예입니다. Azure Container Registry는 사용자에게 컨테이너 콘텐츠에 대한 직접 제어 기능, 통합 인증, 네트워크와 가까운 배포를 위한 글로벌 배포 및 안정성을 지원하는 [지역 복제](container-registry-geo-replication.md), [Private Link로 가상 네트워크 구성](container-registry-private-link.md), [태그 잠금](container-registry-image-lock.md), 기타 향상된 기능을 제공합니다. 

Docker 호환 컨테이너 이미지 외에도 Azure Container Registry는 Helm 차트 및 OCI(Open Container Initiative) 이미지 형식을 비롯한 다양한 [콘텐츠 아티팩트](container-registry-image-formats.md)를 지원합니다.

## <a name="repository"></a>리포지토리

*리포지토리* 는 이름이 같지만 태그가 다른 레지스트리에 있는 컨테이너 이미지 또는 기타 아티팩트의 컬렉션입니다. 예를 들어, 다음 3개의 이미지가 `acr-helloworld` 리포지토리에 있습니다.

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

리포지토리 이름에 [네임스페이스](container-registry-best-practices.md#repository-namespaces)가 포함될 수도 있습니다. 네임스페이스를 통해 슬래시(/)로 구분된 이름을 사용하여 조직에서 관련 리포지토리 및 아티팩트 소유권을 식별할 수 있습니다. 그러나 레지스트리는 계층 구조로서가 아닌 독립적으로 모든 리포지토리를 관리합니다. 예를 들면 다음과 같습니다.

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

리포지토리 이름에는 소문자 영숫자, 마침표, 대시, 밑줄, 슬래시만 포함될 수 있습니다. 

전체 리포지토리 이름 지정 규칙은 [공개 컨테이너 이니셔티브 배포 사양](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)을 참조하세요.

## <a name="artifact"></a>아티팩트

레지스트리 내의 컨테이너 이미지 또는 기타 아티팩트는 하나 이상의 태그와 연결되고, 하나 이상의 레이어를 가지며, 매니페스트로 식별됩니다. 이러한 구성 요소가 서로 관련되는 방식을 이해하면 레지스트리를 효과적으로 관리하는 데 도움이 될 수 있습니다.

### <a name="tag"></a>태그

이미지 또는 기타 아티팩트의 *태그* 는 해당 버전을 지정합니다. 리포지토리 내의 단일 아티팩트에 하나 이상의 태그를 할당할 수 있으며 "태그를 지정하지 않을" 수도 있습니다. 즉, 이미지의 데이터(및 해당 레이어)는 레지스트리에 남아 있는 상태로, 이미지에서 모든 태그를 삭제할 수 있습니다.

리포지토리(또는 리포지토리 및 네임스페이스)와 태그는 이미지의 이름을 정의합니다. 밀어넣기 및 끌어오기 작업에서 해당 이름을 지정하여 이미지를 밀어넣고 끌어올 수 있습니다. 사용자가 Docker 명령에 태그를 제공하지 않는 경우 `latest` 태그가 기본적으로 사용됩니다.

컨테이너 이미지에 태그를 지정하는 방법은 시나리오에 따라 개발하거나 배포합니다. 예를 들어 기본 이미지를 유지하고 이미지 배포를 위한 고유한 태그를 유지할 수 있도록 안정적인 태그가 권장됩니다. 자세한 내용은 [컨테이너 이미지 태그 지정 및 버전 관리에 대한 권장 사항](container-registry-image-tag-version.md)을 참조하세요.

태그 명명 규칙에 대해서는 [Docker 문서](https://docs.docker.com/engine/reference/commandline/tag/)를 참조하세요.

### <a name="layer"></a>계층

컨테이너 이미지와 아티팩트는 하나 이상의 *레이어* 로 구성됩니다. 아티팩트 형식이 다르면 레이어가 다르게 정의됩니다. 예를 들어 Docker 컨테이너 이미지에서 각 레이어는 이미지를 정의하는 Dockerfile의 줄에 해당합니다.

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="컨테이너 이미지의 레이어":::

레지스트리의 아티팩트는 공통 레이어를 공유하므로 스토리지 효율성이 높아집니다. 예를 들어, 여러 리포지토리에 있는 여러 이미지가 공통 ASP.NET Core 기본 레이어를 공유할 수 있지만 레지스트리에는 해당 레이어의 복사본 하나만 저장됩니다. 레이어 공유는 여러 개의 아티팩트가 공통 레이어를 공유하는 방식으로 노드에 대한 레이어 분산을 최적화합니다. 노드에 이미 있는 이미지가 맨 아래에 ASP.NET Core 레이어를 포함하는 경우 이후에 같은 레이어를 참조하는 다른 이미지를 풀할 경우 해당 레이어가 노드로 전송되지 않습니다. 대신, 노드에 이미 존재하는 계층을 참조합니다.

보안 격리와 잠재적 레이어 조작으로부터의 보호를 제공하기 위해 레이어는 레지스트리를 통해 공유되지 않습니다.

### <a name="manifest"></a>file:///

컨테이너 레지스트리로 푸시한 각 컨테이너 이미지 또는 아티팩트는 *매니페스트* 와 연결됩니다. 콘텐츠를 푸시할 때 레지스트리에서 생성된 매니페스트는 아티팩트를 고유하게 식별하고 해당 레이어를 지정합니다.

Linux `hello-world` 이미지에 대한 기본 매니페스트는 다음과 유사합니다.

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
      "mediaType": "application/vnd.docker.container.image.v1+json",
      "size": 1510,
      "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
    },
    "layers": [
      {
        "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
        "size": 977,
        "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
      }
    ]
  }
  ```

Azure CLI 명령 [az acr repository show-manifests][az-acr-repository-show-manifests]를 사용하여 리포지토리에 대한 매니페스트를 나열할 수 있습니다.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

예를 들어, "acr-helloworld" 리포지토리에 대한 매니페스트를 나열하려면 다음과 같이 입력합니다.

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>매니페스트 다이제스트

매니페스트는 고유한 SHA-256 해시 또는 *매니페스트 다이제스트* 에 의해 식별됩니다. 태그 지정 여부에 관계없이 각 이미지 또는 아티팩트는 해당 다이제스트로 식별됩니다. 다이제스트 값은 아티팩트의 레이어 데이터가 다른 아티팩트의 레이어 데이터와 동일하더라도 고유합니다. 이 메커니즘에 따르면 동일하게 태그가 지정된 이미지를 레지스트리에 반복적으로 밀어넣을 수 있습니다. 예를 들어, 각 이미지는 고유한 다이제스트로 식별되므로 `myimage:latest`(을)를 레지스트리에 오류 없이 반복적으로 밀어넣을 수 있습니다.

풀 작업에서 해당 다이제스트를 지정하여 레지스트리에서 아티팩트를 풀할 수 있습니다. 일부 시스템은 다이제스트별로 풀하도록 구성이 가능합니다. 동일하게 태그가 지정된 이미지를 나중에 레지스트리로 푸시하더라도 해당 이미지 버전 풀이 보장되기 때문입니다.

> [!IMPORTANT]
> 동일한 태그가 지정된 아티팩트를 수정한 후 반복적으로 푸시할 경우 태그가 지정되지 않은 아티팩트는 별도로 분리되어 생성될 수 있지만 이 아티팩트는 여전히 레지스트리에서 공간을 차지합니다. 태그가 지정되지 않은 이미지는 태그를 기준으로 이미지를 나열하거나 표시할 경우 Azure CLI 또는 Azure Portal에 표시되지 않습니다. 그러나 해당 계층은 여전히 존재하며 레지스트리에서 공간을 차지합니다. 매니페스트가 특정 레이어를 가리키는 유일한 항목인 경우 또는 마지막 항목인 경우 태그가 지정되지 않은 이미지를 삭제하면 레지스트리 공간이 해제됩니다. 태그가 지정되지 않은 이미지에 사용되는 공간을 확보하는 방법에 대한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조하세요.

## <a name="addressing-an-artifact"></a>아티팩트 주소 지정

Docker 또는 다른 클라이언트 도구를 사용하여 푸시 및 풀 작업에 대한 레지스트리 아티팩트의 주소를 지정하려면 정규화된 레지스트리 이름, 리포지토리 이름(해당 하는 경우 네임스페이스 경로 포함), 아티팩트 태그 또는 매니페스트 다이제스트를 조합합니다. 이러한 용어에 대한 설명은 이전 섹션을 참조하세요.

  **태그별 주소**: `[loginServerUrl]/[repository][:tag]`
    
  **다이제스트별 주소**: `[loginServerUrl]/[repository@sha256][:digest]`  

Docker 또는 다른 클라이언트 도구를 사용하여 아티팩트를 Azure Container Registry로 풀하거나 푸시하는 경우 *로그인 서버* 이름이라고도 하는 레지스트리의 정규화된 URL을 사용합니다. Azure Cloud에서 Azure Container Registry의 정규화된 URL은 `myregistry.azurecr.io`(모두 소문자) 형식으로 되어 있습니다.

> [!NOTE]
> * 레지스트리 로그인 서버 URL(예: `myregistry.azurecr.io:443`)에서는 포트 번호를 지정할 수 없습니다. 
> * 사용자가 명령에 태그를 제공하지 않는 경우 `latest` 태그가 기본적으로 사용됩니다.  

   
### <a name="push-by-tag"></a>태그로 푸시

예: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>태그별로 풀

예제: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>매니페스트 다이제스트별로 풀


예제:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>다음 단계

Azure Container Registry의 [레지스트리 스토리지](container-registry-storage.md) 및 [지원되는 콘텐츠 형식](container-registry-image-formats.md)에 대해 알아봅니다.

Azure Container Registry에서 이미지를 [푸시하고 풀하는](container-registry-get-started-docker-cli.md) 방법을 참조하세요.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
