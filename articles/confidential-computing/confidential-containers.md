---
title: AKS(Azure Kubernetes Service)의 기밀 컨테이너
description: 기밀 컨테이너의 수정되지 않은 컨테이너 지원에 대해 알아봅니다.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.subservice: confidential-computing
ms.openlocfilehash: 7b225fa68b9a280ef4a1c7145ab691b82168202a
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967414"
---
# <a name="confidential-containers"></a>기밀 컨테이너

## <a name="overview"></a>개요

개발자는 **기존 docker 애플리케이션(신규 또는 기존)** 을 가져와 기밀 컴퓨팅 노드 지원을 통해 AKS(Azure Kubernetes Service)에서 안전하게 실행할 수 있습니다.

기밀 컨테이너는 다음을 보호하는 데 유용합니다.

- 데이터 무결성(data integrity) 
- 데이터 기밀성
- 코드 무결성
- 암호화를 통한 컨테이너 코드 보호
- 하드웨어 기반 보증
- 기존 앱 실행 허용
- 신뢰할 수 있는 하드웨어 루트 만들기
- 트러스트 경계에서 호스트 관리자, Kubernetes 관리자, 하이퍼바이저 제거

하드웨어 기반의 TEE(신뢰할 수 있는 실행 환경)는 TCB(신뢰할 수 있는 컴퓨팅 기반) 구성 요소의 하드웨어 및 소프트웨어 측정값을 통해 강력한 보증을 제공하는 데 사용되는 중요한 구성 요소입니다. 이러한 측정값을 확인하면 예상되는 컴퓨팅의 유효성 검사에 도움이 되며 컨테이너 앱의 변조를 확인할 수 있습니다.

기밀 컨테이너는 **Python, Java, Node JS 등으로 개발된 사용자 지정 애플리케이션 또는 NGINX, Redis Cache, MemCache 등과 같은 패키징된 컨테이너 애플리케이션** 을 기밀 컴퓨팅 노드가 있는 AKS에서 수정되지 않고 실행할 수 있도록 지원합니다.

기밀 컨테이너는 컨테이너 기밀성을 유지하는 가장 빠른 경로로, 기존 docker 컨테이너 애플리케이션만 재패키징하면 되며 애플리케이션 코드를 변경하지 않아도 됩니다. 기밀 컨테이너는 TEE에서 실행되도록 패키징된 docker 컨테이너 애플리케이션입니다. 일부 기밀 컨테이너 인에이블러는 스토리지 및 전송 중과 호스트에 탑재된 동안 컨테이너 코드를 보호하는 데 유용할 수 있는 컨테이너 암호화도 제공합니다. 그뿐 아니라 컨테이너 암호화를 사용하면 TEE에 연결된 암호 해독 키를 사용하여 컨테이너에 패키징된 코드/모델을 보호할 수 있습니다.

다음은 개발에서 배포까지의 기밀 컨테이너 관련 프로세스입니다. ![ 기밀 컨테이너 방법 프로세스](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>기밀 컨테이너 인에이블러
기존 docker 컨테이너를 수정하지 않고 실행하려면 애플리케이션 호출에서 사용 가능한 특수 CPU 명령 세트를 사용하여 연결 노출 영역을 줄이고 게스트 OS에 대한 종속성을 갖지 않을 수 있도록 SGX 소프트웨어가 필요합니다. SGX 런타임 소프트웨어로 래핑된 컨테이너는 보호된 enclave에서 자동으로 시작되므로 트러스트 경계에서 게스트 OS, 호스트 OS 또는 하이퍼바이저를 제거합니다. 하드웨어에서 지원되는 메모리 내 데이터 암호화를 사용하여 노드(가상 머신)에서 이와 같이 격리된 방식으로 실행할 경우 전반적인 노출 공격 영역이 줄어들고 운영 체제 또는 하이퍼바이저 계층과 관련된 취약성이 감소합니다.

기밀 컨테이너는 AKS에서 완벽하게 지원되며 Azure 파트너 및 OSS(오픈 소스 소프트웨어) 프로젝트를 통해 사용할 수 있습니다. 개발자는 기능, Azure 서비스와의 통합 및 도구 지원을 기준으로 소프트웨어 공급자를 선택할 수 있습니다.

## <a name="partner-enablers"></a>파트너 인에이블러
> [!NOTE]
> 아래 솔루션은 Azure 파트너를 통해 제공되며 라이선스 요금을 발생할 수 있습니다. 파트너 소프트웨어 조건을 별도로 확인하세요. 

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/)는 AKS에서 수정되지 않은 컨테이너를 실행할 수 있도록 하는 SGX 플랫폼 소프트웨어를 제공합니다. [여기](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)에서 이 기능에 대해 자세히 알아보고 샘플 애플리케이션을 확인하세요.

[여기](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)에서 샘플 Redis Cache 및 Python 사용자 지정 애플리케이션을 시작하세요.

![Anjuna 프로세스](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/)는 개발자에게 애플리케이션을 수정하거나 다시 컴파일하지 않고도 컨테이너화된 애플리케이션을 가져와 SGX 지원 기밀 컨테이너로 변환할 수 있는 포털 및 CLI 기반 환경 옵션을 제공합니다. Fortanix는 기존 애플리케이션, 새로운 enclave 애플리케이션 및 사전 패키징된 애플리케이션을 포함하는 광범위한 애플리케이션을 실행하고 관리할 수 있는 유연성을 제공합니다. 사용자는 [Confidential Computing Manager](https://em.fortanix.com/) UI 또는 [REST API](https://www.fortanix.com/api/em/)로 시작하여 Azure Kubernetes Service에 대한 [빠른 시작](https://fortanix.com/blog/2020/10/fortanix-confidential-containers-on-microsoft-azure-kubernetes-service-aks/) 가이드에 따라 기밀 컨테이너를 만들 수 있습니다.

![Fortanix 배포 프로세스](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone(Scontain)

[SCONE](https://scontain.com/index.html?lang=en)은 인증서, 키 및 비밀을 생성할 수 있는 보안 정책을 지원하며, 애플리케이션의 인증된 서비스에만 표시되도록 합니다. 이러한 방식으로 애플리케이션의 서비스는 애플리케이션이나 TLS를 수정할 필요 없이 TLS를 통해 자동으로 서로를 증명합니다. https://sconedocs.github.io/flask_demo/ 에서는 간단한 Flask 애플리케이션을 활용하여 이 내용이 설명되어 있습니다.  

SCONE은 애플리케이션을 변경하거나 다시 컴파일할 필요 없이 대부분의 이진 파일을 enclave 내부에서 실행되는 애플리케이션으로 변환할 수 있습니다. 또한 SCONE은 Python 코드 파일뿐만 아니라 데이터 파일을 모두 **암호화** 하여 Python과 같은 해석된 언어를 보호합니다. SCONE 보안 정책을 사용하면 무단 액세스, 수정 및 롤백으로부터 암호화된 파일을 보호할 수 있습니다. 기존 Python 애플리케이션을 "sconify"하는 방법은 [여기](https://sconedocs.github.io/sconify_image/)에 설명되어 있습니다.

![Scontain 흐름](./media/confidential-containers/scone-workflow.png)

AKS를 사용한 기밀 컴퓨팅 노드에 대한 Scone 배포는 완전히 지원되며 다른 Azure 서비스와 통합됩니다. https://sconedocs.github.io/aks/ 에 애플리케이션 통합 시작 참조


## <a name="oss-enablers"></a>OSS 인에이블러 
> [!NOTE]
> 아래 솔루션은 Open-Source Projects를 통해 제공되며 ACC(Azure 기밀 컴퓨팅) 또는 Microsoft와 직접적으로 관련되어 있지 않습니다.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/)은 최소한의 호스트 요구 사항으로 단일 Linux 애플리케이션을 실행하도록 설계된 간단한 게스트 OS입니다. Graphene은 완전한 OS를 실행하는 것과 비슷한 이점을 누리면서 격리된 환경에서 애플리케이션을 실행할 수 있으며, 기존 Docker 컨테이너 애플리케이션을 GSC(Graphene Shielded Container)로 변환하기 위한 적절한 도구 지원을 제공합니다.

[여기](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)에서 AKS의 샘플 애플리케이션 및 배포 시작

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/)은 Intel SGX용 LibOS(메모리 안전 다중 프로세스 라이브러리 OS)입니다. 이를 통해 소스 코드를 거의 또는 전혀 수정하지 않고도 SGX에서 레거시 애플리케이션을 실행할 수 있습니다. Occlum은 사용자 워크로드의 기밀성을 투명하게 보호하면서 기존 Docker 애플리케이션으로 쉽게 리프트 앤 시프트할 수 있도록 합니다.

Occlum은 AKS 배포를 지원합니다. [여기](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)에서 다양한 샘플 앱을 포함하는 배포 지침 따르기

### <a name="marblerun"></a>Marblerun

[Marblerun](https://marblerun.sh/)은 기밀 컨테이너에 대한 오케스트레이션 프레임워크입니다. SGX 지원 Kubernetes에서 기밀 서비스를 쉽게 실행하고 확장할 수 있습니다. Marblerun은 클러스터의 서비스 확인, 해당 서비스의 비밀 관리 및 이들 사이에 enclave 간 mTLS 연결 설정과 같은 상용구 작업을 처리합니다. 또한 Marblerun은 기밀 컨테이너 클러스터가 간단한 JSON에 정의된 매니페스트를 준수하도록 합니다. 원격 증명을 통해 외부 클라이언트에서 매니페스트를 확인할 수 있습니다. 

![Marblerun 흐름](./media/confidential-containers/marblerun-workflow.png)

간단히 말해, Marblerun은 단일 Enclave의 기밀성, 무결성 및 검증 가능성 속성을 Kubernetes 클러스터로 확장합니다. 

Marblerun은 Graphene, Occlum 및 EGo를 통해 만든 기밀 컨테이너를 지원합니다. [여기](https://docs.edgeless.systems/marblerun/#/examples?id=examples)에는 각 SDK의 예가 나와 있습니다. Marblerun은 Kubernetes 및 기존 클라우드 네이티브 도구와 함께 실행하도록 빌드되었습니다. 사용하기 쉬운 CLI 및 helm 차트가 함께 제공됩니다. AKS의 기밀 컴퓨팅 노드에 대한 고급 지원을 제공합니다. AKS에서 Marblerun을 배포하는 방법에 대한 정보는 [여기](https://docs.edgeless.systems/marblerun/#/deployment/cloud?id=cloud-deployment)에서 찾을 수 있습니다.

## <a name="confidential-containers-demo"></a>기밀 컨테이너 데모
기밀 컨테이너를 사용하는 기밀 의료 데모를 봅니다. 샘플은 [여기](/azure/architecture/example-scenario/confidential/healthcare-inference)에서 사용할 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>문의하기

구현에 대해 질문이 있거나 인에이블러가 되고 싶나요? 제품 팀 **acconaks@microsoft.com** 으로 메일 보내기

## <a name="reference-links"></a>참조 링크

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[AKS(Azure Kubernetes Service)](../aks/intro-kubernetes.md)
