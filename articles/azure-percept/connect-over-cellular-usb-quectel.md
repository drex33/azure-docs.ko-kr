---
title: Quectel RM500 5G 모뎀을 사용하여 5G 또는 LTE를 통해 커넥트 Azure Percept DK
description: 이 문서에서는 Quectel 5G 모뎀을 사용하여 5G 또는 LTE 네트워크를 통해 Azure Percept DK 연결하는 방법을 설명합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: de4f889883b7c3da4e8129fc66d31aed094ebda6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252424"
---
# <a name="connect-azure-percept-dk-over-5g-or-lte-by-using-a-quectel-rm500-gl-5g-modem"></a>Quectel RM500-GL 5G 모뎀을 사용하여 5G 또는 LTE를 통해 커넥트 Azure Percept DK  

이 문서에서는 Quectel RM500-GL 5G 모뎀을 사용하여 5G 또는 LTE를 통해 Azure Percept DK 연결하는 방법을 설명합니다. 

이 5G 모뎀 개발 키트에 대한 자세한 내용은 Quectel 로컬 영업 팀에 문의하세요.

* 북아메리카 고객의 경우: northamerica-sales@quectel.com
* 글로벌 고객의 경우: sales@quectel.com

> [!Note] 
> **5G 모뎀용 USB 케이블 정보:**  
> 5G 모뎀에는 LTE 모뎀보다 더 많은 전원이 필요하며, 잘못된 USB 케이블이 병목 상태가 되어 가능한 최상의 5G 데이터 속도를 실현할 수 있습니다. 5G 모뎀에 일관적인 충분한 전원을 제공하려면 USB 케이블이 다음 표준을 충족하는지 확인합니다.  
> **전원:**
> - 최대 앰퍼지는 3개의 앰퍼보다 크거나 같아야 합니다.
> - 케이블 길이는 1미터 미만이어야 합니다.
> - 5G 모뎀을 사용하는 경우 Azure Percept DK 하나의 USB A 포트만 활성화되어야 합니다.
>  
> **처리량:**
> - USB 3.1 Gen2
> - USB-IF 인증

## <a name="prepare-to-connect-azure-percept-dk"></a>연결 준비 Azure Percept DK
Azure Percept DK 준비하는 방법을 알아보려면 [USB 모뎀 을 사용하여 5G 또는 LTE 네트워크를 통해 커넥트 Azure Percept DK.](./connect-over-cellular-usb.md) 사용해야 하는 USB 케이블에 대한 주석을 기록해 두어야 합니다. 

### <a name="prepare-the-modem"></a>모뎀 준비
시작하기 전에 모뎀이 MBIM(모바일 광대역 인터페이스 모델) 모드여야 합니다. 문서화되지 않았지만 표준 AT(Quectel Attention) 명령을 사용할 수 `AT+QCFG="usbnet"` 있습니다.

`usbnet`속성은 에서 로 네 개의 다른 값으로 설정할 수 있습니다. `0` `3`
- `0`**NDIS/PPP/QMI 모드의** `qmi_wwan` 경우(드라이버에서 지원, 에서 사용하도록 설정) `CONFIG_USB_NET_QMI_WWAN=y|m`
- `1`**CDC 이더넷 모드의** 경우(인 경우 Linux에서 `CONFIG_USB_NET_CDCETHER=y|m` 지원됨)
- `2`**MBIM 모드의** 경우(인 경우 Linux에서 `CONFIG_USB_NET_CDC_MBIM=y|m` 지원됨)
- `3`**RNDIS 모드의** 경우

모드를 구성하는 가장 쉬운 방법은 Quectel 5G 모뎀을 PC에 연결하고 TeraTerm과 같은 터미널 소프트웨어 또는 QCOM과 같은 Quectel의 자체 PC 소프트웨어를 사용하는 것입니다. Windows 디바이스 관리자를 사용하여 모뎀에 할당된 USB 포트를 확인할 수 있습니다. COM 포트 설정은 다음과 여야 합니다.
* **전송 속도:** 115200
* **중지 비트:** 1
* **패리티:** 없음
* **바이트 크기:** 8
* **Flow 제어:** 제어 흐름 없음

AT 명령은 다음과 같습니다.

현재 실행 중인 USB 모드 Quectel 디바이스를 확인하려면 다음을 사용합니다.

```
AT+QCFG="usbnet"
```

모드 2로 변경하려면 다음을 사용합니다.

```
AT+QCFG="usbnet",2
```

첫 번째 AT 명령을 사용하여 다시 확인하면 다음이 수행되어야 합니다.

```
+QCFG: "usbnet",2`
```

올바른 USB 모드를 설정한 후 다음을 사용하여 하드웨어 재설정을 수행합니다.

```
AT+CFUN=1,1
```

이 시점에서 모뎀은 연결을 끊고 나중에 USB 포트에 다시 연결해야 합니다.


## <a name="use-the-modem-to-connect"></a>모뎀을 사용하여 연결

1. SIM 카드를 큐셀 모뎀에 넣습니다.

1. Azure Percept DK USB 포트에 큐클 모뎀을 연결합니다. 적절한 USB 케이블을 사용해야 합니다.

1. Azure Percept DK 전원을 공급합니다.

1. ModemManager가 실행 중인지 확인합니다.

      ```
      systemctl status ModemManager
      ```
   
      성공하면 다음과 유사한 결과가 표시됩니다.

      ```
      * ModemManager.service - Modem Manager
         Loaded: loaded (/lib/systemd/system/ModemManager.service; enabled; vendor pre set: enabled)
         Active: active (running) since Mon 2021-08-09 20:52:03 UTC; 23s ago
      [...]
      ```

      실패하면 올바른 이미지를 플래시하여 Azure Percept DK(5G 사용)했는지 확인합니다.

1. 활성 모뎀을 나열합니다.

      모뎀을 나열하면 큐클 모뎀이 인식되었고 이제 ModemManager에서 처리되는 것을 볼 수 있습니다.

      ```
      mmcli --list-modems
      ```
   
      다음과 유사한 결과가 생성되어야 합니다.

      ```
      /org/freedesktop/ModemManager1/Modem/0 [Quectel] RM500Q-GL
      ```

      여기서 모뎀 ID는 이며 이를 `0` 해결하기 위해 다음 명령(즉, `--modem 0` )에서 사용됩니다.

1. 모뎀 세부 정보를 얻습니다.

      기본적으로 모뎀은 사용하지 않도록 `Status -> state: disabled` 설정됩니다(). 상태를 보려면 다음을 실행합니다.

      ```
      mmcli --modem 0
      ```
      
      다음과 유사한 결과가 생성되어야 합니다.

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

1. 모뎀을 사용하도록 설정합니다.

      연결을 설정하기 전에 다음을 실행하여 모뎀의 라디오 또는 라디오를 켭니다.

      ```
      mmcli --modem 0 --enable
      ```

      다음과 유사한 응답을 받아야 합니다.

      ```
      successfully enabled the modem
      ```
   
      잠시 후 모뎀을 셀타워에 등록해야 `Status -> state: registered` 하며, 다음 코드를 실행한 후 모뎀 상태가 표시됩니다.
   
      ```
      mmcli --modem 0
      ```

1. APN(액세스 지점 이름) 정보를 사용하여 커넥트.

      일반적으로 모뎀은 사용할 APN을 `3GPP EPS -> initial bearer APN` 제공하므로(정보 참조) 연결을 설정하는 데 사용할 수 있습니다. 모뎀이 APN을 제공하지 않는 경우 APN에서 사용할 수 있도록 휴대폰 공급자에게 문의하세요. 
      
      다음은 Verizon APN 을 사용하여 연결하기 위한 ModemManager `APN=vzwinternet` 명령입니다.

      ```
      mmcli --modem 0 --simple-connect="apn=vzwinternet"
      ```

      다시 다음과 유사한 응답을 받게 됩니다.

      ```
      successfully connected the modem
      ```

1. 모뎀 상태를 확인합니다.

      이제 상태 메시지의 끝에 상태 `Status -> state: connected` 및 새 `Bearer` 범주가 표시됩니다.
 
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

1. Bearer 세부 정보를 얻습니다.

      이전 단계에서 생성된 Bearer는 `--simple-connect` 경로 에 `/org/freedesktop/ModemManager1/Bearer/1` 있습니다.

      활성 연결에 대한 모뎀 정보를 쿼리하는 안내자입니다. 초기 bearer는 활성 연결에 연결되지 않으므로 IP 정보를 보유하지 않습니다.

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

      다음은 몇 가지 주요 세부 정보의 설명입니다.
      - `Status -> interface: wwan0`: 이 모뎀과 일치하는 Linux 네트워크 인터페이스를 나열합니다.
      - `IPv4 configuration`: 사용 가능하도록 설정하기 위해 이전 인터페이스에 대한 IP 구성을 제공합니다.

1. 모뎀 네트워크 인터페이스의 상태를 확인합니다.

      기본적으로 네트워크 인터페이스는 `DOWN` 를 표시합니다.

      ```
      ip link show dev wwan0
      ```

      다음과 유사한 결과가 생성되어야 합니다.

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
      ```

1. 인터페이스를 불러올 수 있습니다.

      ```
      sudo ip link set dev wwan0 up
      ```

1. IP 정보를 확인합니다.

      기본적으로 인터페이스는 IP `UP,LOWER_UP` 정보 없이 를 표시합니다.

      ```
      sudo ip address show dev wwan0
      ```

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. DHCP 요청을 발급합니다.

      이 기능은 Quectel 모듈에만 해당되지만 이에 국한되지 않습니다. IP 정보는 일반적으로 인터페이스 또는 ModemManager(예: NetworkManager)를 지원하는 네트워크 관리자 디먼을 통해 수동으로 설정되지만 여기서는 단순히 Quectel 모뎀에서 dhclient를 사용할 수 있습니다.

      ```
      sudo dhclient wwan0
      ```

1. IP 정보를 확인합니다.

      이 인터페이스의 IP 구성은 ModemManager bearer 세부 정보와 일치해야 합니다.

      ```
      sudo ip address show dev wwan0
      ```

      다음과 유사한 결과가 생성되어야 합니다.

      ```
      4: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
         link/ether ce:92:c2:b8:1e:f2 brd ff:ff:ff:ff:ff:ff
         inet 25.21.113.165/30 brd 25.21.113.167 scope global wwan0
            valid_lft forever preferred_lft forever
         inet6 fe80::cc92:c2ff:feb8:1ef2/64 scope link 
            valid_lft forever preferred_lft forever
      ```

1. 인터페이스 경로를 확인합니다.

      또한 DHCP 클라이언트는 인터페이스를 통과하는 패킷에 대한 기본 경로를 `wwan0` 설정합니다.

      ```
      ip route show dev wwan0
      ```

      다음과 유사한 결과가 생성되어야 합니다.

      ```
      default via 25.21.113.166 
      25.21.113.164/30 proto kernel scope link src 25.21.113.165
      ```

      이제 Quectel 모뎀을 사용하여 Azure Percept DK 대한 연결을 설정했습니다.


1. 연결을 테스트합니다.

      `ping`인터페이스를 통해 요청을 실행합니다. `wwan0`

      ```
      ping -I wwan0 8.8.8.8
      ```

      다음과 유사한 결과가 생성되어야 합니다.

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

디버깅에 대한 일반적인 내용은 [USB 모뎀을 사용하여 커넥트 참조하세요.](./connect-over-cellular-usb.md)

## <a name="next-steps"></a>다음 단계

액세스 권한이 있는 셀룰러 디바이스에 따라 다음 두 가지 방법 중 하나로 연결할 수 있습니다.

* [USB 모뎀을 사용하여 커넥트](./connect-over-cellular-usb.md)
* [5G 또는 LTE를 사용하여 커넥트](./connect-over-cellular.md)
