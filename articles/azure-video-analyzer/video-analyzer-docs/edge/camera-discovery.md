---
title: 로컬 서브넷에서 ONVIF 지원 카메라 검색
description: 이 방법에서는 Video Analyzer 에지 모듈을 사용하여 로컬 서브넷에서 ONVIF 지원 카메라를 검색하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8e0629294ba5d19315da096072fa804824649acc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564027"
---
# <a name="discovering-onvif-capable-cameras-in-the-local-subnet"></a>로컬 서브넷에서 ONVIF 지원 카메라 검색

[!INCLUDE [header](includes/edge-env.md)]

이 가이드 방법은 Azure Video Analyzer 에지 모듈을 사용하여 IoT Edge 디바이스와 동일한 서브넷에서 ONVIF 규격 카메라를 검색하는 방법을 안내합니다. ONVIF(Open Network Video Interface Forum)는 감시 카메라와 같은 개별 IP 기반 물리적 디바이스가 추가 네트워크 디바이스 및 소프트웨어와 통신할 수 있는 개방형 표준입니다. ONVIF에 대한 자세한 내용은 [ONVIF](https://www.onvif.org/about/mission/) 웹 사이트를 방문하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음을 수행해야 합니다.

- 활성 Azure 구독.
- [IoT용 Azure CLI 확장](https://github.com/Azure/azure-iot-cli-extension#installation) 설치
- 다음 중 하나를 수행합니다.
  - [빠른 시작: Azure Video Analyzer 시작](get-started-detect-motion-emit-events.md)
  - [빠른 시작: Azure Portal에서 Azure Video Analyzer 시작](get-started-detect-motion-emit-events-portal.md)
- Video Analyzer 에지 모듈 버전 1.1 이상 버전을 IoT Edge 디바이스에 배포
- [Hyper-V](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/mt169373(v=ws.11)) Virtual Machine 또는 [EFLOW를](../../../iot-edge/how-to-install-iot-edge-on-windows.md?view=iotedge-2018-06&tabs=windowsadmincenter&preserve-view=true) 사용하는 경우 onvifDeviceDiscover 직접 메서드 호출을 수행하려면 [외부 스위치가](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) 필요합니다. 이 요구 사항은 ONVIF 디바이스 검색에 사용되는 [멀티캐스트](https://en.wikipedia.org/wiki/Multicast) 호출 때문입니다.

Video Analyzer 에지 모듈의 ONVIF 기능에는 [ONVIF](#enable-onvif-discovery-feature)검색 기능 사용에서 설명한 대로 특정 컨테이너 만들기 옵션이 필요합니다. 이 ONVIF 검색 기능을 사용하려면 포트 3702를 사용할 수 있어야 합니다.
  
> [!NOTE]
> Video Analyzer 에지 모듈 버전 1.1의 새 배포가 있는 경우 [직접 메서드 호출 사용](#use-direct-method-calls)섹션으로 건너뛸 수 있습니다. 그렇지 않으면 아래 섹션에 따라 기존 Video Analyzer 에지 모듈을 업그레이드하여 ONVIF 검색 기능을 사용하도록 설정합니다.  

## <a name="check-the-version"></a>버전 확인

명령 프롬프트에서 다음 명령을 실행합니다.

```CLI
az account set --subscription <YOUR_SUBSCRIPTION_NAME>
az iot hub module-twin show -m <VIDEO_ANALYZER_IOT_EDGE_MODULE_NAME> -n <IOT_HUB_NAME> -d <IOT_EDGE_DEVICE_NAME> --query 'properties.reported.ProductInfo' -o tsv
```

위 명령의 결과가 **Azure Video Analyzer:1.0.1인** 경우 다음 단계를 실행하여 Video Analyzer 에지 모듈을 버전 1.1로 업데이트합니다.

1. Azure Portal `<IOT_HUB_NAME>` Video Analyzer 에지 모듈 배포에 사용되는 IoT Hub(위)로 이동합니다.
1. 자동 **장치 관리** 아래에서 IoT Edge 클릭하고 `<IOT_EDGE_DEVICE_NAME>` Video Analyzer 에지 모듈이 배포된 IoT Edge 디바이스(위)를 선택합니다.
1. **모듈 설정을** 클릭하고 **검토 + 만들기를** 클릭합니다.
1. **만들기를** 클릭합니다.
1. 몇 분 후에 Video Analyzer 에지 모듈이 업데이트되고 위의 명령을 다시 실행하여 확인할 수 있습니다.

### <a name="enable-onvif-discovery-feature"></a>ONVIF 검색 기능 사용

다음으로, Video Analyzer 에지 모듈에 대한 컨테이너 만들기 옵션에 추가 옵션을 추가합니다. 이렇게 하면 호스트 네트워크를 통해 통신하여 동일한 서브넷에서 ONVIF 지원 카메라를 검색할 수 있습니다.

1. In the Azure portal navigate to the IoT Hub
1. 자동 **장치 관리** 아래에서 IoT Edge 클릭하고 Video Analyzer 에지 모듈이 배포된 IoT Edge 디바이스를 선택합니다.
1. **모듈 설정을** 클릭하고 Video Analyzer 에지 모듈을 선택합니다.
1. **컨테이너 만들기 옵션** 탭으로 이동하여 다음 JSON 항목을 해당 섹션에 추가합니다.

    - JSON 항목 상자에서 첫 번째 항목 다음에 `{` 다음을 입력합니다.
  
        ```JSON
        "NetworkingConfig": {
        "EndpointsConfig": {
            "host": {}
            }
        },
        ```
    - `HostConfig`JSON 개체에서 다음을 수행합니다.

        ```JSON
        "NetworkMode": "host",
        ```
1. 맨 아래에서 **업데이트를** 클릭합니다.
1. **검토 + 만들기** 를 클릭합니다.
1. **생성**

## <a name="use-direct-method-calls"></a>직접 메서드 호출 사용

Video Analyzer 에지 모듈은 동일한 서브넷에서 네트워크 연결 카메라를 ONVIF 검색하기 위한 직접 메서드 호출을 제공합니다. 다음 단계는 `onvifDeviceDiscover` 및 아래 섹션 모두에 `onvifDeviceGet` 적용됩니다.

1. In the Azure portal navigate to the IoT Hub
1. 자동 **장치 관리** 아래에서 IoT Edge 클릭하고 Video Analyzer 에지 모듈이 배포된 IoT Edge 디바이스를 선택합니다.
1. Video Analyzer 에지 모듈을 클릭하고 맨 위에 있는 메뉴 모음에서 **직접 메서드를** 클릭합니다.

### <a name="onvifdevicediscover"></a>onvifDeviceDiscover

이 직접 메서드는 Video Analyzer 에지 모듈과 동일한 서브넷에 있는 검색 가능한 모든 ONVIF 디바이스를 나열합니다.


1. 메서드 이름에 다음을 입력합니다.

    ```JSON
    onvifDeviceDiscover
    ```
1. 페이로드에 다음을 입력합니다.

    ```JSON
    {
        "@apiVersion":"1.1",
        "discoveryDuration":"PT8S"
    }
    ```

   검색 기간은 Video Analyzer 에지 모듈이 ONVIF 검색 가능한 디바이스에서 응답을 받기 위해 대기하는 시간입니다. 이 값을 조정하려면 대규모 환경에서 필요할 수 있습니다.

1. 몇 초 내에 다음과 같은 가 표시됩니다. `result`

    ```JSON
    {
        "status": 200,
        "payload": {
            "value": [
                {
                    "serviceIdentifier": "{urn:uuid}",
                    "remoteIPAddress": "{IP_ADDRESS}",
                    "scopes": [
                        "onvif://www.onvif.org/type/Network_Video_Transmitter",
                        "onvif://www.onvif.org/name/{CAMERA_MANUFACTURE}",
                        "onvif://www.onvif.org/location/",
                        "onvif://www.onvif.org/hardware/{CAMERA_MODEL}",
                        "onvif://www.onvif.org/Profile/Streaming",
                        "onvif://www.onvif.org/Profile/G",
                        "onvif://www.onvif.org/Profile/T"
                    ],
                    "endpoints": [
                        "http://<IP_ADDRESS>/onvif/device_service",
                        "https://<IP_ADDRESS>/onvif/device_service"
                    ],
                    
                }
            ]
        }
    }
    ```
    반환 상태가 200이면 직접 메서드 호출이 성공적으로 처리되었음을 나타냅니다.

### <a name="onvifdeviceget"></a>onvifDeviceGet

onvifDeviceGet 호출은 보안되지 않은 엔드포인트와 TLS 사용 엔드포인트를 모두 지원합니다. 이 직접 메서드 호출은 특정 ONVIF 디바이스에 대한 자세한 정보를 검색합니다.

# <a name="unsecuredendpoint"></a>[UnsecuredEndpoint](#tab/unsecuredendpoint)

> [!NOTE]
> ONVIF 지원 카메라의 보안되지 않은 엔드포인트에 대한 onvifDeviceGet 호출이 수행되면 Video Edge 모듈 ID 쌍 설정을 로 설정해야 `"allowUnsecuredEndpoints"` `true` 합니다. 자세한 내용은 모듈 쌍 속성 문서를 [참조하세요.](./module-twin-configuration-schema.md)

1. 메서드 이름에 다음을 입력합니다.

    ```JSON
    onvifDeviceGet
    ```
1. 페이로드에 다음을 입력합니다.

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "http://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }
    ```

# <a name="tlsendpoint"></a>[TlsEndpoint](#tab/tlsendpoint)

1. 메서드 이름에 다음을 입력합니다.

    ```JSON
    onvifDeviceGet
    ```
1. 페이로드에 다음을 입력합니다.

    ```JSON
    {
        "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.TlsEndpoint",
            "credentials": {
                "username": "<USER_NAME>",
                "password": "<PASSWORD>",
                "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials"
            },
            "url": "https://<IP_ADDRESS>/onvif/device_service"
        },
        "@apiVersion": "1.1"
    }

---

In the above payload:

- `url` is the IP address of the ONVIF device from which you wish to get additional details
- `username` is the user name that is used to authenticate with the device, which has permissions to the ONVIF features. Some devices require specific accounts to be created for this purpose.
- `password` is the password for that user name

    Within a few seconds you should see a result such as:

    ```JSON
    {
        "status": 200,
        "payload": {
            "hostname": {
                "fromDhcp": true,
                "hostname": "{NAME_OF_THE_ONVIF_DEVICE}"
            },
            "systemDateTime": {
                "type": "ntp",
                "time": "2021-09-28T03:05:05.000Z",
                "timeZone": "GMT"
            },
            "dns": {
                "fromDhcp": true,
                "ipv4Address": [
                    "{IP_ADDRESS}"
                ],
                "ipv6Address": []
            },
            "mediaProfiles": [
                {
                    "name": "{Profile1}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 3840,
                            "height": 2160
                        },
                        "rateControl": {
                            "bitRateLimit": 15600,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                },
                {
                    "name": "{Profile2}",
                    "mediaUri": {
                        "uri": "{RTSP_URI}"
                    },
                    "videoEncoderConfiguration": {
                        "encoding": "h264",
                        "resolution": {
                            "width": 1280,
                            "height": 720
                        },
                        "rateControl": {
                            "bitRateLimit": 1900,
                            "encodingInterval": 1,
                            "frameRateLimit": 30,
                            "guaranteedFrameRate": false
                        },
                        "quality": 50,
                        "h264": {
                            "govLength": 255,
                            "profile": "main"
                        }
                    }
                }
            ]
        }
    }
    ```

### <a name="return-status-of-onvifdeviceget"></a>onvifDeviceGet의 반환 상태

| 상태 | 코드      | 의미/솔루션                                                                                                                                                                                                            |
| ------ | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200    | Success   | 직접 메서드 호출이 성공적으로 완료되었습니다.                                                                                                                                                                                |
| 400    | 잘못된 요청  | 요청 형식이 잘못된 경우   |
| 403    | 사용할 수 없음 | 직접 메서드 호출이 인증 실패로 인해 ONVIF 디바이스에서 요청된 정보를 검색할 수 없습니다. 메시지 본문의 사용자 이름 및/또는 암호가 올바른지 확인합니다. |
| 500 | 오류 | 알 수 없는 오류가 발생했습니다.  |
| 502    | 잘못된 게이트웨이 | 업스트림 서비스에서 잘못된 응답을 받았습니다.  |
| 504    | 제한 시간   | 직접 메서드 호출은 ONVIF 디바이스의 응답을 받기 전에 만료되었습니다.  |

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 몇 가지 문제 해결 단계를 설명합니다.

- IoT Edge 모듈 직접 메서드 블레이드의 Azure Portal "오류로 인해 작업이 성공적으로 완료되지 못했습니다. 상태 코드 504로 요청이 실패했습니다." (아래 이미지 참조):

    :::image type="content" source="./media/camera-discovery/five-zero-four-error.png" alt-text="504 오류를 보여 주는 스크린샷.":::

    위의 직접 메서드 호출에서 에 대한 `"discoveryDuration":"PT8S"` 설정이 또는 값보다 짧은지 `Connection Timeout` `Method Timeout` 확인합니다.

    :::image type="content" source="./media/camera-discovery/five-zero-four-error-fix.png" alt-text="504 오류 수정을 보여 주는 스크린샷":::

- 직접 메서드 `Results` 필드에 "{"status":200,"payload":{"value":[]}}가 표시되면 검색 기간이 더 길어질 수 있습니다.

    :::image type="content" source="./media/camera-discovery/result-status-two-hundred-null.png" alt-text="메시지 반환이 직접 메서드 'Results' 필드에 표시됩니다.":::

    의 시간 값(x)을 `"discoveryDuration":"PTxS"` 더 큰 수로 조정합니다. 또한 그에 따라 `Connection Timeout` 및/또는 `Method Timeout` 값을 조정합니다.
- `onvifDeviceGet`직접 메서드 호출은 265 인코드된 미디어 스트림의 미디어 프로필을 표시 하지 않습니다.
- ONVIF 장치에 연결 하는 데 사용 된 사용자 계정에 ONVIF 카메라 기능에 대 한 권한이 없는 경우 403의 반환 상태가 반환 될 수 있습니다. 일부 ONVIF 규격 카메라의 경우 onvif 장치 정보를 검색 하기 위해 사용자를 ONVIF 보안 설정에 추가 해야 합니다.
- 현재 Video Analyzer edge 모듈은 멀티 캐스트를 통해 동일한 서브넷에 연결할 수 있는 최대 200 ONVIF 사용 카메라를 반환 합니다. 또한 포트 3702을 사용할 수 있어야 합니다.
- TLS 끝점을 사용 하 여 onvifDeviceGet을 호출 하는 경우 ONVIF 장치 이름 및 IP 주소를에 지 장치의 호스트 (/etc/hosts) 파일에 추가 해야 합니다.  자체 서명 된 인증서가 TLS 암호화에 사용 되는 경우 인증서를 IoT Edge 장치의 인증서 저장소에 추가 해야 합니다. 
- EFLOW 가상 머신에서 onvifDeviceDiscover 직접 메서드 호출의 경우 다음 단계를 수행 해야 합니다.
  - EFLOW 가상 머신으로 커넥트 하 고 다음을 실행 합니다.`sudo iptables -I INPUT -p udp -j ACCEPT`

## <a name="next-steps"></a>다음 단계

- 검색 된 ONVIF 장치를 사용 하 여 [라이브 비디오 분석을 위한 빠른](analyze-live-video-use-your-model-http.md) 시작을 사용해 보세요.
