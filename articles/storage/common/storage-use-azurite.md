---
title: 로컬 Azure Storage 개발에 Azurite 에뮬레이터 사용
description: Azurite 오픈 소스 에뮬레이터는 Azure Storage 애플리케이션을 테스트할 수 있는 무료 로컬 환경을 제공합니다.
author: normesta
ms.author: normesta
ms.date: 12/03/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 30cdaf899d4ca925ab912ce48bd16724f0e9b242
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133569556"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development"></a>로컬 Azure Storage 개발에 Azurite 에뮬레이터 사용

Azurite 오픈 소스 에뮬레이터는 Azure Blob, Queue Storage 및 Table Storage 애플리케이션을 테스트할 수 있는 무료 로컬 환경을 제공합니다. 애플리케이션이 로컬로 작동하는 방식에 만족하는 경우 클라우드에서 Azure Storage 계정 사용으로 전환합니다. 에뮬레이터는 Windows, Linux 및 macOS에서 플랫폼 간 지원을 제공합니다.

Azurite는 미래의 스토리지 에뮬레이터 플랫폼입니다. Azurite는 [Azure Storage 에뮬레이터](storage-use-emulator.md)를 대체합니다. Azurite는 최신 버전의 Azure Storage API를 지원하도록 계속 업데이트됩니다.

Azurite를 로컬 시스템에 설치하고 실행하는 여러 가지 방법이 있습니다. 이러한 탭 중 하나를 선택합니다.

## <a name="install-azurite"></a>Azurite 설치

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Azurite는 [Visual Studio 2022 미리 보기](https://visualstudio.microsoft.com/vs/preview/)에서 자동으로 사용할 수 있습니다. 이전 버전의 Visual Studio를 실행 하는 경우 노드 패키지 관리자, dockerhub를 사용 하거나 Azurite github 리포지토리를 복제 하 여 Azurite를 설치 해야 합니다. 

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code 내에서 **확장** 창을 선택하고, **확장: 마켓플레이스** 에서 *Azurite* 를 검색합니다.

![Visual Studio Code 마켓플레이스](media/storage-use-azurite/azurite-vs-code-extension.png)

브라우저에서 [Visual Studio Code 확장 시장](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)으로 이동할 수도 있습니다. **설치** 단추를 선택하여 Visual Studio Code를 열고, Azurite 확장 페이지로 직접 이동합니다.

Visual Studio Code 내에서 Azurite를 구성하려면 확장 창을 선택합니다. **Azurite** 에 대한 **관리**(기어) 아이콘을 선택합니다. **확장 설정** 을 선택합니다.

![Azurite 확장 설정 구성](media/storage-use-azurite/azurite-configure-extension-settings.png)

지원되는 설정은 다음과 같습니다.

   - **Azurite: Blob Host** - Blob service 수신 대기 엔드포인트입니다. 기본 설정은 127.0.0.1입니다.
   - **Azurite: Blob Port** - Blob service 수신 대기 포트입니다. 기본 포트는 10000입니다.
   - **Azurite: Cert** - 로컬로 신뢰할 수 있는 PEM 또는 PFX 인증서 파일 경로에 대한 경로입니다. 이는 HTTPS 모드를 사용하도록 설정하기 위한 것입니다.
   - **Azurite: Debug** - 디버그 로그를 Azurite 채널에 출력합니다. 기본 값은 **false** 입니다.
   - **Azurite: Key** - 로컬로 신뢰할 수 있는 PEM 키 파일에 대한 경로입니다. 이는 **Azurite: Cert** 에서 PEM 파일을 가리키는 경우에 필요합니다.
   - **Azurite: Location** - 작업 영역 위치 경로입니다. 기본값은 Visual Studio Code 작업 폴더입니다.
   - **Azurite: Loose** - 지원되지 않는 헤더 및 매개 변수를 무시하는 loose 모드를 사용하도록 설정합니다.
   - **Azurite: Oauth** - OAuth 수준(선택 사항)입니다.
   - **Azurite: Pwd** - PFX 파일의 암호입니다. 이는 **Azurite: Cert** 에서 PFX 파일을 가리키는 경우에 필요합니다.
   - **Azurite: Queue Host** - 큐 서비스 수신 엔드포인트입니다. 기본 설정은 127.0.0.1입니다.
   - **Azurite: Queue Port** - 큐 서비스 수신 포트입니다. 기본 포트는 10001입니다.
   - **Azurite: Silent** - 자동 모드는 액세스 로그를 사용하지 않도록 설정합니다. 기본 값은 **false** 입니다.
   - **Azurite: Skip Api Version Check** - 요청 API 버전 확인을 건너뜁니다. 기본 값은 **false** 입니다.
   - **Azurite: Table Host** - Table service 수신 대기 엔드포인트로, 기본적으로 설정은 127.0.0.1입니다.
   - **Azurite: Table Port** - Table service 수신 대기 포트로, 기본적으로 10002입니다.

### <a name="npm"></a>[npm](#tab/npm)

이 설치 방법을 사용하려면 [Node.js 버전 8.0 이상](https://nodejs.org)이 설치되어 있어야 합니다. Npm(노드 패키지 관리자)은 모든 Node.js 설치에 포함되는 패키지 관리 도구입니다. Node.js가 설치되면 다음 `npm` 명령을 실행하여 Azurite를 설치합니다.

```console
npm install -g azurite
```

### <a name="docker-hub"></a>[Docker Hub](#tab/docker-hub)

다음 명령을 사용하여 [DockerHub](https://hub.docker.com/)를 통해 [최신 Azurite 이미지](https://hub.docker.com/_/microsoft-azure-storage-azurite)를 끌어옵니다.

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**Azurite Docker 이미지 실행**:

다음 명령은 Azurite Docker 이미지를 실행합니다. `-p 10000:10000` 매개 변수는 요청을 호스트 컴퓨터의 10000 포트에서 Docker 인스턴스로 리디렉션합니다.

```console
docker run -p 10000:10000 -p 10001:10001 -p 10002:10002 \
    mcr.microsoft.com/azure-storage/azurite
```

**작업 영역 위치 지정**:

다음 예제에서 `-v c:/azurite:/data` 매개 변수는 Azurite 지속형 데이터 위치로 *c:/azurite* 를 지정합니다. Docker 명령을 실행하기 전에 *c:/azurite* 디렉터리를 만들어야 합니다.

```console
docker run -p 10000:10000 -p 10001:10001 -p 10002:10002 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**Blob service만 실행**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

시작할 때 Azurite를 구성하는 방법에 대한 자세한 내용은 [명령줄 옵션](#command-line-options)을 참조하세요.

### <a name="github"></a>[GitHub](#tab/github)

이 설치 방법을 사용하려면 [Git](https://git-scm.com/)이 설치되어 있어야 합니다. 다음 콘솔 명령을 사용하여 Azurite 프로젝트에 대한 [GitHub 리포지토리](https://github.com/azure/azurite)를 복제합니다.

```console
git clone https://github.com/Azure/Azurite.git
```

소스 코드가 복제되면 복제된 리포지토리의 루트에서 다음 명령을 실행하여 Azurite를 빌드하고 설치합니다.

```console
npm install
npm run build
npm install -g
```

---

## <a name="run-azurite"></a>Azurite 실행

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

몇 가지 구성의 경우 Azure Functions 또는 ASP.NET 프로젝트가 자동으로 Azurite을 시작 합니다. 다른 모든 프로젝트 형식의 경우 명령줄에서 Azurite를 시작 해야 합니다. 

#### <a name="running-azurite-from-the-command-line"></a>명령줄에서 Azurite 실행

Visual Studio 설치의 extensions 폴더에서 Azurite 실행 파일을 찾을 수 있습니다. 특정 위치는 설치한 Visual Studio 버전에 따라 달라질 수 있습니다. 예를 들어 Windows 컴퓨터 또는 VM (가상 컴퓨터)에 Visual Studio 2022 professional 버전을 설치한 경우이 위치에서 Azurite 실행 파일을 찾을 수 있습니다 `C:\Program Files\Microsoft Visual Studio\2022\Professional\Common7\IDE\Extensions\Microsoft\Azure Storage Emulator` .  

실행 파일을 실행 한 후 Azurite는 연결을 수신 대기 합니다. 

> [!div class="mx-imgBorder"]
> ![Azurite 명령줄 출력](media/storage-use-azurite/azurite-command-line-output-vs.png)

#### <a name="running-azurite-from-an-azure-functions-project"></a>Azure Functions 프로젝트에서 Azurite 실행

Visual Studio 2022에서 **Azure Functions** 프로젝트를 만듭니다. 프로젝트를 만들 때 **Storage Emulator** 를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Functions 프로젝트의 Storage 에뮬레이터 옵션](media/storage-use-azurite/visual-studio-azure-function-project-settings.png)

프로젝트를 만든 후에는 Azurite가 자동으로 시작 됩니다. 

> [!div class="mx-imgBorder"]
> ![Azure Functions 프로젝트의 Azurite 명령줄 출력](media/storage-use-azurite/output-window-azure-functions-project.png)

#### <a name="running-azurite-from-an-aspnet-project"></a>ASP.NET 프로젝트에서 Azurite 실행

Visual Studio 2022에서 **ASP.NET Core 웹 앱** 프로젝트를 만듭니다. 그런 다음 **연결된 서비스** 대화 상자를 열고 **서비스 종속성 추가** 를 선택한 다음 **Storage Azurite 에뮬레이터** 를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![ASP.NET Core 웹 앱 프로젝트의 연결 된 서비스 대화 상자](media/storage-use-azurite/connected-service-storage-emulator.png)

**Storage Azurite 에뮬레이터 구성** 대화 상자에서 **연결 문자열 이름** 필드를로 설정 하 `StorageConnectionString` 고 **마침** 을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![Storage Azurite 에뮬레이터 구성 대화 상자](media/storage-use-azurite/connection-string-for-azurite-emulator-configuration.png)

구성이 완료 되 면 **닫기** 를 선택 합니다. Azurite 에뮬레이터가 자동으로 시작 됩니다.

> [!div class="mx-imgBorder"]
> ![ASP.NET 프로젝트의 Azurite 명령줄 출력](media/storage-use-azurite/output-window-asp-net-project.png) 

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

> [!NOTE]
> Visual Studio Code 확장만 설치한 경우 명령줄에서 Azurite를 실행할 수 없습니다. 대신 Visual Studio Code 명령 팔레트를 사용합니다.

이 확장에서 지원하는 Visual Studio Code 명령은 다음과 같습니다. 명령 팔레트를 열려면 Visual Studio Code에서 F1 키를 누릅니다.

   - **Azurite: Clean** - 모든 Azurite 서비스 지속성 데이터 다시 설정
   - **Azurite: Clean Blob Service** - Blob service 정리
   - **Azurite: Clean Queue Service** - 큐 서비스 정리
   - **Azurite: Clean Queue Service** - Table service 정리
   - **Azurite: Close** - 모든 Azurite 서비스 닫기
   - **Azurite: Close Blob Service** - Blob service 닫기
   - **Azurite: Close Queue Service** - 큐 서비스 닫기
   - **Azurite: Close Queue Service** - Table service 닫기
   - **Azurite: Start** - 모든 Azurite 서비스 시작
   - **Azurite: Start Blob Service** - Blob service 시작
   - **Azurite: Start Queue Service** - 큐 서비스 시작
   - **Azurite: Start Queue Service** - Table service 시작

### <a name="npm"></a>[npm](#tab/npm)

다음 명령을 실행 하 여 Azurite를 시작 합니다.

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

이 명령은 모든 데이터를 특정 *c:\azurite* 디렉터리에 저장하도록 Azurite에 지시합니다. `--location` 옵션이 생략되면 현재 작업 디렉터리를 사용합니다.

### <a name="docker-hub"></a>[Docker Hub](#tab/docker-hub)

**Blob service만 실행**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

시작할 때 Azurite를 구성하는 방법에 대한 자세한 내용은 [명령줄 옵션](#command-line-options)을 참조하세요.

### <a name="github"></a>[GitHub](#tab/github)

명령줄을 사용하여 즉시 시작하려면 *c:\azurite* 라는 디렉터리를 만들고, 다음 명령을 실행하여 Azurite를 시작합니다.

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

이 명령은 모든 데이터를 특정 *c:\azurite* 디렉터리에 저장하도록 Azurite에 지시합니다. `--location` 옵션이 생략되면 현재 작업 디렉터리를 사용합니다.

---

## <a name="command-line-options"></a>명령줄 옵션

이 섹션에서는 Azurite를 시작할 때 사용할 수 있는 명령줄 스위치에 대해 자세히 설명합니다.

### <a name="help"></a>도움말

**선택 사항** - `-h` 또는 `--help` 스위치를 사용하여 명령줄 도움말을 가져옵니다.

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Blob 수신 대기 호스트

**선택 사항** - Azurite는 기본적으로 로컬 서버로 127.0.0.1을 수신 대기합니다. `--blobHost` 스위치를 사용하여 주소를 요구 사항에 맞게 설정합니다.

로컬 컴퓨터에서만 요청을 수락합니다.

```console
azurite --blobHost 127.0.0.1
```

원격 요청을 허용합니다.

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> 원격 요청을 허용하면 시스템이 외부 공격에 취약해질 수 있습니다.

### <a name="blob-listening-port-configuration"></a>Blob 수신 대기 포트 구성

**선택 사항** - Azurite는 기본적으로 10000 포트에서 Blob service를 수신 대기합니다. `--blobPort` 스위치를 사용하여 필요한 수신 대기 포트를 지정합니다.

> [!NOTE]
> 사용자 지정된 포트가 사용되면 Azure Storage 도구 또는 SDK에서 연결 문자열 또는 해당 구성을 업데이트해야 합니다.

Blob service 수신 대기 포트를 사용자 지정합니다.

```console
azurite --blobPort 8888
```

시스템에서 사용 가능한 포트를 자동으로 선택하도록 합니다.

```console
azurite --blobPort 0
```

Azurite를 시작하는 동안 사용 중인 포트가 표시됩니다.

### <a name="queue-listening-host"></a>큐 수신 대기 호스트

**선택 사항** - Azurite는 기본적으로 로컬 서버로 127.0.0.1을 수신 대기합니다. `--queueHost` 스위치를 사용하여 주소를 요구 사항에 맞게 설정합니다.

로컬 컴퓨터에서만 요청을 수락합니다.

```console
azurite --queueHost 127.0.0.1
```

원격 요청을 허용합니다.

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> 원격 요청을 허용하면 시스템이 외부 공격에 취약해질 수 있습니다.

### <a name="queue-listening-port-configuration"></a>큐 수신 대기 포트 구성

**선택 사항** - Azurite는 기본적으로 10001 포트에서 큐 서비스를 수신 대기합니다. `--queuePort` 스위치를 사용하여 필요한 수신 대기 포트를 지정합니다.

> [!NOTE]
> 사용자 지정된 포트가 사용되면 Azure Storage 도구 또는 SDK에서 연결 문자열 또는 해당 구성을 업데이트해야 합니다.

큐 서비스 수신 대기 포트를 사용자 지정합니다.

```console
azurite --queuePort 8888
```

시스템에서 사용 가능한 포트를 자동으로 선택하도록 합니다.

```console
azurite --queuePort 0
```

Azurite를 시작하는 동안 사용 중인 포트가 표시됩니다.

### <a name="table-listening-host"></a>테이블 수신 대기 호스트

**선택 사항** - Azurite는 기본적으로 로컬 서버로 127.0.0.1을 수신 대기합니다. `--tableHost` 스위치를 사용하여 주소를 요구 사항에 맞게 설정합니다.

로컬 컴퓨터에서만 요청을 수락합니다.

```console
azurite --tableHost 127.0.0.1
```

원격 요청을 허용합니다.

```console
azurite --tableHost 0.0.0.0
```

> [!CAUTION]
> 원격 요청을 허용하면 시스템이 외부 공격에 취약해질 수 있습니다.

### <a name="table-listening-port-configuration"></a>테이블 수신 대기 포트 구성

**선택 사항** - Azurite는 기본적으로 10002 포트에서 Table service를 수신 대기합니다. `--tablePort` 스위치를 사용하여 필요한 수신 대기 포트를 지정합니다.

> [!NOTE]
> 사용자 지정된 포트가 사용되면 Azure Storage 도구 또는 SDK에서 연결 문자열 또는 해당 구성을 업데이트해야 합니다.

Table service 수신 대기 포트를 사용자 지정합니다.

```console
azurite --tablePort 11111
```

시스템에서 사용 가능한 포트를 자동으로 선택하도록 합니다.

```console
azurite --tablePort 0
```

Azurite를 시작하는 동안 사용 중인 포트가 표시됩니다.

### <a name="workspace-path"></a>작업 영역 경로

**선택 사항** - Azurite는 실행하는 동안 데이터를 로컬 디스크에 저장합니다. `-l` 또는 `--location` 스위치를 사용하여 경로를 작업 영역 위치로 지정합니다. 기본적으로 현재 프로세스 작업 디렉터리가 사용됩니다. 'l' 소문자에 유의하세요.

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>액세스 로그

**선택 사항** - 액세스 로그는 기본적으로 콘솔 창에 표시됩니다. `-s` 또는 `--silent` 스위치를 사용하여 액세스 로그 표시를 사용하지 않도록 설정합니다.

```console
azurite -s
azurite --silent
```


### <a name="debug-log"></a>디버그 로그

**선택 사항** - 디버그 로그에는 모든 요청 및 예외 스택 추적에 대한 자세한 정보가 포함됩니다. `-d` 또는 `--debug` 스위치에 유효한 로컬 파일 경로를 제공하여 디버그 로그를 사용하도록 설정합니다.

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>loose 모드

**선택 사항** - Azurite는 기본적으로 지원되지 않는 요청 헤더 및 매개 변수를 차단하기 위해 strict 모드를 적용합니다. `-L` 또는 `--loose` 스위치를 사용하여 strict 모드를 사용하지 않도록 설정합니다. 'L' 대문자에 유의하세요.

```console
azurite -L
azurite --loose
```


### <a name="version"></a>버전

**선택 사항** - `-v` 또는 `--version` 스위치를 사용하여 설치된 Azuite 버전 번호를 표시합니다.

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>인증서 구성(HTTPS)

**선택 사항** - Azurite는 기본적으로 HTTP 프로토콜을 사용합니다. .pem(Privacy Enhanced Mail) 또는 [.pfx(Personal Information Exchange)](/windows-hardware/drivers/install/personal-information-exchange---pfx--files) 인증서 파일의 경로를 `--cert` 스위치에 제공하여 HTTPS 모드를 사용하도록 설정합니다.

PEM 파일에 대해 `--cert`가 제공되는 경우 해당하는 `--key` 스위치를 제공해야 합니다.

```console
azurite --cert path/server.pem --key path/key.pem
```

PFX 파일에 대해 `--cert`가 제공되는 경우 해당하는 `--pwd` 스위치를 제공해야 합니다.

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

PEM 및 PFX 파일을 만드는 방법에 대한 자세한 내용은 [HTTPS 설정](https://github.com/Azure/Azurite/blob/master/README.md#https-setup)을 참조하세요.

### <a name="oauth-configuration"></a>OAuth 구성

**선택 사항** - `--oauth` 스위치를 사용하여 Azurite에 대한 OAuth 인증을 사용하도록 설정합니다.

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth에는 HTTPS 엔드포인트가 필요합니다. `--oauth` 스위치와 함께 `--cert` 스위치를 제공하여 HTTPS가 사용하도록 설정되었는지 확인합니다.

Azurite는 `basic` 매개 변수를 `--oauth` 스위치에 지정하여 기본 인증을 지원합니다. Azurite는 들어오는 전달자 토큰의 유효성을 검사하고 확인, 발급자, 대상 그룹 및 만료를 확인하는 것과 같은 기본 인증을 수행합니다. Azurite는 토큰 서명 또는 권한을 확인하지 않습니다.

### <a name="skip-api-version-check"></a>API 버전 확인 건너뛰기

**선택 사항** - 시작할 때 Azurite는 요청된 API 버전이 올바른지 확인합니다. 다음 명령은 API 버전 확인을 건너뜁니다.

```console
azurite --skipApiVersionCheck
```

## <a name="authorization-for-tools-and-sdks"></a>도구 및 SDK에 대한 권한 부여

인증 전략을 사용하여 Azure Storage SDK 또는 도구(예 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/))에서 Azurite에 연결합니다. 인증이 필요합니다. Azuite는 OAuth, 공유 키 및 SAS(공유 액세스 서명)를 사용하는 권한 부여를 지원합니다. Azurite는 퍼블릭 컨테이너에 대한 익명 액세스도 지원합니다.

Azure SDK를 사용하는 경우 `--oauth basic and --cert --key/--pwd` 옵션을 사용하여 Azurite를 시작합니다.

### <a name="well-known-storage-account-and-key"></a>잘 알려진 스토리지 계정 및 키

Azurite는 레거시 Azure Storage 에뮬레이터에서 사용하는 것과 동일한 잘 알려진 계정과 키를 허용합니다.

- 계정 이름: `devstoreaccount1`
- 계정 키: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>사용자 지정 스토리지 계정 및 키

Azurite는 `AZURITE_ACCOUNTS` 환경 변수를 `account1:key1[:key2];account2:key1[:key2];...` 형식으로 설정하여 사용자 지정 스토리지 계정 이름 및 키를 지원합니다.

예를 들어 하나의 키가 있는 사용자 지정 스토리지 계정을 사용합니다.

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

또는 각각 두 개의 키가 있는 여러 스토리지 계정을 사용합니다.

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

Azurite는 기본적으로 1분마다 환경 변수에서 사용자 지정 계정 이름과 키를 새로 고칩니다. 이 기능을 사용하면 Azurite를 다시 시작하지 않고도 계정 키를 동적으로 회전하거나 새 스토리지 계정을 추가할 수 있습니다.

> [!NOTE]
> 사용자 지정 스토리지 계정을 설정하는 경우 기본 `devstoreaccount1` 스토리지 계정이 사용하지 않도록 설정됩니다.

### <a name="connection-strings"></a>연결 문자열

애플리케이션에서 Azurite에 연결하는 가장 쉬운 방법은 *UseDevelopmentStorage=true* 바로 가기를 참조하는 애플리케이션의 구성 파일에 연결 문자열을 구성하는 것입니다. *app.config* 파일에 있는 연결 문자열의 예제는 다음과 같습니다.

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP 연결 문자열

다음 연결 문자열을 [Azure SDK](https://aka.ms/azsdk) 또는 도구(예: Azure CLI 2.0 또는 Storage Explorer)에 전달할 수 있습니다.

전체 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

Blob service에만 연결하려면 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

큐 서비스에만 연결하려면 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS 연결 문자열

전체 HTTPS 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

Blob service만 사용하려면 HTTPS 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

큐 서비스만 사용하려면 HTTPS 연결 문자열은 다음과 같습니다.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

`dotnet dev-certs`를 사용하여 자체 서명된 인증서를 생성한 경우 다음 연결 문자열을 사용합니다.

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

[사용자 지정 스토리지 계정 및 키](#custom-storage-accounts-and-keys)를 사용하는 경우 연결 문자열을 업데이트합니다.

자세한 내용은 [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)을 참조하세요.

### <a name="azure-sdks"></a>Azure SDK

[Azure SDK](https://aka.ms/azsdk)와 함께 Azurite를 사용하려면 OAuth 및 HTTPS 옵션을 사용합니다.

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob Storage

그런 다음, BlobContainerClient, BlobServiceClient 또는 BlobClient를 인스턴스화할 수 있습니다.

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

QueueClient 또는 QueueServiceClient를 인스턴스화할 수도 있습니다.

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

Storage Explorer를 사용하여 Azurite에 저장된 데이터를 볼 수 있습니다.

#### <a name="connect-to-azurite-using-http"></a>HTTP를 사용하여 Azurite에 연결

Storage Explorer에서 다음 단계에 따라 Azurite에 연결합니다.

 1. **계정 관리** 아이콘을 선택합니다.
 1. **계정 추가** 를 선택합니다.
 1. **로컬 에뮬레이터에 연결** 을 선택합니다.
 1. **다음** 을 선택합니다.
 1. **표시 이름** 필드를 원하는 이름으로 편집합니다.
 1. **다음** 을 다시 선택합니다.
 1. **연결** 을 선택합니다.

#### <a name="connect-to-azurite-using-https"></a>HTTPS를 사용하여 Azurite에 연결

Storage Explorer는 기본적으로 자체 서명된 인증서를 사용하는 HTTPS 엔드포인트를 열지 않습니다. HTTPS를 사용하여 Azurite를 실행하는 경우 자체 서명된 인증서를 사용할 가능성이 높습니다. Storage Explorer에서 **편집** -> **SSL 인증서** -> **인증서 가져오기** 대화 상자를 통해 SSL 인증서를 가져옵니다.

##### <a name="import-certificate-to-storage-explorer"></a>Storage Explorer로 인증서 가져오기

1. 로컬 컴퓨터에서 인증서를 찾습니다.
1. Storage Explorer에서 **편집** -> **SSL 인증서** -> **인증서 가져오기** 로 차례로 이동하여 인증서를 가져옵니다.

인증서를 가져오지 않으면 다음과 같은 오류가 발생합니다.

`unable to verify the first certificate` 또는 `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>HTTPS 연결 문자열을 통해 Azuite 추가

Azurite HTTPS를 Storage Explorer에 추가하려면 다음 단계를 수행합니다.

1. **탐색기 설정/해제** 를 선택합니다.
1. **로컬 및 첨부** 선택합니다.
1. 마우스 오른쪽 단추로 **스토리지 계정** 을 클릭하고, **Azure Storage에 연결** 을 선택합니다.
1. **연결 문자열 사용** 을 선택합니다.
1. **다음** 을 선택합니다.
1. **표시 이름** 필드에서 값을 입력합니다.
1. 이 문서의 이전 섹션에서 가져온 [HTTPS 연결 문자열](#https-connection-strings)을 입력합니다.
1. **다음** 을 선택합니다.
1. **연결** 을 선택합니다.

## <a name="workspace-structure"></a>작업 영역 구조

Azurite를 초기화하면 작업 영역 위치에서 다음 파일 및 폴더를 만들 수 있습니다.

- `__blobstorage__` - Azurite Blob service 지속형 이진 데이터가 포함된 디렉터리
- `__queuestorage__` - Azurite 큐 서비스 지속형 이진 데이터가 포함된 디렉터리
- `__azurite_db_blob__.json` - Azurite Blob service 메타데이터 파일
- `__azurite_db_blob_extent__.json` - Azurite Blob service 익스텐트 메타데이터 파일
- `__azurite_db_queue__.json` - Azurite 큐 서비스 메타데이터 파일
- `__azurite_db_queue_extent__.json` - Azurite 큐 서비스 익스텐트 메타데이터 파일

Azurite를 정리하려면 위의 파일과 폴더를 삭제하고 에뮬레이터를 다시 시작합니다.

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite와 Azure Storage의 차이점

Azurite의 로컬 인스턴스와 클라우드의 Azure Storage 계정 간에는 기능적 차이점이 있습니다.

### <a name="endpoint-and-connection-url"></a>엔드포인트 및 연결 URL

Azurite의 서비스 엔드포인트는 Azure Storage 계정의 엔드포인트와 다릅니다. 로컬 컴퓨터에서 도메인 이름 확인을 수행하지 않으므로 Azuite 엔드포인트가 로컬 주소여야 합니다.

Azure Storage 계정의 리소스 주소를 지정하는 경우 계정 이름은 URI 호스트 이름의 일부입니다. 주소가 지정되는 리소스는 URI 경로의 일부입니다.

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

다음 URI는 Azure Storage 계정의 Blob에 대한 유효한 주소입니다.

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

로컬 컴퓨터에서 도메인 이름 확인을 수행하지 않으므로 계정 이름은 호스트 이름 대신 URI 경로의 일부입니다. 다음 URI 형식을 Azurite의 리소스에 사용합니다.

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Azurite에서 Blob에 액세스하는 데 다음 주소를 사용할 수 있습니다.

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>스케일링 및 성능

Azurite는 많은 수의 연결된 클라이언트를 지원하지 않습니다. 성능을 보장하지 않습니다. Azurite는 개발 및 테스트용으로 사용됩니다.

### <a name="error-handling"></a>오류 처리

Azurite는 Azure Storage 오류 처리 논리와 일치하지만 차이점이 있습니다. 예를 들어 오류 상태 코드가 일치하지만 오류 메시지가 다를 수 있습니다.

### <a name="ra-grs"></a>RA-GRS

Azurite는 RA-GRS(읽기 액세스 지역 중복 복제)를 지원합니다. 스토리지 리소스의 경우 `-secondary`를 계정 이름에 추가하여 보조 위치에 액세스합니다. 예를 들어 Azurite에서 읽기 전용 보조 계정을 사용하여 Blob에 액세스하는 데 다음 주소를 사용할 수 있습니다.

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

### <a name="table-support"></a>테이블 지원

Azurite의 테이블에 대한 지원은 현재 미리 보기로 제공됩니다. 자세한 내용은 [Azurite V3 Table](https://github.com/Azure/Azurite/wiki/Azurite-V3-Table) 프로젝트를 참조하세요.

지속성 함수를 지원하려면 테이블이 필요합니다.

> [!IMPORTANT]
>
> Table Storage에 대한 Azurite 지원은 현재 **미리 보기** 로 제공됩니다. 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="azurite-is-open-source"></a>오픈 소스인 Azurite

Azurite에 대한 기여와 제안을 환영합니다. 예정된 기능 및 버그 수정을 추적하기 위해 마일스톤 및 작업 항목을 확인하려면 Azurite [GitHub 프로젝트](https://github.com/Azure/Azurite/projects) 페이지 또는 [GitHub 문제](https://github.com/Azure/Azurite/issues)로 이동합니다. 자세한 작업 항목은 GitHub에서도 추적됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage 에뮬레이터를 사용하여 개발 및 테스트](storage-use-emulator.md)는 Azurite로 대체되는 레거시 Azure Storage 에뮬레이터에 대해 설명합니다.
- [Azure Storage 연결 문자열 구성](storage-configure-connection-string.md)은 유효한 Azure Storage 연결 문자열을 어셈블하는 방법에 대해 설명합니다.
