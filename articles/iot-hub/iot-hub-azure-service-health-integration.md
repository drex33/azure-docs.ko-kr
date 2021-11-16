---
title: Azure IoT Hub 서비스 및 리소스 상태 | Microsoft Docs
description: Azure Service Health와 Azure Resource Health를 사용하여 IoT Hub 모니터링
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: lizross
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: f370d87f2c17185fb4c340dc0da4f2f7d9027264
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555303"
---
# <a name="check-iot-hub-service-and-resource-health"></a>IoT Hub 서비스 및 리소스 상태 확인

Azure IoT Hub는 [Azure Service Health 서비스](../service-health/overview.md)와 통합되어 IoT Hub 서비스와 개별 IoT Hub의 서비스 수준 상태를 모니터링할 수 있는 기능을 제공합니다. IoT Hub 서비스 또는 IoT Hub의 상태가 변경될 때 알림을 받도록 경고를 설정할 수도 있습니다. Azure Service Health 서비스는 Azure Resource Health, Azure Service Health 및 Azure 상태 페이지, 이렇게 소규모 서비스 3가지를 조합한 것입니다. 이 문서의 섹션에서는 각 서비스와 IoT Hub에 대한 해당 서비스의 관계를 자세히 설명합니다.

Azure Service Health 서비스는 IoT Hub 서비스와 개별 IoT Hub의 가용성에 영향을 줄 수 있는 중단 및 업그레이드와 같은 서비스 수준 이벤트를 모니터링하는 데 도움이 됩니다. 또한 IoT Hub는 Azure Monitor와 통합되어 특정 IoT Hub에서 발생하는 작업 오류 및 상태를 모니터링하는 데 사용할 수 있는 IoT Hub 플랫폼 메트릭과 IoT Hub 리소스 로그를 제공합니다. 자세히 알아보려면 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요.

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Azure Resource Health로 IoT Hub의 상태 확인

Azure Resource Health는 개별 리소스의 상태를 추적하는 Azure Service Health 서비스의 일부입니다. 포털에서 직접 IoT Hub의 상태를 확인할 수 있습니다.

포털을 사용하여 IoT Hub의 상태 및 상태 기록을 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.

1. 왼쪽 창의 **지원 + 문제 해결** 에서 **리소스 상태** 를 선택합니다.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="IoT Hub에 대한 리소스 상태 페이지":::

Azure Resource Health와 상태 데이터 해석 방법에 대한 자세한 내용은 Azure Service Health 설명서의 [Resource Health 개요](../service-health/resource-health-overview.md)를 참조하세요.

IoT Hub의 상태가 변경될 때 트리거할 경고를 설정하려면 **리소스 상태 경고 추가** 를 선택할 수도 있습니다. 자세한 내용은 Azure Service Health 설명서의 [서비스 상태 이벤트에 대한 경고 구성](../service-health/alerts-activity-log-service-notifications-portal.md)과 관련 항목을 참조하세요.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Azure Service Health를 사용하여 구독에서 IoT Hub의 상태 확인

Azure Service Health를 사용하여 구독에 있는 모든 IoT Hub의 상태를 확인할 수 있습니다.

IoT Hub의 상태를 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **서비스 상태** > **리소스 상태** 로 이동합니다.

3. 드롭다운 상자에서 구독을 선택한 다음 리소스 유형으로 **IoT Hub** 를 선택합니다.

Azure Service Health와 상태 데이터 해석 방법에 대한 자세한 내용은 Azure Service Health 설명서의 [Service Health 개요](../service-health/service-health-overview.md)를 참조하세요.

Azure Service Health를 사용하여 경고를 설정하는 방법에 대한 자세한 내용은 Azure Service Health 설명서의 [서비스 상태 이벤트에 대한 경고 구성](../service-health/alerts-activity-log-service-notifications-portal.md)과 관련 항목을 참조하세요.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Azure 상태 페이지에서 지역별 IoT Hub 서비스의 상태 확인

지역 전체의 IoT Hub와 기타 서비스의 상태를 확인하려면 [Azure 상태 페이지](https://status.azure.com/status)를 사용할 수 있습니다. Azure 상태 페이지에 대한 자세한 내용은 Azure Service Health 설명서에서 [Azure 상태 개요](../service-health/azure-status-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Service Health, Azure Resource Health, Azure 상태 페이지에 대한 자세한 내용은 [Azure Service Health 서비스](../service-health/overview.md)를 참조하세요.
* Azure IoT Hub 모니터링에 대한 설명은 [Azure IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요.
