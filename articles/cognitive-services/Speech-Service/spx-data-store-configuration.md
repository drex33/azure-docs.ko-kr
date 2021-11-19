---
title: Speech CLI 구성 옵션 - Speech Service
titleSuffix: Azure Cognitive Services
description: Azure Speech CLI에서 사용할 구성 파일을 만들고 관리하는 방법에 대해 알아보세요.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: eur
ms.openlocfilehash: 6b830d9afd3177aaa9efd0effb7fa8d3fd2e46dc
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719925"
---
# <a name="speech-cli-configuration-options"></a>Speech CLI 구성 옵션

Speech CLI의 동작은 `@` 기호를 사용하여 참조할 수 있는 구성 파일의 설정에 의존할 수 있습니다. Speech CLI는 Speech CLI의 현재 작업 디렉터리에서 만든 새 `./spx/data` 하위 디렉터리에 새 설정을 저장합니다. 구성 값을 찾을 때 Speech CLI는 현재 작업 디렉터리, `./spx/data`의 데이터 저장소, `spx` 이진의 마지막 읽기 전용 데이터 저장소를 포함한 다른 데이터 저장소에서 차례로 검색합니다. 

Speech CLI 빠른 시작에서 데이터 저장소를 사용하여 `@key` 및 `@region` 값을 저장했으므로 각 `spx` 명령으로 지정할 필요가 없었습니다. 구성 파일을 사용하여 사용자 고유의 구성 설정을 저장하거나, 런타임에 생성된 URL 또는 기타 동적 콘텐츠를 전달하는 데 사용할 수도 있습니다.

> [!NOTE]
> PowerShell에서 [stop-parsing 토큰](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_special_characters?view=powershell-7.2#stop-parsing-token---)(`--%`)은 `spx` 다음에 와야 합니다. 예를 들어 `spx --% config @region`을 실행하여 현재 지역 구성 값을 확인합니다.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>데이터 저장소의 구성 파일 만들기 및 관리

이 섹션에서는 `spx config`를 사용하여 명령 설정을 저장 및 가져오고 `--output` 옵션을 사용하여 Speech CLI의 출력을 저장하기 위해 로컬 데이터 저장소의 구성 파일을 사용하는 방법을 보여줍니다.

다음 예제에서는 `@my.defaults` 구성 파일을 지우고, 파일에 **키** 및 **지역** 에 대한 키-값 쌍을 추가하고, `spx recognize`에 대한 호출에서 구성을 사용합니다.

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

동적 콘텐츠를 구성 파일에 쓸 수도 있습니다. 예를 들어, 다음 명령은 사용자 지정 음성 모델을 만들고 새 모델의 URL을 구성 파일에 저장합니다. 다음 명령은 해당 URL의 모델이 사용할 준비가 될 때까지 기다린 후 반환합니다.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

다음 예제에서는 `@my.datasets.txt` 구성 파일에 두 개의 URL을 작성합니다. 이 시나리오에서 `--output`은 구성 파일을 만들거나 기존 파일에 추가하기 위한 선택적 **add** 키워드를 포함할 수 있습니다.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

기본 구성 파일(명령별 기본 설정의 `@spx.default`, `@default.config` 및 `@*.default.config`) 사용을 포함하여 데이터 저장소 파일에 대한 자세한 내용을 보려면 다음 명령을 입력하세요.

```console
spx help advanced setup
```

## <a name="next-steps"></a>다음 단계 

* [Speech CLI를 사용한 일괄 처리 작업](./spx-batch-operations.md)
