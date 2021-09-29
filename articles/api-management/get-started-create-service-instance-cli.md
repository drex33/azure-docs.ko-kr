---
title: 빠른 시작 - CLI를 사용하여 Azure API Management 인스턴스 만들기
description: Azure CLI를 사용하여 새 Azure API Management 서비스 인스턴스를 만듭니다.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: danlep
ms.openlocfilehash: 65fe80c73203b1b1f4445d4891f35004b4c587d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661339"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 새 Azure API Management 서비스 인스턴스 만들기

Azure APIM(API Management)을 사용하여 조직은 외부, 파트너 및 내부의 개발자에게 API를 게시하여 데이터 및 서비스의 잠재성을 활용할 수 있습니다. API Management는 개발자 참여, 비즈니스 통찰력, 분석, 보안과 보호 등을 통해 성공적인 API 프로그램을 보장하는 핵심적인 역량을 제공합니다. APIM을 사용하면 어디서든 호스팅되는 기존 백 엔드 서비스를 위한 최신 API 게이트웨이를 만들고 관리할 수 있습니다. 자세한 내용은 [개요](api-management-key-concepts.md)를 참조하세요.

이 빠른 시작에서는 Azure CLI에서 [az apim](/cli/azure/apim) 명령을 사용하여 새 API Management 인스턴스를 만드는 단계를 설명합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.11.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

모든 Azure 리소스와 마찬가지로 Azure API Management 인스턴스는 리소스 그룹에 배포해야 합니다. 리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.

먼저, 다음 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 미국 중부 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>새 서비스 만들기

이제 리소스 그룹이 있으므로 API Management 서비스 인스턴스를 만들 수 있습니다. [az apim create](/cli/azure/apim#az_apim_create) 명령을 사용하여 만들고 서비스 이름과 게시자 세부 정보를 제공합니다. 서비스 이름은 Azure 내에서 고유해야 합니다. 

다음 예제에서는 *myapim* 이 서비스 이름에 사용됩니다. 이름을 고유한 값으로 업데이트합니다. 또한 API 게시자의 조직 이름과 알림을 받을 이메일 주소를 업데이트합니다. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

기본적으로 이 명령은 Azure API Management를 평가하는 경제적인 옵션인 개발자 계층에서 인스턴스를 만듭니다. 이 계층은 프로덕션 용도가 아닙니다. API Management 계층 크기 조정에 대한 자세한 내용은 [업그레이드 및 크기 조정](upgrade-and-scale.md)을 참조하세요. 

> [!TIP]
> 이 계층에서 API Management 서비스를 만들고 활성화하는 데 30~40분 정도 걸릴 수 있습니다. 이전 명령은 `--no-wait` 옵션을 사용하여 서비스가 생성되는 동안 명령이 즉시 반환되도록 합니다.

[az apim show](/cli/azure/apim#az_apim_show) 명령을 실행하여 배포의 상태를 확인합니다.

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

처음에 출력은 `Activating` 상태를 표시하는 다음과 유사합니다.

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

활성화 후 상태는 `Online`이고 서비스 인스턴스에는 게이트웨이 주소와 공용 IP 주소가 있습니다. 현재 이러한 주소는 콘텐츠를 노출하지 않습니다. 예를 들면 다음과 같습니다.

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

API Management 서비스 인스턴스가 온라인 상태이면 사용할 준비가 된 것입니다. 자습서를 시작하여 [첫 번째 API를 가져오고 게시](import-and-publish.md)하세요.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹 및 API Management 서비스 인스턴스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 API 가져오기 및 게시](import-and-publish.md)
