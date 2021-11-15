---
title: Azure Active Directory B2C의 하위 사용자 경험
description: Azure Active Directory B2C에서 사용자 지정 정책의 하위 사용자 경험 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 5cb3ebfbc3125e26f1b922ee43360c7527c7fb35
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130063966"
---
# <a name="sub-journeys"></a>하위 사용자 경험

하위 사용자 경험을 통해 사용자 경험 내에서 오케스트레이션 단계의 흐름을 구성하고 간소화할 수 있습니다. [사용자 경험](userjourneys.md)은 명시적 경로를 지정합니다. 이 경로를 통해 정책은 신뢰 당사자 애플리케이션이 사용자에 대해 원하는 클레임을 가져오도록 허용합니다. 이러한 경로를 통해 사용자를 가져와서 신뢰 당사자에게 표시할 클레임을 검색합니다. 즉, 사용자 경험은 Azure AD B2C ID 경험 프레임워크가 요청을 처리할 때 최종 사용자에게 적용되는 비즈니스 논리를 정의합니다. 사용자 경험은 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스로 표시됩니다. 오케스트레이션 단계의 [ClaimsExchange](userjourneys.md#claimsexchanges) 요소는 실행되는 단일 [기술 프로필](technicalprofiles.md)에 연결됩니다.

하위 사용자 경험은 사용자 경험 내의 어느 지점에서든 호출할 수 있는 오케스트레이션 단계를 그룹화한 것입니다. 하위 사용자 경험을 사용하여 재사용 가능한 단계 시퀀스를 만들거나 비즈니스 논리를 더 효과적으로 구현하기 위한 분기를 실행할 수 있습니다.

## <a name="user-journey-branching"></a>사용자 경험 분기

하위 사용자 경험과 사용자 경험은 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스로 표시되므로 하위 사용자 경험은 [사용자 경험](userjourneys.md)과 동일한 방식으로 작동합니다. 사용자 경험을 따로 호출할 수 있으며, 실행하려면 SendClaims 단계를 수행해야 합니다. 하위 사용자 경험은 사용자 경험의 구성 요소이며 따로 호출할 수 없으며 항상 사용자 경험에서 호출됩니다.

분기의 주요 구성 요소는 사용자 경험에서 비즈니스 논리를 더 효과적으로 처리하기 위한 것입니다. 일반적인 오케스트레이션 단계는 개별적으로 호출할 개별 요소로 그룹화됩니다. 하위 사용자 경험을 통해 사전 조건이 동일한 여러 오케스트레이션 단계를 결합하여 사용자 경험을 간소화할 수 있습니다. 하위 사용자 경험은 사용자 경험에서만 호출되며 다른 하위 사용자 경험을 호출해서는 안 됩니다.

하위 사용자 경험에는 다음의 두 가지 유형이 있습니다.

- **호출** - 호출자에게 제어를 반환합니다. 하위 사용자 경험을 실행한 다음, 사용자 경험 내에서 현재 실행 중인 오케스트레이션 단계로 제어가 반환됩니다.
- **전송** - 제어를 하위 사용자 경험(되돌릴 수 없는 분기)으로 전송합니다. 하위 사용자 경험에는 클레임을 신뢰 당사자 애플리케이션으로 다시 반환하는 SendClaims 단계가 있어야 합니다.

## <a name="example-scenarios"></a>예제 시나리오

### <a name="call-sub-journey"></a>하위 사용자 경험 호출

하위 사용자 경험 호출은 다음과 같은 시나리오에서 유용합니다.

- 나이 제한: 나이 제한의 경우 사용자 경험 간에 여러 구성 요소가 공유됩니다. 분기를 통해 공통적인 요소를 공유 가능한 구성 요소로 컴파일할 수 있습니다.  
- 보호자 동의: 분기를 통해 미성년자가 실행한 사용자 경험에서 클레임에 액세스할 수 있도록 하여 보호자 동의 설계를 편리하게 사용할 수 있으며, 동의를 필요로 하는 사용자를 찾은 후 보호자 동의 사용자 경험으로 분기할 수 있습니다. 
- 등록 후 로그인: 사용자가 이미 디렉터리에 있지만 실제로는 계정을 만들었다는 것을 잊었을 수 있는 시나리오를 고려해야 합니다. 이 같은 경우, 사용자가 입력한 자격 증명이 이미 있음을 사용자에게 알리고 사용자 경험을 다시 시작하도록 하는 대신에 해당 사용자를 위해 정책에 따라 등록 흐름을 로그인 흐름으로 전환하는 것이 바람직할 수 있습니다.  

### <a name="transfer-sub-journey"></a>하위 사용자 경험 전송

하위 사용자 경험 전송은 다음과 같은 시나리오에서 유용합니다.

- 블록 페이지를 표시하는 경우
- 요청을 하위 사용자 경험으로 라우팅하여 토큰을 실행 및 발급하는 A/B 테스트를 수행하는 경우

## <a name="adding-a-subjourneys-element"></a>SubJourneys 요소 추가

다음은 컨트롤을 사용자 경험으로 반환하는 `Call` 형식의 `SubJourney` 요소의 예시입니다.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

다음은 토큰을 신뢰 당사자 애플리케이션으로 다시 반환하는 `Transfer` 유형 `SubJourney` 요소의 예시입니다.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-sub-journey-step"></a>하위 사용자 경험 단계 호출

`InvokeSubJourney` 유형의 새 오케스트레이션 단계를 사용하여 하위 사용자 경험을 실행합니다. 다음은 이 오케스트레이션 단계의 모든 실행 요소를 보여 주는 예시입니다.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> 하위 사용자 경험이 다른 하위 사용자 경험을 호출해서는 안 됩니다.

## <a name="components"></a>구성 요소

정책에서 지원하는 사용자 경험을 정의하려면 정책 파일의 최상위 요소에 **SubJourneys** 요소를 추가합니다.

**SubJourneys** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| SubJourney | 1:n | 전체 사용자 흐름에 필요한 모든 구문을 정의하는 하위 사용자 경험입니다. |

**SubJourneys** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 사용자 경험에서 정책의 하위 사용자 경험을 참조하는 데 사용할 수 있는 하위 사용자 경험 식별자입니다. [후보](userjourneys.md#journeylist) 요소의 **SubJourneyReferenceId** 요소는 이 특성을 가리킵니다. |
| 유형 | 예 | 가능한 값은 `Call` 또는 `Transfer`입니다. 자세한 내용은 [사용자 경험 분기](#user-journey-branching)를 참조하세요.|

**SubJourney** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스입니다. 모든 사용자 경험은 순서대로 실행되는 정렬된 오케스트레이션 단계 목록으로 구성됩니다. 한 단계라도 실패하면 트랜잭션은 실패합니다. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

오케스트레이션 단계 요소의 전체 목록은 [UserJourneys](userjourneys.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[UserJourneys](userjourneys.md) 에 대한 자세한 정보
