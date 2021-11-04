---
title: 포함 파일
description: 포함 파일
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/20/2021
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0a2e035cdc5118cc7d952e4046e9093f95094585
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070414"
---
IoT Central 애플리케이션에서 데이터를 이벤트 허브로 안전하게 내보낼 수 있도록 하는 관리 ID를 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)IoT Central 애플리케이션으로 이동합니다.

    > [!TIP]
    > 기본적으로 IoT Central 애플리케이션은 구독의 **IOTC** 리소스 그룹에 만들어집니다.

1. **ID** 를 선택합니다. 그런 다음 **시스템 할당** 페이지에서 상태를 **켜기로** 변경한 **다음, 저장을** 선택합니다.

1. 몇 초 후에 IoT Central 애플리케이션에 대해 시스템이 할당한 관리 ID를 사용하도록 설정되고 **Azure 역할 할당을** 선택할 수 있습니다.

    :::image type="content" source="media/iot-central-managed-identity/azure-role-assignments.png" alt-text="Azure Portal IoT Central 애플리케이션의 ID 페이지 스크린샷.":::

1. Azure **역할 할당** 페이지에서 **+ 역할 할당 추가를** 선택합니다.
