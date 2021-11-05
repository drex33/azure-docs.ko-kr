---
title: 사용자 마이그레이션 방법
titleSuffix: Azure AD B2C
description: 사전 마이그레이션 또는 원활한 마이그레이션 방법을 사용하여 다른 ID 공급자의 사용자 계정을 Azure AD B2C로 마이그레이션합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 0cd0b976ec511070432538e99b7a8e20001a0156
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058153"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Azure AD B2C로 사용자 마이그레이션

다른 ID 공급자에서 Azure AD B2C(Azure Active Directory B2C)로 마이그레이션하려면 기존 사용자 계정을 마이그레이션해야 할 수도 있습니다. 여기서는 *사전 마이그레이션* 및 *원활한 마이그레이션* 의 두 가지 마이그레이션 방법에 대해 설명합니다. 어느 방법을 사용하든 Azure AD B2C에서 사용자 계정을 만들기 위해서는 [Microsoft Graph API](microsoft-graph-operations.md)를 사용하는 애플리케이션이나 스크립트를 작성해야 합니다.

이 동영상을 시청하여 Azure AD B2C 사용자 마이그레이션 전략 및 고려해야 할 단계에 대해 알아봅니다.

>[!Video https://www.youtube.com/embed/lCWR6PGUgz0]

## <a name="pre-migration"></a>사전 마이그레이션

사전 마이그레이션 흐름에서 마이그레이션 애플리케이션은 각 사용자 계정에 대해 다음 단계를 수행합니다.

1. 현재 자격 증명(사용자 이름 및 암호)을 포함하여 이전 ID 공급자에서 사용자 계정을 읽습니다.
1. 현재 자격 증명을 사용하여 Azure AD B2C 디렉터리에 해당 계정을 만듭니다.

다음 두 가지 상황에서 사전 마이그레이션 흐름을 사용합니다.

- 사용자의 일반 텍스트 자격 증명(사용자 이름 및 암호)에 액세스할 수 있습니다.
- 자격 증명은 암호화되지만 해독할 수 있습니다.

프로그래밍 방식으로 사용자 계정을 만드는 방법에 대한 자세한 내용은 [Microsoft Graph를 사용하여 Azure AD B2C 사용자 계정 관리](microsoft-graph-operations.md)를 참조하세요.

## <a name="seamless-migration"></a>원활한 마이그레이션

이전 ID 공급자의 일반 텍스트 암호에 액세스할 수 없는 경우 원활한 마이그레이션 흐름을 사용합니다. 예를 들면 다음과 같습니다.

- 암호는 단방향으로 암호화된 형식으로 저장됩니다(예: 해시 함수를 사용하는 경우).
- 암호는 액세스할 수 없는 방식으로 레거시 ID 공급자에 의해 저장됩니다. 예를 들어 ID 공급자가 웹 서비스를 호출하여 자격 증명의 유효성을 검사하는 경우에 그렇습니다.

원활한 마이그레이션 흐름은 계속해서 사용자 계정을 미리 마이그레이션해야 하지만 [사용자 지정 정책](user-flow-overview.md)을 사용하여 처음 로그인할 때 각 사용자의 암호를 설정하는 [REST API](api-connectors-overview.md)(사용자가 만듦)를 쿼리합니다.

원활한 마이그레이션 흐름은 *사전 마이그레이션* 및 *자격 증명 설정* 의 두 단계로 구성됩니다.

### <a name="phase-1-pre-migration"></a>1단계: 사전 마이그레이션

1. 마이그레이션 애플리케이션은 이전 ID 공급자에서 사용자 계정을 읽습니다.
1. 마이그레이션 애플리케이션은 Azure AD B2C 디렉터리에 해당 사용자 계정을 만들지만 생성되는 *임의의 암호를 설정* 합니다.

### <a name="phase-2-set-credentials"></a>2단계: 자격 증명 설정

계정에 대한 사전 마이그레이션이 완료되면 사용자 지정 정책 및 REST API는 사용자가 로그인할 때 다음을 수행합니다.

1. 입력한 이메일 주소에 해당하는 Azure AD B2C 사용자 계정을 읽습니다.
1. 부울 확장 특성을 평가하여 계정에 마이그레이션에 대한 플래그가 지정되었는지 여부를 확인합니다.
    - 확장 특성이 `True`를 반환하는 경우 REST API를 호출하여 레거시 ID 공급자에 대해 암호의 유효성을 검사합니다.
      - REST API에서 암호가 잘못된 것으로 확인하면 사용자에게 친숙한 오류를 반환됩니다.
      - REST API에서 암호가 올바른 것으로 확인하면 Azure AD B2C 계정에 암호를 쓰고 부울 확장 특성을 `False`로 변경합니다.
    - 부울 확장 특성이 `False`를 반환하는 경우 정상적으로 로그인 프로세스를 계속합니다.

사용자 지정 정책 및 REST API 예를 보려면 GitHub의 [원활한 사용자 마이그레이션 샘플](https://aka.ms/b2c-account-seamless-migration)을 참조하세요.

![사용자 마이그레이션에 대한 원활한 마이그레이션 방법의 순서도 다이어그램](./media/user-migration/diagram-01-seamless-migration.png)<br />*다이어그램: 원활한 마이그레이션 흐름*

## <a name="security"></a>보안

원활한 마이그레이션 방법은 사용자 지정 REST API를 사용하여 레거시 ID 공급자에 대한 사용자 자격 증명의 유효성을 검사합니다.

**무차별 암호 대입 공격에 대해 REST API를 보호해야 합니다.** 공격자는 결국 사용자의 자격 증명을 추측하기 위해 여러 암호를 제출할 수 있습니다. 이러한 공격을 차단하려면 로그인 시도 횟수가 특정 임계값을 초과할 때 REST API에 대한 요청 처리를 중지합니다. 또한 Azure AD B2C와 REST API 간의 통신을 보호합니다. 프로덕션을 위해 RESTful Api를 보호하는 방법을 알아보려면 [Secure RESTful API](secure-rest-api.md)를 참조하세요.

## <a name="user-attributes"></a>사용자 특성

레거시 ID 공급자의 모든 정보를 Azure AD B2C 디렉터리로 마이그레이션해야 하는 것은 아닙니다. 마이그레이션하기 전에 Azure AD B2C에 저장할 적절한 사용자 특성 집합을 식별합니다.

- Azure AD B2C에 **저장할 요소**
  - 사용자 이름, 암호, 이메일 주소, 전화 번호, 회원 번호/식별자.
  - 개인 정보 취급 방침 및 최종 사용자 라이선스 계약에 대한 동의 표시.
- Azure AD B2C에 **저장하면 안 되는 요소**
  - 신용 카드 번호, 주민등록번호, 의료 기록 또는 정부 또는 업계 규정 준수 본문이 규정하는 기타 데이터와 같은 중요한 데이터.
  - 마케팅 또는 통신 기본 설정, 사용자 동작 및 인사이트.

### <a name="directory-cleanup"></a>디렉터리 정리

마이그레이션 프로세스를 시작하기 전에 디렉터리를 정리합니다.

- Azure AD B2C에 저장할 사용자 특성 집합을 확인하고 필요한 항목만 마이그레이션합니다. 필요한 경우 사용자에 대한 추가 데이터를 저장하는 [사용자 지정 특성](user-flow-custom-attributes.md)을 만들 수 있습니다.
- 여러 인증 원본이 있는 환경에서 마이그레이션하는 경우(예: 각 애플리케이션에 고유한 사용자 디렉터리가 있는 경우) Azure AD B2C의 통합 계정으로 마이그레이션합니다.
- 여러 애플리케이션에 다른 사용자 이름이 있는 경우 ID 컬렉션을 사용하여 모두 Azure AD B2C 사용자 계정에 저장할 수 있습니다. 암호에 대해 사용자가 하나를 선택하고 디렉터리에 설정하도록 합니다. 예를 들어 원활한 마이그레이션에서는 선택한 암호만 Azure AD B2C 계정에 저장해야 합니다.
- 사용하지 않는 사용자 계정을 제거하거나 오래된 계정을 마이그레이션하지 마세요.

## <a name="password-policy"></a>암호 정책

마이그레이션하려는 계정이 Azure AD B2C에 의해 적용되는 [강력한 암호 강도](../active-directory/authentication/concept-sspr-policy.md)보다 약한 암호 강도를 사용하는 경우 강력한 암호 요구 사항을 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [암호 정책 속성](user-profile-attributes.md#password-policy-attribute)을 참조하세요.

## <a name="next-steps"></a>다음 단계

GitHub의 [azure-ad-b2c/user-migration](https://github.com/azure-ad-b2c/user-migration) 리포지토리에는 원활한 마이그레이션 사용자 지정 정책 예 및 REST API 코드 샘플이 포함되어 있습니다.

[원활한 사용자 마이그레이션 사용자 정책 및 REST API 코드 샘플](https://aka.ms/b2c-account-seamless-migration)
