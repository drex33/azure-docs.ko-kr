---
title: Azure Live Video Analytics on IoT Edge 및 Azure Custom Vision을 사용하여 라이브 비디오 분석
description: Azure Custom Vision을 사용하여 장난감 트럭을 검색할 수 있는 컨테이너화된 모델을 빌드하는 방법과 Live Video Analytics on Azure IoT Edge의 AI 확장 기능을 사용하여 라이브 비디오 스트림에서 장난감 트럭을 검색하기 위해 에지에 모델을 배포하는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 221232b69d85bb187dd952179712e336afff0906
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215339"
---
# <a name="tutorial-analyze-live-video-with-azure-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>자습서: Azure Live Video Analytics on IoT Edge 및 Azure Custom Vision을 사용하여 라이브 비디오 분석

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 자습서에서는 Azure [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)을 사용하여 장난감 트럭을 탐지할 수 있는 컨테이너화된 모델을 빌드하는 방법과 Azure Live Video Analytics on Azure IoT Edge의 [AI 확장 기능](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model)을 사용하여 라이브 비디오 스트림에서 장난감 트럭을 탐지하는 모델을 에지에 배포하는 방법에 대해 알아봅니다.

몇 가지 이미지를 업로드하고 레이블을 지정하여 컴퓨터 비전 모델을 빌드하고 학습시키는 Custom Vision의 강력한 성능을 통합하는 방법을 보여 드리겠습니다. 데이터 과학, 기계 학습 또는 AI에 대한 지식이 필요하지 않습니다. 간편하게 사용자 지정 모델을 에지에 컨테이너로 배포하고 시뮬레이션된 라이브 비디오 피드를 분석하는 Live Video Analytics의 기능에 대해서도 알아봅니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 관련 리소스 설치
> * 클라우드에서 장난감 트럭을 탐지하고 에지에 배포하는 Custom Vision 모델 빌드
> * HTTP 확장을 사용하여 미디어 그래프를 만들고 Custom Vision 모델에 배포
> * 샘플 코드 실행
> * 결과를 검사하고 해석

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>추천 참고 자료  

시작하기 전에 다음 문서를 검토하세요.

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Azure Custom Vision 개요](../../cognitive-services/custom-vision-service/overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)
* [미디어 그래프 개념](media-graph-concept.md)
* [비디오 녹화가 없는 Live Video Analytics](analyze-live-video-concept.md)
* [사용자 고유의 모델로 Live Video Analytics 실행](use-your-model-quickstart.md)
* [자습서: IoT Edge 모듈 개발](../../iot-edge/tutorial-develop-for-linux.md)
* [deployment.*.template.json을 편집하는 방법](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>필수 구성 요소

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end

> [!IMPORTANT]
> 이 Custom Vision 모듈은 **Intel x86 및 amd64** 아키텍처만 지원합니다. 계속하기 전에 에지 디바이스의 아키텍처를 확인하세요.

## <a name="review-the-sample-video"></a>샘플 비디오 검토

이 자습서에서는 [장난감 자동차 유추 비디오](https://lvamedia.blob.core.windows.net/public/t2.mkv) 파일을 사용하여 라이브 스트림을 시뮬레이션합니다. [VLC media player](https://www.videolan.org/vlc/)와 같은 애플리케이션을 통해 비디오를 검사할 수 있습니다. **Ctrl+N** 을 선택한 다음, [장난감 자동차 유추 비디오](https://lvamedia.blob.core.windows.net/public/t2.mkv)에 대한 링크를 붙여넣어 재생을 시작합니다. 비디오를 시청할 때 36초 마커에서 장난감 트럭이 비디오에 나타납니다. 사용자 지정 모델은 이 특정 장난감 트럭을 검색하도록 학습되었습니다. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LPwK]

이 자습서에서는 Live Video Analytics on IoT Edge를 사용하여 이러한 장난감 트럭을 탐지하고 IoT Edge 허브에 관련 유추 이벤트를 게시합니다.

## <a name="overview"></a>개요

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Custom Vision 개요를 보여주는 다이어그램":::

이 다이어그램에서는 이 자습서의 신호 흐름을 보여 줍니다. [에지 모듈](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스팅하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](media-graph-concept.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [HTTP 확장 프로세서](media-graph-concept.md#http-extension-processor) 노드로 보냅니다.

HTTP 확장 노드는 프록시 역할을 수행합니다.  `samplingOptions` 필드를 사용하여 설정한 수신 비디오 프레임을 샘플링하고 비디오 프레임을 지정된 이미지 형식으로 변환합니다. 그런 다음, REST를 통해 이미지를 HTTP 엔드포인트 내부에서 AI 모델을 실행하는 다른 에지 모듈에 릴레이합니다. 이 예에서 에지 모듈은 Custom Vision을 사용하여 빌드된 장난감 트럭 탐지기 모델입니다. HTTP 확장 프로세서 노드는 탐지 결과를 수집하고, 이벤트를 [Azure IoT Hub 싱크](media-graph-concept.md#iot-hub-message-sink) 노드에 게시합니다. 그런 다음, 노드에서 이러한 이벤트를 [IoT Edge 허브](../../iot-fundamentals/iot-glossary.md#iot-edge-hub)에 보냅니다.

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Custom Vision 장난감 검색 모델 빌드 및 배포 

Custom Vision은 그 이름처럼 클라우드에서 사용자 지정 개체 탐지기 또는 분류자를 빌드하는 데 사용할 수 있습니다. 그리고 컨테이너를 통해 클라우드 또는 에지에 배포할 수 있는 Custom Vision 모델을 빌드하기 위한 간단하고 사용하기 쉬운 직관적인 인터페이스를 제공합니다.

장난감 트럭 탐지기를 빌드하려면 [빠른 시작: Custom Vision 웹 사이트를 사용하여 개체 탐지기 빌드](../../cognitive-services/custom-vision-service/get-started-build-detector.md)의 단계를 수행합니다.

> [!IMPORTANT]
> 이 Custom Vision 모듈은 **Intel x86 및 amd64** 아키텍처만 지원합니다. 계속하기 전에 에지 디바이스의 아키텍처를 확인하세요.

추가 참고 사항:
 
* 이 자습서에서는 빠른 시작 문서의 [필수 조건 섹션](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)에 제공된 샘플 이미지를 사용하지 마세요. 그 대신 특정 이미지 세트를 사용하여 장난감 탐지기 Custom Vision 모델을 빌드했습니다. 빠른 시작에서 [학습 이미지 선택](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) 메시지가 표시되면 [이 이미지](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip)를 사용하세요.
* 빠른 시작의 이미지 태그 지정 섹션에서, 그림에 표시된 장난감 트럭에 "배달 트럭"이라는 태그를 지정했는지 확인하세요.

모두 마쳤으면 **성능** 탭의 **내보내기** 단추를 사용하여 모델을 Docker 컨테이너로 내보낼 수 있습니다. 컨테이너 플랫폼 유형으로 Linux를 선택합니다. 이는 컨테이너가 실행되는 플랫폼입니다. 컨테이너를 다운로드하는 컴퓨터는 Windows 또는 Linux일 수 있습니다. 다음 지침은 Windows 컴퓨터에 다운로드된 컨테이너 파일을 기반으로 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Dockerfile이 선택된 화면을 보여주는 스크린샷":::
 
1. 로컬 컴퓨터에 `<projectname>.DockerFile.Linux.zip`이라는 zip 파일을 다운로드해야 합니다. 
1. Docker가 설치되었는지 확인합니다. 설치되지 않았으면 Windows 데스크톱용 [Docker](https://docs.docker.com/get-docker/)를 설치합니다.
1. 선택한 위치에 다운로드한 파일의 압축을 풉니다. 명령줄을 사용하여 압축을 푼 폴더 디렉터리로 이동합니다.
    
    다음 명령을 실행합니다.
    
    1. `docker build -t cvtruck` 
    
        이 명령은 여러 패키지를 다운로드하고, Docker 이미지를 빌드하고, `cvtruck:latest`라는 태그를 지정합니다.
    
        > [!NOTE]
        > 명령이 성공하면 `Successfully built <docker image id>` 및 `Successfully tagged cvtruck:latest` 메시지가 표시됩니다. 빌드 명령이 실패할 경우 다시 시도합니다. 처음에는 종속성 패키지가 다운로드되지 않을 수 있습니다.
    1. `docker  image ls`

        이 명령은 새 이미지가 로컬 레지스트리에 있는지 확인합니다.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        이 명령은 Docker의 노출된 포트(80)를 로컬 머신의 포트(80)에 게시합니다.
    1. `docker container ls`
    
        이 명령은 포트 매핑과 머신에서 Docker 컨테이너가 성공적으로 실행되고 있는지 여부를 확인합니다. 출력은 다음과 같습니다.

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            이 명령은 로컬 머신의 컨테이너를 테스트합니다. 모델을 학습시킬 때와 동일한 배달 트럭이 이미지에 있는 경우 출력은 다음 예제와 비슷합니다. 배달 트럭이 90.12% 확률로 탐지되었음을 나타냅니다.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>샘플 파일 검사

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>배포 매니페스트 생성 및 배포

1. Visual Studio Code에서 src/cloud-to-device-console-app/operations.json으로 이동합니다.

1. `GraphTopologySet`에서 다음 사항을 충족하는지 확인합니다.<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`
1. `GraphInstanceSet`에서 다음 작업을 수행합니다.
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. 매개 변수 배열의 맨 위에 `{"name": "inferencingUrl","value": "http://cv:80/image"},`를 추가합니다.
    1. `rtspUrl` 매개 변수 값을 `"rtsp://rtspsim:554/media/t2.mkv"`로 변경합니다.
1. `GraphTopologyDelete`에서 `"name": "InferencingWithHttpExtension"`인지 확인합니다.
1. src/edge/ deployment.customvision.template.json 파일을 마우스 오른쪽 단추로 클릭하고, **IoT Edge 배포 매니페스트 생성** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="[IoT Edge 배포 매니페스트 생성]을 보여주는 스크린샷":::
  
    이 작업은 src/edge/config 폴더에 deployment.customvision.amd64.json이라는 매니페스트 파일을 만듭니다.
1. src/edge/ deployment.customvision.template.json 파일을 열고 `registryCredentials` JSON 블록을 찾습니다. 이 블록에서는 사용자 이름 및 암호를 사용하여 Azure 컨테이너 레지스트리의 주소를 찾습니다.
1. 명령줄에서 다음 단계를 수행하여 로컬 Custom Vision 컨테이너를 Azure Container Registry 인스턴스에 푸시합니다.

    1. 다음 명령을 실행하여 레지스트리에 로그인합니다.
    
        `docker login <address>`
    
        인증을 요청하는 경우 사용자 이름 및 암호를 입력합니다.
        
        > [!NOTE]
        > 암호는 명령줄에 표시되지 않습니다.
    1. 다음 명령을 사용하여 이미지에 태그를 지정합니다. <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. 다음 명령을 사용하여 이미지를 푸시합니다. <br/>`docker push <address>/cvtruck`.

        명령이 성공하면 이미지의 SHA와 함께 명령줄에 `Pushed`가 표시됩니다.
    1. Azure Portal에서 Azure Container Registry 인스턴스를 검사하여 확인할 수도 있습니다. 여기에 태그와 함께 리포지토리의 이름이 표시됩니다.
1. 왼쪽 아래 모서리에서 **AZURE IOT HUB** 창 옆에 있는 **추가 작업** 아이콘을 선택하여 IoT Hub 연결 문자열을 설정합니다. appsettings.json 파일에서 문자열을 복사할 수 있습니다. Visual Studio Code 내에서 적절한 IoT 허브를 구성하는 또 다른 권장 방법은 [IoT Hub 선택 명령](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)을 사용하는 것입니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="[IoT Hub 연결 문자열 설정]을 보여주는 스크린샷":::
1. 다음으로 src/edge/config/ deployment.customvision.amd64.json을 마우스 오른쪽 단추로 클릭하고 **단일 디바이스용 배포 만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="[단일 디바이스용 배포 만들기]를 보여주는 스크린샷":::
1. 그러면 IoT Hub 디바이스를 선택하라는 메시지가 표시됩니다. 드롭다운 목록에서 **lva-sample-device** 를 선택합니다.
1. 약 30초 후에 왼쪽 아래 섹션에서 Azure IoT 허브가 새로 고침됩니다. 에지 디바이스에 다음 모듈이 배포되었을 것입니다.

    * `lvaEdge`라는 Live Video Analytics on IoT Edge 모듈
    * RTSP 서버를 시뮬레이션하는 `rtspsim`이라는 모듈. 라이브 비디오 피드의 원본 역할을 합니다.
    * `cv`이라는 모듈. 이름으로 알 수 있듯이 이미지에 Custom Vision을 적용하고 여러 태그 유형을 반환하는 Custom Vision 장난감 트럭 탐지 모델입니다. (이 모델은 배달 트럭이라는 하나의 태그에 대해서만 학습되었습니다.)

## <a name="prepare-for-monitoring-events"></a>이벤트 모니터링 준비

마우스 오른쪽 단추로 Live Video Analytics 디바이스를 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다. 이 단계는 Visual Studio Code의 **출력** 창에서 IoT Hub 이벤트를 모니터링하는 데 필요합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="[기본 제공 이벤트 엔드포인트 모니터링 시작]을 보여주는 스크린샷":::

## <a name="run-the-sample-program"></a>샘플 프로그램 실행

브라우저에서 이 자습서의 그래프 토폴로지를 열면 `inferencingUrl` 값이 `http://cv:80/image`로 설정된 것을 볼 수 있습니다. 이 설정은 유추 서버가 라이브 비디오에서 장난감 트럭을 탐지한 후(있는 경우) 결과를 반환한다는 뜻입니다.

1. Visual Studio Code에서 **확장** 탭을 열고(또는 **Ctrl+Shift+X** 를 선택하고) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="[확장 설정]을 보여주는 스크린샷.":::
1. **자세한 정보 메시지 표시** 를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="[자세한 정보 메시지 표시]를 보여주는 스크린샷":::
1. 디버깅 세션을 시작하려면 **F5** 키를 선택합니다. **터미널** 창에 메시지가 출력되어 표시됩니다.
1. operations.json 코드가 `GraphTopologyList` 및 `GraphInstanceList` 직접 메서드를 호출하여 시작됩니다. 이전 빠른 시작을 완료한 후 리소스를 정리했다면 이 프로세스가 빈 목록을 반환하고 일시 중지됩니다. 계속하려면 **Enter** 키를 선택합니다.
    
   **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.
    
   * 이전 `topologyUrl`을 사용하는 `GraphTopologySet` 호출
   * 다음 본문을 사용하는 `GraphInstanceSet`에 대한 호출
        
   ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
              },
              {
                "name": "rtspUserName",
                "value": "testuser"
              },
              {
                "name": "rtspPassword",
                "value": "testpassword"
              }
            ]
          }
        }
   ```
    
   * 그래프 인스턴스 및 비디오 흐름을 시작하는 `GraphInstanceActivate`에 대한 호출입니다.
   * 그래프 인스턴스가 실행 중 상태임을 보여주는 `GraphInstanceList`에 대한 두 번째 호출입니다.
    
1. **터미널** 창의 출력이 **계속하려면 Enter를 누르세요** 라는 메시지에서 일시 중지됩니다. 아직 **Enter** 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Live Video Analytics on IoT Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 자습서의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 미디어 그래프가 계속 실행되어 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 미디어 그래프를 중지하려면 **터미널** 창으로 돌아가서 **Enter** 키를 선택합니다.
일련의 다음 호출은 리소스를 정리합니다.
    
   * `GraphInstanceDeactivate`에 대한 호출은 그래프 인스턴스를 비활성화합니다.
   * `GraphInstanceDelete`에 대한 호출은 인스턴스를 삭제합니다.
   * `GraphTopologyDelete`에 대한 호출은 토폴로지를 삭제합니다.
   * `GraphTopologyList`에 대한 최종 호출은 목록이 비어 있음을 보여 줍니다.
    
## <a name="interpret-the-results"></a>결과 해석

미디어 그래프를 실행하면 HTTP 확장 프로세서 노드의 결과가 IoT Hub 싱크 노드를 통해 IoT 허브로 전달됩니다. **출력** 창에 표시되는 메시지에는 본문 섹션과 `applicationProperties` 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Live Video Analytics 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

미디어 그래프가 인스턴스화되면 RTSP 원본 노드가 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다. 이벤트 유형은 **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished** 입니다.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

이 메시지에서 다음 세부 정보를 확인합니다.

* 메시지는 진단 이벤트입니다. `MediaSessionEstablished`는 RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결하고 시뮬레이션된 라이브 피드를 받기 시작했음을 나타냅니다.
* `applicationProperties`에서 `subject`는 메시지가 미디어 그래프의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* `applicationProperties`에서 이벤트 유형은 이 이벤트가 진단 이벤트임을 나타냅니다.
* 이벤트 시간은 이벤트가 발생한 시간을 나타냅니다.
* 본문에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="inference-event"></a>유추 이벤트

HTTP 확장 프로세서 노드는 Custom Vision 컨테이너에서 유추 결과를 받고 IoT Hub 싱크 노드를 통해 결과를 유추 이벤트로 내보냅니다.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

이전 메시지에서 다음 정보를 확인합니다.

* `applicationProperties`의 제목은 메시지가 생성된 MediaGraph의 노드를 참조합니다. 이 때 메시지는 HTTP 확장 프로세서에서 시작됩니다.
* `applicationProperties`의 이벤트 유형은 이 이벤트가 분석 유추 이벤트임을 나타냅니다.
* 이벤트 시간은 이벤트가 발생한 시간을 나타냅니다.
* 본문에는 분석 이벤트에 대한 데이터가 포함됩니다. 여기서는 이벤트가 유추 이벤트이므로 본문에는 예측이라는 유추의 배열이 포함됩니다.
* 예측 섹션에는 프레임에서 장난감 배달 트럭(태그는 "배달 트럭")이 발견된 예측 목록이 포함되어 있습니다. 회수할 때 "배달 트럭"은 장난감 트럭의 사용자 지정 학습된 모델에 제공한 사용자 지정 태그입니다. 모델은 입력 비디오에서 확률 신뢰도 점수가 다른 장난감 트럭을 추론하고 식별합니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 자습서 또는 빠른 시작을 진행하려면 지금까지 만든 리소스를 그대로 둡니다. 다른 자습서를 진행할 생각이 없으면 Azure Portal로 이동하여 해당하는 리소스 그룹을 찾고, 이 자습서를 실행한 리소스 그룹을 선택하고, 모든 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

고급 사용자에 대한 추가 문제를 검토합니다.

* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격](https://www.onvif.org/conformant-products/) 제품 페이지에서 RTSP를 지원하는 IP 카메라를 검색할 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
* Azure Linux VM 대신 AMD64 또는 x64 Linux 디바이스를 사용합니다. 이 디바이스는 IP 카메라와 동일한 네트워크에 있어야 합니다. [Linux에 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-provision-single-device-linux-symmetric.md)의 지침을 따릅니다.

그런 다음, [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)의 지침에 따라 디바이스를 Azure IoT Hub에 등록합니다.
