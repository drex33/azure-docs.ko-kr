---
title: 고급 애플리케이션 업그레이드 항목
description: 이 문서에서는 서비스 패브릭 애플리케이션 업그레이드와 관련된 고급 항목을 다룹니다.
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: dca3ec669fde61be86f2424d9b314b4b26546c55
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363314"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Service Fabric 애플리케이션 업그레이드: 고급 항목

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>애플리케이션을 업그레이드하는 동안 서비스 유형 추가 또는 제거

게시된 애플리케이션에 업그레이드의 일부로 새 서비스 유형이 추가되는 경우 배포된 애플리케이션에도 새 서비스 유형이 추가됩니다. 이러한 업그레이드는 이미 애플리케이션에 포함된 서비스 인스턴스에 영향을 주지 않지만, 추가된 서비스 유형의 인스턴스를 만들어야만 새 서비스 유형이 활성화됩니다([New-ServiceFabricService](/powershell/module/servicefabric/new-servicefabricservice) 참조).

마찬가지로, 업그레이드의 일부로 애플리케이션에서 서비스 유형을 제거할 수도 있습니다. 그러나 제거하려는 서비스 유형의 모든 서비스 인스턴스를 제거한 후 업그레이드를 진행해야 합니다([Remove-ServiceFabricService](/powershell/module/servicefabric/remove-servicefabricservice) 참조).

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime"></a>상태 비저장 서비스의 계획된 가동 중지 시간 동안 연결 끊김 방지

애플리케이션/클러스터 업그레이드 또는 노드 비활성화와 같은 계획된 상태 비저장 인스턴스 가동 중지 시간의 경우 인스턴스가 중단된 후 노출된 엔드포인트를 제거하면 연결이 끊어질 수 있으며 이로 인해 연결이 강제 종료됩니다.

이 문제를 방지하려면 서비스 구성에서 *인스턴스 닫기 연기 기간* 을 추가하고 *RequestDrain* 기능을 구성하여 클러스터 내의 기존 요청이 노출된 엔드포인트에서 드레이닝되도록 허용합니다. 이는 인스턴스를 닫기 이전에 지연이 시작되기 *전에* 상태 비저장 인스턴스에 의해 보급되는 엔드포인트가 제거되기 때문에 달성됩니다. 이 지연을 통해 인스턴스가 실제로 중단되기 전에 기존 요청이 정상적으로 드레이닝될 수 있습니다. 클라이언트는 지연이 시작될 때 호출 함수를 통해 엔드포인트 변경에 대한 알림을 받게 되므로 엔드포인트를 다시 확인하고 중단되는 인스턴스에 새 요청을 보내지 않도록 할 수 있습니다. 이러한 요청은 [역방향 프록시](./service-fabric-reverseproxy.md)를 사용하는 클라이언트에서 발생하거나, 엔드포인트를 업데이트하기 위해 알림 모델([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription))에서 서비스 엔드포인트 확인 api를 사용하여 발생할 수 있습니다.

### <a name="service-configuration"></a>서비스 구성

서비스 쪽에서 지연을 구성하는 방법에는 여러 가지가 있습니다.

 * **새 서비스를 만들 때** `-InstanceCloseDelayDuration`을 지정합니다.

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>
    ```

 * **애플리케이션 매니페스트의 기본값 섹션에서 서비스를 정의하는 동안**, `InstanceCloseDelayDurationSeconds` 속성을 할당합니다.

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **기존 서비스를 업데이트할 때** `-InstanceCloseDelayDuration`을 지정합니다.

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

 * **ARM 템플릿을 통해 기존 서비스를 만들거나 업데이트할 때** 값을 지정 합니다 `InstanceCloseDelayDuration` (지원 되는 최소 API 버전: 2020-03-01).

    ```ARM template to define InstanceCloseDelayDuration of 30seconds
    {
      "apiVersion": "2020-03-01",
      "type": "Microsoft.ServiceFabric/clusters/applications/services",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
      "location": "[variables('clusterLocation')]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
      ],
      "properties": {
        "provisioningState": "Default",
        "serviceKind": "Stateless",
        "serviceTypeName": "[parameters('serviceTypeName')]",
        "instanceCount": "-1",
        "partitionDescription": {
          "partitionScheme": "Singleton"
        },
        "serviceLoadMetrics": [],
        "servicePlacementPolicies": [],
        "defaultMoveCost": "",
        "instanceCloseDelayDuration": "00:00:30.0"
      }
    }
    ```

### <a name="client-configuration"></a>클라이언트 구성

엔드포인트가 변경되었을 때 알림을 받으려면 클라이언트가 콜백을 등록해야 합니다([ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription) 참조).
변경 알림은 엔드포인트가 변경되었음을 나타냅니다. 클라이언트는 엔드포인트를 다시 확인해야 하며 더 이상 보급되지 않는 엔드포인트는 곧 중지되므로 사용하지 않아야 합니다.

### <a name="optional-upgrade-overrides"></a>선택적 업그레이드 재정의

서비스당 기본 지연 기간을 설정하는 것 외에도 동일한(`InstanceCloseDelayDurationSec`) 옵션을 사용하여 애플리케이션/클러스터를 업그레이드하는 동안 지연을 재정의할 수 있습니다.

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

재정의된 지연 기간은 호출된 업그레이드 인스턴스에만 적용되며 개별 서비스 지연 구성을 변경하지 않습니다. 예를 들어 사전 구성된 업그레이드 지연을 건너뛰기 위해 이를 사용하여 `0` 지연을 지정할 수 있습니다.

> [!NOTE]
> * 요청을 드레이닝하는 설정은 Azure Load Balancer가 드레이닝 중인 엔드포인트에 새 요청을 보내는 것을 방지할 수 없습니다.
> * 불만 기반 확인 메커니즘은 실패 후 서비스 해결을 트리거하므로 요청이 정상적으로 드레이닝되지 않습니다. 앞에서 설명한 대로 대신 [ServiceNotificationFilterDescription](/dotnet/api/system.fabric.description.servicenotificationfilterdescription)을 사용하여 엔드포인트 변경 알림을 구독하도록 개선해야 합니다.
> * 업그레이드가 영향을 주지 않는 경우 설정이 적용되지 않습니다. 예를 들어 업그레이드 중에 복제본이 중단되지 않는 경우입니다.
>
>

> [!NOTE]
> 이 기능은 클러스터 코드 버전이 7.1.XXX 이상인 경우 위에서 언급한 대로 Update-ServiceFabricService cmdlet 또는 ARM 템플릿을 사용하여 기존 서비스에서 구성할 수 있습니다.
>
>

## <a name="manual-upgrade-mode"></a>수동 업그레이드 모드

> [!NOTE]
> *Monitored* 업그레이드 모드는 모든 Service Fabric 업그레이드에 권장하는 모드입니다.
> *UnmonitoredManual* 모드는 업그레이드가 실패 또는 일시 중단된 경우에만 고려해야 합니다. 
>
>

*Monitored* 모드에서는 업그레이드가 진행되는 동안 애플리케이션이 정상 상태를 유지하도록 Service Fabric에서 상태 정책을 적용합니다. 상태 정책을 위반하는 경우 지정된 *FailureAction* 에 따라 업그레이드가 일시 중지되거나 자동으로 롤백됩니다.

*UnmonitoredManual* 모드에서는 애플리케이션 관리자가 업그레이드의 진행 상황을 총체적으로 제어합니다. 이 모드는 상태 모니터링을 우회하기 위해 사용자 지정 상태 평가 정책을 적용하거나 일반적이지 않은 업그레이드를 수행하는 경우에 유용합니다(예: 애플리케이션 데이터가 이미 손실). 이 모드에서 실행되는 업그레이드는 각 UD가 완료된 후 스스로 일시 중단되며 [Resume-servicefabricapplicationupgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade)를 사용하여 명시적으로 재개해야 합니다. 업그레이드가 일시 중단되고 사용자가 업그레이드를 다시 시작할 준비가 완료되면 업그레이드 상태가 *RollforwardPending* 으로 표시됩니다([UpgradeState](/dotnet/api/system.fabric.applicationupgradestate) 참조).

마지막으로, *UnmonitoredAuto* 모드는 사용자 입력이 필요 없고 애플리케이션 상태 정책을 평가하지 않으므로 서비스를 개발하거나 테스트하는 동안 빠른 업그레이드 반복을 수행하는 데 유용합니다.

## <a name="upgrade-with-a-diff-package"></a>diff 패키지로 업그레이드

전체 애플리케이션 패키지를 프로비전하는 대신, 전체 애플리케이션 매니페스트 및 전체 서비스 매니페스트와 함께 업데이트된 코드/config/데이터 패키지만 포함하는 diff 패키지를 프로비전하여 업그레이드를 수행할 수도 있습니다. 전체 애플리케이션 패키지는 클러스터에 애플리케이션을 처음으로 설치하는 경우에만 필요합니다. 후속 업그레이드는 전체 애플리케이션 패키지 또는 diff 패키지에서 수행할 수 있습니다.  

애플리케이션 패키지에서 찾을 수 없는 diff 패키지의 애플리케이션 매니페스트 또는 서비스 매니페스트는 자동으로 현재 프로비전된 버전으로 대체됩니다.

diff 패키지를 사용하는 시나리오는 다음과 같습니다.

* 여러 서비스 매니페스트 파일 및/또는 여러 코드 패키지, config 패키지 또는 데이터 패키지를 참조하는 대형 애플리케이션 패키지가 있는 경우.
* 애플리케이션 빌드 프로세스에서 직접 빌드 레이아웃을 생성하는 배포 시스템을 사용하는 경우. 이 경우 코드가 변경되지 않았더라도 새로 빌드된 어셈블리는 다른 체크섬을 갖습니다. 전체 애플리케이션 패키지를 사용하려면 모든 코드 패키지의 버전을 업데이트해야 합니다. diff 패키지를 사용하면 변경된 파일과 버전이 변경된 매니페스트 파일만 제공하면 됩니다.

Visual Studio를 사용하여 애플리케이션이 업그레이드되는 경우 diff 패키지가 자동으로 게시됩니다. diff 패키지를 수동으로 만들려면 애플리케이션 매니페스트 및 서비스 매니페스트를 업데이트해야 하지만 변경된 패키지만 최종 애플리케이션 패키지에 포함되어야 합니다.

예를 들어 다음 애플리케이션을 시작하겠습니다(이해하기 쉽도록 버전 번호 제공).

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

diff 패키지를 사용하여 service1의 코드 패키지만 업데이트하려 한다고 가정해 봅시다. 업데이트된 애플리케이션은 다음과 같은 버전 변화를 거쳤습니다.

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

이 경우 애플리케이션 매니페스트를 2.0.0으로 업데이트하고 service1에 대한 서비스 매니페스트가 코드 패키지 업데이트를 반영하도록 업데이트합니다. 애플리케이션 패키지에 대한 폴더 구조는 다음과 같습니다.

```text
app1/
  service1/
    code/
```

즉, 일반적인 방법으로 전체 애플리케이션 패키지를 만든 다음, 버전이 변경되지 않은 코드/config/데이터 패키지 폴더를 제거합니다.

## <a name="upgrade-application-parameters-independently-of-version"></a>버전에 관계 없이 애플리케이션 매개 변수 업그레이드

경우에 따라 매니페스트 버전을 변경하지 않고 Service Fabric 애플리케이션의 매개 변수를 변경하는 것이 좋습니다. 이는 **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell cmdlet과 함께 **-ApplicationParameter** 플래그를 사용하여 편리하게 수행할 수 있습니다. 다음 속성이 있는 Service Fabric 애플리케이션을 가정합니다.

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

이제 **Start-ServiceFabricApplicationUpgrade** cmdlet을 사용하여 애플리케이션을 업그레이드합니다. 이 예는 모니터링되는 업그레이드를 보여 주지만 모니터링되지 않는 업그레이드도 사용할 수 있습니다. 이 cmdlet에서 허용하는 플래그에 대한 전체 설명을 보려면 [Azure Service Fabric PowerShell 모듈 참조](/powershell/module/servicefabric/start-servicefabricapplicationupgrade#parameters)를 참조하세요.

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

업그레이드 후 애플리케이션에 업데이트된 매개 변수와 동일한 버전이 있는지 확인합니다.

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>애플리케이션 업그레이드 롤백

업그레이드를 세 가지 모드(*Monitored*, *UnmonitoredAuto* 또는 *UnmonitoredManual*) 중 하나로 롤포워드할 수 있지만, 롤백은 *UnmonitoredAuto* 또는 *UnmonitoredManual* 모드에서만 가능합니다. *UnmonitoredAuto* 모드에서 수행하는 롤백은 *UpgradeReplicaSetCheckTimeout* 의 기본값이 다르다는 점을 제외하고 롤포워드와 동일한 방식으로 작동합니다([애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 참조). *UnmonitoredManual* 모드에서 수행하는 롤백은 롤포워드와 동일한 방식으로 작동합니다. 롤백은 각 UD가 완료되면 자체적으로 일시 중단한 후 [ Resume-servicefabricapplicationupgrade](/powershell/module/servicefabric/resume-servicefabricapplicationupgrade)를 사용하여 명시적으로 다시 시작하고 롤백을 계속 진행해야 합니다.

*Monitored* 모드에서 *롤백* 의 *FailureAction* 이 지정된 업그레이드 상태 정책을 위반하거나([애플리케이션 업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md) 참조) 명시적으로 [Start-ServiceFabricApplicationRollback](/powershell/module/servicefabric/start-servicefabricapplicationrollback)을 사용하는 경우 롤백을 자동으로 트리거할 수 있습니다.

롤백 중에 언제든지 [Update-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/update-servicefabricapplicationupgrade)를 사용하여 *UpgradeReplicaSetCheckTimeout* 값과 모드를 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial.md)에서는 Visual Studio를 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[Powershell을 사용하여 애플리케이션 업그레이드](service-fabric-application-upgrade-tutorial-powershell.md)에서는 PowerShell을 사용하여 애플리케이션 업그레이드를 진행하는 방법을 안내합니다.

[업그레이드 매개 변수](service-fabric-application-upgrade-parameters.md)를 사용하여 애플리케이션 업그레이드 방법을 제어합니다.

[데이터 직렬화](service-fabric-application-upgrade-data-serialization.md)사용 방법을 익혀 애플리케이션 업그레이드와 호환되도록 만듭니다.

[애플리케이션 업그레이드 문제 해결](service-fabric-application-upgrade-troubleshooting.md)의 단계를 참조하여 애플리케이션 업그레이드 중 발생하는 일반적인 문제를 해결합니다.
