---
title: 커넥트 USB 모뎀을 사용 하는 5G 또는 LTE 네트워크를 통한 Azure Percept
description: 이 문서에서는 USB 모뎀을 사용 하 여 5g 또는 LTE 네트워크를 통해 Azure Percept DK을 연결 하는 방법을 설명 합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 2e9fb2ae534d11fb0a85199baf7256ab174e3f25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008683"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-usb-modem"></a>커넥트 USB 모뎀을 사용 하는 5G 또는 LTE 네트워크를 통한 Azure Percept

아래에서 USB 모뎀을 사용 하 여 Azure Percept DK을 준비 하 고 연결 하는 단계를 찾을 수 있습니다. 이러한 지침은 아래에 있는 지침에 따라 다운로드할 수 있는 특수 Azure Percept DK SW 에게만 적용 됩니다. 이 특수 Azure Percept 이미지는 다양 한 USB 모뎀을 지 원하는 ModemManager 오픈 소스 SW를 포함 합니다. 이 이미지는 OS 또는 기타 SW를 위한 OTA 업데이트를 지원 하지 않습니다. 이 SW를 사용 하면 간단한 비용 효율적인 LTE USB 모뎀 또는 보다 정교한 5G 모뎀을 사용 하 여 Azure Percept를 인터넷 및 Azure에 연결할 수 있습니다. 

> [!Note]
> 이러한 지침은 MBIM 인터페이스를 지 원하는 USB 모뎀과 함께 사용 하기 위한 것입니다. USB 모뎀을 가져오기 전에 먼저 MBIM 인터페이스를 지원 하는지 확인 하세요. 그런 다음 ModemManager 지원 되는 모뎀 목록에 표시 되는지 확인 합니다. ModemManager SW는 다른 인터페이스와 함께 사용할 수 있지만 MBIM 인터페이스에 초점을 두었습니다. ModemManager에 대 한 자세한 내용은 다음을 참조 하세요. https://www.freedesktop.org/wiki/Software/ModemManager/


:::image type="Image" source="media/connect-over-cellular/azure-percept-all-modems.png" alt-text="Azure Percept에 연결 된 USB 모뎀.":::

## <a name="setting-up-the-devkit-for-using-usb-modem"></a>USB 모뎀을 사용 하기 위한 Devkit 설정

- **ModemManager를 지 원하는 Azure PERCEPT SW 이미지 다운로드**  -  [Azure Percept 5G sw 이미지 파일을 압축](https://aka.ms/azpercept5gimage) 하 여 USB 모뎀을 지원 하기 위해 AZURE Percept SW를 업데이트 하는 데 필요한 3 개 파일을 다운로드 합니다.
- **Usb 메서드를 사용 하 여 다운로드 한 파일을 사용 하 여 Azure Percept 업데이트** -이전 단계에서 다운로드 한 특수 LTE/5G 사용 AZURE Percept SW를 사용 하 여 devkit를 업데이트 하 [는 방법-usb를 통해 업데이트](./how-to-update-via-usb.md) 를 수행 합니다. *이전 단계* 에서 다운로드 한 파일을 사용 하는 경우에만 사용 하는 방법에 대해 설명 합니다.
- **일반 설정 프로세스**  -  를 진행 합니다. [percept-](./quickstart-percept-dk-set-up.md) 사용자에 게 익숙하지 않은 경우 설치 프로세스를 진행 하도록 설정 합니다. 설치 환경이이 ModemManager 사용 버전의 Azure Percept에서 다르지 않습니다.
- SSH를 통해 **Azure Percept에 커넥트**  -  [방법-percept-진한](./how-to-ssh-into-percept-dk.md) 지침

## <a name="step-by-step-instructions-for-connecting-three-different-modems"></a>3 개의 서로 다른 모뎀을 연결 하는 단계별 지침

아래에서 세 가지 USB 모뎀에 대 한 지침을 찾을 수 있습니다. 첫 번째는 특별 한 기능이 없는 간단한 Vodafone (USB 동글)입니다. 이 모뎀에 대 한 지침은 비슷한 단순 비용 효율적인 USB 모뎀에 사용 될 수 있습니다. 두 번째 (MultiTech)는 다른 USB 모드의 작업을 수행 하는 USB 모뎀의 한 예입니다. 이러한 유형의 모뎀에 대해 ModemManager에서 지 원하는 MBIM 인터페이스를 사용 하도록 설정 하려면 먼저 적절 한 USB 모드를 사용 하도록 설정 해야 합니다. 세 번째는 5G 모뎀 (Quectel)이 고 다른 모드를 사용 하며 먼저 적절 한 MBIM 모드를 사용 하도록 설정 해야 합니다.  

### <a name="vodafone-usb-connect-4g-v2-modem"></a>Vodafone USB 커넥트 4G v2 모뎀
:::image type="Image" source="media/connect-over-cellular/vodafone-usb-modem-75.png" alt-text="Vodafone USB 모뎀":::

Vodafone usb 커넥트 4G v2와 같은 간단한 usb 모뎀을 사용 하 여 Azure Percept DK 연결 하기 위한 지침은 다음과 같습니다.

[Vodafone 커넥트 4G VS USB 모뎀을 사용 하 여 연결](./connect-over-cellular-usb-vodafone.md)합니다.   

### <a name="multitech-multiconnect-usb-modem"></a>MultiTech Multiconnect USB 모뎀
:::image type="Image" source="media/connect-over-cellular/multitech-usb-modem-75.png" alt-text="MultiTech USB 모뎀":::

MultiTech usb 모뎀 (mtcm-LNA3-B03)과 같은 간단한 USB 모뎀을 사용 하 여 Azure Percept DK 연결 하기 위한 지침은 다음과 같습니다.

[MULTITECH USB 모뎀을 사용 하 여 연결](./connect-over-cellular-usb-multitech.md)합니다.   

### <a name="quectel-5g-developer-kit"></a>개발자 키트
:::image type="Image" source="media/connect-over-cellular/quectel-5-g-dk-75.png" alt-text="Quectel 5G 진한":::

RM500Q와 같은 5g USB 모뎀을 사용 하 여 Azure Percept DK 연결 하기 위한 지침은 다음과 같습니다.

[Quectel 5G 개발자 키트를 사용 하 여 연결](./connect-over-cellular-usb-quectel.md)합니다. 

## <a name="how-to-make-your-lte5g-connection-recover-from-reboot"></a>다시 부팅에서 LTE/5G 연결을 복구 하는 방법 
위의 지침을 사용 하 여 네트워크에 연결 하도록 USB 모뎀을 구성할 수 있지만 장치를 다시 부팅 하는 경우 수동으로 다시 연결 해야 합니다. 이를 개선 하기 위한 솔루션을 사용 하 고 있습니다. 자세한 정보를 azpercept5G@microsoft.com 보려면 여기에서 참조 하는 짧은 노트와 함께 메일을 보내 주세요. 

## <a name="debugging-information"></a>디버깅 정보 
사용 하려는 특정 HW에서 SIM 카드가 작동 하는지 항상 확인 해야 합니다. 여러 매개체에서 데이터 전용 IoT SIM 카드만 한 장치 에서만 작동 하도록 제한 합니다. 따라서 해당 하는 경우 장치 IMEI/일련 번호가 SIM 카드의 "허용 된 장치 목록"에 표시 되는지 확인 해야 합니다.

### <a name="modemmanager-debug-mode"></a>ModemManager 디버그 모드

`/lib/systemd/system/ModemManager.service`아래 예제와 같이 줄에서 파일을 편집 하 고 추가 하 여 ModemManager의 디버그 모드를 사용 하도록 설정할 수 있습니다 `ExecStart=/usr/sbin/ModemManager [...]` `--debug` .
```  
[...]  
ExecStart=/usr/sbin/ModemManager [...] --debug  
[...]  
```
그런 다음 변경 내용을 적용 하려면 서비스를 다시 로드 하 고 ModemManager를 다시 시작 해야 합니다.
```
systemctl daemon-reload
systemctl restart ModemManager
```
로그를 확인 하 고 로그 파일을 정리 하는 데 사용할 수 있는 몇 가지 명령은 다음과 같습니다.
```
journalctl -u ModemManager.service
journalctl --rotate
journalctl --vacuum-time=1s

```

### <a name="reliability-and-stability-enhancement"></a>안정성 및 안정성 향상

#### <a name="strict-mode"></a>strict 모드
ModemManager가 아닌 직렬 인터페이스와의 상호 작용을 방지 하기 위해 [필터 정책을](https://www.freedesktop.org/software/ModemManager/api/latest/ch03s02.html)변경 하 여 검색 되는 인터페이스 (모뎀)를 제한할 수 있습니다. 모드를 사용 하는 것이 좋습니다. `STRICT`

이를 위해 `/lib/systemd/system/ModemManager.service` `ExecStart=/usr/sbin/ModemManager [...]` 아래 예제와 같이 줄에서 편집 하 고를 추가 해야 합니다. `--filter-policy=STRICT`
```
[...]
ExecStart=/usr/sbin/ModemManager --filter-policy=STRICT
[...]
```
그런 다음 변경 내용을 적용 하려면 서비스를 다시 로드 하 고 ModemManager를 다시 시작 해야 합니다.
```
systemctl daemon-reload
systemctl restart ModemManager
```

## <a name="next-steps"></a>다음 단계
이제 다양 한 USB 모뎀에 대 한 지침을 확인 합니다.

[Vodafone 커넥트 4G vs USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb-vodafone.md)

[MultiTech USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb-multitech.md)

[Quectel 5G 개발자 키트를 사용 하 여 커넥트](./connect-over-cellular-usb-quectel.md)

5G 또는 LTE의 주 문서로 돌아갑니다.

[5g 또는 LTE를 사용 하 여 커넥트](./connect-over-cellular.md)합니다.

[셀룰러 게이트웨이를 사용 하 여 커넥트](./connect-over-cellular-gateway.md)합니다.
