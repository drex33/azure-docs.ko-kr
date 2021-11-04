---
title: Azure Batch의 노드 및 풀
description: 컴퓨팅 노드 및 풀에 대해 살펴보고 개발 관점에서 Azure Batch 워크플로에서 이들을 사용하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 6d5556bd719f982995c9353003f2c9dccc1e1a48
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443398"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Azure Batch의 노드 및 풀

Azure Batch 워크플로에서 컴퓨팅 노드(또는 노드)는 애플리케이션의 워크로드 중 일부를 처리하는 가상 머신입니다. 풀은 애플리케이션을 실행할 이러한 노드의 컬렉션입니다. 이 문서에서는 노드 및 풀에 대해 자세히 설명하고 Azure Batch 워크플로에서 이들을 만들고 사용하는 경우 고려해야 할 사항도 설명합니다.

## <a name="nodes"></a>노드

노드는 애플리케이션의 워크로드 중 일부를 처리하도록 전담하는 Azure VM(가상 머신) 또는 클라우드 서비스 VM입니다. 노드의 크기에 따라 노드에 할당되는 CPU 코어 수, 메모리 용량 및 로컬 파일 시스템 크기가 결정됩니다.

[Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1) 또는 사용자가 준비한 사용자 지정 이미지를 사용하여 Windows 또는 Linux 노드의 풀을 만들 수 있습니다.

노드는 노드의 운영 체제 환경에서 지원하는 실행 파일이나 스크립트를 실행할 수 있습니다. 실행 파일 또는 스크립트에는 \*.exe, \*.cmd, \*.bat 및 PowerShell 스크립트(Windows용) 및 이진 파일, 셸 및 Python 스크립트(Linux용)가 포함됩니다.

Batch의 모든 컴퓨팅 노드는 다음 사항도 포함합니다.

- 태스크에서 참조로 사용할 수 있는 표준 [폴더 구조](files-and-directories.md) 및 연결된 [환경 변수](jobs-and-tasks.md)
- **방화벽** 설정
- Windows(RDP(원격 데스크톱 프로토콜)) 노드 및 Linux(SSH(Secure Shell)) 노드 모두에 대한 [원격 액세스](error-handling.md#connect-to-compute-nodes)([원격 액세스를 사용하지 않는 풀](pool-endpoint-configuration.md)을 만들지 않는 경우).

기본적으로 노드는 서로 통신할 수 있지만 동일한 풀에 속하지 않는 가상 머신과는 통신할 수 없습니다. 노드가 다른 가상 머신 또는 온-프레미스 네트워크와 안전하게 통신할 수 있도록 [Azure VNet(가상 네트워크)의 서브넷에서](batch-virtual-network.md) 풀을 프로비저닝할 수 있습니다. 이렇게 하면 공용 IP 주소를 통해 노드에 액세스할 수 있습니다. 공용 IP 주소는 일괄 처리로 생성되며 풀의 수명 동안 바뀔 수 있습니다. 직접 컨트롤할 수 있는 [고정적인 공용 IP 주소가 있는 풀을 생성](create-pool-public-ip.md)하여 IP 주소가 예기치 않게 변경되지 않도록 할 수도 있습니다.

## <a name="pools"></a>풀

풀은 애플리케이션이 실행되는 노드 컬렉션입니다.

Azure Batch 풀은 코어 Azure 컴퓨팅 플랫폼을 기반으로 합니다. Batch 풀은 대규모 할당, 애플리케이션 설치, 데이터 배포, 상태 모니터링 및 풀 내의 유연한 컴퓨팅 노드 수 조정([크기 조정](#automatic-scaling-policy)) 기능을 제공합니다.

풀에 추가된 모든 노드에는 고유 이름 및 IP 주소가 할당됩니다. 노드가 풀에서 제거되면 운영 체제 또는 파일에 적용된 모든 변경 내용이 손실되며, 해당 이름 및 IP 주소가 나중에 사용할 수 있도록 해제됩니다. 노드가 풀에서 제거되면 수명이 끝납니다.

풀은 풀이 생성된 Batch 계정을 통해서만 사용할 수 있습니다. 한 Batch 계정에서 실행될 애플리케이션의 리소스 요구 사항을 충족하기 위해 여러 풀을 만들 수 있습니다.

풀을 수동으로 만들 수도 있고, 수행할 작업을 지정한 경우에는 [Batch 서비스에서 자동으로](#autopools) 풀을 만듭니다. 풀을 만들 때는 다음과 같은 특성을 지정할 수 있습니다.

- [노드 운영 체제 및 버전](#operating-system-and-version)
- [노드 유형 및 노드 대상 수](#node-type-and-target)
- [노드 크기](#node-size)
- [자동 크기 조정 정책](#automatic-scaling-policy)
- [태스크 일정 정책](#task-scheduling-policy)
- [통신 상태](#communication-status)
- [시작 태스크](#start-tasks)
- [애플리케이션 패키지](#application-packages)
- [VNet(가상 네트워크) 및 방화벽 구성](#virtual-network-vnet-and-firewall-configuration)
- [수명](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Batch 계정에는 Batch 계정의 코어 수를 제한하는 기본 할당량이 있습니다. 코어 수는 컴퓨팅 노드 수에 해당합니다. [Azure Batch 서비스 할당량 및 제한](batch-quota-limit.md)에서 [할당량 증가](batch-quota-limit.md#increase-a-quota) 방법에 대한 기본 할당량과 지침을 찾을 수 있습니다. 풀에서 대상 노드 수를 달성하지 못하는 경우 코어 할당량이 원인일 수 있습니다.

## <a name="operating-system-and-version"></a>운영 체제 및 버전

Batch 풀을 만들 때 Azure 가상 머신 구성과 풀의 각 컴퓨팅 노드에서 실행하려는 운영 체제 유형을 지정합니다.

## <a name="configurations"></a>구성

Batch에서 사용할 수 있는 풀 구성에는 두 가지 유형이 있습니다.

> [!IMPORTANT]
> 현재 두 구성 중 아무거나 사용하여 풀을 만들 수 있지만, 새 풀은 Cloud Services 구성이 아니라 Virtual Machine 구성을 사용하여 구성해야 합니다. Batch의 모든 현재 및 새 기능은 Virtual Machine 구성 풀에서 지원됩니다. Cloud Services 구성 풀은 모든 기능을 지원하지 않으며 새로운 기능도 계획되어 있지 않습니다. [2024년 2월 29일 이후](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/) 새 ‘CloudServiceConfiguration’ 풀을 생성하거나 기존 풀에 새 노드를 추가할 수 없습니다.

### <a name="virtual-machine-configuration"></a>가상 머신 구성

**가상 머신 구성** 은 풀이 Azure 가상 머신으로 구성됨을 나타냅니다. 이러한 VM은 Linux 또는 Windows 이미지에서 만들 수 있습니다.

[Batch 노드 에이전트](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md)는 풀의 각 노드에서 실행되고 노드와 Batch 서비스 간의 명령 및 컨트롤 인터페이스를 제공하는 프로그램입니다. SKU라고 하는 노드 에이전트의 구현은 서로 다른 운영 체제에 대해 여러 가지가 있습니다. Virtual Machine 구성에 따라 풀을 만들 때는 노드 크기와 해당 노드를 만드는 데 사용되는 이미지의 원본뿐만 아니라 해당 노드에 설치될 **가상 머신 이미지 참조** 및 Batch **노드 에이전트 SKU** 도 지정해야 합니다. 이러한 풀 속성에 대한 자세한 내용은 [Azure Batch 풀에서 Linux 컴퓨팅 노드 프로비전](batch-linux-nodes.md)을 참조하세요. 필요에 따라 하나 이상의 빈 데이터 디스크를 Marketplace 이미지에서 만든 풀 VM에 연결하거나 VM을 만드는 데 사용되는 사용자 지정 이미지에 데이터 디스크를 포함할 수 있습니다. 데이터 디스크를 포함하는 경우 VM 내에서 디스크를 탑재하고 포맷하여 사용해야 합니다.

### <a name="cloud-services-configuration"></a>Cloud Services 구성

> [!WARNING]
> Cloud Services 구성 풀은 [사용되지 않습니다](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/). Virtual Machine 구성 풀을 대신 사용하세요. 자세한 내용은 [Cloud Services에서 Virtual Machine으로 Batch 풀 구성 마이그레이션](batch-pool-cloud-service-to-virtual-machine-configuration.md)을 참조하세요.

**Cloud Services 구성** 은 풀이 Azure Cloud Services 노드로 구성됨을 나타냅니다. Cloud Services는 Windows 컴퓨팅 노드만 제공합니다.

Cloud Services 구성 풀에 사용할 수 있는 운영 체제는 [Azure Guest OS 릴리스 및 SDK 호환성 매트릭스](../cloud-services/cloud-services-guestos-update-matrix.md)에 나열되고 사용 가능한 컴퓨팅 노드 크기는 [Cloud Services 크기](../cloud-services/cloud-services-sizes-specs.md)에 나열됩니다. Cloud Services 노드를 포함하는 풀을 만들 때 노드 크기와 해당 ‘OS 제품군’(OS와 함께 설치되는 .NET 버전을 결정)을 지정합니다. Cloud Services는 Windows를 실행하는 가상 머신보다 더 빠르게 Azure에 배포됩니다. Windows 컴퓨팅 노드 풀을 원하는 경우 배포 시간 측면에서 Cloud Services가 성능 상의 이점을 제공할 수 있습니다.

Cloud Services 내의 작업자 역할과 마찬가지로 ‘OS 버전’을 지정할 수 있습니다. OS 버전에 `Latest (*)`를 지정하는 것이 좋습니다. 그러면 노드가 자동으로 업그레이드되며 새로 릴리스된 버전을 사용하는 데 필요한 조정 작업이 없습니다. 특정 OS 버전을 선택하는 기본 사용 사례는 버전을 업데이트하기 전에 이전 버전과의 호환성 테스트를 수행할 수 있게 하여 애플리케이션 호환성을 유지하는 것입니다. 유효성 검사 후 풀의 OS 버전을 업데이트하고 새 OS 이미지를 설치할 수 있습니다. 실행 중인 태스크가 모두 중단되고 다시 큐에 저장됩니다.

### <a name="node-agent-skus"></a>노드 에이전트 SKU

풀을 만들 때 기본 VHD 이미지의 OS에 따라 적절한 **nodeAgentSkuId** 를 선택해야 합니다. [지원되는 노드 에이전트 SKU 나열](/rest/api/batchservice/list-supported-node-agent-skus) 작업을 호출하여 사용 가능한 노드 에이전트 SKU ID를 OS 이미지 참조에 매핑할 수 있습니다.

### <a name="custom-images-for-virtual-machine-pools"></a>Virtual Machine 풀에 대한 사용자 지정 이미지

사용자 지정 이미지를 사용하여 풀을 만드는 방법을 알아보려면 [Azure Compute 갤러리를 사용하여 사용자 지정 풀 만들기를](batch-sig-images.md)참조하세요.

또는 [관리 이미지](batch-custom-images.md) 리소스를 사용하여 가상 머신의 사용자 지정 풀을 만들 수 있습니다. Azure VM에서 사용자 지정 Linux 이미지를 준비하는 방법에 대한 자세한 내용은 [가상 머신 또는 VHD의 이미지를 만드는 방법](../virtual-machines/linux/capture-image.md)을 참조하세요. Azure VM에서 사용자 지정 Windows 이미지를 준비하는 방법에 대한 자세한 내용은 [Azure에서 일반화된 VM의 관리 이미지 만들기](../virtual-machines/windows/capture-image-resource.md)를 참조하세요.

### <a name="container-support-in-virtual-machine-pools"></a>Virtual Machine 풀에서 컨테이너 지원

Batch API를 사용하여 Virtual Machine 구성 풀을 만들 때 Docker 컨테이너에서 작업을 실행하도록 풀을 설정할 수 있습니다. 현재 Docker 컨테이너를 지원하는 이미지를 사용하여 풀을 만들어야 합니다. Microsoft Azure Marketplace의 컨테이너 이미지로 Windows Server 2016 Datacenter를 사용하거나 Docker Community Edition이나 Enterprise Edition 및 모든 필수 드라이버를 포함하는 사용자 지정 VM 이미지를 제공합니다. 풀 설정은 풀이 만들어질 때 VM에 컨테이너 이미지를 복사하는 [컨테이너 구성](/rest/api/batchservice/pool/add)을 포함해야 합니다. 그러면 풀에서 실행되는 작업은 컨테이너 이미지와 컨테이너 실행 옵션을 참조할 수 있습니다.

자세한 내용은 [Azure Batch에서 Docker 컨테이너 애플리케이션 실행](batch-docker-container-workloads.md)을 참조하세요.

## <a name="node-type-and-target"></a>노드 유형 및 대상

풀을 만들 때 각각에 대해 원하는 노드 유형 및 대상 수를 지정할 수 있습니다. 두 가지 유형의 노드는 다음과 같습니다.

- **전용 노드**. 전용 컴퓨팅 노드는 사용자 워크로드용으로 예약되어 있습니다. 우선 순위가 낮은 노드보다 비용은 많이 들지만 절대로 선취되지 않습니다.
- **우선 순위가 낮은 노드**. 우선 순위가 낮은 노드는 Azure의 나머지 용량을 활용하여 Batch 워크로드를 실행합니다. 우선 순위가 낮은 노드는 전용 노드보다 시간당 비용이 더 적게 들며, 상당한 컴퓨팅 능력이 필요한 워크로드를 사용할 수 있습니다. 자세한 내용은 [Batch에서 우선 순위가 낮은 VM 사용](batch-low-pri-vms.md)을 참조하세요.

Azure에 여유 용량이 부족하면 우선 순위가 낮은 노드는 선점될 수 있습니다. 작업을 실행하는 중에 노드가 선취되면 해당 작업은 다시 대기 상태가 되고 컴퓨팅 노드를 다시 사용할 수 있게 되면 다시 실행됩니다. 우선 순위가 낮은 노드는 작업 완료 시간이 유연하고 작업은 여러 노드에 분산되어 있는 워크로드에 적합한 옵션입니다. 시나리오에 우선 순위가 낮은 노드를 사용하도록 결정하기 전에 선점으로 인해 손실되는 작업을 최소화하고 쉽게 다시 만들 수 있어야 합니다.

우선 순위가 낮은 컴퓨팅 노드와 전용 컴퓨팅 노드를 모두 동일한 풀에서 사용할 수 있습니다. 각 유형의 노드에는 고유한 대상 설정이 있으므로 원하는 노드 수를 지정할 수 있습니다.

경우에 따라 풀이 원하는 노두 수에 도달하지 못할 수 있기 때문에 컴퓨팅 노드 수를 *대상* 이라고 합니다. 예를 들어, Batch 계정의 [코어 할당량](batch-quota-limit.md)에 먼저 도달하는 경우 풀은 대상에 도달하지 못할 수 있습니다. 또는 최대 노드 수를 제한하는 풀에 자동 크기 조정 수식을 적용한 경우, 풀이 대상에 도달하지 못할 수 있습니다.

우선 순위가 낮은 노드 및 전용 노드에 대한 가격 정보는 [Batch 가격 책정](https://azure.microsoft.com/pricing/details/batch/)을 참조하세요.

## <a name="node-size"></a>노드 크기

Azure Batch 풀을 만들 때 Azure에서 사용할 수 있는 거의 모든 VM 제품군과 크기 중에서 선택할 수 있습니다. Azure는 다양한 작업에 대해 다양한 VM 크기(특수화된 [HPC](../virtual-machines/sizes-hpc.md) 또는 [GPU 사용 가능](../virtual-machines/sizes-gpu.md) VM 크기 포함)를 제공합니다. 노드 크기는 풀을 만들 때만 선택할 수 있습니다. 즉, 풀을 만든 후에는 해당 노드 크기를 변경할 수 없습니다.

자세한 내용은 [Azure Batch 풀의 컴퓨팅 노드에 대한 VM 크기 선택](batch-pool-vm-sizes.md)을 참조하세요.

## <a name="automatic-scaling-policy"></a>자동 크기 조정 정책

동적 워크로드의 경우 풀에 자동 크기 조정 정책을 적용할 수 있습니다. Batch 서비스는 컴퓨팅 시나리오의 현재 워크로드 및 리소스 사용량에 따라 주기적으로 수식을 평가하여 동적으로 풀 내 노드 수를 조정합니다. 그러면 필요한 리소스만을 사용하고 필요하지 않은 리소스를 해제하여 애플리케이션을 실행하는 전체 비용을 낮출 수 있습니다.

[자동 크기 조정 수식](batch-automatic-scaling.md#autoscale-formulas)을 작성하고 해당 수식의 풀과 연결하여 자동 크기 조정을 사용하도록 설정합니다. Batch 서비스는 이 수식을 사용하여 다음 크기 조정 간격(구성할 수 있는 간격)에 대한 풀의 노드 대상 수를 확인합니다. 풀을 만들 때 자동 크기 조정 설정을 지정하거나 나중에 풀에 자동 크기 조정을 사용하도록 설정할 수 있습니다. 크기 조정을 사용하는 풀에 크기 조정 설정을 업데이트할 수도 있습니다.

예를 들어 작업에서 많은 수의 실행할 태스크를 제출해야 합니다. 현재 큐에 대기된 태스크 수 및 작업에서 이러한 태스크의 완료율에 따라 풀의 노드 수를 조정하는 크기 조정 수식을 풀에 할당할 수 있습니다. Batch 서비스는 수식을 정기적으로 계산하고 워크로드 및 다른 수식 설정에 따라 풀 크기를 조정합니다. 서비스는 큐에 대기 중인 태스크 수가 많은 경우 필요에 따라 노드를 추가하고, 큐에 대기 중이거나 실행 중인 태스크가 없는 경우 노드를 제거합니다.

크기 조정 수식은 다음 메트릭을 기반으로 할 수 있습니다.

- **시간 메트릭** 은 지정된 시간 동안 5분 간격으로 수집되는 통계를 기반으로 합니다.
- **리소스 메트릭** 은 CPU 사용량, 대역폭 사용량, 메모리 사용량 및 노드 수를 기반으로 합니다.
- **태스크 메트릭** 은 *활성*(큐에 대기), *실행* 또는 *완료* 등 태스크 상태에 따라 다릅니다.

자동 크기 조정이 풀에서 컴퓨팅 노드 수를 감소시키면 감소 작업 시에 실행 중인 태스크를 처리하는 방법을 고려해야 합니다. 이를 제공하기 위해 Batch가 수식에 포함할 수 있는 [노드 할당 취소 옵션](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption)을 제공합니다. 예를 들어 실행 중인 태스크를 즉시 중지한 다음 다른 노드에서 실행하기 위해 큐에 다시 넣거나 풀에서 노드를 제거하기 전에 완료하도록 지정할 수 있습니다. 노드 할당 취소 옵션을 `taskcompletion` 또는 `retaineddata`로 설정하면 모든 태스크가 완료될 때까지 또는 모든 태스크 보존 기간이 만료될 때까지 각각 풀 크기 조정 작업을 수행할 수 없습니다.

자동으로 애플리케이션 크기를 조정하는 방법에 대한 자세한 내용은 [Azure Batch 풀에서 자동으로 컴퓨팅 노드 크기 조정](batch-automatic-scaling.md)을 참조하세요.

> [!TIP]
> 컴퓨팅 리소스 사용률을 극대화하기 위해 작업 끝에서 목표 노드 수를 0으로 설정하지만 실행 중인 태스크를 완료할 수 있도록 합니다.

## <a name="task-scheduling-policy"></a>태스크 예약 정책

[노드당 최대 태스크](batch-parallel-node-tasks.md) 구성은 풀 내의 각 컴퓨팅 노드에서 병렬로 실행할 수 있는 최대 태스크 수를 결정합니다.

기본 구성은 노드에서 한 번에 하나의 태스크를 실행하도록 지정하지만 노드에서 동시에 두 개 이상의 태스크를 실행하는 것이 효과적인 시나리오도 있습니다. [동시 노드 태스크](batch-parallel-node-tasks.md) 문서에서 [예제 시나리오](batch-parallel-node-tasks.md#example-scenario)를 참조하여 노드당 여러 태스크를 실행하여 이득이 될 수 있는 방법을 확인합니다.

채우기 유형을 지정할 수도 있습니다. 이는 태스크를 다른 노드에 할당하기 전에 Batch가 태스크를 풀의 모든 노드에 균등하게 분산시킬지 또는 각 노드를 최대 태스크 수로 패키징할지 결정합니다.

## <a name="communication-status"></a>통신 상태

대부분의 시나리오에서 태스크는 독립적으로 작동하고 다른 노드와 통신할 필요가 없습니다. 하지만 [MPI 시나리오](batch-mpi.md)와 같이 태스크가 통신해야 하는 애플리케이션이 있습니다.

**노드 간 통신** 을 허용하도록 풀을 구성할 수 있습니다. 그러면 런타임에 풀 내의 노드가 통신할 수 있습니다. 노드 간 통신 기능을 사용하는 경우 Cloud Services 구성 풀의 노드는 1100보다 큰 포트에서 서로 통신할 수 있고 Virtual Machine 구성 풀은 모든 포트에서 트래픽을 제한하지 않습니다.

노드 간 통신을 사용하도록 설정하면 클러스터 내의 노드 배치에도 영향을 주며 배포 제한 때문에 풀의 노드 최대 수를 제한할 수 있습니다. 애플리케이션에 노드 간 통신이 필요하지 않은 경우 Batch 서비스는 잠재적으로 여러 다른 클러스터 및 데이터 센터의 많은 노드를 풀에 할당하여 병렬 처리 능력을 증가시킬 수 있습니다.

## <a name="start-tasks"></a>시작 태스크

원하는 경우 노드가 풀에 조인될 때마다 그리고 노드가 다시 시작되거나 이미지로 다시 설치될 때마다 각 노드에서 실행되는 [시작 태스크](jobs-and-tasks.md#start-task)를 추가할 수 있습니다. 시작 태스크는 태스크가 컴퓨팅 노드에서 실행하는 애플리케이션을 설치하는 등 태스크를 실행하기 위해 컴퓨팅 노드를 준비하는 데 특히 유용합니다.

## <a name="application-packages"></a>애플리케이션 패키지

애플리케이션 패키지를 지정하여 풀에 컴퓨팅 노드를 배포할 수 있습니다. 애플리케이션 패키지는 태스크가 실행하는 애플리케이션의 간소화된 배포 및 버전 관리를 제공합니다. 풀에 지정할 애플리케이션 패키지는 해당 풀에 조인되거나 다시 부팅 또는 이미지로 다시 설치되는 모든 노드에 설치됩니다.

애플리케이션 패키지를 사용하여 Batch 노드에 애플리케이션을 배포하는 방법에 대한 자세한 내용은 [Batch 애플리케이션 패키지를 사용하여 컴퓨팅 노드에 애플리케이션 배포](batch-application-packages.md)를 참조하세요.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>VNet(가상 네트워크) 및 방화벽 구성

Batch에서 컴퓨팅 노드 풀을 프로비전하면 풀을 Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md)의 서브넷과 연결할 수 있습니다. Azure VNet을 사용하려면 Batch 클라이언트 API가 Azure AD(Active Directory) 인증을 사용해야 합니다. Azure AD에 대한 Azure Batch 지원은 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명되어 있습니다.

### <a name="vnet-requirements"></a>VNet 요구 사항

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

VNet에서 Batch 풀 설정에 대한 자세한 내용은 [가상 네트워크로 가상 머신의 풀 만들기](batch-virtual-network.md)를 참조하세요.

> [!TIP]
> 노드에 액세스하는 데 사용되는 공용 IP 주소가 변경되지 않도록 하려면 [관리하고 있는 지정된 공용 IP 주소가 있는 풀을 생성](create-pool-public-ip.md)할 수 있습니다.

## <a name="pool-and-compute-node-lifetime"></a>풀 및 컴퓨팅 노드 수명

Azure Batch 솔루션을 설계할 때 풀을 만드는 방법 및 시기와 해당 풀 내의 컴퓨팅 노드를 사용 가능한 상태로 유지할 기간을 지정해야 합니다.

하나의 극단적인 예로, 제출하는 각 작업에 대해 풀을 만들고 태스크 실행이 완료되는 즉시 그 풀을 제거할 수 있습니다. 이렇게 하면 노드가 필요할 때만 할당되고 유휴 상태가 되면 종료되므로 사용률이 최대화됩니다. 이것은 작업이 노드가 할당되기를 기다려야 한다는 것을 의미하지만 노드가 개별적으로 할당되고 시작 태스크가 완료되는 즉시 태스크의 실행이 예약된다는 점이 중요합니다. Batch는 태스크를 노드에 할당하기 전에 풀 내의 모든 노드를 사용할 수 있을 때까지 기다리지 *않습니다*. 이렇게 하면 사용 가능한 모든 노드의 최대 사용률을 보장합니다.

또 다른 극단적인 예로, 작업을 즉시 시작하는 것이 우선 순위가 가장 높은 경우 미리 풀을 만들고 작업이 제출되기 전에 해당 노드를 사용할 수 있습니다. 이 시나리오에서는 태스크를 즉시 시작할 수 있지만, 태스크가 할당되기를 기다리는 동안 노드가 유휴 상태일 수 있습니다.

통합 접근 방식은 일반적으로 가변적이지만 지속적인 부하를 처리하는 데 사용됩니다. 여러 작업이 제출되는 풀을 사용할 수 있으며 작업 부하에 따라 노드 수를 확장 또는 축소할 수 있습니다. 이는 현재 부하에 따라 사후 대응적으로 수행하거나 부하를 예측할 수 있는 경우 사전 대응적으로 수행할 수 있습니다. 자세한 내용은 [자동 크기 조정 정책](#automatic-scaling-policy)을 참조하세요.

## <a name="autopools"></a>자동 풀

[자동 풀](/rest/api/batchservice/job/add#autopoolspecification)은 풀에서 실행될 작업보다 먼저 풀이 만들어지는 것이 아니라, Batch 서비스에 의해 작업이 제출될 때 생성되는 풀입니다. Batch 서비스는 지정한 특성에 따라 자동 풀 수명을 관리합니다. 대부분의 경우 해당 풀은 작업이 완료된 후 자동으로 삭제되도록 설정됩니다.

## <a name="security-with-certificates"></a>인증서를 사용한 보안

일반적으로 태스크에 대한 중요한 정보(예: [Azure Storage 계정](accounts.md#azure-storage-accounts)의 키)를 암호화하거나 암호를 해독할 때는 인증서를 사용해야 합니다. 이를 지원하려면 노드에 인증서를 설치할 수 있습니다. 암호화된 암호는 명령줄 매개 변수를 통해 태스크에 전달되거나 태스크 리소스 중 하나에 포함되며 암호를 해독하는 데 설치된 인증서를 사용할 수 있습니다.

[인증서 추가](/rest/api/batchservice/certificate/add) 작업(Batch REST) 또는 [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) 메서드(Batch .NET)를 사용하여 Batch 계정에 인증서를 추가할 수 있습니다. 그런 다음 인증서를 기존 풀이나 새 풀에 연결할 수 있습니다.

인증서가 풀에 연결되면 Batch 서비스가 풀의 각 노드에 인증서를 설치합니다. Batch 서비스는 노드가 시작되면 [시작 태스크](jobs-and-tasks.md#start-task) 및 [작업 관리자 태스크](jobs-and-tasks.md#job-manager-task)를 포함하여 모든 태스크를 시작하기 전에 해당 인증서를 설치합니다.

기존 풀에 풀 인증서를 추가하는 경우 인증서를 노드에 적용하려면 해당 컴퓨팅 노드를 다시 부팅해야 합니다.

## <a name="next-steps"></a>다음 단계

- [작업 및 태스크](jobs-and-tasks.md)에 대해 알아봅니다.
- [풀 및 노드 백그라운드 작업에서 오류를 감지하고 방지](batch-pool-node-error-checking.md)하는 방법을 알아봅니다.
