---
title: MultiTech MultiConnect USB 모뎀을 사용 하 여 LTE를 통해 Azure Percept DK 커넥트
description: 이 문서에서는 MultiTech MultiConnect USB 모뎀을 사용 하 여 5g 또는 LTE 네트워크를 통해 Azure Percept DK를 연결 하는 방법을 설명 합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5000c7be25a82069da0585a40252c91262a7ea12
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252443"
---
# <a name="connect-azure-percept-dk-over-lte-by-using-a-multitech-multiconnect-usb-modem"></a>MultiTech MultiConnect USB 모뎀을 사용 하 여 LTE를 통해 Azure Percept DK 커넥트 

이 문서에서는 MultiTech MultiConnect (mtcm-LNA3-B03) USB 모뎀을 사용 하 여 Azure Percept DK를 연결 하는 방법을 설명 합니다. 

> [!Note]
> MultiTech MultiConnect USB 모뎀은 다양 한 모델에서 제공 됩니다. 이 문서에서는 Verizon 및 Vodafone SIM 카드와 함께 작동 하는 모델 LNA3를 사용 했습니다. 현재는&T 네트워크에 연결할 수 없지만 문제를 조사 하 고 근본 원인을 발견 하는 경우이 문서를 업데이트 합니다. MultiTech MultiConnect USB 모뎀에 대 한 자세한 내용은 [MultiTech](https://www.multitech.com/brands/multiconnect-microcell) 사이트를 참조 하십시오.

## <a name="prepare-to-connect-azure-percept-dk"></a>Azure Percept DK 연결 준비
Azure Percept DK을 준비 하는 방법에 대 한 자세한 내용은 [USB 모뎀을 사용 하 여 5g 또는 LTE 네트워크를 통한 커넥트 Azure Percept DK](./connect-over-cellular-usb.md)를 참조 하세요. 사용 해야 하는 USB 케이블에 대 한 설명을 확인 합니다. 

### <a name="prepare-the-modem"></a>모뎀 준비
시작 하기 전에 모뎀은 MBIM (모바일 광대역 인터페이스 모델) 모드에 있어야 합니다. 모뎀을 준비 하는 방법을 알아보려면 [Telit wireless Solutions 주목 (AT) 명령 참조 가이드](
https://www.telit.com/wp-content/uploads/2018/01/Telit-LE910-V2-Modules-AT-Commands-Reference-Guide-r3.pdf)를 참조 하세요.

이 문서에서는 MBIM 인터페이스를 사용 하도록 설정 하기 위해 AT 명령을 사용 `AT#USBCFG=<mode>` 하 여 올바른 USB 모드를 구성 합니다.

AT 명령 참조 가이드에는 가능한 모든 모드가 나열 되지만이 문서에서는 모드에 관심이 `3` 있습니다. 기본 모드는 `0`입니다.

모드를 구성 하는 가장 쉬운 방법은 MultiTech 모뎀을 PC에 연결 하 고 TeraTerm 또는 PuTTY와 같은 터미널 소프트웨어를 사용 하는 것입니다. Windows 장치 관리자를 사용 하 여 모뎀에 할당 된 USB 포트를 확인할 수 있습니다. 여러 포트가 있는 경우에는 명령에 응답 하는 포트가 있는지 테스트 해야 할 수 있습니다. COM 포트 설정은 다음과 같아야 합니다.
* **전송 속도**: 9600 (또는 115200)
* **정지 비트**: 1
* **패리티**: 없음
* **바이트 크기**: 8
* **Flow 컨트롤**: 제어 흐름 없음

AT 명령은 다음과 같습니다.

MultiTech 장치에서 현재 실행 중인 USB 모드를 확인 하려면 다음을 사용 합니다.

```
AT#USBCFG?
```

모드 3으로 변경 하려면 다음을 사용 합니다.

```
AT#USBCFG=3
```

첫 번째 AT 명령을 사용 하 여 다시 확인 하는 경우 다음을 가져와야 합니다. `#USBCFG: 3`

올바른 USB 모드를 설정한 후에는 다음을 사용 하 여 재설정을 실행 해야 합니다.

```
AT#REBOOT
```

이 시점에서 모뎀은 연결을 끊은 후 이전에 설정 된 모드를 사용 하 여 USB 포트에 다시 연결 해야 합니다.

## <a name="use-the-modem-to-connect"></a>모뎀을 사용 하 여 연결

[USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb.md) 문서에서 설명 하는 Azure Percept DK 준비를 완료 했는지 확인 합니다.   

1. MultiTech 모뎀에 SIM 카드를 연결 합니다.

1. MultiTech 모뎀을 Azure Percept DK USB A 포트에 연결 합니다.

1. Azure Percept DK를 켭니다.

1. SSH (Secure Shell) 네트워크 프로토콜을 사용 하 여 Azure Percept DK 커넥트 합니다.

1. SSH 프롬프트에 다음 명령을 작성 하 여 ModemManager가 실행 되 고 있는지 확인 합니다.

    ```
    systemctl status ModemManager
    ```
    성공 하면 다음과 유사한 결과를 얻을 수 있습니다.

    *ModemManager-모뎀 관리자가* *로드 됨: 로드 됨 (/lib/systemd/system/ModemManager.service; 사용; 공급 업체 사전 설정: 사용)* *활성: 월요일 2021-08-09 20:52:03 UTC 이후의 활성 (실행 중), 23 s 전*

1. 활성 모뎀을 나열 합니다.

    ModemManager에서 사용자의 모뎀을 인식할 수 있는지 확인 하려면 다음을 실행 합니다.

    ```
    mmcli --list-modems
    ```

    다음과 유사한 결과를 얻을 수 있습니다.

    ```
    /org/freedesktop/ModemManager1/Modem/0 [Telit] FIH7160
    ```

1. 모뎀 세부 정보를 가져옵니다.

    여기에서 모뎀 ID는 `0` 이지만 결과가 다를 수 있습니다. 모뎀 ID ( `--modem 0` )는 다음과 같은 ModemManager 명령에 사용 됩니다.
    
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

1. 모뎀을 사용 하도록 설정 합니다.

    연결을 설정 하기 전에 다음 코드를 실행 하 여 모뎀의 라디오 또는 라디오 단추를 켭니다.

    ```
    mmcli --modem 0 --enable
    ```

    "모뎀을 사용 하도록 설정 했습니다."와 같은 응답을 받게 됩니다.

    잠시 후에는 모뎀을 셀 타워에 등록 해야 하며, `Status -> state: registered` 다음 코드를 실행 한 후에는의 모뎀 상태가 표시 되어야 합니다.

    ```
    mmcli --modem 0
    ```

1. APN (액세스 지점 이름) 정보를 사용 하 여 커넥트 합니다.

    휴대폰 공급자는 Verizon에 대 한 다음과 같은 apn을 제공 합니다.

    ```
    mmcli --modem 0 --simple-connect="apn=vzwinternet"  
    ```

    "모뎀을 사용 하도록 설정 했습니다."와 같은 응답을 받게 됩니다.

1. 모뎀 상태를 가져옵니다.

    이제 상태 `Status -> state: connected` 메시지의 끝에 및 새 범주가 표시 됩니다 `Bearer` .

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

1. 전달자 세부 정보를 가져옵니다.

    이제 모뎀이 셀룰러 네트워크를 사용 하 여 설정 된 패킷 데이터 연결에 운영 체제를 연결 하기 위한 전달자 정보가 필요 합니다. 이 시점에서 모뎀에는 IP 연결이 있지만 운영 체제가 아직 사용 하도록 구성 되지 않았습니다.
  
    ```
    mmcli --bearer 0
    ```

    전달자 세부 정보는 다음 코드에 나열 됩니다.

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

1. 네트워크 인터페이스를 엽니다.

    ```
    sudo ip link set dev wwan0 up
    ```

1. 네트워크 인터페이스를 구성 합니다.

    전달자에서 제공 하는 정보를 사용 하 여 IP 주소 (예: 100.112.107.46/24를 사용 하는 경우)를 전달자에서 사용 하는 것과 같이 바꿉니다.

    ```
    sudo ip address add 100.112.107.46/24 dev wwan0
    ```

1. IP 정보를 확인 합니다.

    이 인터페이스에 대 한 IP 구성은 ModemManager 전달자 세부 정보와 일치 해야 합니다. 다음을 실행합니다.

    ```
    sudo ip address show dev wwan0
    ```

    전달자 IP는 다음과 같이 나열 됩니다.

    ```
    6: wwan0: <BROADCAST,MULTICAST,NOARP,UP,LOWER_UP> mtu 1428 qdisc pfifo_fast state UNKNOWN group default qlen 1000
        link/ether 1e:fb:08:e9:2a:25 brd ff:ff:ff:ff:ff:ff
        inet 100.112.107.46/24 scope global wwan0
          valid_lft forever preferred_lft forever
        inet6 fe80::1cfb:8ff:fee9:2a25/64 scope link
          valid_lft forever preferred_lft forever
    ```

1. 기본 경로를 설정 합니다.

    다시, 전달자에서 제공한 정보를 사용 하 고 모뎀의 게이트웨이 (100.112.107.1 대체)를 네트워크 패킷의 기본 대상으로 사용 하 여 다음을 실행 합니다.

    ```
    sudo ip route add default via 100.112.107.1 dev wwan0
    ```

    Azure Percept DK 이제 USB 모뎀과 연결 되어 있습니다.

1. 연결을 테스트 합니다.

    이 문서에서는 인터페이스를 통해 요청을 실행 하 고 있습니다 `ping` `wwan0` . 그러나 Azure Percept Studio를 사용 하 여 원격 분석 메시지 도착 여부를 확인할 수도 있습니다. 사용자가 사용 하도록 설정 되지 않은 이더넷 케이블을 사용 하 Wi-Fi 고 있는지 확인 합니다. 다음을 실행합니다.

    ```
    ping -I wwan0 8.8.8.8
    ```

    다음과 유사한 결과를 얻을 수 있습니다.

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

디버깅에 대 한 일반 정보는 [USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

액세스 권한이 있는 셀룰러 장치에 따라 다음 두 가지 방법 중 하나로 연결할 수 있습니다.

* [USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb.md)
* [5G 또는 LTE를 사용 하 여 커넥트](./connect-over-cellular.md)
