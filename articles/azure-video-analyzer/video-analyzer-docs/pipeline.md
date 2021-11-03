---
title: Azure Video Analyzer 파이프라인
description: Azure video Analyzer edge 및 클라우드 내에서 비디오를 수집 하 고, 처리 하 고, 게시 하는 Azure 비디오 분석기 파이프라인. 파이프라인은 원하는 데이터 흐름을 얻기 위해 연결된 노드로 구성됩니다.
ms.topic: conceptual
ms.date: 10/31/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 53ac9631155d35972546f937d872d1f48b476b52
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073898"
---
# <a name="pipeline"></a>파이프라인

Pipelines를 사용 하 여 Azure video Analyzer edge 및 cloud 내에서 비디오를 수집 하 고, 처리 하 고, 게시할 수 있습니다. 파이프라인 토폴로지를 사용 하면 구성 가능한 노드 집합을 통해 내에서 비디오를 수집 처리 하 고 게시 하는 방법을 정의할 수 있습니다. 일단 정의 되 면 토폴로지는 독립적으로 처리 되는 특정 카메라 또는 소스 콘텐츠를 대상으로 하는 개별 파이프라인으로 인스턴스화할 수 있습니다. 온-프레미스 비디오 처리를 위한 edge에서 또는 클라우드에서 정의 하 고 인스턴스화할 수 Pipelines. 아래 다이어그램에서는 이러한 파이프라인을 그래픽으로 표현 합니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/pipeline-representation.svg" alt-text="파이프라인의 표현":::

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/cloud-pipeline.svg" alt-text="클라우드 파이프라인의 표현":::

## <a name="suggested-pre-reading"></a>추천 참고 자료

* [개요](overview.md)
* [용어](terminology.md)

## <a name="pipeline-topologies"></a>파이프라인 토폴로지

파이프라인 토폴로지를 사용 하면 연결 된 노드 집합을 통해 사용자 지정 요구에 대해 라이브 비디오 또는 [녹화 된 비디오](terminology.md#recording) 를 처리 하 고 분석 하는 방법을 설명할 수 있습니다. 라이브 비디오 워크플로에 대 한 템플릿 또는 청사진으로 사용 됩니다. **비디오 분석기는 두 종류의 토폴로지 라이브 및 일괄 처리를 지원 합니다. 이름을 통해 알 수 있듯이 라이브 토폴로지는 카메라의 라이브 비디오와 함께 사용 됩니다. 일괄 처리 토폴로지는 기록 된 비디오를 처리 하는 데 사용 됩니다.**

**파이프라인** 은 여러 유형의 노드를 지원 합니다.

* **원본 노드** 는 데이터를 파이프라인으로 캡처할 수 있습니다. 데이터는 오디오, 비디오 및/또는 메타데이터를 참조합니다.
* **프로세서 노드** 는 파이프라인 내의 미디어를 처리할 수 있습니다.
* **싱크 노드** 는 파이프라인 외부의 서비스 및 앱에 결과를 제공할 수 있습니다.

토폴로지의 노드 종류, 노드를 연결하는 방법을 선택하고 매개 변수를 값의 자리 표시자로 사용하여 다양한 시나리오에 대한 여러 토폴로지를 만들 수 있습니다. 파이프라인은 특정 파이프라인 토폴로지의 개별 인스턴스입니다. 파이프라인은 미디어가 실제로 처리되는 위치입니다. Pipelines은 파이프라인 토폴로지에서 선언 된 사용자 정의 매개 변수를 통해 개별 카메라 또는 녹화 된 비디오와 연결할 수 있습니다. **라이브 토폴로지의 인스턴스는 라이브 파이프라인 이라고 하며, 일괄 처리 토폴로지의 인스턴스를 파이프라인 작업 이라고 합니다.**

예를 들어 여러 IP 카메라에서 비디오를 녹화하려면 RTSP 원본 노드와 비디오 싱크 노드로 구성된 파이프라인 토폴로지를 정의하면 됩니다. RTSP 원본 노드는 RTSP URL, 사용자 이름 및 암호를 매개 변수로 사용할 수 있습니다. 비디오 싱크 노드는 비디오 이름을 매개 변수로 사용할 수 있습니다. 동일한 토폴로지에서 여러 파이프라인을 만들 때(카메라마다 파이프라인 하나씩) 이러한 매개 변수의 값을 제공할 수 있습니다.

Batch 토폴로지는 video analyzer edge 모듈이 아니라 Video Analyzer 서비스 에서만 지원 됩니다. 라이브 파이프라인은 둘 다에서 지원 됩니다.

## <a name="pipeline-states"></a>파이프라인 상태

파이프라인 토폴로지 만들기부터 시작합니다. 토폴로지가 정의되면 매개 변수 값을 제공하여 파이프라인을 만들 수 있습니다.

### <a name="live-pipeline"></a>라이브 파이프라인

파이프라인의 수명 주기는 아래 다이어그램에 표시 됩니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/pipeline-activation.svg" alt-text="라이브 파이프라인의 수명 주기":::

만들기가 완료 되 면 파이프라인이 ' 비활성 ' 상태입니다. 활성화 되 면 파이프라인이 ' 활성 ' 상태로 전환 되기 전에 ' 활성화 ' 상태로 잠시 들어갑니다.
데이터 (라이브 비디오)가 ' 활성 ' 상태인 경우 파이프라인을 통해 이동 하기 시작 합니다. 비활성화 되 면 활성 파이프라인이 ' 비활성화 ' 상태와 ' 비활성 ' 상태로 전환 됩니다. 비활성 파이프라인만 삭제할 수 있습니다.

라이브 파이프라인은 활성 상태로 유지 되 고, 활성화 되 고 나면 원본 (카메라)에서 라이브 비디오를 계속 처리 하도록 설계 되었습니다. 처리를 중지 하려면 명시적인 비활성화 명령이 필요 합니다.
파이프라인은 데이터가 흐르지 않아도(예: 입력 비디오 원본이 오프라인 상태) 활성 상태일 수 있습니다. 파이프라인이 활성 상태이면 Azure 구독 요금이 청구됩니다.

### <a name="batch-pipeline"></a>일괄 처리 파이프라인

파이프라인 작업의 수명 주기는 아래 다이어그램에 표시 됩니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline/batch-pipeline-lifecycle.svg" alt-text="일괄 처리 파이프라인 수명 주기 다이어그램":::

파이프라인 작업이 성공적으로 만들어지면 ' Processing ' 상태가 됩니다. 작업이 성공적으로 완료 되 면 ' 완료 됨 ' 상태가 되 고, 실패 하면 ' 실패 ' 상태로 전환 됩니다. 또는 파이프라인 작업이 ' Processing ' 상태에 있는 동안에는 취소 요청을 실행할 수 있습니다. 해당 요청이 성공 하면 작업이 ' 취소 됨 ' 상태로 전환 됩니다. 파이프라인 작업이 성공적으로 완료 되는 경우에만 Azure 구독에 대 한 요금이 청구 됩니다.

토폴로지의 매개 변수에 여러 값을 제공하여 단일 토폴로지에서 여러 파이프라인을 만들 수 있습니다. 예를 들어 다른 비디오 녹화에 대해 동일한 토폴로지를 사용 하 여 파이프라인 작업을 제출할 수 있습니다. 모든 파이프라인을 삭제한 후 토폴로지를 삭제할 수 있습니다.

## <a name="sources-processors-and-sinks"></a>원본, 프로세서 및 싱크

Video Analyzer를 사용 하면 다음 노드로 파이프라인 토폴로지를 정의할 수 있습니다.

> [!NOTE]
> 모든 노드가 Video Analyzer edge 모듈 및 서비스에서 사용할 수 있는 것은 아닙니다. [노드 사용에 대 한 규칙을](pipeline.md#rules-on-the-use-of-nodes)참조 하세요.

### <a name="sources"></a>원본

#### <a name="rtsp-source"></a>RTSP 원본

Rtsp 원본 노드를 사용 하 여 RTSP 가능 카메라에서 미디어를 캡처할 수 있습니다. 자세한 내용은 [여기](quotas-limitations.md#supported-cameras) 를 참조 하세요. RTSP 원본 노드에서는 인증된 연결을 사용하도록 설정하는 자격 증명과 함께 RTSP URL을 지정해야 합니다.

#### <a name="iot-hub-message-source"></a>IoT Hub 메시지 원본

다른 [IoT Edge 모듈](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-device)과 마찬가지로 Azure Video Analyzer 모듈은 [IoT Edge 허브](../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub)를 통해 메시지를 받을 수 있습니다. 메시지는 Edge 디바이스나 클라우드에서 실행되는 다른 모듈이나 앱에서 보낼 수 있습니다. 이러한 메시지는 비디오 분석기 모듈의 [명명된 입력](../../iot-edge/module-composition.md?view=iotedge-2020-11&preserve-view=true#sink)으로 전달(라우팅)할 수 있습니다. IoT Hub 메시지 원본 노드는 이러한 메시지를 파이프라인으로 수집할 수 있습니다. 그런 다음, 파이프라인에서 메시지를 사용하여 신호 게이트를 활성화할 수 있습니다(아래의 [신호 게이트](#signal-gate-processor) 참조).

예를 들어 문이 열릴 때 메시지를 생성하는 IoT Edge 모듈을 사용할 수 있습니다. 해당 모듈의 메시지를 IoT Edge 허브로 라우팅한 다음, 파이프라인의 IoT 허브 메시지 원본으로 라우팅할 수 있습니다. 파이프라인 내에서 IoT 허브 메시지 원본의 메시지를 신호 게이트 프로세서에 전달할 수 있습니다. 그러면 RTSP 원본에서 파일로 비디오 녹화를 켤 수 있습니다.

#### <a name="video-source"></a>비디오 소스

비디오 분석기 녹화 된 비디오 콘텐츠를 원본으로 사용할 수 있습니다. 이 노드에서는 [비디오 리소스](terminology.md#video)의 이름과 처리 되는 녹화 된 비디오 부분의 시작 및 종료 시간을 지정 해야 합니다.

### <a name="processors"></a>프로세서

#### <a name="motion-detection-processor"></a>동작 감지 프로세서

동작 감지 프로세서 노드를 사용하면 라이브 비디오에서 동작을 감지할 수 있습니다. 수신되는 비디오 프레임을 검사하고 비디오에서 이동이 있는지 확인합니다. 동작이 감지되면 비디오 프레임을 파이프라인의 다음 노드에 전달하고 이벤트를 내보냅니다. 동작 감지 프로세서 노드(다른 노드와 함께)를 사용하여 동작이 감지되면 수신되는 비디오의 녹화를 트리거할 수 있습니다.

#### <a name="http-extension-processor"></a>HTTP 확장 프로세서

HTTP 확장 프로세서 노드는 파이프라인을 자체 IoT Edge 모듈로 확장할 수 있습니다. 이 노드는 디코딩된 비디오 프레임을 입력으로 사용하며, 이러한 프레임을 모듈에서 노출하는 HTTP REST 엔드포인트에 릴레이합니다. 여기서 AI 모델을 사용하여 프레임을 분석하고 유추 결과를 반환할 수 있습니다. 또한 이 노드에는 비디오 프레임을 HTTP 엔드포인트로 릴레이하기 전에 스케일링하고 인코딩하기 위한 기본 제공 이미지 포맷터가 포함되어 있습니다. 스케일러는 이미지 가로 세로 비율을 유지하거나 패딩하거나 늘일 수 있는 옵션을 제공합니다. 이미지 인코더는 JPEG, PNG, BMP 및 RAW 형식을 지원합니다. [여기서 프로세서](pipeline-extension.md#http-extension-processor)에 대해 자세히 알아보세요.

#### <a name="grpc-extension-processor"></a>gRPC 확장 프로세서

gRPC 확장 프로세서 노드는 디코딩된 비디오 프레임을 입력으로 가져오고, 이러한 프레임을 모듈에서 노출하는 [gRPC](terminology.md#grpc) 엔드포인트로 릴레이합니다. 이 노드는 [공유 메모리](https://en.wikipedia.org/wiki/Shared_memory)를 사용하여 데이터를 전송하거나 프레임을 gRPC 메시지의 본문에 직접 포함할 수 있도록 지원합니다. HTTP 확장 프로세스와 마찬가지로 이 노드에도 비디오 프레임을 gRPC 엔드포인트로 릴레이하기 전에 스케일링하고 인코딩하기 위한 기본 제공 이미지 포맷터가 포함되어 있습니다. [여기서 프로세서](pipeline-extension.md#grpc-extension-processor)에 대해 자세히 알아보세요.

#### <a name="cognitive-services-extension-processor"></a>Cognitive Services 확장 프로세서

Cognitive Services 확장 프로세서 노드는 파이프라인을 [공간 분석](https://azure.microsoft.com/services/cognitive-services/computer-vision/) IoT Edge 모듈로 확장할 수 있습니다. 이 노드는 디코딩된 비디오 프레임을 입력으로 사용하며, 이러한 프레임을 모듈에서 노출하는 [gRPC](pipeline-extension.md#grpc-extension-processor) 엔드포인트에 릴레이합니다. 여기서 공간 분석 기술을 사용하여 프레임을 분석하고 유추 결과를 반환할 수 있습니다. [여기서 프로세서](pipeline-extension.md#cognitive-services-extension-processor)에 대해 자세히 알아보세요.

#### <a name="signal-gate-processor"></a>신호 게이트 프로세서

신호 게이트 프로세서 노드를 사용하면 한 노드에서 다른 노드로 미디어를 조건부로 전달할 수 있습니다. 신호 게이트 프로세서 노드 바로 뒤에 비디오 싱크 또는 파일 싱크가 있어야 합니다. 예제 사용 사례는 RTSP 원본 노드와 비디오 싱크 노드 사이에 신호 게이트 프로세서 노드를 삽입하고 동작 감지기 프로세서 노드의 출력을 사용하여 게이트를 트리거하는 것입니다. 이러한 파이프라인을 사용하면 동작이 감지된 경우에만 비디오를 녹화할 수 있습니다. 또한 동작 감지 프로세서 노드 대신 HTTP 또는 gRPC 확장 노드의 출력을 사용하여 게이트를 트리거할 수 있으므로 흥미로운 항목이 감지될 때 비디오를 녹화할 수 있습니다.

#### <a name="object-tracker-processor"></a>개체 추적기 프로세서

개체 추적기 프로세서 노드는 업스트림 HTTP 또는 gRPC 확장 프로세서 노드에서 감지된 개체를 추적할 수 있습니다. 이 노드는 모든 프레임의 개체를 감지해야 하는 경우에 유용하지만, 에지 디바이스는 모든 프레임에 AI 모델을 적용하는 데 필요한 컴퓨팅 성능을 갖고 있지 않습니다. 컴퓨터 비전 모델을 10번째 프레임마다 실행할 수 있는 경우 개체 추적기는 이러한 프레임의 결과를 가져온 다음, [광학 흐름](https://en.wikipedia.org/wiki/Optical_flow) 기술을 사용하여 모델이 다음 프레임에서 다시 적용될 때까지 2번째, 3번째,..., 9번째 프레임에 대한 결과를 생성할 수 있습니다. 이 노드를 사용하는 경우 컴퓨팅 성능과 정확도 사이에 득실이 있습니다. AI 모델이 적용되는 프레임이 가까울수록 정확도가 향상됩니다. 그러나 AI 모델을 더 자주 적용하므로 더 높은 컴퓨팅 성능이 필요하다는 의미입니다. 개체 추적기 프로세서 노드의 일반적인 용도는 개체가 선을 통과할 때 감지하는 것입니다.

#### <a name="line-crossing-processor"></a>선 통과 프로세서

선 통과 프로세서 노드는 개체가 사용자 정의 선을 통과하는 것을 감지할 수 있습니다. 뿐만 아니라 선을 통과하는 개체의 수를 유지합니다(파이프라인이 활성화되는 시간부터). 이 노드는 개체 추적 장치 프로세서 노드의 다운스트림에 사용해야 합니다.

#### <a name="encoder-processor"></a>인코더 프로세서

인코더 프로세서 노드를 사용 하면 사용자가 기록 된 비디오를 다운스트림 처리를 위해 원하는 형식으로 변환할 때 인코딩 속성을 지정할 수 있습니다. 예를 들어 [4k 해상도](https://en.wikipedia.org/wiki/4K_resolution) 로 구성 된 카메라에서 기록 된 비디오를 파일로 내보내려면 먼저 [1920 X 1080 (전체 HD)](https://en.wikipedia.org/wiki/1080p) 해상도로 크기를 조정 해야 할 수 있습니다.

### <a name="sinks"></a>Sinks

#### <a name="video-sink"></a>비디오 싱크

비디오 싱크 노드를 사용하면 비디오 및 관련 메타데이터를 Video Analyzer 클라우드 리소스에 저장할 수 있습니다. 비디오는 이벤트에 따라 지속적으로 또는 이상으로 기록 될 수 있습니다. 비디오 싱크 노드는 클라우드 연결이 끊어지면 에지 디바이스의 비디오를 캐시하고, 연결이 복원되면 업로드를 다시 시작할 수 있습니다. 또한 이 노드의 속성을 구성하는 방법에 대한 자세한 내용은 [연속 비디오 녹화](continuous-video-recording.md) 문서를 참조하세요.

보안상의 이유로, 지정 된 Video Analyzer **edge 모듈** 인스턴스는 새 비디오 항목 또는 이전에 기록한 비디오 항목에 동일한 모듈에만 콘텐츠를 기록할 수 있습니다. 동일한 edge 모듈 인스턴스에 의해 만들어지지 않은 기존 비디오에 콘텐츠를 기록 하려고 하면 기록에 실패 합니다.

#### <a name="file-sink"></a>파일 싱크

파일 싱크 노드를 사용하면 비디오를 에지 디바이스의 로컬 파일 시스템 위치에 쓸 수 있습니다. 파이프라인에는 파일 싱크 노드가 하나만 있어야 하며 신호 게이트 프로세서 노드의 다운스트림이어야 합니다. 그러면 출력 파일의 지속 시간이 신호 게이트 프로세서 노드 속성에 지정된 값으로 제한됩니다. 에지 디바이스의 디스크 공간이 부족하지 않도록 Video Analyzer 에지 모듈이 데이터를 캐시하는 데 사용할 수 있는 최대 크기를 설정할 수도 있습니다.

캐시가 가득 차면 Video Analyzer 에지 모듈은 가장 오래된 데이터를 삭제하고 새 데이터로 바꿉니다.

#### <a name="iot-hub-message-sink"></a>IoT Hub 메시지 싱크

IoT Hub 메시지 싱크 노드를 사용하여 IoT Edge 허브에 이벤트를 게시할 수 있습니다. 데이터를 에지 디바이스의 다른 모듈 또는 앱에 라우팅하거나 배포 매니페스트에 지정된 경로별로 클라우드의 IoT Hub에 라우팅하도록 IoT Edge 허브를 구성할 수 있습니다. IoT Hub 메시지 싱크 노드는 동작 감지 프로세서 노드와 같은 업스트림 프로세서 노드 또는 HTTP 확장 프로세서 노드를 통해 외부 유추 서비스에서 발생한 이벤트를 허용할 수 있습니다.

## <a name="rules-on-the-use-of-nodes"></a>노드 사용 규칙

다음 표에서는 라이브 및 일괄 토폴로지와 비디오 분석기 edge 모듈 및 서비스에서 노드가 허용 되는 현재 규칙을 설명 합니다.

| 노드 이름                              | 토폴로지 종류  |  배포       |
| -------------------------------------- | ---------------|-------------------|
| RTSP 원본                            | 라이브           |  Edge 및 클라우드   |
| IoT hub 메시지 원본                 | 라이브           |  Microsoft Edge             |
| 비디오 소스                           | Batch          |  클라우드            |
| 동작 감지 프로세서             | 라이브           |  Microsoft Edge             |
| HTTP 확장 프로세서               | 라이브           |  Microsoft Edge             |
| gRPC 확장 프로세서               | 라이브           |  Microsoft Edge             |
| Cognitive Services 확장 프로세서 | 라이브           |  Microsoft Edge             |
| 신호 게이트 프로세서                  | 라이브           |  Microsoft Edge             |
| 개체 추적기 프로세서               | 라이브           |  Microsoft Edge             |
| 라인 교차 프로세서                | 라이브           |  Microsoft Edge             |
| 인코더 프로세서                      | Batch          |  클라우드            |
| 비디오 싱크                             | 라이브 및 일괄 처리 |  Edge 및 클라우드   |
| 파일 싱크                              | 라이브           |  Microsoft Edge             |
| IoT hub 메시지 싱크                   | 라이브           |  Microsoft Edge             |

토폴로지 내에서 다양 한 노드를 사용할 수 있는 방법에 대 한 추가 규칙은 [파이프라인의 제한 사항](quotas-limitations.md#limitations-on-pipeline-topologies) 을 참조 하세요.

## <a name="scenarios"></a>시나리오

위에서 정의한 원본, 프로세서 및 싱크 조합을 사용하여 라이브 비디오 분석과 관련된 다양한 시나리오에 대한 파이프라인을 작성할 수 있습니다. 예제 시나리오는 다음과 같습니다.

* [연속 비디오 녹화](continuous-video-recording.md) 
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md) 
* [비디오 녹화 없이 비디오 분석](analyze-live-video-without-recording.md) 

## <a name="next-steps"></a>다음 단계

라이브 비디오 피드에서 동작 감지를 실행하는 방법은 [빠른 시작: Azure Video Analyzer 시작](get-started-detect-motion-emit-events.md)을 참조하세요.
