---
title: 자습서 - Azure CDN 캐싱 규칙 설정 | Microsoft Docs
description: 이 자습서에서는 Azure CDN 전역 캐싱 규칙 및 사용자 지정 캐싱 규칙을 설정합니다.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: duau
ms.custom: mvc
ms.openlocfilehash: a95888225d1b8e695f8950081fbf4247e7959fc9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430955"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>자습서: Azure CDN 캐싱 규칙 설정

> [!NOTE] 
> 캐싱 규칙은 **Verizon의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준** 프로필에만 사용할 수 있습니다. **Microsoft의 Azure CDN** 의 프로필의 경우 [표준 규칙 엔진](cdn-standard-rules-engine-reference.md)을 사용해야 합니다. **Verizon의 Azure CDN Premium** 프로필의 경우 유사한 기능을 위해 **관리** 포털에서 [Verizon Premium 규칙 엔진](./cdn-verizon-premium-rules-engine.md)을 사용해야 합니다.
 

이 자습서에서는 Azure CDN(Content Delivery Network) 캐싱 규칙을 사용하여 전역적으로 그리고 사용자 지정 조건(예: URL 경로 및 파일 확장명)에 따라 기본 캐시 만료 동작을 설정하거나 수정하는 방법을 설명합니다. Azure CDN에는 두 가지 유형의 캐싱 규칙이 제공됩니다.
- 글로벌 캐싱 규칙: 엔드포인트에 대한 모든 요청에 영향을 주는 하나의 글로벌 캐싱 규칙을 프로파일의 각 엔드포인트에 설정할 수 있습니다. 전역 캐싱 규칙은 설정된 경우 모든 HTTP 캐시 지시문 헤더를 재정의합니다.

- 사용자 지정 캐싱 규칙: 프로필의 각 엔드포인트에 대해 하나 이상의 사용자 지정 캐싱 규칙을 설정할 수 있습니다. 사용자 지정 캐싱 규칙은 설정되면 특정 경로 및 파일 확장명과 일치하고, 순서대로 처리되며, 전역 캐싱 규칙을 재정의합니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> - 캐싱 규칙 페이지 열기.
> - 전역 캐싱 규칙 만들기.
> - 사용자 지정 캐싱 규칙 만들기.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서 단계를 완료하기 전에 먼저 CDN 프로필 및 하나 이상의 CDN 엔드포인트를 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure CDN 프로필 및 엔드포인트 만들기](cdn-create-new-endpoint.md)를 참조하세요.

## <a name="open-the-azure-cdn-caching-rules-page"></a>Azure CDN 캐싱 규칙 페이지 열기

1. [Azure Portal](https://portal.azure.com)에서 CDN 프로필을 선택한 다음, 엔드포인트를 선택합니다.

2. 설정 아래의 왼쪽 창에서 **캐싱 규칙** 을 선택합니다.

   ![CDN 캐싱 규칙 단추](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   **캐싱 규칙** 페이지가 나타납니다.

   ![CDN 캐싱 규칙 페이지](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>전역 캐싱 규칙 설정

전역 캐싱 규칙은 다음과 같이 만듭니다.

1. **전역 캐싱 규칙** 에서 **쿼리 문자열 캐시 동작** 을 **쿼리 문자열 무시** 로 설정합니다.

2. **캐싱 동작** 을 **누락된 경우 설정** 으로 설정합니다.
       
3. **캐시 만료 기간** 의 **일** 필드에 10일 입력합니다.

    전역 캐싱 규칙은 엔드포인트에 대한 모든 요청에 영향을 줍니다. 이 규칙은 원본 캐시 지시문 헤더가 있는 경우 해당 헤더를 사용합니다(`Cache-Control` 또는 `Expires`). 그렇지 않고, 지정되어 있지 않으면 캐시를 10일로 설정합니다. 

    ![전역 캐싱 규칙](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>사용자 지정 캐싱 규칙 설정

사용자 지정 캐싱 규칙은 다음과 같이 만듭니다.

1. **사용자 지정 캐싱 규칙** 에서 **일치 조건** 을 **경로** 로 설정하고 **일치 값** 을 `/images/*.jpg`로 설정합니다.
    
2. **캐싱 동작** 을 **재정의** 로 설정하고 **일** 필드에 30일 입력합니다.
       
    이 사용자 지정 캐싱 규칙은 엔드포인트의 `/images` 폴더에 있는 모든 `.jpg` 이미지 파일의 캐시 기간을 30일로 설정합니다. 원본 서버가 보낸 `Cache-Control` 또는 `Expires` HTTP 헤더를 재정의합니다.

    ![사용자 지정 캐싱 규칙](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서는 캐싱 규칙을 만들었습니다. 이러한 캐싱 규칙이 더 이상 필요 없으면 다음 단계를 수행하여 캐싱 규칙을 제거하면 됩니다.
 
1. CDN 프로필을 선택한 다음, 제거하려는 캐싱 규칙이 포함된 엔드포인트를 선택합니다.

2. 설정 아래의 왼쪽 창에서 **캐싱 규칙** 을 선택합니다.

3. **전역 캐싱 규칙** 에서 **캐싱 동작** 을 **설정 안 함** 으로 설정합니다.
 
4. **사용자 지정 캐싱 규칙** 에서 삭제하려면 규칙 옆에 있는 확인란을 선택합니다.

5. **삭제** 를 선택합니다.

6. 페이지 맨 위에서 **저장** 을 선택합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> - 캐싱 규칙 페이지 열기.
> - 전역 캐싱 규칙 만들기.
> - 사용자 지정 캐싱 규칙 만들기.

그 다음 문서로 넘어가서 추가 캐싱 규칙 설정을 구성하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [캐싱 규칙을 사용하여 Azure CDN 캐싱 동작 제어](cdn-caching-rules.md)