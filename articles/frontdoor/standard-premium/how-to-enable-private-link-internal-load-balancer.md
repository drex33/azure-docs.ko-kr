---
title: 프라이빗 링크를 사용하여 내부 부하 분산 장치 원본에 Azure Front Door 프리미엄 연결
titleSuffix: Azure Private Link
description: Azure Front Door 프리미엄을 내부 부하 분산 장치에 연결하는 방법을 알아봅니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: duau
ms.openlocfilehash: e902c8d3a1662f83328ab39a85a11009c58da24b
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122606580"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>프라이빗 링크를 사용하여 내부 부하 분산 장치 원본에 Azure Front Door 프리미엄 연결

이 문서에서는 Azure Private Link 서비스를 사용하여 내부 부하 분산 장치 원본에 연결하도록 Azure Front Door 프리미엄 SKU를 구성하는 방법을 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

[프라이빗 링크 서비스](../../private-link/create-private-link-service-portal.md)를 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-private-link-to-an-internal-load-balancer"></a>내부 부하 분산 장치에 대한 Private Link를 사용하도록 설정
 
이 섹션에서는 Private Link 서비스를 Azure Front Door의 개인 네트워크에서 만든 프라이빗 엔드포인트에 매핑합니다. 

1. Azure Front Door 프리미엄 프로필의 *설정* 아래에서 **원본 그룹** 을 선택합니다.

1. 내부 부하 분산 장치에 대한 Private Link를 사용하도록 설정하려는 원본 그룹을 선택합니다.

1. **+ 원본 추가** 를 선택하여 내부 부하 분산 장치 원본을 추가합니다.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="내부 부하 분산 장치에 대한 프라이빗 링크를 사용하도록 설정하는 스크린샷":::

1. **Azure 리소스 선택** 에서 **내 디렉터리에서** 를 선택합니다. Azure Front Door 프리미엄에서 비공개로 연결할 사이트를 구성하려면 다음 설정을 선택하거나 입력합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 지역 | 원본과 동일하거나 가장 가까운 지역을 선택합니다. |
    | 리소스 유형 | **Microsoft.Network/privateLinkServices** 를 선택합니다. |
    | 리소스 | 내부 부하 분산 장치에 연결된 프라이빗 링크를 선택합니다. |
    | 대상 하위 리소스 | 비워 둡니다. |
    | 메시지 요청 | 메시지를 사용자 지정하거나 기본값을 선택합니다. |

1. 그런 다음, **추가** 를 선택한 다음, **업데이트** 를 선택하여 구성을 저장합니다.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-private-link-service"></a>Private Link 서비스에서 Azure Front Door 프리미엄 프라이빗 엔드포인트 연결 승인

1. Private Link 센터로 이동하고 **프라이빗 링크 서비스** 를 선택합니다. 그런 다음, 프라이빗 링크 이름을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="프라이빗 링크 목록의 스크린샷":::

1. *설정* 아래에서 **프라이빗 엔드포인트 연결** 을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="프라이빗 링크 개요 페이지의 스크린샷":::

1. Azure Front Door 프리미엄에서 *보류 중* 인 프라이빗 엔드포인트 요청을 선택한 다음, **승인** 을 선택합니다.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="프라이빗 링크에 대한 승인 보류 중 스크린샷":::

1. 승인되면 아래 스크린샷과 같이 표시됩니다. 연결이 완전히 설정되는 데 몇 분 정도 걸릴 수 있습니다. 이제 Azure Front Door 프리미엄에서 내부 부하 분산 장치에 액세스할 수 있습니다.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="승인된 프라이빗 링크 요청의 스크린샷":::

## <a name="next-steps"></a>다음 단계

[Private Link 서비스](../../private-link/private-link-service-overview.md)에 대해 알아봅니다.
