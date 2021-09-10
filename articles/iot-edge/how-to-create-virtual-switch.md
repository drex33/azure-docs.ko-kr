---
title: Azure IoT Edge for Linux on Windows에 대한 가상 스위치 만들기 | Microsoft Docs
description: Azure IoT Edge for Linux on Windows에 대한 가상 스위치를 만들기 위한 설치
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/12/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 5db37717356cd06b257867615623e24a1c36a335
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528882"
---
# <a name="azure-iot-edge-for-linux-on-windows-virtual-switch-creation"></a>Azure IoT Edge for Linux on Windows 가상 스위치 만들기
Azure IoT Edge for Linux on Windows는 호스트 머신의 가상 스위치를 사용하여 가상 머신과 통신합니다. Windows 데스크톱 버전에는 사용할 수 있는 기본 스위치가 함께 제공되지만 Windows Server는 그렇지 않습니다. IoT Edge for Linux on Windows를 Windows Server 디바이스에 배포하려면 먼저 가상 스위치를 만들어야 합니다. 또한 필요한 경우 이 가이드를 사용하여 사용자 지정 가상 스위치를 만들 수 있습니다. 

이 문서에서는 다음 단계를 사용하여 IoT Edge for Linux on Windows를 설치하기 위해 Windows 디바이스에서 가상 스위치를 만드는 방법을 보여줍니다.
- 가상 스위치 만들기
- NAT 테이블 만들기
- DHCP 서버 설치 및 설정

## <a name="prerequisites"></a>필수 구성 요소
- Windows 디바이스. 지원되는 Windows 버전은 [운영 체제](support.md#operating-systems)를 참조하세요.
- Hyper-V 역할이 Windows 디바이스에 설치되어 있어야 합니다. Hyper-V를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure IoT Edge for Linux on a Windows 디바이스 설치 및 프로비저닝](./how-to-install-iot-edge-on-windows.md?tabs=powershell#prerequisites)을 참조하세요.

## <a name="create-virtual-switch"></a>가상 스위치 만들기 
이 섹션의 다음 단계는 가상 스위치 만들기에 대한 일반 가이드입니다. 가상 스위치 구성이 네트워킹 환경에 부합해야 합니다.

1. 관리자 권한 세션에서 PowerShell을 엽니다.

2. Windows 호스트의 가상 스위치를 확인하고 사용할 수 있는 가상 스위치가 없는지 확인합니다. 자세한 내용은 [Get-VMSwitch(Hyper-V)](/powershell/module/hyper-v/get-vmswitch)를 확인하세요. 

   ```powershell
   Get-VMSwitch
   ```

   **기본 스위치** 라는 가상 스위치가 이미 만들어져 사용자 지정 가상 스위치가 필요하지 않은 경우 이 가이드의 나머지 단계를 수행하지 않고 IoT Edge for Linux on Windows를 설치할 수 있어야 합니다.

3. 이름이 있고 유형이 **내부** 또는 **프라이빗** 인 새 VM 스위치를 만듭니다. **외부** 가상 스위치를 만들려면 암시적으로 가상 스위치 유형을 **외부** 로 설정하는 **NetAdapterInterfaceDescription** 또는 **NetAdapterName** 매개 변수를 지정합니다. 자세한 내용과 추가 지침은 [New-VMSwitch(Hyper-V)](/powershell/module/hyper-v/new-vmswitch) 및 [Hyper-V 가상 머신용 가상 스위치 만들기](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines)를 확인하세요.
   ```powershell
   New-VMSwitch -Name "{switchName}" -SwitchType {switchType}
   ```

4. 만든 스위치의 인터페이스 인덱스를 가져옵니다. 자세한 내용은 [Get-NetAdapter(NetAdapter)](/powershell/module/netadapter/get-netadapter)를 확인하세요. 
   ```powershell
   (Get-NetAdapter -Name '*{switchName}*').ifIndex
   ```

5. 이전 단계의 인터페이스 인덱스를 사용하여 생성된 스위치 네트워크 어댑터의 IP 주소 옥텟을 가져옵니다. 자세한 내용은 [Get-NetIPAddress(NetTCPIP)](/powershell/module/nettcpip/get-netipaddress)를 확인하세요. 
   ```powershell
   Get-NetIPAddress -AddressFamily IPv4  -InterfaceIndex {ifIndex}
   ```

6. 이전 단계의 IP 주소 집합 및 인터페이스 인덱스를 사용하여 새 게이트웨이 IP 주소를 만들고 설정합니다.  예를 들어 가상 네트워크 스위치 어댑터의 IPv4 주소가 xxx.xxx.xxx.yyy인 경우에는 gatewayIp를 다음 xxx.xxx.xxx.1로 설정할 수 있습니다. 자세한 내용은 [New-NetIPAddress(NetTCPIP)](/powershell/module/nettcpip/new-netipaddress)를 확인하세요.
   ```powershell
   New-NetIPAddress -IPAddress {gatewayIp} -PrefixLength 24 -InterfaceIndex {ifIndex}
   ```

7. 내부 네트워크 주소를 외부 네트워크로 변환하는 NAT(Network Address Translation) 개체를 만듭니다. 이전 단계와 동일한 IPv4 집합 주소를 사용합니다. 예를 들어 가상 네트워크 스위치 어댑터의 IPv4 주소가 xxx.xxx.xxx.yyy인 경우에는 natIp를 다음 xxx.xxx.xxx.0으로 설정할 수 있습니다. 자세한 내용은 [New-NetNat(NetNat)](/powershell/module/netnat/new-netnat)을 확인하세요. 
   ```powershell
   New-NetNat -Name "{switchName}" -InternalIPInterfaceAddressPrefix "{natIp}/24"
   ```

## <a name="create-dhcp-server"></a>DHCP 서버 만들기 

>[!WARNING]
>회사 네트워크 환경에서 DHCP 서버를 배포하려면 권한 부여가 필요할 수 있습니다. 가상 스위치 구성이 회사 네트워크의 정책을 준수하는지 확인합니다. 자세한 내용은 [Windows PowerShell을 사용하여 DHCP 배포](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps) 가이드를 참조하세요. 

1. DHCP 서버 기능이 디바이스에 설치되어 있는지 확인합니다. **설치 상태** 열을 찾습니다.
   ```powershell
   Get-WindowsFeature -Name 'DHCP'
   ```

2. 설치되어 있지 않으면 다음 명령을 사용하여 설치합니다.
   ```powershell
   Install-WindowsFeature -Name 'DHCP' -IncludeManagementTools
   ```

3. DHCP 서버를 기본 로컬 보안 그룹에 추가하고 서버를 다시 시작합니다.
   ```powershell
   netsh dhcp add securitygroups
   Restart-Service dhcpserver
   ```

4. DHCP 서버 범위를 구성합니다. 자세한 내용은 [Add-DhcpServerv4Scope(DhcpServer)](/powershell/module/dhcpserver/add-dhcpserverv4scope)를 확인하세요.  IP의 DHCP 서버 범위는 **startIp** 및 **endIp** 로 결정됩니다. 예를 들어 100개 주소를 사용하려면 5단계에서 가상 네트워크 스위치 어댑터의 xxx.xxx.xxx.yyy IPv4 주소(startIp = xxx.xxx.xxx.100, endIp = xxx.xxx.xxx.200 및 subnetMask = 255.255.255.0)를 따릅니다.
   ```powershell
   Add-DhcpServerV4Scope -Name "AzureIoTEdgeScope" -StartRange {startIp} -EndRange {endIp} -SubnetMask {subnetMask} -State Active
   ```

5. 마지막으로 DHCP 서버에 NAT 개체 및 gatewayIp를 할당하고 서버를 다시 시작하여 구성을 로드합니다.
   ```powershell
   Set-DhcpServerV4OptionValue -ScopeID {natIp} -Router {gatewayIp}
   Restart-service dhcpserver
   ```

## <a name="next-steps"></a>다음 단계
[Windows 디바이스에서 Azure IoT Edge 설치 및 프로비저닝](how-to-install-iot-edge-on-windows.md) 단계에 따라 Windows에서 Linux용 IoT Edge를 사용하여 디바이스를 설정합니다.