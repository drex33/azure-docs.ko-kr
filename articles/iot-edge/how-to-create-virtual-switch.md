---
title: Azure IoT Edge for Linux on Windows에 대한 가상 스위치 만들기 | Microsoft Docs
description: Azure IoT Edge for Linux on Windows에 대한 가상 스위치를 만들기 위한 설치
author: kgremban
ms.reviewer: fcabrera
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/30/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: c379623dfcfea283f24745c6d698ff6bd999e20c
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133545222"
---
# <a name="azure-iot-edge-for-linux-on-windows-virtual-switch-creation"></a>Azure IoT Edge for Linux on Windows 가상 스위치 만들기
Azure IoT Edge for Linux on Windows는 호스트 머신의 가상 스위치를 사용하여 가상 머신과 통신합니다. Windows 데스크톱 버전은 사용할 수 있는 기본 스위치와 함께 제공 되지만 Windows Server는 *그렇지 않습니다*. IoT Edge for Linux on Windows를 Windows Server 디바이스에 배포하려면 먼저 가상 스위치를 만들어야 합니다. 또한 필요한 경우 이 가이드를 사용하여 사용자 지정 가상 스위치를 만들 수 있습니다. 

이 문서에서는 Windows에 Linux 용 IoT Edge를 설치 하는 Windows 장치에서 가상 스위치를 만드는 방법을 보여 줍니다. 이 프로세스는 다음 단계로 구분 됩니다.
- 가상 스위치 만들기
- NAT 테이블 만들기
- DHCP 서버 설치 및 설정

## <a name="prerequisites"></a>필수 구성 요소
- Windows 디바이스. 지원 되는 Windows 버전에 대 한 자세한 내용은 [운영 체제](support.md#operating-systems)를 참조 하세요.
- Hyper-V 역할이 Windows 디바이스에 설치되어 있어야 합니다. Hyper-V를 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure IoT Edge for Linux on a Windows 디바이스 설치 및 프로비저닝](./how-to-provision-single-device-linux-on-windows-symmetric.md?tabs=powershell#prerequisites)을 참조하세요.

## <a name="create-virtual-switch"></a>가상 스위치 만들기 
이 섹션의 다음 단계는 가상 스위치 만들기에 대한 일반 가이드입니다. 가상 스위치 구성이 네트워킹 환경에 부합해야 합니다.

> [!NOTE]
> 다음 단계에서는 **내부** 또는 **개인** 가상 스위치를 만드는 방법을 설명 합니다. **외부** 스위치를 만드는 방법에 대 한 자세한 내용은 [hyper-v 가상 컴퓨터에 대 한 가상 스위치 만들기](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines)를 참조 하세요.
Azure VM을 사용 하는 경우 가상 스위치는 **외부** 에 있을 수 없습니다.

1. 관리자 권한 세션에서 PowerShell을 엽니다. Windows에서 **시작** 창을 열고 "PowerShell"을 입력 하 여이 작업을 수행할 수 있습니다. 표시 되는 **Windows PowerShell** 앱을 마우스 오른쪽 단추로 클릭 하 고 **관리자 권한으로 실행** 을 선택 합니다.

2. Windows 호스트의 가상 스위치를 확인 하 고 사용할 수 있는 가상 스위치가 아직 없는지 확인 합니다. PowerShell에서 다음 [Get VMSwitch](/powershell/module/hyper-v/get-vmswitch) 명령을 실행 하 여이 작업을 수행할 수 있습니다.

    ```powershell
    Get-VMSwitch
    ```

    **기본 스위치** 라는 가상 스위치가 이미 만들어져 사용자 지정 가상 스위치가 필요하지 않은 경우 이 가이드의 나머지 단계를 수행하지 않고 IoT Edge for Linux on Windows를 설치할 수 있어야 합니다.

3. 다음의 [새-VMSwitch](/powershell/module/hyper-v/new-vmswitch) 명령을 실행 하 여 자리 표시자 값을 대체 하 여 선택한 이름 및 **내부** 또는 **개인** 스위치 유형의 새 VM 스위치를 만듭니다.

    ```powershell
    New-VMSwitch -Name "{switchName}" -SwitchType {switchType}
    ```

4. 만든 스위치에 대 한 IP 주소를 가져오려면 먼저 해당 인터페이스 인덱스를 가져와야 합니다. 다음 [get-netadapter](/powershell/module/netadapter/get-netadapter) 명령을 실행 하 여 자리 표시자 값을 대체 하 여이 값을 가져올 수 있습니다.

    ```powershell
    (Get-NetAdapter -Name "{switchName}").ifIndex
    ```

    `Name` `vEthernet ({switchName})` 이 명령을 실행 하려고 할 때 오류가 발생 하는 경우에는 매개 변수 값을 변경 하 여 템플릿을 따르도록 해야 할 수 있습니다. 다음 예제와 유사한 출력이 표시 됩니다.

    :::image type="content" source="media/how-to-create-virtual-switch/get-netadapter-output.png" alt-text="인터페이스 인덱스 값을 강조 표시 하 여 Get-NetAdapter 명령을 실행 하는 출력의 스크린샷" lightbox="media/how-to-create-virtual-switch/get-netadapter-output.png":::

    이후 단계에서 사용 해야 하므로 인터페이스 인덱스 값을 기록해 둡니다.

5. 이전 단계의 인터페이스 인덱스를 사용 하 여 다음 [new-netipaddress](/powershell/module/nettcpip/get-netipaddress) 명령을 실행 하 고 자리 표시자 값을 대체 하 여 생성 된 스위치 네트워크 어댑터의 IP 주소를 가져옵니다.

    ```powershell
    Get-NetIPAddress -AddressFamily IPv4  -InterfaceIndex {interfaceIndex}
    ```

    이 명령을 실행 하면 다음 예제와 유사한 정보가 출력 됩니다.

    :::image type="content" source="media/how-to-create-virtual-switch/get-netipaddress-output.png" alt-text="Get-NetIPAddress 명령을 실행 하 여 IP 주소를 강조 표시 하는 출력의 스크린샷" lightbox="media/how-to-create-virtual-switch/get-netipaddress-output.png":::

    결과 가상 스위치 IP 주소는 각 환경 마다 다릅니다. 이 가이드의 나머지 명령은이 출력 된 주소에서 파생 되는 IP 주소를 더 많이 사용 하므로 IP 주소를 기록해 둡니다.

6. 다른 IP 주소에 대해서는 마지막 옥텟 (IP 주소의 각 점으로 구분 된 숫자)이 다른 값으로 대체 되는 변형을 만들어야 합니다. 다음 IP 주소를 만들고 사용 합니다.
    
   | IP 주소        | 템플릿        | 예제         |
   |-------------------|-----------------|-----------------|
   | 가상 스위치 IP | xxx. xxx. xxx. yyy | 169.254.229.39  |
   | 게이트웨이 IP        | xxx. xxx. xxx. 1   | 169.254.229.1   |
   | NAT IP            | xxx. xxx. xxx. 0   | 169.254.229.0   |
   | 시작 IP          | xxx. xxx. xxx. 100 | 169.254.229.100 |
   | 종료 IP            | xxx. xxx. xxx. 200 | 169.254.229.200 |

7. 가상 스위치 IP의 마지막 8 진수를 새 숫자 값 (예: 1)으로 바꿔 **게이트웨이 IP 주소** 를 설정 합니다. 다음 [new-netipaddress](/powershell/module/nettcpip/new-netipaddress) 명령을 실행 하 여 새 게이트웨이 IP 주소를 설정 하 고 자리 표시자 값을 바꿉니다.

    ```powershell
    New-NetIPAddress -IPAddress {gatewayIp} -PrefixLength 24 -InterfaceIndex {interfaceIndex}
    ```

    이 명령을 실행 하면 다음 예제와 유사한 정보가 출력 됩니다.

    :::image type="content" source="media/how-to-create-virtual-switch/new-netipaddress-output.png" alt-text="New-NetIPAddress 명령을 실행 한 출력의 스크린샷" lightbox="media/how-to-create-virtual-switch/new-netipaddress-output.png":::

8. 내부 네트워크 주소를 외부 네트워크로 변환하는 NAT(Network Address Translation) 개체를 만듭니다. 이전 단계와 동일한 IPv4 집합 주소를 사용합니다. 6 단계의 테이블을 기반으로 하는 **NAT ip 주소** 는 마지막 8 진수가 새 숫자 값 (예: 0)으로 대체 된다는 점을 제외 하 고 원래 가상 스위치 ip 주소에 해당 합니다. 다음 [새-NetNat](/powershell/module/netnat/new-netnat) 명령을 실행 하 여 NAT IP 주소를 설정 하 고 자리 표시자 값을 바꿉니다.

    ```powershell
    New-NetNat -Name "{switchName}" -InternalIPInterfaceAddressPrefix "{natIp}/24"
    ```

    이 명령을 실행 하면 다음 예제와 유사한 정보가 출력 됩니다.

    :::image type="content" source="media/how-to-create-virtual-switch/new-netnat-output.png" alt-text="New-NetNat 명령을 실행 한 출력의 스크린샷" lightbox="media/how-to-create-virtual-switch/new-netnat-output.png":::

이제 스위치가 생성 됩니다. 다음으로 DNS를 설정 합니다.

## <a name="create-dhcp-server"></a>DHCP 서버 만들기 

>[!WARNING]
>회사 네트워크 환경에서 DHCP 서버를 배포하려면 권한 부여가 필요할 수 있습니다. 가상 스위치 구성이 회사 네트워크의 정책을 준수하는지 확인합니다. 자세한 내용은 [Windows PowerShell를 사용 하 여 DHCP 배포](/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)를 참조 하세요. 

1. DHCP 서버 기능이 호스트 컴퓨터에 설치 되어 있는지 확인 하십시오. **설치 상태** 열을 찾습니다. 값이 "설치 됨" 인 경우에는 다음 단계를 건너뛸 수 있습니다.

    ```powershell
    Get-WindowsFeature -Name 'DHCP'
    ```

2. DHCP 서버가 아직 설치 되지 않은 경우 다음 명령을 실행 하 여 설치 합니다.

    ```powershell
    Install-WindowsFeature -Name 'DHCP' -IncludeManagementTools
    ```

3. DHCP 서버를 기본 로컬 보안 그룹에 추가하고 서버를 다시 시작합니다.

    ```powershell
    netsh dhcp add securitygroups
    Restart-Service dhcpserver
    ```

    DHCP 서버를 시작 하는 동안 다음과 같은 경고 메시지가 표시 됩니다. `WARNING: Waiting for service 'DHCP Server (dhcpserver)' to start...`

4. DHCP 서버에서 사용할 수 있는 ip 범위를 구성 하려면 IP 주소를 **시작 ip** 로 설정 하 고 ip 주소를 **끝 ip** 로 설정 해야 합니다. 이 범위는 **StartRange** 및 [DhcpServerv4Scope](/powershell/module/dhcpserver/add-dhcpserverv4scope) 명령의 **EndRange** 매개 변수에 의해 정의 됩니다. 이 명령을 실행할 때 서브넷 마스크도 설정 해야 합니다 .이는 255.255.255.0입니다. 이전 섹션의 테이블에 있는 IP 주소 템플릿 및 예제를 기준으로 **StartRange** 를 169.254.229.100로 설정 하 고 **EndRange** as 169.254.229.200를 설정 하면 100 IP 주소를 사용할 수 있게 됩니다. 다음 명령을 실행 하 여 자리 표시자를 사용자 고유의 값으로 바꿉니다.

    ```powershell
    Add-DhcpServerV4Scope -Name "AzureIoTEdgeScope" -StartRange {startIp} -EndRange {endIp} -SubnetMask 255.255.255.0 -State Active
    ```

    이 명령은 출력을 생성 하지 않습니다.

5. 이전 섹션에서 만든 **NAT** 및 **게이트웨이 IP** 주소를 DHCP 서버에 할당 하 고 서버를 다시 시작 하 여 구성을 로드 합니다. 첫 번째 명령은 출력을 생성 하지 않습니다. 그러나 DHCP 서버를 다시 시작 하면이 섹션의 세 번째 단계에서 받은 것과 동일한 경고 메시지가 출력 됩니다.

    ```powershell
    Set-DhcpServerV4OptionValue -ScopeID {natIp} -Router {gatewayIp}
    Restart-service dhcpserver
    ```

## <a name="next-steps"></a>다음 단계
[Windows 디바이스에서 Azure IoT Edge 설치 및 프로비저닝](how-to-provision-single-device-linux-on-windows-symmetric.md) 단계에 따라 Windows에서 Linux용 IoT Edge를 사용하여 디바이스를 설정합니다.
