---
title: Azure Video Analyzer 서비스 문제 해결
description: 이 문서에서는 Azure Video Analyzer 서비스에 대한 문제 해결 단계를 다룹니다.
ms.topic: troubleshooting
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 732ca8c04cb642e8a17858d00d2ed70bde1c45fc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053168"
---
# <a name="troubleshoot-azure-video-analyzer-service"></a>Azure Video Analyzer 서비스 문제 해결

[!INCLUDE [header](includes/cloud-env.md)]

이 문서에서는 서비스를 사용하는 동안 표시될 수 있는 일반적인 오류 시나리오에 대한 문제 해결 단계를 다룹니다.

## <a name="enable-diagnostics"></a>진단 사용

[모니터링 및 로깅은](./monitor-log-cloud.md) Video Analyzer 서비스 이벤트 분류와 디버깅 문제에 도움이 되는 로그를 생성하는 방법을 이해하는 데 도움이 됩니다.
- Azure Portal Video Analyzer 계정의 **모니터링** 섹션으로 이동하여 진단 **설정을** 선택합니다. 
- 진단 **설정 추가를** 클릭하여 원하는 이벤트 유형의 로그를 사용하도록 설정합니다. `Diagnostics` , , `Audit` `Operational` . 자세한 내용은 [여기](./monitor-log-cloud.md)를 참조하세요.


## <a name="view-diagnostics"></a>진단 보기

진단을 사용하도록 설정하면 다음과 같이 로그에 액세스할 수 있습니다.

> [!TIP]
> 이벤트를 내보낸 라이브 파이프라인을 하나 이상 활성화하는 것이 좋습니다. 

- 포털에서 로그가 기록된 스토리지 계정을 찾습니다.
- 해당 스토리지 계정에 대한 관리 블레이드 열기
- 스토리지 탐색기로 이동한 다음> 스토리지 계정을 확장하면 Blob 컨테이너 "insights-logs-category"가 표시됩니다. 이 Blob에는 JSON 파일 형식의 로그가 있습니다. 이러한 로그를 다운로드하여 문제를 조사할 수 있습니다.

## <a name="view-metrics"></a>메트릭 보기 

또한 Video Analyzer는 파이프라인을 & & 메트릭을 내비며, 이는 다음과 같은 문제를 식별하는 데 도움이 될 수 있습니다.
- IngressBytes - 파이프라인에서 받은 총 바이트 수입니다. 지속적으로 증가하는 값은 파이프라인이 정상이며 RTSP 카메라에서 비디오 데이터를 수신하고 있음을 나타냅니다.
- Pipelines - 파이프라인 상태 및 개수를 확인하는 데 도움이 됩니다.

## <a name="view-activity-logs"></a>활동 로그 보기

**활동 로그는** 자동으로 생성되며 Azure Portal Video Analyzer 계정 관리 블레이드의 '활동 로그' 섹션으로 이동하여 액세스할 수 있습니다. 계정에 대한 ARM API 호출의 기록 및 관련 세부 정보를 볼 수 있습니다.

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

Video Analyzer 서비스에서 발생하는 일반적인 오류 중 일부는 아래에 설명되어 있습니다.

### <a name="unable-to-play-video-after-activating-live-pipeline"></a>라이브 파이프라인을 활성화한 후 비디오를 재생할 수 없음

- Video Analyzer 위젯을 사용하여 비디오를 재생하는 경우 액세스 권한이 있는 경우 Azure Portal 사용해 보세요. 비디오가 Azure Portal 재생되지만 위젯에서는 재생되지 않는 경우 [아래](#playback-error-with-the-widget) 위젯 섹션을 계속 확인해야 합니다.

- 비디오가 Azure Portal 재생되지 않는 경우 Video Analyzer 서비스가 RTSP 카메라에서 비디오 데이터를 수신하는지 확인합니다. 포털의 모니터링->메트릭 섹션으로 이동하여 "수신 바이트" 메트릭을 선택합니다. 집계가 증가하는 경우 RTSP 카메라와 서비스 간의 연결이 정상이면 그래프 아래에서 수신 바이트 합계를 사용할 수 있습니다. 

- 서비스가 RTSP 카메라에서 비디오 데이터를 수신하지 않는 경우 다음 단계는 [관련 진단 로그를 보는 것입니다.](#view-diagnostics) [ProtocolError](#diagnostic-logs-have-a-protocolerror-with-code-401)와 같은 오류가 표시될 수 있으며 아래에서 설명한 대로 추가로 문제를 해결할 수 있습니다.

### <a name="diagnostic-logs-have-a-protocolerror-with-code-401"></a>진단 로그에는 코드 401이 있는 ProtocolError가 있습니다.

- 다음과 같이 코드가 401로 설정된 진단 로그에 Microsoft.VideoAnalyzer.Diagnostics.ProtocolError가 표시되는 경우 첫 번째 단계는 RTSP 자격 증명을 다시 확인하는 것입니다. 샘플 이벤트는 다음과 같습니다.

   ```
   {
       "time": "2021-10-15T02:56:18.7890000Z",
       "resourceId": "/SUBSCRIPTIONS/{GUID}/RESOURCEGROUPS/8AVA/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/AVASAMPLEZ2OHI3VBIRQPC",
       "region": "westcentralus",
       "category": "Diagnostics",
       "operationName": "Microsoft.VideoAnalyzer.Diagnostics.ProtocolError",
       "operationVersion": "1.0",
       "level": "Error",
       "traceContext": "{\n  \"traceId\": \"f728d155-b4fd-4aec-8307-bbe2a324f4c3\"\n}",
       "properties": {
           "subject": "/livePipelines/your-pipeline/sources/rtspSource",
           "body": {
               "code": "401",
               "target": "rtsp://127.0.0.1:33643/some-path",
               "protocol": "rtsp"
           }
       }
   }

   ```

- 라이브 파이프라인을 사용하여 인터넷을 통해 액세스할 수 있는 카메라에 연결하는 경우 라이브 파이프라인을 만들 때 사용된 RTSP URL, 사용자 이름 및 암호를 확인합니다. 라이브 파이프라인에서 GET을 호출하여 URL 및 사용자 이름을 볼 수 있지만 암호는 다시 에코되지 않습니다. 라이브 파이프라인을 만드는 데 사용된 코드를 확인해야 합니다.

- [원격 디바이스 어댑터](./use-remote-device-adapter.md)를 사용하는 경우 다음 단계를 시도합니다.

   - [IoT Hub가 Video Analyzer 계정에 연결되어 있는지 확인합니다.](../create-video-analyzer-account.md#post-deployment-steps) 원격 디바이스 어댑터를 사용하는 데 필요합니다.
   - `remoteDeviceAdapterList`에지 모듈에서 직접 메서드를 실행하고 IP 주소를 확인합니다. 샘플 요청 및 응답은 [여기에](../edge/direct-methods.md) 표시됩니다.
   - 문제가 발생한 라이브 파이프라인에서 사용 중인 원격 디바이스 어댑터에 대한 응답을 검사하고 [이 문서의](use-remote-device-adapter.md)예제와 비교합니다. 카메라의 IP 주소가 올바른지 확인합니다.
   - Azure Portal->Video Analyzer 계정 -> Live -> Pipelines -> 라이브 파이프라인 편집 -> RTSP 사용자 이름 및 암호를 다시 입력합니다. 제공하는 RTSP URL이 로 시작하는지 `rtsp://localhost:554/…` 확인합니다. 여기서는 를 사용해야 `localhost` 합니다.

- 위의 단계가 문제를 해결하는 데 도움이 되지 않고 비디오 재생이 여전히 작동하지 않는 경우 Azure Portal 로그인하고 지원 티켓을 엽니다. Video Analyzer 에지 모듈에서 로그를 연결해야 할 수 있습니다. 에지 문제 해결 문서의 'support-bundle 명령 사용' [섹션을 참조하세요.](../edge/troubleshoot.md#common-error-resolutions)

### <a name="unable-to-record-to-a-video-resource"></a>비디오 리소스에 기록할 수 없음

Video Analyzer를 사용하면 고유한 RTSP 카메라에서 녹화할 때 고유한 비디오 리소스를 사용해야 합니다. 또한 카메라 설정을 변경하는 경우(예: 해상도) 새 비디오 리소스로 전환해야 합니다. 샘플 파이프라인 토폴로지 중 일부에는 비디오 싱크 노드 속성의 비디오 리소스에 대한 하드 코드된 이름이 있습니다. 이러한 토폴로지는 다른 카메라와 함께 직접 사용하는 경우 이 문제가 발생합니다. 비디오 `videoName` 싱크 노드에서 속성을 수정하여 고유성을 보장합니다.

### <a name="interrupted-recording-or-playback"></a>중단된 기록 또는 재생

라이브 파이프라인을 만들 때 RTSP 카메라가 서비스에 비디오를 보낼 최대 비트 전송률()을 지정해야 `bitrateKbps` 합니다.
카메라가 이 제한을 초과하면 Video Analyzer 서비스가 카메라와 잠시 연결을 끊습니다. 비트 전송률에 일시적인 스파이크가 있는 경우 카메라에 다시 연결할 수 있습니다. 오류 코드와 함께 진단 로그에서 Microsoft.VideoAnalyzer.Diagnostics.RtspIngestionSessionEnded 이벤트를 검색하여 이 상황을 식별할 수 `SourceBitrateExceeded` 있습니다.
이 문제를 해결하려면 카메라의 비트 전송률 설정을 줄이거나 카메라 설정과 일치하도록 라이브 파이프라인의 값을 늘려야 `bitrateKbps` 합니다.

### <a name="playback-error-with-the-widget"></a>위젯의 재생 오류

Video Analyzer 위젯에서 '액세스 금지' 오류가 발생하면 감사 로그에 경고 이벤트가 표시됩니다.

- 클라이언트 API JWT 토큰 및 해당 액세스 정책을 생성했는지 확인합니다. 토큰 만들기 및 [액세스 정책](../access-policies.md#creating-an-access-policy) [만들기에](../access-policies.md) 대한 설명서를 참조하세요. 액세스 정책이 올바르게 설정되지 않았고 JWT 토큰이 정책과 일치하지 않으면 플레이어가 작동하지 않습니다. 
- 문제가 해결되지 않으면 위젯 로그를 수집하고 Azure Portal 사용하여 지원 티켓을 제출합니다.
- 위젯 로그 수집:
    - F12 키를 쳐 브라우저 개발자 도구 사용하도록 설정하고 콘솔 탭으로 이동하여 "모든 수준" 로깅을 사용하도록 설정합니다.   
    - 설정 아이콘에서 기본 설정 --> 콘솔 --> 타임스탬프 표시를 선택합니다. 로그를 저장합니다.

## <a name="collect-logs-for-submitting-a-support-ticket"></a>지원 티켓 제출용 로그 수집
   
자체 단계별 문제 해결 단계가 문제를 해결하지 못하고 도움이 필요할 수 있는 문제가 더 이상 있는 경우 관련 세부 정보 & 로그와 함께 Azure Portal 사용하여 지원 티켓을 여세요. 에서 전자 메일을 보내 문의할 수도 videoanalyzerhelp@microsoft.com 있습니다.
   
> [!WARNING]
> 로그에는 IP 주소와 같은 PII(개인 식별 정보)가 포함될 수 있습니다. 로그의 모든 로컬 복사본은 검토를 완료하고 지원 티켓을 닫는 즉시 삭제됩니다.
   
## <a name="next-steps"></a>다음 단계

[FAQ](./faq.yml)
