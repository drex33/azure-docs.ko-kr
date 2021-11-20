---
title: Azure VM을 사용하여 Azure Arc 지원 서버를 평가하는 방법
description: Azure 가상 머신을 사용하여 Azure Arc 지원 서버를 평가하는 방법을 알아봅니다.
ms.date: 10/01/2021
ms.topic: conceptual
ms.openlocfilehash: 83abe15a891359e11bbb374a29c2c0223fe971ac
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132868759"
---
# <a name="evaluate-azure-arc-enabled-servers-on-an-azure-virtual-machine"></a>Azure 가상 머신에서 Azure Arc 지원 서버 평가

Azure Arc 지원 서버는 온-프레미스나 다른 클라우드에서 실행되는 서버를 Azure에 연결하는 데 도움이 되도록 설계되었습니다. Azure Resource Manager의 VM 표현, VM 확장, 관리 ID, Azure Policy를 포함하여 동일한 모든 기능을 VM에서 기본적으로 사용할 수 있기 때문에, 일반적으로 Azure 가상 머신에서는 Azure Arc 지원 서버를 사용하지 않습니다. Azure VM에 Azure Arc 지원 서버를 설치하려고 하면 지원되지 않는다는 오류 메시지가 표시되고 에이전트 설치가 취소됩니다.

프로덕션 시나리오를 위해 Azure VM에 Azure Arc 지원 서버를 설치할 수는 없지만 ‘평가 및 테스트 목적으로만’ Azure VM에서 실행되도록 Azure Arc 지원 서버를 구성할 수 있습니다. 이 문서는 Azure Arc 지원 서버를 사용하도록 설정하기 전에 Azure VM을 설정하는 데 도움이 됩니다.

> [!NOTE]
> 이 문서의 단계는 Azure 클라우드에서 호스트되는 가상 머신을 위한 것입니다. Azure Arc 지원 서버는 Azure Stack Hub 또는 Azure Stack Edge 실행되는 가상 머신에서 지원되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 사용자 계정은 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할에 할당됩니다.
* Azure 가상 머신은 [Azure Arc 지원되는 서버에서 지원하는 운영 체제를](agent-overview.md#supported-operating-systems)실행하고 있습니다. Azure VM이 없는 경우 [단순 Windows VM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json) 또는 [단순 Ubuntu Linux 18.04 LTS VM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json)을 배포할 수 있습니다.
* Azure VM은 아웃바운드 통신을 통해 [Microsoft 다운로드 센터](https://aka.ms/AzureConnectedMachineAgent)에서 Windows용 Azure Connected Machine 에이전트 패키지를 다운로드하고 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)에서 Linux를 다운로드할 수 있습니다. 인터넷에 대한 아웃바운드 연결이 IT 보안 정책에 따라 제한되는 경우 에이전트 패키지를 수동으로 다운로드하여 Azure VM의 폴더에 복사해야 합니다.
* VM에 대한 상승된 권한(즉, 관리자 또는 루트 권한)이 있고 VM에 대한 RDP 또는 SSH 액세스 권한이 있는 계정입니다.
* Azure Arc 지원 서버에 Azure VM을 등록하고 관리하려면 리소스 그룹에서 Azure Connected Machine [리소스 관리자](../../role-based-access-control/built-in-roles.md#azure-connected-machine-resource-administrator) 또는 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할의 구성원입니다.

## <a name="plan"></a>계획

Azure VM을 Azure Arc 지원 서버로 관리하려면 Azure VM을 다음과 같이 변경해야 Azure Arc 지원 서버를 설치하고 구성할 수 있습니다.

1. Azure VM에 배포된 모든 VM 확장(예: Log Analytics 에이전트)을 제거합니다. Azure Arc 지원 서버는 Azure VM과 동일한 많은 확장을 지원하지만 Azure Arc 지원 서버 에이전트는 VM에 이미 배포된 VM 확장을 관리할 수 없습니다.

2. Azure Windows 또는 Linux 게스트 에이전트를 사용하지 않도록 설정합니다. Azure VM 게스트 에이전트는 Azure Connected Machine 에이전트와 비슷한 용도로 사용됩니다. 두 에이전트 간의 충돌을 방지하려면 Azure VM 에이전트를 사용하지 않도록 설정해야 합니다. 사용하지 않도록 설정되면 VM 확장 또는 일부 Azure 서비스를 사용할 수 없습니다.

3. IMDS(Azure Instance Metadata Service)에 대한 액세스를 거부하는 보안 규칙을 만듭니다. IMDS는 애플리케이션이 리소스 ID 및 위치를 포함하여 Azure에서 VM의 표현에 대한 정보를 얻기 위해 호출할 수 있는 REST API입니다. 또한 IMDS는 컴퓨터에 할당된 모든 관리 ID에 대한 액세스 권한을 제공합니다. Azure Arc 지원 서버는 자체 IMDS 구현을 제공하고 VM의 Azure Arc 표현에 대한 정보를 반환합니다. 두 IMDS 엔드포인트를 모두 사용할 수 있고 앱이 둘 중에서 선택해야 하는 상황을 피하려면 Azure Arc 지원 서버의 IMDS 구현만 사용할 수 있도록 Azure VM IMDS에 대한 액세스를 차단합니다.

이렇게 변경한 후에는 Azure VM이 Azure 외부 머신이나 서버처럼 동작하며, Azure Arc 지원 서버를 설치하고 평가하는 데 필요한 시작점에 있습니다.

Azure Arc 지원 서버가 VM에 구성된 경우 Azure에서 두 가지 표현이 표시됩니다. 하나는 `Microsoft.Compute/virtualMachines` 리소스 유형의 Azure VM 리소스이고 다른 하나는 `Microsoft.HybridCompute/machines` 리소스 유형의 Azure Arc 리소스입니다. 공유 물리적 호스트 서버에서 게스트 운영 체제 관리를 방지한 결과 두 리소스에 대해 생각하는 가장 좋은 방법은 Azure VM 리소스이며 사용자 VM에 대한 가상 하드웨어입니다. 이를 통해 전원 상태를 제어하고 해당 SKU, 네트워크 및 스토리지 구성에 대한 정보를 볼 수 있습니다. Azure Arc 리소스는 해당 VM의 게스트 운영 체제를 관리하며, 확장을 설치하고, Azure Policy 규정 준수 데이터를 보고, Azure Arc 지원되는 서버에서 지원되는 다른 작업을 완료하는 데 사용할 수 있습니다.

## <a name="reconfigure-azure-vm"></a>Azure VM 다시 구성

1. Azure VM에서 VM 확장을 제거합니다.

   Azure Portal에서 Azure VM 리소스로 이동하고 왼쪽 창에서 **확장** 을 선택합니다. VM에 설치된 확장이 있는 경우 각 확장을 개별적으로 선택한 다음 **제거** 를 선택합니다. 2단계를 진행하기 전에 모든 확장이 제거를 마칠 때까지 기다립니다.

2. Azure VM 게스트 에이전트를 사용하지 않도록 설정합니다.

   Azure VM 게스트 에이전트를 사용하지 않도록 설정하려면 원격 데스크톱 연결(Windows) 또는 SSH(Linux)를 사용하여 VM에 연결해야 합니다. 연결되면 다음 명령을 실행하여 게스트 에이전트를 사용하지 않도록 설정합니다.

   Windows의 경우 다음 PowerShell 명령을 실행합니다.

   ```powershell
   Set-Service WindowsAzureGuestAgent -StartupType Disabled -Verbose
   Stop-Service WindowsAzureGuestAgent -Force -Verbose
   ```

   Linux의 경우 다음 명령을 실행합니다.

   ```bash
   current_hostname=$(hostname)
   sudo service walinuxagent stop
   sudo waagent -deprovision -force
   sudo rm -rf /var/lib/waagent
   sudo hostnamectl set-hostname $current_hostname
   ```

3. Azure IMDS 엔드포인트에 대한 액세스를 차단합니다.

   서버에 계속 연결되어 있는 동안 다음 명령을 실행하여 Azure IMDS 엔드포인트에 대한 액세스를 차단합니다. Windows의 경우 다음 PowerShell 명령을 실행합니다.

   ```powershell
   New-NetFirewallRule -Name BlockAzureIMDS -DisplayName "Block access to Azure IMDS" -Enabled True -Profile Any -Direction Outbound -Action Block -RemoteAddress 169.254.169.254
   ```

   Linux의 경우 TCP 포트 80을 통해 `169.254.169.254/32`에 대한 아웃바운드 액세스를 차단하는 가장 좋은 방법은 배포 문서를 참조하세요. 일반적으로 기본 제공 방화벽을 사용하여 아웃바운드 액세스를 차단하지만 **iptables** 또는 **nftables** 를 사용하여 일시적으로 차단할 수도 있습니다.

   Azure VM에서 Ubuntu를 실행하는 경우 다음 단계를 수행하여 UFW(복잡하지 않은 방화벽)를 구성합니다.

   ```bash
   sudo ufw --force enable
   sudo ufw deny out from any to 169.254.169.254
   sudo ufw default allow incoming
   ```

   Azure VM에서 CentOS, Red Hat 또는 SLES(SUSE Linux Enterprise Server)를 실행하는 경우 다음 단계를 수행하여 방화벽을 구성합니다.

   ```bash
   firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 1 -p tcp -d 169.254.169.254 -j DROP
   firewall-cmd --reload
   ```

   다른 배포의 경우 방화벽 문서를 참조하거나 다음 명령을 통해 일반 iptables 규칙을 구성합니다.

   ```bash
   iptables -A OUTPUT -d 169.254.169.254 -j DROP
   ```

   > [!NOTE]
   > 영구 iptables 솔루션을 사용하지 않는 한 다시 부팅할 때마다 iptables 구성을 설정해야 합니다.


4. Azure Arc 지원 서버 에이전트를 설치하고 구성합니다.

   이제 VM이 Azure Arc 지원 서버 평가를 시작할 준비가 되었습니다. Azure Arc 지원 서버 에이전트를 설치하고 구성하려면 [Azure Portal 사용하여 하이브리드 머신 커넥트](onboard-portal.md) 참조하고 스크립팅된 방법을 사용하여 설치 스크립트를 생성하고 설치하는 단계를 수행합니다.

   > [!NOTE]
   > 인터넷에 대한 아웃바운드 연결이 Azure VM에서 제한되는 경우 에이전트 패키지를 수동으로 다운로드해야 합니다. 에이전트 패키지를 Azure VM에 복사하고 Azure Arc 지원 서버 설치 스크립트를 수정하여 원본 폴더를 참조합니다.

단계 중 하나를 누락한 경우 설치 스크립트는 Azure VM에서 실행 중임을 감지하고 오류와 함께 종료합니다. 1~3단계를 완료했는지 확인한 다음 스크립트를 다시 실행합니다.

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트를 설치하고 Azure Arc 지원 서버에 등록되도록 구성한 후에는 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://portal.azure.com)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

* Azure에서 필수 보안 관리 및 모니터링 기능의 구성을 간소화하기 위해 [Azure Arc 지원 서버에 대해 많은 머신을 계획하고 사용하도록 설정하는 방법](plan-at-scale-deployment.md)을 알아봅니다.

* Windows 또는 Linux 컴퓨터를 위한 Automation, KeyVault 등의 기타 Azure 서비스와 함께 배포를 단순화하는 데 사용할 수 있는 [지원되는 Azure VM 확장](manage-vm-extensions.md)에 대해 알아봅니다.

* 테스트를 마쳤으면 [Azure Arc 지원 서버 에이전트 제거를](manage-agent.md#remove-the-agent)참조하세요.
