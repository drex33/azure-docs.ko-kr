---
title: Azure VMware Solution by CloudSimple - 서비스
description: 개요를 통해 CloudSimple 서비스에 대해 알아봅니다. 서비스를 만들면 노드를 구매하고, 노드를 예약하고, 프라이빗 클라우드를 만들 수 있습니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 940b9e67bb10c33a76f6b854d7cf03f3a7a6ee49
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315124"
---
# <a name="cloudsimple-service-overview"></a>CloudSimple 서비스 개요

CloudSimple 서비스에서는 Azure VMware Solution by CloudSimple을 사용할 수 있습니다.  서비스를 만들면 노드를 구매하고, 노드를 예약하고, 프라이빗 클라우드를 만들 수 있습니다.  CloudSimple 서비스를 사용할 수 있는 각 Azure 지역에서 CloudSimple 서비스를 만듭니다. 이 서비스는 Azure VMware Solution by CloudSimple의 경계 네트워크를 정의합니다. 경계 네트워크는 VPN, ExpressRoute 및 프라이빗 클라우드에 대한 인터넷 연결을 포함하는 서비스를 지원합니다.

## <a name="gateway-subnet"></a>게이트웨이 서브넷

게이트웨이 서브넷은 CloudSimple 서비스별로 필요하며 만들어진 지역에 대해 고유합니다. 게이트웨이 서브넷은 경계 네트워크를 만들 때 사용되며 /28 CIDR 블록이 필요합니다.  게이트웨이 서브넷 주소 공간은 고유해야 합니다. CloudSimple 환경과 통신하는 네트워크와 겹치지 않아야 합니다. CloudSimple과 통신하는 네트워크에는 온-프레미스 네트워크 및 Azure 가상 네트워크가 포함됩니다.  게이트웨이 서브넷은 일단 만들어지면 삭제할 수 없습니다.  서비스가 삭제되면 게이트웨이 서브넷이 제거됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 CloudSimple 서비스를 만드는](quickstart-create-cloudsimple-service.md) 방법을 알아봅니다.
