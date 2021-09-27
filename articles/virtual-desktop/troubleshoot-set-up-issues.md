---
title: Azure Virtual Desktop 환경 호스트 풀 생성 - Azure
description: Azure Virtual Desktop 환경을 설정하는 동안 테넌트 및 호스트 풀 문제를 해결하는 방법을 알아봅니다.
author: Heidilohr
ms.topic: troubleshooting
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 7e2c4c6a68bb8db4434a233e7b7c658074ed015a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804128"
---
# <a name="host-pool-creation"></a>호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md)를 참조하세요.

이 문서에서는 Azure Virtual Desktop 테넌트 및 관련 세션 호스트 풀 인프라의 초기 설정 중 발생하는 문제에 대해 설명합니다.

## <a name="provide-feedback"></a>피드백 제공

제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Azure Virtual Desktop 서비스에 대해 토론하려면 [Azure Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum)를 방문하세요.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise 다중 세션 이미지 획득

Windows 10 Enterprise 다중 세션 이미지를 사용 하려면 Azure Marketplace으로 이동하여 **시작** > **Microsoft Windows 10** >; 및 [Windows 10 Enterprise 다중 세션 버전 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)를 선택합니다.

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>Azure Portal을 사용하여 호스트 풀을 만들 때 발생하는 문제

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>오류: 서비스에 액세스할 때 "체험 계정 만들기"가 표시됩니다.

> [!div class="mx-imgBorder"]
> !["체험 계정 만들기" 메시지를 표시하는 Azure Portal을 보여주는 이미지](media/create-new-account.png)

**원인**: Azure에 로그인한 계정에 활성 구독이 없거나, 계정에 구독을 볼 수 있는 권한이 없습니다.

**해결 방법**: 최소한 기여자 수준 액세스 권한이 있는 계정으로 세션 호스트 VM(가상 머신)을 배포하는 구독에 로그인합니다.

### <a name="error-exceeding-quota-limit"></a>오류: "할당량 한도 초과"

작업이 할당량 한도를 초과하는 경우 다음 작업 중 하나를 수행할 수 있습니다.

- 동일한 매개 변수를 사용하지만 VM 및 VM 코어 수가 적은 새 호스트 풀을 만듭니다.

- 브라우저의 statusMessage 필드에 표시되는 링크를 열어 지정된 VM SKU에 대한 Azure 구독의 할당량을 늘리는 요청을 제출합니다.

### <a name="error-cant-see-user-assignments-in-app-groups"></a>오류: 앱 그룹에서 사용자 할당을 볼 수 없습니다.

**원인**: 이 오류는 일반적으로 구독을 하나의 AD(Azure Active Directory) 테넌트에서 다른 테넌트로 이동한 후에 발생합니다. 이전 할당이 여전히 이전 Azure AD 테넌트에 연결된 경우에는 Azure Portal에서 추적을 잃게 됩니다.

**해결 방법**: 사용자를 앱 그룹에 다시 할당해야 합니다.

### <a name="i-only-see-us-when-setting-the-location-for-my-service-objects"></a>내 서비스 개체의 위치를 설정하는 경우 US만 표시

**원인**: Azure는 현재 Azure Virtual Desktop 서비스에 대해 해당 지역을 지원하지 않습니다. 지원되는 지역에 대해 알아보려면 [데이터 위치](data-locations.md)를 확인하세요. Azure Virtual Desktop이 위치를 지원하지만 위치를 선택하려고 할 때 여전히 표시되지 않으면 리소스 공급자가 아직 업데이트되지 않은 것입니다.

**해결 방법**: 최신 지역 목록을 가져오려면 리소스 공급자를 다시 등록합니다.

1. **구독** 으로 이동하고 관련 구독을 선택합니다.
2. **리소스 공급자** 를 선택합니다.
3. **Microsoft.DesktopVirtualization** 을 선택한 다음, 작업 메뉴에서 **다시 등록** 을 선택합니다.

리소스 공급자를 다시 등록하면 특정 UI 피드백 또는 업데이트 상태가 표시되지 않습니다. 또한 다시 등록 프로세스는 기존 환경에 방해가 되지 않습니다.

## <a name="azure-resource-manager-template-errors"></a>Azure Resource Manager 템플릿 오류

다음 지침에 따라 Azure Resource Manager 템플릿 및 PowerShell DSC의 실패한 배포 문제를 해결합니다.

1. [Azure Resource Manager에서 배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 사용하여 배포 오류를 검토합니다.
2. 배포에 오류가 없으면 [리소스에 대한 작업을 감사하기 위해 활동 로그 보기](../azure-monitor/essentials/activity-log.md)를 사용하여 활동 로그의 오류를 검토합니다.
3. 오류가 식별되면 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/templates/common-deployment-errors.md)의 오류 메시지와 리소스를 사용하여 문제를 해결합니다.
4. 이전 배포 중에 만들어진 모든 리소스를 삭제하고 템플릿을 다시 배포하는 작업을 다시 시도합니다.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>오류: 배포하지 못했습니다….\<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![배포 실패 스크린샷](media/failure-joindomain.png)

Raw 오류 예:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**원인 1**: VM을 도메인에 가입하기 위해 제공된 자격 증명이 잘못되었습니다.

**해결 방법 1**: [세션 호스트 VM 구성](troubleshoot-vm-configuration.md)에서 도메인에 가입되지 않은 VM에 대한 "잘못된 자격 증명" 오류를 참조하세요.

**원인 2**: 도메인 이름이 확인되지 않습니다.

**해결 방법 2**: [세션 호스트 VM 구성](troubleshoot-vm-configuration.md)에서 [오류: 도메인 이름이 확인되지 않습니다](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve)를 참조하세요.

**원인 3**: 가상 네트워크(VNET) DNS 구성이 **기본값** 으로 설정되어 있습니다.

이 오류를 해결하려면 다음 작업을 수행합니다.

1. Azure Portal을 열고 **가상 네트워크** 탭으로 이동합니다.
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
> ![터미널 프로비저닝 상태가 실패인 배포 실패 스크린샷](media/failure-vmextensionprovisioning.png)

**원인 1:** Azure Virtual Desktop 환경에서 일시적 오류가 발생했습니다.

**원인 2**: 연결 시 일시적 오류가 발생했습니다.

**해결 방법**: PowerShell을 사용해 로그인하여 Azure Virtual Desktop 환경이 정상 상태인지 확인합니다. [PowerShell을 사용하여 호스트 풀 만들기](create-host-pools-powershell.md)에서 수동으로 VM 등록을 완료합니다.

### <a name="error-the-admin-username-specified-isnt-allowed"></a>오류: 지정된 관리자 사용자 이름이 허용되지 않습니다.

> [!div class="mx-imgBorder"]
> ![지정된 관리자가 허용되지 않는 배포 실패 스크린샷](media/failure-username.png)

Raw 오류 예:

```Error
 { …{ "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**원인**: 제공된 암호에 금지된 부분 문자열(admin, administrator, root)이 포함되어 있습니다.

**해결 방법**: 사용자 이름을 업데이트하거나 다른 사용자를 사용합니다.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>오류: 확장을 처리하는 동안 VM이 오류를 보고했습니다.

> [!div class="mx-imgBorder"]
> ![완료되었으나 터미널 프로비저닝 상태가 배포 실패인 리소스 작업 스크린샷](media/failure-processing.png)

Raw 오류 예:

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**원인**: PowerShell DSC 확장에서 VM에 대한 관리자 액세스 권한을 얻을 수 없습니다.

**해결 방법**: 사용자 이름 및 암호에 가상 머신에 대한 관리자 액세스 권한이 있는지 확인하고 Azure Resource Manager 템플릿을 다시 실행합니다.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>오류: DeploymentFailed -PowerShell DSC 구성 'FirstSessionHost'가 완료되었으나 오류가 발생했습니다.

> [!div class="mx-imgBorder"]
> ![PowerShell DSC 구성 'FirstSessionHost'가 완료되었으나 오류가 발생한 배포 실패 스크린샷](media/failure-dsc.png)

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

### <a name="error-cant-delete-a-session-host-from-the-host-pool-after-deleting-the-vm"></a>오류: VM을 삭제한 후 호스트 풀에서 세션 호스트를 삭제할 수 없습니다.

**원인**: VM을 삭제하기 전에 세션 호스트를 삭제해야 합니다.

**해결 방법**: 세션 호스트를 드레이닝 모드로 전환하고, 세션 호스트에서 모든 사용자를 로그아웃한 다음, 호스트를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop과 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)을 참조하세요.
- Azure Virtual Desktop에서 VM(가상 머신)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 머신 구성](troubleshoot-vm-configuration.md)을 참조하세요.
- Azure Virtual Desktop 에이전트 또는 세션 연결과 관련된 문제를 해결하려면 [일반적인 Azure Virtual Desktop 에이전트 문제 해결](troubleshoot-agent.md)을 참조하세요.
- Azure Virtual Desktop 클라이언트 연결 문제를 해결하려면 [Azure Virtual Desktop 서비스 연결](troubleshoot-service-connection.md)을 참조하세요.
- 원격 데스크톱 클라이언트와 관련된 문제를 해결하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md)을 참조하세요.
- Azure Virtual Desktop과 함께 PowerShell을 사용할 때 발생하는 문제를 해결하려면 [Azure Virtual Desktop PowerShell](troubleshoot-powershell.md)을 참조하세요.
- 서비스에 대한 자세한 내용은 [Azure Virtual Desktop 환경](environment-setup.md)을 참조하세요.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)을 참조하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-monitor/essentials/activity-log.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
