---
title: Azure IoT 허브를 복제하는 방법
description: Azure IoT 허브를 복제하는 방법
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 4e66c71dd274f6096113992d9584645d5622f1c1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211075"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>IoT 허브를 다른 Azure 지역에 복제하는 방법

이 문서에서는 IoT Hub를 복제하는 방법을 설명하고, 시작하기 전에 고려해야 하는 몇 가지 질문을 제공합니다. IoT 허브를 복제하려는 이유는 다음과 같습니다.
 
* 회사를 한 지역에서 다른 지역으로 이전하고(예: 유럽에서 북아메리카로 또는 그 반대로) 리소스 및 데이터를 새 위치와 지리적으로 가까운 곳에 두기 위해 허브를 이동해야 합니다.

* 개발 및 프로덕션 환경을 위한 허브를 설정하려 합니다.

* 맞춤형 다중 허브 고가용성을 구현하려 합니다. 자세한 내용은 [IoT Hub 고가용성 및 재해 복구의 지역 간 HA를 달성하는 방법 섹션](iot-hub-ha-dr.md#achieve-cross-region-ha)을 참조하세요.

* 허브에 대해 구성된 [파티션](iot-hub-scaling.md#partitions) 수를 늘리려 합니다. 허브를 처음 만들 때 설정되며, 변경할 수 없습니다. 이 문서의 정보를 사용하여 허브를 복제하고 클론이 생성될 때 파티션 수를 늘릴 수 있습니다.

허브를 복제하려면 원본 허브에 대한 관리자 액세스 권한이 있는 구독이 필요합니다. 새 허브를 새 리소스 그룹 및 지역, 원본 허브와 동일한 구독 또는 새 구독에 배치할 수 있습니다. 허브 이름은 전역적으로 고유해야 하기 때문에 동일한 이름을 사용할 수 없습니다.

> [!NOTE]
> 지금은 IoT 허브를 자동으로 복제하는 데 사용할 수 있는 기능이 없습니다. 주로 수동 프로세스이므로 오류가 발생하기 쉽습니다. 허브 복제의 복잡성은 허브의 복잡성에 비례합니다. 예를 들어 메시지 라우팅 없이 IoT 허브를 복제하면 매우 간단합니다. 메시지 라우팅을 추가하면 복잡성 차수가 하나 증가하는 것이기 때문에 허브 복제가 그만큼 복잡해집니다. 여기에 라우팅 엔드포인트에 사용되는 리소스를 이동하면 허브 복제가 또 그만큼 복잡해집니다. 

## <a name="things-to-consider"></a>고려할 사항

IoT 허브를 복제하기 전에 고려해야 할 여러 가지 사항이 있습니다.

* 원래 위치에서 사용할 수 있는 모든 기능을 새 위치에서도 사용할 수 있어야 합니다. 일부 서비스는 미리 보기이므로 모든 기능을 어디서나 사용할 수 있는 것은 아닙니다.

* 복제된 버전을 만들고 확인하기 전에는 원래 리소스를 제거하지 마세요. 허브를 제거하면 허브가 영원히 사라지며, 허브를 복구하고 설정 또는 데이터를 검사하여 허브가 올바르게 복제되었는지 확인할 수 있는 방법이 없습니다.

* 많은 리소스가 전역적으로 고유한 이름이 필요하므로 복제된 버전마다 다른 이름을 사용해야 합니다. 복제된 허브가 속한 리소스 그룹에도 다른 이름을 사용해야 합니다. 

* 원본 IoT 허브의 데이터는 마이그레이션되지 않습니다. 여기에는 원격 분석 메시지, C2D(클라우드-디바이스) 명령 및 작업 관련 정보(예: 일정 및 기록)가 포함됩니다. 메트릭 및 로깅 결과도 마이그레이션되지 않습니다. 

* Azure Storage로 라우팅되는 데이터 또는 메시지의 경우 원본 스토리지 계정의 데이터를 그대로 둘 수도 있고, 새 지역의 새 스토리지 계정으로 전송할 수도 있고, 이전 데이터는 그대로 두고 새 위치에 새 데이터를 위한 새 스토리지 계정을 만들 수도 있습니다. Blob 스토리지에서 데이터를 이동하는 방법에 대한 자세한 내용은 [AzCopy 시작](../storage/common/storage-use-azcopy-v10.md)을 참조하세요.

* Event Hubs와 Service Bus 토픽 및 큐에 대한 데이터는 마이그레이션할 수 없습니다. 이 데이터는 특정 시점 데이터이며 메시지가 처리된 후에는 저장되지 않습니다.

* 마이그레이션을 위한 가동 중지 시간을 예약해야 합니다. 디바이스를 새 허브에 복제하려면 시간이 걸립니다. Import/Export 방법을 사용하는 경우 디바이스 500,000대를 이동하는 데 약 2시간 정도 소요되고 100만 대를 이동하는 데 4시간 정도 소요되는 것으로 벤치마크 테스트 결과가 나왔습니다. 

* 디바이스를 종료하거나 변경하지 않고도 디바이스를 새 허브에 복사할 수 있습니다. 

    * 처음에 DPS를 사용하여 디바이스를 프로비전한 경우 디바이스를 다시 프로비전하면 각 디바이스에 저장된 연결 정보가 업데이트됩니다. 
    
    * 그렇지 않은 경우 Import/Export 방법을 사용하여 디바이스를 이동한 다음, 새 허브를 사용하도록 디바이스를 수정해야 합니다. 예를 들어 트윈 desired 속성의 IoT Hub 호스트 이름을 사용하도록 디바이스를 설정할 수 있습니다. 그러면 디바이스가 해당 IoT Hub 호스트 이름으로 사용하고, 이전 허브에서 디바이스 연결을 끊은 후 새 허브에 다시 연결합니다.
    
* 사용 중인 인증서를 업데이트해야 합니다. 그래야만 새 리소스에 인증서를 사용할 수 있습니다. 또한 다른 곳의 DNS 테이블에 정의된 허브가 있을 수 있습니다. 이 경우 해당 DNS 정보를 업데이트해야 합니다.

## <a name="methodology"></a>방법

IoT 허브를 한 지역에서 다른 지역으로 이동할 때 권장하는 일반적인 방법입니다. 메시지 라우팅의 경우 리소스가 새 지역으로 이동되지 않는다고 가정합니다. 자세한 내용은 [메시지 라우팅에 대한 섹션](#how-to-handle-message-routing)을 참조하세요.

   1. 허브 및 해당 설정을 Resource Manager 템플릿으로 내보냅니다. 
   
   1. 템플릿을 필요한 대로 변경합니다(예: 복제된 허브의 모든 이름 및 위치 업데이트). 메시지 라우팅 엔드포인트에 사용되는 템플릿의 모든 리소스와 관련하여 해당 리소스에 대한 템플릿의 키를 업데이트합니다.
   
   1. 새 위치의 새 리소스 그룹으로 템플릿을 가져옵니다. 그러면 클론이 생성됩니다.

   1. 필요한 대로 디버그합니다. 
   
   1. 템플릿으로 내보내지지 않은 항목을 추가합니다. 
   
       예를 들어 소비자 그룹은 템플릿으로 내보내지지 않습니다. 소비자 그룹을 템플릿에 수동으로 추가하거나 허브가 생성된 후 [Azure Portal](https://portal.azure.com)을 사용해야 합니다. [Azure Resource Manager 템플릿을 사용하여 IoT Hub 메시지 라우팅 구성](tutorial-routing-config-message-routing-rm-template.md) 문서를 보면 템플릿에 소비자 그룹을 추가하는 예제가 나와 있습니다.
       
   1. 원본 허브에서 클론으로 디바이스를 복사합니다. 이 내용은 [IoT 허브에 등록된 디바이스 관리](#managing-the-devices-registered-to-the-iot-hub) 섹션에서 다룹니다.

## <a name="how-to-handle-message-routing"></a>메시지 라우팅을 처리하는 방법

허브에서 [사용자 지정 라우팅](iot-hub-devguide-messages-read-custom.md)을 사용하는 경우 허브에 대한 템플릿을 내보낼 때 라우팅 구성이 포함되지만 리소스 자체는 포함되지 않습니다. 라우팅 리소스를 새 위치로 이동할지 아니면 원래 위치에 두고 "있는 그대로" 계속 사용할지 선택해야 합니다. 

예를 들어 미국 서부에 메시지를 스토리지 계정(마찬가지로 미국 서부에 있음)으로 라우팅하는 허브가 하나 있는데, 이 허브를 미국 동부로 이동하려 한다고 가정하겠습니다. 허브를 이동하고 메시지를 미국 서부의 스토리지 계정으로 계속 라우팅할 수도 있고, 허브를 이동하고 스토리지 계정까지 이동할 수도 있습니다. 메시지를 다른 지역의 엔드포인트 리소스로 라우팅하면 약간의 성능 저하가 발생할 수 있습니다.

라우팅 엔드포인트에 사용되는 리소스를 이동하지 않으면 메시지 라우팅을 사용하는 허브를 매우 쉽게 이동할 수 있습니다. 

허브에서 메시지 라우팅을 사용하는 경우 다음과 같은 두 가지 옵션이 있습니다. 

1. 라우팅 엔드포인트에 사용되는 리소스를 새 위치로 이동합니다.

    * [Azure Portal](https://portal.azure.com)에서 수동으로 또는 Resource Manager 템플릿을 사용하여 새 리소스를 직접 만들어야 합니다. 

    * 리소스마다 전역적으로 고유한 이름이 있으므로 새 위치에 리소스를 만들 때 모든 리소스의 이름을 바꾸어야 합니다. 
     
    * 새 허브를 만들기 전에 새 허브의 템플릿에서 리소스 이름과 리소스 키를 업데이트해야 합니다. 새 허브를 만들 때 리소스가 있어야 합니다.

1. 라우팅 엔드포인트에 사용되는 리소스를 이동하지 마세요. "원래 위치"에 두고 사용합니다.

   * 템플릿을 편집하는 단계에서 허브를 만들기 전에 각 라우팅 리소스의 키를 검색하여 템플릿에 배치해야 합니다. 

   * 허브는 여전히 원래 라우팅 리소스를 참조하고, 구성된 대로 메시지를 라우팅합니다.

   * 허브와 라우팅 엔드포인트 리소스가 동일한 위치에 있지 않기 때문에 성능이 약간 저하됩니다.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>허브를 다른 지역으로 마이그레이션할 준비

이 섹션에서는 허브를 마이그레이션하기 위한 구체적인 지침을 제공합니다.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>원본 허브를 찾아 리소스 템플릿으로 내보냅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **리소스 그룹** 으로 이동한 다음, 이동하려는 허브가 포함된 리소스 그룹을 선택합니다. **리소스** 로 이동하여 같은 방식으로 허브를 찾을 수도 있습니다. 허브를 선택합니다.

1. 허브에 대한 속성 및 설정 목록에서 **템플릿 내보내기** 를 선택합니다. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-export-template.png" alt-text="IoT Hub에 대한 템플릿을 내보내는 명령을 보여주는 스크린샷" border="true":::

1. **다운로드** 를 선택하여 템플릿을 다운로드합니다. 파일을 다시 찾을 수 있는 위치에 저장합니다. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-download-template.png" alt-text="IoT Hub에 대한 템플릿을 다운로드하는 명령을 보여주는 스크린샷" border="true":::

### <a name="view-the-template"></a>템플릿 보기 

1. Downloads 폴더(또는 템플릿을 내보낼 때 사용한 폴더)로 이동하여 Zip 파일을 찾습니다. zip 파일을 추출하고 라는 파일을 `template.json` 찾습니다. 이를 선택하고 복사합니다. 다른 폴더로 이동하여 템플릿 파일(Ctrl+V)을 붙여넣습니다. 이제 템플릿을 편집할 수 있습니다.
 
    다음은 라우팅 구성이 없는 일반 허브의 예입니다. 미국 서부 지역의 **ContosoHub라는** S1 계층 허브(1단위)입니다.  내보낸 템플릿은 다음과 같습니다.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoHub_connectionString": {
                "type": "SecureString"
            },
            "IotHubs_ContosoHub_containerName": {
                "type": "SecureString"
            },
            "IotHubs_ContosoHub_name": {
                "defaultValue": "ContosoHub",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2021-07-01",
                "name": "[parameters('IotHubs_ContosoHub_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "identity": {
                    "type": "None"
                },
                "properties": {
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 4
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "[parameters('IotHubs_ContosoHub_connectionString')]",
                            "containerName": "[parameters('IotHubs_ContosoHub_containerName')]"
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None",
                    "disableLocalAuth": false,
                    "allowedFqdnList": []
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>템플릿 편집 

템플릿을 사용하여 새 지역에 새 허브를 만들려면 먼저 템플릿을 약간 변경해야 합니다. [VS Code](https://code.visualstudio.com) 또는 텍스트 편집기를 사용하여 템플릿을 편집합니다.

#### <a name="edit-the-hub-name-and-location"></a>허브 이름 및 위치 편집

1. 맨 위에 있는 컨테이너 이름 매개 변수 섹션을 제거 합니다. **ContosoHub** 에 연결 된 컨테이너가 없습니다.

    ``` json
    "parameters": {
      ...
        "IotHubs_ContosoHub_containerName": {
            "type": "SecureString"
        },
      ...
    },
    ```

1. **Storageendpoints** 속성을 제거 합니다.

    ```json
    "properties": {
      ...
        "storageEndpoints": {
        "$default": {
            "sasTtlAsIso8601": "PT1H",
            "connectionString": "[parameters('IotHubs_ContosoHub_connectionString')]",
            "containerName": "[parameters('IotHubs_ContosoHub_containerName')]"
        }
      },
      ...
    
    ```

1. **리소스** 에서 위치를 westus에서 eastus로 변경 합니다.

    이전 버전: 

    ``` json 
    "location": "westus",
    ```
    
    새 버전:  

    ``` json 
    "location": "eastus",
    ```
#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>이동되지 않는 라우팅 리소스의 키를 업데이트합니다.

라우팅이 구성된 허브의 Resource Manager 템플릿을 내보낼 때 해당 리소스의 키가 내보낸 템플릿에 제공되지 않습니다. 리소스 배치는 별표로 표시됩니다. 새 허브의 템플릿을 가져와서 허브를 만들려면 **먼저** 포털에서 해당 리소스로 이동한 후 키를 검색하여 채워야 합니다. 

1. 라우팅 리소스에 필요한 키를 검색하여 템플릿에 넣습니다. [Azure Portal](https://portal.azure.com)에서 리소스의 키를 검색할 수 있습니다. 

   예를 들어 메시지를 스토리지 컨테이너로 라우팅하는 경우 포털에서 스토리지 계정을 찾습니다. [설정] 섹션에서 **액세스 키** 를 선택하고 키 중 하나를 복사합니다. 템플릿을 처음 내보낼 때 키는 다음과 같습니다.

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. 스토리지 계정의 계정 키를 검색한 후에는 템플릿의 `AccountKey=****` 절에 있는 별표 자리에 키를 배치합니다. 

1. Service Bus 큐의 경우 SharedAccessKeyName과 일치하는 공유 액세스 키를 가져옵니다. 다음은 json의 키와 `SharedAccessKeyName`입니다.

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Service Bus 토픽 및 이벤트 허브 연결에도 동일하게 적용됩니다.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>새 위치에 새 라우팅 리소스 만들기

이 섹션은 허브에서 라우팅 엔드포인트에 사용하는 리소스를 이동하는 경우에만 적용됩니다.

라우팅 리소스를 이동하려면 새 위치에 리소스를 수동으로 설정해야 합니다. [Azure Portal](https://portal.azure.com)을 사용하거나 메시지 라우팅에서 사용하는 각 리소스의 Resource Manager 템플릿을 내보내고 편집하고 가져와서 리소스를 라우팅할 수 있습니다. 리소스를 설정한 후에는 허브의 템플릿(라우팅 구성 포함)을 가져올 수 있습니다.

1. 라우팅에 사용되는 각 리소스를 만듭니다. [Azure Portal](https://portal.azure.com)을 사용하여 수동으로 이 작업을 수행할 수도 있고, Resource Manager 템플릿을 사용하여 리소스를 만들 수도 있습니다. 템플릿을 사용하려면 다음 단계를 수행합니다.

    1. 라우팅에 사용되는 각 리소스를 Resource Manager 템플릿으로 내보냅니다.
    
    1. 리소스의 이름과 위치를 업데이트합니다. 

    1. 리소스 간의 상호 참조를 업데이트합니다. 예를 들어 새 스토리지 계정의 템플릿을 만드는 경우 해당 템플릿의 스토리지 계정 이름과 이 이름을 참조하는 다른 템플릿을 업데이트해야 합니다. 대부분의 경우 허브에 대한 템플릿의 라우팅 섹션은 리소스를 참조하는 유일한 다른 템플릿입니다. 

    1. 각 리소스를 배포하는 각 템플릿을 가져옵니다.

    라우팅에 사용되는 리소스를 설정하고 실행한 후에는 계속 진행할 수 있습니다.

1. IoT 허브에 대한 템플릿에서 각 라우팅 리소스의 이름을 새 이름으로 변경하고, 필요한 경우 위치를 업데이트합니다. 

이제 경로를 처리하는 방법에 따라 이전 허브와 거의 똑같이 보이는 새 허브를 만드는 템플릿이 생겼습니다.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>이동 -- 템플릿을 로드하여 새 지역에 새 허브 만들기

템플릿을 사용하여 새 위치에 새 허브를 만듭니다. 이동하려는 라우팅 리소스가 있는 경우 새 위치에 리소스를 설정하고 템플릿의 참조를 일치하도록 업데이트해야 합니다. 라우팅 리소스를 이동하지 않는 경우 업데이트된 키를 사용하는 템플릿에 라우팅 리소스가 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **리소스 만들기** 를 선택합니다. 

1. 검색 상자에 "템플릿 배포"를 입력 하 고 Enter 키를 선택 합니다.

1. **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)** 를 선택합니다. 그러면 [템플릿 배포] 화면으로 이동됩니다. **만들기** 를 선택합니다. 다음 화면이 표시됩니다.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png" alt-text="사용자 고유의 템플릿을 빌드하는 명령을 보여주는 스크린샷":::

1. **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다. 여기서 파일의 템플릿을 업로드할 수 있습니다. 

1. **파일 로드** 를 선택합니다.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-upload-file.png" alt-text="템플릿 파일을 업로드하는 명령을 보여주는 스크린샷":::

1. 편집한 새 템플릿을 찾아 선택한 다음, **열기** 를 선택합니다. 그러면 편집 창에 템플릿이 로드됩니다. **저장** 을 선택합니다. 

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-uploaded-file.png" alt-text="템플릿 로드를 보여주는 스크린샷":::

1. 사용자 지정 배포 페이지에서 다음 필드를 입력 합니다.

   **구독**: 사용할 구독을 선택 합니다.

   **리소스 그룹**: 새 위치에 새 리소스 그룹을 만듭니다. 이미 설정 되어 있는 경우 새 항목을 만드는 대신 선택할 수 있습니다.

   **지역**: 기존 리소스 그룹을 선택한 경우 리소스 그룹의 위치와 일치 하도록 지역이 채워집니다. 새 리소스 그룹을 만든 경우 해당 리소스 그룹의 위치가 됩니다.

   **연결 문자열**: 허브에 대 한 연결 문자열을 입력 합니다.

   **허브 이름**: 새 지역의 새 허브에 이름을 지정 합니다.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment-create.png" alt-text="사용자 지정 배포 페이지를 보여 주는 스크린샷":::

1. **검토 + 만들기** 단추를 선택합니다.

1. **생성** 단추를 선택합니다. 포털은 템플릿의 유효성을 검사 하 고 복제 된 허브를 배포 합니다. 라우팅 구성 데이터가 있는 경우 해당 데이터는 새 허브에 포함되지만 이전 위치의 리소스를 가리킵니다.

   :::image type="content" source="./media/iot-hub-how-to-clone/iot-hub-custom-deployment-final.png" alt-text="최종 사용자 지정 배포 페이지를 보여 주는 스크린샷":::

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>IoT 허브에 등록된 디바이스 관리

클론이 가동 중이므로, 이제 원본 허브의 모든 디바이스를 클론으로 복사해야 합니다. 

세 가지 방법으로 이 작업을 수행할 수 있습니다. 처음에 디바이스를 프로비전할 때 [DPS(IoT Hub Device Provisioning Service)](../iot-dps/about-iot-dps.md)를 사용했을 수도 있고 사용하지 않았을 수도 있습니다. 사용한 경우 이 방법이 어렵지 않습니다. 사용하지 않은 경우 이 방법이 매우 복잡할 수 있습니다. 

디바이스를 프로비전할 때 DPS를 사용하지 않은 경우 다음 섹션을 건너뛰고 [Import/Export를 사용하여 디바이스를 새 허브로 이동](#using-import-export-to-move-the-devices-to-the-new-hub)하는 것부터 시작하면 됩니다.

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>DPS를 사용하여 새 허브에서 디바이스 다시 프로비전

DPS를 사용하여 디바이스를 새 위치로 이동하려면 [디바이스를 다시 프로비전하는 방법](../iot-dps/how-to-reprovision.md)을 참조하세요. 모두 마쳤으면 [Azure Portal](https://portal.azure.com)에서 디바이스를 살펴보고 디바이스가 새 위치에 있는지 확인할 수 있습니다.

[Azure Portal](https://portal.azure.com)을 사용하여 새 허브로 이동합니다. 허브를 선택하고 **IoT 디바이스** 를 선택합니다. 복제된 허브에 다시 프로비전된 디바이스가 표시됩니다. 복제된 허브의 속성을 볼 수도 있습니다. 

라우팅을 구현한 경우 메시지가 리소스로 올바르게 라우팅되는지 테스트합니다.

### <a name="committing-the-changes-after-using-dps"></a>DPS를 사용한 후 변경 내용 커밋

이 변경 내용은 DPS 서비스에 의해 커밋되었습니다.

### <a name="rolling-back-the-changes-after-using-dps"></a>DPS를 사용한 후 변경 내용 롤백 

변경 내용을 롤백하려면 디바이스를 새 허브에서 기존 허브로 다시 프로비전합니다.

허브와 허브의 디바이스 마이그레이션이 완료되었습니다. [정리](#clean-up)로 건너뛰어도 됩니다.

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Import-Export를 사용하여 디바이스를 새 허브로 이동

애플리케이션은 .NET Core를 대상으로 하기 때문에 Windows 또는 Linux에서 애플리케이션을 실행할 수 있습니다. 샘플을 다운로드하고, 연결 문자열을 검색하고, 실행하려는 비트의 플래그를 설정하고, 실행할 수 있습니다. 코드를 열지 않고도 이 작업을 수행할 수 있습니다.

### <a name="downloading-the-sample"></a>샘플 다운로드

1. [C#에 대한 Azure IoT 샘플](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/) 페이지의 IoT C# 샘플을 사용합니다. Zip 파일을 다운로드하고 컴퓨터에 압축을 풉니다. 

1. 관련 코드는 ./iot-hub/Samples/service/ImportExportDevicesSample에 있습니다. 애플리케이션을 실행하기 위해 코드를 보거나 편집할 필요는 없습니다.

1. 애플리케이션을 실행하려면 3개의 연결 문자열과 5개의 옵션을 지정합니다. 이 데이터를 명령줄 인수로 전달하거나, 환경 변수를 사용하거나, 두 가지 방법을 조합하여 사용합니다. 여기서는 옵션을 명령줄 인수로 전달하고 연결 문자열을 환경 변수로 전달하겠습니다. 

   이렇게 하는 이유는 연결 문자열이 길어서 보기 불편하고 변경될 가능성이 별로 없기 때문입니다. 하지만 원한다면 옵션을 변경하고 애플리케이션을 두 번 이상 실행할 수 있습니다. 환경 변수의 값을 변경하려면 사용 중인 명령 창과 Visual Studio 또는 VS Code를 닫아야 합니다. 

### <a name="options"></a>옵션

애플리케이션을 실행할 때 지정하는 5가지 옵션은 다음과 같습니다. 1분 후 이러한 옵션을 명령줄에 배치하겠습니다.

*   **addDevices**(인수 1) -- 자동으로 생성되는 가상 디바이스를 추가하려면 true로 설정합니다. 원본 허브에 추가됩니다. 추가하려는 디바이스 수를 지정하려면 **numToAdd**(인수 2)도 설정합니다. 허브에 등록할 수 있는 최대 디바이스 수는 100만 대입니다. 이 옵션의 목적은 테스트입니다. 특정 수의 디바이스를 생성한 다음, 다른 허브에 복사할 수 있습니다.

*   **copyDevices**(인수 3) -- 한 허브에서 다른 허브로 디바이스를 복사하려면 true로 설정합니다. 

*   **deleteSourceDevices**(인수 4) -- 원본 허브에 등록된 디바이스를 모두 삭제하려면 true로 설정합니다. 모든 디바이스가 전송된 것이 확실할 때까지 기다렸다가 실행하는 것이 좋습니다. 디바이스를 삭제한 후에는 다시 가져올 수 없습니다.

*   **deleteDestDevices**(인수 5) -- 대상 허브(클론)에 등록된 디바이스를 모두 삭제하려면 true로 설정합니다. 디바이스를 두 번 이상 복사하려는 경우에 설정하는 것이 좋습니다. 

기본 명령은 로컬 csproj 파일을 빌드하여 실행하도록 .NET에 지시하는 *dotnet run* 입니다. 끝부분에 명령줄 인수를 추가한 후 실행합니다. 

명령줄은 다음 예제와 비슷합니다.

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>연결 문자열에 대한 환경 변수 사용

1. 샘플을 실행하려면 이전 및 새 IoT 허브에 대한 연결 문자열과 임시 작업 파일에 사용할 수 있는 스토리지 계정에 대한 연결 문자열이 필요합니다. 이러한 값은 환경 변수에 저장됩니다.

1. 연결 문자열 값을 가져오려면 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. 메모장과 같이 검색할 수 있는 위치에 연결 문자열을 배치합니다. 다음을 복사하는 경우 연결 문자열이 이동하는 위치에 직접 연결 문자열을 붙여넣을 수 있습니다. 등호 앞뒤에 공백을 추가하지 마세요. 추가하면 변수 이름이 변경됩니다. 또한 연결 문자열을 큰따옴표로 묶을 필요가 없습니다. 스토리지 계정 연결 문자열을 따옴표로 묶으면 연결 문자열이 작동하지 않습니다.

   Windows에서 환경 변수를 설정하는 방법은 다음과 같습니다.

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Linux에서 환경 변수를 정의하는 방법은 다음과 같습니다.

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. IoT 허브 연결 문자열의 경우 포털에서 각 허브로 이동합니다. 허브에 대한 **리소스** 를 검색하면 됩니다. 리소스 그룹을 알고 있는 경우 **리소스 그룹** 으로 이동하여 리소스 그룹을 선택한 다음, 해당 리소스 그룹의 자산 목록에서 허브를 선택하면 됩니다. 

1. 허브의 [설정]에서 **공유 액세스 정책** 을 선택한 다음, **iothubowner** 를 선택하고 연결 문자열 중 하나를 복사합니다. 대상 허브에도 똑같이 합니다. 적절한 SET 명령에 연결 문자열을 추가합니다.

1. 스토리지 계정 연결 문자열의 경우 **리소스** 또는 **리소스 그룹** 에서 스토리지 계정을 찾아 엽니다. 
   
1. [설정] 섹션에서 **액세스 키** 를 선택하고 연결 문자열 중 하나를 복사합니다. 적절한 SET 명령에 대한 텍스트 파일에 연결 문자열을 배치합니다. 

이제 SET 명령을 사용하는 파일에 환경 변수가 있고, 명령줄 인수가 무엇인지 알고 있습니다. 샘플을 실행하겠습니다.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>샘플 애플리케이션을 실행하고 명령줄 인수 사용

1. 명령 프롬프트 창을 엽니다. Windows를 선택하고 `command prompt`를 입력하여 명령 프롬프트 창을 불러 옵니다.

1. 환경 변수를 설정하는 명령을 한 번에 하나씩 복사하여 명령 프롬프트 창에 붙여넣고 Enter 키를 선택합니다. 모두 마쳤으면 명령 프롬프트 창에 `SET`을 입력하여 환경 변수 및 해당 값을 확인합니다. 이러한 항목을 명령 프롬프트 창에 복사한 후에는 새 명령 프롬프트 창을 열지 않는 한 다시 복사하지 않아도 됩니다.

1. 현재 위치가 ./ImportExportDevicesSample(ImportExportDevicesSample.csproj 파일이 있는 위치)이 될 때까지 명령 프롬프트 창에서 디렉터리를 변경합니다. 그런 후 다음을 입력하고 명령줄 인수를 포함시킵니다.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    dotnet 명령은 애플리케이션을 빌드하고 실행합니다. 애플리케이션을 실행할 때 옵션을 전달하므로 애플리케이션을 실행할 때마다 해당 값을 변경할 수 있습니다. 예를 들어 애플리케이션을 한 번 실행하고 새 디바이스를 만들어서 다시 실행한 다음, 해당 디바이스를 새 허브로 복사하는 방식을 사용할 수 있습니다. 모든 단계를 동일한 실행에서 수행할 수도 있지만, 복제가 끝났다는 것을 확인하기 전에는 디바이스를 삭제하지 않는 것이 좋습니다. 다음은 디바이스 1000대를 만든 후 다른 허브에 복사하는 예제입니다.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    디바이스가 성공적으로 복사된 것을 확인한 후, 다음과 같이 원본 허브에서 디바이스를 제거하면 됩니다.

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>Visual Studio를 사용하여 샘플 애플리케이션 실행

1. Visual Studio에서 애플리케이션을 실행하려면 현재 디렉터리를 IoTHubServiceSamples.sln 파일이 있는 폴더로 변경합니다. 그리고 명령 프롬프트 창에서 다음 명령을 실행하여 Visual Studio에서 솔루션을 엽니다. 환경 변수를 설정한 명령 창에서 이 작업을 수행해야 합니다. 그러면 해당 변수를 알 수 있습니다.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. *ImportExportDevicesSample* 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다.    
    
1. ImportExportDevicesSample 폴더의 Program.cs 맨 위에서 5개 옵션에 대한 변수를 설정합니다.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. F5 키를 눌러 애플리케이션을 실행합니다. 실행이 완료되면 결과를 볼 수 있습니다.

### <a name="view-the-results"></a>결과 보기 

[Azure Portal](https://portal.azure.com)에서 디바이스를 살펴보고 디바이스가 새 위치에 있는지 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 사용하여 새 허브로 이동합니다. 허브를 선택하고 **IoT 디바이스** 를 선택합니다. 방금 이전 허브에서 복제된 허브로 복사한 디바이스가 표시됩니다. 복제된 허브의 속성을 볼 수도 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 Azure 스토리지 계정으로 이동한 후 `devicefiles` 컨테이너의 `ImportErrors.log`를 검사하여 가져오기/내보내기 오류를 확인합니다. 이 파일이 비어 있으면(크기가 0이면) 오류가 없는 것입니다. 동일한 디바이스를 두 번 이상 가져오려고 시도하면 두 번째 시도에서 디바이스가 거부되고 로그 파일에 오류 메시지가 추가됩니다.

### <a name="committing-the-changes"></a>변경 내용 커밋 

허브를 새 위치로 복사하고 디바이스를 새 클론으로 마이그레이션했습니다. 이제 디바이스가 복제된 허브에서 작동하도록 변경 작업을 수행해야 합니다.

변경 내용을 커밋하려면 다음 단계를 수행해야 합니다. 

* 각 디바이스를 업데이트하여 새 허브의 IoT Hub 호스트 이름을 가리키도록 IoT Hub 호스트 이름을 변경합니다. 디바이스를 처음 프로비전할 때 사용한 것과 동일한 방법을 사용하여 이 작업을 수행해야 합니다.

* 이전 허브를 참조하는 모든 애플리케이션이 새 허브를 가리키도록 변경합니다.

* 모두 마치면 새 허브가 작동합니다. 이전 허브는 활성 디바이스가 없고 연결되지 않은 상태입니다. 

### <a name="rolling-back-the-changes"></a>변경 내용 롤백

변경 내용을 롤백하기로 결정하는 경우 다음 단계를 수행합니다.

* 각 디바이스를 업데이트하여 이전 허브의 IoT Hub 호스트 이름을 가리키도록 IoT Hub 호스트 이름을 변경합니다. 디바이스를 처음 프로비전할 때 사용한 것과 동일한 방법을 사용하여 이 작업을 수행해야 합니다.

* 새 허브를 참조하는 모든 애플리케이션이 이전 허브를 가리키도록 변경합니다. 예를 들어 Azure Analytics를 사용하는 경우 [Azure Stream Analytics 입력](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)을 다시 구성해야 할 수도 있습니다.

* 새 허브를 삭제합니다. 

* 라우팅 리소스가 있는 경우에는 이전 허브의 구성이 올바른 라우팅 구성을 가리켜야 하며, 허브를 다시 시작한 후에도 이러한 리소스와 함께 작동해야 합니다.

### <a name="checking-the-results"></a>결과 확인 

결과를 확인하려면 새 위치의 허브를 가리키도록 IoT 솔루션을 변경하고 실행합니다. 다시 말해서, 이전 허브에서 수행한 작업을 새 허브에서 똑같이 수행하고 올바르게 작동하는지 확인합니다. 

라우팅을 구현한 경우 메시지가 리소스로 올바르게 라우팅되는지 테스트합니다.

## <a name="clean-up"></a>정리

새 허브가 작동 중이고 디바이스가 올바르게 작동하는 것을 확인하기 전에는 리소스를 정리하지 마세요. 또한 라우팅을 사용하는 경우에는 라우팅을 테스트해야 합니다. 준비되면 다음 단계를 수행하여 이전 리소스를 정리합니다.

* 이전 허브를 아직 삭제하지 않은 경우 삭제합니다. 그러면 허브의 모든 활성 디바이스가 제거됩니다.

* 라우팅 리소스를 새 위치로 이동한 경우 이전 라우팅 리소스를 삭제해도 됩니다.

## <a name="next-steps"></a>다음 단계

IoT 허브를 새 지역의 새 허브로 복제하고 디바이스를 완료했습니다. IoT Hub에서 ID 레지스트리에 대한 대량 작업을 수행하는 방법에 대한 자세한 내용은 [대량으로 IoT Hub 디바이스 ID 가져오기 및 내보내기](iot-hub-bulk-identity-mgmt.md)를 참조하세요.

IoT Hub 및 허브용 솔루션 개발에 대한 자세한 내용은 다음 문서를 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)

* [IoT Hub 라우팅 자습서](tutorial-routing.md)

* [IoT Hub 디바이스 관리 개요](iot-hub-device-management-overview.md)

* 샘플 애플리케이션을 배포하려면 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/index)를 참조하세요.