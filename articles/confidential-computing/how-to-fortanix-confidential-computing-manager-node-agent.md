---
title: Fortanix Confidential Computing Manager로 앱 실행
description: Fortanix Confidential Computing Manager를 사용하여 컨테이너화된 이미지를 변환하는 방법을 알아봅니다.
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 03/24/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcf3e3a1aab9114ceccc9eb32f96347ce3ed0fc2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306419"
---
# <a name="run-an-application-by-using-fortanix-confidential-computing-manager"></a>Fortanix Confidential Computing Manager를 사용하여 애플리케이션 실행

[Fortanix Confidential Computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.em_managed?tab=Overview) 및 [Fortanix](https://www.fortanix.com/)의 [노드 에이전트](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent)를 사용하여 Azure 기밀 컴퓨팅에서 애플리케이션을 실행하는 방법을 알아봅니다.


Fortanix는 Azure 인프라와 함께 작동하는 제품 및 서비스를 제공하는 타사 소프트웨어 공급업체입니다. Azure에 대해 유사한 기밀 컴퓨팅 서비스를 제공하는 다른 타사 공급업체도 존재합니다.

> [!Note]
> 이 문서에 언급된 일부 제품은 Microsoft에서 제공하지 않습니다. Microsoft는 이 정보를 편의상 제공하고 있습니다. 이러한 타사 제품에 대한 참조는 Microsoft의 보증을 의미하지 않습니다.

이 자습서에서는 애플리케이션 이미지를 기밀 컴퓨팅 보호 이미지로 변환하는 방법을 보여줍니다. 이 환경은 Azure DCsv2 시리즈 Intel SGX 지원 가상 머신에서 지원하는 [Fortanix](https://www.fortanix.com/) 소프트웨어를 사용합니다. 이 솔루션은 ID 검증 및 데이터 액세스 제어와 같은 중요한 보안 정책을 오케스트레이션합니다.

Fortanix 지원을 받으려면 [Fortanix Slack 커뮤니티](https://fortanix.com/community/)에 가입하세요. `#enclavemanager` 채널을 사용하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Fortanix Confidential Computing Manager 계정이 없는 경우 시작하기 전에 먼저 [등록](https://ccm.fortanix.com/auth/sign-up)하세요.
- 변환된 애플리케이션 이미지를 푸시할 프라이빗 [Docker](https://docs.docker.com/) 레지스트리가 필요합니다.
- Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

> [!NOTE]
> 평가판 계정은 이 자습서에서 사용되는 가상 머신에 액세스할 수 없습니다. 자습서를 완료하려면 종량제 구독이 필요합니다.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Fortanix 기밀 컴퓨팅 관리자에 애플리케이션 추가

1. [Fortanix CCM(Fortanix 기밀 컴퓨팅 관리자)](https://ccm.fortanix.com)에 로그인합니다.
1. **계정** 페이지로 이동하고 **계정 추가** 를 선택하여 새 계정을 만듭니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-account-new.png" alt-text="계정을 만드는 방법을 보여 주는 스크린샷.":::

1. 계정을 만든 후 **계정 선택** 을 클릭하여 새로 만든 계정을 선택합니다. 이제 컴퓨팅 노드 등록 및 애플리케이션 만들기를 시작할 수 있습니다.
1. **애플리케이션** 탭에서 **+ 애플리케이션** 을 선택하여 애플리케이션을 추가합니다. 이 예제에서는 Python Flask 서버를 실행하는 Enclave OS 애플리케이션을 추가합니다.

1. **Enclave OS 애플리케이션** 의 **추가** 단추를 선택합니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/add-enclave-application.png" alt-text="애플리케이션을 추가하는 방법을 보여 주는 스크린샷.":::

   > [!NOTE]
   > 이 자습서에서는 Enclave OS 애플리케이션만 추가하는 방법을 다룹니다. EDP Rust 애플리케이션을 추가하는 방법에 대한 자세한 내용은 [Confidential Computing Manager에 EDP Rust 앱 가져오기](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager)를 참조하세요.

1. 이 자습서에서는 샘플 애플리케이션에 Fortanix Docker 레지스트리를 사용합니다. 다음 설정에 대해 지정된 값을 입력합니다. 프라이빗 Docker 레지스트리를 사용하여 출력 이미지를 저장합니다.

    - **애플리케이션 이름**: Python 애플리케이션 서버
    - **설명**: Python Flask 서버
    - **입력 이미지 이름**: fortanix/python-flask
    - **출력 이미지 이름**: fortanix-private/python-flask-sgx(자체 레지스트리로 대체)
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **메모리 크기**: 1GB
    - **스레드 수**: 128

      선택 사항: 변환되지 않은 애플리케이션 실행
    - **Docker Hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **앱**: fortanix/python-flask

      다음 명령 실행:

      ```bash
         sudo docker run fortanix/python-flask
      ```
      > [!NOTE]
      > 출력 이미지를 저장하는 데 프라이빗 Docker 레지스트리를 사용하지 않는 것이 좋습니다.

1. 인증서를 추가합니다. 다음 값을 입력하고, **다음** 을 선택합니다.
    - **도메인**: myapp.domain.com
    - **형식**: 기밀 컴퓨팅 관리자가 발급한 인증서
    - **키 경로**: /run/key.pem
    - **키 형식**: RSA
    - **인증서 경로**: /run/cert.pem
    - **RSA 키 크기**: 2048비트

## <a name="create-an-image"></a>이미지 만들기

Fortanix CCM 이미지는 소프트웨어 릴리스 또는 애플리케이션의 버전입니다. 각 이미지는 하나의 MRENCLAVE(enclave 해시)와 연결되어 있습니다.

1. **이미지 추가** 페이지에서 **출력 이미지 이름** 의 레지스트리 자격 증명을 입력합니다. 자격 증명은 이미지가 푸시될 프라이빗 Docker 레지스트리에 액세스하는 데 사용됩니다. 입력 이미지는 공용 레지스트리에 저장되므로 입력 이미지에 대한 자격 증명을 제공할 필요가 없습니다.
1. 이미지 태그를 입력하고 **만들기** 를 선택합니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/create-image.png" alt-text="이미지를 만드는 방법을 보여 주는 스크린샷.":::


## <a name="domain-and-image-allowlist"></a>도메인 및 이미지 허용 목록

도메인이 허용 목록에 추가된 애플리케이션은 Fortanix Confidential Computing Manager에서 TLS 인증서를 받습니다. Enclave OS 애플리케이션이 시작되면 Fortanix Confidential Computing Manager에 연락하여 해당 TLS 인증서를 받습니다.

화면 왼쪽의 **작업** 탭에서 보류 중인 요청을 승인하여 도메인 및 이미지를 허용합니다.

## <a name="enroll-the-compute-node-agent-in-azure"></a>Azure에서 컴퓨팅 노드 에이전트 등록

### <a name="create-and-copy-a-join-token"></a>연결 토큰 만들기 및 복사

이제 Fortanix Confidential Computing Manager에서 토큰을 만듭니다. 이 토큰은 Azure의 컴퓨팅 노드에게 자신을 인증하도록 허용합니다. Azure 가상 머신에는 이 토큰이 필요합니다.

1. **컴퓨팅 노드** 탭에서 **노드 등록** 을 선택합니다.
1. **복사** 단추를 선택하여 연결 토큰을 복사합니다. 컴퓨팅 노드는 이 연결 토큰을 사용하여 자신을 인증합니다.

### <a name="enroll-nodes-into-fortanix-node-agent"></a>Fortanix 노드 에이전트에 노드 등록

Fortanix 노드 에이전트를 만들면 가상 머신, 네트워크 인터페이스, 가상 네트워크, 네트워크 보안 그룹 및 공용 IP 주소가 Azure 리소스 그룹에 배포됩니다. Azure 구독은 가상 머신에 대해 시간당 요금이 청구됩니다. Fortanix 노드 에이전트를 만들기 전에 DCsv2 시리즈에 대한 Azure [가상 머신 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 검토하세요. 사용하지 않는 Azure 리소스를 삭제합니다.

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)로 이동하여 Azure 자격 증명으로 로그인합니다.
1. 검색 상자에 **Fortanix Confidential Computing 노드 에이전트** 를 입력합니다. 검색 결과에서 **Fortanix Confidential Computing 노드 에이전트** 를 선택하여 [앱의 홈페이지](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent?tab=OverviewFortanix)로 이동합니다.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/search-fortanix-marketplace.png" alt-text="앱의 홈 페이지로 이동하는 방법을 보여주는 스크린샷.":::
1. **지금 받기** 를 선택하고 필요한 경우 정보를 제공한 다음, **계속** 을 선택합니다. Azure Portal로 리디렉션됩니다.
1. **만들기** 를 선택하여 Fortanix Confidential Computing 노드 에이전트 배포 페이지로 이동합니다.
1. 이 페이지에서 가상 머신을 배포하기 위한 정보를 입력합니다. VM은 Fortanix 노드 에이전트 소프트웨어가 설치된 Azure의 DCsv2 시리즈 Intel SGX 지원 가상 머신입니다. 노드 에이전트를 사용하면 변환된 이미지를 Azure의 Intel SGX 노드에서 향상된 보안으로 실행할 수 있습니다. 구독을 선택하고 가상 머신 및 관련 리소스를 배포하려는 리소스 그룹을 선택합니다.

   > [!NOTE]
   > Azure에서 DCsv2 시리즈 가상 머신을 배포할 때 제약 조건이 적용됩니다. 추가 코어에 대한 할당량을 요청해야 할 수도 있습니다. 자세한 내용은 [Azure VM의 기밀 컴퓨팅 솔루션](./virtual-machine-solutions-sgx.md)을 참조하세요.

1. 사용 가능한 지역을 선택합니다.
1. **노드 이름** 상자에 가상 머신의 이름을 입력합니다.
1. 가상 머신에 인증하는 데 사용할 사용자 이름과 암호(또는 SSH 키)를 입력합니다.
1. 기본 **OS 디스크 크기** **200** 을 그대로 둡니다. **VM 크기** 를 선택합니다. (이 자습서에는 **Standard_DC4s_v2** 가 적당합니다.)
1. **연결 토큰** 상자에 이 자습서의 앞부분에서 만든 토큰을 붙여넣습니다.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/deploy-fortanix-node-agent-protocol.png" alt-text="리소스를 배포하는 방법을 보여 주는 스크린샷.":::

1. **검토 + 만들기** 를 선택합니다. 유효성 검사를 통과했는지 확인한 다음, **만들기** 를 선택합니다. 모든 리소스가 배포되면 컴퓨팅 노드가 Fortanix Confidential Computing Manager에 등록됩니다.

## <a name="run-the-application-image-on-the-compute-node"></a>컴퓨팅 노드에서 애플리케이션 이미지 실행

다음 명령을 실행하여 애플리케이션을 실행합니다. 노드 IP, 포트 및 변환된 이미지 이름을 애플리케이션의 값으로 변경해야 합니다.

이 자습서의 경우 실행할 명령은 다음과 같습니다.

```bash
    sudo docker run \
        --device /dev/isgx:/dev/isgx \
        --device /dev/gsgx:/dev/gsgx \
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket \
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

이 명령에서 다음이 적용됩니다.

- `52.152.206.164`는 노드 에이전트 호스트 IP입니다.
- `9092`는 노드 에이전트가 수신 대기하는 기본 포트입니다.
- `fortanix-private/python-flask-sgx`는 변환된 앱입니다. Fortanix Confidential Computing Manager 웹 포털에서 찾을 수 있습니다. **이미지** 테이블의 **이미지 이름** 열에 있는 **이미지** 탭에 있습니다.

## <a name="verify-and-monitor-the-running-application"></a>실행 중인 애플리케이션 확인 및 모니터링

1. [Fortanix 기밀 컴퓨팅 관리자](https://ccm.fortanix.com/console)로 돌아갑니다.
1. 노드를 등록한 **계정** 에서 작업 중인지 확인합니다.
1. **애플리케이션** 탭에서 연결된 컴퓨팅 노드를 사용하여 실행 중인 애플리케이션이 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 가상 머신 및 연결된 리소스가 더 이상 필요하지 않은 경우 삭제해도 됩니다. 리소스 그룹을 삭제하면 변환된 이미지와 연결된 노드의 등록을 취소합니다.

가상 머신에 대한 리소스 그룹을 선택한 다음, **삭제** 를 선택합니다. 리소스 그룹의 이름을 확인하고 리소스 삭제를 마칩니다.

직접 만든 Fortanix Confidential Computing Manager 계정을 삭제하려면 Confidential Computing Manager의 [계정 페이지](https://ccm.fortanix.com/accounts)로 이동합니다. 삭제하려는 계정을 마우스로 가리킵니다. 오른쪽 위 모서리에서 검은 세로 점선을 선택한 다음, **계정 삭제** 를 선택합니다.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager-node-agent/delete-account.png" alt-text="계정을 삭제하는 방법을 보여주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Fortanix 도구를 사용하여 애플리케이션 이미지가 기밀 컴퓨팅 가상 머신을 기반으로 실행되도록 변환했습니다. Azure의 기밀 컴퓨팅 가상 머신에 대한 자세한 내용은 [가상 머신에 대한 솔루션](virtual-machine-solutions-sgx.md)을 참조하세요.

Azure의 기밀 컴퓨팅 제품에 대한 자세한 정보를 보려면 [Azure 기밀 컴퓨팅 개요](overview.md)를 참조하세요.

Azure에서 [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) 및 [scone](https://sconedocs.github.io)과 같은 기타 타사 제품을 사용하여 유사한 작업을 완료하는 방법을 알아볼 수도 있습니다.
