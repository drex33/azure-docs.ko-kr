---
title: Azure Front Door - URL 다시 쓰기 | Microsoft Docs
description: 이 문서에서는 Azure Front Door가 구성된 경우 경로의 URL 다시 쓰기를 수행하는 방법을 설명합니다.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4aedc6e92b02cf81003ecf4b40a5096bf80c7448
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441049"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL 다시 쓰기(사용자 지정 전달 경로)
Azure Front Door는 백 엔드에 전달할 요청을 생성할 때 사용할 선택적 **사용자 지정 전달 경로** 를 구성하여 URL 다시 쓰기를 지원합니다. 기본적으로 사용자 지정 전달 경로를 제공하지 않으면 Front Door는 들어오는 URL 경로를 전달된 요청에서 사용된 URL에 복사합니다. 전달된 요청의 호스트 헤더는 선택한 백 엔드에 맞게 구성됩니다. 호스트 헤더의 역할 및 구성 방법은 [백 엔드 호스트 헤더](front-door-backend-pool.md#hostheader)를 참조하세요.

URL 다시 쓰기가 강력한 이유는 사용자 지정 전달 경로가 들어오는 경로에서 와일드 카드 경로와 일치하는 부분을 전달된 경로(해당 경로 세그먼트는 아래 예제에서 **녹색** 세그먼트임)에 복사하기 때문입니다.
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Azure Front Door URL 다시 쓰기":::

## <a name="url-rewrite-example"></a>URL 다시 쓰기 예제
다음 프런트 엔드 호스트 및 경로의 조합이 구성된 회람 규칙을 고려해 보세요.

| 호스트      | 경로       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

아래 표의 첫 번째 열은 들어오는 요청을 보여주고, 두 번째 열은 "가장 구체적으로" 일치하는 '경로'를 보여줍니다.  테이블의 세 번째 열과 후속 열은 구성된 **사용자 지정 전달 경로** 의 예입니다.

예를 들어 두 번째 행을 보시면, 들어오는 요청 `www.contoso.com/sub`와 관련하여 사용자 지정 전달 경로가 `/`이면 전달된 경로는 `/sub`입니다. 사용자 지정 전달 경로가 `/fwd/`이면 전달된 경로는 `/fwd/sub`입니다. 나머지 열에도 마찬가지 원리가 적용됩니다. 아래 경로에서 **강조된** 부분은 와일드 카드에서 일치하는 부분을 나타냅니다.

| 들어오는 요청       | 가장 구체적으로 일치하는 경로 | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |

> [!NOTE]
> Azure Front Door는 고정 경로에서 다른 고정 경로에 대한 URL 재작성만 지원합니다. 일치하지 않는 경로 유지는 Azure Front Door Standard/Premium SKU에서 지원됩니다. 자세한 내용은 [일치하지 않는 경로 유지](standard-premium/concept-rule-set-url-redirect-and-rewrite.md#preserve-unmatched-path)를 참조하세요.
> 

## <a name="optional-settings"></a>선택적 설정
특정 회람 규칙 설정에 대해 지정할 수 있는 추가 옵션 설정이 있습니다.

* **캐시 구성** - 해제하거나 지정하지 않으면 이 회람 규칙과 일치하는 요청이 캐시된 콘텐츠를 사용하려고 시도하는 대신, 항상 백 엔드에서 가져옵니다. 자세한 내용은 [Front Door를 통한 캐싱](front-door-caching.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
