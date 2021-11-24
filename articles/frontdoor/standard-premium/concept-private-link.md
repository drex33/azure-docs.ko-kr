---
title: Azure Front Door 표준/프리미엄(미리 보기)에서 Private Link를 사용하여 원본 보호
description: 이 페이지에서는 Private Link를 사용하여 원본에 연결을 보호하는 방법에 대한 정보를 제공합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 1a55ee40779c6e16a21f9b2f9b7aad11ed5561c3
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133030422"
---
# <a name="secure-your-origin-with-private-link-in-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄(미리 보기)에서 Private Link를 사용하여 원본 보호

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [Azure Front Door 문서](../front-door-overview.md)를 참조하세요.

## <a name="overview"></a>개요

[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure PaaS Services 및 Azure 호스트 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Front Door Premium SKU는 프라이빗 링크 서비스를 통해 원본에 연결할 수 있습니다. 애플리케이션을 프라이빗 VNet 또는 웹앱 및 스토리지 계정과 같은 PaaS 서비스 뒤에서 호스팅할 수 있으므로 원본에 공개적으로 액세스할 필요가 없습니다.

:::image type="content" source="../media/concept-private-link/front-door-private-endpoint-architecture.png" alt-text="Front Door 프라이빗 엔드포인트 아키텍처":::

Azure Front Door 프리미엄 구성에서 원본에 대한 Private Link를 사용하도록 설정하면 Front Door가 Front Door의 지역 프라이빗 네트워크에서 사용자를 대신하여 프라이빗 엔드포인트를 만듭니다. 이 엔드포인트는 Azure Front Door를 통해 관리됩니다. 원본에서 승인 메시지에 대한 Azure Front Door 프라이빗 엔드포인트 요청을 받게 됩니다. 요청을 승인한 후에는 Front Door의 가상 네트워크에서 개인 IP 주소가 할당되고, Azure Front Door와 원본 간의 트래픽이 Azure 네트워크 백본으로 설정된 프라이빗 링크를 통과합니다. 이제 Azure Front Door에서 제공할 때 원본으로 들어오는 트래픽이 보호됩니다.

:::image type="content" source="../media/concept-private-link/enable-private-endpoint.png" alt-text="프라이빗 엔드포인트 사용":::

> [!NOTE]
> Private Link 원본을 사용하도록 설정하고 프라이빗 엔드포인트 연결을 승인하면 연결이 설정되는 데 몇 분 정도 걸립니다. 이 시간 동안 원본에 대한 요청에는 Front Door 오류 메시지가 표시됩니다. 연결이 설정되면 오류 메시지가 사라집니다.

## <a name="limitations"></a>제한 사항

Azure 전면 도어 개인 끝점은 공개 미리 보기 중 미국 동부, 미국 서 부, 미국 중 남부, 영국 남부 및 일본 동부에서 사용할 수 있습니다.

가장 적절한 대기 시간을 위해 Front Door 프라이빗 엔드포인트를 사용하도록 선택할 때 항상 원본에 가장 가까운 Azure 지역을 선택해야 합니다.

Azure Front Door 프라이빗 엔드포인트는 플랫폼 및 Azure Front Door 구독에 따라 관리됩니다. Azure Front Door는 Front Door 프로필을 만드는 데 사용되는 동일한 고객 구독에 대한 프라이빗 링크 연결을 허용합니다.

## <a name="next-steps"></a>다음 단계

* Private Link 서비스를 통해 Azure Front Door 프리미엄을 웹앱에 연결하려면 [Private Link를 사용하여 Azure Front Door 프리미엄을 웹앱 원본에 연결](../../frontdoor/standard-premium/how-to-enable-private-link-web-app.md)을 참조하세요.
* 프라이빗 링크 서비스를 통해 Azure Front Door 프리미엄을 스토리지 계정에 연결하려면 [Private Link를 사용하여 Azure Front Door 프리미엄을 스토리지 계정 원본에 연결](../../frontdoor/standard-premium/how-to-enable-private-link-storage-account.md)을 참조하세요.
* Private Link 서비스를 통해 Azure Front Door 프리미엄을 내부 부하 분산 장치 원본에 연결하려면 [Private Link를 사용하여 Azure Front Door 프리미엄을 내부 부하 분산 장치 원본에 연결](../../frontdoor/standard-premium/how-to-enable-private-link-internal-load-balancer.md)을 참조하세요.
