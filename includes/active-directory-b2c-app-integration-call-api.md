---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 3d4bb5ff840a2f4ee5c4c33e2cc51dfe440866a7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528152"
---
인증이 완료된 후 사용자는 보호된 웹 API를 호출하는 앱을 조작합니다. 웹 API는 [전달자 토큰](https://datatracker.ietf.org/doc/html/rfc6750) 인증을 사용합니다. 전달자 토큰은 앱이 Azure AD B2C에서 가져온 액세스 토큰입니다. 앱은 HTTPS 요청의 인증 헤더에 토큰을 전달합니다. 
    
```http
Authorization: Bearer <token>
```

액세스 토큰 범위가 웹 API 범위와 일치하지 않는 경우 인증 라이브러리는 올바른 범위의 새 액세스 토큰을 얻게 됩니다.
