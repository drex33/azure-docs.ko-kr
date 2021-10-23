---
title: USB 모뎀을 사용하여 5G 및 LTE 네트워크 커넥트 Azure Percept DK
description: 이 문서에서는 USB 모뎀을 사용하여 5G 및 LTE 네트워크를 통해 Azure Percept DK 연결하는 방법을 설명합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 6d485d855a6116661916325c925ef063d6125dc7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252405"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-usb-modem"></a>USB 모뎀을 사용하여 5G 및 LTE 네트워크 커넥트 Azure Percept DK

이 문서에서는 USB 모뎀을 사용하여 Azure Percept DK 5G 또는 LTE 네트워크에 연결하는 방법을 설명합니다. 

> [!NOTE]
> 이 문서의 정보는 다음 섹션의 지침에 따라 다운로드할 수 있는 특수 Azure Percept DK 소프트웨어에만 적용됩니다. 특수 Azure Percept DK 이미지에는 다양한 USB 모뎀을 지원하는 ModemManager 오픈 소스 소프트웨어가 포함되어 있습니다. 이미지는 운영 체제 또는 기타 소프트웨어에 대한 OTA(OTA)를 지원하지 않습니다. ModemManager 오픈 소스 소프트웨어를 사용하면 간단하고 비용 효율적인 LTE USB 모뎀 또는 보다 정교한 5G 모뎀을 사용하여 인터넷 및 Azure에 Azure Percept DK 연결할 수 있습니다. 
>
> 이 문서의 지침은 MBIM(모바일 광대역 인터페이스 모델) 인터페이스를 지원하는 USB 모뎀과 함께 사용됩니다. USB 모뎀을 얻기 전에 MBIM 인터페이스를 지원하는지 확인합니다. 또한 지원되는 모뎀의 ModemManager 목록에 나열되어 있는지 확인합니다. ModemManager 소프트웨어는 다른 인터페이스와 함께 사용할 수 있지만 이 문서에서는 MBIM 인터페이스에 집중합니다. 자세한 내용은 [freedesktop.org ModemManager 페이지로](https://www.freedesktop.org/wiki/Software/ModemManager/) 이동합니다.


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems.png" alt-text="USB 모뎀을 사용하여 5G 및 LTE 네트워크에 연결하는 Azure Precept DK의 사진 그림입니다.":::

## <a name="set-up-azure-percept-dk-to-use-a-usb-modem"></a>USB 모뎀을 사용하도록 Azure Percept DK 설정

1. ModemManager를 지원하는 [Azure Percept 5G 소프트웨어 이미지를 다운로드합니다.](https://aka.ms/azpercept5gimage) 

   이러한 세 파일은 USB 모뎀을 지원하도록 Azure Percept DK 소프트웨어를 업데이트하는 데 필요합니다.

1. 이전 단계에서 [다운로드한](./how-to-update-via-usb.md) 특수 5G/LTE 사용 Azure Percept DK 소프트웨어로 Azure Percept DK 소프트웨어를 업데이트합니다. 

   > [!IMPORTANT]
   > [USB-C 연결을 통해 Azure Percept DK 업데이트의](./how-to-update-via-usb.md)지침을 따르되, 이전 단계에서 다운로드한 *파일만* 사용하고 문서에 언급된 파일은 사용하지 않도록 해야 합니다.

1. 일반 프로세스에 따라 [Azure Percept DK 디바이스를 설정합니다(익숙하지](./quickstart-percept-dk-set-up.md)않은 경우). 

   이 ModemManager 사용 버전의 Azure Percept DK 설정 환경이 다르지 않습니다.

1. [SSH(Secure Shell) 네트워크 프로토콜 을 사용하여 Azure Percept DK 커넥트.](./how-to-ssh-into-percept-dk.md)

## <a name="connect-to-a-modem"></a>모뎀에 커넥트

다음 세 섹션에는 다양한 USB 모뎀에 연결하기 위한 지침이 있습니다.  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Vodafone USB 커넥트 4G v2 모뎀

이 Vodafone USB 모뎀은 특별한 기능이 없는 간단한 LTE CAT-4 USB 동글입니다. 이 모뎀에 대한 지침은 다른 유사하고 간단하고 비용 효율적인 USB 모뎀에 사용할 수 있습니다.

:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Vodafone 4G v2 USB 모뎀의 위쪽 및 아래쪽 보기를 보여주는 일러스트레이션.":::

Vodafone USB 커넥트 4G v2와 같은 간단한 USB 모뎀을 사용하여 Azure Percept DK 연결하는 방법에 대한 지침은 [Vodafone 커넥트 4G v2 USB 모뎀을 사용하여 커넥트 참조하세요.](./connect-over-cellular-usb-vodafone.md)   

### <a name="multitech-multiconnect-usb-modem"></a>MultiTech 다중 연결 USB 모뎀

이 MultiTech USB 모뎀은 몇 가지 USB 작동 모드를 제공합니다. 이 유형의 모뎀의 경우 ModemManager에서 지원하는 MBIM 인터페이스를 사용하도록 설정하기 전에 먼저 적절한 USB 모드를 사용하도록 설정해야 합니다.

:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="MultiTech 다중 연결 USB 모뎀의 일러스트레이션.":::

MultiTech USB 모뎀(MTCM-LNA3-B03)과 같은 간단한 USB 모뎀을 사용하여 Azure Percept DK 연결하려면 [MultiTech USB 모뎀을 사용하여 커넥트](./connect-over-cellular-usb-multitech.md)지침을 따릅니다.

### <a name="quectel-5g-developer-kit"></a>Quectel 5G 개발자 키트

세 번째 모뎀은 Quectel 5G DK입니다. 또한 여러 모드를 제공하며, 먼저 적절한 MBIM 모드를 사용하도록 설정해야 합니다.

:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Quectel 5G DK USB 모뎀의 일러스트레이션.":::

Quectel RM500Q-GL과 같은 5G USB 모뎀을 사용하여 Azure Percept DK 연결하는 방법에 대한 지침은 [Quectel 5G Developer Kit를 사용하여 커넥트](./connect-over-cellular-usb-quectel.md)참조하세요. 

## <a name="help-your-5g-or-lte-connection-recover-from-reboot"></a>다시 부팅에서 5G 또는 LTE 연결 복구 지원 
네트워크에 연결하도록 USB 모뎀을 구성할 수 있지만 디바이스를 다시 부팅하는 경우 수동으로 다시 연결해야 합니다. 현재 이 환경을 개선하기 위한 솔루션에 대해 작업하고 있습니다. 자세한 내용은 이 문제를 참조하는 짧은 참고 사항으로 [지원 팀에](mailto:azpercept5G@microsoft.com) 문의하세요. 

## <a name="debugging-information"></a>디버깅 정보 
SIM 카드가 사용하려는 특정 하드웨어에서 작동하는지 확인합니다. 여러 운송업체가 하나의 디바이스에서만 작동하도록 데이터 전용 IoT SIM 카드를 제한합니다. 이러한 이유로 디바이스 IMEI(International Mobile Equipment Identity) 또는 일련 번호가 운송업체의 SIM 카드 허용 디바이스 목록에 나열되어 있는지 확인합니다.

### <a name="modemmanager-debug-mode"></a>ModemManager 디버그 모드

다음 예제와 같이 를 추가하여 줄에서 */lib/systemd/system/ModemManager.service* 파일을 편집하여 ModemManager 디버그 `ExecStart=/usr/sbin/ModemManager [...]` 모드를 사용하도록 설정할 수 있습니다. `--debug`

```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```

변경 내용을 적용하려면 다음과 같이 서비스를 다시 로드하고 ModemManager를 다시 시작합니다.

```
systemctl daemon-reload
systemctl restart ModemManager
```

다음 명령을 실행하여 로그를 보고 로그 파일을 정리할 수 있습니다.

```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="enhance-reliability-and-stability"></a>안정성 및 안정성 향상

ModemManager가 비모뎀 직렬 인터페이스와 상호 작용하지 않도록 하려면 [필터 정책을](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html)변경하여 인터페이스를 검색(모뎀 확인)으로 제한할 수 있습니다. 

모드를 사용하는 것이 `STRICT` 좋습니다.

이렇게 하려면 다음 예제와 같이 를 추가하여 줄에서 */lib/systemd/system/ModemManager.service* 파일을 편집합니다. `ExecStart=/usr/sbin/ModemManager [...]` `--filter-policy=STRICT`

```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
변경 내용을 적용하려면 다음과 같이 서비스를 다시 로드하고 ModemManager를 다시 시작합니다.

```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>다음 단계

* [5G 또는 LTE를 사용하여 커넥트](./connect-over-cellular.md)
* [셀룰러 게이트웨이를 사용하여 커넥트](./connect-over-cellular-gateway.md)
