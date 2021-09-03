---
title: Azure Portal을 통한 Azure HANA 대규모 인스턴스 제어 | Microsoft Docs
description: 포털을 통해 Azure HANA 대규모 인스턴스를 식별하고 상호 작용하는 방법을 설명합니다.
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
ms.date: 07/01/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30d16b2042e2bfc8e11824fe64cf709413fd05d8
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113218388"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Azure Portal을 통한 Azure HANA 대규모 인스턴스 제어

>[!NOTE]
>Rev 4.2의 경우 [Azure Portal을 통한 BareMetal 인스턴스 관리](../../../baremetal-infrastructure/connect-baremetal-infrastructure.md) 항목의 지침을 따르세요.

이 문서에서는 [HANA 대규모 인스턴스](./hana-overview-architecture.md)가 [Azure Portal](https://portal.azure.com)에 표시되는 방식과 사용자를 위해 배포된 HANA 대규모 인스턴스 장치를 사용하여 Azure Portal을 통해 수행할 수 있는 작업을 설명합니다. Azure Portal에서 HANA 대규모 인스턴스의 표시 유형은 현재 공개 미리 보기 상태인 HANA 대규모 인스턴스용 Azure 리소스 공급자를 통해 제공됩니다.

## <a name="register-hana-large-instance-resource-provider"></a>HANA 대규모 인스턴스 리소스 공급자 등록
일반적으로 HANA 대규모 인스턴스 배포에 사용하던 Azure 구독은 HANA 대규모 인스턴스 리소스 공급자에 대해 등록됩니다. 그러나 배포한 HANA 대규모 인스턴스 장치가 보이지 않으면 Azure 구독에 리소스 공급자를 등록해야 합니다. HANA 대규모 인스턴스 리소스 공급자를 등록하는 방법에는 두 가지가 있습니다.

### <a name="register-through-cli-interface"></a>CLI 인터페이스를 통해 등록
Azure CLI 인터페이스를 통해 HANA 대규모 인스턴스 배포에 사용되는 Azure 구독에 로그인해야 합니다. 다음 명령을 사용하여 HANA 대규모 인스턴스 공급자를 (다시) 등록할 수 있습니다.
    
```azurecli
az provider register --namespace Microsoft.HanaOnAzure
```

자세한 내용은 [Azure 리소스 공급자 및 형식](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli)을 참조하세요.


### <a name="register-through-azure-portal"></a>Azure Portal을 통해 등록
Azure Portal을 통해 HANA 대규모 인스턴스 리소스 공급자를 (다시) 등록할 수 있습니다. Azure Portal에서 구독을 나열하고, HANA 대규모 인스턴스 장치를 배포하는 데 사용된 구독을 두 번 클릭해야 합니다. 구독의 개요 페이지에서 아래와 같이 ‘리소스 공급자’를 선택하고 검색 창에 ‘HANA’를 입력합니다. 

![Azure Portal을 통해 HLI RP 등록](./media/hana-li-portal/portal-register-hli-rp.png)

표시된 스크린샷에서는 리소스 공급자가 이미 등록되었습니다. 리소스 공급자가 아직 등록되지 않은 경우 ‘다시 등록’ 또는 ‘등록’을 누릅니다.

자세한 내용은 [Azure 리소스 공급자 및 형식](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)을 참조하세요.


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Azure Portal에서 HANA 대규모 인스턴스 장치 표시
HANA 대규모 인스턴스 배포 요청을 제출할 때 HANA 대규모 인스턴스에 연결할 Azure 구독을 지정하라는 메시지가 표시됩니다. HANA 대규모 인스턴스 장치에 대해 작동하는 SAP 애플리케이션 계층을 배포하는 데 사용하는 것과 동일한 구독을 사용하는 것이 좋습니다.
첫 번째 HANA 대규모 인스턴스가 배포되면 HANA 대규모 인스턴스 배포 요청에서 제출한 Azure 구독에 새 [Azure 리소스 그룹](../../../azure-resource-manager/management/manage-resources-portal.md)이 생성됩니다.  새 리소스 그룹은 특정 구독에 배포한 모든 HANA 대규모 인스턴스 장치를 나열합니다.

새 Azure 리소스 그룹을 찾으려면 Azure Portal의 왼쪽 탐색 창으로 이동하여 구독에 리소스 그룹을 나열합니다.

![리소스 그룹 옵션이 강조 표시된 스크린샷.](./media/hana-li-portal/portal-resource-group.png)

리소스 그룹 목록에서 HANA 대규모 인스턴스를 배포하는 데 사용한 구독을 필터링해야 할 수도 있습니다.

![Azure Portal에서 리소스 그룹 필터링](./media/hana-li-portal/portal-filtering-subscription.png)

올바른 구독으로 필터링한 후에도 리소스 그룹의 목록이 길 수 있습니다. **-Txxx** 접미사가 있는 리소스 그룹을 찾습니다. 여기서 ‘xxx’는 **-T050** 과 같은 3자리 숫자입니다. 

리소스 그룹을 찾았으면 리소스 그룹의 세부 정보를 나열합니다. 다음과 비슷한 목록이 수신됩니다.

![Azure Portal의 HLI 목록](./media/hana-li-portal/portal-hli-units-list.png)

나열된 모든 장치는 구독에 배포된 단일 HANA 대규모 인스턴스 장치를 나타냅니다. 이 경우 구독에 배포된 8개의 서로 다른 HANA 대규모 인스턴스 장치가 표시됩니다.

동일한 Azure 구독에서 여러 HANA 대규모 인스턴스 테넌트를 배포한 경우 여러 Azure 리소스 그룹이 표시됩니다. 


## <a name="look-at-attributes-of-single-hli-unit"></a>단일 HLI 장치의 특성 살펴보기
HANA 대규모 인스턴스 장치 목록에서 단일 장치를 클릭하고 단일 HANA 대규모 인스턴스 장치의 세부 정보를 가져올 수 있습니다. 

개요 화면에서 '자세히 표시'를 클릭하면 다음과 같은 장치의 프레젠테이션이 표시됩니다.

![HLI 장치의 개요 표시](./media/hana-li-portal/portal-show-overview.png)

표시되는 다양한 특성을 살펴보면 이러한 특성이 Azure VM 특성과 거의 다르지 않아 보입니다. 왼쪽 헤더에 리소스 그룹, Azure 지역, 구독 이름, ID뿐만 아니라 추가한 일부 태그도 표시됩니다. 기본적으로 HANA 대규모 인스턴스 장치에는 할당된 태그가 없습니다. 헤더의 오른쪽에는 장치의 이름이 배포가 수행될 때 할당된 것으로 나열됩니다. 운영 체제가 IP 주소와 함께 표시됩니다. VM과 마찬가지로 CPU 스레드 수와 메모리를 포함하는 HANA 대규모 인스턴스 장치 유형이 표시됩니다. 다양한 HANA 대규모 인스턴스 장치에 대한 자세한 내용은 다음을 참조하세요.

- [HLI에 사용 가능한 SKU](./hana-available-skus.md)
- [SAP HANA(대규모 인스턴스) 스토리지 아키텍처](./hana-storage-architecture.md) 

오른쪽 아래에 있는 추가 데이터는 HANA 대규모 인스턴스 스탬프의 수정 버전입니다. 가능한 값은 다음과 같습니다.

- 수정 3
- 수정 4

수정 4는 HANA 대규모 인스턴스의 릴리스된 최신 아키텍처로, 수정 4 스탬프 또는 행에서 Azure VM과 배포된 HANA 대규모 인스턴스 장치 간 네트워크 대기 시간이 크게 향상되었습니다.
개요의 오른쪽 아래 모서리에는 배포된 각 HANA 대규모 인스턴스 장치에 대해 자동으로 생성되는 Azure 근접 배치 그룹의 이름과 함께 또 다른 매우 중요한 정보가 있습니다. 이 근접 배치 그룹은 SAP 애플리케이션 계층을 호스트하는 Azure VM을 배포할 때 참조해야 합니다. HANA 대규모 인스턴스 장치와 연결된 [Azure 근접 배치 그룹](../../co-location.md)을 사용하여 Azure VM이 HANA 대규모 인스턴스 장치와 가까운 곳에 배포되는지 확인합니다. 근접 배치 그룹을 사용하여 수정 4에서 호스트되는 HANA 대규모 인스턴스 장치와 동일한 Azure 데이터 센터에서 SAP 애플리케이션 계층을 찾는 방법은 [SAP 애플리케이션에서 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)에 설명되어 있습니다.

헤더의 오른쪽 열에 있는 추가 필드는 HANA 대규모 인스턴스 장치의 전원 상태에 대한 정보를 알려 줍니다.

> [!NOTE]
> 전원 상태는 하드웨어 장치의 전원이 켜져 있는지 꺼져 있는지를 나타냅니다. 실행 중인 운영 체제에 대한 정보는 제공하지 않습니다. HANA 대규모 인스턴스 장치를 다시 시작하면 장치 상태가 **시작 중** 에서 **시작됨** 상태로 전환되는 데 약간 시간이 걸립니다. **시작됨** 상태에 있으면 OS가 시작 중이거나 OS가 완전히 시작된 것입니다. 따라서 장치를 다시 시작한 후 상태가 **시작됨** 으로 전환되는 즉시 장치에 곧바로 로그인할 수 없습니다.
> 

'자세히 보기'를 누르면 추가 정보가 표시됩니다. 추가 정보 중 하나는 장치가 배포된 HANA 대규모 인스턴스 스탬프의 수정을 표시합니다. HANA 대규모 인스턴스 스탬프의 여러 수정에 대해서는 [Azure(대규모 인스턴스)의 SAP HANA란?](./hana-overview-architecture.md) 문서를 참조하세요.

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>단일 HANA 대규모 인스턴스 장치의 활동 확인 
HANA 대규모 인스턴스 장치의 개요를 제공하는 것 외에도 특정 장치의 활동을 확인할 수 있습니다. 활동 로그는 다음과 같습니다.

![Azure Portal의 탐색 창](./media/hana-li-portal/portal-activity-list.png)

기록되는 주요 활동 중 하나는 장치 다시 시작입니다. 나열된 데이터에는 활동의 상태, 활동이 트리거된 타임스탬프, 활동이 트리거된 구독 ID 및 활동을 트리거한 Azure 사용자가 포함됩니다. 

기록되는 또 다른 활동은 Azure 메타데이터에서의 장치 변경 내용입니다. 시작된 다시 시작 외에도 **HANAInstances 쓰기** 활동을 볼 수 있습니다. 이 유형의 활동은 HANA 대규모 인스턴스 장치 자체에서는 변경을 수행하지 않지만 Azure에서 장치의 메타데이터에 대한 변경 내용을 문서화합니다. 나열된 경우 태그를 추가하고 삭제했습니다(다음 섹션 참조).

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>HANA 대규모 인스턴스 장치에 Azure 태그 추가 및 삭제
HANA 대규모 인스턴스 장치에 [태그](../../../azure-resource-manager/management/tag-resources.md)를 추가할 수도 있습니다. 태그가 할당되는 방법은 VM에 태그를 할당하는 것과 다르지 않습니다. VM과 마찬가지로 태그는 Azure 메타데이터에 존재하고, HANA 대규모 인스턴스의 경우 VM의 태그와 동일한 제한이 있습니다.

태그 삭제는 VM과 동일한 방식으로 이루어집니다. 태그 적용 활동과 태그 삭제 활동 모두 특정 HANA 대규모 인스턴스 장치의 활동 로그에 나열됩니다.

## <a name="check-properties-of-a-hana-large-instance-unit"></a>HANA 대규모 인스턴스 장치의 속성 확인
**속성** 섹션에는 인스턴스가 사용자에게 전달될 때 얻을 수 있는 중요한 정보가 포함되어 있습니다. 이 섹션에서는 지원 사례에서 요구하거나 스토리지 스냅샷 구성을 설정할 때 필요한 모든 정보를 얻을 수 있습니다. 이처럼 이 섹션은 인스턴스, 인스턴스와 Azure 및 스토리지 백 엔드의 연결에 관한 데이터 모음입니다. 섹션 위쪽은 다음과 같습니다.


![Azure Portal의 HLI 속성 위쪽 부분](./media/hana-li-portal/portal-properties-top.png)

처음 몇 개의 데이터 항목은 개요 화면에서 이미 봤습니다. 하지만 데이터의 중요한 부분은 배포된 첫 번째 장치가 전달될 때 얻은 ExpressRoute 회로 ID입니다. 일부 지원 사례에서는 이 데이터를 물을 수 있습니다. 중요한 데이터 항목이 스크린샷 아래쪽에 표시됩니다. 표시되는 데이터는 스토리지를 HANA 대규모 인스턴스 스택의 **테넌트** 로 격리하는 NFS 스토리지 헤드의 IP 주소입니다. 이 IP 주소는 [Azure 애플리케이션 일치 스냅샷 도구 구성](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)을 편집할 때도 필요합니다. 

속성 창에서 아래로 스크롤하면 HANA 대규모 인스턴스 장치의 고유 리소스 ID 또는 배포에 할당된 구독 ID와 같은 추가 데이터를 얻을 수 있습니다.

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Azure Portal을 통해 HANA 대규모 인스턴스 장치 다시 시작
Linux 운영 체제 다시 시작을 시작하는 경우 OS가 다시 시작을 성공적으로 완료할 수 없는 다양한 상황이 있었습니다. 강제로 다시 시작하려면 Microsoft 작업에서 HANA 대규모 인스턴스 장치의 전원 다시 시작을 수행하도록 서비스 요청을 열어야 했습니다. HANA 대규모 인스턴스 장치의 전원 다시 시작 기능이 이제 Azure Portal에 통합되었습니다. 현재 위치가 HANA 대규모 인스턴스 장치의 개요 부분이므로 데이터 섹션 위에 다시 시작 단추가 표시됩니다.

![Azure Portal의 다시 시작 1단계](./media/hana-li-portal/portal-restart-first-step.png)

다시 시작 단추를 누르면 장치를 다시 시작할지 여부를 묻는 메시지가 표시됩니다. ‘예’ 단추를 눌러 확인하면 장치가 다시 시작됩니다.

> [!NOTE]
> 다시 시작 프로세스에서는 장치 상태가 **시작 중** 에서 **시작됨** 상태로 전환되는 데 약간 시간이 걸립니다. **시작됨** 상태에 있으면 OS가 시작 중이거나 OS가 완전히 시작된 것입니다. 따라서 장치를 다시 시작한 후 상태가 **시작됨** 으로 전환되는 즉시 장치에 곧바로 로그인할 수 없습니다.

> [!IMPORTANT]
> HANA 대규모 인스턴스 장치의 메모리 양에 따라 하드웨어 및 운영 체제 다시 시작과 재부팅에 최대 1시간이 걸릴 수 있습니다.


## <a name="open-a-support-request-for-hana-large-instances"></a>HANA 대규모 인스턴스 지원 요청 열기
Azure Portal의 HANA 대규모 인스턴스 장치 표시에서도 HANA 대규모 인스턴스 장치에 대한 지원 요청을 만들 수 있습니다. **새 지원 요청** 링크를 따라갑니다. 

![Azure Portal에서 서비스 요청 단계 1단계 시작](./media/hana-li-portal/portal-initiate-support-request.png)

다음 화면에 나열된 SAP HANA 대규모 인스턴스의 서비스를 받으려면 아래와 같이 '모든 서비스'를 선택해야 할 수 있습니다.

![Azure Portal에서 모든 서비스 선택](./media/hana-li-portal/portal-create-service-request.png)

서비스 목록에서 **SAP HANA 대규모 인스턴스** 서비스를 찾을 수 있습니다. 해당 서비스를 선택하면 다음과 같이 특정 문제 유형을 선택할 수 있습니다.


![Azure Portal에서 문제 클래스 선택](./media/hana-li-portal/portal-select-problem-class.png)

다양한 각 문제 유형 아래에서 문제의 특징을 자세히 결정하려면 선택해야 하는 문제 하위 유형 선택이 제공됩니다. 하위 유형을 선택한 후에는 제목 이름을 지정할 수 있습니다. 선택 프로세스를 완료한 후 만들기의 다음 단계로 이동할 수 있습니다. **해결 방법** 섹션에서 문제 해결의 실마리를 줄 수 있는 HANA 대규모 인스턴스에 관한 설명서가 제시됩니다. 제안된 설명서에서 문제의 해결 방법을 찾을 수 없는 경우 다음 단계로 이동합니다. 다음 단계에서는 문제가 VM에 있는지 HANA 대규모 인스턴스 장치에 있는지 묻는 메시지가 표시됩니다. 이 정보는 적합한 전문가에게 지원 요청을 보내는 데 도움이 됩니다. 

![Azure Portal의 지원 사례 세부 정보](./media/hana-li-portal/portal-support-request-details.png)

질문에 답하고 추가 세부 정보를 제공하면 지원 요청을 검토하고 제출하는 다음 단계로 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 SAP HANA(대규모 인스턴스)를 모니터링하는 방법](./troubleshooting-monitoring.md)
- [HANA 쪽에서 모니터링 및 문제 해결](./hana-monitor-troubleshoot.md)