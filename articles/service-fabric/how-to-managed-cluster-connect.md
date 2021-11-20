---
title: Service Fabric 관리형 클러스터에 커넥트
description: Service Fabric 관리형 클러스터에 연결하는 방법 알아보기
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 5521f33dd5af988c4455b9cb99d8a25ba0f93e29
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871436"
---
# <a name="connect-to-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터에 커넥트

[포털, Azure Resource Managed Template](quickstart-managed-cluster-template.md)또는 [PowerShell을](tutorial-managed-cluster-deploy.md) 통해 클러스터를 배포한 후에는 관리형 클러스터에 연결하고 볼 수 있는 다양한 방법이 있습니다. 

## <a name="use-azure-portal"></a>Azure Portal 사용

다음을 통해 관리되는 클러스터 리소스로 이동합니다.

 1) 할 것입니다 https://portal.azure.com/

 2) Service Fabric 검색하고 "Service Fabric 관리형 클러스터"를 선택하여 클러스터 리소스로 이동합니다.

 3) 클러스터 선택

 4) 이 경험에서는 특정 매개 변수를 보고 수정할 수 있습니다. 자세한 내용은 사용 가능한 [클러스터 구성 옵션을](how-to-managed-cluster-configuration.md) 참조하세요.

## <a name="use-service-fabric-explorer"></a>Service Fabric Explorer 사용

[SFX(Service Fabric Explorer)는](https://github.com/Microsoft/service-fabric-explorer) Microsoft Azure Service Fabric 클러스터의 애플리케이션 및 클러스터 상태를 검사하고 관리하기 위한 애플리케이션입니다. 

관리형 클러스터에 대한 SFX로 이동하려면
 
 1) 할 것입니다 https://portal.azure.com/
 
 2) Service Fabric 검색하고 "Service Fabric 관리형 클러스터"를 선택하여 클러스터 리소스로 이동합니다.

 3) 클러스터 선택

 4) 오른쪽 `SF Explorer` 위에 있는 를 찾습니다. 예: `https://mycluster.region.cloudapp.azure.com:19080/Explorer`

## <a name="use-powershell-modules"></a>PowerShell 모듈 사용

클러스터에 대한 구성을 연결, 보기 및 수정하는 데 사용할 수 있는 PowerShell 모듈은 다음과 같습니다. 

* [Service Fabric SDK 및 PowerShell 모듈](service-fabric-get-started.md)을 설치합니다.

* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric)(이상)을 설치합니다.

### <a name="using-the-service-fabric-powershell-module"></a>Service Fabric PowerShell 모듈 사용
이 모듈을 사용하려면 클러스터 인증서 지문이 필요합니다. 리소스 배포의 클러스터 속성 출력에서 또는 기존 리소스의 클러스터 속성을 쿼리하여 이 값을 찾을 수 있습니다.

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

### <a name="using-the-azure-service-fabric-powershell-module"></a>Azure Service Fabric PowerShell 모듈 사용

Azure Service Fabric 모듈을 사용하면 관리형 클러스터 만들기, 노드 유형 크기 조정 및 관리형 클러스터 리소스 정보 보기와 같은 작업을 수행할 수 있습니다. 관리형 클러스터에 지원되는 특정 cmdlet의 이름은 `AzServiceFabricManagedCluster*` [Az.ServiceFabric PowerShell 모듈](/powershell/module/az.servicefabric/) 설명서에서 참조할 수 있습니다.


다음 예제에서는 cmdlet 중 하나를 사용하여 관리되는 클러스터의 세부 정보를 봅니다.

```powershell
$rgName = "testResourceGroup"
$clusterName = "mycluster"
Get-AzServiceFabricManagedCluster -ResourceGroupName $rgName -Name $clusterName
```

## <a name="next-steps"></a>다음 단계

* [Azure Resource Manager를 사용하여 애플리케이션을 관리형 클러스터 배포](how-to-managed-cluster-app-deployment-template.md)
* [관리형 클러스터 옵션 구성](how-to-managed-cluster-configuration.md)
