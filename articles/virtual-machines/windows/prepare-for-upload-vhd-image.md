---
title: Azure에 업로드할 Windows VHD 준비
description: Windows VHD 또는 VHDX를 준비하여 Azure에 업로드하는 방법 알아보기
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: 92b4b495944fb6180e669c495d61b4594f3e4daf
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697198"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure에 업로드할 Windows VHD 또는 VHDX 준비

**적용 대상:** :heavy_check_mark: Windows VM 

온-프레미스에서 Azure로 Windows VM(가상 머신)을 업로드하려면 먼저 가상 하드 디스크(VHD 또는 VHDX)를 준비해야 합니다. Azure는 VHD 파일 형식이며 고정 크기 디스크가 있는 1세대 및 2세대 VM을 모두 지원합니다. 1세대 VM에서 OS VHD에 허용되는 최대 크기는 2TB입니다.

VHDX 파일을 VHD로 변환하여 동적 확장 디스크를 고정 크기 디스크로 변환할 수 있지만 VM의 생성은 변경할 수 없습니다. 자세한 내용은 [Hyper-V에 1세대 또는 2세대 가상 컴퓨터를 만들어야 하나요?](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) 및 [Azure의 2세대 VM 지원](../generation-2.md)을 참조하세요.

Azure VM의 지원 정책에 대한 내용은 [Azure VM의 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/)을 참조하세요.

> [!NOTE]
> 이 문서의 지침은 다음에 적용됩니다.
>
> - 64비트 버전의 Windows Server 2008 R2 이상 Windows Server 운영 체제. Azure에서 실행하는 32비트 버전 운영 체제에 대한 정보는 [Azure VM에서 32비트 운영 체제 지원](https://support.microsoft.com/help/4021388/)을 참조하세요.
> - Azure Site Recovery 또는 Azure Migrate와 같은 재해 복구 도구를 사용하여 워크로드를 마이그레이션하는 경우 마이그레이션 전에 이미지를 준비하려면 게스트 OS에서 이 프로세스가 계속 필요합니다.

## <a name="system-file-checker"></a>시스템 파일 검사기

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>OS 이미지를 일반화하기 전에 Windows 시스템 파일 검사기 유틸리티 실행

SFC(시스템 파일 검사기)를 사용하여 Windows 시스템 파일을 확인하고 바꿀 수 있습니다.

> [!IMPORTANT]
> 관리자 권한 PowerShell 세션을 사용하여 이 문서의 예제를 실행합니다.

SFC 명령을 실행합니다.

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

SFC 검사를 완료한 후 Windows 업데이트를 설치하고 컴퓨터를 다시 시작합니다.

## <a name="set-windows-configurations-for-azure"></a>Azure에 대한 Windows 구성 설정

> [!NOTE]
> 일반화된 이미지에서 Windows VM을 만들 경우 Azure 플랫폼은 ISO 파일을 DVD-ROM에 탑재합니다. 따라서 일반화된 이미지의 OS에서 DVD-ROM을 사용하도록 설정해야 합니다. 사용하지 않도록 설정된 경우 Windows VM은 OOBE(첫 실행 경험)에서 멈춥니다.

1. 라우팅 테이블에서 정적 영구 경로를 제거합니다.

   - 라우팅 테이블을 보려면 `route.exe print`를 실행합니다.
   - **지속성 경로** 섹션을 확인합니다. 지속성 경로가 있는 경우 `route.exe delete` 명령을 사용하여 경로를 제거합니다.

1. WinHTTP 프록시를 제거합니다.

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    VM이 특정 프록시와 작동해야 하는 경우 VM을 Azure에 연결할 수 있도록 Azure IP 주소([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md))에 프록시 예외를 추가합니다.

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. DiskPart를 엽니다.

   ```powershell
   diskpart.exe
   ```

   디스크 SAN 정책을 [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11))로 설정합니다.

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Windows의 UTC(협정 세계시) 시간을 설정합니다. 또한 Windows 시간 서비스인 **w32time** 의 시작 유형을 **자동** 으로 설정합니다.

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. 전원 프로필을 고성능으로 설정합니다.

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. **TEMP** 및 **TMP** 환경 변수가 기본값으로 설정되어 있는지 확인합니다.

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```
1. 레거시 운영 체제(Windows Server 2012 R2 또는 Windows 8.1 이하)가 설치된 VM의 경우 최신 Hyper-V 통합 구성 요소 서비스가 설치되어 있는지 확인합니다. 자세한 내용은 [Windows VM의 Hyper-V 통합 구성 요소 업데이트](https://support.microsoft.com/topic/hyper-v-integration-components-update-for-windows-virtual-machines-8a74ffad-576e-d5a0-5a2f-d6fb2594f990)를 참조하세요.

> [!NOTE]
> 온-프레미스 VMware 서버와 Azure 간의 재해 복구 솔루션으로 VM을 설정하는 시나리오에서는 Hyper-V 통합 구성 요소 서비스를 사용할 수 없습니다. 이 경우 VMware 지원에 문의하여 VM을 Azure로 마이그레이션하고 VMware 서버에 공존하도록 합니다.

## <a name="check-the-windows-services"></a>Windows 서비스 확인

다음의 각 Windows 서비스가 Windows 기본값으로 설정되어 있는지 확인합니다. 이러한 서비스는 VM 연결을 보장하기 위해 구성해야 하는 최소 서비스입니다. 시작 설정값을 설정하려면 다음 명령을 실행합니다.

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>원격 데스크톱 레지스트리 설정 업데이트

원격 액세스에 대해 다음 설정이 올바르게 구성되어 있는지 확인합니다.

> [!NOTE]
> `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>`를 실행할 때 오류 메시지를 수신하는 경우 무시해도 됩니다. 이는 도메인이 그룹 정책 개체를 통해 해당 구성을 설정하지 않음을 의미합니다.

1. RDP(원격 데스크톱 프로토콜)이 활성화되어 있습니다.

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. 기본 포트 3389를 사용하여 RDP 포트가 올바르게 설정되어 있습니다.

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   VM을 배포할 때 포트 3389의 기본 규칙이 생성됩니다. 포트 번호를 변경하려는 경우 Azure에서 VM을 배포한 후에 수행합니다.

1. 수신기가 모든 네트워크 인터페이스에서 수신 대기합니다.

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. RDP 연결의 NLA(네트워크 수준 인증) 모드를 구성합니다.

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. 연결 유지 값을 설정합니다.

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. 다시 연결 옵션을 설정합니다.

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. 동시 연결 수를 제한합니다.

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. RDP 수신기에 연결된 자체 서명된 인증서를 모두 제거합니다.

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   이 코드를 사용하면 VM을 배포할 때 연결할 수 있습니다. 또한 Azure에서 VM을 배포한 후에 이러한 설정을 검토할 수 있습니다.

1. VM이 도메인의 일부인 경우 다음 정책을 확인하여 이전 설정을 되돌리지 않도록 합니다.

    |                 목표                  |                                                                            정책                                                                            |                           값                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP 사용                        | Computer Configuration\Policies\Windows Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | 사용자가 원격 데스크톱을 사용하여 원격으로 연결하도록 허용    |
    | NLA 그룹 정책                      | Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | NLA를 사용하여 원격 액세스에 대한 사용자 인증 필요 |
    | 연결 유지 설정                   | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 연결 유지 연결 간격 구성                   |
    | 다시 연결 설정                    | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 자동으로 다시 연결                                    |
    | 제한된 수의 연결 설정 | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | 연결 수 제한                                |

## <a name="configure-windows-firewall-rules"></a>Windows 방화벽 규칙 구성

1. 세 개 프로필(도메인, 표준 및 공용)에서 Windows 방화벽을 켭니다.

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. 다음 예제를 실행하여 세 개의 방화벽 프로필(도메인, 프라이빗 및 공용)을 통해 WinRM을 허용하고 PowerShell 원격 서비스를 사용하도록 설정합니다.

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -Name WINRM-HTTP-In-TCP, WINRM-HTTP-In-TCP-PUBLIC -Enabled True
   ```

1. 다음 방화벽 규칙을 사용하도록 설정하여 RDP 트래픽을 허용합니다.

   ```powershell
   Set-NetFirewallRule -Group '@FirewallAPI.dll,-28752' -Enabled True
   ```

1. VM이 가상 네트워크 내의 ping 요청에 응답할 수 있도록 파일 및 프린터 공유에 대한 규칙을 사용하도록 설정합니다.

   ```powershell
   Set-NetFirewallRule -Name FPS-ICMP4-ERQ-In -Enabled True
   ```

1. Azure 플랫폼 네트워크의 규칙을 만듭니다.

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. VM이 도메인의 일부인 경우 다음 Azure AD 정책을 확인하여 이전 설정을 되돌리지 않도록 합니다.

    |                 목표                 |                                                                         정책                                                                          |                  값                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Windows 방화벽 프로필을 사용하도록 설정 | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | 모든 네트워크 연결 보호         |
    | RDP를 사용하도록 설정                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | 인바운드 원격 데스크톱 예외 허용 |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | 인바운드 원격 데스크톱 예외 허용 |
    | ICMP-V4를 사용하도록 설정                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | ICMP 예외 허용                   |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | ICMP 예외 허용                   |

## <a name="verify-the-vm"></a>VM 확인

VM이 정상이고 보안이 유지되고 있으며 RDP에 액세스할 수 있는지 확인합니다.

1. 디스크가 정상이고 일관되도록 하려면 다음 VM이 다시 시작할 때 디스크를 검사합니다.

   ```powershell
   chkdsk.exe /f
   ```

   보고서에 깨끗하고 정상적인 디스크가 표시되는지 확인합니다.

1. BCD(부팅 구성 데이터) 설정을 지정합니다.

   ```powershell
   cmd

   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200

   exit
   ```

1. 덤프 로그는 Windows 충돌 문제를 해결하는 데 도움이 될 수 있습니다. 덤프 로그 컬렉션을 사용하도록 설정합니다.

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. WMI(Windows Management Instrumentations) 리포지토리가 일관되는지 확인합니다.

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   리포지토리가 손상된 경우 [WMI: 리포지토리 손상 여부](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484)를 참조하세요.

1. TermService 이외의 다른 애플리케이션에서 포트 3389를 사용하고 있지 않은지 확인합니다. 이 포트는 Azure의 RDP 서비스에 사용됩니다. VM에서 사용되는 포트를 확인하려면 `netstat.exe -anob` 명령을 실행합니다.

   ```powershell
   netstat.exe -anob
   ```
   
   다음은 예제입니다.

   ```powershell
   netstat.exe -anob | findstr 3389
   TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING       4056
   TCP    [::]:3389              [::]:0                 LISTENING       4056
   UDP    0.0.0.0:3389           *:*                                    4056
   UDP    [::]:3389              *:*                                    4056

   tasklist /svc | findstr 4056
   svchost.exe                   4056 TermService
   ```

1. 다음을 수행하여 도메인 컨트롤러인 Windows VHD를 업로드합니다.

   - [이러한 추가 단계](https://support.microsoft.com/kb/2904015)에 따라 디스크를 준비합니다.

   - DSRM(디렉터리 서비스 복원 모드)에서 VM을 시작해야 하는 경우 DSRM 암호를 알고 있어야 합니다. 자세한 내용은 [DSRM 암호 설정](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11))을 참조하세요.

1. 기본 제공 관리자 계정 및 암호를 알고 있어야 합니다. 현재 로컬 관리자 암호를 다시 설정하고, 이 계정을 사용하여 RDP 연결을 통해 Windows에 로그인할 수 있는지 확인할 수 있습니다. 이 액세스 권한은 "원격 데스크톱 서비스를 통한 로그온 허용" 그룹 정책 개체에 의해 제어됩니다. 다음 위치의 로컬 그룹 정책 편집기에서 이 개체를 확인합니다.

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. 다음 Azure AD 정책을 확인하여 RDP 액세스를 블록하지 않는지 확인합니다.

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. 다음 Azure AD 정책을 확인하여 필요한 액세스 계정을 제거하고 있지 않은지 확인합니다.

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   정책은 다음 그룹을 나열해야 합니다.

   - 관리자

   - Backup Operators

   - 모든 사람

   - 사용자

1. VM을 다시 시작하여 Windows가 여전히 정상 상태인지와 RDP 연결을 통해 연결할 수 있는지 확인합니다. 이 시점에서 로컬 Hyper-V 서버에 VM을 만들어 VM이 완전히 시작되도록 고려합니다. 그런 다음 RDP를 통해 VM에 연결할 수 있는지 테스트합니다.

1. 모든 추가 TDI(Transport Driver Interface) 필터를 제거합니다. 예를 들어 TCP 패킷 또는 추가 방화벽을 분석하는 소프트웨어를 제거합니다.

1. 물리적 구성 요소 또는 다른 가상화 기술과 관련된 기타 타사 소프트웨어 또는 드라이버를 제거합니다.

### <a name="install-windows-updates"></a>Windows 업데이트 설치

> [!NOTE]
> VM을 프로비전하는 동안 실수로 다시 부팅하지 않으려면 모든 Windows 업데이트 설치를 완료하고 보류 중인 다시 시작이 없는지 확인하는 것이 좋습니다. 이를 수행하는 한 가지 방법은 모든 Windows 업데이트를 설치하고 Azure로 마이그레이션하기 전에 VM을 다시 부팅하는 것입니다. </br><br>
>OS(sysprep)의 일반화도 수행해야 하는 경우 Sysprep 명령을 실행하기 전에 Windows를 업데이트하고 VM을 다시 시작해야 합니다.

컴퓨터를 *패치 수준* 으로 계속 업데이트하는 것이 가장 좋지만 가능하지 않을 경우, 다음 업데이트가 설치되어 있는지 확인합니다. 최신 업데이트를 다운로드하려면 Windows 업데이트 기록 페이지([Windows 10 및 Windows Server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1, 및 Windows Server 2012 R2](https://support.microsoft.com/help/4009470), [Windows 7 SP1 및 Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469))를 참조하세요.

<br />

|        구성 요소        |     이진     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| 스토리지                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| 네트워크                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| 핵심                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| 원격 데스크톱 서비스 | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| 보안                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> VM을 프로비전하는 동안 실수로 다시 부팅하지 않으려면 모든 Windows 업데이트 설치를 완료하고 보류 중인 업데이트가 없는지 확인하는 것이 좋습니다. 이를 수행하는 한 가지 방법은 가능한 모든 Windows 업데이트를 설치하고 `sysprep.exe` 명령을 실행하기 전에 한 번 다시 부팅하는 것입니다.

## <a name="determine-when-to-use-sysprep"></a>Sysprep 사용 시기 결정

시스템 준비 도구(`sysprep.exe`)는 Windows 설치를 다시 설정하기 위해 실행할 수 있는 프로세스입니다.
Sysprep은 모든 개인 데이터를 제거하고 여러 구성 요소를 다시 설정하여 "첫 실행" 경험을 제공합니다.

일반적으로 `sysprep.exe`를 실행하여 특정 구성이 있는 여러 다른 VM을 배포할 수 있는 템플릿을 만듭니다. 이 템플릿을 *일반화된 이미지* 라고 합니다.

하나의 디스크에서 하나의 VM만 만들려면 Sysprep을 사용할 필요가 없습니다. 대신 *특수화된 이미지* 에서 VM을 만들 수 있습니다. 특수화된 디스크에서 VM을 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [특수화된 디스크에서 VM 만들기](create-vm-specialized.md)
- [특수화된 VHD 디스크에서 VM 만들기](./create-vm-specialized-portal.md)

일반화된 이미지를 만들려면 Sysprep을 실행해야 합니다. 자세한 내용은 [Sysprep 사용 방법: 소개](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10))를 참조하세요.

Windows 기반 컴퓨터에 설치된 모든 역할 또는 애플리케이션이 일반화된 이미지를 지원하는 것은 아닙니다. 이 프로시저를 따르기 전에 Sysprep에서 컴퓨터의 역할을 지원하는지 확인합니다. 자세한 내용은 [Sysprep의 서버 역할 지원](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

특히 Sysprep은 실행하기 전에 드라이브를 완전히 해독해야 합니다. VM에서 암호화를 사용하도록 설정한 경우 Sysprep을 실행하기 전에 사용하지 않도록 설정합니다.


### <a name="generalize-a-vhd"></a>VHD 일반화

>[!NOTE]
> 기존 Azure VM에서 일반화된 이미지를 만드는 경우 sysprep을 실행하기 전에 VM 확장을 제거하는 것이 좋습니다.

>[!NOTE]
> 다음 단계에서 `sysprep.exe`를 실행한 후 VM을 끕니다. Azure에서 이미지를 만들기 전까지 다시 켜면 안 됩니다.

1. Windows VM에 로그인합니다.
1. 관리자로 PowerShell 세션을 실행합니다.
1. Panther 디렉터리(C:\Windows\Panther)를 삭제합니다.
1. 디렉터리를 `%windir%\system32\sysprep`으로 변경합니다. 그런 다음, `sysprep.exe`을 실행합니다.
1. **시스템 준비 도구** 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력** 을 선택하고 **일반화** 확인란이 선택되어 있는지 확인합니다.

    ![시스템 준비 도구](media/prepare-for-upload-vhd-image/syspre.png)
1. **종료 옵션** 에서 **종료** 를 선택합니다.
1. **확인** 을 선택합니다.
1. Sysprep이 완료되면 VM을 종료합니다. VM을 종료할 때 **다시 시작** 을 사용해서는 안 됩니다.

이제 VHD를 업로드할 수 있습니다. 일반화된 디스크에서 VM을 만드는 방법에 대한 자세한 내용은 [일반화된 VHD를 업로드하고 이를 사용하여 Azure에서 새 VM 만들기](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized)를 참조하세요.

>[!NOTE]
> 사용자 지정 *unattend.xml* 파일은 지원되지 않습니다. **additionalUnattendContent** 속성은 지원하지만 Azure 프로비저닝 에이전트가 사용하는 *unattend.xml* 파일에 [microsoft-windows-shell-setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) 옵션을 추가하기 위한 제한된 지원만 제공됩니다. 예를 들어, [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent)를 사용하여 FirstLogonCommands 및 LogonCommands를 추가할 수 있습니다. 자세한 내용은 [additionalUnattendContent FirstLogonCommands 예제](https://github.com/Azure/azure-quickstart-templates/issues/1407)를 참조하세요.

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>가상 디스크를 고정 크기 VHD로 변환

이 섹션의 방법 중 하나를 사용하여 가상 디스크를 Azure에 필요한 형식으로 변환하고 크기를 조정합니다.

1. 가상 디스크 변환 또는 크기 조정 프로세스를 실행하기 전에 VM을 백업합니다.

1. 로컬 서버에서 Windows VHD가 올바르게 작동하는지 확인합니다. Azure로 변환하거나 업로드하기 전에 VM 자체 내에서 오류를 해결해 보세요.

1. 가상 디스크를 고정 형식으로 변환합니다.

1. 다음 Azure 요구 사항에 맞게 가상 디스크의 크기를 조정합니다.

   1. Azure의 디스크는 가상 크기가 1MiB로 맞추어져야 합니다. VHD가 1MiB의 분수인 경우 디스크 크기를 1MiB의 배수로 조정해야 합니다. 디스크가 MiB의 분수이면 업로드된 VHD에서 이미지를 만들 때 오류가 발생합니다. 크기를 확인하려면 PowerShell [Get-VHD](/powershell/module/hyper-v/get-vhd) cmdlet을 사용하여 Azure에서 1MiB의 배수여야 하는 "Size"와 VHD 바닥글의 "Size"에 512바이트를 더한 값인 "FileSize"를 표시할 수 있습니다.
   
      ```powershell
      $vhd = Get-VHD -Path C:\test\MyNewVM.vhd
      $vhd.Size % 1MB
      0
      $vhd.FileSize - $vhd.Size
      512
      ```
   
   1. 1세대 VM에서 OS VHD에 허용되는 최대 크기는 2048GiB(2TiB)입니다. 
   1. 데이터 디스크의 최대 크기는 32767GiB(32TiB)입니다.

> [!NOTE]
> - 고정 디스크로 변환하고 필요에 따라 크기를 조정한 후 Windows OS 디스크를 준비하는 경우 디스크를 사용하는 VM을 만듭니다. VM을 시작하고 로그인한 후 이 문서의 섹션을 계속 진행하여 업로드 준비를 마칩니다.  
> - 데이터 디스크를 준비하는 경우 이 섹션에서 중지하고 디스크 업로드를 진행할 수 있습니다.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-V 관리자를 사용하여 디스크 변환

1. Hyper-V 관리자를 열고 왼쪽에서 로컬 컴퓨터를 선택합니다. 컴퓨터 목록 위의 메뉴에서 **작업** > **디스크 편집** 을 선택합니다.
1. **가상 하드 디스크 찾기** 페이지에서 가상 디스크를 선택합니다.
1. **작업 선택** 페이지에서 **변환** > **다음** 을 선택합니다.
1. VHDX에서 변환하려면 **VHD** > **다음** 을 선택합니다.
1. 동적 확장 디스크에서 변환하려면 **고정 크기** > **다음** 을 선택합니다.
1. 새 VHD 파일을 저장할 경로를 찾아 선택합니다.
1. **마침** 을 선택합니다.

### <a name="use-powershell-to-convert-the-disk"></a>PowerShell을 사용하여 디스크 변환

PowerShell의 [Convert-VHD](/powershell/module/hyper-v/convert-vhd) cmdlet을 사용하여 가상 디스크를 변환할 수 있습니다. 이 cmdlet을 설치하는 방법에 대한 정보가 필요한 경우 [Hyper-V 역할 설치](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)를 참조하세요.

다음 예에서는 디스크를 VHDX에서 VHD로 변환합니다. 또한 디스크를 동적 확장 디스크에서 고정 크기 디스크로 변환합니다.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

이 예에서는 **Path** 값을 변환할 가상 하드 디스크의 경로로 바꿉니다. **DestinationPath** 값을 변환 디스크의 새 경로 및 이름으로 바꿉니다.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Hyper-V 관리자를 사용하여 디스크 크기 조정

1. Hyper-V 관리자를 열고 왼쪽에서 로컬 컴퓨터를 선택합니다. 컴퓨터 목록 위의 메뉴에서 **작업** > **디스크 편집** 을 선택합니다.
1. **가상 하드 디스크 찾기** 페이지에서 가상 디스크를 선택합니다.
1. **작업 선택** 페이지에서 **확장** > **다음** 을 선택합니다.
1. **가상 하드 디스크 찾기** 페이지에서 GiB > **다음** 에 새 크기를 입력합니다.
1. **마침** 을 선택합니다.

### <a name="use-powershell-to-resize-the-disk"></a>PowerShell을 사용하여 디스크 크기 조정

PowerShell에서 [Resize-VHD](/powershell/module/hyper-v/resize-vhd) cmdlet을 사용하여 가상 디스크의 크기를 조정할 수 있습니다. 이 cmdlet을 설치하는 방법에 대한 정보가 필요한 경우 [Hyper-V 역할 설치](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)를 참조하세요.

다음 예제에서는 Azure 맞춤 요구 사항을 충족하기 위해 100.5MiB에서 101MiB로 디스크 크기를 조정합니다.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

이 예에서는 **Path** 값을 크기를 조정할 가상 하드 디스크의 경로로 바꿉니다. **SizeBytes** 값을 디스크의 새 크기(바이트)로 바꿉니다.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK 디스크 형식에서 변환

[VMDK 파일 형식](https://en.wikipedia.org/wiki/VMDK)의 Windows VM 이미지가 있는 경우 [Azure Migrate](../../migrate/server-migrate-overview.md)를 사용하여 VMDK를 변환하고 이를 Azure에 업로드할 수 있습니다.

## <a name="complete-the-recommended-configurations"></a>권장 구성 완료

다음 설정은 VHD 업로드에 영향을 주지 않습니다. 단, 반드시 구성하는 것이 좋습니다.

- [Azure 가상 머신 에이전트](https://go.microsoft.com/fwlink/?LinkID=394789)를 설치합니다. 그런 다음 VM 확장을 사용하도록 설정할 수 있습니다. VM 확장은 VM에서 사용하려는 대부분의 중요 기능을 구현합니다. 예를 들어 암호를 다시 설정하거나 RDP를 구성하려면 확장이 필요합니다. 자세한 내용은 [Azure 가상 머신 에이전트 개요](../extensions/agent-windows.md)를 참조하세요.
- Azure에서 VM을 만든 후에는 성능 향상을 위해 *임시 드라이브 볼륨* 에 페이지 파일을 배치하는 것이 좋습니다. 다음과 같이 파일 배치를 설정할 수 있습니다.

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  데이터 디스크가 VM에 연결되어 있으면 임시 드라이브 볼륨의 문자는 일반적으로 *D* 입니다. 이 지정은 설정 및 사용 가능한 드라이브의 수에 따라 달라질 수 있습니다.

  - 바이러스 백신 소프트웨어에서 제공될 수 있는 스크립트 블로커를 사용하지 않도록 설정하는 것이 좋습니다. 이미지에서 새 VM을 배포할 때 실행되는 Windows 프로비전 에이전트 스크립트를 방해하고 차단할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Resource Manager 배포를 위해 Azure에 Windows VM 이미지 업로드](upload-generalized-managed.md)
- [Azure Windows VM 활성화 문제 해결](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)
