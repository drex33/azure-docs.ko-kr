---
title: 이메일 확인 사용자를 위한 셀프 서비스 등록 - Azure AD | Microsoft Docs
description: Azure AD(Azure Active Directory) 조직에서 셀프 서비스 등록 사용
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 09/01/2021
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a300f13a18e4185877291099255c84c45bc34c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129986970"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Azure Active Directory의 셀프 서비스 등록이란?

이 문서에서는 셀프 서비스 등록을 사용하여 Azure AD(Azure Active Directory)에서 조직을 채우는 방법을 설명합니다. 관리되지 않는 Azure AD 조직에서 도메인 이름을 가져오면 [관리되지 않는 테넌트를 관리자로 가져오기](domains-admin-takeover.md)를 참조하세요.

## <a name="why-use-self-service-sign-up"></a>셀프 서비스 등록을 사용하는 이유

* 고객이 원하는 서비스를 더욱 빠르게 제공합니다.
* 서비스에 대한 메일 기반 제공 사항을 만듭니다.
* 사용자가 기억하기 쉬운 업무용 이메일 별칭을 사용하여 신속하게 ID를 만들 수 있도록 하는 이메일 기반 등록 흐름을 만듭니다.
* 셀프 서비스로 작성된 Azure AD 테넌트는 다른 서비스에 사용할 수 있는 관리되는 테넌트로 전환할 수 있습니다.

## <a name="terms-and-definitions"></a>용어 및 정의

* **셀프 서비스 등록**: 사용자가 클라우드 서비스에 가입하고 해당 이메일 도메인을 기반으로 Azure AD에 ID가 자동으로 생성되게 하는 방법입니다.
* **관리되지 않는 Azure AD 테넌트:** 해당 ID가 만들어지는 테넌트입니다. 관리되지 않는 테넌트는 전역 관리자가 없는 테넌트입니다.
* **이메일로 확인된 사용자**: Azure AD의 사용자 계정의 한 유형입니다. 셀프 서비스 제공 사항에 등록한 후 자동으로 생성된 ID를 갖는 사용자를 메일로 확인된 사용자라고 합니다. 이메일로 확인된 사용자는 creationmethod=EmailVerified라는 태그가 지정된 테넌트의 일반 멤버입니다.

## <a name="how-do-i-control-self-service-settings"></a>셀프 서비스 설정을 제어하는 방법

관리자는 현재 두 개의 셀프 서비스 컨트롤을 보유하며 다음을 제어할 수 있습니다.

* 사용자가 이메일을 통해 테넌트에 조인할 수 있습니다.
* 사용자가 애플리케이션 및 서비스를 자신이 사용하도록 라이선스를 허여할 수 있습니다.

### <a name="how-can-i-control-these-capabilities"></a>이러한 기능은 어떻게 제어할 수 있나요?

관리자는 Azure AD cmdlet인 다음의 Set-MsolCompanySettings 매개 변수를 사용하여 이러한 기능을 구성할 수 있습니다.

* **AllowEmailVerifiedUsers** 는 사용자가 이메일 유효성 검사를 통해 테넌트에 조인할 수 있는지 여부를 제어합니다. 조인하려면 사용자에게 테넌트의 확인된 도메인 중 하나와 일치하는 도메인의 이메일 주소가 있어야 합니다. 이 설정은 테넌트의 모든 도메인에 대해 회사 전체에 적용됩니다. 해당 매개 변수를 $false로 설정하면 이메일로 확인된 사용자가 테넌트에 조인할 수 없습니다.
* **AllowAdHocSubscriptions** 는 사용자가 셀프 서비스 등록을 수행할 수 있는 기능을 제어합니다. 이 매개 변수를 $false로 설정하면 사용자가 셀프 서비스 등록을 수행할 수 없습니다.
  
AllowEmailVerifiedUsers 및 AllowAdHocSubscriptions는 관리되거나 관리되지 않는 테넌트에 적용할 수 있는 테넌트 전체 설정입니다. 예제는 다음과 같습니다.

* contoso.com과 같은 확인된 도메인을 사용하여 테넌트를 관리합니다.
* 다른 테넌트에서 B2B 협업을 사용하여 constoso.com의 홈 테넌트에 아직 존재하지 않는 사용자(userdoesnotexist@contoso.com)를 초대합니다.
* 홈 테넌트에는 AllowEmailVerifiedUsers가 설정되어 있습니다.

이전 조건이 true이면 홈 테넌트에서 멤버 사용자가 만들어지고, 초대하는 테넌트에서 B2B 게스트 사용자가 만들어집니다.

Flow 및 Power Apps 평가판 등록에 대한 자세한 내용은 다음 문서를 참조하세요.

* [내 기존 사용자가 Power BI를 사용하기 시작하지 않도록 방지하는 방법](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [조직의 Flow Q&A](/power-automate/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>컨트롤이 어떻게 작동하나요?
이 두 매개 변수를 함께 사용하여 정의하면 셀프 서비스 등록을 더욱 세밀하게 제어할 수 있습니다. 예를 들어 Azure AD에 이미 계정이 있는 사용자만 다음 명령을 사용하여 셀프 서비스 등록을 수행할 수 있습니다. 즉, 이메일로 확인된 계정을 먼저 만들어야 하는 사용자는 셀프 서비스 등록을 수행할 수 없습니다.

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

다음 순서도는 테넌트와 셀프 서비스 가입에 대한 이러한 매개 변수 및 해당 결과 조건의 다양한 조합을 설명합니다.

![셀프 서비스 등록 제어 순서도](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

이 설정의 세부 정보는 다음 Get-MsolCompanyInformation PowerShell cmdlet을 통해 검색할 수 있습니다. 이에 대한 자세한 내용은 [Get-MsolCompanyInformation](/powershell/module/msonline/get-msolcompanyinformation)을 참조하세요.

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

매개 변수 사용 방법에 대한 자세한 내용 및 예는 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure AD에 사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md)
* [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure Cmdlet 참조](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings)
* [관리되지 않는 테넌트에서 회사 또는 학교 계정 닫기](users-close-account.md)
