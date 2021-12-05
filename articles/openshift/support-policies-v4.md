---
title: Azure Red Hat OpenShift 4 클러스터 지원 정책
description: Red Hat OpenShift 4의 지원 정책 요구 사항을 이해합니다.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 308e21fba5d36d84116ed9362bd373449b9602e3
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570713"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift 지원 정책

Azure Red Hat OpenShift 4 클러스터에 대한 특정 구성은 클러스터의 지원 가능성에 영향을 줄 수 있습니다. Azure Red Hat OpenShift 4를 통해 클러스터 관리자는 내부 클러스터 구성 요소를 변경할 수 있지만 일부 변경은 지원되지 않습니다. 아래 지원 정책에서는 정책을 위반한 수정 사항과 Microsoft 및 Red Hat의 void 지원을 공유합니다.

> [!NOTE]
> OpenShift Container 플랫폼에서 기술 미리 보기로 표시된 기능은 Azure Red Hat OpenShift에서 지원되지 않습니다.

## <a name="cluster-configuration-requirements"></a>클러스터 구성 요구 사항

* 모든 OpenShift 클러스터 연산자는 관리 상태로 유지되어야 합니다. 클러스터 연산자 목록은 `oc get clusteroperators`를 실행하여 반환할 수 있습니다.
* 클러스터에는 최소 3개의 작업자 노드와 3개의 관리자 노드가 있어야 합니다. OpenShift 구성 요소가 예약되지 않도록 하는 테인트가 없어야 합니다. 클러스터 작업자를 0으로 조정하거나 정상적인 클러스터 종료를 시도하지 마세요.
* 클러스터 Prometheus 및 Alertmanager 서비스를 제거하거나 수정하지 마세요.
* Service Alertmanager 규칙을 제거하지 마세요.
* 네트워크 보안 그룹을 제거하거나 수정하지 마세요.
* Azure Red Hat OpenShift 서비스 로깅(mdsd pod)을 제거하거나 수정하지 마세요.
* 'arosvc.azurecr.io' 클러스터 풀 비밀을 제거하거나 수정하지 마세요.
* 모든 클러스터 가상 머신에는 최소한 ARM(Azure Resource Manager) 및 서비스 로깅(Geneva) 엔드포인트에 대한 직접적인 아웃바운드 인터넷 액세스 권한이 있어야 합니다.  HTTPS 프록시 형식은 지원되지 않습니다.
* 어떤 방식으로든 클러스터의 MachineConfig 개체(예: kubelet 구성)를 재정의하지 마세요.
* unsupportedConfigOverrides 옵션을 설정하지 마세요. 이러한 옵션을 설정하면 부 버전 업그레이드를 수행할 수 없습니다.
* Azure Red Hat OpenShift 서비스는 Private Link Service를 통해 클러스터에 액세스합니다.  서비스 액세스를 제거하거나 수정하지 마세요.
* 비 RHCOS 컴퓨팅 노드는 지원되지 않습니다. 예를 들어 RHEL 컴퓨팅 노드를 사용할 수 없습니다.
* ARO RP 관리 클러스터 리소스 그룹에 태그를 요구하는 것과 같이 SRE가 ARO 클러스터에 대해 정상적인 유지 관리를 수행하지 못하도록 하는 정책을 구독 또는 관리 그룹 내에 배치하지 마세요.

## <a name="supported-virtual-machine-sizes"></a>지원되는 가상 머신 크기

Azure Red Hat OpenShift 4는 다음 가상 머신 크기의 작업자 노드 인스턴스를 지원합니다.

### <a name="master-nodes"></a>마스터 노드

|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="general-purpose"></a>범용 가상 컴퓨터

|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>메모리에 최적화

|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>컴퓨팅 최적화

|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="day-2-worker-node"></a>Day 2 작업자 노드
다음 인스턴스 유형은 machinesets를 구성 하 여 2 일 작업으로 지원 됩니다. Machineset을 만드는 방법에 대 한 자세한 내용은 [Azure에서 Machineset 만들기](https://docs.openshift.com/container-platform/4.8/machine_management/creating_machinesets/creating-machineset-azure.html)를 참조 하세요.


|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|L4s|Standard_L4s|4|32|
|L8s|Standard_L8s|8|64|
|L16s|Standard_L16s|16|128|
|L32s|Standard_L32s|32|256|
|L8s_v2|Standard_L8s_v2|8|64|
|L16s_v2|Standard_L16s_v2|16|128|
|L32s_v2|Standard_L32s_v2|32|256|
|L48s_v2|Standard_L48s_v2|32|384|
|L64s_v2|Standard_L48s_v2|64|512|
