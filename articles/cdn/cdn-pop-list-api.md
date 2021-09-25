---
title: Azure CDN에 대한 현재 POP IP 목록 검색 | Microsoft Docs
description: REST API를 사용하여 POP 서버를 가져오는 방법을 알아봅니다. POP 서버는 Azure Content Delivery Network 엔드포인트와 연결된 원본 서버에 요청합니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2021
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 65bb0649f5fed3615887d750b95d2b4ce91216fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626060"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Azure CDN에 대한 현재 POP IP 목록 검색

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Azure CDN에 대한 현재 Verizon POP IP 목록 검색

Verizon의 POP(point of presence) 서버용 IP 집합을 검색하려면 REST API를 사용할 수 있습니다. 이러한 POP 서버는 Verizon 프로필에서 Azure CDN(Content Delivery Network) 엔드포인트(**Verizon에서 Azure CDN 표준** 또는 **Verizon에서 Azure CDN 프리미엄**)와 연결된 원본 서버에 요청합니다. 이 IP 집합은 Pop에 요청할 때 클라이언트가 볼 수 있는 IP와 다른지 확인합니다. 

POP 목록을 검색하기 위한 REST API 작업의 구문은 [에지 노드 - 목록](/rest/api/cdn/edge-nodes/list)을 참조합니다.

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Azure CDN에 대한 현재 Microsoft POP IP 목록 검색

Microsoft Azure CDN의 트래픽만 허용하도록 애플리케이션을 잠그려면 백 엔드에 대한 IP ACL을 설정해야 합니다. Microsoft Azure CDN에서 보낸 헤더 'X-Forwarded-Host'에 대해 허용되는 값 집합을 제한할 수도 있습니다. 이러한 단계는 아래에 자세히 설명되어 있습니다.

백 엔드에 IP ACL을 구성하여 Microsoft Azure CDN의 백 엔드 IP 주소 공간 및 Azure 인프라 서비스의 트래픽만 허용합니다. 

Microsoft의 Azure CDN과 함께 Azure Front Door [서비스 태그](../virtual-network/service-tags-overview.md)를 사용하여 Microsoft의 백 엔드 IP 범위를 구성합니다. 전체 목록은 Microsoft 서비스용 [IP 범위 및 서비스 태그](https://www.microsoft.com/en-us/download/details.aspx?id=56519)를 참조하세요.

## <a name="typical-use-case"></a>일반적인 사용 사례

보안을 위해 이 IP 목록을 사용하여 원본 서버에 대한 요청을 유효한 Verizon POP에서만 할 수 있도록 적용합니다. 예를 들어 누군가 CDN 엔드포인트의 원본 서버에 대한 호스트 이름 또는 IP 주소를 발견한 경우 원본 서버에 직접 요청을 할 수 있으므로 Azure CDN에서 제공하는 크기 조정 및 보안 기능은 바이패스합니다. 반환된 목록의 IP를 원본 서버에서 유일하게 허용된 IP로 설정하여 이 시나리오를 방지할 수 있습니다. 최신 POP 목록이 있는지 확인하려면 하루에 한 번 이상 검색합니다. 

## <a name="next-steps"></a>다음 단계

REST API에 대한 정보는 [Azure CDN REST API](/rest/api/cdn/)를 참조하세요.
