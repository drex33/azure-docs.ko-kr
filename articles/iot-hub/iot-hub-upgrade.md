---
title: Azure IoT Hub 업그레이드 | Microsoft Docs
description: 더 많은 메시징 및 디바이스 관리 기능을 확보하기 위해 IoT Hub에 대한 가격 책정 및 크기 계층을 변경합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: afd7a50a9c18299fb295d6ed978ce1b22bf90e9c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072582"
---
# <a name="how-to-upgrade-your-iot-hub"></a>IoT Hub를 업그레이드하는 방법

IoT 솔루션 증대에 따라 Azure IoT Hub도 고객의 확장을 지원할 수 있습니다. Azure IoT Hub는 다른 기능을 사용하고자 하는 고객에게 부응하기 위해 기본(B) 및 표준(S) 등의 두 계층을 제공합니다. 각 계층 안에는 매일 보낼 수 있는 메시지의 수를 결정하는 3개 크기(1, 2, 3)가 있습니다.

더 많은 디바이스가 있고 추가적인 기능이 필요할 경우 세 가지 방법으로 요구에 맞게 IoT Hub를 조절할 수 있습니다.

* IoT Hub 안에 단위를 추가합니다. 예를 들어 B1 IoT Hub 안의 각 추가 단위는 매일 400,000건의 추가적인 메시지를 허용합니다.

* IoT Hub의 크기를 변경합니다. 예를 들어 B1 계층에서 B2 계층으로 마이그레이션하여 각 단위가 매일 지원할 수 있는 메시지의 수를 늘립니다.

* 상위 계층으로 업그레이드합니다. 예를 들어 B1 계층에서 S1 계층으로 업그레이드하고 동일한 메시징 용량이 있는 고급 기능에 액세스합니다.

이러한 변경은 모두 기존 작업 중단 없이 가능합니다.

IoT 허브를 다운그레이드하려면 단위를 제거하고 IoT 허브의 크기를 줄일 수 있지만 하위 계층으로 다운그레이드할 수는 없습니다. 예를 들어 S2 계층에서 S1 계층으로는 이동할 수 있지만 S2 계층에서 B1 계층으로는 이동할 수 없습니다. IoT Hub당 계층 내에서 한 가지 형식의 [IoT Hub 버전](https://azure.microsoft.com/pricing/details/iot-hub/)만 선택할 수 있습니다. 예를 들어 S1 및 B3 또는 S1 및 S2와 같은 서로 다른 버전의 단위 혼합이 아닌 여러 단위의 S1을 사용하여 IoT Hub를 만들 수 있습니다.

이 예제는 사용자 솔루션 변경에 따라 IoT Hub를 조정하는 방법을 이해하는 데 도움이 됩니다. 각 계층의 기능에 대한 특정 정보는 항상 [Azure IoT Hub 가격 책정](https://azure.microsoft.com/pricing/details/iot-hub/)을 참조하세요.

## <a name="upgrade-your-existing-iot-hub"></a>기존 IoT Hub 업그레이드

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 IoT Hub로 이동합니다.

2. 왼쪽 탐색 창에서 **가격 책정 및 크기 조정을** 선택합니다.

3. 허브에 대한 계층을 변경하려면 **가격 책정 및 계층 크기 조정** 을 선택합니다. 새 계층을 선택한 다음, **선택** 을 클릭합니다.

4. 허브에서 단위 수를 변경하려면 **IoT Hub 단위** 아래 새 값을 입력합니다.

5. **저장** 을 선택하여 변경 내용을 저장합니다.

이제 IoT Hub가 조정되었으며 구성은 변경되지 않았습니다.

기본 계층 IoT Hub 및 표준 계층 IoT Hub에 대한 최대 파티션 제한은 32개입니다. 대부분의 IoT Hub는 4개의 파티션만 필요합니다. 파티션 제한은 IoT Hub가 만들어질 때 선택되며 디바이스-클라우드 메시지를 이러한 메시지의 동시 독자 수와 연결합니다. 기본 계층에서 표준 계층으로 마이그레이션할 때 이 값은 변경되지 않습니다.

## <a name="next-steps"></a>다음 단계

[올바른 IoT Hub 계층 선택 방법](iot-hub-scaling.md)에 대한 자세한 내용을 알아봅니다.