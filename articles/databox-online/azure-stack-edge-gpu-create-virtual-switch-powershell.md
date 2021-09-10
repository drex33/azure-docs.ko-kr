---
title: PowerShell을 통해 Azure Stack Edge에서 새 가상 스위치 만들기
description: PowerShell을 사용하여 Azure Stack Edge 디바이스에서 가상 스위치를 만드는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 9910ac4d817879812803cd41f6b184846e1b02be
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106248"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>PowerShell을 통해 Azure Stack Edge Pro GPU에서 새 가상 스위치 만들기

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에서 새 가상 스위치를 만드는 방법을 설명합니다. 예를 들어 가상 머신이 다른 실제 네트워크 포트를 통해 연결되도록 하려면 새 가상 스위치를 만듭니다.

## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

1. 디바이스의 PowerShell 인터페이스에 연결합니다.
2. 사용 가능한 실제 네트워크 인터페이스를 쿼리합니다.
3. 가상 스위치 만들기에서 구성한 가상 스위치를 지정해야 합니다.
4. 자동으로 생성되는 가상 네트워크 및 서브넷을 확인합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

- 디바이스의 PowerShell 인터페이스에 액세스할 수 있는 클라이언트 컴퓨터에 액세스할 수 있습니다. [PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)을 참조하세요. 

    클라이언트 컴퓨터에서 지원되는 [OS](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 실행해야 합니다.

- 디바이스에서 [컴퓨팅 네트워크 사용](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)의 지침에 따라 로컬 UI를 사용하여 디바이스의 실제 네트워크 인터페이스 중 하나에서 컴퓨팅을 사용하도록 설정할 수 있습니다. 


## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결합니다.

[디바이스의 PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.

## <a name="query-available-network-interfaces"></a>사용 가능한 네트워크 인터페이스 쿼리

1. 다음 명령을 사용하여 새 가상 스위치를 만들 수 있는 실제 네트워크 인터페이스 목록을 표시합니다. 이러한 네트워크 인터페이스 중 하나를 선택합니다.

    ```powershell
    Get-NetAdapter -Physical
    ```
    출력의 예제는 다음과 같습니다.
    
    ```output
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. 다음과 같은 네트워크 인터페이스를 선택합니다.

    - **최신** 상태입니다. 
    - 기존 가상 스위치에서 사용되지 않습니다. 현재 네트워크 인터페이스마다 하나의 가상 스위치만 구성할 수 있습니다. 
    
    기존 가상 스위치와 네트워크 인터페이스 연결을 확인하려면 `Get-HcsExternalVirtualSwitch` 명령을 실행합니다.
 
    출력의 예제는 다음과 같습니다.

    ```output
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    이 경우 포트 2는 기존 가상 스위치와 연결되어 있으므로 사용해서는 안 됩니다.

## <a name="create-a-virtual-switch"></a>가상 스위치 만들기

다음 cmdlet을 사용하여 지정된 네트워크 인터페이스에 새 가상 스위치를 만듭니다. 이 작업이 완료되면 컴퓨팅 인스턴스가 새 가상 네트워크를 사용할 수 있습니다.

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

`Get-HcsExternalVirtualSwitch` 명령을 사용하여 새로 만든 스위치를 식별합니다. 만들어진 새 스위치의 이름은 `vswitch-<InterfaceAlias>`로 지정됩니다. 

출력의 예제는 다음과 같습니다.

```output
[10.100.10.10]: PS> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet-for-switch"></a>네트워크, 스위치에 대한 서브넷을 확인합니다

새 가상 스위치를 만들면 Azure Stack Edge Pro GPU가 이에 해당하는 가상 네트워크 및 서브넷을 자동으로 만듭니다. VM을 만들 때 이 가상 네트워크를 사용할 수 있습니다.

생성한 새 스위치와 연결된 가상 네트워크 및 서브넷을 확인하려면, `Get-HcsVirtualNetwork` cmdlet을 사용합니다. 

## <a name="create-virtual-lans"></a>가상 Lan 생성하기

가상 스위치에 가상 근거리 통신망(LAN) 구성을 추가하려면 다음 cmdlet을 사용합니다.

```powershell
Add-HcsVirtualNetwork-VirtualSwitchName <Virtual Switch name> -VnetName <Virtual Network Name> –VlanId <Vlan Id> –AddressSpace <Address Space> –GatewayIPAddress <Gateway IP>–DnsServers <Dns Servers List> -DnsSuffix <Dns Suffix name>
``` 

다음 매개 변수를 `Add-HcsVirtualNetwork-VirtualSwitchName` cmdlet과 함께 사용할 수 있습니다.


|매개 변수  |설명  |
|---------|---------|
|VNetName     |가상 LAN 네트워크 이름         |
|VirtualSwitchName    |가상 LAN 구성을 추가하려는 가상 스위치 이름         |
|AddressSpace     |가상 LAN 네트워크에 대한 서브넷 주소 공간         |
|GatewayIPAddress     |가상 네트워크에 대한 게이트웨이         |
|DnsServers     |DNS 서버 IP 주소 목록         |
|DnsSuffix     |가상 LAN 네트워크 서브넷의 호스트 파트가 없는 Dns 이름         |



출력의 예제는 다음과 같습니다.

```output
[10.100.10.10]: PS> Add-HcsVirtualNetwork -VirtualSwitchName vSwitch1 -VnetName vlanNetwork100 -VlanId 100 -AddressSpace 5.5.0.0/16 -GatewayIPAddress 5.5.0.1 -DnsServers "5.5.50.50&quot;,&quot;5.5.50.100&quot; -DnsSuffix &quot;name.domain.com"

[10.100.10.10]: PS> Get-HcsVirtualNetwork
 
Name             : vnet2015
AddressSpace     : 10.128.48.0/22
SwitchName       : vSwitch1
GatewayIPAddress : 10.128.48.1
DnsServers       : {}
DnsSuffix        :
VlanId           : 2015
 
Name             : vnet3011
AddressSpace     : 10.126.64.0/22
SwitchName       : vSwitch1
GatewayIPAddress : 10.126.64.1
DnsServers       : {}
DnsSuffix        :
VlanId           : 3011
```
 
> [!NOTE]
> - 동일한 가상 스위치에서 여러 가상 LAN을 구성할 수 있습니다. 
> - 게이트웨이 IP 주소는 주소 공간으로 전달된 매개 변수와 동일한 서브넷에 있어야 합니다.
> - 구성된 가상 LAN이 있으면 가상 스위치를 제거할 수 없습니다. 이 가상 스위치를 삭제하려면, 먼저 가상 LAN을 삭제한 후 가상 스위치를 삭제해야 합니다.

## <a name="verify-network-subnet-for-virtual-lan"></a>네트워크, 가상 LAN 용 서브넷을 확인합니다

가상 LAN을 생성한 후에는 가상 네트워크 및 해당 서브넷이 자동으로 생성됩니다. VM을 만들 때 이 가상 네트워크를 사용할 수 있습니다.

생성한 새 스위치와 연결된 가상 네트워크 및 서브넷을 확인하려면, `Get-HcsVirtualNetwork` cmdlet을 사용합니다.


## <a name="next-steps"></a>다음 단계

- [Azure PowerShell을 통해 Azure Stack Edge Pro GPU 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [Azure Portal을 통해 Azure Stack Edge Pro GPU 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
