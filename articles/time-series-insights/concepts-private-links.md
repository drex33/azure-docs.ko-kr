---
title: Private Links - Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 Private Links 및 공용 액세스 제한 개요입니다.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: 58532e07b80e0084666e6847d5bd62313a51ee76
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207776"
---
# <a name="private-network-access-with-azure-private-link-preview"></a>Azure Private Link를 사용한 프라이빗 네트워크 액세스(미리 보기) 

[Azure Private Link](/azure/private-link/private-link-overview)   는 Azure [리소스(예: Azure Event Hubs, Azure Storage](/azure/event-hubs/event-hubs-about)및 Azure [Cosmos](/azure/storage/common/storage-introduction)DB)와 Azure 호스팅 고객 및 파트너 서비스에 Azure [VNet(Virtual Network)의](/azure/cosmos-db/introduction)프라이빗 엔드포인트를 통해 액세스할 수 있는 [서비스입니다.](/azure/virtual-network/virtual-networks-overview) 

마찬가지로 Time Series Insights 인스턴스에 대한 프라이빗 엔드포인트를 사용하여 가상 네트워크에 있는 클라이언트가 Private Link 통해 인스턴스에 안전하게 액세스할 수 있도록 할 수 있습니다. 

## <a name="about-the-private-endpoints"></a>프라이빗 엔드포인트 정보

프라이빗 엔드포인트는 Azure VNet 주소 공간의 IP 주소를 사용합니다. 프라이빗 네트워크의 클라이언트와 Time Series Insights 인스턴스 간의 네트워크 트래픽은 VNet 및 Microsoft 백본 네트워크의 Private Link 통과하여 공용 인터넷에 대한 노출을 제거합니다. 다음은 이 시스템을 시각적으로 표현한 것입니다. 

[![Time Series Private Links DNS](media/private-links/tsi-dns.png)](media/private-links/tsi-dns.png#lightbox)

고객은 VNET에서만 액세스할 수 있도록 공용 액세스를 위해 TSI 환경에 대한 액세스를 차단할 수도 있습니다. Time Series Insights 인스턴스에 대한 프라이빗 엔드포인트를 구성하면 Time Series Insights 인스턴스를 보호하고 공용 노출을 제거하고 VNet에서 데이터 반출을 방지할 수 있습니다. 

[![Time Series Private Links 네트워크](media/private-links/tsi-network-access.png)](media/private-links/tsi-network-access.png#lightbox)

프라이빗 엔드포인트가 활성화되고 공용 액세스가 제한되면 고객은 TSI 탐색기에 다른 주소를 사용하여 TSI 환경에 액세스해야 합니다. 해당 주소는 개요 섹션의 Azure Portal 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [TSI 환경에 대한 프라이빗 엔드포인트를 구성하는 방법을 참조하세요.](./how-to-private-links.md) 