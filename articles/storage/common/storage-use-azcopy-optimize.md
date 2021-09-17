---
title: Azure Storage를 사용하여 AzCopy v10의 성능 최적화 | Microsoft Docs
description: 이 문서는 Azure Storage를 사용하여 AzCopy v10의 성능을 최적화하는 데 도움이 됩니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 9870ff7f2033372e63de80138b2f37b3896eb4e6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427609"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Azure Storage를 사용하여 AzCopy의 성능 최적화

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 성능을 최적화하는 데 도움이 됩니다.

> [!NOTE]
> AzCopy를 시작하는 데 도움이 되는 콘텐츠를 찾고 있다면 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조하세요.

성능을 벤치마크한 다음 명령과 환경 변수를 사용하여 성능과 리소스 사용 간의 최적의 절충점을 찾을 수 있습니다.

## <a name="run-benchmark-tests"></a>벤치마크 테스트 실행

특정 Blob 컨테이너 또는 파일 공유에서 성능 벤치마크 테스트를 실행하여 일반 성능 통계를 보고 성능 병목 상태를 식별할 수 있습니다. 생성된 테스트 데이터를 업로드하거나 다운로드하여 테스트를 실행할 수 있습니다. 

다음 명령을 사용하여 성능 벤치마크 테스트를 실행합니다.

**구문**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**예제**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

이 명령은 지정된 대상에 테스트 데이터를 업로드하여 성능 벤치마크를 실행합니다. 테스트 데이터는 메모리에서 생성되고 대상에 업로드된 다음, 테스트가 완료된 후 대상에서 삭제됩니다. 선택적 명령 매개 변수를 사용하여 생성할 파일 수와 파일 크기를 지정할 수 있습니다.

데이터를 다운로드하여 이 테스트를 실행하려면 `mode` 매개 변수를 `download`로 설정합니다. 자세한 참조 문서는 [azcopy benchmark](storage-ref-azcopy-bench.md)를 참조하세요. 

## <a name="optimize-for-large-numbers-of-small-files"></a>많은 수의 작은 파일에 대한 최적화

작은 파일을 전송하는 경우, 특히 많은 수의 파일을 전송할 때 처리량이 줄어들 수 있습니다. 성능을 최대화하려면 각 작업의 크기를 줄여야 합니다. 다운로드 및 업로드 작업의 경우 동시성을 늘리고, 로그 작업을 줄이며, 성능 비용을 많이 발생시키는 기능을 해제합니다.

#### <a name="reduce-the-size-of-each-job"></a>각 작업의 크기 줄이기

성능을 최적화하려면 각 작업에서 1천만 개 미만의 파일을 전송해야 합니다. 5천만 개 이상의 파일을 전송하는 작업은 AzCopy 작업 추적 메커니즘이 상당한 양의 오버헤드를 발생시킬 수 있기 때문에 성능이 저하될 수 있습니다. 오버헤드를 줄이려면 대규모 작업을 더 작은 작업으로 분할하는 것이 좋습니다. 

작업의 크기를 줄이는 한 가지 방법은 작업의 영향을 받는 파일 수를 제한하는 것입니다. 명령 매개 변수를 사용하여 이 작업을 수행할 수 있습니다. 예를 들어 작업은 [azcopy copy](storage-ref-azcopy-copy.md) 명령의 일부로 `include path` 매개 변수를 사용하여 디렉터리의 하위 집합만 복사할 수 있습니다. 

`include-pattern` 매개 변수를 사용하여 특정 확장명을 가진 파일(예: `*.pdf`)을 복사합니다. 별도의 작업에서 `exclude-pattern` 매개 변수를 사용하여 `*.pdf` 확장명이 없는 모든 파일을 복사합니다. 예제는 [특정 파일 업로드](storage-use-azcopy-blobs-upload.md#upload-specific-files) 및 [특정 blob 다운로드](storage-use-azcopy-blobs-download.md#download-specific-blobs)를 참조하세요.

대규모 작업을 더 작은 작업으로 나누는 방법을 결정한 후에는 두 개 이상의 VM(가상 머신)에서 작업을 실행하는 것이 좋습니다.

#### <a name="increase-concurrency"></a>동시성 향상

파일을 업로드하거나 다운로드하는 경우 `AZCOPY_CONCURRENCY_VALUE` 환경 변수를 사용하여 컴퓨터에서 발생할 수 있는 동시 요청 수를 늘립니다. 이 변수는 컴퓨터의 성능을 저하시키지 않는 범위에서 최대한 높게 설정합니다. 이 변수에 대한 자세한 내용은 이 문서의 [동시 요청 수 향상](#increase-the-number-of-concurrent-requests) 섹션을 참조하세요.

스토리지 계정 간에 Blob을 복사하는 경우 `AZCOPY_CONCURRENCY_VALUE` 환경 변수의 값을 `1000`보다 큰 값으로 설정하는 것이 좋습니다. AzCopy는 서버 간 API를 사용하므로 데이터가 스토리지 서버 간에 직접 복사되고 컴퓨터의 처리 능력을 사용하지 않아 이 변수를 높게 설정할 수 있습니다.  

#### <a name="decrease-the-number-of-logs-generated"></a>생성된 로그 수 감소

AzCopy가 작업을 완료할 때 만드는 로그 항목 수를 줄여 성능을 향상시킬 수 있습니다. 기본값으로 AzCopy는 작업과 관련된 모든 작업을 기록합니다. 최적의 성능을 얻으려면 복사, 동기화 또는 제거 명령의 `log-level` 매개 변수를 `ERROR`로 설정하는 것이 좋습니다. 이렇게 하면 AzCopy는 오류만 기록합니다. 기본값으로 값 로그 수준은 `INFO`로 설정됩니다. 

#### <a name="turn-off-length-checking"></a>길이 검사 끄기 

파일을 업로드하거나 다운로드하는 경우 복사 및 동기화 명령의 `--check-length`을 `false`로 설정하는 것이 좋습니다. 이렇게 하면 AzCopy가 전송 후 파일 길이를 확인하지 않습니다. 기본값으로 AzCopy는 전송이 완료된 후 원본 및 대상 파일이 일치하는지 확인하기 위해 길이를 확인합니다. AzCopy는 각 파일을 전송한 후 이 검사를 수행합니다. 이 검사는 많은 수의 작은 파일을 전송하는 작업의 성능을 저하시킬 수 있습니다. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>동시 로컬 검사 켜기(Linux)

일부 Linux 시스템의 파일 검사는 모든 병렬 네트워크 연결을 포화시킬 만큼 빠르게 실행되지 않습니다. 이러한 경우 `AZCOPY_CONCURRENT_SCAN`를 `true`로 설정할 수 있습니다. 

## <a name="increase-the-number-of-concurrent-requests"></a>동시 요청 수의 증가

`AZCOPY_CONCURRENCY_VALUE` 환경 변수를 설정하여 처리량을 늘릴 수 있습니다. 이 변수는 발생할 수 있는 동시 요청 수를 지정합니다.  

컴퓨터에 5개 미만의 CPU가 있는 경우 이 변수의 값은 `32`로 설정됩니다. 그렇지 않으면 기본값은 16에 CPU 수를 곱한 값과 같습니다. 이 변수의 최대 기본값은 `300`이지만 수동으로 이 값을 더 높거나 낮게 설정할 수 있습니다. 

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

`azcopy env`를 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 AzCopy 로그 파일의 시작 부분을 확인하여 사용 중인 값을 읽을 수 있습니다. 선택한 값과 선택한 이유가 여기에 보고됩니다.

이 변수를 설정하기 전에 벤치마크 테스트를 실행하는 것이 좋습니다. 벤치마크 테스트 프로세스는 권장 동시성 값을 보고합니다. 또는 네트워크 조건과 페이로드가 다를 경우 이 변수를 특정 숫자 대신 단어 `AUTO`로 설정합니다. 그러면 AzCopy가 항상 벤치마크 테스트에서 사용하는 것과 같은 자동 튜닝 프로세스를 실행합니다.

## <a name="limit-the-throughput-data-rate"></a>처리량 데이터 속도 제한

명령에서 `cap-mbps` 플래그를 사용하여 처리량 데이터 속도를 제한할 수 있습니다. 예를 들어 다음 명령은 작업을 계속하고 처리량을 초당 `10`MB(메가비트)로 제한합니다. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>메모리 사용 최적화

`AZCOPY_BUFFER_GB` 환경 변수를 설정하여 AzCopy가 파일을 다운로드하고 업로드할 때 버퍼링에 사용할 최대 시스템 메모리 크기를 지정합니다. 이 값을 기가바이트(GB) 단위로 표시합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> 작업을 추적하면 항상 메모리 사용량에 추가 오버헤드가 발생합니다. 사용량은 작업의 전송 수에 따라 달라집니다. 버퍼는 메모리 사용량의 가장 큰 구성 요소입니다. `AZCOPY_BUFFER_GB`를 사용하여 대략적인 요구 사항을 충족함으로써 오버헤드를 제어할 수 있지만, 전체 메모리 사용량을 엄격하게 제한하는 플래그는 사용할 수 없습니다.

## <a name="optimize-file-synchronization"></a>파일 동기화 최적화

[sync](storage-ref-azcopy-sync.md) 명령은 대상의 모든 파일을 식별한 다음, 동기화 작업을 시작하기 전에 파일 이름과 마지막으로 수정된 타임스탬프를 비교합니다. 파일 수가 많은 경우 이 선행 처리를 제거하여 성능을 개선할 수 있습니다. 

그러려면 대신 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하고 `--overwrite` 플래그를 `ifSourceNewer`로 설정합니다. AzCopy는 선행 검사 및 비교를 수행하지 않고 파일을 복사할 때 파일을 비교합니다. 따라서 비교할 파일이 많은 경우 성능 우위를 제공합니다.

[azcopy copy](storage-ref-azcopy-copy.md) 명령은 대상에서 파일을 삭제하지 않으므로, 원본에 파일이 더는 존재하지 않을 때 대상에서 해당 파일을 삭제하려면 `--delete-destination` 플래그를 `true` 또는 `prompt` 값으로 설정하여 [azcopy sync](storage-ref-azcopy-sync.md) 명령을 사용합니다.

## <a name="see-also"></a>참조

- [AzCopy 시작](storage-use-azcopy-v10.md)
