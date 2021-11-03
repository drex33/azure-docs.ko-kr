---
title: Azure Video Analyzer에 디바이스 커넥트
description: 이 문서에서는 Azure Video Analyzer에 디바이스를 연결하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 72a83593134b52c652d8d31bb441ecc083d88fea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053595"
---
# <a name="connect-devices-to-azure-video-analyzer"></a>Azure Video Analyzer에 디바이스 커넥트

[!INCLUDE [header](includes/cloud-env.md)]

디바이스에서 비디오를 캡처하고 녹화하려면 Azure Video Analyzer 서비스가 [디바이스에 대한 RTSP](../terminology.md#rtsp) 연결을 설정해야 합니다. 디바이스가 방화벽 뒤에 있는 경우 이러한 연결이 차단되며 Azure에서 인바운드 연결을 허용하는 규칙을 항상 만들 수 있는 것은 아닙니다. 이러한 디바이스를 지원하려면 Video Analyzer에서 IoT Hub 통해 전송된 명령을 수신 대기하는 [Azure IoT 플러그 앤 플레이](../../../iot-develop/overview-iot-plug-and-play.md) 디바이스 구현을 빌드하고 설치한 다음, 서비스에 대한 보안 websocket 터널을 엽니다. 이러한 터널이 설정되면 Video Analyzer는 RTSP 서버에 연결할 수 있습니다.

## <a name="overview"></a>개요 

이 문서에서는 Video Analyzer가 디바이스에서 비디오를 캡처하고 녹화할 수 있는 Azure IoT PnP 디바이스 구현을 빌드하는 방법을 개략적인 개념으로 제공합니다. 

애플리케이션은 다음을 수행해야 합니다. 

1. IoT 디바이스로 실행 
1. 특정 명령()을 통해 [IoT PnP](../../../iot-develop/overview-iot-plug-and-play.md) 인터페이스 구현 `tunnelOpen` 
1. 이러한 명령을 받으면 다음을 수행합니다. 
   * 받은 인수의 유효성 검사 
   * 제공된 토큰을 사용하여 제공된 URL에 대한 보안 websocket 연결을 엽니다.
   * 카메라의 RTSP 서버 TCP 연결에 websocket 바이트를 전달합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-devices/connect-devices.svg" alt-text="클라우드에 디바이스 커넥트":::

## <a name="run-as-an-iot-device"></a>IoT 디바이스로 실행 

Video Analyzer 애플리케이션은 Video Analyzer PnP 플러그 인으로 배포됩니다. 이렇게 하려면 Azure IoT [디바이스 SDK](../../../iot-develop/libraries-sdks.md#device-sdks) 중 하나를 사용하여 IoT PnP 디바이스 구현을 빌드해야 합니다. IoT Hub IoT 디바이스를 등록하여 IoT Hub 디바이스 ID 및 디바이스 연결 문자열을 가져옵니다.

### <a name="iot-device-clientconfiguration"></a>IoT 디바이스 클라이언트 구성

* `“dtmi:azure:videoanalyzer:WebSocketTunneling;1”`PnP 쿼리를 지원하려면 OPTION_MODEL_ID 로 설정합니다.  
* 디바이스가 WebSockets 프로토콜을 통해 MQTT 또는 MQTT를 사용하여 Azure IoT Hub에 연결하는지 확인합니다. 
    * IoT 디바이스에 구성된 경우 HTTPS 프록시를 통해 IoT Hub 커넥트  
*  `tunnelOpen`직접 메서드에 대한 콜백 등록   

## <a name="implement-the-iot-pnp-interface-for-video-analyzer"></a>Video Analyzer용 IoT PnP 인터페이스 구현

다음 [DTDL(Digital Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 모델은 Video Analyzer에 연결할 수 있는 디바이스에 대해 설명합니다.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:azure:videoanalyzer:WebSocketTunneling;1",
  "@type": "Interface",
  "displayName": "Azure Video Analyzer Web Socket Tunneling",
  "description": "This interface enables media publishing to Azure Video Analyzer service from a RTSP compatible device which is located behind a firewall or NAT device.",
  "contents": [
    {
      "@type": "Command",
      "displayName": "Tunnel Open",
      "name": "tunnelOpen",
      "request": {
        "@type": "CommandPayload",
        "displayName": "Parameters",
        "name": "parameters",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "displayName": "Remote Endpoint",
              "description": "The remote endpoint for the web socket tunnel.",
              "name": "remoteEndpoint",
              "schema": "string"
            },
            {
              "displayName": "Remote Authorization Token",
              "description": "The bearer token for the web socket authentication.",
              "name": "remoteAuthorizationToken",
              "schema": "string"
            },
            {
              "displayName": "Local Port",
              "description": "The local port where web socket data should be tunneled to.",
              "name": "localPort",
              "schema": "integer"
            }
          ]
        }
      }
    }
  ]
}
```

IoT 디바이스는 직접 메서드 를 등록합니다.  `tunnelOpen` 여기서 요청 본문에는 위에 표시된 것처럼 , 및 매개 변수가 `remoteEndpoint` `remoteAuthorizationToken` `localPort` 있습니다.

## <a name="implement-the-direct-method-tunnelopen"></a>직접 메서드 구현 `tunnelOpen`
Video `tunnelOpen` Analyzer 서비스에서 직접 메서드를 호출하는 경우 애플리케이션은 다음을 수행해야 합니다.

1. 디바이스의 사용 가능한 RTSP 포트를 얻습니다.
1. 직접 `localPort` 메서드 호출에 지정된 값을 사용 가능한 포트와 비교
   * 일치하는 내용이 없으면 **BadRequest를** 반환합니다(아래 오류 응답 섹션 참조).
1. "(카메라 IP 또는 호스트 이름): "에 대한 TCP 연결을 `localPort` 엽니다. "
   * 연결이 실패하면 **BadRequest를** 반환합니다.
   * 참고: 호스트 이름은 일반적으로 **localhost입니다.**
1. 에 대한 웹 소켓 연결을 `remoteEndpoint` 엽니다(디바이스에 구성된 경우 프록시를 통해).
   * HTTP "Authorization" 헤더를 "Bearer(remoteAuthorizationToken)"로 설정합니다.
   * "PnpDevice" 값으로 "TunnelConnectionSource" 헤더 설정
   * User-Agent 구현을 식별하는 데 도움이 되는 적절한 값으로 설정합니다. 
      * 예를 들어 CPU, OS, 디바이스의 모델/메이크 아키텍처를 캡처할 수 있습니다.
   * 웹 소켓 연결에 성공하면 200 OK를 반환하고, 그렇지 않으면 적절한 오류 코드를 반환합니다.
1. 반환 응답(차단 안 함)
1. IoT PnP 디바이스 구현은 websocket과 RTSP 서버 TCP 연결 간에 양방향으로 TCP 데이터 전송을 시작합니다.

Video Analyzer 서비스는 실패 시 요청을 다시 `tunnelOpen` 시도하므로 애플리케이션에서 재시도는 필요하지 않습니다.

### <a name="error-responses"></a>오류 응답
`tunnelOpen`요청이 실패하면 응답 본문은 다음과 이어야 합니다.

```
{
    "code": "<errorCode>", // Use HTTP status error codes
    "target": "<uri>", // The target URI experiencing the issue
    "message": "<Error message>",  // Short error message describing issue. Do not include end user identifiable information.
}
```
이러한 오류 응답의 예는 다음과 같습니다.

* 로컬 포트는 RTSP 또는 RTSPS 포트로 사용할 수 없습니다 { "code": "400", "target": "(카메라 IP 또는 호스트 이름):{localPort}", "message": "로컬 포트를 사용할 수 없습니다"}
* 시간 제한/RTSP 엔드포인트에 연결할 수 없음 { "code": "400", "target": "(카메라 IP 또는 호스트 이름):{localPort}", "message":"RTSP 엔드포인트에 연결할 수 없습니다."}
*   웹 소켓 연결 시도의 시간 제한/오류 응답 { "code": "{WebSocket 응답 코드}", "target": "{remoteEndpoint}", "message": "{Web socket response error message}"}


## <a name="ingestion-to-video-analyzer"></a>Video Analyzer로의 스트리밍
Video Analyzer에 비디오를 캡처하고 녹화하려면 터널링을 사용하도록 설정된 파이프라인 토폴로지 를 만들어야 합니다. 해당 토폴로지에서 라이브 파이프라인을 만들고 활성화해야 합니다. [이 프로세스에 대한 지침은 여기에 설명되어 있습니다.](use-remote-device-adapter.md#create-pipeline-topology-in-the-video-analyzer-service)

 
## <a name="example-implementation"></a>구현 예제
videoanalyzerhelp@microsoft.com디바이스에서 애플리케이션을 구현하여 Video Analyzer에 연결하려면 에 문의하세요.

## <a name="see-also"></a>참고 항목 

[IoT 플러그 앤 플레이란?](../../../iot-develop/overview-iot-plug-and-play.md)
