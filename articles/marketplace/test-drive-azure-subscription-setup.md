---
title: 호스트된 시험 사용에 대한 Azure Marketplace 구독 설정
description: dataverse에서 dynamics 365 앱에 대 한 Azure Marketplace 구독을 설정 하 고 Power Apps 및 dynamics 365 Operations apps 테스트 드라이브를 설정 하는 방법을 설명 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 12/03/2021
ms.openlocfilehash: d8459eaf3a0a01b4d9538d4115cedf78366cba08
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133542081"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>호스트된 시험 사용에 대한 Azure Marketplace 구독 설정

이 문서에서는 **dataverse 및 Power Apps** 또는 **dynamics 365 Operations apps** 환경에서 테스트 드라이브에 대 한 Azure Marketplace 구독 및 dynamics 365 앱을 설정 하는 방법을 설명 합니다.

## <a name="set-up-for-dynamics-365-apps-on-dataverse-and-power-apps"></a>Dataverse에서 Dynamics 365 앱에 대 한 설정 및 Power Apps

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

             :::image type="content" source="./media/test-drive/new-client-secret.png" alt-text="새 클라이언트 암호를 추가 합니다.":::

        3. *시험 사용* 과 같은 설명을 입력하고 적절한 기간을 선택합니다. 이 키가 만료 되 면 테스트 드라이브가 중단 됩니다 .이 시점에서 AppSource에 새 키를 생성 하 여 제공 해야 하므로 최대 24 개월 기간을 사용 하는 것이 좋습니다.
        4. **추가** 를 선택하여 Azure 앱 비밀을 생성합니다. 이 블레이드는 나가는 대로 숨겨지므로 이 값을 복사합니다. 이 값은 나중에 시험 사용을 구성할 때 필요합니다.

            :::image type="content" source="./media/test-drive/add-client-secret-customer.png" alt-text="클라이언트 암호 만들기.":::

5. 애플리케이션에 서비스 주체 역할을 추가하여 Azure AD 앱이 Azure 테넌트에서 사용자를 제거할 수 있도록 합니다. 이 단계를 완료 하는 두 가지 옵션이 있습니다.

    **옵션 1**

    1. **AZURE AD 역할 및 관리자** 를 검색 하 고 서비스를 선택 합니다.

        :::image type="content" source="./media/test-drive/active-ad-roles.png" alt-text="Azure AD 역할 및 관리자를 검색 하는 방법을 보여 줍니다.":::

    2. **모든 역할** 페이지에서 **사용자 관리자** 역할을 검색 하 고 **사용자 관리자** 를 두 번 클릭 합니다.

        :::image type="content" source="./media/test-drive/user-administrator.png" alt-text="사용자 관리자를 검색 하 고 선택 하는 방법을 보여 줍니다.":::

    3. **할당 추가** 를 선택 합니다.

        :::image type="content" source="./media/test-drive/add-assignments-1.png" alt-text="할당 추가 단추를 표시 합니다.":::

    4. 위에서 만든 앱을 검색 하 고 선택한 다음 **추가** 를 선택 합니다.

        :::image type="content" source="./media/test-drive/add-assignments-2.png" alt-text="성공적인 앱 할당을 표시 합니다.":::

    5. 서비스 주체 역할이 응용 프로그램에 할당 되었습니다.

        :::image type="content" source="./media/test-drive/successful-assignment.png" alt-text="응용 프로그램에 성공적으로 할당 된 서비스 사용자 역할을 보여 줍니다.":::

    **옵션 2**

    1. 관리 수준 PowerShell 명령 프롬프트를 엽니다.
    2. (MSOnline이 설치되어 있지 않은 경우) Install-Module MSOnline 명령을 실행합니다.
    3. Connect-MsolService 명령을 실행하면 팝업 창이 표시됩니다. 새로 만든 조직 테넌트로 로그인합니다.
    4. $applicationId = **<YOUR_APPLICATION_ID>** 명령을 실행합니다.
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId 명령을 실행합니다.
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal 명령을 실행합니다.

         :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="계정에 로그인.":::

6. 새 보안 그룹을 만들고 캔버스 앱(Power Apps)에 추가합니다. 이 단계는 고객 Engagement의 Dynamics 365에만 적용 되며, PowerApps 앱 옵션을 사용 하는 PowerApps 제품 &.
    1. 새 보안 그룹을 만듭니다.
        1. **Azure Active Directory** 로 이동합니다.
        1. **관리** 에서 **그룹** 을 선택합니다.
        1. **+ 새 그룹** 을 선택합니다.
        1. **보안 그룹** 형식을 선택합니다. 
        1. **그룹 이름** 에 대해 *TestDriveSecurityGroup* 을 입력합니다.
        1. **시험 사용에 대한 보안 그룹** 과 같은 설명을 추가합니다.
        1. 다른 필드는 기본값으로 유지하고 **만들기** 를 선택합니다.

            :::image type="content" source="./media/test-drive/create-new-group.png" alt-text="새 보안 그룹을 만드는 방법을 보여 줍니다.":::

    1. 캔버스 앱(Power Apps)에 만든 보안 그룹을 추가합니다.
        1. **PowerApps** 포털 페이지를 열고 로그인합니다.
        1. **앱** 을 선택한 다음, 앱에서 줄임표를 선택합니다.
        1. **공유** 를 선택합니다.
        1. 이전 단계에서 만든 **TestDriveSecurityGroup** 보안 그룹을 검색합니다.
        1. 보안 그룹에 **데이터 권한** 을 추가합니다.
        1. **이메일 보내기** 초대 확인란의 선택을 취소합니다.
        1. **공유** 를 선택합니다.
    
            > [!NOTE]
            > 캔버스 앱(Power Apps)에 대한 CE/Dataverse 이외의 백 엔드 데이터 원본을 사용하는 경우:
            > - 위의 생성된 보안 그룹이 데이터 원본에 액세스할 수 있도록 허용합니다. 예를 들어 SharePoint 데이터 원본입니다.
            > - SharePoint를 열고 보안 그룹과 데이터 테이블을 공유합니다.

7. 방금 만든 Azure 앱을 시험 사용 CRM 인스턴스에 애플리케이션 사용자로 추가합니다. 이 단계는 dataverse의 Dynamics 365 앱 및 Power Apps 제품에만 적용 됩니다.
    1. **CRM 인스턴스** 에 로그인하고 **설정** > **보안** > **사용자** 를 선택합니다.
    2. **애플리케이션 사용자** 로 보기를 변경합니다.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="사용자에 대한 계정 정보 설정.":::

    3. 새 사용자를 추가합니다(양식이 애플리케이션 사용자에 대한 양식인지 확인).
    4. **응용 프로그램 ID** 에서 위의 만든 **Azure ApplicationId** 를 추가 합니다.
    5. **저장** 을 선택합니다.
    6. **역할 관리** 를 선택합니다.
    7. *시스템 관리자* 와 같은 읽기, 쓰기 및 할당 역할 권한을 포함하는 사용자 지정이나 OOB 보안 역할을 할당합니다.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="역할 권한 선택.":::

    10. **다른 사용자 권한을 대신** 하 여 동작을 사용 하도록 설정 합니다.
    11. 시험 사용을 위해 만든 사용자 지정 보안 역할을 애플리케이션 사용자에게 할당합니다.

## <a name="set-up-for--dynamics-365-operations-apps"></a>Dynamics 365 작업 앱에 대 한 설정

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
        4. **추가** 를 선택하여 Azure 앱 비밀을 생성합니다. 이 블레이드는 나가는 대로 숨겨지므로 이 값을 복사합니다. 이 값은 나중에 시험 사용을 구성할 때 필요합니다.

            :::image type="content" source="./media/test-drive/add-client-secret-operations.png" alt-text="클라이언트 암호 추가를 표시 합니다.":::

4. 애플리케이션에 서비스 주체 역할을 추가하여 Azure AD 앱이 Azure 테넌트에서 사용자를 제거할 수 있도록 합니다.
    1. 관리 수준 PowerShell 명령 프롬프트를 엽니다.
    2. (MSOnline이 설치되어 있지 않은 경우) Install-Module MSOnline 명령을 실행합니다.
    3. Connect-MsolService 명령을 실행하면 팝업 창이 표시됩니다. 새로 만든 조직 테넌트로 로그인합니다.
    4. $applicationId = **<YOUR_APPLICATION_ID>** 명령을 실행합니다.
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId 명령을 실행합니다.
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal 명령을 실행합니다.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="계정에 로그인.":::

5. 이제 **Dynamics 365 운영 앱** 에 위의 앱을 추가 하 여 앱이 사용자를 관리할 수 있도록 합니다.
    1. **Dynamics 365 작업 앱** 인스턴스를 찾습니다.
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
