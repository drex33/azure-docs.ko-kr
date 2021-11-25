---
title: 빠른 시작 - 포털을 사용하여 연결된 레지스트리 만들기
description: Azure Portal을 사용하여 이미지 및 기타 아티팩트를 클라우드 레지스트리와 동기화할 수 있는 연결된 Azure Container Registry 리소스를 만듭니다.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021, mode-portal
ms.openlocfilehash: e21b076691fc6249a7169d525695fd6fd852fdfc
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133059521"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 연결된 레지스트리 만들기

이 빠른 시작에서는 Azure Portal을 사용하여 Azure에서 [연결된 레지스트리](intro-connected-registry.md) 리소스를 만듭니다. Azure Container Registry의 연결된 레지스트리 기능을 사용하면 레지스트리를 원격으로 또는 온-프레미스에서 배포하고 이미지 및 기타 아티팩트를 클라우드 레지스트리와 동기화할 수 있습니다. 

여기에서 클라우드 레지스트리에 대해 두 개의 연결된 레지스트리 리소스를 만듭니다. 그 중 하나는 읽기 및 쓰기(아티팩트 풀 및 푸시) 기능을 허용하고, 다른 하나는 읽기 전용 기능을 허용합니다. 

연결된 레지스트리를 만든 후 다른 가이드에 따라 온-프레미스 또는 원격 인프라에서 배포하고 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Container Registry - 컨테이너 레지스트리가 아직 없는 경우 연결된 레지스트리를 지원하는 [지역](intro-connected-registry.md#available-regions)에 [하나를 만듭니다](container-registry-get-started-portal.md)(프리미엄 계층 필요). 

컨테이너 레지스트리로 이미지를 가져오려면 Azure CLI([!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment-no-header.md)])를 사용합니다.

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>클라우드 레지스트리에 대한 전용 데이터 엔드포인트 사용

클라우드의 Azure Container Registry에 대해 [전용 데이터 엔드포인트](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)를 사용합니다. 이 단계는 연결된 레지스트리가 클라우드 레지스트리와 통신하는 데 필요합니다.

1. [Azure Portal](https://portal.azure.com)에서 컨테이너 레지스트리로 이동합니다.
1. **네트워킹 > 공용 액세스** 를 선택합니다.
**전용 데이터 엔드포인트 사용** 확인란을 선택합니다.
1. **저장** 을 선택합니다.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>읽기 및 쓰기 기능을 위한 연결된 레지스트리 리소스 만들기

다음 단계에서는 클라우드 레지스트리에 연결된 [ReadWrite 모드](intro-connected-registry.md#modes)에서 연결된 레지스트리를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 컨테이너 레지스트리로 이동합니다.
1. **연결된 레지스트리(미리 보기) > + 만들기** 를 선택합니다.
1. 다음 표의 값을 입력하거나 선택하고 **저장** 을 선택합니다.


|항목  |설명  |
|---------|---------|
|부모     | 클라우드 레지스트리에 연결된 레지스트리에 대해 **부모 항목 없음** 을 선택합니다.        |
|모드     | **ReadWrite** 를 선택합니다.         |
|이름     | 연결된 레지스트리 이름은 문자로 시작해야 하며 영숫자 문자만 포함해야 합니다. 길이는 5~40자여야 하며 이 Azure 컨테이너 레지스트리의 계층 구조에서 고유해야 합니다.       |
|로깅 속성     | 기본 설정을 적용합니다.       |
|동기화 속성    | 기본 설정을 적용합니다. 기본적으로 정의된 동기화 일정이 없기 때문에 리포지토리는 중단 없이 클라우드 레지스트리와 연결된 레지스트리 간에 동기화됩니다.      |
|리포지토리     | 이전 단계에서 가져온 리포지토리의 이름을 선택하거나 입력합니다. 지정된 리포지토리는 배포되면 클라우드 레지스트리와 연결된 레지스트리 간에 동기화됩니다.     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readwrite-connected-registry.png" alt-text="ReadWrite 모드에서 연결된 레지스트리 만들기":::


> [!IMPORTANT]
> 하위 계층이 인터넷에 액세스할 수 없는 중첩 시나리오를 지원하려면 항상 `acr/connected-registry` 리포지토리의 동기화를 허용해야 합니다. 이 리포지토리에는 연결된 레지스트리 런타임에 대한 이미지가 포함되어 있습니다.

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>읽기 전용 기능을 위한 연결된 레지스트리 리소스 만들기

다음 단계에서는 상위가 이전 섹션에서 만든 연결된 레지스트리인 [ReadOnly 모드](intro-connected-registry.md#modes)에서 연결된 레지스트리를 만듭니다. 이 연결된 레지스트리를 통해 배포 후에는 읽기 전용(아티팩트 풀) 기능을 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 컨테이너 레지스트리로 이동합니다.
1. **연결된 레지스트리(미리 보기) > + 만들기** 를 선택합니다.
1. 다음 표의 값을 입력하거나 선택하고 **저장** 을 선택합니다.


|항목  |설명  |
|---------|---------|
|부모     | 이전에 만든 연결된 레지스트리를 선택합니다.        |
|모드     | **ReadOnly** 를 선택합니다.         |
|이름     | 연결된 레지스트리 이름은 문자로 시작해야 하며 영숫자 문자만 포함해야 합니다. 길이는 5~40자여야 하며 이 Azure 컨테이너 레지스트리의 계층 구조에서 고유해야 합니다.      |
|로깅 속성     | 기본 설정을 적용합니다.       |
|동기화 속성    | 기본 설정을 적용합니다. 기본적으로 정의된 동기화 일정이 없기 때문에 리포지토리는 중단 없이 클라우드 레지스트리와 연결된 레지스트리 간에 동기화됩니다.      |
|리포지토리     | 이전 단계에서 가져온 리포지토리의 이름을 선택하거나 입력합니다. 지정된 리포지토리는 배포되면 부모 레지스트리와 연결된 레지스트리 간에 동기화됩니다.     |

:::image type="content" source="media/quickstart-connected-registry-portal/create-readonly-connected-registry.png" alt-text="ReadOnly 모드에서 연결된 레지스트리 만들기":::

## <a name="view-connected-registry-properties"></a>연결된 레지스트리 속성 보기

포털에서 연결된 레지스트리를 선택하여 연결 상태(오프라인, 온라인 또는 비정상) 및 활성화 여부(온-프레미스 배포)와 같은 속성을 봅니다. 다음 예에서는 연결된 레지스트리가 배포되지 않습니다. "오프라인"의 연결 상태는 현재 클라우드에서 연결이 끊어져 있음을 나타냅니다.

:::image type="content" source="media/quickstart-connected-registry-portal/connected-registry-properties.png" alt-text="연결된 레지스트리 속성 보기":::

이 보기에서 연결 문자열을 생성하고 선택적으로 [동기화 토큰](overview-connected-registry-access.md#sync-token)에 대한 암호를 생성할 수도 있습니다. 연결 문자열에는 연결된 레지스트리를 배포하고 콘텐츠를 부모 레지스트리와 동기화하는 데 사용되는 구성 설정이 포함됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 사용하여 Azure에서 두 개의 연결된 레지스트리 리소스를 만듭니다. 새로 연결된 레지스트리 리소스는 클라우드 레지스트리에 연결되며 클라우드 레지스트리와 아티팩트를 동기화할 수 있습니다.

연결된 레지스트리 배포 가이드를 계속 진행하여 IoT Edge 인프라에서 연결된 레지스트리를 배포하고 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [빠른 시작: IoT Edge에 연결된 레지스트리 배포][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
