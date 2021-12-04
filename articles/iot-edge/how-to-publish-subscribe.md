---
title: Azure IoT Edge를 사용하여 게시 및 구독 | Microsoft Docs
description: IoT Edge MQTT broker를 사용하여 메시지 게시 및 구독
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/30/2021
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 385a1c9a18fec134827c9c28ff0d52f107dee8c7
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133539214"
---
# <a name="publish-and-subscribe-with-azure-iot-edge-preview"></a>Azure IoT Edge를 사용한 게시 및 구독(미리 보기)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Azure IoT Edge mqtt broker를 사용 하 여 메시지를 게시 하 고 구독할 수 있습니다. 이 문서에서는 이 broker에 연결하고, 사용자 정의 토픽을 통해 메시지를 게시 및 구독하며, IoT Hub 메시징 기본 형식을 사용하는 방법을 보여 줍니다. IoT Edge MQTT broker는 IoT Edge 허브에 빌드됩니다. 자세한 내용은 [IoT Edge 허브의 중개 기능](iot-edge-runtime.md)을 참조하세요.

> [!NOTE]
> IoT Edge MQTT broker는 현재 퍼블릭 미리 보기로 제공됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- 유효한 구독이 있는 Azure 계정.
- `azure-iot` CLI 확장이 설치된 [Azure CLI](/cli/azure/). 자세한 내용은 [Azure CLI에 대 한 Azure IoT 확장 설치 단계](/cli/azure/azure-cli-reference-for-iot)를 참조 하세요.
- SKU의 **IoT Hub** F1, S1, S2 또는 S3.
- 1.2 이상 버전의 **IoT Edge 장치가** 있어야 합니다. 장치에는 MQTT broker 기능이 설정 된 상태에서 edgeAgent 및 edgeHub 모듈 버전 1.2 이상을 포함 해야 하며, TLS가 아닌 연결을 사용 하려면 호스트에 바인딩된 edgeHub 포트 1883이 있어야 합니다. Azure VM에서 IoT Edge 1.2을 자동으로 배포 하는 방법에 대 한 자세한 내용은 [Ubuntu Virtual Machines에서 Azure IoT Edge 실행](how-to-install-iot-edge-ubuntuvm.md)을 참조 하세요. IoT Edge MQTT broker는 현재 공개 미리 보기 상태 이므로 `experimentalFeatures__enabled` `experimentalFeatures__mqttBrokerEnabled` edgeHub 모듈에서 및 환경 변수를로 설정 하 여 mqtt broker를 사용 하도록 설정 해야 합니다 `true` .

   에서 오픈 권한 부여 정책과 함께 이러한 조건을 충족 하는 IoT Edge 배포를 빠르게 만들려면 `test_topic` 이 문서의 끝에 있는 [샘플 배포 매니페스트](#appendix---sample-deployment-manifest) 를 사용 하 고 다음 단계를 수행 하면 됩니다.

   1. 배포 파일을 작업 폴더에 저장 합니다.

   2. 다음 Azure CLI 명령을 사용 하 여이 배포를 IoT Edge 장치에 적용 합니다.

    ```azurecli
    az iot edge set-modules --device-id <device_id> --hub-name <hub_name> --content <deployment_file_path>
    ```

    이 명령에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 Azure IoT에 지 모듈 배포](how-to-deploy-modules-cli.md)를 참조 하세요.

- IoT Edge 디바이스에 설치된 **Mosquitto 클라이언트**. 이 문서에서는 널리 사용되는 Mosquitto 클라이언트 [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html)와 [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html)를 사용합니다. 다른 MQTT 클라이언트를 대신 사용할 수 있습니다. Ubuntu 디바이스에 Mosquitto 클라이언트를 설치하려면 다음 명령을 실행합니다.

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    > [!WARNING]
    > Mosquitto 서버는 MQTT 포트 (8883 및 1883)를 차단할 수 있으며 IoT Edge 허브와 충돌 하므로 설치 하지 마세요.

## <a name="connect-to-iot-edge-hub"></a>IoT Edge 허브에 연결

IoT Edge 허브에 연결 하는 것은 [IoT Hub에 연결](../iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub) 하거나 [IoT Edge 허브에 연결](../iot-edge/iot-edge-runtime.md#connecting-to-the-iot-edge-hub)에 설명 된 것과 동일한 단계를 따릅니다. 해당 단계는 다음과 같습니다.

1. 선택적으로 MQTT 클라이언트는 TLS (Transport Layer Security)를 사용 하 여 IoT Edge 허브와의 *보안 연결* 을 설정 합니다.
2. MQTT 클라이언트는 IoT Edge 허브에 대해 자신을 *인증* 합니다.
3. IoT Edge 허브는 권한 부여 정책에 따라 MQTT 클라이언트에 *권한을* 부여 합니다.

### <a name="secure-connection-tls"></a>보안 연결(TLS)

TLS는 클라이언트와 IoT Edge 허브 간의 암호화 된 통신을 설정 하는 데 사용 됩니다.

- TLS를 *사용 하지 않도록 설정* 하려면: 포트 1883 (mqtt)를 사용 하 고 edgeHub 컨테이너를 포트 1883에 바인딩합니다.

- Tls를 *사용 하도록 설정* 하려면: 클라이언트에서 mqtts broker에 포트 8883 (mqtts)에 연결 하는 경우 tls 채널이 시작 됩니다. 브로커가 클라이언트에서 유효성을 검사할 인증서 체인을 보냅니다. 인증서 체인의 유효성을 검사 하려면 MQTT broker의 루트 인증서를 클라이언트에 신뢰할 수 있는 인증서로 설치 해야 합니다. 루트 인증서를 신뢰할 수 없는 경우 클라이언트 라이브러리는 인증서 확인 오류가 발생 하 여 MQTT broker에서 거부 됩니다. 클라이언트에 broker의 루트 인증서를 설치 하는 단계는 [투명 게이트웨이](how-to-create-transparent-gateway.md) 사례와 동일 하며 *방법: 다운스트림 장치를 Azure IoT에 지 게이트웨이의 커넥트* 다운스트림 장치 [준비](how-to-connect-downstream-device.md#prepare-a-downstream-device) 섹션에서 설명 합니다.

### <a name="authentication"></a>인증

자신을 인증 하려면 먼저 MQTT 클라이언트에서 연결 패킷을 MQTT broker에 전송 하 여 해당 이름으로 연결을 시작 해야 합니다. 이 패킷은 `client identifier`, `username`, `password`의 세 가지 인증 정보를 제공합니다.

- `client identifier` 필드는 IoT Hub의 모듈 이름 또는 디바이스 이름입니다. 이 도구는 다음 구문을 사용합니다.

  - 디바이스의 경우: `<device_name>`

  - 모듈의 경우: `<device_name>/<module_name>`

   MQTT broker에 연결 하려면 장치 또는 모듈을 IoT Hub에 등록 해야 합니다.

   broker는 같은 자격 증명을 사용하는 여러 클라이언트의 연결을 허용하지 않습니다. 두 번째 클라이언트가 동일한 자격 증명을 사용 하 여 연결 하는 경우 broker는 이미 연결 된 클라이언트의 연결을 끊습니다.

- 필드는 장치 `username` 또는 모듈 이름과 장치가 속한 IoT hub 이름에서 파생 됩니다. 이 도구는 다음 구문을 사용합니다.

  - 디바이스의 경우: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - 모듈의 경우: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- CONNECT 패킷의 `password` 필드는 다음 인증 모드에 따라 달라집니다.

  - [대칭 키 인증](how-to-authenticate-downstream-device.md#symmetric-key-authentication)을 사용하면 `password` 필드는 SAS 토큰입니다.
  - [X.509 자체 서명 된 인증](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)을 사용 하는 경우이 `password` 필드는 존재 하지 않습니다. 이 인증 모드에서는 TLS 채널이 필요합니다. 클라이언트는 TLS 연결을 설정하기 위해 8883 포트에 연결해야 합니다. TLS 핸드셰이크 중에 MQTT broker가 클라이언트 인증서를 요청합니다. 이 인증서는 클라이언트의 id를 확인 하는 데 사용 되므로 `password` 나중에 연결 패킷이 전송 될 때 해당 필드가 필요 하지 않습니다. 클라이언트 인증서와 암호 필드를 모두 보내면 오류가 발생하고 연결이 닫힙니다. 일반적으로 MQTT 라이브러리 및 TLS 클라이언트 라이브러리를 통해 연결을 시작할 때 클라이언트 인증서를 보낼 수 있습니다. [x.509 자체 서명 인증](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) 섹션 ( *방법: 다운스트림 장치를 Azure IoT 허브에 인증)* 에서 단계별 예제를 확인할 수 있습니다.

IoT Edge에서 배포한 모듈은 [대칭 키 인증](how-to-authenticate-downstream-device.md#symmetric-key-authentication)을 사용하고 로컬 [IoT Edge 워크로드 API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md)를 호출하여 오프라인 상태에서도 프로그래매틱 방식으로 SAS 토큰을 가져올 수 있습니다.

### <a name="authorization"></a>권한 부여

MQTT 클라이언트가 IoT Edge 허브에 인증 되 면 연결할 권한이 있어야 합니다. 일단 연결 되 면 특정 항목을 게시 하거나 구독할 수 있는 권한이 있어야 합니다. 해당 권한은 권한 부여 정책에 따라 IoT Edge 허브에서 부여합니다. 권한 부여 정책은 트윈을 통해 IoT Edge 허브로 전송되는 JSON 구조로 표시된 설명문 세트입니다. IoT Edge 허브 트윈을 편집하여 해당 권한 부여 정책을 구성합니다.

> [!NOTE]
> 퍼블릭 미리 보기의 경우 Azure CLI만 MQTT 브로커 권한 부여 정책이 포함된 배포를 지원합니다. 현재 Azure Portal는 IoT Edge 허브 쌍 및 해당 권한 부여 정책 편집을 지원 하지 않습니다.

각 권한 부여 정책 설명문은 `identities`, `allow` 또는 `deny` 효과, `operations` 및 `resources`의 조합으로 구성됩니다.

- `identities`는 정책의 주체를 설명합니다. CONNECT 패킷의 클라이언트에서 보낸 `username`에 매핑되고 `<iot_hub_name>.azure-devices.net/<device_name>` 또는 `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>` 형식이어야 합니다.
- `allow`나 `deny` 효과는 작업을 허용할지 아니면 거부할지를 정의합니다.
- `operations`는 권한을 부여할 작업을 정의합니다. `mqtt:connect`, `mqtt:publish` 및 `mqtt:subscribe` 는 현재 지원 되는 세 가지 동작입니다.
- `resources`는 정책의 개체를 정의합니다. [MQTT 와일드카드](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)를 사용하여 정의된 토픽이나 토픽 패턴이 될 수 있습니다.

다음 JSON 코드 조각은 클라이언트 "rogue_client"의 연결을 명시적으로 허용 하지 않는 권한 부여 정책의 예제입니다 .이를 통해 Azure IoT 클라이언트를 연결 하 고 "sensor_1"를 토픽에 게시할 수 있습니다 `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "<iot_hub_name>.azure-devices.net/rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "<iot_hub_name>.azure-devices.net/sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

권한 부여 정책을 작성할 때 다음 사항에 유의 하세요.

- 쌍 `$edgeHub` 스키마 버전 1.2이 필요 합니다.
- 기본적으로 모든 작업이 거부됩니다.
- 권한 부여 설명문은 JSON 정의에 표시되는 순서대로 평가됩니다. `identities`먼저를 확인 한 다음 요청에 일치 하는 첫 번째 *허용* 또는 *거부* 문을 선택 합니다. 이러한 문 사이에 충돌이 발생 하면 *deny* 문이 승리 합니다.
- 권한 부여 정책에서 여러 변수(예: substitutions)를 사용할 수 있습니다.

  - `{{iot:identity}}`는 현재 연결된 클라이언트의 ID를 나타냅니다. 예를 들어 `<iot_hub_name>.azure-devices.net/myDevice`와 같은 디바이스 ID 또는 `<iot_hub_name>.azure-devices.net/myEdgeDevice/SampleModule`과 같은 모듈 ID입니다.
  - `{{iot:device_id}}`는 현재 연결된 디바이스의 ID를 나타냅니다. 예를 들어 `myDevice`와 같은 디바이스 ID나 `myEdgeDevice`와 같이 모듈이 실행되는 디바이스 ID입니다.
  - `{{iot:module_id}}`는 현재 연결된 모듈의 ID를 나타냅니다. 이 변수는 연결된 디바이스나 `SampleModule`과 같은 모듈 ID의 경우 비어 있습니다.
  - `{{iot:this_device_id}}`는 권한 부여 정책을 실행하는 IoT Edge 디바이스의 ID를 나타냅니다. 예들 들어 `myIoTEdgeDevice`입니다.

IoT 허브 권한 부여 토픽은 사용자 정의 토픽과 약간 다르게 처리됩니다. 유의 해야 할 주요 사항은 다음과 같습니다.

- Azure IoT 디바이스나 모듈에는 IoT Edge 허브 MQTT broker에 연결하기 위한 명시적 권한 부여 규칙이 필요합니다. 기본 연결 권한 부여 정책은 아래에 제공됩니다.
- Azure IoT 디바이스나 모듈은 명시적 권한 부여 규칙 없이 기본적으로 자체 IoT 허브 토픽에 액세스할 수 있습니다. 그러나 이 경우 권한 부여는 부모/자식 관계에서 비롯되므로 해당 관계를 설정해야 합니다. IoT Edge 모듈은 IoT Edge 디바이스의 자식으로 자동 설정되지만, 디바이스는 IoT Edge 게이트웨이의 자식으로 명시적으로 설정해야 합니다.

다음 JSON 조각은 모든 Azure IoT 디바이스 또는 모듈이 broker에 **연결할** 수 있도록 하는 데 사용할 수 있는 기본 권한 부여 정책의 예입니다.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

이제 IoT Edge MQTT Broker에 연결하는 방법을 이해했으므로 먼저 사용자 정의 토픽에서 메시지를 게시하고 구독한 다음, IoT Hub 토픽 및 마지막으로 다른 MQTT broker에 메시지를 게시하고 구독하는 방법을 알아보겠습니다.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>사용자 정의 토픽에 게시 및 구독

이 섹션에서는 토픽을 구독하는 **sub_client** 라는 클라이언트 하나와 토픽에 게시하는 **pub_client** 라는 다른 클라이언트를 사용합니다. [대칭 키 인증을](how-to-authenticate-downstream-device.md#symmetric-key-authentication)사용하지만 [X.509 자체 서명 인증](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) 또는 [X.509 CA 서명 인증](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)을 사용하여 동일한 작업을 수행할 수 있습니다.

### <a name="create-publisher-and-subscriber-clients"></a>게시자와 구독자 클라이언트 만들기

터미널을 열고 이 섹션에 설명된 Azure CLI 명령을 사용하여 IoT Hub 두 개의 IoT 디바이스를 만들고 암호를 얻습니다.

1. 다음 명령을 실행하여 IoT Hub 두 개의 IoT 디바이스를 만듭니다.

   ```azurecli-interactive
   az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name>
   az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name>
   ```

2. 다음 명령을 실행하여 IoT 디바이스의 부모를 IoT Edge 디바이스로 설정합니다.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   az iot hub device-identity parent set --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   ```


3. 각 IoT 디바이스 또는 모듈에 대해 다음 명령을 실행하여 SAS 토큰을 생성하여 암호를 가져옵니다.

   - 디바이스의 경우:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
     ```

   - 모듈의 경우:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
     ```

    > [!NOTE]
    > 이러한 명령의 매개 변수에 할당된 값 3600은 `du` SAS 토큰 기간(초)에 해당합니다. 매개 변수에 3600을 할당하면 토큰이 1시간 동안 지속됩니다.

4. 출력의 키에 해당하는 값인 SAS `sas` 토큰을 복사합니다. 다음은 3단계에서 실행한 Azure CLI 명령의 출력 예제입니다.

   ```json
   {
      "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
   }
   ```

### <a name="authorize-publisher-and-subscriber-clients"></a>게시자와 구독자 클라이언트에 권한 부여

게시자 및 구독자에게 권한을 부여하려면 다음 권한 부여 정책을 포함하는 IoT Edge 배포에서 IoT Edge 허브 쌍을 편집합니다.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

> [!NOTE]
> 현재 MQTT 권한 부여 속성을 포함하는 배포는 Azure CLI를 사용하여 IoT Edge 디바이스에만 적용할 수 있습니다.

### <a name="symmetric-keys-authentication-without-tls"></a>TLS를 사용하지 않는 대칭 키 인증

#### <a name="subscribe"></a>구독

IoT Edge 디바이스에서 다음 명령을 실행하여 **sub_client** MQTT 클라이언트를 MQTT broker에 연결하고 `test_topic`을 구독합니다.

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

이 코드 조각에서 `<edge_device_address>` `localhost` 는 클라이언트가 IoT Edge 동일한 디바이스에서 실행되기 때문에 입니다.

> [!NOTE]
> 이 첫 번째 예제에서는 TLS를 사용할 수 없으므로 포트 1883(MQTT)이 사용됩니다. 이 예제가 작동하려면 edgeHub 포트 1883을 만들기 옵션을 통해 호스트에 바인딩해야 합니다. 필수 구성 조건 [섹션에 예제가](#prerequisites)제공됩니다. 포트 8883(MQTTS)을 사용하여 TLS를 사용하도록 설정한 또 다른 예제는 이 문서의 더 아래 [TLS를 사용한 대칭 키 인증 섹션에](#symmetric-keys-authentication-with-tls) 표시됩니다.

이제 **sub_client** MQTT 클라이언트가 시작되고 `test_topic`에서 들어오는 메시지를 기다립니다.

#### <a name="publish"></a>게시

pub_client MQTT 클라이언트를 MQTT broker에 **커넥트** `test_topic` 다른 터미널에서 IoT Edge 디바이스에서 다음 명령을 실행하여 위와 동일한 에 메시지를 게시합니다.

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

이 코드 조각에서 `<edge_device_address>` `localhost` 는 클라이언트가 IoT Edge 동일한 디바이스에서 실행되기 때문에 입니다.

명령을 실행하면 **sub_client** MQTT 클라이언트가 “hello” 메시지를 받습니다.

### <a name="symmetric-keys-authentication-with-tls"></a>TLS를 사용하는 대칭 키 인증

TLS를 사용하도록 설정하려면 포트를 1883(MQTT)에서 8883(MQTTS)으로 변경해야 하며, 클라이언트는 MQTT 브로커가 보낸 인증서 체인의 유효성을 검사할 수 있도록 MQTT 브로커의 루트 인증서를 가지고 있어야 합니다. 이렇게 하려면 [TLS(보안 연결) 섹션에](#secure-connection-tls)제공된 단계를 수행합니다.

클라이언트는 위의 예제에서 MQTT 브로커와 동일한 디바이스에서 실행되므로 동일한 단계에 따라 다음과 같은 방법으로 TLS를 사용하도록 설정합니다.

- 포트 번호를 1883(MQTT)에서 8883(MQTTS)으로 변경
- `--cafile /certs/certs/azure-iot-test-only.root.ca.cert.pem`과 유사한 매개 변수를 사용하여 mosquitto_pub 및 mosquitto_sub 클라이언트에 CA 루트 인증서 전달
- 인증서 체인의 유효성을 검사할 수 있도록 mosquitto_pub 및 mosquitto_sub 클라이언트에 전달된 호스트 이름 매개 변수를 통해 `localhost` 대신, IoT Edge에 설정된 실제 호스트 이름 전달

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>IoT Hub 토픽 게시 및 구독

[Azure IoT 디바이스 SDK를](https://github.com/Azure/azure-iot-sdks) 통해 클라이언트는 이미 IoT Hub 작업을 수행할 수 있지만 사용자 정의 토픽을 게시하거나 구독할 수는 없습니다. IoT Hub 기본 프로토콜이 IoT Hub 경우 게시 및 구독 의미 체계를 사용하는 모든 MQTT 클라이언트를 사용하여 작업을 수행할 수 있습니다. 이 가이드의 다음 섹션에서는 이러한 프로토콜의 작동 방식을 설명하는 세부 사항을 설명합니다.

### <a name="send-telemetry-data-to-iot-hub"></a>IoT Hub에 원격 분석 데이터 보내기

원격 분석 데이터를 IoT Hub로 보내는 것은 사용자 정의 토픽에 게시하는 것과 비슷하지만, 특정 IoT Hub 토픽을 사용합니다.

- 디바이스의 경우 `devices/<device_name>/messages/events/` 토픽에서 원격 분석이 전송됩니다.
- 모듈의 경우 `devices/<device_name>/modules/<module_name>/messages/events/` 토픽에서 원격 분석이 전송됩니다.

또한 와 같은 경로를 만들어 `FROM /messages/* INTO $upstream` IoT Edge MQTT broker에서 IoT Hub로 원격 분석을 보냅니다. 라우팅에 대한 자세한 내용은 [경로 선언을 참조하세요.](module-composition.md#declare-routes)

### <a name="get-twin"></a>트윈 가져오기

디바이스 또는 모듈 쌍을 얻는 것은 일반적인 MQTT 패턴이 아닙니다. 클라이언트는 IoT Hub가 서비스를 제공할 트윈에 대한 요청을 발행해야 합니다.

쌍을 받으려면 클라이언트는 IoT Hub 관련 항목을 구독해야 `$iothub/twin/res/#` 합니다. 이 토픽 이름은 IoT Hub에서 상속되며, 모든 클라이언트가 같은 토픽을 구독해야 합니다. 디바이스 또는 모듈이 서로의 쌍을 수신한다는 의미는 아닙니다. IoT Hub 및 IoT Edge 허브는 모든 디바이스가 동일한 토픽 이름을 수신하는 경우에도 어떤 쌍을 배달해야 하는지 알고 있습니다.

구독이 만들어지면 클라이언트는 IoT Hub 관련 항목에 메시지를 게시하여 트윈을 요청해야 `$iothub/twin/GET/?rid=<request_id>/#` 합니다. `<request_id>`이 코드 조각의 는 임의의 식별자입니다. 그러면 IoT Hub 클라이언트가 구독하는 토픽의 요청된 데이터와 함께 응답을 `$iothub/twin/res/200/?rid=<request_id>` 보냅니다. 이 프로세스는 클라이언트가 해당 요청을 응답과 페어링하는 방법입니다.

### <a name="receive-twin-patches"></a>트윈 패치 수신

쌍 패치를 받으려면 클라이언트가 다음과 같은 특수 IoT Hub 항목을 구독해야 `$iothub/twin/PATCH/properties/desired/#` 합니다. 구독하고 나면 클라이언트는 이 토픽에서 IoT Hub가 보낸 트윈 패치를 받습니다.

### <a name="receive-direct-methods"></a>직접 메서드 수신

직접 메서드를 받는 것은 전체 트윈을 받는 것과 비슷하며, 추가로 클라이언트가 호출을 받았음을 확인해 주어야 합니다. 먼저 클라이언트는 다음과 같은 특수 IoT Hub 항목을 `$iothub/methods/POST/#` 구독합니다. 이 항목에서 직접 메서드를 받으면 클라이언트는 직접 메서드가 수신되는 하위 항목에서 요청 식별자를 `rid` 추출하고 마지막으로 다음과 같은 특별한 IoT Hub 항목에 대한 확인 메시지를 게시해야 `$iothub/methods/res/200/<request_id>` 합니다.

### <a name="send-direct-methods"></a>직접 메서드 보내기

직접 메서드를 보내는 것은 HTTP 호출이므로 MQTT broker를 거치지 않습니다. 직접 메서드를 IoT Hub 보내는 방법에 대한 자세한 내용은 [직접 메서드 이해 및 호출을 참조하세요.](../iot-hub/iot-hub-devguide-direct-methods.md) 직접 메서드를 다른 모듈에 로컬로 보내는 방법에 대한 자세한 내용은 이 [Azure IoT C# SDK 직접 메서드 호출 예제를](https://github.com/Azure/azure-iot-sdk-csharp/blob/main/iothub/device/src/ModuleClient.cs#L597)참조하세요.

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>MQTT broker를 게시하고 구독합니다.

두 MQTT broker를 연결하기 위해 IoT Edge 허브에는 MQTT 브리지가 포함됩니다. MQTT 브리지는 일반적으로 실행 중인 MQTT Broker를 다른 MQTT 브로커에 연결하는 데 사용됩니다. 일반적으로 로컬 트래픽의 하위 집합만 다른 broker로 푸시합니다.

> [!NOTE]
> IoT Edge 허브 브리지는 현재 중첩된 IoT Edge 디바이스 간에만 사용할 수 있습니다. IoT Hub 완전한 기능을 갖춘 MQTT 브로커가 아니므로 IoT Hub 데이터를 보내는 데 사용할 수 없습니다. MQTT broker 기능 지원 IoT Hub 대한 자세한 내용은 [MQTT 프로토콜을 사용하여 IoT Hub와 통신을 참조하세요.](../iot-hub/iot-hub-mqtt-support.md) IoT Edge 디바이스를 중첩하는 방법에 관한 자세한 정보는 [다운스트림 IoT Edge 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)을 참조하세요.

중첩 구성에서 IoT Edge 허브 MQTT 브리지는 부모 MQTT broker의 클라이언트 역할을 하기 때문에 자식 EdgeHub에서 브리지가 구성된 특정 사용자 정의 토픽을 게시하고 구독할 수 있도록 부모 EdgeHub에서 권한 부여 규칙을 설정해야 합니다.

IoT Edge MQTT 브리지는 쌍을 통해 IoT Edge 허브로 전송되는 JSON 구조를 통해 구성됩니다. IoT Edge 허브 트윈을 편집하여 MQTT 브리지를 구성합니다.

> [!NOTE]
> 퍼블릭 미리 보기의 경우 Azure CLI만 MQTT 브리지 구성이 포함된 배포를 지원합니다. Azure Portal 현재 IoT Edge 허브 쌍 및 해당 MQTT 브리지 구성 편집을 지원하지 않습니다.

IoT Edge 허브 MQTT broker를 여러 외부 브로커에 연결하도록 MQTT 브리지를 구성할 수 있습니다. 외부 broker마다 다음 설정이 필요합니다.

- `endpoint`는 연결할 원격 MQTT broker의 주소입니다. 현재 부모 IoT Edge 디바이스만 지원되며 `$upstream` 변수로 정의됩니다.
- `settings`는 엔드포인트의 브리지로 연결할 토픽을 정의합니다. 엔드포인트당 설정이 여러 개일 수 있으며, 다음 값을 사용하여 구성할 수 있습니다.
  - `direction` 는 `in` 원격 브로커의 토픽을 구독하거나 원격 `out` 브로커의 토픽에 게시하는 것입니다.
  - `topic` 는 일치시킬 핵심 항목 패턴입니다. [MQTT 와일드카드](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)를 사용하여 이 패턴을 정의할 수 있습니다. 로컬 broker와 원격 broker의 이 토픽 패턴에 서로 다른 접두사를 적용할 수 있습니다.
  - `outPrefix` 는 `topic` 원격 브로커의 패턴에 적용되는 접두사입니다.
  - `inPrefix` 는 `topic` 로컬 브로커의 패턴에 적용되는 접두사입니다.

다음 JSON 조각은 부모 IoT Edge 디바이스의 토픽에서 받은 모든 메시지를 `alerts/#` 동일한 토픽의 자식 IoT Edge 디바이스에 다시 게시하고, 자식 IoT Edge 디바이스의 토픽에서 보낸 모든 메시지를 토픽의 부모 IoT Edge 디바이스에 다시 게시하는 IoT Edge MQTT 브리지 `/local/telemetry/#` 구성의 예입니다. `/remote/messages/#`

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "#",
                    "inPrefix": "/local/telemetry/",
                    "outPrefix": "/remote/messages/"
                }
            ]
        }]
    }
}
```

> [!NOTE]
> MQTT broker가 사용되고 IoT Edge 중첩된 구성(예: 지정된 )에 있는 경우 MQTT 프로토콜이 자동으로 업스트림 프로토콜로 `parent_hostname` 사용됩니다. 업스트림 프로토콜에 관한 자세한 내용은 [클라우드 통신](iot-edge-runtime.md#cloud-communication)을 참조하세요. 중첩 구성에 관한 자세한 정보는 [다운스트림 IoT Edge 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)을 참조하세요.

## <a name="next-steps"></a>다음 단계

IoT Edge 허브에 대한 자세한 내용은 [IoT Edge 허브 이해를 참조하세요.](iot-edge-runtime.md#iot-edge-hub)

## <a name="appendix---sample-deployment-manifest"></a>부록 - 샘플 배포 매니페스트

다음은 IoT Edge에서 MQTT 브로커를 사용하도록 설정하는 데 사용할 수 있는 전체 배포 매니페스트입니다. MQTT Broker 기능이 활성화되고 edgeHub 포트 1883이 활성화된 IoT Edge 버전 1.2가 배포되고 에 대한 권한 부여 정책이 `test_topic` 열립니다.

```json
{
   "modulesContent":{
      "$edgeAgent":{
         "properties.desired":{
            "schemaVersion":"1.1",
            "runtime":{
               "type":"docker",
               "settings":{
                  "minDockerVersion":"v1.25",
                  "loggingOptions":"",
                  "registryCredentials":{
                     
                  }
               }
            },
            "systemModules":{
               "edgeAgent":{
                  "type":"docker",
                  "settings":{
                     "image":"mcr.microsoft.com/azureiotedge-agent:1.2",
                     "createOptions":"{}"
                  }
               },
               "edgeHub":{
                  "type":"docker",
                  "status":"running",
                  "restartPolicy":"always",
                  "settings":{
                     "image":"mcr.microsoft.com/azureiotedge-hub:1.2",
                     "createOptions":"{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"1883/tcp\":[{\"HostPort\":\"1883\"}]}}}"
                  },
                  "env":{
                     "experimentalFeatures__mqttBrokerEnabled":{
                        "value":"true"
                     },
                     "experimentalFeatures__enabled":{
                        "value":"true"
                     },
                     "RuntimeLogLevel":{
                        "value":"debug"
                     }
                  }
               }
            },
            "modules":{
               
            }
         }
      },
      "$edgeHub":{
         "properties.desired":{
            "schemaVersion":"1.2",
            "routes":{
               "Upstream":"FROM /messages/* INTO $upstream"
            },
            "storeAndForwardConfiguration":{
               "timeToLiveSecs":7200
            },
            "mqttBroker":{
               "authorizations":[
                  {
                     "identities":[
                        "{{iot:identity}}"
                     ],
                     "allow":[
                        {
                           "operations":[
                              "mqtt:connect"
                           ]
                        }
                     ]
                  },
                  {
                     "identities":[
                        "{{iot:identity}}"
                     ],
                     "allow":[
                        {
                           "operations":[
                              "mqtt:publish",
                              "mqtt:subscribe"
                           ],
                           "resources":[
                              "test_topic"
                           ]
                        }
                     ]
                  }
               ]
            }
         }
      }
   }
}
```
