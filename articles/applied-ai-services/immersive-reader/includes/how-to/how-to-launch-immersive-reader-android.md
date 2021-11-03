---
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: e080a0b7cc05b776e3085715e20d21bfb2545dd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253975"
---
## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory 인증에 대해 구성된 몰입형 판독기 리소스입니다. [다음 지침](../../how-to-create-immersive-reader.md)에 따라 설정하세요.  환경 속성을 구성할 때 여기서 만든 일부 값이 필요합니다. 나중에 참조할 수 있도록 세션 출력을 텍스트 파일로 저장합니다.
* [Git](https://git-scm.com/)
* [몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>인증 자격 증명 구성

1. Android Studio를 시작하고 **immersive-reader-sdk/js/samples/quickstart-java-android** 디렉터리(Java) 또는 **immersive-reader-sdk/js/samples/quickstart-kotlin** 디렉터리(Kotlin)에서 프로젝트를 엽니다.

1. **/assets** 폴더 내에 **env** 라는 파일을 만듭니다. 다음 이름과 값을 추가하고 적절한 값을 제공합니다. 이 파일에는 공개되어서는 안 되는 비밀이 있으므로 소스 제어로 커밋하지 마세요.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>샘플 콘텐츠를 사용하여 몰입형 리더 시작

AVD Manager에서 디바이스 에뮬레이터를 선택하고 프로젝트를 실행합니다.

## <a name="next-steps"></a>다음 단계

* [몰입형 리더 SDK](https://github.com/microsoft/immersive-reader-sdk) 및 [몰입형 리더 SDK 참조](../../reference.md)를 살펴봅니다.
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)에서 코드 샘플을 봅니다.