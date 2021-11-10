---
title: Azure Spring Cloud에서 Config Server 인스턴스 설정
description: Azure Portal에서 Azure Spring Cloud용 Spring Cloud Config Server 인스턴스를 설정하는 방법을 알아봅니다.
ms.service: spring-cloud
ms.topic: how-to
ms.author: karler
author: karlerickson
ms.date: 10/18/2019
ms.custom: devx-track-java
ms.openlocfilehash: 00411cf37a6f2728d3f8d1d0565e1d836b839192
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135408"
---
# <a name="set-up-a-spring-cloud-config-server-instance-for-your-service"></a>서비스용 Spring Cloud Config 서버 인스턴스 설정

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

이 문서에서는 Spring Cloud Config 서버 인스턴스를 Azure Spring Cloud 서비스에 연결하는 방법을 보여 줍니다.

Spring Cloud Config는 분산 시스템에서 구체화된 구성에 대한 서버 및 클라이언트 쪽 지원을 제공합니다. Config 서버 인스턴스를 사용하면 모든 환경에서 애플리케이션에 대한 외부 속성을 관리할 수 있는 중앙 위치가 있습니다. 자세한 내용은 [Spring Cloud Config 서버 참고 자료](https://spring.io/projects/spring-cloud-config)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 이미 프로비저닝되어 실행되는 Azure Spring Cloud 서비스. Azure Spring Cloud 서비스를 설정하고 시작하려면 [빠른 시작: Azure CLI를 사용하여 Java Spring 애플리케이션 시작](./quickstart.md)을 참조하세요.

## <a name="restriction"></a>제한

Git 백 엔드에서 Config 서버를 사용하는 경우 몇 가지 제한 사항이 있습니다. Config 서버 및 서비스 검색에 액세스하기 위해 일부 속성이 애플리케이션 환경에 자동으로 삽입됩니다. 또한 Config 서버 파일에서 이러한 속성을 구성하면 충돌 및 예기치 않은 동작이 발생할 수 있습니다. 속성은 다음과 같습니다.

```yaml
eureka.client.service-url.defaultZone
eureka.client.tls.keystore
server.port
spring.cloud.config.tls.keystore
spring.application.name
spring.jmx.enabled
```

> [!CAUTION]
> 위의 속성을 Config 서버 애플리케이션 파일에 배치하지 _않는_ 것이 좋습니다.

## <a name="create-your-config-server-files"></a>Config 서버 파일 만들기

Azure Spring Cloud는 Config 서버 파일을 저장하기 위해 Azure DevOps, GitHub, GitLab 및 Bitbucket을 지원합니다. 리포지토리가 준비되면 다음 지침에 따라 구성 파일을 만들어 이 리포지토리에 저장합니다.

또한 일부 구성 가능한 속성은 특정 유형에서만 사용할 수 있습니다. 다음 하위 섹션에는 각 리포지토리 유형의 속성이 나와 있습니다.

### <a name="public-repository"></a>퍼블릭 리포지토리

퍼블릭 리포지토리를 사용하는 경우 구성 가능한 속성이 더욱 제한됩니다.

퍼블릭 Git 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 다음 표에 나와 있습니다.

> [!NOTE]
> 현재 지원되는 유일한 명명 규칙은 하이픈(-)을 사용하여 단어를 구분하는 것입니다. 예를 들어 *default-label* 은 사용할 수 있지만, *defaultLabel* 은 사용할 수 없습니다.

| 속성        | 필수 | 기능                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | 예    | Config 서버 백 엔드로 사용되는 Git 리포지토리의 URI는 *http://* , *https://* , *git@* 또는 *ssh://* 로 시작합니다. |
| `default-label` | 예     | Git 리포지토리의 기본 레이블은 리포지토리의 *분기 이름*, *태그 이름* 또는 *commit-id* 여야 합니다. |
| `search-paths`  | 예     | Git 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |

------

### <a name="private-repository-with-ssh-authentication"></a>SSH 인증을 사용하는 프라이빗 리포지토리

SSH를 사용하는 프라이빗 Git 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 다음 표에 나와 있습니다.

> [!NOTE]
> 현재 지원되는 유일한 명명 규칙은 하이픈(-)을 사용하여 단어를 구분하는 것입니다. 예를 들어 *default-label* 은 사용할 수 있지만, *defaultLabel* 은 사용할 수 없습니다.

| 속성                   | 필수 | 기능                                                      |
| :------------------------- | -------- | ------------------------------------------------------------ |
| `uri`                      | 예    | Config 서버 백 엔드로 사용되는 Git 리포지토리의 URI는 *http://* , *https://* , *git@* 또는 *ssh://* 로 시작해야 합니다. |
| `default-label`            | 예     | Git 리포지토리의 기본 레이블은 리포지토리의 *분기 이름*, *태그 이름* 또는 *commit-id* 여야 합니다. |
| `search-paths`             | 예     | Git 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `private-key`              | 예     | Git 리포지토리에 액세스하기 위한 SSH 프라이빗 키이며, URI가 *git@* 또는 *ssh://* 로 시작하는 경우에 _필요합니다_. |
| `host-key`                 | 예     | Git 리포지토리 서버의 호스트 키이며, `host-key-algorithm`에 포함된 알고리즘 접두사를 포함하지 않아야 합니다. |
| `host-key-algorithm`       | 예     | 호스트 키 알고리즘은 *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384* 또는 *ecdsa-sha2-nistp521* 이어야 합니다. `host-key`가 있는 경우에만 *필요합니다*. |
| `strict-host-key-checking` | 예     | 프라이빗 `host-key`를 활용하는 경우 Config 서버 인스턴스가 시작되지 않는지 여부를 나타냅니다. *true*(기본값) 또는 *false* 여야 합니다. |

> [!NOTE]
> Config Server는 지정되지 않은 경우 `master`(om Git 자체)를 기본 레이블로 사용합니다. 그러나 GitHub는 최근 기본 분기를 `master`에서 `main`으로 변경했습니다. Azure Spring Cloud Config Server 오류를 방지하려면, 특히 새로 만든 리포지토리의 경우 GitHub를 사용하여 Config Server를 설정할 때 기본 레이블에 주의를 기울여야 합니다.

-----

### <a name="private-repository-with-basic-authentication"></a>기본 인증을 사용하는 프라이빗 리포지토리

기본 인증을 사용하는 프라이빗 Git 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 현재 지원되는 유일한 명명 규칙은 하이픈(-)을 사용하여 단어를 구분하는 것입니다. 예를 들어 *default-label* 은 사용할 수 있지만, *defaultLabel* 은 사용할 수 없습니다.

| 속성        | 필수 | 기능                                                      |
| :-------------- | -------- | ------------------------------------------------------------ |
| `uri`           | 예    | Config 서버 백 엔드로 사용되는 Git 리포지토리의 URI는 *http://* , *https://* , *git@* 또는 *ssh://* 로 시작해야 합니다. |
| `default-label` | 예     | Git 리포지토리의 기본 레이블은 리포지토리의 *분기 이름*, *태그 이름* 또는 *commit-id* 여야 합니다. |
| `search-paths`  | 예     | Git 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `username`      | 예     | Git 리포지토리 서버에 액세스하는 데 사용되는 사용자 이름이며, Git 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 경우에 _필요합니다_. |
| `password`      | 예     | Git 리포지토리 서버에 액세스하는 데 사용되는 암호 또는 개인용 액세스 토큰이며, Git 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 경우에 _필요합니다_. |

> [!NOTE]
> 많은 `Git` 리포지토리 서버에서는 암호 대신 토큰을 HTTP 기본 인증에 사용하도록 지원합니다. 일부 리포지토리에서는 토큰을 무기한으로 유지할 수 있습니다. 그러나 Azure DevOps Server를 비롯한 일부 Git 리포지토리 서버는 몇 시간 안에 토큰을 강제로 만료시킵니다. 토큰이 만료되는 리포지토리는 Azure Spring Cloud에서 토큰 기반 인증을 사용하면 안 됩니다.
> Github에서 암호 인증 지원이 제거되었으므로 Github에 암호 인증 대신 개인 액세스 토큰을 사용해야 합니다. 자세한 내용은 [토큰 인증](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)을 참조하세요.

### <a name="git-additional-repositories"></a>Git 추가 리포지토리

패턴을 사용하는 Git 리포지토리를 설정하는 데 사용되는 모든 구성 가능한 속성은 아래에 나와 있습니다.

> [!NOTE]
> 현재 지원되는 유일한 명명 규칙은 하이픈(-)을 사용하여 단어를 구분하는 것입니다. 예를 들어 *default-label* 은 사용할 수 있지만, *defaultLabel* 은 사용할 수 없습니다.

| 속성                           | 필수         | 기능                                                      |
| :--------------------------------- | ---------------- | ------------------------------------------------------------ |
| `repos`                            | 예             | 지정된 이름이 있는 Git 리포지토리의 설정으로 구성된 맵입니다. |
| `repos."uri"`                      | `repos`가 있는 경우 예 | Config 서버 백 엔드로 사용되는 Git 리포지토리의 URI는 *http://* , *https://* , *git@* 또는 *ssh://* 로 시작해야 합니다. |
| `repos."name"`                     | `repos`가 있는 경우 예 | Git 리포지토리를 식별하는 이름이며, `repos`가 있는 경우에만 _필요합니다_. 예를 들어 *team-A*, *team-B* 입니다. |
| `repos."pattern"`                  | 예             | 애플리케이션 이름을 매칭하는 데 사용되는 문자열 배열입니다. 각 패턴의 경우 와일드 카드가 포함된 `{application}/{profile}` 형식을 사용합니다. |
| `repos."default-label"`            | 예             | Git 리포지토리의 기본 레이블은 리포지토리의 *분기 이름*, *태그 이름* 또는 *commit-id* 여야 합니다. |
| `repos."search-paths`"             | 예             | Git 리포지토리의 하위 디렉터리를 검색하는 데 사용되는 문자열 배열입니다. |
| `repos."username"`                 | 예             | Git 리포지토리 서버에 액세스하는 데 사용되는 사용자 이름이며, Git 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 경우에 _필요합니다_. |
| `repos."password"`                 | 예             | Git 리포지토리 서버에 액세스하는 데 사용되는 암호 또는 개인용 액세스 토큰이며, Git 리포지토리 서버에서 `Http Basic Authentication`을 지원하는 경우에 _필요합니다_. |
| `repos."private-key"`              | 예             | Git 리포지토리에 액세스하기 위한 SSH 프라이빗 키이며, URI가 *git@* 또는 *ssh://* 로 시작하는 경우에 _필요합니다_. |
| `repos."host-key"`                 | 예             | Git 리포지토리 서버의 호스트 키이며, `host-key-algorithm`에 포함된 알고리즘 접두사를 포함하지 않아야 합니다. |
| `repos."host-key-algorithm"`       | 예             | 호스트 키 알고리즘은 *ssh-dss*, *ssh-rsa*, *ecdsa-sha2-nistp256*, *ecdsa-sha2-nistp384* 또는 *ecdsa-sha2-nistp521* 이어야 합니다. `host-key`가 있는 경우에만 *필요합니다*. |
| `repos."strict-host-key-checking"` | 예             | 프라이빗 `host-key`를 활용하는 경우 Config 서버 인스턴스가 시작되지 않는지 여부를 나타냅니다. *true*(기본값) 또는 *false* 여야 합니다. |

다음 표에서는 **추가 리포지토리** 섹션에 대한 몇 가지 예를 보여줍니다. 자세한 내용은 Spring 설명서의 [패턴 일치 및 다중 리포지토리를](https://cloud.spring.io/spring-cloud-config/reference/html/#_pattern_matching_and_multiple_repositories) 참조하세요.

| 패턴                        | Description |
| :------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| *test-config-server-app-0/\**   | 패턴 및 리포지토리 URI는 이름이 인 Spring Boot `test-config-server-app-0` 애플리케이션과 모든 프로필과 일치합니다.  |
| *test-config-server-app-1/dev*  | 패턴 및 리포지토리 URI는 dev 프로필이 있는 라는 Spring Boot `test-config-server-app-1` 애플리케이션과 일치합니다.  |
| *test-config-server-app-2/prod* | 패턴 및 리포지토리 URI는 prod 프로필이 있는 라는 Spring Boot `test-config-server-app-2` 애플리케이션과 일치합니다. |

:::image type="content" source="media/spring-cloud-tutorial-config-server/additional-repositories.png" lightbox="media/spring-cloud-tutorial-config-server/additional-repositories.png" alt-text="'추가 리포지토리' 테이블의 패턴 열이 강조 표시된 Config Server 페이지를 보여주는 Azure Portal 스크린샷":::

## <a name="attach-your-config-server-repository-to-azure-spring-cloud"></a>Azure Spring Cloud에 Config 서버 리포지토리 연결

이제 구성 파일이 리포지토리에 저장되었으므로 Azure Spring Cloud를 연결해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Spring Cloud **개요** 페이지로 이동합니다.

3. 왼쪽 탐색 창에서 **Config Server** 를 선택합니다.

4. **기본 리포지토리** 섹션에서 **URI** 를 "https://github.com/Azure-Samples/piggymetrics-config"로 설정합니다.

5. **유효성 검사** 를 선택합니다.

    ![구성 서버로 이동](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

6. 유효성 검사가 완료되면 **적용** 을 선택하여 변경 내용을 저장합니다.

    ![구성 서버 유효성 검사](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

7. 구성을 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.

    ![구성 서버 업데이트](media/spring-cloud-quickstart-launch-app-portal/updating-config.png)

8. 구성이 완료되면 알림을 받게 됩니다.

### <a name="enter-repository-information-directly-to-the-azure-portal"></a>Azure Portal에 리포지토리 정보 직접 입력

#### <a name="default-repository"></a>기본 리포지토리

* **퍼블릭 리포지토리**: **기본 리포지토리** 섹션의 **Uri** 상자에서 리포지토리 URI를 붙여넣습니다.  **레이블** 을 **config** 로 설정합니다. **인증** 설정이 **퍼블릭** 인지 확인한 다음, **적용** 을 선택하여 완료합니다.

* **프라이빗 리포지토리**: Azure Spring Cloud는 기본 암호/토큰 기반 인증 및 SSH를 지원합니다.

    * **기본 인증**: **기본 리포지토리** 섹션의 **Uri** 상자에서 리포지토리 URI를 붙여넣은 다음, **인증**("연필" 아이콘) 단추를 선택합니다. **인증 편집** 창의 **인증 유형** 드롭다운 목록에서 **HTTP Basic** 을 선택한 다음, 사용자 이름과 암호/토큰을 입력하여 Azure Spring Cloud에 대한 액세스 권한을 부여합니다. **확인** 을 선택한 다음, **적용** 을 클릭하여 Config 서버 인스턴스 설정을 완료합니다.

    ![인증 편집 창 기본 인증](media/spring-cloud-tutorial-config-server/basic-auth.png)

    > [!CAUTION]
    > 일부 리포지토리 서버에서는 암호와 같은 *personal-token* 또는 *access-token* 을 **기본 인증** 에 사용합니다. 이러한 종류의 토큰은 만료되지 않으므로 Azure Spring Cloud에서 암호로 사용할 수 있습니다. 그러나 다른 Git 리포지토리 서버(예: Bitbucket 및 Azure DevOps Server)의 경우 *access-token* 은 1~2시간 후에 만료됩니다. 즉, Azure Spring Cloud에서 이러한 리포지토리 서버를 사용하는 경우에는 이 옵션을 사용할 수 없습니다.
    > Github에서 암호 인증 지원이 제거되었으므로 Github에 암호 인증 대신 개인 액세스 토큰을 사용해야 합니다. 자세한 내용은 [토큰 인증](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)을 참조하세요.

    * **SSH**: **기본 리포지토리** 섹션의 **Uri** 상자에서 리포지토리 URI를 붙여넣은 다음, **인증**("연필" 아이콘) 단추를 선택합니다. **인증 편집** 창의 **인증 유형** 드롭다운 목록에서 **SSH** 를 선택한 다음, **프라이빗 키** 를 입력합니다. 필요에 따라 **호스트 키** 및 **호스트 키 알고리즘** 을 지정합니다. Config 서버 리포지토리에 공개 키를 포함해야 합니다. **확인** 을 선택한 다음, **적용** 을 클릭하여 Config 서버 인스턴스 설정을 완료합니다.

    ![인증 편집 창 SSH 인증](media/spring-cloud-tutorial-config-server/ssh-auth.png)

#### <a name="additional-repositories"></a>추가 리포지토리

선택적 **추가 리포지토리를** 사용하여 서비스를 구성하려면 **기본 리포지토리** 와 동일한 방식으로 **URI** 및 **인증을** 지정합니다. 패턴에 대한 **이름** 을 포함한 다음, **적용** 을 선택하여 인스턴스에 연결해야 합니다.

### <a name="enter-repository-information-into-a-yaml-file"></a>YAML 파일에 리포지토리 정보 입력

리포지토리 설정을 사용하여 YAML 파일을 작성한 경우 파일을 로컬 머신에서 Azure Spring Cloud로 직접 가져올 수 있습니다. 기본 인증을 사용하는 프라이빗 리포지토리의 간단한 YAML 파일은 다음과 같습니다.

```yaml
spring:
    cloud:
        config:
            server:
                git:
                    uri: https://github.com/azure-spring-cloud-samples/config-server-repository.git
                    username: <username>
                    password: <password/token>

```

**설정 가져오기** 단추를 선택한 다음, 프로젝트 디렉터리에서 YAML 파일을 선택합니다. **가져오기** 를 선택합니다. 그러면 **알림** 에서 `async` 작업이 팝업됩니다. 1-2분 후 성공 여부가 보고됩니다.

![Config 서버 알림 창](media/spring-cloud-tutorial-config-server/local-yml-success.png)

YAML 파일의 정보가 Azure Portal에 표시됩니다. **적용** 을 선택하여 완료합니다.

## <a name="using-azure-repos-for-azure-spring-cloud-configuration"></a>Azure Spring Cloud 구성에 Azure Repos 사용

Azure Spring Cloud는 퍼블릭 Git 리포지토리, SSH로 보호된 Git 리포지토리 또는 HTTP 기본 인증을 사용하여 보호된 Git 리포지토리에 액세스할 수 있습니다. Azure Repos를 사용하여 쉽게 만들고 관리할 수 있으므로 이 마지막 옵션을 사용해보겠습니다.

### <a name="get-repo-url-and-credentials"></a>리포지토리 URL 및 자격 증명 가져오기

1. 프로젝트의 Azure Repos 포털에서 **복제** 단추를 선택합니다.

    ![복제 단추](media/spring-cloud-tutorial-config-server/clone-button.png)

1. 텍스트 상자에서 복제 URL을 복사합니다. 이 URL은 일반적으로 다음과 같은 형식입니다.

    ```text
    https://<organization name>@dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    `@`을(를) 포함하여 `https://` 이후와 `dev.azure.com` 이전 버전을 모두 제거합니다. 결과 URL의 형식은 다음과 같아야 합니다.

    ```text
    https://dev.azure.com/<organization name>/<project name>/_git/<repository name>
    ```

    다음 섹션에서 사용하기 위해 이 URL을 저장합니다.

1. **Git 자격 증명 생성** 을 선택합니다. 사용자 이름 및 암호가 표시됩니다. 다음 섹션에서 사용하기 위해 저장합니다.

### <a name="configure-azure-spring-cloud-to-access-the-git-repository"></a>Git 리포지토리에 액세스하도록 Azure Spring Cloud 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Spring Cloud **개요** 페이지로 이동합니다.

1. 구성할 서비스를 선택합니다.

1. 서비스 페이지에 있는 왼쪽 창의 **설정** 아래에서 **Config 서버** 탭을 선택합니다. 이전에 만든 리포지토리를 구성합니다.

   - 이전 섹션에서 저장한 리포지토리 URL을 추가합니다.
   - **인증** 을 선택하고 **HTTP 기본** 을 선택합니다.
   - __사용자 이름__ 은 이전 섹션에서 저장한 사용자 이름입니다.
   - __암호__ 는 이전 섹션에서 저장한 암호입니다.
   - **적용** 을 선택하고 작업이 성공할 때까지 기다립니다.

   ![Spring Cloud 구성 서버](media/spring-cloud-tutorial-config-server/config-server-azure-repos.png)

## <a name="delete-your-configuration"></a>구성 삭제

**Config Server** 탭에 표시되는 **다시 설정** 단추를 선택하여 기존 설정을 완전히 지울 수 있습니다. GitHub에서 Azure DevOps로 이동하는 것처럼 Config Server 인스턴스를 다른 원본에 연결하려면 Config Server 설정을 삭제합니다.

## <a name="config-server-refresh"></a>Config Server 새로 고침

속성이 변경되면 변경 내용이 적용되기 전에 해당 속성을 사용하는 서비스에 알려야 합니다. Spring Cloud Config의 기본 솔루션은 [새로 고침 이벤트](https://spring.io/guides/gs/centralized-configuration/)를 수동으로 트리거하는 것으로, 많은 앱 인스턴스가 있는 경우에는 적합하지 않을 수 있습니다. 또는 Azure Spring Cloud에서 구성 클라이언트가 내부 새로 고침을 기반으로 변경 내용을 폴링하도록 하여 Config Server에서 값을 자동으로 새로 고칠 수 있습니다.

- 먼저 pom.xml의 종속성 섹션에 spring-cloud-starter-azure-spring-cloud-client를 포함합니다.

  ```xml
  <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
      <version>2.4.0</version>
  </dependency>
  ```

- 그런 다음, 자동 새로 고침을 사용하도록 설정하고 application.yml에서 적절한 새로 고침 간격을 설정합니다. 이 예제에서 클라이언트는 새로 고침 간격에 대해 설정할 수 있는 최소값인 5초마다 구성 변경 내용을 폴링합니다.
기본적으로 자동 새로 고침은 false로 설정되고 새로 고침 간격은 60초로 설정됩니다.

  ```yml
  spring:
    cloud:
      config:
        auto-refresh: true
        refresh-interval: 5
  ```

- 마지막으로 코드에 @refreshScope를 추가합니다. 이 예제에서는 변수 connectTimeout이 5초마다 자동으로 새로 고쳐집니다.

  ```java
  @RestController
  @RefreshScope
  public class HelloController {
      @Value("${timeout:4000}")
      private String connectTimeout;
  }
  ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Spring Cloud Config Server 인스턴스를 사용하도록 설정하고 구성하는 방법을 알아보았습니다. 애플리케이션을 관리하는 방법에 대한 자세한 내용은 [Azure Spring Cloud의 애플리케이션 크기 조정](./how-to-scale-manual.md)을 참조하세요.
