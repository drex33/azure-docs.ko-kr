---
title: 그룹에 민감도 레이블 할당 - Azure AD | Microsoft Docs
description: 그룹에 민감도 레이블을 할당하는 방법을 알아봅니다. 문제 해결 정보를 참조하고 사용 가능한 추가 리소스를 살펴봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bb00c2554b17ec68cfd1cffa0902bed421b9e4e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433074"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Azure Active Directory의 Microsoft 365 그룹에 민감도 레이블 할당

Azure AD(Azure Active Directory)는 [Microsoft 365 규정 준수 센터](https://sip.protection.office.com/homepage)에서 게시한 민감도 레이블을 Microsoft 365 그룹에 적용하도록 지원합니다. 민감도 레이블은 Outlook, Microsoft Teams, SharePoint 등의 서비스에서 그룹에 적용됩니다. Microsoft 365 앱 지원에 대한 자세한 내용은 [민감도 레이블에 대한 Microsoft 365 지원](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)참조하세요.

> [!IMPORTANT]
> 이 기능을 구성하려면 Azure AD 조직에 활성 Azure Active Directory Premium P1 라이선스가 한 개 이상 있어야 합니다.

## <a name="enable-sensitivity-label-support-in-powershell"></a>PowerShell에서 민감도 레이블 지원 사용

게시된 레이블을 그룹에 적용하려면 먼저 기능을 사용하도록 설정해야 합니다. 다음 단계를 수행하면 Azure AD에서 기능을 사용하도록 설정할 수 있습니다.

1. 컴퓨터에서 Windows PowerShell 창을 엽니다. 높은 권한이 없으면 이 창이 열리지 않습니다.
1. 다음 명령을 실행하여 cmdlet 실행을 준비합니다.

    ```PowerShell
    Install-Module AzureADPreview
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    **계정에 로그인** 페이지에서 관리자 계정과 암호를 입력하여 서비스에 연결하고 **로그인** 을 선택합니다.
1. Azure AD 조직에 대한 현재 그룹 설정을 가져옵니다.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > 이 Azure AD 조직에 대해 그룹 설정이 생성되지 않은 경우 "Cannot bind argument to parameter 'Id' because it is null"("'Id' 매개 변수가 null이므로 인수를 해당 매개 변수에 바인딩할 수 없습니다.")라는 오류 메시지가 위 cmdlet에 표시됩니다. 이 경우 먼저 설정을 만들어야 합니다. [그룹 설정을 구성하는 Azure Active Directory cmdlet](../enterprise-users/groups-settings-cmdlets.md)의 단계에 따라 이 Azure AD 조직에 대한 그룹 설정을 만듭니다.

1. 그런 다음 현재 그룹 설정을 표시합니다.

    ```PowerShell
    $Setting.Values
    ```

1. 다음으로 기능을 사용하도록 설정합니다.

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 그런 다음 변경 내용을 저장하고 설정을 적용합니다.

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

또한 민감도 레이블을 Azure AD와 동기화해야 합니다. 자세한 내용은 [컨테이너에서 민감도 레이블을 사용하도록 설정하고 레이블을 동기화하는 방법](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels)을 참조하세요.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Azure portal에서 새 그룹에 레이블 할당

1. [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
1. **그룹** 을 선택한 다음, **새 그룹** 을 선택합니다.
1. **새 그룹** 페이지에서 **Office 365** 를 선택한 다음 새 그룹에 대한 필수 정보를 작성하고 목록에서 민감도 레이블을 선택합니다.

   ![새 그룹 페이지에서 민감도 레이블 할당](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 변경 내용을 저장하고 **만들기** 를 선택합니다.

그룹이 만들어지고 선택한 레이블과 연결된 사이트 및 그룹 설정이 자동으로 적용됩니다.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Azure portal에서 기존 그룹에 레이블 할당

1. 그룹 관리자 계정 또는 그룹 소유자로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
1. **그룹** 을 선택합니다.
1. **모든 그룹** 페이지에서 레이블을 지정할 그룹을 선택합니다.
1. 선택한 그룹의 페이지에서 **속성** 을 선택하고 목록에서 민감도 레이블을 선택합니다.

   ![그룹에 대한 개요 페이지에서 민감도 레이블 할당](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. **저장** 을 선택하여 변경 내용을 저장합니다.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Azure portal에서 기존 그룹의 레이블 제거

1. 전역 관리자 또는 그룹 관리자 계정 또는 그룹 소유자로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
1. **그룹** 을 선택합니다.
1. **모든 그룹** 페이지에서 레이블을 제거할 그룹을 선택합니다.
1. **그룹** 페이지에서 **속성** 을 선택합니다.
1. **제거** 를 선택합니다.
1. **저장** 을 선택하여 변경 내용을 적용합니다.

## <a name="using-classic-azure-ad-classifications"></a>클래식 Azure AD 분류 사용

이 기능을 사용하도록 설정하면 그룹의 "클래식" 분류에 기존 그룹 및 사이트만 표시되므로 민감도 레이블을 지원하지 않는 앱에서 그룹을 만드는 경우에만 새 그룹에 사용해야 합니다. 필요한 경우 관리자가 나중에 민감도 레이블로 변환할 수 있습니다. 클래식 분류는 Azure AD PowerShell에서 `ClassificationList` 설정에 대한 값을 정의하여 설정한 이전 분류입니다. 이 기능을 사용하도록 설정하면 그러한 분류가 그룹에 적용되지 않습니다.

## <a name="troubleshooting-issues"></a>문제 해결

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>민감도 레이블을 그룹에 할당할 수 없는 경우

민감도 레이블 옵션은 다음 조건이 모두 충족되는 경우에만 그룹에 대해 표시됩니다.

1. 레이블이 이 Azure AD 조직의 Microsoft 365 규정 준수 센터에 게시됩니다.
1. 이 기능을 사용하도록 설정하면 Azure AD PowerShell 모듈에서 EnableMIPLabels가 True로 설정됩니다.
1. 레이블이 Security & Compliance PowerShell 모듈에서 Execute-Execute-AzureAdLabelSync cmdlet을 사용하여 Azure AD에 동기화됩니다.
1. 그룹이 Microsoft 365 그룹입니다.
1. 조직에 활성 Azure Active Directory Premium P1 라이선스가 있습니다.
1. 현재 로그인한 사용자에게 레이블을 할당할 수 있는 충분한 권한이 있습니다. 사용자는 전역 관리자, 그룹 관리자 또는 그룹 소유자여야 합니다.

그룹에 레이블을 할당하려면 모든 조건을 충족하는지 확인하세요.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>할당하려는 레이블이 목록에 없는 경우

찾고 있는 레이블이 목록에 없는 경우 다음 이유 중 하나 때문일 수 있습니다.

- 레이블이 Microsoft 365 규정 준수 센터에 게시되지 않을 수 있습니다. 이 경우 더 이상 게시되지 않은 레이블에도 적용될 수 있습니다. 자세한 내용은 관리자에게 문의하세요.
- 레이블을 게시할 수 있지만 로그인한 사용자는 사용할 수 없습니다. 레이블에 대한 액세스 권한을 얻는 방법에 대한 자세한 내용은 관리자에게 문의하세요.

### <a name="how-to-change-the-label-on-a-group"></a>그룹의 레이블을 변경하는 방법

다음과 같이 레이블을 기존 그룹에 할당하는 것과 동일한 단계를 사용하여 언제든지 레이블을 바꿀 수 있습니다.

1. 전역 또는 그룹 관리자 계정이나 그룹 소유자로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
1. **그룹** 을 선택합니다.
1. **모든 그룹** 페이지에서 레이블을 지정할 그룹을 선택합니다.
1. 선택한 그룹의 페이지에서 **속성** 을 선택하고 목록에서 새 민감도 레이블을 선택합니다.
1. **저장** 을 선택합니다.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>게시된 레이블에 대한 그룹 설정 변경 내용이 그룹에서 업데이트되지 않는 경우

모범 사례로, 레이블을 그룹에 적용한 후에는 레이블에 대한 그룹 설정을 변경하지 않는 것이 좋습니다. [Microsoft 365 규정 준수 센터](https://sip.protection.office.com/homepage)에서 게시된 레이블과 연결된 그룹 설정을 변경하는 경우 해당 정책 변경 내용은 영향을 받는 그룹에 자동으로 적용되지 않습니다.

변경해야 하는 경우 [Azure AD PowerShell 스크립트](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)를 사용하여 영향을 받는 그룹에 업데이트를 수동으로 적용합니다. 이 방법을 사용하면 기존의 모든 그룹이 새 설정을 적용합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Teams, Microsoft 365 그룹 및 SharePoint 사이트에서 민감도 레이블 사용](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Azure AD PowerShell 스크립트를 사용하여 레이블 정책을 수동으로 변경한 후 그룹 업데이트](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [그룹 설정 편집](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [PowerShell 명령을 사용하여 그룹 관리](../enterprise-users/groups-settings-v2-cmdlets.md)
