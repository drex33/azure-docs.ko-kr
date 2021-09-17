---
title: azcopy list | Microsoft Docs
description: 이 문서에서는 azcopy list 명령에 관한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 09/21/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31bbabd194b2b2ef06266feb39b4d9618bbc0794
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435819"
---
# <a name="azcopy-list"></a>azcopy list

지정된 리소스의 엔터티를 나열합니다.

## <a name="synopsis"></a>개요

현재 릴리스에서는 Blob 컨테이너만 지원됩니다.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="examples"></a>예

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>옵션

|옵션|Description|
|--|--|
|-h, --help|list 명령에 대한 도움말 콘텐츠를 표시합니다.|
|--machine-readable|파일 크기(바이트)를 나열합니다.|
|--mega-units|단위를 1024가 아닌 1000 순서로 표시합니다.|
| --속성 |   구분 기호 (;) 목록 출력에 필요한 속성의 값을 구분 합니다. |
|--running-tally|총 파일 수와 해당 크기를 계산합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps float|전송 속도(초당 메가비트)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type string|명령 출력의 형식을 지정합니다. text, json 등을 선택할 수 있습니다. 기본값은 “text”입니다.|
|--trusted-microsoft-suffixes string   |Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.|

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
