---
title: 자격 증명 공격 완화 - Azure AD B2C
titleSuffix: Azure AD B2C
description: 스마트 계정 잠금 기능을 포함하여 Azure Active Directory B2C의 자격 증명 공격(암호 공격)에 대한 검색 및 완화 기술에 대해 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 3ec563e05f21ca1e9697300710def5ad89b10d02
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042924"
---
# <a name="mitigate-credential-attacks-in-azure-ad-b2c"></a>Azure AD B2C에서 자격 증명 공격 완화

자격 증명 공격은 리소스에 대한 무단 액세스로 이어집니다. 사용자가 설정한 암호는 적절한 복잡성을 유지해야 합니다. Azure AD B2C에는 자격 증명 공격에 대한 완화 기술이 포함됩니다. 완화에는 무차별 암호 대입 자격 증명 공격 및 사전 자격 증명 공격 탐지가 포함됩니다. 다양한 신호를 사용하여 Azure AD B2C(Azure Active Directory B2C)는 요청의 무결성을 분석합니다. Azure AD B2C는 의도한 사용자와 해커 및 봇네트 간을 지능적으로 구분하도록 설계되었습니다.

Azure AD B2C는 좀 더 복잡한 계정 잠금 전략을 사용합니다. 계정은 요청의 IP 주소와 입력된 암호를 기반으로 잠깁니다. 또한 실패한 시도가 공격일 가능성에 따라 잠금 기간이 늘어납니다. 암호를 10번 잘못 시도하면(기본 시도 임계값) 1분 동안 잠금이 발생합니다. 계정 잠금이 해제된 후(즉, 잠금 기간이 만료되어 서비스에서 계정이 자동으로 잠금 해제된 후) 다음 로그인이 실패하면 다시 1분 동안 잠금이 발생하고 로그인이 실패할 때마다 계속 이렇게 됩니다. 동일하거나 유사한 암호를 반복적으로 입력하는 것은 여러 번의 로그인 실패로 간주되지 않습니다.

> [!NOTE]
> 이 기능은 [사용자 흐름, 맞춤 정책](user-flow-overview.md) 및 [ROPC](add-ropc-policy.md) 흐름에서 지원됩니다. 기본적으로 활성화되어 있으므로 사용자 흐름이나 사용자 지정 정책에서 구성할 필요가 없습니다.

## <a name="unlock-accounts"></a>계정 잠금 해제

처음 10회에 대한 잠금 기간은 1분입니다. 다음 10회 잠금 기간은 약간 더 길고 10회 잠금 기간마다 기간이 늘어납니다. 계정이 잠겨 있지 않은 상태에서 로그인에 성공하면 잠금 카운터가 0으로 다시 설정됩니다. 잠금 기간은 5시간까지 지속될 수 있습니다. 사용자는 잠금 기간이 만료될 때까지 기다려야 합니다. 그러나 사용자는 셀프 서비스 [암호 사용자 흐름](add-password-reset-policy.md)을 사용하여 잠금을 해제할 수 있습니다.

## <a name="manage-password-protection-settings"></a>암호 보호 설정 관리

잠금 임계값을 포함하여 암호 보호 설정을 관리하려면 다음을 수행합니다.

1. [Azure 포털](https://portal.azure.com)
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **보안** 에서 **인증 방법(미리 보기)** 를 선택한 후 **암호 보호** 를 선택합니다.
1. **사용자 지정 스마트 잠금** 에서 원하는 암호 보호 설정을 입력합니다.

   - **잠금 임계값**: 계정이 처음 잠기기 전에 허용되는 로그인 실패 횟수입니다. 잠금 후 첫 번째 로그인도 실패하면 계정이 다시 잠깁니다.
   - **잠금 기간(초)** : 각 잠금의 최소 기간(초)입니다. 계정이 반복적으로 잠기는 경우 이 지속 시간이 증가합니다.

       ![Azure AD 설정의 Azure Portal 암호 보호 페이지](./media/threat-management/portal-02-password-protection.png)
    <br />**암호 보호** 설정에서 잠금 임계값을 5로 설정합니다.

1. **저장** 을 선택합니다.

## <a name="testing-the-password-protection-settings"></a>암호 보호 설정 테스트

스마트 잠금 기능은 여러 요소를 사용하여 계정을 잠가야 하는 시기를 결정하지만 기본 요소는 암호 패턴입니다. 스마트 잠금 기능은 약간의 암호 변형을 세트로 간주하고 단일 시도로 계산됩니다. 예를 들면 다음과 같습니다.

- 12456! 및 1234567!과 같은 암호 (또는 newAccount1234 및 newaccount1234)는 너무 유사하므로 알고리즘이 이를 사람의 실수로 해석하고 한 번의 시도로 계산합니다.
- 12456! 및 ABCD2!와 같은 더 큰 패턴 변형 은 별도의 시도로 계산됩니다.

스마트 잠금 기능을 테스트할 때 입력하는 각 암호에 대해 고유한 패턴을 사용합니다. [https://passwordsgenerator.net/](https://passwordsgenerator.net/)와 같은 암호 생성 웹앱을 사용해보세요.

스마트 잠금 임계값에 도달하면 계정이 잠겨있는 동안 다음 메시지가 표시됩니다. **무단 사용을 방지하기 위해 계정이 일시적으로 잠겼습니다. 다음에 다시 시도하세요**. 오류 메시지는 [지역화](localization-string-ids.md#sign-up-or-sign-in-error-messages)될 수 있습니다.

> [!NOTE]
> 스마트 잠금을 테스트하는 경우 Azure AD 인증 서비스의 지리적으로 분산되고 부하가 분산된 특성으로 인해 로그인 요청은 다른 데이터 센터에서 처리될 수 있습니다. 이 시나리오에서 각 Azure AD 데이터 센터가 독립적으로 잠금을 추적하기 때문에 잠금을 유발하는 잠금 임계값 시도 횟수를 정의하는 것보다 더 많은 시간이 걸릴 수 있습니다. 사용자는 완전히 잠기기 전에 최대 (threshold_limit * datacenter_count)번의 잘못된 시도를 할 수 있습니다.

## <a name="viewing-locked-out-accounts"></a>잠긴 계정 보기

잠긴 계정에 대한 정보를 얻으려면 Active Directory [로그인 활동 보고서](../active-directory/reports-monitoring/concept-sign-ins.md)를 확인할 수 있습니다. **상태** 에서 **실패** 를 선택합니다. **로그인 오류 코드** 가 `50053`인 실패한 로그인 시도는 잠긴 계정을 나타냅니다.

![잠긴 계정을 보여 주는 Azure AD 로그인 보고서 섹션](./media/threat-management/portal-01-locked-account.png)

Azure Active Directory의 로그인 활동 보고서를 보는 방법에 대한 자세한 내용은 [로그인 활동 보고서 오류 코드](../active-directory/reports-monitoring/concept-sign-ins.md)를 참조하세요.

