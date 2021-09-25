---
title: 레지스트리 모범 사례
description: 다음 모범 사례에 따라 Azure Container Registry를 효과적으로 사용하는 방법을 알아봅니다.
ms.topic: article
ms.date: 08/13/2021
ms.openlocfilehash: 1b713ac047b575c68cd8ed539187e3caac13a322
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626972"
---
# <a name="best-practices-for-azure-container-registry"></a>Azure Container Registry의 모범 사례

이러한 모범 사례를 따르면 Azure에 있는 프라이빗 레지스트리의 성능 및 비용 효과적인 사용을 최대화하여 컨테이너 이미지 및 기타 아티팩트를 저장하고 배포할 수 있습니다.

레지스트리 개념에 대한 배경 지식은 [레지스트리, 리포지토리 및 이미지 정보](container-registry-concepts.md)를 참조하세요. 레지스트리의 태그 및 버전 이미지과 관련된 전략에 대해서는 [컨테이너 이미지 태그 지정 및 버전 관리 권장 사항](container-registry-image-tag-version.md)을 참조하세요. 

## <a name="network-close-deployment"></a>네트워크가 가까운 배포

컨테이너를 배포하는 동일한 Azure 지역에 컨테이너 레지스트리를 만듭니다. 네트워크가 컨테이너 호스트와 가까운 지역에 레지스트리를 배치하면 대기 시간 및 비용을 낮추는 데 도움이 될 수 있습니다.

네트워크와 가까운 배포는 프라이빗 컨테이너 레지스트리를 사용하는 주요 이유 중 하나입니다. Docker 이미지에는 증분 배포를 허용하는 효율적인 [계층 구성](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/)이 있습니다. 그러나 새 노드는 지정된 이미지에 필요한 모든 계층을 끌어와야 합니다. 이 초기 `docker pull`은 수기가바이트까지 빠르게 추가할 수 있습니다. 프라이빗 레지스트리를 배포와 가깝게 유지하면 네트워크 대기 시간이 최소화됩니다.
또한 Azure가 포함하는 모든 퍼블릭 클라우드는 네트워크 송신 비용을 초래합니다. 데이터 센터 간에 이미지를 끌어오면 대기 시간 외에도 네트워크 송신 비용이 추가로 발생합니다.

## <a name="geo-replicate-multi-region-deployments"></a>다중 지역 배포 지리적 복제

여러 영역에 컨테이너를 배포하려는 경우 Azure Container Registry의 [지리적 복제](container-registry-geo-replication.md) 기능을 사용합니다. 로컬 데이터 센터에서 글로벌 고객에게 서비스를 제공하거나 개발 팀이 여러 다른 위치에 있더라도, 레지스트리를 지리적으로 복제하여 레지스트리 관리를 간소화하고 대기 시간을 최소화할 수 있습니다. 또한 이미지가 푸시되는 경우와 같이 특정 복제본의 이벤트를 알리도록 지역 [웹후크](container-registry-webhook.md)를 구성할 수 있습니다.

지역 복제는 [프리미엄](container-registry-skus.md) 레지스트리에서 사용할 수 있습니다. 지리적 복제 사용 방법을 알아보려면 총 3부로 구성된 자습서 시리즈 [Azure Container Registry의 지리적 복제](container-registry-tutorial-prepare-registry.md)를 참조하세요.

## <a name="maximize-pull-performance"></a>끌어오기 성능 최대화

이미지를 배포에 가깝게 배치하는 것 외에도 이미지 자체의 특성이 끌어오기 성능에 영향을 줄 수 있습니다.

* **이미지 크기** - 불필요한 [레이어](container-registry-concepts.md#manifest)를 제거하거나 레이어의 크기를 줄여 이미지 크기를 최소화합니다. 이미지 크기를 줄이는 한 가지 방법은 [다단계 Docker 빌드](https://docs.docker.com/develop/develop-images/multistage-build/) 접근 방식을 사용하여 필요한 런타임 구성 요소만 포함하는 것입니다. 

  또한 이미지에 더 가벼운 기본 OS 이미지가 포함될 수 있는지 확인합니다. 특정 기본 이미지를 캐시하는 Azure Container Instances와 같은 배포 환경을 사용하는 경우 이미지 레이어를 캐시된 이미지 중 하나로 교환할 수 있는지 확인합니다. 
* **레이어 수** - 사용되는 레이어 수의 균형을 맞춥니다. 너무 적은 경우 호스트에서 레이어 재사용 및 캐싱의 이점을 얻지 못합니다. 너무 많은 경우 배포 환경에서 풀링하고 압축을 푸는 데 더 많은 시간이 소요됩니다. 5 ~ 10개의 레이어가 최적입니다.

또한 성능 요구 사항을 충족하는 Azure Container Registry의 [서비스 계층](container-registry-skus.md)을 선택합니다. 프리미엄 계층은 대용량 배포 시 최대 대역폭과 최고 동시 읽기 및 쓰기 작업 속도를 제공합니다.

## <a name="repository-namespaces"></a>리포지토리 네임스페이스

리포지토리 네임스페이스를 사용하면 조직 내의 여러 그룹에서 단일 레지스트리를 공유할 수 있습니다. 레지스트리는 배포 및 팀 간에 공유할 수 있습니다. Azure Container Registry는 중첩된 네임스페이스를 지원하여 그룹 격리를 허용합니다. 그러나 레지스트리는 계층 구조가 아닌 독립적으로 모든 리포지토리를 관리합니다.

예를 들어 다음 컨테이너 이미지 태그를 고려해 보세요. `aspnetcore`처럼 기업 전체에서 사용되는 이미지는 루트 네임스페이스에 두고, 제품 및 마케팅 그룹에서 소유하는 컨테이너 이미지에는 각각 자체 네임스페이스를 사용합니다.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>전용 리소스 그룹

컨테이너 레지스트리는 여러 컨테이너 호스트에서 사용되는 리소스이므로 레지스트리가 자체 리소스 그룹에 상주해야 합니다.

[Azure Container Instances](../container-instances/container-instances-overview.md)와 같은 특정 호스트 유형으로 시험해 볼 수 있지만 완료되면 컨테이너 인스턴스를 삭제하려고 할 것입니다. 그러나 Azure Container Registry에 푸시한 이미지의 컬렉션은 유지하려고 할 수 있습니다. 레지스트리를 자체 리소스 그룹에 배치하여 컨테이너 인스턴스 리소스 그룹을 삭제할 때 레지스트리의 이미지의 컬렉션을 실수로 삭제의 위험을 최소화할 수 있습니다.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure Container Registry에서 인증할 때 두 가지 기본 시나리오, 즉 개별 인증과 서비스(또는 "헤드리스") 인증이 있습니다. 다음 표에서는 이러한 시나리오와 각 시나리오의 권장 인증 방법에 대해 간략하게 설명합니다.

| Type | 예제 시나리오 | 권장 방법 |
|---|---|---|
| 개별 ID | 개발자가 개발 컴퓨터로 이미지를 끌어오거나 개발 컴퓨터에서 이미지를 푸시함 | [az acr login](/cli/azure/acr#az_acr_login) |
| 헤드리스/서비스 ID | 사용자가 직접 참여하지 않은 파이프라인 빌드 및 배포 | [서비스 주체](container-registry-authentication.md#service-principal) |

이러한 Azure Container Registry 인증 시나리오에 대한 자세한 내용은 [Azure 컨테이너 레지스트리로 인증](container-registry-authentication.md)을 참조하세요.

Azure Container Registry는 조직의 보안 관행을 지원하여 다양한 ID에 의무 및 권한을 분배합니다. [역할 기반 액세스 제어](container-registry-roles.md)를 사용하여 다른 사용자, 서비스 주체 또는 서로 다른 레지스트리 작업을 수행하는 다른 ID에 적절한 사용 권한을 할당합니다. 예를 들어, 빌드 파이프라인에서 사용되는 서비스 주체에 푸시 권한을 할당하고 배포에 사용되는 다른 ID에 풀 권한을 할당합니다. 특정 리포지토리에 대한 세분화된 시간 제한 액세스를 위한 [토큰](container-registry-repository-scoped-permissions.md)을 만듭니다.

## <a name="manage-registry-size"></a>레지스트리 크기 관리      

일반적인 시나리오에 맞게 각 [컨테이너 레지스트리 서비스 계층][container-registry-skus]의 스토리지 제약 조건이 결정됩니다. **기본** 은 시작하는 고객, **표준** 은 대부분의 프로덕션 애플리케이션, **프리미엄** 은 하이퍼스케일 성능 및 [지역 복제][container-registry-geo-replication] 시나리오에 적합합니다. 레지스트리 수명 동안에 미사용 콘텐츠는 정기적으로 삭제하여 크기를 관리해야 합니다.

Azure CLI 명령 [az acr show-usage][az-acr-show-usage] 를 사용 하 여 레지스트리의 현재 저장소 및 기타 리소스 사용량을 표시 합니다.

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

샘플 출력:

```
NAME                        LIMIT         CURRENT VALUE    UNIT
--------------------------  ------------  ---------------  ------
Size                        536870912000  215629144        Bytes
Webhooks                    500           1                Count
Geo-replications            -1            3                Count
IPRules                     100           1                Count
VNetRules                   100           0                Count
PrivateEndpointConnections  10            0                Count
```

Azure Portal의 레지스트리 **개요** 에서 현재 저장소 사용을 찾을 수도 있습니다.

![Azure Portal의 레지스트리 사용량 정보][registry-overview-quotas]

> [!NOTE]
> 지역에서 [복제](container-registry-geo-replication.md) 된 레지스트리에서는 홈 지역에 대해 저장소 사용량이 표시 됩니다. 사용 된 총 레지스트리 저장소에 대 한 복제 수를 곱합니다.

### <a name="delete-image-data"></a>이미지 데이터 삭제

Azure Container Registry는 컨테이너 레지스트리에서 이미지 데이터를 삭제하는 여러 가지 방법을 지원합니다. 태그 또는 매니페스트 다이제스트를 기준으로 이미지를 삭제할 수도 있고 전체 리포지토리를 삭제할 수도 있습니다.

태그가 없는 이미지(“누락된” 이미지 또는 “분리된” 이미지라고도 함)를 포함한 이미지 데이터를 레지스트리에서 삭제하는 방법에 대한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조하세요. 태그 없는 매니페스트에 대 한 [보존 정책을](container-registry-retention-policy.md) 설정할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Container Registry는 다양한 기능을 제공하는 SKU라고도 하는 여러 계층에서 사용될 수 있습니다. 사용 가능한 서비스 계층에 대한 자세한 내용은 [Azure 컨테이너 레지스트리 서비스 계층](container-registry-skus.md)을 참조하세요.

컨테이너 레지스트리의 보안 상태를 개선하기 위한 권장 사항은 [Azure Container Registry에 대한 Azure 보안 기준](security-baseline.md)을 참조하세요.

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md