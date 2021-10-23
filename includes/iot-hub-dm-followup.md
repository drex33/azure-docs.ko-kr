---
title: 포함 파일
description: 포함 파일
services: iot-hub
ms.service: iot-hub
author: robinsh
ms.topic: include
ms.date: 02/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 483d51ab654a67ca9c9ac59a5ae1efdd6861419b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130188567"
---
## <a name="customize-and-extend-the-device-management-actions"></a>디바이스 관리 작업 사용자 지정 및 확장

IoT 솔루션에서 정의된 디바이스 관리 패턴 집합을 확장하거나 디바이스 쌍 및 클라우드-디바이스 메서드 기본 형식을 사용하여 사용자 지정 패턴을 활성화하도록 설정할 수 있습니다. 디바이스 관리 작업의 다른 예로 공장 재설정, 펌웨어 업데이트, 소프트웨어 업데이트, 전원 관리, 네트워크 및 연결 관리, 데이터 암호화가 있습니다.

## <a name="device-maintenance-windows"></a>디바이스 유지 관리 기간

일반적으로 서비스 중단 및 가동 중지 시간을 최소화하면서 작업을 수행하도록 디바이스를 구성합니다. 디바이스 유지 관리 기간은 디바이스에서 해당 구성을 업데이트해야 할 경우 시간을 정의하는 데 널리 사용되는 패턴입니다. 백 엔드 솔루션에서는 디바이스 쌍의 desired 속성을 사용하여 유지 관리 기간을 사용하는 디바이스에 대한 정책을 정의하고 활성화할 수 있습니다. 디바이스에서 유지 관리 기간 정책을 수신하면 디바이스 쌍의 reported 속성을 사용하여 정책의 상태를 보고할 수 있습니다. 그런 다음, 백 엔드 앱은 디바이스 쌍 쿼리를 사용하여 디바이스 및 각 정책의 규정 준수를 입증합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에서 원격 다시 시작을 트리거하는 데 직접 메서드를 사용했습니다. 보고된 속성을 사용하여 디바이스에서 마지막으로 다시 시작한 시간을 보고하고, 디바이스 쌍을 쿼리하여 디바이스가 클라우드에서 마지막으로 다시 시작한 시간을 확인했습니다.

[Raspberry Pi 3 B+ 참조 이미지를 사용하여 Azure IoT Hub용 디바이스 업데이트 자습서에서](../articles/iot-hub-device-update/device-update-raspberry-pi.md)엔드투엔드 이미지 기반 업데이트와 같은 IoT Hub 및 디바이스 관리 패턴을 계속 시작하려면

IoT 솔루션을 확장하고 여러 디바이스에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)를 참조하세요.