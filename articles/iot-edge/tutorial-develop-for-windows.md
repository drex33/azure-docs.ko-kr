---
title: 자습서 - Azure IoT Edge를 사용하여 Windows 디바이스용 모듈 개발
description: 이 자습서에서는 Windows 디바이스용 Windows 컨테이너를 사용하여 IoT Edge 모듈을 개발하기 위해 개발 컴퓨터 및 클라우드 리소스를 설정하는 과정을 설명합니다.
author: kgremban
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e3e4add97b63dc7ed6e375f90eb3d5d48a81ffef
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038995"
---
# <a name="tutorial-develop-iot-edge-modules-using-windows-containers"></a>자습서: Windows 컨테이너를 사용하여 IoT Edge 모듈 개발

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Visual Studio를 사용하여 IoT Edge를 실행하는 코드를 개발하고 Windows 디바이스에 배포합니다.

>[!NOTE]
>IoT Edge 1.1 LTS는 Windows 컨테이너를 지원하는 마지막 릴리스 채널입니다. 버전 1.2부터 Windows 컨테이너가 지원되지 않습니다. Windows 디바이스에서 IoT Edge를 실행하려면 [Windows에서 Linux용 IoT Edge](iot-edge-for-linux-on-windows.md)를 사용하거나 이동하는 것이 좋습니다.

이 자습서에서는 고유한 코드를 개발하고 IoT Edge 디바이스에 배포하기 위한 과정을 안내합니다. 이 자습서는 특정 프로그래밍 언어 또는 Azure 서비스에 대해 좀 더 자세한 내용을 설명하는 다른 자습서를 진행하기 전에 먼저 살펴보아야 하는 유용한 내용을 제공합니다.

이 자습서에서는 **Windows 디바이스에 C# 모듈** 을 배포하는 예제를 사용합니다. 이 예가 가장 일반적인 개발 시나리오이기 때문에 선택되었습니다. 다른 언어로 개발하거나 Azure 서비스를 모듈로 배포하려는 경우에도 이 자습서를 통해 개발 도구에 대해 자세히 알아볼 수 있습니다. 개발 개념을 이해하고 나면 기본 언어 또는 Azure 서비스를 선택하여 세부 정보를 자세히 살펴볼 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 개발 머신을 설정합니다.
> * Visual Studio용 IoT Edge 도구를 사용하여 새 프로젝트를 만듭니다.
> * 프로젝트를 컨테이너로 빌드하고 Azure Container Registry에 저장합니다.
> * IoT Edge 디바이스에 코드를 배포합니다.

## <a name="prerequisites"></a>사전 요구 사항

개발 머신:

* Windows 10 1809 업데이트 이상
* 본인의 개발 선호도에 자신의 머신 또는 가상 머신을 사용할 수 있습니다.
  * 개발 머신에서 중첩된 가상화를 지원하는지 확인합니다. 이 기능은 다음 섹션에서 설치하는 컨테이너 엔진을 실행하는 데 필요합니다.
* [Git](https://git-scm.com/)를 설치합니다.

Windows의 Azure IoT Edge 디바이스:

* [Windows 컨테이너를 사용하여 Azure IoT Edge를 설치하고 관리](how-to-install-iot-edge-windows-on-windows.md)합니다.
* 개발 머신에서 IoT Edge를 실행하지 않고, 가능한 한 별도의 디바이스를 사용하는 것이 좋습니다. 이와 같이 개발 머신과 IoT Edge 디바이스를 구분하면 실제 배포 시나리오를 정확히 반영할 수 있고 개념 차이를 유지하는 데 도움이 됩니다.

클라우드 리소스:

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)입니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>주요 개념

이 자습서는 IoT Edge 모듈 개발 과정을 안내합니다. *IoT Edge 모듈* 또는 경우에 따라 간단히 *모듈* 이라고 하는 이 기능은 실행 코드가 포함된 컨테이너입니다. 하나 이상의 모듈을 IoT Edge 디바이스에 배포할 수 있습니다. 모듈은 센서에서 데이터 수집, 데이터 분석 또는 데이터 정리 작업 수행, IoT Hub로 메시지를 보내기 등의 특정 작업을 수행합니다. 자세한 내용은 [Azure IoT Edge 모듈 이해](iot-edge-modules.md)를 참조하세요.

IoT Edge 모듈을 개발할 때 개발 머신과 모듈을 결과적으로 배치할 대상 IoT Edge 디바이스 간의 차이를 이해하는 것이 중요합니다. 모듈 코드를 포함하기 위해 빌드하는 컨테이너는 *대상 디바이스* 의 OS(운영 체제)와 일치해야 합니다. Windows 컨테이너 개발에서는 Windows 컨테이너가 Windows 운영 체제에서만 실행되므로 이 개념이 좀 더 간단합니다. 하지만 예를 들어, Windows 개발 머신을 사용하여 Linux IoT Edge 디바이스용 모듈을 빌드할 수 있습니다. 이 시나리오에서는 개발 머신에서 Linux 컨테이너를 실행 중인지 확인해야 합니다. 이 자습서를 진행할 때는 *개발 머신 OS* 와 *컨테이너 OS* 간의 차이점에 유의해야 합니다.

이 자습서는 IoT Edge를 실행하는 Windows 디바이스를 대상으로 합니다. Windows IoT Edge 디바이스는 Windows 컨테이너를 사용합니다. 이 자습서에서는 Visual Studio를 사용할 예정이므로 이 도구로 Windows 디바이스용 개발을 진행하는 것이 좋습니다. 지원 차이점이 있지만 Visual Studio Code를 사용할 수도 있습니다.

다음 표에는 Visual Studio Code 및 Visual Studio의 **Windows 컨테이너** 에 지원되는 개발 시나리오가 나와 있습니다.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure 서비스** | Azure 기능 <br> Azure Stream Analytics |   |
| **언어** | C#(디버깅이 지원되지 않음) | C <br> C# |
| **자세한 정보** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="install-container-engine"></a>컨테이너 엔진 설치

IoT Edge 모듈은 컨테이너로 패키지되므로, 개발 머신에 컨테이너를 빌드 및 관리하기 위한 컨테이너 엔진이 필요합니다. Docker Desktop이 많은 기능을 제공하며 컨테이너 엔진으로 널리 사용되고 있으므로 개발을 위해 이 도구를 사용하는 것이 좋습니다. Windows 머신에 Docker Desktop이 있으면 Linux 컨테이너와 Windows 컨테이너 간을 전환하면서 다양한 IoT Edge 디바이스 유형에 대한 모듈을 쉽게 개발할 수 있습니다.

개발 머신에 설치하려면 다음 Docker 설명서를 참조하세요.

* [Windows용 Docker Desktop 설치](https://docs.docker.com/docker-for-windows/install/)

  * Windows용 Docker Desktop을 설치할 때 Linux 컨테이너를 사용할지 아니면 Windows 컨테이너를 사용할지 묻는 메시지가 표시됩니다. 이 자습서에서는 **Windows 컨테이너** 를 사용합니다. 자세한 내용은 [Windows 및 Linux 컨테이너 간 전환](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)을 참조하세요.

## <a name="set-up-visual-studio-and-tools"></a>Visual Studio 및 도구 설정

Visual Studio용 IoT 확장을 사용하여 IoT Edge 모듈을 개발합니다. 이러한 확장은 프로젝트 템플릿을 제공하고, 배포 매니페스트 생성을 자동화하고, IoT Edge 디바이스를 모니터링 및 관리할 수 있도록 합니다. 이 섹션에서는 Visual Studio 및 IoT Edge 확장을 설치한 후 Visual Studio 내에서 IoT Hub 리소스를 관리하도록 Azure 계정을 설정합니다.

이 자습서에서는 Visual Studio 2019의 개발 단계를 설명합니다. Visual Studio 2017(버전 15.7 이상)을 사용하는 경우 단계가 유사합니다. Visual Studio Code를 사용하는 경우는 [Visual Studio Code를 사용하여 Azure IoT Edge용 모듈 개발 및 디버그](how-to-vs-code-develop-module.md)의 지침을 참조하세요.

1. 개발 머신에서 Visual Studio 2019를 준비합니다.

   * 개발 머신에 Visual Studio가 아직 없는 경우 다음 워크로드와 함께 [Visual Studio 2019를 설치](/visualstudio/install/install-visual-studio)합니다.

      * Azure 개발
      * C++를 사용한 데스크톱 개발
      * .NET Core 플랫폼 간 개발

   * 개발 머신에 Visual Studio 2019가 이미 있는 경우 [Visual Studio 수정](/visualstudio/install/modify-visual-studio) 단계에 따라 필요한 워크로드를 추가합니다.

2. Visual Studio 2019용 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 확장을 다운로드하여 설치합니다.

   Visual Studio 2017(버전 15.7 이상)을 사용하는 경우 [Visual Studio 2017용 Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)를 다운로드하여 설치합니다.

3. 설치가 완료되면 Visual Studio 2019를 열고 **코드를 사용하지 않고 계속** 을 선택합니다.

4. **보기** > **클라우드 탐색기** 를 선택합니다.

5. 클라우드 탐색기에서 프로필 아이콘을 선택하고 Azure 계정에 로그인합니다(아직 로그인하지 않은 경우).

6. 로그인하면 Azure 구독이 나열됩니다. IoT Hub가 있는 구독을 확장합니다.

7. 구독에서 **IoT Hub** 를 확장한 다음 사용자의 IoT Hub를 확장합니다. IoT 디바이스의 목록이 표시되면 이 탐색기에서 관리할 수 있습니다.

   ![클라우드 탐색기에서 IoT Hub 리소스에 액세스](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>새 모듈 프로젝트 만들기

Azure IoT Edge Tools 확장에서는 Visual Studio에서 지원되는 모든 IoT Edge 모듈 언어를 위한 프로젝트 템플릿이 제공됩니다. 이러한 템플릿에는 작업 모듈을 배포하여 IoT Edge를 테스트하는 데 필요한 모든 파일 및 코드가 포함되어 있으며, 사용자 고유의 비즈니스 논리를 사용하여 템플릿을 사용자 지정하기 위한 시작 지점이 되기도 합니다.

1. **파일** > **새로 만들기** > **프로젝트...** 를 선택합니다.

2. 새 프로젝트 창에서 **IoT Edge** 를 검색하고, **Azure IoT Edge(Windows amd64)** 프로젝트를 선택합니다. **다음** 을 클릭합니다.

   ![새 Azure IoT Edge 프로젝트 만들기](./media/tutorial-develop-for-windows/new-project.png)

3. 새 프로젝트 구성 창에서 프로젝트 및 솔루션의 이름을 구체적인 이름(예: **CSharpTutorialApp**)으로 바꿉니다. **만들기** 를 클릭하여 프로젝트를 만듭니다.

   ![새 Azure IoT Edge 프로젝트 구성](./media/tutorial-develop-for-windows/configure-project.png)

4. 모듈 추가 창에서 다음 값을 사용하여 프로젝트를 구성합니다.

   | 필드 | 값 |
   | ----- | ----- |
   | Visual Studio 템플릿 | **C# 모듈** 을 선택합니다. |
   | 모듈 이름 | 기본값 **IotEdgeModule1** 을 그대로 사용합니다. |
   | 리포지토리 URL | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 모듈 프로젝트 이름 값에서 미리 채워져 있습니다. **localhost:5000** 을 Azure 컨테이너 레지스트리의 **로그인 서버** 값으로 바꿉니다. Azure Portal에 있는 컨테이너 레지스트리의 개요 페이지에서 Login 서버 값을 검색할 수 있습니다. <br><br> 마지막 이미지 리포지토리는 \<registry name\>.azurecr.io/iotedgemodule1과 같습니다. |

      ![대상 디바이스, 모듈 유형 및 컨테이너 레지스트리에 대해 프로젝트 구성](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. **추가** 를 선택하여 모듈을 만듭니다.

Visual Studio 창에 새 프로젝트가 로드되면 생성된 다음 파일을 살펴보고 이해합니다.

* **CSharpTutorialApp** 이라는 IoT Edge 프로젝트입니다.
  * **Modules** 폴더에는 프로젝트에 포함된 모듈에 대한 포인터가 포함되어 있습니다. 이 경우 IotEdgeModule1이어야 합니다.
  * 숨겨진 **.env** 파일은 컨테이너 레지스트리의 자격 증명을 저장합니다. 이러한 자격 증명은 IoT Edge 디바이스와 공유되므로 이러한 디바이스에서 컨테이너 이미지를 끌어올 수 있습니다.
  * **deployment.template.json** 파일은 배포 매니페스트를 만드는 데 도움이 되는 템플릿입니다. *배포 매니페스트* 는 디바이스에 배포하려는 모듈, 구성 방법 및 모듈끼리 및 모듈-클라우드 간에 통신하는 방법을 정확히 정의하는 파일입니다.
    > [!TIP]
    > 레지스트리 자격 증명 섹션에서 솔루션을 만들 때 제공한 정보로 주소가 자동으로 채워집니다. 단, 사용자 이름 및 암호는 .env 파일에 저장된 변수를 참조합니다. 이러한 방식은 보안을 위한 것입니다. .env 파일은 Git에서 무시되지만 배포 템플릿은 무시되지 않기 때문입니다.
* IoT Edge 모듈 프로젝트: **IotEdgeModule1**.
  * **program.cs** 파일은 프로젝트 템플릿과 함께 제공되는 기본 C# 모듈 코드를 포함합니다. 기본 모듈은 소스에서 입력을 가져와 IoT Hub에 전달합니다.
  * **module.json** 파일은 전체 이미지 리포지토리, 이미지 버전, 지원되는 각 플랫폼에 사용할 Dockerfile을 비롯하여 모듈에 대한 세부 정보를 포함합니다.

### <a name="set-iot-edge-runtime-version"></a>IoT Edge 런타임 버전 설정

IoT Edge 확장은 배포 자산을 만들 때 IoT Edge 런타임의 안정적인 최신 버전으로 기본 설정됩니다. 현재 최신 안정적인 버전은 버전 1.2입니다. 

Windows 컨테이너는 1.1 장기 지원 버전 또는 이전 1.0 버전에서만 지원됩니다. Windows 컨테이너를 사용하여 디바이스용 모듈을 개발하려면 Visual Studio에서 IoT Edge 런타임 버전을 업데이트하여 해당 디바이스의 IoT Edge 버전과 일치시킵니다.

1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 런타임 버전 설정** 을 선택합니다.

   :::image type="content" source="./media/how-to-visual-studio-develop-module/set-iot-edge-runtime-version.png" alt-text="프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 IoT Edge 런타임 버전 설정을 선택합니다.":::

1. 드롭다운 메뉴를 사용하여 IoT Edge 디바이스에서 실행 중인 런타임 버전을 선택한 다음 **확인** 을 선택하여 변경 사항을 저장합니다.

1. 새 런타임 버전으로 배포 매니페스트를 다시 생성합니다. 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **IoT Edge에 대한 배포 생성** 을 선택합니다.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>IoT Edge 에이전트에 레지스트리 자격 증명 제공

IoT Edge 런타임은 IoT Edge 디바이스에 컨테이너 이미지를 끌어오기 위해 레지스트리 자격 증명이 필요합니다. IoT Edge 확장은 Azure에서 컨테이너 레지스트리 정보를 가져와서 배포 템플릿에 채우려고 합니다.

1. 모듈 솔루션에서 **deployment.template.json** 파일을 엽니다.

1. **registryCredentials** 속성을 $edgeAgent desired 속성에서 찾습니다. 프로젝트를 만들 때 제공한 정보에서 레지스트리 주소를 자동으로 채운 다음, 사용자 이름 및 암호 필드에 변수 이름을 포함해야 합니다. 다음은 그 예입니다.

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. 모듈 솔루션의 **.env** 파일을 엽니다. (솔루션 탐색기에는 기본적으로 숨겨져 있으므로 표시하려면 **모든 파일 표시** 단추를 선택해야 할 수도 있습니다.)

1. Azure Container Registry에서 복사한 **사용자 이름** 및 **암호** 값을 추가합니다.

1. .env 파일에 변경 내용을 저장합니다.

>[!NOTE]
>이 자습서에서는 개발 및 테스트 시나리오에 편리하게 사용할 수 있는 관리자 로그인 자격 증명을 Azure Container Registry에 사용합니다. 프로덕션 시나리오에 사용할 준비가 되면 서비스 주체 같은 최소 권한 인증 옵션을 사용하는 것이 좋습니다. 자세한 내용은 [컨테이너 레지스트리에 대한 액세스 관리](production-checklist.md#manage-access-to-your-container-registry)를 참조하세요.

### <a name="review-the-sample-code"></a>샘플 코드 검토

만든 솔루션 템플릿에는 IoT Edge 모듈용 샘플 코드가 포함되어 있습니다. 이 샘플 모듈은 메시지 수신한 후 전달합니다. 파이프라인 기능은 IoT Edge의 중요한 개념인 모듈이 서로 통신하는 방식을 보여 줍니다.

각 모듈의 코드에는 여러 개의 *입력* 및 *출력* 큐가 선언될 수 있습니다. 디바이스에서 실행되는 IoT Edge 허브는 모듈 중 하나의 출력에 있는 메시지를 하나 이상의 모듈 입력으로 라우팅합니다. 입력 및 출력을 선언하는 특정 언어는 코드마다 다르지만 개념은 모든 모듈에서 동일합니다. 모듈 간 라우팅에 대한 자세한 내용은 [경로 선언](module-composition.md#declare-routes)을 참조하세요.

프로젝트 템플릿과 함께 제공되는 샘플 C# 코드는 .NET용 IoT Hub SDK에서 [ModuleClient 클래스](/dotnet/api/microsoft.azure.devices.client.moduleclient)를 사용합니다.

1. **program.cs** 파일에서 **SetInputMessageHandlerAsync** 메서드를 찾습니다.

2. [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) 메서드는 들어오는 메시지를 수신하는 입력 큐를 설정합니다. 이 메서드를 검토하고 이 메서드가 **input1** 이라는 입력 큐를 초기화하는 방법을 확인합니다.

   ![SetInputMessageHandlserAsync 생성자에서 입력 이름 찾기](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. 다음으로 **SendEventAsync** 메서드를 찾습니다.

4. [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) 메서드는 수신된 메시지를 처리하며, 해당 메시지를 전달할 출력 큐를 설정합니다. 이 메서드를 검토하고 이 메서드가 **output1** 이라는 출력 큐를 초기화하는 모습을 살펴보세요.

   ![SendEventAsync 생성자에서 출력 이름 찾기](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. **deployment.template.json** 파일을 엽니다.

6. $edgeAgent의 원하는 속성 중에서 **modules** 속성을 찾습니다.

   여기에는 두 개의 모듈이 표시되어야 합니다. 하나는 **SimulatedTemperatureSensor** 모듈로, 모듈을 테스트하는 데 사용할 수 있는 시뮬레이트된 온도 데이터를 제공하기 위해 기본적으로 모든 템플릿에 포함되어 있습니다. 다른 하나는 프로젝트에서 만든 **IotEdgeModule1** 모듈입니다.

   이 모듈 속성은 디바이스 배포에 어떤 모듈을 포함할 것인지 선언합니다.

7. $edgeHub의 원하는 속성 중에서 **routes** 속성을 찾습니다.

   IoT Edge 허브 모듈의 기능 중 하나는 배포의 모든 모듈 간에 메시지를 라우팅하는 것입니다. routes 속성의 값을 검토합니다. 첫 번째 경로 **IotEdgeModule1ToIoTHub** 는 와일드카드 문자(* *\** _)를 사용하여 IotEdgeModule1 모듈의 출력 큐에서 오는 모든 메시지를 포함합니다. 이러한 메시지는 IoT Hub를 나타내는 예약 이름인 _$upstream*으로 이동됩니다. 두 번째 경로 **sensorToIotEdgeModule1** 은 SimulatedTemperatureSensor 모듈에서 들어오는 메시지를 받아서 IotEdgeModule1 모듈의 *input1* 입력 큐로 라우팅합니다.

   ![deployment.template.json의 경로 검토](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>솔루션 빌드 및 푸시

모듈 코드 및 배포 템플릿을 검토하여 몇 가지 주요 배포 개념을 이해했습니다. 이제 IotEdgeModule1 컨테이너 이미지를 빌드하여 컨테이너 레지스트리로 푸시할 준비가 되었습니다. Visual Studio용 IoT 도구 확장을 사용할 경우 이 단계에서도 템플릿 파일의 정보와 솔루션 파일의 모듈 정보에 따라 배포 매니페스트가 생성됩니다.

### <a name="sign-in-to-docker"></a>Docker에 로그인

레지스트리에 저장될 컨테이너 이미지를 푸시할 수 있도록 개발 머신의 Docker에 컨테이너 레지스트리 자격 증명을 제공합니다.

1. PowerShell 또는 Windows 명령 프롬프트를 엽니다.

2. 레지스트리를 만든 후 저장한 Azure Container Registry 자격 증명을 사용하여 Docker에 로그인합니다.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` 사용을 권장하는 보안 경고가 표시될 수 있습니다. 이 모범 사례는 프로덕션 시나리오에 권장되지만 이 자습서에는 포함되지 않습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 참조를 참조하세요.

### <a name="build-and-push"></a>빌드 및 푸시

이제 개발 머신에서 컨테이너 레지스트리에 액세스할 수 있으며 IoT Edge 디바이스도 마찬가지입니다. 다음에는 프로젝트 코드를 컨테이너 이미지로 변환할 차례입니다.

1. **CSharpTutorialApp** 프로젝트 폴더를 마우스 오른쪽 단추로 클릭하고 **IoT Edge 모듈 빌드 및 푸시** 를 선택합니다.

   ![IoT Edge 모듈 빌드 및 푸시](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   빌드 및 푸시 명령은 세 가지 작업을 시작합니다. 먼저, 배포 템플릿 및 기타 솔루션 파일의 정보로 작성된 전체 배포 매니페스트를 포함하는 **config** 라는 새 폴더를 솔루션에 만듭니다. 둘째, `docker build`를 실행하여 대상 아키텍처의 적절한 dockerfile을 기준으로 컨테이너 이미지를 빌드합니다. 그런 다음, `docker push`를 실행하여 컨테이너 레지스트리에 이미지 리포지토리를 푸시합니다.

   이 프로세스는 처음에는 몇 분 정도 걸릴 수 있지만 다음번에 명령을 실행할 때는 더 빨라집니다.

2. 새로 만든 config 폴더에서 **deployment.windows amd64.json** 파일을 엽니다. (Visual Studio의 솔루션 탐색기에는 config 폴더가 표시되지 않을 수 있습니다. 이 경우 솔루션 탐색기 작업 표시줄에서 **모든 파일 표시** 아이콘을 선택합니다.)

3. IotEdgeModule1 섹션에서 **image** 매개 변수를 찾습니다. 이 이미지에는 module.json 파일의 이름, 버전 및 아키텍처 태그가 있는 전체 이미지 리포지토리가 포함됩니다.

4. IotEdgeModule1 폴더에서 **module.json** 파일을 엽니다.

5. 모듈 이미지의 버전 번호를 변경합니다. ($schema-version이 아닌 버전) 예를 들어, 모듈 코드가 약간 수정된 것처럼 패치 버전 번호를 **0.0.2** 로 늘립니다.

   >[!TIP]
   >모듈 버전을 통해 버전을 관리할 수 있으며, 프로덕션 환경에 업데이트를 밸포하기 전에 소규모 디바이스 세트에서 변경 내용을 테스트해볼 수 있습니다. 모듈을 빌드 및 푸시하기 전에 모듈 버전을 늘리지 않으면 컨테이너 레지스트리의 리포지토리를 덮어쓰게 됩니다.

6. module.json 파일에 변경 내용을 저장합니다.

7. **CSharpTutorialApp** 프로젝트 폴더를 다시 마우스 오른쪽 단추로 클릭하고 **IoT Edge 모듈 빌드 및 푸시** 를 다시 선택합니다.

8. **deployment.windows-amd64.json** 파일을 다시 엽니다. 빌드 및 푸시 명령을 다시 실행할 때는 새 파일이 생성되지 않은 것을 알 수 있습니다. 대신, 변경 내용을 반영하도록 동일한 파일이 업데이트되었습니다. 이제 IotEdgeModule1 이미지는 컨테이너의 0.0.2 버전을 가리킵니다. 배포 매니페스트의 이러한 변경은 끌어올 새 모듈 버전이 있음을 IoT Edge 디바이스에 알려주는 방법입니다.

9. 빌드 및 푸시 명령이 어떤 작업을 수행했는지를 추가로 확인하려면 [Azure Portal](https://portal.azure.com)로 이동한 후 컨테이너 레지스트리로 이동합니다.

10. 컨테이너 레지스트리에서 **리포지토리** 를 선택하고 **iotedgemodule1** 을 선택합니다. 이미지의 두 버전이 레지스트리에 푸시되었는지 확인합니다.

    ![컨테이너 레지스트리에서 두 이미지 버전 보기](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>문제 해결

모듈 이미지를 빌드 및 푸시할 때 오류가 발생하는 경우 개발 머신의 Docker 구성과 관련이 있는 경우가 자주 있습니다. 다음 사항을 확인하여 구성을 검토합니다.

* 컨테이너 레지스트리에서 복사한 자격 증명을 사용하여 `docker login` 명령을 실행했나요? 이러한 자격 증명은 Azure에 로그인하는 데 사용하는 자격 증명과 다릅니다.
* 컨테이너 리포지토리가 올바른가요? 컨테이너 레지스트리 이름과 모듈 이름이 올바른가요? IotEdgeModule1 폴더에서 **module.json** 파일을 열어 확인합니다. 리포지토리 값은 **\<registry name\>.azurecr.io/iotedgemodule1** 과 같습니다.
* 모듈에 **IotEdgeModule1** 이외의 이름을 사용한 경우 해당 이름이 솔루션 전체에서 일관되나요?
* 머신에서 빌드하는 것과 같은 유형의 컨테이너가 실행되고 있나요? 이 자습서는 Windows IoT Edge 디바이스용이므로 Visual Studio 파일에 **windows-amd64** 확장명이 있어야 하며 Docker Desktop이 Windows 컨테이너를 실행해야 합니다.

## <a name="deploy-modules-to-device"></a>디바이스에 모듈 배포

이제 빌드한 컨테이너 이미지를 디바이스에 배포해야 하므로 컨테이너 레지스트리에 저장되어 있는지 확인합니다. IoT Edge 디바이스가 작동되고 실행 중인지 확인합니다.

1. Visual Studio에서 클라우드 탐색기를 열고 IoT 허브에 대한 세부 정보를 확장합니다.

2. 배포하려는 디바이스의 이름을 선택합니다. **동작** 목록에서 **배포 만들기** 를 선택합니다.

   ![단일 디바이스용 배포 만들기](./media/tutorial-develop-for-windows/create-deployment.png)

3. 파일 탐색기에서 프로젝트의 config 폴더로 이동한 후 **deployment.windows amd64.json** 파일을 선택합니다. 이 파일은 종종 `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`에 있습니다.

   deployment.template.json 파일은 전체 모듈 이미지 값을 포함하지 않으므로 사용하지 않도록 합니다.

4. 클라우드 탐색기에서 IoT Edge 디바이스에 대한 세부 정보를 확장하여 디바이스에서 모듈을 확인합니다.

5. **새로 고침** 단추로 디바이스 상태를 업데이트하여 SimulatedTemperatureSensor 및 IotEdgeModule1 모듈이 디바이스에 배포되었는지 확인합니다.

   ![IoT Edge 디바이스에서 실행되는 모듈을 봅니다.](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>디바이스에서 메시지 보기

IotEdgeModule1 코드는 입력 큐의 메시지를 받고 출력 큐를 통해 메시지를 전달합니다. 배포 매니페스트는 SimulatedTemperatureSensor에서 IotEdgeModule1로 메시지를 전달한 다음, IotEdgeModule1에서 IoT Hub로 메시지를 전달하는 경로를 선언했습니다. Azure IoT Edge Tools for Visual Studio를 사용하여 개별 디바이스에서 IoT Hub에 도착하는 메시지를 볼 수 있습니다.

1. Visual Studio 클라우드 탐색기에서 배포한 IoT Edge 디바이스의 이름을 선택합니다.

2. **동작** 메뉴에서 **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

3. Visual Studio의 **출력** 섹션에서 IoT Hub에 메시지가 도착하는 것을 확인합니다.

   두 모듈이 모두 시작하는 데 몇 분 정도 걸릴 수 있습니다. IoT Edge 런타임은 새 배포 매니페스트를 받고, 컨테이너 런타임에서 모듈 이미지를 끌어온 후 각 새 모듈을 시작해야 합니다.

   ![디바이스에서 클라우드로 수신되는 메시지 보기](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>디바이스에서 변경 내용 보기

디바이스 자체에서 발생하는 상황을 확인하려는 경우 이 섹션의 명령을 사용하여 디바이스에서 실행되는 IoT Edge 런타임 및 모듈을 검사합니다.

이 섹션의 명령은 개발 머신용이 아니라 IoT Edge 디바이스용입니다. IoT Edge 디바이스용으로 가상 머신을 사용하고 있는 경우 지금 연결합니다. Azure에서 가상 머신의 개요 페이지로 이동한 후 **연결** 을 선택하여 원격 데스크톱 연결에 액세스합니다. 디바이스에서 명령 창 또는 PowerShell 창을 열어 `iotedge` 명령을 실행합니다.

* 디바이스에 배포된 모든 모듈을 확인하고 해당 상태를 확인합니다.

   ```cmd
   iotedge list
   ```

   4개의 모듈(IoT Edge 런타임 모듈 2개, SimulatedTemperatureSensor 및 IotEdgeModule1)이 표시되어야 합니다. 4개의 모듈 모두 실행 중으로 표시됩니다.

* 특정 모듈에 대한 로그를 검사합니다.

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge 모듈은 대/소문자를 구분하지 않습니다.

   SimulatedTemperatureSensor 및 IotEdgeModule1 로그는 처리 중인 메시지를 표시해야 합니다. edgeAgent 모듈은 다른 모듈을 시작하므로, 해당 로그는 배포 매니페스트 구현에 대한 정보를 포함합니다. 나열되지 않거나 실행되고 있지 않은 모듈이 있으면 edgeAgent 로그에 오류가 있을 수 있습니다. edgeHub 모듈은 모듈과 IoT Hub 간의 통신을 담당합니다. 모듈이 작동되고 실행 중이지만 IoT Hub에 메시지가 도착하지 않으면 edgeHub 로그에 오류가 있을 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 디바이스와 동일한 IoT Edge 디바이스를 계속 사용해도 됩니다.

그렇지 않은 경우 요금이 발생하지 않도록 이 문서에서 사용한 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 개발 머신에 Visual Studio 2019를 설치하고 첫 번째 IoT Edge 모듈을 배포했습니다. 기본 개념을 파악했으므로 전달된 데이터를 분석할 수 있게 모듈에 기능을 추가해 보세요. 기본 설정 언어를 선택합니다.

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)