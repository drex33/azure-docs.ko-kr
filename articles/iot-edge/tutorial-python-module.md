---
title: 자습서 - Azure IoT Edge를 사용하는 사용자 지정 Python 모듈 자습서
description: 이 자습서에서는 Python 코드를 사용하여 IoT Edge 모듈을 만들고, 에지 디바이스에 배포하는 방법을 보여줍니다.
services: iot-edge
author: kgremban
ms.reviewer: kgremban
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f315e3987d46bc4a27b5ad5566b0424f410cd16a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485204"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-using-linux-containers"></a>자습서: Linux 컨테이너를 사용하여 Python IoT Edge 모듈 개발 및 배포

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Visual Studio Code를 사용하여 Python 코드를 개발하고 Azure IoT Edge를 실행하는 디바이스에 배포합니다.

비즈니스 논리를 직접 Azure IoT Edge 디바이스에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 빠른 시작에서 설정한 IoT Edge 디바이스에서 센서 데이터를 필터링하는 IoT Edge 모듈을 만들고 배포하는 과정을 안내합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Visual Studio Code를 사용하여 IoT Edge Python 모듈 만들기
> * Visual Studio Code 및 Docker를 사용하여 docker 이미지를 만들어 레지스트리에 게시합니다.
> * 모듈을 IoT Edge 디바이스에 배포합니다.
> * 생성된 데이터를 봅니다.

이 자습서에서 만드는 IoT Edge 모듈은 디바이스에서 생성한 온도 데이터를 필터링합니다. 온도가 지정된 임계값을 초과하는 경우에만 메시지 업스트림을 전송합니다. Edge에서 이 유형의 분석은 클라우드로 전송되고 저장되는 데이터 양을 줄이는 데 유용합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 **Visual Studio Code** 를 사용하여 **Python** 에서 모듈을 개발하고 IoT Edge 디바이스에 배포하는 방법을 보여줍니다.

IoT Edge는 Windows 컨테이너를 사용하는 Python 모듈을 지원하지 않습니다.

Linux 컨테이너를 사용하여 Python 모듈을 개발하고 배포하기 위한 옵션을 이해하려면 다음 표를 사용합니다.

| Python | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Linux AMD64에서 Python 모듈에 대한 VS Code 사용](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Linux ARM32에서 Python 모듈에 대한 VS Code 사용](./media/tutorial-c-module/green-check.png) |  |

이 자습서를 시작하기 전에 이전 자습서를 통해 Linux 컨테이너 개발을 위한 개발 환경을 설정해야 합니다. [Linux 컨테이너를 사용하여 IoT Edge 모듈을 개발](tutorial-develop-for-linux.md)합니다. 이 자습서를 완료하여 다음과 같은 필수 구성 요소를 갖추어야 합니다.

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Azure IoT Edge를 실행하는 디바이스. 빠른 시작을 사용하여 [Linux 디바이스](quickstart-linux.md) 또는 [Windows 디바이스](quickstart.md)를 설정할 수 있습니다.
* [Azure Container Registry](../container-registry/index.yml)와 같은 컨테이너 레지스트리
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 사용하여 구성된 [Visual Studio Code](https://code.visualstudio.com/)
* Linux 컨테이너를 실행하도록 구성된 [Docker CE](https://docs.docker.com/install/)

Python에서 IoT Edge 모듈을 개발하려면 다음 추가 필수 구성 요소를 개발 머신에 설치합니다.

* Visual Studio Code용 [Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python).
* [Python](https://www.python.org/downloads/).
* Python 패키지 설치를 위한 [Pip](https://pip.pypa.io/en/stable/installing/#installation)(일반적으로 Python 설치와 함께 포함됨)

>[!Note]
>`bin` 폴더는 플랫폼 경로에 있어야 합니다. 일반적으로 UNIX 및 macOS에서는 `~/.local/`이고, Windows에서는 `%APPDATA%\Python`입니다.

## <a name="create-a-module-project"></a>모듈 프로젝트 만들기

다음 단계에서는 Visual Studio Code 및 Azure IoT Tools를 사용하여 IoT Edge Python 모듈을 만듭니다.

### <a name="create-a-new-project"></a>새 프로젝트 만들기

사용자 고유의 코드를 사용하여 사용자 지정할 수 있는 Python 솔루션 템플릿을 만듭니다.

1. Visual Studio Code에서 **보기** > **명령 팔레트** 를 차례로 선택하여 VS Code 명령 팔레트를 엽니다.

2. 명령 팔레트에서 **Azure: 로그인** 명령을 입력하고 실행한 다음, 지침에 따라 Azure 계정에 로그인합니다. 이미 로그인한 경우 이 단계를 건너뛸 수 있습니다.

3. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 프롬프트에 따라 다음 정보를 제공하여 솔루션을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값 **EdgeSolution** 을 적용합니다. |
   | 모듈 템플릿 선택 | **Python 모듈** 을 선택합니다. |
   | 모듈 이름 제공 | 모듈 이름을 **PythonModule** 로 지정합니다. |
   | 모듈의 Docker 이미지 리포지토리 제공 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 마지막 단계에 제공한 이름으로 미리 채워져 있습니다. **localhost:5000** 을 Azure 컨테이너 레지스트리의 **로그인 서버** 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. <br><br>마지막 이미지 리포지토리는 \<registry name\>.azurecr.io/pythonmodule 형식입니다. |

   ![Docker 이미지 리포지토리 제공](./media/tutorial-python-module/repository.png)

### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

환경 파일은 컨테이너 리포지토리에 대한 자격 증명을 저장하고 IoT Edge 런타임과 이를 공유합니다. 이러한 자격 증명은 런타임에서 프라이빗 이미지를 IoT Edge 디바이스로 가져오기 위해 필요합니다.

IoT Edge 확장은 Azure에서 컨테이너 레지스트리 자격 증명을 끌어온 후 환경 파일에 채우려고 합니다. 사용자 자격 증명이 이미 포함되어 있는지 확인합니다. 그렇지 않은 경우 다음과 같이 지금 추가합니다.

1. VS Code 탐색기에서 **.env** 파일을 엽니다.
2. 필드를 Azure 컨테이너 레지스트리에서 복사한 **사용자 이름** 및 **암호** 값으로 업데이트합니다.
3. .env 파일을 저장합니다.

>[!NOTE]
>이 자습서에서는 개발 및 테스트 시나리오에 편리하게 사용할 수 있는 관리자 로그인 자격 증명을 Azure Container Registry에 사용합니다. 프로덕션 시나리오에 사용할 준비가 되면 서비스 주체 같은 최소 권한 인증 옵션을 사용하는 것이 좋습니다. 자세한 내용은 [컨테이너 레지스트리에 대한 액세스 관리](production-checklist.md#manage-access-to-your-container-registry)를 참조하세요.

### <a name="select-your-target-architecture"></a>대상 아키텍처 선택

현재 Visual Studio Code에서는 Linux AMD64 및 Linux ARM32v7 디바이스용 Python 모듈을 개발할 수 있습니다. 컨테이너는 아키텍처 유형별로 다르게 빌드되고 실행되므로 각 솔루션에서 대상으로 지정할 대상 아키텍처를 선택해야 합니다. 기본값은 Linux AMD64입니다.

1. 명령 팔레트를 열고 **Azure IoT Edge: 에지 솔루션용 기본 대상 플랫폼 설정** 을 검색하거나 창의 맨 아래에 있는 사이드바에서 바로 가기 아이콘을 선택합니다.

2. 명령 팔레트의 옵션 목록에서 대상 아키텍처를 선택합니다. 이 자습서에서는 Ubuntu 가상 머신을 IoT Edge 디바이스로 사용할 예정이므로 기본값인 **amd64** 를 그대로 둡니다.

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

각 템플릿에는 **SimulatedTemperatureSensor** 모듈에서 시뮬레이션된 센서 데이터를 가져오고 이를 IoT 허브에 라우팅하는 샘플 코드가 포함되어 있습니다. 이 섹션에서는 메시지를 보내기 전에 **PythonModule** 을 확장하여 메시지를 분석하는 코드를 추가합니다.

1. VS Code 탐색기에서 **modules** > **PythonModule** > **main.py** 를 차례로 엽니다.

2. **main.py** 파일의 위쪽에서 **json** 라이브러리를 가져옵니다.

    ```python
    import json
    ```

3. **TEMPERATURE_THRESHOLD**, **RECEIVED_MESSAGES** 및 **TWIN_CALLBACKS** 변수에 대한 전역 정의를 추가합니다. IoT 허브로 데이터를 보내려면 온도 임계값을 측정된 컴퓨터 온도에서 초과해야 하는 값으로 설정합니다.

    ```python
    # global counters
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    RECEIVED_MESSAGES = 0
    ```

4. **create_client** 함수를 다음 코드로 바꿉니다.

    ```python
    def create_client():
        client = IoTHubModuleClient.create_from_edge_environment()

        # Define function for handling received messages
        async def receive_message_handler(message):
            global RECEIVED_MESSAGES
            print("Message received")
            size = len(message.data)
            message_text = message.data.decode('utf-8')
            print("    Data: <<<{data}>>> & Size={size}".format(data=message.data, size=size))
            print("    Properties: {}".format(message.custom_properties))
            RECEIVED_MESSAGES += 1
            print("Total messages received: {}".format(RECEIVED_MESSAGES))

            if message.input_name == "input1":
                message_json = json.loads(message_text)
                if "machine" in message_json and "temperature" in message_json["machine"] and message_json["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
                    message.custom_properties["MessageType"] = "Alert"
                    print("ALERT: Machine temperature {temp} exceeds threshold {threshold}".format(
                        temp=message_json["machine"]["temperature"], threshold=TEMPERATURE_THRESHOLD
                    ))
                    await client.send_message_to_output(message, "output1")

        # Define function for handling received twin patches
        async def receive_twin_patch_handler(twin_patch):
            global TEMPERATURE_THRESHOLD
            global TWIN_CALLBACKS
            print("Twin Patch received")
            print("     {}".format(twin_patch))
            if "TemperatureThreshold" in twin_patch:
                TEMPERATURE_THRESHOLD = twin_patch["TemperatureThreshold"]
            TWIN_CALLBACKS += 1
            print("Total calls confirmed: {}".format(TWIN_CALLBACKS))

        try:
            # Set handler on the client
            client.on_message_received = receive_message_handler
            client.on_twin_desired_properties_patch_received = receive_twin_patch_handler
        except:
            # Cleanup if failure occurs
            client.shutdown()
            raise

        return client
    ```

7. main.py 파일을 저장합니다.

8. VS Code 탐색기에서 IoT Edge 솔루션 작업 영역에 있는 **deployment.template.json** 파일을 엽니다.

9. **PythonModule** 모듈 쌍을 배포 매니페스트에 추가합니다. **$edgeHub** 모듈 쌍 뒤에 있는 **moduleContent** 섹션의 아래쪽에 다음 JSON 내용을 삽입합니다.

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![배포 템플릿에 모듈 쌍 추가](./media/tutorial-python-module/module-twin.png)

10. deployment.template.json 파일을 저장합니다.

## <a name="build-and-push-your-module"></a>모듈 빌드 및 푸시

이전 섹션에서는 IoT Edge 솔루션을 만들었고, 보고된 머신 온도가 허용되는 범위 내에 있는 경우 메시지를 필터링하는 코드를 PythonModule에 추가했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시해야 합니다.

1. **보기** > **터미널** 을 차례로 선택하여 VS Code 통합 터미널을 엽니다.

2. 터미널에 다음 명령을 입력하여 Docker에 로그인합니다. Azure Container Registry의 사용자 이름, 암호 및 로그인 서버로 로그인합니다. Azure Portal에서 레지스트리의 **액세스 키** 섹션에서 이러한 값을 검색할 수 있습니다.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` 사용을 권장하는 보안 경고가 표시될 수 있습니다. 이 모범 사례는 프로덕션 시나리오에 권장되지만 이 자습서에는 포함되지 않습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 참조를 참조하세요.

3. VS Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시** 를 선택합니다.

   빌드 및 푸시 명령은 세 가지 작업을 시작합니다. 먼저, 배포 템플릿 및 기타 솔루션 파일의 정보로 작성된 전체 배포 매니페스트를 포함하는 **config** 라는 새 폴더를 솔루션에 만듭니다. 둘째, `docker build`를 실행하여 대상 아키텍처의 적절한 dockerfile을 기준으로 컨테이너 이미지를 빌드합니다. 그런 다음, `docker push`를 실행하여 컨테이너 레지스트리에 이미지 리포지토리를 푸시합니다.

   이 프로세스는 처음에는 몇 분 정도 걸릴 수 있지만 다음번에 명령을 실행할 때는 더 빨라집니다.

## <a name="deploy-modules-to-device"></a>디바이스에 모듈 배포

Visual Studio Code 탐색기 및 Azure IoT Tools 확장을 사용하여 IoT Edge 디바이스에 모듈 프로젝트를 배포합니다. 사용자의 시나리오를 위한 배포 매니페스트인 **deployment.amd64.json** 파일이 config 폴더에 이미 준비되어 있습니다. 이제 배포를 받을 디바이스를 선택하기만 하면 됩니다.

IoT Edge 디바이스가 작동되고 실행 중인지 확인합니다.

1. Visual Studio Code 탐색기의 **Azure IoT Hub** 섹션에서 **디바이스** 를 확장하여 IoT 디바이스 목록을 표시합니다.

2. IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭한 다음, **단일 디바이스용 배포 만들기** 를 선택합니다.

3. **config** 폴더에서 **deployment.amd64.json** 파일을 선택한 다음, **에지 배포 매니페스트 선택** 을 클릭합니다. deployment.template.json 파일을 사용하지 마세요.

4. 배포되어 실행 중인 모듈의 목록을 보려면 디바이스 아래에서 **모듈** 을 확장합니다. 새로고침 단추를 클릭합니다. **SimulatedTemperatureSensor** 모듈과 **$edgeAgent** 및 **$edgeHub** 와 함께 실행되는 새 **PythonModule** 이 표시됩니다.

    두 모듈이 모두 시작하는 데 몇 분 정도 걸릴 수 있습니다. IoT Edge 런타임은 새 배포 매니페스트를 받고, 컨테이너 런타임에서 모듈 이미지를 끌어온 후 각 새 모듈을 시작해야 합니다.

## <a name="view-the-generated-data"></a>생성된 데이터 보기

IoT Edge 디바이스에 배포 매니페스트를 적용한 후에는 디바이스의 IoT Edge 런타임이 새 배포 정보를 수집하고 그에 따라 실행하기 시작합니다. 배포 매니페스트에 포함되지 않는 디바이스에서 실행되는 모든 모듈은 중지됩니다. 디바이스에서 누락된 모든 모듈이 시작됩니다.

Visual Studio Code Explorer의 **Azure IoT Hub 디바이스** 섹션을 통해 IoT Edge 디바이스 상태를 확인할 수 있습니다. 배포되어 실행 중인 모듈의 목록을 보려면 디바이스 상세 정보를 확장합니다.

1. Visual Studio Code 탐색기에서 IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

2. IoT Hub에 메시지가 들어오는 것을 확인합니다. 메시지가 도착하는 데 시간이 걸릴 수 있습니다. IoT Edge 디바이스는 새 배포를 수신하고 모든 모듈을 시작해야 합니다. 그런 다음, PythonModule 코드를 변경할 경우 머신 온도가 25도에 도달할 때까지 기다렸다가 메시지를 보냅니다. 또한 온도 임계값에 도달하는 모든 메시지에 메시지 유형 **경고** 를 추가합니다.

## <a name="edit-the-module-twin"></a>모듈 쌍 편집

여기서는 배포 매니페스트에서 PythonModule 모듈 쌍을 사용하여 온도 임계값을 25도로 설정했습니다. 모듈 쌍을 사용하면 모듈 코드를 업데이트하지 않고도 기능을 변경할 수 있습니다.

1. Visual Studio Code에서 IoT Edge 디바이스 아래의 세부 정보를 확장하여 실행 중인 모듈을 확인합니다.

2. **PythonModule** 을 마우스 오른쪽 단추로 클릭하고 **모듈 쌍 편집** 을 선택합니다.

3. Desired 속성에서 **TemperatureThreshold** 를 찾습니다. 새 온도를 최근에 보고된 온도보다 5~10도 더 높게 변경합니다.

4. 모듈 쌍 파일을 저장합니다.

5. 모듈 쌍 편집 창의 아무 곳이나 마우스 오른쪽 단추로 클릭하고 **모듈 쌍 업데이트** 를 선택합니다.

6. 들어오는 디바이스-클라우드 메시지를 모니터링합니다. 새 온도 임계값에 도달할 때까지 메시지가 중지되어야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 디바이스와 동일한 IoT Edge 디바이스를 계속 사용해도 됩니다.

그렇지 않은 경우 요금이 발생하지 않도록 이 문서에서 사용한 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 디바이스에서 생성된 원시 데이터를 필터링하는 코드가 포함된 IoT Edge 모듈을 만들었습니다.

다음 자습서를 계속 진행하면서 Azure Cloud Services를 배포하여 에지에서 데이터를 처리 및 분석하는 데 Azure IoT Edge를 어떻게 활용할 수 있는지 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)