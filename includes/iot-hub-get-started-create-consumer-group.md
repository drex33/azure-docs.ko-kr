---
author: robinsh
ms.author: robinsh
ms.topic: include
ms.date: 07/07/2021
ms.openlocfilehash: 090a7ae8e053b82e157d1e46e740d9b3911fef99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077600"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

[소비자 그룹](../articles/event-hubs/event-hubs-features.md#event-consumers)은 앱과 Azure 서비스가 동일한 이벤트 허브 엔드포인트의 데이터를 독립적으로 사용할 수 있는 이벤트 스트림에 대한 독립적인 보기를 제공합니다. 이 섹션에서는 엔드포인트에서 데이터를 끌어올 수 있도록 이 자습서의 뒷부분에서 사용되는 IoT 허브의 기본 제공 엔드포인트에 소비자 그룹을 추가합니다.

IoT Hub에 소비자 그룹을 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub를 엽니다.

1. 왼쪽 창에서 **기본 제공 엔드포인트** 를 선택합니다. 소비자 그룹 아래의 텍스트 상자에 새 **소비자 그룹** 의 이름을 입력합니다.

   :::image type="content" source="./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png" alt-text="IoT hub에서 소비자 그룹을 만드는 방법을 보여 주는 스크린샷" border="true":::

1. 텍스트 상자 바깥쪽 아무 곳이나 클릭하여 소비자 그룹을 저장합니다.