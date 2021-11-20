---
title: 웹앱에서 IoT Hub 데이터의 실시간 데이터 시각화
description: 웹 애플리케이션을 사용하여 센서에서 수집하여 IoT Hub로 보낸 온도 및 습도 데이터를 시각화합니다.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/18/2021
ms.author: lizross
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: cfa345e56744bf2c26f18d98dac3101db8021d2b
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132864477"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>웹 애플리케이션에서 Azure IoT Hub에서 실시간 센서 데이터 시각화

![엔드투엔드 다이어그램](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

이 문서에서는 로컬 컴퓨터에서 실행되는 node.js 웹앱을 사용하여 IoT 허브에서 수신하는 실시간 센서 데이터를 시각화하는 방법을 알아봅니다. 웹앱을 로컬로 실행한 후에는 필요에 따라 Azure App Service에서 웹앱을 호스트하는 단계를 수행할 수 있습니다. Power BI를 사용하여 IoT Hub의 데이터를 시각화하려면 [Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 디바이스 자습서 중 하나를 완료합니다. 예를 들어 [node.js를 사용하는 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)로 이동하거나 [원격 분석 전송](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) 빠른 시작 중 하나로 이동할 수 있습니다. 이러한 문서는 다음 요구 사항을 다룹니다.

  * 활성 Azure 구독
  * 구독 중인 IoT Hub
  * 메시지를 IoT Hub로 보내는 클라이언트 애플리케이션

* [Node.js](https://nodejs.org) 버전 10.6 이상 노드 버전을 확인하려면 `node --version`을 실행합니다.

* [Git 다운로드](https://www.git-scm.com/downloads)

* 이 문서의 단계에서는 Windows 개발 머신을 가정합니다. 그러나 선호하는 셸을 사용하여 Linux 시스템에서 이러한 단계를 쉽게 수행할 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

[소비자 그룹](../event-hubs/event-hubs-features.md#event-consumers)은 앱과 Azure 서비스가 동일한 이벤트 허브 엔드포인트의 데이터를 독립적으로 사용할 수 있는 이벤트 스트림에 대한 독립적인 보기를 제공합니다. 이 섹션에서는 웹앱에서 데이터를 읽는 데 사용하는 IoT Hub의 기본 제공 엔드포인트에 소비자 그룹을 추가합니다.

다음 명령을 실행하여 IoT Hub의 기본 제공 엔드포인트에 소비자 그룹을 추가합니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

이 자습서의 뒷부분에서 필요하므로 선택한 이름을 적어 둡니다.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT Hub의 서비스 연결 문자열 가져오기

IoT Hub는 몇 가지 기본 액세스 정책을 사용하여 생성됩니다. 이러한 정책 중 하나는 서비스에서 IoT Hub의 엔드포인트를 읽고 쓸 수 있는 충분한 권한을 제공하는 **서비스** 정책입니다. 다음 명령을 실행하여 서비스 정책을 준수하는 IoT Hub에 대한 연결 문자열을 가져옵니다.

```azurecli-interactive
az iot hub connection-string show --hub-name YourIotHub --policy-name service
```

이 연결 문자열은 다음과 유사합니다.

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

이 자습서의 뒷부분에서 필요하므로 서비스 연결 문자열을 적어 둡니다.

## <a name="download-the-web-app-from-github"></a>GitHub에서 웹앱 다운로드

명령 창을 열고 다음 명령을 입력하여 GitHub에서 샘플을 다운로드하고 샘플 디렉터리로 변경합니다.

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>웹앱 코드 검사

web-apps-node-iot-hub-data-visualization 디렉터리에서 즐겨 사용하는 편집기를 사용하여 웹앱을 엽니다. 다음은 VS Code에 표시되는 파일 구조입니다.

![웹앱 파일 구조](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

잠시 시간을 내서 다음 파일을 살펴보겠습니다.

* **Server.js** 는 웹 소켓과 이벤트 허브 래퍼 클래스를 초기화하는 서비스 쪽 스크립트입니다. 클래스가 들어오는 메시지를 웹 소켓에 브로드캐스트하는 데 사용하는 이벤트 허브 래퍼 클래스에 대한 콜백을 제공합니다.

* **Event-hub-reader.js** 는 지정된 연결 문자열 및 소비자 그룹을 사용하여 IoT Hub의 기본 제공 엔드포인트에 연결하는 서비스 쪽 스크립트입니다. 들어오는 메시지에 대한 메타데이터에서 DeviceId 및 EnqueuedTimeUtc를 추출한 다음, server.js에서 등록한 콜백 메서드를 사용하여 메시지를 릴레이합니다.

* **Chart-device-data.js** 는 웹 소켓에서 수신 대기하고, 각 DeviceId를 추적하고, 각 디바이스에 대해 마지막 50개의 수신 데이터 요소를 저장하는 클라이언트 쪽 스크립트입니다. 그런 다음, 선택한 디바이스 데이터를 차트 개체에 바인딩합니다.

* **Index.html** 은 웹 페이지에 대한 UI 레이아웃을 처리하고 클라이언트 쪽 논리에 필요한 스크립트를 참조합니다.

## <a name="configure-environment-variables-for-the-web-app"></a>웹앱에 대한 환경 변수 구성

IoT Hub에서 데이터를 읽으려면 웹앱에는 IoT Hub의 연결 문자열 및 읽어야 하는 소비자 그룹의 이름이 필요합니다. server.js의 다음 줄에서 프로세스 환경의 문자열을 가져옵니다.

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

명령 창에서 다음 명령을 사용하여 환경 변수를 설정합니다. 자리 표시자 값을 IoT Hub의 서비스 연결 문자열 및 이전에 만든 소비자 그룹의 이름으로 바꿉니다. 문자열을 따옴표로 묶지는 마세요.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>웹앱 실행

1. 디바이스가 실행 중이고 데이터를 보내고 있는지 확인합니다.

2. 명령 창에서 다음 줄을 실행하여 참조된 패키지를 다운로드한 후 설치하고 웹 사이트를 시작합니다.

   ```cmd
   npm install
   npm start
   ```

3. 웹앱이 IoT Hub에 성공적으로 연결되고 포트 3000에서 수신 대기 중임을 나타내는 출력이 콘솔에 표시됩니다.

   ![콘솔에서 웹앱이 시작됨](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>웹 페이지를 열어 IoT Hub의 데이터 보기

`http://localhost:3000`으로 브라우저를 엽니다.

**디바이스 선택** 목록에서 디바이스를 선택하여 디바이스에서 IoT Hub로 보낸 최근 50개의 온도 및 습도 데이터 요소의 실행 플롯을 볼 수 있습니다.

![실시간 온도 및 습도를 보여 주는 웹앱 페이지](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

또한 콘솔에 웹앱이 브라우저 클라이언트에 브로드캐스트하는 메시지를 표시하는 출력이 표시됩니다.  

![콘솔의 웹앱 브로드캐스트 출력](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>App Service에서 웹앱 호스트

[Azure App Service의 웹앱 기능](../app-service/overview.md)은 웹 애플리케이션을 호스트하기 위한 PAAS(Platform as a Service)를 제공합니다. Azure App Service에서 호스트되는 웹 애플리케이션은 추가 보안, 부하 분산 및 확장성과 같은 강력한 Azure 기능과 연속 배포, 패키지 관리 등과 같은 Azure 및 파트너 DevOps 솔루션의 이점을 누릴 수 있습니다. Azure App Service는 널리 사용되는 여러 언어로 개발되고 Windows 또는 Linux 인프라에 배포된 웹 애플리케이션을 지원합니다.

이 섹션에서는 App Service에서 웹앱을 프로비저닝하고 Azure CLI 명령을 사용하여 코드를 배포합니다. [az webapp](/cli/azure/webapp) 설명서에서 사용되는 명령에 대한 세부 정보를 찾을 수 있습니다. 시작하기 전에 [IoT Hub에 소비자 그룹을 추가하고, IoT Hub에](#add-a-consumer-group-to-your-iot-hub) [대한 서비스 연결 문자열을 얻고,](#get-a-service-connection-string-for-your-iot-hub) [GitHub 에서 웹앱을 다운로드하는](#download-the-web-app-from-github)단계를 완료했는지 확인합니다.

1. [App Service 플랜](../app-service/overview-hosting-plans.md)은 App Service에서 호스트된 앱에서 실행할 컴퓨팅 리소스 세트를 정의합니다. 이 자습서에서는 개발자/무료 계층을 사용하여 웹앱을 호스트합니다. 무료 계층을 사용하여 웹앱은 다른 고객의 앱을 포함하여 다른 App Service 앱과 공유된 Windows 리소스에서 실행됩니다. 또한 Azure는 Linux 컴퓨팅 리소스에 웹앱을 배포하기 위한 App Service 플랜을 제공합니다. 사용하려는 App Service 플랜이 이미 있는 경우 이 단계를 건너뛸 수 있습니다.

   Windows 무료 계층을 사용하여 App Service 플랜을 만들려면 다음 명령을 실행합니다. IoT Hub가 있는 동일한 리소스 그룹을 사용합니다. 서비스 플랜 이름에는 대/소문자, 숫자 및 하이픈을 포함할 수 있습니다.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. 이제 App Service 플랜에서 웹앱을 프로비저닝합니다. `--deployment-local-git` 매개 변수를 사용하면 로컬 머신의 Git 리포지토리에서 웹앱 코드를 업로드하고 배포할 수 있습니다. 웹앱 이름은 전역적으로 고유해야 하며 대/소문자, 숫자 및 하이픈을 포함할 수 있습니다. 사용 중인 Node.js 런타임의 버전에 따라 `--runtime` 매개 변수에 대해 Node 버전 10.6 이상을 지정해야 합니다. `az webapp list-runtimes` 명령을 사용하여 지원되는 런타임 목록을 가져올 수 있습니다.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. 이제 IoT Hub 연결 문자열 및 이벤트 허브 소비자 그룹을 지정하는 환경 변수에 대한 애플리케이션 설정을 추가합니다. 개별 설정은 `-settings` 매개 변수에서 공백으로 구분됩니다. IoT Hub에 대한 서비스 연결 문자열과 이 자습서의 앞부분에서 만든 소비자 그룹을 사용합니다. 값을 따옴표로 묶지는 마세요.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. 웹앱에 웹 소켓 프로토콜을 사용하도록 설정하고 HTTPS 요청만 받도록 웹앱을 설정합니다(HTTP 요청은 HTTPS로 리디렉션됨).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. App Service에 코드를 배포하려면 [사용자 수준 배포 자격 증명](../app-service/deploy-configure-credentials.md)을 사용합니다. 사용자 수준 배포 자격 증명은 Azure 자격 증명과는 다르며 웹앱에 대한 Git 로컬 및 FTP 배포에 사용됩니다. 설정되면 Azure 계정의 모든 구독에 있는 모든 App Service 앱에서 유효합니다. 사용자 수준 배포 자격 증명을 이전에 설정한 경우 해당 자격 증명을 사용할 수 있습니다.

   이전에 사용자 수준 배포 자격 증명을 설정하지 않았거나 암호를 기억할 수 없는 경우 다음 명령을 실행합니다. 배포 사용자 이름은 Azure 내에서 고유해야 하며 로컬 Git 푸시에 대해 ‘\@’ 기호를 포함하면 안 됩니다. 암호를 묻는 메시지가 표시되면 암호를 입력하고 새 암호를 확인합니다. 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. 코드를 App Service까지 푸시하는 데 사용할 Git URL을 가져옵니다.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. App Service에서 웹앱에 대한 Git 리포지토리를 참조하는 복제본에 원격 기능을 추가합니다. \<Git clone URL\>의 경우 이전 단계에서 반환된 URL을 사용합니다. 명령 창에서 다음 명령을 실행합니다. *web-apps-code-iot-hub-data-visualization* 샘플 디렉터리에 있는지 확인한 후 명령 창에서 다음 명령을 실행합니다.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. App Service에 코드를 배포하려면 명령 창에 다음 명령을 입력합니다. 샘플 디렉터리 *web-apps-code-iot-hub-data-visualization* 에 있는지 확인합니다. 자격 증명을 묻는 메시지가 표시되면 5단계에서 만든 사용자 수준 배포 자격 증명을 입력합니다. App Service 원격의 마스터 분기에 푸시합니다.

    ```cmd
    git push webapp master:master
    ```

9. 배포 진행률이 명령 창에서 업데이트됩니다. 성공적으로 배포되면 다음 출력과 유사한 줄로 끝납니다.

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. 다음 명령을 실행하여 웹앱의 상태를 쿼리하고 실행 중인지 확인합니다.

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. 브라우저에서 `https://<your web app name>.azurewebsites.net` 으로 이동합니다. 웹앱을 로컬로 실행할 때 표시되는 것과 비슷한 웹 페이지가 나타납니다. 디바이스가 실행 중이고 데이터를 전송하는 경우 디바이스에서 보낸 50개의 가장 최근 온도 및 습도 판독값의 실행 플롯이 표시됩니다.

## <a name="troubleshooting"></a>문제 해결

이 샘플에서 이슈가 발생하면 다음 섹션의 단계를 수행합니다. 문제가 계속되면 이 항목 맨 아래에서 의견을 보내 주시기 바랍니다.

### <a name="client-issues"></a>클라이언트 이슈

* 목록에 디바이스가 표시되지 않거나 그래프가 그려지지 않는 경우 디바이스에서 디바이스 코드가 실행되고 있는지 확인합니다.

* 대부분의 브라우저에서 F12 키를 누르면 열리는 개발자 도구를 열고 콘솔을 찾습니다. 출력된 경고 또는 오류를 확인합니다.

* /js/chat-device-data.js에서 클라이언트 쪽 스크립트를 디버그할 수 있습니다.

### <a name="local-website-issues"></a>로컬 웹 사이트 이슈

* 콘솔 출력을 위해 노드를 시작한 창에서 출력을 확인합니다.

* 서버 코드, 특히 server.js 및 /scripts/event-hub-reader.js를 디버그합니다.

### <a name="azure-app-service-issues"></a>Azure App Service 이슈

* Azure Portal에서 웹앱으로 이동합니다. 왼쪽 창의 **모니터링** 에서 **App Service 로그** 를 선택합니다. **애플리케이션 로깅(파일 시스템)** 을 켜짐으로 설정하고 **수준** 을 오류로 설정한 후 **저장** 을 선택합니다. 그런 다음, **모니터링** 에서 **로그 스트림** 을 엽니다.

* Azure Portal의 웹앱의 **개발 도구** 에서 **콘솔** 을 선택하고 `node -v` 및 `npm -v`를 사용하여 node 및 npm 버전의 유효성을 검사합니다.

* 패키지를 찾을 수 없다는 오류가 표시되면 진행하는 단계의 순서가 잘못된 것일 수 있습니다. 사이트가 배포될 때(`git push`) App Service는 구성된 현재 노드 버전에 따라 실행되는 `npm install`을 실행합니다. 나중에 구성에서 변경된 경우에는 코드에 대해 의미 없는 변경을 수행하고 다시 푸시해야 합니다.

## <a name="next-steps"></a>다음 단계

웹앱을 사용하여 IoT Hub에서 실시간 센서 데이터를 시각화했습니다.

Azure IoT Hub에서 데이터를 시각화하는 다른 방법은 [Power BI를 사용하여 IoT Hub에서 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-power-bi.md)를 참조하세요.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]