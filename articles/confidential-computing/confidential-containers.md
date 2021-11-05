---
title: Azure의 기밀 컨테이너
description: 기밀 컨테이너를 사용 하 여 수정 되지 않은 컨테이너 지원에 대해 알아봅니다.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 11/04/2021
ms.author: amgowda
ms.service: container-service
ms.custom: ignite-fall-2021
ms.openlocfilehash: 69045817c081ebe4eccf6044829b074d23ecd83c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853556"
---
# <a name="confidential-containers-on-azure"></a>Azure의 기밀 컨테이너

Azure 기밀 컴퓨팅은 기밀 컨테이너를 제공 합니다. [기밀 컨테이너에 대해 선택할 수 있는 여러 옵션이](choose-confidential-containers-offerings.md)있습니다. 증명을 사용 하는 보안 및 격리 된 환경에서는 컨테이너 배포의 전반적인 보안을 향상 시킵니다. 

하드웨어 기반의 티 (신뢰할 수 있는 실행 환경)는 강력한 보증을 제공 합니다. T A는 신뢰할 수 있는 컴퓨팅 기반 (TCB) 구성 요소의 하드웨어 및 소프트웨어 측정치를 제공 합니다. Azure에 대 한 기밀 컨테이너 제공을 통해 이러한 측정을 확인 하 고 컨테이너 앱이 검증 가능한 실행 환경에서 실행 되는지 확인할 수 있습니다.

기밀 컨테이너는 모든 프로그래밍 언어로 개발 된 사용자 지정 응용 프로그램을 지원 합니다. 또한 선반에서 Docker 컨테이너 앱을 실행할 수 있습니다.

:::image type="content" source="./media/confidential-containers/sgx-confidential-container.png" alt-text="관리자에 대 한 액세스를 표시 하지 않는 Kubernetes의 기밀 컨테이너 보호 경계 다이어그램":::

## <a name="enablers-with-intel-sgx-on-azure-kubernetes-serviceaks"></a>Azure Kubernetes 서비스 (AKS)에서 Intel SGX를 사용 하는 enablers

 기존 Docker 컨테이너를 실행 하려면 기밀 컴퓨팅 노드의 응용 프로그램에는 특수 CPU 명령 집합을 사용 하는 추상화 계층 또는 SGX SGX (Software Guard Extensions) 소프트웨어가 필요 합니다. 중요 한 응용 프로그램 코드를 보호 하도록 SGX를 구성 합니다. SGX는 CPU에 대 한 직접 실행을 만들어 트러스트 경계에서 게스트 OS (운영 체제), 호스트 OS 또는 하이퍼바이저를 제거 합니다. 이 단계를 통해 전반적인 노출 공격 영역과 취약성을 줄일 수가 줄어듭니다.

AKS (Azure Kubernetes Service)는 기밀 컨테이너를 완전히 지원 합니다. AKS에서 기존 컨테이너를 실행할 수 있습니다.

:::image type="content" source="./media/confidential-containers/confidential-containers-deploy-steps.png" alt-text="Intel SGX 및 AKS를 사용 하도록 설정 하는 새로운 단계를 포함 하는 기밀 컨테이너 변환의 다이어그램입니다.":::

## <a name="partner-enablers"></a>파트너 조력자

Azure 파트너 및 오픈 소스 소프트웨어 (OSS) 프로젝트에서 기밀 컨테이너를 사용 하도록 설정할 수 있습니다. 개발자는 해당 기능에 따라 소프트웨어 공급자를 선택 하 고, Azure 서비스 및 도구 지원과 통합할 수 있습니다. 

> [!IMPORTANT]
> Azure 파트너는 다음과 같은 솔루션을 제공 합니다. 이러한 솔루션은 라이선스 수수료를 유발할 수 있습니다. 모든 파트너 소프트웨어 용어를 독립적으로 확인 합니다. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) 에는 컨테이너 화 된 응용 프로그램을 SGX 지원 기밀 컨테이너로 변환 하는 포털 및 CLI (명령줄 인터페이스) 환경이 있습니다. 응용 프로그램을 수정 하거나 다시 컴파일할 필요가 없습니다. Fortanix는 광범위 한 응용 프로그램을 실행 하 고 관리할 수 있는 유연성을 제공 합니다. 기존 응용 프로그램, 새 enclave 응용 프로그램 및 사전 패키지 된 응용 프로그램을 사용할 수 있습니다. Fortanix의 [Enclave Manager](https://em.fortanix.com/) UI 또는 [REST api](https://www.fortanix.com/api/em/)를 사용 하 여 시작 합니다. [AKS에 대 한](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service)Fortanix의 빠른 시작 가이드를 사용 하 여 기밀 컨테이너를 만듭니다.

![응용 프로그램을 기밀 컨테이너로 이동 하 고 배포 하는 단계를 보여 주는 Fortanix 배포 프로세스 다이어그램](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>SCONE (Scone)

[Scone](https://scontain.com/) (scone) 보안 정책은 인증서, 키 및 비밀을 생성 합니다. 응용 프로그램에 대 한 증명이 있는 서비스만 이러한 자격 증명을 참조 합니다. 응용 프로그램 서비스는 TLS를 통해 서로 증명을 자동으로 수행 합니다. 응용 프로그램 또는 TLS를 수정할 필요가 없습니다. 자세한 설명은 SCONE의 [Flask 응용 프로그램 데모](https://sconedocs.github.io/flask_demo/)를 참조 하세요.

SCONE은 대부분의 기존 이진 파일을 enclaves 내부에서 실행 되는 응용 프로그램으로 변환할 수 있습니다. 응용 프로그램을 변경 하거나 다시 컴파일할 필요가 없습니다. 또한 SCONE은 데이터 파일과 Python 코드 파일을 모두 암호화 하 여 Python과 같은 해석 된 언어도 보호 합니다. SCONE 보안 정책을 사용 하 여 무단 액세스, 수정 및 롤백 으로부터 암호화 된 파일을 보호할 수 있습니다. 자세한 내용은 SCONE에서 [기존 Python 응용 프로그램으로 SCONE를 사용 하는 방법](https://sconedocs.github.io/sconify_image/)에 대 한 설명서를 참조 하세요.

![SCONE에서 이진 이미지를 처리 하는 방법을 보여 주는 SCONE 워크플로의 다이어그램입니다.](./media/confidential-containers/scone-workflow.png)

AKS를 사용 하 여 Azure 기밀 컴퓨팅 노드에 SCONE을 배포할 수 있습니다. 이 프로세스는 완벽 하 게 지원 되 고 통합 됩니다. 자세한 내용은 [Scone SAMPLE AKS application](https://sconedocs.github.io/aks/)을 참조 하세요.

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) 는 AKS에서 수정 되지 않은 컨테이너를 실행 하는 SGX 플랫폼 소프트웨어를 제공 합니다. 자세한 내용은 [기능 및 샘플 응용 프로그램에 대 한 Anjuna 설명서](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)를 참조 하세요.

[여기](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)에서 샘플 Redis Cache 및 Python 사용자 지정 애플리케이션을 시작하세요.

![Azure 기밀 컴퓨팅에서 컨테이너를 실행 하는 방법을 보여 주는 Anjuna의 프로세스 다이어그램](media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>OSS 조력자

> [!NOTE]
> 오픈 소스 프로젝트는 다음 솔루션을 제공 합니다. Azure 기밀 컴퓨팅 및 Microsoft는 이러한 프로젝트와 솔루션과 직접적인 관련이 없습니다.  

### <a name="gramine"></a>Gramine

[Gramine](https://grapheneproject.io/) 는 최소한의 호스트 요구 사항으로 단일 Linux 응용 프로그램을 실행 하도록 설계 된 경량 게스트 OS입니다. Gramine는 격리 된 환경에서 응용 프로그램을 실행할 수 있습니다. 기존 Docker 컨테이너 응용 프로그램을 GSCs (Gramine 차폐 컨테이너)로 변환할 수 있는 도구가 지원 됩니다.

자세한 내용은 Gramine의 [샘플 응용 프로그램 및 AKS의 배포](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) 를 참조 하세요.

### <a name="occlum"></a>Occlum

[Occlum](https://occlum.io/)은 Intel SGX용 LibOS(메모리 안전 다중 프로세스 라이브러리 OS)입니다. OS를 사용 하면 소스 코드를 수정 하지 않고도 SGX에서 레거시 응용 프로그램을 실행할 수 있습니다. Occlum는 기존 Docker 응용 프로그램을 쉽게 "리프트 앤 시프트" 할 수 있는 동시에 사용자 작업의 기밀성을 투명 하 게 보호 합니다.

Occlum은 AKS 배포를 지원합니다. 자세한 내용은 Occlum의 [배포 지침 및 샘플 앱](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)을 참조 하세요.

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/)은 기밀 컨테이너에 대한 오케스트레이션 프레임워크입니다. SGX 지원 Kubernetes에서 기밀 서비스를 실행 하 고 크기를 조정할 수 있습니다. Marblerun은 클러스터의 서비스 확인, 해당 서비스의 비밀 관리 및 이들 사이에 enclave 간 mTLS 연결 설정과 같은 상용구 작업을 처리합니다. 또한 Marblerun은 기밀 컨테이너 클러스터가 간단한 JSON에 정의된 매니페스트를 준수하도록 합니다. 원격 증명을 통해 외부 클라이언트를 사용 하 여 매니페스트를 확인할 수 있습니다.

이 프레임 워크는 단일 enclave의 기밀성, 무결성 및 안정성 속성을 Kubernetes 클러스터로 확장 합니다.

Graphene, Occlum 및 EGo를 사용 하 여 만든 기밀 컨테이너는 [각 SDK에 대 한 예제](https://docs.edgeless.systems/marblerun/#/examples?id=examples)와 함께 지원 됩니다. 프레임 워크는 기존 클라우드 네이티브 도구와 함께 Kubernetes에서 실행 됩니다. CLI 및 투구 차트가 있습니다. 또한 AKS에서 기밀 컴퓨팅 노드를 지원 합니다. [AKS에 대 한 작업을 배포 하려면](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment)어떻게 해야 합니다.

## <a name="confidential-containers-demo"></a>기밀 컨테이너 데모

샘플 응용 프로그램은 [기밀 컨테이너를 사용 하는 의료 데모](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md)를 참조 하세요. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>문의하기

구현에 대 한 질문이 있나요? 기밀 컨테이너를 위한 수단이 되 고 싶으세요? <acconaks@microsoft.com>에 전자 메일을 보냅니다.

## <a name="next-steps"></a>다음 단계

- [Intel SGX 기밀 VM 노드를 통해 AKS 클러스터 배포](./confidential-enclave-nodes-aks-get-started.md)
- [Microsoft Azure Attestation](../attestation/overview.md)
- [Intel SGX 기밀 Virtual Machines](virtual-machine-solutions-sgx.md)
- [AKS(Azure Kubernetes Service)](../aks/intro-kubernetes.md)
