---
title: Azure Functions Core Tools 참조
description: Azure Functions Core Tools(func.exe)를 지원하는 참조 설명서입니다.
ms.topic: reference
ms.date: 07/13/2021
ms.openlocfilehash: 90d4a9575c2fe6bb2dc4fbd18132e3bc21e4a07c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537222"
---
# <a name="azure-functions-core-tools-reference"></a>Azure Functions Core Tools 참조

이 문서에서는 로컬 컴퓨터에서 Azure Functions 프로젝트를 개발, 관리 및 배포할 수 있는 Azure Functions Core Tools에 대한 참조 설명서를 제공합니다. Core Tools를 사용하는 방법에 대한 자세한 내용은 [Azure Functions Core Tools 작업](functions-run-local.md)을 참조하세요. 

Core Tools 명령은 각각 고유한 작업 세트를 제공하는 다음과 같은 컨텍스트로 구성됩니다.

| 명령 컨텍스트 | Description |
| ----- | ----- |
| [`func`](#func-init) | 로컬 컴퓨터에서 함수를 만들고 실행하는 데 사용되는 명령입니다. |
| [`func azure`](#func-azure-functionapp-fetch-app-settings) | 게시를 포함하여 Azure 리소스 작업에 대한 명령입니다. |
| [`func durable`](#func-durable-delete-task-hub)    | [Durable Functions](./durable/durable-functions-overview.md) 작업에 대한 명령입니다. |
| [`func extensions`](#func-extensions-install) | 확장을 설치하고 관리하기 위한 명령입니다. |
| [`func kubernetes`](#func-kubernetes-deploy) | Kubernetes 및 Azure Functions 작업에 대한 명령입니다. |
| [`func settings`](#func-settings-decrypt)   | 로컬 Functions 호스트에 대한 환경 설정을 관리하기 위한 명령입니다. |
| [`func templates`](#func-templates-list)  | 사용 가능한 함수 템플릿을 나열하기 위한 명령입니다. |

이 문서의 명령을 사용하려면 먼저 [Core Tools를 설치](functions-run-local.md#install-the-azure-functions-core-tools)해야 합니다. 

## <a name="func-init"></a>func init 

새 Functions 프로젝트를 특정 언어로 만듭니다.

```command
func init <PROJECT_FOLDER>
```

`<PROJECT_FOLDER>`를 제공하면 프로젝트가 이 이름의 새 폴더에 만들어집니다. 그렇지 않으면 현재 폴더가 사용됩니다.

`func init`는 다음 옵션을 지원합니다. 이 옵션은 별도로 언급하지 않는 한 버전 3.x/2.x에만 지원됩니다.

| 옵션     | Description                            |
| ------------ | -------------------------------------- |
| **`--csx`** | 버전 1.x 동작인 .NET 함수를 C# 스크립트로 만듭니다. `--worker-runtime dotnet`에만 유효합니다. |
| **`--docker`** | 선택한 `--worker-runtime`을 기반으로 하는 기본 이미지를 사용하여 컨테이너에 대한 Dockerfile을 만듭니다. 사용자 지정 Linux 컨테이너에 게시하려는 경우 이 옵션을 사용합니다. |
| **`--docker-only`** |  Dockerfile을 기존 프로젝트에 추가합니다. 지정되지 않았거나 local.settings.json에 설정되어 있지 않은 경우 작업자-런타임에 대한 메시지를 표시합니다. 사용자 지정 Linux 컨테이너에 기존 프로젝트를 게시하려는 경우 이 옵션을 사용합니다. |
| **`--force`** | 프로젝트에 기존 파일이 있어도 프로젝트를 초기화합니다. 이 설정은 이름이 같은 기존 파일을 덮어씁니다. 프로젝트 폴더의 다른 파일에는 영향이 없습니다. |
| **`--language`** | 언어별 프로젝트를 초기화합니다. 현재는 `--worker-runtime`이 `node`로 설정되었을 때 지원됩니다. 옵션은 `typescript` 및 `javascript`입니다. `--worker-runtime javascript` 또는 `--worker-runtime typescript`를 사용할 수도 있습니다.  |
| **`--managed-dependencies`**  | 관리형 종속성을 설치합니다. 현재는 PowerShell 작업자 런타임에서만 해당 기능을 지원합니다. |
| **`--source-control`** | Git 리포지토리 작성 여부를 제어합니다. 리포지토리는 기본적으로 작성되지 않습니다. `true`이면 리포지토리가 작성됩니다. |
| **`--worker-runtime`** | 프로젝트의 언어 런타임을 설정합니다. 지원되는 값은 `csharp`, `dotnet`, `dotnet-isolated`, `javascript`, `node`(JavaScript), `powershell`, `python` 및 `typescript`입니다. Java의 경우 [Maven](functions-reference-java.md#create-java-functions)을 사용합니다. 프로젝트 파일만 사용하여 언어 중립적 프로젝트를 생성하려면 `custom`을 사용합니다. 설정되지 않으면 초기화 중에 런타임을 선택하라는 메시지가 표시됩니다. |
|

> [!NOTE]
> `--docker` 또는 `--dockerfile` 옵션을 사용하는 경우 Core Tools는 C#, JavaScript, Python 및 PowerShell 함수에 대한 Dockerfile을 자동으로 만듭니다. Java 함수의 경우 Dockerfile을 수동으로 만들어야 합니다. Azure Functions [이미지 목록](https://github.com/Azure/azure-functions-docker)을 사용하여 Azure Functions를 실행하는 컨테이너에 대한 올바른 기본 이미지를 찾습니다.

## <a name="func-logs"></a>func logs

Kubernetes 클러스터에서 실행되는 함수에 대한 로그를 가져옵니다.

```
func logs --platform kubernetes --name <APP_NAME>
``` 

`func logs` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | Description                            |
| ------------------------------------------ | -------------------------------------- |
| **`--platform`** | 함수 앱의 호스팅 플랫폼입니다. 지원되는 옵션은 `kubernetes`입니다. |
| **`--name`** | Azure의 함수 앱 이름입니다. |

자세한 내용은 [KEDA를 사용하는 Kubernetes의 Azure Functions](functions-kubernetes-keda.md)를 참조하세요.

## <a name="func-new"></a>func new

템플릿을 기반으로 하여 새 함수를 현재 프로젝트에 만듭니다.

```
func new
``` 

`func new` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | Description                            |
| ------------------------------------------ | -------------------------------------- |
| **`--authLevel`** | HTTP 트리거에 대한 권한 부여 수준을 설정할 수 있습니다. 지원되는 값은`function`, `anonymous`, `admin`입니다. 로컬에서 실행하는 경우 권한 부여가 적용되지 않습니다. 자세한 내용은 [HTTP 바인딩 문서](functions-bindings-http-webhook-trigger.md#authorization-keys)를 참조하세요. |
| **`--csx`** | (버전 2.x 및 이상 버전)버전 1.x 및 포털에서 사용되는 것과 동일한 C# 스크립트(.csx) 템플릿을 생성합니다. |
| **`--language`**, **`-l`**| C#, F# 또는 JavaScript와 같은 템플릿 프로그래밍 언어 이 옵션은 버전 1.x에서 필요합니다. 버전 2.x 이상에서는 언어가 작업자 런타임에서 정의되므로 이 옵션을 사용하지 않습니다. |
| **`--name`**, **`-n`** | 함수 이름입니다. |
| **`--template`**, **`-t`** | `func templates list` 명령을 사용하여 지원되는 각 언어에 대해 사용 가능한 템플릿의 전체 목록을 확인합니다.   |

자세한 내용은 [함수 만들기](functions-run-local.md#create-func)를 참조하세요.

## <a name="func-run"></a>func run

*버전 1.x 전용* 입니다.

Azure Portal에서 **테스트** 탭을 사용하여 함수를 실행하는 것과 비슷한 함수를 직접 호출할 수 있습니다. 이 작업은 버전 1.x에서만 지원됩니다. 이후 버전의 경우 `func start`를 사용하고 [함수 엔드포인트를 직접 호출](functions-run-local.md#passing-test-data-to-a-function)합니다.

```command
func run
```

`func run` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | Description                            |
| ------------ | -------------------------------------- |
| **`--content`** | 함수에 전달되는 인라인 콘텐츠입니다. |
| **`--debug`** | 함수를 실행하기 전에 호스트 프로세스에 디버거를 연결합니다.|
| **`--file`** | 콘텐츠로 사용할 파일 이름입니다.|
| **`--no-interactive`** | 자동화 시나리오에 유용한 입력 메시지를 표시하지 않습니다.|
| **`--timeout`** | 로컬 Functions 호스트가 준비될 때까지의 대기 시간(초)입니다.|

예를 들어 HTTP 트리거 함수를 호출하고 콘텐츠 본문을 전달하려면 다음 명령을 실행합니다.

```
func run MyHttpTrigger --content '{\"name\": \"Azure\"}'
```

## <a name="func-start"></a>func start

로컬 런타임 호스트를 시작하고 함수 프로젝트를 현재 폴더에 로드합니다. 

특정 명령은 [런타임 버전](functions-versions.md)에 따라 달라집니다.   

# <a name="v2x"></a>[v2.x+](#tab/v2)

```command
func start
```

`func start`은 다음 옵션을 지원합니다.

| 옵션     | Description                            |
| ------------ | -------------------------------------- |
| **`--cert`** | 프라이빗 키가 포함된 .pfx 파일에 대한 경로입니다. `--useHttps`에서만 지원됩니다. |
| **`--cors`** | CORS 원본의 공백 없이 쉼표로 구분된 목록입니다. |
| **`--cors-credentials`** | 쿠키와 인증 헤더를 사용하여 원본 간 인증된 요청을 허용합니다. |
| **`--dotnet-isolated-debug`** | `true`로 설정되면 디버그하는 .NET 격리 프로젝트에서 디버거가 연결될 때까지 .NET 작업자 프로세스를 일시 중지합니다. |
| **`--enable-json-output`** | 가능한 경우 콘솔 로그를 JSON 형식으로 내보냅니다. |
| **`--enableAuth`** | 전체 인증 처리 파이프라인을 사용하도록 설정합니다. |
| **`--functions`** | 공백으로 구분된 로드할 함수의 목록입니다. |
| **`--language-worker`** | 언어 작업자를 구성하는 인수입니다. 예를 들어 [디버그 포트 및 기타 필수 인수](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)를 제공하여 언어 작업자에 대해 디버깅을 사용하도록 설정할 수 있습니다. |
| **`--no-build`** | 실행하기 전에 현재 프로젝트를 빌드하지 않습니다. .NET 클래스 프로젝트 전용입니다. 기본값은 `false`입니다.  |
| **`--password`** | .pfx 파일에 대한 암호가 포함된 암호 또는 파일입니다. `--cert`을 통해서만 사용됩니다. |
| **`--port`** | 수신 대기할 로컬 포트입니다. 기본값: 7071 |
| **`--timeout`** | Functions 호스트를 시작할 제한 시간(초)입니다. 기본값: 20초|
| **`--useHttps`** | `http://localhost:{port}`가 아닌 `https://localhost:{port}`에 바인딩합니다. 기본적으로 이 옵션은 사용자 컴퓨터에 신뢰할 수 있는 인증서를 만듭니다.|

프로젝트가 실행되면 [개별 함수 엔드포인트를 확인](functions-run-local.md#passing-test-data-to-a-function)할 수 있습니다.

# <a name="v1x"></a>[v1.x](#tab/v1)

```command
func host start
```

`func start`은 다음 옵션을 지원합니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--cors`** | CORS 원본의 공백 없이 쉼표로 구분된 목록입니다. |
| **`--port`** | 수신 대기할 로컬 포트입니다. 기본값: 7071 |
| **`--pause-on-error`** | 프로세스를 종료하기 전에 추가 입력을 위해 일시 ​​중지합니다. IDE(통합 개발 환경)에서 Core Tools를 시작할 때만 사용됩니다.|
| **`--script-root`** | 실행하거나 배포할 함수 앱의 루트 경로를 지정하는 데 사용됩니다. 하위 폴더에 프로젝트 파일을 생성하는 컴파일된 프로젝트용으로 사용됩니다. 예를 들어 C# 클래스 라이브러리 프로젝트를 작성할 때는 `MyProject/bin/Debug/netstandard2.0`과 같은 경로를 사용하여 *root* 하위 폴더에 host.json, local.settings.json 및 function.json 파일이 생성됩니다. 이 경우 접두사를 `--script-root MyProject/bin/Debug/netstandard2.0`으로 설정합니다. 이 접두사는 Azure에서 실행할 때의 함수 앱 루트입니다. |
| **`--timeout`** | Functions 호스트를 시작할 제한 시간(초)입니다. 기본값: 20초|
| **`--useHttps`** | `http://localhost:{port}`가 아닌 `https://localhost:{port}`에 바인딩합니다. 기본적으로 이 옵션은 사용자 컴퓨터에 신뢰할 수 있는 인증서를 만듭니다.|

버전 1.x에서는 [`func run` 명령](#func-run)을 사용하여 특정 함수를 실행하고, 테스트 데이터를 전달할 수도 있습니다. 

---

## <a name="func-azure-functionapp-fetch-app-settings"></a>func azure functionapp fetch-app-settings

특정 함수 앱에서 설정을 가져옵니다.

```command
func azure functionapp fetch-app-settings <APP_NAME>
```

예제는 [스토리지 연결 문자열 가져오기](functions-run-local.md#get-your-storage-connection-strings)를 참조하세요.

설정이 프로젝트의 local.settings.json file 파일에 다운로드됩니다. 화면의 값은 보안을 위해 마스킹됩니다. local.settings.json 파일의 설정은 [로컬 암호화를 사용하도록 설정](#func-settings-encrypt)하여 보호할 수 있습니다. 

## <a name="func-azure-functionapp-list-functions"></a>func azure functionapp list-functions

지정된 함수 앱의 함수 목록을 반환합니다.

```command
func azure functionapp list-functions <APP_NAME>
```
## <a name="func-azure-functionapp-logstream"></a>func azure functionapp logstream

로컬 명령 프롬프트를 Azure의 함수 앱에 대한 스트리밍 로그에 연결합니다.

```command
func azure functionapp logstream <APP_NAME>
```

연결의 기본 시간 제한은 2시간입니다. 시간 제한은 시간 제한 값(초)이 포함된 [SCM_LOGSTREAM_TIMEOUT](functions-app-settings.md#scm_logstream_timeout)이라는 앱 설정을 추가하여 변경할 수 있습니다. 사용량 요금제의 Linux 앱에는 아직 지원되지 않습니다. 이러한 앱의 경우 `--browser` 옵션을 사용하여 포털에서 로그를 봅니다.

`deploy` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--browser`** | 기본 브라우저에서 함수 앱에 대한 Azure Application Insights 라이브 스트림을 엽니다. |

자세한 내용은 [스트리밍 로그 사용](functions-run-local.md#enable-streaming-logs)을 참조하세요.

## <a name="func-azure-functionapp-publish"></a>func azure functionapp publish 

Functions 프로젝트를 Azure의 기존 함수 앱 리소스에 배포합니다. 

```command
func azure functionapp publish <FunctionAppName>
```

자세한 내용은 [프로젝트 파일 배포](functions-run-local.md#project-file-deployment)를 참조하세요.

버전에 따라 적용되는 게시 옵션은 다음과 같습니다.

# <a name="v2x"></a>[v2.x+](#tab/v2)

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--additional-packages`** | 네이티브 종속성을 빌드할 때 설치할 패키지 목록입니다. 예: `python3-dev libevent-dev` |
| **`--build`**, **`-b`** | Linux 함수 앱에 배포할 때 빌드 작업을 수행합니다. 수락: `remote` 및 `local`. |
| **`--build-native-deps`** | Python 함수 앱을 게시할 때 `.wheels` 폴더의 생성을 건너뜁니다. |
| **`--csx`** | C# 스크립트(.csx) 프로젝트를 게시합니다. |
| **`--force`** | 특정 시나리오에서 게시 전 확인을 무시합니다. |
| **`--dotnet-cli-params`** | 컴파일된 C#(.csproj) 함수를 게시할 때 Core Tools에서 `dotnet build --output bin/publish`를 호출합니다. 이 명령으로 전달하는 모든 매개 변수는 명령줄에 추가됩니다. |
|**`--list-ignored-files`** | `.funcignore` 파일을 기반으로 하며 게시 중에 무시되는 파일의 목록을 표시합니다. |
| **`--list-included-files`** | `.funcignore` 파일을 기반으로 하며 게시되는 파일의 목록을 표시합니다. |
| **`--no-build`** | 게시하는 동안 프로젝트가 빌드되지 않습니다. Python의 경우 `pip install`이 수행되지 않습니다. |
| **`--nozip`** | 기본 `Run-From-Package` 모드를 끕니다. |
| **`--overwrite-settings -y`** | `--publish-local-settings -i` 사용 시 앱 설정을 덮어쓴다는 메시지를 표시하지 않습니다.|
| **`--publish-local-settings -i`** |  local.settings.json의 설정을 Azure에 게시하고, 설정이 이미 있는 경우 덮어쓸지 묻습니다. Microsoft Azure Storage 에뮬레이터를 사용하는 경우 먼저 앱 설정을 [실제 스토리지 연결](functions-run-local.md#get-your-storage-connection-strings)로 변경합니다. |
| **`--publish-settings-only`**, **`-o`** |  설정만 게시하고 콘텐츠는 건너뜁니다. 기본값은 프롬프트입니다. |
| **`--slot`** | 게시할 특정 슬롯의 선택적 이름입니다. |

# <a name="v1x"></a>[v1.x](#tab/v1)

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--overwrite-settings -y`** | `--publish-local-settings -i` 사용 시 앱 설정을 덮어쓴다는 메시지를 표시하지 않습니다.|
| **`--publish-local-settings -i`** |  local.settings.json의 설정을 Azure에 게시하고, 설정이 이미 있는 경우 덮어쓸지 묻습니다. Microsoft Azure Storage 에뮬레이터를 사용하는 경우 먼저 앱 설정을 [실제 스토리지 연결](functions-run-local.md#get-your-storage-connection-strings)로 변경합니다. |

---

## <a name="func-azure-storage-fetch-connection-string"></a>func azure storage fetch-connection-string    

지정된 Azure Storage 계정에 대한 연결 문자열을 가져옵니다.

```command
func azure storage fetch-connection-string <STORAGE_ACCOUNT_NAME>
```

## <a name="func-deploy"></a>func deploy

KEDA를 사용하지 않고 사용자 지정 Linux 컨테이너의 함수 앱을 Kubernetes 클러스터에 배포합니다.

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <DOCKER_USER>
```

이 명령은 프로젝트를 사용자 지정 컨테이너로 빌드하고, 기본 배율 조정기 또는 KNative를 사용하여 Kubernetes 클러스터에 게시합니다. 동적 크기 조정을 위해 KEDA를 사용하여 클러스터에 게시하려면 [`func kubernetes deploy` 명령](#func-kubernetes-deploy)을 대신 사용합니다. 사용자 지정 컨테이너에는 Dockerfile이 있어야 합니다. Dockerfile을 사용하여 앱을 만들려면 [`func init` 명령](#func-init)과 함께 `--dockerfile` 옵션을 사용합니다.     

`deploy` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--config`** | 선택적 배포 구성 파일을 설정합니다. |
| **`--max`**  | 필요한 경우 배포할 함수 앱 인스턴스의 최대 수를 설정합니다. |
| **`--min`**  | 필요한 경우 배포할 함수 앱 인스턴스의 최소 수를 설정합니다. |
| **`--name`** | 함수 앱 이름입니다(필수). |
| **`--platform`** | 함수 앱에 대한 호스팅 플랫폼입니다(필수). 유효한 옵션은 `kubernetes` 및 `knative`입니다.|
| **`--registry`** | 현재 사용자가 로그인한 Docker 레지스트리의 이름입니다(필수). |

Core Tools는 로컬 Docker CLI를 사용하여 이미지를 빌드하고 게시합니다. 

Docker가 이미 로컬로 설치되어 있는지 확인합니다. `docker login` 명령을 실행하여 계정에 연결합니다.

## <a name="func-durable-delete-task-hub"></a>func durable delete-task-hub

Durrary Functions 작업 허브에서 모든 스토리지 아티팩트를 삭제합니다.

```command
func durable delete-task-hub
```

`delete-task-hub` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 설정의 선택적 이름입니다. |
| **`--task-hub-name`** |             사용할 지속성 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#delete-a-task-hub)를 참조하세요.

## <a name="func-durable-get-history"></a>func durable get-history

지정된 오케스트레이션 인스턴스의 기록을 반환합니다.

```command
func durable get-history --id <INSTANCE_ID>
```

`get-history` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--id`** | 오케스트레이션 인스턴스의 ID를 지정합니다(필수). |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 설정의 선택적 이름입니다. |
| **`--task-hub-name`** |             사용할 지속성 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1)를 참조하세요.

## <a name="func-durable-get-instances"></a>func durable get-instances

모든 오케스트레이션 인스턴스의 상태를 반환합니다. `top` 매개 변수를 사용하여 페이징을 지원합니다.

```command
func durable get-instances
```

`get-instances` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--continuation-token`** | 반환할 요청의 특정 페이지/섹션을 나타내는 선택적 토큰입니다. |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 앱 설정의 선택적 이름입니다. |
| **`--created-after`** | 필요에 따라 이 날짜/시간(UTC) 이후에 만든 인스턴스를 가져옵니다. ISO 8601 형식의 모든 날짜/시간이 허용됩니다. |
| **`--created-before`** | 필요에 따라 특정 날짜/시간(UTC) 이전에 만든 인스턴스를 가져옵니다. ISO 8601 형식의 모든 날짜/시간이 허용됩니다. |
| **`--runtime-status`** | 필요에 따라 `running`, `completed` 및 `failed`를 포함하여 상태가 특정 상태와 일치하는 인스턴스를 가져옵니다. 공백으로 구분된 하나 이상의 상태를 제공할 수 있습니다. |
| **`--top`** | 필요에 따라 지정된 요청에서 반환되는 레코드의 수를 제한합니다. |
| **`--task-hub-name`** | 사용할 Durable Functions 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#azure-functions-core-tools-2)를 참조하세요.

## <a name="func-durable-get-runtime-status"></a>func durable get-runtime-status  

지정된 오케스트레이션 인스턴스의 상태를 반환합니다.

```command
func durable get-runtime-status --id <INSTANCE_ID>
```

`get-runtime-status` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 설정의 선택적 이름입니다. |
| **`--id`** | 오케스트레이션 인스턴스의 ID를 지정합니다(필수). |
| **`--show-input`** | 설정되면 함수의 입력이 응답에 포함됩니다. |
| **`--show-output`** | 설정되면 실행 기록이 응답에 포함됩니다. |
| **`--task-hub-name`** | 사용할 Durable Functions 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1)를 참조하세요.

## <a name="func-durable-purge-history"></a>func durable purge-history

지정된 임계값보다 오래된 오케스트레이션에 대한 오케스트레이션 인스턴스 상태, 기록 및 Blob 스토리지를 제거합니다.

```command
func durable purge-history
```

`purge-history` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 설정의 선택적 이름입니다. |
| **`--created-after`** | 필요에 따라 이 날짜/시간(UTC) 이후에 만든 인스턴스의 기록을 삭제합니다. ISO 8601 형식의 모든 날짜/시간 값이 허용됩니다. |
| **`--created-before`** | 필요에 따라 이 날짜/시간(UTC) 이전에 만든 인스턴스의 기록을 삭제합니다. ISO 8601 형식의 모든 날짜/시간 값이 허용됩니다.|
| **`--runtime-status`** | 필요에 따라 `completd`, `terminated`, `canceled` 및 `failed`를 포함하여 상태가 특정 상태와 일치하는 인스턴스의 기록을 삭제합니다. 공백으로 구분된 하나 이상의 상태를 제공할 수 있습니다. `--runtime-status`를 포함하지 않으면 상태에 관계없이 인스턴스 기록이 삭제됩니다.|
| **`--task-hub-name`** | 사용할 Durable Functions 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#azure-functions-core-tools-7)를 참조하세요.

## <a name="func-durable-raise-event"></a>func durable raise-event         

지정된 오케스트레이션 인스턴스에 대한 이벤트를 발생시킵니다.

```command
func durable raise-event --event-name <EVENT_NAME> --event-data <DATA>
```

`raise-event` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 설정의 선택적 이름입니다. |
| **`--event-data`** | 인라인 또는 JSON 파일에서 이벤트에 전달할 데이터입니다(필수). 파일의 경우 `@path/to/file.json`과 같이 앰퍼샌드(`@`)를 파일 경로 앞에 추가합니다. |
| **`--event-name`** | 발생시킬 이벤트의 이름입니다(필수).
| **`--id`** | 오케스트레이션 인스턴스의 ID를 지정합니다(필수). |
| **`--task-hub-name`** | 사용할 Durable Functions 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#azure-functions-core-tools-5)를 참조하세요.

## <a name="func-durable-rewind"></a>func durable rewind              

지정된 오케스트레이션 인스턴스를 되감습니다.

```command
func durable rewind --id <INSTANCE_ID> --reason <REASON>
```

`rewind` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 설정의 선택적 이름입니다. |
| **`--id`** | 오케스트레이션 인스턴스의 ID를 지정합니다(필수). |
| **`--reason`** | 오케스트레이션을 되감는 이유입니다(필수).|
| **`--task-hub-name`** | 사용할 Durable Functions 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#azure-functions-core-tools-6)를 참조하세요.

## <a name="func-durable-start-new"></a>func durable start-new

지정된 오케스트레이터 함수의 새 인스턴스를 시작합니다.

```command
func durable start-new --id <INSTANCE_ID> --function-name <FUNCTION_NAME> --input <INPUT>
```

`start-new` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 설정의 선택적 이름입니다. |
| **`--function-name`** | 시작할 오케스트레이터 함수의 이름입니다(필수).|
| **`--id`** | 오케스트레이션 인스턴스의 ID를 지정합니다(필수). |
| **`--input`** | 인라인 또는 JSON 파일에서 오케스트레이터 함수에 대한 입력입니다(필수). 파일의 경우 `@path/to/file.json`과 같이 앰퍼샌드(`@`)를 파일 경로 앞에 추가합니다. |
| **`--task-hub-name`** | 사용할 Durable Functions 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#azure-functions-core-tools)를 참조하세요.

## <a name="func-durable-terminate"></a>func durable terminate

지정된 오케스트레이션 인스턴스를 중지합니다.

```command
func durable terminate --id <INSTANCE_ID> --reason <REASON>
```

`terminate` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | 사용할 스토리지 연결 문자열이 포함된 설정의 선택적 이름입니다. |
| **`--id`** | 오케스트레이션 인스턴스의 ID를 지정합니다(필수). |
| **`--reason`** | 오케스트레이션을 중지하는 이유입니다(필수). |
| **`--task-hub-name`** | 사용할 Durable Functions 작업 허브의 선택적 이름입니다. |

자세한 내용은 [Durable Functions 설명서](./durable/durable-functions-instance-management.md#azure-functions-core-tools-4)를 참조하세요.

## <a name="func-extensions-install"></a>func extensions install

Functions 확장을 비 C# 클래스 라이브러리 프로젝트에 설치합니다. 

가능한 경우 확장 번들을 대신 사용해야 합니다. 자세한 내용은 [확장 번들](functions-bindings-register.md#extension-bundles)을 참조하세요.

C# 클래스 라이브러리 및 .NET 격리 프로젝트의 경우 `dotnet add package`와 같은 표준 NuGet 패키지 설치 메서드를 대신 사용합니다.

`install` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | extensions.csproj 파일이 포함된 디렉터리의 경로입니다.|
| **`--csx`** |   C# 스크립팅(.csx) 프로젝트를 지원합니다. |
| **`--force`** |  기존 확장의 버전을 업데이트합니다. |
| **`--output`** |  확장에 대한 출력 경로입니다. |
| **`--package`** |   특정 확장 패키지에 대한 식별자입니다. 지정되지 않으면 `func extensions sync`와 마찬가지로 참조된 모든 확장이 설치됩니다.|
| **`--source`** |  NuGet.org를 사용하지 않는 경우의 NuGet 피드 원본입니다.|
| **`--version`** |  확장 패키지 버전입니다. |

확장 번들이 host.json 파일에 정의되어 있으면 아무 작업도 수행되지 않습니다.

## <a name="func-extensions-sync"></a>func extensions sync

함수 앱에 추가된 모든 확장을 설치합니다.

`sync` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | extensions.csproj 파일이 포함된 디렉터리의 경로입니다.|
| **`--csx`** |   C# 스크립팅(.csx) 프로젝트를 지원합니다. |
| **`--output`** |  확장에 대한 출력 경로입니다. |

누락된 extensions.csproj 파일을 다시 생성합니다. 확장 번들이 host.json 파일에 정의되어 있으면 아무 작업도 수행되지 않습니다.

## <a name="func-kubernetes-deploy"></a>func kubernetes deploy

KEDA를 사용하여 Functions 프로젝트를 사용자 지정 docker 컨테이너로 Kubernetes 클러스터에 배포합니다.

```command
func kubernetes deploy 
```

이 명령은 프로젝트를 사용자 지정 컨테이너로 빌드하고, 동적 크기 조정을 위해 KEDA를 사용하여 Kubernetes 클러스터에 게시합니다. 기본 배율 조정기 또는 KNative를 사용하여 클러스터에 게시하려면 [`func deploy` 명령](#func-deploy)을 대신 사용합니다. 사용자 지정 컨테이너에는 Dockerfile이 있어야 합니다. Dockerfile을 사용하여 앱을 만들려면 [`func init` 명령](#func-init)과 함께 `--dockerfile` 옵션을 사용합니다. 

사용할 수 있는 Kubernetes 배포 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | 필요에 따라 배포 템플릿을 실행하지 않고 표시합니다. |
| **`--config-map-name`** | 배포에 사용할 [함수 앱 설정](functions-how-to-use-azure-function-app-settings.md#settings)이 있는 기존 구성 맵의 선택적 이름입니다. `--use-config-map`가 필요합니다. 기본 동작은 [local.settings.json 파일]의 `Values` ​​개체를 기반으로 하는 설정을 만드는 것입니다.|
| **`--cooldown-period`** | 배포가 0으로 다시 스케일 다운될 때까지 모든 트리거가 더 이상 활성 상태가 아닌 휴지 기간(초)이며, 기본값은 300초입니다. |
| **`--ignore-errors`** | 리소스에서 오류를 반환한 후 배포를 계속합니다. 기본 동작은 오류 발생 시 중지하는 것입니다. |
| **`--image-name`** | Pod 배포에 사용하고 함수를 읽을 이미지의 이름입니다. |
| **`--keda-version`** | 설치할 KEDA의 버전을 설정합니다. 유효한 옵션은 `v1` 및 `v2`(기본값)입니다. |
| **`--keys-secret-name`** | [함수 액세스 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 저장하는 데 사용할 Kubernetes 비밀 컬렉션의 이름입니다. |
| **`--max-replicas`** | HPA(Horizontal Pod Autoscaler)에서 크기 조정하는 최대 복제본 수를 설정합니다. |
| **`--min-replicas`** | HPA에서 크기 조정하지 않는 최소 복제본 수를 설정합니다. |
| **`--mount-funckeys-as-containervolume`** | [함수 액세스 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 컨테이너 볼륨으로 탑재합니다. |
| **`--name`** | Kubernetes의 배포 및 기타 아티팩트에 사용되는 이름입니다. |
| **`--namespace`** | 배포할 Kubernetes 네임스페이스를 설정합니다. 기본값은 기본 네임스페이스입니다. |
| **`--no-docker`** | 이미지 대신 현재 디렉터리에서 함수를 읽습니다. 이미지 파일 시스템을 탑재해야 합니다. |
| **`--registry`** | 설정되면 Docker 빌드가 실행되고 이미지가 해당 이름의 레지스트리에 푸시됩니다. `--registry`는 `--image-name`과 함께 사용할 수 없습니다. Docker의 경우 사용자 이름을 사용합니다. |
| **`--polling-interval`** | HTTP가 아닌 트리거를 확인하는 폴링 간격(초)이며, 기본값은 30초입니다. |
| **`--pull-secret`** | 개인 레지스트리 자격 증명에 액세스하는 데 사용되는 비밀입니다. |
| **`--secret-name`** | 배포에 사용할 [함수 앱 설정](functions-how-to-use-azure-function-app-settings.md#settings)이 포함된 기존 Kubernetes 비밀 컬렉션의 이름입니다. 기본 동작은 [local.settings.json 파일]의 `Values` ​​개체를 기반으로 하는 설정을 만드는 것입니다. |
| **`--show-service-fqdn`** | IP 주소를 사용하는 기본 동작 대신 Kubernetes FQDN을 사용하여 HTTP 트리거의 URL을 표시합니다. |
| **`--service-type`** | Kubernetes Service의 유형을 설정합니다. 지원되는 값은 `ClusterIP`, `NodePort` 및 `LoadBalancer`(기본값)입니다. |
| **`--use-config-map`** | `Secret` 개체(v1) 대신 `ConfigMap` 개체(v1)를 사용하여 [함수 앱 설정](functions-how-to-use-azure-function-app-settings.md#settings)을 구성합니다. 맵 이름은 `--config-map-name`을 사용하여 설정됩니다.|

Core Tools는 로컬 Docker CLI를 사용하여 이미지를 빌드하고 게시합니다. 

Docker가 이미 로컬로 설치되어 있는지 확인합니다. `docker login` 명령을 실행하여 계정에 연결합니다.

자세한 내용은 [Kubernetes에 함수 앱 배포](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes)를 참조하세요.

## <a name="func-kubernetes-install"></a>func kubernetes install

KEDA를 Kubernetes 클러스터에 설치합니다.

```command
func kubernetes install 
```

KEDA를 kubectl 구성 파일에 정의된 클러스터에 설치합니다.

`install` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | 배포 템플릿을 실행하지 않고 표시합니다. |
| **`--keda-version`** | 설치할 KEDA의 버전을 설정합니다. 유효한 옵션은 `v1` 및 `v2`(기본값)입니다. |
| **`--namespace`** | 특정 Kubernetes 네임스페이스에 대한 설치를 지원합니다. 설정되지 않으면 기본 네임스페이스가 사용됩니다. |

자세한 내용은 [Kubernetes에서 KEDA 및 함수 관리](functions-kubernetes-keda.md#managing-keda-and-functions-in-kubernetes)를 참조하세요.

## <a name="func-kubernetes-remove"></a>func kubernetes remove

kubectl 구성 파일에 정의된 Kubernetes 클러스터에서 KEDA를 제거합니다.

```command
func kubernetes remove 
```

kubectl 구성 파일에 정의된 클러스터에서 KEDA를 제거합니다.

`remove` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--namespace`** | 특정 Kubernetes 네임스페이스에서 제거를 지원합니다. 설정되지 않으면 기본 네임스페이스가 사용됩니다. | 

자세한 내용은 [Kubernetes에서 KEDA 제거](functions-kubernetes-keda.md#uninstalling-keda-from-kubernetes)를 참조하세요.

## <a name="func-settings-add"></a>func settings add

새 설정을 [local.settings.json 파일]의 `Values` ​​컬렉션에 추가합니다.

```command
func settings add <SETTING_NAME> <VALUE>
```

`<SETTING_NAME>`을 앱 설정의 이름으로 바꾸고, `<VALUE>`를 설정의 값으로 바꿉니다. 

`add` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | 이름 값 쌍을 `Values` 컬렉션 대신 `ConnectionStrings` 컬렉션에 추가합니다. `ConnectionStrings` 컬렉션은 특정 프레임워크에 필요한 경우에만 사용합니다. 자세한 내용은 [local.settings.json 파일]을 참조하세요. |

## <a name="func-settings-decrypt"></a>func settings decrypt

[local.settings.json 파일]의 `Values` 컬렉션에서 이전에 암호화된 값을 해독합니다.

```command
func settings decrypt
```

`ConnectionStrings` 컬렉션의 연결 문자열 값도 암호 해독됩니다. local.settings.json에서 `IsEncrypted`도 `false`로 설정됩니다. local.settings.json에서 중요한 정보가 누출될 위험을 줄이기 위해 로컬 설정을 암호화합니다. Azure에서 애플리케이션 설정은 항상 암호화된 상태로 저장됩니다. 

## <a name="func-settings-delete"></a>func settings delete

[local.settings.json 파일]의 `Values` 컬렉션에서 기존 설정을 제거합니다.

```command
func settings delete <SETTING_NAME>
```

`<SETTING_NAME>`을 앱 설정의 이름으로 바꾸고, `<VALUE>`를 설정의 값으로 바꿉니다. 

`delete` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | `Values` 컬렉션 대신 `ConnectionStrings` 컬렉션에서 이름 값 쌍을 제거합니다. |

## <a name="func-settings-encrypt"></a>func settings encrypt

[local.settings.json 파일]의 `Values` 컬렉션에 있는 개별 항목의 값을 암호화합니다.

```command
func settings encrypt
```

`ConnectionStrings` 컬렉션의 연결 문자열 값도 암호화됩니다. local.settings.json에서 `IsEncrypted`도 `true`로 설정되어 로컬 런타임에서 설정을 사용하기 전에 암호 해독하도록 지정합니다. local.settings.json에서 중요한 정보가 누출될 위험을 줄이기 위해 로컬 설정을 암호화합니다. Azure에서 애플리케이션 설정은 항상 암호화된 상태로 저장됩니다. 

## <a name="func-settings-list"></a>func settings list

[local.settings.json 파일]의 `Values` 컬렉션에 있는 설정 목록을 출력합니다. 

```command
func settings list
```

`ConnectionStrings` 컬렉션의 연결 문자열도 출력됩니다. 기본적으로 값은 보안을 위해 마스킹됩니다. `--showValue` 옵션을 사용하여 실제 값을 표시할 수 있습니다.

`list` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--showValue`** | 출력에서 마스킹되지 않은 실제 값을 표시합니다. |

## <a name="func-templates-list"></a>func templates list

사용 가능한 함수(트리거) 템플릿을 나열합니다.

`list` 작업에서 지원하는 옵션은 다음과 같습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--language`** | 반환된 템플릿을 필터링할 언어입니다. 기본값은 모든 언어를 반환하는 것입니다. |

[local.settings.json 파일]: functions-develop-local.md#local-settings-file
