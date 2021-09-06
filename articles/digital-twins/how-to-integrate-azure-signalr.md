---
title: Azure SignalR 서비스와 통합
titleSuffix: Azure Digital Twins
description: Azure SignalR을 사용하여 Azure Digital Twins 원격 분석을 클라이언트로 스트리밍하는 방법을 알아봅니다.
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fcf3167e4f407a64d474275e495b73f511feabdf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536814"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Azure Digital Twins를 Azure SignalR Service와 통합

이 문서에서는 Azure Digital Twins를 [Azure SignalR Service](../azure-signalr/signalr-overview.md)와 통합하는 방법을 알아봅니다.

이 문서에서 설명하는 솔루션을 사용하면 단일 웹 페이지 또는 모바일 애플리케이션과 같은 연결된 클라이언트에 디지털 트윈 원격 측정 데이터를 푸시할 수 있습니다. 따라서 서버를 폴링하거나 업데이트를 위해 새로운 HTTP 요청을 제출할 필요 없이 IoT 디바이스의 실시간 메트릭과 상태로 클라이언트가 업데이트됩니다.

## <a name="prerequisites"></a>필수 조건

계속하기 전 사전 요구 사항은 다음과 같습니다.

* 이 문서에서 솔루션을 Azure SignalR Service와 통합하기 전에 Azure Digital Twins [엔드투엔드 솔루션 연결](tutorial-end-to-end.md)을 완료해야 합니다. 이 방법 문서가 이 자습서를 바탕으로 하기 때문입니다. 이 자습서는 가상 IoT 디바이스와 함께 작동하여 디지털 트윈 업데이트를 트리거하는 Azure Digital Twins 인스턴스를 설정하는 과정을 안내합니다. 본 방법 문서에서는 Azure SignalR Service를 사용하여 이러한 업데이트를 샘플 웹앱에 연결합니다.

* 자습서의 다음 값이 필요합니다.
  - Event Grid 토픽
  - Resource group
  - App Service/함수 앱 이름
    
* 컴퓨터에 [Node.js](https://nodejs.org/)가 설치되어 있어야 합니다.

또한 Azure 계정으로 [Azure Portal](https://portal.azure.com/)에 로그인해야 합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

아래 경로를 통해 Azure SignalR Service를 Azure Digital Twins에 연결 합니다. 다이어그램의 A, B 및 C 섹션은 [엔드투엔드 자습서 사전 요구 사항](tutorial-end-to-end.md)에서 발췌한 것입니다. 이 방법 문서에서는 SignalR 및 클라이언트 앱과 통신하는 두 개의 새로운 Azure 함수를 포함하는 기존 아키텍처에서 섹션 D를 빌드합니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Azure Digital Twins에서 들어오고 나가는 데이터를 보여주는 엔드투엔드 시나리오의 Azure 서비스 다이어그램." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>샘플 애플리케이션 다운로드

먼저 필요한 샘플 앱을 다운로드합니다. 다음 샘플이 모두 필요합니다.
* [Azure Digital Twins 엔드투엔드 샘플](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): 이 샘플에는 Azure Digital Twins 인스턴스 주위로 데이터를 이동하기 위한 두 개의 Azure 함수를 보유하는 *AdtSampleApp* 이 포함되어 있습니다([엔드투엔드 솔루션 연결](tutorial-end-to-end.md)에서 이 시나리오에 대해 자세히 알아볼 수 있음). 또한 IoT 디바이스를 시뮬레이션하여 매초마다 새 온도 값을 생성하는 *DeviceSimulator* 샘플 애플리케이션이 포함되어 있습니다.
    - [사전 요구 사항](#prerequisites)에서 자습서의 일부로 샘플을 아직 다운로드하지 않은 경우 [샘플로 이동](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)하고 제목 아래에 있는 *코드 찾아보기* 단추를 선택합니다. 그러면 *코드* 단추와 *ZIP 다운로드* 를 선택하여 .zip으로 다운로드할 수 있는 샘플용 GitHub 리포지토리로 이동합니다.

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="GitHub에 대한 digital-twins-samples 리포지토리의 스크린샷과 이를 zip으로 다운로드하기 위한 단계." lightbox="media/includes/download-repo-zip.png":::

    그러면 샘플 리포지토리의 복사본이 **digital-twins-samples-master.zip** 으로 컴퓨터에 다운로드됩니다. 폴더의 압축을 풉니다.
* [SignalR 통합 웹앱 샘플](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): Azure SignalR Service Azure Digital Twins 원격 분석 데이터를 사용하는 샘플 React 웹앱입니다.
    -  샘플 링크로 이동하고 동일한 다운로드 프로세스를 사용하여 _**digitaltwins-signalr-webapp-sample-main.zip**_ 과 같은 샘플의 복사본을 컴퓨터에 다운로드합니다. 폴더의 압축을 풉니다.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

다음 섹션에서 다시 사용할 것이므로 브라우저 창에서 Azure Portal을 열어 둡니다.

## <a name="publish-and-configure-the-azure-functions-app"></a>Azure Functions 앱 게시 및 구성

이 섹션에서는 2개의 Azure 함수를 설정합니다.
* **negotiate** - HTTP 트리거 함수입니다. 이 함수는 *SignalRConnectionInfo* 입력 바인딩을 사용하여 유효한 연결 정보를 생성하고 반환합니다.
* **broadcast** - [Event Grid](../event-grid/overview.md) 트리거 함수입니다. 이벤트 그리드를 통해 Azure Digital Twins 원격 분석 데이터를 수신하고 이전 단계에서 만든 *SignalR* 인스턴스의 출력 바인딩을 사용하여 연결된 모든 클라이언트 애플리케이션에 메시지를 브로드캐스트합니다.

Visual Studio(또는 선택한 다른 코드 편집기)를 시작하고 *digital-twins-samples-master > ADTSampleApp* 폴더에서 코드 솔루션을 엽니다. 다음 단계에 따라 함수를 만듭니다.

1. *SampleFunctionsApp* 프로젝트에서 **SignalRFunctions.cs** 라는 새 C# 클래스를 만듭니다. 이 작업을 수행하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)을 참조하세요.

1. 클래스 파일의 내용을 다음 코드로 바꿉니다.
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Visual Studio의 *패키지 관리자 콘솔* 창 또는 컴퓨터의 명령 창에서 *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* 폴더로 이동하고 다음 명령을 실행하여 `SignalRService` NuGet 패키지를 프로젝트에 설치합니다.
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    이렇게 하면 클래스의 모든 종속성 문제가 해결됩니다.

1. 함수를 Azure에 게시합니다. 엔드투엔드 자습서 [사전 요구 사항](#prerequisites)에서 사용한 것과 동일한 App Service/함수 앱에 함수를 게시하거나 새로 만들 수 있습니다. 그러나 중복을 최소화하려면 동일한 함수를 사용하는 것이 좋습니다. Visual Studio를 사용하여 함수를 게시하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

### <a name="configure-the-function"></a>함수 구성

다음으로, Azure SignalR 인스턴스와 통신하도록 함수를 구성합니다. 먼저 SignalR 인스턴스의 **연결 문자열** 을 수집한 다음, 함수 앱의 설정에 이를 추가합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동하고 포털 위쪽의 검색 창에서 SignalR 인스턴스의 이름을 검색합니다. 인스턴스를 선택하여 엽니다.
1. SignalR Service 인스턴스의 연결 문자열을 보려면 인스턴스 메뉴에서 **키** 를 선택합니다.
1. *복사* 아이콘을 선택하여 기본 연결 문자열을 복사합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="SignalR 인스턴스에 대한 키 페이지를 표시하는 Azure Portal의 스크린샷. 연결 문자열을 복사하고 있습니다." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. 마지막으로 다음 Azure CLI 명령을 사용하여 함수의 앱 설정에 Azure SignalR **연결 문자열** 을 추가합니다. 또한 자리 표시자를 [자습서 사전 요구 사항](how-to-integrate-azure-signalr.md#prerequisites)의 리소스 그룹 및 App Service/함수 앱 이름으로 바꿉니다. 이 명령은 [Azure Cloud Shell](https://shell.azure.com)에서 실행하거나 [Azure CLI가 머신에 설치](/cli/azure/install-azure-cli)된 경우 로컬로 실행할 수 있습니다.
 
    ```azurecli-interactive
    az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    이 명령의 출력은 Azure 함수에 대해 설정된 모든 앱 설정을 인쇄합니다. 목록 하단에서 `AzureSignalRConnectionString`을 찾아 추가되었는지 확인합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="'AzureSignalRConnectionString'이라는 목록 항목을 표시하는 명령 창의 출력 스크린샷.":::

## <a name="connect-the-function-to-event-grid"></a>Event Grid에 함수 연결

다음으로, [자습서 사전 요구 사항](how-to-integrate-azure-signalr.md#prerequisites)에서 만든 **Event Grid 토픽** 에 *broadcast* Azure 함수를 구독합니다. 이렇게 하면 원격 분석 데이터가 thermostat67 트윈에서 Event Grid 토픽 및 함수로 흐를 수 있습니다. 여기에서 함수는 모든 클라이언트에 데이터를 브로드캐스트할 수 있습니다.

이를 위해 Event Grid 토픽에서 *broadcast* Azure 함수에 대한 **이벤트 구독** 을 엔드포인트로 만듭니다.

[Azure Portal](https://portal.azure.com/)의 위쪽 검색 창에서 해당 이름을 검색하여 이벤트 그리드 토픽으로 이동합니다. *+ 이벤트 구독* 을 선택합니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal에서 이벤트 구독을 만드는 방법에 대한 스크린샷.":::

*이벤트 구독 만들기* 페이지에서 다음과 같이 필드를 입력합니다(기본적으로 채워진 필드는 언급되지 않음).
* *이벤트 구독 정보* > **이름**: 이벤트 구독 이름을 지정합니다.
* *엔드포인트 정보* > **엔드포인트 유형**: 메뉴 옵션에서 *Azure 함수* 를 선택합니다.
* *엔드포인트 세부 정보* > **엔드포인트**: *엔드포인트 선택* 링크를 선택합니다. 그러면 *Azure 함수 선택* 창이 열립니다.
    - **구독**, **리소스 그룹**, **함수 앱** 및 **함수**(*broadcast*)를 입력합니다. 구독을 선택하면 이 중 일부가 자동으로 채워질 수 있습니다.
    - **선택 확인** 을 선택합니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal에서 이벤트 구독을 만들기 위한 양식의 스크린샷.":::

*이벤트 구독 만들기* 페이지에서 **만들기** 를 선택합니다.

이 시점에서 *Event Grid 토픽* 페이지에 2개의 이벤트 구독이 표시되어야 합니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Event Grid 토픽 페이지에 있는 두 이벤트 구독을 보여주는 Azure Portal의 스크린샷." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>웹앱 구성 및 실행

이 섹션에서는 실제 결과를 볼 수 있습니다. 먼저 설정한 Azure SignalR 흐름에 연결하도록 **샘플 클라이언트 웹앱** 을 구성합니다. 다음으로, Azure Digital Twins 인스턴스를 통해 원격 분석 데이터를 보내는 **시뮬레이션된 디바이스 샘플 앱** 을 시작합니다. 그런 다음, 샘플 웹앱을 보고 샘플 웹앱을 실시간으로 업데이트하는 시뮬레이션된 디바이스 데이터를 확인합니다.

### <a name="configure-the-sample-client-web-app"></a>샘플 클라이언트 웹앱 구성

다음으로, 샘플 클라이언트 웹앱을 구성합니다. 먼저 *negotiate* 함수의 **HTTP 엔드포인트 URL** 을 수집한 다음, 이를 사용하여 컴퓨터에서 앱 코드를 구성합니다.

1. Azure Portal의 [함수 앱](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) 페이지로 이동하고 목록에서 함수 앱을 선택합니다. 앱 메뉴에서 *함수* 를 선택하고 *negotiate* 함수를 선택합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="메뉴에 'Functions'가 강조 표시되고 함수 목록에 'negotiate'가 강조 표시된 Azure Portal 함수 앱의 스크린샷.":::

1. *함수 URL 가져오기* 를 선택하고 **_/api_ 까지 값을 복사합니다(마지막 _/negotiate?_ 포함 안 함)** . 이 값은 다음 단계에서 사용합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="'함수 URL 가져오기' 단추 및 함수 URL이 강조 표시된 'negotiate' 함수를 보여주는 Azure Portal의 스크린샷.":::

1. Visual Studio 또는 원하는 코드 편집기를 사용하여 [샘플 애플리케이션 다운로드](#download-the-sample-applications) 섹션에서 다운로드한 압축을 푼 _**digitaltwins-signalr-webapp-main**_ 폴더를 엽니다.

1. *src/App.js* 파일을 열고 `HubConnectionBuilder`의 함수 URL을 이전 단계에서 저장한 **negotiate** 함수의 HTTP 엔드포인트 URL로 바꿉니다.

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function-URL>')
            .build();
    ```
1. Visual Studio의 *개발자 명령 프롬프트* 또는 컴퓨터의 명령 창에서 *digitaltwins-signalr-webapp-sample-main\src* 폴더로 이동합니다. 다음 명령을 실행하여 종속 노드 패키지를 설치합니다.

    ```cmd
    npm install
    ```

다음으로, Azure Portal의 함수 앱에서 권한을 설정합니다.
1. Azure Portal의 [함수 앱](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) 페이지에서 함수 앱 인스턴스를 선택합니다.

1. 인스턴스 메뉴에서 아래로 스크롤하고 *CORS* 를 선택합니다. CORS 페이지에서 `http://localhost:3000`을 빈 상자에 입력하여 허용된 원본으로 추가합니다. *Access-Control-Allow-Credentials 사용* 확인란을 선택하고 *저장* 을 선택합니다.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Azure Function의 CORS 설정을 보여주는 Azure Portal의 스크린샷.":::

### <a name="run-the-device-simulator"></a>디바이스 시뮬레이터 실행

엔드투엔드 자습서 사전 요구 사항 중 IoT Hub를 통해 Azure Digital Twins 인스턴스로 데이터를 보내도록 [디바이스 시뮬레이터를 구성](tutorial-end-to-end.md#configure-and-run-the-simulation)했습니다.

이제 *digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln* 에 있는 시뮬레이터 프로젝트를 시작하기만 하면 됩니다. Visual Studio를 사용하는 경우 프로젝트를 열고 도구 모음의 다음 단추로 이를 실행할 수 있습니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="DeviceSimulator 앱 프로젝트가 열리는 Visual Studio 시작 단추의 스크린샷":::

콘솔 창이 열리고 시뮬레이션된 온도 원격 분석 메시지가 표시됩니다. 이 메시지는 Azure Digital Twins 인스턴스를 통해 전송되며, 여기서 Azure Functions 및 SignalR에서 선택됩니다.

이 콘솔에서 다른 작업을 수행할 필요는 없지만 다음 단계를 완료하는 동안 콘솔을 실행 상태로 둡니다.

### <a name="see-the-results"></a>결과 보기

실제 결과를 보려면 **SignalR 통합 웹앱 샘플** 을 시작합니다. 다음 명령을 실행하여 *digitaltwins-signalr-webapp-sample-main\src* 위치의 모든 콘솔 창에서 이를 수행할 수 있습니다.

```cmd
npm start
```

그러면 시각적 온도 계기를 표시하는 샘플 앱이 실행되는 브라우저 창이 열립니다. 앱이 실행되면 Azure Digital Twins를 통해 전파되는 디바이스 시뮬레이터에서 실시간으로 웹앱에 반영되는 온도 원격 측정 값을 확인해야 합니다.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="시각적 온도 계기를 보여주는 샘플 클라이언트 웹앱의 스크린샷. 반영된 온도는 67.52입니다.":::

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스가 더 이상 필요하지 않으면 다음 단계에 따라 삭제합니다. 

Azure Cloud Shell 또는 로컬 Azure CLI를 사용하면 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 명령으로 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 리소스 그룹을 제거하면 다음도 제거됩니다.
* Azure Digital Twins 인스턴스(엔드투엔드 자습서에서)
* IoT 허브 및 허브 디바이스 등록(엔드투엔드 자습서에서)
* Event Grid 토픽 및 관련 구독
* 세 가지 함수 및 연결된 리소스(예: 스토리지)를 모두 포함하는 Azure Functions 앱
* Azure SignalR 인스턴스

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

마지막으로 로컬 컴퓨터에 다운로드한 프로젝트 샘플 폴더(*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip* 및 압축을 푼 해당 폴더)를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 샘플 클라이언트 애플리케이션에 Azure Digital Twins 원격 분석 이벤트를 브로드캐스트하도록 SignalR을 사용하여 Azure Functions를 설정합니다.

다음으로, Azure SignalR Service에 대해 자세히 알아봅니다.
* [Azure SignalR Service란?](../azure-signalr/signalr-overview.md)

또는 Azure Functions를 통한 Azure SignalR Service 인증에 대해 자세히 알아봅니다.
* [Azure SignalR Service 인증](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)