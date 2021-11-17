---
title: 최소 권한 원칙을 사용하여 앱 보안 높이기
titleSuffix: Microsoft identity platform
description: 최소 권한 원칙이 애플리케이션의 보안, 해당 데이터 및 최소 권한 액세스를 구현하는 데 사용할 수 있는 Microsoft ID 플랫폼 기능을 향상시키는 데 어떻게 도움이 될 수 있는지 알아보세요.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: a7e504bfd13d89ba417067faf88cf17cf12e1d0b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786487"
---
# <a name="enhance-security-with-the-principle-of-least-privilege"></a>최소 권한 원칙을 사용하여 보안 강화

최소 권한 정보 보안 원칙은 사용자 및 애플리케이션에 해당 작업을 수행하는 데 필요한 데이터 및 작업에 대한 액세스 권한만 부여되어야 한다는 사실을 확인합니다.

애플리케이션의 공격 노출 영역을 줄이는 데 도움이 되는 지침을 따라야 합니다. 그러지 않으면 Microsoft ID 플랫폼 통합 애플리케이션에서 보안 위반[*블래스트 반경(blast radius)* ]에 따른 영향이 나타납니다.

## <a name="recommendations-at-a-glance"></a>권장 사항 한 눈에 보기

- *사용하지 않는* 권한 및 *줄일 수 있는* 권한을 취소하여 **과도 권한의** 애플리케이션을 방지합니다.
- ID 플랫폼의 **동의** 프레임워크를 사용하여 사용자가 보호된 데이터에 액세스하려는 앱의 요청에 동의하도록 요구할 수 있습니다.
- 모든 개발 단계에서 최소한의 권한으로 애플리케이션을 **빌드** 하세요.
- 배포된 애플리케이션을 주기적으로 **감사** 하여 과도한 권한이 있는 앱을 식별합니다.

## <a name="whats-an-overprivileged-application"></a>*과도한 권한* 이 있는 애플리케이션이란 무엇인가요?

**사용하지 않는** 또는 **줄일 수 있는** 권한이 부여된 모든 애플리케이션은 "과도한 권한"이 있는 것으로 간주됩니다. 사용하지 않는 권한 및 줄일 수 있는 권한은 앱이나 해당 사용자가 작업을 수행하는 데 반드시 필요하지는 않은 데이터 또는 작업에 대한 무단 또는 의도치 않은 액세스를 제공할 수 있습니다.

:::row:::
   :::column span="":::
      ### <a name="unused-permissions"></a>사용하지 않는 권한

        사용하지 않는 권한은 애플리케이션에 부여된 사용 권한이지만, 의도한 대로 사용될 때 해당 권한에 의해 노출되는 API 또는 작업이 앱에서 호출되지 않습니다.

        - **예**: 애플리케이션은 Microsoft Graph API를 호출하고 [Files.Read](/graph/permissions-reference) 권한을 활용하여 로그인한 사용자의 OneDrive에 저장된 파일 목록을 표시합니다. 그러나 앱에 [Calendars.Read](/graph/permissions-reference#calendars-permissions) 권한도 부여되었으나 이 앱은 달력 기능을 제공하지 않으며 캘린더 API를 호출하지 않습니다.

        - **보안 위험**: 사용하지 않는 권한은 *수평 권한 상승* 보안 위험을 야기합니다. 애플리케이션의 보안 취약성을 악용하는 엔터티는 사용하지 않는 권한을 사용하여 애플리케이션이 의도한 대로 사용될 때 일반적으로 지원 또는 허용하지 않는 API 또는 작업에 액세스할 수 있습니다.

        - **완화**: 애플리케이션에서 수행하는 API 호출에 사용되지 않는 모든 사용 권한을 제거합니다.
   :::column-end:::
   :::column span="":::
      ### <a name="reducible-permissions"></a>줄일 수 있는 권한

        줄일 수 있는 권한은 애플리케이션 및 해당 사용자에게 필요한 작업을 수행하는 데 필요한 액세스 권한을 그대로 제공하는 낮은 권한의 권한입니다.

        - **예**: 애플리케이션은 Microsoft Graph API를 호출하여 로그인한 사용자의 프로필 정보를 표시하지만 프로필 편집은 지원하지 않습니다. 예를 들어 앱에 [User.ReadWrite.All](/graph/permissions-reference#user-permissions) 권한이 부여되었다고 가정해 봅시다. 좀 더 낮은 권한의 *User.Read.All* 권한으로도 사용자 프로필 데이터에 대해 충분한 읽기 전용 액세스 권한을 부여하므로 *User.ReadWrite.All* 권한은 줄일 수 있는 권한으로 간주됩니다.

        - **보안 위험**: 줄일 수 있는 권한은 *수직 권한 상승* 보안 위험을 야기합니다. 애플리케이션의 보안 취약성을 악용하는 엔터티는 데이터에 대한 무단 액세스 또는 해당 엔터티의 역할에서 일반적으로 허용하지 않는 작업을 수행하기 위해 줄일 수 있는 권한을 사용할 수 있습니다.

        - **완화**: 애플리케이션의 의도된 기능을 계속 사용하도록 하면서 애플리케이션의 줄일 수 있는 각 권한을 최소 권한으로 바꿉니다.
   :::column-end:::
:::row-end:::

애플리케이션 또는 사용자가 필요한 작업을 수행하는 데 필요한 최소 액세스 권한을 포함하는 권한인 *충분한* 권한을 부여하여 사용하지 않는 권한 및 줄일 수 있는 권한으로 인한 보안 위험을 방지합니다.

## <a name="use-consent-to-control-access-to-data"></a>동의를 사용하여 데이터에 대한 액세스 제어

사용자가 빌드하는 대부분의 애플리케이션은 보호된 데이터에 액세스해야 하며 해당 데이터의 소유자가 해당 액세스에 [동의](application-consent-experience.md#consent-and-permissions)해야 합니다. Azure AD 테넌트의 *모든* 사용자에게 동의할 수 있는 테넌트 관리자 또는 액세스를 허용할 수 있는 애플리케이션 사용자를 비롯한 여러 역할 사용자가 동의할 수 있습니다.

사용자의 디바이스에서 실행되는 애플리케이션이 보호된 데이터에 대한 액세스를 요청할 때마다 앱은 보호된 데이터에 대한 액세스 권한을 부여하기 전에 사용자의 동의를 요청해야 합니다. 최종 사용자는 애플리케이션 진행을 위해 요청된 권한에 대한 동의를 허용하거나 거부해야 합니다.

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="앱 등록의 API 권한 창을 보여 주는 Azure Portal 스크린샷":::

## <a name="least-privilege-during-app-development"></a>앱 개발 중 최소 권한

애플리케이션을 빌드하는 개발자는 앱 및 사용자 데이터의 보안이 *자기 자신* 의 책임이라고 생각해야 합니다.

애플리케이션을 개발하는 동안 다음 지침을 준수하여 과도한 권한이 있는 앱을 빌드하는 것을 방지합니다.

- 애플리케이션에서 수행해야 하는 API 호출에 필요한 권한을 완전히 이해합니다.
- [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 앱이 수행해야 하는 각 API 호출에 대한 최소 권한을 이해합니다.
- 최소 권한부터 최대 권한 범위에서 해당 [권한](/graph/permissions-reference)을 찾습니다.
- 앱에서 사용 권한이 겹치는 API 호출을 수행하는 경우 모든 중복 권한 집합을 제거합니다.
- 권한 목록에서 최소 권한을 선택하여 애플리케이션에 최소 권한 집합만 적용합니다.

## <a name="least-privilege-for-deployed-apps"></a>배포된 앱에 대한 최소 권한

조직에서는 일반적인 비즈니스 운영에 영향을 주지 않도록 하기 위해 실행 중인 애플리케이션을 수정하는 것을 주저하는 경우가 많습니다. 그러나 조직에서는 앱의 과도한 권한으로 인해 발생하거나 심각해질 수 있는 보안 인시던트의 위험을 완화하는 일이 예약된 애플리케이션 업데이트와 같은 가치가 있다는 사실을 인식해야 합니다.

배포된 앱에 과도한 권한이 부여되거나 시간에 따라 권한이 과도하게 증가하는 것을 방지하기 위해 조직에서 다음과 같은 표준 사례를 만드세요.

- 애플리케이션에서 수행되는 API 호출을 평가합니다.
- 필요한 권한 및 최소 권한에 대해서는 [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 및 [Microsoft Graph](/graph/overview) 설명서를 사용합니다.
- 사용자 또는 애플리케이션에 부여되는 권한을 감사합니다.
- 최소 권한 집합으로 애플리케이션을 업데이트합니다.
- 권한을 정기적으로 검토하여 모든 권한이 여전히 적절한지 확인합니다.

## <a name="next-steps"></a>다음 단계

**보호된 리소스 액세스 및 동의**

보호된 리소스에 대한 액세스를 구성하는 방법 및 보호된 리소스 액세스에 대해 동의를 제공하는 사용자 환경에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Microsoft ID 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md)
- [Azure AD 애플리케이션 동의 환경 이해](../develop/application-consent-experience.md)

**제로 트러스트** - 조직의 예방적 [제로 트러스트 보안 전략](/security/zero-trust/)의 일환으로 여기에 설명된 최소 권한 방안을 채택하는 것이 좋습니다.
