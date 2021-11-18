---
title: 기밀 VM에 대한 Azure Kubernetes Service 플러그 인
description: Azure Kubernetes Service 있는 기밀 VM에 대해 Intel SGX 디바이스 플러그 인 및 Intel SGX 견적 도우미 디먼 집합을 사용하는 방법입니다.
author: agowdamsft
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: article
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2cdb04ba919b9a62cbf68e0243cb9c5d9b9c4b95
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132761685"
---
# <a name="confidential-computing-plugin-for-confidential-vms"></a>기밀 VM에 대한 기밀 컴퓨팅 플러그 인

AKS(Azure Kubernetes Service)는 Azure 기밀 컴퓨팅 VM(가상 머신)에 대한 플러그 인을 제공합니다. 플러그 인 는 `confcom` 디먼 집합입니다. 플러그 인은 AKS 클러스터의 Intel SGX(Software Guard Extensions) 기밀 VM에 대해서만 실행됩니다. 이 플러그 인은 AKS 클러스터 수준에서 등록됩니다. 플러그 인을 사용하여 기밀 노드를 쉽게 관리할 수 있습니다. 시작하기 전에 [를 사용하도록 설정하여 AKS 클러스터에 플러그 인합니다.](./confidential-enclave-nodes-aks-get-started.md)

## <a name="intel-sgx-device-plugin-for-aks"></a>AKS용 Intel SGX 디바이스 플러그 인

SGX 디바이스 플러그 인은 EPC(Enclave 페이지 캐시) 메모리에 대한 Kubernetes 디바이스 플러그 인 인터페이스를 구현합니다. 실제로 이 플러그 인은 Kubernetes에서 EPC 메모리를 또 다른 리소스 유형으로 만듭니다. 사용자는 다른 리소스와 마찬가지로 EPC에 대한 제한을 지정할 수 있습니다. 디바이스 플러그 인은 예약 기능 외에도 SGX 디바이스 드라이버 권한을 기밀 워크로드 컨테이너에 할당하는 데 도움이 됩니다. [EPC 메모리 기반 배포()의 샘플 구현을](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) `kubernetes.azure.com/sgx_epc_mem_in_MiB` 사용할 수 있습니다.

## <a name="psm-with-sgx-quote-helper"></a>SGX 따옴표 도우미가 있는 PSM

원격 증명을 수행하는 Enclave 애플리케이션은 따옴표를 생성해야 합니다. 따옴표는 enclave의 호스트 환경과 함께 애플리케이션의 ID 및 상태에 대한 암호화 증명을 제공합니다. 견적 생성은 PSW/DCAP(SGX Platform Software Components)의 일부인 Intel의 신뢰할 수 있는 특정 소프트웨어 구성 요소를 기반으로 합니다. 이 PSW는 노드당 실행되는 디먼 집합으로 패키지되어 있습니다. enclave 앱에서 증명 견적을 요청할 때 PSW를 사용할 수 있습니다. AKS 제공 서비스를 사용하면 PSW와 호스트의 다른 SW 구성 요소 간의 호환성을 더 잘 유지할 수 있습니다. 아래 기능 세부 정보를 읽어보세요.

원격 증명을 수행하는 [Enclave 애플리케이션에는](confidential-computing-enclaves.md) 생성된 따옴표가 필요합니다. 이 인용문은 애플리케이션의 ID, 상태 및 실행 환경에 대한 암호화 증명을 제공합니다. 이 생성에는 Intel PSW의 일부인 신뢰할 수 있는 소프트웨어 구성 요소가 필요합니다.

### <a name="overview"></a>개요

> [!NOTE]
> 이 기능은 특수 Intel SGX 하드웨어를 사용하는 DCsv2/DCsv3 VM에만 필요합니다. 
 
Intel은 견적 생성을 실행하는 두 가지 증명 모드를 지원합니다. 유형을 선택하는 방법은 [증명 형식의 차이점을 참조하세요.](#attestation-type-differences)

- **in-proc**: enclave 애플리케이션 프로세스 내부에서 신뢰할 수 있는 소프트웨어 구성 요소를 호스팅합니다.

- **out-of-proc**: enclave 애플리케이션 외부에서 신뢰할 수 있는 소프트웨어 구성 요소를 호스팅합니다.
 
Open Enclave SDK를 사용하여 빌드된 SGX 애플리케이션은 기본적으로 in-proc 증명 모드를 사용합니다. SGX 기반 애플리케이션은 out-of-proc를 허용하며 추가 호스팅이 필요합니다. 이러한 애플리케이션은 AESM(아키텍처 Enclave Service Manager)과 같은 필수 구성 요소를 애플리케이션 외부에 노출합니다.

이 기능을 사용하는 것이 좋습니다. 이 기능은 Intel 플랫폼 업데이트 또는 DCAP 드라이버 업데이트 중 enclave 앱의 작동 시간을 향상시킵니다.

### <a name="attestation-type-differences"></a>증명 유형 차이

컨테이너화된 각 애플리케이션에는 PSW의 Quote 생성 구성 요소에 대한 업데이트가 필요하지 않습니다.

out-of-proc를 사용하여 컨테이너 소유자는 컨테이너 내에서 업데이트를 관리할 필요가 없습니다. 대신 컨테이너 소유자는 컨테이너 외부에서 중앙 집중식 서비스를 호출하는 제공된 인터페이스를 사용합니다. 공급자 업데이트 샌드는 이 서비스를 관리합니다.

out-of-proc의 경우 오래된 PSW 구성 요소 때문에 오류에 대한 문제가 없습니다. 따옴표 생성에는 신뢰할 수 있는 SW 구성 요소인 QE(Quoting Enclave) & TCB(신뢰할 수 있는 컴퓨팅 기반)의 일부인 PCE(Provisioning Certificate Enclave)가 포함됩니다. 증명 요구 사항을 유지하려면 이러한 SW 구성 요소가 최신 상태여야 합니다. 공급자가 이러한 구성 요소에 대한 업데이트를 관리합니다. 고객은 컨테이너 내에서 신뢰할 수 있는 SW 구성 요소가 만료되어 증명 오류를 처리할 필요가 없습니다.

Out-of-proc는 EPC 메모리를 더 잘 사용합니다. in-proc 증명 모드에서 각 enclave 애플리케이션은 원격 증명을 위해 QE 및 PCE의 복사본을 인스턴스화합니다. out-of-proc를 사용하는 경우 컨테이너는 해당 enclave를 호스트하지 않으며 컨테이너 할당량에서 enclave 메모리를 소비하지 않습니다.

커널 적용에 대한 보호도 있습니다. SGX 드라이버가 Linux 커널로 스트리밍되면 enclave에 더 높은 권한이 있습니다. 이 권한을 통해 enclave는 PCE를 호출할 수 있으며, 이로 인해 in-proc 모드에서 실행되는 enclave 애플리케이션이 중단됩니다. 기본적으로 enclave에는 이 권한이 부여되지 않습니다. 이 권한을 enclave 애플리케이션에 부여하려면 애플리케이션 설치 프로세스를 변경해야 합니다. out-of-proc 요청을 처리하는 서비스 공급자는 서비스가 이 권한으로 설치되어 있는지 확인합니다.

PSW 및 DCAP와의 이전 버전과의 호환성을 확인할 필요가 없습니다. 공급자는 이전 버전과의 호환성을 위해 PSW의 견적 생성 구성 요소에 대한 업데이트의 유효성을 검사합니다. 이 단계에서는 호환성 문제를 미리 처리하고 기밀 워크로드에 대한 업데이트를 배포하기 전에 해결합니다.

### <a name="out-of-proc-attestation-for-confidential-workloads"></a>기밀 워크로드에 대한 Out-of-proc 증명

out-of-proc 증명 모델은 기밀 워크로드에 대해 작동합니다. 견적 요청자와 견적 생성은 별도로 실행되지만 동일한 물리적 컴퓨터에서 실행됩니다. 견적 생성은 중앙 집중식 방식으로 발생하며 모든 엔터티의 QUOTES에 대한 요청을 제공합니다. 인터페이스를 올바르게 정의하고 엔터티가 따옴표를 요청할 수 있도록 인터페이스를 검색할 수 있도록 합니다.

![따옴표 요청자 및 따옴표 생성 인터페이스의 다이어그램입니다.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

추상 모델은 기밀 워크로드 시나리오에 적용됩니다. 이 모델은 이미 사용 가능한 AESM 서비스를 사용합니다. AESM은 컨테이너화되고 Kubernetes 클러스터에서 디먼 집합으로 배포됩니다. Kubernetes는 Pod로 래핑된 AESM 서비스 컨테이너의 단일 인스턴스가 각 에이전트 노드에 배포되도록 보장합니다. `sgx-device-plugin`AESM 서비스 컨테이너는 QE 및 PCE enclave를 시작하기 위해 에서 EPC 메모리를 요청하기 때문에 새 SGX Quote 디먼 집합은 디먼 `sgx-device-plugin` 집합에 종속됩니다.

각 컨테이너는 만드는 동안 환경 변수를 설정하여 out-of-proc 견적 생성을 사용하도록 옵트인해야 `SGX_AESM_ADDR=1` 합니다. 컨테이너에는 요청을 기본 `libsgx-quote-ex` Unix 도메인 소켓으로 지정하는 패키지도 포함되어야 합니다.

애플리케이션은 여전히 이전처럼 in-proc 증명을 사용할 수 있습니다. 그러나 애플리케이션 내에서 in-proc 및 out-of-proc를 동시에 사용할 수는 없습니다. out-of-proc 인프라는 기본적으로 사용할 수 있으며 리소스를 소비합니다.

### <a name="sample-implementation"></a>샘플 구현

아래 docker 파일은 Open Enclave 기반 애플리케이션에 대한 샘플입니다. Docker `SGX_AESM_ADDR=1` 파일에서 환경 변수를 설정합니다. 또는 배포 파일에서 변수를 설정합니다. Docker 파일 및 배포 YAML 세부 정보는 이 샘플을 따르세요. 

> [!Note] 
> out-of-proc 증명이 제대로 작동하려면 Intel의 **libsgx-quote-ex** 패키지를 애플리케이션 컨테이너에 패키지해야 합니다.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
대신 다음과 같이 배포 YAML 파일에서 out-of-proc 증명 모드를 설정합니다.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>다음 단계

- [AKS에서 기밀 노드(DCsv2/DCsv3 시리즈) 설정](./confidential-enclave-nodes-aks-get-started.md)
- [기밀 컨테이너에 대한 빠른 시작 샘플](https://github.com/Azure-Samples/confidential-container-samples)
- [DCsv2 SKU 목록](../virtual-machines/dcv2-series.md)
- [DCSv3 SKU 목록](../virtual-machines/dcv3-series.md)
- [Azure Attestation](../attestation/index.yml)
- [Azure의 Intel SGX 기밀 Virtual Machines](/virtual-machine-solutions-sgx.md)
