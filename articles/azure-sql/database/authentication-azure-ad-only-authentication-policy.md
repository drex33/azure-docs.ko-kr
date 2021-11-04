---
title: Azure Active Directory 전용 인증에 대한 Azure Policy
description: 이 문서에서는 Azure AD(Azure Active Directory) 전용 인증을 사용하도록 설정된 azure 정책에서 Azure SQL Database 또는 Azure SQL Managed Instance 만드는 방법에 대한 정보를 제공합니다.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/02/2021
ms.openlocfilehash: 7315b27fccf261dcf25ffe347a22fbb41e0d52cb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458801"
---
# <a name="azure-policy-for-azure-active-directory-only-authentication-with-azure-sql"></a>Azure SQL Azure Active Directory 인증에 대한 Azure Policy

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Policy 프로비전하는 동안 Azure [AD 전용 인증을](authentication-azure-ad-only-authentication.md) 사용하도록 설정하여 Azure SQL Database 또는 Azure SQL Managed Instance 만들 수 있습니다. 이 정책이 적용되면 Azure AD 전용 인증을 사용하도록 설정하여 만들어지지 않은 경우 Azure 또는 관리되는 [인스턴스에서 논리 서버를](logical-servers.md) 만들려는 시도가 실패합니다.

Azure Policy 전체 Azure 구독 또는 리소스 그룹 내에 적용할 수 있습니다.

Azure Policy 두 개의 새로운 기본 제공 정책이 도입되었습니다.

- Azure SQL Database는 Azure Active Directory 전용 인증을 사용하도록 설정해야 함
- Azure SQL Managed Instance는 Azure Active Directory 전용 인증을 사용하도록 설정해야 함

Azure Policy 대한 자세한 내용은 Azure Policy [무엇인가요?](../../governance/policy/overview.md) 및 [정의 구조 Azure Policy 참조하세요.](../../governance/policy/concepts/definition-structure.md)

## <a name="permissions"></a>사용 권한

Azure Policy 관리하는 데 필요한 권한에 대한 개요는 Azure Policy [Azure RBAC 권한을 참조하세요.](../../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)

### <a name="actions"></a>동작

사용자 지정 역할을 사용하여 Azure Policy 관리하는 경우 다음 [작업이](../../role-based-access-control/role-definitions.md#actions) 필요합니다.

- */read
- Microsoft.Authorization/policyassignments/*
- Microsoft.Authorization/policydefinitions/*
- Microsoft.Authorization/policyexemptions/*
- Microsoft.Authorization/policysetdefinitions/*
- Microsoft.PolicyInsights/*

사용자 지정 역할에 대한 자세한 내용은 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조하세요.

## <a name="manage-azure-policy-for-azure-ad-only-authentication"></a>Azure AD 전용 인증에 대한 Azure Policy 관리

Azure AD 전용 인증 정책은 [Azure Portal](https://portal.azure.com)로 이동한 후 **정책** 서비스를 검색하여 관리될 수 있습니다. 정의에서 *인증만 Azure Active Directory* **검색합니다.**

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 스크린샷":::

Azure AD 전용 인증에 대한 Azure Policy 추가하는 방법에 대한 가이드는 [Azure Policy 사용하여 Azure SQL Azure Active Directory 인증만 적용을](authentication-azure-ad-only-authentication-policy-how-to.md)참조하세요.

이러한 정책에는 다음과 같은 세 가지 효과가 있습니다.

- **감사** - 기본 설정이며 Azure Policy 활동 로그에서만 감사 보고서를 캡처합니다.
- **거부** - [Azure AD 전용 인증을](authentication-azure-ad-only-authentication.md) 사용하지 않고 논리 서버 또는 관리되는 인스턴스를 만들 수 없습니다.
- **사용 안 함** - 정책을 사용하지 않도록 설정하고 사용자가 Azure AD 전용 인증을 사용하지 않고 논리 서버 또는 관리형 인스턴스를 만들지 못하도록 제한하지 않습니다.

Azure AD 전용 인증에 대한 Azure Policy **거부로** 설정된 경우 Azure SQL 논리 서버 또는 관리되는 인스턴스 만들기가 실패합니다. 이 실패의 세부 정보는 리소스 그룹의 **활동 로그에** 기록됩니다.

## <a name="policy-compliance"></a>정책 규정 준수

**정책** 서비스 아래에서 **준수** 설정을 확인하여 준수 상태를 확인할 수 있습니다. **준수 상태는** 서버 또는 관리되는 인스턴스가 현재 Azure AD 전용 인증을 사용하는지 여부를 알려줍니다. 

Azure Policy Azure AD 전용 인증을 사용하도록 설정하지 않고 새 논리 서버 또는 관리되는 인스턴스가 생성되지 않도록 방지할 수 있지만 서버 또는 관리되는 인스턴스를 만든 후에 기능을 변경할 수 있습니다. 서버 또는 관리되는 인스턴스를 만든 후 사용자가 Azure AD 전용 인증을 사용하지 않도록 `Non-compliant` 설정한 경우 Azure Policy **거부로** 설정되면 준수 상태가 됩니다.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/check-compliance-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 준수 메뉴의 스크린샷":::

## <a name="limitations"></a>제한 사항

- Azure Policy 논리 서버 또는 관리되는 인스턴스를 만드는 동안 Azure AD 전용 인증을 적용합니다. 서버가 만들어지면 특수 역할(예: SQL Security Manager)을 가진 권한 있는 Azure AD 사용자가 Azure AD 전용 인증 기능을 사용하지 않도록 설정할 수 있습니다. Azure Policy 허용하지만 이 경우 서버 또는 관리되는 인스턴스가 준수 보고서에 로 `Non-compliant` 나열되고 보고서는 서버 또는 관리되는 인스턴스 이름을 나타냅니다.  
- 자세한 주의 사항, 알려진 문제 및 필요한 권한은 [Azure AD 전용 인증](authentication-azure-ad-only-authentication.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Policy를 사용하여 Azure SQL로 Azure Active Directory 전용 인증 적용](authentication-azure-ad-only-authentication-policy-how-to.md)