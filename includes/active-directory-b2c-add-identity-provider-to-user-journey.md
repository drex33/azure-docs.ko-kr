---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: kengaderdus
ms.openlocfilehash: 6759953415e204d50f5753b7ab4f24dfba47bc54
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066326"
---
## <a name="add-a-user-journey"></a>사용자 경험 추가 

이 시점에서 ID 공급자가 설정되었지만 로그인 페이지에서 아직 사용할 수는 없습니다. 자체 사용자 지정 사용자 경험이 없는 경우 기존 템플릿 사용자 경험의 복제본을 만듭니다. 그렇지 않으면 다음 단계를 계속 진행합니다. 

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
1. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
1. *TrustFrameworkExtensions.xml* 을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
1. 사용자 경험의 ID 이름을 바꿉니다. 예들 들어 `Id="CustomSignUpSignIn"`입니다.

## <a name="add-the-identity-provider-to-a-user-journey"></a>사용자 경험에 ID 공급자 추가 

이제 사용자 경험이 있으므로 사용자 경험에 새 ID 공급자를 추가합니다. 먼저 로그인 단추를 추가한 다음, 단추를 작업에 연결합니다. 이 작업은 이전에 만든 기술 프로필입니다.

1. 사용자 경험의 `Type="CombinedSignInAndSignUp"` 또는 `Type="ClaimsProviderSelection"`을 포함하는 오케스트레이션 단계 요소를 찾습니다. 일반적으로 첫 번째 오케스트레이션 단계입니다. **ClaimsProviderSelections** 요소에는 사용자가 로그인 할 수 있는 ID 공급자의 목록이 포함되어 있습니다. 요소의 순서는 사용자에게 표시되는 로그인 단추의 순서를 제어합니다. **ClaimsProviderSelection** XML 요소를 추가합니다. **TargetClaimsExchangeId** 의 값을 친숙한 이름으로 설정합니다.

1. 다음 오케스트레이션 단계에서 **ClaimsExchange** 요소를 추가합니다. **ID** 를 대상 클레임 교환 ID의 값으로 설정합니다. **TechnicalProfileReferenceId** 의 값을 이전에 만든 기술 프로필의 ID로 업데이트합니다.

다음 XML은 ID 공급자를 사용하여 사용자 경험의 처음 두 오케스트레이션 단계를 보여줍니다.