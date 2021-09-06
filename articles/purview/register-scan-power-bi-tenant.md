---
title: Power BI 테넌트 등록 및 검사(미리 보기)
description: Azure Purview Portal을 활용하여 Power BI 테넌트를 등록하고 검사하는 방법을 알아봅니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 07/28/2021
ms.openlocfilehash: c29070f85fe0024113b6d5d4857733b23b522615
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530535"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Power BI 테넌트 등록 및 검사(미리 보기)

본 문서에서는 Azure Purview Portal을 활용하여 Power BI 테넌트를 등록하고 검사하는 방법을 보여 줍니다.

> [!Note]
> Purview 인스턴스와 Power BI 테넌트가 동일한 Azure 테넌트 내에 존재하는 경우, Power BI 테넌트 검사를 설정하는 데에는 관리 ID(MSI) 인증만을 사용할 수 있습니다. 

## <a name="create-a-security-group-for-permissions"></a>사용 권한에 대한 보안 그룹 만들기

인증을 설정하려면 보안 그룹을 만들어 Purview 관리 ID를 여기에 추가하여야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 검색합니다.
1. [Azure Active Directory를 이용하여 기본 그룹 만들고 멤버 추가하기](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)에 따라 Azure Active Directory에 새로운 보안 그룹을 만듭니다.

    > [!Tip]
    > 사용하려는 보안 그룹을 이미 보유한 상태에서는 이 단계를 건너뛸 수 있습니다.

1. **그룹 형식** 으로 **보안** 을 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="보안 그룹 유형의 스크린샷":::

1. Purview 관리 ID를 해당 보안 그룹에 추가합니다. **멤버** 를 선택한 다음, **+ 멤버 추가** 를 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="카탈로그의 관리되는 인스턴스를 그룹에 추가하는 방법에 대한 스크린샷":::

1. 자신의 Purview 관리 ID를 찾아 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="이름을 검색하여 카탈로그를 추가하는 방법을 보여주는 스크린샷":::

    추가되었음을 알리는 성공 알림을 확인할 수 있습니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="성공적인 카탈로그 MSI 추가를 보여주는 스크린샷":::

## <a name="associate-the-security-group-with-the-tenant"></a>테넌트에 보안 그룹 연결하기

1. [Power BI 관리자 포털](https://app.powerbi.com/admin-portal/tenantSettings)에 로그인합니다.
1. **테넌트 설정** 페이지를 선택합니다.

    > [!Important]
    > 테넌트 설정 페이지를 확인하려면 Power BI 관리자여야 합니다.

1. **관리자 API 설정** > **서비스 사용자에게 읽기 전용 Power BI 관리자 APIs 사용을 허용하기(미리 보기)** 를 선택합니다.
1. **특정 보안 그룹** 을 선택합니다.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="서비스 사용자가 읽기 전용 Power BI 관리자 API 권한을 가져오도록 허용하는 방법을 보여 주는 이미지":::

1. **관리 API 설정** > **자세한 메타데이터로 관리 API 응답 향상** > 토글 사용을 선택하여 검사의 일환으로 Purview 데이터 맵에서 자동으로 Power BI 데이터 세트 메타데이터를 상세하게 검색하도록 허용합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-sub-artifacts.png" alt-text="하위 아티팩트 검사를 사용하는 Power BI 관리 포털 구성을 보여주는 이미지":::

    > [!Caution]
    > (자신의 Purview 관리 ID를 멤버로 하는) 만들어 놓은 보안 그룹이 읽기 전용 Power BI 관리자 API를 사용하도록 하는 경우, 해당 테넌트 내의 모든 Power BI 아티팩트에 관한 메타데이터(예: 대시보드 및 보고서 이름, 소유자, 설명 등)에도 해당 보안 그룹이 액세스할 수 있게 됩니다. 메타데이터를 Azure Purview에 끌어오면 Power BI 권한이 아니라 Purview 권한이 해당 메타데이터를 확인할 수 있는 인원을 결정합니다.

    > [!Note]
    > 개발자 설정에서 해당 보안 그룹을 제거할 수는 있지만 이미 추출된 메타데이터는 해당 Purview 계정에서 제거되지 않습니다. 원하는 경우 이를 개별적으로 삭제할 수는 있습니다.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Power BI 등록 및 검사 설정

이제 Purview 관리 ID 권한이 Power BI 테넌트의 관리자 API에 대한 연결 권한을 확보한 상태이므로, Azure Purview Studio에서 검사를 설정할 수 있습니다.

1. 왼쪽 탐색 메뉴에서 **데이터 맵** 을 선택합니다.

1. 그런 다음, **등록** 을 선택합니다.

    데이터 소스로 **Power BI** 를 선택합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="선택할 수 있는 데이터 원본의 목록을 보여주는 이미지":::

3. Power BI 인스턴스에 친숙한 이름을 부여합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Power BI 데이터 원본-친숙한 이름을 보여주는 이미지":::

    이름은 3~63자 길이 내의 문자, 숫자, 밑줄, 하이픈으로만 작성되어야 합니다.  공백이 없어야 합니다.

    기본적으로 시스템은 동일 Azure 구독 내에 존재하는 Power BI 테넌트를 찾습니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="등록된 Power BI 데이터 원본을 보여주는 이미지":::

    > [!Note]
    > Power BI의 경우, 데이터 소스 등록과 검사는 하나의 인스턴스에만 할 수 있습니다.


4. 검사 이름을 짓습니다. 그런 다음 개인 작업 영역 포함 여부를 선택합니다. 지원되는 유일한 인증 방법은 **관리 ID** 입니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Power BI 검사 설정을 보여주는 이미지":::

    > [!Note]
    > * 검사가 개인 작업 영역 포함 여부에 대한 구성을 전환하면 PowerBI 소스에 대한 전체 검사가 트리거됩니다.

5. 다음 단계로 계속하기 전에 **테스트 연결** 을 선택합니다. **테스트 연결** 에 실패하면 **보고서 보기** 를 선택하여 자세한 상태를 확인하고 문제를 해결합니다.
    1. 액세스 - 실패 상태는 사용자 인증에 실패했음을 의미합니다. 사용자 인증이 필요하지 않으므로 관리 ID를 사용하는 검색은 항상 통과합니다. [위임된 인증에 올바른 인증을 제공했는지 확인](register-scan-power-bi-tenant.md#register-and-scan-a-cross-tenant-power-bi)
    1. 자산(+ 계보) - 실패 상태는 Purview - Power BI 인증에 실패했음을 의미합니다. [Purview 관리 ID가 Power BI 관리 포털에 연결된 보안 그룹에 추가](register-scan-power-bi-tenant.md#create-a-security-group-for-permissions)되었는지 확인합니다.
    1. 자세한 메타데이터(향상됨) - 실패 상태는 Power BI 관리 포털이 **자세한 메타데이터를 사용하여 관리 API 응답 향상** 설정에 사용되지 않음을 의미합니다.
    
    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-test-connection-status-report.png" alt-text="테스트 연결 상태 보고서 페이지의 스크린샷":::
 
1. 검사 트리거를 설정합니다. 옵션으로는 **1회**, **매 7일마다**, **매 30일마다** 가 있습니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Purview 검사 스케줄러 스크린샷":::

6. **새로운 검사 검토하기** 에서, **저장 후 실행** 을 선택하여 검사를 시작합니다.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI 원본 저장 및 실행의 스크린샷":::

## <a name="register-and-scan-a-cross-tenant-power-bi"></a>교차 테넌트 Power BI 등록 및 검사 

교차 테넌트 시나리오에서 PowerShell을 사용하여 Power BI 테넌트를 등록하고 검사할 수 있습니다. UI 환경을 통해 Azure Purview 스튜디오를 사용하여 원격 테넌트의 자산을 찾아보고 검색할 수 있습니다. 

Power BI 테넌트가 있는 Azure AD 테넌트가 Azure Purview 계정이 프로비전되는 Azure AD 테넌트와 다른 경우 이 가이드를 사용하는 것이 좋습니다. 다음 단계를 수행하여 교차 테넌트 시나리오에서 Azure Purview에 Power BI 테넌트를 하나 이상 등록하고 검사합니다.

1. 원하는 위치에 [관리 검사 PowerShell 모듈](https://github.com/Azure/Purview-Samples/blob/master/Cross-Tenant-Scan-PowerBI/ManagedScanningPowerShell.zip)을 다운로드하고 콘텐츠를 추출합니다.

2. 컴퓨터의 Windows 작업 표시줄에 있는 검색 상자에 **PowerShell** 을 입력합니다. 검색 목록에서 **Windows PowerShell** 을 마우스 오른쪽 단추로 클릭한 다음, **관리자 권한으로 실행** 을 선택합니다.


3. 아직 설치하지 않았으면 컴퓨터에 모듈을 설치하고 가져옵니다.

   ```powershell
    Install-Module -name az
    Import-Module -name az
    Login-AzAccount
    ```

4. Power BI 테넌트가 있는 Azure AD 관리자 자격 증명을 사용하여 Azure 환경에 로그인합니다.

   ```powershell
    Login-AzAccount
    ```

5. PowerShell 창에 다음 명령을 입력하고 `<path-to-managed-scanning-powershell-modules>`를 추출한 모듈의 폴더 경로(예: `C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell`)로 바꿉니다.

   ```powershell
   dir -Path <path-to-managed-scanning-powershell-modules> -Recurse | Unblock-File
   ```

6. 다음 명령을 입력하여 PowerShell 모듈을 설치합니다.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell\Microsoft.DataCatalog.Management.Commands.dll'
   ```
   
7. 동일한 PowerShell 세션을 사용하여 다음 매개 변수를 설정합니다. `purview_tenant_id`를 Azure Purview가 배포되는 Azure AD 테넌트 ID로, `powerbi_tenant_id`를 Power BI 테넌트가 있는 Azure AD 테넌트로 업데이트합니다. `purview_account_name`은 추출한 Azure Purview 계정입니다.
   
   ```powershell
   $azuretenantId = '<purview_tenant_id>'
   $powerBITenantIdToScan = '<powerbi_tenant_id>'
   $purviewaccount = '<purview_account_name>'
   ```
8. 교차 테넌트 서비스 사용자를 만듭니다. 

   1. Power BI가 있는 Azure Active Directory 테넌트에서 앱 등록을 만듭니다. `password` 필드를 강력한 암호로, `app_display_name`을 Power BI 테넌트가 호스트되는 Azure AD 테넌트에 존재하지 않는 애플리케이션 이름으로 업데이트해야 합니다.

    ```powershell   
    $SecureStringPassword = ConvertTo-SecureString -String <'password'> -AsPlainText -Force
    $AppName = '<app_display_name>'
    New-AzADApplication -DisplayName $AppName -Password $SecureStringPassword
    ```
    
   2. Azure Active Directory 대시보드에서 새로 만든 애플리케이션을 선택하고 **앱 등록** 을 선택합니다. 다음과 같은 위임된 권한을 애플리케이션에 할당하고 테넌트에 대한 관리자 동의를 부여합니다.
   
         - Power BI 서비스     Tenant.Read.All
         - Microsoft Graph      openid

   3. Azure Active Directory 대시보드에서 새로 만든 애플리케이션을 선택하고 **인증** 을 선택합니다. **지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정(모든 Azure AD 디렉터리 - 다중 테넌트)** 을 선택합니다. 
   
   4. 웹 브라우저에서 다음 URL을 실행하여 서비스 사용자의 테넌트별 로그인 URL을 생성합니다.
   
     https://login.microsoftonline.com/<purview_tenant_id>/oauth2/v2.0/authorize?client_id=<client_id_to_delegate_the_pbi_admin>&scope=openid&response_type=id_token&response_mode=fragment&state=1234&nonece=67890
    
    매개 변수를 올바른 정보로 바꿔야 합니다. <purview_tenant_id>는 Azure Purview 계정이 프로비전되는 Azure Active Directory 테넌트 ID(GUID)입니다.
    <client_id_to_delegate_the_pbi_admin>은 서비스 사용자에 해당하는 애플리케이션 ID입니다.
   
   5. 관리자가 아닌 계정을 사용하여 로그인합니다. 이는 외부 테넌트에서 서비스 사용자를 프로비전하는 데 필요합니다.
   
   6. 메시지가 표시되면 _기본 프로필 보기_ 와 _액세스 권한이 부여된 데이터에 대한 액세스 유지_ 에 요청된 권한을 수락합니다.

9. `client_id_to_delegate_the_pbi_admin`을 새로 만든 애플리케이션의 애플리케이션(클라이언트) ID로 업데이트하고 PowerShell 세션에서 다음 명령을 실행합니다.
   
   ```powershell
   $ServicePrincipalId = '<client_id_to_delegate_the_pbi_admin>'
   ```

10.  Power BI 테넌트가 있는 Azure Active Directory 테넌트에 사용자 계정을 만들고 Azure AD 역할인 **Power BI 관리자** 를 할당합니다. `pbi_admin_username` 및 `pbi_admin_password`를 해당 정보로 업데이트하고 PowerShell 터미널에서 다음 줄을 추출합니다.

        ```powershell
        $UserName = '<pbi_admin_username>'
        $Password = '<pbi_admin_password>'
        ```

11.  Azure Purview 구독에서 Purview 계정을 찾고 Azure RBAC 역할을 사용하여 _Purview 데이터 원본 관리자_ 를 서비스 사용자와 Power BI 사용자에게 할당합니다.

12. 교차 테넌트 Power BI 테넌트를 Azure Purview 계정 내 새 데이터 원본으로 등록하려면 `service_principal_key`를 업데이트하고 PowerShell 세션에서 다음 cmdlet를 실행합니다.

    ```powershell
    Set-AzDataCatalogSessionSettings -DataCatalogSession -TenantId $azuretenantId -ServicePrincipalAuthentication -ServicePrincipalApplicationId $ServicePrincipalId -ServicePrincipalKey '<service_principal_key>' -Environment Production -DataCatalogAccountName $purviewaccount

    Set-AzDataCatalogDataSource -Name 'pbidatasource' -AccountType PowerBI -Tenant $powerBITenantIdToScan -Verbose
    ```

13. Azure Purview 내에서 새 검사를 만들고 실행하려면 PowerShell 세션에서 다음 cmdlet를 실행합니다.

    ```powershell
    Set-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan' -AuthorizationType PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password  -IncludePersonalWorkspaces $true -Verbose

    Start-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan'
    ```
### <a name="known-limitations"></a>알려진 제한 사항

-   교차 테넌트 시나리오의 경우 현재 Power BI 테넌트에서 등록하고 검사하는 데 사용할 수 있는 UX 환경이 없습니다.
-   Purview 스튜디오를 사용하여 PowerShell에 등록한 Power BI 교차 테넌트를 편집하면 일치하지 않는 검사 동작으로 인해 데이터 원본 등록이 손상될 수 있습니다.

        
## <a name="next-steps"></a>다음 단계

- [Azure Purview 데이터 카탈로그 찾아보기](how-to-browse-catalog.md)
- [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
