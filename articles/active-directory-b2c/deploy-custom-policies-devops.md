---
title: Azure Pipelines를 사용하여 사용자 지정 정책 배포
titleSuffix: Azure AD B2C
description: Azure Pipelines를 사용하여 CI/CD 파이프라인에 Azure AD B2C 사용자 지정 정책을 배포하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d24e1cf8394b697348492ffcddc4634646ebbbb6
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967090"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Azure Pipelines를 사용하여 사용자 지정 정책 배포

[Azure Pipelines](/azure/devops/pipelines)는 지속적이고 일관되게 코드를 테스트하고 빌드하여 모든 대상에 제공하기 위해 CI(연속 통합) 및 CD(지속적인 업데이트)를 지원합니다. 이 문서에서는 Azure Pipelines를 사용하여 Azure Active Directory B2C(Azure AD B2C) [사용자 지정 정책](user-flow-overview.md)의 배포 프로세스를 자동화하는 방법을 설명합니다.

> [!IMPORTANT]
> Azure Pipelines를 사용하여 Azure AD B2C 사용자 지정 정책을 관리하기 위해 현재 Microsoft Graph API `/beta` 엔드포인트에서 사용할 수 있는 **미리 보기** 작업을 사용합니다. 프로덕션 애플리케이션에서는 이러한 API의 사용이 지원되지 않습니다. 자세한 내용은 [Microsoft Graph REST API 베타 엔드포인트 참조](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [Active Directory B2C에서 사용자 지정 정책을 사용하여 시작하기](tutorial-create-user-flows.md)에 있는 단계를 완료합니다.
* DevOps 조직을 만들지 않은 경우 가입의 지침에 따라 조직을 만들고 [Azure DevOps에 로그인](/azure/devops/user-guide/sign-up-invite-teammates)합니다.  

## <a name="register-an-application-for-management-tasks"></a>관리 작업을 위한 애플리케이션 등록

PowerShell 스크립트를 사용하여 Azure AD B2C 정책을 배포합니다. PowerShell 스크립트가 [Microsoft Graph API](microsoft-graph-operations.md)와 상호 작용하기 전에 Azure AD B2C 테넌트에서 애플리케이션 등록을 만듭니다. 아직 등록하지 않은 경우 [Microsoft Graph 애플리케이션을 등록](microsoft-graph-get-started.md)합니다.

PowerShell 스크립트가 MS Graph의 데이터에 액세스하려면 등록된 애플리케이션에 관련 [애플리케이션 권한](/graph/permissions-reference)을 부여합니다. 앱 등록의 **API 권한** 내에서 **Microsoft Graph** > **정책** > **Policy.ReadWrite.TrustFramework** 권한이 부여되었습니다.

## <a name="configure-an-azure-repo"></a>Azure 리포지토리 구성

Microsoft Graph 애플리케이션이 등록되면 정책 파일에 대한 리포지토리를 구성할 준비가 된 것입니다.

1. [Azure DevOps 조직](https://azure.microsoft.com/services/devops/)에 로그인합니다.
1. [새 프로젝트를 만들거나][devops-create-project] 기존 프로젝트를 선택합니다.
1. 프로젝트에서 **리포지토리** 로 이동하고 **파일** 을 선택합니다. 
1. 기존 리포지토리를 선택하거나 새로 만듭니다.
1. 리포지토리의 루트 디렉터리에서 `B2CAssets` 폴더를 만듭니다. Azure AD B2C 사용자 지정 정책 파일을 *B2CAssets* 폴더에 추가합니다.
1. 리포지토리의 루트 디렉터리에서 `Scripts` 폴더를 만듭니다. PowerShell 파일 *DeployToB2C.ps1* 을 만듭니다. 다음 PowerShell 스크립트를 *DeployToB2C.ps1* 에 붙여넣습니다. 
1. 변경 내용을 **커밋** 하고 **푸시** 합니다.

다음 스크립트는 Azure AD에서 액세스 토큰을 획득합니다. 토큰을 사용하여 스크립트는 MS Graph API 호출하여 *B2CAssets* 폴더에 정책을 업로드합니다. 정책 콘텐츠를 업로드하기 전에 변경할 수도 있습니다. 예를 들어 `tenant-name.onmicrosoft.com`을 테넌트 이름으로 바꿉니다.

```PowerShell
[Cmdletbinding()]
Param(
    [Parameter(Mandatory = $true)][string]$ClientID,
    [Parameter(Mandatory = $true)][string]$ClientSecret,
    [Parameter(Mandatory = $true)][string]$TenantId,
    [Parameter(Mandatory = $true)][string]$Folder,
    [Parameter(Mandatory = $true)][string]$Files
)

try {
    $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

    $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
    $token = $response.access_token

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Content-Type", 'application/xml')
    $headers.Add("Authorization", 'Bearer ' + $token)

    # Get the list of files to upload
    $filesArray = $Files.Split(",")

    Foreach ($file in $filesArray) {

        $filePath = $Folder + $file.Trim()

        # Check if file exists
        $FileExists = Test-Path -Path $filePath -PathType Leaf

        if ($FileExists) {
            $policycontent = Get-Content $filePath

            # Optional: Change the content of the policy. For example, replace the tenant-name with your tenant name.
            # $policycontent = $policycontent.Replace("your-tenant.onmicrosoft.com", "contoso.onmicrosoft.com")     
    
    
            # Get the policy name from the XML document
            $match = Select-String -InputObject $policycontent  -Pattern '(?<=\bPolicyId=")[^"]*'
    
            If ($match.matches.groups.count -ge 1) {
                $PolicyId = $match.matches.groups[0].value
    
                Write-Host "Uploading the" $PolicyId "policy..."
    
                $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
                $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers
    
                Write-Host "Policy" $PolicyId "uploaded successfully."
            }
        }
        else {
            $warning = "File " + $filePath + " couldn't be not found."
            Write-Warning -Message $warning
        }
    }
}
catch {
    Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

    $_

    $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
    $streamReader.BaseStream.Position = 0
    $streamReader.DiscardBufferedData()
    $errResp = $streamReader.ReadToEnd()
    $streamReader.Close()

    $ErrResp

    exit 1
}

exit 0
```

## <a name="configure-azure-pipelines"></a>Azure Pipelines 구성

리포지토리를 초기화하여 사용자 지정 정책 파일로 채우면 릴리스 파이프라인을 설정할 준비가 된 것입니다. 파이프라인을 만들려면 다음 단계를 수행합니다.

1. 프로젝트에서 **파이프라인** > **릴리스** > **새 파이프라인** 을 선택합니다.
1. **템플릿 선택** 에서 **빈 작업** 을 선택한 다음, **적용** 을 선택합니다.
1. **단계 이름**(예: *DeployCustomPolicies*)을 입력하고 창을 닫습니다.
1. **아티팩트 추가** 를 선택하고 **원본 형식** 에서 **Azure 리포지토리** 를 선택합니다.
    1. **프로젝트** 의 경우 프로젝트를 선택합니다.
    1. *Scripts* 폴더가 포함된 **원본(리포지토리)** 을 선택합니다.
    1. **기본 분기**(예: ‘마스터’)를 선택합니다.
    1. *기본 분기에서 최신* 의 **기본 버전** 설정을 그대로 둡니다.
    1. 리포지토리의 **원본 별칭** 을 입력합니다. 예: *policyRepo* 
1. **추가** 를 선택합니다.
1. 의도를 반영하도록 파이프라인의 이름을 바꿉니다. 예: *사용자 지정 정책 파이프라인 배포*
1. **저장** 을 선택하여 파이프라인 구성을 저장합니다.

### <a name="configure-pipeline-variables"></a>파이프라인 변수 구성

파이프라인 변수를 통해 파이프라인의 다양한 부분으로 데이터의 핵심 요소를 편리하게 가져올 수 있습니다. 다음 변수는 Azure AD B2C 환경에 대한 정보를 제공합니다.

| 속성 | 값 |
| ---- | ----- |
| `clientId` | 이전에 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 입니다. |
| `clientSecret` | 이전에 만든 **클라이언트 암호** 의 값입니다. <br /> 잠금 아이콘을 선택하여 변수 유형을 **비밀** 로 변경합니다. |
| `tenantId` | `your-b2c-tenant.onmicrosoft.com`에서 *your-b2c-tenant* 는 Azure AD B2C 테넌트의 이름입니다. |

파이프라인 변수를 추가하려면 다음 단계를 수행합니다.

1. 파이프라인에서 **변수** 탭을 선택합니다.
1. **파이프라인 변수** 아래에서 위의 변수를 해당 값과 함께 추가합니다.
1. **저장** 을 선택하여 변수를 저장합니다.

### <a name="add-pipeline-tasks"></a>파이프라인 작업 추가

파이프라인 작업은 작업을 수행하는 미리 패키지된 스크립트입니다. *DeployToB2C.ps1* PowerShell 스크립트를 호출하는 작업을 추가합니다.

1. 만든 파이프라인에서 **작업** 탭을 선택합니다.
1. **에이전트 작업** 을 선택하고 더하기 기호( **+** )를 선택하여 에이전트 작업(Agent job)에 작업(task)을 추가합니다.
1. **PowerShell** 을 검색하고 선택합니다. "Azure PowerShell," "대상 컴퓨터에서 PowerShell" 또는 다른 PowerShell 항목을 선택하지 않습니다.
1. 새로 추가된 **PowerShell 스크립트** 작업을 선택합니다.
1. PowerShell 스크립트 작업에 대해 다음 값을 입력합니다.
    * **작업 버전**: 2.*
    * **표시 이름**: 이 작업에서 업로드할 정책의 이름입니다. 예: *B2C_1A_TrustFrameworkBase*
    * **형식**: 파일 경로입니다.
    * **스크립트 경로**: 줄임표(**_..._* _)를 선택하여 _Scripts* 폴더로 이동한 다음 *DeployToB2C.ps1* 파일을 선택합니다.
    * **인수**: 다음 PowerShell 스크립트를 입력합니다. 


        ```PowerShell
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -Folder $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/ -Files "TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml,ProfileEdit.xml,PasswordReset.xml"
        ```
        
        `-Files` 매개 변수는 배포할 정책 파일의 쉼표 구분 기호 목록입니다. 정책 파일로 목록을 업데이트합니다.
        
        > [!IMPORTANT]
        >  정책이 올바른 순서로 업로드되었는지 확인합니다. 먼저 기본 정책, 확장 정책, 신뢰 당사자 정책입니다. 예: `TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`.
        
1. **저장** 을 선택하여 에이전트 작업을 저장합니다.

## <a name="test-your-pipeline"></a>파이프라인 테스트

릴리스 파이프라인을 테스트하려면:

1. **파이프라인** 을 선택한 다음 **릴리스** 를 선택합니다.
1. 앞서 만든 파이프라인(예: *DeployCustomPolicies*)을 선택합니다.
1. **릴리스 만들기**, **만들기** 를 차례로 선택하여 릴리스 큐에 대기합니다.

릴리스가 대기 중임을 알리는 알림 배너가 표시됩니다. 해당 상태를 보려면 알림 배너에서 링크를 선택하거나 **릴리스** 탭의 목록에서 해당 링크를 선택합니다.


## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [클라이언트 자격 증명을 사용하여 서비스 간 호출](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
