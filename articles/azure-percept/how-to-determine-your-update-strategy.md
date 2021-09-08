---
title: Azure Percept DK에 대한 업데이트 전략 결정
description: Azure Percept DK OTA 또는 USB 케이블 업데이트의 장단점. 다양한 사용자를 위한 최상의 업데이트 접근 방식을 선택하기 위한 권장 사항.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: c26de68bc30e8ebfa6de92c8e142e6ca69088b26
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223353"
---
# <a name="determine-your-update-strategy-for-azure-percept-dk"></a>Azure Percept DK에 대한 업데이트 전략 결정

Azure Percept DK 소프트웨어 업데이트를 최신 상태로 유지하기 위해 Microsoft는 개발 키트에 대해 두 가지 업데이트 방법을 제공합니다. **USB 케이블을 통한 업데이트** 또는 **OTA(무선) 업데이트**.

USB 케이블을 통한 업데이트는 개발 키트에 새로 설치합니다. 새 이미지가 배포된 후 기존 구성과 각 파티션의 모든 사용자 데이터가 지워집니다. 그렇게 하려면 C타입 USB 케이블을 사용하여 개발 키트를 호스트 시스템에 연결합니다. 호스트 시스템은 Windows/Linux 컴퓨터일 수 있습니다.  이 업데이트 방법을 공장 초기화로 사용할 수도 있습니다. 그렇게 하기 위해 정확히 동일한 버전을 개발 키트에 재배포했습니다. USB 케이블 업데이트에 대한 자세한 내용은 [USB-C 케이블 연결을 통해 Azure Percept DK 업데이트](./how-to-update-via-usb.md)를 참조하세요.

OTA 업데이트는 [Device Update for IoT Hub](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-resources) Azure 서비스를 기반으로 합니다. 이 유형의 업데이트를 수행하려면 개발 키트를 Azure IoT Hub에 연결합니다. 구성 및 사용자 데이터는 OTA 업데이트 후에도 보존됩니다. OTA 업데이트 수행에 대한 자세한 내용은 [Azure Percept DK OTA(무선) 업데이트](./how-to-update-over-the-air.md)를 참조하세요.

USB 케이블 업데이트 및 OTA 업데이트 모두에 대한 장단점을 확인한 다음 다양한 시나리오에 대한 Microsoft 권장 사항을 따릅니다.

## <a name="usb-cable-update"></a>USB 케이블 업데이트

- 장점
  - 개발 키트를 인터넷/Azure에 연결할 필요가 없습니다.
  - 최신 이미지는 현재 개발 키트에 로드된 소프트웨어 및 펌웨어 버전에 관계없이 항상 적용할 수 있습니다.
- 단점
  - 디바이스의 이미지를 다시 만들고 구성 및 사용자 데이터를 제거합니다.
  - OOBE를 다시 실행하고 사전 로드되지 않은 컨테이너를 다운로드해야 합니다.
  - 원격으로 수행할 수 없습니다.

## <a name="ota-update"></a>OTA 업데이트

- 장점
  - 사용자 데이터, 구성 및 다운로드한 컨테이너를 보존합니다. 개발 키트는 OTA 이후에도 계속 작동됩니다.
  - 업데이트는 원격으로 수행할 수 있습니다.
  - 여러 유사한 디바이스를 동시에 업데이트할 수 있습니다. 업데이트는 예를 들어 야간에 발생하도록 예약할 수도 있습니다.
- 단점
  - 건너뛸 수 없는 하드 중지 버전이 있을 수 있습니다. [OTA의 하드 중지 버전](./software-releases-over-the-air-updates.md#hard-stop-version-of-ota)을 참조하세요.
  - 디바이스는 “Device Update for IoT Hub” 기능이 올바르게 구성된 IoT Hub에 연결해야 합니다.
  - 다운그레이드 시에는 잘 작동하지 않습니다.

> [!IMPORTANT]
> Device Update for IoT Hub는 현재 실행 중인 OS보다 오래된 버전의 이미지 배포를 차단하지 않습니다. 그러나 개발 키트에 그렇게 하면 데이터와 기능이 손실됩니다.

## <a name="microsoft-recommendations"></a>Microsoft 추천 사항

|형식|시나리오|Update 메서드|
|:---:|---|:---:|
|프로덕션|이미 솔루션을 실행 중이거나 현장에 배포되는 동안 최신 수정 사항 및 보안 패치에 대한 개발 키트를 최신 상태로 유지합니다.|OTA|
|프로덕션/개발|새 개발 키트를 개봉하고 최신 소프트웨어로 업데이트합니다.|USB|
|프로덕션/개발|이미 여러 월간 릴리스를 건너뛰었지만 최신 소프트웨어 버전으로 업데이트하고 싶습니다.|USB|
|프로덕션/개발|개발 키트를 공장 초기화합니다.|USB|
|개발|솔루션 개발 중 개발 키트 OS 및 F/W를 최신 상태로 유지하고 싶습니다.|USB/OTA|
|개발|문제 조사/디버깅을 위해 특정(이전) 버전으로 이동합니다.|USB|

## <a name="next-steps"></a>다음 단계

선택할 업데이트 방법을 결정한 후 다음 페이지를 방문하여 업데이트를 준비합니다.

USB 케이블 업데이트

- [USB-C 케이블 연결을 통해 Azure Percept DK 업데이트](./how-to-update-via-usb.md)
- [USB 케이블 업데이트를 위해 Azure Percept DK 소프트웨어 릴리스](./software-releases-usb-cable-updates.md)

OTA

- [OTA(무선)로 Azure Percept DK 업데이트](./how-to-update-over-the-air.md)
- [OTA 업데이트에 대한 Azure Percept DK 소프트웨어 릴리스](./software-releases-over-the-air-updates.md)
