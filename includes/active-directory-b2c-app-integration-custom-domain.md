---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: e6ab23e9d98d2bb1dfea21a7b4f681dbd5be6e14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802781"
---
## <a name="use-a-custom-domain"></a>사용자 지정 도메인 사용

[사용자 지정 도메인](../articles/active-directory-b2c/custom-domain.md)을 사용하여 인증 URL을 완전히 브랜드화할 수 있습니다. 사용자 관점에서 사용자는 인증 프로세스 중에 Azure AD B2C b2clogin.com 도메인 이름으로 리디렉션되는 대신 도메인에 남아 있습니다.

인증 요청 URL의 B2C 테넌트 이름(contoso.onmicrosoft.com)을 테넌트 ID GUID로 대체하여 URL의 “b2c”에 대한 모든 참조를 제거할 수도 있습니다. 예를 들어, `https://fabrikamb2c.b2clogin.com/contoso.onmicrosoft.com/`을 `https://account.contosobank.co.uk/<tenant ID GUID>/`로 변경할 수 있습니다.
