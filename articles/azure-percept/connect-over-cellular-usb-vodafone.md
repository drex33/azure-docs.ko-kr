---
title: Vodafone USB 모뎀을 사용하여 5G 및 LTE를 통해 커넥트 Azure Percept DK
description: 이 문서에서는 Vodafone USB 모뎀을 사용하여 5G 및 LTE 네트워크를 통해 Azure Percept DK 연결하는 방법을 설명합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 226fc6890253bd4701b400dcd42c420618701630
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252367"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-by-using-a-vodafone-usb-connect-4g-v2-modem"></a>Vodafone USB 커넥트 4G v2 모뎀을 사용하여 5G 및 LTE를 통해 커넥트 Azure Percept DK

이 문서에서는 Vodafone USB 커넥트 4G v2 모뎀을 사용하여 Azure Percept DK 연결하는 방법을 설명합니다.

이 모뎀에 대한 자세한 내용은 [Vodafone 통합 터미널 페이지로](https://www.vodafone.com/business/iot/iot-devices/integrated-terminals) 이동합니다.

## <a name="use-the-modem-to-connect"></a>모뎀을 사용하여 연결

시작하기 전에 [USB 모뎀 을 사용하여 연결할](./connect-over-cellular-usb.md)Azure Percept DK 준비했는지 확인합니다. USB 모뎀 자체에 대한 준비는 필요하지 않습니다.   

1. SIM 카드를 Vodafone 모뎀에 연결합니다.

1. vodafone 모뎀을 Azure Percept USB A 포트에 연결합니다.

1. 전원 Azure Percept DK.

1. SSH(Secure Shell) 네트워크 프로토콜을 사용하여 Azure Percept DK 커넥트.

1. SSH 프롬프트에 다음 명령을 작성하여 ModemManager가 실행 중인지 확인합니다.

    ```
    systemctl status ModemManager
    ```

    성공하면 다음과 유사한 결과가 표시됩니다.

    ```
    ModemManager.service - Modem Manager
    Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)
    Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23 s ago
    ```

1. 활성 모뎀을 나열합니다.

    ModemManager가 모뎀을 인식할 수 있는지 확인하려면 다음을 실행합니다.

    ```
    mmcli --list-modems
    ```

    다음과 유사한 결과가 생성되어야 합니다. 여기서 모뎀 ID는 `0` 이지만 결과가 다를 수 있습니다.

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband
    ```

1. 모뎀 세부 정보를 얻습니다.

    모뎀 상태 세부 정보를 얻으려면 다음 명령을 실행합니다(여기서 modem ID는 `0` ).

    ```
    mmcli --modem 0
    ```

    기본적으로 모뎀은 사용하지 않도록 `Status -> state: disabled` 설정됩니다().

    ```
      --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0
              |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
      --------------------------------
      Hardware |         manufacturer: Alcatel
              |                model: Mobilebroadband
              |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
              |       carrier config: default
              |         h/w revision: 0
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
              |              drivers: option, cdc_mbim
              |               plugin: generic
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
              |                       ttyUSB2 (at), wwan0 (net)
      --------------------------------
      Status   |       unlock retries: sim-pin2 (3)
              |                state: disabled
              |          power state: on
              |       signal quality: 0% (cached)
      --------------------------------
      Modes    |            supported: allowed: 2g; preferred: none
              |                       allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 2g, 3g; preferred: 3g
              |                       allowed: 2g, 3g; preferred: 2g
              |                       allowed: 2g, 4g; preferred: 4g
              |                       allowed: 2g, 4g; preferred: 2g
              |                       allowed: 3g, 4g; preferred: 4g
              |                       allowed: 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 4g
              |                       allowed: 2g, 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 2g
              |              current: allowed: 2g, 3g, 4g; preferred: 2g
      --------------------------------
      Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
              |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
    ```

    기본 설정으로 시작하는 것이 좋습니다. 
    
    `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g`. 
    
    이 설정을 아직 사용하지 않는 경우 다음을 실행합니다.
    
     `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'`.

1. 모뎀을 사용하도록 설정합니다.

    연결을 설정하기 전에 다음 코드를 실행하여 모뎀의 라디오 또는 라디오를 켭니다.

    ```
    mmcli --modem 0 --enable
    ```

    "모뎀을 사용하도록 설정했습니다."와 같은 응답이 표시됩니다.

    잠시 후 모뎀을 셀타워에 등록해야 `Status -> state: registered` 하며, 다음 코드를 실행한 후 모뎀 상태가 표시됩니다.

    ```
    mmcli --modem 0
    ```

1. APN(액세스 지점 이름) 정보를 사용하여 커넥트.

    휴대폰 공급자는 Vodafone에 대해 다음 APN과 같은 APN을 제공합니다.

    ```
    mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
    ```

    "모뎀을 성공적으로 연결했습니다."와 같은 응답이 표시됩니다.

1. 모뎀 상태를 확인합니다.

    이제 상태 메시지의 끝에 상태 `Status -> state: connected` 및 새 `Bearer` 범주가 표시됩니다.

    ```
    mmcli --modem 0
    ```

    ```
      --------------------------------
      General  |                 path: /org/freedesktop/ModemManager1/Modem/0-mobile.
              |            device id: 20a6021958444bcb6f6589b47fd264932c340e69
      --------------------------------
      Hardware |         manufacturer: Alcatel
              |                model: Mobilebroadband
              |    firmware revision: MPSS.JO.2.0.2.c1.7-00004-9607_
              |       carrier config: default
              |         h/w revision: 0
              |            supported: gsm-umts, lte
              |              current: gsm-umts, lte
              |         equipment id: xxx
      --------------------------------
      System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.2
              |              drivers: option, cdc_mbim
              |               plugin: generic
              |         primary port: cdc-wdm0
              |                ports: cdc-wdm0 (mbim), ttyUSB0 (at), ttyUSB1 (qcdm), 
              |                       ttyUSB2 (at), wwan0 (net)
      --------------------------------
      Numbers  |                  own: xxx
      --------------------------------
      Status   |       unlock retries: sim-pin2 (10)
              |                state: connected
              |          power state: on
              |          access tech: lte
              |       signal quality: 19% (recent)
      --------------------------------
      Modes    |            supported: allowed: 2g; preferred: none
              |                       allowed: 3g; preferred: none
              |                       allowed: 4g; preferred: none
              |                       allowed: 2g, 3g; preferred: 3g
              |                       allowed: 2g, 3g; preferred: 2g
              |                       allowed: 2g, 4g; preferred: 4g
              |                       allowed: 2g, 4g; preferred: 2g
              |                       allowed: 3g, 4g; preferred: 4g
              |                       allowed: 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 4g
              |                       allowed: 2g, 3g, 4g; preferred: 3g
              |                       allowed: 2g, 3g, 4g; preferred: 2g
              |              current: allowed: 2g, 3g, 4g; preferred: 2g
      --------------------------------
      Bands    |            supported: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
              |              current: egsm, dcs, pcs, g850, utran-4, utran-5, utran-2, eutran-2, 
              |                       eutran-4, eutran-5, eutran-7, eutran-12, eutran-13, eutran-71
      --------------------------------
      IP       |            supported: ipv4, ipv6, ipv4v6
      --------------------------------
      3GPP     |                 imei: xxxxxxxxxxxxxxx
              |        enabled locks: fixed-dialing
              |          operator id: 302220
              |        operator name: TELUS
              |         registration: roaming
      --------------------------------
      3GPP EPS | ue mode of operation: csps-2
      --------------------------------
      SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
      --------------------------------
      Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
    ```

1. Bearer 세부 정보를 얻습니다.

    모뎀이 셀룰러 네트워크를 통해 설정된 패킷 데이터 연결에 운영 체제를 연결하려면 수신자 세부 정보가 필요합니다. 이 시점에서 모뎀에는 IP 연결이 있지만 운영 체제는 아직 사용하도록 구성되지 않았습니다.

    ```
    mmcli --bearer 0
    ```

    다음 코드에는 bearer 세부 정보가 나열됩니다.

    ```
      --------------------------------
      General            |       path: /org/freedesktop/ModemManager1/Bearer/0
                        |       type: default
      --------------------------------
      Status             |  connected: yes
                        |  suspended: no
                        |  interface: wwan0
                        | ip timeout: 20
      --------------------------------
      Properties         |        apn: internet4gd.gdsp
                        |    roaming: allowed
      --------------------------------
      IPv4 configuration |     method: static
                        |    address: 162.177.2.0
                        |     prefix: 22
                        |    gateway: 162.177.2.1
                        |        dns: 10.177.0.34, 10.177.0.210
                        |        mtu: 1500
      --------------------------------
      Statistics         |   attempts: 1
    ```

1. 네트워크 인터페이스를 불러올 수 있습니다.

    ```
    sudo ip link set dev wwan0 up
    ```

1. 네트워크 인터페이스를 구성합니다.

    전달자가 제공한 정보를 사용하여 IP 주소(예: 여기서 162.177.2.0/22 사용)를 전달자가 가지고 있는 IP 주소로 대체합니다.

    ```
    sudo ip address add 162.177.2.0/22 dev wwan0
    ```

1. IP 정보를 확인합니다.

    이 인터페이스의 IP 구성은 ModemManager bearer 세부 정보와 일치해야 합니다. 다음을 실행합니다.

    ```
    sudo ip address show dev wwan0
    ```

    사용자 IP는 다음과 같이 나열됩니다.

    ```
    wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
        inet 162.177.2.0/22 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::c012:44ff:fec4:273c/64 scope link 
          valid_lft forever preferred_lft forever
    ```

1. 기본 경로를 설정합니다.

    다시 말하지만, 안내자가 제공한 정보를 사용하고 모뎀의 게이트웨이(162.177.2.1 대체)를 네트워크 패킷의 기본 대상으로 사용하여 다음을 실행합니다.

    ```
    sudo ip route add default via 162.177.2.1 dev wwan0
    ```

    이제 Azure Percept DK LTE 모뎀을 사용하여 Azure에 연결할 수 있습니다.


1. 연결을 테스트합니다.

    이 문서에서는 `ping` 인터페이스를 통해 요청을 실행합니다. `wwan0` 그러나 Azure Percept Studio 사용하여 원격 분석 메시지가 도착하는지 확인할 수도 있습니다. 이더넷 케이블을 사용하지 않고 Wi-Fi LTE를 사용하도록 설정되지 않았는지 확인합니다. 다음을 실행합니다.

    ```
    ping -I wwan0 8.8.8.8
    ```

    다음과 유사한 결과가 생성되어야 합니다.

    ```
    PING 8.8.8.8 (8.8.8.8) from 162.177.2.0 wwan0: 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=111 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=92.0 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=114 time=88.8 ms
    ^C
    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 4ms
    rtt min/avg/max/mdev = 88.779/97.254/110.964/9.787 ms
    ```

## <a name="debugging"></a>디버깅

디버깅에 대한 일반적인 내용은 [USB 모뎀을 사용하여 커넥트 참조하세요.](./connect-over-cellular-usb.md)
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>열거형 문제를 완화하기 위한 Vodafone 모뎀 규칙

모뎀이 지원되지 않는 모드에서 열거되지 않도록 하려면 다음 userspace/dev(udev) 규칙을 적용하여 ModemManager가 원치 않는 인터페이스를 무시하도록 하는 것이 좋습니다.

다음 콘텐츠를 사용하여 */usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules* 파일을 만듭니다.

```
ACTION!="add|change|move|bind", GOTO="mm_vodafone_port_types_end"
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1bbb", GOTO="mm_vodafone_generic_vendorcheck"
GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_generic_vendorcheck"
SUBSYSTEMS=="usb", ATTRS{bInterfaceNumber}=="?*", ENV{.MM_USBIFNUM}="$attr{bInterfaceNumber}"

# Interface 1 is QDCM (ignored) and interfaces 3 and 4 are MBIM Control and Data.
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="00", ENV{ID_MM_PORT_TYPE_AT_PRIMARY}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="01", ENV{ID_MM_PORT_IGNORE}="1"
ATTRS{idVendor}=="1bbb", ATTRS{idProduct}=="00b6", ENV{.MM_USBIFNUM}=="02", ENV{ID_MM_PORT_AT_SECONDARY}="1"

GOTO="mm_vodafone_port_types_end"

LABEL="mm_vodafone_port_types_end"
```

설치가 끝나면 udev 규칙을 다시 로드하고 ModemManager를 다시 시작합니다.

```
udevadm control -R
systemctl restart ModemManager
```

## <a name="next-steps"></a>다음 단계

액세스 권한이 있는 셀룰러 디바이스에 따라 다음 두 가지 방법 중 하나로 연결할 수 있습니다.

* [USB 모뎀을 사용하여 커넥트](./connect-over-cellular-usb.md)
* [5G 또는 LTE를 사용하여 커넥트](./connect-over-cellular.md)
