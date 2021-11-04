---
title: Azure 가상 데스크톱 (클래식) (미리 보기)에서 자동으로 마이그레이션-Azure
description: 마이그레이션 모듈을 사용 하 여 Azure 가상 데스크톱 (클래식)에서 Azure 가상 데스크톱으로 자동으로 마이그레이션하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6d5fed6547a32382413c1a128b1a9003bf099c88
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471766"
---
# <a name="migrate-automatically-from-azure-virtual-desktop-classic-preview"></a>Azure 가상 데스크톱 (클래식) (미리 보기)에서 자동으로 마이그레이션

> [!IMPORTANT]
> Azure 가상 데스크톱에 대 한 마이그레이션 모듈 도구는 현재 공개 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

마이그레이션 모듈 도구 (미리 보기)를 사용 하면 Azure 가상 데스크톱 (클래식)에서 Azure 가상 데스크톱으로 자동으로 조직을 마이그레이션할 수 있습니다. 이 문서에서는이 도구를 사용 하는 방법을 보여 줍니다. 

## <a name="requirements"></a>요구 사항

마이그레이션 모듈을 사용 하기 전에 다음 항목을 준비 해야 합니다.

- Azure 구독-새 Azure 서비스 개체를 만듭니다.

- 구독에서 Azure 개체를 만들고 사용자에 게 응용 프로그램 그룹에 사용자를 할당 하려면 사용자 액세스 관리자 역할을 할당 하려면 참가자 역할이 할당 되어야 합니다.

- RDS 테 넌 트 또는 마이그레이션하려는 특정 호스트 풀에 대 한 원격 데스크톱 서비스 (RDS) 참가자 권한이 필요 합니다.

- 최신 버전의 RdInfra PowerShell 모듈 

- 최신 버전의 Az Virtualization PowerShell module 

- 최신 버전의 Az. .Resources PowerShell 모듈 

- 컴퓨터에 마이그레이션 모듈 설치

- PowerShell 또는 PowerShell ISE를 실행 하 여이 문서에 표시 되는 스크립트를 실행 합니다. RdInfra 모듈은 PowerShell Core에서 작동 하지 않습니다.

>[!IMPORTANT]
>마이그레이션은 US 지리에 서비스 개체만 만듭니다. 서비스 개체를 다른 지리로 마이그레이션하려는 경우에는 작동 하지 않습니다. 또한 Azure 가상 데스크톱 (클래식) 배포에 200 개 이상의 앱 그룹이 있는 경우에는 마이그레이션할 수 없습니다. Azure Active Directory (Azure AD) 테 넌 트 내의 앱 그룹 수를 줄이기 위해 환경을 다시 빌드하는 경우에만 마이그레이션할 수 있습니다.

## <a name="prepare-your-powershell-environment"></a>PowerShell 환경 준비

먼저 마이그레이션 프로세스를 위해 PowerShell 환경을 준비 해야 합니다.

PowerShell 환경을 준비 하려면:

1. 시작 하기 전에 다음 cmdlet을 실행 하 여 최신 버전의 Az. Desktop Virtualization 및 Az .Resources 모듈이 있는지 확인 합니다.

    ```powershell
    Get-Module Az.Resources
    Get-Module Az.DesktopVirtualization
    https://www.powershellgallery.com/packages/Az.DesktopVirtualization/
    https://www.powershellgallery.com/packages/Az.Resources/
    ```

    그렇지 않은 경우 다음 cmdlet을 실행 하 여 모듈을 설치 하 고 가져옵니다.

    ```powershell
    Install-module Az.Resources
    Import-module Az.Resources
    Install-module Az.DesktopVirtualization
    Import-module Az.DesktopVirtualization
    ```

2. 다음으로,이 cmdlet을 실행 하 여 현재 RDInfra PowerShell 모듈을 제거 합니다.

    ```powershell
    Uninstall-Module -Name Microsoft.RDInfra.RDPowershell -AllVersions
    ```

3. 그런 다음이 cmdlet을 사용 하 여 RDPowershell 모듈을 설치 합니다.

    ```powershell
    Install-Module -Name Microsoft.RDInfra.RDPowershell -RequiredVersion 1.0.3414.0 -force
    Import-module Microsoft.RDInfra.RDPowershell
    ```

4. 모든 항목 설치가 완료 되 면이 cmdlet을 실행 하 여 모듈의 올바른 버전이 있는지 확인 합니다.

    ```powershell
    Get-Module Microsoft.RDInfra.RDPowershell
    ```

5. 이제 다음 cmdlet을 실행 하 여 마이그레이션 모듈을 설치 하 고 가져옵니다.

    ```powershell
    Install-Module -Name PackageManagement -Repository PSGallery -Force
    Install-Module -Name PowerShellGet -Repository PSGallery -Force
    # Then restart shell
    Install-Module -Name Microsoft.RdInfra.RDPowershell.Migration -RequiredVersion 1.0.3725-Prerelease -AllowPrerelease -AllowClobber
    Import-Module <Full path to the location of the migration module>\Microsoft.RdInfra.RDPowershell.Migration.psd1
    ```

6. 완료 되 면 PowerShell 창에서 Windows 가상 데스크톱 (클래식)에 로그인 합니다.

    ```powershell
    Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
    ```

7. Azure Resource Manager에 로그인 합니다.

    ```powershell
    Login-AzAccount
    ```

8. 여러 구독이 있는 경우이 cmdlet을 사용 하 여 리소스를 마이그레이션할 항목을 선택 합니다.

    ```powershell
    Select-AzSubscription -Subscriptionid <subID>
    ```

9. 선택한 구독에 대 한 Azure Portal에 리소스 공급자를 등록 합니다.

10. 마지막으로 공급자를 등록 해야 합니다. 다음 두 가지 방법으로 이 작업을 수행할 수 있습니다.
    - PowerShell을 사용 하려면이 cmdlet을 실행 합니다.
       
       ```powershell
       Register-AzResourceProvider -ProviderNamespace Microsoft.DesktopVirtualization
       ```
    
    - Azure Portal를 사용 하는 경우에는를 열고 Azure Portal에 로그인 한 다음 **구독** 으로 이동 하 여 사용 하려는 구독 이름을 선택 합니다. 그런 다음 **리소스 공급자**  >  **Microsoft desktopvirtualization** 로 이동 하 여 **다시 등록** 을 선택 합니다. UI에서 아직 어떠한 변경 내용도 표시 되지 않지만 이제 PowerShell 환경에서 모듈을 실행할 준비가 되었습니다.

## <a name="migrate-azure-virtual-desktop-classic-resources-to-azure-resource-manager"></a>Azure 가상 데스크톱 (클래식) 리소스를 Azure Resource Manager로 마이그레이션

이제 PowerShell 환경이 준비 되었으므로 마이그레이션 프로세스를 시작할 수 있습니다.

Azure 가상 데스크톱 (클래식) 리소스를 Azure Resource Manager로 마이그레이션하려면 다음을 수행 합니다.

1. 마이그레이션하기 전에 기존 클래식 리소스가 새 Azure Resource Manager 리소스에 매핑되는 방식을 이해 하려면이 cmdlet을 실행 합니다.
    
    ```powershell
    Get-RdsHostPoolMigrationMapping
    ```

    **RdsHostPoolMigrationMapping** 를 사용 하 여 리소스를 이동할 위치를 매핑하는 CSV 파일을 만들 수 있습니다. 예를 들어 테 넌 트 이름이 "Contoso" 인 경우 "contosouser" 파일에 매핑 파일을 저장 하려면 다음과 같은 cmdlet을 실행 합니다.

    ```powershell
    Get-RdsHostPoolMigrationMapping -Tenant Contoso -HostPool Office -Location EastUS -OutputFile 'C:\\Users\contosouser\OneDrive - Microsoft\Desktop\mapping.csv'
    ```

2. 그런 다음 **시작-RdsHostPoolMigration** cmdlet을 실행 하 여 테 넌 트 내의 단일 호스트 풀 또는 모든 호스트 풀을 마이그레이션할지 선택 합니다.

   예를 들어:

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -Location WestUS
   ```

   리소스를 특정 호스트 풀로 마이그레이션하려면 호스트 풀 이름을 포함 합니다. 예를 들어 "Office" 이라는 호스트 풀을 이동 하려면 다음과 같이 명령을 실행 합니다.

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments $false -Location EastUS
   ```

   작업 영역 이름을 지정 하지 않으면 모듈은 테 넌 트 이름을 기반으로 자동으로 하나를 만듭니다. 그러나 특정 작업 영역을 사용 하려는 경우 다음과 같이 해당 리소스 ID를 입력할 수 있습니다.

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments -Location EastUS -Workspace <Resource ID of workspacename>
   ```
    
   특정 작업 영역을 사용 하지만 해당 리소스 ID를 모르는 경우 다음 cmdlet을 실행 합니다.

   ```powershell
   Get-AzWvdWorkspace -WorkspaceName <workspace> -ResourceGroupName <resource group> |fl
   ```

   또한 기존 사용자 할당에 대 한 사용자 할당 모드를 지정 해야 합니다.

      - **Copy** 를 사용 하 여 이전 앱 그룹의 모든 사용자 할당을 Azure Resource Manager 응용 프로그램 그룹에 복사 합니다. 사용자는 두 버전의 클라이언트에 대 한 피드를 볼 수 있습니다.
      - 사용자 할당을 변경 하지 않으려면 **없음** 을 사용 합니다. 나중에 Azure Portal, PowerShell 또는 API를 사용 하 여 사용자 또는 사용자 그룹을 앱 그룹에 할당할 수 있습니다. 사용자는 Azure 가상 데스크톱 (클래식) 클라이언트를 사용 하는 피드만 볼 수 있습니다.

   구독 당 2000 사용자 할당만 복사할 수 있으므로 제한은 구독에 이미 있는 할당 수에 따라 달라 집니다. 모듈은 이미 보유 한 할당 수에 따라 제한을 계산 합니다. 복사할 충분 한 할당이 없는 경우 "사용자 할당을 복사 하는 데 충분 한 역할 할당 할당량이 없습니다." 라는 오류 메시지가 표시 됩니다. 마이그레이션하기 위해-CopyUserAssignments 스위치 없이 명령을 다시 실행 하십시오. "

3. 명령을 실행 한 후 모듈에서 서비스 개체를 만드는 데 최대 15 분이 소요 됩니다. 사용자 할당을 복사 하거나 이동한 경우 모듈에서 모든 항목 설정을 완료 하는 데 소요 되는 시간에 추가 됩니다.

   **시작-RdsHostPoolMigration** cmdlet이 완료 되 면 다음과 같은 항목이 표시 됩니다.

      - 지정 된 테 넌 트 또는 호스트 풀에 대 한 Azure 서비스 개체

      - 두 개의 새 리소스 그룹:

         - 작업 영역을 포함 하는 "Tenantname" 이라는 리소스 그룹입니다.

         - 호스트 풀 및 데스크톱 앱 그룹을 포함 하는 "Tenantname_originalHostPoolName" 이라는 리소스 그룹입니다.

      - 새로 만든 앱 그룹에 게시 한 모든 사용자입니다.

      - 가상 컴퓨터는 마이그레이션 프로세스 중 사용자 가동 중지 시간을 방지 하기 위해 기존 및 새 호스트 풀 둘 다에서 사용할 수 있습니다. 이렇게 하면 사용자가 동일한 사용자 세션에 연결할 수 있습니다.

   이러한 새 Azure 서비스 개체는 개체 Azure Resource Manager 이므로 모듈에서 RBAC (역할 기반 Access Control) 권한 또는 진단 설정을 설정할 수 없습니다. 따라서 이러한 개체에 대 한 RBAC 권한 및 설정은 수동으로 업데이트 해야 합니다.

   모듈에서 초기 사용자 연결의 유효성을 검사 한 후에는 더 많은 사용자 또는 사용자 그룹에 앱 그룹을 게시할 수도 있습니다 (원하는 경우).

   >[!NOTE]
   >마이그레이션 후 사용자에 게 사용 권한을 할당 한 후 앱 그룹을 다른 리소스 그룹으로 이동 하면 모든 RBAC 역할이 제거 됩니다. 사용자 RBAC 권한을 다시 다시 할당 해야 합니다.

4. 모든 Azure 가상 데스크톱 (클래식) 서비스 개체를 삭제 하려면 **완전 한 RdsHostPoolMigration** 을 실행 하 여 마이그레이션 프로세스를 완료 합니다. 이 cmdlet은 새 Azure 개체만 남기고 모든 Azure 가상 데스크톱 (클래식) 개체를 삭제 합니다. 사용자는 자신의 클라이언트에서 새로 만든 앱 그룹의 피드만 볼 수 있습니다. 이 명령이 완료 되 면 Azure 가상 데스크톱 (클래식) 테 넌 트를 안전 하 게 삭제 하 여 프로세스를 완료할 수 있습니다.

   예를 들어:

   ```powershell
   Complete-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   특정 호스트 풀을 완료 하려면 cmdlet에 호스트 풀 이름을 포함 하면 됩니다. 예를 들어 "Office" 이라는 호스트 풀을 완료 하려면 다음과 같은 명령을 사용 합니다.

    ```powershell
    Complete-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
    ```

    Azure 가상 데스크톱 (클래식)에서 만든 모든 서비스 개체를 삭제 합니다. 새 Azure 개체만으로 유지 되 고 사용자는 자신의 클라이언트에서 새로 만든 앱 그룹의 피드만 볼 수 있습니다. 마이그레이션이 완료 되 면 Azure 가상 데스크톱 (클래식)에서 테 넌 트를 명시적으로 삭제 해야 합니다.

5. 마이그레이션에 대 한 점을 변경 하 고 프로세스를 되돌리려면 **revert-RdsHostPoolMigration** cmdlet을 실행 합니다.
    
   예를 들어:

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   특정 호스트 풀을 되돌리려면 명령에 호스트 풀 이름을 포함 하면 됩니다. 예를 들어 "Office" 이라는 호스트 풀을 되돌리려면 다음과 같이 입력 합니다.

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
   ```

   이 cmdlet은 새로 만든 모든 Azure 서비스 개체를 삭제 합니다. 사용자는 자신의 클라이언트에서 Azure 가상 데스크톱 (클래식) 개체에 대 한 피드만 볼 수 있습니다.

   그러나 cmdlet은 모듈이 생성 된 작업 영역 또는 연결 된 리소스 그룹을 삭제 하지 않습니다. 이러한 항목을 제거 하려면 해당 항목을 수동으로 삭제 해야 합니다.

6. Azure 가상 데스크톱 (클래식) 서비스 개체를 아직 삭제 하지 않고 마이그레이션을 테스트 하려는 경우에는 **Set RdsHostPoolHidden** 을 실행할 수 있습니다.

    예를 들어:

    ```powershell
    Set-RdsHostPoolHidden -Tenant Contoso -Hostpool Office -Hidden $true -Location WestUS
    ```

    상태를 "true"로 설정 하면 Azure 가상 데스크톱 (클래식) 리소스가 숨겨집니다. "False"로 설정 하면 사용자에 게 리소스를 표시 합니다.

    *-Hostpool* 매개 변수는 선택 사항입니다. 숨기려는 특정 Azure 가상 데스크톱 (클래식) 호스트 풀이 있는 경우이 매개 변수를 사용할 수 있습니다.

    이 cmdlet은 Azure 가상 데스크톱 (클래식) 사용자 피드 및 서비스 개체를 삭제 하는 대신 숨깁니다. 그러나이는 일반적으로 테스트에만 사용 되며 마이그레이션 완료로 계산 되지 않습니다. 마이그레이션을 완료 하려면 **전체-RdsHostPoolMigration** 명령을 실행 해야 합니다. 그렇지 않은 경우 **revert-RdsHostPoolMigration** 을 실행 하 여 배포를 되돌립니다.

## <a name="troubleshoot-automatic-migration"></a>자동 마이그레이션 문제 해결

이 섹션에서는 마이그레이션 모듈에서 자주 발생 하는 문제를 해결 하는 방법을 설명 합니다.

### <a name="i-cant-access-the-tenant"></a>테 넌 트에 액세스할 수 없습니다.

먼저 다음과 같은 두 가지 작업을 수행 합니다.

- 관리자 계정에 테 넌 트에 액세스 하는 데 필요한 권한이 있는지 확인 합니다.
- 테 넌 트에서 **RdsTenant** 을 실행 해 보세요.

이러한 두 가지 작업을 수행 하는 경우 **RdsMigrationContext** cmdlet을 실행 하 여 마이그레이션에 대 한 RDS 컨텍스트 및 ADAL 컨텍스트를 설정 합니다.

1. **RdsAccount** cmdlet을 실행 하 여 RDS 컨텍스트를 만듭니다.       

2. *$RdMgmtContext* 전역 변수에서 RDS 컨텍스트를 찾습니다.         

3. *$AdalContext* 전역 변수에서 ADAL 컨텍스트를 찾습니다.

4. 다음 형식으로 찾은 변수를 사용 하 여 **RdsMigrationContext** 를 실행 합니다.

   ```powershell
   Set-RdsMigrationContext -RdsContext <rdscontext> -AdalContext <adalcontext>
   ```

## <a name="next-steps"></a>다음 단계

배포를 수동으로 마이그레이션하는 방법을 알아보려면 [Azure 가상 데스크톱에서 수동으로 마이그레이션 (클래식)](manual-migration.md)을 참조 하세요.

마이그레이션한 후에 [자습서](create-host-pools-azure-marketplace.md)를 확인하여 Azure Virtual Desktop이 어떻게 작동하는지 알아봅니다. [기존 호스트 풀 확장](expand-existing-host-pool.md) 및 [RDP 속성 사용자 지정](customize-rdp-properties.md)에서 고급 관리 기능에 대해 알아봅니다.

서비스 개체에 대해 자세히 알아보려면 [Azure Virtual Desktop 환경](environment-setup.md)을 확인합니다.
