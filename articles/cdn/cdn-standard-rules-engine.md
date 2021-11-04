---
title: 규칙 엔진을 사용하여 표준 Azure CDN에서 HTTPS 적용 | Microsoft Docs
description: Microsoft Standard Azure CDN(Azure Content Delivery Network)용 규칙 엔진을 사용하여 Azure CDN에서 특정 형식의 콘텐츠 배달 차단, 캐싱 정책을 정의, HTTP 헤더 수정을 비롯하여 HTTP 요청을 처리하는 방법을 사용자 지정할 수 있습니다. 이 문서에서는 사용자를 HTTPS로 리디렉션하는 규칙을 만드는 방법을 알아봅니다.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/01/2019
ms.author: duau
ms.openlocfilehash: 7e3215c82acf476d84c58a6c6fb2bc4eeac13d99
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426433"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진 설정

이 문서에서는 Azure CDN(Azure Content Delivery Network)에 대한 표준 규칙 엔진을 설정하고 사용하는 방법을 설명합니다.

## <a name="standard-rules-engine"></a>표준 규칙 엔진

Azure CDN에 대한 표준 규칙 엔진을 사용하여 HTTP 요청을 처리하는 방법을 사용자 지정할 수 있습니다. 예를 들어, 규칙 엔진을 통해 콘텐츠 배달을 적용하여 특정 프로토콜을 사용하거나, 캐싱 정책을 정의하거나, HTTP 헤더를 수정할 수 있습니다. 이 문서에서는 사용자를 자동으로 HTTPS로 리디렉션하는 규칙을 만드는 방법을 보여 줍니다. 

> [!NOTE]
> 이 문서에서 설명하는 규칙 엔진은 Microsoft의 표준 Azure CDN에만 사용할 수 있습니다. 

## <a name="redirect-users-to-https"></a>사용자를 HTTPS로 리디렉션

1. Microsoft 프로필에서 Azure Content Delivery Network로 이동합니다.

1. **CDN 프로필** 페이지에서 규칙을 만들려는 엔드포인트를 선택합니다.
  
1. **규칙 엔진** 탭을 선택합니다.
   
    **규칙 엔진** 창이 열리고 사용 가능한 전역 규칙 목록이 표시됩니다. 
   
    [![Azure CDN 새 규칙 페이지](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > 여러 규칙이 나열된 순서는 규칙이 처리되는 방식에 영향을 줍니다. 규칙에 지정된 작업은 후속 규칙으로 덮어쓸 수 있습니다.
   >

1. **규칙 추가** 를 선택하고 규칙 이름을 입력합니다. 규칙 이름은 문자로 시작해야 하며 숫자 및 문자만 포함할 수 있습니다.

1. 규칙이 적용되는 요청의 유형을 식별하려면 일치 조건을 만듭니다.
    1. **조건 추가** 를 선택한 다음, **요청 프로토콜** 일치 조건을 선택합니다.
    1. **연산자** 에 대해 **같음** 을 선택합니다.
    1. **값** 에 **HTTP** 를 선택합니다.
   
   [![Azure CDN 규칙 일치 조건](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > **조건 추가** 드롭다운 목록에서 여러 일치 조건에서 선택할 수 있습니다. 일치 조건의 자세한 목록은 [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)을 참조하세요.
   
1. 일치 조건을 충족하는 요청에 적용할 작업을 선택합니다.
   1. **작업 추가** 를 선택한 다음, **URL 리디렉션** 을 선택합니다.
   1. **형식** 에 대해 **찾음(302)** 을 선택합니다.
   1. **프로토콜** 에서 **HTTPS** 를 선택합니다.
   1. 들어오는 값을 사용하도록 다른 모든 필드는 비워 둡니다.
   
   [![Azure CDN 규칙 작업](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > **작업 추가** 드롭다운 목록의 여러 작업 중에서 선택할 수 있습니다. 자세한 작업 목록은 [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)을 참조하세요.

6. 새 규칙을 저장하려면 **저장** 을 선택합니다. 이제 규칙을 사용할 수 있습니다.
   
   > [!IMPORTANT]
   > 규칙 변경이 Azure CDN을 통해 전파되기까지 최대 15분이 소요될 수 있습니다.
   >
   

## <a name="next-steps"></a>다음 단계

- [Azure CDN 개요](cdn-overview.md)
- [표준 규칙 엔진 참조](cdn-standard-rules-engine-reference.md)
- [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)
