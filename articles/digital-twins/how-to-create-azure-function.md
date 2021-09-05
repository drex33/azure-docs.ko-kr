---
title: 데이터 처리를 위한 Azure 함수 설정
titleSuffix: Azure Digital Twins
description: 디지털 트윈에서 액세스하고 트리거할 수 있는 Azure 함수를 만드는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 7/14/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: eaa26b4682dc7e984c0c84575d9e87b5255f6c99
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114386976"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Azure에서 데이터 처리를 위한 함수 앱 연결

디지털 트윈은 컴퓨팅 리소스를 통해 [이벤트 경로](concepts-route-events.md)를 사용하여 데이터를 기반으로 업데이트할 수 있습니다. 예를 들어 [Azure Functions](../azure-functions/functions-overview.md)를 사용하여 만든 함수는 다음에 대한 응답으로 디지털 트윈을 업데이트할 수 있습니다.
* Azure IoT Hub의 디바이스 원격 분석 데이터.
* 트윈 그래프 내에서 다른 디지털 트윈의 속성 변경 또는 기타 데이터.

이 문서에서는 Azure Digital Twins에 사용할 Azure 함수를 만드는 방법을 보여줍니다. 함수를 만들려면 다음 기본 단계를 수행합니다.

1. Visual Studio에서 Azure Functions 프로젝트를 만듭니다.
2. [Azure Event Grid](../event-grid/overview.md) 트리거가 있는 함수를 작성합니다.
3. 함수에 인증 코드를 추가하여 Azure Digital Twins에 액세스할 수 있도록 합니다.
4. 함수 앱을 Azure에 게시합니다.
5. 함수 앱에 대한 [보안](concepts-security.md)을 설정합니다.

## <a name="prerequisite-set-up-azure-digital-twins"></a>필수 구성 요소: Azure Digital Twins 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Visual Studio에서 함수 앱 만들기

Visual Studio를 사용하여 함수 앱을 만드는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Event Grid 트리거가 있는 함수를 작성합니다.

함수 앱에 SDK를 추가하여 함수를 작성할 수 있습니다. 함수 앱은 [.NET(C#)용 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)를 사용하여 Azure Digital Twins와 상호 작용합니다. 

SDK를 사용하려면 다음 패키지를 프로젝트에 포함해야 합니다. Visual Studio NuGet 패키지 관리자를 사용하여 패키지를 설치합니다. 또는 명령줄 도구에서 `dotnet`을 사용하여 패키지를 추가합니다.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

그런 다음, Visual Studio 솔루션 탐색기에서 샘플 코드가 포함된 _.cs_ 파일을 엽니다. 패키지에 대한 다음 `using` 문을 추가합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>함수에 인증 코드 추가

이제 클래스 수준 변수를 선언하고 함수가 Azure Digital Twins에 액세스할 수 있게 하는 인증 코드를 추가합니다. 변수 및 코드를 함수에 추가합니다.

* **Azure Digital Twins 서비스 URL을 환경 변수로 읽도록 코딩합니다.** 함수에 하드 코딩하는 대신 환경 변수에서 서비스 URL을 읽는 것이 좋습니다. 이 환경 변수의 값은 [이 문서의 뒷부분](#set-up-security-access-for-the-function-app)에서 설정합니다. 환경 변수에 대한 자세한 내용은 [함수 앱 관리](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)를 참조하세요.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **HttpClient 인스턴스를 보유하는 정적 변수.** HttpClient는 생성하는 데 비교적 비용이 많이 들기 때문에 보통 모든 함수 호출에 대해 HttpClient를 생성하지 않기를 원합니다.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **관리 ID 자격 증명.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **지역 변수 _DigitalTwinsClient_.** 함수 내에 변수를 추가하여 Azure Digital Twins 클라이언트 인스턴스를 보관합니다. 클래스 내부에서 이 변수를 정적으로 설정하지 *마세요*.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **_adtInstanceUrl_ 에 대한 Null 검사.** Null 검사를 추가하고 함수 논리를 try/catch 블록으로 래핑하여 예외를 catch합니다.

이렇게 변경하면 함수 코드가 다음과 같이 보입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

이제 애플리케이션을 작성했으므로 Azure에 게시할 수 있습니다.

## <a name="publish-the-function-app-to-azure"></a>Azure에 함수 앱 게시

함수 앱을 게시하는 방법에 대한 지침은 [Visual Studio를 사용하여 Azure Functions 개발](../azure-functions/functions-develop-vs.md#publish-to-azure)을 참조하세요.

### <a name="verify-the-publication-of-your-function"></a>함수의 게시를 확인합니다.

1. [Azure Portal](https://portal.azure.com/)에서 자격 증명을 사용하여 로그인합니다.
2. 창 맨 위에 있는 검색 상자에서 함수 앱 이름을 검색하여 선택합니다.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Azure Portal을 보여주는 스크린샷. 검색 필드에 함수 앱 이름을 입력합니다." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. **함수 앱** 페이지가 열리면 왼쪽에 있는 메뉴 옵션에서 **함수** 를 선택합니다. 함수가 성공적으로 게시되면 목록에 해당 이름이 표시됩니다.

    > [!Note] 
    > 게시된 함수 목록에 함수가 표시되기까지 몇 분 정도 기다리면서 페이지를 새로 고칩니다.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Azure Portal에서 게시된 함수를 보여주는 스크린샷." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

함수 앱이 Azure Digital Twins에 액세스하려면 Azure Digital Twins 인스턴스에 액세스할 수 있는 권한이 있는 시스템 관리 ID가 있어야 합니다. 이는 다음에 설정하게 됩니다.

## <a name="set-up-security-access-for-the-function-app"></a>함수 앱에 대한 보안 액세스 설정

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Digital Twins와 함께 사용하기 위한 Azure 함수 앱을 설정했습니다. 다음으로, 기본 함수를 빌드하여 [Azure Digital Twins로 IoT Hub 데이터를 수집](how-to-ingest-iot-hub-data.md)하는 방법을 알아봅니다.
