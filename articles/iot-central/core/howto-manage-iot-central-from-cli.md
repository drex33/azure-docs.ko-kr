---
title: Azure CLI 또는 PowerShell에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 Azure CLI 또는 PowerShell을 사용하여 IoT Central 애플리케이션을 만들고 관리하는 방법을 설명합니다. 이러한 도구를 사용하여 애플리케이션을 보고, 수정하고, 제거할 수 있습니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: 05eca5bb95906ebb34e51f79d70a33cc003f2220
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091638"
---
# <a name="manage-iot-central-from-azure-cli-or-powershell"></a>Azure CLI 또는 PowerShell에서 IoT Central 관리

[Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 애플리케이션을 만들고 관리하는 대신 [Azure CLI](/cli/azure/) 또는 [Azure PowerShell](/powershell/azure/)을 사용하여 애플리케이션을 관리할 수도 있습니다.

JavaScript, Python, C#, Ruby 또는 Go와 같은 언어를 사용하려는 경우 [Azure IoT Central ARM SDK 샘플](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) 리포지토리에서 Azure IoT Central 애플리케이션을 만들고, 업데이트하고, 나열하고, 삭제하는 방법을 보여 주는 코드 샘플을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [azure-powershell-requirements-no-header](../../../includes/azure-powershell-requirements-no-header.md)]

> [!TIP]
> 다른 Azure 구독에서 PowerShell 명령을 실행해야 하는 경우 [활성 구독 변경](/powershell/azure/manage-subscriptions-azureps#change-the-active-subscription)을 참조하세요.

다음 명령을 실행하여 PowerShell 환경에 [IoT Central 모듈](/powershell/module/az.iotcentral/)이 설치되었는지 확인합니다.

```powershell
Get-InstalledModule -name Az.I*
```

설치된 모듈 목록에 **Az.IotCentral** 이 포함되지 않은 경우 다음 명령을 실행합니다.

```powershell
Install-Module Az.IotCentral
```

---

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="create-an-application"></a>애플리케이션 만들기

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot central app create](/cli/azure/iot/central/app#az_iot_central_app_create) 명령을 사용하여 Azure 구독에서 IoT Central 애플리케이션을 만듭니다. 예를 들어:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

이 명령은 먼저 애플리케이션의 미국 동부 지역에 리소스 그룹을 만듭니다. 다음 표에서는 **az iot central app create** 명령에 사용되는 매개 변수를 설명합니다.

| 매개 변수         | Description |
| ----------------- | ----------- |
| resource-group    | 애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
| 위치          | 기본적으로 이 명령은 리소스 그룹의 위치를 사용합니다. 현재 **오스트레일리아 동부**, 미국 **중부**, **미국 동부**, 미국 **동부 2**, **일본 동부**, **서유럽**, **동남 아시아**, **영국 남부**, **유럽 서부** 및 **미국 서 부** 지역에서 IoT Central 응용 프로그램을 만들 수 있습니다. |
| name              | Azure Portal의 애플리케이션 이름입니다. 특수 문자를 사용하지 말고 대신 소문자(a-z), 숫자(0-9) 및 대시(-)를 사용합니다.|
| subdomain         | 애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 `https://mysubdomain.azureiotcentral.com`입니다. |
| sku               | 현재 **ST1** 또는 **ST2** 를 사용할 수 있습니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
| template          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
| display-name      | UI에 표시되는 애플리케이션 이름입니다. |

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AzIotCentralApp](/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet을 사용하여 Azure 구독에 IoT Central 애플리케이션을 만듭니다. 예를 들면 다음과 같습니다.

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

스크립트는 먼저 애플리케이션의 미국 동부 지역에 리소스 그룹을 만듭니다. 다음 표에서는 **New-AzIotCentralApp** 명령에 사용되는 매개 변수를 설명합니다.

|매개 변수         |Description |
|------------------|------------|
|ResourceGroupName |애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
|위치 |기본적으로 이 cmdlet은 리소스 그룹의 위치를 사용합니다. 현재 **오스트레일리아 동부**, 미국 **중부**, **미국 동부**, 미국 **동부 2**, **일본 동부**, **서유럽**, **동남 아시아**, **영국 남부**, **유럽 서부** 및 **미국 서 부** 지역에서 IoT Central 응용 프로그램을 만들 수 있습니다. |
|이름              |Azure Portal의 애플리케이션 이름입니다. 특수 문자를 사용하지 말고 대신 소문자(a-z), 숫자(0-9) 및 대시(-)를 사용합니다. |
|하위 도메인         |애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 `https://mysubdomain.azureiotcentral.com`입니다. |
|SKU               |현재 **ST1** 또는 **ST2** 를 사용할 수 있습니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
|템플릿          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
|DisplayName       |UI에 표시되는 애플리케이션 이름입니다. |

---

### <a name="application-templates"></a>애플리케이션 템플릿

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

사용자 고유의 애플리케이션 템플릿을 만든 경우 이 템플릿을 사용하여 새 애플리케이션을 만들 수 있습니다. 애플리케이션 템플릿을 요청하면 앱의 [애플리케이션 템플릿 내보내기](howto-create-iot-central-application.md#create-and-use-a-custom-application-template) 섹션 아래에 있는 내보낸 앱의 URL 공유 가능 링크에 표시된 앱 ID를 입력합니다.

## <a name="view-applications"></a>애플리케이션 보기

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot central app list](/cli/azure/iot/central/app#az_iot_central_app_list) 명령을 사용하여 IoT Central 애플리케이션을 나열하고 메타데이터를 봅니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Get-AzIotCentralApp](/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet을 사용하여 IoT Central 애플리케이션을 나열하고 메타데이터를 봅니다.

---

## <a name="modify-an-application"></a>애플리케이션 수정

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) 명령을 사용하여 IoT Central 애플리케이션의 메타데이터를 업데이트합니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Set-AzIotCentralApp](/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet을 사용하여 IoT Central 애플리케이션의 메타데이터를 업데이트합니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

---

## <a name="delete-an-application"></a>애플리케이션 삭제

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot central app delete](/cli/azure/iot/central/app#az_iot_central_app_delete) 명령을 사용하여 IoT Central 애플리케이션을 삭제합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Remove-AzIotCentralApp](/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet을 사용하여 IoT Central 애플리케이션을 삭제합니다. 예를 들면 다음과 같습니다.

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

---

## <a name="next-steps"></a>다음 단계

이제 Azure CLI 또는 PowerShell에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
