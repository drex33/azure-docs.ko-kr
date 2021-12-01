---
title: Azure Active Directory를 사용 하 여 Azure 파일 공유 만들기
description: Azure Active Directory 도메인을 사용 하 여 기존 azure 가상 데스크톱 호스트 풀의 azure 파일 공유에 fslogix 프로필 컨테이너를 설정 합니다.
services: virtual-desktop
author: Heidilohr
manager: femila
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 12/01/2021
ms.author: helohr
ms.openlocfilehash: 7f8733f44f2545fa5ae237a9a44fe30223a933c0
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407937"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-active-directory"></a>Azure Files 및 Azure Active Directory를 사용 하 여 프로필 컨테이너 만들기

> [!IMPORTANT]
> AD (Azure Active Directory)에 대 한 Azure Files에 fslogix 프로필을 저장 하는 것은 현재 공개 미리 보기로 제공 되 고 있습니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Active Directory (AD)로 인증 된 하이브리드 사용자 id로 액세스할 수 있는 FSLogix 프로필을 저장 하는 Azure Files 공유를 만드는 방법에 대해 알아봅니다. 이제 azure AD 사용자가 Kerberos 인증을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다. 이 구성은 Azure AD를 사용 하 여 업계 표준 SMB 프로토콜을 통해 파일 공유에 액세스 하는 데 필요한 Kerberos 티켓을 발급 합니다. 최종 사용자는 하이브리드 Azure AD 조인 및 Azure AD 조인 Vm에서 도메인 컨트롤러에 대 한 시야를 요구 하지 않고 인터넷을 통해 Azure 파일 공유에 액세스할 수 있습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Azure AD를 사용 하 여 인증을 위해 Azure storage 계정을 구성 합니다.
- Azure Files 공유에 대 한 사용 권한을 구성 합니다.
- Azure Files에서 FSLogix 사용자 프로필을 저장할 세션 호스트를 구성 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD Kerberos 기능은 다음 운영 체제 에서만 사용할 수 있습니다.

  - 단일 또는 다중 세션을 Windows 11 Enterprise 합니다.
  - 최신 누적 업데이트가 설치 된 Windows 10 Enterprise 단일 또는 다중 세션 버전 2004 이상, 특히 [Windows 10에 대 한 KB5007253-2021-11 누적 업데이트 미리 보기](https://support.microsoft.com/topic/november-22-2021-kb5007253-os-builds-19041-1387-19042-1387-19043-1387-and-19044-1387-preview-d1847be9-46c1-49fc-bf56-1d469fc1b3af)
  - 최신 누적 업데이트가 설치 된 Windows Server 버전 2022, 특히 [Microsoft 서버 운영 체제 버전 21h2에 대 한 KB5007254-2021-11 누적 업데이트 미리 보기](https://support.microsoft.com/topic/november-22-2021-kb5007254-os-build-20348-380-preview-9a960291-d62e-486a-adcc-6babe5ae6fc1)입니다.

사용자 계정은 [하이브리드 사용자 id](../active-directory/hybrid/whatis-hybrid-identity.md)여야 합니다. 즉, Active Directory Domain Services (AD DS) 및 Azure AD Connect도 필요 합니다. Active Directory에서 이러한 계정을 만들고 Azure AD와 동기화 해야 합니다.

Azure 파일 공유에 대 한 RBAC (Azure Role-Based Access Control) 권한을 사용자 그룹에 할당 하려면 Active Directory에 그룹을 만들고 Azure AD에 동기화 해야 합니다.

> [!IMPORTANT]
> 이 기능은 현재 Azure 공용 클라우드에서만 지원 됩니다.

## <a name="configure-your-azure-storage-account"></a>Azure Storage 계정 구성

아직 없는 경우 [Azure Storage 계정을 만들어](../storage/files/storage-how-to-create-file-share.md#create-a-storage-account) 시작 합니다.

> [!NOTE]
> Azure Storage 계정은 azure AD와 Active Directory Domain Services (AD DS) 또는 azure AD DS 같은 두 번째 방법으로 인증할 수 없습니다. 한 가지 인증 방법을 사용할 수 있습니다.

다음 섹션의 지침에 따라 Azure AD 인증을 구성 하 고, Azure AD 서비스 주체를 구성 하 고, 저장소 계정에 대 한 API 권한을 설정 합니다.

### <a name="configure-azure-ad-authentication-on-your-azure-storage-account"></a>Azure Storage 계정에서 Azure AD 인증 구성

- Azure Storage PowerShell 모듈을 설치 합니다. 이 모듈은 Azure Storage 리소스에 대 한 관리 cmdlet을 제공 합니다. 저장소 계정을 만들고, 저장소 계정에서 Azure AD 인증을 사용 하도록 설정 하 고, 저장소 계정의 Kerberos 키를 검색 하는 데 필요 합니다. 모듈을 설치 하려면 PowerShell을 열고 다음 명령을 실행 합니다.

    ```powershell
    Install-Module -Name Az.Storage
    ```

- Azure AD PowerShell 모듈을 설치합니다. 이 모듈에서는 사용자 및 서비스 사용자 관리와 같은 Azure AD 관리 작업을 위한 관리 cmdlet을 제공 합니다. 이 모듈을 설치 하려면 PowerShell을 열고 다음 명령을 실행 합니다.

    ```powershell
    Install-Module -Name AzureAD
    ```

    자세한 내용은 [AZURE AD PowerShell 모듈 설치](/powershell/azure/active-directory/install-adv2)를 참조 하세요.

- 다음 PowerShell cmdlet을 실행 하 여 저장소 계정 이름 및 리소스 그룹 이름에 대 한 변수를 설정 하 고, 값을 사용자 환경에 적합 한 값으로 바꿉니다.

    ```powershell
    $resourceGroupName = "<MyResourceGroup>"
    $storageAccountName = "<MyStorageAccount>"
    ```
 
- 다음 PowerShell cmdlet을 실행 하 여 저장소 계정에서 Azure AD 인증을 사용 하도록 설정 합니다.

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

- 다음 PowerShell 명령을 실행 하 여 저장소 계정에 대 한 kerb1 저장소 계정 키를 생성 합니다.

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName -KeyName kerb1 -ErrorAction Stop 
    ```

### <a name="configure-the-azure-ad-service-principal-and-application"></a>Azure AD 서비스 주체 및 응용 프로그램 구성

저장소 계정에서 Azure AD 인증을 사용 하도록 설정 하려면 azure ad에서 저장소 계정을 나타내는 Azure AD 응용 프로그램을 만들어야 합니다. 이 구성은 공개 미리 보기 동안에는 Azure Portal에서 사용할 수 없습니다. PowerShell을 사용 하 여 응용 프로그램을 만들려면 다음 단계를 수행 합니다.

- 저장소 계정의 Kerberos 키를 기반으로 암호 (서비스 주체 암호)를 설정 합니다. Kerberos 키는 Azure AD와 Azure Storage 간에 공유 되는 암호입니다. Kerberos는 저장소 계정의 kerb1 키의 처음 32 바이트에서 암호 값을 파생 합니다. 암호를 설정 하려면 다음 cmdlet을 실행 합니다.

    ```powershell
    $kerbKey1 = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName -ListKerbKey | Where-Object { $_.KeyName -like "kerb1" }
    $aadPasswordBuffer = [System.Linq.Enumerable]::Take([System.Convert]::FromBase64String($kerbKey1.Value), 32);
    $password = "kk:" + [System.Convert]::ToBase64String($aadPasswordBuffer);
    ```

- Azure AD에 커넥트 다음 cmdlet을 실행 하 여 테 넌 트 정보를 검색 합니다.

    ```powershell
    Connect-AzureAD
    $azureAdTenantDetail = Get-AzureADTenantDetail;
    $azureAdTenantId = $azureAdTenantDetail.ObjectId
    $azureAdPrimaryDomain = ($azureAdTenantDetail.VerifiedDomains | Where-Object {$_._Default -eq $true}).Name
    ```

- 이러한 cmdlet을 실행 하 여 Azure AD 서비스 주체에 대 한 서비스 사용자 이름을 생성 합니다.

    ```powershell
    $servicePrincipalNames = New-Object string[] 3
    $servicePrincipalNames[0] = 'HTTP/{0}.file.core.windows.net' -f $storageAccountName
    $servicePrincipalNames[1] = 'CIFS/{0}.file.core.windows.net' -f $storageAccountName
    $servicePrincipalNames[2] = 'HOST/{0}.file.core.windows.net' -f $storageAccountName
    ```

- 이 cmdlet을 실행 하 여 저장소 계정에 대 한 응용 프로그램을 만듭니다.

    ```powershell
    $application = New-AzureADApplication -DisplayName $storageAccountName -IdentifierUris $servicePrincipalNames -GroupMembershipClaims "All";
    ```

- 이 cmdlet을 실행 하 여 저장소 계정에 대 한 서비스 주체를 만듭니다.

    ```powershell
    $servicePrincipal = New-AzureADServicePrincipal -AccountEnabled $true -AppId $application.AppId -ServicePrincipalType "Application";
    ```

- 다음 cmdlet을 실행 하 여 저장소 계정의 서비스 주체에 대 한 암호를 설정 합니다.

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

### <a name="set-the-api-permissions-on-the-newly-created-application"></a>새로 만든 응용 프로그램에 대 한 API 권한 설정

다음 단계를 수행 하 여 [Azure Portal](https://portal.azure.com) 에서 API 권한을 구성할 수 있습니다.

1. **Azure Active Directory** 를 엽니다.
2. 왼쪽 창에서 **앱 등록** 을 선택 합니다.
3. **모든 응용 프로그램** 을 선택 합니다.
4. 저장소 계정과 일치 하는 이름을 가진 응용 프로그램을 선택 합니다.
5. 왼쪽 창에서 **API 사용 권한** 을 선택 합니다.
6. **+ 권한 추가를** 선택 합니다.
7. 페이지 맨 위에서 **Microsoft Graph** 를 선택 합니다.
8. **위임된 권한** 을 선택합니다.
9. **Openid connect** 권한 그룹 아래에서 **openid connect** and **profile** 을 선택 합니다.
10. **사용자 권한 그룹** 에서 **사용자를 선택 합니다.**
11. 페이지 맨 아래에 있는 **사용 권한 추가** 를 선택 합니다.
12. **"DirectoryName"에 대 한 관리자 동의 부여를** 선택 합니다.

## <a name="configure-your-azure-files-share"></a>Azure Files 공유 구성

시작 하려면 저장소 계정에서 [Azure Files 공유를 만들어](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) FSLogix 프로필을 저장 합니다 (아직 없는 경우).

다음 섹션의 지침에 따라 사용자에 게 적절 한 액세스 수준을 제공 하도록 Azure Files 공유에 대 한 공유 수준 및 디렉터리 수준 권한을 구성 합니다.

### <a name="assign-share-level-permissions"></a>공유 수준 권한 할당
    
사용자에 게 파일 공유에 대 한 액세스 권한을 부여 해야 해당 파일을 사용할 수 있습니다. 공유 수준 권한을 할당 하는 방법에는 두 가지가 있습니다. 즉, 특정 Azure AD 사용자 또는 사용자 그룹에 할당 하거나 모든 인증 된 id에 기본 공유 수준 권한으로 할당할 수 있습니다. 공유 수준 권한 할당에 대 한 자세한 내용은 [id에 공유 수준 권한 할당](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)을 참조 하세요.

사용 중인 저장소 계정에 저장 된 fslogix 프로필을 사용 해야 하는 모든 사용자에 게 **Storage 파일 데이터 SMB 공유 참가자** 역할을 할당 해야 합니다.

> [!IMPORTANT]
> Azure 가상 데스크톱은 현재 하이브리드 사용자 및 사용자 그룹에 대 한 특정 권한 할당만 지원 합니다. 사용자 및 사용자 그룹은 Active Directory에서 관리 되 고 Azure AD Connect를 사용 하 여 Azure AD와 동기화 되어야 합니다.

### <a name="assign-directory-level-access-permissions"></a>디렉터리 수준 액세스 권한 할당

사용자가 다른 사용자의 사용자 프로필에 액세스 하지 못하도록 하려면 디렉터리 수준 권한도 할당 해야 합니다. 이 섹션에서는 사용 권한을 구성 하는 단계를 제공 합니다. [프로필 컨테이너에 대 한 저장소 권한 구성](/fslogix/fslogix-storage-config-ht) 에서 FSLogix 프로필에 대 한 권장 권한 목록에 대해 자세히 알아보세요.

> [!IMPORTANT]
> 적절 한 디렉터리 수준 사용 권한이 없는 사용자는 사용자 프로필을 삭제 하거나 다른 사용자의 개인 정보에 액세스할 수 있습니다. 실수로 인 한 삭제를 방지 하기 위해 사용자에 게 적절 한 권한이 있는지 확인 하는 것이 중요 합니다.

icacls 명령줄 유틸리티 또는 Windows 탐색기를 사용 하 여 파일 및 디렉터리에 대 한 사용 권한 (acl)을 설정할 수 있습니다. 사용 권한을 구성 하는 데 사용 하는 시스템은 다음 요구 사항을 충족 해야 합니다.

- Windows 버전은 [전제 조건](#prerequisites) 섹션에 정의 된 지원 되는 OS 요구 사항을 충족 합니다.
- Azure AD 조인 또는 하이브리드 Azure AD는 저장소 계정과 동일한 Azure AD 테 넌 트에 조인 됩니다.
- 도메인 컨트롤러에 대 한 시야를 가집니다.
- 은 (는) Active Directory 도메인에 가입 되어 있습니다 (Windows 탐색기 메서드만 해당).

공개 미리 보기 중에 Windows 탐색기를 사용 하 여 사용 권한을 구성 하려면 저장소 계정 구성도 필요 합니다. Icacls를 사용 하는 경우이 구성 단계를 건너뛸 수 있습니다.

저장소 계정을 구성 하려면:

1. Active Directory에 도메인 가입 된 장치에서 [ActiveDirectory PowerShell 모듈](/powershell/module/activedirectory/?view=windowsserver2019-ps&preserve-view=true) 을 설치 합니다 (아직 설치 하지 않은 경우).

2. 셸 환경을 지원 하도록 저장소 계정의 ActiveDirectoryProperties을 설정 합니다. Azure AD는 현재 셸에서 Acl 구성을 지원 하지 않으므로 대신 Active Directory를 사용 해야 합니다. 셸을 구성 하려면 PowerShell에서 다음 명령을 실행 합니다.
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

3. 다음 PowerShell cmdlet을 실행 하 여 함수를 호출 합니다.

    ```powershell
    Connect-AzAccount
    Set-StorageAccountAadKerberosADProperties -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
    ```

다음 목록에서 그룹 정책 또는 레지스트리 값을 구성 하 여 Azure AD Kerberos 기능을 사용 하도록 설정 합니다.

- 그룹 정책: `Administrative Templates\System\Kerberos\Allow retrieving the Azure AD Kerberos Ticket Granting Ticket during logon`
- 레지스트리 값: `reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters /v CloudKerberosTicketRetrievalEnabled /t REG_DWORD /d 1`

다음 지침에 따라 Kerberos TGT (허용 티켓)를 검색할 수 있는지 확인 합니다.

1. 명령 창을 엽니다.
2. 다음 명령을 실행합니다. 

    ```
    dsregcmd /RefreshPrt
    ```

3. 동일한 사용자 계정을 사용 하 여 장치를 잠그고 잠금을 해제 합니다.
4. 명령 창에서 다음 명령을 실행 합니다.

    ```
    klist purge
    klist get krbtgt
    ```

5. 서버 속성이 인 항목을 검색 하 여 Kerberos TGT가 있는지 확인 `krbtgt/KERBEROS.MICROSOFTONLINE.COM @ KERBEROS.MICROSOFTONLINE.COM` 합니다.
6. 명령 창에서 다음 명령을 실행 하 여 네트워크 공유를 탑재할 수 있는지 확인 합니다.

    ```
    net use <DriveLetter>: \\<storage-account-name>.file.core.windows.net\<fIle-share-name>
    ```

마지막으로, icacls [및 파일 수준 권한 구성](../storage/files/storage-files-identity-ad-ds-configure-permissions.md) 의 지침에 따라 icacls 또는 Windows 탐색기를 사용 하 여 사용 권한 구성을 마칩니다.

## <a name="configure-the-session-hosts"></a>세션 호스트 구성

Azure AD 조인 VM에서 FSLogix 프로필에 대 한 Azure 파일 공유에 액세스 하려면 세션 호스트를 구성 해야 합니다. 세션 호스트를 구성 하려면:

1. 다음 목록에 있는 값을 사용 하 여 그룹 정책 또는 레지스트리 값을 구성 하 여 Azure AD Kerberos 기능을 사용 하도록 설정 합니다. 이러한 값을 구성한 후에는 시스템을 다시 시작 하 여 변경 내용이 적용 되도록 합니다.

    - 그룹 정책: `Administrative Templates\System\Kerberos\Allow retrieving the Azure AD Kerberos Ticket Granting Ticket during logon`
    - 레지스트리 값: `reg add HKLM\SYSTEM\CurrentControlSet\Control\Lsa\Kerberos\Parameters /v CloudKerberosTicketRetrievalEnabled /t REG_DWORD /d 1`

2. FSLogix와 같은 로밍 프로필 솔루션과 함께 Azure AD를 사용 하는 경우 자격 증명 관리자의 자격 증명 키는 현재 로드 하는 프로필에 속해야 합니다. 이렇게 하면 한 개만으로 제한 되는 것이 아니라 다양 한 Vm에서 프로필을 로드할 수 있습니다. 이 설정을 사용 하도록 설정 하려면 다음 명령을 실행 하 여 새 레지스트리 값을 만듭니다.

    ```
    reg add HKLM\Software\Policies\Microsoft\AzureADAccount /v LoadCredKeyFromProfile /t REG_DWORD /d 1
    ```

> [!NOTE]
> 세션 호스트에는 도메인 컨트롤러에 대 한 네트워크 회선이 필요 하지 않습니다.

### <a name="configure-fslogix-on-the-session-host"></a>세션 호스트에서 FSLogix 구성

이 섹션에서는 FSLogix를 사용하여 VM을 구성하는 방법을 보여 줍니다. 세션 호스트를 구성할 때마다 이 지침을 따라야 합니다. 모든 세션 호스트에서 레지스트리 키를 설정하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 이러한 옵션을 이미지에서 설정하거나 그룹 정책을 구성할 수 있습니다.

FSLogix를 구성 하려면:

1. 필요한 경우 세션 호스트에서 [FSLogix를 업데이트 하거나 설치](/fslogix/install-ht) 합니다.

2. [프로필 컨테이너 레지스트리 설정 구성](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings) 의 지침에 따라 **Enabled** 및 **VHDLocations** 레지스트리 값을 만듭니다. **VHDLocations** 의 값을로 설정 `\\<Storage-account-name>.file.core.windows.net\<file-share-name>` 합니다.

## <a name="test-your-deployment"></a>배포 테스트

FSLogix를 설치 하 고 구성한 후에는 호스트 풀의 응용 프로그램 그룹에 할당 된 사용자 계정으로 로그인 하 여 배포를 테스트할 수 있습니다. 로그인 하는 사용자 계정에는 파일 공유를 사용할 수 있는 권한이 있어야 합니다.

이전에 사용자가 로그인 한 경우이 세션 중에 서비스에서 사용할 기존 로컬 프로필을 갖게 됩니다. 로컬 프로필을 만들지 않으려면 테스트에 사용할 새 사용자 계정을 만들거나 자습서: *DeleteLocalProfileWhenVHDShouldApply* 설정을 사용 하도록 [사용자 프로필을 리디렉션하도록 프로필 컨테이너 구성](/fslogix/configure-profile-container-tutorial/) 에 설명 된 구성 방법을 사용 합니다.

마지막으로 프로필을 테스트 하 여 작동 하는지 확인 합니다.

1. Azure Portal를 열고 관리 계정으로 로그인 합니다.

2. 사이드바에서 **스토리지 계정** 을 선택합니다.

3. 세션 호스트 풀에 대해 구성한 저장소 계정을 선택 합니다.

4. 사이드바에서 **파일 공유** 를 선택 합니다.

5. 프로필을 저장 하도록 구성한 파일 공유를 선택 합니다.

6. 모든 항목이 올바르게 설정 되 면 다음과 같이 이름이 지정 된 디렉터리가 표시 됩니다 `<user SID>_<username>` .

## <a name="next-steps"></a>다음 단계

- FSLogix 문제를 해결하려면 [이 문제 해결 가이드](/fslogix/fslogix-trouble-shooting-ht)를 참조하세요.
- Azure Active Directory 도메인 서비스를 사용 하 여 Azure Files에서 fslogix 프로필을 구성 하려면 [Azure Files 및 Azure AD DS를 사용 하 여 프로필 컨테이너 만들기](create-profile-container-adds.md)를 참조 하세요.
- Active Directory Domain Services를 사용 하 여 Azure Files에서 FSLogix 프로필을 구성 하려면 [Azure Files 및 AD DS를 사용 하 여 프로필 컨테이너 만들기](create-file-share.md)를 참조 하세요.
