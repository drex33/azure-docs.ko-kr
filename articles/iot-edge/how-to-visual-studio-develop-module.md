---
title: Visual Studio에서 모듈 개발 및 디버그 - Azure IoT Edge
description: Azure IoT Tools와 함께 Visual Studio를 사용하여 C 또는 C# IoT Edge 모듈을 개발하고 배포 매니페스트에 구성된 대로 IoT Hub에서 IoT 디바이스로 푸시합니다.
services: iot-edge
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 5f546acb48a84ddddeb822601d9284818d2211fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423074"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio 2019를 사용하여 Azure IoT Edge용 모듈 개발 및 디버그

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

이 문서에서는 Visual Studio 2019를 사용하여 Azure IoT Edge 모듈을 개발하고 디버그하는 방법을 보여 줍니다.

Visual Studio용 Azure IoT Edge Tools 확장은 다음과 같은 이점을 제공합니다.

* 로컬 개발 컴퓨터에서 IoT Edge 솔루션 및 모듈을 생성, 편집, 빌드, 실행 및 디버그합니다.
* Azure IoT Hub를 통해 IoT Edge 디바이스에 IoT Edge 솔루션을 배포합니다.
* C 또는 C#으로 Azure IoT 모듈을 코딩하면서 Visual Studio 개발 환경의 모든 이점을 활용할 수 있습니다.
* IoT Edge 디바이스 및 모듈을 UI로 관리합니다.

이 문서에서는 Visual Studio 2019용 Azure IoT Edge 도구를 사용하여 IoT Edge 모듈을 개발하는 방법을 설명합니다. IoT Edge 디바이스에 프로젝트를 배포하는 방법도 알아봅니다. 현재 Visual Studio 2019는 C 및 C#으로 작성된 모듈을 지원합니다. 지원되는 디바이스 아키텍처는 Windows X64 및 Linux X64 또는 ARM32입니다. 지원되는 운영 체제, 언어 및 아키텍처에 대한 자세한 내용은 [언어 및 아키텍처 지원](module-development.md#language-and-architecture-support)을 참조하세요.
  
## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 Windows를 실행하는 컴퓨터를 개발 컴퓨터로 사용한다고 가정합니다. Windows 컴퓨터에서는 Windows 또는 Linux 모듈을 개발할 수 있습니다.

* **Windows 컨테이너** 로 모듈을 개발하려면 버전 1809/빌드 17763 이상을 실행하는 Windows 컴퓨터를 사용하세요.
* **Linux 컨테이너** 로 모듈을 개발하려면 [Docker Desktop 요구 사항](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)을 충족하는 Windows 컴퓨터를 사용하세요.

개발 컴퓨터에서 Visual Studio를 설치합니다. Visual Studio 2019 설치에 **Azure 개발** 및 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 확인합니다. [Visual Studio 2019를 수정](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true)하여 필요한 워크로드를 추가할 수 있습니다.

Visual Studio 2019가 준비되면 다음 도구와 구성 요소도 필요합니다.

* Visual Studio 마켓플레이스에서 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)를 다운로드 및 설치하여 Visual Studio 2019에서 IoT Edge 프로젝트를 만듭니다.

  > [!TIP]
  > Visual Studio 2017을 사용하는 경우 Visual Studio Marketplace에서 VS 2017용 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)를 다운로드하여 설치합니다.

* [Docker Community Edition](https://docs.docker.com/install/)을 개발 머신에 다운로드 및 설치하여 모듈 이미지를 빌드하고 실행합니다. 개발 중인 모듈 유형에 따라 Linux 컨테이너 모드 또는 Windows 컨테이너 모드에서 실행되도록 Docker CE를 설정해야 합니다.

* [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/)을 설치하여 IoT Edge 솔루션을 디버그, 실행 및 테스트할 로컬 개발 환경을 설정합니다. [Python(3.5/3.6/3.7/3.8) 및 Pip](https://www.python.org/)를 설치한 후 터미널에서 다음 명령을 실행하여 **iotedgehubdev** 패키지를 설치합니다. Azure IoT EdgeHub Dev Tool 버전이 0.3.0보다 높아야 합니다.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

* Vcpkg 라이브러리 관리자를 설치한 다음 Windows용 **azure-iot-sdk-c 패키지** 를 설치합니다.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

* 모듈 이미지를 저장할 [Azure Container Registry](../container-registry/index.yml) 또는 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)의 인스턴스를 만듭니다.

  > [!TIP]
  > 클라우드 레지스트리 대신 로컬 Docker 레지스트리를 프로토타입 및 테스트 목적으로 사용할 수 있습니다.

* 디바이스에서 모듈을 테스트하려면 하나 이상의 IoT Edge 디바이스가 있는 활성 IoT 허브가 필요합니다. 테스트용 IoT Edge 디바이스를 빠르게 만들려면 [Linux](quickstart-linux.md) 또는 [Windows](quickstart.md)에 대한 빠른 시작의 단계를 따르세요. 개발 머신에서 IoT Edge 디먼을 실행할 경우, Visual Studio에서 개발을 시작하기 전 EdgeHub 및 EdgeAgent를 중지해야 할 수도 있습니다.

### <a name="check-your-tools-version"></a>도구 버전 확인

1. **확장** 메뉴에서 **확장 관리** 를 선택합니다. **설치됨 > 도구** 를 펼치면 **Visual Studio용 Azure IoT Edge** 와 **Visual Studio용 클라우드 탐색기** 가 보입니다.

1. 설치된 버전을 적어둡니다. 이 버전을 Visual Studio Marketplace([클라우드 탐색기](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))의 최신 버전과 비교할 수 있습니다.

1. Visual Studio Marketplace에서 제공되는 것보다 버전이 낮을 경우, 다음 섹션에 나오는 대로 Visual Studio에서 도구를 업데이트합니다.

### <a name="update-your-tools"></a>도구 업데이트

1. **확장 관리** 창에서 **업데이트 > Visual Studio Marketplace** 를 펼치고, **Azure IoT Edge Tools** 또는 **Visual Studio용 클라우드 탐색기** 를 선택한 다음 **업데이트** 를 선택합니다.

1. 도구 업데이트를 다운로드한 후에 Visual Studio를 닫고 VSIX 설치 관리자를 사용하여 도구 업데이트를 트리거합니다.

1. 설치 관리자에서 **확인** 을 선택하여 시작한 다음, **수정** 을 선택하여 도구를 업데이트합니다.

1. 업데이트가 완료되면 **닫기** 를 선택하고 Visual Studio를 다시 시작합니다.

## <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge 프로젝트 만들기

Visual Studio의 IoT Edge 프로젝트 템플릿은 IoT Edge 디바이스에 배포할 수 있는 솔루션을 만듭니다. 먼저 Azure IoT Edge 솔루션을 만든 다음, 이 솔루션에서 첫 번째 모듈을 생성합니다. 각 IoT Edge 솔루션에는 둘 이상의 모듈이 포함될 수 있습니다.

> [!TIP]
> Visual Studio에서 만든 IoT Edge 프로젝트 구조는 Visual Studio Code의 구조와는 다릅니다.

1. Visual Studio에서 새 프로젝트를 만듭니다.

1. **새 프로젝트 만들기** 페이지에서 **Azure IoT Edge** 를 검색합니다. IoT Edge 디바이스의 플랫폼 및 아키텍처와 일치하는 프로젝트를 선택하고 **다음** 을 클릭합니다.

   :::image type="content" source="./media/how-to-visual-studio-develop-module/create-new-project.png" alt-text="새 프로젝트 만들기":::

1. **새 프로젝트 구성** 페이지에서 프로젝트 이름을 입력하고 위치를 지정한 다음 **만들기** 를 선택합니다.

1. **모듈 추가** 창에서 개발할 모듈 유형을 선택합니다. **기존 모듈** 을 선택하여 기존 IoT Edge 모듈을 배포에 추가할 수도 있습니다. 모듈 이름과 모듈 이미지 리포지토리를 지정합니다.

   Visual Studio는 **localhost:5000/<module name\>** 으로 리포지토리 URL을 자동으로 채웁니다. 테스트를 위해 로컬 Docker 레지스트리를 사용하는 경우 **localhost** 를 사용해도 됩니다. Azure Container Registry를 사용하는 경우 **localhost:5000** 을 레지스트리 설정의 로그인 서버로 바꿉니다. 로그인 서버는 **_\<registry name\>_ .azurecr.io** 와 같습니다. 최종 결과는 **\<*registry name*\>.azurecr.io/ _\<module name\>_** 와 같아야 합니다.

   **추가** 를 선택하여 프로젝트에 모듈을 추가합니다.

   ![애플리케이션 및 모듈 추가](./media/how-to-visual-studio-develop-csharp-module/add-module.png)

이제 Visual Studio 솔루션에 IoT Edge 프로젝트와 IoT Edge 모듈이 있습니다.

모듈 폴더에는 선택한 언어에 따라 이름이 `program.cs` 또는 `main.c`인 모듈 코드용 파일이 포함되어 있습니다. 이 폴더에는 모듈의 메타데이터를 설명하는 `module.json`이라는 파일도 포함되어 있습니다. 다양한 Docker 파일은 모듈을 Windows 또는 Linux 컨테이너로 빌드하는 데 필요한 정보를 제공합니다.

프로젝트 폴더에는 해당 프로젝트에 포함된 모든 모듈의 목록이 포함되어 있습니다. 지금은 하나의 모듈만 표시해야 하지만 더 추가할 수 있습니다. 프로젝트에 모듈을 추가하는 방법에 대한 자세한 내용은 이 문서 뒷부분의 [여러 모듈 빌드 및 디버그](#build-and-debug-multiple-modules) 섹션을 참조하세요.

프로젝트 폴더에는 `deployment.template.json`이라는 파일도 포함되어 있습니다. 이 파일은 IoT Edge 배포 매니페스트의 템플릿으로, 서로 통신하는 방법과 함께 디바이스에서 실행될 모든 모듈을 정의합니다. 배포 매니페스트에 대한 자세한 내용은 [모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요. 이 배포 템플릿을 열면 이 Visual Studio 프로젝트에서 만든 사용자 지정 모듈과 함께 두 개의 런타임 모듈인 **edgeAgent** 및 **edgeHub** 가 포함되어 있음을 알 수 있습니다. **SimulatedTemperatureSensor** 라는 네 번째 모듈도 포함되어 있습니다. 이 기본 모듈은 모듈을 테스트하는 데 사용할 수 있는 시뮬레이션된 데이터를 생성하거나 필요하지 않은 경우 삭제할 수 있습니다. 시뮬레이션된 온도 센서의 작동 방법을 확인하려면 [SimulatedTemperatureSensor.csproj 소스 코드](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)를 확인합니다.

### <a name="set-iot-edge-runtime-version"></a>IoT Edge 런타임 버전을 확인합니다.

IoT Edge 확장은 배포 자산을 만들 때 IoT Edge 런타임의 안정적인 최신 버전으로 기본 설정됩니다. 현재 최신 안정화 버전은 버전 1.2입니다. 1\.1 장기 지원 버전 또는 이전 1.0 버전을 실행하는 디바이스용 모듈을 개발하는 경우 일치하도록 Visual Studio에서 IoT Edge 런타임 버전을 업데이트합니다.

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 런타임 버전 설정** 을 선택합니다.

   :::image type="content" source="./media/how-to-visual-studio-develop-module/set-iot-edge-runtime-version.png" alt-text="프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 IoT Edge 런타임 버전 설정을 선택합니다.":::

1. 드롭다운 메뉴를 사용하여 IoT Edge 디바이스에서 실행 중인 런타임 버전을 선택한 다음 **확인** 을 선택하여 변경 사항을 저장합니다.

1. 새 런타임 버전으로 배포 매니페스트를 다시 생성합니다. 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **IoT Edge에 대한 배포 생성** 을 선택합니다.

## <a name="develop-your-module"></a>모듈 개발

새 모듈을 추가하면 코드를 건드리지 않고도 테스트를 시작할 수 있도록 빌드 및 디바이스에 배포할 준비가 된 기본 코드가 함께 제공됩니다. 모듈 코드는 `Program.cs`(C#의 경우) 또는 `main.c`(C의 경우)라는 파일의 모듈 폴더에 있습니다.

기본 솔루션은 **SimulatedTemperatureSensor** 모듈의 시뮬레이션된 데이터가 입력을 받아 IoT Hub로 전송하는 모듈로 라우팅되도록 빌드됩니다.

고유한 코드를 사용하여 모듈 템플릿을 사용자 지정할 준비가 된 경우 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용하여 보안, 디바이스 관리 및 안정성 등 IoT 솔루션에 대한 주요 요구 사항을 해결하는 모듈을 빌드합니다.

## <a name="set-up-the-iotedgehubdev-testing-tool"></a>iotedgehubdev 테스트 도구 설정

IoT edgeHub 개발자 도구는 로컬 개발 및 디버그 환경을 제공합니다. 이 도구는 IoT Edge 런타임 없이 IoT Edge 모듈을 시작할 수 있도록 하므로 IoT Edge 모듈 및 솔루션을 로컬로 만들고 개발하고 테스트하고 실행하고 디버그할 수 있습니다. 이미지를 컨테이너 레지스트리로 푸시한 후 테스트용 디바이스에 배포할 필요가 없습니다.

자세한 내용은 [Azure IoT EdgeHub 개발 도구](https://pypi.org/project/iotedgehubdev/)를 참조하세요.

도구를 초기화하려면 IoT Hub에서 IoT Edge 디바이스 연결 문자열을 제공합니다.

1. Azure Portal, Azure CLI 또는 Visual Studio Cloud Explorer에서 IoT Edge 디바이스의 연결 문자열을 검색합니다. 

1. **도구** 메뉴에서 **Azure IoT Edge Tools** > **IoT Edge 시뮬레이터 설정** 을 선택합니다.

1. 연결 문자열을 붙여넣고 **확인** 을 클릭합니다.

> [!NOTE]
> 모든 후속 Azure IoT Edge 솔루션에 결과가 자동으로 적용되기 때문에 이 단계들은 개발 컴퓨터에서 한 번만 수행해야 합니다. 다른 연결 스트링으로 변경해야 할 경우 이 과정을 다시 밟으면 됩니다.

## <a name="build-and-debug-a-single-module"></a>단일 모듈 빌드 및 디버그

여러 모듈이 포함된 전체 솔루션 내에서 실행하기 전데 모듈을 각각 테스트 및 디버그하는 것이 일반적입니다.

>[!TIP]
>개발 중인 IoT Edge 모듈의 유형에 따라 올바른 Docker 컨테이너 모드(Linux 컨테이너 모드 또는 Windows 컨테이너 모드)로 전환했는지 확인합니다. Docker 데스크톱 메뉴에서 두 가지 유형의 모드 간에 전환할 수 있습니다. **Windows 컨테이너를** 사용하도록 Windows 컨테이너로 전환을 선택하거나 **Linux 컨테이너로 전환을 선택하여 Linux 컨테이너를** 사용합니다. 

1. **솔루션 탐색기** 에서 모듈 폴더를 마우스 오른쪽 단추로 클릭하고 메뉴에서 **StartUp 프로젝트로 설정** 을 선택합니다.

   ![시작 프로젝트 설정](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. **F5** 키를 누르거나 도구 모음에서 실행 단추를 클릭하여 모듈을 실행합니다. 처음에는 10&ndash;20초가 걸릴 수 있습니다.

   ![모듈 실행](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 모듈이 성공적으로 초기화된 경우 .NET Core 콘솔 앱이 시작된 것을 볼 수 있습니다.

1. 모듈을 검사하기 위해 중단점을 설정합니다.

   * C#으로 개발하는 경우 **Program.cs** 의 `PipeMessage()` 함수에 중단점을 설정합니다.
   * C를 사용하는 경우 **main.c** 의 `InputQueue1Callback()` 함수에 중단점을 설정합니다.

1. **Git Bash** 또는 **WSL Bash** 셸에서 다음 명령을 실행하여 메시지를 전송해 모듈을 테스트합니다. (PowerShell 또는 명령 프롬프트에서는 `curl` 명령을 실행할 수 없습니다.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![단일 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

   중단점이 트리거됩니다. Visual Studio **로컬** 창에서 변수를 확인할 수 있습니다.

   > [!TIP]
   > `curl` 대신 [PostMan](https://www.getpostman.com/) 또는 다른 API 도구를 사용하여 메시지를 보낼 수도 있습니다.

1. **Ctrl + F5** 를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-debug-multiple-modules"></a>여러 모듈 빌드 및 디버그

단일 모듈 개발을 마친 후 여러 모듈이 있는 전체 솔루션을 실행하고 디버그합니다.

1. **솔루션 탐색기** 에서 프로젝트 폴더를 마우스 오른쪽 단추로 클릭하여 솔루션에 두 번째 모듈을 추가합니다. 메뉴에서 **추가** > **새 IoT Edge 모듈** 을 선택합니다.

   ![기존 IoT Edge 프로젝트에 새 모듈 추가](./media/how-to-visual-studio-develop-csharp-module/add-new-module.png)

1. `deployment.template.json` 파일을 열면 **모듈** 섹션에 새 모듈이 추가된 것을 볼 수 있습니다. 새 모듈에서 IoT Hub로 메시지를 보내기 위한 새 경로도 **경로** 섹션에 추가되었습니다. 시뮬레이션된 온도 센서에서 새 모듈로 데이터를 보내려면 다음 예와 같이 다른 경로를 추가합니다. 

    ```json
   "sensorTo<NewModuleName>": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/<NewModuleName>/inputs/input1\")"
    ```

1. 프로젝트 폴더를 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **시작 프로젝트로 설정** 을 선택합니다.

1. 중단점을 만든 다음 **F5** 를 눌러 여러 개의 모듈을 동시에 실행 및 디버그합니다. 여러 개의 .NET Core 콘솔 앱 창을 볼 수 있으며 각 창은 각기 다른 모듈을 나타냅니다.

   ![여러 모듈 디버그](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. **Ctrl + F5** 를 누르거나 중지 버튼을 클릭하여 디버깅을 중지합니다.

## <a name="build-and-push-images"></a>이미지 빌드 및 푸시

1. IoT Edge 프로젝트가 개별 모듈 중 하나가 아니라 시작 프로젝트인지 확인합니다. 모듈 이미지를 빌드할 구성으로 **디버그** 와 **릴리스** 중 하나를 선택합니다.

    > [!NOTE]
    > **디버그** 를 선택하면, Visual Studio가 `Dockerfile.(amd64|windows-amd64).debug`을(를) 사용하여 Docker 이미지를 빌드합니다. 이미지를 빌드하는 동안 컨테이너 이미지의 .NET Core 명령 줄 디버거 VSDBG가 포함됩니다. 프로덕션이 준비된 IoT Edge 모듈의 경우, VSDBG 없이 `Dockerfile.(amd64|windows-amd64)`을(를) 사용하는 **릴리스** 구성을 권장합니다.

1. ACR(Azure Container Registry) 같은 프라이빗 레지스트리를 사용할 경우, 다음 Docker 명령을 사용하여 로그인합니다.  Azure Portal에 있는 레지스트리의 **액세스 키** 페이지에서 사용자 이름과 암호를 가져올 수 있습니다. 로컬 레지스트리를 사용할 경우 [로컬 레지스트리를 실행](https://docs.docker.com/registry/deploying/#run-a-local-registry)할 수 있습니다.

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Azure Container Registry 같은 프라이빗 레지스트리를 사용할 경우, `deployment.template.json` 파일에 있는 런타임 설정에 레지스트리 로그인 정보를 추가해야 합니다. 자리 표시자를 실제 ACR 관리 사용자 이름, 암호 및 레지스트리 이름으로 바꿉니다.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

   >[!NOTE]
   >이 문서에서는 개발 및 테스트 시나리오에 편리하게 사용할 수 있는 관리자 로그인 자격 증명을 Azure Container Registry에 사용합니다. 프로덕션 시나리오에 사용할 준비가 되면 서비스 주체 같은 최소 권한 인증 옵션을 사용하는 것이 좋습니다. 자세한 내용은 [컨테이너 레지스트리에 대한 액세스 관리](production-checklist.md#manage-access-to-your-container-registry)를 참조하세요.

1. **솔루션 탐색기** 에서 프로젝트 폴더를 마우스 오른쪽 단추로 클릭한 다음, **IoT Edge 모듈 빌드 및 푸시** 를 선택하여 각 모듈의 Docker 이미지를 빌드하고 푸시합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

IoT Edge 디바이스를 설정할 때 사용한 빠른 시작 문서에서는 Azure Portal을 사용하여 모듈을 배포했습니다. Visual Studio 용 클라우드 탐색기를 사용하여 모듈을 배포할 수도 있습니다. 시나리오의 배포 매니페스트인 `deployment.json` 파일이 이미 있으므로 배포를 수신할 디바이스만 선택하면 됩니다.

1. **보기** > **클라우드 탐색기** 를 클릭하여 **클라우드 탐색기** 를 엽니다. Visual Studio 2019에 로그인되었는지 확인합니다.

1. **클라우드 탐색기** 에서 구독을 펼쳐서 배포하려는 Azure IoT Hub 및 Azure IoT Edge 디바이스를 찾습니다.

1. IoT Edge 디바이스를 마우스 오른쪽 단추로 클릭하여 배포를 만듭니다. `deployment.arm32v7.json`과 같은 Visual Studio 솔루션의 **config** 폴더에 있는 플랫폼에 대해 구성된 배포 매니페스트로 이동합니다.

1. 새로 고침 버튼을 클릭하여 **SimulatedTemperatureSensor** 모듈과 함께 실행되는 새 모듈과 **$edgeAgent** 및 **$edgeHub** 를 확인합니다.

## <a name="view-generated-data"></a>생성된 데이터 보기

1. 특정 IoT Edge 디바이스에 대한 D2C 메시지를 모니터링하려면 **클라우드 탐색기** 의 IoT 허브에서 선택한 후 **작업** 창에서 **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 클릭합니다.

1. 데이터 모니터링을 중지하려면 **작업** 창에서 **기본 제공 이벤트 엔드포인트 모니터링 중지** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

IoT Edge 디바이스의 사용자 지정 모듈을 개발하려면 [Azure IoT Hub SDK에 대해 숙지하고 사용](../iot-hub/iot-hub-devguide-sdks.md)하십시오.
