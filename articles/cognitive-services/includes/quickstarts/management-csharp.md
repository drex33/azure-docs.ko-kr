---
title: '빠른 시작: .NET용 Azure 관리 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 Azure 관리 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 9424766aea22b0dc8b8b43e2eb14be5f3621f688
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520796"
---
[참조 설명서](/dotnet/api/overview/azure/cognitiveservices/management) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/8.0.0-preview/) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>C# 필수 구성 요소

* 유효한 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/).
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* [!INCLUDE [contributor-requirement](./contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

새 .NET Core 애플리케이션을 만듭니다. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `azure-management-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs* 라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다. 

```console
dotnet new console -n azure-management-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Azure Management 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

### <a name="import-libraries"></a>라이브러리 가져오기

*program.cs* 를 열고 다음 `using` 문을 파일 맨 위에 추가합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>클라이언트 인증

다음 필드를 *program.cs* 의 루트에 추가하고 사용자가 만든 서비스 주체와 Azure 계정 정보를 사용하여 해당 값을 입력합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

그런 다음, **Main** 메서드에서 이러한 값을 사용하여 **CognitiveServicesManagementClient** 개체를 구성합니다. 이 개체는 모든 Azure 관리 작업에 필요합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>호출 관리 메서드

**Main** 메서드에 다음 코드를 추가하여 사용 가능한 리소스를 나열하고, 샘플 리소스를 만들고, 소유한 리소스를 나열한 다음, 샘플 리소스를 삭제합니다. 다음 단계에서 이러한 메서드를 정의합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Cognitive Services 리소스 만들기(C#)

새 Cognitive Services 리소스를 만들고 구독하려면 **Create** 메서드를 사용합니다. 이 메서드는 전달하는 리소스 그룹에 청구 가능한 새 리소스를 추가합니다. 새 리소스를 만들 때 가격 책정 계층(또는 SKU) 및 Azure 위치와 함께 사용할 서비스의 "종류"를 알아야 합니다. 다음 메서드는 이러한 모든 것을 인수로 사용하여 리소스를 만듭니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>서비스 및 가격 책정 계층 선택

새 리소스를 만들 때 원하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/)(또는 SKU)과 함께 사용할 서비스의 "종류"를 알아야 합니다. 리소스를 만들 때 이 정보와 기타 정보를 매개 변수로 사용합니다. 스크립트에서 다음 메서드를 호출하여 사용 가능한 Cognitive Service "종류"의 목록을 찾을 수 있습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>리소스 보기

모든 리소스 그룹에서 Azure 계정 아래의 모든 리소스를 보려면 다음 메서드를 사용합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>리소스 삭제

다음 메서드는 지정된 리소스 그룹에서 지정된 리소스를 삭제합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

삭제된 리소스를 복구해야 하는 경우 [삭제된 Cognitive Services 리소스 복구](../../manage-resources.md)를 참조하세요.

## <a name="run-the-application"></a>애플리케이션 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run
```

## <a name="see-also"></a>참고 항목

* Cognitive Services를 안전하게 사용하는 방법을 알아보려면 **[Azure Cognitive Services 요청 인증](../../authentication.md)** 을 참조하세요.
* Cognitive Services에 포함된 다양한 범주 목록을 가져오려면 **[Azure Cognitive Services란?](../../what-are-cognitive-services.md)** 을 참조하세요.
* Cognitive Services에서 지원하는 자연어 목록을 보려면 **[자연어 지원](../../language-support.md)** 을 참조하세요.
* Cognitive Services를 온-프레미스로 사용하는 방법은 **[Cognitive Services를 컨테이너로 사용](../../cognitive-services-container-support.md)** 을 참조하세요.
* Cognitive Services 사용 비용을 추산하려면 **[Cognitive Services 비용 계획 및 관리](../../plan-manage-costs.md)** 를 참조하세요.
* 관리 SDK에 대한 자세한 내용은 **[Cognitive Services 관리 SDK 참조 설명서](/dotnet/api/overview/azure/cognitiveservices/management)** 를 참조하세요.
