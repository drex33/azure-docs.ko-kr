---
title: Microsoft Azure IP 연결 필터 IoT DPS
description: 특정 IP 주소에서 Azure IoT DPS 인스턴스로의 연결을 차단하도록 IP 필터링을 사용하는 방법입니다.
author: anastasia-ms
ms.author: v-stharr
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 11/05/2021
ms.openlocfilehash: 01a3449526166ceb54565e73683a6d3c7dc41603
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133270"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Azure IoT DPS IP 연결 필터 사용

보안은 모든 IoT 솔루션의 중요한 측면입니다. 경우에 따라 디바이스가 보안 구성의 일부로 연결할 수 있는 IP 주소를 명시적으로 지정해야 합니다. Azure IoT Hub DPS(Device Provisioning Service)의 *IP 필터* 기능을 사용하면 특정 IPv4 주소에서 들어오는 트래픽을 거부하거나 수락하는 규칙을 구성할 수 있습니다.

## <a name="when-to-use"></a>사용 시기

특정 IP 주소에서 DPS 엔드포인트로의 연결을 차단하는 것이 유용한 두 가지 사용 사례가 있습니다.

* DPS는 지정된 범위의 IP 주소에서 오는 트래픽만 수신하고 이외의 트래픽은 거부해야 합니다. 예를 들어 DPS를 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)와 사용하여 DPS와 디바이스 간의 프라이빗 연결을 만드는 경우입니다.

* DPS 관리자에 의해 의심스러운 것으로 식별된 IP 주소에서 오는 트래픽을 거부해야 합니다.

>[!Note]
>IP 필터링을 사용하도록 설정하면 더 이상 Azure Portal 사용하여 서비스 작업(예: 등록 관리)을 수행할 수 없습니다. 포털을 사용하여 서비스 작업을 수행하려면 일시적으로 IP 필터링을 비활성화하고 작업을 완료한 다음 IP 필터링 기능을 다시 사용하도록 설정해야 합니다. 사용자 고유의 클라이언트를 사용하고 IP 필터 비활성화를 방지하려면 컴퓨터의 IP 주소를 에 추가하고 `ipFilterRules` CLI를 통해 DPS에서 등록을 관리하도록 선택할 수 있습니다.

## <a name="how-filter-rules-are-applied"></a>필터 규칙이 적용되는 방식

IP 필터 규칙은 DPS 인스턴스 수준에 적용됩니다. 따라서 IP 필터 규칙은 지원되는 모든 프로토콜을 사용하는 디바이스 및 백 엔드 앱의 모든 연결에 적용됩니다.

DPS 인스턴스의 거부 IP 규칙에 일치하는 IP 주소에서 오는 모든 연결 시도는 권한 없음 401 상태 코드 및 설명을 수신합니다. 응답 메시지는 IP 규칙을 언급하지 않습니다.

## <a name="default-setting"></a>기본 설정

기본적으로 DPS에 대한 포털의 **IP 필터** 그리드는 비어 있습니다. 이러한 기본 설정은 DPS가 모든 IP 주소의 연결을 수락한다는 것을 의미합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다.

![IoT DPS 기본 IP 필터 설정](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP 필터 규칙 추가 또는 편집

IP 필터 규칙을 추가하려면 **+ IP 필터 규칙 추가** 를 선택합니다.

![IP 필터 규칙을 IoT DPS에 추가](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

**IP 필터 규칙 추가** 를 선택한 후 필드를 입력합니다.

![IP 필터 규칙 추가를 선택한 후](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* IP 필터 규칙의 **이름** 을 제공합니다. 이름은 최대 128자 길이의 대/소문자를 구분하는 고유한 영숫자 문자열이어야 합니다. ASCII 7 비트 영숫자 및 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`만 허용됩니다.

* 단일 IPv4 주소 또는 CIDR 표기법으로 IP 주소 블록을 제공합니다. 예를 들어 CIDR 표기법 192.168.100.0/22는 192.168.100.0에서 192.168.103.255까지 IPv4 주소 1024개를 나타냅니다.

* IP 필터 규칙에 대한 **작업** 으로 **허용** 또는 **차단** 을 선택합니다.

필드를 채운 후 **저장** 을 선택하여 규칙을 저장합니다. 업데이트가 진행 중임을 알리는 경고가 표시됩니다.

![IP 필터 규칙 저장에 대한 알림](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

최대 100개의 IP 필터 규칙에 도달하면 **추가** 옵션이 사용하지 않도록 설정됩니다.

기존 규칙을 편집하려면 변경하려는 데이터를 선택하고 변경 내용을 적용한 다음, **저장** 을 선택하여 편집 내용을 저장합니다.

> [!NOTE]
> IP 주소를 거부하면 다른 Azure 서비스가 DPS 인스턴스와 상호 작용하는 것을 방지할 수 있습니다.

## <a name="delete-an-ip-filter-rule"></a>IP 필터 규칙 삭제

IP 필터 규칙을 삭제하려면 해당 행에서 휴지통 아이콘을 선택하고 **저장** 을 선택합니다. 규칙이 제거되고 변경 내용이 저장됩니다.

![IoT DPS IP 필터 규칙 삭제](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>코드에서 IP 필터 규칙 업데이트

Azure 리소스 공급자의 REST 엔드포인트를 사용하여 DPS IP 필터를 검색 및 수정할 수도 있습니다. [createorupdate 메서드](/rest/api/iot-dps/iotdpsresource/createorupdate)의 `properties.ipFilterRules`를 참조하세요.

DPS IP 필터 규칙 업데이트는 현재 Azure CLI 또는 Azure PowerShell에서 지원되지 않지만 Azure Resource Manager 템플릿으로 수행할 수 있습니다. Resource Manager 템플릿 사용에 대한 지침은 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 참조하세요. 다음 템플릿 예는 DPS IP 필터 규칙을 생성, 편집 및 삭제하는 방법을 보여줍니다.

### <a name="add-an-ip-filter-rule"></a>IP 필터 규칙 추가

다음 템플릿 예에서는 모든 트래픽을 허용하는 "AllowAll"이라는 새 IP 필터 규칙을 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

요구 사항에 따라 템플릿의 IP 필터 규칙 속성을 업데이트합니다.

| attribute                | Description |
| ------------------------ | ----------- |
| **FilterName**           | IP 필터 규칙의 이름을 제공합니다. 이름은 최대 128자 길이의 대/소문자를 구분하는 고유한 영숫자 문자열이어야 합니다. ASCII 7비트 영숫자 및  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` 만 허용됩니다. |
| **동작**               | 허용되는 값은 IP 필터 규칙에 대한 작업으로 **수락**  또는  **거부** 입니다. |
| **ipMask**               | 단일 IPv4 주소 또는 CIDR 표기법으로 IP 주소 블록을 제공합니다. 예를 들어 CIDR 표기법 192.168.100.0/22는 192.168.100.0에서 192.168.103.255까지 IPv4 주소 1024개를 나타냅니다. |


### <a name="update-an-ip-filter-rule"></a>IP 필터 규칙 업데이트

다음 템플릿 예에서는 이전에 표시된 "AllowAll"이라는 IP 필터 규칙을 업데이트하여 모든 트래픽을 거부합니다.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>IP 필터 규칙 삭제

다음 템플릿 예에서는 DPS 인스턴스에 대한 모든 IP 필터 규칙을 삭제합니다.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>IP 필터 규칙 평가

IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

예를 들어 192.168.100.0/22 범위의 주소를 수락하고 그 외의 주소는 거부하려는 경우 그리드에 있는 첫 번째 규칙이 주소 범위 192.168.100.0/22를 수락해야 합니다. 다음 규칙은 0.0.0.0/0 범위를 사용하여 모든 주소를 거부해야 합니다.

행의 시작 부분에 있는 세 개의 세로 점을 클릭하고 끌어서 놓기를 사용하여 그리드에서 IP 필터 규칙의 순서를 변경할 수 있습니다.

새 IP 필터 규칙 순서를 저장하려면 **저장** 을 클릭합니다.

![DPS IP 필터 규칙의 순서 변경](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>다음 단계

관리 DPS에 대해 자세히 살펴보려면 다음을 참조하세요.

* [IoT DPS IP 주소 이해](iot-dps-understand-ip-address.md)
* [Azure CLI를 사용하여 DPS 설정](quick-setup-auto-provision-cli.md)
* [DPS에 대한 액세스 제어](how-to-control-access.md)
