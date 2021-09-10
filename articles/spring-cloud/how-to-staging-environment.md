---
title: Azure Spring Cloud에서 스테이징 환경 설정 | Microsoft Docs
description: Azure Spring Cloud에서 청록색 배포를 사용하는 방법 알아보기
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: e17bc1bd057ba631e7d7dadb2d53f7d8b3db633b
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567507"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure Spring Cloud에서 스테이징 환경 설정

**이 문서는 다음에 적용됩니다.** ✔️ Java

이 문서는 Azure Spring Cloud에서 파란색-녹색 배포 패턴을 사용하여 스테이징 배포를 설정하는 방법을 설명합니다. 파란색-녹색 배포는 새로운(녹색) 버전이 배포되는 동안 기존(파란색) 버전을 유지하도록 하는 Azure DevOps의 지속적인 업데이트 패턴입니다. 이 문서에서는 프로덕션 배포를 변경하지 않고 스테이징 배포를 프로덕션에 배치하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 표준 가격 책정 계층의 Azure Spring Cloud 인스턴스
* Azure CLI용 [Azure Spring Cloud 확장](/cli/azure/azure-cli-extensions-overview)

이 문서에서는 Spring Initializr에서 빌드된 애플리케이션을 사용합니다. 이 예제에 다른 애플리케이션을 사용하려는 경우 애플리케이션의 공용 주소 부분을 간단하게 변경해서 스테이징 배포를 프로덕션과 구분해야 합니다.

> [!TIP]
> [Azure Cloud Shell](https://shell.azure.com)은 이 문서의 지침을 실행하는 데 사용할 수 있는 무료 대화형 셸입니다.  최신 버전의 Git, JDK, Maven 및 Azure CLI를 포함하는 미리 설치된 공통 Azure 도구를 포함합니다. Azure 구독에 로그인되어 있으면 Cloud Shell 인스턴스를 시작하세요. 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요.

Azure Spring Cloud에서 파란색-녹색 배포를 설정하려면 다음 섹션의 지침을 따르세요.

## <a name="install-the-azure-cli-extension"></a>Azure CLI 확장 설치

다음 명령을 사용하여 Azure CLI용 Azure Spring Cloud 확장을 설치합니다.

```azurecli
az extension add --name spring-cloud
```

## <a name="prepare-the-app-and-deployments"></a>앱 및 배포 준비

애플리케이션을 빌드하려면 다음 단계를 수행합니다.

1. 샘플 앱의 코드를 생성하려면 [이 구성](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client)으로 Spring Initializr를 사용하세요.

2. 코드를 다운로드합니다.
3. 다음 *HelloController.java* 원본 파일을 *\src\main\java\com\example\hellospring\* 폴더에 추가합니다.

   ```java
   package com.example.hellospring;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.web.bind.annotation.RequestMapping;

   @RestController

   public class HelloController {

   @RequestMapping("/")

     public String index() {
         return "Greetings from Azure Spring Cloud!";
     }

   }
   ```

4. *.jar* 파일을 빌드합니다.

   ```azurecli
   mvn clean package -DskipTests
   ```

5. Azure Spring Cloud 인스턴스에서 앱을 만듭니다.

   ```azurecli
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
   ```

6. Azure Spring Cloud에 앱을 배포합니다.

   ```azurecli
   az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```

7. 스테이징 배포의 코드를 수정합니다.

   ```java
   package com.example.hellospring;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.web.bind.annotation.RequestMapping;

   @RestController

   public class HelloController {

   @RequestMapping("/")

     public String index() {
         return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT";
     }

   }
   ```

8. *.jar* 파일을 다시 빌드합니다.

   ```azurecli
   mvn clean package -DskipTests
   ```

9. 녹색 배포를 만듭니다.

   ```azurecli
   az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```

## <a name="view-apps-and-deployments"></a>앱 및 배포 보기

다음 프로시저를 통해 배포된 앱을 봅니다.

1. Azure Portal에서 Azure Spring Cloud 인스턴스로 이동합니다.

1. 왼쪽 창에서 **앱** 창을 열어 서비스 인스턴스에 대한 앱을 봅니다.

   ![앱 열기 창의 스크린샷.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. 앱을 선택하고 세부 정보를 볼 수 있습니다.

   ![앱에 대한 세부 정보의 스크린샷.](media/spring-cloud-blue-green-staging/app-overview.png)

1. **배포** 를 열어 앱의 모든 배포를 확인합니다. 그리드는 프로덕션 및 스테이징 배포를 모두 표시합니다.

   ![나열된 앱 배포를 표시하는 스크린샷.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. URL을 선택하여 현재 배포된 애플리케이션을 엽니다.

   ![배포된 애플리케이션에 대한 URL을 보여 주는 스크린샷.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. **프로덕션** 을 **상태** 열에서 선택하여 기본 앱을 표시합니다.

   ![기본 앱에 대한 URL을 보여 주는 스크린샷.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. **준비** 를 **상태** 열에서 선택하여 스테이징 앱을 확인합니다.

   ![스테이징 앱에 대한 URL을 보여 주는 스크린샷.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * 테스트 엔드포인트가 슬래시(/)로 끝나는지 확인하여 CSS 파일이 올바르게 로드되는지 확인합니다.
> * 브라우저에서 페이지를 보기 위해 로그인 자격 증명을 입력해야 하는 경우, [URL 디코드](https://www.urldecoder.org/)를 사용하여 테스트 엔드포인트를 디코드합니다. URL 디코딩은 URL을 *https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green* 형식으로 반환합니다. 이 형식을 사용하여 엔드포인트에 액세스합니다.

>[!NOTE]
> 구성 서버 설정은 스테이징 환경과 프로덕션 환경에 모두 적용됩니다. 예를 들어, 구성 서버의 앱 게이트웨이에 대한 컨텍스트 경로(*server.servlet.context-path*)를 *somepath* 로 설정했다면, 녹색 배포로 가는 경로가 *https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...* 로 변경됩니다.

이 지점에서 공개 주소 앱 게이트웨이를 방문하면 새로운 변경 없이 이전 페이지가 표시됩니다.

## <a name="set-the-green-deployment-as-the-production-environment"></a>녹색 배포를 프로덕션 환경으로 설정

1. 스테이징 환경에서 변경 내용을 확인한 후 프로덕션 환경으로 푸시할 수 있습니다. **앱** > **배포** 페이지에서 현재 **프로덕션** 중인 애플리케이션을 선택합니다.

1. 녹색 배포의 **등록 상태** 뒤에 있는 줄임표를 선택하고 **프로덕션으로 설정** 을 선택합니다.

   ![스테이징 빌드를 프로덕션으로 설정하기 위한 선택 영역을 표시하는 스크린샷.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. 앱의 URL에 변경 내용이 표시되는지 확인합니다.

   ![현재 프로덕션 환경에서 앱의 URL을 보여 주는 스크린샷.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> 녹색 배포를 프로덕션 환경으로 설정한 후에는 이전 배포가 스테이징 배포가 됩니다.

## <a name="modify-the-staging-deployment"></a>스테이징 배포 수정

변경에 만족하지 않는 경우 애플리케이션 코드를 수정하고, 새 .jar 패키지를 빌드한 후, Azure CLI를 사용하여 녹색 배포에 업로드할 수 있습니다.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>스테이징 배포 삭제

Azure Portal에서 스테이징 배포를 삭제하려면 스테이징 배포에 대한 페이지로 이동하여 **삭제** 단추를 선택합니다.

또는 다음 명령을 실행하여 Azure CLI에서 스테이징 배포를 삭제합니다.

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud에 대한 CI/CD](./how-to-cicd.md?pivots=programming-language-java)
