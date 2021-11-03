---
title: Azure IoT Hub IP 필터 | Microsoft Docs
description: 특정 IP 주소에서 Azure IoT 허브로의 연결을 허용하도록 IP 필터링을 사용하는 방법입니다.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: jlian
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b984b7ee718a051456f8f51e5c9a49c247b884b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040566"
---
# <a name="use-ip-filters"></a>IP 필터 사용

보안은 Azure IoT Hub를 기반으로 하는 모든 IoT 솔루션의 중요한 측면입니다. 경우에 따라 디바이스가 보안 구성의 일부로 연결할 수 있는 IP 주소를 명시적으로 지정해야 합니다. *IP 필터* 기능을 사용하면 특정 IPv4 주소에서 들어오는 트래픽을 거부하거나 수락하는 규칙을 구성할 수 있습니다.

## <a name="when-to-use"></a>사용 시기

IP 필터를 사용하여 지정된 범위의 IP 주소에서 오는 트래픽만 수신하고 이외의 트래픽을 거부합니다. 예를 들어 IoT 허브를 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)와 함께 사용하여 IoT 허브와 온-프레미스 인프라 간의 프라이빗 연결을 만드는 경우입니다.

## <a name="default-setting"></a>기본 설정

IP 필터 설정 페이지로 이동하려면 **네트워킹**, **공용 액세스** 를 차례로 선택한 다음, **선택한 IP 범위** 를 선택합니다.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="기본 IP 필터 설정을 설정하는 방법을 보여주는 스크린샷.":::

기본적으로 IoT Hub에 대한 포털의 **IP 필터** 그리드는 비어있습니다. 이 기본 설정은 허브가 모든 IP 주소의 연결을 차단한다는 것을 의미합니다. 이 기본 설정은 `0.0.0.0/0` IP 주소 범위를 차단하는 규칙과 같습니다.

## <a name="add-or-edit-an-ip-filter-rule"></a>IP 필터 규칙 추가 또는 편집

IP 필터 규칙을 추가하려면 **+ IP 필터 규칙 추가** 를 선택합니다. 컴퓨터의 IP 주소를 빠르게 추가하려면 **클라이언트 IP 주소 추가** 를 클릭합니다. 

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="IoT Hub에 IP 필터 규칙을 추가하는 방법을 보여주는 스크린샷":::

**IP 필터 규칙 추가** 를 선택한 후 필드를 입력합니다. 클라이언트 IP 주소를 추가하도록 선택한 경우 해당 필드는 미리 채워집니다.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="IP 필터 규칙을 추가한 후 수행할 작업을 보여 주는 스크린샷":::

* IP 필터 규칙의 **이름** 을 제공합니다. 이름은 최대 128자 길이의 대/소문자를 구분하는 고유한 영숫자 문자열이어야 합니다. ASCII 7 비트 영숫자 및 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`만 허용됩니다.

* 단일 IPv4 주소 또는 CIDR 표기법으로 IP 주소 블록을 제공합니다. 예를 들어 CIDR 표기법 192.168.100.0/22는 192.168.100.0에서 192.168.103.255까지 IPv4 주소 1024개를 나타냅니다.

필드를 채운 후 **저장** 을 선택하여 규칙을 저장합니다. 업데이트가 진행 중임을 알리는 경고가 표시됩니다.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="IP 필터 규칙 저장에 대한 알림을 보여 주는 스크린샷":::

최대 10개의 IP 필터 규칙에 도달하면 **추가** 옵션이 비활성화됩니다.

기존 규칙을 편집하려면 변경하려는 데이터를 선택하고 변경 내용을 적용한 다음, **저장** 을 선택하여 편집 내용을 저장합니다.

## <a name="delete-an-ip-filter-rule"></a>IP 필터 규칙 삭제

IP 필터 규칙을 삭제하려면 해당 행에서 휴지통 아이콘을 선택하고 **저장** 을 선택합니다. 규칙이 제거되고 변경 내용이 저장됩니다.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="IoT Hub IP 필터 규칙을 삭제하는 방법을 보여주는 스크린샷":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>기본 제공 Event Hub 호환 엔드포인트에 IP 필터 규칙 적용

IP 필터 규칙을 기본 제공 Event Hub 호환 엔드포인트에 적용하려면 **기본 제공 엔드포인트에 IP 필터를 적용하시겠어요?** 옆에 있는 확인란을 선택하고 **저장** 을 선택합니다.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="기본 제공 엔드포인트에 대한 토글을 보여주는 스크린샷.":::

> [!NOTE]
> 체험용(F1) IoT 허브에서는 이 옵션을 사용할 수 없습니다. 기본 제공 엔드포인트에 IP 필터 규칙을 적용하려면 유료 IoT 허브를 사용해야 합니다.

이 옵션을 사용하도록 설정하면 IP 필터 규칙이 기본 제공 엔드포인트에 복제되므로 신뢰할 수 있는 IP 범위만 해당 엔드포인트에 액세스할 수 있습니다.

이 옵션을 사용하지 않도록 설정하면 모든 IP 주소에서 기본 제공 엔드포인트에 액세스할 수 있습니다. 이 동작은 Azure Stream Analytics와 같이 IP 주소가 변경되는 서비스를 사용하여 엔드포인트에서 읽으려는 경우에 유용할 수 있습니다. 

## <a name="how-filter-rules-are-applied"></a>필터 규칙이 적용되는 방식

IP 필터 규칙은 IoT Hub 서비스 수준에 적용됩니다. 따라서 IP 필터 규칙은 지원되는 모든 프로토콜을 사용하는 디바이스 및 백 엔드 앱의 모든 연결에 적용됩니다. 또한 [기본 제공 Event Hub 호환 엔드포인트](iot-hub-devguide-messages-read-builtin.md)(IoT Hub 연결 문자열을 통하지 않는 경우)가 해당 규칙에 바인딩되어 있도록 할지를 선택할 수 있습니다. 

명시적으로 허용되지 않은 IP 주소에서 오는 모든 연결 시도는 권한 없음 401 상태 코드 및 설명을 수신합니다. 응답 메시지는 IP 규칙을 언급하지 않습니다. IP 주소를 거부하면 다른 Azure 서비스(예: Azure Stream Analytics, Azure Virtual Machines 또는 Azure Portal의 Device Explorer)가 IoT 허브와 상호 작용하는 것을 막을 수 있습니다.

> [!NOTE]
> IP 필터를 사용하도록 설정한 상태에서 ASA(Azure Stream Analytics)를 사용하여 IoT 허브의 메시지를 읽어야 하는 경우, **기본 제공 엔드포인트에 IP 필터 적용** 을 **사용하지 않음** 으로 설정하고 이벤트 허브와 호환되는 IoT 허브 이름 및 엔드포인트를 사용하여 수동으로 ASA에 [Event Hubs 스트림 입력](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)을 추가합니다.

### <a name="ordering"></a>순서 지정

IP 필터 규칙은 ‘허용’ 규칙이며 순서 없이 적용됩니다. 추가한 IP 주소만 IoT Hub에 연결할 수 있습니다. 

예를 들어 `192.168.100.0/22` 범위의 주소를 수락하고 그 외의 주소를 거부하려는 경우 그리드에 주소 범위 `192.168.100.0/22`가 있는 규칙을 추가해야 합니다.

### <a name="azure-portal"></a>Azure portal 

IP 필터 규칙은 Azure Portal을 통해 IoT Hub를 사용하는 경우에도 적용됩니다. 이는 IoT Hub 서비스에 대한 API 호출이 다른 Azure 서비스와 일치하는 자격 증명을 사용하여 직접 브라우저를 사용하기 때문입니다. IP 필터의 사용이 설정된 상태에서 Azure Portal을 사용하여 IoT Hub에 액세스하려면 컴퓨터의 IP 주소를 허용 목록에 추가합니다. 

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Azure CLI를 사용하여 IP 필터 검색 및 업데이트

IoT Hub의 IP 필터는 [Azure  CLI](/cli/azure/)를 통해 검색 및 업데이트할 수 있습니다.

IoT Hub의 현재 IP 필터를 검색하려면 다음을 실행합니다.

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

그러면 JSON 개체로 돌아가고 기존 IP 필터가 `properties.networkRuleSets` 키 아래에 나열됩니다.

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

IoT Hub에 대한 새 IP 필터를 추가하려면 다음을 실행합니다.

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

IoT Hub의 기존 IP 필터를 제거하려면 다음을 실행합니다.

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

이 경우 `<ipFilterIndexToRemove>`가 IoT Hub의 `properties.networkRuleSets.ipRules`에 있는 IP 필터의 순서와 일치해야 합니다.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Azure PowerShell을 사용하여 IP 필터 검색 및 업데이트

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

IoT Hub의 IP 필터는 [Azure PowerShell](/powershell/azure/)을 통해 검색 및 설정할 수 있습니다.

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>REST를 사용하여 IP 필터 규칙 업데이트


또한 Azure 리소스 공급자의 REST 엔드포인트를 사용하여 IoT 허브의 IP 필터를 검색 및 수정할 수도 있습니다. [createorupdate 메서드](/rest/api/iothub/iothubresource/createorupdate)의 `properties.networkRuleSets`를 참조하세요.

## <a name="ip-filter-classic-retirement"></a>IP 필터(클래식) 사용 중지

클래식 IP 필터는 더 이상 사용되지 않습니다. 자세히 알아보려면 [IoT Hub 클래식 IP 필터 및 업그레이드하는 방법](iot-hub-ip-filter-classic.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 메트릭](./monitor-iot-hub.md)
* [Private Link 및 관리 ID를 사용하는 가상 네트워크에 대한 IoT Hub 지원](virtual-network-support.md)
* [IoT 허브의 공용 네트워크 액세스 관리](iot-hub-public-network-access.md)
* [IoT Hub 모니터링](monitor-iot-hub.md)
