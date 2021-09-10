---
title: 앱 인증 코드 쓰기
titleSuffix: Azure Digital Twins
description: 클라이언트 애플리케이션에서 인증 코드를 작성하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 8/26/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a1b2465f29ae659f3e255a4843a2abd5f9ab75b2
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224860"
---
# <a name="write-client-app-authentication-code"></a>클라이언트 앱 인증 코드 작성

[Azure Digital Twins 인스턴스와 인증을 설정](how-to-set-up-instance-portal.md)한 후에는 인스턴스와 상호 작용하는 데 사용할 클라이언트 애플리케이션을 만들 수 있습니다. 시작 클라이언트 프로젝트를 설정한 후에는 Azure Digital Twins 인스턴스에 대해 **클라이언트 앱에서 인증하는 코드를 작성** 해야 합니다.

Azure Digital Twins는 [OAUTH 2.0을 기반으로 하는 Azure AD 보안 토큰](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)을 사용하여 인증을 수행합니다. SDK를 인증하려면 Azure Digital Twins에 대한 올바른 사용 권한이 있는 전달자 토큰을 가져와서 API 호출과 함께 전달해야 합니다. 

이 문서에서는 `Azure.Identity` 클라이언트 라이브러리를 사용하여 자격 증명을 가져오는 방법을 설명합니다. 이 문서에서는 [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)에 대해 작성하는 것과 같은 C#의 코드 예를 보여주지만, 사용 중인 SDK에 관계없이 `Azure.Identity` 버전을 사용할 수 있습니다. Azure Digital Twins에 사용할 수 있는 SDK에 대한 자세한 내용은 [Azure Digital Twins API 및 SDK](concepts-apis-sdks.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

먼저 [인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)에서 설정 단계를 완료합니다. 이렇게 하면 Azure Digital Twins 인스턴스가 있고 사용자에게 액세스 권한이 있는지 확인할 수 있습니다. 설정한 후에는 클라이언트 앱 코드를 작성할 준비가 된 것입니다.

계속하려면 코드를 작성하는 클라이언트 앱 프로젝트가 필요합니다. 클라이언트 앱 프로젝트를 아직 설정하지 않은 경우 이 자습서에서 사용할 수 있도록 선택한 언어로 기본 프로젝트를 만듭니다.

## <a name="authenticate-using-azureidentity-library"></a>Azure.Identity 라이브러리를 사용하여 인증

`Azure.Identity`는 전달자 토큰을 가져오고 SDK를 사용하여 인증하는 데 사용할 수 있는 몇 가지 자격 증명 가져오기 방법을 제공하는 클라이언트 라이브러리입니다. 이 문서에서는 C#의 예제를 제공하지만 다음을 비롯한 여러 언어에 대한 `Azure.Identity`를 볼 수 있습니다.

* [.NET (C#)](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

`Azure.Identity`의 세 가지 일반적인 자격 증명 가져오기 메서드는 다음과 같습니다.

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true)은 Azure에 배포되는 애플리케이션에 대한 기본 `TokenCredential` 인증 흐름을 제공하며 **로컬 개발에 권장** 되는 옵션입니다. 또한 이 문서에서 권장하는 다른 두 가지 메서드를 사용하는 데 활용할 수 있습니다. 즉, `ManagedIdentityCredential`을 래핑하고 구성 변수를 사용하여 `InteractiveBrowserCredential`에 액세스할 수 있습니다.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true)은 [MSI(관리 ID)](../active-directory/managed-identities-azure-resources/overview.md)를 필요로 하는 경우에 적합하며, Azure Functions에서 사용 및 Azure 서비스 배포에 적합합니다.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)은 대화형 애플리케이션을 위한 것이며 인증된 SDK 클라이언트를 만드는 데 사용할 수 있습니다.

이 문서의 나머지 부분에서는 이를 [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)와 함께 사용하는 방법을 보여 줍니다.

### <a name="add-azureidentity-to-your-net-project"></a>.NET 프로젝트에 Azure.Identity 추가

`Azure.Identity`로 인증하도록 .NET 프로젝트를 설정하려면 다음 단계를 완료합니다.

1. 프로젝트에 SDK 패키지 `Azure.DigitalTwins.Core` 및 `Azure.Identity` 패키지를 포함합니다. 선택한 도구에 따라 Visual Studio 패키지 관리자 또는 `dotnet` 명령줄 도구를 사용하여 패키지를 포함할 수 있습니다. 

1. 다음 using 문을 프로젝트 코드에 추가합니다.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

다음으로, `Azure.Identity`의 메서드 중 하나를 사용하여 자격 증명을 가져오는 코드를 추가합니다. 다음 섹션에서는 각각을 사용하는 방법에 대해 자세히 설명합니다.

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential 메서드

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true)은 Azure에 배포되는 애플리케이션에 대한 기본 `TokenCredential` 인증 흐름을 제공하며 **로컬 개발에 권장** 되는 옵션입니다.

기본 Azure 자격 증명을 사용하려면 Azure Digital Twins 인스턴스의 URL([찾아볼 수 있는 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))이 필요합니다.

프로젝트에 `DefaultAzureCredential`을 추가하는 코드 샘플은 다음과 같습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>로컬 Azure 자격 증명 설정

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 메서드

[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) 메서드는 [관리 ID(MSI)](../active-directory/managed-identities-azure-resources/overview.md)가 필요한 경우(예: [Azure Functions로 인증](#authenticate-azure-functions))에 유용합니다.

즉, `DefaultAzureCredential` 또는 `InteractiveBrowserCredential`과 동일한 프로젝트에서 `ManagedIdentityCredential`을 사용하여 프로젝트의 다른 부분을 인증할 수 있습니다.

기본 Azure 자격 증명을 사용하려면 Azure Digital Twins 인스턴스의 URL([찾아볼 수 있는 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))이 필요합니다. 또한 [앱 등록](./how-to-create-app-registration-portal.md)과 등록 [애플리케이션(클라이언트) ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)가 필요할 수 있습니다.

Azure 함수에서 다음과 같은 관리 ID 자격 증명을 사용할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 메서드

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 메서드는 대화형 애플리케이션을 위한 것이며 인증을 위해 웹 브라우저를 엽니다. 대화형 인증이 필요한 경우 `DefaultAzureCredential` 대신 이를 사용할 수 있습니다.

대화형 브라우저 자격 증명을 사용하려면 Azure Digital Twins API에 대한 사용 권한을 갖게 되는 **앱 등록** 과정이 필요합니다. 이 앱 등록을 설정하는 방법에 대한 단계는 [앱 등록 만들기](./how-to-create-app-registration-portal.md)를 참조하세요. 앱 등록을 설정한 후에는 다음이 필요합니다.
* [앱 등록의 애플리케이션(클라이언트) ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [앱 등록의 디렉터리(테넌트) ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [Azure Digital Twins 인스턴스의 URL](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

다음은 `InteractiveBrowserCredential`을 사용하여 인증된 SDK 클라이언트를 만드는 코드의 예제입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> 위와 같이 클라이언트 ID, 테넌트 ID, 인스턴스 URL을 코드에 직접 저장할 수 있지만, 코드에서 구성 파일 또는 환경 변수로부터 이러한 값을 가져오는 것이 좋습니다.

## <a name="authenticate-azure-functions"></a>Azure Functions 인증

이 섹션에는 Azure Functions로 인증하는 컨텍스트에서 몇 가지 중요한 구성 선택 사항이 포함되어 있습니다. 먼저, 함수가 Azure Digital Twins에 액세스할 수 있도록 하는 권장 클래스 수준 변수 및 인증 코드에 대해 읽습니다. 그런 다음 코드가 Azure에 게시된 후 함수에 대해 완료해야 하는 몇 가지 최종 구성 단계에 대해 읽습니다. 

### <a name="write-application-code"></a>애플리케이션 코드 작성

Azure 함수를 작성할 때 다음 변수와 코드를 함수에 추가하는 것이 좋습니다.

* **Azure Digital Twins 서비스 URL을 환경 변수 또는 구성 설정으로 읽도록 코딩합니다.** 함수에 하드 코딩하는 대신 [애플리케이션 설정/환경 변수](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)에서 서비스 URL을 읽는 것이 좋습니다. Azure 함수에서 환경 변수를 읽는 코드는 다음과 같습니다. 

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

    나중에 함수를 게시한 후 이 코드에서 읽을 환경 변수 값을 만들고 설정합니다. 이 작업을 수행하는 방법에 대한 지침은 [애플리케이션 설정 구성](#configure-application-settings)으로 건너뜁니다.

* **HttpClient 인스턴스를 보유하는 정적 변수.** HttpClient는 만드는 데 상대적으로 비용이 많이 들기 때문에 모든 함수 호출에 대해 만들지 않도록 인증 코드를 사용하여 한 번 만들 수 있습니다.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **관리 ID 자격 증명.** 함수에서 Azure Digital Twins에 액세스하는 데 사용할 관리 ID 자격 증명을 만듭니다.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

    나중에 함수를 게시한 후 함수의 ID에 Azure Digital Twins API를 액세스할 수 있는 권한이 있는지 확인합니다. 이 작업을 수행하는 방법에 대한 지침은 [액세스 역할 할당](#assign-an-access-role)으로 건너뜁니다.    

* **지역 변수 _DigitalTwinsClient_.** 함수 내에 변수를 추가하여 Azure Digital Twins 클라이언트 인스턴스를 보관합니다. 클래스 내부에서 이 변수를 정적으로 설정하지 *마세요*.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **_adtInstanceUrl_ 에 대한 Null 검사.** Null 검사를 추가하고 함수 논리를 try/catch 블록으로 래핑하여 예외를 catch합니다.

이를 함수에 추가한 후 함수 코드는 다음 예와 같을 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

인증 및 함수 논리 추가를 포함하여 함수 코드를 완료했으면 [Azure에 앱을 게시](../azure-functions/functions-develop-vs.md#publish-to-azure)합니다.

### <a name="configure-published-app"></a>게시된 앱 구성

마지막으로 게시된 Azure 기능에 대해 다음 구성 단계를 완료하여 Azure Digital Twins 인스턴스에 액세스할 수 있는지 확인합니다.

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="authenticate-across-tenants"></a>테넌트에서 인증

Azure Digital Twins는 Azure Digital Twins 인스턴스가 있는 구독의 기본 테넌트인 하나의 [Azure AD(Azure Active Directory) 테넌트](../active-directory/develop/quickstart-create-new-tenant.md)만 지원하는 서비스입니다.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

인스턴스와 다른 테넌트에 속한 서비스 주체 또는 사용자 계정을 사용하여 Azure Digital Twins 인스턴스에 액세스해야 하는 경우 다른 테넌트의 각 페더레이션 ID가 Azure Digital Twins 인스턴스의 "home" 테넌트에서 **토큰** 을 요청하도록 할 수 있습니다. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

코드의 자격 증명 옵션에서 홈 테넌트를 지정할 수도 있습니다. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>기타 자격 증명 방법

위에서 강조한 인증 시나리오가 사용자 앱의 요구 사항을 해결하지 못하는 경우 [Microsoft ID 플랫폼](../active-directory/develop/v2-overview.md#getting-started)에서 제공하는 다른 유형의 인증을 찾아볼 수 있습니다. 이 플랫폼에 대한 문서는 애플리케이션 유형별로 구성된 추가 인증 시나리오를 다룹니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에서 보안이 작동하는 방식에 대해 자세히 알아보세요.
* [Azure Digital Twins 솔루션에 대한 보안](concepts-security.md)

또는 이제 인증이 설정되었으므로 인스턴스에서 모델 만들기 및 관리로 이동합니다.
* [DTDL 모델 관리](how-to-manage-model.md)