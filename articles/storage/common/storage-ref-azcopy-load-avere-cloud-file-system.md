---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: 이 문서에서는 azcopy load clfs 명령에 관한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b678b2830ede076169f82c4a3280845edd7db90b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624921"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

로컬 데이터를 컨테이너로 전송하고 Microsoft Avere CLFS(Cloud FileSystem) 형식으로 저장합니다.

## <a name="synopsis"></a>개요

load 명령을 사용하면 Azure Blob Storage 컨테이너에 데이터를 복사한 다음 Microsoft Avere CLFS(Cloud FileSystem) 형식으로 해당 데이터를 저장합니다. 전용 CLFS 형식은 Azure HPC 캐시 및 Avere vFXT for Azure 제품에서 사용됩니다.

이 명령을 활용하려면 pip3 install clfsload~=1.0.23을 통해 필수 확장을 설치합니다. CLFSLoad.py가 경로에 있는지 확인합니다. 이 단계에 대한 자세한 내용은 [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs)를 참조하세요.

이 명령은 특정 Microsoft 고성능 컴퓨팅 캐시 제품에서 사용하기 위해 기존 데이터를 클라우드 스토리지로 이동하는 간단한 옵션입니다.

이러한 제품은 전용 클라우드 파일 시스템 형식을 사용하여 데이터를 관리하기 때문에 네이티브 복사 명령을 통해 해당 데이터를 로드할 수 없습니다.

대신 캐시 제품 자체를 통하거나 올바른 전용 형식을 사용하는 이 load 명령을 통해 데이터를 로드해야 합니다.
이 명령을 사용하여 캐시를 사용하지 않고 데이터를 전송할 수 있습니다. 예를 들어 스토리지를 미리 채우거나 캐시 로드를 늘리지 않고 작업 집합에 파일을 추가합니다.

대상은 빈 Azure Storage 컨테이너입니다. 전송이 완료되면 Azure HPC Cache instance 또는 Avere vFXT for Azure 클러스터에서 대상 컨테이너를 사용할 수 있습니다.

> [!NOTE]
> 이는 load 명령의 미리 보기 릴리스입니다. 문제가 있을 시 AzCopy GitHub 리포지토리에 보고하세요.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="examples"></a>예

CLFS 형식의 SAS를 사용하여 컨테이너에 전체 디렉터리를 로드합니다.

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>옵션

**--compression-type** 문자열   전송에 사용할 압축 유형을 지정합니다. 사용 가능한 값은 `DISABLED`, `LZ4`입니다. (기본값 `LZ4`)

**--help**    `azcopy load clfs` 명령에 대한 도움말입니다.

**--log-level** 문자열   로그 파일의 로그 세부 정보 표시를 정의합니다. 사용 가능한 수준은 `DEBUG`, `INFO`, `WARNING`, `ERROR`입니다. (기본값 `INFO`)

**--max-errors** uint32   허용할 최대 전송 실패 횟수를 지정합니다. 오류가 발생하는 경우 즉시 작업을 중지합니다.

**--new-session**   기존 작업을 계속하는 대신 추적 정보가 `--state-path`에 유지되는 새 작업을 시작합니다. (기본값 true)

**--preserve-hardlinks**    하드 링크 관계를 유지합니다.

**--state-path** 문자열   작업 상태 추적을 위한 로컬 디렉토리의 필요한 경로입니다. 작업을 다시 시작하려면 경로가 기존 디렉터리를 가리켜야 합니다. 새 작업에 대해 비어 있어야 합니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps float|전송 속도(초당 메가비트)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type string|명령 출력의 형식을 지정합니다. text, json 등을 선택할 수 있습니다. 기본값은 “text”입니다.|
|--trusted-microsoft-suffixes string   | Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.|

## <a name="see-also"></a>참조

- [azcopy](storage-ref-azcopy.md)
