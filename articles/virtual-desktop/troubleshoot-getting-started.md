---
title: Azure Virtual Desktop 시작 기능 문제 해결
description: Azure Virtual Desktop 시작 기능 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21cdccf5a025db4516d53c506c070fcf6f580fb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536574"
---
# <a name="troubleshoot-the-azure-virtual-desktop-getting-started-feature"></a>Azure Virtual Desktop 시작 기능 문제 해결

Azure Virtual Desktop 시작 기능은 중첩된 템플릿을 사용하여 Azure Virtual Desktop에서 유효성 검사 및 자동화에 사용할 리소스를 배포합니다. 시작 기능은 실행 중인 구독에 기존 AD DS(Active Directory Domain Services) 또는 Azure AD DS(Azure Active Directory Domain Services)가 있는지 여부에 따라 2개 또는 3개의 리소스 그룹을 만듭니다. 모든 리소스 그룹은 동일한 사용자 정의 접두사로 시작합니다.

중첩된 템플릿을 실행하면 3개의 리소스 그룹과 Azure Resource Manager 리소스를 프로비저닝하는 템플릿 하나가 생성됩니다. 다음 목록에서는 각 리소스 그룹 및 해당 리소스 그룹에서 실행하는 템플릿을 보여줍니다.

"-deployment"로 끝나는 리소스 그룹은 다음 템플릿을 실행합니다.

- easy-button-roleassignment-job-linked-template
- easy-button-prerequisitecompletion-job-linked-template
- easy-button-prerequisite-job-linked-template
- easy-button-inputvalidation-job-linked-template
- easy-button-deploymentResources-linked-template
- easy-button-prerequisite-user-setup-linked-template

>[!NOTE]
>easy-button-prerequisite-user-setup-linked-template은 선택 사항이며 유효성 검사 사용자를 만든 경우에만 표시됩니다.

"-wvd"로 끝나는 리소스 그룹은 다음 템플릿을 실행합니다.

- NSG-linkedTemplate
- vmCreation-linkedTemplate
- Workspace-linkedTemplate
- wvd-resources-linked-template
- easy-button-wvdsetup-linked-template

"-prerequisite"로 끝나는 리소스 그룹은 다음 템플릿을 실행합니다.

- easy-button-prerequisite-resources-linked-template

>[!NOTE]
>이 리소스 그룹은 선택 사항이며 구독에 Azure AD DS 또는 AD DS가 없는 경우에만 표시됩니다.

## <a name="no-subscriptions"></a>구독 없음

이 문제가 있으면 시작 기능을 열 때 "구독 없음"이라는 오류 메시지가 표시됩니다. 이 문제는 활성 Azure 구독 없이 기능을 열려고 할 때 발생합니다.

이 문제를 해결하려면 구독 또는 영향을 받는 사용자에게 활성 Azure 구독이 있는지 확인하세요. 활성 구독이 없으면 해당 사용자에게 구독에 대한 소유자 RBAC(역할 기반 액세스 제어) 역할을 할당합니다.

## <a name="you-dont-have-permissions"></a>권한 없음

이 문제는 시작 기능을 열면 "권한 없음"이라는 오류 메시지가 표시될 때 발생합니다. 이 메시지는 기능을 실행하는 사용자에게 활성 Azure 구독에 대한 소유자 권한이 없을 때 표시됩니다.

이 문제를 해결하려면 소유자 권한이 있는 Azure 계정으로 로그인한 다음, 영향을 받는 계정에 소유자 RBAC 역할을 할당합니다.

## <a name="fields-under-virtual-machine-tab-are-grayed-out"></a>Virtual Machine 탭 아래의 필드가 회색으로 표시됩니다.

이 문제는 **가상 머신** 탭을 열면 "사용자가 이 머신을 공유하게 하시겠습니까?" 아래의 필드가 회색으로 표시될 때 발생합니다. 이 문제가 발생하면 이미지 유형을 변경하거나, 사용할 이미지를 선택하거나, VM 크기를 변경할 수 없습니다.

배포를 시작하는 데 이미 사용된 접두사가 붙은 기능을 실행하면 이 문제가 발생합니다. 이 기능은 배포를 만들 때 Azure에서 배포를 나타내는 개체를 만듭니다. 여러 개체가 동일한 이미지를 사용하는 것을 방지하기 위해 이미지와 같은 개체의 특정 값이 해당 개체에 연결됩니다.

이 문제를 해결하려면 기존 접두사가 있는 모든 리소스 그룹을 삭제하거나 새 접두사를 사용합니다.

## <a name="username-must-not-include-reserved-words"></a>사용자 이름은 예약어를 포함하지 않아야 합니다.

이 문제는 사용자가 필드에 입력하는 새 사용자 이름을 시작 기능에서 허용하지 않을 때 발생합니다.

Azure가 퍼블릭 엔드포인트의 사용자 이름에 특정 단어를 허용하지 않기 때문에 이 오류 메시지가 표시됩니다. 차단된 단어의 전체 목록은 [예약된 리소스 이름 오류 해결](../azure-resource-manager/templates/error-reserved-resource-name.md)을 참조하세요.

이 문제를 해결하려면 새 단어를 사용해 보거나 차단된 단어에 문자를 추가하여 고유한 단어로 만들어 봅니다. 예를 들어 "admin"이라는 단어가 차단된 경우 "AVDadmin"을 대신 사용해 봅니다.

## <a name="the-value-must-be-between-12-and-72-characters-long"></a>값은 12~72자여야 합니다.

이 오류 메시지는 너무 길거나 너무 짧아서 문자 길이 요구 사항을 충족하지 않는 암호를 입력할 때 표시됩니다. Azure 암호 길이 및 복잡성 요구 사항은 나중에 Windows에서 사용하는 필드에도 적용되며, Windows의 요구 사항이 덜 엄격합니다.

이 문제를 해결하려면 [Microsoft 암호 지침](https://www.microsoft.com/research/publication/password-guidance)을 준수하는 계정을 사용하거나 [Azure AD 암호 보호](../active-directory/authentication/concept-password-ban-bad.md)를 사용해야 합니다.

## <a name="error-messages-for-easy-button-prerequisite-user-setup-linked-template"></a>easy-button-prerequisite-user-setup-linked-template에 대한 오류 메시지

사용 중인 AD DS VM에 이미 Microsoft.Powershell.DSC라는 확장이 연결된 경우 다음과 같은 오류 메시지가 표시됩니다.

```azure
"error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \\\"DSC Configuration 'AddADDSUser' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: Some error occurred in DSC CreateUser SetScript: \\r\\n\\r\\nException             : Microsoft.ActiveDirectory.Management.ADIdentityNotFoundException: Cannot find an object with \\r\\n                        identity: 'Adam S' under: 'DC=GT090617,DC=onmicrosoft,DC=com'.\\r\\n                           at Microsoft.ActiveDirectory.Management.Commands.ADFactoryUtil.GetObjectFromIdentitySearcher(\\r\\n                        ADObjectSearcher searcher, ADEntity identityObj, String searchRoot, AttributeSetRequest attrs, \\r\\n                        CmdletSessionInfo cmdletSessionInfo, String[]& warningMessages)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.ADFactory`1.GetDirectoryObjectFromIdentity(T \\r\\n                        identityObj, String searchRoot, Boolean showDeleted)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.SetADGroupMember`1.ValidateMembersParameter()\\r\\nTargetObject          : Adam S\\r\\nCategoryInfo          : ObjectNotFound: (Adam S:ADPrincipal) [Add-ADGroupMember], ADIdentityNotFoundException\\r\\nFullyQualifiedErrorId : SetADGroupMember.ValidateMembersParameter,Microsoft.ActiveDirectory.Management.Commands.AddADGro\\r\\n                        upMember\\r\\nErrorDetails          : \\r\\nInvocationInfo        : System.Management.Automation.InvocationInfo\\r\\nScriptStackTrace      : at <ScriptBlock>, C:\\\\Packages\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.83.1.0\\\\DSCWork\\\\DSCADUserCreatio\\r\\n                        nScripts_2020-04-28.2\\\\Script-CreateADDSUser.ps1: line 98\\r\\n                        at <ScriptBlock>, <No file>: line 8\\r\\n                        at ScriptExecutionHelper, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfi\\r\\n                        guration\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 270\\r\\n                        at Set-TargetResource, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfigur\\r\\n                        ation\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 144\\r\\nPipelineIterationInfo : {}\\r\\nPSMessageDetails      : \\r\\n\\r\\n\\r\\n\\r\\n  The SendConfigurationApply function did not succeed.\\\"\\r\\n\\r\\nMore information on troubleshooting is available at https://aka.ms/VMExtensionDSCWindowsTroubleshoot \"\r\n      }\r\n    ]\r\n  }\r\n}"
            }
        ]
    }

```

이 문제를 해결하려면 Microsoft.Powershell.DSC 확장을 제거하고 시작 기능을 다시 실행합니다.

## <a name="error-messages-for-easy-button-prerequisite-job-linked-template"></a>easy-button-prerequisite-job-linked-template에 대한 오류 메시지

다음과 같은 오류 메시지가 표시되는 경우 easy-button-prerequisite-job-linked-template 템플릿에 대한 리소스 작업이 성공적으로 완료되지 않은 것입니다.

```azure
{
    "status": "Failed",
    "error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\"\r\n  }\r\n}"
            }
        ]
    }
}

```

이 문제를 처리하려는 것이 맞는지 확인하려면 다음을 수행합니다.

1. **easy-button-prerequisite-job-linked-template** 을 선택한 다음, 나타나는 오류 메시지 창에서 **확인** 을 선택합니다.

2. **\<prefix\>-deployment 리소스 그룹** 으로 이동하여 **resourceSetupRunbook** 을 선택합니다.

3. **실패** 상태를 선택합니다.

4. **예외** 탭을 선택하면 다음과 같은 오류 메시지가 표시됩니다.
   
   ```azure
   The running command stopped because the preference variable "ErrorActionPreference" or common parameter is set to Stop: Error while creating and adding validation user <your-username-here> to group <your-resource-group-here>
   ```

현재는 이 문제를 영구적으로 해결할 수 있는 방법이 없습니다. 임시 해결책은 Azure Virtual Desktop 시작 기능을 다시 실행하되, 이번에는 유효성 검사 사용자를 만들지 않는 것입니다. 그런 다음, 수동 프로세스만 사용하여 새 사용자를 만듭니다.

### <a name="validate-that-the-domain-administrator-upn-exists-for-a-new-profile"></a>새 프로필의 도메인 관리자 UPN이 있는지 확인하세요.

UPN 주소 때문에 템플릿 관련 문제가 발생하는지 확인하려면 다음을 수행합니다.

1. **easy-button-prerequisite-job-linked-template** 을 선택하고 실패한 단계에서 다음을 수행합니다. 오류 메시지를 확인합니다.

2. **\<prefix\>-deployment 리소스 그룹** 으로 이동하여 **resourceSetupRunbook** 을 클릭합니다.

3. **실패** 상태를 선택합니다.

4. **출력** 탭을 선택합니다.

새 구독에 UPN이 있으면 문제의 잠재적 원인은 다음 두 가지입니다.

- 사용자가 이미 있으므로 시작 기능에서 도메인 관리자 프로필을 만들지 못했습니다. 이 문제를 해결하려면 시작 기능을 다시 실행하되, 이번에는 ID 공급자에 아직 없는 사용자 이름을 입력합니다.
- 시작 기능에서 유효성 검사 사용자 프로필을 만들지 못했습니다. 이 문제를 해결하려면 시작 기능을 다시 실행하되, 이번에는 유효성 검사 사용자를 만들지 않습니다. 그런 다음, 수동 프로세스만 사용하여 새 사용자를 만듭니다.

## <a name="error-messages-for-easy-button-inputvalidation-job-linked-template"></a>easy-button-inputvalidation-job-linked-template에 대한 오류 메시지

easy-button-inputvalidation-job-linked-template 템플릿과 관련된 문제가 있는 경우 다음과 같은 오류 메시지가 표시됩니다.

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state 'Failed'."
    }
}
```

발생한 문제가 이 문제인지 확인하려면 다음을 수행합니다.

1. \<prefix\>-deployment 리소스 그룹을 열고 **inputValidationRunbook을 찾습니다.**

2. 최근 작업을 보면 실패 상태의 작업이 있습니다. **실패** 를 클릭합니다.

3. **작업 세부 정보** 창에서 **예외** 를 선택합니다.

이 오류는 입력한 Azure 관리자 UPN이 올바르지 않은 경우에 발생합니다. 이 문제를 해결하려면 올바른 사용자 이름과 암호를 입력했는지 확인한 후 다시 시도하세요.

## <a name="multiple-vmextensions-per-handler-not-supported"></a>각 처리기에서 여러 VMExtension이 지원되지 않습니다.

Azure AD DS 또는 AD DS가 있는 구독에서 시작 기능을 실행하는 경우 시작 기능은 Microsoft.Powershell.DSC 확장을 사용하여 유효성 검사 사용자를 만들고 FSLogix를 구성합니다. 그러나 Azure의 Windows VM은 동일한 유형의 확장 여러 개를 동시에 실행할 수 없습니다.

여러 버전의 Microsoft.Powershell.DSC를 실행하려고 하면 다음과 같은 오류 메시지가 표시됩니다.

```azure
{
    "status": "Failed",
    "error": {
        "code": "BadRequest",
        "message": "Multiple VMExtensions per handler not supported for OS type 'Windows'. VMExtension 'Microsoft.Powershell.DSC' with handler 'Microsoft.Powershell.DSC' already added or specified in input."
    }
}
```

이 문제를 해결하려면 시작 기능을 실행하기 전에 도메인 컨트롤러 VM에서 현재 실행 중인 Microsoft.Powershell.DSC 인스턴스를 모두 제거해야 합니다.

## <a name="failure-in-easy-button-prerequisitecompletion-job-linked-template"></a>easy-button-prerequisitecompletion-job-linked-template 오류

유효성 검사 사용자의 사용자 그룹은 "USERS" 컨테이너에 있습니다. 그러나 사용자 그룹이 제대로 작동하려면 Azure AD와 동기화되어야 합니다. 그렇지 않으면 다음과 같은 오류 메시지가 표시됩니다.

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state ‘Failed’."
    }
}
```

유효성 검사 사용자 그룹이 동기화되지 않는 것이 문제의 원인인지 확인하려면 \<prefix\>-prerequisites 리소스 그룹을 열고 **prerequisiteSetupCompletionRunbook** 이라는 파일을 찾습니다. Runbook을 선택하고 **모든 로그** 를 선택합니다.

이 문제를 해결하려면 다음을 수행합니다.

1. "USERS" 컨테이너에 대해 Azure AD와 동기화하도록 설정합니다.

2. Azure와 동기화되는 조직 구성 단위에 AVDValidationUsers 그룹을 만듭니다.

## <a name="next-steps"></a>다음 단계

[시작 기능을 사용하여 Azure Virtual Desktop 배포](getting-started-feature.md)에서 시작 기능에 대해 자세히 알아봅니다.
