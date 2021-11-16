---
title: PowerShell cmdlet을 사용하여 Azure IoT Hub 만들기 | Microsoft Docs
description: PowerShell cmdlet을 사용하여 리소스 그룹을 생성한 다음 리소스 그룹에서 IoT hub를 생성하는 방법에 대해 알아봅니다. 허브를 제거하는 방법에 대해서도 알아봅니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1730887d38edf2d64a3456a188a50c3becaabbad
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551842"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>New-AzIotHub cmdlet을 사용하여 IoT Hub 생성하기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>소개

Azure PowerShell cmdlet을 사용하여 Azure IoT Hub를 만들고 관리할 수 있습니다. 이 자습서에서는 PowerShell로 IoT Hub를 만드는 방법을 보여 줍니다.

이 방법 문서를 완료하려면 Azure 구독이 필요합니다. Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Azure 구독에 연결

Cloud Shell을 사용하는 경우 이미 구독에 로그인되어 있습니다. 대신 PowerShell을 로컬로 실행하는 경우 다음 명령을 입력하여 Azure 구독에 로그인합니다.

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

IoT Hub를 배포할 리소스 그룹이 필요합니다. 기존 리소스 그룹을 사용하거나 리소스 그룹을 새로 만들 수 있습니다.

IoT 허브에 대한 리소스 그룹을 생성하려면 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) 명령을 사용합니다. 이 예에서는 **미국 동부** 지역에 **MyIoTRG1** 이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 단계에서 생성한 리소스 그룹에 IoT 허브를 생성하려면 [New-AzIotHub](/powershell/module/az.IotHub/New-azIotHub) 명령을 사용합니다. 이 예제에서는 **미국 동부** 지역에 **MyTestIoTHub** 라는 **S1** 허브를 만듭니다.

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT 허브의 이름은 전역적으로 고유해야 합니다.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[Get-AzIotHub](/powershell/module/az.IotHub/Get-azIotHub) 명령을 사용하여 구독에 있는 모든 IoT 허브를 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzIotHub
```

이 예제에서는 이전 단계에서 만든 S1 표준 IoT Hub를 보여 줍니다.

[Remove-AzIotHub](/powershell/module/az.iothub/remove-aziothub) 명령을 사용하여 IoT 허브를 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

또는 [Remove-AzResourceGroup](/powershell/module/az.Resources/Remove-azResourceGroup) 명령을 사용하여 리소스 그룹과 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>다음 단계

이제 PowerShell cmdlet을 사용하여 IoT 허브를 배포했습니다. 더 자세히 알아보려면 다음 문서를 참조하세요.

* [IoT 허브 작업을 다른 PowerShell cmdlet](/powershell/module/az.iothub/)

* [IoT Hub 리소스 공급자 REST API](/rest/api/iothub/iothubresource)

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/quickstart-linux.md)
