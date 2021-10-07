---
title: Azure Firewall을 사용하여 Azure Virtual Desktop 보호
description: Azure Firewall을 사용하여 Azure Virtual Desktop 배포를 보호하는 방법 알아보기
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 10/06/2021
ms.author: victorh
ms.openlocfilehash: 9ac87633dbb4d1b21dfe4fa4430012d91243b8d5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620510"
---
# <a name="use-azure-firewall-to-protect-azure-virtual-desktop-deployments"></a>Azure Firewall을 사용하여 Azure Virtual Desktop 배포 보호

Azure Virtual Desktop은 Azure에서 실행되는 데스크톱 및 앱 가상화 서비스입니다. 최종 사용자가 Azure Virtual Desktop에 연결하는 경우 세션은 호스트 풀에 의해 실행됩니다. 호스트 풀은 세션 호스트로 Azure Virtual Desktop에 등록한 Azure 가상 머신의 컬렉션입니다. 이러한 가상 머신은 가상 네트워크에서 실행되고 가상 네트워크 보안 제어 적용 대상입니다. 원활한 작동을 위해 Azure Virtual Desktop 서비스에 대한 아웃바운드 인터넷 액세스가 필요하며, 최종 사용자에 대한 아웃바운드 인터넷 액세스가 필요할 수도 있습니다. Azure Firewall을 통해 사용자 환경을 잠그고 아웃바운드 트래픽을 필터링할 수 있습니다.

[ ![Azure Virtual Desktop 아키텍처](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

이 문서의 지침에 따라 Azure Firewall을 사용하여 Azure Virtual Desktop 호스트 풀에 대한 추가 보호를 제공하세요.

## <a name="prerequisites"></a>필수 구성 요소

 - 배포된 Azure Virtual Desktop 환경 및 호스트 풀.
 - 하나 이상의 Firewall Manager 정책을 사용하여 Azure Firewall 배포

   자세한 내용은 [자습서: Azure Portal을 사용하여 호스트 풀 만들기](../virtual-desktop/create-host-pools-azure-marketplace.md)를 참조하세요.

Azure Virtual Desktop 환경에 대해 자세히 알아보려면 [Azure Virtual Desktop 환경](../virtual-desktop/environment-setup.md)을 참조하세요.

## <a name="host-pool-outbound-access-to-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 호스트 풀 아웃바운드 액세스

Azure Virtual Desktop에 대해 만든 Azure 가상 머신이 제대로 작동하려면 여러 FQDN(정규화된 도메인 이름)에 액세스할 수 있어야 합니다. Azure Firewall은 이 구성을 간소화하기 위해 Azure Virtual Desktop FQDN 태그를 제공합니다. 다음 단계를 사용하여 아웃바운드 Azure Virtual Desktop 플랫폼 트래픽을 허용합니다.

Azure Firewall 정책을 만들고 네트워크 규칙 및 애플리케이션 규칙에 대한 규칙 컬렉션을 만들어야 합니다. 규칙 컬렉션에 우선 순위와 허용 또는 거부 작업을 지정합니다.

### <a name="create-network-rules"></a>네트워크 규칙 만들기

| 이름      | 소스 형식 | 원본                    | 프로토콜 | 대상 포트 | 대상 형식 | 대상                       |
| --------- | ----------- | ------------------------- | -------- | ----------------- | ---------------- | --------------------------------- |
| 규칙 이름 | IP 주소  | VNet 또는 서브넷 IP 주소 | TCP      | 80                | IP 주소       | 169.254.169.254, 168.63.129.16    |
| 규칙 이름 | IP 주소  | VNet 또는 서브넷 IP 주소 | TCP      | 443               | 서비스 태그      | AzureCloud, WindowsVirtualDesktop |
| 규칙 이름 | IP 주소  | VNet 또는 서브넷 IP 주소 | TCP, UDP | 53                | IP 주소       | *                                 |
|규칙 이름  | IP 주소  | VNet 또는 서브넷 IP 주소 | TCP      | 1688              | IP 주소       | 23.102.135.246                    |

> [!NOTE]
> 일부 배포에는 DNS 규칙이 필요하지 않을 수 있습니다. 예를 들어, Azure Active Directory Domain 컨트롤러는 DNS 쿼리를 168.63.129.16에서 Azure DNS로 전달합니다.

### <a name="create-application-rules"></a>애플리케이션 규칙 만들기

| 이름      | 소스 형식 | 원본                    | 프로토콜   | 대상 형식 | 대상                                                                                 |
| --------- | ----------- | ------------------------- | ---------- | ---------------- | ------------------------------------------------------------------------------------------- |
| 규칙 이름 | IP 주소  | VNet 또는 서브넷 IP 주소 | Https:443  | FQDN 태그         | WindowsVirtualDesktop, WindowsUpdate, Windows Diagnostics, MicrosoftActiveProtectionService |

> [!IMPORTANT]
> Azure 가상 데스크톱에는 TLS 검사를 사용 하지 않는 것이 좋습니다. 자세한 내용은 [프록시 서버 지침](../virtual-desktop/proxy-server-support.md#dont-use-ssl-termination-on-the-proxy-server)을 참조 하세요.

## <a name="host-pool-outbound-access-to-the-internet"></a>인터넷에 대한 호스트 풀 아웃바운드 액세스

조직의 요구 사항에 따라 최종 사용자에 대한 보안 아웃바운드 인터넷 액세스를 사용하도록 설정할 수 있습니다. 허용되는 대상 목록이 잘 정의된 경우(예: [Microsoft 365 액세스](/microsoft-365/enterprise/microsoft-365-ip-web-service)) Azure Firewall 애플리케이션 및 네트워크 규칙을 사용하여 필요한 액세스를 구성할 수 있습니다. 그러면 최상의 성능을 위해 최종 사용자 트래픽을 인터넷으로 직접 라우팅합니다. Windows 365 또는 intune에 대한 네트워크 연결을 허용해야 하는 경우 [Windows 365에 대한 네트워크 요구 사항](/windows-365/requirements-network#allow-network-connectivity) 및 [intune의 네트워크 엔드포인트](/mem/intune/fundamentals/intune-endpoints)를 참조하세요.

기존 온-프레미스 보안 웹 게이트웨이를 사용하여 아웃바운드 사용자 인터넷 트래픽을 필터링하려는 경우 명시적 프록시 구성을 사용하여 Azure Virtual Desktop 호스트 풀에서 실행되는 웹 브라우저 또는 다른 애플리케이션을 구성할 수 있습니다. 예를 들어 [Microsoft Edge 명령줄 옵션을 사용하여 프록시 설정을 구성하는 방법](/deployedge/edge-learnmore-cmdline-options-proxy-settings)을 참조하세요. 이러한 프록시 설정은 최종 사용자 인터넷 액세스에만 영향을 미치며, Azure Firewall을 통해 직접 Azure Virtual Desktop 플랫폼 아웃바운드 트래픽을 허용합니다.

## <a name="control-user-access-to-the-web"></a>웹에 대한 사용자 액세스 제어

관리자는 다른 웹 사이트 범주에 대한 사용자 액세스를 허용하거나 거부할 수 있습니다. 특정 IP 주소에서 웹 범주로 허용하거나 거부할 애플리케이션 컬렉션에 대한 규칙을 추가합니다. 모든 [웹 범주](web-categories.md)를 검토합니다.

## <a name="additional-considerations"></a>기타 고려 사항

요구 사항에 따라 추가 방화벽 규칙을 구성해야 할 수도 있습니다.

- NTP 서버 액세스

  기본적으로 Windows를 실행하는 가상 머신은 시간 동기화를 위해 UDP 포트 123을 통해 `time.windows.com`에 연결합니다. 이 액세스를 허용하는 네트워크 규칙 또는 사용자 환경에서 사용하는 시간 서버를 만듭니다.

## <a name="next-steps"></a>다음 단계

- Azure Virtual Desktop에 대해 자세히 알아보기: [Azure Virtual Desktop이란?](../virtual-desktop/overview.md)
