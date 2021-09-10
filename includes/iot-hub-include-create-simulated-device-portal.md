---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 28c3ccbd7dc0aae27cd64c0c213869cf968c0a9f
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969358"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

다음으로 디바이스 ID를 만들고 나중에 사용할 키를 저장합니다. 이 디바이스 ID는 IoT Hub에 메시지를 보내는 시뮬레이션 애플리케이션에서 사용됩니다. 이 기능은 PowerShell이나 Azure Resource Manager 템플릿을 사용하는 경우 사용할 수 없습니다. 다음 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 시뮬레이션된 디바이스를 만드는 방법을 알려줍니다.

1. [Azure Portal](https://portal.azure.com)을 열고 사용자의 Azure 계정에 로그인합니다.

2. **리소스 그룹** 을 선택한 후 리소스 그룹을 선택합니다. 이 자습서에서는 **ContosoResources** 를 사용합니다.

3. 리소스 목록에서 IoT 허브를 선택합니다. 이 자습서에서는 **ContosoTestHub** 를 사용합니다. 허브 창에서 **IoT 디바이스** 를 선택합니다.

4. **IoT 디바이스** 창에서 **+디바이스 추가** 를 선택합니다. 추가 디바이스 창에서 디바이스 ID를 입력합니다. 이 자습서에서는 **Contoso-Test-Device** 를 사용합니다. 키를 비워 두고 **키 자동 생성** 을 선택합니다. **IoT Hub에 디바이스 연결** 이 사용하도록 설정되어 있는지 확인합니다. **저장** 을 선택합니다.

   ![디바이스 추가 화면](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. 생성되었으면 디바이스를 선택하여 생성된 키를 확인합니다. 기본 키에서 복사 아이콘을 선택하고 이 자습서의 테스트 단계를 위해 메모장 등에 저장합니다.

   ![키를 포함한 디바이스 세부 정보](./media/iot-hub-include-create-simulated-device-portal/device-details.png)