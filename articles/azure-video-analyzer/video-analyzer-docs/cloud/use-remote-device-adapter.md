---
title: 원격 장치 어댑터를 사용 하 여 클라우드로 카메라 커넥트
description: 이 문서에서는 원격 장치 어댑터를 사용 하 여 Azure Video Analyzer 서비스에 카메라를 연결 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71bcf82420d9777158cbb878c4943b350d51353b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053165"
---
# <a name="connect-cameras-to-the-cloud-using-a-remote-device-adapter"></a>원격 장치 어댑터를 사용 하 여 클라우드에 카메라 커넥트

[!INCLUDE [header](includes/cloud-env.md)]

Azure Video Analyzer 서비스를 사용 하면 사용자가 클라우드에 연결 된 RTSP 카메라에서 비디오를 캡처 및 기록할 수 있습니다. 이렇게 하려면 인터넷을 통해 이러한 카메라에 액세스할 수 있어야 합니다 .이는 대부분의 경우 허용 되지 않을 수 있습니다. 대신, RTSP 카메라와 동일한 (개인) 네트워크의 경량 장치에 비디오 분석기 edge 모듈을 배포 하 고,에 지 장치를 인터넷에 연결할 수 있습니다. Edge 모듈은 이제 비디오 분석기 서비스를 사용 하 여 *원격 장치* (카메라)에 연결할 수 있는 *어댑터로* 설정할 수 있습니다. Edge 모듈은 RTSP 카메라와 Video Analyzer 서비스 간의 비디오 트래픽에 대 한 [투명 게이트웨이](../../../iot-edge/iot-edge-as-gateway.md) 역할을 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-remote-device-adapter/use-remote-device-adapter.svg" alt-text="원격 장치 어댑터를 사용 하 여 클라우드에 카메라 커넥트":::

## <a name="pre-reading"></a>미리 읽기

* [클라우드로 카메라 커넥트](connect-cameras-to-cloud.md)

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* IoT Hub
* 연결 된 [비디오 분석기 계정](../create-video-analyzer-account.md) :
  * 스토리지 계정
  * 사용자 할당 관리 id (UAMI)
  * [IoT Hub 비디오 분석기 계정에 연결 해야 합니다.](../create-video-analyzer-account.md#post-deployment-steps)
* [Video Analyzer Edge 모듈이 설치 및 구성 된 IoT Edge 장치](../edge/deploy-iot-edge-device.md)
* [RTSP 카메라](../quotas-limitations.md#supported-cameras)
  * 카메라가 edge 장치와 동일한 네트워크에 있는지 확인 합니다.
  * 최대 대역폭 (kBps 또는 k b/초 단위로 측정 됨) 이하로 비디오를 보내도록 카메라를 구성할 수 있는지 확인 합니다.

## <a name="overview"></a>개요
원격 장치 어댑터를 사용 하 여 비디오 분석기 서비스에 카메라를 연결 하려면 다음을 수행 해야 합니다.

1. IoT Hub에 IoT 장치를 만들어 RTSP 카메라를 나타냄
1. 비디오 분석기 edge 모듈에서 위의 장치에 대 한 투명 게이트웨이 역할을 하는 장치 어댑터 만들기
1. 비디오 분석기 서비스에서 라이브 파이프라인을 만들 때 IoT 장치 및 장치 어댑터를 사용 하 여 카메라에서 비디오 캡처 및 기록



## <a name="create-an-iot-device"></a>IoT 장치 만들기

비디오 분석기 서비스에 연결 해야 하는 각 RTSP 카메라를 나타내는 IoT 장치를 만듭니다. Azure Portal에서 다음을 수행합니다.

1. IoT Hub 탐색
1. **장치 관리** 에서 **장치** 창을 선택 합니다.
1. **+ 장치 추가** 를 선택 합니다. 
1. 고유한 문자열을 사용 하 여 **장치 ID** 입력 (예: building404-camera1)
1. **인증 유형은** **대칭 키** 여야 합니다.
1. 다른 모든 속성은 기본값으로 남겨둘 수 있습니다.
1. **저장** 을 선택 하 여 IoT 장치를 만듭니다.
1. IoT 장치를 선택 하 고 **기본 키** 또는 **보조 키** 를 기록 합니다. 아래에 필요 합니다.

## <a name="create-a-remote-device-adapter"></a>원격 장치 어댑터 만들기

비디오 분석기 edge 모듈이 카메라와 Video Analyzer 서비스 간의 비디오에 대 한 투명 게이트웨이 역할을 하도록 설정 하려면 각 카메라에 대해 원격 장치 어댑터를 만들어야 합니다. 다음 값을 필요로 하는 [ **Remotedeviceadapterset** 직접 메서드](../edge/direct-methods.md) 를 호출 합니다. 

* IoT 장치에 대 한 장치 ID
* IoT 장치에 대 한 기본 키
* 카메라의 IP 주소

Azure Portal에서 다음을 수행합니다.

1. IoT Hub 탐색
1. **자동 장치 관리** 아래에서 **IoT Edge** 창을 선택 합니다.
1. 비디오 분석기 Edge 모듈이 배포 된 IoT Edge 장치 (예: **ava-샘플 장치**)를 선택 합니다.
1. 모듈에서 Video Analyzer edge 모듈 (예: **avaedge**)을 선택 합니다.
1. **</> 직접 메서드** 를 선택 합니다. 
1. 메서드 이름에 대 한 **Remotedeviceadapterset** 을 입력 합니다.
1. **페이로드에** 대해 다음을 입력 합니다.

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

성공 하면 상태 코드가 201 인 응답을 받게 됩니다.


## <a name="create-pipeline-topology-in-the-video-analyzer-service"></a>Video Analyzer 서비스에서 파이프라인 토폴로지 만들기

방화벽 뒤의 카메라에서 수집 하기 위해 클라우드 파이프라인 토폴로지를 만들 때, 터널링은 파이프라인 토폴로지의 RTSP 원본 노드에서 사용 하도록 설정 되어야 합니다. 이러한 [파이프라인 토폴로지의](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cloud-record-camera-behind-firewall)예를 참조 하세요.  

[이 빠른](get-started-livepipelines-portal.md#deploy-a-live-pipeline) 시작에서는 Azure Portal에서 파이프라인 토폴로지 및 라이브 파이프라인을 만드는 단계를 간략하게 설명 합니다. 샘플 토폴로지를 사용 `Live capture, record, and stream from RTSP camera behind firewall` 합니다.

다음 값은 이전 지침에서 프로 비전 된 IoT 장치에 따라 RTSP 원본 노드에서 터널링을 사용 하도록 설정 하는 데 필요 합니다.

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
* `Transport` 는로 설정 됩니다. `tcp`
* `Endpoint` 는로 설정 됩니다. `UnsecuredEndpoint`
* `Tunnel` 는로 설정 됩니다. `SecureIotDeviceRemoteTunnel`

## <a name="create-and-activate-a-live-pipeline"></a>라이브 파이프라인 만들기 및 활성화

라이브 파이프라인을 만들 때 RTSP URL, RTSP 사용자 이름, RTSP 암호 및 IoT Hub 장치 ID를 정의 해야 합니다. 샘플 페이로드는 다음과 같습니다.

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
                "value": "rtsp://localhost:554/<camera-specific-suffix>"
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
RTSP URL은 **localhost** 여야 합니다. `bitrateKbps`값이 RTSP 카메라의 비디오에 대 한 최대 비트 전송률 설정과 일치 하는지 확인 합니다.

라이브 파이프라인을 만든 후 파이프라인을 활성화 하 여 Video Analyzer 비디오 리소스에 기록을 시작할 수 있습니다. 이전 단계에서 언급 한 [빠른 시작은](get-started-livepipelines-portal.md#deploy-a-live-pipeline) Azure Portal에서 라이브 파이프라인을 활성화 하는 방법에 대해서도 간략하게 설명 합니다.


### <a name="playback-recorded-video-in-the-azure-portal"></a>Azure Portal에서 녹화 된 비디오 재생

1. 라이브 파이프라인을 활성화 한 후 비디오 리소스는 Azure Portal 비디오 분석기 계정 **비디오** 창에서 사용할 수 있습니다. 상태는 파이프라인이 활성 상태이 고 기록 **중으로 사용** 중임을 표시 합니다.
1. 라이브 파이프라인에서 정의한 비디오 리소스를 선택 하 여 비디오를 봅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="클라우드의 라이브 파이프라인에서 캡처된 라이브 비디오의 스크린샷":::

비디오를 재생 하는 동안 오류가 발생 하는 경우 [이 문제 해결 가이드](troubleshoot.md#unable-to-play-video-after-activating-live-pipeline)의 단계를 수행 합니다.

[!INCLUDE [activate-deactivate-pipeline](../edge/includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>다음 단계

이제 비디오 분석기 계정에 비디오가 있으므로 [이 자습서](export-portion-of-video-as-mp4.md)를 사용 하 여이 녹화 된 비디오의 클립을 MP4 형식으로 내보낼 수 있습니다.
