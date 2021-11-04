---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 0a29cf2d195e1fdd0bda03f718b1693fffaa5e9c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007797"
---
로그아웃 흐름에 포함되는 단계는 다음과 같습니다.

1. 앱에서 사용자가 로그아웃합니다.
1. 앱은 해당 세션 개체를 지우고, 인증 라이브러리는 해당 토큰 캐시를 지웁니다.
1. 앱이 사용자를 Azure AD B2C 로그아웃 엔드포인트로 이동하여 Azure AD B2C 세션을 종료합니다.
1. 사용자가 앱으로 다시 리디렉션됩니다.
