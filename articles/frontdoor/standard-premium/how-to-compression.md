---
title: Azure Front Door 표준/프리미엄에서 파일을 압축하여 성능 향상(미리 보기)
description: Azure Front Door에서 파일을 압축하여 파일 전송 속도를 개선하고 페이지 로드 성능을 향상시키는 방법을 알아봅니다.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: ca9c2e3b4e9873d4880385479b701d36c92238b0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750186"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄에서 파일을 압축하여 성능 향상(미리 보기)

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

파일 압축은 파일 전송 속도를 개선하고 페이지 로드 성능을 향상시키는 효과적인 방법입니다. 압축은 파일이 서버에서 전송되기 전에 파일 크기를 줄입니다. 파일 압축을 통해 대역폭 비용을 절감하고 보다 나은 사용자 환경을 제공할 수 있습니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
파일 압축을 사용하도록 설정하는 두 가지 방법이 있습니다.

- 원본 서버에서 압축을 사용하도록 설정합니다. Azure Front Door는 압축된 파일을 전달하고 이러한 파일을 요청하는 클라이언트에 배달합니다.
- Azure Front Door POP 서버에서 직접 압축을 사용하도록 설정(즉석에서 압축)합니다. 이 경우 Azure Front Door는 파일을 압축하여 최종 사용자에게 보냅니다.

> [!NOTE]
> 범위 요청은 다른 크기로 압축될 수 있습니다. Azure Front Door GET HTTP 요청에 대해 콘텐츠 길이 값이 동일해야 합니다. 클라이언트가 헤더를 사용하여 바이트 범위 요청을 보내면 `accept-encoding` 원본이 다른 콘텐츠 길이로 응답하면 Azure Front Door 503 오류를 반환합니다. 원본/Azure Front Door 압축을 사용하지 않도록 설정하거나 바이트 범위 요청에 대한 요청에서 제거할 규칙 집합 규칙을 만들 수 `accept-encoding` 있습니다.

> [!IMPORTANT]
> Azure Front Door 구성 변경이 네트워크 전체에 전파되는 데 최대 10분이 걸립니다. CDN 엔드포인트에 처음으로 압축을 설정하는 경우 압축 설정이 모든 POP까지 전파되도록 1~2시간 기다렸다가 문제를 해결합니다.

## <a name="enabling-compression"></a>압축을 사용하도록 설정

> [!Note]
> Azure Front Door에서 압축은 경로에서 **캐싱 사용** 의 일부입니다. **캐싱을 사용하도록 설정** 하는 경우에만 Azure Front Door에서 압축을 활용할 수 있습니다.

압축을 사용하도록 설정하는 방법은 다음과 같습니다.
* 빨리 만들기 중 - 캐싱을 사용하도록 설정하면서 압축을 사용하도록 설정할 수 있습니다.
* 사용자 지정 만들기 중 - 경로를 추가할 때 캐싱 및 압축을 사용하도록 설정합니다. 
* Endpoint Manager 경로에서.
* 최적화 페이지에서.

### <a name="enable-compression-in-endpoint-manager"></a>Endpoint Manager에서 압축 사용 설정

1. Azure Front Door 표준/프리미엄 프로필 페이지에서 **Endpoint Manager** 로 이동하고 압축을 사용하도록 설정할 엔드포인트를 선택합니다.

1. **엔드포인트 편집** 을 선택한 다음 압축을 사용하도록 설정할 **경로** 를 선택합니다. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Endpoint Manager 방문 페이지의 스크린샷." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. **캐싱 사용** 이 선택되어 있는지 확인한 다음 **압축 사용** 확인란을 선택합니다.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Endpoint Manager에서 압축 사용 설정.":::   

1. **업데이트** 를 선택하여 구성을 저장합니다.

### <a name="enable-compression-in-optimization"></a>최적화에서 압축 사용 설정

1. Azure Front Door 표준/프리미엄 프로필 페이지에서 설정 아래의 **최적화** 로 이동합니다. 엔드포인트를 펼쳐 경로 목록을 표시합니다. 

1. 압축을 사용하지 않도록 설정된 **경로** 옆의 점 세 개를 선택합니다. 그런 다음 **경로 구성** 을 선택합니다.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="최적화 페이지의 압축 사용 화면." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. **캐싱 사용** 이 선택되어 있는지 확인한 다음 **압축 사용** 확인란을 선택합니다.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Endpoint Manager에서 압축을 사용하도록 설정하는 스크린샷."::: 

1. **업데이트** 를 클릭합니다.

## <a name="modify-compression-content-type"></a>압축 콘텐츠 형식 수정

최적화 페이지에서 MIME 형식의 기본 목록을 수정할 수 있습니다.

1. Azure Front Door 표준/프리미엄 프로필 페이지에서 설정 아래의 **최적화** 로 이동합니다. 그런 다음 압축을 사용하도록 설정된 **경로** 를 선택합니다.

1. 압축을 사용하도록 설정된 **경로** 옆의 점 세 개를 선택합니다. 그런 다음 **압축 파일 형식 보기** 를 선택합니다.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="최적화 페이지의 스크린샷." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. 기본 형식을 삭제하거나 **추가** 를 선택하여 새 콘텐츠 형식을 추가합니다.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="파일 압축 사용자 지정 페이지의 스크린샷."::: 

1. **저장** 을 선택하여 압축 구성을 업데이트합니다.

## <a name="disabling-compression"></a>압축 사용 안 함

압축을 사용하지 않도록 설정하는 방법은 다음과 같습니다.
* Endpoint Manager에서 압축을 사용하지 않도록 설정합니다.
* 최적화에서 압축을 사용하지 않도록 설정합니다.

### <a name="disable-compression-in-endpoint-manager"></a>Endpoint Manager에서 압축을 사용하지 않도록 설정

1. Azure Front Door 표준/프리미엄 프로필 페이지에서 설정 아래의 **Endpoint Manager** 로 이동합니다. 압축을 사용하지 않도록 설정할 엔드포인트를 선택합니다.

1. **엔드포인트 편집** 을 선택한 다음 압축을 사용하지 않도록 설정할 **경로** 를 선택합니다. **압축 사용** 확인란의 선택을 취소합니다.

1. **업데이트** 를 선택하여 구성을 저장합니다.

### <a name="disable-compression-in-optimizations"></a>최적화에서 압축을 사용하지 않도록 설정

1. Azure Front Door 표준/프리미엄 프로필 페이지에서 설정 아래의 **최적화** 로 이동합니다. 그런 다음 압축을 사용하도록 설정된 **경로** 를 선택합니다.

1. 압축을 사용하도록 설정된 **경로** 옆의 점 세 개를 선택한 다음 경로 구성을 선택합니다.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="최적화 페이지에서 압축 사용 안함 스크린샷."::: 

1. **압축 사용** 확인란의 선택을 취소합니다.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="압축을 사용하지 않도록 설정하는 경로 업데이트 페이지의 스크린샷."::: 

1. **업데이트** 를 선택하여 구성을 저장합니다.

## <a name="compression-rules"></a>압축 규칙

Azure Front Door에서는 적격 파일만 압축됩니다. 압축이 가능하려면 파일이 다음 조건을 충족해야 합니다.
* MIME 형식이어야 합니다 
* 1KB보다 커야 합니다
* 8MB보다 작아야 합니다

이러한 프로필은 다음과 같은 압축 인코딩을 지원합니다.
* gzip(GNU zip)
* brotli 

요청에서 두 가지 이상의 압축 형식을 지원하는 경우 brotli 압축이 우선적으로 사용됩니다.

자산에 대한 요청이 gzip 압축을 지정하고 캐시의 요청 결과가 누락된 경우 Azure Front Door는 POP 서버에서 직접 자산의 gzip 압축을 수행합니다. 이후 압축된 파일은 캐시에서 제공됩니다.

원본에서 CTE(청크 분할 전송 인코딩)를 사용하여 압축된 데이터를 Azure Front Door POP로 보내는 경우 8MB보다 큰 응답 크기는 지원되지 않습니다. 

## <a name="next-steps"></a>다음 단계

- 첫 번째 [규칙 집합](how-to-configure-rule-set.md)을 구성하는 방법 알아보기
- [규칙 집합 일치 조건](concept-rule-set-match-conditions.md)에 대해 자세히 알아보기
- [Azure Front Door 규칙 집합](concept-rule-set.md)에 대해 자세히 알아보기
