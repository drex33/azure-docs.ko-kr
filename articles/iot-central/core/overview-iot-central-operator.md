---
title: Azure IoT Central 운영자 가이드
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 IoT Central의 운영자 역할에 대한 개요를 제공합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 86a5d327e00ee7c9595cc586aa7e63023a386bba
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586548"
---
# <a name="iot-central-operator-guide"></a>Azure IoT Central 운영자 가이드

IoT Central 애플리케이션을 사용하면 수백만 대의 디바이스를 수명 주기 전체 동안 모니터링하고 관리할 수 있습니다. 이 가이드는 IoT Central 애플리케이션을 사용하여 IoT 디바이스를 관리하는 운영자를 대상으로 합니다.

운영자는

- 애플리케이션에 연결된 디바이스를 모니터링 및 관리합니다.
- 디바이스 관련 문제 해결 및 수정합니다.
- 새 디바이스를 프로비전합니다.

## <a name="monitor-and-manage-devices"></a>디바이스 모니터링 및 관리

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="디바이스 보기를 표시하는 스크린샷":::

디바이스를 모니터링하기 위해 운영자는 디바이스 템플릿의 일부로 솔루션 빌더에서 정의한 디바이스 뷰를 사용할 수 있습니다. 이러한 보기에는 디바이스 원격 분석 및 속성 값이 표시 될 수 있습니다. 예를 들어 앞의 스크린샷에 표시된 **개요** 보기가 있습니다.

자세한 내용은 운영자가 디바이스 그룹 및 기본 제공 분석 기능을 사용할 수 있습니다. 자세한 정보를 알아보려면 [분석을 사용하여 디바이스 데이터를 분석하는 방법](howto-create-analytics.md)을 참조하세요.

운영자는 개별 디바이스를 관리하기 위해 디바이스 보기를 사용하여 디바이스 및 클라우드 속성을 설정하고 디바이스 명령을 호출할 수 있습니다. 예를 들어 이전 스크린샷에서 **디바이스 관리** 및 **명령** 보기를 포함합니다.

디바이스를 대량으로 관리하기 위해 운영자는 작업을 만들고 예약할 수 있습니다. 작업은 속성을 업데이트하고 여러 디바이스에서 명령을 실행할 수 있습니다. 더 자세한 정보는 [Azure IoT Central 애플리케이션에서 작업 만들기 및 실행](howto-manage-devices-in-bulk.md)을 참고하여 주십시오.

## <a name="troubleshoot-and-remediate-issues"></a>문제 해결 및 수정

운영자는 애플리케이션 및 해당 디바이스의 디바이스를 담당합니다. [문제 해결 가이드](troubleshoot-connection.md)를 통해 운영자는 일반적인 문제를 진단하고 수정할 수 있습니다. 운영자는 **디바이스** 페이지를 사용하여 문제가 해결될 때까지 작동하지 않는 것으로 보이는 디바이스를 차단할 수 있습니다.

## <a name="add-and-remove-devices"></a>디바이스 추가 및 제거

운영자는 개별적으로 또는 대량으로 IoT Central 애플리케이션에 디바이스를 추가하고 제거할 수 있습니다. 더 자세한 정보는 [Azure IoT Central 애플리케이션에서 디바이스 관리](howto-manage-devices-individually.md)를 참고하여 주십시오.

## <a name="personalize"></a>개인 설정

운영자는 자주 사용하는 리소스에 대한 링크를 포함하는 IoT Central 애플리케이션에서 개인 대시보드를 만들 수 있습니다. 자세한 내용은 [대시보드 관리](howto-manage-dashboards.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

IoT Central 사용에 대해 자세히 알아보려면 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md)부터 시작하여 빠른 시작을 완료하세요.
