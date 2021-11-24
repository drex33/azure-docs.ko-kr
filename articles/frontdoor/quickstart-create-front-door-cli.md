---
title: '빠른 시작: Azure Front Door를 사용하여 고가용성 설정 - Azure CLI'
description: 이 빠른 시작에서는 Azure Front Door를 사용하여 Azure CLI를 사용하는 고가용성 및 고성능 글로벌 웹 애플리케이션을 만드는 방법을 보여 줍니다.
services: front-door
author: duongau
manager: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/19/2021
ms.author: duau
ms.custom: mode-other
ms.openlocfilehash: 61f96f47f8d250c4352a60803b8344309bd0115e
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041894"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하는 고가용성 글로벌 웹 애플리케이션을 위한 Front Door 만들기

Azure CLI를 사용하여 고가용성 및 고성능 글로벌 웹 애플리케이션을 만들어 Azure Front Door를 시작하세요.

Front Door는 웹 트래픽을 백 엔드 풀의 특정 리소스로 보냅니다. 프런트 엔드 도메인을 정의하고, 백 엔드 풀에 리소스를 추가하고, 라우팅 규칙을 만듭니다. 이 문서에서는 두 개의 웹앱 리소스와 기본 경로 일치 “/*”를 사용하는 단일 라우팅 규칙을 포함하는 하나의 백 엔드 풀에 대한 간단한 구성을 사용합니다.

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Azure CLI를 사용하는 Front Door 배포 환경의 다이어그램." border="false":::

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure CLI 또는 Azure Cloud Shell
- front-door 확장이 Azure CLI에 추가되어 있는지 확인

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

로컬로 CLI를 설치하여 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.

이 빠른 시작에서는 두 개의 리소스 그룹이 필요합니다. *미국 중부* 에서 하나이며 *미국 중남부* 에서 두 번째입니다.

[az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDEast \
    --location eastus
```

## <a name="create-two-instances-of-a-web-app"></a>웹앱의 두 인스턴스 만들기

이 빠른 시작에는 서로 다른 Azure 지역에서 실행되는 웹 애플리케이션의 두 인스턴스가 필요합니다. 두 웹 애플리케이션 인스턴스는 모두 활성/활성 모드로 실행되므로 두 인스턴스 중 하나에서 트래픽을 서비스할 수 있습니다.

웹앱이 아직 없는 경우 다음 스크립트를 사용하여 두 가지 예제 웹앱을 설정합니다.

### <a name="create-app-service-plans"></a>앱 서비스 계획 만들기

웹앱을 만들려면 먼저 *미국 중부* 와 *미국 동부* 에 하나씩 두 개의 앱 서비스 계획이 필요합니다.

[az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true)를 사용하여 앱 서비스 계획을 만듭니다.

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanEastUS \
--resource-group myRGFDEast
```

### <a name="create-web-apps"></a>웹앱 만들기

다음 명령을 실행하면 이전 단계의 각 앱 서비스 계획에 웹앱이 만들어집니다. 웹앱 이름은 전역적으로 고유해야 합니다.

[az webapp create](/cli/azure/webapp#az_webapp_create&preserve-view=true)를 사용하여 웹앱을 만듭니다.

```azurecli-interactive
az webapp create \
--name WebAppContoso-1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso-2 \
--resource-group myRGFDEast \
--plan myAppServicePlanEastUS
```

다음 단계에서 Front Door를 배포할 때 백 엔드 주소를 정의할 수 있도록 각 웹앱의 기본 호스트 이름을 적어 둡니다.

## <a name="create-the-front-door"></a>Front Door 만들기

다음을 실행하여 기본 부하 분산 설정, 상태 프로브 및 라우팅 규칙을 사용하여 기본 Front 도어를 만듭니다.

[az network front-door create](/cli/azure/network/front-door#az_network_front_door_create&preserve-view=true)를 사용하여 Front Door를 만듭니다.

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso-1.azurewebsites.net webappcontoso-2.azurewebsites.net 
```

**--resource-group:** Front Door를 배포하려는 리소스 그룹을 지정합니다.

**--name:** Azure Front Door의 전역적으로 고유 이름을 지정합니다. 

**--accepted-protocols:** 허용되는 값은 **http** 및 **https** 입니다. 두 가지를 모두 사용하려는 경우 공백으로 구분합니다.

**--backend-address:** 여기에서 두 웹앱 호스트 이름을 공백으로 구분하여 정의합니다.

배포가 성공적으로 완료되면 *frontEndpoints* 섹션에서 호스트 이름을 적어 둡니다.

## <a name="test-the-front-door"></a>Front Door 테스트

웹 브라우저를 열고 명령에서 호스트 이름 가져오기를 입력합니다. Front Door가 백 엔드 리소스 중 하나로 요청을 보냅니다.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Front Door 테스트 페이지":::

## <a name="clean-up-resources"></a>리소스 정리

Front Door로 만든 리소스가 더 이상 필요하지 않으면 두 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 Front Door 및 모든 관련 리소스도 함께 삭제됩니다. 

리소스 그룹을 삭제하려면 [az group delete](/cli/azure/group#az_group_delete&preserve-view=true)를 사용합니다.

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDEast
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.
* Front Door
* 두 개의 웹앱

사용자 지정 도메인을 Front Door에 추가하는 방법을 알아보려면 Front Door 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [사용자 지정 도메인 추가](front-door-custom-domain.md)
