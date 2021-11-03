---
title: Service Fabric 관리 되는 클러스터에 커넥트
description: Service Fabric 관리 클러스터에 연결 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: aa168a283be909678a199eee887d69bb3eb2024a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052882"
---
# <a name="connect-to-a-service-fabric-managed-cluster"></a>Service Fabric 관리 되는 클러스터에 커넥트

[포털, Azure 리소스 관리 템플릿](quickstart-managed-cluster-template.md)또는 [PowerShell](tutorial-managed-cluster-deploy.md) 을 통해 클러스터를 배포한 후에는 다양 한 방법으로 관리 되는 클러스터에 연결 하 고 볼 수 있습니다. 

## <a name="use-azure-portal"></a>Azure Portal 사용

다음 방법으로 관리 되는 클러스터 리소스로 이동 합니다.

 1) 할 것입니다 https://portal.azure.com/

 2) Service Fabric를 검색 하 고 "관리 되는 클러스터 Service Fabric"를 선택 하 여 클러스터 리소스로 이동 합니다.

 3) 클러스터 선택

 4) 이 환경에서는 특정 매개 변수를 확인 하 고 수정할 수 있습니다. 자세한 내용은 사용 가능한 [클러스터 구성 옵션](how-to-managed-cluster-configuration.md) 을 참조 하십시오.

## <a name="use-service-fabric-explorer"></a>Service Fabric Explorer 사용

SFX ( [Service Fabric Explorer](https://github.com/Microsoft/service-fabric-explorer) )는 Microsoft Azure Service Fabric 클러스터의 응용 프로그램 및 클러스터 상태를 검사 하 고 관리 하기 위한 응용 프로그램입니다. 

관리 되는 클러스터에 대 한 SFX로 이동 하려면
 
 1) 할 것입니다 https://portal.azure.com/
 
 2) Service Fabric를 검색 하 고 "관리 되는 클러스터 Service Fabric"를 선택 하 여 클러스터 리소스로 이동 합니다.

 3) 클러스터 선택

 4) `SF Explorer`오른쪽 위에 있는를 찾습니다. 예를 들면 다음과 같습니다.https://mycluster.region.cloudapp.azure.com:19080/Explorer

## <a name="use-powershell-modules"></a>PowerShell 모듈 사용

클러스터에 대 한 구성을 연결 하 고, 보고, 수정 하는 데 사용할 수 있는 PowerShell 모듈은 다음과 같습니다. 

* [Service Fabric SDK 및 PowerShell 모듈](service-fabric-get-started.md)을 설치합니다.

* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric)(이상)을 설치합니다.

### <a name="using-the-service-fabric-powershell-module"></a>Service Fabric PowerShell 모듈 사용
이 모듈을 사용 하려면 클러스터 인증서 지문이 필요 합니다. 리소스 배포의 클러스터 속성 출력에서 또는 기존 리소스의 클러스터 속성을 쿼리하여 이 값을 찾을 수 있습니다.

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

Azure Service Fabric 모듈을 사용 하면 관리 되는 클러스터 만들기, 노드 유형 크기 조정, 관리 클러스터 리소스 정보 보기 등의 작업을 수행할 수 있습니다. 관리 클러스터에 대해 지원 되는 특정 cmdlet은 `AzServiceFabricManagedCluster*` [ServiceFabric PowerShell 모듈](/powershell/module/az.servicefabric/) 설명서에서 참조할 수 있도록 이름이 지정 됩니다.


다음 예에서는 cmdlet 중 하나를 사용 하 여 관리 되는 클러스터에 대 한 세부 정보를 확인 합니다.

```powershell
$rgName = "testResourceGroup"
$clusterName = "mycluster"
Get-AzServiceFabricManagedCluster -ResourceGroupName $rgName -Name $clusterName
```

## <a name="next-steps"></a>다음 단계

* [Azure Resource Manager를 사용하여 애플리케이션을 관리형 클러스터 배포](how-to-managed-cluster-app-deployment-template.md)
* [관리 클러스터 옵션 구성](how-to-managed-cluster-configuration.md)



