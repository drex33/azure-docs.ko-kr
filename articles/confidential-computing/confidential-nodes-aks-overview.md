---
title: AKS(Azure Kubernetes Service)의 기밀 컴퓨팅 노드
description: AKS의 기밀 컴퓨팅 노드
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/04/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2a0a6452b978b6924574f1499916a0781413a24
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290232"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Azure Kubernetes Service의 기밀 컴퓨팅 노드

[Azure 기밀 컴퓨팅](overview.md)을 사용하면 사용 중인 중요한 데이터를 보호할 수 있습니다. 기본 기밀 컴퓨팅 인프라는 하드웨어에서 지원되는 신뢰할 수 있는 실행 컨테이너 환경을 통해 다른 애플리케이션, 관리자 및 클라우드 공급자로부터 이 데이터를 보호합니다. 기밀 컴퓨팅 노드를 추가하면 격리되고 하드웨어를 통 해 보호되고 증명 가능한 환경에서 실행되도록 컨테이너 애플리케이션을 대상으로 지정할 수 있습니다.

## <a name="overview"></a>개요

AKS(Azure Kubernetes Service)는 Intel SGX에서 제공되는 [DCsv2 기밀 컴퓨팅 노드](confidential-computing-enclaves.md) 추가를 지원합니다. 이러한 노드를 통해 하드웨어 기반 TEE(신뢰 실행 환경) 내에서 중요한 워크로드를 실행할 수 있습니다. TEE를 사용하면 CPU를 통해 코드를 직접 실행하기 위해 컨테이너의 사용자 수준 코드에서 메모리의 프라이빗 영역을 할당할 수 있습니다. CPU를 통해 직접 실행되는 이러한 프라이빗 메모리 영역을 enclave라고 합니다. enclave는 동일한 노드에서 실행되는 다른 프로세스로부터 데이터 기밀성, 데이터 무결성 및 코드 무결성을 보호하는 데 도움이 됩니다. 또한 Intel SGX 실행 모델은 게스트 OS, 호스트 OS 및 하이퍼바이저의 중간 계층을 제거하여 공격 노출 영역을 줄입니다. 노드의 *컨테이너 격리 실행별 하드웨어 기반* 모델을 사용하면 컨테이너별로 암호화된 특수 메모리 블록을 유지하면서 애플리케이션에서 CPU를 통해 직접 실행할 수 있습니다. 기밀 컨테이너를 사용하는 기밀 컴퓨팅 노드는 제로 트러스트 보안 계획 및 심층 방어 컨테이너 전략에 매우 적합한 추가 요소입니다.

:::image type="content" source="./media/confidential-nodes-aks-overview/sgx-aks-node.png" alt-text="내부에서 보안이 설정된 기밀 컨테이너와 코드 및 데이터를 보여 주는 AKS 기밀 컴퓨팅 노드의 그래픽입니다.":::

## <a name="aks-confidential-nodes-features"></a>AKS 기밀 노드 기능

- Intel SGX TEE(신뢰 실행 환경)를 통한 하드웨어 기반 및 프로세스 수준 컨테이너 격리 
- 형식이 다른 노드 풀 클러스터(기밀 및 비 기밀 노드 풀의 혼합)
- EPC(암호화된 페이지 캐시) 메모리 기반 Pod 예약(추가 기능 필요)
- SGX DCAP 드라이버 사전 설치
- CPU 사용량 기반 수평 Pod 자동 크기 조정 및 클러스터 자동 크기 조정
- Ubuntu 18.04 Gen 2 VM 작업자 노드를 통한 Linux 컨테이너 지원

## <a name="confidential-computing-add-on-for-aks"></a>AKS용 기밀 컴퓨팅 추가 기능
추가 기능은 클러스터에서 기밀 컴퓨팅 노드 풀을 실행할 때 AKS에서 추가 기능을 사용하도록 설정합니다. 이 추가 기능을 통해 사용할 수 있는 기능은 다음과 같습니다.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Intel SGX용 Azure 디바이스 플러그 인 <a id="sgx-plugin"></a>

디바이스 플러그 인은 EPC(암호화된 페이지 캐시) 메모리에 대한 Kubernetes 디바이스 플러그 인 인터페이스를 구현하고 노드에서 디바이스 드라이버를 공개합니다. 실제로 이 플러그 인은 EPC 메모리를 Kubernetes의 또 다른 리소스 종류로 만듭니다. 사용자는 다른 리소스와 마찬가지로 이 리소스에 대한 제한을 지정할 수 있습니다. 디바이스 플러그 인은 예약 기능 외에도 Intel SGX 디바이스 드라이버 권한을 기밀 워크로드 컨테이너에 할당하는 데 도움이 됩니다. 이 플러그 인을 사용하면 개발자가 Intel SGX 드라이버 볼륨을 배포 파일에 탑재하지 않을 수 있습니다. EPC 메모리 기반 배포(`kubernetes.azure.com/sgx_epc_mem_in_MiB`) 샘플의 샘플 구현은 [여기](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)에 있습니다.


## <a name="programming-models"></a>프로그래밍 모델

### <a name="confidential-containers"></a>기밀 컨테이너

[기밀 컨테이너](confidential-containers.md)는 대부분의 **일반 프로그래밍 언어** 런타임(Python, Node, Java 등)의 수정되지 않은 기존 컨테이너 애플리케이션을 기밀로 실행하는 데 도움이 됩니다. 이 패키징 모델은 소스 코드를 수정하거나 다시 컴파일할 필요가 없습니다. 이는 표준 Docker 컨테이너를 오픈 소스 프로젝트 또는 Azure 파트너 솔루션으로 패키지하여 달성할 수 있는 가장 빠른 기밀성 방법입니다. 이 패키징 및 실행 모델에서는 컨테이너 애플리케이션의 모든 부분이 신뢰할 수 있는 경계(enclave)에 로드됩니다. 이 모델은 시장에서 사용할 수 있는 기성 컨테이너 애플리케이션 또는 현재 범용 노드에서 실행되는 사용자 지정 애플리케이션에 적합합니다.

### <a name="enclave-aware-containers"></a>enclave 인식 컨테이너
AKS의 기밀 컴퓨팅 노드는 CPU에서 사용할 수 있는 **특수 명령 세트** 를 활용하기 위해 enclave에서 실행되도록 프로그래밍된 컨테이너도 지원합니다. 이 프로그래밍 모델을 사용하면 실행 흐름을 더 엄격하게 제어할 수 있으며, 특수 SDK 및 프레임워크를 사용해야 합니다. 이 프로그래밍 모델은 가장 낮은 TCB(신뢰 컴퓨팅 기반)를 사용하여 애플리케이션 흐름을 대부분 제어합니다. enclave 인식 컨테이너 개발에는 컨테이너 애플리케이션의 신뢰할 수 없는 부분과 신뢰할 수 있는 부분이 포함되므로 enclave가 실행되는 일반 메모리 및 EPC(암호화된 페이지 캐시) 메모리를 관리할 수 있습니다. enclave 인식 컨테이너에 대해 [자세히 참조하세요](enclave-aware-containers.md).

## <a name="next-steps"></a>다음 단계

[기밀 컴퓨팅 노드를 사용하여 AKS 클러스터 배포](./confidential-enclave-nodes-aks-get-started.md)

[빠른 시작 기밀 컨테이너 샘플](https://github.com/Azure-Samples/confidential-container-samples)

[Intel SGX Confidential VM - DCsv2 SKU 목록](../virtual-machines/dcv2-series.md)

[Intel SGX Confidential VM - DCsv3 SKU 목록](../virtual-machines/dcv3-series.md)

[기밀 컨테이너를 통한 심층 방어 웨비나 세션](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions-sgx.md
