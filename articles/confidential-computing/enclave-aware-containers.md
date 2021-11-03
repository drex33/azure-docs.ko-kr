---
title: Azure의 Enclave 인식 컨테이너
description: AKS(Azure Kubernetes Service)에서 enclave 준비 애플리케이션 컨테이너 지원
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2f1f13a67bb827aa29b9d2de83f2fb46f95095d6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056664"
---
# <a name="enclave-aware-containers-with-intel-sgx"></a>Intel SGX가 있는 Enclave 인식 컨테이너

enclave은 데이터 및 코드 실행에 대한 기밀성을 제공하는 보호된 메모리 영역입니다. 하드웨어로 보호되는 TEE(Trusted Execution Environment)의 인스턴스입니다. AKS의 기밀 컴퓨팅 VM 지원은 [Intel SGX(Software Guard Extensions)](https://software.intel.com/sgx)를 사용하여 각 컨테이너 애플리케이션 간의 노드에 격리된 enclave 환경을 만듭니다.

Intel SGX 가상 머신과 마찬가지로 enclaves에서 실행되도록 개발된 컨테이너 에플리케이션에는 다음과 같은 두 가지 구성 요소가 있습니다.

- 신뢰할 수 없는 구성 요소(호스트라고 함) 및
- 신뢰할 수 있는 구성 요소(enclave라고 함).

![Enclave 인식 컨테이너 아키텍처](./media/enclave-aware-containers/enclaveawarecontainer.png)

Enclave 인식 컨테이너 애플리케이션 아키텍처는 Enclave의 코드 공간을 낮게 유지하면서 구현에 대한 대부분의 제어를 제공합니다. enclave에서 실행되는 코드를 최소화하면 공격 노출 영역을 줄일 수 있습니다.   

## <a name="enablers"></a>Enablers

### <a name="open-enclave-sdk"></a>Enclave SDK 열기
[Open Enclave SDK](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs)는 하드웨어 기반의 신뢰할 수 있는 실행 환경을 사용하는 C, C++ 애플리케이션을 개발하기 위한 하드웨어 독립적인 오픈 소스 라이브러리입니다. 현재 구현에서는 Intel SGX에 대한 지원과 [Arm TrustZone의 OP-TEE OS](https://optee.readthedocs.io/en/latest/general/about.html)에 대한 미리 보기 지원을 제공합니다.

[여기](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs)에서 Open Enclave 기반 컨테이너 애플리케이션 시작

### <a name="intel-sgx-sdk"></a>Intel SGX SDK
Intel은 Linux 및 Windows 컨테이너 작업용 SGX 애플리케이션을 빌드하기 위한 소프트웨어 개발 키트를 유지 관리합니다. 현재 Windows 컨테이너는 AKS 기밀 컴퓨팅 노드에서 지원되지 않습니다.

[여기](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html)에서 Intel SGX 기반 애플리케이션 시작

### <a name="confidential-consortium-framework-ccf"></a>CCF(기밀 컨소시엄 프레임워크)
CCF(기밀 컨소시엄 프레임워크)는 다자간 컴퓨팅 및 데이터에 초점을 맞춘 안전하고 가용성이 높은 고성능 애플리케이션의 새로운 범주를 빌드하기 위한 오픈 소스 프레임워크입니다. CCF는 주요 엔터프라이즈 요구 사항을 충족하는 대규모 기밀 네트워크를 사용할 수 있습니다. 이를 통해 컨소시엄 기반 블록체인 및 다자간 컴퓨팅 기술의 프로덕션 및 엔터프라이즈 채택을 가속화할 수 있는 수단을 제공합니다.

[여기](https://github.com/Microsoft/CCF)에서 Azure 기밀 컴퓨팅 및 CCF 시작

### <a name="confidential-inferencing-onnx-runtime"></a>기밀 추론 ONNX 런타임

오픈 소스 enclave 기반 ONNX 런타임은 클라이언트와 유추 서비스 간의 보안 채널을 설정하여 요청 및 응답이 모두 보안 enclave를 벗어날 수 없도록 합니다. 

이 솔루션을 사용하면 기존 ML 학습 모델을 가져와 기밀로 실행할 수 있으며 증명 및 검증을 통해 클라이언트와 서버 간에 트러스트를 제공할 수 있습니다. 

ML 모델 리프트를 시작하고 [여기](https://aka.ms/confidentialinference)에서 ONNX 런타임으로 전환

### <a name="ego"></a>EGo

오픈 소스 [EGo SDK](https://www.ego.dev)는 Go 프로그래밍 언어를 enclave에 지원합니다. EGo는 Open Enclave SDK를 기반으로 합니다. 기밀 마이크로서비스를 쉽게 빌드할 수 있도록 하는 것을 목표로 합니다. 이 [단계별 가이드](https://github.com/edgelesssys/ego/tree/master/samples/aks)에 따라 AKS에 EGo 기반 서비스를 배포합니다.

## <a name="container-based-sample-implementations"></a>컨테이너 기반 샘플 구현

[AKS의 enclave 인식 컨테이너에 대한 Azure 샘플](https://github.com/Azure-Samples/confidential-computing/tree/main/containersamples)

[Intel SGX 기밀 VM 노드를 통해 AKS 클러스터 배포](./confidential-enclave-nodes-aks-get-started.md)

<!-- LINKS - external -->
[Azure Attestation](../attestation/overview.md)


<!-- LINKS - internal -->
[Azure의 Intel SGX 기밀 가상 머신](./virtual-machine-solutions-sgx.md)
[기밀 컨테이너](./confidential-containers.md)
