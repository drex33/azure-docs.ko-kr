---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 9de7b8c91b1dfdbf10b4e8f5e61e9c8cd9f68abf
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300260"
---
이 섹션에서는 [Azure Portal](https://portal.azure.com)을 사용하여 IoT Hub를 만드는 방법에 대해 설명합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure 홈페이지에서 **+리소스 만들기** 를 클릭한 다음, **Marketplace 검색** 필드에 *IoT Hub* 를 입력합니다.

1. 검색 결과에서 **IoT Hub**, **만들기** 를 차례로 선택합니다.

1. **기본** 탭에서 다음과 같이 필드를 완성합니다.

   - **구독**: 허브에 사용할 구독을 선택합니다.

   - **리소스 그룹**: 리소스 그룹을 선택하거나 새로 만듭니다. 새 리소스 그룹을 만들려면 **새로 만들기** 를 선택하고 사용하려는 이름을 입력합니다. 기존 리소스 그룹을 사용하려면 해당 기존 리소스 그룹을 선택합니다. 자세한 내용은 [Azure Resource Manager 리소스 그룹 관리](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)를 참조하세요.

   - **지역**: 허브를 배치할 지역을 선택합니다. 가장 가까운 위치를 선택합니다. [IoT Hub 디바이스 스트림](../articles/iot-hub/iot-hub-device-streams-overview.md)과 같은 일부 기능은 특정 지역에서만 사용할 수 있습니다. 이러한 제한된 기능의 경우 지원되는 지역 중 하나를 선택해야 합니다.

   - **IoT Hub 이름**: 허브 이름을 입력합니다. 이 이름은 전역적으로 고유해야 하며 3~50자 사이의 영숫자여야 합니다. 이름에 대시(`'-'`) 문자를 포함할 수도 있습니다.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png" alt-text="Azure Portal에서 허브를 만듭니다.":::

1. 완료되면 **다음: 네트워킹** 을 선택하여 허브를 계속 만듭니다.

   디바이스에서 IoT Hub에 연결하는 데 사용할 수 있는 엔드포인트를 선택합니다. **퍼블릭 엔드포인트(모든 네트워크)** 기본 설정을 선택하거나 **퍼블릭 엔드포인트(선택한 IP 범위)** 또는 **프라이빗 엔드포인트** 를 선택할 수 있습니다. 이 예제의 기본 설정을 적용합니다.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-network-screen.png" alt-text="연결할 수 있는 엔드포인트를 선택합니다.":::

1. 완료되면 **다음: 관리** 를 선택하여 허브를 계속 만듭니다.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-management.png" alt-text="Azure Portal을 사용하여 새 허브의 크기 및 배율을 설정합니다.":::

    여기서 기본 설정을 적용할 수 있습니다. 원하는 경우 다음 필드 중 하나를 수정할 수 있습니다.

    - **가격 책정 및 크기 조정 계층**: 선택한 계층입니다. 원하는 기능 수와 하루에 솔루션을 통해 보내는 메시지 수에 따라 여러 계층에서 선택할 수 있습니다. 무료 계층은 테스트와 평가를 위해 제공됩니다. 허브에 500개 디바이스를 연결할 수 있으며 하루에 8,000개 메시지까지 허용합니다. 각 Azure 구독은 체험 계층에 하나의 IoT Hub를 만들 수 있습니다.

      IoT Hub 디바이스 스트림용 빠른 시작을 통해 작업하는 경우 무료 계층을 선택합니다.

    - **IoT Hub 단위**: 하루 단위당 허용되는 메시지의 수는 허브의 가격 책정 계층에 따라 다릅니다. 예를 들어 허브가 700,000개의 메시지 수신을 지원하려면 S1 계층 단위 2개를 선택합니다.
    다른 계층 옵션에 대한 자세한 내용은 [적절한 IoT Hub 계층 선택](../articles/iot-hub/iot-hub-scaling.md)을 참조하세요.

    - **Defender for IoT**: IoT 및 디바이스에 추가 위협 방지 계층을 추가하려면 이 옵션을 설정합니다. 무료 계층의 허브에는 이 옵션을 사용할 수 없습니다. 이 기능에 대한 자세한 내용은 [Azure Defender for IoT](/azure/asc-for-iot/)를 참조하세요.

    - **고급 설정** > **디바이스-클라우드 파티션**: 이 속성은 디바이스-클라우드 메시지를 메시지의 동시 판독기 수와 연결합니다. 대부분의 허브에는 4개의 파티션만 필요합니다.

1. 완료되면 **다음: 태그** 를 선택하여 다음 화면으로 계속 진행합니다.

    태그는 이름/값 쌍입니다. 동일한 태그를 여러 리소스 및 리소스 그룹에 할당하여 리소스를 범주화하고 청구를 통합할 수 있습니다. 이 문서에서는 태그를 추가하지 않습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../articles/azure-resource-manager/management/tag-resources.md)을 참조하세요.

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-tags.png" alt-text="Azure Portal을 사용하여 허브에 태그를 할당합니다.":::

1. 완료되면 **다음: 검토 + 만들기** 를 선택하여 선택 사항을 검토합니다. 이 화면과 유사한 내용이 표시되지만 허브를 만들 때 선택한 값이 표시됩니다.

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-review-and-create.png" alt-text="새 허브를 만들기 위한 정보를 검토합니다.":::

1. **만들기** 를 선택하여 새 허브의 배포를 시작합니다. 허브가 만들어지는 몇 분 동안 배포가 진행됩니다. 배포가 완료되면 **리소스로 이동** 을 선택하여 새 허브를 엽니다.