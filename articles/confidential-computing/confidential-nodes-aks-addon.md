---
title: 기밀 Vm에 대 한 Azure Kubernetes 서비스 플러그 인
description: Azure Kubernetes Service를 사용 하 여 기밀 Vm에 대 한 Intel SGX 장치 플러그 인 및 Intel SGX 시세 도우미 데몬 집합을 사용 하는 방법입니다.
author: agowdamsft
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: article
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: a3d764788ed03185b6144925a47c718ca5747c2e
ms.sourcegitcommit: 7e5bfb91cc0a4f94fbe0399f900b0fc7cb59e009
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2021
ms.locfileid: "133187405"
---
# <a name="confidential-computing-plugin-for-confidential-vms"></a>기밀 Vm에 대 한 기밀 컴퓨팅 플러그 인

AKS (azure Kubernetes Service)는 Azure 기밀 컴퓨팅 Vm (가상 머신)에 대 한 플러그 인을 제공 합니다. 플러그 인은 `confcom` 디먼 집합입니다. 플러그 인은 AKS 클러스터에서 Intel 소프트웨어 가드 확장 (SGX) 기밀 Vm에 대해서만 실행 됩니다. 이 플러그 인은 AKS 클러스터 수준에서 등록 됩니다. 플러그 인을 사용 하 여 기밀 노드를 쉽게 관리할 수 있습니다. 시작 하기 전에 [AKS 클러스터에서 플러그 인을 사용 하도록 설정](./confidential-enclave-nodes-aks-get-started.md) 합니다.

## <a name="intel-sgx-device-plugin-for-aks"></a>AKS 용 Intel SGX 장치 플러그 인

SGX 장치 플러그 인은 EPC (Enclave Page Cache) 메모리에 대 한 Kubernetes 장치 플러그 인 인터페이스를 구현 합니다. 실제로이 플러그 인은 Kubernetes에서 EPC 메모리를 다른 리소스 유형으로 만듭니다. 사용자는 다른 리소스와 마찬가지로 EPC에 대 한 제한을 지정할 수 있습니다. 디바이스 플러그 인은 예약 기능 외에도 SGX 디바이스 드라이버 권한을 기밀 워크로드 컨테이너에 할당하는 데 도움이 됩니다. [EPC 메모리 기반 배포 ()의 샘플 구현을](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) `kubernetes.azure.com/sgx_epc_mem_in_MiB` 사용할 수 있습니다.

## <a name="psm-with-sgx-quote-helper"></a>PSM SGX를 사용 하는 경우

원격 증명을 수행 하는 Enclave 응용 프로그램은 견적을 생성 해야 합니다. 따옴표는 enclave의 호스트 환경과 함께 id의 암호화 증명 및 응용 프로그램의 상태를 제공 합니다. 견적 생성은 SGX 플랫폼 소프트웨어 구성 요소 (PSW/d s d)의 일부인 Intel의 특정 신뢰할 수 있는 소프트웨어 구성 요소에 의존 합니다. 이 PSW는 노드당 실행되는 디먼 집합으로 패키지되어 있습니다. Enclave apps에서 증명 견적을 요청할 때 PSW를 사용할 수 있습니다. AKS 제공 서비스를 사용 하면 호스트의 PSW와 기타 SW 구성 요소 간 호환성을 보다 효율적으로 유지할 수 있습니다. 아래의 기능 정보를 읽어 보세요.

원격 증명을 수행 하는 [Enclave 응용 프로그램](confidential-computing-enclaves.md) 에는 생성 된 따옴표가 필요 합니다. 이 따옴표는 응용 프로그램의 id, 상태 및 실행 중인 환경에 대 한 암호화 증명을 제공 합니다. 세대에는 Intel의 PSW에 포함 된 신뢰할 수 있는 소프트웨어 구성 요소가 필요 합니다.

### <a name="overview"></a>개요

> [!NOTE]
> 이 기능은 특수화 된 Intel SGX 하드웨어를 사용 하는 DCsv2/DCsv3 Vm에만 필요 합니다. 
 
Intel은 견적 생성을 실행 하는 두 가지 증명 모드를 지원 합니다. 형식을 선택 하는 방법에 대 한 자세한 내용은 [증명 형식 차이점](#attestation-type-differences)을 참조 하세요.

- **in-proc**: enclave 애플리케이션 프로세스 내부에서 신뢰할 수 있는 소프트웨어 구성 요소를 호스팅합니다.

- **out-of-proc**: enclave 애플리케이션 외부에서 신뢰할 수 있는 소프트웨어 구성 요소를 호스팅합니다.
 
Open Enclave SDK를 사용하여 빌드된 SGX 애플리케이션은 기본적으로 in-proc 증명 모드를 사용합니다. SGX 기반 응용 프로그램은 in-proc를 허용 하며 추가 호스팅을 필요로 합니다. 이러한 응용 프로그램은 응용 프로그램 외부에서 AESM (아키텍처 Enclave Service Manager)와 같은 필수 구성 요소를 노출 합니다.

이 기능을 사용 하는 것이 좋습니다. 이 기능은 Intel 플랫폼 업데이트 또는 enclave 앱에 대 한 가동 시간을 향상 시킵니다.

### <a name="attestation-type-differences"></a>증명 형식 차이

컨테이너화된 각 애플리케이션에는 PSW의 Quote 생성 구성 요소에 대한 업데이트가 필요하지 않습니다.

프로시저 외부에서 컨테이너 소유자는 컨테이너 내에서 업데이트를 관리할 필요가 없습니다. 컨테이너 소유자는 컨테이너 외부에서 중앙 서비스를 호출 하는 제공 된 인터페이스를 대신 사용 합니다. 공급자 업데이트 샌드은이 서비스를 관리 합니다.

In-proc의 경우 오래 된 PSW 구성 요소 때문에 오류가 발생 하지 않습니다. 견적 생성에는 신뢰할 수 있는 컴퓨팅 기반 (TCB)의 일부인 신뢰할 수 있는 SW Enclave (QE) & 프로 비전 인증서 Enclave (PCE)가 포함 됩니다. 증명 요구 사항을 유지하려면 이러한 SW 구성 요소가 최신 상태여야 합니다. 공급자가 이러한 구성 요소에 대한 업데이트를 관리합니다. 고객은 해당 컨테이너 내에서 신뢰할 수 있는 SW 구성 요소가 만료 되어 증명 오류를 처리할 필요가 없습니다.

프로세서 외부에서 EPC 메모리를 사용 하는 것이 좋습니다. In-proc 증명 모드에서 각 enclave 응용 프로그램은 원격 증명에 대 한 QE 및 PCE의 복사본을 인스턴스화합니다. In-proc를 사용 하는 경우 컨테이너는 이러한 enclaves를 호스팅하지 않으며 컨테이너 할당량에서 enclave 메모리를 사용 하지 않습니다.

커널 적용에 대 한 보호책도 있습니다. SGX 드라이버가 Linux 커널로 스트리밍되는 경우 enclave에 더 높은 권한이 있습니다. 이 권한을 통해 enclave는 PCE를 호출 하 여 in-proc 모드로 실행 되는 enclave 응용 프로그램을 중단 합니다. 기본적으로 enclave에는 이 권한이 부여되지 않습니다. 이 권한을 enclave 애플리케이션에 부여하려면 애플리케이션 설치 프로세스를 변경해야 합니다. In-proc 요청을 처리 하는 서비스 공급자는 서비스를이 권한으로 설치 해야 합니다.

PSW 및 d 캡과의 이전 버전과의 호환성을 확인할 필요가 없습니다. 공급자는 이전 버전과의 호환성을 위해 PSW의 견적 생성 구성 요소에 대 한 업데이트의 유효성을 검사 합니다. 이 단계는 이전 버전과의 호환성 문제를 처리 하 고 기밀 작업을 위한 업데이트를 배포 하기 전에 해결 합니다.

### <a name="out-of-proc-attestation-for-confidential-workloads"></a>비밀 워크 로드에 대 한 in-proc 증명

In-proc 증명 모델은 기밀 작업에 대해 작동 합니다. 견적 요청자 및 견적 생성은 개별적으로 실행 되지만 동일한 물리적 컴퓨터에서 실행 됩니다. 견적 생성은 중앙 집중식으로 발생 하며 모든 엔터티에서 견적에 대 한 요청을 처리 합니다. 인터페이스를 적절히 정의 하 고 따옴표를 요청 하는 엔터티에서 인터페이스를 검색할 수 있도록 설정 합니다.

![견적 요청자 및 견적 생성 인터페이스의 다이어그램입니다.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

추상 모델은 기밀 작업 시나리오에 적용 됩니다. 이 모델은 이미 사용할 수 있는 AESM 서비스를 사용 합니다. AESM은 컨테이너 화 된 되 고 Kubernetes 클러스터에 대 한 디먼 집합으로 배포 됩니다. Kubernetes는 Pod로 래핑된 AESM 서비스 컨테이너의 단일 인스턴스가 각 에이전트 노드에 배포되도록 보장합니다. `sgx-device-plugin`AESM 서비스 컨테이너는 `sgx-device-plugin` QE 및 PCE enclaves를 시작 하기 위해에서 EPC 메모리를 요청 하므로 새 SGX 인용 데몬 집합은 디먼 집합에 종속 됩니다.

각 컨테이너는 생성 하는 동안 환경 변수를 설정 하 여 in-proc 견적 생성을 사용 하도록 옵트인 해야 `SGX_AESM_ADDR=1` 합니다. 컨테이너는 또한 `libsgx-quote-ex` 기본 Unix 도메인 소켓으로 요청을 전달 하는 패키지를 포함 해야 합니다.

응용 프로그램은 이전과 마찬가지로 in-process 증명을 계속 사용할 수 있습니다. 그러나 응용 프로그램 내에서 in-proc 및 in-proc를 동시에 사용할 수는 없습니다. out-of-proc 인프라는 기본적으로 사용할 수 있으며 리소스를 소비합니다.

### <a name="sample-implementation"></a>샘플 구현

아래 docker 파일은 Open Enclave 기반 애플리케이션에 대한 샘플입니다. `SGX_AESM_ADDR=1`Docker 파일에서 환경 변수를 설정 합니다. 또는 배포 파일에 변수를 설정 합니다. Docker 파일 및 배포 YAML 세부 정보에 대 한이 샘플을 따릅니다. 

> [!Note] 
> Intel의 응용 프로그램 컨테이너에서 패키지를 실행 하려면 응용 프로그램 컨테이너에서 패키지 **를 패키지 해야** 합니다.
    
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
대신 다음과 같이 배포 YAML 파일에서 in-proc 증명 모드를 설정 합니다.

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

- [AKS에서 기밀 노드 (DCsv2/DCsv3 시리즈) 설정](./confidential-enclave-nodes-aks-get-started.md)
- [기밀 컨테이너에 대 한 빠른 시작 샘플](https://github.com/Azure-Samples/confidential-container-samples)
- [DCsv2 SKU 목록](../virtual-machines/dcv2-series.md)
- [DCSv3 SKU 목록](../virtual-machines/dcv3-series.md)
- [Azure Attestation](../attestation/index.yml)
- [Azure의 Intel SGX 기밀 Virtual Machines](./virtual-machine-solutions-sgx.md)
