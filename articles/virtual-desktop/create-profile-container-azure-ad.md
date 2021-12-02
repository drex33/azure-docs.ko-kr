---
title: Azure Active Directory 사용하여 Azure 파일 공유 만들기(미리 보기)
description: Azure Active Directory 도메인(미리 보기)을 사용하여 기존 Azure Virtual Desktop 호스트 풀의 Azure 파일 공유에 FSLogix 프로필 컨테이너를 설정합니다.
services: virtual-desktop
author: Heidilohr
manager: femila
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 12/01/2021
ms.author: helohr
ms.openlocfilehash: 578a69aaa5b0045f0ea8c70e5c0de2b10f1a6e41
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483050"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-active-directory-preview"></a>Azure Files 및 Azure Active Directory(미리 보기)를 사용하여 프로필 컨테이너 만들기

> [!IMPORTANT]
> AD(Azure Active Directory) 조인 VM에 대한 Azure Files FSLogix 프로필을 저장하는 것은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Active Directory(AD)로 인증된 하이브리드 사용자 ID로 액세스할 수 있는 FSLogix 프로필을 저장하는 Azure Files 공유를 만드는 방법을 알아봅니다. 이제 Azure AD 사용자는 Kerberos 인증을 사용하여 Azure 파일 공유에 액세스할 수 있습니다. 이 구성은 Azure AD를 사용하여 업계 표준 SMB 프로토콜을 사용하여 파일 공유에 액세스하는 데 필요한 Kerberos 티켓을 발급합니다. 최종 사용자는 하이브리드 Azure AD 조인 및 Azure AD 조인 VM에서 도메인 컨트롤러에 대한 가시선 없이 인터넷을 통해 Azure 파일 공유에 액세스할 수 있습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Azure AD를 사용하여 인증을 위해 Azure Storage 계정을 구성합니다.
- Azure Files 공유에 대한 사용 권한을 구성합니다.
- Azure Files FSLogix 사용자 프로필을 저장하도록 세션 호스트를 구성합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD Kerberos 기능은 다음 운영 체제에서만 사용할 수 있습니다.

  - 단일 세션 또는 다중 세션을 Windows 11 Enterprise.
  - 최신 누적 업데이트가 설치된 단일 또는 다중 세션 버전 2004 이상, 특히 Windows 10 [용 KB5007253 - 2021-11 누적 업데이트 미리 보기를 Windows 10 Enterprise.](https://support.microsoft.com/topic/november-22-2021-kb5007253-os-builds-19041-1387-19042-1387-19043-1387-and-19044-1387-preview-d1847be9-46c1-49fc-bf56-1d469fc1b3af)
  - Windows Server, 최신 누적 업데이트가 설치된 버전 2022, 특히 [Microsoft 서버 운영 체제 버전 21H2용 KB5007254 - 2021-11 누적 업데이트 미리 보기.](https://support.microsoft.com/topic/november-22-2021-kb5007254-os-build-20348-380-preview-9a960291-d62e-486a-adcc-6babe5ae6fc1)

사용자 계정은 하이브리드 [사용자 ID여야](../active-directory/hybrid/whatis-hybrid-identity.md)합니다. 즉, Active Directory Domain Services(AD DS) 및 Azure AD 커넥트 필요합니다. Active Directory에서 이러한 계정을 만들고 Azure AD에 동기화해야 합니다.

Azure 파일 공유에 대한 AZURE RBAC(Role-Based Access Control) 권한을 사용자 그룹에 할당하려면 Active Directory에서 그룹을 만들고 Azure AD와 동기화해야 합니다.

> [!IMPORTANT]
> 이 기능은 현재 Azure 퍼블릭 클라우드에서만 지원됩니다.

## <a name="configure-your-azure-storage-account"></a>Azure Storage 계정 구성

계정이 없는 경우 [Azure Storage 계정을 만들어](../storage/files/storage-how-to-create-file-share.md#create-a-storage-account) 시작합니다.

> [!NOTE]
> Azure Storage 계정은 Azure AD와 Active Directory Domain Services(AD DS) 또는 Azure AD DS 같은 두 번째 방법으로 인증할 수 없습니다. 인증 방법은 하나만 사용할 수 있습니다.

다음 섹션의 지침에 따라 Azure AD 인증을 구성하고, Azure AD 서비스 주체를 구성하고, 스토리지 계정에 대한 API 권한을 설정합니다.

### <a name="configure-azure-ad-authentication-on-your-azure-storage-account"></a>Azure Storage 계정에서 Azure AD 인증 구성

- Azure Storage PowerShell 모듈을 설치합니다. 이 모듈에서는 Azure Storage 리소스에 대한 관리 cmdlet을 제공합니다. 스토리지 계정을 만들고, 스토리지 계정에서 Azure AD 인증을 사용하도록 설정하고, 스토리지 계정의 Kerberos 키를 검색해야 합니다. 모듈을 설치하려면 PowerShell을 열고 다음 명령을 실행합니다.

    ```powershell
    Install-Module -Name Az.Storage
    ```

- Azure AD PowerShell 모듈을 설치합니다. 이 모듈에서는 사용자 및 서비스 주체 관리와 같은 Azure AD 관리 작업에 대한 관리 cmdlet을 제공합니다. 이 모듈을 설치하려면 PowerShell을 열고 다음 명령을 실행합니다.

    ```powershell
    Install-Module -Name AzureAD
    ```

    자세한 내용은 [Azure AD PowerShell 모듈 설치를 참조하세요.](/powershell/azure/active-directory/install-adv2)

- 다음 PowerShell cmdlet을 실행하여 스토리지 계정 이름 및 리소스 그룹 이름 모두에 대한 변수를 설정하고, 값을 사용자 환경과 관련된 값으로 대체합니다.

    ```powershell
    $resourceGroupName = "<MyResourceGroup>"
    $storageAccountName = "<MyStorageAccount>"
    ```
 
- 다음 PowerShell cmdlet을 실행하여 스토리지 계정에서 Azure AD 인증을 사용하도록 설정합니다.

    ```powershell
    Connect-AzAccount
    $Subscription =  $(Get-AzContext).Subscription.Id;
    $ApiVersion = '2021-04-01'

    $Uri = ('https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Storage/storageAccounts/{2}?api-version={3}' -f $Subscription, $ResourceGroupName, $StorageAccountName, $ApiVersion);
    
    $json = 
       @{properties=@{azureFilesIdentityBasedAuthentication=@{directoryServiceOptions="AADKERB"}}};
    $json = $json | ConvertTo-Json -Depth 99

    $token = $(Get-AzAccessToken).Token
    $headers = @{ Authorization="Bearer $token" }

    try {
        Invoke-RestMethod -Uri $Uri -ContentType 'application/json' -Method PATCH -Headers $Headers -Body $json;
    } catch {
        Write-Host $_.Exception.ToString()
        Write-Error -Message "Caught exception setting Storage Account directoryServiceOptions=AADKERB: $_" -ErrorAction Stop
    } 
    ```

- 다음 PowerShell 명령을 실행하여 스토리지 계정에 대한 kerb1 스토리지 계정 키를 생성합니다.

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName -KeyName kerb1 -ErrorAction Stop 
    ```

### <a name="configure-the-azure-ad-service-principal-and-application"></a>Azure AD 서비스 주체 및 애플리케이션 구성

스토리지 계정에서 Azure AD 인증을 사용하도록 설정하려면 Azure AD에서 스토리지 계정을 나타내는 Azure AD 애플리케이션을 만들어야 합니다. 이 구성은 공개 미리 보기 동안 Azure Portal 사용할 수 없습니다. PowerShell을 사용하여 애플리케이션을 만들려면 다음 단계를 수행합니다.

- 스토리지 계정의 Kerberos 키에 따라 암호(서비스 주체 비밀)를 설정합니다. Kerberos 키는 Azure AD와 Azure Storage 간에 공유되는 암호입니다. Kerberos는 스토리지 계정 kerb1 키의 처음 32바이트에서 암호 값을 파생시킵니다. 암호를 설정하려면 다음 cmdlet을 실행합니다.

    ```powershell
    $kerbKey1 = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName -ListKerbKey | Where-Object { $_.KeyName -like "kerb1" }
    $aadPasswordBuffer = [System.Linq.Enumerable]::Take([System.Convert]::FromBase64String($kerbKey1.Value), 32);
    $password = "kk:" + [System.Convert]::ToBase64String($aadPasswordBuffer);
    ```

- Azure AD에 커넥트 다음 cmdlet을 실행하여 테넌트 정보를 검색합니다.

    ```powershell
    Connect-AzureAD
    $azureAdTenantDetail = Get-AzureADTenantDetail;
    $azureAdTenantId = $azureAdTenantDetail.ObjectId
    $azureAdPrimaryDomain = ($azureAdTenantDetail.VerifiedDomains | Where-Object {$_._Default -eq $true}).Name
    ```

- 다음 cmdlet을 실행하여 Azure AD 서비스 주체에 대한 서비스 주체 이름을 생성합니다.

    ```powershell
    $servicePrincipalNames = New-Object string[] 3
    $servicePrincipalNames[0] = 'HTTP/{0}.file.core.windows.net' -f $storageAccountName
    $servicePrincipalNames[1] = 'CIFS/{0}.file.core.windows.net' -f $storageAccountName
    $servicePrincipalNames[2] = 'HOST/{0}.file.core.windows.net' -f $storageAccountName
    ```

- 다음 cmdlet을 실행하여 스토리지 계정에 대한 애플리케이션을 만듭니다.

    ```powershell
    $application = New-AzureADApplication -DisplayName $storageAccountName -IdentifierUris $servicePrincipalNames -GroupMembershipClaims "All";
    ```

- 다음 cmdlet을 실행하여 스토리지 계정에 대한 서비스 주체를 만듭니다.

    ```powershell
    $servicePrincipal = New-AzureADServicePrincipal -AccountEnabled $true -AppId $application.AppId -ServicePrincipalType "Application";
    ```

- 다음 cmdlet을 실행하여 스토리지 계정의 서비스 주체에 대한 암호를 설정합니다.

    ```powershell
    $Token = ([Microsoft.Open.Azure.AD.CommonLibrary.AzureSession]::AccessTokens['AccessToken']).AccessToken
    $apiVersion = '1.6'
    $Uri = ('https://graph.windows.net/{0}/{1}/{2}?api-version={3}' -f $azureAdPrimaryDomain, 'servicePrincipals', $servicePrincipal.ObjectId, $apiVersion)
    $json = @'
    {
      "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "<STORAGEACCOUNTENDDATE>",
        "value": "<STORAGEACCOUNTPASSWORD>",
        "startDate": "<STORAGEACCOUNTSTARTDATE>"
      }]
    }
    '@
    $now = [DateTime]::UtcNow
    $json = $json -replace "<STORAGEACCOUNTSTARTDATE>", $now.AddDays(-1).ToString("s")
    $json = $json -replace "<STORAGEACCOUNTENDDATE>", $now.AddMonths(12).ToString("s")
    $json = $json -replace "<STORAGEACCOUNTPASSWORD>", $password
    $Headers = @{'authorization' = "Bearer $($Token)"}
    try {
      Invoke-RestMethod -Uri $Uri -ContentType 'application/json' -Method Patch -Headers $Headers -Body $json 
      Write-Host "Success: Password is set for $storageAccountName"
    } catch {
      Write-Host $_.Exception.ToString()
      Write-Host "StatusCode: " $_.Exception.Response.StatusCode.value
      Write-Host "StatusDescription: " $_.Exception.Response.StatusDescription
    }
    ```

### <a name="set-the-api-permissions-on-the-newly-created-application"></a>새로 만든 애플리케이션에 대한 API 권한 설정

다음 단계를 수행하여 [Azure Portal](https://portal.azure.com) API 권한을 구성할 수 있습니다.

1. **Azure Active Directory** 를 엽니다.
2. 왼쪽 창에서 **앱 등록** 선택합니다.
3. **모든 애플리케이션을** 선택합니다.
4. 스토리지 계정과 일치하는 이름의 애플리케이션을 선택합니다.
5. 왼쪽 창에서 **API 권한을** 선택합니다.
6. **+ 권한 추가를** 선택합니다.
7. 페이지 위쪽에서 **Microsoft Graph** 선택합니다.
8. **위임된 권한** 을 선택합니다.
9. **OpenID** 권한 그룹에서 **openid** 및 **프로필을** 선택합니다.
10. 사용자 **권한** 그룹에서 **User.Read를** 선택합니다.
11. 페이지 아래쪽에서 **권한 추가를** 선택합니다.
12. **"DirectoryName"에 대한 관리자 동의 부여를** 선택합니다.

## <a name="configure-your-azure-files-share"></a>Azure Files 공유 구성

시작하려면 스토리지 계정 아래에 [Azure Files 공유를 만들어](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) FSLogix 프로필을 저장합니다(아직 저장하지 않은 경우).

다음 섹션의 지침에 따라 Azure Files 공유에 대한 공유 수준 및 디렉터리 수준 권한을 구성하여 사용자에게 올바른 수준의 액세스를 제공합니다.

### <a name="assign-share-level-permissions"></a>공유 수준 권한 할당
    
파일 공유를 사용하려면 먼저 사용자에게 파일 공유에 대한 액세스 권한을 부여해야 합니다. 공유 수준 권한을 할당할 수 있는 방법에는 특정 Azure AD 사용자 또는 사용자 그룹에 할당하거나 모든 인증된 ID에 기본 공유 수준 권한으로 할당할 수 있습니다. 공유 수준 사용 권한을 할당하는 방법에 대한 자세한 내용은 [ID에 공유 수준 권한 할당을 참조하세요.](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)

사용하려는 스토리지 계정에 FSLogix 프로필을 저장해야 하는 모든 **사용자에게는 Storage 파일 데이터 SMB 공유 기여자** 역할이 할당되어야 합니다.

> [!IMPORTANT]
> Azure Virtual Desktop은 현재 하이브리드 사용자 및 사용자 그룹에 특정 권한 할당만 지원합니다. 사용자 및 사용자 그룹은 Active Directory에서 관리되고 Azure AD 커넥트 사용하여 Azure AD와 동기화되어야 합니다.

### <a name="assign-directory-level-access-permissions"></a>디렉터리 수준 액세스 권한 할당

사용자가 다른 사용자의 사용자 프로필에 액세스하지 못하도록 하려면 디렉터리 수준 권한도 할당해야 합니다. 이 섹션에서는 사용 권한을 구성하는 단계를 제공합니다. 프로필 컨테이너에 대한 스토리지 권한 구성에서 FSLogix [프로필에 권장되는 사용 권한](/fslogix/fslogix-storage-config-ht) 목록에 대해 자세히 알아보세요.

> [!IMPORTANT]
> 적절한 디렉터리 수준 권한이 없으면 사용자는 사용자 프로필을 삭제하거나 다른 사용자의 개인 정보에 액세스할 수 있습니다. 실수로 삭제되는 것을 방지하기 위해 사용자에게 적절한 권한이 있는지 확인하는 것이 중요합니다.

명령줄 유틸리티 또는 Windows 탐색기를 사용하여 파일 및 디렉터리에 대한 권한(ACL)을 설정할 수 있습니다. 사용 권한을 구성하는 데 사용하는 시스템은 다음 요구 사항을 충족해야 합니다.

- Windows 버전은 필수 조건 섹션에 정의된 지원되는 OS 요구 사항을 [충족합니다.](#prerequisites)
- Azure AD 조인 또는 하이브리드 Azure AD가 스토리지 계정과 동일한 Azure AD 테넌트에서 조인됩니다.
- 도메인 컨트롤러에 대한 가시선이 있습니다.
- Active Directory에 도메인에 가입되어 있습니다(Windows Explorer 메서드에만 해당).

공개 미리 보기 동안 Windows Explorer를 사용하여 권한을 구성하려면 스토리지 계정 구성도 필요합니다. icacls를 사용하는 경우 이 구성 단계를 건너뛸 수 있습니다.

스토리지 계정을 구성하려면 다음을 수행합니다.

1. Active Directory에 도메인에 가입된 디바이스에서 [ActiveDirectory PowerShell 모듈을](/powershell/module/activedirectory/?view=windowsserver2019-ps&preserve-view=true) 아직 설치하지 않은 경우 설치합니다.

2. 셸 환경을 지원하도록 스토리지 계정의 ActiveDirectoryProperties를 설정합니다. Azure AD는 현재 셸에서 ACL 구성을 지원하지 않으므로 대신 Active Directory를 사용해야 합니다. 셸을 구성하려면 PowerShell에서 다음 명령을 실행합니다.
    ```powershell
    function Set-StorageAccountAadKerberosADProperties {
        [CmdletBinding()]
        param(
            [Parameter(Mandatory=$true, Position=0)]
            [string]$ResourceGroupName,

            [Parameter(Mandatory=$true, Position=1)]
            [string]$StorageAccountName,

            [Parameter(Mandatory=$false, Position=2)]
            [string]$Domain
        )  

        $AzContext = Get-AzContext;
        if ($null -eq $AzContext) {
            Write-Error "No Azure context found.  Please run Connect-AzAccount and then retry." -ErrorAction Stop;
        }
    
        $AdModule = Get-Module ActiveDirectory;
        if ($null -eq $AdModule) {
            Write-Error "Please install and/or import the ActiveDirectory PowerShell module." -ErrorAction Stop;
        }   
    
        if ([System.String]::IsNullOrEmpty($Domain)) {
            $domainInformation = Get-ADDomain
            $Domain = $domainInformation.DnsRoot
        } else {
            $domainInformation = Get-ADDomain -Server $Domain
        }

        $domainGuid = $domainInformation.ObjectGUID.ToString()
        $domainName = $domainInformation.DnsRoot
        $domainSid = $domainInformation.DomainSID.Value
        $forestName = $domainInformation.Forest
        $netBiosDomainName = $domainInformation.DnsRoot
        $azureStorageSid = $domainSid + "-123454321";

        Write-Verbose "Setting AD properties on $StorageAccountName in $ResourceGroupName : `
            EnableActiveDirectoryDomainServicesForFile=$true, ActiveDirectoryDomainName=$domainName, `
            ActiveDirectoryNetBiosDomainName=$netBiosDomainName, ActiveDirectoryForestName=$($domainInformation.Forest) `
            ActiveDirectoryDomainGuid=$domainGuid, ActiveDirectoryDomainSid=$domainSid, `
            ActiveDirectoryAzureStorageSid=$azureStorageSid"

        $Subscription =  $AzContext.Subscription.Id;
        $ApiVersion = '2021-04-01'

        $Uri = ('https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Storage/storageAccounts/{2}?api-version={3}' `
            -f $Subscription, $ResourceGroupName, $StorageAccountName, $ApiVersion);
    
        $json=
            @{
                properties=
                    @{azureFilesIdentityBasedAuthentication=
                        @{directoryServiceOptions="AADKERB";
                            activeDirectoryProperties=@{domainName="$($domainName)";
                                                        netBiosDomainName="$($netBiosDomainName)";
                                                        forestName="$($forestName)";
                                                        domainGuid="$($domainGuid)";
                                                        domainSid="$($domainSid)";
                                                        azureStorageSid="$($azureStorageSid)"}
                                                        }
                        }
            };  

        $json = $json | ConvertTo-Json -Depth 99

        $token = $(Get-AzAccessToken).Token
        $headers = @{ Authorization="Bearer $token" }

        try {
            Invoke-RestMethod -Uri $Uri -ContentType 'application/json' -Method PATCH -Headers $Headers -Body $json
        } catch {
            Write-Host $_.Exception.ToString()
            Write-Host "Error setting Storage Account AD properties.  StatusCode:" $_.Exception.Response.StatusCode.value__ 
            Write-Host "Error setting Storage Account AD properties.  StatusDescription:" $_.Exception.Response.StatusDescription
            Write-Error -Message "Caught exception setting Storage Account AD properties: $_" -ErrorAction Stop
        }
    }
    ```

3. 다음 PowerShell cmdlet을 실행하여 함수를 호출합니다.

    ```powershell
    Connect-AzAccount
    Set-StorageAccountAadKerberosADProperties -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
    ```

다음 목록에서 그룹 정책 또는 레지스트리 값을 구성하여 Azure AD Kerberos 기능을 사용하도록 설정합니다.

- 그룹 정책: `Administrative Templates\System\Kerberos\Allow retrieving the Azure AD Kerberos Ticket Granting Ticket during logon`
- 레지스트리 값: `reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters /v CloudKerberosTicketRetrievalEnabled /t REG_DWORD /d 1`

다음으로, 다음 지침에 따라 Kerberos TGT(Ticket Granting Ticket)를 검색할 수 있는지 확인합니다.

1. 명령 창을 엽니다.
2. 다음 명령을 실행합니다. 

    ```
    dsregcmd /RefreshPrt
    ```

3. 동일한 사용자 계정을 사용하여 디바이스를 잠그고 잠금을 해제합니다.
4. 명령 창에서 다음 명령을 실행합니다.

    ```
    klist purge
    klist get krbtgt
    ```

5. 서버 속성이 인 항목을 찾아 Kerberos TGT가 있는지 `krbtgt/KERBEROS.MICROSOFTONLINE.COM @ KERBEROS.MICROSOFTONLINE.COM` 확인합니다.
6. 명령 창에서 다음 명령을 실행하여 네트워크 공유를 탑재할 수 있는지 확인합니다.

    ```
    net use <DriveLetter>: \\<storage-account-name>.file.core.windows.net\<fIle-share-name>
    ```

마지막으로 [디렉터리 및 파일 수준 권한 구성의](../storage/files/storage-files-identity-ad-ds-configure-permissions.md) 지침에 따라 explorerls 또는 Windows Explorer를 사용하여 권한 구성을 완료합니다.

## <a name="configure-the-session-hosts"></a>세션 호스트 구성

FSLogix 프로필용 Azure AD 조인 VM에서 Azure 파일 공유에 액세스하려면 세션 호스트를 구성해야 합니다. 세션 호스트를 구성하려면 다음을 수행합니다.

1. 다음 목록의 값으로 그룹 정책 또는 레지스트리 값을 구성하여 Azure AD Kerberos 기능을 사용하도록 설정합니다. 이러한 값을 구성한 후에는 시스템을 다시 시작하여 변경 내용을 적용합니다.

    - 그룹 정책: `Administrative Templates\System\Kerberos\Allow retrieving the Azure AD Kerberos Ticket Granting Ticket during logon`
    - 레지스트리 값: `reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters /v CloudKerberosTicketRetrievalEnabled /t REG_DWORD /d 1`

2. FSLogix와 같은 로밍 프로필 솔루션에서 Azure AD를 사용하는 경우 자격 증명 관리자 자격 증명 키는 현재 로드 중인 프로필에 속해야 합니다. 이렇게 하면 하나의 VM으로만 제한되는 대신 여러 다른 VM에 프로필을 로드할 수 있습니다. 이 설정을 사용하도록 설정하려면 다음 명령을 실행하여 새 레지스트리 값을 만듭니다.

    ```
    reg add HKLM\Software\Policies\Microsoft\AzureADAccount /v LoadCredKeyFromProfile /t REG_DWORD /d 1
    ```

> [!NOTE]
> 세션 호스트는 도메인 컨트롤러에 대한 네트워크 가시선이 필요하지 않습니다.

### <a name="configure-fslogix-on-the-session-host"></a>세션 호스트에서 FSLogix 구성

이 섹션에서는 FSLogix를 사용하여 VM을 구성하는 방법을 보여 줍니다. 세션 호스트를 구성할 때마다 이 지침을 따라야 합니다. 모든 세션 호스트에서 레지스트리 키를 설정하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 이러한 옵션을 이미지에서 설정하거나 그룹 정책을 구성할 수 있습니다.

FSLogix를 구성하려면 다음을 수행합니다.

1. 필요한 경우 세션 호스트에서 [FSLogix를 업데이트하거나 설치합니다.](/fslogix/install-ht)

2. [프로필 컨테이너 레지스트리 설정 구성의](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings) 지침에 따라 **Enabled** 및 **VHDLocations** 레지스트리 값을 만듭니다. **VHDLocations** 값을 로 `\\<Storage-account-name>.file.core.windows.net\<file-share-name>` 설정합니다.

## <a name="test-your-deployment"></a>배포 테스트

FSLogix를 설치하고 구성한 후에는 호스트 풀의 애플리케이션 그룹에 할당된 사용자 계정으로 로그인하여 배포를 테스트할 수 있습니다. 로그인하는 사용자 계정에는 파일 공유를 사용할 수 있는 권한이 있어야 합니다.

사용자가 이전에 로그인한 경우 이 세션 중에 서비스에서 사용할 기존 로컬 프로필이 있습니다. 로컬 프로필을 만들지 않으려면 테스트에 사용할 새 사용자 계정을 만들거나 자습서: *DeleteLocalProfileWhenVHDShouldApply* 설정을 사용하도록 [사용자 프로필을 리디렉션하도록 프로필 컨테이너 구성에](/fslogix/configure-profile-container-tutorial/) 설명된 구성 방법을 사용합니다.

마지막으로 프로필을 테스트하여 작동하는지 확인합니다.

1. Azure Portal 열고 관리 계정으로 로그인합니다.

2. 사이드바에서 **스토리지 계정** 을 선택합니다.

3. 세션 호스트 풀에 대해 구성한 스토리지 계정을 선택합니다.

4. 사이드바에서 파일 **공유** 를 선택합니다.

5. 프로필을 저장하도록 구성한 파일 공유를 선택합니다.

6. 모든 것이 올바르게 설정된 경우 다음과 같이 형식이 지정된 이름의 디렉터리를 볼 수 `<user SID>_<username>` 있습니다.

## <a name="next-steps"></a>다음 단계

- FSLogix 문제를 해결하려면 [이 문제 해결 가이드](/fslogix/fslogix-trouble-shooting-ht)를 참조하세요.
- Azure Active Directory Domain Services를 사용하여 Azure Files FSLogix 프로필을 구성하려면 Azure Files 사용하여 [프로필 컨테이너 만들기 및 Azure AD DS.](create-profile-container-adds.md)
- Active Directory Domain Services 사용하여 Azure Files FSLogix 프로필을 구성하려면 Azure Files 사용하여 [프로필 컨테이너 만들기 및 AD DS.](create-file-share.md)
