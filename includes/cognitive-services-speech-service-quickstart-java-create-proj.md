---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: c73b7bf5dbc9d1676c1e4d9129d551fbac1d4688
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070061"
---
1. Eclipse를 시작합니다.

1. Eclipse Launcher에서 **작업 영역** 필드에 새 작업 영역 디렉터리의 이름을 입력합니다. 그리고 **시작** 을 선택합니다.

   ![Eclipse Launcher의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. 잠시 후 Eclipse IDE의 주 창이 표시됩니다. 시작 화면이 표시되는 경우 **시작** 화면을 닫습니다.

1. Eclipse 메뉴 모음에서 **파일** > **새로 만들기** > **프로젝트** 를 선택하여 새 프로젝트를 만듭니다.

1. **새 프로젝트** 대화 상자가 나타납니다. **Java 프로젝트** 를 선택하고 **다음** 을 선택합니다.

   ![Java 프로젝트가 강조 표시된 새 프로젝트 대화 상자의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. **새 Java 프로젝트** 마법사가 시작됩니다. **프로젝트 이름** 필드에 **빠른 시작** 을 입력하고, 실행 환경으로 **JavaSE-1.8** 을 선택합니다. **마침** 을 선택합니다.

   ![새 Java 프로젝트 마법사의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. **연결된 큐브 뷰를 열까요?** 라는 창이 나타나면 **큐브 뷰 열기** 를 선택합니다.

1. **패키지 탐색기** 에서 **빠른 시작** 프로젝트를 마우스 오른쪽 단추로 클릭합니다. 바로 가기 메뉴에서 **구성** > **Maven 프로젝트로 변환** 을 선택합니다.

   ![패키지 탐색기의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. **새 POM 만들기** 창이 나타납니다. **그룹 ID** 필드에 *com.microsoft.cognitiveservices.speech.samples* 를 입력하고, *아티팩트 ID* 에 **빠른 시작** 을 입력합니다. 그런 다음, **마침** 을 선택합니다.

   ![[새 POM 만들기] 창의 스크린샷](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. *pom.xml* 파일을 열고 편집합니다.

   * 파일 끝 부분에서 닫는 `</project>` 태그 앞에 다음과 같이 Speech SDK의 Maven 리포지토리에 대한 참조를 사용하여 `repositories` 요소를 만듭니다.

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * 또한 Speech SDK 버전 1.18.0을 종속성으로 사용하여 `dependencies` 요소를 추가합니다:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * 변경 내용을 저장합니다.
