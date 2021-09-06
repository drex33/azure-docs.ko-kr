---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 08/04/2021
ms.author: mimart
ms.openlocfilehash: 6b4ee09212bc62f43c583c73299ac33a842dc942
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528150"
---
1. 사용자가 만든 *my-api1* 애플리케이션(앱 ID: 2)을 선택하여 **개요** 페이지를 엽니다.

1. **관리** 에서 **API 표시** 를 선택합니다.
1. **애플리케이션 ID URI** 옆에 있는 **설정** 링크를 선택합니다. 기본값(GUID)을 고유한 이름(예: *tasks-api*)으로 바꾼 다음, **저장** 을 선택합니다. 
 
   웹 애플리케이션은 웹 API에 대한 액세스 토큰을 요청할 때 API에 대해 정의된 각 범위의 접두사로 이 URI를 추가해야 합니다.
1. **이 API에서 정의한 범위** 에서 **범위 추가** 를 선택합니다.
1. API에 대한 읽기 권한을 정의하는 범위를 만들려면 다음 값을 입력합니다.

    a. **범위 이름**: *tasks.read*  
    b. **관리자 동의 표시 이름**: *작업 API에 대한 읽기 권한*  
    다. **관리자 동의 설명**: *작업 API에 대한 읽기 권한을 허용합니다*.

1. **범위 추가** 를 선택합니다.

1. **범위 추가** 를 선택한 후, 다음 값을 입력하여 API에 대한 쓰기 권한을 정의하는 범위를 추가합니다. 

    a. **범위 이름**: *tasks.write*  
    b. **관리자 동의 표시 이름**: *작업 API에 대한 쓰기 권한*  
    다. **관리자 동의 설명**: *작업 API에 대한 쓰기 권한을 허용합니다*.
1. **범위 추가** 를 선택합니다.