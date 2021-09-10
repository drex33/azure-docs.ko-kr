---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 2bab69917caf103cd4e2f0b10e28b46acd8e48c3
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220295"
---
## <a name="prepopulate-the-sign-in-name"></a>로그인 이름 미리 채우기

사용자가 로그인하는 동안, 앱은 특정 사용자를 대상으로 지정할 수 있습니다. 앱이 사용자를 대상으로 하는 경우 권한 부여 요청에 `login_hint` 쿼리 매개 변수와 사용자의 로그인 이름을 지정할 수 있습니다. Azure AD B2C가 자동으로 로그인 이름을 채우며, 사용자는 암호만 입력하면 됩니다. 

로그인 이름을 미리 입력하려면 다음을 수행합니다.