---
title: Private Links 사용 - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Portal 사용하여 Private Link Azure Time Series Insights Gen2 솔루션에 대한 프라이빗 액세스를 사용하도록 설정하는 방법을 참조하세요.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 21a6c529aaa086fa75aef9e665cbbdce882baf5a
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057106"
---
# <a name="enable-private-access-for-tsi-with-private-link-preview"></a>Private Link(미리 보기)를 사용하여 TSI에 대한 프라이빗 액세스 사용

이 문서에서는 Azure Time Series Insights Gen2 환경(현재 미리 [보기)에 프라이빗 엔드포인트를 사용하여 Private Link 사용하도록 설정하는](concepts-private-links.md) 방법을 설명합니다. Azure Time Series Insights Gen2 환경에 대한 프라이빗 엔드포인트를 구성하면 Azure Time Series Insights 환경을 보호하고 공개 노출을 제거하고 [Azure VNet(Virtual Network)에서](../virtual-network/virtual-networks-overview.md)데이터 반출을 방지할 수 있습니다.

이 문서에서는 [**Azure Portal**](https://portal.azure.com)을 사용하여 프로세스를 안내합니다.

이 문서에서 설명하는 단계는 다음과 같습니다. 
1. Private Link 켜고 Time Series Insights Gen2 환경에 대한 프라이빗 엔드포인트를 구성합니다.
1. 공용 네트워크 액세스 플래그를 사용하지 않도록 설정하거나 사용하도록 설정하여 Private Link 연결에 대한 액세스만 제한합니다.

## <a name="prerequisites"></a>사전 요구 사항

프라이빗 엔드포인트를 설정하려면 먼저 엔드포인트를 배포할 수 있는 [**Azure Virtual Network(VNet)**](../virtual-network/virtual-networks-overview.md)가 필요합니다. VNet이 아직 없으면 Azure Virtual Network [빠른 시작](../virtual-network/quick-create-portal.md) 중 하나에 따라 이를 설정할 수 있습니다.

## <a name="add-a-private-endpoint-for-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 환경에 대한 프라이빗 엔드포인트 추가 

[Azure Portal](https://portal.azure.com)사용하는 경우 환경의 초기 설정의 일부로 Azure Time Series Insights Gen2 환경에 대한 프라이빗 엔드포인트를 사용하여 Private Link 켜거나, 이미 존재하는 환경에서 나중에 사용하도록 설정할 수 있습니다. 

이러한 생성 방법 중 하나는 사용자 환경에 대해 동일한 구성 옵션과 동일한 최종 결과를 제공합니다. 이 섹션에서는 각 작업을 수행하는 방법을 설명합니다.

>[!TIP]
> Azure Time Series Insights Gen2 환경 대신 Private Link 서비스를 통해 Private Link 엔드포인트를 설정할 수도 있습니다. 이 방법도 동일한 구성 옵션 및 동일한 최종 결과를 제공합니다.
>
> Private Link 리소스 설정에 대한 자세한 내용은 [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md) 또는 [PowerShell](../private-link/create-private-endpoint-powershell.md)에 대한 Private Link 설명서를 참조하세요.

### <a name="add-a-private-endpoint-during-environment-creation"></a>환경을 만드는 동안 프라이빗 엔드포인트 추가

이 섹션에서는 현재 만들어지는 Azure Time Series Insights Gen2 환경에서 프라이빗 엔드포인트를 사용하여 Private Link 사용하도록 설정합니다. 이 섹션에서는 만들기 프로세스의 네트워킹 단계에 중점을 둡니다. 새 Azure Time Series Insights Gen2 환경을 만드는 전체 연습은 [*방법: 환경 설정을 참조하세요.*](tutorial-set-up-environment.md)

Private Link 옵션은 환경 설정의 **네트워킹** 탭에 있습니다.

이 탭에서는 **연결 방법** 에 대해 **프라이빗 엔드포인트** 옵션을 선택하여 프라이빗 엔드포인트를 사용하도록 설정할 수 있습니다.

그러면 프라이빗 엔드포인트 세부 정보를 구성할 수 있는 **프라이빗 엔드포인트 연결** 이라는 섹션이 추가됩니다. 계속해서 **+ 추가** 단추를 선택합니다.

:::image type="content" source="media/private-links/create-instance-networking.png" alt-text="Time Series Insights Gen2에 대한 리소스 만들기 대화 상자의 네트워킹 탭을 보여 Azure Portal 스크린샷. 탭 이름, 연결 방법에 대한 프라이빗 엔드포인트 옵션 및 + 추가 단추를 강조 표시하여 새 프라이빗 엔드포인트 연결을 만듭니다." lightbox="media/private-links/create-instance-networking.png":::

그러면 새 프라이빗 엔드포인트의 세부 정보를 입력할 수 있는 페이지가 열립니다.

:::image type="content" source="media/private-links/create-private-endpoint.png" alt-text="프라이빗 엔드포인트 만들기 페이지를 보여 주는 Azure Portal의 스크린샷입니다. 아래에서 설명된 필드가 포함되어 있습니다.":::

1. **구독** 및 **리소스 그룹** 에 선택 항목을 입력합니다. **위치** 를 사용할 VNet과 동일한 위치로 설정합니다. 엔드포인트의 **이름을** 선택하고 대상 **하위 리소스에** *대해 환경* 또는 *tsiExplorer* 를 선택합니다.

1. 그런 후 엔드포인트 배포를 위해 사용할 **가상 네트워크** 및 **서브넷** 을 선택합니다.

1. 마지막으로 **비공개 DNS 영역과 통합** 할지 여부를 선택합니다. 기본값 **예** 를 사용하거나 이 옵션에 대한 도움말이 필요하면 포털 링크에 따라 [프라이빗 DNS 통합에 대해 자세히 알아볼 수 있습니다](../private-link/private-endpoint-overview.md#dns-configuration).

구성 옵션을 입력한 후 **확인** 을 눌러 완료합니다.

그러면 Azure Time Series Insights Gen2 환경 설정의 **네트워킹** 탭으로 돌아갑니다. 여기서 새 **엔드포인트는 프라이빗 엔드포인트 연결** 아래에 표시됩니다.

그런 다음, 아래쪽 탐색 단추를 사용하여 나머지 환경 설정을 계속 진행할 수 있습니다.

### <a name="add-a-private-endpoint-to-an-existing-environment"></a>기존 환경에 프라이빗 엔드포인트 추가

이 섹션에서는 이미 존재하는 Azure Time Series Insights Gen2 환경에 대한 프라이빗 엔드포인트를 사용하여 Private Link 사용하도록 설정합니다.

1. 먼저 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다. 포털 검색 창에서 이름을 검색하여 Azure Time Series Insights Gen2 환경을 불러올 수 있습니다.

1. 왼쪽 메뉴에서 **네트워킹(미리 보기)** 를 선택합니다.

1. **프라이빗 엔드포인트 연결** 탭으로 전환합니다.

1. **+ 프라이빗 엔드포인트** 를 선택하여 **프라이빗 엔드포인트 만들기** 설정을 엽니다.

    :::image type="content" source="media/private-links/add-private-endpoint.png" alt-text="Azure Time Series Insights Gen2 환경의 네트워킹(미리 보기) 페이지를 보여 Azure Portal 스크린샷. 프라이빗 엔드포인트 연결 탭이 강조 표시되고 + 프라이빗 엔드포인트 단추도 강조 표시됩니다." lightbox="media/private-links/add-private-endpoint.png":::

1.  **기본**  탭에서 **구독** 및 프로젝트의 **리소스 그룹** 을 선택하고 엔드포인트의 **이름** 및 **지역** 을 선택합니다. 지역은 사용 중인 VNet의 지역과 동일해야 합니다.

    :::image type="content" source="media/private-links/create-private-endpoint-2.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 첫 번째(기본) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **다음: 리소스 >** 단추를 선택하여 다음 탭으로 이동합니다.

1. **리소스** 탭에서 이 정보를 입력하거나 선택합니다. 
    * **연결 방법:** **내 디렉터리의 Azure 리소스에 커넥트** 선택하여 Azure Time Series Insights Gen2 환경을 검색합니다.
    * **구독**: 구독을 입력합니다.
    * **리소스 종류:** **Microsoft.TimeSeriesInsights/environments를 선택합니다.**
    * **리소스:** Azure Time Series Insights Gen2 환경의 이름을 선택합니다.
    * **대상 하위 리소스:** **환경** 또는 **tsiExplorer** 를 선택합니다.

    :::image type="content" source="media/private-links/create-private-endpoint-3.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 두 번째(리소스) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **다음: 연결 >** 단추를 선택하여 다음 탭으로 이동합니다.    

1.  **구성** 탭에서 다음 정보를 입력하거나 선택합니다.
    * **가상 네트워크**: 가상 네트워크를 선택합니다.
    * **서브넷**: 가상 네트워크에서 서브넷을 선택합니다.
    * **프라이빗 DNS 영역과 통합**: **프라이빗 DNS 영역과 통합** 여부를 선택합니다. 기본값 **예** 를 사용하거나 이 옵션에 대한 도움말이 필요하면 포털 링크에 따라 [프라이빗 DNS 통합에 대해 자세히 알아볼 수 있습니다](../private-link/private-endpoint-overview.md#dns-configuration).
    **예** 를 선택하면 기본 구성 정보를 그대로 둘 수 있습니다.

    :::image type="content" source="media/private-links/create-private-endpoint-4.png" alt-text="프라이빗 엔드포인트 만들기 대화 상자의 세 번째(구성) 탭을 보여 주는 Azure Portal의 스크린샷입니다. 위에서 설명된 필드가 포함되어 있습니다.":::

    완료되었으면 **검토 + 만들기** 단추를 선택하여 설정을 완료할 수 있습니다. 

1. **검토 + 만들기** 탭에서 선택 항목을 검토한 후  **만들기** 단추를 선택합니다. 

엔드포인트 배포가 완료되면 Azure Time Series Insights Gen2 환경의 프라이빗 엔드포인트 연결에 표시됩니다.

>[!TIP]
> Azure Portal의 Private Link 센터에서도 엔드포인트를 확인할 수 있습니다.

## <a name="disable--enable-public-network-access-flags"></a>공용 네트워크 액세스 플래그 사용 안 함/사용

모든 공용 연결을 거부하고 프라이빗 엔드포인트를 통한 연결만 허용하여 네트워크 보안을 강화하도록 Azure Time Series Insights Gen2 환경을 구성할 수 있습니다. 이 작업은 **공용 네트워크 액세스 플래그** 를 사용하여 수행됩니다. 

이 정책을 사용하면 Private Link 연결에 대한 액세스만 제한할 수 있습니다. 공용 네트워크 액세스 플래그를 *사용* 안 함으로 설정하면 퍼블릭 클라우드에서 Azure Time Series Insights Gen2 환경 데이터 평면에 대한 모든 REST API 호출이 `403, Unauthorized` 를 반환합니다. 또는 정책이 *사용 안 함* 으로 설정되었고 프라이빗 엔드포인트를 통해 요청을 수행하면 API 호출이 성공합니다.

[Azure Portal](https://portal.azure.com)공용 네트워크 액세스를 사용하지 않도록 설정하거나 사용하려면 포털을 열고 Azure Time Series Insights Gen2 환경으로 이동합니다.

1. 왼쪽 메뉴에서 **네트워킹(미리 보기)** 를 선택합니다.

1. **공용 액세스** 탭에서 **공용 네트워크 액세스 허용** 을 **사용 안 함** 또는 **모든 네트워크** 로 설정합니다.

    :::row:::
        :::column:::
            :::image type="content" source="media/private-links/network-flag-portal.png" alt-text="Azure Time Series Insights Gen2 환경의 네트워킹(미리 보기) 페이지를 보여 Azure Portal 스크린샷. 공용 액세스 탭이 강조 표시되고 공용 네트워크 액세스를 허용할지 여부를 선택하는 옵션도 강조 표시됩니다." lightbox="media/private-links/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Private Link에 대해 자세히 알아보기: 
* [*Azure Private Link 서비스는 무엇입니까?*](../private-link/private-link-service-overview.md)