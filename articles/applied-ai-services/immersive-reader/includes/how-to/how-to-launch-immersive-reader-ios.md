---
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: e063e0ee745dc27a329665defa39fe69ca68f95a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253134"
---
## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory 인증에 대해 구성된 몰입형 판독기 리소스입니다. [다음 지침](../../how-to-create-immersive-reader.md)에 따라 설정하세요.  환경 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/)
* [몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>인증 자격 증명 구성

1. Xcode를 열고 **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj** 를 엽니다.
1. 상단 메뉴에서 **제품** > **스키마** > **스키마 편집** 을 선택합니다.
1. **실행** 보기에서 **인수** 탭을 선택합니다.
1. **환경 변수** 섹션에서 다음 이름과 값을 추가합니다. 몰입형 리더 리소스를 만들 때 지정된 값을 제공합니다.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

이 변경 내용에는 공개되어서는 안 되는 비밀이 있으므로 이 변경 내용을 소스 제어로 커밋하지 마세요.

## <a name="start-the-immersive-reader-with-sample-content"></a>샘플 콘텐츠를 사용하여 몰입형 리더 시작

Xcode에서 **Ctrl-R** 을 선택하여 프로젝트를 실행합니다.

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 리더 SDK 참조](../../reference.md)를 살펴봅니다.
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)에서 코드 샘플을 봅니다.
