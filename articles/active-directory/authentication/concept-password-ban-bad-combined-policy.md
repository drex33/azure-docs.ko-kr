---
title: Azure Active Directory에서 결합된 암호 정책 및 약한 암호 확인
description: Azure Active Directory에서 결합된 암호 정책 및 약한 암호 확인에 대해 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: justinha
author: sajiang
manager: daveba
ms.reviewer: sajiang
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21fb52047822df5c14e8b5a21c017a2e4ca5f1d6
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130138769"
---
# <a name="combined-password-policy-and-weak-password-check-in-azure-active-directory"></a>Azure Active Directory에서 결합된 암호 정책 및 약한 암호 확인

2021년 10월부터 암호 정책 규정 준수를 위한 Azure AD(Azure Active Directory)의 유효성 검사에는 [알려진 약한 암호](concept-password-ban-bad.md) 및 해당 변형에 대한 검사도 포함됩니다. 암호 정책 및 금지된 암호에 대한 결합 검사가 테넌트로 롤아웃되면 Azure AD 및 Office 365 관리 센터 사용자는 암호를 생성, 변경 또는 재설정할 때 차이점을 볼 수 있습니다. 이 항목에서는 Azure AD에서 확인한 암호 정책 조건에 대해 자세히 설명합니다. 

## <a name="azure-ad-password-policies"></a>Azure AD 암호 정책

암호 정책은 Azure AD에서 직접 만들고 관리하는 모든 사용자 및 관리자 계정에 적용됩니다. [약한 암호를 금지](concept-password-ban-bad.md)하고 잘못된 암호를 반복적으로 시도한 후 [계정을 잠그는](howto-password-smart-lockout.md) 매개 변수를 정의할 수 있습니다. 다른 암호 정책 설정은 수정할 수 없습니다.

EnforceCloudPasswordPolicyForPasswordSyncedUsers를 사용하도록 설정하지 않으면 Azure AD 암호 정책이 Azure AD Connect를 사용하여 온-프레미스 AD DS 환경에서 동기화된 사용자 계정에 적용되지 않습니다.

다음 Azure AD 암호 정책 요구 사항은 Azure AD에서 생성, 변경 또는 재설정되는 모든 암호에 적용됩니다. 요구 사항은 사용자 프로비저닝, 암호 변경 및 암호 재설정 흐름 중에 적용됩니다. 다음 설정은 변경이 가능하다고 명시되지 않는 한 변경할 수 없습니다.

| 속성 | 요구 사항 |
| --- | --- |
| 허용되는 문자 |대문자(A - Z)<br>소문자(a - z)<br>숫자(0 - 9)<br>기호:<br>- @ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ' , . ? / \` ~ " ( ) ; < ><br>- 공백 |
| 허용되지 않는 문자 | 유니코드 문자 |
| 암호 길이 |암호 필요<br>- 최소 8자<br>- 최대 256자</li> |
| 암호 복잡성 |암호는 다음 4개 중 3개가 필요합니다.<br>- 대문자<br>- 소문자<br>- 숫자 <br>- 기호<br> 참고: 교육 테넌트에는 암호 복잡성 검사가 필요하지 않습니다. |
| 최근에 사용되지 않은 암호 | 사용자가 암호를 변경하거나 재설정하는 경우 새 암호는 현재 또는 최근에 사용한 암호와 같을 수 없습니다. |
| [Azure AD 암호 보호](concept-password-ban-bad.md)에 의해 암호가 금지되지 않음 | 암호는 Azure AD 암호 보호에 대한 금지된 암호의 전역 목록 또는 조직과 관련한 사용자 지정 가능한 금지 암호 목록에 있을 수 없습니다. |

## <a name="password-expiration-policies"></a>암호 만료 정책

암호 만료 정책은 변경되지 않지만 완전성을 위해 이 항목에 포함되어 있습니다. ‘전역 관리자’ 또는 ‘사용자 관리자’는 [Windows PowerShell용 Microsoft Azure AD 모듈](/powershell/module/Azuread/)을 사용하여 사용자 암호가 만료되지 않도록 설정할 수 있습니다. 

> [!NOTE]
> 기본적으로 Azure AD Connect를 통해 동기화되지 않는 사용자 계정의 암호만 만료되지 않도록 구성할 수 있습니다. 디렉터리 동기화에 대한 자세한 내용은 [Azure AD와 AD 연결](../hybrid/how-to-connect-password-hash-synchronization.md#password-expiration-policy)을 참조하세요.

또한 PowerShell을 사용하여 만료되지 않는 구성을 제거하거나 만료되지 않도록 설정된 사용자 암호를 확인할 수도 있습니다.

Intune 및 Microsoft 365와 같은 ID 및 디렉터리 서비스에 Azure AD를 사용하는 다른 공급자에는 다음과 같은 만료 요구 사항이 적용됩니다. 

| 속성 | 요구 사항 |
| --- | --- |
| 암호 만료 기간(최대 암호 사용 기간) |<ul><li>기본값: **90** 일</li><li>값은 Windows PowerShell용 Azure Active Directory 모듈에서 `Set-MsolPasswordPolicy` cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료 알림(사용자에게 암호 만료 알림이 제공됨) |<ul><li>기본값: **14** 일(암호 만료 이전)</li><li>값은 `Set-MsolPasswordPolicy` cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료(암호가 만료되지 않도록 함) |<ul><li>기본값: **false**(암호의 만료 날짜가 있음을 나타냄).</li><li>`Set-MsolUser` cmdlet을 사용하여 개별 사용자 계정에 대한 값을 구성할 수 있습니다.</li></ul> |

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory에서 암호 정책 및 계정 제한](concept-sspr-policy.md)
- [Azure Active Directory 암호 보호를 사용하여 잘못된 암호 제거](concept-password-ban-bad.md)
