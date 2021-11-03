---
title: Microsoft Authenticator 앱(미리 보기)을 설치하도록 사용자 유도 - Azure Active Directory
description: 덜 안전한 인증 방법에서 Microsoft Authenticator 앱으로 조직을 이동하는 방법 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd27eb5e55b679d8cfd1589857cc88d7f27917d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054365"
---
# <a name="how-to-nudge-users-to-set-up-microsoft-authenticator-preview---microsoft-authenticator-app"></a>Microsoft Authenticator 앱(미리 보기)을 설치하도록 사용자를 유도하는 방법 - Microsoft Authenticator 앱

로그인하는 동안 Microsoft Authenticator를 설치하도록 사용자를 유도할 수 있습니다. 사용자는 일반 로그인을 통해 평소처럼 다단계 인증을 수행하게 됩니다. 그런 다음, Microsoft Authenticator를 설치하라는 메시지가 표시됩니다. 사용자 또는 그룹을 포함하거나 제외하여 앱을 설치하도록 유도할 대상을 제어할 수 있습니다. 이렇게 하여 대상 캠페인을 통해 덜 안전한 인증 방법에서 Microsoft Authenticator로 사용자를 이동할 수 있습니다.  

유도할 수 있는 사용자를 선택하는 것 외에도 사용자가 이를 연기하거나 “다시 알림” 일수를 정의할 수 있습니다. 사용자가 **나중에** 를 탭하여 앱 설치를 다시 알리도록 선택하면, 다시 알림 기간이 경과한 후 다음 MFA 시도에서 유도 문구가 표시됩니다. 

## <a name="prerequisites"></a>사전 요구 사항 

- 조직에서 Azure MFA를 사용하도록 설정한 상태여야 합니다. 
- 사용자는 자신의 계정에서 푸시 알림에 대해 Microsoft Authenticator를 아직 설치하지 않은 상태여야 합니다. 
- 관리자는 다음 정책 중 하나를 통해 사용자가 Microsoft Authenticator를 사용하도록 설정해야 합니다.  
  - MFA 등록 정책: 사용자가 **모바일 앱을 통한 알림** 을 사용하도록 설정해야 합니다.  
  - 인증 방법 정책: 사용자가 Microsoft Authenticator를 사용하도록 설정하고 인증 모드를 **모두** 또는 **푸시** 로 설정해야 합니다. 정책이 **암호 없음** 으로 설정된 경우에는 사용자가 유도 대상이 될 수 없습니다. 

## <a name="user-experience"></a>사용자 환경

1. 사용자가 Azure MFA를 사용하여 MFA를 성공적으로 수행합니다. 

1. 로그인 환경을 개선하기 위해 Microsoft Authenticator 앱을 설치하라는 메시지가 사용자에게 표시됩니다. 참고: Microsoft Authenticator 푸시 알림이 허용되고 현재 설치되지 않은 사용자에게만 프롬프트가 표시됩니다. 

   ![사용자가 다단계 인증을 수행합니다.](./media/how-to-nudge-authenticator-app/user-mfa.png)

1. 사용자가 **다음** 을 탭하고 Microsoft Authenticator 설치를 진행합니다. 
   1. 먼저 앱을 다운로드합니다.  

      ![사용자가 Microsoft Authenticator 다운로드](./media/how-to-nudge-authenticator-app/download.png)

   1. Microsoft Authenticator 설치 방법을 확인합니다. 
   
      ![사용자가 Microsoft Authenticator 설치](./media/how-to-nudge-authenticator-app/setup.png)

   1. QR 코드를 스캔합니다. 

      ![사용자가 QR 코드 스캔](./media/how-to-nudge-authenticator-app/scan.png)

   1. 테스트 알림을 승인합니다.

      ![사용자가 테스트 알림 승인](./media/how-to-nudge-authenticator-app/test.png)

   1. 알림이 승인됩니다.

      ![승인 확인](./media/how-to-nudge-authenticator-app/approved.png)

   1. 이제 Authenticator 앱이 사용자의 기본 로그인 방법으로 성공적으로 설치되었습니다.

      ![설치 완료](./media/how-to-nudge-authenticator-app/finish.png)

1. 사용자는 Authenticator 앱을 설치하지 않으려는 경우 **나중에** 를 탭하여 며칠 후에 프롬프트를 다시 알리도록 선택할 수 있습니다(관리자가 일수를 정의할 수 있음). 
 
   ![설치 다시 알림](./media/how-to-nudge-authenticator-app/snooze.png)

## <a name="enable-the-nudge-policy"></a>유도 정책 사용

유도를 사용하도록 설정하려면 Graph API를 사용하여 인증 방법 정책을 사용해야 합니다. **전역 관리자** 및 **인증 방법 정책 관리자** 가 정책을 업데이트할 수 있습니다. 

Graph Explorer를 사용하여 정책을 구성하려면:

1. Graph Explorer에 로그인하고, **Policy.Read.All** 및 **Policy.ReadWrite.AuthenticationMethod** 사용 권한에 동의했는지 확인합니다.

   권한 패널을 열려면:

   ![Graph Explorer의 스크린샷](./media/how-to-nudge-authenticator-app/permissions.png)

1. 인증 방법 정책을 검색합니다. `GET https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

1. 정책의 registrationEnforcement 및 authenticationMethodsRegistrationCampaign 섹션을 업데이트하여 사용자 또는 그룹에 대한 유도를 사용하도록 설정합니다.

   ![캠페인 섹션](./media/how-to-nudge-authenticator-app/campaign.png)

정책을 업데이트하려면 업데이트된 registrationEnforcement 섹션만 사용하여 인증 방법 정책에 대한 패치를 수행합니다. `PATCH https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

다음 표에서는 **authenticationMethodsRegistrationCampaign** 속성을 보여 줍니다.

| 속성 | 가능한 값 | Description |
|------|-----------------|-------------|
| state | "enabled"<br>"disabled"<br>"default" | 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다.<br>기본값은 구성이 명시적으로 설정되지 않은 경우 사용되며 이 설정에 대해서는 Azure AD 기본값을 사용합니다. 현재 disabled로 매핑됩니다.<br>필요에 따라 상태를 enabled 또는 disabled로 변경합니다.  |
| snoozeDurationInDays | 범위: 0~14 | 사용자에게 유도 문구가 다시 표시될 경과 일수를 정의합니다.<br>값이 0이면 사용자가 MFA를 시도할 때마다 유도 문구가 표시됩니다.<br>기본값: 1일 |
| includeTargets | 해당 없음 | 이 기능의 대상이 될 여러 사용자 및 그룹을 포함할 수 있습니다. |
| excludeTargets | 해당 없음 | 이 기능을 생략할 여러 사용자 및 그룹을 제외할 수 있습니다. 제외된 그룹과 포함된 그룹에 모두 있는 사용자는 기능에서 제외됩니다.|

다음 표에서는 **includeTargets** 속성을 보여 줍니다.

| 속성 | 가능한 값 | Description |
|------|-----------------|-------------|
| targetType| "user"<br>"group" | 대상으로 지정된 엔터티의 종류입니다. |
| Id | GUID 식별자 | 대상으로 지정된 사용자 또는 그룹의 ID입니다. |
| targetedAuthenticationMethod | "microsoftAuthenticator" | 인증 방법 사용자에게 등록하라는 메시지가 표시됩니다. 유일하게 허용되는 값은 "microsoftAuthenticator"입니다. |

다음 표에서는 **excludeTargets** 속성을 보여 줍니다.

| 속성       | 가능한 값   | Description                           |
|------------|-------------------|---------------------------------------|
| targetType | "user"<br>"group" | 대상으로 지정된 엔터티의 종류입니다.          |
| Id         | 문자열          | 대상으로 지정된 사용자 또는 그룹의 ID입니다. |

### <a name="examples"></a>예

다음은 시작하는 데 사용할 수 있는 몇 가지 샘플 JSON입니다. 

- 모든 사용자 포함 
  
  테넌트의 모든 사용자를 포함하려면 [이 JSON을 다운로드](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/All%20Users%20Enabled.json)하여 Graph Explorer에 붙여넣고 엔드포인트에서 `PATCH`를 실행하기만 하면 됩니다. 

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [],
              "includeTargets": [
                  {
                      "id": "all_users",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

- 특정 사용자 또는 사용자 그룹 포함

  특정 사용자 또는 그룹을 테넌트에 포함하려면 [이 JSON을 다운로드](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes.json)하고 사용자 및 그룹의 관련 GUID로 업데이트합니다. 그런 다음, JSON을 Graph Explorer에 붙여넣고 엔드포인트에서 `PATCH`를 실행합니다. 

  ```json
  {
  "registrationEnforcement": {
        "authenticationMethodsRegistrationCampaign": {
            "snoozeDurationInDays": 0,
            "state": "enabled",
            "excludeTargets": [],
            "includeTargets": [
                {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "group",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                },
                {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "user",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                }
            ]
        }
    }
  ```

- 특정 사용자/사용자 그룹 포함 및 제외

  테넌트에서 특정 사용자/사용자 그룹을 포함하고 제외하려면 [이 JSON을 다운로드](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes%20and%20Excludes.json)하여 Graph Explorer에 붙여넣고 엔드포인트에서 `PATCH`를 실행합니다. 사용자 및 그룹에 대한 올바른 GUID를 입력합니다.

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group"
                  },
                {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user"
                  }
              ],
              "includeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  },
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

### <a name="identify-the-guids-of-users-to-insert-in-the-jsons"></a>JSON에 삽입할 사용자의 GUID 식별

1. Azure Portal로 이동합니다.
1. **Azure Active Directory** 를 탭합니다.
1. **관리** 블레이드에서 **사용자** 를 탭합니다.
1. **사용자** 페이지에서 대상으로 지정할 특정 사용자를 식별합니다.
1. 특정 사용자를 탭하면 사용자의 GUID인 **개체 ID** 가 표시됩니다.

   ![사용자 개체 ID](./media/how-to-nudge-authenticator-app/object-id.png)

### <a name="identify-the-guids-of-groups-to-insert-in-the-jsons"></a>JSON에 삽입할 그룹의 GUID 식별

1. Azure Portal로 이동합니다.
1. **Azure Active Directory** 를 탭합니다.
1. **관리** 블레이드에서 **그룹** 을 탭합니다.
1. **그룹** 페이지에서 대상으로 지정할 특정 그룹을 식별합니다.
1. 그룹을 탭하여 **개체 ID** 를 가져옵니다.

   ![그룹 유도](./media/how-to-nudge-authenticator-app/group.png)

<!---comment out PS until ready>

### PowerShell

1. Install the module.
1. Ensure you pass the right roles:
   
   ```powershell
   Connect-MgGraph -Scopes "User.Read.All","Group.ReadWrite.All"
   ```

1. Select the beta profile.
1. Call `Update-MgPolicyAuthenticationMethod`.

<---->

## <a name="limitations"></a>제한 사항

Android 또는 iOS를 실행하는 모바일 디바이스에는 유도 문구가 표시되지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**MFA 서버에서 이 기능을 사용할 수 있나요?** 아니요. 이 기능은 Azure MFA를 사용하는 사용자에 대해서만 사용할 수 있습니다. 

**캠페인이 얼마 동안 실행되나요?** API를 사용하여 원하는 기간만큼 캠페인을 사용하도록 설정할 수 있습니다. 캠페인 실행을 중단하려는 경우 언제든지 API를 사용하여 캠페인을 사용하지 않도록 설정하면 됩니다.  
 
**사용자 그룹마다 다시 알림 기간이 다를 수 있나요?** 아니요. 프롬프트의 다시 알림 기간은 테넌트 전체의 설정이며 범위의 모든 그룹에 적용됩니다. 

**사용자가 암호 없는 휴대폰 로그인을 설정하도록 유도할 수 있나요?** 이 기능은 관리자가 암호 없는 휴대폰 로그인이 아닌 Authenticator 앱을 사용하여 사용자가 MFA를 설정할 수 있도록 하는 것을 목표로 합니다.  

**타사 인증 앱이 설치되어 있는 사용자에게 유도 문구가 표시되나요?** Microsoft Authenticator 앱이 푸시 알림용으로 설치되어 있지 않고 이 사용자가 정책에 따라 푸시 알림을 사용하도록 설정되어 있는 경우에는 사용자에게 유도 문구가 표시됩니다. 

**TOTP 코드용으로만 Microsoft Authenticator 앱을 설치한 사용자에게 유도 문구가 표시되나요?** 예. Microsoft Authenticator 앱이 푸시 알림용으로 설치되어 있지 않고 사용자가 정책에 따라 푸시 알림을 사용하도록 설정되어 있는 경우에는 사용자에게 유도 문구가 표시됩니다.

**사용자가 MFA 등록을 방금 마친 경우 동일한 로그인 세션에서 유도 문구가 표시되나요?** 아니요. 좋은 사용자 환경을 제공하기 위해, 다른 인증 방법을 등록한 동일한 세션에서는 사용자에게 Authenticator 설치 유도 문구가 표시되지 않습니다.  

**또 다른 인증 방법을 등록하도록 사용자를 유도할 수 있나요?** 아니요. 현재 이 기능은 Microsoft Authenticator 앱을 설치하도록 사용자를 유도하는 것만을 목표로 합니다. 

**다시 알림 옵션을 숨기고 사용자가 Authenticator 앱을 설치하도록 강제할 수 있는 방법이 있나요?**  
유도 문구에서 다시 알림 옵션을 숨기는 방법은 없습니다. snoozeDuration을 0으로 설정하여 사용자가 MFA를 시도할 때마다 유도 문구가 표시되도록 할 수 있습니다.  

**내가 Azure MFA를 사용하지 않는 경우 내 사용자를 유도할 수 있나요?** 아니요. 유도 문구는 Azure MFA 서비스를 사용하여 MFA를 수행하는 사용자에 대해서만 작동합니다. 

**내 테넌트의 게스트/B2B 사용자를 유도할 수 있나요?** 예. 정책을 사용하여 유도에 대한 범위가 지정된 경우 가능합니다. 

**사용자가 브라우저를 닫으면 어떻게 되나요?** 다시 알림과 같습니다.


## <a name="next-steps"></a>다음 단계

[Microsoft Authenticator 앱에서 암호 없는 로그인을 사용하도록 설정](howto-authentication-passwordless-phone.md)