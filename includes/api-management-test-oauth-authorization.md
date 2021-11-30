---
title: 포함 파일
description: 포함 파일
services: api-management
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 08/20/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 102e6f0ad062bb98d323ee871a0e8d9fb1656683
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133286614"
---
OAuth 2.0 권한 부여 서버를 구성하고 해당 서버를 사용하도록 API를 구성한 후에는 개발자 포털로 가서 API를 호출하여 테스트할 수 있습니다. 

1. Azure API Management 인스턴스 **개요** 페이지의 위쪽 메뉴에서 **개발자 포털을** 선택합니다.
1. 개발자 포털의 API에서 모든 작업을 찾습니다. 
1. **사용해 보세요를** 선택하여 개발자 콘솔로 가져옵니다.

1. 방금 추가한 권한 부여 서버에 해당하는 **권한 부여** 섹션의 새 항목을 참고합니다.

1. **권한 부여** 드롭다운 목록에서 권한 부여 코드를 선택합니다. 

    :::image type="content" source="../includes/media/api-management-test-oauth-authorization/select-authorization-code-authorization.png" alt-text="권한 부여 코드 권한 부여를 선택합니다.":::
1. 메시지가 표시되면 Azure AD 테넌트에서 로그인합니다. 
    * 계정에 이미 로그인한 경우 메시지가 표시되지 않을 수 있습니다.

1. 성공적으로 로그인한 후 `Authorization` 헤더가 Azure AD의 액세스 토큰과 함께 요청에 추가됩니다. 다음은 약식 샘플 토큰(Base64로 인코딩)입니다.

   ```
   Authorization: Bearer eyJ0eXAiOi[...]3pkCfvEOyA
   ```

1. **보내기** 를 선택하면 API를 성공적으로 호출할 수 있습니다.