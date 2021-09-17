---
title: Azure IoT Hub 관리 ID | Microsoft Docs
description: 관리 ID를 사용하여 IoT Hub에서 다른 Azure 리소스로의 송신 연결을 허용하는 방법입니다.
author: miag
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: miag
ms.openlocfilehash: e230f06c91e775de87b42fcc2112fc699f9ecafc
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449248"
---
# <a name="iot-hub-support-for-managed-identities"></a>관리 ID에 대한 IoT Hub 지원 

관리 ID는 Azure AD에서 자동으로 관리되는 ID를 안전한 방식으로 Azure 서비스에 제공합니다. 이렇게 하면 개발자가 ID를 제공하여 자격 증명을 관리할 필요가 없습니다. 관리 ID는 시스템 할당 및 사용자 할당의 두 가지 유형이 있습니다. IoT Hub는 둘 다 지원합니다. 

IoT Hub에서 관리 ID는 [메시지 라우팅](iot-hub-devguide-messages-d2c.md), [파일 업로드](iot-hub-devguide-file-upload.md) 및 [대량 디바이스 가져오기/내보내기](iot-hub-bulk-identity-mgmt.md)와 같은 기능을 위해 IoT Hub에서 다른 Azure 서비스로의 송신 연결에 사용될 수 있습니다. 이 문서에서는 다양한 기능을 위해 IoT Hub에서 시스템 할당 및 사용자 할당 관리 ID를 사용하는 방법에 대해 알아봅니다. 


## <a name="prerequisites"></a>필수 구성 요소
- 시스템 할당과 사용자가 할당한 관리 ID 간의 차이점을 이해하려면 [Azure 리소스에 대한 관리 ID](./../active-directory/managed-identities-azure-resources/overview.md) 문서를 읽어보세요.

- IoT Hub가 없는 경우 계속하기 전에 [만듭니다](iot-hub-create-through-portal.md).


## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID 

### <a name="add-and-remove-a-system-assigned-managed-identity-in-azure-portal"></a>Azure Portal에서 시스템 할당 관리 ID 추가 및 제거
1.  Azure Portal에 로그인하고 원하는 IoT Hub로 이동합니다.
2.  IoT Hub 포털에서 **ID** 로 이동합니다.
3.  **시스템 할당** 탭에서 **켜기** 를 선택하고 **저장** 을 클릭합니다.
4.  IoT Hub에서 시스템 할당 관리 ID를 제거하려면 **끄기** 를 선택하고 **저장** 을 클릭합니다.

    :::image type="content" source="./media/iot-hub-managed-identity/system-assigned.png" alt-text="IoT Hub에 대해 시스템 할당 관리 ID를 켜는 위치를 보여 주는 스크린샷":::        

### <a name="enable-system-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>ARM 템플릿을 사용하여 허브를 만들 때 시스템 할당 관리 ID 사용

리소스 프로비저닝 시간에 IoT Hub에서 시스템 할당 관리 ID를 사용하도록 설정하려면 아래 ARM(Azure Resource Manager) 템플릿을 사용합니다. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": 
    {
      "iotHubName": {
        "type": "string",
        "metadata": {
          "description": "Name of iothub resource"
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "S1",
        "metadata": {
          "description": "SKU name of iothub resource, by default is Standard S1"
        }
      },
      "skuTier": {
        "type": "string",
        "defaultValue": "Standard",
        "metadata": {
          "description": "SKU tier of iothub resource, by default is Standard"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
        }
      }
    },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
              "name": "[parameters('skuName')]",
              "tier": "[parameters('skuTier')]",
              "capacity": 1
              }
            }
          ] 
        }
      }
    }
  ]
}
```

리소스 `name`, `location`, `SKU.name` 및 `SKU.tier`의 값을 대체한 후 Azure CLI에서 다음을 사용하여 기존 리소스 그룹에 리소스를 배포할 수 있습니다.

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

리소스가 생성된 후 Azure CLI를 사용하여 허브에 할당된 시스템을 검색할 수 있습니다.

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```
## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID 
이 섹션에서는 Azure Portal을 사용하여 IoT 허브에서 사용자 할당 관리 ID를 추가하고 제거하는 방법을 알아봅니다.
1.  우선 독립 실행형 리소스로 사용자가 할당한 관리 ID를 만들어야 합니다. 이렇게 하려면 [사용자가 할당한 관리 ID 만들기](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)의 지침을 따르세요.
2.  IoT Hub로 이동하고 IoT Hub 포털에서 **ID** 로 이동합니다.
3.  **사용자 할당** 탭에서 **사용자 할당 관리 id 연결** 을 클릭 합니다. 허브에 추가할 사용자가 할당한 관리 ID를 선택한 다음 **선택** 을 클릭합니다. 
4.  IoT Hub에서 사용자 할당 ID를 제거할 수 있습니다. 제거할 사용자 할당 ID를 선택하고 **제거** 단추를 클릭합니다. IoT Hub에서만 제거하며, 이 제거는 사용자 할당 ID를 리소스로 삭제하지 않습니다. 사용자가 할당한 ID를 리소스로 삭제하려면 [사용자가 할당한 관리 ID 삭제](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#delete-a-user-assigned-managed-identity)의 지침을 따르세요.

    :::image type="content" source="./media/iot-hub-managed-identity/user-assigned.png" alt-text="IoT Hub에 대해 사용자가 할당한 관리 ID를 추가하는 방법을 보여 주는 스크린샷":::        


### <a name="enable-user-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>ARM 템플릿을 사용하여 허브를 만들 때 사용자가 할당한 관리 ID 사용
다음 예제 템플릿을 사용 하 여 사용자 할당 관리 id로 허브를 만들 수 있습니다. 이 템플릿은 이름이 *[iothub-제공 됨]-id* 이 고 생성 된 IoT hub에 할당 된 사용자 할당 id를 하나 만듭니다. 필요에 따라 템플릿을 변경 하 여 사용자 할당 id를 여러 개 추가할 수 있습니다.
 
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "iotHubName": {
      "type": "string",
      "metadata": {
        "description": "Name of iothub resource"
      }
    },
  "skuName": {
    "type": "string",
    "defaultValue": "S1",
    "metadata": {
      "description": "SKU name of iothub resource, by default is Standard S1"
    }
  },
  "skuTier": {
    "type": "string",
    "defaultValue": "Standard",
    "metadata": {
      "description": "SKU tier of iothub resource, by default is Standard"
    }
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]",
    "metadata": {
      "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
    }
  }
},
  "variables": {
    "identityName": "[concat(parameters('iotHubName'), '-identity')]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
              "name": "[variables('identityName')]",
              "apiVersion": "2018-11-30",
              "location": "[resourceGroup().location]"
            },
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                  "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]": {}
                }
              },
              "sku": {
                "name": "[parameters('skuName')]",
                "tier": "[parameters('skuTier')]",
                "capacity": 1
              },
              "dependsOn": [
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]"
              ]
            }
          ]
        }
      }
    }
  ]
}
```
```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

리소스를 만든 후 Azure CLI를 사용하여 사용자가 할당한 관리 ID를 검색할 수 있습니다.

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```
## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub에서 다른 Azure 리소스로의 송신 연결
관리 되는 id를 사용 하 여 [메시지 라우팅](iot-hub-devguide-messages-d2c.md), [파일 업로드](iot-hub-devguide-file-upload.md)및 [대량 장치 가져오기/내보내기를](iot-hub-bulk-identity-mgmt.md)위해 IoT Hub에서 다른 Azure 서비스로의 송신 연결을 설정할 수 있습니다. 스토리지 계정, 이벤트 허브 및 서비스 버스 엔드포인트를 포함하여 고객 소유 엔드포인트에 대한 각 IoT Hub 송신 연결에 사용할 관리 ID를 선택할 수 있습니다. 

## <a name="configure-message-routing-with-managed-identities"></a>관리 ID로 메시지 라우팅 구성
이 섹션에서는 이벤트 허브 사용자 지정 끝점에 대 한 [라우팅 메시지](iot-hub-devguide-messages-d2c.md) 를 예로 사용 합니다. 이 예제는 다른 라우팅 사용자 지정 끝점에 적용 됩니다. 

1.  먼저 Azure Portal 이벤트 허브로 이동하여 관리 ID에 올바른 액세스 권한을 할당해야 합니다. 이벤트 허브에서 **액세스 제어(IAM)** 탭으로 이동하고 **추가** 를 클릭한 다음 **역할 할당 추가** 를 클릭합니다. 역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

2.  **Event Hubs 데이터 보내는 사람을 역할로** 선택합니다.

    > [!NOTE] 
    > 스토리지 계정의 경우 **Storage Blob 데이터 기여자**([기여자 또는 스토리지 계정 기여자가 *아님*](../storage/blobs/assign-azure-role-data-access.md))를 **역할** 로 선택합니다. 서비스 버스의 경우 **서비스 버스 데이터 보낸 사람** 을 **역할** 로 선택합니다.

3.  사용자 할당의 경우 **다음에 대한 액세스 할당** 에서 **사용자가 할당한 관리 ID** 를 선택합니다. 드롭다운 목록에서 구독 및 사용자가 할당한 관리 ID를 선택합니다. **저장** 단추를 클릭합니다.

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-user-assigned.png" alt-text="사용자가 할당한 IoT Hub 메시지 라우팅":::

4.  시스템 할당의 경우 **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택하고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 을 클릭합니다.

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-system-assigned.png" alt-text="시스템이 할당한 IoT Hub 메시지 라우팅":::

    VNet을 통해 사용자 지정 엔드포인트에 대한 연결을 제한해야 하는 경우에는 신뢰할 수 있는 Microsoft 자사 예외를 설정하여 IoT Hub에 특정 엔드포인트에 대한 액세스 권한을 부여해야 합니다. 예를 들어 이벤트 허브 사용자 지정 엔드포인트를 추가하는 경우 이벤트 허브의 **방화벽 및 가상 네트워크** 탭으로 이동하여 **선택한 네트워크에서 액세스 허용** 옵션을 사용하도록 설정합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스가 이벤트 허브에 액세스하도록 허용합니다** 확인란을 선택합니다. **저장** 단추를 클릭합니다. 이는 스토리지 계정 및 Service Bus에도 적용됩니다. [가상 네트워크에 대한 IoT Hub 지원](./virtual-network-support.md)에 대해 자세히 알아봅니다.

    > [!NOTE]
    > IoT Hub에서 사용자 지정 엔드포인트로 이벤트 허브를 추가하기 전에 관리 ID를 올바른 액세스 권한으로 할당하려면 위의 단계를 완료해야 합니다. 역할 할당이 전파되기까지 몇 분 정도 기다려 주세요. 

5. 다음으로, IoT Hub로 이동합니다. 허브에서 **메시지 라우팅** 으로 이동한 다음 **사용자 지정 엔드포인트** 를 클릭합니다. **추가** 를 클릭하고 사용할 엔드포인트 유형을 선택합니다. 이 섹션에서는 이벤트 허브를 예로 사용합니다.
6.  페이지 하단에서 기본 **인증 유형** 을 선택합니다. 이 섹션에서는 **사용자 지정** 을 예로 사용합니다. 드롭다운에서 기본 사용자가 할당한 관리 ID를 선택한 다음 **만들기** 를 클릭합니다.

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-routing-endpoint.png" alt-text="사용자가 할당된 IoT Hub 이벤트 허브":::

7. 사용자 지정 엔드포인트를 만들었습니다. 
8. 만든 후에도 인증 유형을 변경할 수 있습니다. 인증 유형을 변경할 사용자 지정 엔드포인트를 선택한 다음 **인증 유형 변경** 을 클릭합니다.

    :::image type="content" source="./media/iot-hub-managed-identity/change-authentication-type.png" alt-text="IoT Hub 인증 유형":::

9. 이 엔드포인트에 대해 업데이트할 새 인증 유형을 선택하고 **저장** 을 클릭합니다.

## <a name="configure-file-upload-with-managed-identities"></a>관리 ID로 파일 업로드 구성
IoT Hub의 [파일 업로드](iot-hub-devguide-file-upload.md) 기능에서는 디바이스가 고객 소유 스토리지 계정으로 파일을 업로드할 수 있도록 허용합니다. 파일 업로드가 기능하도록 하려면 IoT Hub가 모두 스토리지 계정에 연결되어 있어야 합니다. 메시지 라우팅과 마찬가지로 Azure Storage 계정에 대한 IoT Hub 송신 연결을 위해 기본 인증 유형 및 관리 ID를 선택할 수 있습니다. 

1. Azure Portal에서 스토리지 계정의 **액세스 제어(IAM)** 탭으로 이동하여 **역할 할당 추가** 섹션에서 **추가** 를 클릭합니다.
2. 역할로 **Storage Blob 데이터 기여자**(기여자 또는 스토리지 계정 기여자 아님)를 선택합니다.
3. 사용자 할당의 경우 다음에 대한 액세스 할당에서 **사용자가 할당한 관리 ID** 를 선택합니다. 드롭다운 목록에서 구독 및 사용자가 할당한 관리 ID를 선택합니다. **저장** 단추를 클릭합니다.
4. 시스템 할당의 경우 **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택하고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 을 클릭합니다.

    VNet을 통해 스토리지 계정에 대한 연결을 제한해야 하는 경우 IoT Hub에 스토리지 계정에 대한 액세스 권한을 부여하기 위해 신뢰할 수 있는 Microsoft 자사 예외를 설정해야 합니다. 스토리지 계정 리소스 페이지에서 **방화벽 및 가상 네트워크** 탭으로 이동하여 **선택한 네트워크에서 액세스 허용** 옵션을 사용하도록 설정합니다. **예외** 목록에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다** 확인란을 선택합니다. **저장** 단추를 클릭합니다. [가상 네트워크에 대한 IoT Hub 지원](./virtual-network-support.md)에 대해 자세히 알아봅니다. 


    > [!NOTE]
    > 관리 ID를 사용하여 파일 업로드를 위해 IoT Hub에 스토리지 계정을 저장하기 전에 관리 ID에 올바른 액세스 권한을 할당하려면 위의 단계를 완료해야 합니다. 역할 할당이 전파되기까지 몇 분 정도 기다려 주세요. 
 
5. IoT Hub의 리소스 페이지에서 **파일 업로드** 탭으로 이동합니다.
6. 표시되는 페이지에서 Blob Storage에서 사용할 컨테이너를 선택하고, **파일 알림 설정, SAS TTL, 기본 TTL 및 최대 전달 횟수** 를 원하는 대로 구성합니다. 기본 인증 유형을 선택하고 **저장** 을 클릭합니다. 이 단계에서 오류가 발생하면 **모든 네트워크** 에서 액세스할 수 있도록 스토리지 계정을 임시로 설정한 다음 다시 시도합니다. 파일 업로드 구성이 완료되면 스토리지 계정에서 방화벽을 구성할 수 있습니다.

    :::image type="content" source="./media/iot-hub-managed-identity/file-upload.png" alt-text="msi로 IoT Hub 파일 업로드":::

    > [!NOTE]
    > 파일 업로드 시나리오에서 허브와 디바이스는 모두 스토리지 계정에 연결해야 합니다. 위의 단계는 원하는 인증 유형을 사용하여 스토리지 계정에 IoT 허브를 연결하기 위한 것입니다. 계속 SAS URI를 사용하여 디바이스를 스토리지에 연결해야 합니다. 현재 SAS URI는 연결 문자열을 사용하여 생성됩니다. 관리 ID를 사용하여 SAS URI를 생성하는 지원을 곧 추가할 예정입니다. [파일 업로드](iot-hub-devguide-file-upload.md)의 다음 단계를 따르세요.

## <a name="configure-bulk-device-importexport-with-managed-identities"></a>관리 ID로 대량 디바이스 가져오기/내보내기 구성

IoT Hub는 고객이 제공한 스토리지 Blob에서/(으)로 디바이스의 정보를 대량으로 [가져오기/내보내기](iot-hub-bulk-identity-mgmt.md)하는 기능을 지원합니다. 이 기능을 사용하려면 IoT Hub에서 스토리지 계정으로의 연결이 필요합니다. 

1. Azure Portal에서 스토리지 계정의 **액세스 제어(IAM)** 탭으로 이동하여 **역할 할당 추가** 섹션에서 **추가** 를 클릭합니다.
2. 역할로 **Storage Blob 데이터 기여자**(기여자 또는 스토리지 계정 기여자 아님)를 선택합니다.
3. 사용자 할당의 경우 다음에 대한 액세스 할당에서 **사용자가 할당한 관리 ID** 를 선택합니다. 드롭다운 목록에서 구독 및 사용자가 할당한 관리 ID를 선택합니다. **저장** 단추를 클릭합니다.
4. 시스템 할당의 경우 **다음에 대한 액세스 할당** 에서 **사용자, 그룹 또는 서비스 주체** 를 선택하고 드롭다운 목록에서 IoT Hub의 리소스 이름을 선택합니다. **저장** 을 클릭합니다.


### <a name="using-rest-api-or-sdk-for-import-and-export-jobs"></a>가져오기 및 내보내기 작업에 REST API 또는 SDK 사용

이제 Azure IoT REST API를 사용하여 가져오기 및 내보내기 작업을 만들 수 있습니다. 요청 본문에 다음 속성을 제공해야 합니다.

- **storageAuthenticationType**: 값을 **identityBased** 로 설정합니다. 
- **inputBlobContainerUri**: 가져오기 작업에서만 이 속성을 설정합니다.
- **outputBlobContainerUri**: 가져오기 및 내보내기 작업 모두에 대해 이 속성을 설정합니다.
- **identity**: 사용할 관리 ID로 값을 설정합니다.


Azure IoT Hub SDK는 서비스 클라이언트의 레지스트리 관리자에서도 이 기능을 지원합니다. 다음 코드 조각은 C# SDK를 사용하여 가져오기 작업 또는 내보내기 작업을 시작하는 방법을 보여 줍니다.

**C# 코드 조각**

```csharp
    // Create an export job
 
    using RegistryManager srcRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForExportJob(
        outputBlobContainerUri: blobContainerUri,
        excludeKeysInExport: false,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```
 
```csharp
    // Create an import job
    
    using RegistryManager destRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForImportJob(
        inputBlobContainerUri: blobContainerUri,
        outputBlobContainerUri: blobContainerUri,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```  

**Python 코드 조각**

```python
# see note below
iothub_job_manager = IoTHubJobManager("<IoT Hub connection string>")

# Create an import job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="import",
    input_blob_container_uri="<input container URI>",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    )
))

# Create an export job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="export",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    exclude_keys_in_export=True,
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    ) 
))
```

> [!NOTE]
> - **storageAuthenticationType** 이 **identityBased** 로 설정되고 **userAssignedIdentity** 속성이 **null** 이 아닌 경우 작업은 지정된 사용자가 할당한 관리 ID를 사용합니다.
> - IoT Hub가 **userAssignedIdentity** 에 지정된 사용자가 할당한 관리 ID로 구성되지 않은 경우 작업이 실패합니다.
> - **storageAuthenticationType** 이 **identityBased** 로 설정된 경우 **userAssignedIdentity** 속성이 null이면 작업에서 시스템 할당 ID를 사용합니다.
> - IoT Hub가 사용자가 할당한 관리 ID로 구성되지 않은 경우 작업이 실패합니다.
> - **storageAuthenticationType** 이 **identityBased** 로 설정되고 **사용자 할당** 및 **시스템 할당** 관리 ID가 허브에 구성되지 않은 경우 작업이 실패합니다.

## <a name="sdk-samples"></a>SDK 샘플
- [.NET SDK 샘플](https://aka.ms/iothubmsicsharpsample)
- [Java SDK 샘플](https://aka.ms/iothubmsijavasample)
- [Python SDK 샘플](https://aka.ms/iothubmsipythonsample)
- Node.js SDK 샘플: [대량 디바이스 가져오기](https://aka.ms/iothubmsinodesampleimport), [대량 디바이스 내보내기](https://aka.ms/iothubmsinodesampleexport)

## <a name="next-steps"></a>다음 단계

IoT Hub 기능에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [메시지 라우팅](./iot-hub-devguide-messages-d2c.md)
* [파일 업로드](./iot-hub-devguide-file-upload.md)
* [대량 디바이스 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md)