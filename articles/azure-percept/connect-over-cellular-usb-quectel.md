---
title: 커넥트 USB 모뎀과 함께 5G 또는 LTE를 사용 하는 Azure Percept Quectel RM500
description: 이 문서에서는 quectel USB 모뎀을 사용 하 여 5g 또는 LTE 네트워크를 통해 Azure Percept DK을 연결 하는 방법을 설명 합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 52914140f80bad9c8bd8bb57bec0938211c25e71
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008689"
---
# <a name="connect-azure-percept-over-5g-or-lte-with-usb-modem-quectel-5g-rm500-gl"></a>커넥트 USB 모뎀과 5G 또는 LTE를 통한 Azure Percept 

Azure Percept를 사용 하 여 Azure를 연결 하는 방법에 대 한 단계는 RM500-GL 5G 모뎀. 이 5G 모뎀 개발자 키트에 대 한 자세한 내용은 다음을 참조 하세요.

northamerica-sales@quectel.com – NA 고객의 경우

sales@quectel.com – 글로벌 고객용

> [!Note] 
> 5G 모뎀 및 USB 케이블 정보이는 5G 모뎀에만 적용 됩니다. 5G 모뎀은 LTE 모뎀 보다 더 많은 전력이 필요 합니다. 또한 USB 케이블은 가능한 최고 5G 데이터 요금에 대해 병목 상태가 될 수 있습니다. 5G 모뎀에 충분 하 고 일관 된 전원을 제공 하기 위해 USB 케이블이 너무 길지 않은지 확인 하 고 3A 이상 전류를 견딜 수 있습니다. 최대 처리량을 위해 USB 3.1 Gen2 케이블을 사용 하 고 인증을 나타내는 USB-IF 로고가 있는지 확인 해야 합니다. 이 **에 대 한 요약: Power:**
> - 최대 amperage은 3 Amp 보다 크거나 같아야 합니다.
> - 1 m 미만
> - 5g 모뎀을 사용 하는 경우 Azure Percept DK의 USB 포트 하나만 활성화 해야 합니다.
>  
> **처리량:**
> - USB 3.1 Gen2
> - USB-인증 된 경우

## <a name="preparation"></a>준비
여기에서 Azure Percept 준비를 완료 했는지 확인 하 고 [usb 모뎀을 사용 하 여 연결](./connect-over-cellular-usb.md) 하 고 사용 해야 하는 usb 케이블에 대 한 설명을 적어 두어야 합니다. 

### <a name="preparation-of-the-modem"></a>모뎀 준비
시작 하려면 모뎀이 MBIM 모드에 있어야 합니다. 문서화 되지 않았지만 표준에 대 한 명령을 사용할 수 `AT+QCFG="usbnet"` 있습니다.

`usbnet`속성은에서로 네 가지 값으로 설정할 수 있습니다 `0` `3` .
- `0`**NDIS/PPP/QMI 모드** (드라이버에서 지원 `qmi_wwan` , 사용 하도록 설정 됨 `CONFIG_USB_NET_QMI_WWAN=y|m` )
- `1`**CDC 이더넷 모드** 의 경우 (경우 Linux에서 지원 됨 `CONFIG_USB_NET_CDCETHER=y|m` )
- `2`**Mbim 모드** 의 경우 (경우 Linux에서 지원 됨 `CONFIG_USB_NET_CDC_MBIM=y|m` )
- `3`**RNDIS 모드** 의 경우

모드를 구성 하는 가장 쉬운 방법은 5Ectel 5G 모뎀을 PC에 연결 하 고, TeraTerm 또는 Quectel의 고유한 PC 소프트웨어 (예: QCOM)를 사용 하는 것입니다. Windows 장치 관리자를 사용 하 여 AT 명령에 할당 된 USB 포트를 확인 하 고이 포트를 사용할 수 있습니다. COM 포트 설정은 전송 속도: 115200 stopbits.one: 1 패리티: 없음 bytesize: 8 Flow 컨트롤: ctrl Flow 없음을 지정 해야 합니다.

여기에는 AT 명령이 있습니다. USB 모드의 대상 장치를 확인 하려면:
```
AT+QCFG="usbnet"
```
모드 2로 변경 합니다.
```
AT+QCFG="usbnet",2
```
첫 번째 at 명령을 사용 하 여 다시 확인 하는 경우 "+ QCFG:" cnet ", 2"를 가져와야 합니다.

올바른 USB 모드를 설정한 후에는 다음을 사용 하 여 하드웨어 재설정을 실행 해야 합니다.
```
AT+CFUN=1,1
```
이 시점에서 모뎀의 연결을 끊고 나중에 USB 포트에 다시 연결 해야 합니다.


## <a name="using-the-modem-to-connect"></a>모뎀을 사용 하 여 연결

**1 .이 카드에 SIM 카드를 넣습니다.**

**2. Azure Percept USB 포트에 Azure USB 포트를 연결 합니다. 적절 한 USB 케이블을 사용 해야 합니다.**

**3. Azure Percept**

**4. ModemManager가 실행 중인지 확인 합니다.**

```
systemctl status ModemManager
```
다음과 같은 내용이 표시 됩니다.
```
* ModemManager.service - Modem Manager
   Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
   Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
[...]
```
그렇지 않은 경우 Azure Percept (5G 사용)에 올바른 이미지를 확인 해야 합니다.

**5. 활성 모뎀 나열**

모뎀이 표시 되 면이 모뎀이 인식 되 고 ModemManager에서 현재 처리 되 고 있는 것을 확인할 수 있습니다.
```
mmcli --list-modems
```
다음과 같이 표시 됩니다.
```
 /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
```
여기에는 모뎀 ID가 있습니다 .이 ID는 `0` 다음 명령에서 처리 하는 데 사용 됩니다 (예: `--modem 0` ).

**6. 모뎀 세부 정보 가져오기**

기본적으로 모뎀은 사용 하지 않도록 설정 됩니다 ( `Status -> state: disabled` ). 상태를 참조 하세요.
```
mmcli --modem 0
```
그리고 다음과 같은 내용이 표시 되어야 합니다.
```
General  |                    path: /org/freedesktop/ModemManager1/Modem/0
           |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
  -----------------------------------
  Hardware |            manufacturer: Quectel
           |                   model: RM500Q-GL
           |       firmware revision: RM500QGLABR11A04M4G
           |          carrier config: CDMAless-Verizon
           | carrier config revision: 0A010126
           |            h/w revision: RM500Q-GL
           |               supported: gsm-umts, lte, 5gnr
           |                 current: gsm-umts, lte, 5gnr
           |            equipment id: xxxx
  -----------------------------------
  System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
           |                 drivers: option, cdc_mbim
           |                  plugin: quectel
           |            primary port: cdc-wdm0
           |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
           |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
  -----------------------------------
  Numbers  |                     own: +1xxxx
  -----------------------------------
  Status   |          unlock retries: sim-pin2 (3)
           |                   state: disabled
           |             power state: on
           |          signal quality: 0% (cached)
  -----------------------------------
  Modes    |               supported: allowed: 3g; preferred: none
           |                          allowed: 4g; preferred: none
           |                          allowed: 3g, 4g; preferred: 4g
           |                          allowed: 3g, 4g; preferred: 3g
           |                          allowed: 5g; preferred: none
           |                          allowed: 3g, 5g; preferred: 5g
           |                          allowed: 3g, 5g; preferred: 3g
           |                          allowed: 4g, 5g; preferred: 5g
           |                          allowed: 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 5g
           |                          allowed: 3g, 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 3g
           |                 current: allowed: 3g, 4g, 5g; preferred: 5g
  -----------------------------------
  Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
           |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
  -----------------------------------
  IP       |               supported: ipv4, ipv6, ipv4v6
  -----------------------------------
  3GPP     |                    imei: xxxxxxxxxxxxxxx
           |           enabled locks: fixed-dialing
  -----------------------------------
  3GPP EPS |    ue mode of operation: csps-1
           |      initial bearer apn: ims
           |  initial bearer ip type: ipv4v6
  -----------------------------------
  SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
```

**7. 모뎀 사용**

연결을 설정 하기 전에 모뎀의 라디오를 켜야 합니다.
```
mmcli --modem 0 --enable
```
그러면 다음과 같은 작업을 수행 해야 합니다.
```
successfully enabled the modem
```
잠시 후에 모뎀이 셀 타워에 등록 되어 있어야 하며, 모뎀 상태:가 표시 되어야 `Status -> state: registered` 합니다. 다음을 사용 하 여이를 다시 확인할 수 있습니다.
```
mmcli --modem 0
```

**8. APN 정보를 사용 하 여 커넥트**

일반적으로 모뎀은 사용 하 여 연결을 설정 하는 데 사용할 수 있는 APN (정보 참조)을 제공 합니다 `3GPP EPS -> initial bearer APN` . 그렇지 않으면 사용할 APN의 휴대폰 공급자에 게 문의 하세요. 다음은 Verizon APN과 같이를 사용 하 여 연결 하는 ModemManager 명령입니다. `APN=vzwinternet`
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"
```
그리고 다음을 수행 해야 합니다.
```
successfully connected the modem
```

**9. 모뎀 상태 가져오기**

이제 `Status -> state: connected` `Bearer` 상태 메시지의 끝에 및 새 범주가 표시 됩니다.
```
mmcli -m 0
```
```
-----------------------------------
  General  |                    path: /org/freedesktop/ModemManager1/Modem/0
           |               device id: 8e3fb84e3755524d25dfa6f3f1943dc568958a2b
  -----------------------------------
  Hardware |            manufacturer: Quectel
           |                   model: RM500Q-GL
           |       firmware revision: RM500QGLABR11A04M4G
           |          carrier config: CDMAless-Verizon
           | carrier config revision: 0A010126
           |            h/w revision: RM500Q-GL
           |               supported: gsm-umts, lte, 5gnr
           |                 current: gsm-umts, lte, 5gnr
           |            equipment id: xxx
  -----------------------------------
  System   |                  device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb4/4-1/4-1.1
           |                 drivers: option, cdc_mbim
           |                  plugin: quectel
           |            primary port: cdc-wdm0
           |                   ports: cdc-wdm0 (mbim), ttyUSB0 (qcdm), ttyUSB1 (gps),
           |                          ttyUSB2 (at), ttyUSB3 (at), wwan0 (net)
  -----------------------------------
  Numbers  |                     own: +1xxxx
  -----------------------------------
  Status   |          unlock retries: sim-pin2 (3)
           |                   state: connected
           |             power state: on
           |             access tech: lte
           |          signal quality: 12% (recent)
  -----------------------------------
  Modes    |               supported: allowed: 3g; preferred: none
           |                          allowed: 4g; preferred: none
           |                          allowed: 3g, 4g; preferred: 4g
           |                          allowed: 3g, 4g; preferred: 3g
           |                          allowed: 5g; preferred: none
           |                          allowed: 3g, 5g; preferred: 5g
           |                          allowed: 3g, 5g; preferred: 3g
           |                          allowed: 4g, 5g; preferred: 5g
           |                          allowed: 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 5g
           |                          allowed: 3g, 4g, 5g; preferred: 4g
           |                          allowed: 3g, 4g, 5g; preferred: 3g
           |                 current: allowed: 3g, 4g, 5g; preferred: 5g
  -----------------------------------
  Bands    |               supported: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
           |                 current: utran-1, utran-3, utran-4, utran-6, utran-5, utran-8,
           |                          utran-2, eutran-1, eutran-2, eutran-3, eutran-4, eutran-5, eutran-7,
           |                          eutran-8, eutran-12, eutran-13, eutran-14, eutran-17, eutran-18,
           |                          eutran-19, eutran-20, eutran-25, eutran-26, eutran-28, eutran-29,
           |                          eutran-30, eutran-32, eutran-34, eutran-38, eutran-39, eutran-40,
           |                          eutran-41, eutran-42, eutran-43, eutran-46, eutran-48, eutran-66,
           |                          eutran-71, utran-19
  -----------------------------------
  IP       |               supported: ipv4, ipv6, ipv4v6
  -----------------------------------
  3GPP     |                    imei: xxxxxxxxxxxxxxx
           |           enabled locks: fixed-dialing
           |             operator id: 311480
           |           operator name: Verizon
           |            registration: home
           |                     pco: 0: (partial) '27058000FF0100'

  -----------------------------------
  3GPP EPS |    ue mode of operation: csps-1
           |     initial bearer path: /org/freedesktop/ModemManager1/Bearer/0
           |      initial bearer apn: ims
           |  initial bearer ip type: ipv4v6
  -----------------------------------
  SIM      |        primary sim path: /org/freedesktop/ModemManager1/SIM/0
  -----------------------------------
  Bearer   |                   paths: /org/freedesktop/ModemManager1/Bearer/1

```

**10. 전달자 세부 정보 가져오기**

위의 결과로 생성 된 전달자는 `--simple-connect` 경로에 `/org/freedesktop/ModemManager1/Bearer/1` 있습니다.
현재 연결에 대 한 모뎀 정보를 쿼리 하는 중입니다. 초기 전달자는 활성 연결에 연결 되지 않으므로 IP 정보를 포함 하지 않습니다.
```
mmcli --bearer 1
```
```
  --------------------------------
  General            |       path: /org/freedesktop/ModemManager1/Bearer/1
                     |       type: default
  --------------------------------
  Status             |  connected: yes
                     |  suspended: no
                     |  interface: wwan0
                     | ip timeout: 20
  --------------------------------
  Properties         |        apn: fast.t-mobile.com
                     |    roaming: allowed
  --------------------------------
  IPv4 configuration |     method: static
                     |    address: 25.21.113.165
                     |     prefix: 30
                     |    gateway: 25.21.113.166
                     |        dns: 10.177.0.34, 10.177.0.210
                     |        mtu: 1500
  --------------------------------
  Statistics         |   attempts: 1
```
다음에 대 한 몇 가지 세부 정보:
- `Status -> interface: wwan0` -이 모뎀과 일치 하는 Linux 네트워크 인터페이스를 나열 합니다.
- `IPv4 configuration` -사용할 수 있도록 설정 하기 위해 위의 인터페이스에 대 한 IP 구성을 제공 합니다.

**11. 모뎀 네트워크 인터페이스 상태 확인**

기본적으로 네트워크 인터페이스가 있어야 합니다 `DOWN` .
```
ip link show dev wwan0
```
결과:
```
4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
```

**12. 인터페이스를 가져옵니다.**

```
sudo ip link set dev wwan0 up
```

**13. IP 정보 확인**

기본적으로 IP 정보가 없는 것 처럼 인터페이스가 표시 되어야 합니다 `UP,LOWER_UP` .
```
sudo ip address show dev wwan0
```
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**14. DHCP 요청 발급**

이는 특별 한 기능으로,이 모듈은 특별 한 기능으로 제한 되지 않습니다. 일반적으로 IP 정보는 인터페이스로 수동으로 설정 하거나 ModemManager를 지 원하는 네트워크 관리자 디먼 (예: NetworkManager)을 통해 설정 해야 하지만 여기서는 다음과 같이 원하는 경우에는 다음과 같이 사용 하면 됩니다.
```
sudo dhclient wwan0
```

**15. IP 정보 확인**

이 인터페이스에 대 한 IP 구성은 ModemManager 전달자 세부 정보와 일치 해야 합니다.
```
sudo ip address show dev wwan0
```
및 결과:
```
4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
    inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
       valid_lft forever preferred_lft forever
```

**16. 인터페이스 경로 확인**

또한 DHCP 클라이언트는 인터페이스를 통과 하는 패킷의 기본 경로를 설정 합니다 `wwan0` .
```
ip route show dev wwan0
```
이런 식으로:
```
default via 25.21.113.166 
25.21.113.164/30 proto kernel scope link src 25.21.113.165
```
이제 Azure에 대 한 연결을 설정 했습니다.


**17. 연결 테스트**

`ping`인터페이스를 통해 요청을 실행 `wwan0` 합니다.
```
ping -I wwan0 8.8.8.8
```
```
PING 8.8.8.8 (8.8.8.8) from 25.21.113.165 wwan0: 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=137 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=114 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 2ms
rtt min/avg/max/mdev = 113.899/125.530/137.162/11.636 ms
```

## <a name="debugging"></a>디버깅
[커넥트 USB 모뎀 사용](./connect-over-cellular-usb.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
[USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb.md)합니다.

5G 또는 LTE의 주 문서로 돌아갑니다.

[5g 또는 LTE를 사용 하 여 커넥트](./connect-over-cellular.md)합니다.  