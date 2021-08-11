---
title: 호스트된 시험 사용에 대한 Azure Marketplace 구독 설정
description: Dynamics 365 for Customer Engagement 및 Dynamics 365 for Operations 시험 사용에 대한 Azure Marketplace 구독을 설정하는 방법을 설명합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 03/16/2020
ms.openlocfilehash: c82f68ee35ae95a424c0847be9a9cc770185af43
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112005738"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>호스트된 시험 사용에 대한 Azure Marketplace 구독 설정

이 문서에서는 **Dynamics 365 for Customer Engagement & Power Apps** 및 **Dynamics 365 for Operations** 시험 사용을 위한 환경에 대해 Azure Marketplace 구독을 설정하는 방법을 설명합니다.

## <a name="set-up-for-dynamics-365-for-customer-engagement--power-apps"></a>Dynamics 365 for Customer Engagement 및 Power Apps 설정

1. 관리자 계정으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 오른쪽 위 모서리의 계정 아이콘을 가리켜 Dynamics 365 시험 사용 인스턴스와 연결된 테넌트에 있는지 확인합니다. 올바른 테넌트에 있지 않은 경우 계정 아이콘을 선택하여 올바른 테넌트로 전환합니다.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="올바른 테넌트 선택.":::

3. **Dynamics 365 for Customer Engagement 플랜** 라이선스를 사용할 수 있는지 확인합니다.

    [![플랜 라이선스 확인.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Azure에서 AD(Azure Active Directory) 앱을 만듭니다. AppSource는 이 앱을 사용하여 테넌트에서 시험 사용 사용자를 프로비전하고 해제합니다.
    1. 필터 창에서 **Azure Active Directory** 를 선택합니다.
    2. **앱 등록** 을 선택합니다.

        [![앱 등록 선택.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. **새 등록** 을 선택합니다.
    4. 적절한 애플리케이션 이름을 입력합니다.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="애플리케이션을 등록합니다.":::

    5. 지원되는 계정 유형 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정** 을 선택합니다.
    6. **만들기** 를 선택하고, 앱이 만들어질 때까지 기다립니다.
    7. 앱이 만들어지면 개요 화면에 표시된 **애플리케이션 ID** 를 확인합니다. 이 값은 나중에 시험 사용을 구성할 때 필요합니다.
    8. **애플리케이션 관리** 에서 **API 권한** 을 선택합니다.
    9. **권한 추가** 를 선택한 다음 **Microsoft Graph API** 를 선택합니다.
    10. **애플리케이션** 권한 범주를 선택한 다음 **User.ReadWrite.All**, **Directory.Read.All**, **Directory.ReadWrite.All** 권한을 선택합니다.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="애플리케이션 권한 설정.":::

    11. 권한이 추가되면 **Microsoft에 관리자 동의 부여** 를 선택합니다.
    12. 메시지 경고에서 **예** 를 선택합니다.

        [![애플리케이션 권한을 성공적으로 부여했음을 보여줌.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. Azure AD 앱의 비밀을 생성하려면 다음과 같이 합니다.
        1. **애플리케이션 관리** 에서 **인증서 및 비밀** 을 선택합니다.
        2. 클라이언트 비밀 아래에서 **+ 새 클라이언트 비밀** 을 선택합니다.
        3. *시험 사용* 과 같은 설명을 입력하고 적절한 기간을 선택합니다. 시험 사용은 이 키가 만료되면 중단됩니다. 이 시점에서 AppSource에 새 키를 생성하고 제공해야 합니다.
        4. **추가** 를 선택하여 Azure 앱 비밀을 생성합니다. 이 블레이드는 정리되는 대로 숨겨지므로 이 값을 복사합니다. 이 값은 나중에 시험 사용을 구성할 때 필요합니다.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="클라이언트 암호 만들기.":::

5. 애플리케이션에 서비스 주체 역할을 추가하여 Azure AD 앱이 Azure 테넌트에서 사용자를 제거할 수 있도록 합니다.
    1. 관리 수준 PowerShell 명령 프롬프트를 엽니다.
    2. (MSOnline이 설치되어 있지 않은 경우) Install-Module MSOnline 명령을 실행합니다.
    3. Connect-MsolService 명령을 실행하면 팝업 창이 표시됩니다. 새로 만든 조직 테넌트로 로그인합니다.
    4. $applicationId = **<YOUR_APPLICATION_ID>** 명령을 실행합니다.
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId 명령을 실행합니다.
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal 명령을 실행합니다.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="계정에 로그인.":::

6. 위에서 만든 Azure 앱을 시험 사용 CRM 인스턴스에 애플리케이션 사용자로 추가합니다.
    1. **Azure Active Directory** 에 새 사용자를 추가합니다. 사용자를 만드는 데는 동일한 테넌트에 속하는 **이름** 및 **사용자 이름** 값만 필요하며 다른 필드는 기본값으로 둡니다. 사용자 이름 값을 복사합니다.
    2. **CRM 인스턴스** 에 로그인하고 **설정** > **보안** > **사용자** 를 선택합니다.
    3. **애플리케이션 사용자** 로 보기를 변경합니다.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="사용자에 대한 계정 정보 설정.":::

    4. 새 사용자를 추가합니다(양식이 애플리케이션 사용자에 대한 양식인지 확인).
    5. **기본 이메일** 및 **사용자 이름** 필드에 동일한 사용자 이름을 입력합니다. **애플리케이션 ID** 에 **Azure ApplicationId** 를 추가합니다.
    6. **전체 이름** 을 지정합니다.
    7. **저장** 을 선택합니다.
    8. **역할 관리** 를 선택합니다.
    9. *시스템 관리자* 와 같은 읽기, 쓰기 및 할당 역할 권한을 포함하는 사용자 지정이나 OOB 보안 역할을 할당합니다.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="역할 권한 선택.":::

    10. 또한 **다른 사용자를 대신** 권한을 사용하도록 설정합니다.
    11. 시험 사용을 위해 만든 사용자 지정 보안 역할을 애플리케이션 사용자에게 할당합니다.

## <a name="set-up-for-dynamics-365-for-operations"></a>Dynamics 365 for Operations 설정

1. 관리자 계정으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 오른쪽 위 모서리의 계정 아이콘을 가리켜 Dynamics 365 시험 사용 인스턴스와 연결된 테넌트에 있는지 확인합니다. 올바른 테넌트에 있지 않은 경우 계정 아이콘을 선택하여 올바른 테넌트로 전환합니다.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="올바른 테넌트 선택.":::

3. Azure에서 Azure AD 앱을 만듭니다. AppSource는 이 앱을 사용하여 테넌트에서 시험 사용 사용자를 프로비전하고 해제합니다.
    1. 필터 창에서 **Azure Active Directory** 를 선택합니다.
    2. **앱 등록** 을 선택합니다.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="앱 등록 선택.":::

    3. **새 등록** 을 선택합니다.
    4. 적절한 애플리케이션 이름을 입력합니다.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="애플리케이션을 등록합니다.":::

    5. 지원되는 계정 유형 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정** 을 선택합니다.
    6. **만들기** 를 선택하고, 앱이 만들어질 때까지 기다립니다.
    7. 앱이 만들어지면 개요 화면에 표시된 **애플리케이션 ID** 를 확인합니다. 이 값은 나중에 시험 사용을 구성할 때 필요합니다.
    8. **애플리케이션 관리** 에서 **API 권한** 을 선택합니다.
    9. **권한 추가** 를 선택한 다음 **Microsoft Graph API** 를 선택합니다.
    10. **애플리케이션** 권한 범주를 선택한 다음 **Directory.Read.All** 및 **Directory.ReadWrite.All** 권한을 선택합니다.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="애플리케이션 권한을 설정합니다.":::

    11. **권한 추가** 를 선택합니다.
    12. 권한이 추가되면 **Microsoft에 관리자 동의 부여** 를 선택합니다.
    13. 메시지 경고에서 **예** 를 선택합니다.

        [![애플리케이션 권한을 성공적으로 부여했음을 보여줌.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Azure AD 앱의 비밀을 생성하려면 다음과 같이 합니다.
        1. **애플리케이션 관리** 에서 **인증서 및 비밀** 을 선택합니다.
        2. 클라이언트 비밀 아래에서 **+ 새 클라이언트 비밀** 을 선택합니다.
        3. *시험 사용* 과 같은 설명을 입력하고 적절한 기간을 선택합니다. 시험 사용은 이 키가 만료되면 중단됩니다. 이 시점에서 AppSource에 새 키를 생성하고 제공해야 합니다.
        4. **추가** 를 선택하여 Azure 앱 비밀을 생성합니다. 이 블레이드는 정리되는 대로 숨겨지므로 이 값을 복사합니다. 이 값은 나중에 시험 사용을 구성할 때 필요합니다.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="클라이언트 암호 만들기.":::

4. 애플리케이션에 서비스 주체 역할을 추가하여 Azure AD 앱이 Azure 테넌트에서 사용자를 제거할 수 있도록 합니다.
    1. 관리 수준 PowerShell 명령 프롬프트를 엽니다.
    2. (MSOnline이 설치되어 있지 않은 경우) Install-Module MSOnline 명령을 실행합니다.
    3. Connect-MsolService 명령을 실행하면 팝업 창이 표시됩니다. 새로 만든 조직 테넌트로 로그인합니다.
    4. $applicationId = **<YOUR_APPLICATION_ID>** 명령을 실행합니다.
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId 명령을 실행합니다.
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal 명령을 실행합니다.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="계정에 로그인.":::

5. 이제 앱이 사용자를 관리할 수 있도록 하는 **Dynamics 365 for Operations** 에 위의 앱을 추가합니다.
    1. **Dynamics 365 for Operations** 인스턴스를 찾습니다.
    2. 왼쪽 위 모서리에서 삼선 메뉴(햄버거)를 클릭합니다.
    3. **시스템 관리** 를 선택합니다.
    4. **Azure Active Directory 애플리케이션** 을 선택합니다.
    5. **+새로 만들기** 를 선택합니다.
    6. 작업을 대신하여 수행할 **AZURE AD 앱의 클라이언트 ID** 를 입력합니다.

    > [!NOTE]
    > 작업을 수행하는 사용자 ID입니다. 일반적으로 인스턴스의 시스템 관리자이거나 다른 사용자를 추가할 권한이 있는 사용자입니다.

    [![작업을 수행하는 사용자 ID입니다. 일반적으로 인스턴스의 시스템 관리자이거나 다른 사용자를 추가할 권한이 있는 사용자입니다.](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
