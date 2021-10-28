---
title: Azure Active Directory B2C의 레거시 사용자 흐름 버전
description: Azure Active Directory B2C에서 사용할 수 있는 레거시 사용자 흐름 버전을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: ccbcbc067fd117288c43e3200c2ee6354d3201ce
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130043893"
---
# <a name="legacy-user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 레거시 사용자 흐름 버전

> [!IMPORTANT]
> 이 문서에서는 사용자 흐름에 대한 레거시 버전 관리 메서드를 설명합니다. 이를 통해 사용자 흐름의 V1(프로덕션 준비) 버전, 그리고 V 1.1 및 V2(미리 보기) 버전이 제공됩니다. Azure 공용 클라우드가 아닌 환경에서는 이 레거시 버전 관리 메서드를 계속 사용합니다. Azure 공용 클라우드에서 이 메서드는 [새로운 **권장** 및 **미리 보기** 버전](user-flow-versions.md)으로 대체됩니다.
> 
Azure AD B2C(Azure Active Directory B2C)의 사용자 흐름은 고객 ID 환경을 완벽하게 설명하는 공통 [정책](user-flow-overview.md)을 설정하는 데 도움이 됩니다. 이러한 환경에는 가입, 로그인, 암호 재설정 또는 프로필 편집이 포함됩니다.

아래 표에서 사용자 흐름이 **권장** 으로 확인되는 경우를 제외하고 *미리 보기* 상태인 것으로 간주됩니다. 프로덕션 애플리케이션에는 추천되는 사용자 흐름만 사용해야 합니다.

## <a name="v1"></a>V1

| 사용자 흐름 | 권장 | Description |
| --------- | ----------- | ----------- |
| 암호 재설정 | 예 | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |
| 프로필 편집 | 예 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| ROPC를 사용하여 로그인 | 예 | 로컬 계정이 있는 사용자가 네이티브 애플리케이션에서 직접 로그인할 수 있도록 합니다(브라우저 필요 없음). 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li></ul> |
| 로그인 | 예 | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>로그인 차단</li><li>암호 재설정 강제 적용</li><li>KMSI(로그인 유지)</ul><br>이 사용자 흐름을 사용하여 사용자 인터페이스를 사용자 지정할 수 없습니다. |
| 등록 | 예 | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |
| 등록 및 로그인 | 예 | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul>|

## <a name="v11"></a>V1.1

| 사용자 흐름 | 권장 | Description |
| --------- | ----------- | ----------- |
| 암호 재설정 v1.1 | 예 | 사용자가 메일을 확인한 후 새 암호를 선택할 수 있습니다(새 페이지 레이아웃 사용 가능). 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |

## <a name="v2"></a>V2

| 사용자 흐름 | 권장 | Description |
| --------- | ----------- | ----------- |
| v2 암호 재설정 | 예 | 사용자가 이메일을 확인한 후 새 암호를 선택할 수 있습니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>토큰 호환성 설정</li><li>[나이 제한](age-gating.md)</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |
| 프로필 편집 v2 | 예 | 사용자가 사용자 특성을 구성할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li></ul> |
| v2 로그인 | 예 | 사용자가 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](age-gating.md)</li><li>로그인 페이지 사용자 지정</li></ul> |
| v2 가입 | 예 | 사용자가 계정을 만들 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[토큰 수명](tokens-overview.md)</li><li>토큰 호환성 설정</li><li>세션 동작</li><li>[나이 제한](age-gating.md)</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |
| v2 가입 및 로그인 | 예 | 사용자가 계정을 만들거나 계정에 로그인할 수 있도록 합니다. 이 사용자 흐름을 사용하여 다음을 구성할 수 있습니다. <ul><li>[다단계 인증](multi-factor-authentication.md)</li><li>[나이 제한](age-gating.md)</li><li>[암호 복잡성 요구 사항](password-complexity.md)</li></ul> |