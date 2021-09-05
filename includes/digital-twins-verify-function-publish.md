---
author: baanders
description: Azure 함수가 게시되었는지 확인하는 방법을 설명하는 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 543c905cc9e35d985c5b1962067690a0f895eab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535604"
---
### <a name="verify-the-publication-of-your-function"></a>함수의 게시를 확인합니다.

함수 게시 프로세스가 완료되면 다음 단계를 사용하여 게시에 성공했는지 확인할 수 있습니다.
 
1. [Azure Portal](https://portal.azure.com/)로 이동하여 자격 증명으로 로그인합니다.
2. 창 맨 위에 있는 검색 상자에서 함수 앱 이름을 검색하여 선택합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/search-function-app.png" alt-text="Azure Portal을 보여주는 스크린샷. 검색 필드에 함수 앱 이름을 입력합니다." lightbox="../articles/digital-twins/media/includes/azure-functions/search-function-app.png":::

3. **함수 앱** 페이지가 열리면 왼쪽 메뉴에서 **함수** 를 선택합니다. 목록에서 함수 이름을 찾아 성공적으로 게시되었는지 확인합니다.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/view-published-functions.png" alt-text="Azure Portal에서 게시된 함수를 보여주는 스크린샷." lightbox="../articles/digital-twins/media/includes/azure-functions/view-published-functions.png":::

    > [!Note] 
    > 게시된 함수 목록에 함수가 표시되기까지 몇 분 정도 기다리거나 페이지를 새로 고쳐야 할 수 있습니다.