---
title: IntelliJ를 사용하여 Azure Functions에서 Kotlin 함수 만들기
description: IntelliJ를 사용하여 간단한 HTTP 트리거 Kotlin 함수를 만든 다음, Azure의 서버리스 환경에서 실행되도록 게시하는 방법에 대해 알아봅니다.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f2ec995334069dc707c8eb09fae3601f58c6c549
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287261"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>IntelliJ를 사용하여 Azure에서 첫 번째 Kotlin 함수 만들기

이 문서에서는 IntelliJ IDEA 프로젝트에서 HTTP로 트리거되는 Java 함수를 만들고, IDE(통합 개발 환경)에서 프로젝트를 실행 및 디버그하고, 마지막으로 함수 프로젝트를 Azure의 함수 앱에 배포하는 방법을 보여줍니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>개발 환경 설정

IntelliJ를 사용하여 Kotlin 함수를 만들고 Azure에 게시하려면 다음 소프트웨어를 설치합니다.

- [JDK(Java Developer Kit)](/azure/developer/java/fundamentals/java-support-on-azure) 버전 8
- [Apache Maven](https://maven.apache.org) 버전 3.0 이상
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download) Community 또는 Ultimate 버전(Maven 포함)
- [Azure CLI](/cli/azure)
- Azure Functions Core Tools의 [버전 2.x](functions-run-local.md#v2). 이 버전은 Azure Functions의 작성, 실행 및 디버깅을 위한 로컬 개발 환경을 제공합니다.

> [!IMPORTANT]
> 이 문서의 단계를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.

## <a name="create-a-function-project"></a>함수 프로젝트 만들기

1. IntelliJ IDEA에서 **새 프로젝트 만들기** 를 선택합니다.  
1. **새 프로젝트** 창의 왼쪽 창에서 **Maven** 을 선택합니다.
1. **archetype에서 만들기** 확인란을 선택한 다음, [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)에 대해 **Archetype 추가** 를 선택합니다.
1. **Archetype 추가** 창에서 다음과 같이 필드를 완성합니다.
    - _GroupId_: com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype
    - _버전_: [중앙 리포지토리](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![IntelliJ IDEA의 archetype에서 Maven 프로젝트 만들기](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)의 최신 버전을 사용합니다.  
1. **확인** 선택하고 **다음** 을 선택합니다.
1. 현재 프로젝트에 대한 세부 정보를 입력하고 **완료** 를 선택합니다.

Maven은 이름이 _ArtifactId_ 값인 새 폴더에 프로젝트 파일을 만듭니다. 프로젝트에서 생성된 코드는 HTTP 트리거 요청의 본문을 에코하는 간단한 [HTTP 트리거](./functions-bindings-http-webhook.md) 함수입니다.

## <a name="run-project-locally-in-the-ide"></a>IDE에서 로컬로 프로젝트 실행

> [!NOTE]
> 로컬로 프로젝트를 실행하고 디버그하려면 [Azure Functions Core Tools 버전 2](functions-run-local.md#v2)를 설치해야 합니다.

1. 변경 내용을 수동으로 가져오거나 [자동 가져오기](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)를 사용합니다.
1. **Maven 프로젝트** 도구 모음을 엽니다.
1. **수명 주기** 를 확장하고 **패키지** 를 엽니다. 새로 만든 대상 디렉터리에 솔루션이 빌드 및 패키징됩니다.
1. **플러그 인** > **azure-functions** 를 확장하고 **azure-functions:run** 을 열어 Azure Functions 로컬 런타임을 시작합니다.  
  ![Azure Functions에 대한 Maven 도구 모음](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. 함수 테스트를 완료했으면 실행 대화 상자를 닫습니다. 한 번에 하나의 함수 호스트만 활성화되고 로컬로 실행될 수 있습니다.

## <a name="debug-the-project-in-intellij"></a>IntelliJ에서 프로젝트 디버그

1. 디버그 모드로 함수 호스트를 시작하려면 함수를 실행할 때 인수로 **-DenableDebug** 를 추가합니다. [maven 목표](https://www.jetbrains.com/help/idea/maven-support.html#run_goal)에서 구성을 변경하거나 터미널 창에서 다음 명령을 실행할 수 있습니다.  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   이 명령을 사용하면 함수 호스트가 5005에서 디버그 포트를 엽니다.

1. **실행** 메뉴에서 **구성 편집** 을 선택합니다.
1. **(+)** 를 선택하여 **원격** 을 추가합니다.
1. _이름_ 및 _설정_ 필드를 완료한 다음, **확인** 을 선택하여 구성을 저장합니다.
1. 설정이 끝나면 **디버그 < 원격 구성 이름 >** 를 선택하거나 키보드에서 Shift+F9를 눌러 디버깅을 시작합니다.

   ![IntelliJ에서 프로젝트 디버그](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. 디버깅이 끝나면 디버거 및 실행 중인 프로세스를 중지합니다. 한 번에 하나의 함수 호스트만 활성화되고 로컬로 실행될 수 있습니다.

## <a name="deploy-the-project-to-azure"></a>Azure에 프로젝트 배포

1. 프로젝트를 Azure의 함수 앱에 배포하려면 먼저 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)해야 합니다.

   ``` azurecli
   az login
   ```

1. `azure-functions:deploy` Maven 대상을 사용하여 새 함수 앱에 코드를 배포합니다. Maven 프로젝트 창에서 **azure-functions:deploy** 옵션을 선택할 수도 있습니다.

   ```
   mvn azure-functions:deploy
   ```

1. 함수 앱이 성공적으로 배포된 후 Azure CLI 출력에서 HTTP 트리거 함수의 URL을 찾습니다.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>다음 단계

이제 첫 번째 Kotlin 함수 앱을 Azure에 배포했으므로 Java 함수 및 Kotlin 함수 개발에 대한 자세한 내용은 [Azure Functions Java 개발자 가이드](functions-reference-java.md)를 검토하세요.
- `azure-functions:add` Maven 대상을 사용하여 다른 트리거가 있는 추가 함수 앱을 프로젝트에 추가합니다.
