---
title: Service Fabric 관리형 클러스터 배포
description: 이 자습서에서는 테스트를 위해 Service Fabric 관리형 클러스터를 배포합니다.
ms.topic: tutorial
ms.date: 8/23/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 3117c4c248aa073fb961dc031342d8b3d9489bbd
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867407"
---
# <a name="tutorial-deploy-a-service-fabric-managed-cluster"></a>자습서: Service Fabric 관리형 클러스터 배포

이 자습서 시리즈에서는 다음을 설명합니다.

> [!div class="checklist"]
> * Service Fabric 관리형 클러스터 배포 방법 
> * [Service Fabric 관리형 클러스터 확장 방법](tutorial-managed-cluster-scale.md)
> * [Service Fabric 관리형 클러스터에서 노드를 추가 및 제거하는 방법](tutorial-managed-cluster-add-remove-node-type.md)
> * [Service Fabric 관리형 클러스터에 애플리케이션을 배포하는 방법](tutorial-managed-cluster-deploy-app.md)

시리즈의 이번 부분에서는 다음 작업을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Azure 계정에 연결
> * 새 리소스 그룹 만들기
> * Service Fabric 관리형 클러스터 배포
> * 클러스터에 기본 노드 유형 추가

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* [Service Fabric SDK 및 PowerShell 모듈](service-fabric-get-started.md)을 설치합니다.

* [Azure PowerShell 4.7.0](/powershell/azure/release-notes-azureps#azservicefabric)(이상)을 설치합니다.

## <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

`<your-subscription>`을 Azure 계정의 구독 문자열로 바꾸고 다음과 같이 연결합니다.

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <your-subscription>

```

## <a name="create-a-new-resource-group"></a>새 리소스 그룹 만들기

그런 다음, `<your-rg>` 및 `<location>`을 원하는 그룹 이름과 위치로 바꾼 관리형 Service Fabric 클러스터에 대한 리소스 그룹을 만듭니다.

```powershell
$resourceGroup = "myResourceGroup"
$location = "EastUS2"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="deploy-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터 배포

### <a name="create-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터 만들기

이 단계에서는 New-AzServiceFabricManagedCluster PowerShell 명령을 사용하여 Service Fabric 관리형 클러스터를 만듭니다. 다음 예제에서는 myResourceGroup이라는 리소스 그룹에 myCluster라는 클러스터를 만듭니다. 이 리소스 그룹은 이전 자습서에서 eastus2 지역에 만들었습니다.

이 단계에서 다음 템플릿 매개 변수에 대해 직접 값을 입력합니다.

* **클러스터 이름**: 클러스터에 대한 고유 이름(예: *mysfcluster*)을 입력합니다.
* **관리자 암호**: 클러스터의 기본 VM에서 RDP에 사용할 관리자에 대한 암호를 입력합니다.
* **클라이언트 인증서 지문**: 클러스터에 액세스하는 데 사용할 클라이언트 인증서의 지문을 제공합니다. 인증서가 없는 경우 [인증서 설정 및 검색](../key-vault/certificates/quick-create-portal.md)을 수행하여 자체 서명된 인증서를 만듭니다.
* **클러스터 SKU**: 배포할 [Service Fabric 관리형 클러스터의 유형](overview-managed-cluster.md#service-fabric-managed-cluster-skus)을 지정합니다. *기본* SKU 클러스터는 테스트 배포용으로만 사용하여야 하며, 노드 유형 추가 또는 제거용으로는 허용되지 않습니다.

```powershell
$clusterName = "<unique cluster name>"
$password = "Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$clientThumbprint = "<certificate thumbprint>"
$clusterSku = "Standard"

New-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroup -Location $location -ClusterName $clusterName -ClientCertThumbprint $clientThumbprint -ClientCertIsAdmin -AdminPassword $password -Sku $clusterSKU -Verbose
```

### <a name="add-a-primary-node-type-to-the-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터에 주 노드 유형 추가

이 단계에서는 방금 만든 클러스터에 주 노드 유형을 추가합니다. 모든 Service Fabric 클러스터에는 하나 이상의 주 노드 유형이 있어야 합니다.

이 단계에서 다음 템플릿 매개 변수에 대해 직접 값을 입력합니다.

* **노드 유형 이름**: 클러스터에 추가할 노드 유형에 대한 고유한 이름(예: “NT1”)을 입력합니다.

> [!NOTE]
> 추가하는 노드 유형이 클러스터에서 첫 번째 또는 유일한 노드 유형인 경우 Primary 속성을 사용해야 합니다.

```powershell
$nodeType1Name = "NT1"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeType1Name -Primary -InstanceCount 5
```

이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

### <a name="review-deployed-resources"></a>배포된 리소스 검토

배포가 완료되면 포털의 Service Fabric 관리형 클러스터 리소스 개요 페이지에서 Service Fabric Explorer 값을 찾습니다. 인증서를 요청하는 메시지가 표시되면 PowerShell 명령에서 클라이언트 지문이 제공된 인증서를 사용합니다.

## <a name="next-steps"></a>다음 단계

이 단계에서는 첫 번째 Service Fabric 관리형 클러스터를 만들어 배포했습니다. 클러스터의 크기를 조정하는 방법을 자세히 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 확장 방법](tutorial-managed-cluster-scale.md)
