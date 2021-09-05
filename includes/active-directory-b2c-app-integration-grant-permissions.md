---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: b356480175c4b3da537b1e3e49fcdca676b2d6d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528151"
---
앱(앱 ID: 1) 권한을 부여하려면 다음 단계를 수행합니다. 

1. **앱 등록** 을 선택한 다음, 사용자가 만든 앱(앱 ID: 1)을 선택합니다.
1. **관리** 아래에서 **API 권한** 을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가** 를 선택합니다.
1. **내 API** 탭을 선택합니다.
1. 웹 애플리케이션에 액세스 권한을 부여할 API를 선택합니다(앱 ID: 2). 예를 들어 *my-api1* 을 입력합니다.
1. **권한** 에서 **작업** 을 펼친 다음, 앞에서 정의한 범위(예: *tasks.read* 및 *tasks.write*)를 선택합니다.
1. **권한 추가** 를 선택합니다.
1. **\<*your tenant name*>에 대한 관리자 동의 허용** 을 선택합니다.
1. **예** 를 선택합니다.
1. **새로 고침** 을 선택한 다음, 두 범위 모두 **상태** 아래에 "...에 대해 허용됨"이 표시되는지 확인합니다.
1. **구성된 권한** 목록에서 범위를 선택한 다음, 범위 전체 이름을 복사합니다. 

    ![부여된 읽기 액세스 권한을 보여주는 구성된 권한 창의 스크린샷.](./media/active-directory-b2c-app-integration-grant-permissions/get-azure-ad-b2c-app-permissions.png)  