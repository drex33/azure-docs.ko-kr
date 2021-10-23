---
title: Azure Policy 사용하여 Azure Active Directory 인증만 적용
description: 이 문서에서는 Azure Policy 사용하여 Azure SQL Database 및 Azure SQL Managed Instance Azure Active Directory(Azure AD) 인증만 적용하는 방법을 안내합니다.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 6ad1e2819a2bd71d00943e4df11b50eb2f0ceb18
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256148"
---
# <a name="using-azure-policy-to-enforce-azure-active-directory-only-authentication-with-azure-sql"></a>Azure Policy를 사용하여 Azure SQL로 Azure Active Directory 전용 인증 적용

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 이 문서에서 설명한 **Azure AD 전용 인증** 및 관련 Azure Policy 기능은 공개 미리 **보기** 에 있습니다. 

이 문서에서는 사용자가 Azure SQL Managed Instance 만들거나 Azure SQL Database 논리 [서버를](logical-servers.md) 만들 때 Azure AD 전용 인증을 적용하는 Azure Policy 만드는 작업을 안내합니다. 리소스를 만드는 동안 Azure AD 전용 인증에 대한 자세한 내용은 [Azure SQL Azure AD 전용 인증을 사용하도록 설정된 서버 만들기를](authentication-azure-ad-only-authentication-create-server.md)참조하세요.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> - [Azure AD 전용 인증을](authentication-azure-ad-only-authentication.md) 사용하도록 설정하여 논리 서버 또는 관리되는 인스턴스 만들기를 적용하는 Azure Policy 만들기
> - Azure Policy 준수 확인

## <a name="prerequisite"></a>필수 조건

- Azure Policy 관리할 수 있는 권한이 있습니다. 자세한 내용은 [Azure Policy Azure RBAC 권한을](../../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)참조하세요.

## <a name="create-an-azure-policy"></a>Azure Policy 만들기

Azure AD 전용 인증을 사용하도록 설정된 SQL Database 또는 Managed Instance 프로비저닝을 적용하는 Azure Policy 만들어 시작합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 서비스 정책 를 **검색합니다.**
1. 제작 설정에서 정의를 **선택합니다.**
1. **검색** 상자에서 *인증만 Azure Active Directory* 검색합니다.

   Azure AD 전용 인증을 적용하는 데 사용할 수 있는 두 가지 기본 제공 정책이 있습니다. 하나는 SQL Database 위한 것이고 다른 하나는 Managed Instance 위한 것입니다.

   - Azure SQL Database는 Azure Active Directory 전용 인증을 사용하도록 설정해야 함
   - Azure SQL Managed Instance는 Azure Active Directory 전용 인증을 사용하도록 설정해야 함

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 스크린샷":::

1. 서비스의 정책 이름을 선택합니다. 이 예제에서는 Azure SQL Database 사용합니다. **Azure Active Directory 인증만 사용하도록 설정해야 Azure SQL Database** 선택합니다.
1. 새 메뉴에서 **할당을** 선택합니다.

   > [!NOTE]
   > 메뉴의 JSON 스크립트에는 템플릿으로 사용하여 SQL Database 대한 사용자 지정 Azure Policy 빌드할 수 있는 기본 제공 정책 정의가 표시됩니다. 기본값은 로 `Audit` 설정됩니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/assign-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 할당하는 스크린샷":::

1. 기본 **탭에서** 상자 옆에 있는 선택기(**...**)를 사용하여 **범위를** 추가합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/selecting-scope-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 범위를 선택하는 스크린샷":::

1. **범위** 창의 드롭다운 메뉴에서 **구독을** 선택하고 이 정책에 대한 **리소스 그룹을** 선택합니다. 완료되면 **선택** 단추를 사용하여 선택 항목을 저장합니다.

   > [!NOTE]
   > 리소스 그룹을 선택하지 않으면 정책이 전체 구독에 적용됩니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/adding-scope-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 범위를 추가하는 스크린샷":::

1. **기본 사항** 탭으로 돌아가면 **할당 이름을** 사용자 지정하고 선택적 **설명** 를 제공합니다. **정책 적용이** **사용인지 확인합니다.**
1. **매개 변수** 탭으로 이동합니다. **입력이 필요한 매개 변수만 표시** 옵션을 선택 취소합니다.
1. **효과에서** **거부를** 선택합니다. 이 설정은 Azure AD 전용 인증을 사용하지 않고 논리 서버를 만드는 것을 방지합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/deny-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 효과 매개 변수의 스크린샷":::

1. **비준수 메시지** 탭에서 정책 위반이 발생한 경우 표시되는 정책 메시지를 사용자 지정할 수 있습니다. 이 메시지는 사용자에게 서버를 만드는 동안 적용된 정책을 알 수 있도록 합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/non-compliance-message-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 비준수 메시지의 스크린샷":::

1. **검토 + 만들기** 를 선택합니다. 정책을 검토하고 **만들기** 단추를 선택합니다.

> [!NOTE]
> 새로 만든 정책이 적용되는 데 다소 시간이 걸릴 수 있습니다.

## <a name="check-policy-compliance"></a>정책 준수 확인

**정책** 서비스에서 **준수** 설정을 확인하여 준수 상태를 확인할 수 있습니다.

이전에 정책에 지정한 할당 이름을 검색합니다.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/compliance-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대한 Azure Policy 규정 준수 스크린샷":::

Azure AD 전용 인증을 사용하여 논리 서버를 만든 후 정책 보고서는 준수 상태별 리소스 시각적 개체에서 **카운터를 늘입니다.** 어떤 리소스가 규정을 준수하는지 또는 비준수인지 확인할 수 있습니다.

정책에 포함하도록 선택한 리소스 그룹에 이미 생성된 서버가 포함된 경우 정책 보고서에는 규정을 준수하고 비준수인 리소스가 표시됩니다.

> [!NOTE]
> 규정 준수 보고서를 업데이트하는 데 다소 시간이 걸릴 수 있습니다. 리소스 만들기 또는 Azure AD 전용 인증 설정과 관련된 변경 내용은 즉시 보고되지 않습니다.    

## <a name="provision-a-server"></a>서버 프로비전

그런 다음 Azure Policy 할당한 리소스 그룹에서 논리 서버 또는 관리되는 인스턴스를 프로비전할 수 있습니다. 서버를 만드는 동안 Azure AD 전용 인증을 사용하도록 설정하면 프로비전이 성공합니다. Azure AD 전용 인증을 사용하도록 설정하지 않으면 프로비전이 실패합니다.

자세한 내용은 [Azure SQL Azure AD 전용 인증을 사용하도록 설정된 서버 만들기를 참조하세요.](authentication-azure-ad-only-authentication-create-server.md)

## <a name="next-steps"></a>다음 단계

- Azure [AD 전용 인증에 대한 Azure Policy](authentication-azure-ad-only-authentication-policy.md) 개요
- [Azure SQL에서 Azure AD 전용 인증이 사용하도록 설정된 서버 만들기](authentication-azure-ad-only-authentication-create-server.md)
- Azure [AD 전용 인증](authentication-azure-ad-only-authentication.md) 개요