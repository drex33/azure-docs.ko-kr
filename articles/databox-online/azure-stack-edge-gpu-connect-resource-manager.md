---
title: Azure Stack Edge GPU 디바이스의 Azure Resource Manager에 연결
description: Azure PowerShell을 사용하여 Azure Stack Edge Pro GPU에서 실행되는 Azure Resource Manager에 연결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/09/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ac064d53f4d85d8f517ef7bba67e88abe625f16
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515670"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-device"></a>Azure Stack Edge 디바이스의 Azure Resource Manager에 연결

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. Azure Stack Edge 디바이스는 로컬 구독에서 VM 만들기 업데이트 및 삭제를 수행하는 동일한 Azure Resource Manager API를 지원합니다. 이 지원을 통해 클라우드와 일치하는 방식으로 디바이스를 관리할 수 있습니다. 

이 문서에서는 Azure PowerShell을 사용하여 Azure Resource Manager를 통해 Azure Stack Edge 디바이스의 로컬 API에 연결하는 방법을 설명합니다.


## <a name="endpoints-on-azure-stack-edge-device"></a>Azure Stack Edge 디바이스의 엔드포인트

다음 표에서는 디바이스에 노출되는 다양한 엔드포인트, 지원되는 프로토콜 및 해당 엔드포인트에 액세스하는 포트를 요약하여 설명합니다. 이 문서 전체에서 이러한 엔드포인트에 대한 참조를 찾을 수 있습니다.

| # | 엔드포인트 | 지원되는 프로토콜 | 사용되는 포트 | 사용 대상 |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | 자동화를 위해 Azure Resource Manager에 연결 |
| 2. | 보안 토큰 서비스 | https | 443 | 액세스 및 새로 고침 토큰을 통해 인증 |
| 3. | Blob* | https | 443 | REST를 통해 Blob Storage에 연결 |

\**Azure Resource Manager에 연결하기 위해 Blob Storage 엔드포인트 연결은 필요하지 않습니다.*
 
## <a name="connecting-to-azure-resource-manager-workflow"></a>Azure Resource Manager 워크플로에 연결

Azure Resource Manager를 사용하여 디바이스의 로컬 API에 연결하는 프로세스에는 다음 단계가 필요합니다.

| 단계 # | 수행하는 단계 | .. 수행하는 위치 |
| --- | --- | --- |
| 1. | [Azure Stack Edge 디바이스 구성](#step-1-configure-azure-stack-edge-device) | 로컬 웹 UI |
| 2. | [인증서 만들기 및 설치](#step-2-create-and-install-certificates) | Windows 클라이언트/로컬 웹 UI |
| 3. | [필수 구성 요소 검토 및 구성](#step-3-install-powershell-on-the-client) | Windows 클라이언트 |
| 4. | [클라이언트에서 Azure PowerShell 설정](#step-4-set-up-azure-powershell-on-the-client) | Windows 클라이언트 |
| 5. | [엔드포인트 이름 확인을 위해 호스트 파일 수정](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows 클라이언트 또는 DNS 서버 |
| 6. | [엔드포인트 이름이 확인되었는지 검사](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows 클라이언트 |
| 7. | [Azure PowerShell cmdlet을 사용하여 Azure Resource Manager 연결 확인](#step-7-set-azure-resource-manager-environment) | Windows 클라이언트 |

다음 섹션에서는 Azure Resource Manager 연결 시 위의 각 단계에 대해 자세히 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 Azure Resource Manager를 통해 디바이스에 연결하는 데 사용되는 클라이언트가 TLS 1.2를 사용하는지 확인합니다. 자세한 내용은 [Azure Stack Edge 디바이스에 액세스하는 Windows 클라이언트에서 TLS 1.2 구성](azure-stack-edge-gpu-configure-tls-settings.md)을 참조하세요.

## <a name="step-1-configure-azure-stack-edge-device"></a>1단계: Azure Stack Edge 디바이스 구성 

Azure Stack Edge 디바이스의 로컬 웹 UI에서 다음 단계를 수행합니다.

1. Azure Stack Edge 디바이스에 대한 네트워크 설정을 완료합니다. 

    ![로컬 웹 UI "네트워크 설정" 페이지](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    디바이스 IP 주소를 적어 둡니다. 이 IP는 나중에 사용됩니다.

2. **디바이스** 페이지에서 디바이스 이름과 DNS 도메인을 구성합니다. 나중에 사용하므로 디바이스 이름과 DNS 도메인을 기록해 둡니다.

    ![로컬 웹 UI "디바이스" 페이지](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > 디바이스 이름, DNS 도메인은 노출되는 엔드포인트를 구성하는 데 사용됩니다.
    > 로컬 웹 UI의 **디바이스** 페이지에 있는 Azure Resource Manager 및 BLOB 엔드포인트를 사용합니다.


## <a name="step-2-create-and-install-certificates"></a>2단계: 인증서 만들기 및 설치

인증서를 통해 통신을 신뢰할 수 있는지 확인합니다. Azure Stack Edge 디바이스에서 자체 서명된 어플라이언스, BLOB 및 Azure Resource Manager 인증서가 자동으로 생성됩니다. 필요에 따라 자체 서명된 BLOB 및 Azure Resource Manager 인증서를 가져올 수 있습니다.

자체 서명된 인증서를 가져오는 경우 인증서의 해당 서명 체인도 필요합니다. 서명 체인, Azure Resource Manager 및 디바이스의 BLOB 인증서의 경우 디바이스를 인증하고 통신하려면 클라이언트 컴퓨터에도 해당 인증서가 있어야 합니다.

Azure Resource Manager에 연결하려면 서명 체인 및 엔드포인트 인증서를 만들거나 취득하고, Windows 클라이언트에 이러한 인증서를 가져온 후 마지막으로 디바이스에 이러한 인증서를 업로드해야 합니다.

### <a name="create-certificates"></a>인증서 만들기

테스트 및 개발용으로만 사용되는 경우 Windows PowerShell을 사용하여 로컬 시스템에서 인증서를 만들 수 있습니다. 클라이언트에 대한 인증서를 만드는 동안 다음 지침을 따르세요.

1. 먼저 서명 체인에 대한 루트 인증서를 만들어야 합니다. 자세한 내용은 [서명 체인 인증서를 만드는](azure-stack-edge-gpu-create-certificates-powershell.md#create-signing-chain-certificate) 단계를 참조하세요.

2. 그런 다음, Azure Resource Manager 및 Blob에 대한 엔드포인트 인증서를 만들 수 있습니다. 로컬 웹 UI의 **디바이스** 페이지에서 이러한 엔드포인트를 가져올 수 있습니다. [엔드포인트 인증서를 만드는](azure-stack-edge-gpu-create-certificates-powershell.md#create-signed-endpoint-certificates) 단계를 참조하세요.

3. 이러한 모든 인증서의 경우 주체 이름 및 주체의 대체 이름이 다음 지침을 따라야 합니다.

    |형식 |SN(주체 이름)  |SAN(주체 대체 이름)  |주체 이름 예 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage*|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |두 엔드포인트에 대한 다중 SAN 단일 인증서|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

\* Azure Resource Manager에 연결하는 데 Blob Storage는 필요하지 않습니다. 디바이스에서 로컬 스토리지 계정을 만드는 경우 여기에 나열됩니다.

인증서에 대한 자세한 내용은 [디바이스에 인증서를 업로드하고 디바이스에 액세스하는 클라이언트에서 인증서를 가져오는](azure-stack-edge-gpu-manage-certificates.md) 방법으로 이동하세요.

### <a name="upload-certificates-on-the-device"></a>디바이스에 인증서 업로드

이전 단계에서 만든 인증서는 클라이언트의 개인 저장소에 저장됩니다. 이러한 인증서는 클라이언트에서 적절한 형식 파일로 내보내야 합니다. 그러면 디바이스에 업로드할 수 있습니다.

1. 루트 인증서는 *.cer* 파일 확장명을 가진 DER 형식 파일로 내보내야 합니다. 자세한 단계는 [.cer 형식 파일로 인증서 내보내기](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format)를 참조하세요.

2. 엔드포인트 인증서는 프라이빗 키를 사용하여 *.pfx* 파일로 내보내야 합니다. 자세한 단계는 [프라이빗 키를 사용하여 .pfx 파일로 인증서 내보내기](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key)를 참조하세요.

3. 그런 다음, 로컬 웹 UI의 **인증서** 페이지에서 **+인증서 추가** 옵션을 사용하여 디바이스에 루트 및 엔드포인트 인증서를 업로드합니다. 인증서를 업로드하려면 [인증서 업로드](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)의 단계를 따르세요.


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Azure PowerShell을 실행하는 클라이언트에 인증서 가져오기

Azure Resource Manager API를 호출하는 Windows 클라이언트는 디바이스와의 신뢰를 설정해야 합니다. 이를 위해 이전 단계에서 만든 인증서를 Windows 클라이언트의 적절한 인증서 저장소로 가져와야 합니다.

1. 이제 *.cer* 확장명을 사용하여 DER 형식으로 내보낸 루트 인증서를 클라이언트 시스템의 신뢰할 수 있는 루트 인증 기관에 가져와야 합니다. 자세한 단계는 [신뢰할 수 있는 루트 인증 기관 저장소로 인증서 가져오기](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)를 참조하세요.

2. *.pfx* 로 내보낸 엔드포인트 인증서를 *.cer* 파일로 내보내야 합니다. 그런 다음, 이 *.cer* 을 시스템의 **개인** 인증서 저장소로 가져옵니다. 자세한 단계는 [개인 저장소로 인증서 가져오기](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)를 참조하세요.

## <a name="step-3-install-powershell-on-the-client"></a>3단계: 클라이언트에 PowerShell 설치 

### <a name="az"></a>[Az](#tab/Az)

Windows 클라이언트는 다음 사전 요구 사항을 충족해야 합니다.


1. PowerShell 버전 5.0을 실행합니다. PowerShell 버전 5.0이 있어야 합니다. 시스템의 PowerShell 버전을 확인하려면 다음 cmdlet을 실행합니다.

    ```powershell
    $PSVersionTable.PSVersion
    ```

    **주** 버전을 비교하여 5.0 이상인지 확인합니다.

    만료된 버전을 사용하는 경우 [기존 Windows PowerShell 업그레이드](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell)를 참조합니다.

    PowerShell 5.0이 없으면 [Windows PowerShell 설치](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true)를 수행합니다.

    예제 출력은 다음과 같습니다.

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      19041  906
    ```
    
1. PowerShell 갤러리에 액세스할 수 있습니다.

    관리자 권한으로 PowerShell을 실행합니다. PowerShellGet 버전이 2.2.3보다 이전 버전인지 확인합니다. 또한 `PSGallery`가 리포지토리로 등록되어 있는지 확인합니다.

    ```powershell
    Install-Module PowerShellGet –MinimumVersion 2.2.3
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    예제 출력은 다음과 같습니다.
    
    ```output
    PS C:\windows\system32> Install-Module PowerShellGet –MinimumVersion 2.2.3
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```    

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Windows 클라이언트는 다음 사전 요구 사항을 충족해야 합니다.


1. PowerShell 버전 5.0을 실행합니다. PowerShell 버전 5.0이 있어야 합니다. PowerShell Core는 지원되지 않습니다. 시스템의 PowerShell 버전을 확인하려면 다음 cmdlet을 실행합니다.

    ```powershell
    $PSVersionTable.PSVersion
    ```

    **주** 버전을 비교하여 5.0 이상인지 확인합니다.

    만료된 버전을 사용하는 경우 [기존 Windows PowerShell 업그레이드](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell)를 참조합니다.

    PowerShell 5.0이 없으면 [Windows PowerShell 설치](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true)를 수행합니다.

    예제 출력은 다음과 같습니다.

    ```output
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. PowerShell 갤러리에 액세스할 수 있습니다.

    관리자 권한으로 PowerShell을 실행합니다. `PSGallery`가 리포지토리로 등록되어 있는지 확인합니다.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    예제 출력은 다음과 같습니다.
    
    ```output
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
---    
리포지토리를 신뢰할 수 없거나 추가 정보가 필요한 경우 [PowerShell 갤러리 접근성 유효성 검사](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility)를 참조하세요.

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>4단계: 클라이언트에서 Azure PowerShell 설정 

### <a name="az"></a>[Az](#tab/Az)

디바이스 작업을 수행할 클라이언트에 Azure PowerShell 모듈을 설치합니다.

1. PowerShell을 관리자 권한으로 실행합니다. PowerShell 갤러리에 대한 액세스 권한이 필요합니다. 


1. 먼저 클라이언트에 기존 버전의 `AzureRM` 및 `Az` 모듈이 없는지 확인합니다. 확인하려면 다음 명령을 실행합니다.

    ```powershell
    # Check existing versions of AzureRM modules
    Get-InstalledModule -Name AzureRM -AllVersions

    # Check existing versions of Az modules
    Get-InstalledModule -Name Az -AllVersions
    ```

    기존 버전이 있는 경우 `Uninstall-Module` cmdlet을 사용하여 제거합니다. 자세한 내용은 
    - [AzureRM 모듈 제거](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-az-module)
    - [Az 모듈 제거](/powershell/azure/uninstall-az-ps?view=azps-6.0.0&preserve-view=true#uninstall-the-azurerm-module)

1. PowerShell 갤러리에서 필요한 Azure PowerShell 모듈을 설치하려면 다음 명령을 실행합니다.

    - 클라이언트에서 PowerShell Core 버전 7.0 이상을 사용하는 경우

        ```powershell
        # Install the Az.BootStrapper module. Select Yes when prompted to install NuGet.
        
        Install-Module -Name Az.BootStrapper
        
        # Install and import the API Version Profile into the current PowerShell session.
        
        Use-AzProfile -Profile 2020-09-01-hybrid -Force
        
        # Confirm the installation of PowerShell
        Get-Module -Name "Az*" -ListAvailable
        ```
    
    - 클라이언트에서 PowerShell 5.1 이상 버전을 사용하는 경우
        
        ```powershell
        #Install the Az module version 1.10.0
        
        Install-Module –Name Az –RequiredVersion 1.10.0    
        ```

3.  설치가 끝나면 Az 모듈 버전 1.10.0이 실행되고 있는지 확인합니다. 
   

    PowerShell Core 7.0 이상 버전을 사용하는 경우 아래 예제 출력은 Az 버전 1.10.0 모듈이 성공적으로 설치되었음을 나타냅니다.
    
    ```output
   
    PS C:\windows\system32> Install-Module -Name Az.BootStrapper
    PS C:\windows\system32> Use-AzProfile -Profile 2020-09-01-hybrid -Force
    Loading Profile 2020-09-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Az*" -ListAvailable
    ```

    PowerShell 5.1 이상 버전을 사용하는 경우 아래 예제 출력은 Az 버전 1.10.0 모듈이 성공적으로 설치되었음을 나타냅니다.
     
    ```powershell
    PS C:\WINDOWS\system32> Get-InstalledModule -Name Az -AllVersions
    Version              Name                                Repository           Description
    -------              ----                                ----------           ------
    1.10.0               Az                                  PSGallery            Mic...  
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

디바이스 작업을 수행할 클라이언트에 Azure PowerShell 모듈을 설치합니다.

1. PowerShell을 관리자 권한으로 실행합니다. PowerShell 갤러리에 대한 액세스 권한이 필요합니다. 


2. PowerShell 갤러리에서 필요한 Azure PowerShell 모듈을 설치하려면 다음 명령을 실행합니다.

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    설치가 끝나면 Azure RM 모듈 버전 2.5.0이 실행되고 있는지 확인합니다. 
    필요한 버전과 일치하지 않는 기존 버전의 Azure RM 모듈이 있는 경우 다음 명령을 사용하여 제거합니다.

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    이제 필요한 버전을 다시 설치해야 합니다.
   

    AzureRM 버전 2.5.0 모듈이 성공적으로 설치되었음을 나타내는 샘플 출력이 아래에 나와 있습니다.
    
    ```powershell
    PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
    PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Loading Profile 2019-03-01-hybrid
    PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
     
        Directory: C:\Program Files\WindowsPowerShell\Modules
     
    ModuleType Version    Name                          ExportedCommands
    ---------- -------    ----                          ----------------
    Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
    Script     2.5.0      AzureRM
    Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
    Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
    Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
    Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
    Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
    Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
    Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
    Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
    Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
    Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
    Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
    Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...
    
     
        Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
     
    ModuleType Version    Name                            ExportedCommands
    ---------- -------    ----                           ----------------
    Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
    ```
---   

## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>5단계: 엔드포인트 이름 확인을 위해 호스트 파일 수정 

이제 다음에 디바이스 IP 주소를 추가합니다.

- 클라이언트의 호스트 파일 또는
- DNS 서버 구성

> [!IMPORTANT]
> 엔드포인트 이름 확인을 위해 DNS 서버 구성을 수정하는 것이 좋습니다.

디바이스에 연결하는 데 사용하는 Windows 클라이언트에서 다음 단계를 수행합니다.

1. 관리자로 **메모장** 을 시작한 다음, C:\Windows\System32\Drivers\etc에 있는 **hosts** 파일을 엽니다.

    ![Windows 탐색기 호스트 파일](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. 다음 항목을 디바이스에 적합한 값으로 대체하고 **hosts** 파일에 추가합니다. 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > hosts 파일의 항목은 이후 단계에서 Azure Resource Manager에 연결하기 위해 제공되는 것과 정확히 일치해야 합니다. 여기에서 DNS 도메인 항목이 모두 소문자로 되어 있는지 확인합니다. `<appliance name>` 및 `<DNS domain>` 값을 가져오려면 디바이스의 로컬 UI에서 **디바이스** 페이지로 이동합니다.

    이전 단계에서 로컬 웹 UI의 디바이스 IP를 저장했습니다.

    `login.<appliance name>.<DNS domain>` 항목은 STS(보안 토큰 서비스)의 엔드포인트입니다. STS는 보안 토큰의 생성, 유효성 검사, 갱신 및 취소를 담당합니다. 보안 토큰 서비스는 디바이스와 클라이언트 사이의 연속 통신에 사용되는 액세스 토큰과 새로 고침 토큰을 만드는 데 사용됩니다.

    Azure Resource Manager에 연결하는 경우 Blob Storage에 대한 엔드포인트는 선택 사항입니다. 이 엔드포인트는 스토리지 계정을 통해 Azure로 데이터를 전송할 때 필요합니다.

3. 참조를 위해 다음 이미지를 사용합니다. **호스트** 파일을 저장합니다.

    ![메모장의 hosts 파일](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>6단계: 클라이언트에서 엔드포인트 이름 확인을 확인

엔드포인트 이름이 디바이스에 연결하는 데 사용하는 클라이언트에서 확인되는지 검사합니다.

1. `ping.exe` 명령줄 유틸리티를 사용하여 엔드포인트 이름이 확인되는지 검사할 수 있습니다. IP 주소를 지정하는 경우 `ping` 명령은 추적 중인 컴퓨터의 TCP/IP 호스트 이름을 반환합니다.

    아래 예제에 나온 것처럼 `-a` 스위치를 명령줄에 추가합니다. 호스트 이름이 반환 가능한 경우에도 잠재적으로 중요한 이 정보가 회신으로 반환됩니다.

    ![명령 프롬프트에서 Ping 실행](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>7단계: Azure Resource Manager 환경 설정

### <a name="az"></a>[Az](#tab/Az)

Azure Resource Manager 환경을 설정하고 디바이스와 클라이언트 간 통신이 Azure Resource Manager를 통해 제대로 작동하는지 확인합니다. 이를 확인하려면 다음 단계를 수행합니다.


1. `Add-AzEnvironment` cmdlet을 사용하여 Azure Resource Manager를 통한 통신이 정상적으로 작동하고 API 호출이 Azure Resource Manager 전용 포트(443)를 통과하는지 확인합니다.

    `Add-AzEnvironment` cmdlet은 엔드포인트와 메타데이터를 추가하여 Azure Resource Manager cmdlet이 Azure Resource Manager의 새 인스턴스에 연결될 수 있도록 합니다. 


    > [!IMPORTANT]
    > 다음 cmdlet에 제공하는 Azure Resource Manager 엔드포인트 URL은 대/소문자가 구분됩니다. 엔드포인트 URL이 모두 소문자이고 hosts 파일에 제공된 것과 일치하는지 확인합니다. 대/소문자가 일치하지 않으면 오류가 표시됩니다.

    ```powershell
    Add-AzEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    샘플 출력은 다음과 같습니다.
    
    ```output
    PS C:\WINDOWS\system32> Add-AzEnvironment -Name AzASE -ARMEndpoint "https://management.myasegpu.wdshcsso.com/"
    
    Name  Resource Manager Url                      ActiveDirectory Authority
    ----  --------------------                      -------------------------
    AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...
    ```

2. 환경을 Azure Stack Edge로 설정하고 Azure Resource Manager 호출에 사용할 포트를 443으로 설정합니다. 다음 두 가지 방법으로 환경을 정의합니다.

    - 환경을 설정합니다. 다음 명령을 입력합니다.

        ```powershell
        Set-AzEnvironment -Name <Environment Name>
        ```
        
        출력의 예제는 다음과 같습니다.

        ```output
        PS C:\WINDOWS\system32> Set-AzEnvironment -Name AzASE

        Name  Resource Manager Url                      ActiveDirectory Authority
        ----  --------------------                      -------------------------
        AzASE https://management.myasegpu.wdshcsso.com/ https://login.myasegpu.wdshcsso.c...     
        ```
        자세한 내용은 [Set-AzEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true)를 참조하세요.    

    - 실행하는 모든 cmdlet에 대해 인라인으로 환경을 정의합니다. 이렇게 하면 모든 API 호출이 올바른 환경을 통해 전달됩니다. 기본적으로 호출은 Azure 퍼블릭을 통해 전달되지만, 이러한 호출은 Azure Stack Edge 디바이스에 대해 설정된 환경을 통해 전달되기를 원합니다.

    - [ 환경을 전환하는](#switch-environments) 방법에 대한 자세한 내용을 참조하세요.

2. 로컬 디바이스 API를 호출하여 Azure Resource Manager에 대한 연결을 인증합니다. 

    1. 이러한 자격 증명은 로컬 컴퓨터 계정용이며 API 액세스에만 사용됩니다.

    2. `login-AzAccount` 또는 `Connect-AzAccount` 명령을 통해 연결할 수 있습니다. 

        1. 로그인하려면 다음 명령을 입력합니다. 
        
            ```powershell
            $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            ```

            이 인스턴스에서는 하드 코딩되므로 테넌트 ID c0257de7-538f-415c-993a-1b87a031879d를 사용합니다.
            다음 사용자 이름과 암호를 사용하세요.

            - **사용자 이름** - *EdgeArmUser*

            - **암호** - [Azure Resource Manager의 암호를 설정](azure-stack-edge-gpu-set-azure-resource-manager-password.md)하고 이 암호를 사용하여 로그인합니다. 
       


            다음은 `Connect-AzAccount`의 예제 출력입니다.

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzASE
            
            PS C:\windows\system32>
            ```                   
        
            `login-AzAccount` cmdlet을 사용하여 로그인할 수도 있습니다. 
            
            `login-AzAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            출력의 예제는 다음과 같습니다. 
         
            ```output
            PS C:\WINDOWS\system32> login-AzAccount -EnvironmentName AzASE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account               SubscriptionName              TenantId
            -------               ----------------              --------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a...            
            
            PS C:\WINDOWS\system32>
            ```
3. 디바이스에 대한 연결이 작동하는지 확인하려면 `Get-AzResource` 명령을 사용합니다. 이 명령은 디바이스에 로컬로 존재하는 모든 리소스를 반환해야 합니다.

    출력의 예제는 다음과 같습니다.

    ```output
    PS C:\WINDOWS\system32> Get-AzResource
    
    Name              : aseimagestorageaccount
    ResourceGroupName : ase-image-resourcegroup
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ase-image-resourcegroup/providers/Microsoft.Storage/storageac
                        counts/aseimagestorageaccount
    Tags              :
    
    Name              : myaselinuxvmimage1
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Compute/images
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Compute/images/myaselinuxvmimage1
    Tags              :
    
    Name              : ASEVNET
    ResourceGroupName : ASERG
    ResourceType      : Microsoft.Network/virtualNetworks
    Location          : dbelocal
    ResourceId        : /subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Network/virtualNetworks/ASEVNET
    Tags              :
    
    PS C:\WINDOWS\system32>  
    ```

   

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

Azure Resource Manager 환경을 설정하고 디바이스와 클라이언트 간 통신이 Azure Resource Manager를 통해 제대로 작동하는지 확인합니다. 이를 확인하려면 다음 단계를 수행합니다.


1. `Add-AzureRmEnvironment` cmdlet을 사용하여 Azure Resource Manager를 통한 통신이 정상적으로 작동하고 API 호출이 Azure Resource Manager 전용 포트(443)를 통과하는지 확인합니다.

    `Add-AzureRmEnvironment` cmdlet은 엔드포인트와 메타데이터를 추가하여 Azure Resource Manager cmdlet이 Azure Resource Manager의 새 인스턴스에 연결될 수 있도록 합니다. 


    > [!IMPORTANT]
    > 다음 cmdlet에 제공하는 Azure Resource Manager 엔드포인트 URL은 대/소문자가 구분됩니다. 엔드포인트 URL이 모두 소문자이고 hosts 파일에 제공된 것과 일치하는지 확인합니다. 대/소문자가 일치하지 않으면 오류가 표시됩니다.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    샘플 출력은 다음과 같습니다.
    
    ```output
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. 환경을 Azure Stack Edge로 설정하고 Azure Resource Manager 호출에 사용할 포트를 443으로 설정합니다. 다음 두 가지 방법으로 환경을 정의합니다.

    - 환경을 설정합니다. 다음 명령을 입력합니다.

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    자세한 내용은 [Set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true)를 참조하세요.

    - 실행하는 모든 cmdlet에 대해 인라인으로 환경을 정의합니다. 이렇게 하면 모든 API 호출이 올바른 환경을 통해 전달됩니다. 기본적으로 호출은 Azure 퍼블릭을 통해 전달되지만, 이러한 호출은 Azure Stack Edge 디바이스에 대해 설정된 환경을 통해 전달되기를 원합니다.

    - [AzureRM 환경을 전환하는 방법](#switch-environments)에 대한 자세한 내용을 참조하세요.

2. 로컬 디바이스 API를 호출하여 Azure Resource Manager에 대한 연결을 인증합니다. 

    1. 이러한 자격 증명은 로컬 컴퓨터 계정용이며 API 액세스에만 사용됩니다.

    2. `login-AzureRMAccount` 또는 `Connect-AzureRMAccount` 명령을 통해 연결할 수 있습니다. 

        1. 로그인하려면 다음 명령을 입력합니다. 이 인스턴스의 테넌트 ID는 c0257de7-538f-415c-993a-1b87a031879d로 하드 코딩됩니다. 다음 사용자 이름과 암호를 사용하세요.

            - **사용자 이름** - *EdgeArmUser*

            - **암호** - [Azure Resource Manager의 암호를 설정](azure-stack-edge-gpu-set-azure-resource-manager-password.md)하고 이 암호를 사용하여 로그인합니다. 

            ```output
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            `login-AzureRmAccount` cmdlet을 사용하여 로그인할 수도 있습니다. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d` 

            명령의 샘플 출력은 다음과 같습니다. 
         
            ```output
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```
---

Azure Resource Manager 연결에 문제가 있는 경우 지침은 [Azure Resource Manager 문제 해결](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)을 참조하세요. 

> [!IMPORTANT]
> Azure Resource Manager에 대한 연결은 1시간 30분마다 또는 Azure Stack Edge Pro 디바이스를 다시 시작하는 경우 만료됩니다. 이런 경우 실행 하는 모든 cmdlet은 Azure에 더 이상 연결되지 않는다는 오류 메시지를 반환합니다. 다시 로그인해야 합니다.

## <a name="switch-environments"></a>환경 전환

두 환경 간을 전환해야 할 수 있습니다.

### <a name="az"></a>[Az](#tab/Az)

`Disconnect-AzAccount` 명령을 실행하여 다른 `AzEnvironment`로 전환합니다. `Disconnect-AzAccount`를 사용하지 않고 `Set-AzEnvironment` 및 `Login-AzAccount`를 사용하는 경우 환경은 실제로 전환되지 않습니다.  

다음 예에서는 두 환경(`AzASE1` 및 `AzASE2`) 간에 전환하는 방법을 보여 줍니다.

먼저 클라이언트의 기존 환경을 모두 나열합니다.


```output
PS C:\WINDOWS\system32> Get-AzEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

다음으로 Azure Resource Manager를 통해 현재 연결되어 있는 환경을 가져옵니다.

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

이제 다른 환경으로 전환하기 전에 현재 환경과의 연결을 끊어야 합니다.


```output
PS C:\WINDOWS\system32> Disconnect-AzAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

다른 환경에 로그인합니다. 샘플 출력은 다음과 같습니다.

```output
PS C:\WINDOWS\system32> Login-AzAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

이 cmdlet을 실행하여 연결된 환경을 확인할 수 있습니다.

```output
PS C:\WINDOWS\system32> Get-AzContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
이제 원하는 환경으로 전환했습니다.

### <a name="azurerm"></a>[AzureRM](#tab/AzureRM)

`Disconnect-AzureRmAccount` 명령을 실행하여 다른 `AzureRmEnvironment`로 전환합니다. `Disconnect-AzureRmAccount`를 사용하지 않고 `Set-AzureRmEnvironment` 및 `Login-AzureRmAccount`를 사용하는 경우 환경은 실제로 전환되지 않습니다.  

다음 예에서는 두 환경(`AzDBE1` 및 `AzDBE2`) 간에 전환하는 방법을 보여 줍니다.

먼저 클라이언트의 기존 환경을 모두 나열합니다.


```output
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

다음으로 Azure Resource Manager를 통해 현재 연결되어 있는 환경을 가져옵니다.

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

이제 다른 환경으로 전환하기 전에 현재 환경과의 연결을 끊어야 합니다.


```output
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, ...], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

다른 환경에 로그인합니다. 샘플 출력은 다음과 같습니다.

```output
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

이 cmdlet을 실행하여 연결된 환경을 확인할 수 있습니다.

```output
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (...) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : ...
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
---

이제 원하는 환경으로 전환했습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager 문제를 해결](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)합니다.
- [Azure Stack Edge Pro 디바이스에 VM을 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)합니다.
