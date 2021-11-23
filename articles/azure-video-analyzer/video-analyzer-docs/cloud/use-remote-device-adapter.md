---
title: 원격 디바이스 어댑터를 사용하여 클라우드에 카메라 커넥트
description: 이 문서에서는 원격 디바이스 어댑터를 사용하여 Azure Video Analyzer 서비스에 카메라를 연결하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9e3e6f508c3abc77124da8c93eff1c092ad66847
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936678"
---
# <a name="connect-cameras-to-the-cloud-using-a-remote-device-adapter"></a>원격 디바이스 어댑터를 사용하여 클라우드에 카메라 커넥트

[!INCLUDE [header](includes/cloud-env.md)]

Azure Video Analyzer 서비스를 사용하면 사용자가 클라우드에 연결된 RTSP 카메라에서 비디오를 캡처하고 녹화할 수 있습니다. 이렇게 하려면 인터넷을 통해 이러한 카메라에 액세스할 수 있어야 하며, 대부분의 경우 허용되지 않을 수 있습니다. 대신 RTSP 카메라와 동일한(프라이빗) 네트워크의 경량 에지 디바이스에 Video Analyzer 에지 모듈을 배포하고 에지 디바이스를 인터넷에 연결할 수 있습니다. 이제 Edge 모듈을 Video Analyzer 서비스가 원격 디바이스(카메라)에  연결할 수 있도록 하는 *어댑터로* 설정할 수 있습니다. 에지 모듈은 RTSP 카메라와 Video Analyzer 서비스 간의 비디오 트래픽에 대한 [투명 게이트웨이](../../../iot-edge/iot-edge-as-gateway.md) 역할을 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-remote-device-adapter/use-remote-device-adapter.svg" alt-text="원격 디바이스 어댑터를 사용하여 클라우드에 카메라 커넥트":::

## <a name="pre-reading"></a>사전 읽기

* [클라우드에 카메라 커넥트](connect-cameras-to-cloud.md)
* [빠른 시작: Azure Portal Video Analyzer 라이브 파이프라인 시작](get-started-livepipelines-portal.md)

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [IoT Edge 디바이스에 Azure Video Analyzer 배포](../edge/deploy-iot-edge-device.md)
* [IoT Hub Video Analyzer 계정에 연결해야 합니다.](../create-video-analyzer-account.md#post-deployment-steps)
* [RTSP 카메라](../quotas-limitations.md#supported-cameras)
  * 카메라가 에지 디바이스와 동일한 네트워크에 있는지 확인합니다.
  * 최대 대역폭(kBps 또는 KBps/초 단위로 측정) 이하의 비디오를 보내도록 카메라를 구성할 수 있는지 확인합니다.

## <a name="overview"></a>개요
원격 디바이스 어댑터를 사용하여 Video Analyzer 서비스에 카메라를 연결하려면 다음을 수행해야 합니다.

1. IOT HUB RTSP 카메라를 나타내는 IoT 디바이스 만들기
1. Video Analyzer 에지 모듈에서 디바이스 어댑터를 만들어 위의 디바이스에 대한 투명 게이트웨이 역할을 합니다.
1. Video Analyzer 서비스에서 라이브 파이프라인을 만들 때 IoT 디바이스 및 디바이스 어댑터를 사용하여 카메라에서 비디오를 캡처하고 녹화합니다.



## <a name="create-an-iot-device"></a>IoT 디바이스 만들기

Video Analyzer 서비스에 연결해야 하는 각 RTSP 카메라를 나타내는 IoT 디바이스를 만듭니다. Azure Portal에서 다음을 수행합니다.

1. IoT Hub 이동합니다.
1. **디바이스** **관리에서 디바이스** 창을 선택합니다.
1. **+디바이스 추가를 선택합니다.** 
1. 고유 문자열을 사용하여 **디바이스 ID** 입력(예: building404-camera1)
1. **인증 유형은** **대칭 키여야** 합니다.
1. 다른 모든 속성은 기본값으로 남겨질 수 있습니다.
1. **저장을** 선택하여 IoT 디바이스를 만듭니다.
1. IoT 디바이스를 선택하고 아래에 필요하므로 **기본 키** 또는 **보조 키를** 기록합니다.

## <a name="create-a-remote-device-adapter"></a>원격 디바이스 어댑터 만들기

Video Analyzer 에지 모듈이 카메라와 Video Analyzer 서비스 간의 비디오에 대한 투명 게이트웨이 역할을 하도록 설정하려면 각 카메라에 대한 원격 디바이스 어댑터를 만들어야 합니다. 다음 값이 필요한 [ **remoteDeviceAdapterSet** 직접 메서드를](../edge/direct-methods.md) 호출합니다. 

* IoT 디바이스의 디바이스 ID
* IoT 디바이스의 기본 키
* 카메라의 IP 주소

Azure Portal에서 다음을 수행합니다.

1. IoT Hub 이동합니다.
1. **디바이스 관리에서** **IoT Edge** 창을 선택합니다.
1. Video Analyzer 에지 모듈이 배포된 IoT Edge 디바이스(예: **ava-sample-device)를** 선택합니다.
1. 모듈 아래에서 Video Analyzer 에지 모듈(예: **avaedge)을** 선택합니다.
1. **</> 직접 메서드** 선택 
1. 메서드 이름에 **remoteDeviceAdapterSet를** 입력합니다.
1. **페이로드에** 다음을 입력합니다.

```
 {
   "@apiVersion" : "1.1",
   "name": "<name of remote device adapter such as remoteDeviceAdapterCamera1>",
   "properties": {
     "target": {
       "host": "<Camera's IP address>"
      },
     "iotHubDeviceConnection": {
      "deviceId": "<IoT Hub Device ID>",
      "credentials": {
        "@type": "#Microsoft.VideoAnalyzer.SymmetricKeyCredentials",
        "key": "<Primary or Secondary Key>"
       }
     }
   }
 }
 
```

성공하면 상태 코드 201이 있는 응답을 받게 됩니다.

설정된 모든 원격 디바이스 어댑터를 나열하려면 다음 페이로드를 사용하여 **remoteDeviceAdapterList** 직접 메서드를 호출합니다.
```
 {
   "@apiVersion" : "1.1"
 }
```


## <a name="create-pipeline-topology-in-the-video-analyzer-service"></a>Video Analyzer 서비스에서 파이프라인 토폴로지 만들기

클라우드 파이프라인 토폴로지에서 방화벽 뒤의 카메라에서 을(를) 생성하는 경우 파이프라인 토폴로지의 RTSP 원본 노드에서 터널링을 사용하도록 설정해야 합니다. 이러한 [파이프라인 토폴로지의](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/cloud-topologies/cloud-record-camera-behind-firewall)예를 참조하세요.


RTSP 원본 노드에서 터널링을 사용하도록 설정하려면 이전 지침에서 만든 IoT 디바이스에 따라 다음 값이 필요합니다.

* IoT Hub 이름
* IoT Hub 디바이스 ID

```
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "url": "${rtspUrlParameter}",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUsernameParameter}",
                        "password": "${rtspPasswordParameter}"
                    },
                    "tunnel": { 
                        "@type": "#Microsoft.VideoAnalyzer.SecureIotDeviceRemoteTunnel",
                        "iotHubName" : "<IoT Hub Name>",
                        "deviceId": "${ioTHubDeviceIdParameter}"
                    }
                }
            }
``` 

다음 사항을 확인합니다.
* `Transport` 가 로 설정된 경우 `tcp`
* `Endpoint` 가 로 설정된 경우 `UnsecuredEndpoint`
* `Tunnel` 가 로 설정된 경우 `SecureIotDeviceRemoteTunnel`

[이 빠른 시작은](get-started-livepipelines-portal.md#deploy-a-live-pipeline) Azure Portal 파이프라인 토폴로지 및 라이브 파이프라인을 만드는 단계를 간략하게 설명하는 참조를 사용할 수 있습니다. 샘플 토폴로지 `Live capture, record, and stream from RTSP camera behind firewall` 를 사용합니다. 

## <a name="create-and-activate-a-live-pipeline"></a>라이브 파이프라인 만들기 및 활성화

라이브 파이프라인을 만들 때 RTSP URL, RTSP 사용자 이름, RTSP 암호 및 IoT Hub 디바이스 ID를 정의해야 합니다. 샘플 페이로드는 아래와 같습니다.

```
   {
    "name": "record-from-building404-camera1",
    "properties": {
        "topologyName": "record-camera-behind-firewall",
        "description": "Capture, record and stream video from building404-camera1 via a remote device adapter",
        "bitrateKbps": 1500,
        "parameters": [
            {
                "name": "rtspUrlParameter",
                "value": "<RTSP URL for building404-camera1 such as rtsp://localhost:554/media/video>"
            },
            {
                "name": "rtspUsernameParameter",
                "value": "<User name for building404-camera1>"
            },
            {
                "name": "rtspPasswordParameter",
                "value": "<Password for building404-camera1>"
            },
            {
                "name": "ioTHubDeviceIdParameter",
                "value": "<IoT Hub Device ID such as building404-camera1>"
            },
            {
                "name": "videoName",
                "value": "video-from-building404-camera1"
            }
          ]
       }
   }
```
RTSP URL IP 주소는 **localhost** 여야 합니다. `bitrateKbps`값이 RTSP 카메라의 비디오에 대한 최대 비트 전송률 설정과 일치하는지 확인합니다.

라이브 파이프라인을 만든 후 파이프라인을 활성화하여 Video Analyzer 비디오 리소스에 대한 녹화를 시작할 수 있습니다. 이전 단계에서 언급한 [빠른 시작에서는](get-started-livepipelines-portal.md#deploy-a-live-pipeline) Azure Portal 라이브 파이프라인을 활성화하는 방법도 간략하게 설명합니다.


### <a name="playback-recorded-video-in-the-azure-portal"></a>Azure Portal 녹화된 비디오 재생

1. 라이브 파이프라인을 활성화하면 Azure Portal Video Analyzer 계정 **비디오** 창에서 비디오 리소스를 사용할 수 있습니다. 상태는 **파이프라인이** 활성 상태이고 기록 중임을 나타냅니다.
1. 라이브 파이프라인에 정의된 비디오 리소스를 선택하여 비디오를 봅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="클라우드의 라이브 파이프라인에서 캡처한 라이브 비디오의 스크린샷.":::

비디오를 재생하는 동안 오류가 발생하는 경우 [이 문제 해결 가이드](troubleshoot.md#unable-to-play-video-after-activating-live-pipeline)의 단계를 따르세요.

[!INCLUDE [activate-deactivate-pipeline](../edge/includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>다음 단계

이제 Video Analyzer 계정에 비디오가 존재하기 때문에 [이 자습서를](export-portion-of-video-as-mp4.md)사용하여 녹화된 비디오의 클립을 MP4 형식으로 내보낼 수 있습니다.
