---
title: Azure Service Fabric 클러스터 업그레이드
description: Azure Service Fabric 클러스터를 업데이트하는 옵션에 관해 알아보기
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 7fad28e0357b26b0b8753496310c7788f6dbb0c8
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401151"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Azure Service Fabric 클러스터 업그레이드 및 업데이트

Azure Service Fabric 클러스터는 사용자 고유 리소스이지만, Microsoft에서 부분적으로 관리합니다. 이 문서에서는 Azure Service Fabric 클러스터를 업데이트하는 시기와 방법에 관한 옵션을 설명합니다.

## <a name="automatic-versus-manual-upgrades"></a>자동 업그레이드 대 수동 업그레이드

Service Fabric 클러스터가 항상 [지원되는 런타임 버전](service-fabric-versions.md)을 실행하는지 확인하는 것이 중요합니다. Microsoft에서 새로운 버전의 Service Fabric 릴리스를 발표할 때마다 이전 버전은 해당 날짜부터 최소 60일 후 *지원 종료일* 되는 것으로 표시됩니다. 새로운 릴리스는 [Service Fabric 팀 블로그](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)에서 발표됩니다.

클러스터가 실행 중인 릴리스가 만료되기 14일 전에, 클러스터를 *경고* 성능 상태로 전환하는 상태 이벤트가 생성됩니다. 지원되는 런타임 버전으로 업그레이드할 때까지 클러스터는 경고 상태로 유지됩니다.

Microsoft에서 릴리스하는 대로 자동 Service Fabric 업그레이드를 받도록 클러스터를 설정하거나 현재 지원되는 버전 목록에서 수동으로 선택할 수 있습니다. 해당 옵션은 Service Fabric 클러스터 리소스의 **패브릭 업그레이드** 섹션에서 사용할 수 있습니다.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Azure Portal에서 클러스터 리소스의 ‘패브릭 업그레이드’ 섹션에서 자동 또는 수동 업그레이드를 선택":::합니다.

클러스터 업그레이드 모드를 설정하고 [Resource Manager 템플릿을 사용](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template)하여 런타임 버전을 선택할 수도 있습니다.

자동 업그레이드가 권장되는 업그레이드 모드입니다. 이 옵션을 사용하면 클러스터가 지원되는 상태를 유지하고 최신 수정 및 기능의 이점을 얻을 수 있을 뿐만 아니라, [웨이브 배포](#wave-deployment-for-automatic-upgrades) 전략을 사용하여 워크로드 중단을 최소화하는 방식으로 업데이트를 예약할 수 있기 때문입니다.

> [!NOTE]
> 기존 클러스터를 자동 모드로 변경하면 새 릴리스부터 다음 업그레이드 기간 동안 클러스터가 등록됩니다. 새로운 릴리스는 [Service Fabric 팀 블로그](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)에서 발표됩니다. 업그레이드 기간당 가능한 가장 높은 업그레이드 경로가 선택됩니다. [지원되는 버전을 참조하세요.](service-fabric-versions.md) 수동 업그레이드 모드는 즉시 업그레이드를 트리거합니다. 

## <a name="wave-deployment-for-automatic-upgrades"></a>자동 업그레이드를 위한 웨이브 배포

웨이브 배포를 사용하면 워크로드에 따라 업그레이드의 완성도 수준을 선택하여 클러스터 업그레이드 중단을 최소화할 수 있습니다. 예를 들어, 프로덕션 워크로드에 적용하기 전에 런타임 업그레이드의 호환성을 테스트하기 위해 다양한 Service Fabric 클러스터의 *테스트* -> *스테이지* -> *프로덕션* 웨이브 배포 파이프라인을 설정할 수 있습니다.

웨이브 배포를 옵트인하려면 배포 템플릿에서 클러스터에 대해 다음 웨이브 값 중 하나를 지정합니다.

* **웨이브 0**: 새 Service Fabric 빌드를 릴리스하는 즉시 클러스터가 업데이트됩니다. 테스트/개발 클러스터용입니다.
* **웨이브 1**: 새 빌드를 릴리스하고 1주(7일) 후에 클러스터가 업데이트됩니다. 사전 프로덕션/준비 클러스터용입니다.
* **웨이브 2**: 새 빌드를 릴리스하고 2주(14일) 후에 클러스터가 업데이트됩니다. 프로덕션 클러스터용입니다.

클러스터 업그레이드에 실패하는 경우 추가 도움말 링크를 사용하여 메일로 알림을 등록할 수 있습니다. 시작하려면 [자동 업그레이드를 위한 웨이브 배포](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades)를 참조하세요.

## <a name="phases-of-automatic-upgrade"></a>자동 업그레이드 단계

Microsoft는 Azure 클러스터에서 실행하는 Service Fabric 런타임 코드 및 구성을 유지 관리합니다. 필요한 기준으로 소프트웨어에 자동 모니터링된 업그레이드를 수행합니다. 이러한 업그레이드는 코드, 구성 또는 둘 모두가 될 수 있습니다. 해당 업그레이드가 애플리케이션에 미치는 영향을 최소화하기 위해 다음 단계에서 수행됩니다.

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>1단계: 모든 클러스터 상태 정책을 사용하여 업그레이드 수행

이 단계 동안 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행하고 클러스터에서 실행 중이던 애플리케이션은 가동 중지 시간 없이 계속 실행합니다. 업그레이드 중에 클러스터 상태 정책(노드 상태 및 애플리케이션 상태)을 준수합니다.

클러스터 상태 정책이 충족되지 않으면 업그레이드가 롤백되고 구독 소유자에게 메일이 전송됩니다. 전자 메일에는 다음 정보가 포함되어 있습니다.

* 클러스터 업그레이드를 롤백해야 했다는 알림.
* 권장 수정 작업(있는 경우).
* 2단계를 실행할 때까지의 일 수(*n*).

인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 메일이 전송된 날짜에서 *n* 일 후 2단계로 진행합니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이 상황은 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 과도한 메일 발송을 피하기 위해 성공적으로 실행될 때 메일로 확인하지 않습니다. 메일을 받으면 정상적인 작업에 대한 예외가 발생했음을 나타냅니다. 클러스터 업그레이드가 애플리케이션 가용성에 영향을 주지 않고 성공되기를 예상합니다.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>2단계: 기본 상태 정책만을 사용하여 업그레이드 수행

이 단계의 상태 정책은 업그레이드 시작 시 정상 상태가 업그레이드 프로세스의 기간에 동일하게 유지되는 애플리케이션의 수 방식으로 설정됩니다. 1단계와 마찬가지로 2단계 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행하고 클러스터에서 실행 중이던 애플리케이션은 가동 중지 시간 없이 계속 실행합니다. 클러스터 상태 정책(노드 상태 및 클러스터에서 실행 중인 모든 애플리케이션의 상태 조합)은 업그레이드의 기간을 준수합니다.

적용되는 클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 그런 다음 구독 소유자에게 전자 메일이 전송됩니다. 전자 메일에는 다음 정보가 포함되어 있습니다.

* 클러스터 업그레이드를 롤백해야 했다는 알림.
* 권장 수정 작업(있는 경우).
* 3단계를 실행할 때까지 일 수(n)

인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 미리 알림 전자 메일은 n일이 끝나기 몇 일 전에 전송됩니다. 전자 메일이 전송된 날짜에서 n일 후 3단계로 진행합니다. 2단계에서 전송한 전자 메일은 심각하게 받아들여져야 하며 수정 작업이 수행되어야 합니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이는 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 성공적 실행에 대한 전자 메일 확인은 없습니다.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>3단계: 까다로운 상태 정책을 사용하여 업그레이드 수행

이 단계의 이러한 상태 정책은 애플리케이션의 상태보다 업그레이드 완료에 맞춰집니다. 이 단계에서 매우 적은 클러스터 업그레이드가 끝납니다. 클러스터가 이 단계에 도달하는 경우 애플리케이션이 비정상이 되고/되거나 가용성이 손실될 수 있습니다.

다른 두 단계와 마찬가지로 3단계 업그레이드는 한 번에 하나의 업그레이드 도메인을 진행합니다.

클러스터 상태 정책이 충족되지 않는 경우 업그레이드가 롤백됩니다. 인프라 이유로 업그레이드가 실패한 경우 동일한 업그레이드를 몇 번 더 실행하기 위해 노력합니다. 그런 다음 지원 및/또는 업그레이드를 더 이상 받지 않도록 클러스터가 고정됩니다.

이 정보가 있는 메일이 수정 작업과 함께 구독 소유자에게 전송됩니다. 3단계가 실패된 상태로 클러스터가 도달하지 않을 것입니다.

클러스터 상태 정책이 충족하는 경우 업그레이드가 성공한 것으로 간주하고 완료로 표시됩니다. 이는 초기 업그레이드 또는 이 단계의 업그레이드 다시 실행 중 발생할 수 있습니다. 성공적 실행에 대한 전자 메일 확인은 없습니다.

## <a name="custom-policies-for-manual-upgrades"></a>수동 업그레이드에 관한 사용자 지정 정책

수동 클러스터 업그레이드에 관한 사용자 지정 정책을 지정할 수 있습니다. 이 정책은 새 런타임 버전을 선택할 때마다 적용되어 클러스터 업그레이드를 시작하도록 시스템을 트리거합니다. 정책을 재정의하지 않으면 기본값이 사용됩니다. 자세한 내용은 [수동 업그레이드에 대한 사용자 지정 정책 설정](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades)을 참조하세요.

## <a name="other-cluster-updates"></a>기타 클러스터 업데이트

런타임을 업그레이드하는 것 외에 다음을 포함하여 클러스터를 최신 상태로 유지하기 위해 수행해야 할 여러 다른 작업이 있습니다.

### <a name="managing-certificates"></a>인증서 관리

Service Fabric은 클러스터 노드 간 통신을 보호하도록 클러스터를 만들고 클라이언트를 인증할 때 지정하는 [X.509 서버 인증서](service-fabric-cluster-security.md)를 사용합니다. [Azure Portal](https://portal.azure.com)에서 또는 PowerShell/Azure CLI를 사용하여 클러스터 및 클라이언트에 대한 인증서를 추가, 업데이트 또는 삭제할 수 있습니다.  자세히 알아보려면 [인증서 추가 또는 제거](service-fabric-cluster-security-update-certs-azure.md)를 참조하세요.

### <a name="opening-application-ports"></a>애플리케이션 포트 열기

노드 유형에 연결된 부하 분산 장치 리소스 속성을 변경하여 애플리케이션 포트를 변경할 수 있습니다. Azure Portal을 사용하거나 PowerShell/Azure CLI를 사용할 수 있습니다. 자세한 내용은 [클러스터에 대한 애플리케이션 포트 열기](create-load-balancer-rule.md)를 참조하세요.

### <a name="defining-node-properties"></a>노드 속성 정의

때로는 특정 워크로드가 클러스터의 특정 노드 형식에서만 실행되도록 하려고 할 수 있습니다. 예를 들어, 일부 워크로드는GPU 또는 SSD가 필요할 수 있습니다. 클러스터에서 각 노드 형식에 대해 클러스터 노드에 사용자 지정 노드 속성을 추가할 수 있습니다. 배치 제약 조건은 하나 이상의 노드 속성에 대해 선택하는 개별 서비스에 연결되는 명령문입니다. 배치 제약 조건은 서비스를 실행해야 하는 위치를 정의합니다.

배치 제약 조건의 사용, 노드 속성 및 정의하는 방법에 대한 자세한 내용은 [노드 속성 및 배치 제약 조건](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)을 참조하세요.

### <a name="adding-capacity-metrics"></a>용량 메트릭 추가

각 노드 유형의 경우 부하를 보고하도록 사용하려는 사용자 용량 메트릭을 애플리케이션에 추가할 수 있습니다. 부하를 보고하는 용량 메트릭 사용에 대한 자세한 내용은 서비스 패브릭 클러스터 리소스 관리자 설명서에서 [클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md) 및 [메트릭 및 부하](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

### <a name="customizing-settings-for-your-cluster"></a>클러스터의 설정 사용자 지정

클러스터에서 클러스터 및 노드 속성의 안정성 수준과 같은 다양한 다른 구성 설정을 사용자 지정할 수 있습니다. 자세한 내용은 [Service Fabric 클러스터 패브릭 설정](service-fabric-cluster-fabric-settings.md)을 참조하세요.

### <a name="upgrading-os-images-for-cluster-nodes"></a>클러스터 노드의 OS 이미지 업그레이드

Service Fabric 클러스터 노드에 자동 OS 이미지 업그레이드를 사용하는 것이 가장 좋습니다. 이 작업을 수행하려면 몇 가지 클러스터 요구 사항과 단계를 수행해야 합니다. 다른 옵션은 POA(패치 오케스트레이션 애플리케이션)가 Service Fabric 클러스터에서 가동 중지 시간 없이 운영 체제 패치를 자동화하는 Service Fabric 애플리케이션을 사용하는 것입니다. 이 옵션에 관해 자세히 알아보려면 [Service Fabric 클러스터에서 Windows 운영 체제 패치](how-to-patch-cluster-nodes-windows.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Service Fabric 업그레이드 관리](service-fabric-cluster-upgrade-version-azure.md)
* [Service Fabric 클러스터 설정](service-fabric-cluster-fabric-settings.md) 사용자 지정
* [클러스터 스케일 인 및 스케일 아웃](service-fabric-cluster-scale-in-out.md)
* [애플리케이션 업그레이드](service-fabric-application-upgrade.md)
