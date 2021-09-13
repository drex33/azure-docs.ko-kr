---
title: 배치 정책 만들기(미리 보기)
description: Azure VMware Solution에 배치 정책을 만들어 Azure Portal 통해 클러스터 내 호스트의 VM(가상 머신) 배치를 제어하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 8/18/2021
ms.openlocfilehash: 85146ce86dea0d3cfa7397cdaae6fefc8cf4a23b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967546"
---
# <a name="create-a-placement-policy-in-azure-vmware-solution-preview"></a>Azure VMware Solution 배치 정책 만들기(미리 보기)

>[!IMPORTANT]
>Azure VMware Solution 배치 정책(미리 보기)은 현재 미리보기로 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 미리 보기 기능을 사용하려면 [_DRS 배치 정책_ 및 _초기 액세스_ 기능](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade)를 모두 등록해야 합니다.  기능을 검색하여 선택한 다음, **등록** 을 선택합니다.

Azure VMware Solution 프라이빗 클라우드의 클러스터는 관리되는 리소스입니다. 따라서 cloudadmin 역할은 DRS(분산 리소스 스케줄러) 규칙 관리를 포함하여 vSphere 클라이언트에서 클러스터를 특정하게 변경할 수 없습니다.

배치 정책 기능은 모든 Azure VMware Solution 지역에서 사용할 수 있습니다.  배치 정책을 사용하면 Azure Portal 통해 클러스터 내의 호스트에서 VM(가상 머신)의 배치를 제어할 수 있습니다. 배치 정책을 만들 때 지정된 vSphere 클러스터에 DRS (분산 리소스 스케줄러) 규칙이 포함됩니다. 또한 Azure VMware Solution 작업과의 상호 운용성을 위한 추가 논리도 포함됩니다.

배치 정책에는 5개 이상의 필수 구성 요소가 있습니다. 

- **이름** - 정책의 이름을 정의하며 [Azure 리소스](../azure-resource-manager/management/resource-name-rules.md)의 명명 제약 조건이 적용됩니다.

- **형식** - 정책에 포함된 리소스에 적용할 컨트롤의 형식을 정의합니다.

- **클러스터** - 정책의 클러스터를 정의합니다. 배치 정책의 범위는 vSphere 클러스터이므로 동일한 클러스터의 리소스만 동일한 배치 정책에 포함될 수 있습니다.

- **상태** - 정책을 사용할지 여부를 정의합니다. 특정 시나리오에서는 충돌하는 규칙을 만들 때 정책이 자동으로 비활성화될 수 있습니다. 자세한 내용은 [고려사항](#considerations)을 참조하세요.

- **가상 머신** - 정책에 대한 VM 및 호스트를 정의합니다. 만드는 규칙 유형에 따라 정책에 따라 일부 수의 VM 및 호스트를 지정해야 할 수 있습니다. 자세한 내용은 [배치 정책 유형](#placement-policy-types)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

- 배치 정책을 관리하려면 프라이빗 클라우드에 대한 _Contributor_ 수준 액세스 권한이 있어야 합니다.

- _DRS 배치 정책_ 및 _초기 엑세스_ [기능이 등록됩니다.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade)


## <a name="placement-policy-types"></a>배치 정책 유형

### <a name="vm-vm-policies"></a>VM(가상 머신)-VM(가상 머신) 정책

**VM-VM** 정책은 선택한 VM을 동일한 호스트에서 실행해야 하는지 아니면 별도의 호스트에서 유지해야 하는지를 지정합니다.  정책의 이름 및 클러스터를 선택하는 것 외에도 **VM-VM** 정책을 사용하려면 할당할 VM을 두 개 이상 선택해야 합니다. 호스트 할당은 이 정책 유형에 필요하지 않거나 허용되지 않습니다.

- **VM-VM 선호도** 정책은 지정된 VM을 동일한 호스트에 함께 유지하도록 DRS에 지시합니다. 예를 들어 성능상의 이유로 유용합니다.

- **VM-VM 선호도 방지** 정책은 지정된 VM을 별도의 호스트에서 서로 분리하도록 DRS에 지시합니다. 이는 한 호스트의 문제가 동일한 정책 내의 여러 VM에 영향을 미치지 않는 시나리오에서 유용합니다.


### <a name="vm-host-policies"></a>VM 호스트 정책

**VM 호스트** 정책은 선택한 VM을 선택한 호스트에서 실행할 수 있는지를 지정합니다.  호스트 유지 관리 모드 및 호스트 교체와 같은 플랫폼 관리 작업의 간섭을 방지하기 위해 Azure VMware Solution의 **VM 호스트** 정책은 항상 우선("해야 함" 규칙이라고도 한다)입니다. 따라서 **VM 호스트** 정책은 [특정 시나리오에서 적용될 수 없습니다](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-793013E2-0976-43B7-9A00-340FA76859D0.html). 자세한 내용은 [작업에 대한 정책 모니터](#monitor-the-operation-of-a-policy)를 참조하세요.

특정 플랫폼 작업은 **VM 호스트** 정책에 정의된 호스트 목록을 동적으로 업데이트합니다. 예를 들어 배치 정책의 멤버인 호스트를 삭제하면 두 개 이상의 호스트가 해당 정책의 일부인 경우 호스트가 제거됩니다. 또한 호스트가 정책의 일부이고 플랫폼 관리 작업의 일부로 대체되어야 하는 경우 정책은 새 호스트로 동적으로 업데이트됩니다.

정책의 이름 및 클러스터를 선택하는 것 외에도 **VM 호스트** 정책을 사용하려면 정책에 할당할 VM과 호스트를 하나 이상 선택해야 합니다.

- **VM 호스트 선호도** 정책은 정의된 호스트에서 지정된 VM을 실행하도록 DRS에 지시합니다.

- **VM 호스트 선호도 방지** 정책은 DRS에 정의된 VM이 아닌 호스트에서 지정된 VM을 실행하도록 지시합니다.


## <a name="considerations"></a>고려 사항

### <a name="cluster-scale-in"></a>클러스터 규모 감축

Azure VMware Solution 클러스터 규모 축소 작업을 수행할 때 특정 DRS 규칙 위반이 발생하지 않도록 합니다.

VM 호스트 정책에서 마지막 호스트를 제거할 수 없습니다. 그러나 정책에서 마지막 호스트를 제거해야 하는 경우 클러스터에서 호스트를 제거하기 전에 정책에 다른 호스트를 추가하여 수정할 수 있습니다. 또는 호스트를 제거하기 전에 배치 정책을 삭제할 수 있습니다.

클러스터의 호스트 수보다 더 많은 VM을 가진 VM-VM 선호도 방지 정책을 사용할 수 없습니다. 호스트를 제거하면 클러스터의 호스트 수가 VM보다 적을 경우 작업을 방해하는 오류가 표시됩니다. 먼저 규칙에서 VM을 제거한 다음 클러스터에서 호스트를 제거하여 수정할 수 있습니다.


### <a name="rule-conflicts"></a>규칙 충돌

VM-VM 정책을 만들 때 DRS 규칙 충돌이 감지되면 표준 [VMware DRS 규칙 동작](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.resmgmt.doc/GUID-69C738B6-5FC8-4189-9CB5-DD90A5A05979.html)에 따라 해당 정책이 비활성화된 상태로 만들어집니다. 규칙 충돌 보기에 대한 자세한 내용은 아래 [정책 작업 모니터링을](#monitor-the-operation-of-a-policy) 참조하세요.



## <a name="create-a-placement-policy"></a>배치 정책 만들기

만들 수 있는 정책들의 갯수는 제한이 없습니다. 그러나 배치 제약 조건이 많을수록 vSphere DRS가 클러스터 내에서 가상 머신을 효과적으로 이동하고 워크로드에 필요한 리소스를 제공하는 것이 더 어렵습니다.      

[정책 유형](#placement-policy-types)에 대한 요구 사항을 검토해야 합니다.

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **배치 정책** > **만들기** 를 선택합니다.

   >[!TIP]
   >배치 정책 개요 창에서 클러스터를 선택한 **만들기** 선택할 수도 있습니다.
   >
   >:::image type="content" source="media/placement-policies/create-placement-policy-cluster.png" alt-text="배치 정책을 만들기 위한 대체 옵션을 보여주는 스크린샷.":::



   :::image type="content" source="media/placement-policies/create-placement-policy.png" alt-text="VM-VM 배치 정책을 만드는 프로세스를 시작하는 방법을 보여주는 스크린샷." lightbox="media/placement-policies/create-placement-policy.png":::


1. 설명이 포함된 이름을 제공하고 정책 유형을 선택한 다음 정책이 만들어지는 클러스터를 선택합니다. 그런 다음 **사용** 을 선택합니다.

   >[!WARNING]
   >정책을 사용하지 않도록 설정하면 정책 및 기본 DRS 규칙이 만들어지지만 정책을 사용하도록 설정할 때까지 정책 작업은 무시됩니다. 

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-host-affinity-1.png" alt-text="배치 정책 옵션을 보여주는 스크린샷." lightbox="media/placement-policies/create-placement-policy-vm-host-affinity-1.png":::   

1. 형식으로 **VM 호스트 선호도** 또는 **VM 호스트 선호도 방지** 를 선택한 경우 **+ 호스트 추가** 및 정책에 포함할 호스트를 선택합니다. 여러 호스트를 선택할 수 있습니다.

   >[!NOTE]
   >호스트 선택 창에는 호스트와 연결된 VM 호스트 정책 수와 관련 정책에 포함된 총 VM 수가 표시됩니다.
   >
   >:::image type="content" source="media/placement-policies/hosts-associated-policies-vms.png" alt-text="호스트와 연결된 VM 호스트 정책의 수와 관련 정책에 포함된 VM 수를 보여주는 스크린샷.":::


1. **+ 가상 머신 추가** 및 정책에 포함할 VM을 선택합니다. 여러 VM을 선택할 수 있습니다.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-2.png" alt-text="VM 목록을 보여 주는 스크린샷":::
   
   >[!NOTE]
   >호스트 선택 창에는 호스트와 연결된 VM 호스트 정책 수와 관련 정책에 포함된 총 VM 수가 표시됩니다. 

1. 원하는 VM 추가를 마쳤으면 **가상 머신 추가** 를 선택합니다. 

1. **다음: 검토 + 만들기** 를 선택하여 정책을 검토합니다. 

1. **Create policy**(정책 만들기)를 선택합니다. 변경 하려면 **뒤로: 기본 사항** 을 선택 합니다.

   :::image type="content" source="media/placement-policies/create-placement-policy-vm-vm-affinity-3.png" alt-text="배치 정책 설정을 생성하기 전의 스크린샷.":::

1. 배치 정책을 만든 후 **새로 고침을** 선택하여 목록에서 확인합니다.

   :::image type="content" source="media/placement-policies/create-placement-policy-8.png" alt-text="배치 정책을 만든 후 사용으로 보여주는 스크린샷." lightbox="media/placement-policies/create-placement-policy-8.png":::



## <a name="edit-a-placement-policy"></a>배치 정책 편집하기

정책의 상태를 변경하고 새 리소스를 추가하고, 기존 리소스를 할당 취소할 수 있습니다.

### <a name="change-the-policy-state"></a>정책 상태 변경하기

정책의 상태를 **사용** 혹은 **사용 안 함** 으로 변경할 수 있습니다. 

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **배치 정책** 을 선택합니다.

1. 편집하려는 정책을 위해, **기타** 를 선택한 다음, **편집** 을 선택합니다.

   >[!TIP]
   >설정 드롭다운에서 **사용 안 함** 을 선택하여 배치 정책 개요에서 정책을 사용하지 않도록 설정할 수 있습니다. 설정 드롭다운에서 정책을 사용하도록 설정할 수 없습니다.

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="배치 정책을 편집하는 방법을 보여주는 스크린샷." lightbox="media/placement-policies/edit-placement-policy.png":::

1.  정책을 사용하도록 설정했지만 사용하지 않으려면 **사용 안 함** 을 선택한 다음 확인 메시지에서 **사용 안 함** 을 선택합니다. 그렇지 않은 경우 정책이 사용하지 않도록 설정되어 있고 사용하도록 설정하려면 **사용** 을 선택합니다.

1.  **검토 + 생성** 을 선택합니다. 
 
1.  모든 변경 내용을 검토하고 **정책 업데이트** 를 선택합니다. 변경 하려면 **뒤로: 기본 사항** 을 선택 합니다.


### <a name="update-the-resources-in-a-policy"></a>정책에서 리소스 업데이트

VM 또는 호스트와 같은 새 리소스를 정책에 추가 하거나 기존 리소스를 제거할 수 있습니다. 

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **배치 정책** 을 선택합니다.

1. 편집하려는 정책을 위해, **기타** 를 선택한 다음, **편집** 을 선택합니다.

   :::image type="content" source="media/placement-policies/edit-placement-policy.png" alt-text="배치 정책에서 리소스를 편집 하는 방법을 보여 주는 스크린샷." lightbox="media/placement-policies/edit-placement-policy.png":::

   - 기존 리소스를 제거 하려면 제거 하려는 리소스를 하나 이상 선택하고 **할당 해제** 를 선택합니다. 

      :::image type="content" source="media/placement-policies/edit-placement-policy-unassign.png" alt-text="배치 정책에서 기존 리소스를 제거 하는 방법을 보여 주는 스크린샷.":::

   - 새 리소스를 추가 하려면 **가상 머신 편집** 또는 **호스트 편집** 을 선택하고 추가 하려는 리소스를 선택한 다음 **저장** 을 선택합니다. 

1. **다음: 검토 + 주문** 을 선택합니다. 

1. 모든 변경 내용을 검토하고 **정책 업데이트** 를 선택합니다.  변경 하려면 **뒤로: 기본 사항** 을 선택 합니다.



## <a name="delete-a-policy"></a>정책 삭제

배치 정책 및 해당 DRS 규칙을 삭제할 수 있습니다. 

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **배치 정책** 을 선택합니다.

1. 편집하려는 정책을 위해, **기타** 를 선택한 다음, **삭제** 을 선택합니다.

   :::image type="content" source="media/placement-policies/delete-placement-policy.png" alt-text="배치 정책을 편집하는 방법을 보여주는 스크린샷." lightbox="media/placement-policies/delete-placement-policy.png":::

1. 확인 메시지에서 **삭제** 를 선택합니다.



## <a name="monitor-the-operation-of-a-policy"></a>정책 작업 모니터링

VSphere 클라이언트를 사용하여 배치 정책의 해당 DRS 규칙 작업을 모니터링 합니다. 

Cloudadmin 역할의 소유자는 VM/호스트 규칙에 따라 클러스터의 구성 탭에서 배치 정책에 의해 생성 된 DRS 규칙을 볼 수는 있지만 편집할 수는 없습니다. DRS 규칙이 충돌 상태에 있는 경우와 같은 추가 정보를 볼 수 있습니다.

또한 클러스터의 모니터 탭에서 권장 사항 및 오류와 같은 다양한 DRS 규칙 작업을 모니터링할 수 있습니다.


## <a name="faqs"></a>FAQ

### <a name="are-these-the-same-as-drs-affinity-rules"></a>DRS 선호도 규칙과 동일 합니까?
예 및 아니요. VSphere DRS는 현재 정책 집합을 구현 하지만 환경을 간소화 했습니다. VM 그룹과 호스트 그룹 수정은 특히 호스트가 본질적으로 임시적이며 클라우드 환경에서 교체될 수 있기 때문에 번거로운 작업입니다. 온-프레미스 환경의 vSphere 인벤토리에서 호스트가 교체되면 vSphere 관리자는 원하는 VM-호스트 배치 제약 조건이 계속 적용되도록 호스트 그룹을 수정해야 합니다. Azure VMware 솔루션의 배치 정책은 호스트를 회전 하거나 변경할 때 호스트 그룹을 업데이트 합니다. 마찬가지로 클러스터에서 크기를 조정 하는 경우 해당 하는 경우 호스트 그룹이 자동으로 업데이트 됩니다. 이렇게 하면 고객에 대 한 호스트 그룹을 관리 하는 오버헤드가 제거 됩니다.


### <a name="as-this-is-an-existing-functionality-available-in-vcenter-why-cant-i-use-it-directly"></a>VCenter에서 사용할 수 있는 기존 기능이지만 이 기능을 직접 사용할 수 없는 이유는 무엇입니까? 

Azure VMware Solution은 Azure에서 VMware 프라이빗 클라우드를 제공합니다. 이 관리되어 있는 VMware 인프라에서 Microsoft는 사설 클라우드의 클러스터, 호스트, 데이터 저장소 및 분산 가상 스위치를 관리 합니다. 동시에 테넌트는 사설 클라우드에 배포된 워크로드를 관리 하는 일을 담당 합니다. 따라서 사설 클라우드를 관리 하는 테넌트는 온-프레미스 배포에서 VMware 관리자에 게 제공 되는 것과 [동일한 권한 집합을 가지고 있지 않습니다](concepts-identity.md). 

또한 vSphere 권한에서 원하는 세분성이 부족하면 사설 클라우드의 워크 로드 배치를 관리할 때 몇 가지 어려움이 발생 합니다. 예를 들어, 온-프레미스에서 선호도와 비선호도 규칙을 정의하는 데 일반적으로 사용되는 vSphere DRS 규칙은 VMware 클라우드 환경에서 있는 그대로 사용할 수 없습니다. 해당 규칙 중 일부는 프라이빗 클라우드의 일상적인 작업을 차단할 수 있기 때문입니다. 배치 정책은 Azure VMware Solution 포털을 사용하여 해당 규칙을 정의하는 방법을 제공하므로 DRS 규칙을 사용할 필요가 없습니다. 간소화된 환경과 함께 해당 정책은 규칙이 일상적인 인프라 유지 관리 및 작업 활동에 영향을 주지 않도록 합니다. 


###  <a name="what-caveats-should-i-know-about"></a>어떤 주의 사항을 알고 있어야 하나요?

VM 호스트 **MUST** 규칙은 유지 관리 작업을 차단하기 때문에 지원되지 않습니다. 

VM 호스트 **SHOULD** 규칙은 vSphere DRS가 최대한 규칙을 수용하려는 경우 우선 규칙입니다. 경우에 따라 vSphere DRS는 워크로드가 필요한 리소스를 가져오도록 VM 호스트 **SHOULD** 규칙이 적용되는 VM을 vMotion할 수 있습니다. 표준 vSphere DRS 동작이며 배치 정책 기능은 기본 vSphere DRS 동작을 변경하지 않습니다.

충돌 하는 규칙을 만드는 경우 해당 충돌은 vCenter에 표시 될 수 있으며 새로 정의된 규칙이 적용되지 않을 수 있습니다. 이는 vCenter에서 관찰할 수 있는 로그인 표준 vSphere DRS 동작입니다.
