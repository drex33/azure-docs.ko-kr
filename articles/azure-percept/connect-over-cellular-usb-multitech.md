---
title: 커넥트 USB 모뎀 MultiTech Multiconnect를 사용 하는 LTE를 통한 Azure Percept
description: 이 문서에서는 MultiTech USB 모뎀을 사용 하 여 5g 또는 LTE 네트워크를 통해 Azure Percept DK을 연결 하는 방법을 설명 합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 336e67de6d178dcff1b57fe75d57f2272386d2c5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008713"
---
# <a name="connect-azure-percept-over-lte-with-usb-modem-multitech-multiconnect"></a>커넥트 USB 모뎀 MultiTech Multiconnect를 사용 하는 LTE를 통한 Azure Percept 
MultiTech Multiconnect (MTCM-LNA3-B03) USB 모뎀을 사용 하 여 Azure Percept를 연결 하는 단계는 다음과 같습니다. 

> [!Note]
> 몇 가지 모델을 사용 하며 Verizon 및 Vodafone SIM 카드를 사용 하 여 작동 하는 LNA3을 사용 했습니다. &T 네트워크에서에 연결할 수 없습니다 .이를 조사 하 고 근본 원인을 발견 하면이를 수정 합니다. 이 특정 모뎀에 대 한 자세한 내용은 다음 페이지를 참조 하세요. https://www.multitech.com/brands/multiconnect-microcell

## <a name="preparation"></a>준비
여기에서 Azure Percept 준비를 완료 했는지 확인 하 고 [usb 모뎀을 사용 하 여 연결](./connect-over-cellular-usb.md) 하 고 사용 해야 하는 usb 케이블에 대 한 설명을 적어 두어야 합니다. 

### <a name="preparation-of-the-modem"></a>모뎀 준비
시작 하려면 모뎀이 MBIM 모드에 있어야 합니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 다음 명령 참조 가이드에서 찾을 수 있습니다. https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf

AT 명령을 사용 하 여 `AT#USBCFG=<mode>` MBIM 인터페이스를 사용 하도록 적절 한 USB 모드를 구성 합니다.

AT 명령 참조 가이드에는 가능한 모든 모드가 나열 되어 있지만, 기본적으로는 모드에 관심이 `3` `0` 있습니다.

모드를 구성 하는 가장 쉬운 방법은 MultiTech 모뎀을 PC에 연결 하 고 TeraTerm 또는 Putty PC 소프트웨어와 같은 터미널 SW를 사용 하는 것입니다. Windows 장치 관리자를 사용 하 여 모뎀에 할당 된 USB 포트를 확인할 수 있습니다 .이 경우에는 명령에 응답 하는 것이 여러 개 있는 경우 테스트 해야 할 수 있습니다. COM 포트 설정은 전송 속도: 9600 (또는 115200) stopbits.one: 1 패리티: 없음 bytesize: 8 Flow 컨트롤: ctrl Flow 없음입니다.

여기에는 다음 명령이 있습니다. 현재 USB 모드 MultiTech 장치를 확인 하려면:
```
AT#USBCFG?
```
모드 3으로 변경 합니다.
```
AT#USBCFG=3
```
첫 번째 at 명령을 사용 하 여 다시 확인 하는 경우 다음을 가져와야 합니다. `#USBCFG: 3`

올바른 USB 모드를 설정한 후에는 다음을 사용 하 여 재설정을 실행 해야 합니다.
```
AT#REBOOT
```
이 시점에서 모뎀의 연결을 끊고 나중에 위의 설정 모드를 사용 하 여 USB 포트에 다시 연결 해야 합니다.

## <a name="using-the-modem-to-connect"></a>모뎀을 사용 하 여 연결
여기에서 [USB 모뎀을 사용 하 여 연결](./connect-over-cellular-usb.md)에서 Azure Percept 준비를 완료 했는지 확인 합니다.   

**1. MultiTech 모뎀에 SIM 카드를 연결 합니다.**

**2. MultiTech 모뎀을 Azure Percept USB A 포트에 연결 합니다.**

**3. Azure Percept**

**4. SSH를 Azure Percept DK 합니다.**

**5. ModemManager이 실행 중인지 확인 합니다.**

SSH 프롬프트에 다음 명령을 작성 합니다.
```
systemctl status ModemManager
```
모두 양호 인 경우 다음과 같은 내용이 표시 됩니다.

*ModemManager-모뎀 관리자* 
 *로드 됨: 로드 됨 (/lib/systemd/system/ModemManager.service; 사용; 공급 업체 사전 설정: 사용)* 
 *활성: 월요일 2021-08-09 20:52:03 UTC 이후 활성 (실행 중), 23 s 전*

**6. 활성 모뎀 나열**

이 경우 ModemManager에서 FIH7160 모델을 인식 한다는 것을 알 수 있습니다.
```
mmcli --list-modems
```
그리고 다음과 같은 내용이 표시 됩니다. */org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160*

**7. 모뎀 세부 정보 가져오기**

여기서는 모뎀 ID가 `0` 다를 수 있습니다. 모뎀 ID ( `--modem 0` .)는 다음과 같은 ModemManager 명령에 사용 됩니다.
```
mmcli --modem 0
```
기본적으로 모뎀은 사용 하지 않도록 설정 됩니다 ( `Status -> state: disabled` ).
```
 --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
  --------------------------------
  Hardware |         manufacturer: Telit
           |                model: FIH7160
           |    firmware revision: 20.00.525
           |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
           |              drivers: cdc_acm, cdc_mbim
           |               plugin: telit
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
           |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
           |                       wwan0 (net)
  --------------------------------
  Status   |       unlock retries: sim-pin2 (3)
           |                state: disabled
           |          power state: on
           |       signal quality: 0% (cached)
  --------------------------------
  Modes    |            supported: allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 3g, 4g; preferred: none
           |              current: allowed: 3g, 4g; preferred: none
  --------------------------------
  Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
           |                       eutran-13, eutran-17
           |              current: utran-2, eutran-2
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

**8. 모뎀 사용**

연결을 설정 하기 전에 모뎀의 라디오를 켜야 합니다.
```
mmcli --modem 0 --enable
```
그리고 다음과 같은 응답을 받아야 합니다. *모뎀을 사용 하도록 설정 했습니다* .

잠시 후에 모뎀은 셀 타워에 등록 해야 하며 `Status -> state: registered` , 다시 실행 하는 경우에는 모뎀 상태 ()가 표시 되어야 합니다.
```
mmcli --modem 0
```

**9. APN 정보를 사용 하 여 커넥트**

액세스 지점 이름 = APN은 Verizon에 대해 다음과 같이 휴대폰 공급자에 의해 제공 됩니다.
```
mmcli --modem 0 --simple-connect="apn=vzwinternet"  
```
그리고 모두 정상 이면 *모뎀이 성공적으로 연결* 됩니다.

**10. 모뎀 상태 가져오기**

`Status -> state: connected`이제 `Bearer` 상태 메시지의 끝에 새 범주가 표시 됩니다.
```
mmcli --modem 0
```
```
 --------------------------------
  General  |                 path: /org/freedesktop/ModemManager1/Modem/0
           |            device id: f89a480d73f1a9cfef28102a0b44be2a47329c8b
  --------------------------------
  Hardware |         manufacturer: Telit
           |                model: FIH7160
           |    firmware revision: 20.00.525
           |         h/w revision: XMM7160_V1.1_HWID437_MBIM_NAND
           |            supported: gsm-umts, lte
           |              current: gsm-umts, lte
           |         equipment id: xxxx
  --------------------------------
  System   |               device: /sys/devices/platform/soc@0/38200000.usb/xhci-hcd.1.auto/usb3/3-1/3-1.1
           |              drivers: cdc_acm, cdc_mbim
           |               plugin: telit
           |         primary port: cdc-wdm0
           |                ports: cdc-wdm0 (mbim), ttyACM1 (at), ttyACM2 (ignored),
           |                       ttyACM3 (ignored), ttyACM4 (at), ttyACM5 (ignored), ttyACM6 (ignored),
           |                       wwan0 (net)
  --------------------------------
  Numbers  |                  own: +1xxxxxxxx
  --------------------------------
  Status   |       unlock retries: sim-pin2 (3)
           |                state: connected
           |          power state: on
           |          access tech: lte
           |       signal quality: 16% (recent)
  --------------------------------
  Modes    |            supported: allowed: 3g; preferred: none
           |                       allowed: 4g; preferred: none
           |                       allowed: 3g, 4g; preferred: none
           |              current: allowed: 3g, 4g; preferred: none
  --------------------------------
  Bands    |            supported: utran-5, utran-2, eutran-2, eutran-4, eutran-5, eutran-12,
           |                       eutran-13, eutran-17
           |              current: utran-2, eutran-2
  --------------------------------
  IP       |            supported: ipv4, ipv6, ipv4v6
  --------------------------------
  3GPP     |                 imei: xxxxxxxxxxxxxxx
           |        enabled locks: fixed-dialing
           |          operator id: 311480
           |        operator name: Verizon
           |         registration: home
  --------------------------------
  3GPP EPS | ue mode of operation: csps-2
  --------------------------------
  SIM      |     primary sim path: /org/freedesktop/ModemManager1/SIM/0
  --------------------------------
  Bearer   |                paths: /org/freedesktop/ModemManager1/Bearer/0
```

**11. 전달자 세부 정보 가져오기**

이제 모뎀이 셀룰러 네트워크를 통해 설정 된 패킷 데이터 연결에 OS를 연결 하는 데 전달자 정보가 필요 합니다. 따라서이 시점에서 모뎀에는 IP 연결이 있지만 OS는이를 사용 하도록 아직 구성 되지 않았습니다.
```
mmcli --bearer 0
```
전달자 세부 정보 나열:
```
 ------------------------------------
  General            |           path: /org/freedesktop/ModemManager1/Bearer/0
                     |           type: default
  ------------------------------------
  Status             |      connected: yes
                     |      suspended: no
                     |      interface: wwan0
                     |     ip timeout: 20
  ------------------------------------
  Properties         |            apn: vzwinternet
                     |        roaming: allowed
  ------------------------------------
  IPv4 configuration |         method: static
                     |        address: 100.112.107.46
                     |         prefix: 24
                     |        gateway: 100.112.107.1
                     |            dns: 198.224.166.135, 198.224.167.135
  ------------------------------------
  Statistics         |       duration: 119
                     |       attempts: 1
                     | total-duration: 119
```

**12. 네트워크 인터페이스 가져오기**

```
sudo ip link set dev wwan0 up
```

**13. 네트워크 인터페이스 구성**

전달자에서 제공 하는 정보를 사용 하 여 IP 주소 (여기서 100.112.107.46/24)를 전달자에 있는 것으로 바꿉니다.
```
sudo ip address add 100.112.107.46/24 dev wwan0
```

**14. IP 정보 확인**

이 인터페이스에 대 한 IP 구성은 ModemManager 전달자 세부 정보와 일치 해야 합니다.
```
sudo ip address show dev wwan0
```
전달자 IP가 아래에 나열 되어 있는지 확인 합니다.
```
6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
    inet 100.112.107.46/24 scope global wwan0
       valid_lft forever preferred_lft forever
    inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
       valid_lft forever preferred_lft forever
```

**15. 기본 경로 설정**

전달자에서 제공한 정보를 다시 사용 하 고 모뎀의 게이트웨이 (100.112.107.1 replace)를 네트워크 패킷의 기본 대상으로 사용 합니다.
```
sudo ip route add default via 100.112.107.1 dev wwan0
```
이제 Azure Percept에는 USB 모뎀을 사용 하는 연결이 있습니다.

**16. 연결 테스트**

`ping`인터페이스를 통해 요청을 실행 `wwan0` 합니다. 그러나 Azure Percept Studio를 사용 하 고 원격 분석 메시지의 발생 여부를 확인할 수도 있습니다 (LTE를 사용 하려면 이더넷 케이블이 없거나 Wi-Fi 사용 하도록 설정 되어 있는지 확인).
```
ping -I wwan0 8.8.8.8
```
그리고
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
일반적으로 [커넥트 USB 모뎀 사용](./connect-over-cellular-usb.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
[USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb.md)합니다.

5G 또는 LTE의 주 문서로 돌아갑니다.

[5g 또는 LTE를 사용 하 여 커넥트](./connect-over-cellular.md)합니다.
   

