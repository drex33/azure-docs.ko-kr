---
title: Azure Virtual Desktop(클래식)(미리 보기)에서 자동으로 마이그레이션 - Azure
description: 마이그레이션 모듈을 사용하여 Azure Virtual Desktop(클래식)에서 Azure Virtual Desktop으로 자동으로 마이그레이션하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ec3ba6a71ea1430dc060d431776a31bc6c5e1b81
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547507"
---
# <a name="migrate-automatically-from-azure-virtual-desktop-classic-preview"></a>Azure Virtual Desktop(클래식)에서 자동으로 마이그레이션(미리 보기)

> [!IMPORTANT]
> Azure Virtual Desktop용 마이그레이션 모듈 도구는 현재 공개 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

마이그레이션 모듈 도구(미리 보기)를 사용하면 Azure Virtual Desktop(클래식)에서 Azure Virtual Desktop으로 조직을 자동으로 마이그레이션할 수 있습니다. 이 문서에서는 도구를 사용하는 방법을 보여줍니다. 

## <a name="requirements"></a>요구 사항

마이그레이션 모듈을 사용하기 전에 다음 사항을 준비해야 합니다.

- 새 Azure 서비스 개체를 만들 Azure 구독

- 구독에서 Azure 개체를 만들려면 기여자 역할이 할당되고, 애플리케이션 그룹에 사용자를 할당하려면 사용자 액세스 관리자 역할이 할당되어야 합니다.

- RDS 테넌트 또는 마이그레이션하는 특정 호스트 풀에 대한 최소 원격 데스크톱 서비스(RDS) 기여자 권한

- 최신 버전의 Microsoft.RdInfra.RDPowershell PowerShell 모듈 

- 최신 버전의 Az.DesktopVirtualization PowerShell 모듈 

- 최신 버전의 Az.Resources PowerShell 모듈 

- 컴퓨터에 마이그레이션 모듈 설치

- PowerShell 또는 PowerShell ISE를 사용하여 이 문서에 표시되는 스크립트를 실행합니다. Microsoft.RdInfra.RDPowershell 모듈은 PowerShell Core 작동하지 않습니다.

>[!IMPORTANT]
>마이그레이션은 미국 지역에만 서비스 개체를 만듭니다. 서비스 개체를 다른 지리적으로 마이그레이션하려고 하면 작동하지 않습니다. 또한 Azure Virtual Desktop(클래식) 배포에 200개 이상의 앱 그룹이 있는 경우 마이그레이션할 수 없습니다. Azure AD(Azure Active Directory) 테넌트 내에서 앱 그룹 수를 줄이기 위해 환경을 다시 빌드하는 경우에만 마이그레이션할 수 있습니다.

## <a name="prepare-your-powershell-environment"></a>PowerShell 환경 준비

먼저 마이그레이션 프로세스를 위해 PowerShell 환경을 준비해야 합니다.

PowerShell 환경을 준비하려면 다음을 수행합니다.

1. 시작하기 전에 다음 cmdlet을 실행하여 최신 버전의 Az.Desktop Virtualization 및 Az.Resources 모듈이 있는지 확인합니다.

    ```powershell
    Get-Module Az.Resources
    Get-Module Az.DesktopVirtualization
    https://www.powershellgallery.com/packages/Az.DesktopVirtualization/
    https://www.powershellgallery.com/packages/Az.Resources/
    ```

    그렇지 않으면 다음 cmdlet을 실행하여 모듈을 설치하고 가져옵니다.

    ```powershell
    Install-module Az.Resources
    Import-module Az.Resources
    Install-module Az.DesktopVirtualization
    Import-module Az.DesktopVirtualization
    ```

2. 다음으로, 다음 cmdlet을 실행하여 현재 RDInfra PowerShell 모듈을 제거합니다.

    ```powershell
    Uninstall-Module -Name Microsoft.RDInfra.RDPowershell -AllVersions
    ```

3. 그런 다음 이 cmdlet을 통해 RDPowershell 모듈을 설치합니다.

    ```powershell
    Install-Module -Name Microsoft.RDInfra.RDPowershell -RequiredVersion 1.0.3414.0 -force
    Import-module Microsoft.RDInfra.RDPowershell
    ```

4. 모든 설치가 완료되면 이 cmdlet을 실행하여 올바른 버전의 모듈이 있는지 확인합니다.

    ```powershell
    Get-Module Microsoft.RDInfra.RDPowershell
    ```

5. 이제 다음 cmdlet을 실행하여 마이그레이션 모듈을 설치하고 가져오겠습니다.

    ```powershell
    Install-Module -Name PackageManagement -Repository PSGallery -Force
    Install-Module -Name PowerShellGet -Repository PSGallery -Force
    # Then restart shell
    Install-Module -Name Microsoft.RdInfra.RDPowershell.Migration -RequiredVersion 1.0.3725-Prerelease -AllowPrerelease -AllowClobber
    Import-Module <Full path to the location of the migration module>\Microsoft.RdInfra.RDPowershell.Migration.psd1
    ```

6. 완료되면 PowerShell 창에서 Windows Virtual Desktop(클래식)에 로그인합니다.

    ```powershell
    Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
    ```

7. Azure Resource Manager 로그인합니다.

    ```powershell
    Login-AzAccount
    ```

8. 구독이 여러 개 있는 경우 이 cmdlet을 사용하여 리소스를 마이그레이션할 구독을 선택합니다.

    ```powershell
    Select-AzSubscription -Subscriptionid <subID>
    ```

9. 선택한 구독에 대해 Azure Portal 리소스 공급자를 등록합니다.

10. 마지막으로 공급자를 등록해야 합니다. 다음 두 가지 방법으로 이 작업을 수행할 수 있습니다.
    - PowerShell을 사용하려면 다음 cmdlet을 실행합니다.
       
       ```powershell
       Register-AzResourceProvider -ProviderNamespace Microsoft.DesktopVirtualization
       ```
    
    - Azure Portal 사용하려는 경우 를 열고 Azure Portal 로그인한 다음 **구독으로** 이동하여 사용할 구독의 이름을 선택합니다. 그런 **다음, 리소스 공급자**  >  **Microsoft.Desktop가상화로** 이동하여 다시 **등록을** 선택합니다. UI에서 변경된 내용이 아직 표시되지 않지만 이제 PowerShell 환경에서 모듈을 실행할 준비가 되었습니다.

## <a name="migrate-azure-virtual-desktop-classic-resources-to-azure-resource-manager"></a>Azure Virtual Desktop(클래식) 리소스를 Azure Resource Manager 마이그레이션

PowerShell 환경이 준비되면 마이그레이션 프로세스를 시작할 수 있습니다.

Azure Virtual Desktop(클래식) 리소스를 Azure Resource Manager 마이그레이션하려면 다음을 수행합니다.

1. 마이그레이션하기 전에 기존 클래식 리소스가 새 Azure Resource Manager 리소스에 매핑될 방법을 이해하려면 다음 cmdlet을 실행합니다.
    
    ```powershell
    Get-RdsHostPoolMigrationMapping
    ```

    **Get-RdsHostPoolMigrationMapping** 을 사용하면 리소스가 이동하는 위치를 매핑하는 CSV 파일을 만들 수 있습니다. 예를 들어 테넌트의 이름이 "Contoso"이고 매핑 파일을 "contosouser" 파일에 저장하려는 경우 다음과 같은 cmdlet을 실행합니다.

    ```powershell
    Get-RdsHostPoolMigrationMapping -Tenant Contoso -HostPool Office -Location EastUS -OutputFile 'C:\\Users\contosouser\OneDrive - Microsoft\Desktop\mapping.csv'
    ```

2. 다음으로 **Start-RdsHostPoolMigration** cmdlet을 실행하여 단일 호스트 풀을 마이그레이션할지 아니면 테넌트 내의 모든 호스트 풀을 마이그레이션할지 선택합니다.

    예:

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -Location WestUS
    ```

    리소스를 특정 호스트 풀로 마이그레이션하려면 호스트 풀 이름을 포함합니다. 예를 들어 "Office"이라는 호스트 풀을 이동하려면 다음과 같은 명령을 실행합니다.

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments $false -Location EastUS
    ```

    작업 영역 이름을 지정하지 않으면 모듈에서 테넌트 이름에 따라 자동으로 이름을 만듭니다. 그러나 특정 작업 영역을 사용하려는 경우 다음과 같이 해당 리소스 ID를 입력할 수 있습니다.

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments -Location EastUS -Workspace <Resource ID of workspacename>
    ```
    
    특정 작업 영역을 사용하지만 해당 리소스 ID를 모르는 경우 다음 cmdlet을 실행합니다.

    ```powershell
    Get-AzWvdWorkspace -WorkspaceName <workspace> -ResourceGroupName <resource group> |fl
    ```

  또한 기존 사용자 할당에 대한 사용자 할당 모드를 지정해야 합니다.

  - **복사를** 사용하여 이전 앱 그룹의 모든 사용자 할당을 Azure Resource Manager 애플리케이션 그룹에 복사합니다. 사용자는 두 버전의 클라이언트에 대한 피드를 볼 수 있습니다.
  - 사용자 할당을 변경하지 않으려면 **없음을** 사용합니다. 나중에 Azure Portal, PowerShell 또는 API를 사용하여 앱 그룹에 사용자 또는 사용자 그룹을 할당할 수 있습니다. 사용자는 Azure Virtual Desktop(클래식) 클라이언트를 사용하여 피드만 볼 수 있습니다.

  구독당 2,000개의 사용자 할당만 복사할 수 있으므로 한도는 구독에 이미 있는 할당 수에 따라 달라집니다. 모듈은 이미 할당된 횟수에 따라 제한을 계산합니다. 복사할 할당이 충분하지 않으면 "역할 할당 할당량이 부족하여 사용자 할당을 복사할 수 없습니다. -CopyUserAssignments 스위치 없이 명령을 다시 실행하여 마이그레이션합니다."

3. 명령을 실행하면 모듈에서 서비스 개체를 만드는 데 최대 15분이 걸립니다. 사용자 할당을 복사하거나 이동한 경우 모듈이 모든 설정을 완료하는 데 걸리는 시간이 추가됩니다.

   **Start-RdsHostPoolMigration** cmdlet이 완료되면 다음과 같은 내용이 표시됩니다.

      - 지정한 테넌트 또는 호스트 풀에 대한 Azure 서비스 개체

      - 두 개의 새 리소스 그룹:

         - 작업 영역을 포함하는 "Tenantname"이라는 리소스 그룹입니다.

         - 호스트 풀 및 데스크톱 앱 그룹을 포함하는 "Tenantname_originalHostPoolName"이라는 리소스 그룹입니다.

      - 새로 만든 앱 그룹에 게시한 모든 사용자입니다.

      - 마이그레이션 프로세스 중에 사용자 가동 중지 시간을 방지하기 위해 기존 호스트 풀과 새 호스트 풀 모두에서 가상 머신을 사용할 수 있습니다. 이렇게 하면 사용자가 동일한 사용자 세션에 연결할 수 있습니다.

   이러한 새 Azure 서비스 개체는 Azure Resource Manager 개체이므로 모듈은 RBAC(역할 기반 Access Control) 권한 또는 진단 설정을 설정할 수 없습니다. 따라서 이러한 개체에 대한 RBAC 권한 및 설정을 수동으로 업데이트해야 합니다.

   모듈이 초기 사용자 연결의 유효성을 검사하면 원하는 경우 더 많은 사용자 또는 사용자 그룹에 앱 그룹을 게시할 수도 있습니다.

   >[!NOTE]
   >마이그레이션 후 사용자에게 권한을 할당한 후 앱 그룹을 다른 리소스 그룹으로 이동하면 모든 RBAC 역할이 제거됩니다. 사용자 RBAC 권한을 다시 할당해야 합니다.

4. 모든 Azure Virtual Desktop(클래식) 서비스 개체를 삭제하려면 **Complete-RdsHostPoolMigration을** 실행하여 마이그레이션 프로세스를 완료합니다. 이 cmdlet은 모든 Azure Virtual Desktop(클래식) 개체를 삭제하고 새 Azure 개체만 남깁니다. 사용자는 클라이언트에서 새로 만든 앱 그룹에 대한 피드만 볼 수 있습니다. 이 명령이 완료되면 Azure Virtual Desktop(클래식) 테넌트에서 안전하게 삭제하여 프로세스를 완료할 수 있습니다.

   예:

   ```powershell
   Complete-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   특정 호스트 풀을 완료하려는 경우 cmdlet에 호스트 풀 이름을 포함할 수 있습니다. 예를 들어 "Office"이라는 호스트 풀을 완료하려면 다음과 같은 명령을 사용합니다.

    ```powershell
    Complete-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
    ```

    그러면 Azure Virtual Desktop(클래식)에서 만든 모든 서비스 개체가 삭제됩니다. 새 Azure 개체만 남게 되고 사용자는 클라이언트에서 새로 만든 앱 그룹에 대한 피드만 볼 수 있습니다. 마이그레이션을 완료한 후에는 Azure Virtual Desktop(클래식)에서 테넌트 를 명시적으로 삭제해야 합니다.

5. 마이그레이션에 대한 생각이 바뀌었고 프로세스를 되돌리려면 **Revert-RdsHostPoolMigration** cmdlet을 실행합니다.
    
   예:

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   특정 호스트 풀을 되돌리려면 명령에 호스트 풀 이름을 포함할 수 있습니다. 예를 들어 "Office"이라는 호스트 풀을 되돌리려면 다음과 같이 입력합니다.

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
   ```

   이 cmdlet은 새로 만든 모든 Azure 서비스 개체를 삭제합니다. 사용자는 클라이언트에서 Azure Virtual Desktop(클래식) 개체에 대한 피드만 볼 수 있습니다.

   그러나 cmdlet은 모듈에서 만든 작업 영역 또는 관련 리소스 그룹을 삭제하지 않습니다. 이러한 항목을 제거하려면 해당 항목을 수동으로 삭제해야 합니다.

6. Azure Virtual Desktop(클래식) 서비스 개체를 아직 삭제하지 않고 마이그레이션을 테스트하려는 경우 **Set-RdsHostPoolHidden** 을 실행할 수 있습니다.

    예:

    ```powershell
    Set-RdsHostPoolHidden -Tenant Contoso -Hostpool Office -Hidden $true -Location WestUS
    ```

    상태를 "true"로 설정하면 Azure Virtual Desktop(클래식) 리소스가 숨겨지게 됩니다. "False"로 설정 하면 사용자에 게 리소스를 표시 합니다.

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