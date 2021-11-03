---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: adbcef246e8b027230ec436de9d9a1da838e523f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102736"
---
Language Studio를 사용 하려면 인증을 위한 언어에 대 한 Azure 리소스가 필요 합니다. 또한이 리소스를 사용 하 여 기능 REST Api 및 클라이언트 라이브러리를 호출할 수 있습니다. 시작 하려면 다음 단계를 따르세요. 

> [!IMPORTANT] 
> 사용자 지정 기능에 대 한 설치 프로세스와 요구 사항은 서로 다릅니다. 사용자 지정 기능 중 하나를 사용 하는 경우 보다 쉽게 시작 하려면 아래 링크 된 빠른 시작 문서 중 하나를 따르는 것이 좋습니다.  
> * [대화형 Language Understanding](../conversational-language-understanding/quickstart.md)
> * [사용자 지정 텍스트 분류](../custom-classification/quickstart.md)
> * [사용자 지정 명명 된 엔터티 인식 (NER)](../custom-named-entity-recognition/quickstart.md) 

1. Azure 구독을 만듭니다. [무료로 하나를 만들](https://azure.microsoft.com/free/ai/)수 있습니다. 

2. [Language Studio에 로그인](https://aka.ms/languageStudio)합니다. 처음 로그인 할 때 언어 리소스를 선택할 수 있는 창이 표시 됩니다. 

   :::image type="content" source="../media/language-resource-small.png" alt-text="Language Studio의 리소스 선택 화면을 보여 주는 스크린샷" lightbox="../media/language-resource.png":::

3. **새 언어 리소스 만들기를** 선택 합니다. 그런 다음 새 리소스에 대 한 정보 (예: 이름, 위치 및 리소스 그룹)를 입력 합니다.

    
    > [!TIP]
    > * Azure 리소스에 대 한 위치를 선택 하는 경우 대기 시간을 단축 하기 위해 가장 가까운 것을 선택 합니다.
    > * Azure에서 요청을 인증 하는 데 **관리 id** **옵션을** 설정 하는 것이 좋습니다.
    > * 무료 가격 책정 계층을 사용 하는 경우 Azure 무료 평가판 또는 서비스 크레딧이 만료 된 후에도 언어 서비스를 계속 사용할 수 있습니다. 

    :::image type="content" source="../media/create-new-resource-small.png" alt-text="언어 스튜디오에서 리소스 만들기 화면을 보여 주는 스크린샷" lightbox="../media/create-new-resource.png":::

4. **완료** 를 선택합니다. 리소스가 생성 되 고 언어 서비스에서 제공 하는 다양 한 기능을 사용해 볼 수 있습니다. 예를 들어 **연결 된 엔터티 찾기** 를 선택 합니다.

    :::image type="content" source="../media/language-studio-main-screen.png" alt-text="언어 스튜디오의 주 화면을 보여 주는 스크린샷" lightbox="../media/language-studio-main-screen.png":::


5. 이 기능에는 텍스트를 입력 하거나 파일을 업로드 하거나 텍스트 샘플을 선택 하 여 기능의 작동 방식을 보여 주는 섹션이 있습니다. 데모를 시도 하려면 리소스를 선택 하 고 [가격 책정 계층](https://aka.ms/unifiedLanguagePricing)에 따라 사용량을 확인 해야 합니다.

    :::image type="content" source="../media/language-studio-feature.png" alt-text="언어 스튜디오의 기능을 보여 주는 스크린샷" lightbox="../media/language-studio-feature.png":::

6. 텍스트를 보낸 후 JSON 응답과 함께 시각화를 볼 수 있습니다. 페이지 맨 아래에 방금 보낸 API 요청에 대 한 다음 단계와 말아 넘기기 명령이 표시 됩니다.

    :::image type="content" source="../media/language-studio-feature-result.png" alt-text="언어 스튜디오의 기능 결과를 보여 주는 스크린샷" lightbox="../media/language-studio-feature-result.png":::
