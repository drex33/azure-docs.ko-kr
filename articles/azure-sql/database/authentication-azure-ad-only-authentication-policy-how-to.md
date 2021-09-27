---
title: Azure Policy를 사용 하 여 Azure Active Directory 인증만 적용
description: 이 문서에서는 Azure Policy를 사용 하 여 Azure SQL Database 및 azure SQL에서 Azure Active Directory (Azure AD) 인증만 적용 하는 방법을 안내 Managed Instance
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 24cb978a20bc3a6e2385ed37994389abb6d32a32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634336"
---
# <a name="using-azure-policy-to-enforce-azure-active-directory-only-authentication-with-azure-sql"></a>Azure Policy를 사용 하 여 Azure SQL 인증 Azure Active Directory 적용

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 이 문서에서 설명 하는 **AZURE AD 전용 인증** 및 관련 Azure Policy 기능은 **공개 미리 보기로** 제공 됩니다. 

이 문서에서는 사용자가 azure SQL Managed Instance를 만들 때 또는 Azure SQL Database 용 [논리 서버](logical-servers.md) 를 만들 때 azure AD 전용 인증을 적용 하는 Azure Policy를 만드는 과정을 안내 합니다. 리소스를 만드는 동안 Azure AD 전용 인증에 대 한 자세한 내용은 azure [SQL에서 AZURE ad 전용 인증을 사용 하도록 설정 된 서버 만들기](authentication-azure-ad-only-authentication-create-server.md)를 참조 하세요.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> - [AZURE AD 전용 인증](authentication-azure-ad-only-authentication.md) 을 사용 하 여 논리 서버 또는 관리 되는 인스턴스 만들기를 적용 하는 Azure Policy 만들기
> - Azure Policy 준수 확인

## <a name="prerequisite"></a>필수 조건

- Azure Policy를 관리할 수 있는 권한이 있어야 합니다. 자세한 내용은 [Azure Policy의 AZURE RBAC 권한](/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy)을 참조 하세요.

## <a name="create-an-azure-policy"></a>Azure Policy 만들기

SQL Database를 적용 하는 Azure Policy을 만들거나 Azure AD 전용 인증을 사용 하는 Managed Instance 프로 비전을 사용 하 여 시작 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. 서비스 **정책을** 검색 합니다.
1. 제작 설정에서 **정의** 를 선택 합니다.
1. **검색** 상자에서 *Azure Active Directory 인증만* 검색 합니다.

   Azure AD 전용 인증을 적용 하는 데 사용할 수 있는 두 가지 기본 제공 정책이 있습니다. 하나는 SQL Database에 대 한 것이 고 다른 하나는 Managed Instance에 대 한 것입니다.

   - Azure SQL Database는 Azure Active Directory 전용 인증을 사용하도록 설정해야 함
   - Azure SQL Managed Instance는 Azure Active Directory 전용 인증을 사용하도록 설정해야 함

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대 한 Azure Policy 스크린샷":::

1. 서비스의 정책 이름을 선택 합니다. 이 예제에서는 Azure SQL Database를 사용 합니다. **Azure Active Directory 인증만 사용 Azure SQL Database** 선택 합니다.
1. 새로 만들기 메뉴에서 **할당** 을 선택 합니다.

   > [!NOTE]
   > 메뉴의 JSON 스크립트는 SQL Database에 대 한 사용자 지정 Azure Policy를 빌드하기 위해 템플릿으로 사용할 수 있는 기본 제공 정책 정의를 보여 줍니다. 기본값은로 설정 됩니다 `Audit` .

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/assign-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대 한 Azure Policy 할당 스크린샷":::

1. **기본 사항** 탭에서 상자 옆의 선택기 (**...**)를 사용 하 여 **범위** 를 추가 합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/selecting-scope-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대 한 Azure Policy 범위를 선택 하는 스크린샷":::

1. **범위** 창의 드롭다운 메뉴에서 **구독** 을 선택 하 고이 정책에 대 한 **리소스 그룹** 을 선택 합니다. 완료 되 면 **선택** 단추를 사용 하 여 선택 항목을 저장 합니다.

   > [!NOTE]
   > 리소스 그룹을 선택 하지 않으면 정책이 전체 구독에 적용 됩니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/adding-scope-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대 한 Azure Policy 범위 추가 스크린샷":::

1. **기본** 탭으로 돌아가서 **할당 이름을** 사용자 지정 하 고 **설명**(선택 사항)을 제공 합니다. **정책 적용** 이 **사용 하도록 설정** 되어 있는지 확인 합니다.
1. **매개 변수** 탭으로 이동 합니다. **입력이 필요한 매개 변수만 표시** 옵션의 선택을 취소 합니다.
1. **적용** 에서 **거부** 를 선택 합니다. 이 설정을 사용 하면 Azure AD 전용 인증을 사용 하지 않고 논리 서버를 만들 수 없습니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/deny-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대 한 Azure Policy 효과 매개 변수의 스크린샷":::

1. **비준수 메시지** 탭에서 정책 위반이 발생 한 경우 표시 되는 정책 메시지를 사용자 지정할 수 있습니다. 메시지를 통해 서버를 만드는 동안 적용 된 정책을 사용자에 게 알릴 수 있습니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/non-compliance-message-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대 한 Azure Policy 비호환 메시지의 스크린샷":::

1. **검토 + 만들기** 를 선택합니다. 정책을 검토 하 고 **만들기** 단추를 선택 합니다.

> [!NOTE]
> 새로 만든 정책이 적용 되는 데 약간의 시간이 걸릴 수 있습니다.

## <a name="check-policy-compliance"></a>정책 준수 확인

**정책** 서비스에서 **준수** 설정을 확인 하 여 준수 상태를 확인할 수 있습니다.

이전에 정책에 지정한 할당 이름을 검색 합니다.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/compliance-policy-azure-ad-only-authentication.png" alt-text="Azure AD 전용 인증에 대 한 Azure Policy 준수의 스크린샷":::

Azure AD 전용 인증을 사용 하 여 논리 서버를 만든 후 정책 보고서는 **준수 상태** 시각적 개체의 리소스에서 카운터를 증가 시킵니다. 규격 또는 비규격 리소스를 확인할 수 있습니다.

정책에 포함 된 리소스 그룹에 이미 생성 된 서버가 포함 되어 있는 경우 정책 보고서는 규격 및 비규격 리소스를 표시 합니다.

> [!NOTE]
> 준수 보고서를 업데이트 하는 데 시간이 걸릴 수 있습니다. 리소스 만들기 또는 Azure AD 전용 인증 설정과 관련 된 변경 내용은 즉시 보고 되지 않습니다.    

## <a name="provision-a-server"></a>서버 프로 비전

그런 다음 Azure Policy 할당 한 리소스 그룹에서 논리 서버 또는 관리 되는 인스턴스를 프로 비전 할 수 있습니다. 서버를 만드는 동안 Azure AD 전용 인증을 사용 하는 경우 프로 비전이 성공 합니다. Azure AD 전용 인증을 사용 하지 않을 경우 프로 비전이 실패 합니다.

자세한 내용은 azure [SQL에서 AZURE AD 전용 인증을 사용 하도록 설정 된 서버 만들기](authentication-azure-ad-only-authentication-create-server.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [AZURE AD 전용 인증에 대 한 Azure Policy](authentication-azure-ad-only-authentication-policy.md) 개요
- [Azure SQL에서 Azure AD 전용 인증이 사용하도록 설정된 서버 만들기](authentication-azure-ad-only-authentication-create-server.md)
- [AZURE AD 전용 인증](authentication-azure-ad-only-authentication.md) 개요
