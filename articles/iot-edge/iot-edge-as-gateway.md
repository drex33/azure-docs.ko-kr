---
title: 다운스트림 디바이스의 게이트웨이 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge를 사용하여 데이터를 여러 다운스트림 디바이스에서 클라우드로 보내거나 로컬로 처리하는 투명, 불투명 또는 프록시 게이트웨이 디바이스를 만듭니다.
author: kgremban
ms.author: kgremban
ms.date: 03/23/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 478bf78549e15c3c0989ae4850d515bdcce5217d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706742"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge 디바이스를 게이트웨이로 사용하는 방법

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge 디바이스는 게이트웨이로 작동할 수 있으며 네트워크의 다른 디바이스와 IoT Hub 간에 연결을 제공합니다.

IoT Edge 허브 모듈은 IoT Hub처럼 작동하므로 동일한 IoT hub를 사용하는 ID가 있는 다른 디바이스의 연결을 처리할 수 있습니다. 이 형식의 게이트웨이 패턴은 그 사이에 게이트웨이가 없었던 것처럼 다운스트림 디바이스에서 IoT Hub로 전달될 수 있기 때문에 ‘투명’이라고 합니다.

자체 IoT Hub에 연결되지 않거나 연결할 수 없는 디바이스의 경우 IoT Edge 게이트웨이에서 해당 연결을 제공할 수 있습니다. IoT Edge 디바이스는 수신 다운스트림 디바이스 메시지가 IoT Hub로 전달되기 전에 이에 대한 프로세스를 수행해야 하므로 이 형식의 게이트웨이 패턴을 ‘변환’이라고 합니다. 해당 시나리오에는 단계를 처리하기 위해 IoT Edge 게이트웨이의 추가 모듈이 필요합니다.

투명 및 변환 게이트웨이 패턴은 상호 배타적이지 않습니다. 단일 IoT Edge 디바이스는 투명 게이트웨이 및 변환 게이트웨이로 작동할 수 있습니다.

모든 게이트웨이 패턴은 다음과 같은 이점을 제공합니다.

* **에지에서의 분석** – 클라우드에 전체 충실도 원격 분석 데이터를 보내지 않고 AI 서비스를 로컬로 사용하여 다운스트림 디바이스에서 오는 데이터를 처리합니다. 고급 정보를 로컬로 검색하여 대응하고 IoT Hub에는 데이터 하위 집합만 보냅니다.
* **다운스트림 디바이스 격리** – 게이트웨이 디바이스는 모든 다운스트림 디바이스를 인터넷에 노출되지 않도록 보호할 수 있습니다. 연결되지 않은 OT(운영 기술) 네트워크와 웹에 대한 액세스를 제공하는 IT(정보 기술) 네트워크 사이에 위치할 수 있습니다. 마찬가지로 자체 IoT Hub에 연결하는 기능이 없는 디바이스는 대신 게이트웨이 디바이스에 연결할 수 있습니다.
* **멀티플렉싱 연결** - IoT Edge 게이트웨이를 통해 IoT Hub에 연결하는 모든 디바이스는 동일한 기본 연결을 사용합니다. 이 멀티플렉싱 기능을 사용하려면 IoT Edge 게이트웨이에서 AMQP를 업스트림 프로토콜로 사용해야 합니다.
* **트래픽 다듬기** - IoT Hub가 트래픽을 제한할 경우 IoT Edge 디바이스는 메시지를 로컬로 유지하면서 지수 백오프를 자동으로 구현합니다. 이를 통해 트래픽이 급증할 때 솔루션에 복원력을 제공합니다.
* **오프라인 지원** - 게이트웨이 디바이스는 IoT Hub에 제공할 수 없는 메시지 및 트윈 업데이트를 저장합니다.

## <a name="transparent-gateways"></a>투명 게이트웨이

투명 게이트웨이 패턴에서 이론적으로 IoT Hub에 연결할 수 있는 디바이스는 게이트웨이 디바이스에 대신 연결할 수 있습니다. 다운스트림 디바이스는 고유한 IoT Hub ID를 갖고, MQTT 또는 AMQP 프로토콜 중 하나를 사용하여 연결합니다. 게이트웨이는 디바이스와 IoT Hub 간에 통신을 전달하기만 하면 됩니다. 디바이스 및 IoT Hub를 통해 디바이스와 상호 작용하는 사용자 모두 게이트웨이가 통신을 매개하는 것을 인식하지 못합니다. 인식 부족은 게이트웨이가 ‘투명’한 것으로 간주됨을 의미합니다.

IoT Edge 허브가 다운스트림 디바이스와 클라우드 간의 통신을 관리하는 방법에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"
![다이어그램 - 투명 게이트웨이 패턴](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

>[!NOTE]
>IoT Edge 버전 1.1 이하에서 IoT Edge 디바이스는 IoT Edge 게이트웨이의 다운스트림일 수 없습니다.
>
>IoT Edge 버전 1.2부터 투명 게이트웨이는 다운스트림 IoT Edge 디바이스의 연결을 처리할 수 있습니다. 자세한 내용은 이 문서의 [IoT Edge 1.2](?view=iotedge-2020-11&preserve-view=true) 버전으로 전환하여 확인하세요.

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 버전 1.2부터 투명 게이트웨이는 다운스트림 IoT Edge 디바이스의 연결을 처리할 수 있습니다.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>부모 및 자식 관계

IoT Edge 게이트웨이를 연결된 다운스트림 디바이스 ‘자식’의 ‘부모’로 설정하여 IoT Hub에서 투명 게이트웨이 관계를 정합니다. 

부모/자식 관계는 게이트웨이 구성에서 세 지점으로 설정됩니다.

#### <a name="cloud-identities"></a>클라우드 ID

투명 게이트웨이 시나리오의 모든 디바이스는 IoT Hub에 인증할 수 있도록 클라우드 ID가 필요합니다. 디바이스 ID를 만들거나 업데이트할 때 디바이스의 부모 또는 자식 디바이스를 설정할 수 있습니다. 이 구성은 부모 게이트웨이 디바이스에 해당 자식 디바이스에 대한 인증을 처리하는 권한을 부여합니다.

>[!NOTE]
>IoT Hub에서 부모 디바이스를 설정하는 것은 대칭 키 인증을 사용하는 다운스트림 디바이스의 선택 사항 단계였습니다. 그러나 버전 1.1.0부터 모든 다운스트림 디바이스는 부모 디바이스에 할당되어야 합니다.
>
>환경 변수 **AuthenticationMode** 를 **CloudAndScope** 값으로 설정하여 이전 동작으로 이동하도록 IoT Edge 허브를 구성할 수 있습니다.

자식 디바이스는 하나의 부모 디바이스만을 가집니다. 기본적으로 부모에는 최대 100명의 자식이 있을 수 있습니다. 부모 디바이스의 edgeHub 모듈에서 **MaxConnectedClients** 환경 변수를 설정하여 이 제한을 변경할 수 있습니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
IoT Edge 디바이스는 투명 게이트웨이 관계에서 부모 디바이스 및 자식 디바이스가 될 수 있습니다. 서로 보고하는 여러 IoT Edge 디바이스의 계층 구조를 만들 수 있습니다. 게이트웨이 계층 구조의 최상위 노드에는 최대 5세대의 자식이 있을 수 있습니다. 예를 들어 IoT Edge 디바이스에는 아래 자식으로 연결된 5개의 IoT Edge 디바이스 계층이 있을 수 있습니다. 하지만 다섯 번째 세대의 IoT Edge 디바이스에는 자식, IoT Edge 등을 포함할 수 없습니다.
::: moniker-end

#### <a name="gateway-discovery"></a>게이트웨이 검색

자식 디바이스는 로컬 네트워크에서 부모 디바이스를 찾을 수 있어야 합니다. 관련 자식 디바이스가 찾는데 사용할 FQDN(정규화된 도메인 이름) 또는 IP 주소를 **호스트 이름** 으로 사용하여 게이트웨이 디바이스를 구성합니다.

다운스트림 IoT 디바이스에서 연결 문자열의 **gatewayHostname** 매개 변수를 사용하여 부모 디바이스를 가리킵니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
다운스트림 IoT Edge 디바이스에서 구성 파일의 **parent_hostname** 매개 변수를 사용하여 부모 디바이스를 가리킵니다.
::: moniker-end

#### <a name="secure-connection"></a>보안 연결 사용

또한 부모 및 자식 디바이스는 서로에 대한 연결을 인증해야 합니다. 각 디바이스에는 자식 디바이스가 적절한 게이트웨이에 연결하는지 확인하는 데 사용하는 공유 루트 CA 인증서의 복사본이 필요합니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
여러 IoT Edge 게이트웨이가 게이트웨이 계층 구조에서 서로 연결되는 경우 계층 구조의 모든 디바이스는 단일 인증서 체인을 사용해야 합니다.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>투명 게이트웨이 뒤에 있는 디바이스 기능

IoT Edge의 메시지 파이프라인과 함께 작동하는 모든 IoT Hub 기본 형식은 투명 게이트웨이 시나리오도 지원합니다. 각 IoT Edge 게이트웨이에는 이를 통해 들어오는 메시지에 대한 저장 및 전달 기능이 있습니다.

다음 표를 사용하여 게이트웨이 뒤의 디바이스와 비교하여 디바이스에 지원되는 다양한 IoT Hub 기능을 확인하세요.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

| 기능 | IoT 디바이스 | 게이트웨이 뒤의 IoT |
| ---------- | ---------- | -------------------- |
| [D2C(디바이스-클라우드) 메시지](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![예 - IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [C2D(클라우드-디바이스) 메시지](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![예 - IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![예 - IoT 자식 C2D](./media/iot-edge-as-gateway/check-yes.png) |
| [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md) | ![예 - IoT 직접 메서드](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT 직접 메서드](./media/iot-edge-as-gateway/check-yes.png) |
| [디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 및 [모듈 쌍](../iot-hub/iot-hub-devguide-module-twins.md) | ![예 - IoT 쌍](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT 쌍](./media/iot-edge-as-gateway/check-yes.png) |
| [파일 업로드](../iot-hub/iot-hub-devguide-file-upload.md) | ![예 - IoT 파일 업로드](./media/iot-edge-as-gateway/check-yes.png) | ![아니요 - IoT 자식 파일 업로드 안 함](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| 기능 | IoT 디바이스 | 게이트웨이 뒤의 IoT | IoT Edge 디바이스 | 게이트웨이 뒤의 IoT Edge |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [D2C(디바이스-클라우드) 메시지](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![예 - IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![예 - IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [C2D(클라우드-디바이스) 메시지](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![예 - IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![예 - IoT 자식 C2D](./media/iot-edge-as-gateway/check-yes.png) | ![아니요 - IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![아니요 - IoT Edge 자식 C2D](./media/iot-edge-as-gateway/crossout-no.png) |
| [직접 메서드](../iot-hub/iot-hub-devguide-direct-methods.md) | ![예 - IoT 직접 메서드](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT 직접 메서드](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 직접 메서드 IoT Edge](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT Edge 직접 메서드](./media/iot-edge-as-gateway/check-yes.png) |
| [디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 및 [모듈 쌍](../iot-hub/iot-hub-devguide-module-twins.md) | ![예 - IoT 쌍](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT 쌍](./media/iot-edge-as-gateway/check-yes.png) | ![예 - IoT Edge 쌍](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT Edge 쌍](./media/iot-edge-as-gateway/check-yes.png) |
| [파일 업로드](../iot-hub/iot-hub-devguide-file-upload.md) | ![예 - IoT 파일 업로드](./media/iot-edge-as-gateway/check-yes.png) | ![아니요 - IoT 자식 파일 업로드 안 함](./media/iot-edge-as-gateway/crossout-no.png) | ![아니요 - IoT Edge 파일 업로드](./media/iot-edge-as-gateway/crossout-no.png) | ![아니요 - IoT Edge 자식 파일 업로드](./media/iot-edge-as-gateway/crossout-no.png) |
| 컨테이너 이미지 풀 |   |   | ![예 - IoT Edge 컨테이너 풀](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT Edge 컨테이너 풀](./media/iot-edge-as-gateway/check-yes.png) |
| Blob 업로드 |   |   | ![예 - IoT Edge blob 업로드](./media/iot-edge-as-gateway/check-yes.png) | ![예 - 자식 IoT Edge blob 업로드](./media/iot-edge-as-gateway/check-yes.png) |

**컨테이너 이미지** 를 다운로드하여 저장하고 부모 디바이스에서 자식 디바이스로 전달할 수 있습니다.

지원 번들 및 로그를 포함한 **BLOB** 은 자식 디바이스에서 부모 디바이스로 업로드할 수 있습니다.

::: moniker-end

## <a name="translation-gateways"></a>변환 게이트웨이

다운스트림 디바이스를 IoT Hub에 연결할 수 없는 경우 IoT Edge 게이트웨이가 변환기로 작동해야 합니다. 이 패턴은 일반적으로 MQTT, AMQP 또는 HTTP를 지원하지 않는 디바이스에 필요합니다. 해당 디바이스는 IoT Hub에 연결할 수 없기 때문에 일부 사전 처리 없이는 IoT Edge 허브 모듈에도 연결할 수 없습니다.

일반적으로 다운스트림 디바이스의 하드웨어 또는 프로토콜과 관련된 사용자 지정 또는 타사 모듈을 IoT Edge 게이트웨이에 배포해야 합니다. 변환 모듈은 들어오는 메시지를 가져와서 IoT Hub에서 이해할 수 있는 형식으로 변환합니다.

변환 게이트웨이에는 ‘프로토콜 변환’ 및 ‘ID 변환’과 같은 두 가지 패턴이 있습니다. 

![다이어그램 - 변환 게이트웨이 패턴](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>프로토콜 변환

프로토콜 변환 게이트웨이 패턴에서 IoT Edge 게이트웨이에만 IoT Hub ID가 있습니다. 변환 모듈은 다운스트림 디바이스에서 메시지를 수신하여 지원되는 프로토콜로 변환한 다음 다운스트림 디바이스 대신 IoT Edge 디바이스에서 메시지를 보냅니다. 모든 정보는 하나의 디바이스인 게이트웨이에서 오는 것처럼 보입니다. 클라우드 애플리케이션이 디바이스별로 데이터를 분석하려는 경우 다운스트림 디바이스는 메시지에 추가 식별 정보를 포함해야 합니다. 또한 쌍 및 직접 메서드 같은 IoT Hub 기본 형식은 게이트웨이 디바이스만 지원하고 다운스트림 디바이스에는 지원되지 않습니다. 이 패턴의 게이트웨이는 다운스트림 디바이스의 ID를 모호하게 하므로 투명 게이트웨이와 달리 ‘불투명’한 것으로 간주합니다.

프로토콜 변환은 리소스가 제한된 디바이스를 지원합니다. 많은 기존 디바이스가 비즈니스 통찰력을 얻을 수 있는 데이터를 생산하지만, 클라우드 연결을 염두에 두고 개발되지는 않았습니다. 불투명 게이트웨이는 IoT 솔루션에서 이 데이터를 잠금 해제하고 사용할 수 있습니다.

### <a name="identity-translation"></a>ID 변환

ID 변환 게이트웨이 패턴은 프로토콜 변환을 기반으로 하지만, IoT Edge 게이트웨이는 다운스트림 디바이스를 대신하여 IoT Hub 디바이스 ID를 제공합니다. 변환 모듈은 다운스트림 디바이스에서 사용하는 프로토콜을 이해하고, ID를 부여하고, 메시지를 IoT Hub 기본 형식으로 변환하는 일을 담당합니다. 다운스트림 디바이스는 IoT Hub에서 쌍 및 메서드를 포함한 고급 디바이스로 표시됩니다. 사용자는 IoT Hub의 디바이스와 상호 작용할 수 있고 중간 게이트웨이 디바이스를 인식하지 못합니다.

ID 변환은 프로토콜 변환의 이점을 제공할 뿐 아니라 클라우드에서 다운스트림 디바이스를 완벽하게 관리할 수 있습니다. IoT 솔루션의 모든 디바이스는 사용하는 프로토콜에 관계없이 IoT Hub에 표시됩니다.

### <a name="device-capabilities-behind-translation-gateways"></a>변환 게이트웨이 뒤의 디바이스 기능

다음 표에서는 두 변환 게이트웨이 패턴의 다운스트림 디바이스로 IoT Hub 기능이 확장되는 방법에 대해 설명합니다.

| 기능 | 프로토콜 변환 | ID 변환 |
| ---------- | -------------------- | -------------------- |
| IoT Hub ID 레지스트리에 저장된 ID | 게이트웨이 디바이스의 ID만 | 연결된 모든 디바이스의 ID |
| 디바이스 쌍 | 게이트웨이에만 디바이스 및 모듈 쌍이 있음 | 연결된 각 디바이스에 자체 디바이스 쌍이 있음 |
| 직접 방식 및 클라우드-디바이스 메시지 | 클라우드가 게이트웨이 디바이스만 처리할 수 있음 | 클라우드가 연결된 각 디바이스를 개별적으로 처리할 수 있음 |
| [IoT Hub 제한 및 할당량](../iot-hub/iot-hub-devguide-quotas-throttling.md) | 게이트웨이 디바이스에 적용 | 각 디바이스에 적용 |

프로토콜 변환 패턴을 사용하는 경우 해당 게이트웨이 통해 연결되는 모든 디바이스는 최대 50개의 메시지를 포함할 수 있는 동일한 클라우드-디바이스 큐를 공유합니다. 프로토콜 변환 패턴은 각 필드 게이트웨이를 통해 연결되는 디바이스 수가 매우 적고 해당 클라우드-디바이스 트래픽이 낮을 경우에만 사용해야 합니다.

IoT Edge 런타임은 프로토콜 또는 ID 변환 기능을 포함하지 않습니다. 해당 패턴에는 사용되는 하드웨어 및 프로토콜과 관련된 사용자 지정 또는 타사 모듈이 필요합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)에는 선택할 수 있는 몇 가지 프로토콜 변환 모듈이 포함되어 있습니다. ID 변환 패턴을 사용하는 샘플의 경우 [Azure IoT Edge LoRaWAN 시작 키트](https://github.com/Azure/iotedge-lorawan-starterkit)를 참조하세요.

## <a name="next-steps"></a>다음 단계

투명 게이트웨이를 설정하기 위한 세 가지 단계를 알아봅니다.

* [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)
* [Azure IoT Hub에 다운스트림 디바이스 인증](how-to-authenticate-downstream-device.md)
* [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)
