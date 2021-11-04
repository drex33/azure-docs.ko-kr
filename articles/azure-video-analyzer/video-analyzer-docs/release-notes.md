---
title: Azure Video Analyzer 릴리스 정보 - Azure
description: 이 항목에서는 Azure Video Analyzer 릴리스, 개선 사항, 버그 수정 및 알려진 문제에 대한 릴리스 정보를 제공합니다.
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 89ab651416237e06343f950d2a9202f082555e58
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556448"
---
# <a name="azure-video-analyzer-release-notes"></a>Azure Video Analyzer 릴리스 정보

>이 URL(`https://docs.microsoft.com/api/search/rss?search=%22Azure+Video+Analyzer+on+IoT+Edge+release+notes%22&locale=en-us`)을 RSS 피드 판독기에 복사하고 붙여넣어 업데이트를 위해 이 페이지를 다시 방문해야 하는 시기에 대한 알림을 받습니다.

이 문서에서는 다음에 대한 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

<hr width=100%>

## <a name="november-2-2021"></a>2021년 11월 2일

이 릴리스는 Video Analyzer 에지 모듈 및 Video Analyzer 서비스에 대한 업데이트입니다. 에지 모듈의 릴리스 태그는 다음과 같습니다.

```
mcr.microsoft.com/media/video-analyzer:1.1.0
```

> [!NOTE]
> 빠른 시작 및 자습서에서 배포 매니페스트는 태그 1(video-analyzer: 1)을 사용합니다. 따라서 이러한 매니페스트를 다시 배포하면 최신 태그가 릴리스될 때 에지 디바이스의 모듈이 업데이트되어야 합니다.

Video Analyzer 서비스의 ARM API 버전은 다음과 같습니다.

```
2021-11-01-preview
```

### <a name="new-functionalities"></a>새로운 기능

* [Cognitive Services 공간 분석](edge/computer-vision-for-spatial-analysis.md) AI 서비스에 대한 Computer Vision Video Analyzer를 사용하는 경우 라이브 비디오에서 사람의 속도, 방향, 내역과 같은 새로운 인사이트를 생성하고 볼 수 있습니다.
* 에지 디바이스의 로컬 서브넷에서 [ONVIF 지원](edge/camera-discovery.md) 디바이스를 검색할 수 있습니다.
* 라이브 [비디오를 클라우드 에서 직접 캡처하고 녹화할](cloud/connect-cameras-to-cloud.md)수 있습니다.
  * [짧은 대기 시간 스트리밍을](playback-recordings-how-to.md#low-latency-streaming) 사용하여 엔드투엔드 대기 시간이 약 2초인 RTSP 카메라에서 라이브 비디오를 볼 수 있습니다.
  * RTSP 카메라에서 [Video Analyzer IoT PnP 계약을](cloud/connect-devices.md) 구현하여 디바이스에서 Video Analyzer 서비스로 비디오 캡처를 사용하도록 설정할 수 있습니다.
* [녹화된 비디오의 원하는 부분을](cloud/export-portion-of-video-as-mp4.md) MP4 파일로 내보낼 수 있습니다.
* 녹화된 비디오에 대한 보존 정책을 지정할 수 있습니다. 여기서 서비스는 지정된 일 수보다 오래된 콘텐츠를 주기적으로 트리밍합니다.
* Video Analyzer 에지 모듈을 사용하여 녹화된 비디오에는 미리 보기 이미지 또는 미리 [보기가](edge/enable-video-preview-images.md) 주기적으로 포함될 수 있으므로 더 나은 검색 환경을 사용할 수 있습니다.

### <a name="known-issues"></a>알려진 문제
* 짧은 대기 시간 스트리밍을 사용하는 경우 한 번에 하나의 클라이언트만 서비스에 연결할 수 있습니다.
* 공유 메모리를 유추하기 위해 gRPC 확장 모듈을 사용하는 경우 Video Analyzer 에지 모듈과 확장 모듈이 모두 동일한 [사용자 및 그룹에서](https://docs.docker.com/engine/reference/builder/#user) 실행되어야 합니다.

## <a name="october-1-2021"></a>2021년 10월 1일
이제 오스트레일리아 동부에서 Video Analyzer 서비스를 미리 보기로 사용할 수 있습니다. 최신 가용성 정보는 [여기를](https://azure.microsoft.com/global-infrastructure/services/?products=video-analyzer&regions=all) 참조하세요.

[Video Analyzer 위젯을](embed-player-in-power-bi.md) Power BI 포함할 수 있는 방법을 설명하는 새 문서가 추가되었습니다.

## <a name="june-3-2021"></a>2021년 6월 3일

모듈의 2021년 6월 새로 고침에 대한 릴리스 태그는 다음과 같습니다.

```
     mcr.microsoft.com/media/video-analyzer:1.0.1
```
> [!NOTE]
> 빠른 시작 및 자습서에서 배포 매니페스트는 태그 1(video-analyzer: 1)을 사용합니다. 따라서 이러한 매니페스트를 다시 배포하면 최신 태그가 릴리스될 때 에지 디바이스의 모듈이 업데이트되어야 합니다.

### <a name="module-updates"></a>모듈 업데이트
* RTSP 카메라에 연결하기 위한 자격 증명에서 유니코드 문자를 지원합니다.
* 디버그 모드에서 세부 로그를 사용하도록 업데이트

<hr width=100%>

## <a name="may-25-2021"></a>2021년 5월 25일

이 릴리스는 Azure Video Analyzer의 첫 번째 공개 미리 보기 릴리스입니다. 릴리스 태그는 다음과 같습니다.

```
mcr.microsoft.com/media/video-analyzer:1.0.0
```

> [!NOTE]
> 빠른 시작 및 자습서에서 배포 매니페스트는 태그 1(video-analyzer: 1)을 사용합니다. 따라서 이러한 매니페스트를 다시 배포하면 최신 태그가 릴리스될 때 에지 디바이스의 모듈이 업데이트되어야 합니다.

### <a name="supported-functionalities"></a>지원되는 기능

* 원하는 AI 모듈을 사용하여 라이브 비디오 스트림을 분석하고 필요에 따라 에지 디바이스 또는 클라우드에서 비디오 녹화
* 클라우드에서 유추 메타데이터와 함께 비디오 녹화
* 자체 개체 감지 모델을 사용하여 개체가 선을 넘을 때 이벤트 트리거
* 자체 감지 모델로 감지된 개체 추적 
* Video Analyzer 플레이어 위젯(웹 구성 요소)을 사용하여 녹화된 비디오 및 유추 메타데이터 재생
* Azure Portal 또는 Visual Studio Code를 사용하여 IoT Hub를 통해 모듈 배포 및 구성
<!--REDIRECT* Manage [pipeline topologies](pipeline.md#pipeline-topologies) remotely or locally using [direct method](direct-methods.md) calls-->

## <a name="next-steps"></a>다음 단계

[개요](overview.md)
