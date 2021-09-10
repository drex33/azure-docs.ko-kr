---
title: 문서 번역 C#/.NET 또는 Python 클라이언트 라이브러리
titleSuffix: Azure Cognitive Services
description: 클라우드 기반 일괄 처리 문서 번역 서비스 및 프로세스에 Translator C#/.NET 또는 Python 클라이언트 라이브러리(SDK) 사용
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 07/06/2021
ms.author: lajanuar
ms.openlocfilehash: b7bcf5339f6bff6a0f055e2016bcd3e12bfd5f45
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566276"
---
# <a name="document-translation-client-library-sdks"></a>문서 번역 클라이언트 라이브러리 SDK
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD001 -->
[문서 번역](overview.md)은 [Azure Translator](../translator-overview.md) 서비스의 클라우드 기반 기능입니다. 원래 문서 구조와 형식을 유지하면서 전체 문서를 번역하거나 일괄 처리 문서 번역을 다양한 파일 형식으로 처리할 수 있습니다. 이 문서에서는 문서 번역 서비스 C#/.NET 및 Python 클라이언트 라이브러리를 사용하는 방법을 알아봅니다. REST API에 대해서는 [빠른 시작](get-started-with-document-translation.md) 가이드를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* 활성 [**Azure 계정**](https://azure.microsoft.com/free/cognitive-services/).  계정이 없는 경우 [**무료 계정을 만들 수**](https://azure.microsoft.com/free/) 있습니다.

* [**단일 서비스 Translator 리소스**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)(다중 서비스 Cognitive Services 리소스 **아님**).

* [**Azure Blob Storage 계정**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). 원본 및 대상 파일에 대한 Azure Blob Storage 계정에서 [**컨테이너를 만듭니다**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

  * **원본 컨테이너**. 이 컨테이너는 번역을 위해 파일을 업로드하는 위치입니다(필수).
  * **대상 컨테이너**. 이 컨테이너는 번역된 파일이 저장되는 위치입니다(필수).

* 또한 원본 및 대상 컨테이너에 대한 SAS(공유 액세스 서명) 토큰을 만들어야 합니다. `sourceUrl` 및 `targetUrl`에는 쿼리 문자열로 추가된 SAS(공유 액세스 서명) 토큰이 포함되어야 합니다. 토큰은 컨테이너 또는 특정 Blob에 할당될 수 있습니다. [**문서 번역을 위한 SAS 토큰 생성 프로세스**](create-sas-tokens.md)를 *참조하세요*.

  * **원본** 컨테이너 또는 Blob에는 **읽기** 및 **나열** 액세스가 지정되어 있어야 합니다.
  * **대상** 컨테이너 또는 Blob에는 **쓰기** 및 **나열** 액세스가 지정되어 있어야 합니다.

자세한 내용은 [SAS 토큰 만들기](create-sas-tokens.md)를 ‘참조’하세요.

## <a name="client-libraries"></a>클라이언트 라이브러리

### <a name="cnet"></a>[C#/.NET](#tab/csharp)

| [패키지(NuGet)][documenttranslation_nuget_package] | [클라이언트 라이브러리][documenttranslation_client_library_docs] |  [REST API][documenttranslation_rest_api] | [제품 설명서][documenttranslation_docs] | [샘플][documenttranslation_samples] |

> [!IMPORTANT]
> 문서 번역 SDK의 시험판 버전입니다. 고객이 먼저 이용해 보고 피드백을 제공할 수 있도록 소개 기반으로 제공됩니다. 시험판 버전은 아직 개발 중이며 변경될 수 있고 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 프로덕션 애플리케이션에서는 사용하지 마세요.

### <a name="set-up-your-project"></a>프로젝트 설정

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `batch-document-translation`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs* 라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n batch-document-translation
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음 명령을 사용하여 애플리케이션을 빌드합니다.

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

애플리케이션 디렉터리 내에서 다음 방법 중 하나를 사용하여 .NET용 문서 번역 클라이언트 라이브러리를 설치합니다.

#### <a name="net-cli"></a>**.NET CLI**

```console
dotnet add package Azure.AI.Translation.Document --version 1.0.0-beta.2
```

#### <a name="nuget-package-manager"></a>**NuGet 패키지 관리자**

```console
Install-Package Azure.AI.Translation.Document -Version 1.0.0-beta.2
```

#### <a name="nuget-packagereference"></a>**NuGet PackageReference**

```xml
<ItemGroup>
    <!-- ... -->
<PackageReference Include="Azure.AI.Translation.Document" Version="1.0.0-beta.2" />
    <!-- ... -->
</ItemGroup>
```

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 Program.cs 파일을 엽니다. 다음 using 지시문을 추가합니다.

```csharp
using Azure;
using Azure.AI.Translation.Document;

using System;
using System.Threading;
```

애플리케이션의 **Program** 클래스에서 구독 키와 사용자 지정 엔드포인트에 대한 변수를 만듭니다. 자세한 내용은 [사용자 지정 도메인 이름 및 구독 키](get-started-with-document-translation.md#custom-domain-name-and-subscription-key)를 ‘참조’하세요.

```csharp
private static readonly string endpoint = "<your custom endpoint>";
private static readonly string subscriptionKey = "<your subscription key>";
```

### <a name="translate-a-document-or-batch-files"></a>문서 또는 배치 파일 번역

* 단일 Blob 컨테이너에서 하나 이상의 문서에 대한 번역 작업을 시작하려면 `StartTranslationAsync` 메서드를 호출합니다.

* `StartTranslationAsync`를 호출하려면 다음 매개 변수를 포함하는 `DocumentTranslationInput` 개체를 초기화해야 합니다.

* **sourceUri**. 번역할 문서를 포함하는 원본 컨테이너의 SAS URI입니다.
* **targetUri** 번역된 문서가 기록될 대상 컨테이너의 SAS URI입니다.
* **targetLanguageCode**. 번역된 문서의 언어 코드입니다. 언어 코드는 [언어 지원](../language-support.md) 페이지에서 확인할 수 있습니다.

```csharp

public void StartTranslation() {
  Uri sourceUri = new Uri("<sourceUrl>");
  Uri targetUri = new Uri("<targetUrl>");

  DocumentTranslationClient client = new DocumentTranslationClient(new Uri(endpoint), new AzureKeyCredential(subscriptionKey));

  DocumentTranslationInput input = new DocumentTranslationInput(sourceUri, targetUri, "es")

  DocumentTranslationOperation operation = await client.StartTranslationAsync(input);

  await operation.WaitForCompletionAsync();

  Console.WriteLine($ "  Status: {operation.Status}");
  Console.WriteLine($ "  Created on: {operation.CreatedOn}");
  Console.WriteLine($ "  Last modified: {operation.LastModified}");
  Console.WriteLine($ "  Total documents: {operation.DocumentsTotal}");
  Console.WriteLine($ "    Succeeded: {operation.DocumentsSucceeded}");
  Console.WriteLine($ "    Failed: {operation.DocumentsFailed}");
  Console.WriteLine($ "    In Progress: {operation.DocumentsInProgress}");
  Console.WriteLine($ "    Not started: {operation.DocumentsNotStarted}");

  await foreach(DocumentStatusResult document in operation.Value) {
    Console.WriteLine($ "Document with Id: {document.DocumentId}");
    Console.WriteLine($ "  Status:{document.Status}");
    if (document.Status == TranslationStatus.Succeeded) {
      Console.WriteLine($ "  Translated Document Uri: {document.TranslatedDocumentUri}");
      Console.WriteLine($ "  Translated to language: {document.TranslatedTo}.");
      Console.WriteLine($ "  Document source Uri: {document.SourceDocumentUri}");
    }
    else {
      Console.WriteLine($ "  Error Code: {document.Error.ErrorCode}");
      Console.WriteLine($ "  Message: {document.Error.Message}");
    }
  }
}
```

이것으로 끝입니다. .NET 클라이언트 라이브러리를 사용하여 Blob 컨테이너에서 문서를 번역하는 프로그램을 만들었습니다.

### <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
 > [**더 많은 C# 코드 샘플 살펴보기**](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.Translation.Document_1.0.0-beta.2/sdk/translation/Azure.AI.Translation.Document/samples)

<!-- LINKS -->

[documenttranslation_nuget_package]: https://www.nuget.org/packages/Azure.AI.Translation.Document/1.0.0-beta.2
[documenttranslation_client_library_docs]: /dotnet/api/azure.ai.translation.document
[documenttranslation_docs]: overview.md
[documenttranslation_rest_api]: reference/rest-api-guide.md
[documenttranslation_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/translation/Azure.AI.Translation.Document/samples

### <a name="python"></a>[Python](#tab/python)

| [패키지(PyPI)][python-dt-pypi] |  [클라이언트 라이브러리][python-dt-client-library] |  [REST API][python-rest-api] | [제품 설명서][python-dt-product-docs] | [샘플][python-dt-samples] |

> [!IMPORTANT]
> 문서 번역 SDK의 시험판 버전입니다. 고객이 먼저 이용해 보고 피드백을 제공할 수 있도록 소개 기반으로 제공됩니다. 시험판 버전은 아직 개발 중이며 변경될 수 있고 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 프로덕션 애플리케이션에서는 사용하지 마세요.

### <a name="set-up-your-project"></a>프로젝트 설정

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

아직 설치하지 않은 경우 [Python](https://www.python.org/downloads/)을 설치한 다음, 최신 버전의 Translator 클라이언트 라이브러리를 설치합니다.

```console
pip install azure-ai-translation-document
```

### <a name="create-your-application"></a>애플리케이션 만들기

선호하는 편집기 또는 IDE에서 Python 애플리케이션을 새로 만듭니다. 그런 다음, 다음 라이브러리를 가져옵니다.

```python
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.translation.document import DocumentTranslationClient
```

리소스 구독 키, 사용자 지정 엔드포인트, sourceUrl 및 targetUrl에 대한 변수를 만듭니다. 자세한 내용은 [사용자 지정 도메인 이름 및 구독 키](get-started-with-document-translation.md#custom-domain-name-and-subscription-key)를 ‘참조’하세요.

```python
 subscriptionKey = "<your-subscription-key>"
 endpoint = "<your-custom-endpoint>"
 sourceUrl = "<your-container-sourceUrl>"
 targetUrl = "<your-container-targetUrl>"
```

### <a name="translate-a-document-or-batch-files"></a>문서 또는 배치 파일 번역

```python
client = DocumentTranslationClient(endpoint, AzureKeyCredential(subscriptionKey))

    poller = client.begin_translation(sourceUrl, targetUrl, "fr")
    result = poller.result()

    print("Status: {}".format(poller.status()))
    print("Created on: {}".format(poller.details.created_on))
    print("Last updated on: {}".format(poller.details.last_updated_on))
    print("Total number of translations on documents: {}".format(poller.details.documents_total_count))

    print("\nOf total documents...")
    print("{} failed".format(poller.details.documents_failed_count))
    print("{} succeeded".format(poller.details.documents_succeeded_count))

    for document in result:
        print("Document ID: {}".format(document.id))
        print("Document status: {}".format(document.status))
        if document.status == "Succeeded":
            print("Source document location: {}".format(document.source_document_url))
            print("Translated document location: {}".format(document.translated_document_url))
            print("Translated to language: {}\n".format(document.translated_to))
        else:
            print("Error Code: {}, Message: {}\n".format(document.error.code, document.error.message))
```

이것으로 끝입니다. Python 클라이언트 라이브러리를 사용하여 Blob 컨테이너에서 문서를 번역하는 프로그램을 만들었습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [더 많은 Python 코드 샘플 살펴보기](https://github.com/Azure/azure-sdk-for-python/tree/azure-ai-translation-document_1.0.0b1/sdk/translation/azure-ai-translation-document/samples)

<!-- LINKS -->
[python-dt-pypi]: https://aka.ms/azsdk/python/texttranslation/pypi
[python-dt-client-library]: https://aka.ms/azsdk/python/documenttranslation/docs
[python-rest-api]: reference/rest-api-guide.md
[python-dt-product-docs]: overview.md
[python-dt-samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/translation/azure-ai-translation-document/samples

---