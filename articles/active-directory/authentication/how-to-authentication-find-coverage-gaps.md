---
title: Azure Active Directory에서 관리자에 대한 강력한 인증 범위의 격차를 찾고 해결하기
description: Azure Active Directory에서 관리자에 대한 강력한 인증 범위의 격차를 찾고 해결하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: inbarckMS
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81d3ce6564c0ed4233f34bc43d661b2d9e1c1388
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505406"
---
# <a name="find-and-address-gaps-in-strong-authentication-coverage-for-your-administrators"></a>관리자에 대한 강력한 인증 범위의 격차를 찾고 해결하기

테넌트의 관리자에게 MFA(다단계 인증)를 요구하는 것은 테넌트의 보안을 강화하기 위해 수행할 수 있는 첫 번째 단계 중 하나입니다. 이 문서에서는 모든 관리자에게 다단계 인증이 적용되는지 확인하는 방법을 설명합니다.

## <a name="detect-current-usage-for-azure-ad-built-in-administrator-roles"></a>Azure AD 기본 제공 관리자 역할에 대한 현재 사용량 검색

[Azure AD 보안 점수](../fundamentals/identity-secure-score.md)는 테넌트의 **관리 역할에 대해 MFA 필요** 에 대한 점수를 제공합니다. 이 개선 작업은 전역 관리자, 보안 관리자, Exchange 관리자 및 SharePoint 관리자의 MFA 사용을 추적합니다. 

관리자에게 MFA 정책이 적용되는지 확인하는 다양한 방법이 있습니다. 

- 특정 관리자의 로그인 문제를 해결하려면 로그인 로그를 사용할 수 있습니다. 로그인 로그를 사용하면 특정 사용자에 대한 **인증 요구 사항** 을 필터링할 수 있습니다. **인증 요구 사항** 이 **단일 단계 인증** 인 로그인은 로그인에 필요한 다단계 인증 정책이 없음을 의미합니다.

  ![로그인 로그의 스크린샷](./media/how-to-authentication-find-coverage-gaps/auth-requirement.png)

  [MFA 요구 사항에 대한 세부 정보](../reports-monitoring/concept-sign-ins.md#authentication-details)는 **인증 세부 정보** 를 클릭합니다.
  
  ![인증 작업 세부 정보의 스크린샷](./media/how-to-authentication-find-coverage-gaps/details.png)

- 사용자 라이선스에 따라 사용할 정책을 선택하기 위해 [MFA 정책을 비교](concept-mfa-licensing.md#compare-multi-factor-authentication-policies)하고 조직에 적합한 단계를 확인하는 데 도움이 되는 새로운 MFA 사용 마법사가 있습니다. 이 마법사에는 지난 30일 동안 MFA로 보호된 관리자가 표시됩니다.

  ![다단계 인증 사용 마법사의 스크린샷](./media/how-to-authentication-find-coverage-gaps/wizard.png)

- 테넌트에서 관리자 역할이 있는 모든 사용자와 강력한 인증 상태를 나열하는 보고서를 프로그래밍 방식으로 만들려면 [PowerShell 스크립트](https://github.com/microsoft/AzureADToolkit/blob/main/src/Find-UnprotectedUsersWithAdminRoles.ps1)를 실행합니다. 이 스크립트는 모든 영구 및 적격 기본 제공 및 사용자 지정 역할 할당과 역할이 할당된 그룹을 열거하고 인증 방법 및 로그인 활동을 평가하여 MFA에 등록되지 않았거나 MFA로 로그인하지 않은 사용자를 찾습니다.

## <a name="enforce-multi-factor-authentication-on-your-administrators"></a>관리자에게 다단계 인증 적용

발견된 격차에 따라 관리자는 다음 방법 중 하나를 사용하여 다단계 인증을 사용해야 합니다.

- 관리자에게 Azure AD Premium에 대한 라이선스가 있는 경우 [조건부 액세스 정책을 만들어](tutorial-enable-azure-mfa.md) 관리자에게 MFA를 적용할 수 있습니다. 사용자 지정 역할에 있는 사용자에게 MFA를 요구하도록 이 정책을 업데이트할 수도 있습니다.  

- [MFA 사용 마법사](https://aka.ms/MFASetupGuide)를 실행하여 MFA 정책을 선택합니다.

- [PIM(Privileged Identity Management)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)에서 사용자 지정 또는 기본 제공 관리자 역할을 할당하는 경우 역할 활성화 시 다단계 인증이 필요합니다.

## <a name="use-passwordless-and-phishing-resistant-authentication-methods-for-your-administrators"></a>관리자에게 암호 없는 피싱 방지 인증 방법 사용

관리자가 다단계 인증을 시행하고 한동안 이를 사용한 후에는 강력한 인증에 대한 기준을 높이고 다음과 같은 암호 없는 피싱 방지 인증 방법을 사용해야 합니다. 

- [전화 로그인(Microsoft Authenticator 사용)](concept-authentication-authenticator-app.md)
- [FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- [비즈니스용 Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-overview)

[Azure AD 인증 방법](concept-authentication-methods.md)에서 이러한 인증 방법 및 보안 고려 사항에 대해 자세히 알아볼 수 있습니다.


