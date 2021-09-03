---
title: Azure Digital Twins API 및 SDK
titleSuffix: Azure Digital Twins
description: Azure Digital Twins API 및 SDK 옵션을 이해합니다.
author: baanders
ms.author: baanders
ms.date: 04/30/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6a69e1eeeb1861f7d2a14e1a96c959c18a090682
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438748"
---
# <a name="azure-digital-twins-apis-and-sdks"></a>Azure Digital Twins API 및 SDK

Azure Digital Twins는 인스턴스 및 해당 요소를 관리하기 위한 **컨트롤 플레인 API** 와 **데이터 평면 API** 를 모두 제공합니다. 
* 컨트롤 플레인 API는 [ARM(Azure Resource Manager)](../azure-resource-manager/management/overview.md) API이며, 인스턴스를 만들고 삭제하는 것과 같은 리소스 관리 작업을 다룹니다. 
* 데이터 평면 API는 Azure Digital Twins API이며 모델, 트윈 및 그래프 관리와 같은 데이터 관리 작업에 사용됩니다.

이 문서에서는 사용할 수 있는 API의 개요와 이 API와 상호 작용하는 방법을 제공합니다. [Postman](how-to-use-postman.md)과 같은 도구를 통해 또는 SDK를 통해 REST API를 관련 Swagger에 함께 직접 사용할 수 있습니다.

## <a name="overview-control-plane-apis"></a>개요: 컨트롤 플레인 API

컨트롤 플레인 API는 전체 인스턴스를 만들거나 삭제하는 것과 같은 작업을 수행하기 위해 Azure Digital Twins 인스턴스 전체를 관리하는 데 사용되는 [ARM](../azure-resource-manager/management/overview.md) API입니다. 또한 엔드포인트를 만들고 삭제하는 이러한 API를 사용합니다.

최신 컨트롤 플레인 API 버전은 _**2020-12-01**_ 입니다.

컨트롤 플레인 API를 사용하려면:
* [컨트롤 플레인 Swagger 리포지토리](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)의 최신 Swagger 폴더를 참조하여 API를 직접 호출할 수 있습니다. 이 폴더에는 사용법을 보여 주는 예제 폴더도 포함되어 있습니다.
* 현재 다음에서 컨트롤 API에 대한 SDK에 액세스할 수 있습니다.
  -  [.NET(C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([참조 [자동 생성]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true))([원본](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [Java](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins)([참조 [자동 생성]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true))([원본](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins)([원본](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/)([원본](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt)([원본](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

또한 [Azure Portal](https://portal.azure.com) 및 [CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)를 통해 Azure Digital Twins와 상호 작용하여 컨트롤 플레인 API를 연습할 수 있습니다.

## <a name="overview-data-plane-apis"></a>개요: 데이터 평면 API

데이터 평면 API는 Azure Digital Twins 인스턴스 내에서 요소를 관리하는 데 사용되는 Azure Digital Twins API입니다. 여기에는 경로 만들기, 모델 업로드, 관계 만들기 및 트윈 관리와 같은 작업이 포함되며 다음 범주로 크게 나눌 수 있습니다.
* **DigitalTwinModels** - DigitalTwinModels 범주에는 Azure Digital Twins 인스턴스에서 [모델](concepts-models.md)을 관리하는 API가 포함되어 있습니다. 관리 작업에는 DTDL에서 작성된 모델의 업로드, 유효성 검사, 검색 및 삭제가 포함됩니다.
* **DigitalTwins** - DigitalTwins 범주에는 개발자가 Azure Digital Twins 인스턴스에서 [디지털 트윈](concepts-twins-graph.md) 및 해당 관계를 만들고, 수정하고, 삭제할 수 있도록 하는 API가 포함되어 있습니다.
* **쿼리** - 쿼리 범주를 통해 개발자는 관계 전반에 걸쳐 [트윈 그래프에서 디지털 트윈 집합을 찾을](how-to-query-graph.md) 수 있습니다.
* **이벤트 경로** - 이벤트 경로 범주에는 시스템 및 다운스트림 서비스를 통해 [데이터를 라우팅](concepts-route-events.md)하는 API가 포함되어 있습니다.

최신 데이터 평면 API 버전은 _**2020-10-31**_ 입니다.

데이터 평면 API를 사용하려면:
* 다음 방법을 통해 API를 직접 호출할 수 있습니다.
   - [데이터 평면 swagger 리포지토리](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)의 최신 Swagger 폴더를 참조합니다. 이 폴더에는 사용법을 보여 주는 예제 폴더도 포함되어 있습니다. 
   - [API 참조 설명서](/rest/api/azure-digitaltwins/)를 확인합니다.
* **.NET(C#) SDK** 를 사용할 수 있습니다. .NET SDK를 사용하려면...
   - NuGet: [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core)에서 패키지를 보고 추가할 수 있습니다. 
   - [SDK 참조 설명서](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)를 볼 수 있습니다.
   - GitHub: [.NET용 Azure IoT Digital Twins 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)에서 샘플 폴더를 포함하여 SDK 원본을 찾을 수 있습니다. 
   - 이 문서의  [.NET(C#) SDK(데이터 평면)](#net-c-sdk-data-plane)  섹션을 계속 진행하면 자세한 정보 및 사용 예를 볼 수 있습니다.
* **JAVA SDK** 를 사용할 수 있습니다. Java SDK를 사용하려면...
   - Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)에서 패키지를 보고 설치할 수 있습니다.
   - [SDK 참조 설명서](/java/api/overview/azure/digitaltwins/client?view=azure-java-stable&preserve-view=true)를 볼 수 있습니다.
   - GitHub: [Java용 Azure IoT Digital Twins 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)에서 SDK 원본을 찾을 수 있습니다.
* **JavaScript SDK** 를 사용할 수 있습니다. JavaScript SDK를 사용하려면...
   - npm: [JavaScript용 Azure Azure Digital Twins Core 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/digital-twins-core)에서 패키지를 보고 설치할 수 있습니다.
   - [SDK 참조 설명서](/javascript/api/@azure/digital-twins-core/?view=azure-node-latest&preserve-view=true)를 볼 수 있습니다.
   - GitHub: [JavaScript용 Azure Azure Digital Twins Core 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)에서 SDK 원본을 찾을 수 있습니다.
* **Python SDK** 를 사용할 수 있습니다. Python SDK를 사용하려면...
   - PyPi: [Python용 Azure Azure Digital Twins Core 클라이언트 라이브러리](https://pypi.org/project/azure-digitaltwins-core/)에서 패키지를 보고 설치할 수 있습니다.
   - [SDK 참조 설명서](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true)를 볼 수 있습니다.
   - GitHub: [Python용 Azure Azure Digital Twins Core 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)에서 SDK 원본을 찾을 수 있습니다.

[CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)를 통해 Azure Digital Twins와 상호 작용하여 데이터 평면 API를 연습할 수도 있습니다.

## <a name="net-c-sdk-data-plane"></a>.NET(C#) SDK(데이터 평면)

Azure Digital Twins .NET(C#) SDK는 .Net용 Azure SDK의 일부입니다. 오픈 소스이며 Azure Digital Twins 데이터 평면 API를 기반으로 합니다.

> [!NOTE]
> SDK 설계에 대한 자세한 내용은 일반적인 [Azure SDK 디자인 원칙](https://azure.github.io/azure-sdk/general_introduction.html) 및 특정 [.NET 설계 지침](https://azure.github.io/azure-sdk/dotnet_introduction.html)을 참조하세요.

SDK를 사용하려면 NuGet 패키지 **Azure.DigitalTwins.Core** 를 프로젝트에 포함합니다. 최신 버전의 **Azure.Identity** 패키지도 필요합니다. Visual Studio에서 NuGet 패키지 관리자를 사용하여 이러한 패키지를 추가할 수 있습니다(*도구 > Nuget 패키지 관리자 > 솔루션에 대한 Nuget 패키지 관리* 를 통해 액세스). 또한 아래 NuGet 패키지 링크에 있는 명령과 함께 .NET 명령줄 도구를 사용하여 프로젝트에 추가할 수 있습니다.
* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core): [.NET용 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)용 패키지입니다. 
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity): Azure에 대한 인증에 도움이 되는 도구를 제공하는 라이브러리입니다.

실제로 API를 사용하는 방법에 대한 자세한 연습은 [클라이언트 앱 코딩](tutorial-code.md)을 참조하세요. 

### <a name="serialization-helpers"></a>Serialization 도우미

Serialization 도우미는 기본 정보에 대한 액세스를 위해 트윈 데이터를 신속하게 만들거나 역직렬화하기 위해 SDK 내에서 사용할 수 있는 도우미 함수입니다. 핵심 SDK 메서드는 기본적으로 트윈 데이터를 JSON으로 반환하므로 이러한 도우미 클래스를 사용하여 트윈 데이터를 더 분할하는 것이 유용할 수 있습니다.

사용 가능한 도우미 클래스는 다음과 같습니다.
* `BasicDigitalTwin`: 일반적으로 디지털 트윈의 핵심 데이터를 나타냅니다.
* `BasicDigitalTwinComponent`: 일반적으로 `BasicDigitalTwin`의 `Contents` 속성의 구성 요소를 나타냅니다.
* `BasicRelationship`: 일반적으로 관계의 핵심 데이터를 나타냅니다.
* `DigitalTwinsJsonPropertyName`: 사용자 지정 디지털 트윈 유형에 대한 JSON serialization 및 deserialization에 사용되는 문자열 상수를 포함합니다.

## <a name="general-apisdk-usage-notes"></a>일반 API/SDK 사용 메모

> [!NOTE]
> Azure Digital Twins는 현재 **CORS(원본 간 리소스 공유)** 를 지원하지 않습니다. 영향 및 해결 방법에 대한 자세한 내용은 개념: Azure Digital Twins 솔루션에 대한 보안의 *[CORS(원본 간 리소스 공유)](concepts-security.md#cross-origin-resource-sharing-cors)* 섹션을 참조하세요.

다음 목록에서는 API 및 SDK 사용에 대한 추가 세부 정보 및 일반적인 지침을 제공합니다.

* Postman과 같은 HTTP REST 테스트 도구를 사용하여 Azure Digital Twins API에 대한 직접 호출을 수행할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [Postman을 사용하여 API 요청 만들기](how-to-use-postman.md)를 참조하세요.
* SDK를 사용하려면 `DigitalTwinsClient` 클래스를 인스턴스화합니다. 생성자에는 `Azure.Identity` 패키지의 다양한 종류의 인증 방법을 사용하여 얻을 수 있는 자격 증명이 필요합니다. `Azure.Identity`에 대한 자세한 내용은 [네임스페이스 설명서](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)를 참조하세요. 
* 시작하는 동안 유용한 `InteractiveBrowserCredential`을 찾을 수 있지만, Azure Digital Twins에 대해 [MSI로 설정된 Azure 함수](../app-service/overview-managed-identity.md?tabs=dotnet)를 인증하는 데 사용할 수 있는 [관리 ID](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)에 대한 자격 증명을 비롯하여 몇 가지 다른 옵션이 있습니다. `InteractiveBrowserCredential`에 대한 자세한 내용은 [클래스 설명서](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)를 참조하세요.
* Azure Digital Twins API에 대한 요청에는 Azure Digital Twins 인스턴스가 있는 동일한 [Azure AD](../active-directory/fundamentals/active-directory-whatis.md)(Azure Active Directory) 테넌트의 일부인 사용자 또는 서비스 주체가 필요합니다. Azure Digital Twins 엔드포인트의 악의적인 검색을 방지하기 위해 원래 테넌트 외부에서 액세스 토큰이 있는 요청은 "404 하위 도메인을 찾을 수 없음" 오류 메시지가 반환됩니다. Azure AD B2B 협업을 통해 사용자나 서비스 주체가 Azure Digital Twins 데이터 소유자나 Azure Digital Twins 데이터 Reader [역할](../active-directory/external-identities/what-is-b2b.md)을 확보 *했더라도* 해당 오류는 반환됩니다. 여러 테넌트 간 액세스를 구현하는 방법에 대한 자세한 내용은 [앱 인증 코드 작성](how-to-authenticate-client.md#authenticate-across-tenants)을 참조하세요.
* 모든 서비스 API 호출은 `DigitalTwinsClient` 클래스에서 멤버 함수로 노출됩니다.
* 모든 서비스 함수는 동기 및 비동기 버전으로 존재합니다.
* 모든 서비스 함수는 400 이상의 반환 상태에 대한 예외를 throw합니다. `try` 섹션으로 호출을 래핑하고 최소한의 `RequestFailedExceptions`를 catch해야 합니다. 이러한 형식의 예외에 대한 자세한 내용은 해당 [참조 설명서](/dotnet/api/azure.requestfailedexception?view=azure-dotnet&preserve-view=true)를 참조하세요.
* 대부분의 서비스 메서드는 `Response<T>`(또는 비동기 호출의 경우 `Task<Response<T>>`)를 반환합니다. 여기서 `T`는 서비스 호출에 대한 반환 개체의 클래스입니다. [Response](/dotnet/api/azure.response-1?view=azure-dotnet&preserve-view=true) 클래스는 서비스 반환을 캡슐화하고 해당 `Value` 필드에 반환 값을 제공합니다.  
* 페이징 결과를 포함하는 서비스 메서드는 `Pageable<T>` 또는 `AsyncPageable<T>`를 결과로 반환합니다. `Pageable<T>` 클래스에 대한 자세한 내용은 해당 [참조 설명서](/dotnet/api/azure.pageable-1?view=azure-dotnet&preserve-view=true)를 참조하세요. `AsyncPageable<T>`에 대한 자세한 내용은 해당 [참조 설명서](/dotnet/api/azure.asyncpageable-1?view=azure-dotnet&preserve-view=true)를 참조하세요.
* `await foreach` 루프를 사용하여 페이징 결과를 반복할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [관련 설명서](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)를 참조하세요.
* 기본 SDK는 `Azure.Core`입니다. SDK 인프라 및 유형에 대한 참조는 [Azure 네임스페이스 설명서](/dotnet/api/azure?view=azure-dotnet&preserve-view=true)를 참조하세요.


서비스 메서드는 가능한 경우 항상 강력한 형식의 개체를 반환합니다. 그러나 Azure Digital Twins는 런타임에 사용자가 구성한 모델을 기반으로 하기 때문에(서비스에 업로드된 DTDL 모델을 통해) 많은 서비스 API가 트윈 데이터를 JSON 형식으로 사용하고 반환합니다.

## <a name="monitor-api-metrics"></a>API 메트릭 모니터링

요청, 대기 시간 및 실패율과 같은 API 메트릭은 [Azure Portal](https://portal.azure.com/)에서 볼 수 있습니다. 

포털 홈페이지에서 Azure Digital Twins 인스턴스를 검색하여 세부 정보를 가져옵니다. Azure Digital Twins 인스턴스 메뉴에서 **메트릭** 옵션을 선택하여 *메트릭* 페이지를 표시합니다.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins의 메트릭 페이지를 보여주는 스크린샷.":::

여기에서 인스턴스에 대한 메트릭을 보고 사용자 지정 보기를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

Postman을 사용하여 API에 대한 직접 요청을 만드는 방법을 참조하세요.
* [Postman으로 API 요청 만들기](how-to-use-postman.md)

또는 다음 자습서를 사용하여 클라이언트 앱을 만들어 .NET SDK 사용을 연습합니다.
* [클라이언트 앱 코딩](tutorial-code.md)
