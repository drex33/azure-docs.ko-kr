---
title: Azure Certified Device 프로그램 - 자습서 - 인증 프로그램 선택
description: 디바이스에 적합한 인증 프로그램을 선택하는 단계별 가이드
author: Chuckb1300
ms.author: cbroad
ms.service: certification
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: c1174ec9d79241f188ec4e578ffd823e6a91c0e2
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132761253"
---
# <a name="tutorial-select-your-certification-program"></a>자습서: 인증 프로그램 선택

Azure Certified Device 프로그램 선택을 축하합니다! 인증된 디바이스의 에코시스템에 참여하게 되어 기쁘게 생각합니다. 시작하려면 먼저 디바이스 기능에 가장 적합한 인증 프로그램을 결정해야 합니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * 디바이스에 가장 적합한 인증 프로그램 선택

## <a name="selecting-a-certification-program-for-your-device"></a>디바이스에 사용할 인증 프로그램 선택

모든 디바이스는 **Azure Certified Device** 인증에 설명된 기준 요구 사항을 충족해야 합니다. 디바이스를 더 잘 승격하고 분리할 수 있도록 추가 기능의 유효성을 검사하는 선택적 인증 프로그램("IoT 플러그 앤 플레이", "Edge Managed" 및 "Edge Secured-core *미리 보기")을 제공합니다.

1. 아래 표의 각 인증 프로그램을 검토하여 디바이스를 승격하는 데 가장 적합한 프로그램을 식별할 수 있습니다.

    |프로그램 요구 사항|프로세서|Architecture|OS|
    ---|---|---|---
    [Azure Certified Device](./program-requirements-azure-certified-device.md)|모두|모두|모두|
    [IoT 플러그 앤 플레이](./program-requirements-edge-secured-core.md)|모두|모두|모두|
    [Edge Managed](./program-requirements-edge-managed.md)|MPU/CPU|ARM/x86/AMD64|[계층 1 OS](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    [*Edge Secured-core](./program-requirements-edge-secured-core.md)|MPU/CPU|ARM/AMD64|[계층 1 OS](../iot-edge/support.md?view=iotedge-2018-06&preserve-view=true)|
    

1. 선택한 프로그램에 대한 특정 요구 사항을 검토하고 디바이스가 Azure에 연결하여 요구 사항의 유효성을 검사할 준비가 되었는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

이제 디바이스 인증을 시작할 준비가 되었습니다. 다음 문서로 이동하여 프로젝트를 시작합니다.
> [!div class="nextstepaction"]
>[자습서: 프로젝트 만들기](tutorial-01-creating-your-project.md)
