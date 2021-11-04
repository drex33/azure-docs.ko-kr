---
title: Azure Video Analyzer 서비스 문제 해결
description: 이 문서에서는 Azure Video Analyzer 서비스에 대 한 문제 해결 단계를 다룹니다.
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 26d4675274d05500b54bbc43ecb84838b114f0c9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554700"
---
# <a name="troubleshoot-azure-video-analyzer-service"></a>Azure Video Analyzer 서비스 문제 해결

[!INCLUDE [header](includes/cloud-env.md)]

이 문서에서는 서비스를 사용 하는 동안 발생할 수 있는 일반적인 오류 시나리오에 대 한 문제 해결 단계를 다룹니다.

## <a name="enable-diagnostics"></a>진단 사용

[모니터링 및 로깅은](./monitor-log-cloud.md) Video Analyzer 서비스 이벤트 분류를 이해 하 고 문제를 디버깅 하는 데 도움이 되는 로그를 생성 하는 데 도움이 됩니다.
- Azure Portal에서 Video Analyzer 계정의 **모니터링** 섹션으로 이동 하 고 **진단 설정** 을 선택 합니다. 
- **진단 설정 추가** 를 클릭 하 여 원하는 이벤트 유형의 로그를 사용 하도록 설정 합니다., `Diagnostics` `Audit` , `Operational` 자세한 내용은 [여기](./monitor-log-cloud.md)를 참조하세요.


## <a name="view-diagnostics"></a>진단 보기

진단을 사용 하도록 설정 하면 다음과 같이 로그에 액세스할 수 있습니다.

> [!TIP]
> 이벤트를 내보내려면 하나 이상의 라이브 파이프라인을 활성화 하는 것이 좋습니다. 

- 포털에서 로그가 기록 된 저장소 계정을 찾습니다.
- 해당 저장소 계정에 대 한 관리 블레이드를 엽니다.
- Storage > 탐색기로 이동 하 여 저장소 계정을 확장 하면 Blob 컨테이너 "insights-로그-범주"가 표시 됩니다. 이 blob은 JSON 파일 형식의 로그를 포함 합니다. 이러한 로그를 다운로드 하 여 문제를 조사할 수 있습니다.

## <a name="view-metrics"></a>메트릭 보기 

또한 Video analyzer는 다음과 같이 문제를 식별 하는 데 도움이 될 수 있는 수집 & 파이프라인에 대 한 메트릭을 내보냅니다.
- IngressBytes-파이프라인이 받은 총 바이트 수입니다. 지속적으로 늘어나는 값은 파이프라인이 양호 하 고 RTSP 카메라에서 비디오 데이터를 수신 함을 나타냅니다.
- Pipelines-파이프라인 상태와 카운트를 확인 하는 데 도움이 됩니다.

## <a name="view-activity-logs"></a>활동 로그 보기

**활동 로그** 는 자동으로 생성 되며, Azure Portal에서 Video Analyzer 계정 관리 블레이드의 ' 활동 로그 ' 섹션으로 이동 하 여 액세스할 수 있습니다. 계정에 대 한 ARM API 호출 기록과 관련 세부 정보를 볼 수 있습니다.

## <a name="common-error-scenarios"></a>일반적인 오류 시나리오

Video Analyzer 서비스에서 발생 하는 일반적인 오류 중 일부는 아래에 설명 되어 있습니다.

### <a name="unable-to-play-video-after-activating-live-pipeline"></a>라이브 파이프라인을 활성화 한 후 비디오를 재생할 수 없음

- 비디오 분석기 위젯을 사용 하 여 비디오를 재생 하는 경우에는 액세스할 수 있는 경우 Azure Portal 사용해 보세요. 비디오는 Azure Portal에서 재생 되 고 위젯에는 재생 되지 않으면 [아래의](#playback-error-with-the-widget) 위젯 섹션을 계속 확인 해야 합니다.

- 비디오를 Azure Portal 재생 하지 않는 경우 비디오 분석기 서비스가 RTSP 카메라에서 비디오 데이터를 받고 있는지 확인 합니다. 포털의 모니터링->메트릭 섹션으로 이동 하 여 "수신 바이트" 메트릭을 선택 합니다. 집계가 늘어나면 RTSP 카메라와 서비스 간의 연결이 정상 이며 수신 바이트 합계는 그래프 아래에서 사용할 수 있습니다. 

- 서비스가 RTSP 카메라에서 비디오 데이터를 수신 하지 않는 경우 다음 단계는 [관련 진단 로그를 확인](#view-diagnostics)하는 것입니다. [Protocolerror](#diagnostic-logs-have-a-protocolerror-with-code-401)와 같은 오류가 표시 될 수 있으며 아래에 설명 된 대로 추가로 문제를 해결할 수 있습니다.

### <a name="diagnostic-logs-have-a-protocolerror-with-code-401"></a>진단 로그에 코드 401의 ProtocolError가 있습니다.

- 다음과 같이 코드를 401로 설정 하 여 진단 로그에 오류가 표시 되 면 첫 번째 단계는 RTSP 자격 증명을 다시 검사 하는 것입니다. 찾을 수 있는 샘플 이벤트는 다음과 같습니다.

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

- 라이브 파이프라인을 사용 하 여 인터넷을 통해 액세스할 수 있는 카메라에 연결 하는 경우 라이브 파이프라인을 만들 때 사용 된 RTSP URL, 사용자 이름 및 암호를 확인 합니다. 라이브 파이프라인에서 GET을 호출 하 여 URL과 사용자 이름을 볼 수 있지만 암호는 다시 표시 되지 않습니다. 라이브 파이프라인을 만드는 데 사용 된 코드를 확인 해야 합니다.

- [원격 장치 어댑터](./use-remote-device-adapter.md)를 사용 하는 경우 다음 단계를 수행 합니다.

   - [IoT hub가 비디오 분석기 계정에 연결 되어](../create-video-analyzer-account.md#post-deployment-steps)있는지 확인 합니다. 원격 장치 어댑터를 사용 하는 데 필요 합니다.
   - `remoteDeviceAdapterList`Edge 모듈에서 직접 메서드를 실행 하 고 IP 주소를 확인 합니다. 예제 요청 및 응답은 [다음과](../edge/direct-methods.md) 같습니다.
   - 문제가 발생 하는 라이브 파이프라인에서 사용 중인 원격 장치 어댑터에 대 한 응답을 확인 하 고 [이 문서의](use-remote-device-adapter.md)예제와 비교 합니다. 카메라의 IP 주소가 올바른지 확인 합니다.
   - Azure Portal->Video Analyzer 계정 > 라이브 > Pipelines > 라이브 파이프라인 편집-> RTSP 사용자 이름 및 암호를 다시 입력 합니다. 제공 하는 RTSP URL이로 시작 하는지 확인 `rtsp://localhost:554/…` 합니다. 여기서는를 사용 `localhost` 해야 합니다.

- 위의 단계를 수행 해도 문제가 해결 되지 않고 비디오 재생이 여전히 작동 하지 않는 경우 Azure Portal에 로그인 하 여 지원 티켓을 엽니다. 비디오 분석기 edge 모듈에서 로그를 첨부 해야 할 수 있습니다. [edge 문제 해결 문서](../edge/troubleshoot.md#common-error-resolutions) 의 ' 지원 번들 사용 명령 ' 섹션을 참조 하세요.

### <a name="unable-to-record-to-a-video-resource"></a>비디오 리소스에 기록할 수 없습니다.

비디오 분석기를 사용 하 여 고유한 RTSP 카메라에서 기록할 때 고유한 비디오 리소스를 사용 해야 합니다. 카메라 설정을 변경 하는 경우 (예: 해상도) 새 비디오 리소스로 전환 해야 할 수도 있습니다. 일부 샘플 파이프라인 토폴로지에서는 비디오 싱크 노드 속성에 비디오 리소스에 대 한 하드 코드 된 이름이 있습니다. 다른 카메라에서 직접 이러한 토폴로지를 사용 하는 경우이 문제가 발생 합니다. 고유 하도록 `videoName` 비디오 싱크 노드의 속성을 수정 합니다.

### <a name="interrupted-recording-or-playback"></a>녹화 또는 재생 중단

라이브 파이프라인을 만들 때 `bitrateKbps` RTSP 카메라가 서비스에 비디오를 보낼 최대 비트 전송률 ()을 지정 해야 합니다.
카메라가이 제한을 초과 하면 Video Analyzer 서비스는 카메라에서 잠깐 연결을 끊습니다. 비트 전송률이 일시적으로 급증 하는 경우에는 카메라에 대 한 연결을 다시 시도할 수 있습니다. 진단 로그에서 RtspIngestionSessionEnded 이벤트를 검색 하 여 오류 코드와 함께이 상황을 식별할 수 있습니다. `SourceBitrateExceeded`
이 문제를 해결 하려면 카메라의 비트 전송률 설정을 낮추거나 `bitrateKbps` 카메라 설정에 맞게 라이브 파이프라인의 값을 늘립니다.

### <a name="playback-error-with-the-widget"></a>위젯에 재생 오류

Video Analyzer 위젯에 ' 액세스가 금지 되었습니다. ' 오류가 표시 되 면 감사 로그에 경고 이벤트가 표시 됩니다.

- 클라이언트 API JWT 토큰 및 해당 액세스 정책을 생성 했는지 확인 하 고 [토큰 만들기](../access-policies.md) 및 [액세스-정책](../access-policies.md#creating-an-access-policy)만들기에 대 한 설명서를 참조 하세요. 액세스 정책이 올바르게 설정 되지 않은 경우 플레이어는 작동 하지 않으며 JWT 토큰이 정책과 일치 하지 않습니다. 
- 문제가 해결 되지 않으면 위젯 로그를 수집 하 고 Azure Portal를 사용 하 여 지원 티켓을 제출 합니다.
- 위젯 로그를 모으고 있습니다.
    - F12 키를 눌러 브라우저 개발자 도구를 사용 하도록 설정 하 고 콘솔 탭으로 이동 하 여 "모든 수준" 로깅을 사용 하도록 설정 합니다.   
    - 설정 아이콘에서 기본 설정--> 콘솔--> 타임 스탬프 표시를 선택 합니다. 로그를 저장 합니다.

## <a name="collect-logs-for-submitting-a-support-ticket"></a>지원 티켓 제출용 로그 수집
   
자체를 기반으로 하는 문제 해결 단계에서 문제를 해결할 수 없고 도움이 필요할 수 있는 더 많은 문제가 있는 경우 관련 세부 정보 & 로그와 함께 Azure Portal를 사용 하 여 지원 티켓을 여세요. 에서 전자 메일을 보내 microsoft에 연락할 수도 있습니다 videoanalyzerhelp@microsoft.com .
   
> [!WARNING]
> 로그에는 IP 주소와 같은 PII(개인 식별 정보)가 포함될 수 있습니다. 로그의 모든 로컬 복사본은 검토를 완료하고 지원 티켓을 닫는 즉시 삭제됩니다.
   
## <a name="next-steps"></a>다음 단계

[FAQ](./faq.yml)
