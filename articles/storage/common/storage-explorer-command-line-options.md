---
title: Azure Storage Explorer 명령줄 옵션 | Microsoft Docs
description: Azure Storage Explorer 시작 명령줄 옵션 설명서
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: f5c43822bb9b856126c9b1ff80212d457044887b
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449534"
---
# <a name="azure-storage-explorer-command-line-options"></a>Azure Storage Explorer 명령줄 옵션

Microsoft Azure Storage Explorer에는 애플리케이션을 시작할 때 추가할 수 있는 명령줄 옵션 집합이 있습니다. 대부분의 명령줄 옵션은 디버깅 또는 문제 해결을 위한 옵션입니다.

## <a name="command-line-options"></a>명령줄 옵션
옵션  | Description
:------- | :-----------
`--debug`/`--prod`  | 디버그 또는 프로덕션 모드에서 애플리케이션을 시작합니다. 디버그 모드에서는 로컬 첨부 파일 데이터가 애플리케이션의 로컬 스토리지에 저장되며 암호화되지 않습니다. 숨겨진 속성은 선택한 리소스 노드의 속성 패널에 표시됩니다. 로그의 세부 정보 표시 수준은 Storage Explorer의 내부 설정 논리를 보여 주는 디버그 메시지를 인쇄하도록 설정됩니다. 기본값은 `--prod`입니다.
`--lang`  | 지정된 언어로 애플리케이션을 시작합니다. 예들 들어 `--lang="zh-Hans"`입니다.
`--disable-gpu` | GPU 가속 없이 애플리케이션을 시작합니다.
`--auto-open-dev-tools` | 브라우저 창이 표시되는 즉시 애플리케이션이 개발자 도구 창을 열도록 합니다. 이 옵션은 브라우저 창의 시작 코드 줄에서 중단점을 넣으려는 경우에 유용합니다.
`--verbosity` | Storage Explorer 로깅의 세부 정보 표시 수준을 설정합니다. 지원되는 세부 정보 표시 수준은 `debug`, `verbose`, `info`, `warn`, `error`, `silent`가 있습니다. 예들 들어 `--verbosity=verbose`입니다. 프로덕션 모드에서 실행하는 경우 기본 세부 정보 표시 수준은 `info`입니다. 디버그 모드에서 실행하는 경우 로그 세부 정보 표시 수준은 항상 `debug`입니다.
`--log-dir` | 로그 파일을 저장할 디렉터리를 설정합니다. 예들 들어 `--log-dir=path_to_a_directory`입니다.

사용자 지정 명령줄 옵션을 사용하여 Storage Explorer를 시작하는 예제

```shell
./MicrosoftAzureStorageExplorer --lang=en --auto-open-dev-tools
```

> [!NOTE]
> 해당 명령줄 옵션은 새 Storage Explorer 버전에서 변경될 수 있습니다.

## <a name="when-to-use-command-line-options"></a>명령줄 옵션을 사용해야 하는 경우

일부 명령줄 옵션을 사용하여 Storage Explorer를 사용자 지정할 수 있습니다. `--lang`과 같이 해당하는 사용자 설정이 있는 옵션의 경우, 명령줄 옵션을 사용하는 대신 사용자 설정을 사용하는 것이 좋습니다. 

다른 명령줄 옵션은 디버깅 및 문제 해결에 유용할 수 있습니다. Storage Explorer에서 문제가 발생하는 경우 디버그 모드에서 문제를 재현하면 더 자세한 정보를 얻어 조사할 수 있습니다.