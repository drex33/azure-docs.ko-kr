---
title: 보고용 Azure AD PowerShell cmdlet | Microsoft Docs
description: 보고용 Azure AD PowerShell cmdlet에 대한 참조입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 710bdbd0661fcf711898a59c0d59b0c0b4b97d01
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995448"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>보고용 Azure AD PowerShell cmdlet

> [!NOTE] 
> 이러한 PowerShell cmdlet은 현재 [Azure AD 미리 보기](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#directory_auditing) 모듈에서만 작동합니다. 미리 보기 모듈은 프로덕션 환경에서 사용하지 않는 것이 좋습니다. 

퍼블릭 미리 보기 릴리스를 설치하려면 다음을 사용합니다. 

```powershell
Install-module AzureADPreview
```

PowerShell을 사용하여 Azure AD에 연결하는 방법에 대한 자세한 내용은 [Graph용 Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) 문서를 참조하세요.  

Azure Active Directory(Azure AD) 보고서를 사용하여 방향(감사 로그) 및 인증 데이터(로그인 로그)의 모든 쓰기 작업에 대한 세부 정보를 얻을 수 있습니다. MS Graph API를 사용하여 정보를 얻을 수 있지만 이제는 보고용 Azure AD PowerShell cmdlet을 사용하여 동일한 데이터를 검색할 수 있습니다.

이 문서에서는 감사 로그 및 로그인 로그에 사용할 PowerShell cmdlet에 대한 개요를 제공합니다.

## <a name="audit-logs"></a>감사 로그

[감사 로그](concept-audit-logs.md) - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통해 추적 기능을 제공합니다. 감사 로그의 예로는 사용자, 앱, 그룹, 역할, 정책의 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

`Get-AzureADAuditDirectoryLogs cmdlet을 사용하여 감사 로그에 액세스할 수 있습니다.


| 시나리오                      | PowerShell 명령 |
| :--                           | :--                |
| 애플리케이션 표시 이름      | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync'" |
| 범주                      | Get-AzureADAuditDirectoryLogs -Filter "category eq 'ApplicationManagement'" |
| 작업 날짜 시간            | Get-AzureADAuditDirectoryLogs -Filter "activityDateTime gt 2019-04-18" |
| 위 항목 모두              | Get-AzureADAuditDirectoryLogs -Filter "initiatedBy/app/displayName eq 'Azure AD Cloud Sync' and category eq 'ApplicationManagement' and activityDateTime gt 2019-04-18"|


다음 이미지는 이 명령에 대한 예제를 보여줍니다. 

![스크린샷은 Get-Azure D Audit Directory Logs 명령의 결과를 보여줍니다.](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>로그인 로그

[로그인](concept-sign-ins.md) 로그에서는 관리형 애플리케이션 사용량과 사용자 로그인 활동에 대한 정보를 확인할 수 있습니다.

`Get-AzureADAuditSignInLogs cmdlet을 사용하여 로그인 로그에 액세스할 수 있습니다.


| 시나리오                      | PowerShell 명령 |
| :--                           | :--                |
| 사용자 표시 이름             | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins'" |
| 만든 날짜 시간              | Get-AzureADAuditSignInLogs -Filter "createdDateTime gt 2019-04-18T17:30:00.0Z" (Everything since 5:30 pm on 4/18) |
| 상태                        | Get-AzureADAuditSignInLogs -Filter "status/errorCode eq 50105" |
| 애플리케이션 표시 이름      | Get-AzureADAuditSignInLogs -Filter "appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |
| 위 항목 모두              | Get-AzureADAuditSignInLogs -Filter "userDisplayName eq 'Timothy Perkins' and status/errorCode ne 0 and appDisplayName eq 'StoreFrontStudio [wsfed enabled]'" |


다음 이미지는 이 명령에 대한 예제를 보여줍니다. 

![스크린샷은 Get-Azure A D Audit Sign In Logs 명령의 결과를 보여줍니다.](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>다음 단계

- [Azure AD 보고서 개요](overview-reports.md).
- [감사 로그 보고서](concept-audit-logs.md). 
- [Azure AD 보고서에 대한 프로그래밍 방식 액세스](concept-reporting-api.md)
