---
title: '빠른 시작: 애플리케이션의 HA용 프로필 만들기 - Azure CLI - Azure Traffic Manager'
description: 이 빠른 시작 문서에서는 Azure CLI를 사용하여 고가용성 웹 애플리케이션을 빌드하기 위한 Traffic Manager 프로필을 만드는 방법을 설명합니다.
services: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2021
ms.author: duau
ms.custom: devx-track-azurecli, mode-other
ms.openlocfilehash: 5bbde83a98f01ffb575058ad5df76ef66115f1ec
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069195"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 고가용성 웹 애플리케이션에 대한 Traffic Manager 프로필 만들기

이 빠른 시작에서는 웹 애플리케이션에 고가용성을 제공하는 Traffic Manager 프로필을 만드는 방법에 대해 설명합니다.

여기서는 웹 애플리케이션의 두 인스턴스를 만듭니다. 각각 다른 Azure 지역에서 실행됩니다. [엔드포인트 우선 순위](traffic-manager-routing-methods.md#priority-traffic-routing-method)에 따라 Traffic Manager 프로필을 만듭니다. 프로필은 웹 애플리케이션을 실행하는 주 사이트로 사용자 트래픽을 보냅니다. Traffic Manager는 웹 애플리케이션을 지속적으로 모니터링합니다. 주 사이트를 사용할 수 없는 경우 백업 사이트에 자동 장애 조치를 제공합니다.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="CLI을 사용하는 Traffic Manager 배포 환경의 다이어그램." border="false":::

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

[az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create)를 사용하여 엔드포인트 우선 순위에 따라 사용자 트래픽을 보내는 Traffic Manager 프로필을 만듭니다.

다음 예제에서 **<profile_name>** 을 고유한 Traffic Manager 프로필 이름으로 바꿉니다.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>웹앱 만들기

이 빠른 시작에는 두 개의 서로 다른 Azure 지역(*미국 동부* 및 *서유럽*)에 배포된 두 개의 웹 애플리케이션 인스턴스가 필요합니다. 각각은 Traffic Manager에 대한 기본 및 장애 조치 엔드포인트의 역할을 합니다.

### <a name="create-web-app-service-plans"></a>웹앱 서비스 계획 만들기
[az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)를 사용하여 서로 다른 두 Azure 지역에 배포할 웹 애플리케이션의 두 인스턴스에 대한 웹앱 서비스 계획을 만듭니다.

다음 예제에서 **<appspname_eastus>** 및 **<appspname_westeurope>** 을 고유한 App Service 계획 이름으로 바꿉니다.

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>앱 서비스 계획에 웹앱 만들기
[az webapp create](/cli/azure/webapp#az_webapp_create)를 사용하여 웹 애플리케이션의 두 인스턴스를 *미국 동부* 및 *서유럽* Azure 지역의 App Service 계획에 만듭니다.

다음 예제에서 **<app1name_eastus>** 및 **<app2name_westeurope>** 을 고유한 앱 이름으로 바꾸고, **<appspname_eastus>** 및 **<appspname_westeurope>** 을 이전 섹션에서 App Service 계획을 만드는 데 사용한 이름으로 바꿉니다.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager 엔드포인트 추가
다음과 같이 [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create)를 사용하여 두 Web App을 Traffic Manager 엔드포인트로 Traffic Manager 프로필에 추가합니다.

- Web App ID를 확인하고, *미국 서부* Azure 지역에 있는 Web App을 모든 사용자 트래픽을 라우팅하는 기본 엔드포인트로 추가합니다. 
- Web App ID를 확인하고, *서유럽* Azure 지역에 있는 Web App을 장애 조치(failover) 엔드포인트로 추가합니다. 

기본 엔드포인트를 사용할 수 없으면 트래픽이 자동으로 장애 조치 엔드포인트로 라우팅됩니다.

다음 예제에서는 **<app1name_eastus>** 및 **<app2name_westeurope>** 를 이전 섹션의 각 지역에 대해 만든 앱 이름으로 바꿉니다. 그런 다음, **<profile_name>** 을 이전 섹션에서 사용한 프로필 이름으로 바꿉니다. 

**미국 동부 엔드포인트**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

출력에 표시된 ID를 적어두고, 다음 명령을 사용하여 엔드포인트를 추가합니다.

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**서유럽 엔드포인트**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

출력에 표시된 ID를 적어두고, 다음 명령을 사용하여 엔드포인트를 추가합니다.

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Traffic Manager 프로필 테스트

이 섹션에서는 Traffic Manager 프로필의 도메인 이름을 확인합니다. 또한 기본 엔드포인트를 사용할 수 없도록 구성합니다. 마지막으로 웹앱을 계속 사용할 수 있는지 확인합니다. 이는 Traffic Manager에서 트래픽을 장애 조치 엔드포인트로 보내기 때문입니다.

다음 예제에서는 **<app1name_eastus>** 및 **<app2name_westeurope>** 를 이전 섹션의 각 지역에 대해 만든 앱 이름으로 바꿉니다. 그런 다음, **<profile_name>** 을 이전 섹션에서 사용한 프로필 이름으로 바꿉니다.

### <a name="determine-the-dns-name"></a>DNS 이름 확인

[az network traffic-manager profile show](/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_show)를 사용하여 Traffic Manager 프로필의 DNS 이름을 확인합니다.

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

**RelativeDnsName** 값을 복사합니다. Traffic Manager 프로필의 DNS 이름은 *http://<* relativednsname *>.trafficmanager.net* 입니다. 

### <a name="view-traffic-manager-in-action"></a>실행 중인 Traffic Manager 보기
1. 웹 브라우저에서 Traffic Manager 프로필의 DNS 이름(*http://<* relativednsname *>.trafficmanager.net*)을 입력하여 웹앱의 기본 웹 사이트를 표시합니다.

    > [!NOTE]
    > 이 빠른 시작 시나리오에서는 모든 요청이 기본 엔드포인트로 라우팅됩니다. **우선 순위 1** 로 설정됩니다.
2. 작동 중인 Traffic Manager 장애 조치를 보려면 [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update)를 사용하여 기본 사이트를 사용하지 않도록 설정합니다.

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Traffic Manager 프로필의 DNS 이름(*http://<* relativednsname *>.trafficmanager.net*)을 복사하여 새 웹 브라우저 세션에서 웹 사이트를 표시합니다.
4. 웹앱을 계속 사용할 수 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

완료되면 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 리소스 그룹, 웹 애플리케이션 및 모든 관련 리소스를 삭제합니다.

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 웹 애플리케이션에 고가용성을 제공하는 Traffic Manager 프로필을 만들었습니다. 트래픽 라우팅에 대해 자세히 알아보려면 Traffic Manager 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Traffic Manager 자습서](tutorial-traffic-manager-improve-website-response.md)
