---
title: Azure Red Hat OpenShift 4 클러스터에서 Open Liberty/WebSphere Liberty로 Java 애플리케이션 배포
description: Azure Red Hat OpenShift 4 클러스터에서 Open Liberty/WebSphere Liberty로 Java 애플리케이션을 배포합니다.
author: majguo
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, microprofile, open-liberty, websphere-liberty, aro, openshift, red hat
ms.custom: devx-track-java, devx-track-javaee, devx-track-javaee-liberty, devx-track-javaee-liberty-aro
ms.openlocfilehash: 3be80719c155e49a99ff7a3de1b7f9782f866a32
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004913"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 클러스터에서 Open Liberty/WebSphere Liberty로 Java 애플리케이션 배포

이 가이드는 Open Liberty/WebSphere Liberty 런타임에서 Java, Java EE, [Jakarta EE](https://jakarta.ee/), [MicroProfile](https://microprofile.io/) 애플리케이션을 실행한 다음 Open Liberty Operator를 사용하여 컨테이너화된 애플리케이션을 Azure Red Hat OpenShift (ARO) 4 클러스터에 배포하는 방법을 안내합니다. 이 문서는 Liberty 애플리케이션을 준비하고 애플리케이션 Docker 이미지를 빌드하여 ARO 4 클러스터에서 컨테이너화된 애플리케이션을 실행하는 과정을 안내합니다.  Open Liberty에 대한 자세한 내용은 [Open Liberty 프로젝트 페이지](https://openliberty.io/)를 참조하세요. IBM WebSphere Liberty에 대한 자세한 내용은 [WebSphere Liberty 제품 페이지](https://www.ibm.com/cloud/websphere-liberty)를 참조하세요.

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 가이드를 성공적으로 진행하려면 다음 필수 구성 요소를 충족해야 합니다.

> [!NOTE]
> Azure Red Hat OpenShift에는 OpenShift 클러스터를 만들고 실행하는 데 최소 40개의 코어가 필요합니다. 새 Azure 구독에 대한 기본 Azure 리소스 할당량은 이 요구 사항을 충족하지 않습니다. 리소스 제한 늘리기를 요청하려면 [표준 할당량: VM 시리즈별 제한 늘리기](../azure-portal/supportability/per-vm-quota-requests.md)를 참조하세요. 무료 평가판 구독은 할당량 증가에 적합하지 않으므로 할당량 증가를 요청하기 전에 [종량제 구독으로 업그레이드](../cost-management-billing/manage/upgrade-azure-subscription.md)하세요.

1. Unix와 비슷한 운영 체제가 설치된 로컬 컴퓨터를 준비합니다(예: Ubuntu, macOS).
1. Java SE 구현을 설치합니다(예: [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. [Maven](https://maven.apache.org/download.cgi) 3.5.0 이상을 설치합니다.
1. 해당 OS용 [Docker](https://docs.docker.com/get-docker/)를 설치합니다.
1. [Azure CLI](/cli/azure/install-azure-cli)(2.0.75 이상)를 설치합니다.
1. 운영 체제에 사전 설치되지 않은 경우 [`envsubst`](https://command-not-found.com/envsubst)를 확인하고 설치합니다.
1. 로컬 시스템에서 이 샘플에 대한 코드를 복제합니다. 샘플은 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro)에 있습니다.
1. [Azure Red Hat OpenShift 4 클러스터 만들기](./tutorial-create-cluster.md)의 지침을 따릅니다.

   "Red Hat 끌어오기 암호 가져오기" 단계는 선택 사항이라고 레이블이 지정되어 있지만 **이 문서에서는 필수** 입니다.  끌어오기 암호를 사용하면 Azure Red Hat OpenShift 클러스터에서 Open Liberty Operator를 찾을 수 있습니다.

   클러스터에서 메모리를 많이 사용하는 애플리케이션을 실행하려는 경우 `--worker-vm-size` 매개 변수를 사용하여 작업자 노드에 대해 적절한 가상 컴퓨터 크기를 지정합니다. 예를 들어 `Standard_E4s_v3`는 클러스터에 Elasticsearch Operator를 설치하기 위한 최소 가상 머신 크기입니다. 자세한 내용은 다음을 참조하세요.

   * [클러스터를 만드는 Azure CLI](/cli/azure/aro#az_aro_create)
   * [최적화된 메모리에 지원되는 가상 머신 크기](./support-policies-v4.md#memory-optimized)
   * [Elasticsearch Operator를 설치하기 위한 필수 구성 요소](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. [Azure Red Hat OpenShift 4 클러스터에 연결](./tutorial-connect-cluster.md)의 단계를 수행하여 클러스터에 연결합니다.
   * 이 문서의 뒷부분에서 `oc` 명령을 사용하게 되니 “OpenShift CLI 설치”의 단계를 수행하세요.
   * `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`와 같은 클러스터 콘솔 URL을 기록해 둡니다.
   * `kubeadmin` 자격 증명을 기록해 둡니다.

1. 사용자 `kubeadmin`에 대한 토큰으로 OpenShift CLI에 로그인할 수 있는지 확인합니다.

### <a name="enable-the-built-in-container-registry-for-openshift"></a>OpenShift에 대한 기본 제공 컨테이너 레지스트리 사용

이 자습서 단계에서는 OpenShift에 액세스할 수 있는 컨테이너 레지스트리로 푸시해야 하는 Docker 이미지를 만듭니다. 가장 간단한 옵션은 OpenShift에서 제공하는 기본 제공 레지스트리를 사용하는 것입니다. 기본 제공 컨테이너 레지스트리를 사용하도록 설정하려면 [Azure Red Hat OpenShift 4에 대한 기본 제공 컨테이너 레지스트리 구성](built-in-container-registry.md)의 단계를 따르세요. 이 문서에서는 이러한 단계의 세 가지 항목을 사용합니다.

* OpenShift 웹 콘솔에 로그인하는 데 사용되는 Azure AD 사용자의 사용자 이름 및 암호입니다.
* OpenShift CLI에 로그인하는 단계를 수행한 후의 `oc whoami` 출력입니다.  이 값은 토론을 위한 **aad-user** 라고 합니다.
* 컨테이너 레지스트리 URL.

기본 제공 컨테이너 레지스트리를 사용하도록 설정하는 단계를 완료할 때 이러한 항목을 적어둡니다.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Java 앱에 대한 OpenShift 네임스페이스 만들기

1. `kubeadmin` 자격 증명을 사용하여 브라우저에서 OpenShift 웹 콘솔에 로그인합니다.
2. **관리** > **네임스페이스** > **네임스페이스 만들기** 로 이동합니다.
3. 다음에 표시된 대로 **이름** 에 `open-liberty-demo`를 입력하고 **만들기** 를 선택합니다.

   ![네임스페이스 만들기](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>데모 프로젝트에 대한 관리자 만들기

이미지 관리 외에도 **aad-user** 에게는 ARO 4 클러스터의 데모 프로젝트에서 리소스를 관리할 수 있는 관리 권한이 부여됩니다.  OpenShift CLI에 로그인하고 다음 단계를 수행하여 필요한 권한을 **aad-user** 에게 부여합니다.

1. `kubeadmin` 자격 증명을 사용하여 브라우저에서 OpenShift 웹 콘솔에 로그인합니다.
1. 웹 콘솔의 오른쪽 위에서 로그인한 사용자의 바로 가기 메뉴를 확장하고 **로그인 명령 복사** 를 선택합니다.
1. 필요한 경우 동일한 사용자로 새 탭 창에 로그인합니다.
1. **토큰 표시** 를 선택합니다.
1. 여기서 보이는 것처럼 **이 토큰을 사용하여 로그인** 아래에 나열된 값을 클립보드에 복사하고 셸에서 실행합니다.
1. 다음 명령을 실행하여 `open-liberty-demo` 네임스페이스에서 **aad-user** 에게 `admin` 역할을 부여합니다.

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Open Liberty OpenShift Operator 설치

클러스터를 만들고 연결한 후 Open Liberty Operator를 설치합니다.  Open Liberty Operator의 기본 시작 페이지는 [GitHub](https://github.com/OpenLiberty/open-liberty-operator)에 있습니다.

1. `kubeadmin` 자격 증명을 사용하여 브라우저에서 OpenShift 웹 콘솔에 로그인합니다.
2. **Operator** > **OperatorHub** 로 이동하고 **Open Liberty Operator** 를 검색합니다.
3. 검색 결과에서 **Open Liberty Operator** 를 선택합니다.
4. **설치** 를 선택합니다.
5. **Operator 구독 만들기** 팝업에서 **설치 모드** 로 **클러스터의 모든 네임스페이스(기본값)** , **업데이트 채널** 로 **베타**, **승인 전략** 으로 **자동** 을 선택합니다.

   ![Open Liberty Operator에 대한 Operator 구독 만들기](./media/howto-deploy-java-liberty-app/install-operator.png)
6. **구독** 을 선택하고 Open Liberty Operator가 표시될 때까지 1~2분 정도 기다립니다.
7. "Succeeded" 상태인 Open Liberty Operator를 관찰합니다.  관찰하지 않을 경우 계속하기 전에 문제를 진단하고 해결합니다.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Open Liberty가 설치되어 있음을 표시하는 설치된 Operator.":::

## <a name="prepare-the-liberty-application"></a>Liberty 애플리케이션 준비

이 가이드에서는 예제로 Java EE 8 애플리케이션을 사용합니다. Open Liberty는 [Java EE 8 전체 프로필](https://javaee.github.io/javaee-spec/javadocs/) 호환 서버이므로 애플리케이션을 쉽게 실행할 수 있습니다.  Open Liberty는 또한 [Jakarta EE 8 전체 프로필 호환](https://jakarta.ee/specifications/platform/8/apidocs/)입니다.

### <a name="run-the-application-on-open-liberty"></a>Open Liberty에서 애플리케이션 실행

Open Liberty에서 애플리케이션을 실행하려면 [Liberty Maven 플러그 인](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)에서 배포용 애플리케이션을 패키지할 수 있도록 Open Liberty 서버 구성 파일을 만들어야 합니다. OpenShift에 애플리케이션을 배포하는 데 Liberty Maven 플러그 인은 필요하지 않습니다.  그러나 본 예제에서는 Open Liberty의 개발자(dev) 모드를 통해 사용하도록 합니다.  개발자 모드를 사용하면 애플리케이션을 로컬로 쉽게 실행할 수 있습니다. 로컬 컴퓨터에서 다음 단계를 완료합니다.

1. `1-start/src/main/liberty/config`에 `2-simple/src/main/liberty/config/server.xml`을 복사하여 길이가 0인 기존 파일을 덮어씁니다. 이 `server.xml`은 Java EE 기능으로 Open Liberty 서버를 구성합니다.
1. `2-simple/pom.xml`을 `1-start/pom.xml`에 복사합니다.  이 단계는 `liberty-maven-plugin`을 POM에 추가합니다.
1. 디렉터리를 로컬 복제본의 `1-start`로 변경합니다.
1. 디렉터리 `./target`에서 war 패키지 `javaee-cafe.war`을 생성하려면 콘솔에서 `mvn clean package`를 실행합니다.
1. `mvn liberty:dev`를 실행하여 개발 모드에서 Open Liberty를 시작합니다.
1. 서버가 시작될 때까지 기다립니다. 콘솔 출력이 다음 메시지와 함께 종료됩니다.

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. 브라우저에서 `http://localhost:9080/`을 열어 애플리케이션 홈 페이지를 방문합니다. 애플리케이션은 다음 이미지와 유사합니다.

   ![JavaEE Cafe 웹 UI](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. **Ctrl + C** 를 눌러 애플리케이션과 Open Liberty 서버를 중지합니다.

로컬 클론의 `2-simple` 디렉터리에는 위의 변경 내용이 이미 적용된 Maven 프로젝트가 표시됩니다.

## <a name="prepare-the-application-image"></a>애플리케이션 이미지 준비

ARO 4 클러스터에서 Liberty 애플리케이션을 배포하고 실행하려면 [Open Liberty 컨테이너 이미지](https://github.com/OpenLiberty/ci.docker) 또는 [WebSphere Liberty 컨테이너 이미지](https://github.com/WASdev/ci.docker)를 사용하여 애플리케이션을 Docker 이미지로 컨테이너화합니다.

### <a name="build-application-image"></a>애플리케이션 이미지 빌드

다음 단계에 따라 애플리케이션 이미지를 빌드합니다.

1. 디렉터리를 로컬 복제본의 `2-simple`로 변경합니다.
2. `mvn clean package`를 실행하여 애플리케이션을 패키징합니다.
3. 다음 명령 중 하나를 실행하여 애플리케이션 이미지를 빌드합니다.
   * Open Liberty 베이스 이미지로 빌드:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * WebSphere Liberty 베이스 이미지로 빌드:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Docker로 로컬에서 애플리케이션 실행

원격 클러스터에 컨테이너화된 애플리케이션을 배포하기 전에 로컬 Docker로 실행하여 작동하는지 확인합니다.

1. 콘솔에서 `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0`을 실행합니다.
2. Liberty 서버가 시작되고 애플리케이션이 성공적으로 배포될 때까지 기다립니다.
3. 브라우저에서 `http://localhost:9080/`을 열어 애플리케이션 홈 페이지를 방문합니다.
4. **Ctrl + C** 를 눌러 애플리케이션과 Liberty 서버를 중지합니다.

### <a name="push-the-image-to-the-container-image-registry"></a>컨테이너 이미지 레지스트리에 이미지 푸시

애플리케이션의 상태에 만족하는 경우 아래 지침에 따라 기본 제공 컨테이너 이미지 레지스트리에 푸시합니다.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Azure AD 사용자로 OpenShift CLI에 로그인

1. Azure AD 사용자 자격 증명을 사용하여 브라우저에서 OpenShift 웹 콘솔에 로그인합니다.

   1. InPrivate, Incognito 또는 기타 유사한 브라우저 창 기능을 사용하여 콘솔에 로그인합니다.
   1. **openid** 선택

   > [!NOTE]
   > 여기에서 로그인하는 데 사용하는 사용자 이름과 암호를 기록해 둡니다. 이 사용자 이름과 암호는 이 문서와 다른 문서에서 다른 작업의 관리자 역할을 합니다.
1. 다음 단계를 따라 OpenShift CLI로 로그인합니다.  이 프로세스를 `oc login`라고 합니다.
   1. 웹 콘솔의 오른쪽 위에서 로그인한 사용자의 바로 가기 메뉴를 확장하고 **로그인 명령 복사** 를 선택합니다.
   1. 필요한 경우 동일한 사용자로 새 탭 창에 로그인합니다.
   1. **토큰 표시** 를 선택합니다.
   1. 여기서 보이는 것처럼 **이 토큰을 사용하여 로그인** 아래에 나열된 값을 클립보드에 복사하고 셸에서 실행합니다.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>OpenShift의 컨테이너 레지스트리에 컨테이너 이미지 푸시

다음 명령을 실행하여 OpenShift에 대한 컨테이너 레지스트리에 이미지를 푸시합니다.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

성공적인 출력은 다음과 유사합니다.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

다음 명령을 사용하여 기본 제공 컨테이너 이미지 레지스트리에 이미지를 푸시합니다.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>ARO 4 클러스터에 애플리케이션 배포

이제 필수 구성 요소를 통해 작업할 때, 앞서 만든 Azure Red Hat OpenShift 4 클러스터에 샘플 Liberty 애플리케이션을 배포할 수 있습니다.

### <a name="deploy-the-application-from-the-web-console"></a>웹 콘솔에서 애플리케이션 배포

Open Liberty Operator를 사용하여 Liberty 애플리케이션을 관리하기 때문에 "OpenLibertyApplication" 형식의 사용자 지정 리소스 정의 인스턴스를 만들어야 합니다. 그러면 Operator가 배포에 필요한 OpenShift 리소스를 관리하는 모든 양상을 처리합니다.

1. Azure AD 사용자의 자격 증명을 사용하여 브라우저에서 OpenShift 웹 콘솔에 로그인합니다.
1. **홈** 을 확장하고 **프로젝트** > **open-liberty-demo** 를 선택합니다.
1. **Operator** > **설치된 Operator** 로 이동합니다.
1. 페이지 중간에서 **Open Liberty Operator** 를 선택합니다.
1. 페이지 중간에서 **Open Liberty Application** 을 선택합니다.  사용자 인터페이스의 항목 탐색은 사용 중인 기술의 실제 포함 계층 구조를 미러링합니다.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java 포함](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. **OpenLibertyApplication 만들기** 를 선택합니다.
1. 생성된 yaml를 `<path-to-repo>/2-simple/openlibertyapplication.yaml`에 있는 사용자의 것으로 바꿉니다.
1. **만들기** 를 선택합니다. OpenLibertyApplications 목록으로 돌아갑니다.
1. **javaee-cafe-simple** 을 선택합니다.
1. 페이지 중간에서 **리소스** 를 선택합니다.
1. 테이블에서 **경로** 의 **종류** 로 **javaee-cafe-simple** 에 대한 링크를 선택합니다.
1. 페이지가 열리면 **위치** 아래에서 링크를 선택합니다.

애플리케이션 홈 페이지가 브라우저에서 열립니다.

### <a name="delete-the-application-from-the-web-console"></a>웹 콘솔에서 애플리케이션 삭제

애플리케이션을 다 쓴 후에는 다음 단계에 따라 Open Shift에서 애플리케이션을 삭제합니다.

1. 왼쪽 탐색 창에서 **Operator** 에 대한 항목을 확장합니다.
1. **설치된 Operator** 를 선택합니다.
1. **Open Liberty Operator** 를 선택합니다.
1. 페이지 중간에서 **Open Liberty Application** 을 선택합니다.
1. 세로 줄임표(세로로 쌓인 점 3개)를 선택한 다음 **OpenLiberty 애플리케이션 삭제** 를 선택합니다.

### <a name="deploy-the-application-from-cli"></a>CLI에서 애플리케이션 배포

웹 콘솔 GUI를 사용하는 대신 CLI에서 애플리케이션을 배포할 수 있습니다. 아직 배포하지 않은 경우에는 Red Hat 설명서 [CLI 시작](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)에 따라 `oc` 명령줄 도구를 다운로드하고 설치합니다.

1. Azure AD 사용자의 자격 증명을 사용하여 브라우저에서 OpenShift 웹 콘솔에 로그인합니다.
2. Azure AD 사용자에 대한 토큰을 사용하여 OpenShift CLI에 로그인합니다.
3. 로컬 클론의 디렉터리를 `2-simple`로 변경하고 다음 명령을 실행하여 Liberty 애플리케이션을 ARO 4 클러스터에 배포합니다.  명령 출력도 인라인으로 표시됩니다.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. 계속하기 전에 `READY` 열 아래에 `1/1`이 표시되는지 확인합니다.  표시되지 않을 경우 계속하기 전에 문제를 조사하고 해결합니다.
5. 다음과 같이 `oc get route` 명령으로 애플리케이션에 대한 경로 호스트를 검색합니다.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Liberty 애플리케이션이 실행되면 브라우저에서 **경로 호스트** 의 출력을 열어 애플리케이션 홈 페이지를 방문합니다.

### <a name="delete-the-application-from-cli"></a>CLI에서 애플리케이션 삭제

이 명령을 실행하여 CLI에서 애플리케이션을 삭제합니다.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>리소스 정리

[자습서: Azure Red Hat OpenShift 4 클러스터 삭제](./tutorial-delete-cluster.md)의 단계를 수행하여 ARO 클러스터를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서 다음을 수행하는 방법을 배웠습니다.
> [!div class="checklist"]
>
> * Liberty 애플리케이션 준비
> * 애플리케이션 이미지 빌드
> * GUI 및 CLI로 ARO 4 클러스터에서 컨테이너화된 애플리케이션 실행

이 가이드에 사용된 참조 문서에서 더 자세히 알아볼 수 있습니다.

* [Open Liberty](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Open Liberty 서버 구성](https://openliberty.io/docs/ref/config/)
* [Liberty Maven 플러그 인](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Open Liberty 컨테이너 이미지](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty 컨테이너 이미지](https://github.com/WASdev/ci.docker)
