---
title: Azure의 기밀 컨테이너
description: 기밀 컨테이너를 통해 수정되지 않은 컨테이너 지원에 대해 알아봅니다.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 11/04/2021
ms.author: amgowda
ms.service: container-service
ms.custom: ignite-fall-2021
ms.openlocfilehash: ee3bdf0aad1e49f6f94d72cf3998760d5f0560f0
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133518168"
---
# <a name="confidential-containers-on-azure"></a>Azure의 기밀 컨테이너

Azure 기밀 컴퓨팅은 기밀 컨테이너를 제공합니다. 기밀 컨테이너 에 대해 선택할 수 있는 여러 [옵션이 있습니다.](choose-confidential-containers-offerings.md) 증명을 통해 보안이 유지되고 격리된 환경은 컨테이너 배포의 전반적인 보안을 향상시킵니다. 

하드웨어 기반 TEE(신뢰할 수 있는 실행 환경)는 강력한 보증을 제공합니다. TEE는 TCB(신뢰할 수 있는 컴퓨팅 기반) 구성 요소에서 하드웨어 및 소프트웨어 측정값을 제공합니다. Azure의 기밀 컨테이너 제품을 사용하면 이러한 측정값을 확인하고 컨테이너 애플리케이션이 확인 가능한 실행 환경에서 실행되는지 유효성을 검사할 수 있습니다.

기밀 컨테이너는 모든 프로그래밍 언어로 개발된 사용자 지정 애플리케이션을 지원합니다. 또한 Docker 컨테이너를 기성품에서 실행할 수도 있습니다.

:::image type="content" source="./media/confidential-containers/sgx-confidential-container.png" alt-text="관리자에 대한 액세스 권한이 없음을 보여 주는 Kubernetes의 기밀 컨테이너 보호 경계 다이어그램":::

## <a name="enablers-with-intel-sgx-on-azure-kubernetes-serviceaks"></a>AKS(Azure Kubernetes Service Intel SGX를 사용하는 Enablers)

 기존 Docker 컨테이너를 실행하려면 기밀 컴퓨팅 노드의 애플리케이션에서 특수 CPU 명령 집합을 사용하려면 추상화 계층 또는 SGX(Intel Software Guard Extensions) 소프트웨어가 필요합니다. 중요한 애플리케이션 코드를 보호하도록 SGX를 구성합니다. SGX는 CPU에 대한 직접 실행을 만들어 트러스트 경계에서 OS(게스트 운영 체제), 호스트 OS 또는 하이퍼바이저를 제거합니다. 이 단계에서는 전반적인 표면 공격 영역 및 취약성을 줄입니다.

AKS(Azure Kubernetes Service)는 기밀 컨테이너를 완벽하게 지원합니다. AKS에서 기존 컨테이너를 기밀로 실행할 수 있습니다.

:::image type="content" source="./media/confidential-containers/confidential-containers-deploy-steps.png" alt-text="Intel SGX 및 AKS를 사용하도록 설정하는 새로운 단계가 있는 기밀 컨테이너 변환 다이어그램":::

## <a name="partner-enablers"></a>파트너 사용

Azure 파트너 및 OSS(오픈 소스 소프트웨어) 프로젝트에서 기밀 컨테이너를 사용하도록 설정할 수 있습니다. 개발자는 해당 기능, Azure 서비스와의 통합 및 도구 지원에 따라 소프트웨어 공급자를 선택할 수 있습니다. 

> [!IMPORTANT]
> Azure 파트너는 다음과 같은 솔루션을 제공합니다. 이러한 솔루션은 라이선스 요금이 발생할 수 있습니다. 모든 파트너 소프트웨어 사용 약관을 독립적으로 확인합니다. 

### <a name="fortanix"></a>Fortanix

[Fortanix에는](https://www.fortanix.com/) 컨테이너화된 애플리케이션을 SGX 지원 기밀 컨테이너로 변환하는 포털 및 CLI(명령줄 인터페이스) 환경이 있습니다. 애플리케이션을 수정하거나 다시 컴파일할 필요가 없습니다. Fortanix는 광범위한 애플리케이션 집합을 실행하고 관리할 수 있는 유연성을 제공합니다. 기존 애플리케이션, 새 enclave 네이티브 애플리케이션 및 미리 패키지된 애플리케이션을 사용할 수 있습니다. Fortanix의 Enclave [관리자](https://em.fortanix.com/) UI 또는 [REST API로 시작합니다.](https://www.fortanix.com/api/em/) AKS에 대한 Fortanix의 [빠른 시작 가이드를](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service)사용하여 기밀 컨테이너를 만듭니다.

![애플리케이션을 기밀 컨테이너로 이동하고 배포하는 단계를 보여 주는 Fortanix 배포 프로세스 다이어그램](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>SCONE(Scontain)

[](https://scontain.com/) SCONE(Scontain) 보안 정책은 인증서, 키 및 비밀을 생성합니다. 애플리케이션에 대한 증명이 있는 서비스만 이러한 자격 증명을 볼 수 있습니다. 애플리케이션 서비스는 TLS를 통해 서로에 대한 증명을 자동으로 수행합니다. 애플리케이션 또는 TLS를 수정할 필요가 없습니다. 자세한 내용은 SCONE의 [Flask 애플리케이션 데모를 참조하세요.](https://sconedocs.github.io/flask_demo/)

SCONE은 대부분의 기존 이진 파일을 enclave 내에서 실행되는 애플리케이션으로 변환할 수 있습니다. 애플리케이션을 변경하거나 다시 컴파일할 필요가 없습니다. 또한 SCONE은 데이터 파일과 Python 코드 파일을 모두 암호화하여 Python과 같은 해석된 언어를 보호합니다. SCONE 보안 정책을 사용하여 무단 액세스, 수정 및 롤백으로부터 암호화된 파일을 보호할 수 있습니다. 자세한 내용은 기존 Python 애플리케이션에서 SCONE을 사용하는 방법에 대한 SCONE 설명서를 [참조하세요.](https://sconedocs.github.io/sconify_image/)

![SCONE이 이진 이미지를 처리하는 방법을 보여 주는 SCONE 워크플로의 다이어그램.](./media/confidential-containers/scone-workflow.png)

AKS를 통해 Azure 기밀 컴퓨팅 노드에 SCONE을 배포할 수 있습니다. 이 프로세스는 완전히 지원되고 통합됩니다. 자세한 내용은 [SCONE 샘플 AKS 애플리케이션을 참조하세요.](https://sconedocs.github.io/aks/)

### <a name="anjuna"></a>Anjuna

[Anjuna는](https://www.anjuna.io/) AKS에서 수정되지 않은 컨테이너를 실행하는 SGX 플랫폼 소프트웨어를 제공합니다. 자세한 내용은 Anjuna의 [기능 및 샘플 애플리케이션에 대한 설명서를 참조하세요.](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

[여기](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)에서 샘플 Redis Cache 및 Python 사용자 지정 애플리케이션을 시작하세요.

![컨테이너가 Azure 기밀 컴퓨팅에서 실행되는 방법을 보여 주는 Anjuna 프로세스의 다이어그램.](media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS 사용

> [!NOTE]
> 오픈 소스 프로젝트는 다음과 같은 솔루션을 제공합니다. Azure 기밀 컴퓨팅 및 Microsoft는 이러한 프로젝트 및 솔루션과 직접적인 관련이 없습니다.  

### <a name="gramine"></a>그라미네

[Gramine은](https://grapheneproject.io/) 최소한의 호스트 요구 사항으로 단일 Linux 애플리케이션을 실행하도록 설계된 경량 게스트 OS입니다. Gramine은 격리된 환경에서 애플리케이션을 실행할 수 있습니다. 기존 Docker 컨테이너 애플리케이션을 GSC(보호된 컨테이너)로 변환하기 위한 도구 지원이 있습니다.

자세한 내용은 AKS에서 Gramine의 [샘플 애플리케이션 및 배포를 참조하세요.](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum

[Occlum](https://occlum.io/)은 Intel SGX용 LibOS(메모리 안전 다중 프로세스 라이브러리 OS)입니다. OS를 사용하면 소스 코드를 거의 또는 전혀 수정하지 않고도 SGX에서 레거시 애플리케이션을 실행할 수 있습니다. 폐색은 사용자 워크로드의 기밀성을 투명하게 보호하면서 기존 Docker 애플리케이션으로 쉽게 "리프트 앤 시프트"할 수 있도록 합니다.

Occlum은 AKS 배포를 지원합니다. 자세한 내용은 Occlum의 [배포 지침 및 샘플 앱을 참조하세요.](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/)은 기밀 컨테이너에 대한 오케스트레이션 프레임워크입니다. SGX 지원 Kubernetes에서 기밀 서비스를 실행하고 확장할 수 있습니다. Marblerun은 클러스터의 서비스 확인, 해당 서비스의 비밀 관리 및 이들 사이에 enclave 간 mTLS 연결 설정과 같은 상용구 작업을 처리합니다. 또한 Marblerun은 기밀 컨테이너 클러스터가 간단한 JSON에 정의된 매니페스트를 준수하도록 합니다. 원격 증명을 통해 외부 클라이언트를 사용하여 매니페스트를 확인할 수 있습니다.

이 프레임워크는 단일 Enclave의 기밀성, 무결성 및 확인 가능성 속성을 Kubernetes 클러스터로 확장합니다.

Graphene, Occlum 및 EGo를 통해 만든 기밀 컨테이너와 [각 SDK에 대한 예제를 지원합니다.](https://docs.edgeless.systems/marblerun/#/examples?id=examples) 프레임워크는 기존 클라우드 네이티브 도구와 함께 Kubernetes에서 실행됩니다. CLI 및 Helm 차트가 있습니다. 또한 이Run은 AKS에서 기밀 컴퓨팅 노드를 지원합니다. [AkS에 을 배포하려면 브러런의 가이드에 따라 을 배포합니다.](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment)

## <a name="confidential-containers-demo"></a>기밀 컨테이너 데모

샘플 애플리케이션은 기밀 [컨테이너가 있는 의료 데모를 참조하세요.](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md) 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>문의하기

구현에 대한 질문이 있나요? 기밀 컨테이너의 사용자가 되고 싶으신가요? <acconaks@microsoft.com>에 전자 메일을 보냅니다.

## <a name="next-steps"></a>다음 단계

- [Intel SGX 기밀 VM 노드를 통해 AKS 클러스터 배포](./confidential-enclave-nodes-aks-get-started.md)
- [Microsoft Azure Attestation](../attestation/overview.md)
- [Intel SGX 기밀 Virtual Machines](virtual-machine-solutions-sgx.md)
- [AKS(Azure Kubernetes Service)](../aks/intro-kubernetes.md)
