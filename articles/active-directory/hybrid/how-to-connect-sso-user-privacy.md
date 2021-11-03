---
title: 사용자 개인 정보 및 Azure AD 원활한 Single Sign-On | Microsoft Docs
description: 이 문서에서는 Azure AD(Azure Active Directory) Seamless SSO 및 GDPR 준수에 대해 다룹니다.
services: active-directory
keywords: Azure AD Connect의 정의, GDPR, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21d4253da1d3e4b190cfad92349ee885541766c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067988"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>사용자 개인 정보 및 Azure AD 원활한 Single Sign-On

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>개요


Azure AD Seamless SSO는 개인 데이터를 포함할 수 있는 다음 로그 형식을 만듭니다. 

- Azure AD Connect 추적 로그 파일.

다음 두 가지 방법으로 Seamless SSO에 대한 사용자 개인 정보를 개선합니다.

1.  요청 시 사람에 대한 데이터를 추출하고 그 사람의 데이터를 설치에서 제거합니다.
2.  데이터는 48시간 이상 데이터가 보존하지 않도록 합니다.

구현 및 유지 관리가 훨씬 편한 두 번째 옵션을 강력하게 권장합니다. 각 로그 형식에 대한 다음 지침을 참조하세요.

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect 추적 로그 파일을 삭제합니다.

Azure AD Connect를 설치 또는 업그레이드하거나 Seamless SSO 구성을 수정한 지 48시간 이내에 **%ProgramData%\AADConnect** 폴더의 콘텐츠를 확인하고 이 폴더의 추적 로그 콘텐츠(**trace-\*.log** 파일)를 삭제합니다. 이 작업으로 인해 GDPR에서 다루는 데이터가 생성될 수 있기 때문입니다.

>[!IMPORTANT]
>이 폴더의 **PersistedState.xml** 파일은 삭제하지 마세요. 이 파일은 이전에 설치한 Azure AD Connect의 상태를 유지하고 업그레이드 설치를 수행할 때 사용됩니다. 이 파일에는 사람에 대한 데이터가 포함되지 않으므로 삭제해서는 안됩니다.

Windows 탐색기를 사용하여 이러한 추적 로그 파일을 검토한 후 삭제할 수 있고, 다음과 같은 PowerShell 스크립트를 사용하여 필요한 작업을 수행할 수도 있습니다.

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

확장명이 ".PS1"인 파일에 스크립트를 저장합니다. 필요에 따라 이 스크립트를 실행합니다.

관련 Azure AD Connect GDPR 요구 사항에 대한 자세한 내용은 [이 문서](reference-connect-user-privacy.md)를 참조하세요.

### <a name="note-about-domain-controller-logs"></a>도메인 컨트롤러 로그에 대한 참고 사항

감사 로깅이 설정된 경우 해당 제품은 도메인 컨트롤러에 대한 보안 로그를 생성할 수 있습니다. 감사 정책 구성에 대한 자세한 내용은 이 [문서](/previous-versions/tn-archive/dd277403(v=technet.10))를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [보안 센터에서 Microsoft 개인 정보 취급 방침을 검토합니다.](https://www.microsoft.com/trustcenter)
  - [**문제 해결**](tshoot-connect-sso.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
  - [**UserVoice**](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) - 새로운 기능 요청을 제출합니다.