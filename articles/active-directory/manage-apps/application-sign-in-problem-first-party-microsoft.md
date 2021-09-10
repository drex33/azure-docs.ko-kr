---
title: Microsoft 애플리케이션에 로그인하는 문제 | Microsoft Docs
description: Microsoft 365와 같은 Azure AD를 사용하여 자사 Microsoft 애플리케이션에 로그인할 때 직면하는 일반적인 문제 해결
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: davidmu
ms.reviewer: alamaral
ms.collection: M365-identity-device-management
ms.openlocfilehash: c52cd2062745bd3506f687492dff240d15bbcc1b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566895"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Microsoft 애플리케이션에 로그인하는 문제

Microsoft 애플리케이션(예: Exchange, SharePoint, Yammer 등)은 Single Sign-On을 위해 Azure AD와 통합하는 타사 SaaS 애플리케이션 또는 다른 애플리케이션과 약간 다른 방식으로 할당 및 관리됩니다.

사용자는 세 가지 방법으로 Microsoft 게시 애플리케이션에 대한 액세스 권한을 얻을 수 있습니다.

- Microsoft 365 또는 기타 유료 도구 모음에 있는 애플리케이션의 경우 사용자는 **라이선스 할당** 을 통해 해당 사용자 계정으로 직접 액세스가 부여되거나 그룹 기반 라이선스 할당 기능을 사용하여 그룹을 통해 액세스가 부여됩니다.

- Microsoft 또는 타사에서 누구나 자유롭게 사용하도록 게시한 애플리케이션의 경우 사용자는 **사용자 동의** 를 통해 액세스 권한을 부여받을 수 있습니다. 즉, 해당 Azure AD 회사 또는 학교 계정을 사용하여 애플리케이션에 로그인하고 해당 계정에 대한 일부 제한된 데이터 집합에 대한 액세스 권한을 가질 수 있습니다.

- Microsoft 또는 타사에서 누구나 자유롭게 사용하도록 게시한 애플리케이션의 경우 사용자도 **관리자 동의** 를 통해 액세스 권한을 부여받을 수 있습니다. 즉, 관리자는 전역 관리자 계정 사용하여 애플리케이션에 로그인하고 조직의 모든 사용자에게 액세스 권한을 부여하도록 조직의 모든 사용자가 애플리케이션을 사용할 수 있게 결정했습니다.

문제를 해결하려면 [고려할 애플리케이션 액세스의 일반적인 문제 영역](#general-problem-areas-with-application-access-to-consider)으로 시작하고 연습: Microsoft 애플리케이션 액세스를 해결하는 단계를 읽고 세부 정보를 얻습니다.

## <a name="general-problem-areas-with-application-access-to-consider"></a>고려할 애플리케이션 액세스의 일반적인 문제 영역

시작할 위치를 파악하는 경우 자세히 알아볼 수 있는 일반적인 문제 영역의 목록은 다음과 같습니다. 하지만 신속하게 연습: Microsoft 애플리케이션 액세스를 해결하는 단계 연습을 읽어보는 것이 좋습니다.

- [사용자의 계정과 관련된 문제](#problems-with-the-users-account)

- [그룹과 관련된 문제](#problems-with-groups)

- [조건부 액세스 정책과 관련된 문제](#problems-with-conditional-access-policies)

- [애플리케이션 동의와 관련된 문제](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Microsoft 애플리케이션 액세스 문제를 해결하는 단계

사용자가 Microsoft 애플리케이션에 로그인할 수 없는 경우 발생하는 몇 가지 일반적인 문제는 다음과 같습니다.

- 먼저 확인해야 할 일반적인 문제

  * 사용자가 로컬 애플리케이션 URL이 아닌 **올바른 URL** 에 로그인해야 합니다.

  * 사용자의 계정이 **잠겨 있지 않는지** 확인합니다.

  * Azure Active Directory에 **사용자의 계정이 존재해야** 합니다. [Azure Active Directory에 사용자의 계정이 존재하는지 확인](#problems-with-the-users-account)

  * 사용자의 계정이 로그인에 대해 **사용하도록 설정** 되어 있는지 확인합니다. [사용자의 계정 상태를 확인합니다](#problems-with-the-users-account).

  * 사용자의 **암호가 만료되거나 기억하지 못하는지** 확인합니다. [사용자의 암호 재설정](#reset-a-users-password) 또는 [셀프 서비스 암호 재설정 사용](../authentication/tutorial-enable-sspr.md)

  * **Multi-Factor Authentication** 에서 사용자 액세스를 차단하지 않는지 확인합니다. [사용자의 Multi-Factor Authentication 상태 확인](#check-a-users-multi-factor-authentication-status) 또는 [사용자의 인증 연락처 정보 확인](#check-a-users-authentication-contact-info)

  * **조건부 액세스 정책** 또는 **ID 보호** 정책이 사용자 액세스를 차단하지 않는지 확인합니다. [특정 조건부 액세스 정책 확인](#problems-with-conditional-access-policies) 또는 [특정 애플리케이션의 조건부 액세스 정책 확인](#check-a-specific-applications-conditional-access-policy) 또는 [특정 조건부 액세스 정책 사용 안 함](#disable-a-specific-conditional-access-policy)

  * 사용자의 **인증 연락처 정보** 를 최신으로 유지하여 Multi-Factor Authentication 또는 조건부 액세스 정책을 적용할 수 있도록 해야 합니다. [사용자의 Multi-Factor Authentication 상태 확인](#check-a-users-multi-factor-authentication-status) 또는 [사용자의 인증 연락처 정보 확인](#check-a-users-authentication-contact-info)

- 라이선스를 필요로 하는 **Microsoft** **애플리케이션**(예: office 365)의 경우 위의 일반 문제를 확인하면 확인할 몇 가지 특정 문제는 다음과 같습니다.

  * 사용자를 확인하거나 **라이선스를 할당합니다.** [사용자의 할당된 라이선스 확인](#check-a-users-assigned-licenses) 또는 [그룹의 할당된 라이선스 확인](#check-a-groups-assigned-licenses)

  * 라이선스가 **정적 그룹** **에 할당되는** 경우 해당 그룹의 **사용자가 구성원인지** 확인합니다. [사용자의 그룹 구성원 자격 확인](#check-a-users-group-memberships)

  * 라이선스가 **동적 그룹** **에 할당되는** 경우 **동적 그룹 규칙을 올바르게 설정했는지** 확인합니다. [동적 그룹의 구성원 자격 조건 확인](#check-a-dynamic-groups-membership-criteria)

  * 라이선스가 **동적 그룹** **에 할당되는** 경우 동적 그룹이 해당 구성원의 **처리를 완료했는지** 및 **사용자가 구성원인지** 확인합니다(시간이 걸릴 수 있음). [사용자의 그룹 구성원 자격 확인](#check-a-users-group-memberships)

  *  라이선스가 할당되었는지 확인하면 라이선스가 **만료되지 않았는지** 확인합니다.

  *  라이선스가 액세스하는 **애플리케이션에 적용되는지** 확인합니다.

- 라이선스를 필요로 하지 않는 **Microsoft** **애플리케이션** 의 경우 확인할 몇 가지 다른 항목은 다음과 같습니다.

  * 애플리케이션이 **사용자 수준 권한** 을 요청하는 경우(예: "이 사용자의 사서함에 액세스") 사용자가 애플리케이션에 로그인하고 **사용자 수준 동의 작업** 을 수행하여 애플리케이션이 해당 데이터에 액세스할 수 있도록 합니다.

  * 애플리케이션이 **관리자 수준 사용 권한** 을 요청하는 경우(예: "모든 사용자의 사서함 액세스 권한") 전역 관리자가 조직의 **모든 사용자를 대신하여 관리자 수준 동의 작업** 을 수행했는지 확인합니다.

## <a name="problems-with-the-users-account"></a>사용자의 계정과 관련된 문제

애플리케이션에 할당된 사용자와 관련된 문제로 인해 애플리케이션 액세스를 차단할 수 있습니다. 다음 몇 가지 방법으로 사용자 및 해당 계정 설정과 관련된 문제를 해결할 수 있습니다.

- [Azure Active Directory에 사용자의 계정이 존재하는지 확인](#check-if-a-user-account-exists-in-azure-active-directory)

- [사용자의 계정 상태 확인](#check-a-users-account-status)

- [사용자의 암호 다시 설정](#reset-a-users-password)

- [셀프 서비스 암호 재설정 사용](#enable-self-service-password-reset)

- [사용자의 Multi-Factor Authentication 상태 확인](#check-a-users-multi-factor-authentication-status)

- [사용자의 인증 연락처 정보 확인](#check-a-users-authentication-contact-info)

- [사용자의 그룹 구성원 자격 확인](#check-a-users-group-memberships)

- [사용자의 할당된 라이선스 확인](#check-a-users-assigned-licenses)

- [사용자에게 라이선스 할당](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Azure Active Directory에 사용자의 계정이 존재하는지 확인

사용자의 계정이 있는지를 확인하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 사용자** 를 선택합니다.

6. 관심이 있는 사용자를 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. 사용자 개체의 속성이 예상한 대로 표시되고 데이터가 누락되지 않았는지 확인합니다.

### <a name="check-a-users-account-status"></a>사용자의 계정 상태 확인

사용자의 계정 상태를 확인하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 사용자** 를 선택합니다.

6. 관심이 있는 사용자를 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **프로필** 을 선택합니다.

8. **설정** 아래에서 **로그인 차단** 이 **아니오** 로 설정되어야 합니다.

### <a name="reset-a-users-password"></a>사용자의 암호 다시 설정

사용자의 암호를 다시 설정하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 사용자** 를 선택합니다.

6. 관심이 있는 사용자를 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. 사용자 창 맨 위에 있는 **암호 재설정** 단추를 선택합니다.

8. 표시되는 **암호 재설정** 창에서 **암호 재설정** 단추를 선택합니다.

9. 사용자를 위한 **임시 암호** 또는 **새 암호 입력** 을 복사합니다.

10. 사용자에게 이 새 암호를 전달하고 다음 번에 Azure Active Directory에 로그인하는 동안 이 암호를 변경하도록 해야 합니다.

### <a name="enable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용

셀프 서비스 암호 재설정을 사용하려면 아래 배포 단계를 따르세요.

- [사용자가 Azure Active Directory 암호를 재설정할 수 있도록 설정](../authentication/tutorial-enable-sspr.md)

- [사용자가 Active Directory 온-프레미스 암호를 재설정하거나 변경하도록 설정](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>사용자의 Multi-Factor Authentication 상태 확인

사용자의 Multi-Factor Authentication 상태를 확인하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 사용자** 를 선택합니다.

6. 창 맨 위에 있는 **다단계 인증** 단추를 선택합니다.

7. **Multi-Factor Authentication 관리 포털** 을 일단 로드하면 **사용자** 탭인지 확인합니다.

8. 검색, 필터링 또는 정렬을 사용하여 사용자 목록에서 사용자를 찾습니다.

9. 사용자 목록에서 사용자를 선택하고 원하는 대로 Multi-Factor Authentication을 **사용**, **사용 안 함** 또는 **적용** 합니다.

   * **참고**: 사용자가 **강제된** 상태인 경우 일시적으로 **사용 안 함** 으로 설정하여 해당 계정으로 다시 돌아올 수 있도록 할 수 있습니다. 다시 돌아오면 해당 상태를 **사용** 으로 변경하여 다음 번에 로그인하는 도중 해당 연락처 정보를 다시 등록할 수 있습니다. 또는 [사용자의 인증 연락처 정보 확인](#check-a-users-authentication-contact-info)에 있는 단계를 수행하여 이 데이터를 확인하거나 설정할 수 있습니다.

### <a name="check-a-users-authentication-contact-info"></a>사용자의 인증 연락처 정보 확인

Multi-Factor Authentication, 조건부 액세스, ID 보호 및 암호 재설정에 사용되는 사용자의 인증 연락처 정보를 확인하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 사용자** 를 선택합니다.

6. 관심이 있는 사용자를 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **프로필** 을 선택합니다.

8. **인증 연락처 정보** 까지 스크롤합니다.

9. 필요에 따라 사용자 및 업데이트에 등록된 데이터를 **검토** 합니다.

### <a name="check-a-users-group-memberships"></a>사용자의 그룹 구성원 자격 확인

사용자의 그룹 구성원 자격을 확인하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 사용자** 를 선택합니다.

6. 관심이 있는 사용자를 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **그룹** 을 선택하여 사용자가 구성원으로 참여하는 그룹을 확인합니다.

### <a name="check-a-users-assigned-licenses"></a>사용자의 할당된 라이선스 확인

사용자의 할당된 라이선스를 확인하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 사용자** 를 선택합니다.

6. 관심이 있는 사용자를 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **라이선스** 를 선택하여 사용자가 현재 할당된 라이선스를 봅니다.

### <a name="assign-a-user-a-license"></a>사용자에게 라이선스 할당

사용자에게 라이선스를 할당하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 사용자** 를 선택합니다.

6. 관심이 있는 사용자를 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **라이선스** 를 선택하여 사용자가 현재 할당된 라이선스를 봅니다.

8. **할당** 단추를 선택합니다.

9. 사용 가능한 제품 목록에서 **하나 이상의 제품** 을 선택합니다.

10. **선택 사항** 제품을 상세하게 할당하려면 **할당 옵션** 항목을 클릭합니다. 완료되면 **확인** 을 클릭합니다.

11. **할당** 단추를 선택하여 이러한 라이선스를 이 사용자에게 할당합니다.

## <a name="problems-with-groups"></a>그룹과 관련된 문제

애플리케이션에 할당된 그룹과 관련된 문제로 인해 애플리케이션 액세스를 차단할 수 있습니다. 다음 몇 가지 방법으로 그룹 및 구성원 자격과 관련된 문제를 해결할 수 있습니다.

- [그룹의 구성원 자격 확인](#check-a-groups-membership)

- [동적 그룹의 구성원 자격 조건 확인](#check-a-dynamic-groups-membership-criteria)

- [그룹의 할당된 라이선스 확인](#check-a-groups-assigned-licenses)

- [그룹의 라이선스 다시 처리](#reprocess-a-groups-licenses)

- [그룹에 라이선스 할당](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>그룹의 구성원 자격 확인

그룹의 멤버 자격을 확인하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 그룹** 을 선택합니다.

6. 관심이 있는 그룹을 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **멤버** 를 선택하여 이 그룹에 할당된 사용자 목록을 검토합니다.

### <a name="check-a-dynamic-groups-membership-criteria"></a>동적 그룹의 구성원 자격 조건 확인

동적 그룹의 구성원 자격 조건을 확인하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 그룹** 을 선택합니다.

6. 관심이 있는 그룹을 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **동적 멤버 자격 규칙** 을 선택합니다.

8. 이 그룹에 대해 정의된 **단순** 또는 **고급** 규칙을 검토하고 이 그룹의 구성원으로 포함하려는 사용자가 이러한 조건을 충족하는지 확인합니다.

### <a name="check-a-groups-assigned-licenses"></a>그룹의 할당된 라이선스 확인

그룹의 할당된 라이선스를 확인하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 그룹** 을 선택합니다.

6. 관심이 있는 그룹을 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **라이선스** 를 선택하여 그룹이 현재 할당한 라이선스를 봅니다.

### <a name="reprocess-a-groups-licenses"></a>그룹의 라이선스 다시 처리

그룹의 할당된 라이선스를 다시 처리하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 그룹** 을 선택합니다.

6. 관심이 있는 그룹을 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **라이선스** 를 선택하여 그룹이 현재 할당한 라이선스를 봅니다.

8. **다시 처리** 단추를 선택하여 이 그룹의 구성원에게 할당된 라이선스가 최신인지 확인합니다. 그룹의 크기와 복잡성에 따라 시간이 오래 걸릴 수 있습니다.

   >[!NOTE]
   >시간을 단축하려면 일시적으로 라이선스를 사용자에게 직접 할당할 수도 있습니다. [사용자에게 라이선스를 할당합니다](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>그룹에 라이선스 할당

그룹에 라이선스를 할당하려면 다음 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **사용자 및 그룹** 을 선택합니다.

5. **모든 그룹** 을 선택합니다.

6. 관심이 있는 그룹을 **검색** 하고 **행을 클릭** 하여 선택합니다.

7. **라이선스** 를 선택하여 그룹이 현재 할당한 라이선스를 봅니다.

8. **할당** 단추를 선택합니다.

9. 사용 가능한 제품 목록에서 **하나 이상의 제품** 을 선택합니다.

10. **선택 사항** 제품을 상세하게 할당하려면 **할당 옵션** 항목을 클릭합니다. 완료되면 **확인** 을 클릭합니다.

11. **할당** 단추를 선택하여 이러한 라이선스를 이 그룹에게 할당합니다. 그룹의 크기와 복잡성에 따라 시간이 오래 걸릴 수 있습니다.

    >[!NOTE]
    >시간을 단축하려면 일시적으로 라이선스를 사용자에게 직접 할당할 수도 있습니다. [사용자에게 라이선스를 할당합니다](#problems-with-application-consent).
    >
    >

## <a name="problems-with-conditional-access-policies"></a>조건부 액세스 정책과 관련된 문제

### <a name="check-a-specific-conditional-access-policy"></a>특정 조건부 액세스 정책 확인

단일 조건부 액세스 정책을 확인하거나 유효성을 검사하려면:

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다.

5. **조건부 액세스** 탐색 항목을 선택합니다.

6. 검사하려는 정책을 선택합니다.

7. 특정 조건, 할당 또는 사용자 액세스를 차단할 수 있는 기타 설정이 없는지 검토합니다.

   >[!NOTE]
   >이 정책을 일시적으로 사용하지 않도록 설정하여 로그인에 영향을 주지 않도록 하려면 **정책 사용** 토글을 **아니요** 로 설정하고 **저장** 단추를 클릭합니다.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>특정 애플리케이션의 조건부 액세스 정책 확인

단일 애플리케이션의 현재 구성된 조건부 액세스 정책을 확인하거나 유효성을 검사하려면:

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다.

5. **모든 애플리케이션** 을 선택합니다.

6. 애플리케이션 표시 이름 또는 애플리케이션 ID를 기준으로 관심이 있는 애플리케이션이나 로그인하려는 사용자를 검색합니다.

     >[!NOTE]
     >찾고 있는 애플리케이션이 보이지 않으면 **필터** 단추를 클릭하고 목록 범위를 **모든 애플리케이션** 으로 확장합니다. 더 많은 열을 표시하려면 **열** 단추를 클릭하여 애플리케이션의 추가 세부 정보를 추가합니다.
     >
     >

7. **조건부 액세스** 탐색 항목을 선택합니다.

8. 검사하려는 정책을 선택합니다.

9. 특정 조건, 할당 또는 사용자 액세스를 차단할 수 있는 기타 설정이 없는지 검토합니다.

     >[!NOTE]
     >이 정책을 일시적으로 사용하지 않도록 설정하여 로그인에 영향을 주지 않도록 하려면 **정책 사용** 토글을 **아니요** 로 설정하고 **저장** 단추를 클릭합니다.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>특정 조건부 액세스 정책 사용 안 함

단일 조건부 액세스 정책을 확인하거나 유효성을 검사하려면:

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. 탐색 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다.

5. **조건부 액세스** 탐색 항목을 선택합니다.

6. 검사하려는 정책을 선택합니다.

7. **정책 사용** 토글을 **아니오** 로 설정하여 정책을 사용하지 않도록 설정하고 **저장** 단추를 클릭합니다.

## <a name="problems-with-application-consent"></a>애플리케이션 동의와 관련된 문제

적절한 사용 권한 동의 작업이 발생하지 않았기 때문에 애플리케이션 액세스를 차단할 수 있습니다. 애플리케이션 동의 문제를 해결할 수 있는 몇 가지 방법은 다음과 같습니다.

- [사용자 수준 동의 작업 수행](#perform-a-user-level-consent-operation)

- [애플리케이션에 대해 관리자 수준 동의 작업 수행](#perform-administrator-level-consent-operation-for-any-application)

- [단일 테넌트 애플리케이션에 대해 관리자 수준 동의 작업 수행](#perform-administrator-level-consent-for-a-single-tenant-application)

- [다중 테넌트 애플리케이션에 대해 관리자 수준 동의 작업 수행](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>사용자 수준 동의 작업 수행

- 사용 권한을 요청하는 Open ID Connect 사용 애플리케이션에서 애플리케이션의 로그인 화면으로 이동하면 로그인한 사용자의 애플리케이션에 대한 사용자 수준 동의가 수행됩니다.

- 이 작업을 프로그래밍 방식으로 수행하려는 경우 [개별 사용자의 동의 요청](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)을 참조하세요.

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>애플리케이션에 대해 관리자 수준 동의 작업 수행

- **V1 애플리케이션 모델을 사용하여 개발된 애플리케이션** 의 경우에만 애플리케이션의 로그인 URL의 끝에 “**?prompt=admin\_consent**”를 추가하여 발생하는 이 관리자 수준 동의를 강제할 수 있습니다.

- **V2 애플리케이션 모델을 사용하여 개발된 애플리케이션** 의 경우 [관리 동의 엔드포인트 사용](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)의 **디렉터리 관리에서 사용 권한 요청** 섹션 아래에 있는 지침에 따라 발생하는 이 관리자 수준 동의를 강제할 수 있습니다.

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>단일 테넌트 애플리케이션에 대해 관리자 수준 동의 작업 수행

- 사용 권한을 요청하는 **단일 테넌트 애플리케이션** 의 경우(예: 사용자가 개발하거나 조직에서 소유한 애플리케이션) 전역 관리자로 로그인하고 **애플리케이션 레지스트리 -&gt; 모든 애플리케이션 -&gt; 앱 선택 -&gt; 필요한 권한** 창의 맨 위에 있는 **권한 부여** 단추를 클릭하여 모든 사용자를 대신하여 **관리 수준 동의** 작업을 수행할 수 있습니다.

- **V1 또는 V2 애플리케이션 모델을 사용하여 개발된 애플리케이션** 의 경우 [관리 동의 엔드포인트 사용](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)의 **디렉터리 관리에서 사용 권한 요청** 섹션 아래에 있는 지침에 따라 발생하는 이 관리자 수준 동의를 강제할 수 있습니다.

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>다중 테넌트 애플리케이션에 대해 관리자 수준 동의 작업 수행

- 권한을 요청하는 **다중 테넌트 애플리케이션** 의 경우(예: 타사 또는 Microsoft에서 개발하는 애플리케이션) **관리 수준 동의** 작업을 수행할 수 있습니다. 전역 관리자로 로그인하고 **엔터프라이즈 애플리케이션 -&gt; 모든 애플리케이션 -&gt; 앱 선택 -&gt; 사용 권한** 창에서 **권한 부여** 단추를 클릭합니다(곧 사용 가능).

- 또한 [관리 동의 엔드포인트 사용](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)의 **디렉터리 관리에서 사용 권한 요청** 섹션 아래에 있는 지침에 따라 발생하는 이 관리자 수준 동의를 강제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[관리 동의 엔드포인트 사용](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)
