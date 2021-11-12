---
title: 디바이스 모델 리포지토리의 개념 이해 | Microsoft Docs
description: 솔루션 개발자 또는 IT 전문가는 디바이스 모델 리포지토리의 기본 개념에 대해 알아봅니다.
author: rido-min
ms.author: rmpablos
ms.date: 11/12/2021
ms.topic: conceptual
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 7b32983707f2c23ef6385fc974f4f1d50ac48e50
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399299"
---
# <a name="device-models-repository"></a>디바이스 모델 리포지토리

디바이스 모델 리포지토리(DMR)를 사용하면 디바이스 빌더가 IoT 플러그 앤 플레이 디바이스 모델을 관리하고 공유할 수 있습니다. 디바이스 모델은 [DTDL(Digital Twins 모델링 언어)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)을 사용하여 정의된 JSON LD 문서입니다.

DMR은 DTMI(디바이스 쌍 모델 식별자)를 기반으로 하는 폴더 구조에 DTDL 인터페이스를 저장하는 패턴을 정의합니다. DTMI를 상대 경로로 변환하여 DMR에서 인터페이스를 찾을 수 있습니다. 예를 들어 `dtmi:com:example:Thermostat;1` dtmi는를로 변환 하 `/dtmi/com/example/thermostat-1.json` 고 url의 공용 기준 url에서 가져올 수 있습니다 `devicemodels.azure.com` [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.json) .

## <a name="index-expanded-and-metadata"></a>인덱스, 확장 및 메타 데이터

DMR 규칙에는 호스트 된 모델의 사용을 단순화 하기 위한 추가 아티팩트가 포함 됩니다. 이러한 기능은 사용자 지정 또는 개인 리포지토리의 경우 _선택 사항_ 입니다.

- _Index_. 사용 가능한 모든 DTMIs는 json 파일의 시퀀스로 구성 된 *인덱스* 를 통해 노출 됩니다. 예를 들면 다음과 같습니다. [https://devicemodels.azure.com/index.page.2.json](https://devicemodels.azure.com/index.page.2.json)
- _확장_ 됨. 모든 종속성을 포함 하는 파일은 각 인터페이스에서 사용할 수 있습니다. 예를 들면 다음과 같습니다. [https://devicemodels.azure.com/dtmi/com/example/temperaturecontroller-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/temperaturecontroller-1.expanded.json)
- _Metadata_. 이 파일은 리포지토리의 주요 특성을 노출 하며, 최신 게시 된 모델 스냅숏으로 정기적으로 새로 고쳐집니다. 여기에는 모델 인덱스 또는 확장 된 모델 파일을 사용할 수 있는지 여부와 같이 리포지토리에서 구현 하는 기능이 포함 됩니다. 에서 DMR 메타 데이터에 액세스할 수 있습니다. [https://devicemodels.azure.com/metadata.json](https://devicemodels.azure.com/metadata.json)

## <a name="public-device-models-repository"></a>공용 디바이스 모델 리포지토리

Microsoft는 다음과 같은 특징을 가진 공용 DMR을 호스팅합니다.

- 큐레이팅된 모델. Microsoft는 GitHub PR(끌어오기 요청) 유효성 검사 워크플로를 사용하여 사용 가능한 모든 인터페이스를 검토하고 승인합니다.
- 불변성.  게시된 후에는 인터페이스를 업데이트할 수 없습니다.
- 하이퍼스케일. Microsoft는 디바이스 모델을 게시하고 사용할 수 있는 안전하고 확장성 있는 엔드포인트를 만드는 데 필요한 인프라를 제공합니다.

## <a name="custom-device-models-repository"></a>사용자 지정 디바이스 모델 리포지토리

동일한 DMR 패턴을 사용하여 로컬 파일 시스템 또는 사용자 지정 HTTP 웹 서버와 같은 모든 스토리지 매체에서 사용자 지정 DMR을 만듭니다. DMR에 액세스하는 데 사용되는 기본 URL을 변경하여 공용 DMR에서와 동일한 방법으로 사용자 지정 DMR에서 디바이스 모델을 검색할 수 있습니다.

> [!NOTE]
> Microsoft는 공용 DMR에서 디바이스 모델의 유효성을 검사하는 도구를 제공합니다. 사용자 지정 리포지토리에서 이러한 도구를 다시 사용할 수 있습니다.

## <a name="public-models"></a>공용 모델

모델 리포지토리에 저장된 공용 디바이스 모델은 모든 사용자가 애플리케이션에서 사용하고 통합할 수 있습니다. 공용 디바이스 모델을 사용하면 디바이스 빌더와 솔루션 개발자가 IoT 플러그 앤 플레이 디바이스 모델을 공유하고 다시 사용할 수 있는 개방형 에코 시스템을 사용할 수 있습니다.

모델 리포지토리에 모델을 게시하여 공용으로 만드는 방법에 대한 지침은 [모델 게시](#publish-a-model) 섹션을 참조하세요.

사용자는 공식 [GitHub 리포지토리](https://github.com/Azure/iot-plugandplay-models)에서 공용 인터페이스를 찾아보고 검색하며 볼 수 있습니다.

`dtmi` 폴더의 모든 인터페이스는 공용 엔드포인트 [https://devicemodels.azure.com](https://devicemodels.azure.com)에서도 사용할 수 있습니다

### <a name="resolve-models"></a>모델 확인

이러한 인터페이스에 프로그래밍 방식으로 액세스하려면 NuGet 패키지 [Azure.IoT.ModelsRepository](https://www.nuget.org/packages/Azure.IoT.ModelsRepository)에서 사용할 수 있는 `ModelsRepositoryClient`를 사용할 수 있습니다. 이 클라이언트는 기본적으로 [devicemodels.azure.com](https://devicemodels.azure.com/)에서 사용 가능한 공용 DMR을 쿼리하도록 구성되며 모든 사용자 정의 리포지토리에 구성할 수 있습니다.

클라이언트는 `DTMI`를 입력으로 수락하고 모든 필수 인터페이스와 함께 사전을 반환합니다.

```cs
using Azure.IoT.ModelsRepository;

var client = new ModelsRepositoryClient();
IDictionary<string, string> models = client.GetModels("dtmi:com:example:TemperatureController;1");
models.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

예상 출력은 종속성 체인에 있는 세 가지 인터페이스의 `DTMI`를 표시해야 합니다.

```txt
dtmi:com:example:TemperatureController;1
dtmi:com:example:Thermostat;1
dtmi:azure:DeviceManagement:DeviceInformation;1
```

`ModelsRepositoryClient`는 http(s)를 통해 사용할 수 있는 사용자 지정 모델 리포지토리를 쿼리하도록 구성하고 사용 가능한 `ModelDependencyResolution`를 사용하여 종속성 확인을 지정할 수 있습니다.

- 사용 안 함. 종속성 없이 지정된 인터페이스만 반환합니다.
- 사용. 종속성 체인의 모든 인터페이스를 반환합니다.

> [!Tip] 
> 사용자 지정 리포지토리는 `.expanded.json` 파일을 노출하지 않을 수 있으며, 사용할 수 없는 경우 클라이언트는 각 종속성을 로컬로 처리하도록 대체합니다.

다음 샘플 코드는 사용자 지정 리포지토리 기본 URL을 사용하여 `ModelsRepositoryClient`를 초기화하는 방법을 보여줍니다. 이 경우에는 `raw` 엔드포인트에서 사용할 수 없기 때문에 `expanded` 형식을 사용하지 않고 GitHub API의 `raw` URL을 사용합니다. 클라이언트에서 수행하는 HTTP 요청을 검사하기 위해 `AzureEventSourceListener`가 초기화됩니다.

```cs
using AzureEventSourceListener listener = AzureEventSourceListener.CreateConsoleLogger();

var client = new ModelsRepositoryClient(
    new Uri("https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main"));

ModelResult model = await client.GetModelAsync(
    "dtmi:com:example:TemperatureController;1", 
    dependencyResolution: ModelDependencyResolution.Enabled);

model.Content.Keys.ToList().ForEach(k => Console.WriteLine(k));
```

Azure SDK GitHub 리포지토리의 소스 코드 내에서 사용할 수 있는 샘플은 다음과 같습니다. [Azure.Iot.ModelsRepository/samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/modelsrepository/Azure.IoT.ModelsRepository/samples)

## <a name="publish-a-model"></a>모델 게시

> [!Important]
> 공용 DMR에 모델을 제출할 수 있으려면 GitHub 계정이 있어야 합니다.

1. 공용 GitHub 리포지토리를 포크합니다. [https://github.com/Azure/iot-plugandplay-models](https://github.com/Azure/iot-plugandplay-models)
1. 포크된 리포지토리를 복제합니다. 필요에 따라 새 분기를 만들어 변경 내용을 `main` 분기와 격리하십시오.
1. 폴더/파일 이름 규칙을 사용하여 `dtmi` 폴더에 새 인터페이스를 추가합니다. 자세한 내용은 [`dtmi/` 폴더로 모델 가져오기](#import-a-model-to-the-dtmi-folder)를 참조하세요.
1. `dmr-client` 도구를 사용하여 로컬에서 모델의 유효성을 검사합니다. 자세한 내용은 [모델 유효성 검사](#validate-models)를 참조하세요.
1. 로컬에서 변경 내용을 커밋하고 포크로 푸시합니다.
1. 포크에서 `main` 분기를 대상으로 하는 끌어오기 요청을 만듭니다. [문제 만들기 또는 끌어오기 요청](https://docs.github.com/free-pro-team@latest/desktop/contributing-and-collaborating-using-github-desktop/creating-an-issue-or-pull-request) 문서를 참조하세요.
1. [끌어오기 요청 요구 사항](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)을 검토합니다.

끌어오기 요청은 제출된 인터페이스의 유효성을 검사하는 GitHub 작업 집합을 트리거하고 끌어오기 요청이 모든 요구 사항을 충족하는지 확인합니다.

Microsoft는 영업일 3일 이내에 모든 검사를 수행하여 끌어오기 요청에 응답합니다.

### <a name="dmr-client-tools"></a>`dmr-client` 도구

PR 검사 중 모델의 유효성을 검사하는 데 사용되는 도구를 활용하여 DTDL 인터페이스를 로컬로 추가하고 유효성을 검사할 수도 있습니다.

> [!NOTE]
> 이 도구를 사용하려면 [.NET SDK](https://dotnet.microsoft.com/download) 버전 3.1 이상이 필요합니다.

### <a name="install-dmr-client"></a>`dmr-client` 설치

```bash
dotnet tool install --global Microsoft.IoT.ModelsRepository.CommandLine --version 1.0.0-beta.5
```

### <a name="import-a-model-to-the-dtmi-folder"></a>모델을 `dtmi/` 폴더로 가져오기

모델이 이미 json 파일에 저장되어 있는 경우 `dmr-client import` 명령을 사용하여 올바른 파일 이름으로 `dtmi/` 폴더에 추가할 수 있습니다.

```bash
# from the local repo root folder
dmr-client import --model-file "MyThermostat.json"
```

> [!TIP]
> `--local-repo` 인수를 사용하여 로컬 리포지토리 루트 폴더를 지정할 수 있습니다.

### <a name="validate-models"></a>모델 유효성 검사

`dmr-client validate` 명령을 사용하여 모델의 유효성을 검사할 수 있습니다.

```bash
dmr-client validate --model-file ./my/model/file.json
```

> [!NOTE]
> 유효성 검사에서는 최신 DTDL 파서 버전을 사용하여 모든 인터페이스가 DTDL 언어 사양과 호환되는지 확인합니다.

외부 종속성의 유효성을 검사하려면 로컬 리포지토리에 있어야 합니다. 모델의 유효성을 검사하려면 `--repo` 옵션을 사용하여 종속성을 확인할 `local` 또는 `remote` 폴더를 지정합니다.

```bash
# from the repo root folder
dmr-client validate --model-file ./my/model/file.json --repo .
```

### <a name="strict-validation"></a>Strict 유효성 검사

DMR에는 추가 [요구 사항](https://github.com/Azure/iot-plugandplay-models/blob/main/pr-reqs.md)이 포함되어 있으며 `stict` 플래그를 사용하여 모델의 유효성을 검사합니다.

```bash
dmr-client validate --model-file ./my/model/file.json --repo . --strict true
```

콘솔 출력에서 오류 메시지를 확인합니다.

### <a name="export-models"></a>모델 내보내기

JSON 배열을 사용하여 지정된 리포지토리(로컬 또는 원격)에서 단일 파일로 모델을 내보낼 수 있습니다.

```bash
dmr-client export --dtmi "dtmi:com:example:TemperatureController;1" -o TemperatureController.expanded.json
```

### <a name="create-the-repository-index"></a>리포지토리 만들기 `index`

DMR에는 게시할 때 사용할 수 있는 모든 DTMIs 목록이 포함 된 *인덱스가* 포함 될 수 있습니다. [DMR 도구 Wiki](https://github.com/Azure/iot-plugandplay-models-tools/wiki/Model-Index)의 설명에 따라이 파일을 여러 파일로 분할할 수 있습니다.

사용자 지정 또는 개인 DMR에서 인덱스를 생성 하려면 index 명령을 사용 합니다.

```bash
dmr-client index -r . -o index.json
```

> [!NOTE]
> 공용 DMR는에서 사용할 수 있는 업데이트 된 인덱스를 제공 하도록 구성 됩니다. https://devicemodels.azure.com/index.json

### <a name="create-expanded-files"></a>*확장* 된 파일 만들기

다음 명령을 사용 하 여 확장 된 파일을 생성할 수 있습니다.

```bash
dmr-client expand -r .
```

## <a name="next-steps"></a>다음 단계

권장되는 다음 단계는 [IoT 플러그 앤 플레이 아키텍처](concepts-architecture.md)를 검토하는 것입니다.
