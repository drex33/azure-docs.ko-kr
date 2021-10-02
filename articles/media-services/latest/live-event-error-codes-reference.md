---
title: Azure Media Services 라이브 이벤트 오류 코드
description: 이 문서에는 라이브 이벤트 오류 코드가 나열되어 있습니다.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 24428aeb07548556a96c292b6dc1be0f2014a479
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389839"
---
# <a name="media-services-live-event-error-codes"></a>Media Services 라이브 이벤트 오류 코드

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

다음 표에서는 [라이브 이벤트](live-event-outputs-concept.md) 오류 코드를 나열합니다.

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

라이브 이벤트의 [Event Grid](../../event-grid/index.yml) 이벤트를 구독하면 [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) 이벤트에서 다음 오류 중 하나가 표시될 수 있습니다.
> [!div class="mx-tdCol2BreakAll"]
>| Error | 정보 |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Description | 잘못된 수집 URL입니다. |
>|추천 솔루션| APPID는 RTMP 수집 URL의 GUID 토큰입니다. API의 수집 URL과 일치하는지 확인합니다. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Description |인코더 IP가 구성된 IP 허용 목록에 없습니다. |
>| 추천 솔루션| 인코더의 IP가 IP 허용 목록에 있는지 확인합니다. *whoismyip* 또는 *CIDR 계산기* 와 같은 온라인 도구를 사용하여 적절한 값을 설정합니다.  인코더가 실제 라이브 이벤트 전에 서버에 도달할 수 있는지 확인합니다. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Description|RTMP 인코더에서 `setDataFrame` 명령을 보내지 않았습니다. |
>| 추천 솔루션|대부분의 상용 인코더는 스트림 메타데이터를 전송합니다. 단일 비트 전송률 수집을 푸시하는 인코더의 경우 문제가 되지 않을 수 있습니다. LiveEvent는 스트림 메타데이터가 없을 때 들어오는 비트 전송률을 계산할 수 있습니다.  PassThru 채널 또는 이중 푸시 시나리오에 대한 다중 비트 전송률 수집의 경우 수집 URL에 ‘videodatarate’및 ‘audiodatarate’와 함께 쿼리 문자열을 추가할 수 있습니다. 대략적인 값이 작동할 수 있습니다. 단위는 Kbit입니다. 예: `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Description|지정된 코덱이 지원되지 않습니다.|
>| 추천 솔루션| LiveEvent가 지원되지 않는 코덱을 수신했습니다. 예를 들어 RTMP 수집, LiveEvent가 AVC가 아닌 비디오 코덱을 수신했습니다.  인코더 사전 설정을 확인합니다. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Description |실제 미디어 데이터를 제공하기 전에 미디어 설명 정보를 받지 못했습니다. |
>| 추천 솔루션|LiveEvent는 인코더로부터 스트림 설명(헤더 또는 FLV 태그)을 수신하지 않습니다. 프로토콜 위반입니다. 인코더 공급업체에 문의하세요. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Description| 오디오 또는 비디오 형식의 품질 수준 수가 허용 된 최대 한도를 초과 했습니다. 이 제한을 초과 하는 품질 수준은 서비스에서 무시 됩니다.|
>| 추천 솔루션|라이브 이벤트 모드가 Live Encoding인 경우 인코더는 비디오 및 오디오의 단일 비트 전송률을 푸시해야 합니다.  같은 비트 전송률에서 중복 푸시를 사용할 수 있습니다. 인코더 사전 설정 또는 출력 설정을 확인하여 단일 비트 전송률 스트림을 출력하는지 확인합니다. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Description|라이브 이벤트 또는 채널 서비스의 총 수신 비트 전송률이 최대 허용 한도를 초과했습니다. |
>| 추천 솔루션|인코더가 들어오는 최대 비트 전송률을 초과했습니다. 이 한도는 기여하는 인코더에서 들어오는 모든 데이터를 집계합니다. 비트 전송률을 줄이기 위해 인코더 사전 설정 또는 출력 설정을 확인합니다. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Description|RTMP 인코더에서 비디오 또는 오디오 FLVTag에 대한 타임스탬프가 잘못되었습니다. |
>| 추천 솔루션|더 이상 사용되지 않습니다. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Description|프레임 속도가 있는 들어오는 인코더 수집 스트림이 라이브 이벤트/채널 인코딩에 허용되는 최대 30fps를 초과했습니다. |
>| 추천 솔루션|인코더 사전 설정을 확인하여 프레임 속도를 36fps 미만으로 낮춥니다. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Description|들어오는 인코더 수집 스트림은 허용 되는 다음 해상도를 초과 합니다. 1920x1088는 라이브 이벤트/채널 인코딩에 대 한, 기본 및 표준 통과 라이브 이벤트/채널의 경우 4096 x 2160입니다. |
>| 추천 솔루션|한도를 초과하지 않도록 비디오 해상도를 낮추기 위해 인코더 사전 설정을 확인합니다. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Description|라이브 이벤트가 한 번에 많은 양의 오디오 데이터를 수신했거나 키 프레임 없이 많은 양의 비디오 데이터를 수신했습니다. 올바른 데이터로 다시 시도할 수 있도록 인코더 연결을 끊었습니다. |
>| 추천 솔루션|인코더가 모든 키 프레임 간격(GOP)에 대해 키 프레임을 전송하는지 확인합니다.  “CBR(상수 비트 전송률)” 또는 “키 프레임 맞춤”과 같은 설정을 사용하도록 설정합니다. 기여하는 인코더를 다시 설정하면 도움이 되는 경우가 있습니다. 도움이 되지 않으면 인코더 공급업체에 문의하세요. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

[LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) 이벤트에서 다음 오류 중 하나가 표시될 수 있습니다.

> [!div class="mx-tdCol2BreakAll"]
>| Error | 정보 |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Description|허용된 시간 한도 동안 유휴 상태가 된 후 RTMP 세션 시간이 초과되었습니다. |
>|추천 솔루션|일반적으로 인코더가 입력 피드 수신을 중지하여 푸시할 데이터가 없으므로 세션이 유휴 상태가 될 때 발생합니다. 인코더 또는 입력 피드 상태가 정상인지 확인합니다. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Description| RTMP 인코더에서 비디오 또는 오디오 FLVTag에 대한 타임스탬프가 잘못되었습니다. |
>| 추천 솔루션| 더 이상 사용되지 않습니다. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Description|데이터를 너무 빨리 보내는 인코더입니다. |
>| 추천 솔루션|인코더가 짧은 기간에 대규모 조각 세트를 버스트하는 경우에 발생합니다.  이론적으로 네트워크 문제 때문에 인코더가 데이터를 푸시할 수 없고 네트워크를 사용할 수 있을 때 데이터를 버스트하는 경우 발생할 수 있습니다. 인코더 로그 또는 시스템 로그에서 이유를 찾으세요. |
>|**알 수 없는 오류 코드** |
>| Description| 이러한 오류 코드는 메모리 오류에서 해시 맵의 중복 항목에 이르기까지 다양할 수 있습니다. 인코더가 짧은 기간에 대규모 조각 세트를 전송하는 경우에 발생할 수 있습니다.  인코더가 네트워크 문제 때문에 데이터를 한동안 푸시하지 못한 다음, 네트워크를 사용할 수 있게 되었을 때 연기된 모든 조각을 한 번에 전송하는 경우에도 발생할 수 있습니다. |
>|추천 솔루션| 인코더 로그를 확인합니다.|

## <a name="other-error-codes"></a>기타 오류 코드

> [!div class="mx-tdCol2BreakAll"]
>| Error | 정보 |거부/연결 끊김 이벤트|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||예|
>| Description|일반 오류입니다. ||
>|추천 솔루션| 없음||
>|**MPI_SYSTEM_MAINTENANCE** ||예|
>| Description|서비스 업데이트 또는 시스템 유지 관리로 인해 인코더의 연결을 끊었습니다. ||
>|추천 솔루션|인코더가 ‘자동 연결’을 사용하도록 설정되었는지 확인합니다. 인코더가 유지 관리 중이 아닌 중복 라이브 이벤트 엔드포인트에 다시 연결할 수 있습니다. ||
>|**MPE_BAD_URL_SYNTAX** ||예|
>| Description|수집 URL의 형식이 잘못되었습니다. ||
>|추천 솔루션|수집 URL의 형식이 올바르게 지정되었는지 확인합니다. RTMP의 경우 `rtmp[s]://hostname:port/live/GUID_APPID/streamname`이어야 합니다. ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||예|
>| Description|인코더에서 세션의 연결을 끊었습니다.  ||
>|추천 솔루션|오류가 아닙니다. 인코더가 정상적인 연결 끊기를 포함하여 연결 끊기를 시작했습니다. 예기치 않게 연결이 끊기면 인코더 로그를 확인하세요. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||예|
>| Description|들어오는 데이터 전송률이 예상 비트 전송률과 일치하지 않습니다. ||
>|추천 솔루션|들어오는 데이터 속도가 너무 느리거나 빠를 때 발생하는 경고입니다. 인코더 로그 또는 시스템 로그를 확인합니다.||
>|**MPE_INGEST_DISCONTINUITY** ||예|
>| Description| 들어오는 데이터가 불연속적입니다.||
>|추천 솔루션| 네트워크 문제 또는 시스템 리소스 문제로 인해 인코더가 데이터를 삭제한다는 경고입니다. 인코더 로그 또는 시스템 로그를 확인합니다. 시스템 리소스(CPU, 메모리 또는 네트워크)도 모니터링합니다. 시스템 CPU가 너무 높으면 비트 전송률을 낮추거나 시스템 그래픽 카드의 H/W 인코더 옵션을 사용합니다.||

## <a name="see-also"></a>참조

[스트리밍 엔드포인트(원본) 오류 코드](stream-streaming-endpoint-error-codes-reference.md)

## <a name="next-steps"></a>다음 단계

[자습서: Media Services로 라이브 스트리밍](stream-live-tutorial-with-api.md)
