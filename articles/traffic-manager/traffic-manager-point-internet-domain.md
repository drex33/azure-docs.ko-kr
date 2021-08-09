---
title: 인터넷 도메인이 Traffic Manager를 가리키도록 설정 - Azure Traffic Manager
description: 이 문서에서는 사용자의 회사 도메인 이름이 Traffic Manager 도메인 이름을 가리킵니다.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: duau
ms.openlocfilehash: e0e2acfb0ec0068dcd08ae660e397f65e039a665
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183747"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>회사 인터넷 도메인이 Azure Traffic Manager 도메인을 가리키도록 설정

Traffic Manager 프로필을 만들 때에는 Azure에서 해당 프로필에 DNS 이름을 자동으로 할당합니다. DNS 영역의 이름을 사용하려면 Traffic Manager 프로필의 도메인 이름으로 매핑하는 CNAME DNS 레코드를 만듭니다. Traffic Manager 프로필의 구성 페이지에 있는 **일반** 섹션에서 Traffic Manager 도메인 이름을 확인할 수 있습니다.

예를 들어 이름 `www.contoso.com`이 Traffic Manager DNS 이름 `contoso.trafficmanager.net`을 가리키도록 하려면 다음 DNS 리소스 레코드를 만듭니다.

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

이제 *www\.contoso.com* 에 대한 모든 트래픽 요청이 *contoso.trafficmanager.net* 으로 이동합니다.

> [!IMPORTANT]
> *contoso.com* 과 같은 두 번째 수준의 도메인이 Traffic Manager 도메인을 가리킬 수 없습니다. DNS 프로토콜 표준에서는 두 번째 수준 도메인 이름에 대한 CNAME 레코드를 허용하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Traffic Manager 라우팅 방법](traffic-manager-routing-methods.md)
* [Traffic Manager - 프로필 사용 안 함, 사용 또는 삭제](./traffic-manager-manage-profiles.md)
* [Traffic Manager - 엔드포인트 사용 안 함 또는 사용](./traffic-manager-manage-endpoints.md)