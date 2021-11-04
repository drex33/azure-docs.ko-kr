---
title: Azure Video Analyzer로 장치 커넥트
description: 이 문서에서는 Azure 비디오 분석기에 장치를 연결 하는 방법을 설명 합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 605c7641c5960bf5643a0ca26e29339d5cd21db0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563248"
---
# <a name="connect-devices-to-azure-video-analyzer"></a>Azure Video Analyzer로 장치 커넥트

[!INCLUDE [header](includes/cloud-env.md)]

장치에서 비디오를 캡처 및 기록 하기 위해 Azure Video Analyzer 서비스는 [RTSP](../terminology.md#rtsp) 연결을 설정 해야 합니다. 장치가 방화벽 뒤에 있는 경우 이러한 연결이 차단 되며, Azure에서 인바운드 연결을 허용 하는 규칙을 만드는 것이 항상 가능한 것은 아닙니다. 이러한 장치를 지원 하기 위해 [Azure IoT 플러그 앤 플레이](../../../iot-develop/overview-iot-plug-and-play.md) 장치 구현을 빌드 및 설치 하 여 비디오 분석기에서 IoT Hub를 통해 전송 된 명령을 수신 하 고 서비스에 대 한 보안 websocket 터널을 열 수 있습니다. 이러한 터널이 설정 되 면 Video Analyzer는 RTSP 서버에 연결할 수 있습니다.

## <a name="overview"></a>개요 

이 문서에서는 비디오 분석기를 사용 하 여 장치에서 비디오를 캡처 및 기록 하는 데 사용할 수 있는 Azure IoT PnP 장치 구현을 빌드하기 위한 개략적인 개념을 제공 합니다. 

응용 프로그램은 다음을 수행 해야 합니다. 

1. IoT 장치로 실행 
1. 특정 명령 ()을 사용 하 여 [IoT PnP](../../../iot-develop/overview-iot-plug-and-play.md) 인터페이스를 구현 합니다. `tunnelOpen` 
1. 이러한 명령이 수신 될 때: 
   * 받은 인수 유효성 검사 
   * 제공 된 토큰을 사용 하 여 제공 된 URL에 대 한 보안 websocket 연결을 엽니다.
   * Websocket 바이트를 카메라의 RTSP 서버 TCP 연결로 전달

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-devices/connect-devices.svg" alt-text="클라우드로 장치 커넥트":::

## <a name="run-as-an-iot-device"></a>IoT 장치로 실행 

Video Analyzer 응용 프로그램은 Video Analyzer PnP 플러그인으로 배포 됩니다. 이렇게 하려면 [Azure IoT 장치 sdk](../../../iot-develop/libraries-sdks.md#device-sdks) 중 하나를 사용 하 여 IoT PnP 장치 구현을 빌드해야 합니다. IoT 장치를 IoT Hub에 등록 하 여 IoT Hub 장치 ID 및 장치 연결 문자열을 가져옵니다.

### <a name="iot-device-clientconfiguration"></a>IoT 장치 클라이언트 구성

* `“dtmi:azure:videoanalyzer:WebSocketTunneling;1”`PnP 쿼리를 지원 하도록 OPTION_MODEL_ID 설정  
* 장치가 Azure IoT 허브에 연결 하기 위해 mqtt 또는 websocket을 통한 mqtt 프로토콜을 사용 하 고 있는지 확인 합니다. 
    * IoT 장치에 구성 된 경우 HTTPS 프록시를 통해 IoT Hub 커넥트  
*  `tunnelOpen`직접 메서드에 대 한 콜백 등록   

## <a name="implement-the-iot-pnp-interface-for-video-analyzer"></a>비디오 분석기에 대 한 IoT PnP 인터페이스 구현

다음의 [DTDL (디지털 Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 모델은 Video Analyzer에 연결할 수 있는 장치를 설명 합니다.

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

IoT 장치는 직접 메서드를 등록 합니다  `tunnelOpen` . 여기서 요청 본문에는 `remoteEndpoint` `remoteAuthorizationToken` `localPort` 위에 표시 된 것 처럼, 및 매개 변수가 포함 됩니다.

## <a name="implement-the-direct-method-tunnelopen"></a>직접 메서드 구현 `tunnelOpen`
`tunnelOpen`Video Analyzer 서비스에서 직접 메서드를 호출 하는 경우 응용 프로그램은 다음을 수행 해야 합니다.

1. 장치의 사용 가능한 RTSP 포트를 가져옵니다.
1. `localPort`직접 메서드 호출에 지정 된 값을 사용 가능한 포트와 비교 합니다.
   * 일치 하는 항목이 없는 경우 **Badrequest** 반환 (아래의 오류 응답 섹션 참조)
1. "(카메라 IP 또는 호스트 이름)"에 대 한 TCP 연결을 `localPort` 엽니다.
   * 연결에 실패 하면 **Badrequest** 반환
   * 참고: 호스트 이름은 일반적으로 **localhost** 입니다.
1. 에 대 한 웹 소켓 연결 열기 `remoteEndpoint` (장치에 구성 된 경우 프록시를 통해)
   * HTTP "Authorization" 헤더를 "전달자 (remoteAuthorizationToken)"로 설정 합니다.
   * "Pnp 장치" 값을 사용 하 여 "TunnelConnectionSource" 헤더를 설정 합니다.
   * User-Agent를 구현을 식별 하는 데 도움이 되는 적절 한 값으로 설정 합니다. 
      * 예를 들어 장치의 CPU, OS, 모델/제조업체의 아키텍처를 캡처할 수 있습니다.
   * 웹 소켓 연결에 성공 하면 200을 반환 하 고 그렇지 않으면 적절 한 오류 코드를 반환 합니다.
1. 반환 응답 (차단 안 함)
1. IoT PnP 장치 구현이 websocket 및 RTSP 서버 TCP 연결 간에 TCP 데이터 양방향을 보내기 시작 합니다.

Video Analyzer 서비스는 `tunnelOpen` 실패 시 요청을 다시 시도 하므로 응용 프로그램에서 다시 시도는 필요 하지 않습니다.

### <a name="error-responses"></a>오류 응답
`tunnelOpen`요청이 실패 하면 응답 본문은 다음과 같아야 합니다.

```
{
    "code": "<errorCode>", // Use HTTP status error codes
    "target": "<uri>", // The target URI experiencing the issue
    "message": "<Error message>",  // Short error message describing issue. Do not include end user identifiable information.
}
```
이러한 오류 응답의 예는 다음과 같습니다.

* 로컬 포트를 RTSP 또는 RTSPS 포트 {"code": "400", "target": "(카메라 IP 또는 호스트 이름): {localPort}", "message": "로컬 포트를 사용할 수 없음"}로 사용할 수 없습니다.
* 제한 시간/RTSP 끝점 {"code": "400", "target": "(카메라 IP 또는 호스트 이름): {localPort}", "message": "RTSP 끝점에 연결할 수 없습니다."}에 연결할 수 없습니다.
*   웹 소켓 연결 시도 {"code": "{WebSocket 응답 코드}", "대상": "{remoteEndpoint}", "메시지": "{웹 소켓 응답 오류 메시지}"}의 시간 제한/오류 응답


## <a name="ingestion-to-video-analyzer"></a>비디오 분석기에 수집
비디오 분석기에 비디오를 캡처 및 기록 하기 위해 터널링을 사용 하는 파이프라인 토폴로지를 만들어야 합니다. 해당 토폴로지에서 라이브 파이프라인을 만들고 활성화 해야 합니다. [이 프로세스에 대 한 지침은 여기에 설명 되어 있습니다.](use-remote-device-adapter.md#create-pipeline-topology-in-the-video-analyzer-service)

 
## <a name="example-implementation"></a>구현 예
videoanalyzerhelp@microsoft.com장치에서 응용 프로그램을 구현 하 여 비디오 분석기에 연결 하려는 경우에 문의 하세요.

## <a name="see-also"></a>참고 항목 

[IoT 플러그 앤 플레이란?](../../../iot-develop/overview-iot-plug-and-play.md)
