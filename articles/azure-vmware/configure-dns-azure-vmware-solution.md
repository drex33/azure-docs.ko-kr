---
title: Azure VMware Solution에 대한 DNS 전달자 구성
description: Azure Portal을 사용하여 Azure VMware Solution에 대한 DNS 전달자를 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/15/2021
ms.openlocfilehash: 4dbf13234f29eb572519f6975e152f22b7093543
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567990"
---
# <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Azure Portal에서 DNS 전달자 구성

>[!IMPORTANT]
>2021년 7월 1일 이후에 생성된 Azure VMware Solution 프라이빗 클라우드의 경우 이제 프라이빗 DNS 확인을 구성할 수 있습니다. 2021년 7월 1일 이전에 만들어져 프라이빗 DNS 확인이 필요한 프라이빗 클라우드의 경우 [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)을 열고 프라이빗 DNS 구성을 요청하세요. 

기본적으로 vCenter와 같은 Azure VMware Solution 관리 구성 요소는 퍼블릭 DNS를 통해 사용할 수 있는 이름 레코드만 확인할 수 있습니다. 그러나 특정 하이브리드 사용 사례는 올바르게 작동하기 위해 Azure VMware Solution 관리 구성 요소가 vCenter 및 Active Directory와 같은 고객 관리형 시스템을 포함하여 개인적으로 호스트된 DNS의 이름 레코드를 확인해야 합니다.

Azure VMware Solution 관리 구성 요소에 대한 프라이빗 DNS를 사용하면 NSX-T DNS 서비스를 통해 선택한 프라이빗 DNS 서버 집합으로 전달하도록 원하는 도메인 이름에 대한 조건부 전달 규칙을 정의할 수 있습니다. 

이 기능은 NSX-T의 DNS 전달자 서비스를 사용합니다. DNS 서비스 및 기본 DNS 영역은 프라이빗 클라우드의 일부로 제공됩니다. Azure VMware Solution 관리 구성 요소가 프라이빗 DNS 시스템의 레코드를 확인할 수 있도록 하려면 FQDN 영역을 정의하고 NSX-T DNS 서비스에 적용해야 합니다. DNS 서비스는 해당 영역에 정의된 외부 DNS 서버를 기준으로 각 영역에 대한 DNS 쿼리를 조건부로 전달합니다.

>[!NOTE]
>DNS 서비스는 최대 5개의 FQDN 영역에 연결됩니다. 각 FQDN 영역에는 최대 3개의 DNS 서버가 연결됩니다.

>[!TIP]
>원할 경우 NSX DNS 서비스 IP 주소를 DNS 서버로 사용하도록 해당 세그먼트의 가상 머신을 구성하여 워크로드 세그먼트에 대한 조건부 전달 규칙을 사용할 수도 있습니다.


## <a name="architecture"></a>아키텍처

이 다이어그램은 NSX-T DNS 서비스가 Azure 및 온-프레미스 환경에서 호스트되는 DNS 시스템에 DNS 쿼리를 전달할 수 있음을 보여 줍니다.

:::image type="content" source="media/networking/dns/dns-forwarder-diagram.png" alt-text="NSX-T DNS 서비스가 Azure 및 온-프레미스 환경에서 호스트되는 DNS 시스템에 DNS 쿼리를 전달할 수 있음을 보여 주는 다이어그램" border="false":::


## <a name="configure-dns-forwarder"></a>DNS 전달자 구성

1. Azure VMware Solution 프라이빗 클라우드의 **워크로드 네트워킹** 에서 **DNS** > **DNS 영역** 을 선택합니다. 그런 다음, **추가** 를 선택합니다.

   >[!NOTE]
   >2021년 7월 1일 이후에 생성된 프라이빗 클라우드의 경우 프라이빗 클라우드를 만드는 동안 기본 DNS 영역이 생성됩니다.

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-1.png" alt-text="Azure VMware Solution 프라이빗 클라우드에 DNS 영역을 추가하는 방법을 보여 주는 스크린샷":::

1. **FQDN 영역** 을 선택하고 이름과 **10.0.0.53** 형식의 최대 3개의 DNS 서버 IP 주소를 지정합니다. 그런 다음, **확인** 을 선택합니다.

   :::image type="content" source="media/networking/dns/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="FQDN 영역을 추가하는 데 필요한 정보를 보여 주는 스크린샷":::

   >[!IMPORTANT]
   >NSX-T는 DNS 영역 이름에서 공백과 영숫자가 아닌 문자를 허용하지만 DNS 영역과 같은 특정 NSX 리소스는 이름이 특정 문자를 허용하지 않는 Azure 리소스에 매핑됩니다. 
   >
   >따라서 NSX-T에서 유효하지 않은 DNS 영역 이름은 [Azure 리소스 명명 규칙](../azure-resource-manager/management/resource-name-rules.md#microsoftresources)에 따라 조정해야 할 수 있습니다.

      이 작업은 완료하는 데 몇 분 정도 걸리며 **알림** 의 진행 상황을 따를 수 있습니다. DNS 영역을 만들 때 알림에 메시지가 표시됩니다.

1. 프라이빗 클라우드의 일부로 생성된 기본 DNS 영역에 대한 메시지는 무시합니다.

1. **DNS 서비스** 탭을 선택하고 **편집** 을 선택합니다.

   >[!TIP]
   >2021년 7월 1일 이후에 생성된 프라이빗 클라우드의 경우, 프라이빗 클라우드를 만드는 동안 생성된 기본 DNS 영역에 대한 메시지는 무시해도 됩니다.


   >[!IMPORTANT]
   >프라이빗 클라우드의 특정 작업은 NSX-T Manager에서 수행할 수 있지만, 2021년 7월 1일 이후에 생성된 프라이빗 클라우드의 경우 기본 Tier-1 게이트웨이에 대해 수행된 구성 변경에 맞게 Azure Portal의 단순화된 네트워킹 환경에서 DNS 서비스를 _편집해야 합니다_.  

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-2.png" alt-text="편집 단추가 선택된 DNS 서비스 탭을 보여 주는 스크린샷":::   

1. **FQDN 영역** 드롭다운에서 새로 만든 FQDN을 선택하고 **확인** 을 선택합니다.

   :::image type="content" source="media/networking/dns/configure-dns-forwarder-3.png" alt-text="DNS 서비스에 대해 선택한 FQDN을 보여 주는 스크린샷":::

   완료하는 데 몇 분 정도 걸리며 완료되면 **알림** 에서 *완료됨* 메시지가 표시됩니다. 이 시점에서 프라이빗 클라우드의 관리 구성 요소는 NSX-T DNS 서비스에 제공된 FQDN 영역의 DNS 항목을 확인할 수 있어야 합니다. 

1. 해당하는 역방향 조회 영역을 포함하여 다른 FQDN 영역에 대해 위 단계를 반복합니다.


## <a name="verify-name-resolution-operations"></a>이름 확인 작업 확인

DNS 전달자를 구성한 후에는 이름 확인 작업을 확인하는 데 사용할 수 있는 몇 가지 옵션이 제공됩니다. 

### <a name="nsx-t-manager"></a>NSX-T Manager

NSX-T Manager는 글로벌 서비스 수준 및 영역별 기준으로 DNS 전달자 서비스 통계를 제공합니다. 

1. NSX-T Manager에서 **네트워킹** > **DNS** 를 선택한 다음, DNS 전달자 서비스를 확장합니다.

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services.png" alt-text="의 DNS 서비스 탭을 보여 주는 스크린샷":::

1. **통계 보기** 를 선택한 다음, **영역 통계** 드롭다운에서 FQDN 영역을 선택합니다.

   상단 절반에는 전체 서비스에 대한 통계가 표시되고, 하단 절반에는 지정된 영역에 대한 통계가 표시됩니다. 이 예제에서는 FQDN 영역을 구성하는 동안 지정된 DNS 서비스에 전달된 쿼리를 볼 수 있습니다.

   :::image type="content" source="media/networking/dns/nsxt-manager-dns-services-statistics.png" alt-text="DNS 전달자 통계를 보여 주는 스크린샷":::


### <a name="powercli"></a>PowerCLI

NSX-T 정책 API를 사용하면 NSX-T DNS 전달자 서비스에서 nslookup 명령을 실행할 수 있습니다. 필수 cmdlet은 PowerCLI에 있는 `VMware.VimAutomation.Nsxt` 모듈의 일부입니다. 다음 예제에서는 해당 모듈의 버전 12.3.0에서 발생한 출력을 보여 줍니다.

1. NSX-T Server에 연결합니다. 

   >[!TIP]
   >Azure Portal의 **관리** > **ID** 에서 NSX-T Server의 IP 주소를 획득할 수 있습니다.
 
   ```powershell
   Connect-NsxtServer -Server 10.103.64.3
   ```

1. DNS 전달자의 nslookup 서비스에 대한 프록시를 가져옵니다.

   ```powershell
   $nslookup = Get-NsxtPolicyService -Name com.vmware.nsx_policy.infra.tier_1s.dns_forwarder.nslookup
   ```

1. DNS 전달자 서비스에서 조회를 수행합니다.

   ```powershell
   $response = $nslookup.get('TNT86-T1', 'vc01.contoso.corp')
   ```

  명령의 첫 번째 매개 변수는 Azure Portal의 DNS 서비스 탭에서 가져올 수 있는 프라이빗 클라우드의 T1 게이트웨이에 대한 ID입니다.

1. 응답의 다음 속성을 사용하여 조회에서 원시응답을 가져옵니다.

   ```powershell
   $response.dns_answer_per_enforcement_point.raw_answer; (()) DiG 9.10.3-P4-Ubuntu (()) @10.103.64.192 -b 10.103.64.192 vc01.contoso.corp +timeout=5 +tries=3 +nosearch ; (1 server found) ;; global options: +cmd ;; Got answer: ;; -))HEADER((- opcode: QUERY, status: NOERROR, id: 10684 ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1  ;; OPT PSEUDOSECTION: ; EDNS: version: 0, flags:; udp: 4096 ;; QUESTION SECTION: ;vc01.contoso.corp.  IN A  ;; ANSWER SECTION: vc01.contoso.corp. 3046 IN A 172.21.90.2  ;; Query time: 0 msec ;; SERVER: 10.103.64.192:53(10.103.64.192) ;; WHEN: Thu Jul 01 23:44:36 UTC 2021 ;; MSG SIZE  rcvd: 62
   ```

   이 예제에서는 주소가 172.21.90.2인 A 레코드를 표시하는 vc01.contoso.corp 쿼리에 대한 응답을 볼 수 있습니다. 또한 이 예제에서는 DNS 전달자 서비스의 캐시된 응답을 보여 주므로 출력이 약간 다를 수 있습니다.
