---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 9de7912b426d659a03e8a7653c690cb16f83010e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007798"
---
인증이 완료된 후 사용자는 보호된 웹 API를 호출하는 앱을 조작합니다. 웹 API는 [전달자 토큰](https://datatracker.ietf.org/doc/html/rfc6750) 인증을 사용합니다. 전달자 토큰은 앱이 Azure AD B2C에서 가져온 액세스 토큰입니다. 앱은 HTTPS 요청의 인증 헤더에 토큰을 전달합니다. 
    
```http
Authorization: Bearer <token>
```

액세스 토큰의 범위가 웹 API의 범위와 일치하지 않는 경우 인증 라이브러리는 올바른 범위의 새 액세스 토큰을 얻게 됩니다.
