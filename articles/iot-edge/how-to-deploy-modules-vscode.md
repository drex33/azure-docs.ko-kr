---
title: Visual Studio Code에서 모듈 배포 - Azure IoT Edge
description: Azure IoT Tools에서 Visual Studio Code를 사용하여 배포 매니페스트에 구성된 대로 IoT Hub의 IoT Edge 모듈을 IoT Edge 디바이스로 푸시합니다.
author: kgremban
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.reviewer: ''
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8a1ba312d404a0045e08d2fbc966141471b57eb5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537035"
---
# <a name="deploy-azure-iot-edge-modules-from-visual-studio-code"></a>Visual Studio Code에서 Azure IoT Edge 모듈 배포

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

비즈니스 논리를 사용하여 IoT Edge 모듈을 만들면 디바이스에 배포하여 에지에서 작동시킵니다. 데이터를 수집하고 처리하기 위해 다중 모듈을 사용한 경우 한 번에 모두 배포하고 여기에 연결된 회람 규칙을 선언할 수 있습니다.

이 아티클에서는 JSON 배포 매니페스트를 만든 다음, 해당 파일을 사용하여 IoT Edge 디바이스에 배포를 푸시하는 방법을 보여줍니다. 해당 공유 태그에 따라 다중 디바이스를 대상으로 지정하는 배포를 만드는 방법에 대한 정보는 [Visual Studio Code를 사용하여 대규모로 IoT Edge 모듈 배포](how-to-deploy-vscode-at-scale.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
* IoT Edge 디바이스

  IoT Edge 디바이스를 설정하지 않은 경우 Azure 가상 머신에서 만들 수 있습니다. 빠른 시작 문서 중 하나에 있는 단계에 따라 [가상 Linux 디바이스를 만들거나](quickstart-linux.md) [가상 Windows 디바이스를 만듭니다](quickstart.md).

* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code용 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)

## <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. 배포 매니페스트의 작동 방식 및 생성 방법에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

Visual Studio Code를 사용하여 모듈을 배포하려면 배포 매니페스트를 로컬에 .JSON 파일로 저장합니다. 명령을 실행하여 디바이스에 구성을 적용할 때 다음 섹션에서 파일 경로를 사용합니다.

예를 들어 한 개의 모듈이 있는 기본 배포 매니페스트의 예제는 다음과 같습니다.

>[!NOTE]
>이 샘플 배포 매니페스트는 IoT Edge 에이전트 및 허브용 스키마 버전 1.1을 사용합니다. 스키마 버전 1.1은 IoT Edge 버전 1.0.10과 함께 출시되었으며 모듈 시작 순서 및 경로 우선 순위와 같은 기능을 사용 설정합니다.

   ```json
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                 "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.1",
           "routes": {
               "route": "FROM /messages/* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {}
       }
     }
   }
   ```

## <a name="sign-in-to-access-your-iot-hub"></a>로그인하여 IoT Hub에 액세스

Visual Studio Code용 Azure IoT 확장을 사용하여 IoT Hub에 대한 작업을 수행할 수 있습니다. 이러한 작업이 수행되려면 Azure 계정에 로그인하고 작업 중인 IoT Hub를 선택해야 합니다.

1. Visual Studio Code에서 **탐색기** 보기를 엽니다.

1. 탐색기의 맨 아래에서 **Azure IoT Hub** 섹션을 확장합니다.

   ![Azure IoT Hub 확장 섹션](./media/how-to-deploy-modules-vscode/azure-iot-hub-devices.png)

1. **Azure IoT Hub** 섹션 헤더에서 **...** 를 클릭합니다. 줄임표가 표시되지 않으면 헤더를 마우스로 가리킵니다.

1. **IoT Hub 선택** 을 선택합니다.

1. Azure 계정에 로그인하지 않은 경우, 프롬프트를 따라 로그인합니다.

1. Azure 구독을 선택합니다.

1. IoT Hub를 선택합니다.

## <a name="deploy-to-your-device"></a>새 디바이스에 배포

모듈 정보를 사용하여 구성한 배포 매니페스트를 적용하여 디바이스에 모듈을 배포합니다.

1. Visual Studio Code 탐색기 보기에서 **Azure IoT Hub** 섹션을 확장한 후 **디바이스** 노드를 확장합니다.

1. 배포 매니페스트로 구성하려는 IoT Edge 디바이스를 마우스 오른쪽 단추로 클릭합니다.

    > [!TIP]
    > 선택한 디바이스가 IoT Edge 디바이스인지 확인하려면 선택하여 모듈 목록을 확장하고 **$edgeHub** 및 **$edgeAgent** 가 있는지 확인합니다. 모든 IoT Edge 디바이스에 이러한 두 모듈이 포함됩니다.

1. **단일 디바이스용 배포 만들기** 를 선택합니다.

1. 사용하려는 배포 매니페스트 JSON 파일로 이동하고 **에지 배포 매니페스트 선택** 을 클릭합니다.

   ![에지 배포 매니페스트 선택](./media/how-to-deploy-modules-vscode/select-deployment-manifest.png)

배포의 결과는 VS Code 출력으로 인쇄됩니다. 대상 디바이스가 실행되고 인터넷에 연결된 경우 몇 분 안에 성공한 배포가 적용됩니다.

## <a name="view-modules-on-your-device"></a>디바이스에서 모듈 보기

디바이스에 모듈을 배포하면 **Azure IoT Hub** 섹션에서 모두 볼 수 있습니다. 확장하려는 IoT Edge 디바이스 옆에 있는 화살표를 선택합니다. 현재 실행 중인 모듈이 모두 표시됩니다.

최근 디바이스에 새 모듈을 배포한 경우 **Azure IoT Hub 디바이스** 섹션 헤더에 커서를 올려두고 새로 고침 아이콘을 선택하여 보기를 업데이트합니다.

모듈의 이름을 마우스 오른쪽 단추로 클릭하고 모듈 쌍을 보고 편집합니다.

## <a name="next-steps"></a>다음 단계

[Visual Studio Code를 사용하여 대규모 IoT Edge 모듈을 배포 및 모니터링](how-to-deploy-at-scale.md)하는 방법 알아보기
