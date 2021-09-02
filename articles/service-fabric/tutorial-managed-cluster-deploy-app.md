---
title: PowerShell을 통해 Service Fabric 관리형 클러스터에 애플리케이션 배포
description: 이 자습서에서는 Service Fabric 관리형 클러스터에 연결하고 PowerShell을 통해 애플리케이션을 배포합니다.
ms.topic: tutorial
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b1228deaf5627c8a367a9b0927320f93d98a50e
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867443"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster"></a>자습서: Service Fabric 관리형 클러스터에 앱 배포

이 자습서 시리즈에서는 다음을 설명합니다.

> [!div class="checklist"]
> * [Service Fabric 관리형 클러스터 배포 방법](tutorial-managed-cluster-deploy.md)
> * [Service Fabric 관리형 클러스터 확장 방법](tutorial-managed-cluster-scale.md)
> * [Service Fabric 관리형 클러스터에서 노드를 추가 및 제거하는 방법](tutorial-managed-cluster-add-remove-node-type.md)
> * Service Fabric 관리형 클러스터에 애플리케이션을 배포하는 방법

시리즈의 이번 부분에서는 다음 작업을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Service Fabric 관리형 클러스터에 연결
> * 클러스터에 애플리케이션 업로드
> * 클러스터에서 애플리케이션 인스턴스화
> * 클러스터에서 애플리케이션 제거

## <a name="prerequisites"></a>사전 요구 사항

* Service Fabric 관리형 클러스터([*관리형 클러스터 배포*](tutorial-managed-cluster-deploy.md) 참조)

## <a name="connect-to-your-cluster"></a>클러스터에 연결

클러스터에 연결하려면 클러스터 인증서 지문이 필요합니다. 리소스 배포의 클러스터 속성 출력에서 또는 기존 리소스의 클러스터 속성을 쿼리하여 이 값을 찾을 수 있습니다.

다음 명령을 사용하여 클러스터 리소스에서 클러스터 인증서 지문을 쿼리할 수 있습니다.

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprints
```

클러스터 인증서 지문을 사용하여 클러스터에 연결할 준비가 되었습니다.

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>애플리케이션 패키지 업로드

이 자습서에서는 [Service Fabric 투표 애플리케이션](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy) 샘플을 사용합니다. PowerShell을 통한 Service Fabric 애플리케이션 배포에 대한 자세한 내용은 [Service Fabric 배포 및 애플리케이션 제거](service-fabric-deploy-remove-applications.md)를 참조하세요.

> [!NOTE]
> Service Fabric 관리형 클러스터 미리 보기에서는 Visual Studio에서 직접 애플리케이션을 게시할 수 없습니다.

먼저 [배포용 애플리케이션을 패키징](service-fabric-package-apps.md)해야 합니다. 이 자습서에서는 Visual Studio 내에서 애플리케이션을 패키징하는 단계를 수행하세요. 애플리케이션이 패키징된 경로는 아래 경로에 사용되므로 기록해 두어야 합니다.

애플리케이션 패키지를 만든 후에는 클러스터에 애플리케이션 패키지를 업로드할 수 있습니다. `$path` 값을 업데이트하여 애플리케이션 패키지가 있는 경로를 표시하고, 다음을 실행합니다.

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>애플리케이션 만들기

New-ServiceFabricApplication cmdlet을 사용하여 성공적으로 등록된 모든 애플리케이션 유형 버전에서 애플리케이션을 인스턴스화할 수 있습니다. 각 애플리케이션의 이름은 반드시 “fabric:” 체계로 시작하고 각 애플리케이션 인스턴스에 대해 고유해야 합니다. 대상 애플리케이션 형식의 애플리케이션 매니페스트에 정의된 모든 기본 서비스도 만들어집니다.

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

이 작업이 완료되면 애플리케이션 인스턴스가 Service Fabric Explorer에서 실행되는 것을 볼 수 있습니다.

### <a name="remove-an-application"></a>애플리케이션 제거

애플리케이션 인스턴스가 더 이상 필요하지 않은 경우 `Remove-ServiceFabricApplication` cmdlet을 사용하여 이름으로 영구적으로 제거할 수 있습니다. 그러면 애플리케이션에 속하는 모든 서비스도 자동으로 제거되어 모든 서비스 상태가 영구적으로 제거됩니다.

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>다음 단계

이 단계에서는 Service Fabric 관리형 클러스터에 애플리케이션을 배포했습니다. 애플리케이션 배포 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [관리형 클러스터에 애플리케이션 비밀 배포](how-to-managed-cluster-application-secrets.md)
* [Azure Resource Manager를 사용하여 애플리케이션을 관리형 클러스터 배포](how-to-managed-cluster-app-deployment-template.md)
* [관리 ID가 있는 애플리케이션을 관리형 클러스터에 배포](how-to-managed-cluster-application-managed-identity.md)


관리형 클러스터 구성 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [관리 클러스터 구성](how-to-managed-cluster-configuration.md)
