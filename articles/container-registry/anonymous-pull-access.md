---
title: 익명 끌어오기 액세스 사용
description: 필요에 따라 익명 끌어오기 액세스를 사용하도록 설정하여 Azure 컨테이너 레지스트리의 콘텐츠를 공개적으로 사용할 수 있도록 합니다.
ms.topic: how-to
ms.date: 09/17/2021
ms.custom: ''
ms.openlocfilehash: e82bb43ee8865642ae7939a94291e0c9f5432198
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161973"
---
# <a name="make-your-container-registry-content-publicly-available"></a>컨테이너 레지스트리 콘텐츠를 공개적으로 사용할 수 있도록 만들기

익명(인증되지 않은) 끌어오기 액세스를 위해 Azure 컨테이너 레지스트리를 설정하는 것은 인터넷 액세스 권한이 있는 모든 사용자가 레지스트리에서 콘텐츠를 끌어올 수 있는 기능을 허용하는 선택적 기능입니다.

익명 끌어오기 액세스는 표준 및 Premium 서비스 계층에서 사용할 수 있는 미리 보기 [기능입니다.](container-registry-skus.md) 익명 끌어오기 액세스를 구성하려면 Azure CLI(버전 2.21.0 이상)를 사용하여 레지스트리를 업데이트합니다. 설치하거나 업그레이드하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="about-anonymous-pull-access"></a>익명 끌어오기 액세스 정보

기본적으로 Azure Container Registry에서 콘텐츠를 끌어오거나 푸시하는 액세스는 [인증된](container-registry-authentication.md) 사용자만 사용할 수 있습니다. 익명(인증되지 않은) 끌어오기 액세스를 사용하도록 설정하면 모든 레지스트리 콘텐츠를 읽기(끌어오기) 작업에 공개적으로 사용할 수 있습니다. 익명 끌어오기 액세스는 공용 컨테이너 이미지 배포와 같이 사용자 인증이 필요하지 않은 시나리오에서 사용할 수 있습니다.

- 기존 레지스트리의 속성을 업데이트하여 익명 끌어오기 액세스를 사용하도록 설정합니다.
- 익명 끌어오기 액세스를 사용하도록 설정한 후에는 언제든지 해당 액세스를 사용하지 않도록 설정할 수 있습니다.
- 인증되지 않은 클라이언트에서는 데이터 평면 작업만 사용할 수 있습니다.
- 레지스트리는 높은 비율의 인증되지 않은 요청을 제한할 수 있습니다.
- 이전에 레지스트리에 인증한 경우 익명 끌어오기 작업을 시도하기 전에 자격 증명을 지워야 합니다.

> [!WARNING]
> 익명 풀 액세스는 현재 레지스트리의 모든 리포지토리에 적용됩니다. 리포지토리 범위 토큰을 사용하여 [리포지토리](container-registry-repository-scoped-permissions.md)액세스를 관리하는 경우 모든 사용자가 익명 끌어오기를 사용하도록 설정된 레지스트리의 해당 리포지토리에서 끌어올 수 있습니다. 익명 풀 액세스가 사용하도록 설정된 경우 토큰을 삭제하는 것이 좋습니다.

## <a name="configure-anonymous-pull-access"></a>익명 끌어오기 액세스 구성 

### <a name="enable-anonymous-pull-access"></a>익명 끌어오기 액세스 사용
[az acr update](/cli/azure/acr#az_acr_update) 명령을 사용하여 레지스트리를 업데이트하고 `--anonymous-pull-enabled` 매개 변수를 전달합니다. 기본적으로 익명 끌어오기는 레지스트리에서 사용하지 않도록 설정됩니다.
          
```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

> [!IMPORTANT]
> 이전에 Docker 자격 증명을 사용하여 레지스트리에 인증한 경우 `docker logout` 를 실행하여 익명 끌어오기 작업을 시도하기 전에 기존 자격 증명을 지웁니다. 그렇지 않으면 "끌어오기 액세스 거부됨"과 유사한 오류 메시지가 표시될 수 있습니다.

### <a name="disable-anonymous-pull-access"></a>익명 끌어오기 액세스 사용 안 함
를 로 설정하여 익명 끌어오기 액세스를 사용하지 않도록 `--anonymous-pull-enabled` `false` 설정합니다.

```azurecli
az acr update --name myregistry --anonymous-pull-enabled false
```

## <a name="next-steps"></a>다음 단계

* [리포지토리 범위 토큰](container-registry-repository-scoped-permissions.md)사용에 대해 알아봅니다.
* Azure Container Registry에 [인증하는](container-registry-authentication.md) 옵션에 대해 알아봅니다.
