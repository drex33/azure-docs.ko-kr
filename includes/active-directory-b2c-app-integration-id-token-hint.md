---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f66eac86f4267a7b824eb551cdd877d3d34472a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802589"
---
## <a name="pass-an-id-token-hint"></a>ID 토큰 힌트 전달

신뢰 당사자 애플리케이션은 OAuth2 권한 부여 요청의 일부로 인바운드 JWT(JSON Web Token)를 보낼 수 있습니다.  인바운드 토큰은 사용자 또는 권한 부여 요청에 대한 힌트입니다. Azure AD B2C는 토큰의 유효성을 검사하고 클레임을 추출합니다.

인증 요청에 ID 토큰 힌트를 포함하려면 다음을 수행합니다.