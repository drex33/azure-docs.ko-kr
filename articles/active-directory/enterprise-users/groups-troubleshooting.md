---
title: 동적 그룹 멤버 자격 관련 문제 해결 - Azure AD | Microsoft Docs
description: Azure Active Directory의 동적 그룹 멤버 자격에 대한 문제 해결 팁
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: troubleshooting
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 787017bd4c783e28a07870f61d708686fe6313fb
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531139"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>그룹 문제 해결

## <a name="troubleshooting-group-creation-issues"></a>그룹 만들기 문제 해결

**Azure Portal에서 보안 그룹 만들기를 사용하지 않도록 설정했지만 Powershell을 통해 그룹을 계속 만들 수 있음**  
**Azure Portal의 사용자가 Azure Portal에서 보안 그룹을 만들 수 있음** 설정은 관리자가 아닌 사용자가 액세스 패널이나 Azure Portal에서 보안 그룹을 만들 수 있는지 여부를 제어합니다. PowerShell을 통해 보안 그룹 만들기를 제어하지는 않습니다.

PowerShell에서 관리자가 아닌 사용자에 대해 그룹 만들기를 사용하지 않도록 설정하려면 다음을 수행합니다.
1. 관리자가 아닌 사용자가 그룹을 만들 수 있는지 확인합니다.

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

2. `UsersPermissionToCreateGroupsEnabled : True`가 반환되면 관리자가 아닌 사용자가 그룹을 만들 수 있는 것입니다. 이 기능을 사용하지 않도록 설정하려면

   ```powershell
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

**PowerShell에서 동적 그룹을 만들려고 할 때 최대 그룹이 허용됨 오류가 표시됨**  
‘동적 그룹 정책 허용되는 최대 그룹 수에 도달함’을 나타내는 메시지가 PowerShell에 표시되면 이는 조직의 동적 그룹에 대한 최대 한도에 도달했음을 의미합니다. 조직당 최대 동적 그룹 수는 5,000입니다.

새 동적 그룹을 만들려면 먼저 일부 기존 동적 그룹을 삭제해야 합니다. 한도를 늘릴 수 있는 방법은 없습니다.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>그룹의 동적 멤버 자격 문제 해결

**그룹에 대한 규칙을 구성했으나 그룹에서 구성원이 업데이트되지 않음**  
1. 규칙에서 사용자 또는 디바이스 특성의 값을 확인합니다. 규칙을 충족하는 사용자가 있는지 확인합니다.
디바이스의 경우 디바이스 속성을 확인하여 동기화된 특성에 예상 값이 포함되는지 확인합니다.  
2. 멤버 자격 처리 상태를 확인하여 완료되었는지 확인합니다. 그룹에 대한 **개요** 페이지에서 [멤버 자격 처리 상태](groups-create-rule.md#check-processing-status-for-a-rule)와 마지막으로 업데이트한 날짜를 확인할 수 있습니다.

모든 것이 제대로 된 것 같으면 그룹이 채워지도록 잠시 기다립니다. Azure AD 조직의 크기에 따라 처음으로 또는 규칙 변경 후에 그룹을 채우는 데 최대 24시간이 걸릴 수 있습니다.

**규칙을 구성했으나 규칙의 기존 구성원이 제거됨**  
이는 예상된 동작입니다. 규칙을 사용하도록 설정하거나 변경하면 그룹의 기존 멤버가 제거됩니다. 규칙 평가에서 반환된 사용자는 그룹에 멤버로 추가됩니다.

**규칙을 추가하거나 변경하는 즉시 멤버 자격 변경 내용이 표시되지 않는 이유는 무엇인가요?**  
위임된 구성원은 비동기식 백그라운드 프로세스에서 주기적으로 평가되기 때문입니다. 프로세스에서 걸리는 시간은 디렉터리의 사용자 수와 규칙 결과로 만들어진 그룹의 크기로 결정됩니다. 일반적으로 사용자 수가 적은 디렉터리에는 2-3분 내에 그룹 멤버 자격 변경 내용이 표시됩니다. 사용자 수가 많은 디렉터리는 채우는 데 30분 이상이 걸릴 수 있습니다.

**지금 그룹을 강제로 처리하려면 어떻게 해야 하나요?**  
현재는 요청 시 처리되도록 그룹을 자동으로 트리거할 수 있는 방법이 없습니다. 그러나 끝에 공백을 추가하도록 멤버 관리 규칙을 업데이트하여 재처리를 수동으로 트리거할 수 있습니다.

**오류 처리 규칙이 발생함**  
다음 표에서는 일반적인 동적 멤버 자격 규칙 오류와 이를 수정하는 방법을 나열합니다.

| 규칙 파서 오류 | 잘못된 사용법 | 수정된 사용법 |
| --- | --- | --- |
| 오류: 특성이 지원되지 않습니다. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>[지원되는 속성 목록](groups-dynamic-membership.md#supported-properties)에 있는 특성인지 확인합니다. |
| 오류: 특성에서는 연산자가 지원되지 않습니다. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>사용된 연산자는 속성 형식에 지원되지 않습니다(이 예제에서 -contains는 부울 형식에 사용할 수 없습니다). 속성 형식에 올바른 연산자를 사용합니다. |
| 오류: 쿼리 컴파일 오류입니다. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "\*@domain.ext") | 1. 연산자가 없습니다. 조인 조건자에 대해 -and 또는 -or를 사용합니다.<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. -match와 함께 사용되는 정규식 오류입니다.<br>(user.userPrincipalName -match ".\*@domain.ext")<br>or alternatively: (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure Active Directory란?](../fundamentals/active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)
