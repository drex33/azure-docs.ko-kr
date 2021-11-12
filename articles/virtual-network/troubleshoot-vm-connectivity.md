---
title: Azure VM 연결 문제 해결
description: Azure VM(가상 머신)에 영향을 주는 연결 문제를 진단하고 해결하는 방법을 알아봅니다.
author: TobyTu
ms.author: kaushika
manager: dcscontentpm
audience: ITPro
ms.topic: troubleshooting
ms.service: virtual-network
localization_priority: Normal
ms.date: 08/29/2019
ms.openlocfilehash: ac29c46f9e7b1bdb3201e33bc35e35811d8adc98
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335350"
---
# <a name="troubleshoot-azure-vm-connectivity-problems"></a>Azure VM 연결 문제 해결

이 문서를 통해 관리자는 Azure VM(가상 머신)에 영향을 주는 연결 문제를 진단하고 해결할 수 있습니다.

## <a name="problems"></a>문제

- [Resource Manager를 사용하여 배포된 Azure VM은 동일한 가상 네트워크의 다른 Azure VM에 연결할 수 없습니다.](#azure-vm-cannot-connect-to-another-azure-vm-in-same-virtual-network)
- [Azure VM은 동일한 가상 네트워크에 있는 Azure VM의 두 번째 네트워크 어댑터에 연결할 수 없습니다.](#azure-vm-cannot-connect-to-the-second-network-adapter-of-an-azure-vm-in-same-virtual-network)
- [Azure VM은 인터넷에 연결할 수 없습니다](#azure-vm-cannot-connect-to-the-internet).

이러한 문제를 해결하려면 다음 섹션의 단계를 따르세요.

## <a name="resolution"></a>해결 방법

### <a name="azure-vm-cannot-connect-to-another-azure-vm-in-same-virtual-network"></a>Azure VM이 동일한 가상 네트워크의 다른 Azure VM에 연결할 수 없음

#### <a name="step-1-verify-that-vms-can-communicate-with-each-other"></a>1단계: VM이 서로 통신할 수 있는지 확인합니다.

1. 원본 VM에 TCping을 다운로드합니다.
2. 명령 프롬프트 창을 엽니다.
3. TCping을 다운로드한 폴더로 이동합니다.
4. 다음 명령을 사용하여 원본 VM에서 대상을 Ping합니다.

    ![스크린샷은 IP 주소를 지속적으로 ping하는 명령 프롬프트 창을 보여줍니다.](media/troubleshoot-vm-connectivity/tcping.png)

    ```cmd
    tcping64.exe -t <destination VM address> 3389
    ```

> [!TIP]
> Ping 테스트가 성공하면 3단계로 이동합니다. 그렇지 않으면 다음 단계로 진행합니다.

#### <a name="step-2-check-the-network-security-group-settings"></a>2단계: 네트워크 보안 그룹 설정을 확인합니다.

각 VM에 대해 기본 인바운드 포트 규칙(‘VNet 인바운드 허용’ 및 ‘Load Balancer 인바운드 허용’)을 확인합니다. 또한 우선 순위가 낮은 규칙 아래에 나열된 일치하는 차단 규칙은 없는지 확인해야 합니다.

> [!NOTE]
> 숫자가 낮은 규칙이 먼저 일치합니다. 예를 들어, 우선 순위 1000 및 6500이 있는 규칙이 있을 경우 우선 순위 1000이 있는 규칙이 먼저 일치합니다.

그런 다음, 원본 VM에서 대상을 다시 ping해 봅니다.

```cmd
tcping64.exe -t <destination VM address> 3389
```

#### <a name="step-3-check-whether-you-can-connect-to-the-destination-vm-by-using-remote-desktop-or-ssh"></a>3단계: 원격 데스크톱 또는 SSH를 사용하여 대상 VM에 연결할 수 있는지 확인합니다.

원격 데스크톱을 사용하여 연결하려면 다음 단계를 수행 합니다.

**Windows**:

1. Azure Portal에 로그인합니다.
2. 왼쪽 메뉴에서 **Virtual Machines** 을 선택합니다.
3. 목록에서 가상 머신을 선택합니다.
4. 가상 머신에 대한 페이지에서 **연결** 을 선택합니다.

자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그인하는 방법](../virtual-machines/windows/connect-logon.md)을 참조하세요.

**Linux**:

자세한 내용은 [Azure에서 Linux VM에 연결](../virtual-machines/linux/quick-create-portal.md)을 참조하세요.

원격 데스크톱 또는 SSH 연결에 성공하면 다음 단계로 이동합니다.

#### <a name="step-4-perform-a-connectivity-check"></a>4단계: 연결 확인을 수행합니다.

원본 VM에 대한 연결 확인을 실행하고 응답을 확인합니다.

**Windows**: [PowerShell을 사용하여 Azure Network Watcher를 통해 연결 확인](../network-watcher/network-watcher-connectivity-powershell.md)

**Linux**: [Azure CLI 2.0을 사용하여 Azure Network Watcher를 통해 연결 확인](../network-watcher/network-watcher-connectivity-cli.md)

다음은 응답 예제입니다.

```
ConnectionStatus : Unreachable
AvgLatencyInMs   :
MinLatencyInMs   :
MaxLatencyInMs   :
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
```

#### <a name="step-5-fix-the-issue-in-the-connectivity-check-result"></a>5단계: 연결 확인 결과에서 문제를 해결합니다.

1. 수신한 연결 확인 응답의 **홉** 섹션에서 나열된 **문제** 를 확인합니다.

    ![연결 응답](media/troubleshoot-vm-connectivity/connectivity-response.png)

2. 다음 표에서 해당하는 해결 방법을 확인하고 표시된 단계에 따라 문제를 해결합니다.

    |문제 유형  |값  |해결 작업 |
    |---------|---------|---------|
    |NetworkSecurityRule|차단 NSG의 이름|[여기](./manage-network-security-group.md#change-a-security-rule)에 설명된 대로 [NSG 규칙을 삭제](./manage-network-security-group.md#delete-a-security-rule)하거나 규칙을 수정할 수 있습니다.|
    |UserDefinedRoute     |   차단 UDR의 이름      | 이 경로가 필요하지 않다면 UDR을 삭제합니다. 경로를 삭제할 수 없는 경우 적절한 주소 접두사 및 다음 홉을 사용하여 경로를 업데이트합니다. 네트워크 가상 어플라이언스를 조정하여 트래픽을 적절하게 전달할 수도 있습니다. 자세한 내용은 [가상 네트워크 트래픽 라우팅](./virtual-networks-udr-overview.md) 및 [PowerShell을 사용하여 경로 테이블이 포함된 네트워크 트래픽 라우팅](./tutorial-create-route-table-powershell.md)을 참조하세요.|
    |CPU    |    사용량     |     [Linux 또는 Windows를 실행하는 Azure 가상 머신에 대한 일반적인 성능 문제 해결](https://support.microsoft.com/en-in/help/3150851/generic-performance-troubleshooting-for-azure-virtual-machine-running)에 설명된 다음 권장 사항을 따릅니다.|
    |메모리    |      사용량   |    [Linux 또는 Windows를 실행하는 Azure 가상 머신에 대한 일반적인 성능 문제 해결](https://support.microsoft.com/en-in/help/3150851/generic-performance-troubleshooting-for-azure-virtual-machine-running)에 설명된 다음 권장 사항을 따릅니다.|
    |게스트 방화벽    |      방화벽 차단의 이름   |      [Windows Defender 방화벽 켜기 또는 끄기](https://support.microsoft.com/help/4028544/windows-turn-windows-firewall-on-or-off) 단계를 수행합니다.|
    |DNS 확인     |    DNS 이름     |    Azure 가상 네트워크의 리소스에 대한 [Azure DNS 문제 해결 가이드](../dns/dns-troubleshoot.md) 및 [Azure 가상 네트워크에서 리소스에 대한 이름 확인](./virtual-networks-name-resolution-for-vms-and-role-instances.md) 단계를 수행합니다.     |
    |소켓 오류    |      적용할 수 없음   |     지정한 포트는 다른 애플리케이션에서 이미 사용 중입니다. 다른 포트를 사용해 보세요.    |

3. 연결 확인을 다시 실행하여 문제가 해결되었는지 확인합니다.

### <a name="azure-vm-cannot-connect-to-the-second-network-adapter-of-an-azure-vm-in-same-virtual-network"></a>Azure VM은 동일한 가상 네트워크에 있는 Azure VM의 두 번째 네트워크 어댑터에 연결할 수 없습니다.

#### <a name="step-1-make-sure-that-the-second-network-adapter-is-enabled-to-talk-outside-the-subnet"></a>1단계: 서브넷 외부에서 통신할 수 있도록 두 번째 네트워크 어댑터를 사용할 수 있는지 확인합니다.

기본적으로 보조 네트워크 어댑터(네트워크 인터페이스 카드 또는 네트워크 어댑터라고도 함)는 기본 게이트웨이를 갖도록 구성되어 있지 않습니다. 따라서 보조 어댑터의 트래픽 흐름은 동일한 서브넷으로 제한됩니다.

![IP 구성](media/troubleshoot-vm-connectivity/ipconfig.png)

보조 네트워크 어댑터가 자체 서브넷 외부와 통신할 수 있도록 설정하려는 경우, 게이트웨이를 구성하도록 라우팅 테이블에 항목을 추가해야 합니다. 이렇게 하려면 다음 단계를 따르십시오.

1. 두 번째 네트워크 어댑터가 구성된 VM에서 관리자 권한으로 명령 프롬프트 창을 엽니다.
2. 다음 명령을 실행하여 라우팅 테이블에 항목을 추가합니다.

    ```cmd
    Route add 0.0.0.0 mask 0.0.0.0 -p <Gateway IP>
    ```

    예를 들어 두 번째 IP 주소가 192.168.0.4인 경우 게이트웨이 IP는 192.168.0.1이어야 합니다. 다음 명령을 실행해야 합니다.

    ```cmd
    Route add 0.0.0.0 mask 0.0.0.0 -p 192.168.0.1
    ```

3. route print를 실행합니다. 항목이 성공적으로 추가되면 다음과 유사한 항목이 표시됩니다.

    ![IP 경로](media/troubleshoot-vm-connectivity/iproute.png)

이제 보조 네트워크 어댑터에 연결해 봅니다. 그래도 연결에 실패하면 다음 단계로 이동합니다.

#### <a name="step-2-check-nsg-settings-for-the-network-adapters"></a>2단계: 네트워크 어댑터에 대한 NSG 설정을 확인합니다.

기본 및 보조 네트워크 어댑터 둘 다에 대해 두 네트워크 어댑터에서 인바운드 포트 규칙(VNet 인바운드 허용, Load Balancer 허용)을 모두 선택합니다. 또한 우선 순위가 낮은 규칙이 상위에 있는 일치하는 차단 규칙은 없는지 확인해야 합니다.

![스크린샷에는 가상 머신에 대한 네트워킹 설정이 표시되며, 여기서 VNet 인바운드 허용 및 VNet 아웃바운드 허용을 볼 수 있습니다.](media/troubleshoot-vm-connectivity/nsg.png)

#### <a name="step-3-run-a-connectivity-check-to-the-secondary-network-adapter"></a>3단계: 보조 네트워크 어댑터에 대한 연결 확인을 실행합니다.

1. 보조 네트워크 어댑터에 대한 연결 확인을 실행합니다.
2. 환경 전체에서 연결 확인을 실행하여 프로세스가 엔드 투 엔드로 작동하는지 확인합니다.

연결 확인을 실행하는 방법에 관한 자세한 내용은 다음 문서를 참조하세요.

**Windows**: [PowerShell을 사용하여 Azure Network Watcher를 통해 연결 확인](../network-watcher/network-watcher-connectivity-powershell.md)

**Linux**: [Azure CLI 2.0을 사용하여 Azure Network Watcher를 통해 연결 확인](../network-watcher/network-watcher-connectivity-cli.md).

다음은 응답 예제입니다.

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
```

#### <a name="step-4-refer-the-table-under-step-5-and-follow-these-steps-to-resolve-the-issues"></a>4단계: [5단계](#step-5-fix-the-issue-in-the-connectivity-check-result)에서 표를 참조하고 다음 단계를 수행하여 문제를 해결합니다.

### <a name="azure-vm-cannot-connect-to-the-internet"></a>Azure VM이 인터넷에 연결할 수 없음

#### <a name="step-1-check-whether-the-network-adapter-is-in-a-failed-state"></a>1단계: 네트워크 어댑터가 실패 상태인지 확인합니다.

다음 단계에 따라 NIC의 상태를 확인합니다.

1. 리소스 탐색기 포털에 로그인합니다.
2. 왼쪽 창에서 **구독** 을 선택합니다.
3. 왼쪽 창에서 영향을 받는 네트워크 어댑터 또는 VM이 속한 리소스 그룹을 선택합니다.
4. **Microsoft 네트워크** 로 이동합니다.
5. **네트워크 인터페이스** 옵션을 선택합니다.
6. 영향을 받는 네트워크 인터페이스를 선택합니다.
7. 포털 위쪽에서 **읽기/쓰기** 옵션을 선택합니다.
8. **편집** 옵션을 선택합니다.

    ![NIC1](media/troubleshoot-vm-connectivity/nicedit1.png)

    > [!NOTE]
    > **편집** 옵션을 선택하면 ‘Get’ 옵션이 **Put** 옵션으로 변경됩니다.

    ![NIC2](media/troubleshoot-vm-connectivity/nicedit2.png)

9. 영향을 받는 네트워크 인터페이스를 선택한 후 **Put** 옵션을 선택합니다.

    > [!NOTE]
    > 이 항목을 선택하면 **ProvisioningState** 가 **업데이트 중** 으로 표시됩니다. 동일한 결과가 일반 Azure Resource Manager 포털에 표시됩니다. 작업이 성공적으로 완료되면 화면에 표시된 대로 **ProvisioningState** 값이 **성공** 으로 변경됩니다.
10. 포털을 새로 고칩니다. 네트워크 어댑터가 성공 상태여야 합니다.

#### <a name="step-2-follow-step-4-to-run-a-connectivity-check"></a>2단계: [4단계](#step-4-perform-a-connectivity-check)를 수행하여 연결 확인을 실행합니다.

#### <a name="step-3-refer-the-table-under-step-5-and-follow-the-steps-to-resolve-the-issues"></a>3단계: [5단계](#step-5-fix-the-issue-in-the-connectivity-check-result)에서 표를 참조하고 다음 단계를 수행하여 문제를 해결합니다.

## <a name="next-steps"></a>다음 단계

[Azure VM 간의 연결 문제 해결](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
