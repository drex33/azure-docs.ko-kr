---
title: Azure 앱 컨테이너화 Java; Java 웹 애플리케이션을 컨테이너화하여 Azure App Service로 마이그레이션
description: '자습서: Java 웹 애플리케이션을 컨테이너화하여 Azure App Service로 마이그레이션'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: 768bcc39505671c452d99dc1d8d1d50ec3f3b9e5
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259643"
---
# <a name="java-web-app-containerization-and-migration-to-azure-app-service"></a>Java 웹앱을 컨테이너화하고 Azure App Service로 마이그레이션

이 문서에서는 Azure Migrate: 앱 컨테이너화 도구를 사용하여 Java 웹 애플리케이션(Apache Tomcat에서 실행)을 컨테이너화하고 [Azure App Service](https://azure.microsoft.com/services/app-service/)로 마이그레이션하는 방법을 알아봅니다. 컨테이너화 프로세스는 코드베이스에 대한 액세스가 필요하지 않으며 기존 애플리케이션을 컨테이너화하는 간편한 방법을 제공합니다. 이 도구는 서버에서 애플리케이션의 실행 상태를 사용하여 애플리케이션 구성 요소를 결정하고 컨테이너 이미지에 패키지하도록 돕습니다. 그런 다음, 컨테이너화된 애플리케이션을 Azure App Service에 배포할 수 있습니다.

Azure Migrate: 앱 컨테이너화 도구는 현재 다음과 같은 작업을 지원합니다.

- Apache Tomcat(Linux 서버에 있는)의 Java 웹앱을 컨테이너화하고 App Service의 Linux 컨테이너에 배포.
- Apache Tomcat(Linux 서버)의 Java 웹앱을 컨테이너화하여 AKS의 Linux 컨테이너에 배포. [자세히 알아보기](./tutorial-app-containerization-java-kubernetes.md)
- ASP.NET 앱을 컨테이너화하여 AKS의 Windows 컨테이너에 배포 [자세히 알아보기](./tutorial-app-containerization-aspnet-kubernetes.md)
- ASP.NET 앱을 컨테이너화하여 App Service의 Windows 컨테이너에 배포. [자세히 알아보기](./tutorial-app-containerization-aspnet-app-service.md)


Azure Migrate: 앱 컨테이너화 도구를 사용하면 다음 작업을 수행할 수 있습니다.

- **애플리케이션 검색**: 도구가 Java 웹 애플리케이션(Apache Tomcat에서 실행)을 실행하는 애플리케이션 서버에 원격으로 연결하여 애플리케이션 구성 요소를 검색합니다. 이 도구는 애플리케이션의 컨테이너 이미지를 만드는 데 사용할 수 있는 Dockerfile을 만듭니다.
- **컨테이너 이미지 빌드**: 애플리케이션 요구 사항에 따라 Dockerfile의 검사 및 추가 사용자 지정을 수행하고 이를 사용하여 애플리케이션 컨테이너 이미지를 빌드할 수 있습니다. 애플리케이션 컨테이너 이미지는 지정한 Azure Container Registry로 푸시됩니다.
- **Azure App Service에 배포**: 그러면 도구에서 컨테이너화된 애플리케이션을 Azure App Service에 배포하는 데 필요한 배포 파일을 생성합니다.

> [!NOTE]
> Azure Migrate: 앱 컨테이너화 도구를 사용하면 애플리케이션 서버에서 특정 애플리케이션 유형(Apache Tomcat의 ASP.NET 및 Java 웹앱) 및 해당 구성 요소를 검색할 수 있습니다. 온-프레미스 컴퓨터에서 실행되는 서버와 앱, 역할, 기능의 인벤토리를 검색하려면 Azure Migrate: 검색 및 평가 기능을 사용합니다. [자세히 알아보기](./tutorial-discover-vmware.md)

모든 애플리케이션을 상당한 재설계 없이 컨테이너로 직접 이동할 수 있는 것은 아니지만 기존 앱을 다시 작성하지 않고 컨테이너로 이동하면 다음과 같은 몇 가지 이점을 얻을 수 있습니다.

- **향상된 인프라 사용률:** 컨테이너를 사용하면 여러 애플리케이션이 리소스를 공유하고 동일한 인프라에서 호스트될 수 있습니다. 이를 통해 인프라를 통합하고 사용률을 향상할 수 있습니다.
- **간소화된 관리:** AKS, App Service 같은 최신 관리형 플랫폼에서 애플리케이션을 호스트하여 관리 방식을 간소화할 수 있습니다. 이는 일반적으로 소유한 인프라를 사용하여 수행하는 인프라 유지 관리 및 관리 프로세스를 사용 중지하거나 축소하는 방식으로 달성할 수 있습니다.
- **애플리케이션 이식성:** 컨테이너 사양 형식 및 플랫폼의 채택과 표준화가 증가하면서 애플리케이션 이식성은 더 이상 문제가 되지 않습니다.
- **DevOps를 통한 최신 관리 채택:** 관리, 보안 및 DevOps로의 전환에 대한 최신 사례를 채택하고 표준화하는 데 도움이 됩니다.


이 자습서에서는 다음과 같은 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure 계정을 설정합니다.
> * Azure Migrate: 앱 컨테이너화 도구를 설치합니다.
> * Java 웹 애플리케이션을 검색합니다.
> * 컨테이너 이미지를 빌드합니다.
> * 컨테이너화된 애플리케이션을 App Service에 배포합니다.

> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 다음을 수행해야 합니다.

**요구 사항** | **세부 정보**
--- | ---
**도구를 설치할 컴퓨터를 식별** | Azure Migrate: 앱 컨테이너화 도구를 설치하고 실행하는 Windows 컴퓨터입니다. Windows 컴퓨터는 서버(Windows Server 2016 이상) 또는 클라이언트(Windows 10) 운영 체제일 수 있습니다. 즉, 도구가 데스크톱에서도 실행될 수 있습니다. <br/><br/> 도구를 실행하는 Windows 컴퓨터에는 컨테이너화될 수 있는 Java 웹 애플리케이션을 호스팅하는 서버/가상 머신에 대한 네트워크 연결이 있어야 합니다.<br/><br/> 애플리케이션 아티팩트를 저장하기 위해 Azure Migrate: 앱 컨테이너화 도구를 실행하는 Windows 컴퓨터에서 6GB의 공간을 사용할 수 있어야 합니다. <br/><br/> Windows 컴퓨터에서 직접 또는 프록시를 통해 인터넷에 액세스할 수 있어야 합니다.
**애플리케이션 서버** | - 컨테이너화할 Java 애플리케이션을 실행하는 서버의 22 포트에서 SSH(Secure Shell) 연결을 사용하도록 설정합니다. <br/>
**Java 웹 애플리케이션** | 이 도구는 현재 다음을 지원합니다. <br/><br/> - Tomcat 8 이상에서 실행되는 애플리케이션<br/> - Ubuntu Linux 16.04/18.04/20.04, Debian 7/8, CentOS 6/7, Red Hat Enterprise Linux 5/6/7의 애플리케이션 서버 <br/> - Java 버전 7 이상을 사용하는 애플리케이션  <br/><br/> 이 도구는 현재 다음을 지원하지 않습니다. <br/><br/> - 여러 Tomcat 인스턴스를 실행하는 애플리케이션 서버 <br/>  


## <a name="prepare-an-azure-user-account"></a>Azure 사용자 계정 준비

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

구독을 설정한 후에는 다음을 포함하는 Azure 사용자 계정이 필요합니다.
- Azure 구독에 대한 소유자 권한
- Azure Active Directory 앱을 등록할 수 있는 권한

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다. 구독 소유자가 아닌 경우 다음과 같이 소유자와 협력하여 권한을 할당합니다.

1. Azure Portal에서 "구독"을 검색하여 **서비스** 에서 **구독** 을 선택합니다.

    ![Azure 구독을 검색하는 검색 상자](./media/tutorial-discover-vmware/search-subscription.png)

2. **구독** 페이지에서 Azure Migrate 프로젝트를 만들려는 구독을 선택합니다.
3. 구독에서 **액세스 제어(IAM)**  > **액세스 확인** 을 선택합니다.
4. **액세스 확인** 에서 관련 사용자 계정을 검색합니다.
5. **역할 할당 추가** 에서 **추가** 를 클릭합니다.

    ![사용자 계정을 검색하여 액세스 권한을 확인하고 역할을 할당합니다.](./media/tutorial-discover-vmware/azure-account-access.png)

6. **역할 할당 추가** 에서 소유자 역할을 선택하고, 계정(이 예제에서는 azmigrateuser)을 선택합니다. 그런 다음 **Save** 를 클릭합니다.

    ![계정에 역할을 할당하는 역할 할당 추가 페이지를 엽니다.](./media/tutorial-discover-vmware/assign-role.png)

7. 또한 Azure 계정에는 **Azure Active Directory 앱을 등록할 수 있는 권한** 이 필요합니다.
8. Azure Portal에서 **Azure Active Directory** > **사용자** > **사용자 설정** 으로 차례로 이동합니다.
9. **사용자 설정** 에서 Azure AD 사용자가 애플리케이션을 등록할 수 있는지 확인합니다(기본적으로 **예** 로 설정됨).

      ![사용자 설정에서 사용자가 Active Directory 앱을 등록할 수 있는지 확인합니다.](./media/tutorial-discover-vmware/register-apps.png)

10. '앱 등록' 설정이 '아니요'로 설정된 경우 테넌트/전역 관리자에게 필요한 권한을 할당하도록 요청합니다. 또는 테넌트/전역 관리자가 **애플리케이션 개발자** 역할을 계정에 할당하여 Azure Active Directory 앱 등록을 허용할 수 있습니다. [자세히 알아보기](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Azure Migrate: 앱 컨테이너화 도구 다운로드 및 설치

1. Windows 컴퓨터에서 Azure Migrate: 앱 컨테이너화 설치 프로그램을 [다운로드](https://go.microsoft.com/fwlink/?linkid=2134571)합니다.
2. 관리자 모드에서 PowerShell을 시작하고 PowerShell 디렉터리를 설치 프로그램이 포함된 폴더로 변경합니다.
3. 명령을 사용하여 설치 스크립트를 실행합니다.

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>앱 컨테이너화 도구 시작

1. 앱 컨테이너화 도구를 실행하는 Windows 컴퓨터에 연결할 수 있는 머신에서 브라우저를 열고 도구 URL **https://*머신 이름 또는 IP 주소*: 44369** 를 엽니다.

   또는 바탕 화면에서 앱 바로 가기를 선택하여 앱을 열 수 있습니다.

2. 연결이 비공개가 아님을 나타내는 경고가 표시되는 경우 고급을 클릭하고 웹 사이트로 이동하도록 선택합니다. 이 경고는 웹 인터페이스가 자체 서명된 TLS/SSL 인증서를 사용하기 때문에 표시되는 것입니다.
3. 로그인 화면에서 머신의 로컬 관리자 계정을 사용하여 로그인합니다.
4. 컨테이너화할 애플리케이션 유형으로 **Tomcat의 Java 웹앱** 을 선택합니다.
5. 대상 Azure 서비스를 지정하려면 **Azure App Service의 컨테이너** 를 선택합니다.
![앱 컨테이너화 도구에 대한 기본 로드입니다.](./media/tutorial-containerize-apps-aks/tool-home.png)

### <a name="complete-tool-pre-requisites"></a>전체 도구 필수 구성 요소
1. **사용 조건** 에 동의하고 타사 정보를 읽습니다.
6. 도구 웹앱 > **필수 구성 요소 설정** 에서 다음 단계를 수행합니다.
   - **연결**: 도구가 Windows 컴퓨터에서 인터넷에 액세스할 수 있는지 확인합니다. 컴퓨터에서 프록시를 사용하는 경우:
     - **프록시 설정** 을 클릭하여 프록시 주소(IP 주소 또는 FQDN 형식) 및 수신 대기 포트를 지정합니다.
     - 프록시에 인증이 필요한 경우 자격 증명을 지정합니다.
     - HTTP 프록시만 지원됩니다.
     - 프록시 세부 정보를 추가하거나 프록시 및/또는 인증을 사용하지 않도록 설정한 경우 **저장** 을 클릭하여 연결 확인을 다시 트리거합니다.
   - **업데이트 설치**: 도구가 자동으로 최신 업데이트를 확인하고 설치합니다. [여기](https://go.microsoft.com/fwlink/?linkid=2134571)에서 수동으로 최신 버전의 도구를 설치할 수도 있습니다.
   - **SSH(Secure Shell) 사용**: 도구가 컨테이너화할 Java 웹 애플리케이션을 실행하는 애플리케이션 서버에서 SSH(Secure Shell)를 사용하도록 설정했는지 알려줍니다.


## <a name="sign-in-to-azure"></a>Azure에 로그인

**로그인** 을 클릭하여 Azure 계정에 로그인합니다.

1. Azure로 인증하려면 디바이스 코드가 필요합니다. 로그인을 클릭하면 디바이스 코드가 포함된 모달이 열립니다.
2. **코드 복사 및 로그인** 을 클릭하여 디바이스 코드를 복사하고, 새 브라우저 탭에서 Azure 로그인 프롬프트를 엽니다. 프롬프트가 표시되지 않으면 브라우저에서 팝업 차단이 사용하지 않도록 설정되었는지 확인합니다.

    ![디바이스 코드를 보여주는 모달.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. 새 탭에서 디바이스 코드를 붙여넣고, Azure 계정 자격 증명을 사용하여 로그인을 완료합니다. 로그인이 완료되면 브라우저 탭을 닫고, 앱 컨테이너화 도구의 웹 인터페이스로 돌아갈 수 있습니다.
4. 사용할 **Azure 테넌트** 를 선택합니다.
5. 사용할 **Azure 구독** 을 지정합니다.

## <a name="discover-java-web-applications"></a>Java 웹 애플리케이션 검색

앱 컨테이너화 도우미 도구는 제공된 자격 증명을 사용하여 애플리케이션 서버에 원격으로 연결하고 애플리케이션 서버에서 호스트되는 Java 웹 애플리케이션(Apache Tomcat에서 실행)을 검색하려고 합니다.

1. 애플리케이션 검색을 위해 서버에 원격으로 연결하는 데 사용해야 하는 Java 웹 애플리케이션을 실행하는 서버의 **IP 주소/FQDN 및 자격 증명** 을 지정합니다.
    - 애플리케이션 서버의 루트 계정(Linux)에 대한 자격 증명을 제공해야 합니다.
    - 도메인 계정의 경우(사용자가 애플리케이션 서버의 관리자여야 함) *<domain\username>* 형식으로 사용자 이름 앞에 도메인 이름을 붙입니다.
    - 한 번에 최대 5대의 서버에서 애플리케이션 검색을 실행할 수 있습니다.

2. **유효성 검사** 를 클릭하여 도구를 실행하는 컴퓨터에서 애플리케이션 서버에 연결할 수 있는지, 자격 증명이 유효한지 확인합니다. 유효성 검사가 성공적으로 완료되면 상태 열에서 상태가 **매핑됨** 으로 표시됩니다.  

    ![서버 IP 및 자격 증명을 보여 주는 스크린샷.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. **계속** 을 클릭하여 선택한 애플리케이션 서버에서 애플리케이션 검색을 시작합니다.

4. 애플리케이션 검색이 성공적으로 완료되면 컨테이너화할 애플리케이션의 목록을 선택할 수 있습니다.

    ![검색된 Java 웹 애플리케이션을 보여 주는 스크린샷](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. 확인란을 사용하여 컨테이너화 애플리케이션을 선택합니다.
5. **컨테이너 이름 지정**: 선택한 애플리케이션 각각에 대한 대상 컨테이너의 이름을 지정합니다. 컨테이너 이름은 <*name:tag*>로 지정해야 합니다. 여기서 태그는 컨테이너 이미지에 사용됩니다. 예를 들어 대상 컨테이너 이름을 *appname:v1* 으로 지정할 수 있습니다.   

### <a name="parameterize-application-configurations"></a>애플리케이션 구성 매개 변수화
구성을 매개 변수화하여 배포 시간 매개 변수로 사용할 수 있습니다. 이렇게 하면 컨테이너 이미지의 특정 값으로 하드 코딩하는 대신 애플리케이션을 배포하는 동안 이 설정을 구성할 수 있습니다. 예를 들어 이 옵션은 데이터베이스 연결 문자열과 같은 매개 변수에 유용합니다.
1. **앱 구성** 을 클릭하여 검색된 구성을 검토합니다.
2. 검색된 애플리케이션 구성을 매개 변수화하는 확인란을 선택합니다.
3. 매개 변수화할 구성을 선택한 후 **적용** 을 클릭합니다.

   ![앱 구성 매개 변수화 Java 애플리케이션 스크린샷](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>파일 시스템 종속성 외부화

 애플리케이션이 사용하는 다른 폴더를 추가할 수 있습니다. 해당 폴더가 컨테이너 이미지의 일부여야 하는지, 아니면 Azure 파일 공유를 통해 영구 스토리지로 외부화되어야 하는지를 지정합니다. 외부 영구 스토리지를 사용하는 것은 상태를 컨테이너 외부에 저장하거나 다른 정적 콘텐츠가 파일 시스템에 저장되는 상태 저장 애플리케이션에 적합합니다.

1. 앱 폴더 아래에서 **편집** 을 클릭하여 검색된 애플리케이션 폴더를 검토합니다. 검색된 애플리케이션 폴더는 애플리케이션에 필요한 필수 아티팩트로 식별된 것이며 컨테이너 이미지에 복사됩니다.

2. **폴더 추가** 를 클릭하고 추가할 폴더 경로를 지정합니다.
3. 동일한 볼륨에 여러 폴더를 추가하려면 쉼표(`,`)로 구분된 값을 제공합니다.
4. 폴더를 영구 스토리지의 컨테이너 외부에 저장하려면 스토리지 옵션으로 **Azure 파일 공유** 를 선택합니다.
5. 애플리케이션 폴더를 검토한 후 **저장** 을 클릭합니다.
   ![앱 볼륨 스토리지 선택을 보여 주는 스크린샷.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. **계속** 을 클릭하여 컨테이너 이미지 빌드 단계로 이동합니다.

## <a name="build-container-image"></a>컨테이너 이미지 만들기


1. **Azure Container Registry 선택**: 드롭다운을 사용하여 앱의 컨테이너 이미지를 빌드하고 저장하는 데 사용할 [Azure Container Registry](../container-registry/index.yml)를 선택합니다. 기존 Azure Container Registry를 사용하거나 새 레지스트리 만들기 옵션을 사용하여 새로 만들도록 선택할 수 있습니다.

    ![앱 ACR 선택을 보여 주는 스크린샷.](./media/tutorial-containerize-apps-aks/build-java-app.png)

> [!NOTE]
> 관리 사용자가 사용하도록 설정된 Azure 컨테이너 레지스트리만 표시됩니다. 관리자 계정은 현재 이미지를 Azure 컨테이너 레지스트리에서 Azure App Service로 배포하는 데 필요합니다. [자세한 정보](../container-registry/container-registry-authentication.md#admin-account)

2. **Dockerfile 검토**: 선택한 각 애플리케이션의 컨테이너 이미지를 빌드하는 데 필요한 Dockerfile이 빌드 단계를 시작할 때 생성됩니다. **검토** 를 클릭하여 Dockerfile을 검토합니다. 검토 단계에서 Dockerfile에 필요한 사용자 지정 항목을 추가하고 변경 내용을 저장한 후에 빌드 프로세스를 시작할 수도 있습니다.

3. **Application Insights 구성**: 코드를 계측하지 않고 App Service에서 실행되는 Java 앱을 모니터링할 수 있습니다. 이 도구는 Java 독립 실행형 에이전트를 컨테이너 이미지의 일부로 설치합니다. 배포 중에 구성되면 Java 에이전트는 Application Insights로 모니터링하는 데 사용할 수 있는 애플리케이션의 다양한 요청, 종속성, 로그, 메트릭 등을 자동으로 수집합니다. 이 옵션은 모든 Java 애플리케이션에 대해 기본적으로 사용하도록 설정됩니다.  

4. **빌드 프로세스 트리거**: 이미지를 빌드할 애플리케이션을 선택하고 **빌드** 를 클릭합니다. 빌드를 클릭하면 각 애플리케이션에 대한 컨테이너 이미지 빌드가 시작됩니다. 이 도구는 빌드 상태를 지속적으로 모니터링하고 빌드가 성공적으로 완료되면 다음 단계를 진행하도록 합니다.

5. **빌드 상태 추적**: 상태 열에서 **빌드 진행 중** 링크를 클릭하여 빌드 단계의 진행률을 모니터링할 수도 있습니다. 빌드 프로세스를 트리거한 후 링크가 활성화하는 데 몇 분 정도 걸립니다.  

6. 빌드가 완료되면 **계속** 을 클릭하여 배포 설정을 지정합니다.

    ![앱 컨테이너 이미지 빌드 완료를 보여 주는 스크린샷.](./media/tutorial-containerize-apps-aks/build-java-app-completed.png)

## <a name="deploy-the-containerized-app-on-azure-app-service"></a>Azure App Service에 컨테이너화된 앱 배포

컨테이너 이미지가 빌드되면 다음 단계는 애플리케이션을 [Azure App Service](https://azure.microsoft.com/services/app-service/)의 컨테이너로 배포하는 것입니다.

1. **Azure App Service 요금제 선택**: 애플리케이션에서 사용해야 하는 Azure App Service 요금제를 지정합니다.

     - App Service 요금제가 없거나 사용할 새 App Service 요금제를 만들려는 경우 **새 App Service 요금제 만들기** 를 클릭하여 도구에서 만들도록 선택할 수 있습니다.      
     - App Service 요금제를 선택한 후 **계속** 을 클릭합니다.

2. **비밀 저장소 및 모니터링 작업 영역 지정**: 애플리케이션 구성을 매개 변수화하도록 선택한 경우 애플리케이션에 사용할 비밀 저장소를 지정합니다. 애플리케이션 비밀을 관리하기 위해 Azure Key Vault 또는 App Service 애플리케이션 설정을 선택할 수 있습니다. [자세한 정보](../app-service/configure-common.md#configure-connection-strings)

     - 비밀 관리를 위해 App Service 애플리케이션 설정을 선택한 경우 **계속** 을 클릭합니다.
     - Azure Key Vault를 사용하여 애플리케이션 비밀을 관리하려는 경우 사용할 Azure Key Vault를 지정합니다.     
         - Azure Key Vault가 없거나 새 Key Vault를 만들려는 경우 **새로 만들기** 를 클릭하여 도구에서 만들도록 선택할 수 있습니다.
         - 이 도구는 Key Vault를 통해 비밀을 관리하는 데 필요한 권한을 자동으로 할당합니다.
    - **모니터링 작업 영역**: Application Insights로 모니터링을 사용하도록 선택한 경우 사용할 Application Insights 리소스를 지정합니다. 모니터링 통합을 사용하지 않도록 설정한 경우에는 이 옵션이 표시되지 않습니다.
         - Application Insights 리소스가 없거나 새 리소스를 만들려는 경우 **새로 만들기** 를 클릭하여 도구에서 만들도록 선택할 수 있습니다.

3. **Azure 파일 공유 지정**: 디렉터리/폴더를 더 추가하고 영구 스토리지에 대해 Azure 파일 공유 옵션을 선택한 경우 배포 프로세스 중에 Azure Migrate: 앱 컨테이너화 도구에서 사용할 Azure 파일 공유를 지정합니다. 이 도구는 Azure Files에 대해 구성된 애플리케이션 디렉터리/폴더를 복사하고, 배포 중에 애플리케이션 컨테이너에 탑재합니다. 

     - Azure 파일 공유가 없거나 새 Azure 파일 공유를 만들려는 경우 **새 스토리지 계정 및 파일 공유 만들기** 를 클릭하여 도구에서 만들도록 선택할 수 있습니다.  

4. **애플리케이션 배포 구성**: 위의 단계를 완료한 후에는 애플리케이션에 대한 배포 구성을 지정해야 합니다. **구성** 을 클릭하여 애플리케이션 배포를 사용자 지정합니다. 구성 단계에서 다음과 같은 사용자 지정을 구성할 수 있습니다.
     - **이름**: 애플리케이션에 대한 고유한 앱 이름을 지정합니다. 이름은 애플리케이션 URL을 생성하는 데 사용되며, 이 배포의 일환으로 만드는 다른 리소스의 접두사로 사용됩니다.
     - **애플리케이션 구성**: 매개 변수화된 애플리케이션 구성에 대해 현재 배포에 사용할 값을 제공합니다.
     - **스토리지 구성**: 영구 스토리지로 구성된 모든 애플리케이션 디렉터리/폴더에 대한 정보를 검토합니다.

    ![배포 앱 구성을 보여 주는 스크린샷.](./media/tutorial-containerize-apps-aks/deploy-java-app-config.png)

5. **애플리케이션 배포**: 애플리케이션 배포 구성이 저장되면 도구는 애플리케이션에 대한 Kubernetes 배포 YAML을 생성합니다.
     - **검토** 를 클릭하여 애플리케이션에 대한 배포 구성을 검토합니다.
     - 배포할 애플리케이션을 선택합니다.
     - **배포** 를 클릭하여 선택한 애플리케이션의 배포를 시작합니다.

         ![앱 배포 구성을 보여 주는 스크린샷.](./media/tutorial-containerize-apps-aks/deploy-java-app-deploy.png)

     - 애플리케이션이 배포되면 ‘배포 상태’ 열을 클릭하여 애플리케이션에 대해 배포된 리소스를 추적할 수 있습니다.


## <a name="troubleshoot-issues"></a>문제 해결

도구와 관련된 문제를 해결하기 위해 앱 컨테이너화 도구를 실행하는 Windows 컴퓨터에서 로그 파일을 확인할 수 있습니다. 도구 로그 파일은 *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs* 폴더에 있습니다.

## <a name="next-steps"></a>다음 단계

- Apache Tomcat(Linux 서버에 있는)의 Java 웹앱을 컨테이너화하고 AKS의 Linux 컨테이너에 배포. [자세한 정보](./tutorial-app-containerization-java-kubernetes.md)
- ASP.NET 웹앱을 컨테이너화하고 AKS의 Windows 컨테이너에 배포. [자세한 정보](./tutorial-app-containerization-aspnet-kubernetes.md)
- ASP.NET 웹앱을 컨테이너화하고 Azure App Service의 Windows 컨테이너에 배포. [자세한 정보](./tutorial-app-containerization-aspnet-app-service.md)