---
title: 방법 - 소스 코드에서 Spring Cloud 애플리케이션 시작
description: 이 빠른 시작에서는 소스 코드에서 직접 Azure Spring Cloud의 애플리케이션을 시작하는 방법을 알아봅니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/12/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0815f48fd9b194a84566138b25b4bedc97de1a83
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490329"
---
# <a name="how-to-deploy-spring-boot-applications-from-azure-cli"></a>Azure CLI에서 Spring Boot 애플리케이션을 배포하는 방법

**이 문서는 다음에 적용됩니다.** ✔️ Java

Azure Spring Cloud는 Azure에서 Spring Boot 애플리케이션을 사용하도록 설정합니다.

Java 소스 코드 또는 미리 빌드된 JAR에서 직접 애플리케이션을 시작할 수 있습니다. 이 문서에서는 배포 절차를 설명합니다.

이 빠른 시작에서는 다음을 수행하는 방법을 설명합니다.

> [!div class="checklist"]
> * 서비스 인스턴스 프로비저닝
> * 인스턴스에 대한 구성 서버 설정
> * 로컬로 애플리케이션 빌드
> * 각 애플리케이션 배포
> * 애플리케이션에 대한 퍼블릭 엔드포인트 할당

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 Azure 구독에 필요한 종속성이 있는지 확인합니다.

1. [Git 설치](https://git-scm.com/)
2. [JDK 8 설치](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Maven 3.0 이상 설치](https://maven.apache.org/download.cgi)
4. [Azure CLI 설치](/cli/azure/install-azure-cli)
5. [Azure 구독에 가입](https://azure.microsoft.com/free/)

> [!TIP]
> Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함하는 일반적인 Azure 도구가 미리 설치되어 있습니다. Azure 구독에 로그인한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다.  Azure Cloud Shell에 대한 자세한 내용은 [설명서를 참조하세요](../cloud-shell/overview.md).

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장 설치

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Azure CLI를 사용하여 서비스 인스턴스 프로비저닝

Azure CLI에 로그인하고 활성 구독을 선택합니다.

```azurecli
az login
az account list -o table
az account set --subscription
```

Azure Spring Cloud에 서비스를 포함할 리소스 그룹을 만듭니다. [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)에 대해 자세히 알아볼 수 있습니다.

```azurecli
az group create --location eastus --name <resource-group-name>
```

다음 명령을 실행하여 Azure Spring Cloud의 인스턴스를 프로비저닝합니다. Azure Spring Cloud에서 서비스 이름을 준비합니다. 이름은 4~32자 사이여야 하며 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다. 서비스 이름의 첫 글자는 문자여야 하며 마지막 문자는 문자 또는 숫자여야 합니다.

```azurecli
az spring-cloud create --resource-group <resource-group-name> --name <resource-name>
```

서비스 인스턴스를 배포하는 데 약 5분이 걸립니다.

다음 명령을 사용하여 기본 리소스 그룹 이름 및 Azure Spring Cloud 인스턴스 이름을 설정합니다.

```azurecli
az config set defaults.group=<service-group-name>
az config set defaults.spring-cloud=<service-instance-name>
```

## <a name="create-the-application-in-azure-spring-cloud"></a>Azure Spring Cloud에서 애플리케이션 만들기

다음 명령은 구독에서 Azure Spring Cloud의 애플리케이션을 만듭니다.  그러면 애플리케이션을 업로드할 수 있는 빈 서비스가 만들어집니다.

```azurecli
az spring-cloud app create --name <app-name>
```

## <a name="deploy-your-spring-boot-application"></a>Spring Boot 애플리케이션 배포

미리 빌드한 JAR이나 Gradle 또는 Maven 리포지토리에서 애플리케이션을 배포할 수 있습니다.  아래 각 사례에 대한 지침을 찾습니다.

### <a name="deploy-a-pre-built-jar"></a>미리 빌드된 JAR 배포

빌드한 JAR에서 로컬 컴퓨터에 배포하려면 빌드가 [fat-JAR](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)을 생성하는지 확인합니다.

활성 배포에 fat-JAR을 배포하려면

```azurecli
az spring-cloud app deploy --name <app-name> --jar-path <path-to-fat-JAR>
```

특정 배포에 fat-JAR을 배포하려면

```azurecli
az spring-cloud app deployment create --app <app-name> \
    --name <deployment-name> \
    --jar-path <path-to-fat-JAR>
```

### <a name="deploy-from-source-code"></a>소스 코드에서 배포

Azure Spring Cloud는 [kpack](https://github.com/pivotal/kpack)을 사용하여 프로젝트를 빌드합니다.  Azure CLI를 사용하여 소스 코드를 업로드하고, kpack을 사용하여 프로젝트를 빌드하고, 대상 애플리케이션에 배포할 수 있습니다.

> [!WARNING]
> 프로젝트는 `target`(Maven 배포의 경우) 또는 `build/libs`(Gradle 배포의 경우)의 `MANIFEST.MF`에서 `main-class` 항목을 포함하는 JAR 파일을 하나만 생성해야 합니다.  `main-class` 항목을 포함하는 JAR 파일이 여러 개 있으면 배포가 실패합니다.

단일 모듈 Maven/Gradle 프로젝트의 경우:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy --name <app-name>
```

여러 모듈이 있는 Maven/Gradle 프로젝트의 경우 각 모듈에 대해 다음 작업을 반복합니다.

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy --name <app-name> \
    --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>배포 로그 표시

다음 명령을 사용하여 kpack 빌드 로그를 검토합니다.

```azurecli
az spring-cloud app show-deploy-log --name <app-name>
```

> [!NOTE]
> Kpack 로그는 해당 배포가 kpack을 사용하여 원본에서 빌드된 경우에만 최신 배포를 표시합니다.

## <a name="assign-a-public-endpoint-to-gateway"></a>게이트웨이에 공용 엔드포인트 할당

1. **애플리케이션 대시보드** 페이지를 엽니다.
2. `gateway` 애플리케이션을 선택하여 **애플리케이션 세부 정보** 페이지를 표시합니다.
3. **엔드포인트 할당** 을 선택하여 게이트웨이에 퍼블릭 엔드포인트를 할당합니다. 몇 분 정도 걸릴 수 있습니다.
4. 브라우저에 할당된 공용 IP를 입력하여 실행 중인 애플리케이션을 봅니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 서비스 인스턴스 프로비저닝
> * 인스턴스에 대한 구성 서버 설정
> * 로컬로 애플리케이션 빌드
> * 각 애플리케이션 배포
> * 애플리케이션에 대한 환경 변수 편집
> * 애플리케이션 게이트웨이에 대한 퍼블릭 IP 할당

> [!div class="nextstepaction"]
> [Spring Cloud 로그, 메트릭, 추적](./quickstart-logs-metrics-tracing.md)

GitHub에서 더 많은 샘플을 사용할 수 있습니다. [Azure Spring Cloud 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
