---
title: Azure Service Fabric 관리형 클러스터 업그레이드
description: Azure Service Fabric 관리형 클러스터를 업그레이드하는 옵션에 관해 알아보기
ms.topic: how-to
ms.date: 06/16/2021
ms.openlocfilehash: 50af042be1dc69f39e61447901d4d5f07da2a1e7
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112290092"
---
# <a name="manage-service-fabric-managed-cluster-upgrades"></a>Service Fabric 관리형 클러스터 업그레이드 관리

Azure Service Fabric 클러스터는 사용자 고유 리소스이지만, Microsoft에서 부분적으로 관리합니다. Microsoft에서 Azure Service Fabric 관리형 클러스터를 업데이트하는 시기와 방법을 관리하는 방법은 다음과 같습니다.

## <a name="set-upgrade-mode"></a>업그레이드 모드 설정

Azure Service Fabric 관리형 클러스터는 [웨이브 배포](#wave-deployment-for-automatic-upgrades) 전략을 사용하여 Microsoft에서 릴리스된 자동 Service Fabric 업그레이드를 받도록 기본적으로 설정되어 있습니다. 또는 현재 지원되는 버전 목록에서 선택하는 수동 모드 업그레이드를 설정할 수 있습니다. Azure Portal의 *패브릭 업그레이드* 컨트롤이나 클러스터 배포 템플릿의 `ClusterUpgradeMode` 설정을 통해 이러한 설정을 구성할 수 있습니다.

## <a name="wave-deployment-for-automatic-upgrades"></a>자동 업그레이드를 위한 웨이브 배포

웨이브 배포를 사용하면 기본 제공 ‘베이킹 시간’으로 구분된 테스트, 스테이지 및 프로덕션 클러스터를 순서대로 업그레이드하기 위한 파이프라인을 만들어 프로덕션 클러스터를 업데이트하기 전에 예정된 Service Fabric 버전의 유효성을 검사할 수 있습니다.

>[!NOTE]
>기본적으로 클러스터는 웨이브 0으로 설정됩니다.

자동 업그레이드를 위해 웨이브 배포를 선택하려면 먼저 클러스터를 할당할 웨이브를 결정합니다.

* **웨이브 0**(`Wave0`): 새 Service Fabric 빌드를 릴리스하는 즉시 클러스터가 업데이트됩니다.
* **웨이브 1**(`Wave1`): 베이킹 시간을 허용하도록 웨이브 0 이후 클러스터가 업데이트됩니다. 이러한 업데이트는 웨이브 0이 있고 최소 7일 후에 발생합니다.
* **웨이브 2**(`Wave2`): 클러스터는 추가적인 베이킹 시간을 허용하기 위해 마지막에 업데이트됩니다. 이러한 업데이트는 웨이브 0이 있고 최소 14일 후에 발생합니다.

## <a name="set-the-wave-for-your-cluster"></a>클러스터에 대한 웨이브 설정

Azure Portal의 *패브릭 업그레이드* 컨트롤 또는 클러스터 배포 템플릿의 `ClusterUpgradeMode` 설정에서 클러스터를 사용 가능한 웨이브 중 하나로 설정할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

Azure Portal을 사용하여 새 Service Fabric 클러스터를 만들 때 사용 가능한 자동 웨이브 중에서 선택합니다.

:::image type="content" source="./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png" alt-text="‘고급’ 옵션에서 Azure Portal에 새 클러스터를 만들 때 사용 가능한 여러 웨이브 중에서 선택":::

기존 클러스터 리소스의 **패브릭 업그레이드** 섹션에서 사용 가능한 자동 웨이브 간을 전환할 수도 있습니다.

:::image type="content" source="./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png" alt-text="Azure Portal에 있는 클러스터 리소스의 ‘패브릭 업그레이드’ 섹션에서 여러 다른 자동 웨이브 중에서 선택":::

### <a name="resource-manager-template"></a>Resource Manager 템플릿

Resource Manager 템플릿을 사용하여 클러스터 업그레이드 모드를 변경하려면 *Microsoft.ServiceFabric/clusters* 리소스 정의의 `ClusterUpgradeMode` 속성에 대해 *자동* 또는 *수동* 을 지정합니다. 수동 업그레이드를 선택하는 경우 `clusterCodeVersion`을 현재 [지원되는 패브릭 버전](#query-for-supported-cluster-versions)으로 설정합니다.

#### <a name="manual-upgrade"></a>수동 업그레이드

```json
{
"apiVersion": "2021-05-01",
"type": "Microsoft.ServiceFabric/managedClusters",
"properties": {
        "ClusterUpgradeMode": "Manual",
        "ClusterCodeVersion": "8.0.514.9590"
        }
}
```

템플릿이 성공적으로 배포되면 클러스터 업그레이드 모드의 변경 내용이 적용됩니다. 클러스터가 수동 모드이면 클러스터 업그레이드가 자동으로 시작됩니다.

클러스터 상태 정책(노드 상태 및 클러스터에서 실행되는 모든 애플리케이션의 상태 조합)은 업그레이드 기간을 준수합니다. 클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다.

롤백이 발생하면 롤백으로 인해 초래된 문제를 수정하고 이전과 같은 단계에 따라 업그레이드를 다시 시작해야 합니다.

#### <a name="automatic-upgrade-with-wave-deployment"></a>웨이브 배포를 사용하여 자동 업그레이드

자동 업그레이드 및 웨이브 배포를 구성하려면 간단히 `ClusterUpgradeMode` 추가/유효성 검사를 `Automatic`으로 설정하면 됩니다. 그러면 `upgradeWave` 속성이 Resource Manager 템플릿에서 위에 나열된 웨이브 값 중 하나로 정의됩니다.

```json
{
"apiVersion": "2021-05-01",
"type": "Microsoft.ServiceFabric/managedClusters",
"properties": {
        "ClusterUpgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        }  
}
```

업데이트된 템플릿을 배포하면 다음 업그레이드 기간과 그 이후에 지정된 웨이브에 클러스터가 등록됩니다.

## <a name="query-for-supported-cluster-versions"></a>지원되는 클러스터 버전 쿼리

[Azure REST API](/rest/api/azure/)를 사용하여 지정된 위치와 구독에 사용할 수 있는 모든 Service Fabric 런타임 버전([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list))을 나열할 수 있습니다.

지원되는 버전과 운영 체제에 관한 자세한 내용은 [Service Fabric 버전](service-fabric-versions.md)도 참조할 수 있습니다.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/managedclusterVersions?api-version=2021-05-01

"value": [
  {
    "id": "subscriptions/eec8e14e-b47d-40d9-8bd9-23ff5c381b40/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/7.2.477.9590",
    "name": "7.2.477.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "2021-11-30T00:00:00",
      "osType": "Windows",
      "clusterCodeVersion": "7.2.477.9590"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/8.0.514.9590",
    "name": "8.0.514.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "osType": "Windows",
      "clusterCodeVersion": "8.0.514.9590"
    }
  }
]

```

출력의 `supportExpiryUtc`는 지정된 릴리스가 만료될 시기나 만료된 시기를 보고합니다. 최신 릴리스는 유효한 날짜를 포함하지 않으며 *9999-12-31T23:59:59.9999999* 값을 포함합니다. 이는 만료 날짜가 아직 설정되지 않음을 의미합니다.

## <a name="next-steps"></a>다음 단계

* [Service Fabric 관리형 클러스터 구성 사용자 지정](how-to-managed-cluster-configuration.md)
* [애플리케이션 업그레이드](service-fabric-application-upgrade.md)

<!--Image references-->
[Upgrade-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png
[New-Cluster-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png
