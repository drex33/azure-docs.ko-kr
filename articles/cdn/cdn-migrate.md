---
title: Verizon Standard에서 Verizon Premium으로 Azure CDN 프로필 마이그레이션
description: Verizon Standard에서 Verizon Premium으로 프로필 마이그레이션에 대한 세부 정보를 알아봅니다.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 14396a95e7da343c18db433b89bf50faf391c0ca
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450531"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Standard Verizon에서 Premium Verizon으로 Azure CDN 프로필 마이그레이션

Azure CDN(Content Delivery Network) 프로필을 만들어서 엔드포인트를 관리하는 경우 Azure CDN은 사용자가 선택할 수 있는 네 가지 서로 다른 제품을 제공합니다. 다양한 제품 및 사용 가능한 기능에 대한 정보는 [Azure CDN 제품 기능 비교](cdn-features.md)를 참조하세요.

**Verizon의 Azure CDN Standard** 프로필을 만든 상태에서 이를 사용하여 CDN 엔드포인트를 관리하려는 경우 **Verizon의 Azure CDN Premium** 프로필로 업그레이드할 수 있는 옵션이 있습니다. 업그레이드하면 CDN 엔드포인트 및 모든 데이터는 유지됩니다. 

> [!IMPORTANT]
> **Verizon의 Azure CDN Premium** 프로필로 업그레이드하면 나중에 **Verizon의 Azure CDN Standard** 프로필로 다시 전환할 수는 없습니다.
> 

**Verizon의 Azure CDN Standard** 프로필을 업그레이드하려면 [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="profile-comparison"></a>프로필 비교
**Verizon의 Azure CDN Premium** 프로필은 **Verizon의 Azure CDN Standard** 프로필에 비해 다음과 같은 주요 차이점이 있습니다.
- [압축](cdn-improve-performance.md), [캐시 규칙](cdn-caching-rules.md) 및 [지역 필터링](cdn-restrict-access-by-country-region.md)과 같은 특정 Azure CDN 기능의 경우 Azure CDN 인터페이스를 사용할 수 없습니다. **관리** 단추를 통해 Verizon 포털을 사용해야 합니다.
- API: Standard Verizon과 달리 Premium Verizon 포털에서 액세스하는 이러한 기능을 제어하기 위해 API를 사용할 수 없습니다. 단, 엔드포인트 만들기/삭제, 캐시된 자산 제거/로드 및 사용자 지정 도메인 설정/해제와 같은 다른 일반적인 기능을 제어할 때는 API를 사용할 수 있습니다.
- 가격 책정: Premium Verizon은 데이터 전송에 대해 Standard Verizon과는 다른 가격 책정 구조를 제공합니다. 자세한 정보는 [Content Delivery Network 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

**Verizon의 Azure CDN Premium** 프로필에는 다음과 같은 추가 기능이 포함되어 있습니다.
- [토큰 인증](cdn-token-auth.md): 사용자가 토큰을 획득 및 사용하여 보안 리소스를 페치할 수 있습니다.
- [규칙 엔진](./cdn-verizon-premium-rules-engine.md): HTTP 요청을 처리하는 방법을 사용자 지정할 수 있습니다.
- 고급 분석 도구:
   - [자세한 HTTP 분석](cdn-advanced-http-reports.md)
   - [에지 성능 분석](cdn-edge-performance.md)
   - [실시간 분석](cdn-real-time-alerts.md)


## <a name="next-steps"></a>다음 단계
규칙 엔진에 대한 자세한 내용은 [Azure CDN 규칙 엔진 참조](./cdn-verizon-premium-rules-engine-reference.md)를 참조하세요.