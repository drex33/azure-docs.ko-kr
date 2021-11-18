---
title: Azure Site Recovery를 통해 Azure 간 재해 복구에 대한 연결 문제 해결
description: Azure VM 재해 복구의 연결 문제 해결
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 24ffce1528aa5c82fec9666fa0cb7b8717107f54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652265"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Azure 간 VM 네트워크 연결 문제 해결

이 문서에서는 한 지역에서 다른 지역으로 Azure VM(가상 머신)을 복제 및 복구할 때 네트워크 연결과 관련된 일반적인 문제를 설명합니다. 네트워킹 요구 사항에 대한 자세한 내용은 [Azure VM 복제를 위한 연결 요구 사항](azure-to-azure-about-networking.md)을 참조하세요.

Site Recovery 복제가 작동하려면 VM에서 특정 URL 또는 IP 범위에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다.

| **이름**                  | **상업용**                               | **정부**                                 | **설명** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 스토리지                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다. VM에 대한 모든 캐시 스토리지 계정을 알고 있는 경우 특정 스토리지 계정 URL에 대해 허용 목록을 사용할 수 있습니다. 예를 들어, `*.blob.core.windows.net` 대신 `cache1.blob.core.windows.net` 및 `cache2.blob.core.windows.net`을 사용합니다. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Site Recovery 서비스 URL에 대한 권한 부여 및 인증에 필요합니다. |
| 복제               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | VM에서 Site Recovery 서비스 통신이 발생할 수 있도록 하는 데 필요합니다. 방화벽 프록시가 IP를 지원하는 경우 해당하는 _사이트 복구 IP_ 를 사용할 수 있습니다. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | VM에서 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 하는 데 필요합니다. 방화벽 프록시가 IP를 지원하는 경우 해당하는 _사이트 복구 모니터링 IP_ 를 사용할 수 있습니다. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Site Recovery URL 또는 IP 범위에 대한 아웃바운드 연결(오류 코드 151037 또는 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>문제 1: Site Recovery에 Azure 가상 머신을 등록하지 못했습니다(151195).

#### <a name="possible-cause"></a>가능한 원인

DNS(Domain Name System) 해결 실패로 인해 사이트 복구 엔드포인트에 연결을 설정할 수 없습니다. 이 문제는 VM을 장애 조치했지만 DR(재해 복구) 지역에서 DNS 서버에 도달할 수 없는 경우 다시 보호하는 동안 더 일반적입니다.

#### <a name="resolution"></a>해결 방법

사용자 지정 DNS를 사용하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인합니다.

VM에서 사용자 지정 DNS 설정을 사용하는지 확인하는 방법:

1. **가상 머신** 을 열고 VM을 선택합니다.
1. VM **설정** 으로 이동하여 **네트워킹** 을 선택합니다.
1. **가상 네트워크/서브넷** 에서 가상 네트워크의 리소스 페이지가 열리는 링크를 선택합니다.
1. **설정** 으로 이동하여 **DNS 서버** 를 선택합니다.

가상 머신에서 DNS 서버에 액세스를 시도합니다. DNS 서버에 액세스할 수 없는 경우 DNS 서버를 장애 조치(failover)하거나 DR 네트워크 및 DNS 사이를 연결하여 액세스할 수 있도록 합니다.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>문제 2: Site Recovery 구성이 실패했습니다(151196).

> [!NOTE]
> VM이 **표준** 내부 부하 분산 장치 뒤에 있는 경우 `login.microsoftonline.com`과 같은 Microsoft 365 IP에 액세스할 수 없습니다. **기본** 내부 부하 분산 장치 형식으로 변경하거나 [Azure CLI를 사용하여 표준 Load Balancer에서 부하 분산 및 아웃바운드 규칙 구성](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration) 문서에 설명된 대로 아웃바운드 액세스를 만듭니다.

#### <a name="possible-cause"></a>가능한 원인

Microsoft 365 인증에 연결하여 IP4 엔드포인트를 식별할 수 없습니다.

#### <a name="resolution"></a>해결 방법

- Azure Site Recovery가 인증을 위해 Microsoft 365 IP 범위에 액세스할 수 있어야 합니다.
- Azure NSG(네트워크 보안 그룹) 규칙/방화벽 프록시를 사용하여 VM의 아웃바운드 네트워크 연결을 제어하는 경우 Microsoft 365 IP 범위로의 통신을 허용해야 합니다. Azure AD에 해당하는 모든 IP 주소에 대한 액세스를 허용하는 [Azure AD(Azure Active Directory) 서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags) 기반 NSG 규칙을 만듭니다.
- 나중에 Azure AD에 새 주소가 추가된 경우 새 NSG 규칙을 만들어야 합니다.

### <a name="example-nsg-configuration"></a>NSG 구성 예제

이 예제에서는 복제할 VM에 대한 NSG 규칙을 구성하는 방법을 보여 줍니다.

- NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 필요한 모든 IP 주소 범위에 대해 포트 443에 **HTTPS 아웃바운드 허용** 규칙을 사용합니다.
- 이 예제에서는 VM 원본 위치가 **미국 동부** 이고 대상 위치가 **미국 중부** 라고 가정합니다.

#### <a name="nsg-rules---east-us"></a>NSG 규칙 - 미국 동부

1. 다음 스크린샷에 표시된 대로 NSG에 대해 HTTP 아웃바운드 보안 규칙을 만듭니다. 이 예제에서는 **대상 서비스 태그**: _Storage.EastUS_ 및 **대상 포트 범위**: _443_ 을 사용합니다.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="Storage.EastUS에 대한 보안 규칙에 대해 아웃바운드 보안 규칙 추가 창을 보여주는 스크린샷.":::

1. 다음 스크린샷에 표시된 대로 NSG에 대해 HTTP 아웃바운드 보안 규칙을 만듭니다. 이 예제에서는 **대상 서비스 태그**: _AzureActiveDirectory_ 및 **대상 포트 범위**: _443_ 을 사용합니다.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="Azure Active Directory용 보안 규칙에 대해 아웃바운드 보안 규칙 추가 창을 보여주는 스크린샷.":::

1. 위의 보안 규칙과 마찬가지로 대상 위치에 해당하는 NSG에서 "EventHub.CentralUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다. 이 규칙은 Site Recovery 모니터링에 대한 액세스를 허용합니다.
1. NSG에서 “AzureSiteRecovery”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다. 이 규칙은 모든 지역에서 Site Recovery 서비스에 대한 액세스를 허용합니다.

#### <a name="nsg-rules---central-us"></a>NSG 규칙 - 미국 중부

이 예제의 경우 이러한 NSG 규칙은 장애 조치(failover) 후 대상 지역에서 원본 지역으로의 복제를 활성화하는 데 필요합니다.

1. _Storage.CentralUS_ 에 대한 HTTPS 아웃바운드 보안 규칙 만들기:

   - **대상 서비스 태그**: _Storage.CentralUS_
   - **대상 포트 범위**: _443_

1. _AzureActiveDirectory_ 에 대한 HTTPS 아웃바운드 보안 규칙을 만듭니다.

   - **대상 서비스 태그**: _AzureActiveDirectory_
   - **대상 포트 범위**: _443_

1. 위의 보안 규칙과 마찬가지로 원본 위치에 해당하는 NSG에서 "EventHub.EastUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다. 이 규칙은 Site Recovery 모니터링에 대한 액세스를 허용합니다.
1. NSG에서 “AzureSiteRecovery”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다. 이 규칙은 모든 지역에서 Site Recovery 서비스에 대한 액세스를 허용합니다.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>문제 3: Site Recovery 구성이 실패했습니다(151197).

#### <a name="possible-cause"></a>가능한 원인

Azure Site Recovery 서비스 엔드포인트에 연결할 수 없습니다.

#### <a name="resolution"></a>해결 방법

Azure NSG(네트워크 보안 그룹) 규칙/방화벽 프록시를 사용하여 컴퓨터에서 아웃바운드 네트워크 연결을 제어하는 경우 허용해야 하는 몇 가지 서비스 태그가 있습니다. [자세히 알아봅니다](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>문제 4: 네트워크 트래픽이 온-프레미스 프록시 서버를 통과할 때 Azure 간 복제에 실패했습니다(151072).

#### <a name="possible-cause"></a>가능한 원인

사용자 지정 프록시 설정이 잘못되어 Azure Site Recovery Mobility Service 에이전트가 IE(Internet Explorer)에서 프록시 설정을 자동으로 검색하지 않았습니다.

#### <a name="resolution"></a>해결 방법

1. Mobility Service 에이전트는 Windows의 경우 IE에서, Linux의 경우 `/etc/environment`에서 프록시 설정을 검색합니다.
1. Azure Site Recovery Mobility Service에 대해서만 프록시를 설정하려는 경우 다음 위치에 있는 _ProxyInfo.conf_ 에서 프록시 세부 정보를 제공할 수 있습니다.

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ 에는 다음 _INI_ 형식의 프록시 설정이 있어야 합니다.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery Mobility Service 에이전트는 **인증되지 않은 프록시** 만 지원합니다.

### <a name="fix-the-problem"></a>문제 해결

[필요한 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 [필요한 IP 범위](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)를 허용하려면 [네트워킹 지침 문서](./azure-to-azure-about-networking.md)의 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계

[Azure VM을 다른 Azure 지역에 복제](azure-to-azure-how-to-enable-replication.md)