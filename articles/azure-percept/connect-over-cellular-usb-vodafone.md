---
title: 커넥트 USB 모뎀 Vodafone을 통해 5G 또는 LTE를 통해 Azure Percept
description: 이 문서에서는 Vodafone USB 모뎀을 사용하여 5G 또는 LTE 네트워크를 통해 Azure Percept DK 연결하는 방법을 설명합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 6264bd855cdc4ff186f51748bf426573e6316e5c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008686"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-vodafone-usb-connect-4g"></a>커넥트 USB 모뎀 Vodafone USB 연결 4G를 통해 LTE를 통해 Azure Percept
Vodafone USB 커넥트 4G v2를 사용하여 Azure Percept 연결하는 단계는 다음과 같습니다.
이 특정 모뎀 HW에 대한 자세한 내용은 Vodafone에서 다음 페이지를 참조하세요. https://www.vodafone.com/business/iot/iot-devices/integrated-terminals

## <a name="using-the-modem-to-connect"></a>모뎀을 사용하여 연결
[USB 모뎀을 사용하여 연결에서](./connect-over-cellular-usb.md)Azure Percept DK 준비를 완료했는지 확인합니다. USB 모뎀 자체에 대한 준비는 필요하지 않습니다.   

**1. Vodafone 모뎀에 SIM 카드 연결**

**2. Azure Percept USB A 포트에 Vodafone 모뎀 연결**

**3. 전원 켜기 Azure Percept**

**4. Azure Percept DK SSH**

**5. ModemManager가 실행 중인지 확인**

SSH 프롬프트에 다음 명령을 씁니다.
```
systemctl status ModemManager
```
모두 정상이면 다음과 같은 내용이 있습니다.

*ModemManager.service - Modem Manager* 
 *로드됨: 로드됨(/lib/systemd/system/ModemManager.service; enabled; vendor preset: enabled)* 
 *활성: 활성(실행 중) 이후 Mon 2021-08-09 20:52:03 UTC; 23 s ago*

**6. 활성 모뎀 나열**

그런 다음, ModemManager가 모뎀을 인식할 수 있는지 확인해 보겠습니다.
```
mmcli --list-modems
```
모뎀 ID가 인 경우 다음과 같은 내용이 `0` 있지만 다른 것일 수 있습니다.

 */org/freedesktop/ModemManager1/Modem/0 [Alcatel] Mobilebroadband*

**7. 모뎀 세부 정보 얻기**

모뎀 상태 세부 정보를 얻으려면 아래 명령 및 모뎀 ID를 사용할 수 있습니다. `0`
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
를 아직 사용하지 않는 경우 설정할 수 있는 기본 설정인 로 시작하는 것이 `Modes: current: allowed: 2g, 3g, 4g; preferred: 2g` `mmcli --modem 0 --set-allowed-modes='2g|3g|4g' --set-preferred-mode='2g'` 좋습니다.

**8. 모뎀 사용**

연결을 설정하기 전에 모뎀의 라디오를 켜야 합니다.
```
mmcli --modem 0 --enable
```
그리고 다음과 같은 응답이 표시됩니다. *모뎀을 사용하도록 설정했습니다.*

잠시 후 모뎀이 셀타워에 등록되어야 하며, 다시 실행하는 경우 모뎀 상태가 표시됩니다. `Status -> state: registered`
```
mmcli --modem 0
```

**9. APN 정보를 사용하여 커넥트**

Access Point Name=APN은 Vodafone에 대해 다음과 같이 휴대폰 공급자가 제공합니다.
```
mmcli --modem 0 --simple-connect="apn=internet4gd.gdsp"  
```
그리고 모두 정상이면 *모뎀에 성공적으로 연결됩니다.*

**10. 모뎀 상태 확인**

`Status -> state: connected`이제 상태 메시지 끝에 새 `Bearer` 범주가 표시됩니다.
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

**11. Bearer 세부 정보 얻기**

모뎀이 셀룰러 네트워크를 통해 설정된 패킷 데이터 연결에 OS를 연결하려면 Bearer 세부 정보가 필요합니다. 따라서 이 시점에서 모뎀에는 IP 연결이 있지만 OS는 아직 사용하도록 구성되지 않았습니다.

```
mmcli --bearer 0
```
나열된 Bearer 세부 정보:
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

**12. 네트워크 인터페이스 가져오기**

```
sudo ip link set dev wwan0 up
```

**13. 네트워크 인터페이스 구성**

전달자 세부 정보에서 제공하는 정보를 사용하여 IP 주소(여기 162.177.2.0/22)를 전달자가 가지고 있는 IP 주소로 대체합니다.
```
sudo ip address add 162.177.2.0/22 dev wwan0
```

**14. IP 정보 확인**

이 인터페이스의 IP 구성은 ModemManager bearer 세부 정보와 일치해야 합니다.
```
sudo ip address show dev wwan0
```
그리고 아래와 같이 Bearer IP가 나열되어 있는지 확인합니다.
```
wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether c2:12:44:c4:27:3c brd ff:ff:ff:ff:ff:ff
    inet 162.177.2.0/22 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::c012:44ff:fec4:273c/64 scope link 
       valid_lft forever preferred_lft forever
```

**15. 기본 경로 설정**

안내자가 제공한 정보를 다시 사용하고 모뎀의 게이트웨이(162.177.2.1 대체)를 네트워크 패킷의 기본 대상으로 사용합니다.
```
sudo ip route add default via 162.177.2.1 dev wwan0
```
이제 Azure Percept LTE 모뎀을 사용하여 Azure에 연결할 수 있도록 설정해야 합니다.


**16. 연결 테스트**

`ping`인터페이스를 통해 요청을 `wwan0` 실행합니다. 그러나 Azure Percept Studio 사용하여 원격 분석 메시지가 제공되는지 확인할 수도 있습니다(LTE를 사용하도록 이더넷 케이블 또는 Wi-Fi 사용하도록 설정되어 있지 않은지 확인).
```
ping -I wwan0 8.8.8.8
```
그리고 다음을 수행해야 합니다.
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
일반적으로 USB [모뎀을 사용하는 커넥트 참조하세요.](./connect-over-cellular-usb.md)
   
### <a name="vodafone-modem-rules-to-mitigate-enumeration-issues"></a>열거형 문제를 완화하기 위한 Vodafone 모뎀 규칙

모뎀이 지원되지 않는 모드에서 열거되지 않도록 하려면 아래 UDEV 규칙을 사용하여 ModemManager가 원치 않는 인터페이스를 무시하도록 하는 것이 좋습니다.

만들 파일: `/usr/lib/udev/rules.d/77-mm-vodafone-port-types.rules` , 콘텐츠:
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
설치되면 UDEV 규칙을 다시 로드하고 ModemManager를 다시 시작합니다.
```
udevadm control -R
systemctl restart ModemManager
```
## <a name="next-steps"></a>다음 단계
[USB 모뎀 을 사용하는 커넥트.](./connect-over-cellular-usb.md)

5G 또는 LTE의 기본 문서로 돌아갑니다.

[5G 또는 LTE를 사용하는 커넥트.](./connect-over-cellular.md)
