---
title: 연결된 레지스트리에 대한 액세스 이해
description: Azure Container Registry에서 연결된 레지스트리에 대한 토큰 기반 인증 및 권한 부여 소개
author: toddysm
ms.author: memladen
ms.service: container-registry
ms.topic: overview
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a98772cced7b5f7e72c66d134e9ff652f64b4086
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053208"
---
# <a name="understand-access-to-a-connected-registry"></a>연결된 레지스트리에 대한 액세스 이해

[연결된 레지스트리](intro-connected-registry.md)를 액세스하고 관리하기 위해 현재 ACR [토큰 기반 인증](container-registry-repository-scoped-permissions.md)만 지원됩니다. 다음 이미지에 표시된 것처럼 각 연결된 레지스트리에서 두 가지 유형의 토큰을 사용합니다.

* [**클라이언트 토큰**](#client-tokens) - 온-프레미스 클라이언트에서 연결된 레지스트리를 인증하고 이미지 및 아티팩트를 보내거나 받는 데 사용하는 하나 이상의 토큰입니다.
* [**동기화 토큰**](#sync-token) - 각 연결된 레지스트리가 부모에 액세스하고 콘텐츠를 동기화하는 데 사용하는 토큰입니다.

![연결된 레지스트리 인증 개요](media/overview-connected-registry-access/connected-registry-authentication-overview.svg)

> [!IMPORTANT]
> 각 연결된 레지스트리에 대한 토큰 암호를 안전한 위치에 저장합니다. 토큰 암호를 만든 후에는 토큰 암호를 검색할 수 없습니다. 언제든지 토큰 암호를 다시 생성할 수 있습니다.

## <a name="client-tokens"></a>클라이언트 토큰

연결된 레지스트리에 대한 클라이언트 액세스를 관리하려면 하나 이상의 리포지토리에서 작업 범위가 지정된 토큰을 만듭니다. 토큰을 만든 후에는 [az acr connected-registry update](/cli/azure/acr/connected-registry#az_acr_connected_registry_update) 명령을 사용하여 토큰을 수락하도록 연결된 레지스트리를 구성합니다. 그러면 클라이언트가 토큰 자격 증명을 사용하여 연결된 레지스트리 엔드포인트에 액세스할 수 있습니다. 예를 들어 Docker CLI 명령을 사용하여 연결된 레지스트리에서 이미지를 풀 또는 푸시할 수 있습니다.

클라이언트 토큰 작업을 구성하는 옵션은 연결된 레지스트리가 푸시 작업과 풀 작업을 모두 허용하는지 또는 풀 전용 미러로 작동하는지 여부에 따라 달라집니다. 
* 기본 [ReadWrite 모드](intro-connected-registry.md#modes)의 연결된 레지스트리를 사용하면 풀 및 푸시 작업을 모두 수행할 수 있으므로 해당 레지스트리의 리포지토리 콘텐츠에 대한 *읽기* 및 *쓰기* 동작을 모두 허용하는 토큰을 만들 수 있습니다. 
* [ReadOnly 모드](intro-connected-registry.md#modes)의 연결된 레지스트리의 경우 클라이언트 토큰은 리포지토리 콘텐츠에 대한 *읽기* 동작만 허용할 수 있습니다.

### <a name="manage-client-tokens"></a>클라이언트 토큰 관리

[az acr token](/cli/az/acr#az_acr_token) 및 [az acr scope-map](/cli/az/acr#az_acr_scope-map) 명령을 사용하여 필요에 따라 클라이언트 토큰, 암호 또는 범위 맵을 업데이트합니다. 클라이언트 토큰 업데이트는 토큰을 수락하는 연결된 레지스트리에 자동으로 전파됩니다.

## <a name="sync-token"></a>동기화 토큰

각 연결된 레지스트리는 동기화 토큰을 사용하여 직계 부모로 인증합니다. 부모는 다른 연결된 레지스트리 또는 클라우드 레지스트리일 수 있습니다. 연결된 레지스트리는 콘텐츠를 부모와 동기화하거나 다른 업데이트를 수행할 때 자동으로 이 토큰을 사용합니다. 

* 동기화 토큰 및 암호는 연결된 레지스트리 리소스를 만들 때 자동으로 생성됩니다. [az acr connected-registry install renew-credentials][az-acr-connected-registry-install-renew-credentials] 명령을 실행하여 암호를 다시 생성합니다.
* 연결된 레지스트리를 온-프레미스에 배포하는 데 사용되는 구성에 동기화 토큰 자격 증명을 포함합니다. 
* 기본적으로 동기화 토큰에는 선택한 리포지토리를 해당 부모와 동기화할 수 있는 권한이 부여됩니다. 연결된 레지스트리 리소스를 만들 때 동기화할 기존 동기화 토큰 또는 하나 이상의 리포지토리를 제공해야 합니다.
* 연결된 레지스트리의 부모와 통신하는 데 사용되는 게이트웨이에서 동기화 메시지를 읽고 쓸 수 있는 권한도 갖습니다. 이러한 메시지는 동기화 일정을 제어하고 연결된 레지스트리와 해당 부모 간의 기타 업데이트를 관리합니다.

### <a name="manage-sync-token"></a>동기화 토큰 관리

[az acr token](/cli/az/acr#az_acr_token) 및 [az acr scope-map](/cli/az/acr#az_acr_scope-map) 명령을 사용하여 필요에 따라 동기화 토큰, 암호 또는 범위 맵을 업데이트합니다. 동기화 토큰 업데이트는 연결된 레지스트리에 자동으로 전파됩니다. 동기화 토큰을 업데이트할 때 암호를 회전하는 표준 방법을 따릅니다.

> [!NOTE]
> 동기화 토큰은 토큰과 연결되어 있는 연결된 레지스트리를 삭제할 때까지 삭제할 수 없습니다. 동기화 토큰의 상태를 `disabled`로 설정하여 연결된 레지스트리를 비활성화할 수 있습니다. 

## <a name="registry-endpoints"></a>레지스트리 엔드포인트

연결된 레지스트리에 대한 토큰 자격 증명은 특정 레지스트리 엔드포인트에 액세스하도록 범위가 지정됩니다.

* 클라이언트 토큰은 연결된 레지스트리의 엔드포인트에 액세스합니다. 연결된 레지스트리 엔드포인트는 로그인 서버 URI로, 일반적으로 이를 호스트하는 서버 또는 디바이스의 IP 주소입니다.

* 동기화 토큰은 부모 레지스트리의 엔드포인트에 액세스합니다. 이 엔드포인트는 다른 연결된 레지스트리 엔드포인트이거나 클라우드 레지스트리 자체입니다. 클라우드 레지스트리에 액세스하도록 범위가 지정된 경우 동기화 토큰은 다음과 같은 두 개의 레지스트리 엔드포인트에 연결되어야 합니다.

    - 정규화된 로그인 서버 이름, 예: `contoso.azurecr.io`. 이 엔드포인트는 인증에 사용됩니다.
    - 클라우드 레지스트리의 정규화된 지역 [데이터 엔드포인트](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints), 예: `contoso.westus2.data.azurecr.io`. 이 엔드포인트는 동기화를 위해 연결된 레지스트리와 메시지를 교환하는 데 사용됩니다. 

## <a name="next-steps"></a>다음 단계

다음 문서를 계속 진행하여 연결된 레지스트리를 활용할 수 있는 특정 시나리오에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [개요: 연결된 레지스트리 및 IoT Edge][overview-connected-registry-and-iot-edge]

<!-- LINKS - internal -->
[az-acr-connected-registry-update]: /cli/azure/acr/connected-registry#az_acr_connected_registry_update
[az-acr-connected-registry-install-renew-credentials]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_install_renew_credentials
[overview-connected-registry-and-iot-edge]:overview-connected-registry-and-iot-edge.md
[repository-scoped-permissions]: container-registry-repository-scoped-permissions.md
