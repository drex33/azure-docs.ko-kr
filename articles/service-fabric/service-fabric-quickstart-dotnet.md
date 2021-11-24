---
title: Azure의 Service Fabric에서 .NET 앱을 신속하게 만들기
description: 이 빠른 시작에서는 Service Fabric 안정적인 서비스 애플리케이션 예제를 사용하여 Azure용 .NET 애플리케이션을 만듭니다.
ms.topic: quickstart
ms.date: 06/26/2019
ms.custom: mvc, devcenter, vs-azure, mode-other
ms.openlocfilehash: dbda7681852f7afae7c7f75aa34b6f910dd95825
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133073558"
---
# <a name="quickstart-deploy-a-net-reliable-services-application-to-service-fabric"></a>빠른 시작: Service Fabric에 .NET 안정적인 서비스 애플리케이션 배포

Azure Service Fabric은 확장성 있고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다.

이 빠른 시작에서는 Service Fabric에 첫 번째 .NET 애플리케이션을 배포하는 방법을 보여 줍니다. 완료하면 투표 결과를 클러스터의 상태 저장 백 엔드 서비스에 저장하는 ASP.NET Core 웹 프런트 엔드가 있는 투표 애플리케이션이 생깁니다.

![애플리케이션 스크린샷](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

이 애플리케이션을 사용하여 다음 방법에 대해 알아봅니다.

* .NET 및 Service Fabric을 사용하여 애플리케이션 만들기
* ASP.NET Core를 웹 프런트 엔드로 사용
* 상태 저장 서비스에 애플리케이션 데이터 저장
* 로컬에서 애플리케이션 디버그
* 애플리케이션을 여러 노드에 걸쳐 스케일 아웃
* 애플리케이션 롤링 업그레이드 수행

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음이 필요합니다.

1. **Azure 개발** 과 **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019 설치](https://www.visualstudio.com/)
2. [Git 설치](https://git-scm.com/)
3. [Microsoft Azure Service Fabric SDK 설치](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. 다음 명령을 실행하여 Visual Studio에서 로컬 Service Fabric 클러스터에 배포하도록 합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
   ```
    
## <a name="build-a-cluster"></a>클러스터 빌드

런타임, SDK, Visual Studio 도구 및 Docker를 설치한 후에 Docker를 실행하고 5노드 로컬 개발 클러스터를 만듭니다.

> [!Note]
> 클러스터를 만들 때 Docker 실행하는 이유는 컨테이너 기능이 설정된 상태로 클러스터가 만들어지기 때문입니다. Docker가 실행 중이 아니면 컨테이너 기능을 사용하도록 클러스터를 다시 만들어야 합니다.
> 이 특별한 빠른 시작에는 불필요하지만 클러스터를 만들 때 Docker를 실행하도록 하는 지침이 모범 사례로 포함됩니다.
> 터미널 창을 열고 오류가 발생하는지를 확인하는 `docker ps`를 실행하여 Docker가 실행되는지 테스트합니다. 응답이 오류를 나타내지 않는 경우 Docker가 실행되고 클러스터를 빌드할 준비가 되었습니다.
>
> [컨테이너에 대해 Windows 10 또는 Windows Server 설정](/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-10-Client)

1. 관리자 권한으로 새롭게 상승된 PowerShell 창을 엽니다.
2. 다음 PowerShell 명령을 실행하여 개발 클러스터를 만듭니다.

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
   ```
3. 로컬 클러스터 관리자 도구를 시작하려면 다음 명령을 실행합니다.

   ```powershell
   . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
   ```

>[!NOTE]
> 이 빠른 시작의 샘플 애플리케이션은 Windows 7에서 제공되지 않는 기능을 사용합니다.
>

## <a name="download-the-sample"></a>샘플 다운로드

명령 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>애플리케이션을 로컬로 실행

시작 메뉴에서 Visual Studio 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행** 을 선택합니다. 디버거를 서비스에 연결하려면 관리자 권한으로 Visual Studio를 실행해야 합니다.

복제한 리포지토리에서 **Voting.sln** Visual Studio 솔루션을 엽니다.

기본적으로 투표 애플리케이션은 포트 8080에서 수신 대기합니다.  애플리케이션 포트는 */VotingWeb/PackageRoot/ServiceManifest.xml* 파일에서 설정됩니다.  **엔드포인트** 요소의 **포트** 특성을 업데이트하여 애플리케이션 포트를 변경할 수 있습니다.  애플리케이션을 로컬로 배포하고 실행하려면 애플리케이션 포트가 열려 있고 컴퓨터에서 사용 가능해야 합니다.  애플리케이션 포트를 변경하는 경우 이 문서 전체에서 "8080"을 새 애플리케이션 포트 값으로 대체합니다.

애플리케이션을 배포하려면 **F5** 키를 누릅니다.

> [!NOTE]
> Visual Studio 출력 창에서 "애플리케이션 URL이 설정되지 않았거나 HTTP/HTTPS URL이 아니므로 애플리케이션에 대한 브라우저가 열리지 않습니다."라는 메시지가 표시됩니다.  메시지가 오류를 나타내지 않지만 브라우저가 자동으로 시작되지 않습니다.

배포가 완료되면 브라우저를 시작하고 애플리케이션의 웹 프런트 엔드를 보려면 `http://localhost:8080`을 엽니다.

![애플리케이션 프런트 엔드](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

이제 투표 옵션 집합을 추가하고 투표 하기를 시작할 수 있습니다. 애플리케이션이 실행되고 모든 데이터가 Service Fabric 클러스터에 저장되며 별도의 데이터베이스가 필요하지 않습니다.

## <a name="walk-through-the-voting-sample-application"></a>투표 애플리케이션 예제 연습

투표 애플리케이션은 두 가지 서비스로 구성됩니다.

* 웹 프런트 엔드 서비스(VotingWeb) - ASP.NET Core 웹 프런트 엔드 서비스로, 웹 페이지를 제공하며 백 엔드 서비스와 통신하기 위한 Web API를 공개합니다.
* 백 엔드 서비스(VotingData) - ASP.NET Core 웹 서비스로, 투표 결과를 디스크에 보관된 신뢰할 수 있는 사전에 저장하기 위한 API를 공개합니다.

![애플리케이션 다이어그램](./media/service-fabric-quickstart-dotnet/application-diagram.png)

애플리케이션에 투표하는 경우 다음 이벤트가 발생합니다.

1. JavaScript가 투표 요청을 웹 프런트 엔드 서비스의 Web API에 HTTP PUT 요청으로 보냅니다.

2. 웹 프런트 엔드 서비스는 프록시를 사용하여 HTTP PUT 요청을 찾아 백 엔드 서비스에 전달합니다.

3. 백 엔드 서비스는 들어오는 요청을 받고 업데이트된 결과를, 클러스터 내의 여러 노드에 복제되고 디스크에 보관된 신뢰할 수 있는 사전에 저장합니다. 애플리케이션의 모든 데이터가 클러스터에 저장되므로 데이터베이스가 필요하지 않습니다.

## <a name="debug-in-visual-studio"></a>Visual Studio에서 디버그

애플리케이션은 정상적으로 실행되지만 디버거를 사용하여 애플리케이션의 주요 부분이 어떻게 작동하는지 확인할 수 있습니다. Visual Studio에서 애플리케이션을 디버깅할 때 로컬 Service Fabric 개발 클러스터를 사용합니다. 사용자 시나리오에 맞게 디버깅 환경을 조정할 수 있습니다. 이 애플리케이션에서는 신뢰할 수 있는 사전을 사용하여 데이터가 백 엔드 서비스에 저장됩니다. Visual Studio는 디버거를 중지하는 경우 기본값에 대해 애플리케이션을 제거합니다. 애플리케이션을 제거하면 백 엔드 서비스의 데이터도 제거됩니다. 디버깅 세션 간에 데이터를 유지하려면 *투표* 프로젝트 **속성** 을 열고 [**애플리케이션 디버그 모드**](service-fabric-manage-application-in-visual-studio.md#to-set-the-application-debug-mode-property)를 *자동 업그레이드* 로 변경합니다.

코드에서 수행되는 작업을 살펴보려면 다음 단계를 완료합니다.

1. **/VotingWeb/Controllers/VotesController.cs** 파일을 열고, 웹 API의 **Put** 메서드(69번 줄)에서 중단점을 설정합니다. 이 파일은 Visual Studio의 솔루션 탐색기에서 검색할 수 있습니다.

2. **/VotingData/Controllers/VoteDataController.cs** 파일을 열고, 이 웹 API의 **Put** 메서드(54번 줄)에서 중단점을 설정합니다.

3. 브라우저로 돌아가서 투표 옵션을 클릭하거나 새 투표 옵션을 추가합니다. 웹 프런트 엔드의 API 컨트롤러에서 첫 번째 중단점에 도달합니다.
   * 이 단계에서는 브라우저의 JavaScript가 프런트 엔드 서비스의 Web API 컨트롤러에 요청을 보냅니다.

     ![투표 프런트 엔드 서비스 추가](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

   * 먼저 백 엔드 서비스 **(1)** 에 대해 ReverseProxy에 대한 URL을 구성합니다.
   * 그런 다음 ReverseProxy **(2)** 에 HTTP PUT 요청을 보냅니다.
   * 마지막으로 백 엔드 서비스로부터 클라이언트 **(3)** 에 응답을 반환합니다.

4. 계속하려면 **F5** 키를 누릅니다.
   - 브라우저에서 메시지가 표시되면 ServiceFabricAllowedUsers 그룹에 디버그 모드에 대한 읽기 및 실행 권한을 부여합니다.
   - 이제 백 엔드 서비스의 중단점에 있습니다.

     ![투표 백 엔드 서비스 추가](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

   - 메서드의 첫 번째 줄 **(1)** 에서는 `StateManager`가 신뢰할 수 있는 사전 `counts`를 가져오거나 추가합니다.
   - 신뢰할 수 있는 사전에 있는 값과의 모든 상호 작용에는 트랜잭션이 필요하며 using 문 **(2)** 으로 트랜잭션이 만들어집니다.
   - 트랜잭션에서는 투표 옵션에 대한 관련 키 값을 업데이트하고 작업을 커밋합니다 **(3)**. 커밋 메서드가 반환되면 사전에 데이터가 업데이트되고 클러스터의 다른 노드에 복제됩니다. 이제 데이터는 클러스터에 안전하게 저장되며 백 엔드 서비스는 데이터를 계속 제공하면서 다른 노드로 장애 조치할 수 있습니다.
5. 계속하려면 **F5** 키를 누릅니다.

디버깅 세션을 중지하려면 **Shift+F5** 키를 누릅니다.

## <a name="perform-a-rolling-application-upgrade"></a>애플리케이션 롤링 업그레이드 수행

새 업데이트를 애플리케이션에 배포할 때는 Service Fabric이 안전한 방식으로 업데이트를 공개합니다. 롤링 업그레이드를 사용하면 업그레이드하는 동안 가동 중지 시간이 없으며 오류가 발생하면 자동화된 롤백을 수행합니다.

애플리케이션을 업그레이드하려면 다음을 수행합니다.

1. Visual Studio에서 **/VotingWeb/Views/Home/Index.cshtml** 파일을 엽니다.
2. 텍스트를 추가하거나 업데이트하여 페이지의 제목을 변경합니다. 예를 들어 제목을 “Service Fabric Voting 샘플 v2”로 변경합니다.
3. 파일을 저장합니다.
4. 솔루션 탐색기에서 **Voting** 을 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다. [게시] 대화 상자가 나타납니다.
5. **매니페스트 버전** 단추를 클릭하여 서비스 및 애플리케이션의 버전을 변경합니다.
6. **VotingWebPkg** 아래에서 **Code** 요소의 버전을, 예를 들어 “2.0.0”으로 변경하고 **저장** 을 클릭합니다.

    ![[버전 변경] 대화 상자](./media/service-fabric-quickstart-dotnet/change-version.png)
7. **Service Fabric 애플리케이션 게시** 대화 상자에서 **애플리케이션 업그레이드** 확인란을 선택합니다.
8.  **대상 프로필** 을 **PublishProfiles\Local.5Node.xml** 로 변경하고 **연결 엔드포인트** 가 **로컬 클러스터** 로 설정되어 있는지 확인합니다.
9. **애플리케이션 업그레이드** 를 선택합니다.

    ![게시 대화 상자 업그레이드 설정](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. **게시** 를 클릭합니다.

    업그레이드가 실행되는 동안 애플리케이션을 계속 사용할 수 있습니다. 클러스터에서 실행되는 서비스에는 두 인스턴스가 있으므로 일부 요청은 애플리케이션의 업그레이드된 버전을 가질 수 있는 반면 일부는 이전 버전을 가질 수 있습니다.

11. 브라우저를 열고 포트 19080에서 클러스터 주소로 이동합니다. 예: `http://localhost:19080/`.
12. 트리 뷰의 **애플리케이션** 노드를 클릭한 후 오른쪽 창에서 **Upgrades in Progress(진행 중인 업그레이드)** 를 클릭합니다. 업그레이드가 클러스터에서 업그레이드 도메인을 어떻게 통과하고 다음으로 진행하기 전에 각 도메인 상태가 정상인지 확인하게 됩니다. 도메인 상태가 확인되면 진행률 표시줄에 업그레이드 도메인이 녹색으로 표시됩니다.
    ![Service Fabric Explorer에서 업그레이드 보기](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric에서는 클러스터의 각 노드에서 서비스를 업그레이드 한 후 2분 대기함으로써 안전하게 업그레이드합니다. 전체 업데이트에는 약 8분이 소요될 것으로 예상됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음과 같은 방법을 배웠습니다.

* .NET 및 Service Fabric을 사용하여 애플리케이션 만들기
* ASP.NET Core를 웹 프런트 엔드로 사용
* 상태 저장 서비스에 애플리케이션 데이터 저장
* 로컬에서 애플리케이션 디버그
* 애플리케이션을 여러 노드에 걸쳐 스케일 아웃
* 애플리케이션 롤링 업그레이드 수행

Service Fabric 및 .NET에 대해 자세히 알아보기 위해 다음 자습서를 살펴보겠습니다.
> [!div class="nextstepaction"]
> [Service Fabric에서 .NET 애플리케이션](service-fabric-tutorial-create-dotnet-app.md)
