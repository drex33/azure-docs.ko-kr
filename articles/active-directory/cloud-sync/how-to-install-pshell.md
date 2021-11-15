---
title: CLI(명령줄 인터페이스) 및 PowerShell을 사용하여 Azure AD Connect 클라우드 프로비저닝 에이전트 설치
description: PowerShell cmdlet을 사용하여 Azure AD Connect 클라우드 프로비저닝 에이전트를 설치하는 방법을 알아봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9587c60cc37badc29a9a9f3ba80a77f6f193a3a9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583048"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-by-using-a-cli-and-powershell"></a>CLI 및 PowerShell을 사용하여 Azure AD Connect 프로비저닝 에이전트 설치
이 문서에서는 PowerShell cmdlet을 사용하여 Azure AD(Azure Active Directory) Connect 프로비저닝 에이전트를 설치하는 방법을 보여줍니다.
 
>[!NOTE]
>이 문서에서는 CLI(명령줄 인터페이스)를 사용하여 프로비저닝 에이전트를 설치하는 방법을 다룹니다. 마법사를 사용하여 Azure AD Connect 프로비저닝 에이전트를 설치하는 방법에 대한 자세한 내용은 [Azure AD Connect 프로비저닝 에이전트 설치](how-to-install.md)를 참조하세요.

## <a name="prerequisite"></a>필수 조건

PowerShell cmdlet을 사용하여 Azure AD Connect 프로비저닝 에이전트를 설치하기 전에 Windows 서버에 TLS 1.2를 사용하도록 설정해야 합니다. TLS 1.2를 사용하도록 설정하려면 [Azure AD Connect 클라우드 동기화에 대한 필수 구성 요소](how-to-prerequisites.md#tls-requirements)의 단계를 따릅니다.

>[!IMPORTANT]
>다음 설치 지침에서는 모든 [필수 구성 요소](how-to-prerequisites.md)가 충족되었다고 가정합니다.

## <a name="install-the-azure-ad-connect-provisioning-agent-by-using-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 Azure AD Connect 프로비저닝 에이전트 설치 

 1. Azure Portal에 로그인한 다음 **Azure Active Directory** 로 이동합니다.
 1. 왼쪽 메뉴에서 **Azure AD Connect** 를 선택합니다.
 1. **프로비저닝 관리** > **모든 에이전트 검토** 를 선택합니다.
 1. Azure Portal에서 Azure AD Connect 프로비저닝 에이전트를 다운로드합니다.

    ![온-프레미스 에이전트 다운로드를 보여주는 스크린샷.](media/how-to-install/install-9.png)</br>

 1. 이러한 지침의 목적에 따라 에이전트는 C:\temp 폴더에 다운로드되었습니다. 
 1. 자동 모드에서 ProvisioningAgent를 설치합니다.

       ```
       $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
       $installerProcess.WaitForExit()  
       ```
 1. 프로비저닝 에이전트 PS 모듈을 가져옵니다.

       ```
       Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.PowerShell.dll" 
       ```
 1. 전역 관리자 자격 증명을 사용하여 Azure AD에 연결합니다. 이 섹션을 사용자 지정하여 보안 저장소에서 암호를 가져올 수 있습니다. 

       ```
       $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 
    
       $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
       
       Connect-AADCloudSyncAzureAD -Credential $globalAdminCreds 
       ```
 1. gMSA 계정을 추가하고 도메인 관리자의 자격 증명을 제공하여 기본 gMSA 계정을 만듭니다.
 
       ```
       $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 
    
       Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
       ```
 1. 또는 이전 cmdlet을 사용하여 미리 만든 gMSA 계정을 제공합니다.
 
       ```
       Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
       ```
 1. 도메인을 추가합니다.

       ```
       $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
       ```
 1. 또는 이전 cmdlet을 사용하여 기본 설정 도메인 컨트롤러를 구성합니다.

       ```
       $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
       ```
 1. 이전 단계를 반복하여 도메인을 더 추가합니다. 각 도메인의 계정 이름과 도메인 이름을 제공합니다.
 
 1. 서비스를 다시 시작합니다.
 
       ```
       Restart-Service -Name AADConnectProvisioningAgent  
       ```
 1. Azure Portal로 이동하여 클라우드 동기화 구성을 만듭니다.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>프로비저닝 에이전트 gMSA PowerShell cmdlet
이제 에이전트를 설치했으므로 gMSA에 더 세부적인 사용 권한을 적용할 수 있습니다. 권한을 구성하는 방법에 대한 자세한 내용 및 단계별 지침은 [Azure AD Connect 클라우드 프로비저닝 에이전트 gMSA PowerShell cmdlet](how-to-gmsa-cmdlets.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝 에이전트 gMSA PowerShell cmdlets](how-to-gmsa-cmdlets.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
