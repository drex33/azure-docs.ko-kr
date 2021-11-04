---
title: 음성 컨테이너용 일괄 처리 키트
titleSuffix: Azure Cognitive Services
description: 일괄 처리 키트를 사용하여 음성 컨테이너 요청의 크기를 조장합니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: eur
ms.openlocfilehash: b7d72497cd19bddb038ac5a51f2290fae9b99103
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509420"
---
# <a name="batch-processing-kit-for-speech-containers"></a>음성 컨테이너용 일괄 처리 키트

일괄 처리 키트를 사용하여 음성 컨테이너의 워크로드를 보완하고 스케일 아웃합니다. 컨테이너로 사용할 수 있는 이 오픈 소스 유틸리티는 온-프레미스 및 클라우드 기반 음성 컨테이너 엔드포인트에서 많은 수의 오디오 파일을 쉽게 일괄 전사할 수 있도록 지원합니다. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="일괄 처리 키트 컨테이너 워크플로의 예를 보여 주는 다이어그램":::

일괄 처리 키트 컨테이너는 [GitHub](https://github.com/microsoft/batch-processing-kit) 및 [Docker Hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags)에서 무료로 제공됩니다. 사용 중인 음성 컨테이너에 대해서만 [요금이 청구](speech-container-howto.md#billing)됩니다.

| 기능  | Description  |
|---------|---------|
| 오디오 파일 일괄 배포     | 많은 수의 파일을 온-프레미스 또는 클라우드 기반 음성 컨테이너 엔드포인트로 자동으로 디스패치합니다. 파일은 네트워크 파일 시스템을 포함하여 모든 POSIX 호환 볼륨에 있을 수 있습니다.       |
| Speech SDK 통합 | n-best 가설, 음성 분할, 언어, 욕설 마스킹 등의 공통 플래그를 Speech SDK에 전달합니다.  |
|실행 모드     | 일괄 처리 클라이언트를 한 번 실행하거나, 백그라운드에서 계속 실행하거나, 오디오 파일에 대한 HTTP 엔드포인트를 만듭니다.         |
| 내결함성 | 진행 상황에 영향을 주지 않고 전사를 자동으로 다시 시도하고 계속 진행하여, 다시 시도할 수 있는 오류와 다시 시도할 수 없는 오류를 구분합니다. |
| 엔드포인트 가용성 검색 | 엔드포인트를 사용할 수 없는 경우 일괄 처리 클라이언트는 다른 컨테이너 엔드포인트를 사용하여 계속 전사합니다. 다시 사용할 수 있게 되면 클라이언트에서 자동으로 엔드포인트를 사용하기 시작합니다.   |
| 엔드포인트 핫 스왑 | 일괄 처리를 중단하지 않고 런타임 중에 음성 컨테이너 엔드포인트를 추가, 제거 또는 수정합니다. 업데이트는 즉시 실행됩니다. |
| 실시간 로깅 | 각 오디오 파일에 대해 Speech SDK 로그 파일을 사용하여 시도된 요청, 타임스탬프 및 실패 이유를 실시간으로 로깅합니다. |

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull`을 사용하여 컨테이너 이미지 가져오기

[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 최신 일괄 처리 키트 컨테이너를 다운로드합니다.

[!INCLUDE [pull-image-include](../../../includes/pull-image-include.md)]

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>엔드포인트 구성

일괄 처리 클라이언트는 온-프레미스 컨테이너 엔드포인트를 지정하는 yaml 구성 파일을 사용합니다. 다음 예를 `/mnt/my_nfs/config.yaml`에 쓸 수 있으며 아래 예에서 사용됩니다. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

이 yaml 예는 3개의 호스트에 있는 3개의 음성 컨테이너를 지정합니다. 첫 번째 호스트는 IPv4 주소로 지정되고, 두 번째 호스트는 배치-클라이언트와 동일한 VM에서 실행되며, 세 번째 컨테이너는 다른 VM의 DNS 호스트 이름으로 지정됩니다. `concurrency` 값은 동일한 컨테이너에서 실행할 수 있는 최대 동시 파일 전사를 지정합니다. `rtf`(Real-Time Factor) 값은 선택 사항이며 성능을 조정하는 데 사용할 수 있습니다.
 
일괄 처리 클라이언트는 컨테이너 다시 시작 또는 네트워킹 문제 등으로 인해 엔드포인트를 사용할 수 없게 되거나 다시 사용할 수 있게 된 시기를 동적으로 감지할 수 있습니다. 전사 요청은 사용할 수 없는 컨테이너로 전송되지 않으며 클라이언트는 사용 가능한 다른 컨테이너를 계속 사용합니다. 일괄 처리 진행을 중단하지 않고 언제든지 엔드포인트를 추가, 제거 또는 편집할 수 있습니다.


## <a name="run-the-batch-processing-container"></a>일괄 처리 컨테이너 실행
  
> [!NOTE] 
> * 이 예에서는 구성 파일과 입력, 출력 및 로그 디렉터리에 동일한 디렉터리(`/my_nfs`)를 사용합니다. 이러한 폴더에 호스트되거나 NFS로 탑재된 디렉터리를 사용할 수 있습니다.
> * `–h`를 사용하여 클라이언트를 실행하면 사용 가능한 명령줄 매개 변수와 해당 기본값이 나열됩니다. 
> * 일괄 처리 컨테이너는 Linux에서만 지원됩니다.

Docker `run` 명령을 사용하여 컨테이너를 시작합니다. 그러면 컨테이너 내에서 대화형 셸이 시작됩니다.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

일괄 처리 클라이언트를 실행하려면:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

단일 명령으로 일괄 처리 클라이언트 및 컨테이너를 실행하려면:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


클라이언트가 실행되기 시작합니다. 오디오 파일이 이전 실행에서 이미 전사된 경우 클라이언트는 파일을 자동으로 건너뜁니다. 일시적인 오류가 발생할 경우 자동 재시도 기능으로 파일이 전송되며 클라이언트가 다시 시도할 오류를 구분할 수 있습니다. 전사 오류 시 클라이언트는 전사를 계속하며 진행 상황에 영향을 주지 않고 다시 시도할 수 있습니다.  

## <a name="run-modes"></a>실행 모드 

일괄 처리 키트는 `--run-mode` 매개 변수를 사용하여 세 가지 모드를 제공합니다.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` 모드는 한 번의 일괄 처리로 오디오 파일을 입력 디렉터리 및 옵션 파일 목록에서 출력 폴더로 전사합니다.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Oneshot 모드에서 파일을 처리하는 일괄 처리 키트 컨테이너를 보여 주는 다이어그램":::

1. 일괄 처리 클라이언트가 `config.yaml` 파일에 사용할 음성 컨테이너 엔드포인트를 정의합니다. 
2. 전사를 위해 오디오 파일을 입력 디렉터리에 저장합니다.  
3. 디렉터리에서 컨테이너를 호출하면 파일 처리가 시작됩니다. 이전 실행에서 오디오 파일이 동일한 출력 디렉터리(동일한 파일 이름 및 체크섬)로 이미 전사된 경우 클라이언트는 해당 파일을 건너뜁니다. 
4. 파일은 1단계의 컨테이너 엔드포인트로 디스패치됩니다.
5. 로그 및 음성 컨테이너 출력이 지정된 출력 디렉터리에 반환됩니다. 

#### <a name="daemon"></a>[디먼](#tab/daemon)

> [!TIP]
> 여러 파일이 동시에 입력 디렉터리에 추가되는 경우 정기적으로 파일을 추가하여 성능을 향상시킬 수 있습니다.

`DAEMON` 모드는 지정된 폴더의 기존 파일을 전사하고 추가되는 새 오디오 파일을 계속 전사합니다.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="daemon 모드에서 파일을 처리하는 일괄 처리 키트 컨테이너를 보여 주는 다이어그램":::

1. 일괄 처리 클라이언트가 `config.yaml` 파일에 사용할 음성 컨테이너 엔드포인트를 정의합니다. 
2. 입력 디렉터리에서 컨테이너를 호출합니다. 일괄 처리 클라이언트가 디렉터리로 들어오는 파일에 대한 모니터링을 시작합니다. 
3. 입력 디렉터리에 대한 연속 오디오 파일 전송을 설정합니다. 이전 실행에서 오디오 파일이 동일한 출력 디렉터리(동일한 파일 이름 및 체크섬)로 이미 전사된 경우 클라이언트는 해당 파일을 건너뜁니다. 
4. 파일 쓰기 또는 POSIX 신호가 감지되면 컨테이너가 응답하도록 트리거됩니다.
5. 파일은 1단계의 컨테이너 엔드포인트로 디스패치됩니다.
6. 로그 및 음성 컨테이너 출력이 지정된 출력 디렉터리에 반환됩니다. 

#### <a name="rest"></a>[REST (영문)](#tab/rest)

`REST` 모드는 오디오 파일 일괄 제출, 상태 확인 및 긴 폴링을 위한 기본 HTTP 엔드포인트 집합을 제공하는 API 서버 모드입니다. 또한 Python 모듈 확장을 사용하거나 하위 모듈로 가져오기를 사용하여 프로그래밍 방식을 사용할 수 있습니다.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="REST 모드에서 파일을 처리하는 일괄 처리 키트 컨테이너를 보여 주는 다이어그램":::

1. 일괄 처리 클라이언트가 `config.yaml` 파일에 사용할 음성 컨테이너 엔드포인트를 정의합니다. 
2. API 서버의 엔드포인트 중 하나로 HTTP 요청을 보냅니다. 
        
    |엔드포인트  |설명  |
    |---------|---------|
    |`/submit`     | 새 일괄 처리 요청을 만들기 위한 엔드포인트입니다.        |
    |`/status`     | 일괄 처리 요청 상태를 확인하기 위한 엔드포인트입니다. 일괄 처리가 완료될 때까지 연결이 열린 상태로 유지됩니다.       |
    |`/watch`     | 일괄 처리가 완료될 때까지 HTTP 긴 폴링을 사용하기 위한 엔드포인트입니다.        |

3. 오디오 파일은 입력 디렉터리에서 업로드됩니다. 이전 실행에서 오디오 파일이 동일한 출력 디렉터리(동일한 파일 이름 및 체크섬)로 이미 전사된 경우 클라이언트는 해당 파일을 건너뜁니다. 
4. 요청이 `/submit` 엔드포인트로 전송되는 경우 파일은 1단계의 컨테이너 엔드포인트로 디스패치됩니다.
5. 로그 및 음성 컨테이너 출력이 지정된 출력 디렉터리에 반환됩니다. 

---

## <a name="logging"></a>로깅

> [!NOTE]
> *run.log* 파일이 너무 커지면 일괄 처리 클라이언트가 주기적으로 덮어쓸 수 있습니다.

클라이언트는 docker `run` 명령의 `-log_folder` 인수로 지정된 디렉터리에 *run.log* 파일을 생성합니다. 로그는 기본적으로 디버그 수준에서 캡처됩니다. 동일한 로그가 `stdout/stderr`로 전송되고 `-file_log_level` 또는 `console_log_level` 인수에 따라 필터링됩니다. 이 로그는 디버깅 또는 지원을 위해 추적을 보내야 하는 경우에만 필요합니다. 로깅 폴더에는 각 오디오 파일에 대한 Speech SDK 로그도 포함되어 있습니다.

`-output_folder`로 지정된 출력 디렉터리에는 *run_summary.json* 파일이 포함되는데, 이 파일은 30초마다 또는 새 전사 작업이 완료될 때마다 주기적으로 다시 작성됩니다. 이 파일을 사용하여 일괄 처리가 진행되는 동안 진행 상황을 확인할 수 있습니다. 또한 일괄 처리가 완료되면 모든 파일의 최종 실행 통계와 최종 상태가 포함됩니다. 프로세스가 완전히 종료되면 일괄 처리가 완료됩니다. 

## <a name="next-steps"></a>다음 단계

* [컨테이너 설치 및 실행 방법](speech-container-howto.md)
