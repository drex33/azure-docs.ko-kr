---
title: Azure Virtual Desktop(클래식) 테넌트 호스트 풀 만들기 - Azure
description: Azure Virtual Desktop(클래식) 테넌트 환경을 설정하는 동안 테넌트 및 호스트 풀 문제를 해결하는 방법을 알아봅니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 61aea8002faa92c88081c78bc9ff05ec7e053094
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124818513"
---
# <a name="tenant-and-host-pool-creation-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop(클래식)에서 테넌트 및 호스트 풀 만들기

>[!IMPORTANT]
>이 내용은 Azure Resource Manager의 Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager의 Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../troubleshoot-set-up-issues.md)를 참조하세요.

이 문서에서는 Azure Virtual Desktop 테넌트 및 관련 세션 호스트 풀 인프라의 초기 설정 중 발생하는 문제에 대해 설명합니다.

## <a name="provide-feedback"></a>피드백 제공

제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Azure Virtual Desktop 서비스에 대해 토론하려면 [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하세요.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise 다중 세션 이미지 획득

Windows 10 Enterprise 다중 세션 이미지를 사용하려면 Azure Marketplace으로 이동하여 **시작** > **Microsoft Windows 10** > [Windows 10 Enterprise for Virtual Desktops, Version 1809(가상 데스크톱용 Windows 10 Enterprise, 버전 1809)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)를 선택합니다.

> [!div class="mx-imgBorder"]
> ![Windows 10 Enterprise for Virtual Desktops, Version 1809(가상 데스크톱용 Windows 10 Enterprise, 버전 1809)을 선택하는 스크린샷](../media/AzureMarketPlace.png)

## <a name="creating-azure-virtual-desktop-tenant&quot;></a>Azure Virtual Desktop 테넌트 만들기

이 섹션에서는 Azure Virtual Desktop 테넌트를 만들 때 발생할 수 있는 문제를 다룹니다.

### <a name=&quot;error-aadsts650052-the-app-needs-access-to-a-service&quot;></a>오류: AADSTS650052 앱이 서비스에 액세스해야 합니다.

Raw 오류 예:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**원인:** Azure Active Directory 인스턴스의 Azure Virtual Desktop에 동의가 부여되지 않았습니다.

**해결 방법:** [이 가이드에 따라](./tenant-setup-azure-active-directory.md#grant-permissions-to-azure-virtual-desktop) 동의를 부여합니다.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>오류: 사용자에게 관리 서비스를 쿼리할 수 있는 권한이 없습니다.

> [!div class="mx-imgBorder"]
> ![사용자가 관리 서비스를 쿼리할 권한이 없는 PowerShell 창의 스크린샷](../media/UserNotAuthorizedNewTenant.png)

Raw 오류 예:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**원인:** 로그인한 사용자에게 Azure Active Directory의 TenantCreator 역할이 할당되지 않았습니다.

**해결 방법:** [Azure Active Directory 테넌트의 사용자에게 TenantCreator 애플리케이션 역할 할당](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)의 지침을 따릅니다. 이 지침을 수행하면 TenantCreator 역할에 사용자가 할당됩니다.

> [!div class="mx-imgBorder"]
> ![TenantCreator 역할이 할당된 스크린샷](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-azure-virtual-desktop-session-host-vms"></a>Azure Virtual Desktop 세션 호스트 VM 만들기

세션 호스트 VM은 여러 가지 방법으로 만들 수 있지만 Azure Virtual Desktop 팀은 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 제품과 관련된 VM 프로비저닝 문제만 지원합니다. 자세한 내용은 [Azure Virtual Desktop 사용 문제 - 호스트 풀 Azure Marketplace 제품 프로비저닝](#issues-using-azure-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)을 참조하세요.

## <a name="issues-using-azure-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Azure Virtual Desktop 사용 문제 - 호스트 풀 Azure Marketplace 제품 프로비저닝

Azure Virtual Desktop - 호스트 풀 프로비저닝 템플릿은 Azure Marketplace에서 사용할 수 있습니다.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>오류: GitHub의 링크를 사용할 때 "체험 계정 만들기"라는 메시지가 나타납니다.

> [!div class="mx-imgBorder"]
> ![체험 계정 만들기 스크린샷](../media/be615904ace9832754f0669de28abd94.png)

**원인 1**: Azure에 로그인하는 데 사용된 계정에 활성 구독이 없거나 사용된 계정에 구독을 볼 수 있는 권한이 없습니다.

**해결 방법 1:** 세션 호스트 VM이 배포될 구독에 대한 기여자 액세스 권한(최소)이 있는 계정으로 로그인합니다.

**원인 2:** 사용 중인 구독이 Microsoft CSP(클라우드 서비스 공급자) 테넌트의 일부입니다.

**해결 방법 2:** **새 Azure Virtual Desktop 호스트 풀 만들기 및 프로비저닝** 을 위한 GitHub 위치로 이동하고 다음 지침을 따릅니다.

1. **Azure에 배포** 를 마우스 오른쪽 단추로 클릭하고 **링크 주소 복사** 를 선택합니다.
2. **메모장** 을 열고 링크를 붙여 넣습니다.
3. # 문자 앞에 CSP 최종 고객 테넌트 이름을 삽입합니다.
4. 브라우저에서 새 링크를 열면 Azure Portal이 템플릿을 로드합니다.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>오류: "template deployment is not valid(템플릿 배포가 잘못됨)" 오류가 표시됩니다.

> [!div class="mx-imgBorder"]
> !["template deployment is not valid(템플릿 배포가 잘못됨)" 오류 스크린샷](../media/troubleshooting-marketplace-validation-error-generic.png)

특정 작업을 수행하기 전에 활동 로그를 확인하여 실패한 배포 유효성 검사에 대한 자세한 오류를 확인해야 합니다.

활동 로그에서 오류를 보려면 다음을 수행합니다.

1. 현재 Azure Marketplace 배포 제품을 종료합니다.
2. 위쪽의 검색 창에서 **활동 로그** 를 찾아 선택합니다.
3. 상태가 **실패** 인 **배포 유효성 검사** 라는 활동을 찾아 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![**실패** 상태인 개별 **배포 유효성 검사** 활동의 스크린샷](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. JSON을 선택하고 "statusMessage" 필드가 나타날 때까지 화면 아래쪽으로 스크롤합니다.

   > [!div class="mx-imgBorder"]
   > ![JSON 텍스트의 statusMessage 속성 주위에 빨간색 상자가 있는 실패한 활동의 스크린샷](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

작업 템플릿이 할당량 한도를 초과하는 경우 다음 작업 중 하나를 수행하여 이를 수정할 수 있습니다.

 - 처음 사용한 매개 변수로 Azure Marketplace를 실행하지만 이번에는 더 적은 수의 VM과 VM 코어를 사용합니다.
 - 브라우저의 **statusMessage** 필드에 표시되는 링크를 열어 지정된 VM SKU에 대한 Azure 구독의 할당량을 늘리는 요청을 제출합니다.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager 템플릿 및 PowerShell DSC(Desired State Configuration) 오류

다음 지침에 따라 Azure Resource Manager 템플릿 및 PowerShell DSC의 실패한 배포 문제를 해결합니다.

1. [Azure Resource Manager에서 배포 작업 보기](../../azure-resource-manager/templates/deployment-history.md)를 사용하여 배포 오류를 검토합니다.
2. 배포에 오류가 없으면 [리소스에 대한 작업을 감사하기 위해 활동 로그 보기](../../azure-monitor/essentials/activity-log.md)를 사용하여 활동 로그의 오류를 검토합니다.
3. 오류가 식별되면 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../../azure-resource-manager/templates/common-deployment-errors.md)의 오류 메시지와 리소스를 사용하여 문제를 해결합니다.
4. 이전 배포 중에 만들어진 모든 리소스를 삭제하고 템플릿을 다시 배포하는 작업을 다시 시도합니다.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>오류: 배포하지 못했습니다….\<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![배포 실패 스크린샷](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Raw 오류 예:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**원인 1**: VM을 도메인에 가입하기 위해 제공된 자격 증명이 잘못되었습니다.

**해결 방법 1**: [세션 호스트 VM 구성](troubleshoot-vm-configuration-2019.md)에서 도메인에 가입되지 않은 VM에 대한 "잘못된 자격 증명" 오류를 참조하세요.

**원인 2**: 도메인 이름이 확인되지 않습니다.

**해결 방법 2**: [세션 호스트 VM 구성](troubleshoot-vm-configuration-2019.md)에서 [오류: 도메인 이름이 확인되지 않습니다](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve)를 참조하세요.

**원인 3**: 가상 네트워크(VNET) DNS 구성이 **기본값** 으로 설정되어 있습니다.

이 오류를 해결하려면 다음 작업을 수행합니다.

1. Azure Portal를 열고 **가상 네트워크** 탭으로 이동합니다.
2. VNET을 찾은 다음, **DNS 서버** 를 선택합니다.
3. DNS 서버 메뉴가 화면 오른쪽에 표시됩니다. 해당 메뉴에서 **사용자 지정** 을 선택합니다.
4. 사용자 지정 아래에 나열된 DNS 서버가 도메인 컨트롤러 또는 Active Directory 도메인과 일치하는지 확인합니다. DNS 서버가 표시되지 않으면 **DNS 서버 추가** 필드에 해당 값을 입력하여 추가할 수 있습니다.

### <a name="error-your-deployment-failedunauthorized"></a>오류: 배포하지 못했습니다...\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**원인:** 사용 중인 구독은 고객이 배포를 시도하는 지역의 필수 기능에 액세스할 수 없는 유형입니다. 예를 들어 MSDN, 무료 또는 교육 구독에 이 오류가 표시될 수 있습니다.

**해결 방법**: 구독 유형 또는 지역을 필요한 기능에 액세스할 수 있는 것으로 변경합니다.

### <a name="error-vmextensionprovisioningerror"></a>오류: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![터미널 프로비저닝 상태가 실패인 배포 실패 스크린샷](../media/7aaf15615309c18a984673be73ac969a.png)

**원인 1:** Azure Virtual Desktop 환경에서 일시적 오류가 발생했습니다.

**원인 2**: 연결 시 일시적 오류가 발생했습니다.

**해결 방법**: PowerShell을 사용해 로그인하여 Azure Virtual Desktop 환경이 정상 상태인지 확인합니다. [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell-2019.md)에서 수동으로 VM 등록을 완료합니다.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>오류: 지정된 관리자 사용자 이름이 허용되지 않습니다.

> [!div class="mx-imgBorder"]
> ![지정된 관리자가 허용되지 않는 배포 실패 스크린샷](../media/f2b3d3700e9517463ef88fa41875bac9.png)

Raw 오류 예:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**원인**: 제공된 암호에 금지된 부분 문자열(admin, administrator, root)이 포함되어 있습니다.

**해결 방법**: 사용자 이름을 업데이트하거나 다른 사용자를 사용합니다.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>오류: 확장을 처리하는 동안 VM이 오류를 보고했습니다.

> [!div class="mx-imgBorder"]
> ![완료되었으나 터미널 프로비저닝 상태가 배포 실패인 리소스 작업 스크린샷](../media/49c4a1836a55d91cd65125cf227f411f.png)

Raw 오류 예:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**원인**: PowerShell DSC 확장에서 VM에 대한 관리자 액세스 권한을 얻을 수 없습니다.

**해결 방법**: 사용자 이름 및 암호에 가상 머신에 대한 관리자 액세스 권한이 있는지 확인하고 Azure Resource Manager 템플릿을 다시 실행합니다.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>오류: DeploymentFailed -PowerShell DSC 구성 'FirstSessionHost'가 완료되었으나 오류가 발생했습니다.

> [!div class="mx-imgBorder"]
> ![PowerShell DSC 구성 'FirstSessionHost'가 완료되었으나 오류가 발생한 배포 실패 스크린샷](../media/64870370bcbe1286906f34cf0a8646ab.png)

Raw 오류 예:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**원인**: PowerShell DSC 확장에서 VM에 대한 관리자 액세스 권한을 얻을 수 없습니다.

**해결 방법**: 제공된 사용자 이름 및 암호에 가상 머신에 대한 관리자 액세스 권한이 있는지 확인하고 Azure Resource Manager 템플릿을 다시 실행합니다.

### <a name="error-deploymentfailed--invalidresourcereference"></a>오류: DeploymentFailed – InvalidResourceReference

Raw 오류 예:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**원인**: 리소스 그룹 이름의 일부가 템플릿에서 만드는 특정 리소스에 사용됩니다. 기존 리소스와 일치하는 이름으로 인해 템플릿은 다른 그룹에서 기존 리소스를 선택할 수 있습니다.

**해결 방법**: Azure Resource Manager 템플릿을 실행하여 세션 호스트 VM을 배포하는 경우 구독 리소스 그룹 이름에 대해 처음 두 문자를 고유하게 만듭니다.

### <a name="error-deploymentfailed--invalidresourcereference"></a>오류: DeploymentFailed – InvalidResourceReference

Raw 오류 예:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**원인**: 이 오류는 Azure Resource Manager 템플릿으로 만든 NIC가 VNET에 이미 있는 다른 NIC와 동일한 이름을 갖고 있기 때문입니다.

**해결 방법**: 다른 호스트 접두사를 사용합니다.

### <a name="error-deploymentfailed--error-downloading"></a>오류: DeploymentFailed – 다운로드 오류

Raw 오류 예:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**원인**: 이 오류는 Azure Resource Manager 템플릿에 연결된 zip 파일의 다운로드를 차단하는 고정 경로, 방화벽 규칙 또는 NSG 때문입니다.

**해결 방법**: 차단 고정 경로, 방화벽 규칙 또는 NSG를 제거합니다. 필요에 따라 텍스트 편집기에서 Azure Resource Manager 템플릿 json 파일을 열고, zip 파일에 대한 링크를 가져온 다음, 허용된 위치에 리소스를 다운로드합니다.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>오류: 사용자에게 관리 서비스를 쿼리할 수 있는 권한이 없습니다.

Raw 오류 예:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**원인:** 지정된 Azure Virtual Desktop 테넌트 관리자에게 유효한 역할 할당이 없습니다.

**해결 방법:** Azure Virtual Desktop 테넌트를 만든 사용자가 Azure Virtual Desktop PowerShell에 로그인하고 시도한 사용자에게 역할을 할당해야 합니다. GitHub Azure Resource Manager 템플릿 매개 변수를 실행하는 경우 PowerShell 명령을 사용하여 다음 지침을 따르세요.

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Azure Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>오류: 사용자에게 Azure AD MFA(Multi-Factor Authentication)가 필요합니다.

> [!div class="mx-imgBorder"]
> ![MFA(Multi-Factor Authentication) 부족으로 인한 배포 실패 스크린샷](../media/MFARequiredError.png)

Raw 오류 예:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**원인:** 지정된 Azure Virtual Desktop 테넌트 관리자가 로그인하려면 Azure AD MFA(Multi-Factor Authentication)가 필요합니다.

**해결 방법:** [자습서: PowerShell을 사용하여 서비스 주체 및 역할 할당 만들기](create-service-principal-role-powershell.md)의 단계에 따라 서비스 주체를 만들고 Azure Virtual Desktop 테넌트에 대한 역할을 할당합니다. 서비스 주체로 Azure Virtual Desktop에 로그인할 수 있는지 확인한 후 사용 중인 방법에 따라 Azure Marketplace 제품 또는 GitHub Azure Resource Manager 템플릿을 다시 실행합니다. 아래 지침에 따라 메서드에 대한 올바른 매개 변수를 입력합니다.

Azure Marketplace 제품을 실행하는 경우 다음 매개 변수의 값을 제공하여 Azure Virtual Desktop에 올바르게 인증합니다.

- Azure Virtual Desktop 테넌트 RDS 소유자: 서비스 주체
- 애플리케이션 ID: 만든 새 서비스 주체의 애플리케이션 ID
- 암호/암호 확인: 서비스 주체에 대해 생성한 암호 비밀
- Azure AD 테넌트 ID: 만든 서비스 주체의 Azure AD 테넌트 ID

GitHub Azure Resource Manager 템플릿을 실행하는 경우 다음 매개 변수의 값을 제공하여 Azure Virtual Desktop에 올바르게 인증합니다.

- 테넌트 관리자 UPN(사용자 계정 이름) 또는 애플리케이션 ID: 만든 새 서비스 주체의 애플리케이션 ID
- 테넌트 관리자 암호: 서비스 주체에 대해 생성한 암호 비밀
- IsServicePrincipal: **true**
- AadTenantId: 만든 서비스 주체의 Azure AD 테넌트 ID

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>오류: 가상 네트워크를 구성할 때 vmSubnet을 사용할 수 없음

**원인:** Azure Virtual Desktop Marketplace 템플릿에서 UI는 템플릿에 지정된 총 VM 수만큼 사용 가능한 최소한의 IP 주소가 있는 서브넷만 표시합니다. 서브넷에서 사용 가능한 실제 IP 주소 수는 배포 중인 새 VM 수와 동일해야 하지만 현재 UI로 계산할 수 없습니다.

**해결 방법:** Marketplace UI를 사용하고 추가되는 VM 수만큼 사용 가능한 IP 주소 이상의 서브넷을 지정할 수 있습니다. [기존 배포를 다시 배포](expand-existing-host-pool-2019.md#redeploy-from-azure)하거나 [GitHub의 기본 ARM 템플릿을 사용하여 배포](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)할 때 "**existingSubnetName**" 매개 변수에 서브넷 이름을 지정하면 됩니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop과 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview-2019.md)을 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration-2019.md)을 참조하세요.
- Azure Virtual Desktop 클라이언트 연결 문제를 해결하려면 [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection-2019.md)을 참조하세요.
- 원격 데스크톱 클라이언트와 관련된 문제를 해결하려면 [원격 데스크톱 클라이언트 문제 해결](../troubleshoot-client.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell-2019.md)을 참조하세요.
- 서비스에 대한 자세한 내용은 [Azure Virtual Desktop 환경](environment-setup-2019.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../../azure-monitor/essentials/activity-log.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../../azure-resource-manager/templates/deployment-history.md)를 참조하세요.