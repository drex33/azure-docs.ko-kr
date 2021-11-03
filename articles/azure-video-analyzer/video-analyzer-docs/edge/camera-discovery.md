---
title: 로컬 서브넷에서 ONVIF 지원 카메라 검색
description: 이 방법에서는 Video Analyzer edge 모듈을 사용 하 여 로컬 서브넷에서 ONVIF 지원 카메라를 검색 하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 04d46daedd88ceef8b91ce4f0135d80b2d1c9045
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102853"
---
# <a name="discovering-onvif-capable-cameras-in-the-local-subnet"></a>로컬 서브넷에서 ONVIF 지원 카메라 검색

[!INCLUDE [header](includes/edge-env.md)]

이 방법 가이드에서는 Azure Video Analyzer edge 모듈을 사용 하 여 IoT Edge 장치와 동일한 서브넷에서 ONVIF 규격 카메라를 검색 하는 방법을 안내 합니다. ONVIF (open Network Video Interface 포럼)는 감시 카메라와 같은 개별 IP 기반 물리적 장치가 네트워크로 연결 된 장치 및 소프트웨어와 통신할 수 있는 오픈 표준입니다. ONVIF에 대 한 자세한 내용은 [onvif](https://www.onvif.org/about/mission/) 웹 사이트를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음을 수행해야 합니다.

- 활성 Azure 구독.
- [IoT 용 Azure CLI 확장](https://github.com/Azure/azure-iot-cli-extension#installation) 설치
- 다음 중 하나를 수행합니다.
  - [빠른 시작: Azure Video Analyzer 시작](get-started-detect-motion-emit-events.md)
  - [빠른 시작: Azure Portal에서 Azure Video Analyzer 시작](get-started-detect-motion-emit-events-portal.md)
- IoT Edge 장치에 비디오 분석기 edge 모듈 버전 1.1 (또는 최신 버전)이 배포 되어 있어야 합니다.
- [Hyper-v](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/mt169373(v=ws.11)) 가상 머신 또는 [eflow](../../../iot-edge/how-to-install-iot-edge-on-windows.md?view=iotedge-2018-06&tabs=windowsadmincenter&preserve-view=true) 를 사용 하는 경우 onvifDeviceDiscover direct 메서드 호출을 수행 하려면 [외부 스위치가](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) 필요 합니다. 이 요구 사항은 ONVIF 장치 검색에 사용 되는 [멀티 캐스트](https://en.wikipedia.org/wiki/Multicast) 호출로 인 한 것입니다.

Video Analyzer edge 모듈의 ONVIF 기능을 [사용 하려면 onvif 검색 사용 기능](#enable-onvif-discovery-feature)에 설명 된 대로 특정 컨테이너 만들기 옵션이 필요 합니다. 이 ONVIF 검색 기능을 사용 하려면 포트 3702을 사용할 수 있어야 합니다.
  
> [!NOTE]
> Video Analyzer edge 모듈의 버전 1.1을 새로 배포 하는 경우 [직접 메서드 호출 사용](#use-direct-method-calls)에 대 한 섹션으로 건너뛸 수 있습니다. 그렇지 않은 경우에는 아래 섹션에 따라 기존 Video Analyzer edge 모듈을 업그레이드 하 여 ONVIF 검색 기능을 사용 하도록 설정 합니다.  

## <a name="check-the-version"></a>버전 확인

명령 프롬프트에서 다음 명령을 실행 합니다.

```CLI
az account set --subscription <YOUR_SUBSCRIPTION_NAME>
az iot hub module-twin show -m <VIDEO_ANALYZER_IOT_EDGE_MODULE_NAME> -n <IOT_HUB_NAME> -d <IOT_EDGE_DEVICE_NAME> --query 'properties.reported.ProductInfo' -o tsv
```

위의 명령 결과가 **Azure Video analyzer: 1.0.1** 인 경우에는 다음 단계를 실행 하 여 Video analyzer edge 모듈을 버전 1.1으로 업데이트 합니다.

1. Azure Portal에서 `<IOT_HUB_NAME>` Video Analyzer edge 모듈 배포에 사용 되는 IoT Hub (위)로 이동 합니다.
1. 자동 장치 관리에서 **IoT Edge** 를 클릭 하 고 `<IOT_EDGE_DEVICE_NAME>` Video Analyzer Edge 모듈이 배포 된 IoT Edge 장치 (위)를 선택 합니다.
1. **모듈 설정** 을 클릭 하 고 **검토 + 만들기** 를 클릭 합니다.
1. **만들기** 를 클릭 합니다.
1. 몇 분 후에 Video Analyzer edge 모듈이 업데이트 되므로 위의 명령을 다시 실행 하 여 다음을 확인할 수 있습니다.

### <a name="enable-onvif-discovery-feature"></a>ONVIF 검색 기능 사용

다음으로, 비디오 분석기 edge 모듈의 컨테이너 만들기 옵션에 추가 옵션을 추가 합니다. 이렇게 하면 호스트 네트워크를 통해 통신 하 여 동일한 서브넷에서 ONVIF 사용 카메라를 검색할 수 있습니다.

1. Azure Portal에서 IoT Hub 이동 합니다.
1. 자동 장치 관리에서 **IoT Edge** 를 클릭 하 고 Video Analyzer Edge 모듈이 배포 된 IoT Edge 장치를 선택 합니다.
1. **모듈 설정** 을 클릭 하 고 Video Analyzer edge 모듈을 선택 합니다.
1. **컨테이너 만들기 옵션** 탭으로 이동 하 여 해당 섹션에 다음 JSON 항목을 추가 합니다.

    - JSON 항목 상자에서 먼저 다음을 입력 합니다 `{` .
  
        ```JSON
        "NetworkingConfig": {
        "EndpointsConfig": {
            "host": {}
            }
        },
        ```
    - `HostConfig`JSON 개체에서:

        ```JSON
        "NetworkMode": "host",
        ```
1. 맨 아래에 있는 **업데이트** 를 클릭 합니다.
1. **검토 + 만들기** 를 클릭합니다.
1. **생성**

## <a name="use-direct-method-calls"></a>직접 메서드 호출 사용

Video Analyzer edge 모듈은 동일한 서브넷에서 네트워크에 연결 된 카메라의 ONVIF 검색에 대 한 직접 메서드 호출을 제공 합니다. 다음 단계는 `onvifDeviceDiscover` 및 아래 섹션 모두에 적용 됩니다 `onvifDeviceGet` .

1. Azure Portal에서 IoT Hub 이동 합니다.
1. 자동 장치 관리에서 **IoT Edge** 를 클릭 하 고 Video Analyzer Edge 모듈이 배포 된 IoT Edge 장치를 선택 합니다.
1. Video Analyzer edge 모듈을 클릭 하 고 맨 위에 있는 메뉴 모음에서 **직접 메서드** 를 클릭 합니다.

### <a name="onvifdevicediscover"></a>onvifDeviceDiscover

이 직접 메서드는 Video Analyzer edge 모듈과 동일한 서브넷에 있는 모든 검색 가능 ONVIF 장치를 나열 합니다.


1. 메서드 이름에 다음을 입력 합니다.

    ```JSON
    onvifDeviceDiscover
    ```
1. 페이로드에서 다음을 입력 합니다.

    ```JSON
    {
        "@apiVersion":"1.1",
        "discoveryDuration":"PT8S"
    }
    ```

   검색 기간은 Video Analyzer edge 모듈이 ONVIF 검색 가능 장치에서 응답을 받기 위해 대기 하는 시간입니다. 이 값을 조정 하려면 많은 환경에서 필요할 수 있습니다.

1. 몇 초 내에 다음과 같은이 표시 됩니다 `result` .

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
    200의 반환 상태는 직접 메서드 호출이 성공적으로 처리 되었음을 나타냅니다.

### <a name="onvifdeviceget"></a>onvifDeviceGet

OnvifDeviceGet 호출은 안전 하지 않은 TLS 사용 끝점을 모두 지원 합니다. 이 직접 메서드 호출은 특정 ONVIF 장치에 대 한 세부 정보를 검색 합니다.

# <a name="unsecuredendpoint"></a>[UnsecuredEndpoint](#tab/unsecuredendpoint)

> [!NOTE]
> ONVIF 사용 카메라의 보안 되지 않은 끝점에 대 한 onvifDeviceGet 호출이 수행 되 면 비디오 가장자리 모듈 Id 쌍 설정을로 설정 해야 `"allowUnsecuredEndpoints"` `true` 합니다. 자세한 내용은 [모듈 쌍 속성](./module-twin-configuration-schema.md)문서를 참조 하세요.

1. 메서드 이름에 다음을 입력 합니다.

    ```JSON
    onvifDeviceGet
    ```
1. 페이로드에서 다음을 입력 합니다.

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

1. 메서드 이름에 다음을 입력 합니다.

    ```JSON
    onvifDeviceGet
    ```
1. 페이로드에서 다음을 입력 합니다.

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

### <a name="return-status-of-onvifdeviceget"></a>OnvifDeviceGet의 반환 상태

| 상태 | 코드      | 의미/솔루션                                                                                                                                                                                                            |
| ------ | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 200    | Success   | 직접 메서드 호출이 완료 되었습니다.                                                                                                                                                                                |
| 400    | 잘못된 요청  | 요청의 형식이 잘못 되었습니다.   |
| 403    | 사용할 수 없음 | 인증 실패로 인해 직접 메서드 호출이 ONVIF 장치에서 요청 된 정보를 성공적으로 검색할 수 없습니다. 메시지 본문의 사용자 이름 및/또는 암호가 올바른지 확인 합니다. |
| 500 | 오류 | 알 수 없는 오류가 발생했습니다.  |
| 502    | 잘못된 게이트웨이 | 업스트림 서비스에서 잘못 된 응답을 받았습니다.  |
| 504    | 제한 시간   | ONVIF 장치의 응답을 받기 전에 직접 메서드 호출이 만료 되었습니다.  |

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 몇 가지 문제 해결 단계를 설명 합니다.

- "오류가 발생 하 여 작업을 성공적으로 완료 하지 못했습니다." 오류가 발생 하는 경우에는 IoT Edge module direct method 블레이드에서 Azure Portal 합니다. 요청이 실패 했으며 상태 코드는 504입니다. " (아래 이미지 참조):

    :::image type="content" source="./media/camera-discovery/five-zero-four-error.png" alt-text="504 오류를 보여 주는 스크린샷":::

    `"discoveryDuration":"PT8S"`위의 직접 메서드 호출에서에 대 한 설정이 또는 값 보다 짧은 지 확인 합니다 `Connection Timeout` `Method Timeout` .

    :::image type="content" source="./media/camera-discovery/five-zero-four-error-fix.png" alt-text="504 오류 수정을 보여 주는 스크린샷":::

- 직접 메서드 `Results` 필드가 "{" status ": 200," 페이로드 ": {" value ": []}}를 표시 하는 경우 검색 기간이 더 길어질 수 있습니다.

    :::image type="content" source="./media/camera-discovery/result-status-two-hundred-null.png" alt-text="메시지 반환이 직접 메서드 ' 결과 ' 필드에 표시 됩니다.":::

    에서 시간 값 (x)을 `"discoveryDuration":"PTxS"` 더 큰 값으로 조정 합니다. 또한 `Connection Timeout` 및/또는 값을 적절 하 게 조정 `Method Timeout` 합니다.
- `onvifDeviceGet`직접 메서드 호출은 265 인코드된 미디어 스트림의 미디어 프로필을 표시 하지 않습니다.
- ONVIF 장치에 연결 하는 데 사용 된 사용자 계정에 ONVIF 카메라 기능에 대 한 권한이 없는 경우 403의 반환 상태가 반환 될 수 있습니다. 일부 ONVIF 규격 카메라의 경우 onvif 장치 정보를 검색 하기 위해 사용자를 ONVIF 보안 설정에 추가 해야 합니다.
- 현재 Video Analyzer edge 모듈은 멀티 캐스트를 통해 동일한 서브넷에 연결할 수 있는 최대 200 ONVIF 사용 카메라를 반환 합니다. 또한 포트 3702을 사용할 수 있어야 합니다.
- TLS 끝점을 사용 하 여 onvifDeviceGet을 호출 하는 경우 ONVIF 장치 이름 및 IP 주소를에 지 장치의 호스트 (/etc/hosts) 파일에 추가 해야 합니다.  자체 서명 된 인증서가 TLS 암호화에 사용 되는 경우 인증서를 IoT Edge 장치의 인증서 저장소에 추가 해야 합니다. 
- EFLOW 가상 머신에서 onvifDeviceDiscover 직접 메서드 호출의 경우 다음 단계를 수행 해야 합니다.
  - EFLOW 가상 머신으로 커넥트 하 고 다음을 실행 합니다.`sudo iptables -I INPUT -p udp -j ACCEPT`

## <a name="next-steps"></a>다음 단계

- 검색 된 ONVIF 장치를 사용 하 여 [라이브 비디오 분석을 위한 빠른](analyze-live-video-use-your-model-http.md) 시작을 사용해 보세요.
