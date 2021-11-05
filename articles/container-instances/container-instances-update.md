---
title: 컨테이너 그룹 업데이트
description: Azure Containers Instances 컨테이너 그룹에서 실행되는 컨테이너를 업데이트하는 방법을 알아봅니다.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: f2ec8ed3641fd9e692c89c6fdb29a7bea268b023
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853342"
---
# <a name="update-containers-in-azure-container-instances"></a>Azure Container Instances에서 컨테이너 업데이트

컨테이너 인스턴스가 정상적으로 작동하는 동안 [컨테이너 그룹](./container-instances-container-groups.md)의 실행 중인 컨테이너를 업데이트해야 할 수 있습니다. 예를 들어 이미지 버전, DNS 이름, 환경 변수와 같은 속성을 업데이트하거나 애플리케이션이 충돌한 컨테이너의 속성을 새로 고칠 수 있습니다.

하나 이상의 속성이 수정된 기존 그룹을 다시 배포하여 실행 중인 컨테이너 그룹의 컨테이너를 업데이트합니다. 컨테이너 그룹을 업데이트하면 그룹에서 실행 중인 모든 컨테이너가 현재 위치, 일반적으로 동일한 기본 컨테이너 호스트에서 다시 시작됩니다.

> [!NOTE]
> 종료되거나 삭제된 컨테이너 그룹은 업데이트할 수 없습니다. 컨테이너 그룹이 종료(성공 또는 실패 상태임)되거나 삭제된 경우 그룹을 새로 배포해야 합니다. 기타 [제한 사항](#limitations)을 참조하세요.

## <a name="update-a-container-group"></a>컨테이너 그룹 업데이트

기존 컨테이너 그룹을 업데이트하려면 다음을 수행합니다.

* create 명령을 실행하거나 Azure Portal을 사용하고 기존 그룹의 이름을 지정합니다. 
* 다시 배포할 때 업데이트를 지원하는 그룹의 속성을 하나 이상 수정하거나 추가합니다. 특정 속성은 [업데이트를 지원하지 않습니다](#properties-that-require-container-delete).
* 이전에 제공한 값을 사용하여 다른 속성을 설정합니다. 속성의 값을 설정하지 않으면 기본값으로 돌아갑니다.

> [!TIP]
> [YAML 파일](./container-instances-container-groups.md#deployment)은 컨테이너 그룹의 배포 구성을 유지 관리하는 데 도움이 되며, 업데이트된 그룹을 배포하기 위한 시작점을 제공합니다. 다른 방법을 사용하여 그룹을 만든 경우 [az container export][az-container-export]를 사용하여 YAML로 구성을 내보낼 수 있습니다. 

### <a name="example"></a>예제

다음 Azure CLI 예제에서는 컨테이너 그룹을 새 DNS 이름 레이블로 업데이트합니다. 그룹의 DNS 이름 레이블 속성을 업데이트할 수 있으므로 컨테이너 그룹이 다시 배포되고 해당 컨테이너가 다시 시작됩니다.

*myapplication-staging* DNS 이름 레이블을 사용한 초기 배포는 다음과 같습니다.

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

새 DNS 이름 레이블 *myapplication* 으로 컨테이너 그룹을 업데이트하고, 앞에서 사용한 값으로 나머지 속성을 설정합니다.

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>업데이트 혜택

기존 컨테이너 그룹을 업데이트하는 주요 이점은 배포 속도가 더 빠르다는 것입니다. 기존 컨테이너 그룹을 다시 배포하면 이전 배포에서 캐시된 계층에서 해당 컨테이너 이미지 계층을 가져옵니다. 새 배포를 통해 수행되는 것처럼 레지스트리에서 모든 이미지 계층을 새로 가져오는 대신 수정된 계층(있는 경우)만 가져옵니다.

Windows Server Core와 같이 더 큰 컨테이너 이미지를 기반으로 하는 애플리케이션에서 삭제하여 새로 배포하는 대신 업데이트하는 경우 배포 속도가 크게 향상될 수 있습니다.

## <a name="limitations"></a>제한 사항

* 컨테이너 그룹의 일부 속성만 업데이트를 지원합니다. 컨테이너 그룹의 일부 속성을 변경하려면 먼저 해당 그룹을 삭제한 다음, 다시 배포해야 합니다. [컨테이너를 삭제해야 하는 속성](#properties-that-require-container-delete)을 참조하세요.
* 컨테이너 그룹을 업데이트하면 컨테이너 그룹의 모든 컨테이너가 다시 시작됩니다. 다중 컨테이너 그룹에 속한 특정 컨테이너에 대한 업데이트 또는 적절한 재시작은 수행할 수 없습니다.
* 컨테이너 그룹의 IP 주소는 일반적으로 업데이트 간에 유지되지만 동일하게 유지된다고 보장하지는 않습니다. 컨테이너 그룹이 동일한 기본 호스트에 배포되는 한 컨테이너 그룹에서 해당 IP 주소를 유지합니다. 드문 경우지만 다른 호스트로 다시 배포되도록 하는 몇 가지 Azure 내부 이벤트가 있습니다. 이 문제를 완화하려면 컨테이너 인스턴스에 DNS 이름 레이블을 사용하는 것이 좋습니다.
* 종료되거나 삭제된 컨테이너 그룹은 업데이트할 수 없습니다. 컨테이너 그룹이 중지(‘종료됨’ 상태임)되거나 삭제되면 그룹이 새로 배포됩니다.

## <a name="properties-that-require-container-delete"></a>컨테이너를 삭제해야 하는 속성

일부 컨테이너 그룹 속성은 업데이트할 수 없습니다. 예를 들어 컨테이너의 다시 시작 정책을 변경하려면 먼저 컨테이너 그룹을 삭제한 다음, 다시 만들어야 합니다.

이러한 속성을 변경하려면 먼저 컨테이너 그룹을 삭제한 후에 다시 배포해야 합니다.

* OS 유형
* CPU, 메모리 또는 GPU 리소스
* 정책 다시 시작
* 네트워크 프로필
* 가용성 영역

컨테이너 그룹을 삭제하고 다시 만들면 해당 그룹은 "다시 배포"되지 않고 새로 만들어집니다. 모든 이미지 계층은 이전 배포에서 캐시된 계층이 아니라 레지스트리에서 새로 가져옵니다. 컨테이너의 IP 주소도 다른 기본 호스트에 배포되어 변경될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 **컨테이너 그룹** 이 여러 번 언급되었습니다. Azure Container Instances의 모든 컨테이너는 컨테이너 그룹에 배포되고, 이 컨테이너 그룹에는 둘 이상의 컨테이너가 포함될 수 있습니다.

[Azure Container Instances의 컨테이너 그룹](./container-instances-container-groups.md)

[다중 컨테이너 그룹 배포](container-instances-multi-container-group.md)

[Azure Container Instances에서 수동으로 컨테이너 중지 또는 시작](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export
