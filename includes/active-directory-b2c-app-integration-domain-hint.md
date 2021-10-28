---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 8363f7d13c2a991df031e3a2ca53efb8c1b11761
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037100"
---
## <a name="preselect-an-identity-provider"></a>ID 공급자 사전 선택

Facebook, LinkedIn 또는 Google 등과 같은 소셜 계정을 포함하도록 애플리케이션에 대한 로그인 과정을 구성한 경우 `domain_hint` 매개 변수를 지정할 수 있습니다. 이 쿼리 매개 변수는 로그인에 사용해야 하는 소셜 ID 공급자에 대한 힌트를 Azure AD B2C에 제공합니다. 예를 들어, 애플리케이션이 `domain_hint=facebook.com`을 지정하는 경우, 로그인 흐름을 통해 Facebook 로그인 페이지로 직접 이동됩니다. 

외부 ID 공급자로 사용자를 리디렉션하려면 다음을 수행합니다.
