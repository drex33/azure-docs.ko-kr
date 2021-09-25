---
title: azcopy bench | Microsoft Docs
description: 이 문서에서는 azcopy bench 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 6bd80367ab407be3d6b43750c6525b2d2ae30200
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605870"
---
# <a name="azcopy-benchmark"></a>azcopy benchmark

지정된 대상에 테스트 데이터를 업로드하거나 지정된 대상에서 테스트 데이터를 다운로드하여 성능 벤치마크를 실행합니다. 업로드의 경우 테스트 데이터가 자동으로 생성됩니다.

벤치마크 명령은 다음 사항을 제외하고는 ‘복사’와 동일한 프로세스를 실행합니다.

  - 벤치마크는 원본 및 대상 매개 변수를 둘 다 사용하는 것이 아니라 하나만 사용합니다. 업로드하거나 다운로드할 Blob 컨테이너, Azure Files 공유 또는 Azure Data Lake Storage Gen2 파일 시스템입니다.

  - ‘mode’ 매개 변수는 AzCopy가 지정된 대상에서 업로드를 테스트해야 하는지 다운로드를 테스트해야 하는지 설명합니다. 유효한 값은 ‘Upload’ 및 ‘Download’입니다. 기본값은 ‘Upload’입니다.

  - 업로드 벤치마크의 경우 자동으로 생성되는 파일의 수와 파일의 중요도를 제어하는 명령줄 매개 변수를 통해 페이로드가 설명됩니다. 생성 프로세스는 메모리에서만 발생합니다. 디스크가 사용되지 않습니다.

  - 다운로드의 경우 페이로드는 원본에 이미 있는 파일로 구성됩니다. (필요한 경우 테스트 파일을 생성하는 방법에 대한 아래 예제를 참조하세요.)

  - 복사 명령에 사용할 수 있는 몇 가지 선택적 매개 변수만 지원됩니다.

  - 추가 진단이 측정되고 보고됩니다.

  - 업로드의 경우 기본 동작은 테스트 실행이 끝날 때 전송된 데이터를 삭제하는 것입니다.  다운로드의 경우 데이터가 로컬에 저장되지 않습니다.

벤치마크 모드에서는 최대 처리량을 제공하는 병렬 TCP 연결 수에 맞게 자동으로 튜닝됩니다. 끝에는 해당 숫자가 표시됩니다. 자동 튜닝을 방지하려면 AZCOPY_CONCURRENCY_VALUE 환경 변수를 특정 연결 수로 설정합니다.

일반적인 인증 유형은 모두 지원됩니다. 하지만 가장 간편한 업로드 벤치마크 방법은 일반적으로 SAS 토큰이 포함된 빈 컨테이너를 만들어 SAS 인증을 사용하는 것입니다. (다운로드 모드를 사용하려면 대상 컨테이너에 테스트 데이터 세트가 있어야 합니다.)

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [Azure Storage를 사용하여 AzCopy v10의 성능 최적화](storage-use-azcopy-optimize.md)

## <a name="examples"></a>예

```azcopy
azcopy benchmark [destination] [flags]
```

기본 매개 변수를 사용하여 벤치마크 테스트를 실행합니다(최대 1Gbps인 네트워크를 벤치마크하는 데 적합함).

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```

각각의 크기가 2GiB인 파일 100개를 업로드하는 벤치마크 테스트를 실행합니다(빠른 네트워크(예: 10Gbps)에서 벤치마크하는 데 적합함).

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```

벤치마크 테스트를 실행하되 각각의 크기가 8MiB인 파일 50,000개를 사용하고 해당 MD5 해시를 계산합니다(복사 명령에서 `--put-md5` 플래그가 수행하는 방식과 동일함). 벤치마크 시 `--put-md5`의 용도는 MD5 계산이 선택된 파일 수 및 크기의 처리량에 영향을 주는지 테스트하는 것입니다.

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

대상에서 기존 파일을 다운로드하는 벤치마크 테스트를 실행합니다.

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

전송된 파일을 삭제하지 않는 업로드를 실행합니다. (해당 파일은 다운로드 테스트에 대한 페이로드로 사용할 수 있습니다.)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>옵션

**--blob-type** 문자열: 대상에서 Blob 형식을 정의합니다. 다양한 Blob 형식을 벤치마크하도록 허용하는 데 사용합니다. 복사 명령에 있는 같은 이름의 매개 변수와 동일합니다(기본값 “Detect”).

**--block-size-mb** float: 이 블록 크기를 사용합니다(MiB로 지정됨). 기본값은 파일 크기에 따라 자동으로 계산됩니다. 소수를 사용할 수 있습니다(예: 0.25). 복사 명령에 있는 같은 이름의 매개 변수와 동일합니다.

**--check-length**: 전송 후 대상에서 파일의 길이를 확인합니다. 원본과 대상이 일치하지 않는 경우 전송이 실패한 것으로 표시됩니다. (기본값: true)

**--delete-test-data**: true이면 벤치마크 실행이 끝날 때 벤치마크 데이터가 삭제됩니다.  데이터를 대상에 유지하려는 경우, 예를 들어 벤치마크 모드 이외의 모드에서 수동 테스트에 사용하려는 경우 false로 설정하세요(기본값: true).

**--file-count** uint:  사용하려는 자동 생성된 데이터 파일 수입니다(기본값: 100).

**--help**: bench에 대한 도움말입니다.

**--log-level** 문자열: 로그 파일의 로그 세부 정보 표시 수준을 정의합니다. 사용할 수 있는 수준으로는 INFO(모든 요청/응답), WARNING(느린 응답), ERROR(실패한 요청만), NONE(출력 로그 없음)이 있습니다. (기본값: “INFO”)

**--mode** 문자열: Azcopy가 이 대상에서 업로드를 테스트해야 하는지 다운로드를 테스트해야 하는지 정의합니다. 유효한 값은 ‘upload’ 및 ‘download’입니다. 기본 옵션은 ‘upload’입니다. (기본값: ‘upload’)

**--number-of-folders** uint: 0보다 큰 경우 폴더를 만들어 데이터를 나눕니다.

**--put-md5**: 각 파일의 MD5 해시를 만들고 대상 Blob/파일의 Content-MD5 속성으로 해시를 저장합니다. (기본적으로 해시를 만들지 않습니다.) 복사 명령에 있는 같은 이름의 매개 변수와 동일합니다.

**--size-per-file** 문자열: 자동 생성되는 각 데이터 파일의 크기입니다. 바로 뒤에 K, M 또는 G가 오는 숫자여야 합니다. 예를 들어 12k 또는 200G가 있습니다(기본값 “250M”).

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

**--cap-mbps float**: 전송 속도(Mbps)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.

**--output-type** 문자열: 명령 출력의 형식입니다. text, json 중에서 선택할 수 있습니다. 기본값은 ‘text’입니다. (기본값: ‘text’)

**--trusted-microsoft-suffixes** 문자열: Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.

## <a name="see-also"></a>참조

- [azcopy](storage-ref-azcopy.md)
