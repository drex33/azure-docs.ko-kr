---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 7196d4596a3e67f1482fe2955a933fbf9efc8d47
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227656"
---
로그아웃 흐름에 포함되는 단계는 다음과 같습니다.

1. 앱에서 사용자가 로그아웃합니다.
1. 앱은 해당 세션 개체를 지우고, 인증 라이브러리는 해당 토큰 캐시를 지웁니다.
1. 앱이 사용자를 Azure AD B2C 로그아웃 엔드포인트로 이동하여 Azure AD B2C 세션을 종료합니다.
1. 사용자가 앱으로 다시 리디렉션됩니다.
