---
title: gRPC 유추 서버 개발 및 배포 - Azure Video Analyzer
description: 이 문서에서는 Azure Video Analyzer와 함께 사용할 gRPC 유추 서버를 개발하고 배포하는 방법에 대한 지침을 제공합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 004b80f77d4fdd410ab3530315990d5ad0132e5c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434937"
---
# <a name="develop-and-deploy-grpc-inference-server"></a>gRPC 유추 서버 개발 및 배포

[!INCLUDE [header](includes/edge-env.md)]

이 문서에서는 파이프라인 확장을 통해 AVA(Azure Video Analyzer)와 통합할 수 있도록 gRPC 유추 서버 내에 선택한 AI 모델을 래핑하는 방법을 보여 줍니다.

## <a name="suggested-pre-reading"></a>추천 참고 자료

* 파이프라인 확장
* gRPC 확장 프로토콜
* 유추 메타데이터 스키마
* [gRPC 소개](https://www.grpc.io/docs/what-is-grpc/introduction/)
* [proto3 언어 가이드](https://developers.google.com/protocol-buffers/docs/proto3)

## <a name="prerequisites"></a>필수 구성 요소

* [지원되는 Linux 운영 체제](../../../iot-edge/support.md#operating-systems) 또는 Windows 머신 중 하나를 실행하는 x86-64 또는 ARM64 디바이스
* 머신에 [Docker를 설치](https://docs.docker.com/desktop/#download-and-install)합니다.
* [IoT Edge 런타임](../../../iot-edge/how-to-install-iot-edge.md?tabs=linux)을 설치합니다.

## <a name="grpc-implementation-steps"></a>gRPC 구현 단계

gRPC 유추 서버를 만들어 Video Analyzer를 통해 확장으로 구현하려면 다음 단계를 수행합니다.

### <a name="setup-video-analyzer-module"></a>Video Analyzer 모듈 설치

IoT Edge 디바이스에서 Video Analyzer 모듈을 배포하고 작동하는 데 필요한 단계를 수행합니다.

> [!NOTE]
> 배포 매니페스트는 태그를 1 (비디오-분석기: 1)로 사용 합니다. 따라서 이러한 매니페스트를 다시 배포하면 최신 태그가 릴리스될 때 에지 디바이스의 모듈이 업데이트되어야 합니다.

### <a name="high-level-implementation-steps"></a>개략적인 구현 단계

1. gRPC에서 지원되는 여러 언어인 C#, C++, Dart, Go, Java, Node, Objective-C, PHP, Python, Ruby 중 하나를 선택합니다.
1. [proto3 파일](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)을 사용하여 Video Analyzer와 통신하는 gRPC 서버를 구현합니다.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/inference-srv-container-process.svg" alt-text="proto3 파일을 사용하여 Video Analyzer와 통신하는 gRPC 서버":::

    이 서비스 내에서 다음을 수행합니다.
    1. 서버와 클라이언트 간의 세션 설명 메시지 교환을 처리합니다.
    1. [샘플 메시지](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto)를 처리하고 결과를 반환합니다.

        1. 학습된 모델을 사용하는 유추 엔진을 호출하여 들어오는 메시지에 대해 유추를 만듭니다.
        1. 엔진에서 유추 결과를 수신하고 미디어 샘플로 다시 패키지한 다음, [inferencing.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/inferencing.proto) 파일을 사용하여 Video Analyzer로 다시 제출합니다.

            또는 미디어 변환 함수를 미디어 샘플로 호출합니다.
    1. gRPC 서버 구현을 배포합니다. 이 작업은 다음 두 가지 방법으로 수행합니다.

        1. Video Analyzer 모듈과 함께 배치되는 IoT 모듈로 배포
        1. Video Analyzer 모듈과 데이터를 교환할 수 있는 네트워크 액세스 가능 노드(온-프레미스 또는 클라우드)에 IoT 모듈로 배포합니다.
    1. Video Analyzer 모듈을 사용하여 Video Analyzer 파이프라인 토폴로지를 구성하고 gRPC 서버를 가리킵니다.

### <a name="recommendation"></a>권장

동일한 노드에 배치할 경우 최상의 성능을 위해 `shared memory`를 사용할 수 있습니다. 이렇게 하려면 프로그래밍 언어/환경에서 노출하는 Linux 공유 메모리 기능을 사용해야 합니다.

1. Linux 공유 메모리 핸들을 엽니다.
1. 프레임이 수신되면 공유 메모리 내의 주소 오프셋에 액세스합니다.
1. Video Analyzer에서 메모리를 회수할 수 있도록 프레임 처리 완료를 승인합니다.

## <a name="create-a-grpc-inference-server"></a>gRPC 유추 서버 만들기

이제 공유 메모리를 통해 [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) 메시지를 사용하여 Video Analyzer의 비디오 프레임을 수락하는 IoT Edge 모듈(외부 AI)을 빌드하고, 프레임을 **dark** 또는 **light** 로 분류한 후 유추 메타데이터 스키마를 사용하여 Video Analyzer의 IoT Hub 메시지 싱크로 유추 결과를 반환합니다.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv/external-ai.png" alt-text="IoT Edge 모듈(외부 AI) 빌드":::

이 gRPC 유추 서버는 Video Analyzer와 사용자 지정 AI 간에 전송되는 [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) 메시지를 처리하도록 빌드된 .NET Core 콘솔 애플리케이션입니다. 다음은 Video Analyzer와 gRPC 유추 서버 간의 메시지 흐름입니다.

1. Video Analyzer에서 미디어 스트림 설명자([extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto))를 전송하면 이 설명자는 미디어 스트림 정보를 정의합니다. 이 정보와 비디오 프레임이 차례로 gRPC 스트림 세션을 통해 [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) 메시지로서 서버로 전송됩니다.
1. 서버는 스트림 설명자의 유효성을 검사하고 승인하며, 원하는 데이터 전송 방법을 설정합니다.
1. 그러면 Video Analyzer가 비디오 프레임을 포함하는 MediaSample 파일을 보내기 시작합니다.
1. 서버는 수신되는 비디오 프레임을 분석하고 사용자가 정의한 이미지 프로세서를 사용하여 비디오 프레임을 처리하기 시작합니다.
1. 그런 다음, 서버는 유추 결과를 사용할 수 있게 되는 즉시 [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) 메시지로 반환합니다.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/grpc-external-srv.png" alt-text="gRPC 유추 서버 만들기":::

비디오 프레임은 [공유 메모리](https://en.wikipedia.org/wiki/Shared_memory#:~:text=In%20computer%20science%2C%20shared%20memory,of%20passing%20data%20between%20programs.)를 통해 전송하거나 protobuf 메시지 내에 포함할 수 있습니다. 데이터 전송 모드는 프레임 전송 방법을 결정하기 위해 AVA 파이프라인 토폴로지에서 구성할 수 있습니다. 이렇게 하려면 아래와 같이 `GrpcExtension` 속성의 **dataTransfer** 요소를 구성합니다.

Embedded:

```
"dataTransfer": {
              "mode": "Embedded"
            }
```

공유 메모리:

```
"dataTransfer": {
              "mode": "sharedMemory",
              "SharedMemorySizeMiB": "20"
            }
```

> [!NOTE]
> 공유 메모리를 통해 통신할 때 IpcMode 값은 **공유 가능** 으로 설정되고 gRPC 서버 모듈에서 IpcMode 값은 으로 설정해야 합니다. 이러한 설정은 모듈을 Azure IoT Hub 배포하는 데 사용되는 배포 매니페스트 파일에서 수행해야 합니다. 다음은 IoT Edge 모듈을 설정할 때 사용할 컨테이너 옵션의 샘플입니다.

Azure Video Analyzer 모듈:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "shareable"
    }
}
```

gRPC 확장 모듈:

```
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "10m",
                "max-file": "10"
            }
        },
        "IpcMode": "container:avaedge"
    }
}
```

> [!NOTE]
> grpcExtension 내에서 **container:avaedge** 의 공유 메모리 영역에 액세스할 수 있는지 확인합니다.

## <a name="sample-grpc-server"></a>샘플 gRPC 서버

gRPC 서버를 개발하는 방법에 대한 세부 정보를 이해하기 위해 코드 샘플을 살펴보겠습니다.

1. GitHub 링크([https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp))의 리포지토리를 복제합니다.
1. VSCode를 시작하고/src/edge/modules/grpcExtension 폴더로 이동합니다.
1. 파일로 간단한 연습을 수행해 보겠습니다.

    1. **Program .cs**: 애플리케이션의 진입점입니다. 호스트 역할을 하는 gRPC 서버를 초기화하고 관리하는 일을 담당합니다. 이 샘플에서 gRPC 클라이언트(예: Azure Video Analyze)에서 들어오는 gRPC 메시지를 수신 대기하는 포트는 appConfig.json의 grpcBindings 구성 요소를 사용하여 지정합니다.

        ```json
        {
          "grpcBinding": "tcp://0.0.0.0:5001"
        }
        ```
    1. **Services\PipelineExtensionService.cs**: 이 클래스는 [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) 메시지를 처리하는 일을 담당합니다. 메시지의 프레임을 읽고 ImageProcessor를 호출하고 유추 결과를 씁니다.
      이제 gRPC 서버 포트 연결을 구성하고 초기화했으므로 들어오는 gRPC 메시지를 처리할 수 있는 방법을 살펴보겠습니다.

        1. gRPC 세션이 설정되면 gRPC 서버에서 클라이언트로부터 수신하는 첫 번째 메시지(Azure Video Analyzer)는 [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto)에 정의된 MediaStreamDescriptor입니다.

            ```
            message MediaStreamDescriptor {
              oneof stream_identifier {
                GraphIdentifier graph_identifier = 1;     // Media Stream graph identifier
                string extension_identifier = 2;          // Media Stream extension identifier
              }
              MediaDescriptor media_descriptor = 5;       // Session media information.
              // Additional data transfer properties. If none is set, it is assumed
              // that all content will be transferred through messages (embedded transfer).
              oneof data_transfer_properties {
                SharedMemoryBufferTransferProperties shared_memory_buffer_transfer_properties = 10;
              }
            }
            ```
        1. 서버 구현에서 메서드 `ProcessMediaStreamDescriptor`는 Video 파일에 대해 MediaStreamDescriptor의 MediaDescriptor 속성이 유효한지 검사한 다음, 토폴로지 및 사용되는 배포 템플릿 파일에 지정하는 내용에 따라 데이터 전송 모드(공유 메모리를 사용하거나 포함된 프레임 전송 모드 사용)를 설정합니다.
        1. 메시지를 받고 데이터 전송 모드를 성공적으로 설정하면 gRPC 서버는 승인을 나타내기 위해 MediaStreamDescriptor 메시지를 클라이언트로 다시 반환하여 서버와 클라이언트 간에 연결을 설정합니다.
        1. Video Analyzer는 승인을 받으면 gRPC 서버로 미디어 스트림을 전송하기 시작합니다. 서버 구현에서 메서드 `ProcessMediaStream`은 들어오는 MediaStreamMessage를 처리합니다. MediaStreamMessage는 [extension.proto](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc/extension.proto)에도 정의됩니다.

            ```
            message MediaStreamMessage {
              uint64 sequence_number = 1;       // Monotonically increasing directional message identifier starting from 1 when the gRPC connection is created
              uint64 ack_sequence_number = 2;   // 0 if this message is not referencing any sent message.


              // Possible payloads are strongly defined by the contract below
              oneof payload {
                MediaStreamDescriptor media_stream_descriptor = 5;
                MediaSample media_sample = 6;
              }
            }
            ```
        1. *appconfig.json* 의 `batchSize` 값에 따라 서버는 메시지를 계속 받고 비디오 프레임을 목록에 저장합니다. btchSize 제한에 도달하면 함수는 이미지를 처리하는 파일 또는 함수를 호출합니다. 이 경우 메서드는 **BatchImageProcessor.cs** 라는 파일을 호출합니다.
    1. **Processors\BatchImageProcessor.cs**: 클래스는 이미지를 처리하는 일을 담당합니다. 이 샘플에서는 이미지 분류 모델을 사용했습니다. 처리되는 모든 이미지에 대해 사용되는 알고리즘은 다음과 같습니다.

        1. 처리를 위해 바이트 배열의 이미지를 변환합니다. `GetBytes(Bitmap image)` 메서드를 참조하세요.

            사용 중인 샘플 프로세서가 JPG 인코딩 이미지 프레임만 지원하고 픽셀 형식으로는 어떤 것도 지원하지 않습니다. 사용자 지정 프로세서가 다른 인코딩 및/또는 형식을 지원하는 경우 프로세서 클래스의 `IsMediaFormatSupported` 메서드를 업데이트합니다.
        1. [ColorMatrix 클래스](/dotnet/api/system.drawing.imaging.colormatrix?preserve-view=true&view=dotnet-plat-ext-3.1)를 사용하여 이미지를 회색조로 변환합니다. `ToGrayScale(Image source)` 메서드를 참조하세요.
        1. 회색조 이미지를 가져온 후에는 회색조 바이트의 평균을 계산합니다.
        1. 평균 값이 127 미만인 경우 이미지를 "dark"로 분류하고, 그렇지 않은 경우 신뢰도 값이 1.0인 "light"로 분류합니다. `ProcessImage(List<Image> images)` 메서드를 참조하세요.

    이 클래스를 수정하거나 새 클래스를 추가한 후 메서드를 구현하여 사용자 고유의 프로세서 논리를 추가할 수 있습니다.

    ```
    IEnumerable<Inference> ProcessImage(List<Image> images)
    ```

    새 클래스를 추가한 후에는 **PipelineExtensionService.cs** 를 업데이트하여 클래스를 인스턴스화하고 처리 논리를 실행하기 위해 ProcessImage 메서드를 호출해야 합니다.

## <a name="connect-with-video-analyzer-module"></a>Video Analyzer 모듈과 연결

이제 gRPC 확장 모듈을 만들었으므로 파이프라인 토폴로지를 만들어 배포할 것입니다.

1. Visual Studio Code를 사용하여 [이러한 지침](../../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution)에 따라 Docker에 로그인합니다.
1. Visual Studio Code에서 *src/edge* 로 이동합니다. *env* 파일 및 몇 가지 배포 템플릿 파일이 표시됩니다.

    **배포 템플릿** 은 에지 디바이스에 대한 배포 매니페스트를 나타냅니다. 일부 자리 표시자 값을 포함합니다. .env 파일에는 이러한 변수에 대한 값이 포함되어 있습니다.

    그런 다음, **IoT Edge 솔루션 빌드 및 푸시** 를 선택합니다. 이 단계에서는 *src/edge/deployment.grpc.template.json* 을 사용합니다.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/build-push-iot-edge-solution.png" alt-text="Video Analyzer 모듈과 연결":::

    이 작업은 grpc 서버 모듈을 빌드하고 이미지를 Azure Container Registry로 푸시합니다.
    *.env* 파일에 환경 변수 `CONTAINER_REGISTRY_USERNAME_myacr` 및 `CONTAINER_REGISTRY_PASSWORD_myacr`이 정의되어 있는지 확인합니다.
1. *src/cloud-to-device-console-app* 폴더로 이동합니다. 여기에는 *appsettings.json* 파일 및 몇 가지 다른 파일이 표시됩니다.

    * **c2d-console-app.csproj** - Visual Studio Code에 대한 프로젝트 파일
    * **operations.json** - 프로그램을 실행하려는 작업의 목록입니다.
    * **Program.cs** - 샘플 프로그램 코드입니다. 이 코드에서는 다음을 수행합니다.

        * 앱 설정을 로드합니다.
        * Azure Video Analyzer 모듈에서 공개하는 직접 메서드를 호출합니다. 모듈을 사용하여 해당 직접 메서드를 호출하여 라이브 비디오 스트림을 분석할 수 있습니다.
        * 터미널 창에서 프로그램의 출력을 검사하고, 출력 창에서 모듈에서 생성된 이벤트를 검사할 수 있도록 일시 중지합니다.
        * 리소스를 정리하는 직접 메서드를 호출합니다.
1. operations.json 파일을 편집합니다.

    * 파이프라인 토폴로지의 링크를 변경합니다.

        * `"topologyUrl" : https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json`
        * `livePipelineSet` 아래에서 이전 링크의 값과 일치하도록 파이프라인 토폴로지의 이름을 편집합니다.<br/>`"topologyName": "EVRtoVideoSinkByGrpcExtension"`
        * `pipelineTopologyDelete` 아래에서 이름을 편집합니다.<br/>`"name": "EVRtoVideoSinkByGrpcExtension"`

    토폴로지는 확장 주소를 정의해야 합니다. 예를 들어 매개 변수를 만들고 GrpcExtension 노드 내에서 사용할 수 있습니다.
    * 확장 주소 매개 변수

        ```
        {
            "name": "grpcExtensionAddress",
            "type": "String",
            "description": "gRPC AVA Extension Address",
            "default": "https://<REPLACE-WITH-IP-OR-CONTAINER-NAME>/score"
        },
        ```
    * 구성

        ```
        {
            "@apiVersion": "1.1",
            "name": "TopologyName",
            "properties": {
            "processors": [
              {
                "@type": "#Microsoft.VideoAnalyzer.GrpcExtension",
                "name": "grpcExtension",
                "endpoint": {
                  "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                  "url": "${grpcExtensionAddress}",
                  "credentials": {
                    "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                    "username": "${grpcExtensionUserName}",
                    "password": "${grpcExtensionPassword}"
                  }
                },
                "dataTransfer": {
                        "mode": "sharedMemory",
                        "SharedMemorySizeMiB": "75"
                },
                "image": {
                  "scale": {
                    "mode": "${imageScaleMode}",
                    "width": "${frameWidth}",
                    "height": "${frameHeight}"
                  },
                  "format": {
                    "@type": "#Microsoft.VideoAnalyzer.ImageFormatEncoded",
                    "encoding": "${imageEncoding}",
                    "quality": "${imageQuality}"
                  }
                }
            ]
          }
        }
        ```

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트 생성 및 배포

배포 매니페스트는 에지 디바이스에 배포되는 모듈 및 해당 모듈의 구성 설정을 정의합니다. 다음 단계에 따라 템플릿 파일에서 매니페스트를 생성한 다음, 에지 디바이스에 배포합니다.
1. 이 단계에서는 *src/edge/config/deployment.grpc.amd64.json* 에 IoT Edge 배포 매니페스트를 만듭니다. 마우스 오른쪽 단추로 해당 파일을 클릭하고 **단일 디바이스용 배포 만들기** 를 선택합니다.

    :::image type="content" source="./media/develop-deploy-grpc-inference-srv/create-deployment-single-device.png" alt-text="IoT Edge 배포 매니페스트 생성 및 배포":::

1. 다음으로, Visual Studio Code에서 IoT Hub 디바이스를 선택하라는 메시지를 표시합니다. IoT Hub 디바이스(`avasample-iot-edge-device`)를 선택합니다.
이 단계에서는 IoT Edge 디바이스에 에지 모듈을 배포하는 작업이 시작되었습니다. 약 30초 후에 Visual Studio Code의 왼쪽 아래 섹션에서 Azure IoT Hub를 새로 고칩니다. 새 모듈이 avaextension이라는 이름으로 배포된 것을 볼 수 있습니다.

:::image type="content" source="./media/develop-deploy-grpc-inference-srv/devices.png" alt-text="avaextension이라는 이름으로 배포된 새 모듈":::

## <a name="next-steps"></a>다음 단계

* 모델을 사용하여 라이브 비디오 분석 빠른 시작에 설명된 **이벤트 모니터링 준비** 단계에 따라 샘플을 실행하고 결과를 해석합니다.
* 또한 [gRPCExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json), [CVRWithGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json), [EVRtoAssetsByGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json) 및 [EVROnMotionPlusGrpcExtension](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/motion-with-grpcExtension/topology.json) 샘플 gRPC 토폴로지도 확인해 보세요.
