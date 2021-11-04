---
title: Microsoft의 Azure CDN에 대한 HTTP 헤더 디버그 | Microsoft Docs
description: 디버그 캐시 요청 헤더는 요청된 자산에 적용되는 캐시 정책에 대한 추가 정보를 제공합니다. 해당 헤더는 Microsoft의 Azure CDN에만 적용됩니다.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: duau
ms.openlocfilehash: 5253d700f271ed18f6e1eae5488184da8ed6db7f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450555"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Microsoft의 Azure CDN에 대한 HTTP 헤더 디버그
디버그 응답 헤더인 `X-Cache`는 콘텐츠가 제공된 CDN 스택의 계층에 대한 세부 정보를 제공합니다. 해당 헤더는 Microsoft의 Azure CDN에만 적용됩니다.

### <a name="response-header-format"></a>응답 헤더 형식

헤더 | 설명
-------|------------
X-Cache: TCP_HIT | 해당 헤더는 CDN 에지 캐시에서 콘텐츠를 제공할 때 반환됩니다. 
X-Cache: TCP_REMOTE_HIT | 해당 헤더는 CDN 지역 캐시(원본 방패 계층)에서 콘텐츠를 제공할 때 반환됩니다.
X-Cache: TCP_MISS | 캐시 누락이 있는 경우 해당 헤더가 반환되고 원본에서 콘텐츠가 제공됩니다.
X-Cache: PRIVATE_NOSTORE | 이 헤더는 Cache-Control 응답 헤더가 프라이빗 또는 저장소 없음으로 설정되어 요청을 캐시할 수 없는 경우 반환됩니다.
X-Cache: CONFIG_NOCACHE | 요청이 CDN 프로필에 캐시되지 않도록 구성된 경우 이 헤더가 반환됩니다.
X-Cache: 해당 없음 | 이 헤더는 서명된 URL 및 규칙 집합에서 요청을 거부할 때 반환됩니다.

Azure CDN에서 지원되는 HTTP 헤더에 대한 자세한 내용은 [Front Door-백 엔드](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend)를 참조하세요.
