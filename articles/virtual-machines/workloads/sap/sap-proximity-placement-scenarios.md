---
title: SAP 애플리케이션용 Azure 근접 배치 그룹 | Microsoft Docs
description: Azure 근접 배치 그룹을 사용하는 SAP 배포 시나리오에 대해 설명합니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccbced3ce40856f858fa7e0028a27c1e1c59460
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484684"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>SAP 애플리케이션에서 네트워크 대기 시간을 최적화하기 위한 Azure 근접 배치 그룹

> [!IMPORTANT]
> 2021년 11월에 영역 배포에서 SAP 워크로드와 함께 근접 배치 그룹을 사용하는 방식이 크게 변경되었습니다. 

SAP NetWeaver 또는 SAP S/4HANA 아키텍처를 기반으로 하는 SAP 애플리케이션은 SAP 애플리케이션 계층과 SAP 데이터베이스 계층 간의 네트워크 대기 시간에 민감합니다. 이러한 민감도는 애플리케이션 레이어에서 실행되는 대부분의 비즈니스 논리에 따른 결과입니다. SAP 애플리케이션 레이어는 비즈니스 논리를 실행하므로 데이터베이스 계층에 대한 쿼리를 초당 수천 또는 수만 개 속도의 높은 빈도로 실행합니다. 대부분의 경우 이러한 쿼리의 특성은 간단합니다. 즉 데이터베이스 계층에서 500마이크로초 이하로 실행되는 경우가 많습니다.

애플리케이션 계층에서 데이터베이스 계층으로 이러한 쿼리를 보내고 다시 보낸 결과를 받는 데 소요된 시간은 비즈니스 프로세스를 실행하는 데 걸리는 시간에 큰 영향을 줍니다. 이러한 네트워크 대기 시간 민감도는 SAP 배포 프로젝트에서 특정 최소 네트워크 대기 시간을 달성하려는 이유입니다. 네트워크 대기 시간을 분류하는 방법에 대한 지침은 [SAP Note #1100926 - FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)을 참조하세요.

많은 Azure 지역에서 데이터 센터의 수가 증가했습니다. 이와 동시에 고객은 특히 고급 SAP 시스템의 경우 M 또는 Mv2 제품군과 같은 보다 특별한 VM 제품군을 사용하거나 드문 경우에서 HANA 큰 인스턴스를 사용하고 있습니다. 이러한 Azure 가상 머신 유형은 Azure 지역에 수집되는 각 데이터 센터에서 항상 사용할 수 있는 것은 아닙니다. 이러한 사실은 SAP 애플리케이션 레이어와 SAP DBMS 레이어 간의 네트워크 대기 시간을 최적화할 수 있는 기회를 창출할 수 있습니다.

Azure는 네트워크 대기 시간을 최적화할 수 있도록 [근접 배치 그룹](../../co-location.md)을 제공합니다. 근접 배치 그룹을 사용하여 단일 네트워크 에서 서로 다른 VM 유형을 강제로 그룹화할 수 있습니다. 이 그룹은 아직 제공되지 않은 서로 다른 VM 유형 간에 네트워크 대기 시간이 충분히 낮습니다. 이러한 근접 배치 그룹에 첫 번째 VM을 배포하는 과정에서 VM은 특정 네트워크 침입에 바인딩됩니다. 동일한 근접 배치 그룹에 배포될 다른 모든 VM과 마찬가지로 해당 VM은 동일한 네트워크 네트워크 네트워크 아래에 그룹화됩니다. 이 잠재 고객처럼 매력적인 구문의 사용은 몇 가지 제한 사항과 문제도 도입합니다.

- 모든 Azure VM 유형은 모든 Azure 데이터 센터 또는 각 네트워크 에서 사용할 수 있다고 가정할 수 없습니다. 따라서 하나의 근접 배치 그룹 내에서 서로 다른 VM 유형의 조합을 심각하게 제한할 수 있습니다. 이러한 제한은 특정 VM 유형을 실행하는 데 필요한 호스트 하드웨어가 데이터 센터 또는 근접 배치 그룹이 할당된 네트워크 아래에 없을 수 있기 때문에 발생합니다.
- 하나의 근접 배치 그룹 내에 있는 VM의 일부 크기를 조정하면 모든 경우에 새 VM 유형을 동일한 데이터 센터 또는 네트워크 아래에서 사용할 수 있다고 자동으로 가정할 수 없습니다. 근접 배치 그룹이 할당되었습니다.
- Azure는 하드웨어를 해제할 때 근접 배치 그룹의 특정 VM을 다른 Azure 데이터 센터 또는 다른 네트워크 침입으로 강제할 수 있습니다. 이 사례에 대한 자세한 내용은 [근접 배치 그룹](../../co-location.md#planned-maintenance-and-proximity-placement-groups) 문서를 읽어보기  

> [!IMPORTANT]
> 잠재적인 제한으로 인해 근접 배치 그룹은 다음만 사용해야 합니다.
>
> - 특정 시나리오에서 필요한 경우(나중에 참조)
> - 애플리케이션 계층과 DBMS 계층 간의 네트워크 대기 시간이 너무 높고 워크로드에 영향을 미치는 경우
> - 전체 시스템 환경 또는 전체 SAP 환경이 아닌 단일 SAP 시스템의 세분성에서만
> - 다른 VM 유형과 근접 배치 그룹 내의 VM 수를 최소로 유지하는 방식에서


지금까지 근접 배치 그룹을 사용한 시나리오는 다음과 같습니다.

- 가용성 집합을 통해 SAP 워크로드 배포 SAP 데이터베이스 계층, SAP 애플리케이션 계층 및 ASCS/SCS VM이 세 가지 가용성 집합으로 그룹화되었습니다. 이러한 경우 Azure 지역에 따라 네트워크 대기 시간이 발생하여 SAP 워크로드에 부정적인 영향을 줄 수 있기 때문에 가용성 집합이 전체 Azure 지역에 분산되지 않았는지 확인하려고 했습니다.
- SAP 워크로드의 중요한 리소스를 서로 다른 가용성 영역 배포하고, 반면에 가용성 집합을 사용하여 각 영역에 있는 애플리케이션 계층의 VM이 서로 다른 장애 도메인에 분산되도록 하려고 했습니다. 이 경우 나중에 문서에 설명된 대로 근접 배치 그룹은 필요한 붙입니다.
- 근접 배치 그룹을 사용하여 VM을 그룹화하여 VM에서 호스트되는 서비스 간에 최적의 네트워크 대기 시간을 달성했습니다.

배포 시나리오 #1의 경우, 특히 가용성 영역 없는 지역과 가용성 영역 있는 대부분의 지역에서 VM이 있는 위치에 독립적인 네트워크 대기 시간이 허용됩니다. Azure의 일부 지역에서는 가용성 집합을 사용하는 세 가지 다른 가용성 집합을 배치하지 않고는 충분히 좋은 환경을 제공할 수 없습니다. 배포 시나리오 #2에서는 이 문서의 다음 섹션에서 근접 배치 그룹을 사용하는 다른 방법을 권장합니다.



## <a name="what-are-proximity-placement-groups"></a>근접 배치 그룹이란? 
Azure 근접 배치 그룹은 논리적 구조입니다. 근접 배치 그룹이 정의되면 Azure 지역 및 Azure 리소스 그룹에 바인딩됩니다. VM이 배포되면 다음에서 근접 배치 그룹을 참조합니다.

- 네트워크 아래에 배포된 첫 번째 Azure VM은 많은 Azure 컴퓨팅 단위와 낮은 네트워크 대기 시간으로 가득합니다. 이러한 네트워크 집계는 종종 단일 Azure 데이터 센터와 일치합니다. 첫 번째 가상 머신은 결국 배포 매개 변수와 결합되는 Azure 할당 알고리즘을 기반으로 컴퓨팅 배율 단위에 배포되는 "범위 VM"으로 생각할 수 있습니다.
- 근접 배치 그룹을 참조하는 배포된 모든 후속 VM은 첫 번째 가상 머신과 동일한 네트워크 블로어 아래에 배포됩니다.

> [!NOTE]
> 첫 번째 VM이 배치된 네트워크 스트루에서 특정 VM 유형을 실행할 수 있는 배포된 호스트 하드웨어가 없는 경우 요청된 VM 유형의 배포가 성공하지 못합니다. 근접 배치 그룹의 경계 내에서 VM을 지원할 수 없다는 할당 오류 메시지가 표시됩니다.

하나의 [Azure 리소스 그룹](../../../azure-resource-manager/management/manage-resources-portal.md)에 여러 근접 배치 그룹을 할당할 수 있습니다. 그러나 근접 배치 그룹은 하나의 Azure 리소스 그룹에만 할당할 수 있습니다.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Azure VM만 사용하는 SAP 시스템이 포함된 근접 배치 그룹
이 섹션에서는 지금까지 사용된 배포 아키텍처와 새로운 권장 사항을 안내합니다.

### <a name="proximity-placement-groups-with-zonal-deployments"></a>영역 배포가 있는 근접 배치 그룹
HANA 큰 인스턴스를 사용하지 않는 배포의 경우 SAP 애플리케이션 계층과 DBMS 계층 간에 네트워크 대기 시간을 상당히 낮게 제공해야 합니다. 제한된 시나리오 집합에 대해 네트워크 대기 시간을 상당히 낮게 설정하기 위해 이러한 SAP 시스템에 대해 Azure 근접 배치 그룹을 정의할 수 있습니다.

여러 SAP 프로덕션 또는 비프로덕션 시스템을 단일 근접 배치 그룹으로 묶지 않도록 합니다. 근접 배치 그룹에서 그룹화할 시스템이 많을수록 가능성이 높기 때문에 SAP 시스템 번들을 사용하지 마십시오.

- 근접 배치 그룹이 할당된 네트워크 부족에서 사용할 수 없는 VM 유형이 필요합니다.
- 시간이 지남에 따라 VM 수를 근접 배치 그룹으로 확장해야 하는 경우 M 시리즈 VM과 같은 비분류 VM의 리소스가 결국 채워지지 않을 수 있습니다.

지금까지 권장한 근접 배치 그룹 사용량은 이 그래픽과 같습니다.

![영역이 있는 이전 근접 배치 그룹](./media/sap-proximity-placement-scenarios/vm-ppg-zone-old.png)

SAP 시스템을 배포한 두 가용성 영역 각각에 PPG(근접 배치 그룹)를 만들었습니다. 특정 영역의 모든 VM은 해당 특정 영역의 개별 근접 배치 그룹에 속합니다. 각 영역에서 시작하여 DBMS VM을 배포하여 PPG의 범위를 지정한 다음, ASCS VM을 동일한 영역 및 PPG에 배포했습니다. 세 번째 단계에서는 Azure 가용성 집합을 만들고, 가용성 집합을 범위가 지정된 PPG에 할당하고, SAP 애플리케이션 계층을 배포했습니다. 이 구성의 장점은 모든 구성 요소가 동일한 네트워크 부족 아래에 잘 정렬되었다는 것입니다. 큰 단점은 가상 머신 크기를 조정하는 유연성이 제한될 수 있다는 것입니다.


Microsoft에서 Azure 지역에 배포하여 Azure 가용성 영역 내의 네트워크 대기 시간을 줄이기 위해 배포한 많은 개선에 따라 영역 배포에 대한 새로운 배포 지침은 다음과 같습니다.

![영역이 있는 새 근접 배치 그룹](./media/sap-proximity-placement-scenarios/vm-ppg-zone.png)

지금까지 제공된 권장 사항의 차이점은 두 영역의 데이터베이스 VM이 더 이상 근접 배치 그룹에 속하지 않는다는 것입니다. 이제 영역당 근접 배치 그룹의 범위는 SAP ASCS/SCS 인스턴스를 실행하는 VM의 배포로 지정됩니다. 즉, 여러 데이터 센터에서 가용성 영역 수집되는 지역의 경우 ASCS/SCS 인스턴스 및 애플리케이션 계층이 한 네트워크 에서 실행되고 데이터베이스 VM이 다른 네트워크 네트워크에서 실행될 수 있습니다. 네트워크가 향상되었지만 SAP 애플리케이션 계층과 DBMS 계층 간의 네트워크 대기 시간이 충분히 양호한 성능 및 처리량에 충분해야 합니다. 이 새 구성의 이점은 VM 크기를 조정하거나 SAP 시스템의 DBMS 계층 또는/및 애플리케이션 계층을 사용하여 새 VM 유형으로 이동하는 데 더 많은 유연성이 있다는 것입니다. 


### <a name="proximity-placement-groups-with-availability-set-deployments"></a>가용성 집합 배포가 있는 근접 배치 그룹
이 경우 근접 배치 그룹을 사용하여 다른 가용성 집합을 통해 배포된 VM을 배치하는 것이 목적입니다. 이 사용 시나리오에서는 지역의 여러 가용성 영역 제어된 배포를 사용하지 않습니다. 대신 가용성 집합을 사용하여 SAP 시스템을 배포하려고 합니다. 결과적으로 DBMS VM, ASCS/SCS VM 및 애플리케이션 계층 VM에 대한 가용성 집합이 적어도 있습니다. VM 배포 시 가용성 집합 및 가용성 영역을 지정할 수 없으므로 다른 가용성 집합의 VM이 할당될 위치를 제어할 수 없습니다. 이로 인해 일부 Azure 지역에서 서로 다른 VM 간의 네트워크 대기 시간이 여전히 너무 높아서 충분한 성능 환경을 제공할 수 없습니다. 따라서 결과 아키텍처는 다음과 같습니다.


![AvSets가 있는 근접 배치 그룹](./media/sap-proximity-placement-scenarios/vm-ppg-avsets.png)

이 그래픽에서는 단일 근접 배치 그룹이 단일 SAP 시스템에 할당됩니다. 이 PPG는 세 가지 가용성 집합에 할당됩니다. 그런 다음, 첫 번째 데이터베이스 계층 VM을 DBMS 가용성 집합에 배포하여 근접 배치 그룹의 범위를 설정합니다. 이 아키텍처 권장 사항은 동일한 네트워크 네트워크 아래에 있는 모든 VM을 함께 정렬합니다. 이 문서의 앞부분에서 언급한 제한 사항도 소개합니다. 따라서 근접 배치 그룹 아키텍처는 드물게 사용해야 합니다.


## <a name="proximity-placement-groups-and-hana-large-instances"></a>근접 배치 그룹 및 HANA(대규모 인스턴스)
일부 SAP 시스템에서 [HANA(대규모 인스턴스)](./hana-overview-architecture.md)를 애플리케이션 레이어에 사용하는 경우 [수정 버전 4 행 또는 스탬프](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)에 배포된 HANA(대규모 인스턴스) 단위를 사용하면 HANA(대규모 인스턴스) 단위와 Azure VM 간의 네트워크 대기 시간이 크게 향상될 수 있습니다. 향상된 한 가지 사항은 HANA(대규모 인스턴스) 단위가 배포될 때 근접 배치 그룹과 함께 배포된다는 것입니다. 이 근접 배치 그룹을 사용하여 애플리케이션 레이어 VM을 배포할 수 있습니다. 따라서 이러한 VM은 HANA(대규모 인스턴스) 단위를 호스팅하는 동일한 데이터 센터에 배포됩니다.

HANA(대규모 인스턴스) 단위가 수정 버전 4 스탬프 또는 행에 배포되었는지 확인하려면 [Azure Portal을 통한 Azure HANA(대규모 인스턴스) 제어](./hana-li-portal.md#look-at-attributes-of-single-hli-unit) 문서를 확인합니다. HANA(대규모 인스턴스) 단위를 배포할 때 만들어졌으므로 HANA(대규모 인스턴스) 단위의 특성 개요에서 근접 배치 그룹의 이름을 확인할 수도 있습니다. 특성 개요에 표시되는 이름은 애플리케이션 레이어 VM을 배포해야 하는 근접 배치 그룹의 이름입니다.

Azure 가상 머신만 사용하는 SAP 시스템과 비교하여 HANA(대규모 인스턴스)를 사용하는 경우 사용할 [Azure 리소스 그룹](../../../azure-resource-manager/management/manage-resources-portal.md)의 수를 결정하는 데 있어 유연성이 떨어집니다. [이 문서](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal)에서 설명한 대로 [HANA(대규모 인스턴스) 테넌트](./hana-know-terms.md)의 모든 HANA(대규모 인스턴스) 단위는 단일 리소스 그룹으로 그룹화됩니다. 예를 들어 프로덕션 시스템과 비 프로덕션 시스템 또는 다른 시스템과 같이 분리하기 위해 다른 테넌트에 배포하는 경우를 제외하고는 모든 HANA(대규모 인스턴스) 단위가 하나의 HANA(대규모 인스턴스) 테넌트에 배포됩니다. 이 테넌트는 리소스 그룹과 일대일 관계에 있습니다. 그러나 각 단일 단위에 대해 별도의 근접 배치 그룹이 정의됩니다.

따라서 단일 테넌트에 대한 Azure 리소스 그룹과 근접 배치 그룹 간의 관계는 다음과 같습니다.

![근접 배치 그룹 및 HANA(대규모 인스턴스)](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>근접 배치 그룹을 사용한 배포의 예제
다음은 Azure 근접 배치 그룹을 사용하여 VM을 배포하는 데 사용할 수 있는 몇 가지 PowerShell 명령입니다.

[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)에 로그인한 후 첫 번째 단계는 배포에 사용하려는 Azure 구독에 있는지 확인하는 것입니다.

<pre><code>
Get-AzureRmContext
</code></pre>

다른 구독으로 변경해야 하는 경우 다음 명령을 실행하여 변경할 수 있습니다.

<pre><code>
Set-AzureRmContext -Subscription "PPG test subscription"
</code></pre>

다음 명령을 실행하여 새 Azure 리소스 그룹을 만듭니다.

<pre><code>
New-AzResourceGroup -Name "ppgexercise" -Location "westus2"
</code></pre>

다음 명령을 실행하여 새 근접 배치 그룹을 만듭니다.

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate" -Location "westus2"
</code></pre>

다음과 같은 명령을 사용하여 첫 번째 VM을 근접 배치 그룹에 배포합니다.

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "ppgscopevm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

이전 명령에서는 Windows 기반 VM을 배포합니다. 이 VM을 배포 하 고 나면 근접 배치 그룹의 네트워크 옆면 범위가 Azure 지역 내에서 정의 됩니다. 이전 명령에 표시 된 것과 같이 근접 배치 그룹을 참조 하는 모든 후속 VM 배포는 해당 네트워크 옆면에 배치 된 하드웨어에서 VM 유형을 호스트 하 고 해당 VM 유형의 용량을 사용할 수 있는 경우 동일한 네트워크 옆면에 배포 됩니다.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>근접 배치 그룹과 가용성 집합 및 가용성 영역 결합
SAP 시스템 배포에 가용성 영역를 사용 하는 문제 중 하나는 특정 가용성 영역 내에서 가용성 집합을 사용 하 여 SAP 응용 프로그램 계층을 배포할 수 없다는 것입니다. Sap 응용 프로그램 계층을 SAP ASCS/SCS Vm과 동일한 영역에 배포 하려고 합니다. 단일 VM을 배포할 때 가용성 영역 및 가용성 집합을 참조 하는 것은이 시점에서 가능 하지 않습니다. 그러나 가용성 영역을 지시 하는 VM을 배포 하기만 하면 응용 프로그램 계층 Vm이 여러 업데이트 및 실패 도메인에 분산 되도록 하는 기능이 손실 됩니다.

근접 배치 그룹을 사용하면 이 제한을 무시할 수 있습니다. 배포 시퀀스는 다음과 같습니다.

- 근접 배치 그룹을 만듭니다.
- 가용성 영역을 참조 하 여 권장 되는 ASCS/SCS VM 인 앵커 VM을 배포 합니다.
- Azure 근접 그룹을 참조하는 가용성 집합을 만듭니다. (이 문서의 뒷부분에 나오는 명령을 참조하세요.)
- 가용성 집합 및 근접 배치 그룹을 참조하여 애플리케이션 레이어 VM을 배포합니다.

이전 섹션에서 설명한 대로 첫 번째 VM을 배포하는 대신, VM을 배포할 때 가용성 영역 및 근접 배치 그룹을 참조합니다.

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "centralserviceszone1" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "collocate" -Size "Standard_E8s_v4"
</code></pre>

이 가상 머신이 성공적으로 배포 되 면 하나의 가용성 영역에서 SAP 시스템의 ASCS/SCS 인스턴스를 호스팅합니다. 근접 배치 그룹의 범위는 사용자가 정의한 가용성 영역에서 네트워크 spines 중 하나로 고정 됩니다.

다음 단계에서는 SAP 시스템의 애플리케이션 레이어에 사용하려는 가용성 집합을 만들어야 합니다.

근접 배치 그룹을 정의하고 만듭니다. 가용성 집합을 만드는 명령에는 이름이 아닌 근접 배치 그룹 ID에 대한 추가 참조가 필요합니다. 다음 명령을 사용하여 근접 배치 그룹의 ID를 가져올 수 있습니다.

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate"
</code></pre>

가용성 집합을 만들 때 관리 디스크(달리 지정되지 않은 경우 기본값) 및 근접 배치 그룹을 사용하는 경우 다음과 같은 추가 매개 변수를 고려해야 합니다.

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "ppgexercise" -Name "ppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

세 개의 장애 도메인을 사용하는 것이 가장 좋습니다. 그러나 지원되는 장애 도메인의 수는 지역마다 다를 수 있습니다. 이 경우 특정 지역에 사용할 수 있는 최대 장애 도메인 수는 2개입니다. 애플리케이션 레이어 VM을 배포하려면 다음과 같이 가용성 집합 이름 및 근접 배치 그룹 이름에 대한 참조를 추가해야 합니다.

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "appinstance1" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

이 배포의 결과는 다음과 같습니다.
- 특정 가용성 영역 또는 가용성 영역에 있는 SAP 시스템에 대 한 중앙 서비스입니다.
- SAP 중앙 서비스 (ASCS/SCS) VM 또는 Vm과 동일한 네트워크 옆면의 가용성 집합을 통해 위치한 SAP 응용 프로그램 계층입니다.

> [!NOTE]
> 하나의 영역에 DBMS 및 ASCS/SCS Vm을 배포 하 고 다른 영역에 두 번째 DBMS 및 ASCS/SCS Vm을 배포 하 여 고가용성 구성을 만들려면 각 영역에 대해 다른 근접 배치 그룹이 필요 합니다. 사용하는 모든 가용성 집합에 대해서도 마찬가지입니다.

## <a name="change-proximity-placement-group-configurations-of-an-existing-system"></a>기존 시스템의 근접 배치 그룹 구성 변경
지금까지 제공 된 권장 사항에 따라 근접 배치 그룹을 구현 하 고 새 구성으로 조정 하려는 경우 다음 문서에서 설명 하는 방법을 사용 하 여이 작업을 수행할 수 있습니다.

- [Azure CLI를 사용하여 근접 배치 그룹에 VM 배포](../../linux/proximity-placement-groups.md)
- [PowerShell을 사용 하 여 근접 배치 그룹에 Vm 배포](../../windows/proximity-placement-groups.md) 

근접 배치 그룹에서 기존 VM을 사용 하 여 새 VM 유형으로 이동할 수 없는 경우 할당 오류가 발생 하는 경우에도 이러한 명령을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 설명서를 확인하세요.

- [Azure의 SAP 워크로드: 계획 및 배포 검사 목록](./sap-deployment-checklist.md)
- [Azure CLI를 사용하여 근접 배치 그룹에 VM 배포](../../linux/proximity-placement-groups.md)
- [PowerShell을 사용 하 여 근접 배치 그룹에 Vm 배포](../../windows/proximity-placement-groups.md)
- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](./dbms_guide_general.md)