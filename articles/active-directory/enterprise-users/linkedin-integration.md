---
title: LinkedIn 계정 연결에 대한 관리자 동의 - Azure AD | Microsoft Docs
description: Azure Active Directory에서 Microsoft 앱용 LinkedIn 통합 계정 연결을 사용하거나 사용하지 않도록 설정하는 방법 설명
services: active-directory
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27b7b7281a156cadcf8cda7b9ebf1339f0a8dc2b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985659"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Azure Active Directory에서 LinkedIn 계정 연결 통합

조직의 사용자가 일부 Microsoft 앱 내에서 LinkedIn 연결에 액세스하도록 허용할 수 있습니다. 사용자가 계정에 연결하도록 동의하기 전에는 데이터가 공유되지 않습니다. Azure AD(Azure Active Directory) [관리 센터](https://aad.portal.azure.com)에서 조직을 통합할 수 있습니다.

> [!IMPORTANT]
> LinkedIn 계정 연결 설정은 현재 Azure AD 조직에 공개되어 있습니다. 조직에 공개되면 기본적으로 사용하도록 설정됩니다.
>
> 예외:
>
> * 이 설정은 미국 정부용 Microsoft Cloud, 독일 Microsoft Cloud 또는 중국 21Vianet에서 운영하는 Azure 및 Microsoft 365를 사용하는 고객은 사용할 수 없습니다.
> * 독일에서 프로비전된 Azure AD 조직에 대해서는 설정이 기본적으로 꺼져 있습니다. Microsoft Cloud Germany를 사용하는 고객은 이 설정을 사용할 수 없습니다.
> * 프랑스에서 프로비전된 조직에 대해서는 설정이 기본적으로 꺼져 있습니다.
>
> 조직에 대해 LinkedIn 계정 연결을 사용하도록 설정하면 사용자가 본인 대신 회사 데이터에 액세스하는 앱에 동의한 후에 계정 연결이 작동합니다. 사용자 동의 설정에 대한 자세한 내용은 [애플리케이션에 대한 사용자 액세스를 제거하는 방법](../manage-apps/methods-for-removing-user-access.md)을 참조하세요.

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Azure Portal에서 LinkedIn 계정 연결 사용

조직 전체에서부터 선택한 사용자까지 원하는 사용자에게만 액세스 권한을 부여하여 LinkedIn 계정 연결을 사용하도록 설정할 수 있습니다.

1. Azure AD 조직의 전역 관리자 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com/)에 로그인합니다.
1. **사용자** 를 선택합니다.
1. **사용자** 페이지에서 **사용자 설정** 을 선택합니다.
1. **Linkedin 계정 연결** 에서 사용자가 본인 계정을 연결하여 일부 Microsoft 앱 내에서 LinkedIn 연결에 액세스하도록 허용합니다. 사용자가 계정에 연결하도록 동의하기 전에는 데이터가 공유되지 않습니다.

    * 조직의 모든 사용자에 대해 서비스를 사용하도록 설정하려면 **예** 를 선택합니다.
    * 조직에서 선택한 사용자 그룹에 대해서만 서비스를 사용하도록 설정하려면 **선택한 그룹** 을 선택합니다.
    * 조직 내 모든 사용자로부터 동의를 철회하려면 **아니요** 를 선택합니다.

    ![조직에서 LinkedIn 계정 연결 통합](./media/linkedin-integration/linkedin-integration.png)

1. 선택을 마치면 **저장** 을 선택하여 설정을 저장합니다.

> [!Important]
> 사용자가 계정에 연결하도록 동의하기 전까지는 LinkedIn 통합을 완전히 사용할 수 없습니다. 사용자에 대해 계정 연결을 사용하도록 설정하면 데이터가 공유되지 않습니다.

### <a name="assign-selected-users-with-a-group"></a>선택한 사용자를 그룹에 할당

사용자 목록을 지정하는 '선택됨' 옵션을 사용자 그룹을 선택하는 옵션으로 대체하여 여러 개별 사용자 대신 단일 그룹에 대해 LinkedIn 및 Microsoft 계정을 연결하는 기능을 사용할 수 있도록 했습니다. 선택한 개별 사용자에 대해 LinkedIn 계정 연결을 사용하도록 설정하지 않은 경우에는 아무것도 수행할 필요가 없습니다. 이전에 선택한 개별 사용자에 대해 LinkedIn 계정 연결을 사용하도록 설정한 경우 다음을 수행해야 합니다.

1. 개별 사용자의 현재 목록 가져오기
1. 현재 사용하도록 설정된 개별 사용자를 그룹으로 이동
1. Azure AD 관리 센터의 LinkedIn 계정 연결 설정에서 이전 그룹을 선택한 그룹으로 사용합니다.

> [!NOTE]
> 현재 선택한 개별 사용자를 그룹으로 이동하지 않더라도 Microsoft 앱에서 LinkedIn 정보를 계속 볼 수 있습니다.

### <a name="move-currently-selected-users-to-a-group"></a>현재 선택한 사용자를 그룹으로 이동

1. LinkedIn 계정 연결에 대해 선택한 사용자의 CSV 파일을 만듭니다.
1. 관리자 계정으로 Microsoft 365에 로그인합니다.
1. PowerShell을 시작합니다.
1. `Install-Module AzureAD`를 실행하여 Azure AD 모듈 설치
1. 다음 스크립트를 실행합니다.

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Azure AD 관리 센터의 LinkedIn 계정 연결 설정에서 2단계의 그룹을 선택한 그룹으로 사용하려면 [Azure Portal에서 LinkedIn 계정 연결 사용](#enable-linkedin-account-connections-in-the-azure-portal)을 참조하세요.

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>그룹 정책을 사용하여 LinkedIn 계정 연결 사용

1. [Office 2016 관리 템플릿 파일(ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) 다운로드
1. **ADMX** 파일을 추출하고 중앙 저장소에 복사합니다.
1. 그룹 정책 관리를 엽니다.
1. **사용자 구성** > **관리 템플릿** > **Microsoft Office 2016** > **기타** > **Office 애플리케이션에서 LinkedIn 기능 표시** 설정을 사용하여 그룹 정책 개체를 만듭니다.
1. **사용** 또는 **사용 안 함** 을 선택합니다.
  
   시스템 상태 | 영향
   ------ | ------
   **Enabled** | Office 2016 옵션의 **Office 애플리케이션에 LinkedIn 기능 표시** 설정이 사용으로 설정됩니다. 조직 내 사용자가 Office 2016 애플리케이션에서 LinkedIn 기능을 사용할 수 있습니다.
   **사용 안 함** | Office 2016 옵션의 **Office 애플리케이션에 LinkedIn 기능 표시** 설정이 사용 안 함으로 설정되며, 최종 사용자가 이 설정을 변경할 수 없습니다. 조직 내 사용자가 Office 2016 애플리케이션에서 LinkedIn 기능을 사용할 수 있습니다.

이 그룹 정책은 로컬 컴퓨터용 Office 2016 앱에만 영향을 줍니다. 사용자가 Office 2016 앱에서 LinkedIn을 사용하지 않도록 설정하여도 Microsoft 365에서 LinkedIn 기능을 계속 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [LinkedIn에 대한 사용자 동의 및 데이터 공유](linkedin-user-consent.md)

* [Microsoft 앱의 LinkedIn 정보 및 기능](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 도움말 센터](https://www.linkedin.com/help/linkedin)

* [Azure Portal에서 현재 LinkedIn 통합 설정 보기](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)