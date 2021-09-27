---
title: ISO 27001 Shared Services 청사진 샘플 개요
description: ISO 27001 Shared Services 청사진 샘플에 대한 개요 및 아키텍처입니다. 이 청사진 샘플은 고객이 특정 ISO 27001 컨트롤을 평가하는 데 도움이 됩니다.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: d038345ad07cb41a7a3161dcfb4175d558a34ea3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632028"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 Shared Services 청사진 샘플 개요

ISO 27001 Shared Services 청사진은 ISO 27001 증명에 도움이 되는 일련의 호환 인프라 패턴 및 정책 가드 레일을 제공합니다. 이 청사진은 고객이 승인 또는 규정 준수 요구 사항이 있는 시나리오에 대한 솔루션을 제공하는 클라우드 기반 아키텍처를 구축하는 데 도움이 됩니다.

[ISO 27001 App Service Environment/SQL Database 워크로드](../iso27001-ase-sql-workload/index.md) 청사진 샘플은 이 샘플을 확장합니다.

## <a name="architecture"></a>Architecture

ISO 27001 Shared Services 청사진 샘플은 VDC(Virtual Datacenter) 방식을 기반으로 여러 워크로드를 호스팅하기 위해 조직에서 사용할 수 있는 Azure의 기반 인프라를 배포합니다.
VDC는 Microsoft에서 대기업 고객과 함께 사용하는 입증된 참조 아키텍처 세트, 자동화 도구 및 참여 모델입니다. Shared Services 청사진 샘플은 아래 표시된 완전한 네이티브 Azure VDC 환경을 기반으로 합니다.

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="ISO 27001 Shared Services 청사진 샘플 디자인" border="false":::

이 환경은 ISO 27001 표준을 기반으로 하는 완전히 모니터링되는 안전한 엔터프라이즈급 공유 서비스 인프라를 제공하는 데 사용되는 여러 가지 Azure 서비스로 구성됩니다. 이 환경은 다음과 같은 요소로 구성됩니다.

- 컨트롤 플레인 관점에서 직무 분리에 사용되는 [Azure 역할](../../../../role-based-access-control/overview.md) 인프라를 배포하기 전에 다음과 같은 세 가지 역할이 정의됩니다.
  - NetOps 역할에는 방화벽 설정, NSG 설정, 라우팅 및 기타 네트워크 기능을 비롯한 네트워크 환경을 관리할 수 있는 권한이 있습니다.
  - SecOps 역할에는 [Azure Security Center](../../../../security-center/security-center-introduction.md)를 배포 및 관리하고 [Azure Policy](../../../policy/overview.md) 정의 및 기타 보안 관련 권한을 정의하는 데 필요한 권한이 있습니다.
  - SysOps 역할에는 다른 운영 권한 중에서도 구독 내에서 [Azure Policy](../../../policy/overview.md) 정의를 정의하고 전체 환경에 대한 [Log Analytics](../../../../azure-monitor/overview.md)를 관리하는 데 필요한 권한이 있습니다.
- [Log Analytics](../../../../azure-monitor/overview.md)는 첫 번째 Azure 서비스로 배포되어 보안 배포를 시작한 순간부터 모든 작업과 서비스가 중앙 위치에 기록되도록 합니다.
- 온-프레미스 데이터 센터에 다시 연결하기 위한 서브넷을 지원하는 가상 네트워크, 인터넷 연결을 위한 수신 및 송신 스택, 다음을 비롯한 전체 마이크로 세분화를 위해 NSG 및 ASG를 사용하는 공유 서비스 서브넷:
  - 수신 스택 서브넷에 배포된 [Azure Firewall](../../../../firewall/overview.md)을 통해서만 액세스할 수 있는, 관리 용도로 사용되는 Jumpbox 또는 요새 호스트
  - Azure AD DS(Azure Active Directory Domain Services) 및 DNS를 실행하는 두 대의 가상 머신은 Jumpbox를 통해서만 액세스할 수 있으며, VPN 또는 [ExpressRoute](../../../../expressroute/expressroute-introduction.md) 연결을 통해서만 AD를 복제하도록 구성할 수 있습니다(청사진으로 배포되지 않음).
  - [Azure Net Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) 및 표준 DDoS 보호 사용
- 공유 서비스 환경에 배포된 VM에 사용되는 비밀을 호스팅하는 데 사용되는 [Azure Key Vault](../../../../key-vault/general/overview.md) 인스턴스

이러한 모든 요소는 [Azure 아키텍처 센터 - 참조 아키텍처](/azure/architecture/reference-architectures/)에 게시된 검증된 사례를 따릅니다.

> [!NOTE]
> ISO 27001 Shared Services 인프라는 워크로드에 대한 기본 아키텍처를 배치합니다.
> 이러한 기본 아키텍처 뒤에 워크로드를 배포해야 합니다.

자세한 내용은 [Virtual Datacenter 설명서](/azure/architecture/vdc/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

ISO 27001 Shared Services 청사진 샘플에 대한 개요 및 아키텍처를 검토했습니다.
이제 다음 문서를 참조하여 컨트롤 매핑 및 이 샘플을 배포하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [ISO 27001 Shared Services 청사진 - 컨트롤 매핑](./control-mapping.md)
> [ISO 27001 Shared Services 청사진 - 배포 단계](./deploy.md)

청사진 및 사용 방법에 대한 추가 문서:

- [청사진 수명 주기](../../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../../how-to/update-existing-assignments.md)하는 방법 알아보기
