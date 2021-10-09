---
title: Service Fabric 클러스터 업그레이드 관리
description: Service Fabric 클러스터 런타임이 업데이트되는 시기 및 방법 관리
ms.topic: how-to
ms.date: 03/26/2021
ms.openlocfilehash: 129bdae4dc131013bd7c13377b61575141c27ccd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714587"
---
# <a name="manage-service-fabric-cluster-upgrades"></a>Service Fabric 클러스터 업그레이드 관리

Azure Service Fabric 클러스터는 사용자 고유 리소스이지만, Microsoft에서 부분적으로 관리합니다. Microsoft에서 Azure Service Fabric 클러스터를 업데이트하는 시기와 방법을 관리하는 방법은 다음과 같습니다.

클러스터 업그레이드 개념과 프로세스에 관한 자세한 배경은 [Azure Service Fabric 클러스터 업그레이드 및 업데이트](service-fabric-cluster-upgrade.md)를 참조하세요.

## <a name="set-upgrade-mode"></a>업그레이드 모드 설정

Microsoft에서 릴리스하는 대로 자동 Service Fabric 업그레이드를 받도록 클러스터를 설정하거나 클러스터의 업그레이드 모드를 설정하여 현재 지원되는 버전 목록에서 수동으로 선택할 수 있습니다. 이 작업은 Azure Portal의 *패브릭 업그레이드 모드* 제어 또는 클러스터 배포 템플릿의 `upgradeMode` 설정을 통해 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 새 Service Fabric 클러스터를 만들 때 자동 또는 수동 업그레이드 중에서 선택합니다.

:::image type="content" source="media/service-fabric-cluster-upgrade/portal-new-cluster-upgrade-mode.png" alt-text="‘고급’ 옵션에서 Azure Portal에 새 클러스터를 만들 때 자동 또는 수동 업그레이드 중에서 선택":::

기존 클러스터 리소스의 **패브릭 업그레이드** 섹션에서 자동 업그레이드 또는 수동 업그레이드 간에 전환할 수도 있습니다.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Azure Portal에서 클러스터 리소스의 ‘패브릭 업그레이드’ 섹션에서 자동 또는 수동 업그레이드 선택":::

### <a name="manual-upgrades-with-azure-portal"></a>Azure Portal에서 수동 업그레이드

수동 업그레이드 옵션을 선택하는 경우 업그레이드를 시작하려면 사용 가능한 버전 드롭다운에서 선택한 다음, *저장* 하기만 하면 됩니다. 여기에서 클러스터 업그레이드가 즉시 시작됩니다.

[클러스터 상태 정책](#custom-policies-for-manual-upgrades)(노드 상태 및 클러스터에서 실행 중인 모든 애플리케이션의 상태 조합)은 업그레이드의 기간을 준수합니다. 클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다.

롤백을 일으킨 문제를 수정했으면 이전과 같은 단계에 따라 업그레이드를 다시 시작해야 합니다.

### <a name="resource-manager-template"></a>Resource Manager 템플릿

Resource Manager 템플릿을 사용하여 클러스터 업그레이드 모드를 변경하려면 *Microsoft.ServiceFabric/clusters* 리소스 정의의 `upgradeMode` 속성에 대해 *자동* 또는 *수동* 을 지정합니다. 수동 업그레이드를 선택하는 경우 `clusterCodeVersion`을 현재 [지원되는 패브릭 버전](#check-for-supported-cluster-versions)으로 설정합니다.

:::image type="content" source="./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG" alt-text="스크린샷은 구조를 반영하기 위해 들여쓰기된 일반 텍스트인 템플릿을 보여줍니다. ‘clusterCodeVersion’과 ‘upgradeMode’ 속성이 강조 표시됩니다.":::

템플릿이 성공적으로 배포되면 클러스터 업그레이드 모드의 변경 내용이 적용됩니다. 클러스터가 수동 모드이면 클러스터 업그레이드가 자동으로 시작됩니다.

[클러스터 상태 정책](#custom-policies-for-manual-upgrades)(노드 상태 및 클러스터에서 실행 중인 모든 애플리케이션의 상태 조합)은 업그레이드의 기간을 준수합니다. 클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다.

롤백을 일으킨 문제를 수정했으면 이전과 같은 단계에 따라 업그레이드를 다시 시작해야 합니다.

## <a name="wave-deployment-for-automatic-upgrades"></a>자동 업그레이드를 위한 웨이브 배포

자동 업그레이드 모드를 사용하는 경우 클러스터에서 웨이브 배포를 사용하도록 설정하는 옵션이 있습니다. 웨이브 배포를 사용하면 기본 제공 ‘베이킹 시간’으로 구분된 테스트, 스테이지 및 프로덕션 클러스터를 순서대로 업그레이드하기 위한 파이프라인을 만들어 프로덕션 클러스터를 업데이트하기 전에 예정된 Service Fabric 버전의 유효성을 검사할 수 있습니다.

### <a name="enable-wave-deployment"></a>웨이브 배포 사용

> [!NOTE]
> 웨이브 배포에는 *Microsoft.ServiceFabric/clusters* 리소스의 `2020-12-01-preview`(이상) API 버전이 필요합니다.

자동 업그레이드를 위해 웨이브 배포를 사용하도록 설정하려면 먼저 클러스터를 할당할 웨이브를 결정합니다.

* **웨이브 0**(`Wave0`): 새 Service Fabric 빌드를 릴리스하는 즉시 클러스터가 업데이트됩니다. 테스트/개발 클러스터용입니다.
* **웨이브 1**(`Wave1`): 새 빌드를 릴리스하고 1주(7일) 후에 클러스터가 업데이트됩니다. 사전 프로덕션/준비 클러스터용입니다.
* **웨이브 2**(`Wave2`): 새 빌드를 릴리스하고 2주(14일) 후에 클러스터가 업데이트됩니다. 프로덕션 클러스터용입니다.

그런 다음, 위에 나열된 웨이브 값 중 하나를 사용하여 `upgradeWave` 속성을 클러스터 리소스 템플릿에 추가하기만 하면 됩니다. 클러스터 리소스 API 버전이 `2020-12-01-preview` 이상인지 확인합니다.

```json
{
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "fabricSettings": [...],
        "managementEndpoint": ...,
        "nodeTypes": [...],
        "provisioningState": ...,
        "reliabilityLevel": ...,
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
       ...
```

업데이트된 템플릿을 배포하면 다음 업그레이드 기간과 그 이후에 지정된 웨이브에 클러스터가 등록됩니다.

클러스터 업그레이드에 실패하는 경우 추가 도움말 링크를 사용하여 [메일로 알림을 등록](#register-for-notifications)할 수 있습니다.

### <a name="register-for-notifications"></a>알림 등록

클러스터 업그레이드가 실패하면 알림을 등록할 수 있습니다. 업그레이드 실패에 관한 자세한 내용과 추가 도움말 링크가 포함된 메일이 지정된 메일 주소로 전송됩니다.

> [!NOTE]
> 업그레이드 실패에 대한 알림을 받기 위해 웨이브 배포에 등록할 필요가 없습니다.

알림에 등록하려면 클러스터 리소스 템플릿에 `notifications` 섹션을 추가하고 알림을 받을 하나 이상의 메일 주소(*받는 사람*)를 지정합니다.

```json
    "apiVersion": "2020-12-01-preview",
    "type": "Microsoft.ServiceFabric/clusters",
     ...
        "upgradeMode": "Automatic",
        "upgradeWave": "Wave1",
        "notifications": [
        {
            "isEnabled": true,
            "notificationCategory": "WaveProgress",
            "notificationLevel": "Critical",
            "notificationTargets": [
            {
                "notificationChannel": "EmailUser",
                "receivers": [
                    "devops@contoso.com"
                ]
            }]
        }]
```

업데이트된 템플릿을 배포하고 나면 업그레이드 실패 알림에 등록됩니다.

## <a name="custom-policies-for-manual-upgrades"></a>수동 업그레이드에 관한 사용자 지정 정책

수동 클러스터 업그레이드에 관한 사용자 지정 상태 정책을 지정할 수 있습니다. 이 정책은 새 런타임 버전을 선택할 때마다 적용되어 클러스터 업그레이드를 시작하도록 시스템을 트리거합니다. 정책을 재정의하지 않으면 기본값이 사용됩니다.

사용자 지정 상태 정책을 지정하거나 **업그레이드 정책** 의 *사용자 지정* 옵션을 선택하여 Azure Portal에 있는 클러스터 리소스의 **패브릭 업그레이드** 섹션에서 현재 설정을 검토할 수 있습니다.

:::image type="content" source="./media/service-fabric-cluster-upgrade/custom-upgrade-policy.png" alt-text="업그레이드 중에 사용자 지정 상태 정책을 설정하려면 Azure Portal에서 클러스터 리소스의 ‘패브릭 업그레이드’ 섹션에서 ‘사용자 지정’ 업그레이드 정책 옵션 선택":::

## <a name="check-for-supported-cluster-versions"></a>지원 되는 클러스터 버전 확인

지원 되는 버전 및 운영 체제에 대 한 자세한 내용은 [Service Fabric 버전](service-fabric-versions.md) 을 참조 하세요.

[Azure REST API](/rest/api/azure/) 를 사용 하 여 지정 된 위치 및 구독에 사용할 수 있는 모든 사용 가능한 Service Fabric 런타임 버전 ([clusterversions](/rest/api/servicefabric/sfrp-api-clusterversions_list))을 나열할 수도 있습니다.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2018-02-01

"value": [
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
    "name": "5.0.1427.9490",
    "type": "Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.0.1427.9490",
      "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
    "name": "5.1.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "5.1.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Windows"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
    "name": "4.4.1427.9490",
    "type": " Microsoft.ServiceFabric/environments/clusterVersions",
    "properties": {
      "codeVersion": "4.4.1427.9490",
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "environment": "Linux"
    }
  }
]
}
```

출력의 `supportExpiryUtc`는 지정된 릴리스가 만료될 시기나 만료된 시기를 보고합니다. 최신 릴리스는 유효한 날짜를 포함하지 않으며 *9999-12-31T23:59:59.9999999* 값을 포함합니다. 이는 만료 날짜가 아직 설정되지 않음을 의미합니다.


## <a name="check-for-supported-upgrade-path"></a>지원 되는 업그레이드 경로 확인

지원 되는 업그레이드 경로 및 관련 버전 정보는 [Service Fabric 버전](service-fabric-versions.md) 설명서를 참조할 수 있습니다. 

지원 되는 대상 버전 정보를 사용 하 여 다음 PowerShell 단계를 사용 하 여 지원 되는 업그레이드 경로를 확인할 수 있습니다.

1) Azure에 로그인
   ```PowerShell
   Login-AzAccount
   ```

2) 구독 선택
   ```PowerShell
   Set-AzContext -SubscriptionId <your-subscription>
   ```

3) API 호출
   ```PowerShell
   $params = @{ "TargetVersion" = "<target version>"}
   Invoke-AzResourceAction -ResourceId -ResourceId <cluster resource id> -Parameters $params -Action listUpgradableVersions -Force
   ```

   예제: 
   ```PowerShell
   $params = @{ "TargetVersion" = "8.1.335.9590"}
   Invoke-AzResourceAction -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabric/clusters/myCluster -Parameters $params -Action listUpgradableVersions -Force

   Output
   supportedPath
   -------------
   {8.1.329.9590, 8.1.335.9590}
   ```


## <a name="next-steps"></a>다음 단계

* [Service Fabric 업그레이드 관리](service-fabric-cluster-upgrade-version-azure.md)
* [Service Fabric 클러스터 설정](service-fabric-cluster-fabric-settings.md) 사용자 지정
* [클러스터 스케일 인 및 스케일 아웃](service-fabric-cluster-scale-in-out.md)
* [애플리케이션 업그레이드](service-fabric-application-upgrade.md)


<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
