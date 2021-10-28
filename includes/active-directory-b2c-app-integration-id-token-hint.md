---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: bf7295f4cc7b2010e7c700fbde25840f7a7f0df1
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037082"
---
## <a name="pass-an-id-token-hint"></a>ID 토큰 힌트 전달

신뢰 당사자 애플리케이션은 OAuth2 권한 부여 요청의 일부로 인바운드 JWT(JSON Web Token)를 보낼 수 있습니다.  인바운드 토큰은 사용자 또는 권한 부여 요청에 대한 힌트입니다. Azure AD B2C는 토큰의 유효성을 검사하고 클레임을 추출합니다.

인증 요청에 ID 토큰 힌트를 포함하려면 다음을 수행합니다.