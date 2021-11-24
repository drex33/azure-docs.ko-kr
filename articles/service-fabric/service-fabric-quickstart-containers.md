---
title: Azure의 Service Fabric에서 Windows 컨테이너 앱 만들기
description: 이 빠른 시작에서는 Azure Service Fabric에서 첫 번째 Windows 컨테이너 애플리케이션을 만듭니다.
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc, devx-track-azurepowershell, mode-other
ms.openlocfilehash: a6de3e8f7de40e0da51fdec45e5e908de513793c
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133043466"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>빠른 시작: Windows 컨테이너를 Service Fabric에 배포

Azure Service Fabric은 확장성 있고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다.

Service Fabric 클러스터의 Windows 컨테이너에서 기존 애플리케이션을 실행하더라도 애플리케이션을 변경할 필요가 없습니다. 이 빠른 시작에서는 Service Fabric 애플리케이션에서 미리 작성된 Docker 컨테이너 이미지를 배포하는 방법을 보여줍니다. 완료하면 Windows Server Core 2016 서버 및 IIS 컨테이너를 실행하게 됩니다. 이 빠른 시작에서는 Windows 컨테이너를 배포합니다. Linux 컨테이너를 배포하려면 [이 빠른 시작](service-fabric-quickstart-containers-linux.md)을 참조하세요.

![IIS default web page][iis-default]

이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.

* Docker 이미지 컨테이너 패키징
* 통신 구성
* Service Fabric 애플리케이션 빌드 및 패키징
* Azure에 컨테이너 애플리케이션 배포


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구속([체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만들 수 있음)
* 다음을 실행하는 개발 컴퓨터
  * Visual Studio 2019 또는 Windows 2019
  * [Service Fabric SDK 및 도구](service-fabric-get-started.md)

## <a name="package-a-docker-image-container-with-visual-studio"></a>Visual Studio에서 Docker 이미지 컨테이너 패키징

Service Fabric SDK 및 도구는 컨테이너를 Service Fabric 클러스터에 배포할 수 있는 서비스 템플릿을 제공합니다.

"관리자" 권한으로 Visual Studio를 시작합니다.  **File** > **New** > **Project** 를 선택합니다.

**Service Fabric 애플리케이션** 을 선택하고 "MyFirstContainer"라는 이름을 지정하고 **만들기** 를 클릭합니다.

**호스트된 컨테이너 및 애플리케이션** 템플릿에서 **컨테이너** 를 선택합니다.

**이미지 이름** 에 "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016", [Windows Server Core 서버 및 IIS 기본 이미지](https://hub.docker.com/_/microsoft-windows-servercore-iis)를 입력합니다.

포트 80의 서비스로 들어오는 요청이 컨테이너의 포트 80에 매핑되도록 컨테이너 포트와 호스트 포트 간 매핑을 구성합니다.  **컨테이너 포트** 를 "80"으로, **호스트 포트** 를 "80"으로 설정합니다.  

"MyContainerService" 서비스 이름을 지정하고 **확인** 을 클릭합니다.

![새 서비스 대화 상자][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>컨테이너 이미지에 대한 OS 빌드 지정

특정 버전의 Windows Server를 사용하여 빌드한 컨테이너는 다른 버전의 Windows Server를 실행하는 호스트에서 실행되지 않을 수 있습니다. 예를 들어 Windows Server 버전 1709를 사용하여 빌드한 컨테이너는 Windows Server 2016을 실행하는 호스트에서 실행되지 않습니다. 자세히 알아보려면 [Windows Server 컨테이너 OS 및 호스트 OS 호환성](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility)을 참조하세요. 

Service Fabric 런타임 버전 6.1 이상을 사용하여 컨테이너당 여러 OS 이미지를 지정하고 각각을 배포되어야 하는 OS의 빌드 버전으로 태그 지정할 수 있습니다. 이렇게 하면 애플리케이션이 서로 다른 버전의 Windows OS를 실행하는 호스트에서 실행되는지 확인할 수 있습니다. 자세히 알아보려면 [OS 빌드 관련 컨테이너 이미지 지정](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)을 참조하세요. 

Microsoft는 다른 버전의 Windows Server에서 빌드한 IIS 버전에 대한 서로 다른 이미지를 게시합니다. Service Fabric이 애플리케이션을 배포하는 클러스터 노드에서 실행 중인 Windows Server의 버전과 호환되는 컨테이너를 배포하는지 확인하려면 *ApplicationManifest.xml* 파일에 다음 줄을 추가합니다. Windows Server 2016에 대한 빌드 버전은 14393이며 Windows Server 버전 1709의 빌드 버전은 16299입니다.

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

서비스 매니페스트는 nanoserver, `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016`에 대한 하나의 이미지만을 계속해서 지정합니다.

또한 *ApplicationManifest.xml* 파일에서 **PasswordEncrypted** 를 **false** 로 변경합니다. Docker Hub에 있는 공개 컨테이너 이미지의 경우 계정 및 암호가 비어 있으므로 암호화를 해제합니다. 빈 암호를 암호화하면 빌드 오류가 발생하기 때문입니다.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>클러스터 만들기

다음 샘플 스크립트는 X.509 인증서를 사용하여 보호되는 5노드 Service Fabric 클러스터로 만듭니다. 이 명령은 자체 서명된 인증서를 만들고 새로운 Key Vault에 업로드합니다. 인증서는 로컬 디렉터리에도 복사됩니다. [Service Fabric 클러스터 만들기](scripts/service-fabric-powershell-create-secure-cluster-cert.md)에서 이 스크립트를 사용하여 클러스터를 만드는 방법에 대해 자세히 알아볼 수 있습니다.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치합니다.

다음 스크립트를 실행하기 전에 PowerShell에서 `Connect-AzAccount`를 실행하여 Azure와의 연결을 설정합니다.

다음 스크립트를 클립보드에 복사하고 **Windows PowerShell ISE** 를 엽니다.  빈 Untitled1.ps1 창에 내용을 붙여넣습니다. 그런 다음, 스크립트의 `subscriptionId`, `certpwd`, `certfolder`, `adminuser`, `adminpwd` 등의 변수에 대한 값을 제공합니다.  스크립트를 실행하기 전에 `certfolder`에 지정한 디렉터리가 존재해야 합니다.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

변수에 대한 값을 제공했으면 **F5** 키를 눌러서 스크립트를 실행합니다.

스크립트가 실행되고 클러스터가 생성되면 출력에서 `ClusterEndpoint`를 찾아보세요. 예를 들어:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>클러스터에 인증서 설치

이제 *CurrentUser\My* 인증서 저장소에 PFX를 설치합니다. PFX 파일은 위의 PowerShell 스크립트에서 `certfolder` 환경 변수를 사용하여 지정한 디렉터리에 있을 것입니다.

해당 디렉터리로 이동한 후 다음 PowerShell 명령을 실행하고 `certfolder` 디렉터리에 있는 PFX 파일의 이름과 `certpwd` 변수에 지정한 암호를 바꿉니다. 이 예에서 현재 디렉터리는 PowerShell 스크립트에서 `certfolder` 변수로 지정한 디렉터리로 설정됩니다. 여기에서 `Import-PfxCertificate` 명령이 실행됩니다.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

이 명령은 지문을 반환합니다.

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

다음 단계를 위해 지문 값을 기억합니다.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Visual Studio를 사용하여 Azure에 애플리케이션 배포

애플리케이션이 준비되면 Visual Studio에서 클러스터에 직접 배포할 수 있습니다.

솔루션 탐색기에서 **MyFirstContainer** 를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다. [게시] 대화 상자가 나타납니다.

위의 `Import-PfxCertificate` 명령을 실행할 때 PowerShell 창에서 **CN=** 다음에 콘텐츠를 복사하고 `19000` 포트를 추가합니다. 예: `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. 이를 **연결 엔드포인트** 필드에 복사합니다. 이 값은 이후 단계에서 필요하므로 기억해 두세요.

**고급 연결 매개 변수** 를 클릭하고 연결 매개 변수 정보를 확인합니다.  *FindValue* 및 *ServerCertThumbprint* 값은 이전 단계에서 `Import-PfxCertificate`를 실행할 때 설치한 인증서의 지문과 일치해야 합니다.

![[게시] 대화 상자](./media/service-fabric-quickstart-containers/publish-app.png)

**게시** 를 클릭합니다.

클러스터의 각 애플리케이션에는 고유한 이름이 있어야 합니다. 이름이 충돌하는 경우 Visual Studio 프로젝트의 이름을 바꾸고 다시 배포합니다.

브라우저를 열고 이전 단계에서 **연결 엔드포인트** 필드에 입력한 주소로 이동합니다. 필요에 따라 URL에 `http://` 스키마 식별자를 앞쪽에 추가하고 `:80` 포트를 추가할 수 있습니다. 예: http:\//mysfcluster.SouthCentralUS.cloudapp.azure.com:80.

 IIS 기본 웹 페이지가 표시됩니다. ![IIS 기본 웹 페이지][iis-default]

## <a name="clean-up"></a>정리

클러스터가 실행되는 동안 요금이 계속 청구되므로 [클러스터를 삭제](./service-fabric-tutorial-delete-cluster.md)하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음과 같은 방법을 배웠습니다.

* Docker 이미지 컨테이너 패키징
* 통신 구성
* Service Fabric 애플리케이션 빌드 및 패키징
* Azure에 컨테이너 애플리케이션 배포

Service Fabric에서 Windows 컨테이너 작업에 대해 자세히 알아보려면 Windows 컨테이너 앱에 대한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Windows 컨테이너 앱 만들기](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
