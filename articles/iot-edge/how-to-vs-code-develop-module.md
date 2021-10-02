---
title: Azure IoT Edge용 모듈 개발 및 디버그 | Microsoft Docs
description: Visual Studio Code에서 C#, Python, Node.js, Java 또는 C를 사용하여 Azure IoT Edge용 모듈 개발, 빌드 및 디버그
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: c65cd9e9e6d80222f60a3ee060ba697743713738
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362716"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio Code를 사용하여 Azure IoT Edge용 모듈 개발 및 디버그

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

비즈니스 논리를 Azure IoT Edge용 모듈로 전환할 수 있습니다. 이 문서에서는 Visual Studio Code를 기본 도구로 사용하여 모듈을 개발하고 디버그하는 방법을 보여 줍니다.

Visual Studio Code에서 c #, Node.js 또는 Java로 작성된 모듈을 디버그하는 방법에는 두 가지가 있습니다. 모듈 컨테이너에서 프로세스를 연결하거나 디버그 모드에서 모듈 코드를 시작할 수 있습니다. Python 또는 C로 작성된 모듈의 경우 Linux amd64 컨테이너에서 프로세스에 연결해야 디버그할 수 있습니다.

Visual Studio Code의 디버깅 기능에 익숙하지 않은 경우, [디버깅](https://code.visualstudio.com/Docs/editor/debugging)에 대해 읽어 보시기 바랍니다.

이 문서는 여러 아키텍처의 여러 언어로 모듈을 개발 및 디버깅하기 위한 지침을 제공합니다. 현재 Visual Studio Code는 c #, c, Python, Node.js 및 Java로 작성된 모듈을 지원합니다. 지원되는 디바이스 아키텍처는 X64 및 ARM32입니다. 지원되는 운영 체제, 언어 및 아키텍처에 대한 자세한 내용은 [언어 및 아키텍처 지원](module-development.md#language-and-architecture-support)을 참조하세요.

>[!NOTE]
>Linux ARM64 디바이스를 위한 개발 및 디버그 지원은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 상태입니다. 자세한 내용은 [Visual Studio Code(미리 보기)에서 ARM64 IoT Edge 모듈 개발 및 디버그](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Windows, macOS 또는 Linux를 실행하는 컴퓨터 또는 가상 머신을 개발 머신으로 사용할 수 있습니다. Windows 컴퓨터에서는 Windows 또는 Linux 모듈을 개발할 수 있습니다. Windows 모듈을 개발하려면 1809/빌드 17763 이상 버전을 실행하는 Windows 컴퓨터를 사용해야 합니다. Linux 모듈을 개발하려면 [Docker Desktop에 대한 요구 사항](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)을 충족하는 Windows 컴퓨터를 사용해야 합니다.

[Visual Studio Code](https://code.visualstudio.com/)를 먼저 설치한 후 다음 확장을 추가합니다.

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker 확장](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- 개발 중인 언어에 특정한 Visual Studio 확장:
  - C#(Azure Functions 포함): [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Visual Studio Code용 Java 확장 팩](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++ 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

또한 모듈을 개발하기 위해 몇 가지 추가적인 언어 특정 도구를 설치해야 합니다.

- Azure Functions를 포함한 C#: [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1)

- Python: Python 패키지 설치를 위한 [Python](https://www.python.org/downloads/) 및 [Pip](https://pip.pypa.io/en/stable/installing/#installation)(일반적으로 Python 설치와 함께 포함됨).

- Node.js: [Node.js](https://nodejs.org). 또한 [Yeoman](https://www.npmjs.com/package/yo) 및 [Azure IoT Edge Node.js 모듈 생성기](https://www.npmjs.com/package/generator-azure-iot-edge-module)를 설치할 수 있습니다.

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-support-on-azure) 및 [Maven](https://maven.apache.org/). JDK 설치를 가리키려면 [`JAVA_HOME` 환경 변수를 설정](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)해야 합니다.

모듈 이미지를 빌드하고 배포하려면 모듈 이미지를 빌드하기 위한 Docker와 모듈 이미지를 저장하기 위한 컨테이너 레지스트리가 필요합니다.

- 개발 머신의 [Docker Community Edition](https://docs.docker.com/install/)

- [Azure Container Registry](../container-registry/index.yml) 또는 [Docker 허브](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다.

C에서 모듈을 개발하지 않는 한, IoT Edge 솔루션을 디버그, 실행 및 테스트하는 로컬 개발 환경을 설정하기 위해 Python 기반 [Azure IoT EdgeHub 개발 도구](https://pypi.org/project/iotedgehubdev/)도 필요합니다. 아직 수행 하지 않은 경우 [Python (2.7/3.6/3.7/3.8) 및 Pip](https://www.python.org/) 를 설치한 다음 터미널에서이 명령을 실행 하 여 **iotedgehubdev** 를 설치 합니다.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
>
> 예를 들어 Ubuntu 또는 macOS에 사전 설치된 python 2.7을 비롯한 여러 Python이 있는 경우 올바른 `pip` 또는 `pip3`을(를) 사용하여 **iotedgehubdev** 를 설치하는지 확인합니다.

디바이스에서 모듈을 테스트하려면 하나 이상의 IoT Edge 디바이스가 있는 활성 IoT 허브가 필요합니다. 컴퓨터를 IoT Edge 디바이스로 사용하려면 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)의 빠른 시작에서 단계를 수행합니다. 개발 머신에서 IoT Edge 디먼을 실행하는 경우 다음 단계로 이동하기 전에 EdgeHub 및 EdgeAgent를 중지해야 합니다.

## <a name="create-a-new-solution-template"></a>새 솔루션 템플릿 만들기

다음 단계는 Visual Studio Code 및 Azure IoT Tools를 사용하여 원하는 개발 언어(Azure Functions 포함, C#으로 작성)로 IoT Edge 모듈을 만드는 방법을 보여줍니다. 먼저 솔루션을 만들고 해당 솔루션에 첫 번째 모듈을 생성합니다. 각 솔루션은 여러 모듈을 포함할 수 있습니다.

1. **뷰** > **명령 팔레트** 를 선택합니다.

1. 명령 팔레트에서 **Azure IoT Edge: 새로운 IoT Edge 솔루션** 명령을 입력하고 실행합니다.

   ![New IoT Edge Solution 실행](./media/how-to-develop-csharp-module/new-solution.png)

1. 새 솔루션을 만들려는 폴더로 이동한 후 **폴더 선택** 을 선택합니다.

1. 솔루션에 대한 이름을 입력합니다.

1. 원하는 개발 언어의 모듈 템플릿을 솔루션의 첫 번째 모듈로 선택합니다.

1. 모듈의 이름을 입력합니다. 컨테이너 레지스트리 내에서 고유한 이름을 선택합니다.

1. 모듈의 이미지 리포지토리 이름을 입력합니다. Visual Studio Code는 **localhost:5000/<모듈 이름\>** 으로 모듈 이름을 자동으로 채웁니다. 고유한 레지스트리 정보로 바꿉니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 **localhost** 를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 레지스트리 설정의 로그인 서버를 사용합니다. 로그인 서버는 **_\<registry name\>_ .azurecr.io** 와 같은 모양입니다. 최종 결과가 **\<*registry name*\>.azurecr.io/ _\<your module name\>_** 와 같이 표시되도록 문자열의 **localhost:5000** 부분을 바꾸기만 하면 됩니다.

   ![Docker 이미지 리포지토리 제공](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다.

솔루션 내에 4개의 항목이 있습니다.

- **.vscode** 폴더에는 디버그 구성이 들어 있습니다.

- **modules** 폴더에는 각 모듈의 하위 폴더가 있습니다.  각 모듈의 폴더 내에는 모듈을 빌드하고 배포하는 방법을 제어하는 파일 **module.json** 이 들어 있습니다.  모듈 배포 컨테이너 레지스트리를 localhost에서 원격 레지스트리로 변경하려면 이 파일을 수정해야 합니다. 이 시점에는 모듈이 하나뿐입니다.  하지만 **Azure IoT Edge: Add IoT Edge Module** 명령을 사용하여 명령 팔레트에서 모듈을 더 추가할 수 있습니다.

- **.env** 파일은 환경 변수를 나열합니다. Azure Container Registry가 레지스트리인 경우 거기에 Azure Container Registry 사용자 이름 및 암호가 있습니다.

  프로덕션 시나리오에서는 서비스 주체를 사용하여 .env 파일 대신 컨테이너 레지스트리에 대한 액세스를 제공하는 것이 좋습니다. 자세한 내용은 [컨테이너 레지스트리에 대한 액세스 관리](production-checklist.md#manage-access-to-your-container-registry)를 참조하세요.

  > [!NOTE]
  > 환경 파일은 모듈에 대한 이미지 리포지토리를 제공하는 경우에만 생성됩니다. localhost 기본값을 로컬로 테스트하고 디버그하도록 수락하는 경우 환경 변수를 선언할 필요가 없습니다.

- **deployment.template.json** 파일은 테스트에 사용할 수 있는 데이터를 시뮬레이션하는 샘플 **SimulatedTemperatureSensor** 모듈과 함께 새 모듈을 나열합니다. 배포 매니페스트 작동 방식에 대한 자세한 내용은 [배포 매니페스트를 사용하여 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요.

시뮬레이션된 온도 모듈의 작동 방법을 확인하려면 [SimulatedTemperatureSensor.csproj 소스 코드](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)를 확인합니다.

### <a name="set-iot-edge-runtime-version"></a>IoT Edge 런타임 버전 설정

IoT Edge 확장은 배포 자산을 만들 때 IoT Edge 런타임의 안정적인 최신 버전으로 기본 설정됩니다. 현재 최신 안정적인 버전은 버전 1.2입니다. 1\.1 장기 지원 버전 또는 이전 1.0 버전을 실행하는 디바이스용 모듈을 개발하는 경우 일치하도록 Visual Studio Code에서 IoT Edge 런타임 버전을 업데이트합니다.

1. **뷰** > **명령 팔레트** 를 선택합니다.

1. 명령 팔레트에서 **Azure IoT Edge: 기본 IoT Edge 런타임 버전 설정** 명령을 입력하고 실행합니다.

1. 목록에서 IoT Edge 디바이스가 실행 중인 런타임 버전을 선택합니다.

새 런타임 버전을 선택하면 런타임 모듈 이미지에 대한 변경 사항을 반영하도록 배포 매니페스트가 동적으로 업데이트됩니다.

## <a name="add-additional-modules"></a>모듈 더 추가

솔루션에 모듈을 더 추가하려면 명령 팔레트에서 **Azure IoT Edge: IoT Edge 모듈 추가** 명령을 실행합니다. Visual Studio Code Explorer 보기의 **modules** 폴더 또는 `deployment.template.json` 파일을 마우스 오른쪽 단추로 클릭한 후 **IoT Edge 모듈 추가** 를 선택할 수도 있습니다.

## <a name="develop-your-module"></a>모듈 개발

솔루션과 함께 제공되는 기본 모듈 코드는 다음 위치에 있습니다.

- Azure 함수(C#): **modules > ‘&lt;모듈 이름&gt;’ > &lt;모듈 이름&gt;’.cs** 
- C#: **modules > ‘&lt;모듈 이름&gt;’ > Program.cs**
- Python: **modules > ‘&lt;모듈 이름&gt;’ > main.py**
- Node.js: **modules > ‘&lt;모듈 이름&gt;’ > app.js**
- Java: **modules > ‘&lt;모듈 이름&gt;’ > src > main > java > com > edgemodulemodules > App.java**
- C: **modules > ‘&lt;모듈 이름&gt;’ > main.c**

모듈 및 deployment.template.json 파일은 솔루션을 빌드하고, 컨테이너 레지스트리에 푸시하고, 디바이스에 배포하여 코드를 변경하지 않고 테스트를 시작하도록 설정됩니다. 모듈은 단순히 원본에서 입력을 가져오고(이 경우에 데이터를 시뮬레이션하는 SimulatedTemperatureSensor 모듈) IoT Hub로 파이핑하도록 빌드됩니다.

고유한 코드를 사용하여 템플릿을 사용자 지정할 준비가 된 경우 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하여 보안, 디바이스 관리 및 안정성 등 IoT 솔루션에 대한 주요 요구 사항을 해결하는 모듈을 빌드합니다.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>컨테이너 없이 모듈 디버그(C#, Node.js, Java)

C#, Node.js 또는 Java에서 개발하는 모듈은 기본 모듈 코드에서 **ModuleClient** 개체를 사용해야 메시지를 시작, 실행 및 라우팅할 수 있습니다. 또한 모듈이 메시지를 수신할 때 작업을 수행하는 데 기본 입력 채널 **input1** 을 사용합니다.

### <a name="set-up-iot-edge-simulator"></a>IoT Edge 시뮬레이터 설정

IoT Edge 모듈을 실행하고 디버그하려면 IoT Edge 환경이 필요합니다. 전체 IoT Edge 보안 디먼 및 런타임을 실행하는 대신 개발 머신에서 IoT Edge 시뮬레이터를 사용할 수 있습니다. 디바이스를 시뮬레이션하여 여러 모듈로 솔루션을 디버그하거나 단일 모듈 애플리케이션을 시뮬레이션할 수 있습니다.

옵션 1: IoT Edge 솔루션 시뮬레이션:

1. 왼쪽의 **탐색기** 탭에서 **Azure IoT Hub** 섹션을 확장합니다. IoT Edge 디바이스 ID를 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 시뮬레이터 설치** 를 선택하여 디바이스 연결 문자열을 사용해 시뮬레이터를 시작합니다.
1. 통합 터미널에서 진행 세부 정보를 검토하여 IoT Edge 시뮬레이터가 설치되었는지 확인할 수 있습니다.

옵션 2: 단일 IoT Edge 모듈 시뮬레이션:

1. Visual Studio Code 명령 팔레트에서 **Azure IoT Edge: 단일 모듈용 IoT Edge Hub 시뮬레이터 시작** 명령을 실행합니다.
1. 모듈에서 테스트하려는 입력의 이름을 제공합니다. 기본 샘플 코드를 사용하는 경우 **input1** 값을 사용합니다.
1. 이 명령은 **iotedgehubdev** CLI를 트리거한 후 IoT Edge 시뮬레이터 및 테스트 유틸리티 모듈 컨테이너를 시작합니다. 시뮬레이터가 단일 모듈 모드에서 성공적으로 시작된 경우 통합 터미널에서 다음과 같은 출력을 볼 수 있습니다. 또한 메시지를 전송하는 데 도움이 되는 `curl` 명령도 볼 수 있습니다. 나중에 필요합니다.

   ![단일 모듈 앱에 대한 IoT Edge 시뮬레이터 설치](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Visual Studio Code에서 Docker Explorer 보기를 사용하여 모듈의 실행 상태를 확인할 수 있습니다.

   ![시뮬레이터 모듈 상태](media/how-to-develop-csharp-module/simulator-status.png)

   **edgeHubDev** 컨테이너는 로컬 IoT Edge 시뮬레이터의 핵심입니다. 이 컨테이너는 IoT Edge 보안 디먼 없이 개발 머신에서 실행되며, 네이티브 모듈 앱 또는 모듈 컨테이너에 대한 환경 설정을 제공할 수 있습니다. **input** 컨테이너는 모듈의 대상 입력 채널에 메시지를 연결하도록 도와주는 REST API를 표시합니다.

### <a name="debug-module-in-launch-mode"></a>시작 모드에서 모듈 디버그

시뮬레이터가 성공적으로 시작된 후 모듈 코드를 디버그할 수 있습니다.

1. 개발 언어의 요구 사항에 따라 디버그할 환경을 준비하고, 모듈에서 중단점을 설정하고, 사용할 디버그 구성을 선택합니다.
   - **C#**
     - Visual Studio Code 통합 터미널에서 디렉터리를 ‘***&lt;모듈 이름&gt;***’ 폴더로 변경한 후 다음 명령을 실행하여 .NET Core 애플리케이션을 빌드합니다.

       ```cmd
       dotnet build
       ```

     - `Program.cs` 파일을 열고 중단점을 추가합니다.

     - 왼쪽 메뉴에서 디버그 아이콘을 선택하거나 `Ctrl+Shift+D`를 입력하여 Visual Studio Code 디버그 보기로 이동합니다. 드롭다운에서 디버그 구성 **‘&lt;모듈 이름&gt;’ 로컬 디버그(.NET Core)** 를 선택합니다.

        > [!NOTE]
        > .NET Core `TargetFramework`이 `launch.json`의 프로그램 경로와 일관성이 없는 경우 .csproj 파일의 `TargetFramework`과 일치하도록 `launch.json`의 프로그램 경로를 수동으로 업데이트하면 Visual Studio Code에서 이 프로그램을 시작할 수 있습니다.

   - **Node.JS**
     - Visual Studio Code 통합 터미널에서 디렉터리를 ‘***&lt;모듈 이름&gt;***’ 폴더로 변경한 후 다음 명령을 실행하여 Node 패키지를 설치합니다.

       ```cmd
       npm install
       ```

     - `app.js` 파일을 열고 중단점을 추가합니다.

     - 왼쪽 메뉴에서 디버그 아이콘을 선택하거나 `Ctrl+Shift+D`를 입력하여 Visual Studio Code 디버그 보기로 이동합니다. 드롭다운에서 디버그 구성 **‘&lt;모듈 이름&gt;’ 로컬 디버그(Node.js)** 를 선택합니다.
   - **Java**
     - `App.java` 파일을 열고 중단점을 추가합니다.

     - 왼쪽 메뉴에서 디버그 아이콘을 선택하거나 `Ctrl+Shift+D`를 입력하여 Visual Studio Code 디버그 보기로 이동합니다. 드롭다운에서 디버그 구성 **‘&lt;모듈 이름&gt;’ 로컬 디버그(Java)** 를 선택합니다.

1. **디버깅 시작** 을 클릭하거나 **F5** 키를 눌러 디버그 세션을 시작합니다.

1. Visual Studio Code 통합 터미널에서 다음 명령을 실행하여 모듈에 **Hello World** 메시지를 보냅니다. 이 명령은 이전 단계에서 IoT Edge 시뮬레이터를 설치할 때 표시되는 명령입니다.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows를 사용하는 경우 Visual Studio Code 통합 터미널의 셸은 **Git Bash** 또는 **WSL Bash** 입니다. PowerShell 또는 명령 프롬프트에서는 `curl` 명령을 실행할 수 없습니다.
   > [!TIP]
   > `curl` 대신 [PostMan](https://www.getpostman.com/) 또는 다른 API 도구를 사용하여 메시지를 보낼 수도 있습니다.

1. Visual Studio Code 디버그 보기의 왼쪽 패널에 변수가 표시됩니다.

1. 디버깅 세션을 중지하려면 중지 단추를 선택하거나 **Shift + f5** 를 누른 다음 명령 팔레트에서 **Azure IoT Edge: IoT Edge 시뮬레이터 중지** 를 실행하여 시뮬레이터를 중지하고 정리합니다.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>IoT Edge 시뮬레이터를 사용하여 연결 모드에서 디버그(C#, Node.js, Java, Azure Functions)

기본 솔루션에는 2개의 모듈, 즉 시뮬레이트된 온도 센서 모듈과 파이프 모듈이 포함되어 있습니다. 시뮬레이트된 온도 센서가 파이프 모듈로 메시지를 보내면 해당 메시지는 IoT Hub에 파이프됩니다. 만든 모듈 폴더에는 서로 다른 컨테이너 유형에 사용되는 여러 Docker 파일이 있습니다. **.debug** 확장명으로 끝나는 파일을 사용하여 테스트할 모듈을 빌드합니다.

현재 지원되는 연결 모드의 디버깅은 다음과 같습니다.

- Azure Functions의 모듈을 포함하여 C# 모듈은 Linux amd64 컨테이너에서 디버깅을 지원함
- Node.js 모듈은 Linux amd64 및 arm32v7 컨테이너와 Windows amd64 컨테이너에서 디버깅을 지원함
- Java 모듈은 Linux amd64 및 arm32v7 컨테이너에서 디버깅을 지원함

> [!TIP]
> Visual Studio Code 상태 표시줄의 항목을 클릭하여 IoT Edge 솔루션의 기본 플랫폼 옵션 간에 전환할 수 있습니다.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge 솔루션에 대한 IoT Edge 시뮬레이터 설치

개발 머신에서 IoT Edge 보안 디먼을 설치하는 대신 IoT Edge 시뮬레이터를 시작하여 IoT Edge 솔루션을 실행할 수 있습니다.

1. 왼쪽의 **탐색기** 탭에서 **Azure IoT Hub** 섹션을 확장합니다. IoT Edge 디바이스 ID를 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 시뮬레이터 설치** 를 선택하여 디바이스 연결 문자열을 사용해 시뮬레이터를 시작합니다.

1. 통합 터미널에서 진행 세부 정보를 검토하여 IoT Edge 시뮬레이터가 설치되었는지 확인할 수 있습니다.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>디버깅을 위한 컨테이너 빌드/실행 및 연결 모드에서 디버그

1. 모듈 파일(`Program.cs`, `app.js`, `App.java` 또는 `<your module name>.cs`)을 열고 중단점을 추가합니다.

1. Visual Studio Code Explorer 보기에서 솔루션의 `deployment.debug.template.json` 파일을 마우스 오른쪽 단추로 클릭한 후 **Build and Run IoT Edge solution in Simulator**(시뮬레이터에서 IoT Edge 솔루션 빌드 및 실행)를 선택합니다. 같은 창에서 모든 모듈 컨테이너 로그를 볼 수 있습니다. Docker 보기로 이동하여 컨테이너 상태를 확인할 수도 있습니다.

   ![변수 보기](media/how-to-vs-code-develop-module/view-log.png)

1. Visual Studio Code 디버그 보기로 이동하고 모듈의 디버그 구성 파일을 선택합니다. 디버그 옵션 이름은 **‘&lt;모듈 이름&gt;’ 원격 디버그(Java)** 와 비슷해야 합니다.

1. **디버깅 시작** 을 선택하거나 **F5** 키를 누릅니다. 연결할 프로세스를 선택합니다.

1. Visual Studio Code 디버그 보기의 왼쪽 패널에 변수가 표시됩니다.

1. 디버깅 세션을 중지하려면 먼저 중지 단추를 선택하거나 **Shift + f5** 를 누른 다음 명령 팔레트에서 **Azure IoT Edge: IoT Edge 시뮬레이터 중지** 를 실행합니다.

> [!NOTE]
> 앞의 예제에서는 컨테이너에서 IoT Edge 모듈을 디버그하는 방법을 보여 줍니다. 모듈의 컨테이너 `createOptions` 설정에 노출된 포트를 추가했습니다. 모듈의 디버깅이 끝나면 프로덕션에서 사용할 준비가 완료된 IoT Edge 모듈을 얻기 위해 이러한 노출된 포트를 제거하는 것이 좋습니다.
>
> Azure Functions를 포함하여 C#으로 작성된 모듈의 경우 이 예제는 빌드하는 동안 컨테이너 이미지에 .NET Core 명령줄 디버거(VSDBG)를 포함하는 디버그 버전의 `Dockerfile.amd64.debug`를 기반으로 합니다. C# 모듈을 디버그한 후에 프로덕션에서 사용할 준비가 된 IoT Edge 모듈에 VSDBG를 사용하지 않고 Dockerfile을 직접 사용하는 것이 좋습니다.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>IoT Edge 런타임을 사용하여 모듈 디버그

각 모듈 폴더에는 서로 다른 컨테이너 유형에 사용되는 여러 Docker 파일이 있습니다. **.debug** 확장명으로 끝나는 파일을 사용하여 테스트할 모듈을 빌드합니다.

이 방법을 사용하여 모듈을 디버그하는 경우에는 모듈이 IoT Edge 런타임을 기반으로 실행됩니다. IoT Edge 디바이스와 Visual Studio Code가 동일한 머신에 있는 경우도 있지만, Visual Studio Code는 개발 머신에 있고 IoT Edge 런타임과 모듈은 다른 물리적 머신에서 실행되는 경우가 보다 일반적입니다. Visual Studio Code에서 디버그하려면 다음을 수행해야 합니다.

- IoT Edge 디바이스를 설정하고 **.debug** Dockerfile로 IoT Edge 모듈을 빌드한 다음, IoT Edge 디바이스에 배포합니다.
- 디버거를 연결할 수 있도록 모듈의 IP 및 포트를 노출합니다.
- Visual Studio Code가 원격 머신의 컨테이너에 있는 프로세스에 연결할 수 있도록 `launch.json`을 업데이트합니다. 이 파일은 작업 영역의 `.vscode` 폴더에 있으며 디버깅을 지원하는 새 모듈을 추가할 때마다 업데이트됩니다.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>모듈을 빌드하고 IoT Edge 디바이스에 배포

1. Visual Studio Code에서 적절한 `createOptions` 값이 설정된 모듈 이미지의 디버그 버전을 포함하는 `deployment.debug.template.json` 파일을 엽니다.

1. Python에서 모듈을 개발하는 경우 계속하기 전에 다음 단계를 따르세요.
   - `main.py` 파일을 열고 가져오기 섹션 뒤에 이 코드를 추가합니다.

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - 디버그할 콜백에 다음 단일 코드 줄을 추가합니다.

      ```python
      ptvsd.break_into_debugger()
      ```

     예를 들어 `receive_message_listener` 함수를 디버그하려면 아래에 표시된 대로 해당 코드 줄을 삽입합니다.

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. Visual Studio Code 명령 팔레트에서:
   1. **Azure IoT Edge: IoT Edge 솔루션 빌드 및 푸시** 명령을 실행합니다.

   1. 솔루션의 `deployment.debug.template.json` 파일을 선택합니다.

1. Visual Studio Code Explorer 보기에서 **Azure IoT Hub 디바이스** 섹션에서:
   1. IoT Edge 디바이스 ID를 마우스 오른쪽 단추로 클릭하고 **단일 디바이스용 배포 만들기** 를 선택합니다.

      > [!TIP]
      > 선택한 디바이스가 IoT Edge 디바이스인지 확인하려면 선택하여 모듈 목록을 확장하고 **$edgeHub** 및 **$edgeAgent** 가 있는지 확인합니다. 모든 IoT Edge 디바이스에 이러한 두 모듈이 포함됩니다.

   1. 솔루션의 **config** 폴더로 이동하고, `deployment.debug.amd64.json` 파일을 선택한 다음, **Edge 배포 매니페스트 선택** 을 선택합니다.

통합 터미널에서 배포 ID를 사용하여 생성된 배포가 표시됩니다.

터미널에서 `docker ps` 명령을 실행하여 컨테이너 상태를 확인할 수 있습니다. Visual Studio Code와 IoT Edge 런타임이 동일한 머신에서 실행되는 경우 Visual Studio Code Docker 보기에서 상태를 확인할 수도 있습니다.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>디버거에 대해 모듈의 IP 및 포트 노출

사용자 모듈이 Visual Studio Code와 동일한 머신에서 실행 중인 경우 이 섹션을 건너뛰어도 됩니다. 이런 경우에는 localhost를 사용하여 컨테이너에 연결하고 **.debug** Dockerfile, 모듈의 컨테이너 `createOptions` 설정 및 `launch.json` 파일에 이미 포트 설정이 올바르게 되어 있기 때문입니다. 사용자 모듈과 Visual Studio Code가 별도의 머신에서 실행 중인 경우에는 개발 언어에 대한 단계를 따르세요.

- **Azure Functions를 포함한 C#**

  [개발 머신과 IoT Edge 디바이스에서 SSH 채널을 구성](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes)하고 연결할 `launch.json` 파일을 편집합니다.

- **Node.JS**

  - 디버그할 머신의 모듈이 실행 중이고 디버거에서 연결할 준비가 되었으며, 포트 9229에 외부적으로 액세스할 수 있는지 확인합니다. 디버거 머신에서 `http://<target-machine-IP>:9229/json`을 열어 이를 확인할 수 있습니다. 이 URL은 디버그할 Node.js 모듈에 대한 정보를 표시합니다.
  
  - 개발 머신에서 Visual Studio Code를 열고 **&lt;모듈 이름&gt; 원격 디버그(Node.js)** 프로필(또는 모듈이 Windows 컨테이너로 실행되는 경우 **&lt;모듈 이름&gt; 원격 디버그(Windows 컨테이너의 Node.js)** 프로필)의 주소 값이 디버그 중인 머신의 IP가 되도록 `launch.json`을 편집합니다.

- **Java**

  - `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`을 실행하여 디버그할 머신에 대한 SSH 터널을 빌드합니다.
  
  - 개발 머신에서 Visual Studio Code를 열고 대상 머신에 연결할 수 있도록 `launch.json`의 **&lt;모듈 이름&gt; 원격 디버그(Java)** 프로필을 편집합니다. Visual Studio Code를 사용하여 `launch.json`을 편집하고 Java를 디버그하는 방법을 자세히 알아보려면 [디버거 구성](https://code.visualstudio.com/docs/java/java-debugging#_configuration) 섹션을 참조하세요.

- **Python**

  - 디버그할 머신의 포트 5678이 열려 있고 액세스 가능한지 확인합니다.

  - 앞에서 `main.py`에 삽입한 코드 `ptvsd.enable_attach(('0.0.0.0', 5678))`에서 **0.0.0.0** 을 디버그할 머신의 IP 주소로 변경합니다. IoT Edge 모듈을 다시 빌드, 푸시 및 배포합니다.

  - 개발 머신에서 Visual Studio Code를 열고 **&lt;모듈 이름&gt; 원격 디버그(Python)** 프로필의 `host` 값이 `localhost` 대신 대상 머신의 IP 주소를 사용하도록 `launch.json`을 편집합니다.

### <a name="debug-your-module"></a>모듈 디버그

1. Visual Studio Code 디버그 보기에서 모듈의 디버그 구성 파일을 선택합니다. 디버그 옵션 이름은 **‘&lt;모듈 이름&gt;’ 원격 디버그(Java)** 와 비슷해야 합니다.

1. 개발 언어의 모듈 파일을 열고 중단점을 추가합니다.

   - **Azure 함수(C #)** : 파일에 중단점을 추가합니다`<your module name>.cs`.
   - **C#** : 파일에 중단점을 추가합니다`Program.cs`.
   - **Node.js**: 파일에 중단점을 추가합니다`app.js`.
   - **Java**: 파일에 중단점을 추가합니다`App.java`.
   - **Python**: `ptvsd.break_into_debugger()`줄을 추가한 파일의 `main.py`콜백 메서드에 중단점을 추가합니다.
   - **C**: 파일에 중단점을 추가합니다`main.c`.

1. **디버깅 시작** 을 선택하거나 **F5** 키를 누릅니다. 연결할 프로세스를 선택합니다.

1. Visual Studio Code 디버그 보기의 왼쪽 패널에 변수가 표시됩니다.

> [!NOTE]
> 앞의 예제에서는 컨테이너에서 IoT Edge 모듈을 디버그하는 방법을 보여 줍니다. 모듈의 컨테이너 `createOptions` 설정에 노출된 포트를 추가했습니다. 모듈의 디버깅이 끝나면 프로덕션에서 사용할 준비가 완료된 IoT Edge 모듈을 얻기 위해 이러한 노출된 포트를 제거하는 것이 좋습니다.

## <a name="build-and-debug-a-module-remotely"></a>원격으로 모듈 빌드 및 디버그

SSH 연결을 지원하기 위한 Docker 및 Moby 엔진의 최신 변경 내용과 환경 설정을 Visual Studio Code 명령 팔레트 및 Azure IoT Edge 터미널에 삽입할 수 있는 Azure IoT Tools의 새 설정을 사용하여 이제 원격 디바이스에서 모듈을 빌드하고 디버그할 수 있습니다.

자세한 내용과 단계별 지침은 이 [IoT 개발자 블로그 항목](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

모듈을 빌드한 후에 [Visual Studio Code에서 Azure IoT Edge 모듈을 배포](how-to-deploy-modules-vscode.md)하는 방법을 알아봅니다.

IoT Edge 디바이스의 모듈을 개발하려면 [Azure IoT Hub SDK를 이해하고 사용](../iot-hub/iot-hub-devguide-sdks.md)합니다.
