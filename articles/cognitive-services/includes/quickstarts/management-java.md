---
title: '빠른 시작: Java용 Azure 관리 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Java용 Azure 관리 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/04/2021
ms.author: pafarley
ms.openlocfilehash: 1c397c1fd3078de7144d2e4cd292ce23a8510b7d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123123522"
---
[참조 설명서](/java/api/com.microsoft.azure.management.cognitiveservices) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/cognitiveservices/azure-resourcemanager-cognitiveservices/src/main/java/com/azure/resourcemanager/cognitiveservices) | [패키지(Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="java-prerequisites"></a>Java 필수 구성 요소

* 유효한 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/).
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* [!INCLUDE [contributor-requirement](./contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./terms-azure-portal.md)]


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

작업 디렉터리에서 `gradle init` 명령을 실행합니다. 이 명령은 *build.gradle.kts* 를 포함하여 런타임에 애플리케이션을 만들고 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다.

```console
gradle init --type basic
```

**DSL** 을 선택하라는 메시지가 표시되면 **Kotlin** 을 선택합니다.

작업 디렉터리에서 다음 명령을 실행합니다.

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

이 빠른 시작에서는 Gradle 종속성 관리자를 사용합니다. 다른 종속성 관리자에 대한 클라이언트 라이브러리 및 정보는 [Maven 중앙 리포지토리](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)에서 찾을 수 있습니다.

프로젝트의 *build.gradle.kts* 파일에서 필요한 플러그 인 및 설정과 함께 클라이언트 라이브러리를 `implementation` 문으로 포함합니다.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>라이브러리 가져오기

새 **src/main/java** 폴더로 이동하여 *Management.java* 라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>클라이언트 인증

*Management.java* 에 클래스를 추가한 다음, 그 안에 다음 필드와 해당 값을 추가합니다. 사용자가 만든 서비스 주체와 기타 Azure 계정 정보를 사용하여 해당 값을 입력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

그런 다음, **main** 메서드에서 이러한 값을 사용하여 **CognitiveServicesManager** 개체를 생성합니다. 이 개체는 모든 Azure 관리 작업에 필요합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>호출 관리 메서드

**Main** 메서드에 다음 코드를 추가하여 사용 가능한 리소스를 나열하고, 샘플 리소스를 만들고, 소유한 리소스를 나열한 다음, 샘플 리소스를 삭제합니다. 다음 단계에서 이러한 메서드를 정의합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-java"></a>Cognitive Services 리소스 만들기(Java)

새 Cognitive Services 리소스를 만들고 구독하려면 **create** 메서드를 사용합니다. 이 메서드는 전달하는 리소스 그룹에 청구 가능한 새 리소스를 추가합니다. 새 리소스를 만들 때 가격 책정 계층(또는 SKU) 및 Azure 위치와 함께 사용할 서비스의 "종류"를 알아야 합니다. 다음 메서드는 이러한 모든 것을 인수로 사용하여 리소스를 만듭니다.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>서비스 및 가격 책정 계층 선택

새 리소스를 만들 때 원하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/)(또는 SKU)과 함께 사용할 서비스의 "종류"를 알아야 합니다. 리소스를 만들 때 이 정보와 기타 정보를 매개 변수로 사용합니다. 다음 메서드를 호출하여 사용 가능한 Cognitive Service "종류"의 목록을 찾을 수 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>리소스 보기

모든 리소스 그룹에서 Azure 계정 아래의 모든 리소스를 보려면 다음 메서드를 사용합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>리소스 삭제

다음 메서드는 지정된 리소스 그룹에서 지정된 리소스를 삭제합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

삭제된 리소스를 복구해야 하는 경우 [삭제된 Cognitive Services 리소스 복구](../../manage-resources.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

* Cognitive Services를 안전하게 사용하는 방법을 알아보려면 **[Azure Cognitive Services 요청 인증](../../authentication.md)** 을 참조하세요.
* Cognitive Services에 포함된 다양한 범주 목록을 가져오려면 **[Azure Cognitive Services란?](../../what-are-cognitive-services.md)** 을 참조하세요.
* Cognitive Services에서 지원하는 자연어 목록을 보려면 **[자연어 지원](../../language-support.md)** 을 참조하세요.
* Cognitive Services를 온-프레미스로 사용하는 방법은 **[Cognitive Services를 컨테이너로 사용](../../cognitive-services-container-support.md)** 을 참조하세요.
* Cognitive Services 사용 비용을 추산하려면 **[Cognitive Services 비용 계획 및 관리](../../plan-manage-costs.md)** 를 참조하세요.
* 관리 SDK에 대한 자세한 내용은 **[Azure 관리 SDK 참조 설명서](/java/api/com.microsoft.azure.management.cognitiveservices)** 를 참조하세요.
