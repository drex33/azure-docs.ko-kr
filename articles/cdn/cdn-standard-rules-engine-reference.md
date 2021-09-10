---
title: Azure CDN에 대한 표준 규칙 엔진 참조 | Microsoft Docs
description: Azure CDN(Azure Content Delivery Network)에 대한 표준 규칙 엔진의 일치 조건 및 동작에 대한 참조 설명서입니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 07/31/2021
ms.author: allensu
ms.openlocfilehash: 4fcb16f44aed90f483be80c64d31781f54203de6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528577"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진 참조

Azure CDN(Azure Content Delivery Network)에 대한 [표준 규칙 엔진](cdn-standard-rules-engine.md)에서 규칙은 하나 이상의 일치 조건 및 동작으로 구성됩니다. 이 문서에서는 Azure CDN에 대한 표준 규칙 엔진에서 사용할 수 있는 일치 조건 및 기능에 대해 자세히 설명합니다.

규칙 엔진은 특정 형식의 요청이 표준 Azure CDN에서 처리되는 방식을 최종적으로 결정하도록 설계되었습니다.

**규칙의 일반적인 용도**:

- 사용자 지정 캐시 정책을 재정의하거나 정의합니다.
- 요청을 리디렉션합니다.
- HTTP 요청 및 응답 헤더를 수정합니다.

## <a name="terminology"></a>용어

규칙 엔진에서 규칙을 정의하려면 [일치 조건](cdn-standard-rules-engine-match-conditions.md) 및 [동작](cdn-standard-rules-engine-actions.md)을 설정합니다.

 ![Azure CDN 규칙 구조](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

각 규칙에는 최대 10개의 일치 조건 및 5개의 동작이 있을 수 있습니다. 각 Azure CDN 엔드포인트에는 최대 25개의 규칙이 있을 수 있습니다. 

이 제한에는 기본 *전역 규칙* 이 포함됩니다. 전역 규칙에 일치 조건이 없습니다. 전역 규칙에 정의된 작업은 항상 트리거됩니다.

   > [!IMPORTANT]
   > 여러 규칙이 나열된 순서는 규칙이 처리되는 방식에 영향을 줍니다. 규칙에 지정된 작업은 후속 규칙으로 덮어쓸 수 있습니다.

## <a name="limits-and-pricing"></a>제한 및 가격 책정 

규칙 제한에 대해서는 [CDN 스케일링 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#content-delivery-network-limits)을 참조하세요. 규칙 엔진 가격 책정에 대해서는 [Content Delivery Network 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

## <a name="syntax"></a>구문

규칙에서 특수 문자를 처리하는 방법은 서로 다른 일치 조건 및 동작에서 텍스트 값을 처리하는 방법에 따라 달라집니다. 일치 조건 또는 동작은 다음 방법 중 하나로 텍스트를 해석할 수 있습니다.

- [리터럴 값](#literal-values)
- [와일드카드 값](#wildcard-values)


### <a name="literal-values"></a>리터럴 값

리터럴 값으로 해석되는 텍스트는 *% 기호를 제외* 한 모든 특수 문자를 규칙에서 일치해야 하는 값의 일부로 취급합니다. 예를 들어, `'*'`로 설정된 리터럴 일치 조건은 정확한 값 `'*'`가 발견될 경우에만 만족합니다.

백분율 기호는 URL 인코딩을 나타내는 데 사용됩니다(예: `%20`).

### <a name="wildcard-values"></a>와일드카드 값

현재 표준 규칙 엔진의 **UrlPath 일치 조건** 에 와일드카드 문자가 지원됩니다. \* 문자는 하나 이상의 문자를 나타내는 와일드카드입니다. 

## <a name="next-steps"></a>다음 단계

- [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)
- [표준 규칙 엔진을 사용하여 HTTPS 적용](cdn-standard-rules-engine.md)
- [Azure CDN 개요](cdn-overview.md)
