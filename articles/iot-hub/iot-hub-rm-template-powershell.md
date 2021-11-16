---
title: 템플릿을 사용하여 Azure IoT Hub 만들기(PowerShell) | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Azure PowerShell로 IoT Hub를 만드는 방법입니다.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0efd7f4c8106408a870e90de2f3adebe1c80f2b4
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552435"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Azure Resource Manager 템플릿을 사용하여 IoT Hub 만들기(PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manager 템플릿을 사용하여 IoT Hub 및 소비자 그룹을 만드는 방법을 알아봅니다. Resource Manager 템플릿은 솔루션에 배포해야 하는 리소스를 정의하는 JSON 파일입니다. Resource Manager 템플릿을 개발하는 방법에 대한 자세한 내용은 [Azure Resource Manager 설명서](../azure-resource-manager/index.yml)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이 빠른 시작에서 사용되는 Resource Manager 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/iothub-with-consumergroup-create/)에서 가져온 것입니다. 다음은 템플릿의 복사본입니다.

[!code-json[iothub-creation](~/quickstart-templates/quickstarts/microsoft.devices/iothub-with-consumergroup-create/azuredeploy.json)]

템플릿은 엔드포인트 3개(eventhub, cloud-to-device, messaging)와 소비자 그룹 1개를 사용하여 Azure Iot 허브를 만듭니다. 더 많은 템플릿 샘플을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)을 참조하세요. Iot Hub 템플릿 스키마는 [여기](/azure/templates/microsoft.devices/iothub-allversions)에서 확인할 수 있습니다.

템플릿을 배포하는 방법에는 여러 가지가 있습니다.  이 자습서에서는 Azure PowerShell을 사용합니다.

PowerShell 스크립트를 실행하려면 **사용해 보기** 를 선택하여 Azure Cloud Shell을 엽니다. 스크립트를 붙여넣으려면 셸을 마우스 오른쪽 단추로 클릭하고 붙여넣기를 선택합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.devices/iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

PowerShell 스크립트에서 확인할 수 있듯이, 사용되는 템플릿은 Azure 빠른 시작 템플릿에서 가져온 것입니다. 사용자 고유의 템플릿을 사용하려면 먼저 템플릿 파일을 Cloud Shell에 업로드한 다음 `-TemplateFile` 스위치를 사용하여 파일 이름을 지정해야 합니다.  예제는 [템플릿 배포](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿을 사용하여 IoT 허브를 배포했으므로 이제 구체적인 내용을 살펴보시기 바랍니다.

* [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.
* Azure Resource Manager의 기능에 대해 자세히 알아보려면 [Azure Resource Manager 개요][lnk-azure-rm-overview]를 참조하세요.
* 템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.Devices 리소스 종류](/azure/templates/microsoft.devices/iothub-allversions)를 참조하세요.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md
