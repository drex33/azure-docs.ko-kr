---
title: Azure IoT Edge 및 Azure IoT Central | Microsoft Docs
description: IoT Central 애플리케이션 함께 Azure IoT Edge를 사용하는 방법을 이해합니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 2233e6f40c1023f1b02543f4e234b00422f6f77f
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110077197"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 연결

Azure IoT Edge는 조직에서 데이터 관리 대신 비즈니스 통찰력에 집중할 수 있도록 클라우드 분석 및 사용자 지정 비즈니스 논리를 디바이스로 이동합니다. 비즈니스 논리를 표준 컨테이너에 패키징하여 IoT 솔루션을 스케일 아웃하고, 디바이스에 해당 컨테이너를 배포하고, 클라우드에서 모든 컨테이너를 모니터링합니다.

이 문서에서는 다음을 설명합니다.

* IoT Edge 디바이스를 IoT Central 애플리케이션에 연결하는 방법.
* IoT Central을 사용하여 IoT Edge 디바이스를 관리하는 방법

IoT Edge에 대한 자세한 내용은 [Azure IoT Edge란?](../../iot-edge/about-iot-edge.md)을 참조하세요.

## <a name="iot-edge"></a>IoT Edge

IoT Edge는 다음 세 가지 구성 요소로 구성됩니다.

* *IoT Edge 모듈* 은 Azure 서비스, 파트너 서비스 또는 사용자 고유의 코드를 실행하는 컨테이너입니다. 모듈은 IoT Edge 디바이스에 배포되며, 해당 디바이스에서 로컬로 실행됩니다. 자세한 내용은 [Azure IoT Edge 모듈 이해](../../iot-edge/iot-edge-modules.md)를 참조하세요.
* *IoT Edge 런타임* 은 각 IoT Edge 디바이스에서 실행되며, 각 디바이스에 배포된 모듈을 관리합니다. 런타임은 *IoT Edge 에이전트* 와 *IoT Edge 허브* 라는 두 개의 IoT Edge 모듈로 구성됩니다. 자세한 내용은 [Azure IoT Edge 런타임 및 아키텍처 이해](../../iot-edge/iot-edge-runtime.md)를 참조하세요.
* *클라우드 기반 인터페이스* 를 사용하여 IoT Edge 디바이스를 원격으로 모니터링 및 관리할 수 있습니다. IoT Central은 클라우드 인터페이스의 예입니다.

IoT Edge 디바이스는 다음에 해당할 수 있습니다.

* 모듈로 구성된 독립 실행형 디바이스.
* 다운스트림 디바이스가 연결되는 *게이트웨이 디바이스*.

## <a name="iot-edge-as-a-gateway"></a>IoT Edge를 게이트웨이로 사용

IoT Edge 디바이스는 네트워크의 다른 다운스트림 디바이스와 IoT Central 애플리케이션 간의 연결을 제공하는 게이트웨이로 작동할 수 있습니다.

다음과 같은 두 가지 게이트웨이 패턴이 있습니다.

* *투명 게이트웨이* 패턴에서 IoT Edge 허브 모듈은 IoT Central처럼 작동하며, IoT Central에 등록된 디바이스에서 들어오는 연결을 처리합니다. 메시지는 중간에 게이트웨이가 없는 것처럼 다운스트림 디바이스에서 IoT Central로 전달됩니다.

* *변환 게이트웨이* 패턴에서 IoT Central에 직접 연결할 수 없는 디바이스는 사용자 지정 IoT Edge 모듈에 대신 연결합니다. IoT Edge 디바이스의 모듈은 들어오는 다운스트림 디바이스 메시지를 처리한 다음, IoT Central로 전달합니다.

투명 및 변환 게이트웨이 패턴은 상호 배타적이지 않습니다. 단일 IoT Edge 디바이스는 투명 게이트웨이 및 변환 게이트웨이로 작동할 수 있습니다.

IoT Edge 게이트웨이 패턴에 대한 자세한 내용은 [IoT Edge 디바이스를 게이트웨이로 사용하는 방법](../../iot-edge/iot-edge-as-gateway.md)을 참조하세요.

### <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>게이트웨이 및 모듈과의 다운스트림 디바이스 관계

다운스트림 디바이스는 *IoT Edge 허브* 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 이 시나리오에서 IoT Edge 디바이스는 투명 게이트웨이입니다.

:::image type="content" source="media/concepts-iot-edge/gateway-transparent.png" alt-text="투명한 게이트웨이의 다이어그램" border="false":::

다운스트림 디바이스는 사용자 지정 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수도 있습니다. 다음 시나리오에서 다운스트림 디바이스는 *Modbus* 사용자 지정 모듈을 통해 연결합니다. 이 시나리오에서 IoT Edge 디바이스는 변환 게이트웨이입니다.

:::image type="content" source="media/concepts-iot-edge/gateway-module.png" alt-text="사용자 지정 모듈 연결의 다이어그램" border="false":::

다음 다이어그램에서는 두 가지 유형의 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결하는 방법을 보여 줍니다. 이 시나리오에서 IoT Edge 디바이스는 투명 및 변환 게이트웨이 겸용입니다.

:::image type="content" source="media/concepts-iot-edge/gateway-module-transparent.png" alt-text="두 연결 모듈을 통한 연결의 다이어그램" border="false":::

다운스트림 디바이스는 여러 사용자 지정 모듈을 통해 IoT Edge 게이트웨이 디바이스에 연결할 수 있습니다. 다음 다이어그램에서는 Modbus 사용자 지정 모듈, BLE 사용자 지정 모듈 및 *IoT Edge 허브* 모듈을 통해 연결하는 다운스트림 디바이스를 보여 줍니다.

:::image type="content" source="media/concepts-iot-edge/gateway-two-modules-transparent.png" alt-text="여러 사용자 지정 모듈을 통한 연결의 다이어그램" border="false":::

## <a name="iot-edge-devices-and-iot-central"></a>IoT Edge 디바이스 및 IoT Central

IoT Edge 디바이스는 *공유 액세스 서명* 토큰 또는 x.509 인증서를 사용하여 IoT Central에 인증할 수 있습니다. IoT Edge 디바이스를 처음으로 연결하기 전에 IoT Central에 수동으로 등록하거나 디바이스 프로비저닝 서비스를 사용하여 등록을 처리할 수 있습니다. 자세한 내용은 [Azure IoT Central에 연결](concepts-get-connected.md)을 참조하세요.

IoT Central은 [디바이스 템플릿](concepts-device-templates.md)을 사용하여 IoT Central이 디바이스와 상호 작용하는 방법을 정의합니다. 예를 들어 디바이스 템플릿은 다음을 지정합니다.

* IoT Central이 해석하고 시각화를 만들 수 있도록 디바이스에서 전송하는 원격 분석 및 속성의 유형.
* IoT Central에서 운영자가 명령을 호출하는 데 사용하는 UI를 표시할 수 있도록 디바이스가 응답하는 명령.

IoT Edge 디바이스는 원격 분석을 전송하고, 속성 값을 동기화하고, 표준 디바이스와 같은 방식으로 명령에 응답할 수 있습니다. 따라서 IoT Edge 디바이스를 사용하려면 IoT Central에 디바이스 템플릿이 필요합니다.

### <a name="iot-edge-device-templates"></a>IoT Edge 디바이스 템플릿

IoT Central 디바이스 템플릿은 모델을 사용하여 디바이스의 기능을 설명합니다. 다음 다이어그램은 IoT Edge 디바이스의 모델 구조를 보여 줍니다.

:::image type="content" source="media/concepts-iot-edge/iot-edge-model.png" alt-text="IoT Central에 연결된 IoT Edge 디바이스의 모델 구조" border="false":::

IoT Central은 IoT Edge 디바이스를 다음과 같이 모델링합니다.

* 모든 IoT Edge 디바이스 템플릿에는 기능 모델이 있습니다.
* 배포 매니페스트에 나열된 모든 사용자 지정 모듈에 대한 모듈 기능 모델이 생성됩니다.
* 각 모듈 기능 모델과 디바이스 모델 간의 관계가 설정됩니다.
* 모듈 기능 모델은 하나 이상의 모듈 인터페이스를 구현합니다.
* 각 모듈 인터페이스에는 원격 분석, 속성 및 명령이 포함됩니다.

### <a name="iot-edge-deployment-manifests-and-iot-central-device-templates"></a>IoT Edge 배포 매니페스트 및 IoT Central 디바이스 템플릿

IoT Edge에서는 비즈니스 논리를 모듈 형태로 배포하고 관리합니다. IoT Edge 모듈은 IoT Edge에서 관리하는 가장 작은 계산 단위이며, Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션 관련 코드를 포함할 수 있습니다.

IoT Edge *배포 매니페스트* 는 디바이스에 배포할 IoT Edge 모듈과 해당 모듈의 구성 방법을 나열합니다. 자세한 내용은 [IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기](../../iot-edge/module-composition.md)를 참조하세요.

Azure IoT Central에서 IoT Edge 디바이스에 대한 디바이스 템플릿을 만들기 위해 배포 매니페스트를 가져옵니다.

다음 코드 조각은 IoT Edge 배포 매니페스트 예제를 보여 줍니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10
      }
    }
  }
}
```

이전 코드 조각에서는 다음을 확인할 수 있습니다.

* 세 개의 모듈이 있습니다. 모든 배포 매니페스트에 있는 *IoT Edge 에이전트* 및 *IoT Edge 허브* 시스템 모듈. 사용자 지정 **SimulatedTemperatureSensor** 모듈.
* 공개 모듈 이미지는 연결하는 데 자격 증명이 필요하지 않은 Azure Container Registry 리포지토리에서 가져옵니다. 비공개 모듈 이미지의 경우 *IoT Edge 에이전트* 모듈의 `registryCredentials` 설정에서 사용할 컨테이너 레지스트리 자격 증명을 설정합니다.
* 사용자 지정 **SimulatedTemperatureSensor** 모듈에는 `"SendData": true` 및 `"SendInterval": 10`이라는 두 개의 속성이 있습니다.

이 배포 매니페스트를 IoT Central 애플리케이션으로 가져오면 다음 디바이스 템플릿이 생성됩니다.

:::image type="content" source="media/concepts-iot-edge/device-template.png" alt-text="배포 매니페스트로 생성된 디바이스 템플릿을 보여 주는 스크린샷":::

이전 스크린샷에서 다음을 확인할 수 있습니다.

* **SimulatedTemperatureSensor** 라는 모듈. *IoT Edge 에이전트* 및 *IoT Edge 허브* 시스템 모듈은 템플릿에 표시되지 않습니다.
* **SendData** 및 **SendInterval** 이라는 두 개의 쓰기 가능 속성이 포함된 **management** 라는 인터페이스.

배포 매니페스트에는 **SimulatedTemperatureSensor** 모듈에서 전송하는 원격 분석 또는 응답하는 명령에 대한 정보가 포함되지 않습니다. 디바이스 템플릿을 게시하기 전에 이러한 정의를 디바이스 템플릿에 수동으로 추가하세요.

자세한 내용은 [자습서: Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 추가](tutorial-add-edge-as-leaf-device.md)를 참조하세요.

### <a name="update-a-deployment-manifest"></a>배포 매니페스트 업데이트

배포 매니페스트를 바꾸면 연결된 모든 IoT Edge 디바이스가 새 매니페스트를 다운로드하고 해당 모듈을 업데이트합니다. 그러나 IoT Central은 모듈 구성에 대한 변경 내용으로 디바이스 템플릿의 인터페이스를 업데이트하지 않습니다. 예를 들어, 이전 코드 조각에 표시된 매니페스트를 다음 매니페스트로 바꾸면 디바이스 템플릿의 **관리** 인터페이스에 **SendUnits** 속성이 자동으로 표시되지 않습니다. IoT Central이 인식할 수 있도록 **관리** 인터페이스에 새 속성을 수동으로 추가합니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
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
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0.9",
              "createOptions": "{}"
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0.9",
              "createOptions": "{}"
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
        "schemaVersion": "1.0",
        "routes": {
            "route": "FROM /* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      "properties.desired": {
           "SendData": true,
           "SendInterval": 10,
           "SendUnits": "Celsius"
      }
    }
  }
}
```

## <a name="deploy-the-iot-edge-runtime"></a>IoT Edge 런타임 배포

IoT Edge 런타임을 실행할 수 있는 위치에 대한 자세한 내용은 [Azure IoT Edge 지원 시스템](../../iot-edge/support.md)을 참조하세요.

다음 환경에서 IoT Edge 런타임을 설치할 수도 있습니다.

* [Linux용 Azure IoT Edge 설치 또는 제거](../../iot-edge/how-to-install-iot-edge.md)
* [Windows 디바이스에서 Linux용 Azure IoT Edge 설치 및 프로비저닝(미리 보기)](../../iot-edge/how-to-install-iot-edge-on-windows.md)
* [Azure의 Ubuntu Virtual Machines에서 Azure IoT Edge 실행](../../iot-edge/how-to-install-iot-edge-ubuntuvm.md)

## <a name="iot-edge-gateway-devices"></a>IoT Edge 게이트웨이 디바이스

IoT Edge 디바이스를 게이트웨이 디바이스로 선택한 경우 게이트웨이 디바이스에 연결하려는 디바이스의 디바이스 모델에 다운스트림 관계를 추가할 수 있습니다.

자세한 내용은 [IoT Edge 투명 게이트웨이를 통해 디바이스를 연결하는 방법](how-to-connect-iot-edge-transparent-gateway.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 단계에서 [고유의 IoT Edge 모듈을 개발](../../iot-edge/module-development.md)하는 방법을 배우는 것이 좋습니다.
