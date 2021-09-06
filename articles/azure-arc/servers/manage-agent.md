---
title: Azure Arc 지원 서버 에이전트 관리
description: 이 문서에서는 Azure Arc 지원 서버 Connected Machine 에이전트의 수명 주기 동안 일반적으로 수행하는 다양한 관리 작업을 설명합니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 7905a3fd2bf02ceafcfda8cf574d2f99c1e01b26
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609604"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Connected Machine 에이전트 관리 및 유지 관리

Windows 또는 Linux용 Azure Arc 지원 서버 Connected Machine 에이전트의 초기 배포 이후 에이전트를 재구성 및 업그레이드하거나 컴퓨터에서 에이전트를 제거해야 할 수도 있습니다. 이러한 일상적인 유지 관리 작업을 수동으로 또는 자동화를 통해 간단히 관리하여 조작 오류와 비용을 모두 줄일 수 있습니다.

## <a name="before-uninstalling-agent"></a>에이전트를 제거하기 전에

Arc 지원 서버에서 Connected Machine 에이전트를 제거하기 전에 다음 사항을 고려하여 예기치 않은 문제나 Azure 청구서에 비용이 추가되지 않도록 합니다.

* Azure VM 확장을 지원 서버에 배포한 이후 Connected Machine 에이전트를 제거하거나 리소스 그룹에서 Arc 지원 서버를 나타내는 리소스를 삭제하는 경우, 해당 확장은 계속해서 실행되고 정상적으로 작업을 수행합니다.

* 리소스 그룹에서 Arc 지원 서버를 나타내는 리소스를 삭제했지만 VM 확장은 제거하지 않은 경우, 머신을 다시 등록할 때 설치된 VM 확장을 관리할 수 없습니다.

더 이상 Azure Arc 지원 서버로 관리하지 않으려는 서버 또는 머신의 경우, 다음 단계를 수행하여 관리를 중지해야 합니다.

1. 머신 또는 서버에서 VM 확장을 제거합니다. 자세한 내용은 아래에서 설명합니다.

2. 다음 메서드 중 하나를 사용하여 Azure Arc에서 머신의 연결을 끊습니다.

    * 머신 또는 서버에서 `azcmagent disconnect` 명령을 실행합니다.

    * Azure Portal에 등록된 Arc 지원 서버를 선택하고 상단 막대에서 **삭제** 를 선택합니다.

    * [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) 또는 [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource)을 사용합니다. `ResourceType` 매개 변수에 대해 `Microsoft.HybridCompute/machines`를 사용합니다.

3. 아래 단계를 수행하여 머신 또는 서버에서 [에이전트를 제거](#remove-the-agent)합니다.

## <a name="renaming-a-machine"></a>머신 이름 바꾸기

Azure Arc 지원 서버에 연결된 Linux 또는 Windows 머신의 이름을 변경해도 Azure의 리소스 이름은 변경되지 않으므로 새 이름이 자동으로 인식되지 않습니다. 다른 Azure 리소스와 마찬가지로 새 이름을 사용하기 위해서는 리소스를 삭제하고 다시 만들어야 합니다.

Arc 지원 서버의 경우 머신 이름을 바꾸기 전에 먼저 VM 확장을 제거한 후 계속해야 합니다.

> [!NOTE]
> 해당 프로시저가 완료된 이후 설치된 확장이 계속해서 실행되고 정상적으로 작업을 수행하는 동안에는 확장을 관리할 수 없습니다. 머신에 확장을 다시 배포하려고 하면 예기치 않은 동작이 발생할 수 있습니다.

> [!WARNING]
> 머신의 이름을 바꾸지 않고 반드시 필요한 경우에만 해당 프로시저를 수행하는 것이 좋습니다.

1. [Azure CLI](manage-vm-extensions-cli.md#list-extensions-installed) 또는 [Azure PowerShell](manage-vm-extensions-powershell.md#list-extensions-installed)을 사용하여 머신에 설치된 VM 확장을 감사하고 구성을 확인합니다.

2. [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension) 또는 [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)을 사용하여 [Azure Portal](manage-vm-extensions-portal.md#uninstall-extensions)에서 설치된 VM 확장을 제거합니다.

3. **azcmagent** 도구를 [연결 끊기](manage-agent.md#disconnect) 매개 변수와 함께 사용하여 Azure Arc에서 머신의 연결을 끊고 Azure에서 머신 리소스를 삭제합니다. Arc 지원 서버에서 머신 연결을 끊으면 Connected Machine 에이전트가 제거되지 않으므로, 이 프로세스의 일부로 에이전트를 제거할 필요는 없습니다. 대화형으로 로그온하는 동안 수동으로 azcmagent를 실행하거나, 여러 에이전트를 온보딩할 때 사용한 동일한 서비스 주체 또는 Microsoft ID 플랫폼 [액세스 토큰](../../active-directory/develop/access-tokens.md)을 사용하여 자동화합니다. 서비스 주체를 사용하여 Azure Arc 지원 서버에 머신을 등록하지 않은 경우 서비스 주체를 만들려면 다음 [문서](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)를 참조하세요.

4. 머신의 이름을 바꿉니다.

5. Connected Machine 에이전트를 Arc 지원 서버에 다시 등록합니다. [Connect](manage-agent.md#connect) 매개 변수와 함께 `azcmagent` 도구를 실행하여 해당 단계를 완료합니다.

6. 머신에 원래 배포되었던 VM 확장을 Arc 지원 서버에서 다시 배포합니다. Azure Policy 정의를 사용하여 VM용 Azure Monitor(인사이트) 에이전트 또는 Log Analytics 에이전트를 배포한 경우, 다음 [평가 주기](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) 후에 에이전트가 다시 배포됩니다.

## <a name="upgrading-agent"></a>에이전트 업그레이드

Azure Connected Machine 에이전트는 버그 수정, 안정성 향상 및 새로운 기능을 처리하기 위해 정기적으로 업데이트됩니다. [Azure Advisor](../../advisor/advisor-overview.md)는 최신 버전의 머신 에이전트를 사용하지 않는 리소스를 식별하여 최신 버전으로 업그레이드하도록 권장합니다. **개요** 페이지에 배너를 표시하거나 Azure Portal을 통해 Advisor에 액세스하여 Arc 지원 서버를 선택하면 사용자에게 관련 사실을 알립니다.

Windows 및 Linux용 Azure Connected Machine 에이전트는 요구 사항에 따라 수동 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다.

다음 표에서는 에이전트 업그레이드를 수행하는 데 지원되는 방법을 설명합니다.

| 운영 체제 | 업그레이드 방법 |
|------------------|----------------|
| Windows | 수동<br> Windows 업데이트 |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows 에이전트

Windows용 Connected Machine 에이전트에 대한 업데이트 패키지는 다음 위치에서 제공됩니다.

* Microsoft Update

* [Microsoft Update 카탈로그](https://www.catalog.update.microsoft.com/Home.aspx)

* Microsoft 다운로드 센터에서 [Windows 에이전트 Windows Installer 패키지](https://aka.ms/AzureConnectedMachineAgent)를 다운로드합니다.

소프트웨어 업데이트 관리 프로세스를 지원하는 다양한 메서드를 통해 에이전트를 업그레이드할 수 있습니다. Microsoft 업데이트에서 가져오는 것 외에 명령 프롬프트를 사용하거나, 스크립트 또는 기타 자동화 솔루션을 사용하거나, UI 마법사에서 `AzureConnectedMachine.msi`를 실행하여 수동으로 다운로드하고 실행할 수 있습니다.

> [!NOTE]
> * 에이전트를 업그레이드하려면 관리자 권한이 있어야 합니다.
> * 수동으로 업그레이드하려면 먼저 설치 관리자 패키지를 다운로드하여 대상 서버의 폴더 또는 공유 네트워크 폴더에 복사해야 합니다. 

Windows Installer 패키지에 대한 명령줄 옵션에 익숙하지 않은 경우 [Msiexec 표준 명령줄](/windows/win32/msi/standard-installer-command-line-options) 및 [Msiexec 명령줄 옵션](/windows/win32/msi/command-line-options)을 검토하세요.

#### <a name="to-upgrade-using-the-setup-wizard"></a>설치 마법사를 사용하여 업그레이드하려면

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **AzureConnectedMachineAgent.msi** 를 실행하여 설치 마법사를 시작합니다.

설치 마법사는 이전 버전이 있는지 검색한 다음 자동으로 에이전트 업그레이드를 수행합니다. 업그레이드가 완료되면 설치 마법사가 자동으로 닫힙니다.

#### <a name="to-upgrade-from-the-command-line"></a>명령줄에서 업그레이드하려면

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 에이전트를 자동으로 업그레이드하고 `C:\Support\Logs` 폴더에 설치 로그 파일을 만들려면 다음 명령을 실행합니다.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 에이전트

Linux 머신의 에이전트를 최신 버전으로 업데이트하려면 두 개의 명령이 필요합니다. 로컬 패키지 인덱스를 리포지토리에서 사용 가능한 최신 패키지 목록으로 업데이트하는 명령과 로컬 패키지를 업그레이드하는 명령입니다.

Microsoft [패키지 리포지토리](https://packages.microsoft.com/)에서 최신 에이전트 패키지를 다운로드할 수 있습니다.

> [!NOTE]
> 에이전트를 업그레이드하려면 루트 액세스 권한이 있거나 Sudo를 사용하는 승격된 권한이 있는 계정을 사용해야 합니다.

#### <a name="upgrade-ubuntu"></a>Ubuntu 업그레이드

1. 로컬 패키지 인덱스를 리포지토리의 최신 변경 내용으로 업데이트하려면 다음 명령을 실행합니다.

    ```bash
    apt update
    ```

2. 시스템을 업그레이드하려면 다음 명령을 실행합니다.

    ```bash
    apt upgrade
    ```

패키지 설치 및 제거와 같은 [apt](https://help.ubuntu.com/lts/serverguide/apt.html) 명령의 동작은 `/var/log/dpkg.log` 로그 파일에 기록됩니다.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Red Hat/CentOS/Amazon Linux 업그레이드

1. 로컬 패키지 인덱스를 리포지토리의 최신 변경 내용으로 업데이트하려면 다음 명령을 실행합니다.

    ```bash
    yum check-update
    ```

2. 시스템을 업그레이드하려면 다음 명령을 실행합니다.

    ```bash
    yum update
    ```

패키지 설치 및 제거와 같은 [yum](https://access.redhat.com/articles/yum-cheat-sheet) 명령의 동작은 `/var/log/yum.log` 로그 파일에 기록됩니다. 

#### <a name="upgrade-suse-linux-enterprise"></a>SUSE Linux Enterprise 업그레이드

1. 로컬 패키지 인덱스를 리포지토리의 최신 변경 내용으로 업데이트하려면 다음 명령을 실행합니다.

    ```bash
    zypper refresh
    ```

2. 시스템을 업그레이드하려면 다음 명령을 실행합니다.

    ```bash
    zypper update
    ```

패키지 설치 및 제거와 같은 [zypper](https://en.opensuse.org/Portal:Zypper) 명령의 동작은 `/var/log/zypper.log` 로그 파일에 기록됩니다.

## <a name="about-the-azcmagent-tool"></a>Azcmagent 도구 정보

Azcmagent 도구(Azcmagent.exe)는 설치 도중에 Azure Arc 지원 서버 Connected Machine 에이전트를 구성하거나, 설치 이후에 에이전트의 초기 구성을 수정하는 데 사용됩니다. Azcmagent.exe는 에이전트를 사용자 지정하고 해당 상태를 볼 수 있는 명령줄 매개 변수를 제공합니다.

* **Connect** - Azure Arc에 머신을 연결합니다.

* **Disconnect** - Azure Arc에서 머신의 연결을 끊습니다.

* **Show** - 에이전트와 관련된 문제를 해결할 때 도움이 될 수 있는 에이전트 상태 및 해당 구성 속성(리소스 그룹 이름, 구독 ID, 버전 등)을 표시합니다. `-j` 매개 변수를 포함하여 JSON 형식으로 결과를 출력합니다.

* **로그** - 현재 디렉터리에 문제 해결 중에 도움이 되는 로그를 포함하는 .zip 파일을 만듭니다.

* **버전** - Connected Machine 에이전트의 버전을 표시합니다.

* **-useStderr**-오류 및 자세한 출력을 stderr로 보냅니다. `-json` 매개 변수를 포함하여 JSON 형식으로 결과를 출력합니다.

* **-h 또는 --help** - 사용 가능한 명령줄 매개 변수를 표시합니다.

    예를 들어 **Connect** 매개 변수에 대한 도움말 세부 정보를 보려면 `azcmagent connect -h`를 입력합니다. 

* **-v 또는 --verbose** - 자세한 정보 로깅을 사용합니다.

대화형으로 로그온하는 동안 수동으로 **Connect** 및 **Disconnect** 를 수행할 수 있으며, 또는 여러 에이전트를 온보딩할 때 사용한 동일한 서비스 주체를 사용하거나 Microsoft ID 플랫폼 [액세스 토큰](../../active-directory/develop/access-tokens.md)을 사용하여 자동화할 수 있습니다. 서비스 주체를 사용하여 Azure Arc 지원 서버에 머신을 등록하지 않은 경우 서비스 주체를 만들려면 다음 [문서](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)를 참조하세요.

>[!NOTE]
>**azcmagent** 를 실행하려면 Linux 머신에 대한 *루트* 액세스 권한이 있어야 합니다.

### <a name="connect"></a>연결

이 매개 변수는 Azure Resource Manager의 리소스를 지정하여 Azure에서 머신이 만들어짐을 나타냅니다. 리소스는 지정된 구독 및 리소스 그룹에 있으며, 머신에 대한 데이터는 `--location` 설정에 지정된 Azure 지역에 저장됩니다. 이름을 지정하지 않으면 기본 리소스 이름은 머신의 호스트 이름으로 지정됩니다.

그런 다음 머신의 시스템 할당 ID에 해당하는 인증서를 다운로드하여 로컬로 저장합니다. 이 단계가 완료되면 Azure Connected Machine Metadata Service 및 게스트 구성 에이전트가 Azure Arc 지원 서버와 동기화를 시작합니다.

서비스 주체를 사용하여 연결하려면 다음 명령을 실행합니다.

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

액세스 토큰을 사용하여 연결하려면 다음 명령을 실행합니다.

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

관리자 권한으로 로그온한 자격 증명을 사용하여 연결하려면(대화형) 다음 명령을 실행합니다.

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>연결 끊기

이 매개 변수는 Azure Resource Manager의 리소스를 지정하여 머신이 Azure에서 삭제됨을 나타냅니다. 해당 과정은 머신에서 에이전트를 제거하지 않으며 에이전트는 별도로 제거해야 합니다. 머신의 연결이 끊긴 후에 Azure Arc 지원 서버에 다시 등록하려면, `azcmagent connect`를 사용하여 Azure에서 새 리소스를 만듭니다.

> [!NOTE]
> 하나 이상의 Azure VM 확장을 Arc 지원 서버에 배포하고 Azure에서 해당 등록을 삭제하는 경우, 확장은 여전히 설치되어 있습니다. 설치된 확장에 따라 확장이 함수를 적극적으로 수행한다는 것을 이해하는 것이 중요합니다. 사용을 중지했거나 Arc 지원 서버에서 더 이상 관리하지 않는 머신은 Azure에서 등록을 제거하기 전에 먼저 확장을 제거해야 합니다.

서비스 주체를 사용하여 연결을 끊으려면 다음 명령을 실행합니다.

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

액세스 토큰을 사용하여 연결을 끊으려면 다음 명령을 실행합니다.

`azcmagent disconnect --access-token <accessToken>`

관리자 권한으로 로그온한 자격 증명을 사용하여 연결을 끊으려면(대화형) 다음 명령을 실행합니다.

`azcmagent disconnect`

## <a name="remove-the-agent"></a>에이전트 제거

다음 방법 중 하나를 수행하여 머신에서 Windows 또는 Linux Connected Machine 에이전트를 제거합니다. 에이전트를 제거해도 Arc 지원 서버에서 머신의 등록이 취소되거나 설치된 Azure VM 확장이 제거되지 않습니다. 더 이상 Azure Arc 지원 서버로 관리하지 않으려는 서버 또는 머신의 경우, 다음 단계를 수행하여 관리를 중지해야 합니다. 

1. [Azure Portal](manage-vm-extensions-portal.md#uninstall-extensions)에서 설치된 VM 확장을 제거하거나 [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension)를 사용하거나 머신에 유지하지 않으려는 [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension)을 사용합니다.
1. `azcmagent disconnect`를 실행해 Azure에서 Arc 지원 서버 리소스를 삭제하여 머신을 등록 취소합니다. 이 작업이 실패하면 Azure에서 리소스를 수동으로 삭제할 수 있습니다. 그렇지 않고 리소스가 Azure에서 삭제된 경우 서버에서 `azcmagent disconnect --force-local-only`를 실행하여 로컬 구성을 제거해야 합니다.

### <a name="windows-agent"></a>Windows 에이전트

다음 메서드는 모두 에이전트를 제거하지만 머신에서 *C:\Program Files\AzureConnectedMachineAgent* 폴더를 제거하지는 않습니다.

#### <a name="uninstall-from-control-panel"></a>제어판에서 제거

1. 머신에서 Windows 에이전트를 제거하려면 다음을 수행합니다.

    a. 관리자 권한이 있는 계정으로 컴퓨터에 로그인합니다.  
    b. **제어판** 에서 **프로그램 및 기능** 을 선택합니다.  
    다. **프로그램 및 기능** 에서 **Azure Connected Machine 에이전트**,  **제거**, **예** 를 차례로 선택합니다.  

    >[!NOTE]
    > **AzureConnectedMachineAgent.msi** 설치 관리자 패키지를 두 번 클릭하여 에이전트 설정 마법사를 실행할 수도 있습니다.

#### <a name="uninstall-from-the-command-line"></a>명령줄에서 제거

명령 프롬프트에서 에이전트를 수동으로 제거하거나 스크립트와 같은 자동화된 방법을 사용하려면 다음 예제를 사용할 수 있습니다. 먼저 운영 체제에서 제품 코드(애플리케이션 패키지의 기본 식별자인 GUID)를 검색해야 합니다. 제거는 Msiexec.exe 명령줄 `msiexec /x {Product Code}`를 사용하여 수행됩니다.

1. 레지스트리 편집기를 엽니다.

2. `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 레지스트리 키 아래에서 제품 코드 GUID를 찾아 복사합니다.

3. 그리고 나서 다음 예제에 따라 Msiexec.exe를 사용하여 에이전트를 제거할 수 있습니다.

   * 명령줄에서 다음을 입력합니다.

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * PowerShell을 사용하여 동일한 단계를 수행할 수 있습니다.

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 에이전트

> [!NOTE]
> 에이전트를 제거하려면 루트 액세스 권한이 있거나 Sudo를 사용하는 승격된 권한이 있는 계정을 사용해야 합니다.

Linux 에이전트를 제거하려면 사용할 명령은 Linux 운영 체제에 따라 달라집니다.

- Ubuntu의 경우 다음 명령을 실행합니다.

    ```bash
    sudo apt purge azcmagent
    ```

- RHEL, CentOS 및 Amazon Linux의 경우 다음 명령을 실행합니다.

    ```bash
    sudo yum remove azcmagent
    ```

- SLES의 경우 다음 명령을 실행합니다.

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>머신 등록 취소

Azure에서 지원 서비스를 사용한 머신 관리를 중지하려는 경우, 다음 단계를 수행하여 Arc 지원 서버에서 머신을 등록 취소합니다. 머신에서 Connected Machine 에이전트를 제거하기 전이나 후에 이러한 단계를 수행할 수 있습니다.

1. [Azure Portal](https://aka.ms/hybridmachineportal)로 이동하여 Azure Arc 지원 서버를 엽니다.

2. 목록에서 머신을 선택하고, 줄임표 ( **...** )를 선택한 다음, **삭제** 를 선택합니다.

## <a name="update-or-remove-proxy-settings"></a>프록시 설정 업데이트 또는 제거

프록시 서버를 통해 서비스와 통신하도록 에이전트를 구성하거나 배포 후 이 구성을 제거하려면 다음 방법 중 하나를 사용하여 이 작업을 완료합니다. 에이전트는 이 시나리오에서 HTTP 프로토콜을 사용하여 아웃바운드 통신을 수행합니다.

> [!NOTE]
> Arc 사용 서버는 Connected Machine 에이전트에 대한 프록시로 [Log Analytics 게이트웨이](../../azure-monitor/agents/gateway.md)를 사용하는 것을 지원하지 않습니다.
>

### <a name="windows"></a>Windows

프록시 서버 환경 변수를 설정하려면 다음 명령을 실행합니다.

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

프록시 서버를 통한 통신을 중지하도록 에이전트를 구성하려면 다음 명령을 실행하여 프록시 서버 환경 변수를 제거하고 에이전트 서비스를 다시 시작합니다.

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

프록시 서버를 설정하려면 에이전트 설치 패키지를 다운로드한 디렉터리에서 다음 명령을 실행합니다.

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

프록시 서버를 통한 통신을 중지하도록 에이전트를 구성하려면 다음 명령을 실행하여 프록시 구성을 제거합니다.

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM 인사이트로](../../azure-monitor/vm/vminsights-enable-policy.md)로 모니터링 사용 등).
