---
title: Private Link로 프라이빗 액세스 사용(미리 보기)
titleSuffix: Azure Digital Twins
description: Private Link로 Azure Digital Twins 솔루션에 대해 프라이빗 액세스를 사용하도록 설정하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 7/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: 93435355a2d75a2346d076c44b377726789431a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452338"
---
# <a name="enable-private-access-with-private-link-preview"></a>Private Link로 프라이빗 액세스 사용(미리 보기)

이 문서에서는 [Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트로 Private Link를 사용 설정](concepts-security.md#private-network-access-with-azure-private-link-preview)하는 여러 방법을 설명합니다(현재 미리 보기). Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트를 구성하면 [Azure Virtual Network(VNet)](../virtual-network/virtual-networks-overview.md)에서 데이터 반출을 방지할 뿐만 아니라 Azure Digital Twins 인스턴스를 보호하고 공개 노출을 없앨 수 있습니다.

이 문서에서 설명하는 단계는 다음과 같습니다. 
1. Private Link를 켜고 Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트를 구성합니다.
1. 인스턴스에서 프라이빗 엔드포인트를 보거나 편집하거나 삭제합니다.
1. Private Link 연결만으로 API 액세스를 제한하기 위해 공용 네트워크 액세스 플래그를 사용하지 않거나 사용하도록 설정합니다.

## <a name="prerequisites"></a>필수 조건

프라이빗 엔드포인트를 설정하려면 먼저 엔드포인트를 배포할 수 있는 [Azure Virtual Network(VNet)](../virtual-network/virtual-networks-overview.md) 가 필요합니다. VNet이 아직 없으면 [Azure Virtual Network 빠른 시작](../virtual-network/quick-create-portal.md) 중 하나에 따라 이를 설정할 수 있습니다.

## <a name="add-a-private-endpoint-to-azure-digital-twins"></a>Azure Digital Twins에 프라이빗 엔드포인트 추가 

[Azure Portal](https://portal.azure.com) 또는 [Azure CLI](/cli/azure/what-is-azure-cli)를 사용하여 Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트로 Private Link를 설정할 수 있습니다. 

인스턴스 초기 설정의 일부로 Private Link를 설정하려면 Azure Portal을 사용해야 합니다. Private Link를 만든 후 인스턴스에서 Private Link를 사용하도록 설정하려면 Azure Portal 또는 Azure CLI를 사용할 수 있습니다. 이러한 생성 방법은 해당 인스턴스에 대해 동일한 구성 옵션 및 동일한 최종 결과를 제공합니다.

아래 섹션의 탭을 사용하여 원하는 환경의 지침을 선택할 수 있습니다.

>[!TIP]
> 또한 Azure Digital Twins 인스턴스 대신 Private Link 서비스를 통해 Private Link 엔드포인트를 설정할 수 있습니다. 이 방법도 동일한 구성 옵션 및 동일한 최종 결과를 제공합니다.
>
> Private Link 리소스 설정에 대한 자세한 내용은 [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [Azure Resource Manager](../private-link/create-private-endpoint-template.md) 또는 [PowerShell](../private-link/create-private-endpoint-powershell.md)에 대한 Private Link 설명서를 참조하세요.

### <a name="add-a-private-endpoint-during-instance-creation"></a>인스턴스를 만드는 동안 프라이빗 엔드포인트 추가

이 섹션에서는 Azure Digital Twins 인스턴스의 초기 설정의 일부로 Private Link를 사용하여 프라이빗 엔드포인트를 만듭니다. 이 작업은 Azure Portal에서만 수행할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal)

이 섹션에서는 Azure Portal에서 Azure Digital Twins 인스턴스를 설정하는 동안 Private Link를 설정하는 방법에 대해 설명합니다. 

Private Link 옵션은 인스턴스 설정의 **네트워킹** 탭에 있습니다.

1. Azure Portal에서 Azure Digital Twins 인스턴스 설정을 시작합니다. 지침은 [인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)을 참조하세요.
1. 인스턴스 설정의 **네트워킹** 탭에 도달하면 **연결 방법** 에 대해 **프라이빗 엔드포인트** 옵션을 선택하여 프라이빗 엔드포인트를 사용할 수 있습니다.

    그러면 프라이빗 엔드포인트 세부 정보를 구성할 수 있는 **프라이빗 엔드포인트 연결** 이라는 섹션이 추가됩니다. 계속해서 **+ 추가** 단추를 선택합니다.
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="프라이빗 엔드포인트를 만드는 방법을 강조 표시하는 새 Azure Digital Twins 인스턴스의 네트워킹 탭을 보여주는 Azure Portal의 스크린샷. '추가' 단추가 강조 표시됩니다." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

1. 열린 **프라이빗 엔드포인트 만들기** 페이지에서 새 프라이빗 엔드포인트에 대한 세부 정보를 입력합니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="프라이빗 엔드포인트 만들기 페이지를 보여 주는 Azure Portal의 스크린샷입니다. 아래에서 설명된 필드가 포함되어 있습니다.":::

    1. **구독** 및 **리소스 그룹** 에 선택 항목을 입력합니다. **위치** 를 사용할 VNet과 동일한 위치로 설정합니다. 엔드포인트 **이름** 을 선택하고 **대상 하위 리소스** 로 *API* 를 선택합니다.

    1. 다음으로, 엔드포인트 배포를 위해 사용할 **가상 네트워크** 및 **서브넷** 을 선택합니다.

    1. 마지막으로 **비공개 DNS 영역과 통합** 할지 여부를 선택합니다. 기본값 **예** 를 사용하거나 이 옵션에 대한 도움말이 필요하면 포털 링크에 따라 [프라이빗 DNS 통합에 대해 자세히 알아볼 수 있습니다](../private-link/private-endpoint-overview.md#dns-configuration).

    1. 구성 옵션을 입력한 후 **확인** 을 선택하여 완료합니다.

1. 이렇게 하면 Azure Digital Twins 인스턴스 설정의 **네트워킹** 탭으로 돌아옵니다. **프라이빗 엔드포인트 연결** 아래에 새 엔드포인트가 표시되는지 확인합니다.
    
    :::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="새로 만든 프라이빗 엔드포인트가 있는 Azure Digital Twins의 네트워킹 탭을 보여주는 Azure Portal의 스크린샷." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

1. 아래의 탐색 단추를 사용하여 남은 인스턴스 설정을 계속할 수 있습니다.

# <a name="cli"></a>[CLI](#tab/cli)

Azure CLI를 사용하여 인스턴스를 만드는 동안에는 Private Link 엔드포인트를 추가할 수 없습니다. 

Azure Portal로 전환하여 인스턴스를 만드는 동안 엔드포인트를 추가하거나 다음 섹션으로 계속 진행하여 인스턴스를 만든 후 CLI를 사용하여 프라이빗 엔드포인트를 추가할 수 있습니다.

--- 

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>기존 인스턴스에 프라이빗 엔드포인트 추가

이 섹션에서는 이미 존재하는 Azure Digital Twins 인스턴스에 대해 프라이빗 엔드포인트로 Private Link를 사용 설정합니다.

# <a name="portal"></a>[포털](#tab/portal)

1. 먼저 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다. 포털 검색 창에서 이름을 검색하여 Azure Digital Twins 인스턴스를 표시합니다.

1. 왼쪽 메뉴에서 **네트워킹(미리 보기)** 를 선택합니다.

1. **프라이빗 엔드포인트 연결** 탭으로 전환합니다.

1. **+ 프라이빗 엔드포인트** 를 선택하여 **프라이빗 엔드포인트 만들기** 설정을 엽니다.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="프라이빗 엔드포인트를 만드는 방법을 강조 표시하는 기존 Azure Digital Twins 인스턴스에 대한 네트워킹 페이지를 보여주는 Azure Portal의 스크린샷." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1.  **기본**  탭에서 **구독** 및 프로젝트의 **리소스 그룹** 을 선택하고 엔드포인트의 **이름** 및 **지역** 을 선택합니다. 지역은 사용 중인 VNet의 지역과 동일해야 합니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 첫 번째(기본) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **다음: 리소스 >** 단추를 선택하여 다음 탭으로 이동합니다.

1. **리소스** 탭에서 이 정보를 입력하거나 선택합니다. 
    * **연결 방법**: **내 디렉터리의 Azure 리소스에 연결** 을 선택하여 Azure Digital Twins 인스턴스를 검색합니다.
    * **구독**: 구독을 입력합니다.
    * **리소스 종류**: **Microsoft.DigitalTwins/digitalTwinsInstances** 를 선택합니다.
    * **리소스**: Azure Digital Twins 인스턴스의 이름을 선택합니다.
    * **대상 하위 리소스**: **API** 를 선택합니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 두 번째(리소스) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **다음: 연결 >** 단추를 선택하여 다음 탭으로 이동합니다.    

1.  **구성** 탭에서 다음 정보를 입력하거나 선택합니다.
    * **가상 네트워크**: 가상 네트워크를 선택합니다.
    * **서브넷**: 가상 네트워크에서 서브넷을 선택합니다.
    * **프라이빗 DNS 영역과 통합**: **프라이빗 DNS 영역과 통합** 여부를 선택합니다. 기본값 **예** 를 사용하거나 이 옵션에 대한 도움말이 필요하면 포털 링크에 따라 [프라이빗 DNS 통합에 대해 자세히 알아볼 수 있습니다](../private-link/private-endpoint-overview.md#dns-configuration).
    **예** 를 선택하면 기본 구성 정보를 그대로 둘 수 있습니다.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 세 번째(구성) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **검토 + 만들기** 단추를 선택하여 설정을 완료할 수 있습니다. 

1. **검토 + 만들기** 탭에서 선택 항목을 검토한 후  **만들기** 단추를 선택합니다. 

엔드포인트 배포가 완료되었으면 Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트 연결이 표시됩니다.

# <a name="cli"></a>[CLI](#tab/cli)

프라이빗 엔드포인트를 만들어 Azure CLI를 사용하여 Azure Digital Twins 인스턴스에 연결하려면 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create) 명령을 사용합니다. `--private-connection-resource-id` 매개 변수에 정규화된 ID를 사용하여 Azure Digital Twins 인스턴스를 식별합니다.

다음은 명령을 사용하여 필수 매개 변수만 있는 프라이빗 엔드포인트를 만드는 예제입니다.

```azurecli-interactive
az network private-endpoint create --connection-name <private-link-service-connection> --name <name-for-private-endpoint> --resource-group <resource-group> --subnet <subnet-ID> --private-connection-resource-id "/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<Azure-Digital-Twins-instance-name>" 
```

필수 및 선택적 매개 변수의 전체 목록과 더 많은 프라이빗 엔드포인트 생성 예제는 [az network private-endpoint create 참조 설명서](/cli/azure/network/private-endpoint?view=azure-cli-latest&preserve-view=true#az_network_private_endpoint_create)를 참조하세요.

--- 

## <a name="manage-private-endpoint-connections"></a>프라이빗 엔드포인트 연결 관리

이 섹션에서는 프라이빗 엔드포인트를 만든 후 보고, 편집하고, 삭제하는 방법을 확인합니다.

# <a name="portal"></a>[포털](#tab/portal)

Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트가 만들어지면 Azure Digital Twins 인스턴스의 **네트워킹(미리 보기)** 탭에서 볼 수 있습니다. 이 페이지에는 인스턴스와 연결된 모든 프라이빗 엔드포인트 연결이 표시됩니다.

:::image type="content" source="media/how-to-enable-private-link/view-endpoint-digital-twins.png" alt-text="하나의 프라이빗 엔드포인트가 있는 기존 Azure Digital Twins 인스턴스에 대한 네트워킹 페이지를 보여 주는 Azure Portal의 스크린샷" lightbox="media/how-to-enable-private-link/view-endpoint-digital-twins.png":::


엔드포인트를 선택하여 해당 정보를 자세히 보거나, 구성 설정을 변경하거나, 연결을 삭제합니다.

>[!TIP]
> Azure Portal의 Private Link 센터에서도 엔드포인트를 확인할 수 있습니다.

# <a name="cli"></a>[CLI](#tab/cli)

Azure Digital Twins 인스턴스에 대한 프라이빗 엔드포인트를 만든 후에는 [az dt network private-endpoint connection](/cli/azure/dt/network/private-endpoint/connection?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 인스턴스와 관련된 프라이빗 엔드포인트 **연결** 을 계속 관리할 수 있습니다. 작업에는 다음 항목이 포함됩니다.
* 프라이빗 엔드포인트 연결 표시
* 프라이빗 엔드포인트 연결의 상태 설정
* 프라이빗 엔드포인트 연결 삭제
* 인스턴스에 대한 모든 프라이빗 엔드포인트 연결 나열

자세한 내용 및 예제는 [az dt network private-endpoint 참조 설명서](/cli/azure/dt/network/private-endpoint?view=azure-cli-latest&preserve-view=true)를 참조하세요.

### <a name="get-additional-private-link-information"></a>추가 Private Link 정보 가져오기

[az dt network private-link](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 인스턴스의 Private Link 상태에 대한 추가 정보를 가져올 수 있습니다. 작업에는 다음 항목이 포함됩니다.
* Azure Digital Twins 인스턴스와 연결된 Private Link 나열
* 인스턴스와 연결된 Private Link 표시

자세한 내용 및 예제는 [az dt network private-link 참조 설명서](/cli/azure/dt/network/private-link?view=azure-cli-latest&preserve-view=true)를 참조하세요.

--- 

## <a name="disable--enable-public-network-access-flags"></a>공용 네트워크 액세스 플래그 사용 안 함/사용

네트워크 보안 향상을 위해 모든 공용 연결을 거부하고 프라이빗 엔드포인트를 통한 연결만 허용하도록 Azure Digital Twins 인스턴스를 구성할 수 있습니다. 이 작업은 **공용 네트워크 액세스 플래그** 를 사용하여 수행됩니다. 

이 정책을 사용하면 API 액세스를 Private Link 연결만으로 제한할 수 있습니다. 공용 네트워크 액세스 플래그가 *사용 안 함* 으로 설정되었으면 퍼블릭 클라우드에서 Azure Digital Twins 인스턴스 데이터 영역으로의 모든 REST API 호출이 `403, Unauthorized`를 반환합니다. 또는 정책이 *사용 안 함* 으로 설정되었고 프라이빗 엔드포인트를 통해 요청을 수행하면 API 호출이 성공합니다.

[Azure Portal](https://portal.azure.com), [Azure CLI](/cli/azure/) 또는 [ARMClient 명령 도구](https://github.com/projectkudu/ARMClient)를 사용하여 네트워크 플래그의 값을 업데이트할 수 있습니다.

# <a name="portal"></a>[포털](#tab/portal-2)

[Azure Portal](https://portal.azure.com)에서 공용 네트워크 액세스를 사용 안 함 또는 사용하도록 설정하려면 포털을 열고 Azure Digital Twins 인스턴스로 이동합니다.

1. 왼쪽 메뉴에서 **네트워킹(미리 보기)** 를 선택합니다.

1. **공용 액세스** 탭에서 **공용 네트워크 액세스 허용** 을 **사용 안 함** 또는 **모든 네트워크** 로 설정합니다.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="공용 액세스를 토글하는 방법을 강조 표시하는 Azure Digital Twins 인스턴스에 대한 네트워킹 페이지를 보여주는 Azure Portal의 스크린샷." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    **저장** 을 선택합니다.

# <a name="cli"></a>[CLI](#tab/cli-2)

Azure CLI에서 `az dt create` 명령에 `--public-network-access` 매개 변수를 추가하여 공용 네트워크 액세스를 사용하거나 사용하지 않도록 설정할 수 있습니다. 이 명령을 사용하여 새 인스턴스를 만들 수도 있지만, 이 명령을 사용하여 이미 존재하는 인스턴스의 이름을 제공하여 기존 인스턴스의 속성을 편집할 수 있습니다. 이 명령에 대한 자세한 내용은 해당 [참조 설명서](/cli/azure/dt?view=azure-cli-latest&preserve-view=true#az_dt_create) 또는 [Azure Digital Twins 인스턴스를 설정하기 위한 일반 지침](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)을 참조하세요.

Azure Digital Twins 인스턴스에 대해 공용 네트워크 액세스를 **사용하지 않도록 설정** 하려면 다음과 같이 `--public-network-access` 매개 변수를 사용합니다.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Disabled
```

현재 사용하지 않도록 설정된 인스턴스에서 공용 네트워크 액세스를 **사용하도록 설정** 하려면 다음과 유사한 명령을 사용합니다.

```azurecli-interactive
az dt create --dt-name <name-of-existing-instance> --resource-group <resource-group> --public-network-access Enabled
```

# <a name="armclient"></a>[ARMClient](#tab/arm-client-2)

[ARMClient 명령 도구](https://github.com/projectkudu/ARMClient)를 사용하면 아래 명령을 사용하여 공용 네트워크 액세스를 사용하거나 사용하지 않도록 설정할 수 있습니다. 

공용 네트워크 액세스 **사용 안 함**:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

공용 네트워크 액세스 **사용**:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

---


## <a name="next-steps"></a>다음 단계

Azure Private Link에 대해 자세히 알아보기: 
* [Azure Private Link 서비스는 무엇입니까?](../private-link/private-link-service-overview.md)