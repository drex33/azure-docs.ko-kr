---
title: Media Services 계정 ARM 템플릿
description: 이 문서에서는 ARM 템플릿을 사용하여 미디어 서비스 계정을 만드는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.topic: quickstart
ms.date: 03/23/2021
ms.author: inhenkel
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: e694e3558e8b38342c50be698355ef2f58fa3209
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133071402"
---
# <a name="quickstart-media-services-account-arm-template"></a>빠른 시작: Media Services 계정 ARM 템플릿

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 미디어 서비스 계정을 만드는 방법을 보여줍니다.

## <a name="introduction"></a>소개

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

ARM 템플릿에 익숙한 리더는 [배포 섹션](#deploy-the-template)을 계속 진행할 수 있습니다.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.media%2Fmedia-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이전에 ARM 템플릿을 배포한 적이 없다면 [Azure ARM 템플릿](../../azure-resource-manager/templates/index.yml)에 대해 읽고 [자습서](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)를 살펴보는 것이 좋습니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/media-services-create/)에서 나온 것입니다.

JSON 코드 펜스의 구문은 다음과 같습니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.media/media-services-create/azuredeploy.json":::

템플릿에 정의된 세 가지 Azure 리소스 유형은 다음과 같습니다.

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): 스토리지 계정 만들기
- [Microsoft.Media/mediaservices](/azure/templates/microsoft.media/mediaservices): Media Services 계정 만들기

## <a name="set-the-account"></a>계정 설정

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>배포 파일에 변수 할당

편의를 위해 템플릿 파일의 경로를 저장하는 변수를 만듭니다. 해당 변수를 사용하면 배포할 때마다 경로를 다시 입력할 필요가 없기 때문에 배포 명령을 더 쉽게 실행할 수 있습니다.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>템플릿 배포

미디어 서비스 계정 이름을 입력하라는 메시지가 표시됩니다.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>배포된 리소스 검토

다음과 유사한 JSON 응답이 표시되어야 합니다.

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

Azure Portal에서 리소스를 만들었는지 확인합니다.

![빠른 시작 리소스 생성](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>리소스 정리

방금 만든 리소스를 사용하지 않으려는 경우 리소스 그룹을 삭제할 수 있습니다.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>다음 단계

매개 변수, 변수 등을 사용하여 템플릿을 만드는 프로세스를 수행하여 ARM 템플릿 사용에 대해 자세히 알아보려면 다음을 시도하세요.

> [!div class="nextstepaction"]
> 첫 번째 ARM 템플릿 만들기 및 배포[
