---
title: '자습서: IntelliJ를 사용하여 Azure Spring Cloud 애플리케이션 배포'
description: IntelliJ를 사용하여 Azure Spring Cloud에 애플리케이션을 배포합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: c3f1ead5d0c7e9ab7011a1179badc41b40912c83
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015385"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>IntelliJ를 사용하여 Azure Spring Cloud 애플리케이션 배포

**이 문서는 다음에 적용됩니다.** ✔️ Java

Azure Spring Cloud용 IntelliJ 플러그 인은 IntelliJ IDEA의 애플리케이션 배포를 지원합니다.

이 예제를 실행하기 전에 [기본 빠른 시작](./quickstart.md)을 시도해 볼 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [IntelliJ IDEA, Community/Ultimate Edition, 버전 2020.1/2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>플러그 인 설치

IntelliJ **플러그 인** UI에서 Azure Toolkit for IntelliJ IDEA 3.51.0을 추가할 수 있습니다.

1. IntelliJ를 시작합니다.  이전에 프로젝트를 연 경우 프로젝트를 닫아 시작 대화 상자를 표시합니다. 오른쪽 아래 링크에서 **구성** 을 선택한 다음, **플러그 인** 을 선택하여 플러그 인 구성 대화 상자를 열고 **디스크에서 플러그 인 설치** 를 선택합니다.

    ![구성 선택](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Azure Toolkit for IntelliJ를 검색합니다. **설치** 를 선택합니다.

    ![플러그 인 설치](media/spring-cloud-intellij-howto/install-plugin.png)

1. **IDE 다시 시작** 을 선택합니다.

## <a name="tutorial-procedures"></a>자습서 절차

다음 절차에서는 IntelliJ IDEA를 사용하여 Hello World 애플리케이션을 배포합니다.

* gs-spring-boot 프로젝트 열기
* Azure Spring Cloud에 배포
* 스트리밍 로그 표시

## <a name="open-gs-spring-boot-project"></a>gs-spring-boot 프로젝트 열기

1. 이 자습서의 원본 리포지토리를 다운로드하여 압축을 풀거나 Git 명령 `git clone https://github.com/spring-guides/gs-spring-boot.git`을 사용하여 복제합니다.
1. *gs-spring-boot\complete* 폴더로 이동합니다.
1. IntelliJ **시작** 대화 상자를 열고 **프로젝트 가져오기** 를 선택하여 가져오기 마법사를 엽니다.
1. *gs-spring-boot\complete* 폴더를 선택합니다.

    ![프로젝트 가져오기](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Azure Spring Cloud에 배포

Azure에 배포하려면 Azure 계정으로 로그인하고 구독을 선택해야 합니다.  로그인 세부 정보는 [설치 및 로그인](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)을 참조하세요.

1. IntelliJ 프로젝트 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Azure** -> **Azure Spring Cloud에 배포** 를 선택합니다.

    ![Azure 1에 배포](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. **이름** 필드에서 앱 이름을 적용합니다. **이름** 은 앱 이름이 아니라 구성을 나타냅니다. 사용자는 일반적으로 변경할 필요가 없습니다.
1. 프로젝트에서 **아티팩트** 에 대한 식별자를 적용합니다.
1. **앱:** 을 선택한 다음 **앱 만들기...** 를 선택합니다.

    ![Azure 2에 배포](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. **앱 이름** 을 입력한 다음 **확인** 을 선택합니다.

    ![Azure OK에 배포](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. **실행** 단추를 선택하여 배포를 시작합니다.

    ![Azure 3에 배포](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. 플러그 인은 프로젝트에서 `mvn package` 명령을 실행한 다음, 새 앱을 만들고 `package` 명령으로 생성된 jar을 배포합니다.

1. 출력 창에 앱 URL이 표시되지 않으면 Azure Portal에서 가져옵니다. 리소스 그룹에서 Azure Spring Cloud의 인스턴스로 이동합니다.  그런 다음, **앱** 을 선택합니다.  실행 중인 앱이 나열됩니다.

    ![테스트 URL 가져오기](media/spring-cloud-intellij-howto/get-test-url.png)

1. 브라우저에서 URL로 이동합니다.

    ![브라우저 2에서 탐색](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>스트리밍 로그 표시

로그를 가져오려면 다음을 수행합니다.

1. **Azure Explorer** 를 선택한 다음, **Spring Cloud** 를 선택합니다.
1. 실행 중인 앱을 마우스 오른쪽 단추로 클릭합니다.
1. 드롭다운 목록에서 **스트리밍 로그** 를 선택합니다.

    ![스트리밍 로그 선택](media/spring-cloud-intellij-howto/streaming-logs.png)

1. 인스턴스를 선택합니다.

    ![인스턴스 선택](media/spring-cloud-intellij-howto/select-instance.png)

1. 스트리밍 로그는 출력 창에 표시됩니다.

    ![스트리밍 로그 출력](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud용 Spring 애플리케이션 준비](how-to-prepare-app-deployment.md)
* [Azure Toolkit for IntelliJ에 대한 자세한 정보](/azure/developer/java/toolkit-for-intellij/)
