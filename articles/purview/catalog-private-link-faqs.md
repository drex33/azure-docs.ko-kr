---
title: Azure Purview 프라이빗 엔드포인트 FAQ(질문과 대답)
description: 이 문서에는 Azure Purview 프라이빗 엔드포인트에 관해 자주 묻는 질문과 대답이 나와 있습니다.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 3d7a37565b46137a95f0dd30a3e29b750bed3afe
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122539613"
---
# <a name="faq-about-azure-purview-private-endpoints"></a>Azure Purview 프라이빗 엔드포인트에 관한 FAQ

이 문서에는 고객 및 현장 팀이 [Azure Private Link](../private-link/private-link-overview.md)를 사용하여 Azure Purview 네트워크 구성에 관해 자주 묻는 일반적인 질문과 대답이 나와 있습니다. Azure Purview 방화벽 설정, 프라이빗 엔드포인트, DNS 구성, 관련 구성에 관한 질문을 명확하게 설명하기 위한 것입니다.

Private Link를 사용하여 Azure Purview를 설정하려면 [Azure Purview 계정에 프라이빗 엔드포인트 사용](./catalog-private-link.md)을 참조하세요.

## <a name="common-questions"></a>일반적인 질문

다음과 같은 일반적인 질문과 대답을 확인하세요.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-account-private-endpoint"></a>Azure Purview 계정 프라이빗 엔드포인트를 배포하는 목적은 무엇인가요?

Azure Purview 계정 프라이빗 엔드포인트는 가상 네트워크 내에서 시작되는 클라이언트 호출만 계정에 액세스할 수 있는 시나리오를 사용하도록 설정하여 다른 보안 계층을 추가하는 데 사용됩니다. 이 프라이빗 엔드포인트는 포털 프라이빗 엔드포인트의 필수 조건이기도 합니다.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-portal-private-endpoint"></a>Azure Purview 포털 프라이빗 엔드포인트를 배포하는 목적은 무엇인가요?

Azure Purview 포털 프라이빗 엔드포인트는 Azure Purview Studio에 대한 프라이빗 연결을 제공합니다.

### <a name="whats-the-purpose-of-deploying-the-azure-purview-ingestion-private-endpoints"></a>Azure Purview 수집 프라이빗 엔드포인트를 배포하는 목적은 무엇인가요?

Azure Purview는 수집 프라이빗 엔드포인트를 사용하여 Azure 또는 온-프레미스 환경에서 데이터 원본을 검사할 수 있습니다. 수집 프라이빗 엔드포인트를 만들 때 세 가지 다른 프라이빗 엔드포인트 리소스가 배포되고 Azure Purview 관리되는 리소스에 연결됩니다.

- **Blob** 은 Azure Purview 관리형 스토리지 계정에 연결됩니다.
- **큐** 는 Azure Purview 관리형 스토리지 계정에 연결됩니다.
- **네임스페이스** 는 Azure Purview 관리형 이벤트 허브 네임스페이스에 연결됩니다.

### <a name="can-i-scan-data-through-a-public-endpoint-if-a-private-endpoint-is-enabled-on-my-azure-purview-account"></a>Azure Purview 계정에서 프라이빗 엔드포인트가 사용되는 경우 퍼블릭 엔드포인트를 통해 데이터를 검사할 수 있나요?

예. 프라이빗 엔드포인트를 통해 연결되지 않은 데이터 원본은 퍼블릭 엔드포인트를 사용하여 검사할 수 있지만 Azure Purview는 프라이빗 엔드포인트를 사용하도록 구성됩니다.

### <a name="can-i-scan-data-through-a-service-endpoint-if-a-private-endpoint-is-enabled"></a>프라이빗 엔드포인트가 사용되는 경우 서비스 엔드포인트를 통해 데이터를 검사할 수 있나요?

예. 프라이빗 엔드포인트를 통해 연결되지 않은 데이터 원본은 서비스 엔드포인트를 사용하여 검사할 수 있지만 Azure Purview는 프라이빗 엔드포인트를 사용하도록 구성됩니다.

Azure에서 데이터 원본 리소스의 서비스 엔드포인트 구성 내에 있는 리소스에 액세스하려면 **신뢰할 수 있는 Microsoft 서비스 허용** 을 사용하도록 설정해야 합니다. 예를 들어, 방화벽 및 가상 네트워크 설정이 **선택된 네트워크** 로 지정된 Azure Blob Storage를 검사하려는 경우 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 확인란이 예외로 선택되어 있는지 확인합니다.

### <a name="can-i-access-azure-purview-studio-from-a-public-network-if-public-network-access-is-set-to-deny-in-azure-purview-account-networking"></a>공용 네트워크 액세스가 Azure Purview 계정 네트워킹에서 거부로 설정된 경우 공용 네트워크에서 Azure Purview Studio에 액세스할 수 있나요?

아니요. **공용 네트워크 액세스** 가 **거부** 로 설정된 퍼블릭 엔드포인트에서 Azure Purview에 연결하면 다음 오류 메시지가 표시됩니다.

“이 Purview 계정에 액세스할 수 있는 권한이 없습니다. 이 Purview 계정은 프라이빗 엔드포인트 뒤에 있습니다. Purview 계정의 프라이빗 엔드포인트에 대해 구성된 동일한 VNet(가상 네트워크)의 클라이언트에서 계정에 액세스하세요.”

이 경우 Azure Purview Studio를 열려면 Azure Purview 포털 프라이빗 엔드포인트와 동일한 가상 네트워크에 배포된 머신을 사용하거나 하이브리드 연결이 허용되는 CorpNet에 연결된 VM을 사용합니다.

### <a name="is-it-possible-to-restrict-access-to-the-azure-purview-managed-storage-account-and-event-hub-namespace-for-private-endpoint-ingestion-only-but-keep-portal-access-enabled-for-users-across-the-web"></a>Azure Purview 관리형 스토리지 계정과 이벤트 허브 네임스페이스에 대한 액세스를 제한하지만(프라이빗 엔드포인트 수집에만 해당) 웹에서 사용자에 대해 포털 액세스를 사용하도록 유지할 수 있나요?

아니요. **공용 네트워크 액세스** 를 **거부** 로 설정하면 Azure Purview 관리형 스토리지 계정과 이벤트 허브 네임스페이스에 대한 액세스는 프라이빗 엔드포인트 수집에 대해서만 자동으로 설정됩니다. **공용 네트워크 액세스** 를 **허용** 으로 설정하면 Azure Purview 관리형 스토리지 계정과 이벤트 허브 네임스페이스에 대한 액세스는 **모든 네트워크** 에 대해 자동으로 설정됩니다. 관리형 스토리지 계정 또는 이벤트 허브 네임스페이스에 대한 프라이빗 엔드포인트 수집을 수동으로 수정할 수는 없습니다.

### <a name="if-public-network-access-is-set-to-allow-does-it-mean-the-managed-storage-account-and-event-hub-namespace-can-be-publicly-accessible"></a>공용 네트워크 액세스가 허용으로 설정된 경우 이는 관리형 스토리지 계정과 이벤트 허브 네임스페이스에 공개적으로 액세스할 수 있음을 의미하나요?

아니요. 보호된 리소스로 Azure Purview 관리형 스토리지 계정 및 이벤트 허브 네임스페이스에 대한 액세스는 Azure Purview로만 제한됩니다. 이러한 리소스는 모든 보안 주체에 대해 거부 할당을 사용하여 배포되므로 모든 애플리케이션, 사용자 또는 그룹이 해당 리소스에 대한 액세스 권한을 얻지 못합니다.

Azure 거부 할당에 관한 자세한 내용은 [Azure 거부 할당 이해](../role-based-access-control/deny-assignments.md)를 참조하세요.

### <a name="what-are-the-supported-authentication-types-when-you-use-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하는 경우 지원되는 인증 유형은 무엇인가요?

Azure Key Vault 또는 서비스 주체.

### <a name="what-private-dns-zones-are-required-for-azure-purview-for-a-private-endpoint"></a>프라이빗 엔드포인트의 경우 Azure Purview에 필요한 프라이빗 DNS 영역은 무엇인가요?

Azure Purview ‘계정’ 및 ‘포털’ 프라이빗 엔드포인트의 경우: 

- `privatelink.purview.azure.com`

Azure Purview ‘수집’ 프라이빗 엔드포인트의 경우:

- `privatelink.blob.core.windows.net`
- `privatelink.queue.core.windows.net`
- `privatelink.servicebus.windows.net`

### <a name="do-i-have-to-use-a-dedicated-virtual-network-and-dedicated-subnet-when-i-deploy-azure-purview-private-endpoints"></a>Azure Purview 프라이빗 엔드포인트를 배포할 때 전용 가상 네트워크와 전용 서브넷을 사용해야 하나요?

아니요. 그러나 프라이빗 엔드포인트를 배포하기 전에 대상 서브넷에서 `PrivateEndpointNetworkPolicies`를 사용하지 않도록 설정해야 합니다. 데이터 원본을 프레미스 간에 검사하려는 경우 VNet 피어링을 통해 데이터 원본 가상 네트워크에 대한 네트워크 연결과 온-프레미스 네트워크에 대한 액세스가 있는 가상 네트워크에 Azure Purview를 배포하는 것이 좋습니다.

[프라이빗 엔드포인트에 대한 네트워크 정책을 사용하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md)하는 방법을 알아봅니다.

### <a name="can-i-deploy-azure-purview-private-endpoints-and-use-existing-private-dns-zones-in-my-subscription-to-register-the-a-records"></a>Azure Purview 프라이빗 엔드포인트를 배포하고 내 구독의 기존 프라이빗 DNS 영역을 사용하여 A 레코드를 등록할 수 있나요?

예. Azure Purview와 모든 데이터 원본 레코드에 필요한 모든 내부 DNS 영역에 대한 허브 또는 데이터 관리 구독에서 프라이빗 엔드포인트 DNS 영역을 중앙 집중화할 수 있습니다. Azure Purview에서 프라이빗 엔드포인트 내부 IP 주소를 사용하여 데이터 원본을 확인하려면 이 방법을 사용하는 것이 좋습니다.

또한 기존 프라이빗 DNS 영역에서 가상 네트워크에 대해 [가상 네트워크 링크](../dns/private-dns-virtual-network-links.md)를 설정해야 합니다.

### <a name="can-i-use-azure-integration-runtime-to-scan-data-sources-through-a-private-endpoint"></a>Azure 통합 런타임을 사용하여 프라이빗 엔드포인트를 통해 데이터 원본을 검사할 수 있나요?

아니요. 프라이빗 연결을 사용하여 데이터를 검사하려면 자체 호스팅 통합 런타임을 배포하고 등록해야 합니다. Azure Key Vault 또는 서비스 주체는 데이터 원본에 대한 인증 방법으로 사용해야 합니다.

### <a name="what-are-the-outbound-ports-and-firewall-requirements-for-virtual-machines-with-self-hosted-integration-runtime-for-azure-purview-when-you-use-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하는 경우 Azure Purview에 대한 자체 호스팅 통합 런타임이 있는 가상 머신의 아웃바운드 포트 및 방화벽 요구 사항은 무엇인가요?

자체 호스팅 통합 런타임이 배포되는 VM은 포트 443을 통해 Azure 엔드포인트와 Azure Purview 개인 IP 주소에 대한 아웃바운드 액세스 권한이 있어야 합니다.

### <a name="do-i-need-to-enable-outbound-internet-access-from-the-virtual-machine-running-self-hosted-integration-runtime-if-a-private-endpoint-is-enabled"></a>프라이빗 엔드포인트를 사용하는 경우 자체 호스팅 통합 런타임을 실행하는 가상 머신에서 아웃바운드 인터넷 액세스를 사용하도록 설정해야 하나요?

아니요. 그러나 자체 호스팅 통합 런타임을 실행하는 가상 머신은 포트 443을 사용하여 내부 IP 주소를 통해 Azure Purview 인스턴스에 연결할 수 있습니다. 이름 확인과 연결 테스트에 nslookup.exe, Test-NetConnection 등의 일반적인 문제 해결 도구를 사용합니다.

### <a name="why-do-i-receive-the-following-error-message-when-i-try-to-launch-azure-purview-studio-from-my-machine"></a>내 컴퓨터에서 Azure Purview Studio를 시작하려고 할 때 다음 오류 메시지가 수신된 이유는 무엇인가요?

“이 Purview 계정은 프라이빗 엔드포인트 뒤에 있습니다. Purview 계정의 프라이빗 엔드포인트에 대해 구성된 동일한 VNet(가상 네트워크)의 클라이언트에서 계정에 액세스하세요.”

Azure Purview 계정이 Private Link 사용하여 배포되고 Azure Purview 계정에서 퍼블릭 액세스가 사용되지 않을 수 있습니다. 따라서 Azure Purview에 대한 내부 네트워크 연결이 있는 가상 머신에서 Azure Purview Studio를 검색해야 합니다.

하이브리드 네트워크 뒤의 VM에서 연결하거나 가상 네트워크에 연결된 점프 머신을 사용하는 경우 이름 확인과 연결 테스트에 nslookup.exe, Test-NetConnection 등의 일반적인 문제 해결 도구를 사용합니다.

1. Azure Purview 계정의 개인 IP 주소를 통해 다음 주소를 확인할 수 있는지 유효성을 검사합니다.

   - `Web.Purview.Azure.com`
   - `<YourPurviewAccountName>.Purview.Azure.com`

1. 다음 PowerShell 명령을 사용하여 Azure Purview 계정에 대한 네트워크 연결을 확인합니다.

   ```powershell
   Test-NetConnection -ComputerName <YourPurviewAccountName>.Purview.Azure.com -Port 443
   ```

1. 사용자 고유의 DNS 확인 인프라를 사용하는 경우 프레미스 간 DNS 구성을 확인합니다.

프라이빗 엔드포인트의 DNS 설정에 관한 자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Private Link를 사용하여 Azure Purview를 설정하려면 [Azure Purview 계정에 프라이빗 엔드포인트 사용](./catalog-private-link.md)을 참조하세요.
