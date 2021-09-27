---
title: Azure Security Benchmark Foundation 청사진 샘플 개요
description: Azure Security Benchmark Foundation 청사진 샘플의 개요 및 아키텍처.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 82521251ca395a0d5a1409dae62954950b7da8a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614734"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Azure Security Benchmark Foundation 청사진 샘플의 개요

Azure Security Benchmark Foundation 청사진 샘플은 안전하고 규정을 준수하는 Azure 환경을 구축하는 데 도움이 되는 일련의 기준 인프라 패턴을 제공합니다. 청사진은 고객이 승인 또는 규정 준수 요구 사항이 있는 시나리오에 대한 솔루션을 제공하는 클라우드 기반 아키텍처를 배포하는 데 도움이 됩니다. [Azure Security Benchmark](../../../../security/benchmarks/index.yml)에 정의된 정책 및 기타 가드레일에 따라 네트워크 경계, 모니터링 및 기타 리소스를 배포하고 구성합니다.

## <a name="architecture"></a>아키텍처

이 청사진 샘플로 만들어진 기본 환경은 [허브 및 스포크 모델](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)의 아키텍처 원칙을 기반으로 합니다.
청사진은 추가/선택적 관리, 유지 관리, 관리 및 연결 인프라를 호스트하는 연결, 유지 관리 및 점프 박스 서브넷을 위해 Azure Bastion, 게이트웨이 및 방화벽과 같은 공통 및 공유 리소스, 서비스 및 아티팩트를 포함하는 허브 가상 네트워크를 배포합니다. 웹 및 데이터베이스 서비스와 같은 애플리케이션 워크로드를 호스트하기 위해 하나 이상의 스포크 가상 네트워크가 배포됩니다. 스포크 가상 네트워크는 원활하고 안전한 연결을 위해 Azure 가상 네트워크 피어링을 사용하여 허브 가상 네트워크에 연결됩니다. 샘플 청사진을 다시 할당하거나 수동으로 Azure 가상 네트워크를 만들고 허브 가상 네트워크와 피어링하여 추가 스포크를 추가할 수 있습니다. 스포크 가상 네트워크 및 서브넷에 대한 모든 외부 연결은 허브 가상 네트워크 그리고 방화벽, 게이트웨이 및 유지 관리 점프 박스를 통해 라우팅하도록 구성됩니다.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Azure Security Benchmark Foundation 청사진 샘플 아키텍처 다이어그램" border="false":::

이 청사진은 여러 Azure 서비스를 배포하여 안전하고 모니터링되는 엔터프라이즈급 기반을 제공합니다. 이 환경은 다음과 같은 요소로 구성됩니다.

- 리소스 로그, 활동 로그, 메트릭 및 네트워크 트래픽 흐름이 간편한 쿼리, 분석, 보관 및 경고를 위해 중앙 위치에 저장되도록 하는 [Azure Monitor 로그](../../../../azure-monitor/logs/data-platform-logs.md) 및 Azure 스토리지 계정.
- Azure 리소스에 대한 위협 방지 기능을 제공하는 [Azure Security Center](../../../../security-center/security-center-introduction.md)(표준 버전).
- 온-프레미스 네트워크에 다시 연결하기 위한 서브넷, 인터넷 연결을 위한 수신 및 송신 스택, 추가 관리 또는 유지 관리 서비스 배포를 위한 선택적 서브넷을 지원하는 허브의 [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md). 스포크의 가상 네트워크에는 애플리케이션 워크로드를 호스팅하기 위한 서브넷이 포함됩니다. 적용 가능한 시나리오를 지원하기 위해 필요에 따라 배포 후 추가 서브넷을 만들 수 있습니다.
- 모든 아웃바운드 인터넷 트래픽을 라우팅하고 점프 박스를 통해 인바운드 인터넷 트래픽을 활성화하는 [Azure Firewall](../../../../firewall/overview.md). (기본 방화벽 규칙은 모든 인터넷 인바운드 및 아웃바운드 트래픽을 차단하며 해당되는 경우 배포 후 규칙을 구성해야 합니다.)
- 모든 인터넷 인바운드 및 아웃바운드 트래픽을 차단하도록 구성된 모든 서브넷(Azure Bastion, Gateway 및 Azure Firewall과 같은 서비스 소유 서브넷 제외)에 할당된 [네트워크 보안 그룹](../../../../virtual-network/network-security-group-how-it-works.md)(NSG).
- 공통 네트워크 보안 정책을 적용하기 위해 Azure 가상 머신을 그룹화할 수 있는 [애플리케이션 보안 그룹](../../../../virtual-network/application-security-groups.md).
- 방화벽을 통해 서브넷에서 모든 아웃바운드 인터넷 트래픽을 라우팅하기 위한 [경로 테이블](../../../../virtual-network/manage-route-table.md). (Azure Firewall 및 NSG 규칙은 배포 후 연결을 열도록 구성해야 합니다.)
- Azure 가상 네트워크에서 리소스의 메트릭을 모니터링하고 진단하고 볼 수 있는 [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md).
- DDoS 공격으로부터 Azure 리소스를 보호하는 [Azure DDoS Protection Standard](../../../../ddos-protection/ddos-protection-overview.md).
- 공용 IP 주소, 에이전트 또는 특수 클라이언트 소프트웨어가 필요하지 않은 가상 머신에 원활하고 안전한 연결을 제공하는 [Azure Bastion](../../../../bastion/bastion-overview.md).
- 공용 인터넷을 통해 Azure 가상 네트워크와 온-프레미스 위치 간에 암호화된 트래픽을 사용하도록 설정하는 [Azure VPN Gateway](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md).

> [!NOTE]
> Azure Security Benchmark Foundation은 워크로드에 대한 기본 아키텍처를 배치합니다. 위의 아키텍처 다이어그램에는 서브넷의 잠재적인 사용을 보여주는 몇 가지 개념 리소스가 포함되어 있습니다. 여전히 이 기본 아키텍처에 워크로드를 배포해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Security Benchmark Foundation 청사진 샘플의 개요 및 아키텍처를 검토했습니다.

> [!div class="nextstepaction"]
> [Azure Security Benchmark Foundation 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
