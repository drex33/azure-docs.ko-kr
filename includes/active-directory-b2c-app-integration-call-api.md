---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: ebc113e993eadc41c2b1c58c9130908727101a6f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227766"
---
인증이 완료된 후 사용자는 보호된 웹 API를 호출하는 앱을 조작합니다. 웹 API는 [전달자 토큰](https://datatracker.ietf.org/doc/html/rfc6750) 인증을 사용합니다. 전달자 토큰은 앱이 Azure AD B2C에서 가져온 액세스 토큰입니다. 앱은 HTTPS 요청의 인증 헤더에 토큰을 전달합니다. 
    
```http
Authorization: Bearer <token>
```

액세스 토큰의 범위가 웹 API의 범위와 일치하지 않는 경우 인증 라이브러리는 올바른 범위의 새 액세스 토큰을 얻게 됩니다.
