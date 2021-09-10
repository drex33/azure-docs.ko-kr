---
title: Azure 트래픽 분석 스키마 | Microsoft Docs
description: Azure 네트워크 보안 그룹 흐름 로그를 분석하는 트래픽 분석 스키마를 이해합니다.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: fb32ff13df7329e6e78095b8ee28639312cc62b5
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113216246"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>트래픽 분석에서 스키마 및 데이터 집계

트래픽 분석은 클라우드 네트워크의 사용자 및 애플리케이션 작업에 대한 가시성을 제공하는 클라우드 기반 솔루션입니다. 트래픽 분석은 Network Watcher NSG(네트워크 보안 그룹) 흐름 로그를 분석하여 Azure 클라우드 내 트래픽 흐름에 대한 정보를 제공합니다. 트래픽 분석을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Azure 구독에서 이루어지는 네트워크 작업을 시각화하고 핫스폿을 식별합니다.
- 열린 포트, 인터넷 액세스를 시도하는 애플리케이션, 불량 네트워크에 연결하는 VM(가상 머신) 같은 정보를 통해 네트워크에 대한 위협을 식별하고 네트워크를 보호합니다.
- Azure 지역 및 인터넷의 트래픽 흐름 패턴을 파악하여 네트워크 성능 및 용량에 맞게 네트워크 배포를 최적화합니다.
- 네트워크에서 연결 실패의 원인이 되는 네트워크 구성 오류를 정확히 파악합니다.
- 네트워크 사용량(바이트, 패킷 또는 흐름)을 파악합니다.

### <a name="data-aggregation"></a>데이터 집계

1. “FlowIntervalStartTime_t”와 “FlowIntervalEndTime_t” 사이에 발생한 NSG의 모든 흐름 로그는 트래픽 분석에서 처리되기 전에 1분 간격으로 스토리지 계정에 Blob으로 캡처됩니다.
2. 트래픽 분석의 기본 처리 간격은 60분입니다. 즉, 트래픽 분석은 60분마다 집계를 위해 스토리지에서 Blob을 선택합니다. 선택한 처리 간격이 10분인 경우 트래픽 분석은 10분마다 스토리지 계정에서 Blob을 선택합니다.
3. 원본 IP, 대상 IP, 대상 포트, NSG 이름, NSG 규칙, 흐름 방향, 전송 계층 프로토콜(TCP 또는 UDP)이 동일한 흐름(참고: 원본 포트는 집계에서 제외됨)은 트래픽 분석을 통해 단일 흐름으로 결합됩니다.
4. 이 단일 레코드는 Log Analytics에서 트래픽 분석을 통해 데코레이트되고(아래 섹션의 세부 정보) 수집됩니다. 이 프로세스는 최대 1시간이 걸릴 수 있습니다.
5. FlowStartTime_t 필드는 “FlowIntervalStartTime_t”와 “FlowIntervalEndTime_t” 사이의 흐름 로그 처리 간격에서 집계된 흐름(동일한 4튜플)이 처음 발생한 시간을 나타냅니다.
6. TA의 모든 리소스에 대해 UI에 표시된 흐름은 NSG에 표시되는 총 흐름이지만 Log Analytics에서는 사용자가 축소된 단일 레코드만 볼 수 있습니다. 모든 흐름을 보려면 Storage에서 참조할 수 있는 blob_id 필드를 사용합니다. 해당 레코드의 총 흐름 수는 Blob에 있는 개별 흐름과 일치합니다.

아래 쿼리는 지난 30일 동안 비 Azure 공용 IP와 상호 작용한 모든 서브넷을 확인하는 데 도움이 됩니다.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
위에서 언급한 쿼리의 흐름에 대한 Blob 경로를 보려면 아래 쿼리를 사용합니다.

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

위의 쿼리는 Blob에 직접 액세스하기 위한 URL을 생성합니다. 자리 표시자가 있는 URL은 다음과 같습니다.

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>트래픽 분석 스키마에서 사용되는 필드
  > [!IMPORTANT]
  > 트래픽 분석 스키마는 2019년 8월 22일에 업데이트되었습니다. 새 스키마는 원본 및 대상 IP를 별도로 제공하므로 FlowDirection 필드를 구문 분석할 필요가 없어 쿼리가 더욱 간단해집니다. </br>
  > FASchemaVersion_s가 1에서 2로 업데이트되었습니다. </br>
  > 사용되지 않는 필드: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > 새 필드: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > 사용되지 않는 필드는 2019년 11월 22일까지 사용할 수 있습니다.

트래픽 분석은 Log Analytics 위에 빌드되었으므로 트래픽 분석을 통해 데코레이트된 데이터에 대해 사용자 지정 쿼리를 실행하고 동일한 데이터에 경고를 설정할 수 있습니다.

아래에는 스키마의 필드와 해당 필드가 나타내는 내용이 나와 있습니다.

| 필드 | 형식 | 의견 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 트래픽 분석 데이터 테이블
| SubType_s | FlowLog | 흐름 로그의 하위 유형. “FlowLog”만 사용하며, SubType_s의 다른 값은 제품의 내부 작업에 사용됩니다. |
| FASchemaVersion_s |   2   | 스키마 버전. NSG 흐름 로그 버전은 반영되지 않습니다. |
| TimeProcessed_t   | 날짜 및 시간(UTC)  | 트래픽 분석이 스토리지 계정의 원시 흐름 로그를 처리한 시간 |
| FlowIntervalStartTime_t | 날짜 및 시간(UTC) |  흐름 로그 처리 간격의 시작 시간. 흐름 간격이 측정되는 시작 시간입니다. |
| FlowIntervalEndTime_t | 날짜 및 시간(UTC) | 흐름 로그 처리 간격의 종료 시간 |
| FlowStartTime_t | 날짜 및 시간(UTC) |  “FlowIntervalStartTime_t”와 “FlowIntervalEndTime_t” 사이의 흐름 로그 처리 간격에서 흐름(집계됨)이 처음 발생한 시간. 이 흐름은 집계 논리에 따라 집계됩니다. |
| FlowEndTime_t | 날짜 및 시간(UTC) | “FlowIntervalStartTime_t”와 “FlowIntervalEndTime_t” 사이의 흐름 로그 처리 간격에서 흐름(집계됨)이 마지막으로 발생한 시간. 흐름 로그 v2에서는 이 필드에 동일한 4튜플을 가진 마지막 흐름이 시작된 시간이 포함됩니다(원시 흐름 레코드에서 “B”로 표시됨). |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * 알 수 없는 프라이빗 <br> * 알 수 없음 | 정의는 표 아래의 참고에 나와 있습니다. |
| SrcIP_s | 원본 IP 주소 | AzurePublic 및 ExternalPublic 흐름의 경우 비어 있습니다. |
| DestIP_s | 대상 IP 주소 | AzurePublic 및 ExternalPublic 흐름의 경우 비어 있습니다. |
| VMIP_s | VM의 IP | AzurePublic 및 ExternalPublic 흐름에 사용됩니다. |
| PublicIP_s | 공용 IP 주소 | AzurePublic 및 ExternalPublic 흐름에 사용됩니다. |
| DestPort_d | 대상 포트 | 트래픽이 들어오는 포트 |
| L4Protocol_s  | * T <br> * U  | 전송 프로토콜. T = TCP <br> U = UDP |
| L7Protocol_s  | 프로토콜 이름 | 대상 포트에서 파생됩니다. |
| FlowDirection_s | * I = 인바운드<br> * O = 아웃바운드 | 흐름 로그에 따른 NSG의 흐름 방향(인/아웃) |
| FlowStatus_s  | * A = NSG 규칙에서 허용됨 <br> * D = NSG 규칙에서 거부됨  | 흐름 로그에 따라 NSG에서 허용/차단된 흐름의 상태 |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | 흐름과 연결된 NSG(네트워크 보안 그룹) |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  이 흐름을 허용하거나 거부한 NSG 규칙 |
| NSGRule_s | NSG_RULENAME |  이 흐름을 허용하거나 거부한 NSG 규칙 |
| NSGRuleType_s | * 사용자 정의 * 기본값 |   흐름에서 사용하는 NSG 규칙의 유형 |
| MACAddress_s | MAC 주소 | 흐름이 캡처된 NIC의 MAC 주소 |
| Subscription_s | 이 필드에는 Azure 가상 네트워크/네트워크 인터페이스/가상 머신의 구독이 채워집니다. | FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow, UnknownPrivate 흐름 유형(한쪽만 Azure인 흐름 유형)인 경우에만 적용할 수 있습니다 |
| Subscription1_s | 구독 ID | 흐름의 원본 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 머신의 구독 ID |
| Subscription2_s | 구독 ID | 흐름의 대상 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 머신의 구독 ID |
| Region_s | 흐름의 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 머신의 Azure 지역 | FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow, UnknownPrivate 흐름 유형(한쪽만 Azure인 흐름 유형)인 경우에만 적용할 수 있습니다 |
| Region1_s | Azure 지역 | 흐름의 원본 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 머신의 Azure 지역 |
| Region2_s | Azure 지역 | 흐름의 대상 IP가 속한 가상 네트워크의 Azure 지역 |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  트래픽을 전송하거나 수신하는 VM과 연결된 NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 흐름의 원본 IP와 연결된 NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 흐름의 대상 IP와 연결된 NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | 네트워크 인터페이스 NIC_s와 연결된 가상 머신 |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | 흐름의 원본 IP와 연결된 가상 머신 |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | 흐름의 대상 IP와 연결된 가상 머신 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | NIC_s와 연결된 서브넷 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 흐름의 원본 IP와 연결된 서브넷 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | 흐름의 대상 IP와 연결된 서브넷 |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 흐름의 원본 IP와 연결된 애플리케이션 게이트웨이 |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 흐름의 대상 IP와 연결된 애플리케이션 게이트웨이 |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 흐름의 원본 IP와 연결된 부하 분산 장치 |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 흐름의 대상 IP와 연결된 부하 분산 장치 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 흐름의 원본 IP와 연결된 로컬 네트워크 게이트웨이 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 흐름의 대상 IP와 연결된 로컬 네트워크 게이트웨이 |
| ConnectionType_s | 가능한 값: VNetPeering, VpnGateway, ExpressRoute |    연결 유형 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 연결 이름. flowtype P2S의 경우 <gateway name>_<VPN Client IP>로 형식이 지정됩니다 |
| ConnectingVNets_s | 공백으로 구분된 가상 네트워크 이름 목록 | 허브 및 스포크 토폴로지의 경우 허브 가상 네트워크가 여기에 채워집니다. |
| Country_s | 두 자로 된 국가 코드(ISO 3166-1 alpha-2) | 흐름 유형이 ExternalPublic인 경우에 채워집니다. PublicIPs_s 필드의 모든 IP 주소가 동일한 국가 코드를 공유합니다. |
| AzureRegion_s | Azure 지역 위치 | 흐름 유형이 AzurePublic인 경우에 채워집니다. PublicIPs_s 필드의 모든 IP 주소가 Azure 지역을 공유합니다. |
| AllowedInFlows_d | | 허용된 인바운드 흐름 수. 흐름이 캡처된 네트워크 인터페이스로 인바운드되는, 동일한 4튜플을 공유한 흐름 수를 나타냅니다. |
| DeniedInFlows_d |  | 거부된 인바운드 흐름 수 (흐름이 캡처된 네트워크 인터페이스로 인바운드됨) |
| AllowedOutFlows_d | | 허용된 아웃바운드 흐름 수(흐름이 캡처된 네트워크 인터페이스로 아웃바운드됨) |
| DeniedOutFlows_d  | | 거부된 아웃바운드 흐름 수(흐름이 캡처된 네트워크 인터페이스로 아웃바운드됨) |
| FlowCount_d | 더 이상 사용되지 않습니다. 동일한 4튜플과 일치하는 총 흐름입니다. 흐름 유형이 ExternalPublic 및 AzurePublic인 경우 다양한 PublicIP 주소의 흐름도 개수에 포함됩니다.
| InboundPackets_d | 대상에서 흐름의 원본으로 전송된 패킷을 나타냅니다 | NSG 흐름 로그 스키마 버전 2에서만 채워집니다. |
| OutboundPackets_d  | 원본에서 흐름 대상으로 전송된 패킷을 나타냅니다 | NSG 흐름 로그 스키마 버전 2에서만 채워집니다. |
| InboundBytes_d |  대상에서 흐름의 원본으로 전송된 바이트를 나타냅니다 | NSG 흐름 로그 스키마 버전 2에서만 채워집니다. |
| OutboundBytes_d |원본에서 흐름 대상으로 전송된 바이트를 나타냅니다 | NSG 흐름 로그 스키마 버전 2에서만 채워집니다. |
| CompletedFlows_d  |  | NSG 흐름 로그 스키마 버전 2에서만 0이 아닌 값으로 채워집니다. |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 각 항목은 막대로 구분됩니다. |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 각 항목은 막대로 구분됩니다. |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 각 항목은 막대로 구분됩니다. |

### <a name="public-ip-details-schema"></a>공용 IP 세부 정보 스키마

트래픽 분석 고객 환경의 모든 공용 IP에 대한 WHOIS 데이터 및 지리적 위치를 제공합니다. 악성 IP의 경우, Microsoft 보안 인텔리전스 솔루션에서 식별한 DNS 도메인, 위협 유형, 스레드 설명을 제공합니다. IP 세부 정보는 사용자 지정 쿼리를 생성하고 경고를 표시할 수 있도록 Log Analytics 작업 영역에 게시됩니다. 트래픽 분석 대시보드에서 미리 채워진 쿼리에 액세스할 수도 있습니다.

다음은 공용 IP 세부 정보를 위한 스키마입니다.

| 필드 | 형식 | 의견 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalyticsIPDetails_CL | 트래픽 분석 IP 세부 정보 데이터가 포함된 테이블 |
| SubType_s | FlowLog | 흐름 로그의 하위 유형. **“FlowLog”만 사용하며**, SubType_s의 다른 값은 제품의 내부 작업에 사용됩니다 |
| FASchemaVersion_s | 2 | 스키마 버전. NSG 흐름 로그 버전은 반영되지 않습니다 |
| FlowIntervalStartTime_t | 날짜 및 시간(UTC) | 흐름 로그 처리 간격의 시작 시간. 흐름 간격이 측정되는 시작 시간입니다. |
| FlowIntervalEndTime_t | 날짜 및 시간(UTC) | 흐름 로그 처리 간격의 종료 시간 |
| FlowType_s | * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow | 정의는 표 아래의 참고에 나와 있습니다. |
| IP | 공용 IP | 레코드에 정보가 제공되는 공용 IP |
| 위치 | IP의 위치 | - Azure 공용 IP의 경우: IP가 속한 가상 네트워크/네트워크 인터페이스/가상 머신의 Azure 지역 <br> - 외부 공용 IP 및 악성 IP의 경우: IP가 있는 2자 국가 번호(ISO 3166-1 alpha-2) |
| PublicIPDetails | IP에 대한 정보 | - AzurePublic IP의 경우: IP 뒤에 있는 Azure 서비스 <br> - ExternalPublic/악성 IP: IP의 WhoIS 정보 |
| ThreatType | 악성 IP로 인한 위협 | **악성 IP만 해당**: 현재 허용되는 값 목록의 위협 중 하나(아래에 설명되어 있음) |
| ThreatDescription | 위협에 대한 설명 | **악성 IP에만 해당**: 악성 IP에서 제기되는 위협에 대한 설명 |
| DNSDomain | DNS 도메인 | **악성 IP에만 해당**: IP와 연결된 도메인 이름 |

위협 유형 목록:

| 값 | 설명 |
|:---   |:---    |
| 봇넷 | 표시기는 봇넷 노드/멤버를 자세히 설명합니다. |
| C2 | 표시기는 봇넷의 명령 및 제어 노드를 자세히 설명합니다. |
| CryptoMining | 이 네트워크 주소/URL과 관련된 트래픽은 CyrptoMining/리소스 남용을 나타냅니다. |
| DarkNet | 표시기는 Darknet 노드/네트워크의 표시기입니다. |
| DDos | 활성 또는 예정된 DDoS 캠페인과 관련된 지표입니다. |
| MaliciousUrl | 맬웨어를 처리하는 URL입니다. |
| 맬웨어 | 악성 파일을 설명하는 표시기입니다. |
| 피싱 | 피싱 캠페인과 관련된 표시기입니다. |
| Proxy (프록시) | 프록시 서비스인 표시기입니다. |
| PUA | 사용자 동의 없이 설치된 애플리케이션. |
| WatchList | 이는 위협이 무엇인지 정확하게 확인할 수 없거나 수동 해석이 필요한 경우 표시기가 배치되는 일반적인 버킷에 해당됩니다. 이는 일반적으로 데이터를 시스템으로 전송하는 파트너가 사용하지 않아야 합니다. |


### <a name="notes"></a>메모

1. AzurePublic과 ExternalPublic 흐름의 경우 VMIP_s 필드에는 고객이 소유한 Azure VM IP가 채워지고, PublicIPs_s 필드에는 공용 IP 주소가 채워집니다. 두 흐름 유형의 경우 SrcIP_s와 DestIP_s 필드 대신 VMIP_s와 PublicIPs_s 필드를 사용해야 합니다. AzurePublic과 ExternalPublicIP 주소의 경우 고객 로그 분석 작업 영역에 수집되는 레코드 수가 최소화되도록 추가로 집계합니다. 이 필드는 곧 사용되지 않으므로, Azure VM이 흐름의 원본인지 또는 대상인지에 따라 SrcIP_s 및 DestIP_s를 사용해야 합니다
1. 흐름 유형에 대한 세부 정보: 흐름과 관련된 IP 주소를 기준으로 흐름은 다음과 같은 흐름 유형으로 분류됩니다.
1. IntraVNet – 흐름의 IP 주소가 둘 다 동일한 Azure Virtual Network에 상주합니다.
1. InterVNet - 흐름의 IP 주소가 서로 다른 두 개의 Azure Virtual Network에 상주합니다.
1. S2S – (사이트 간) IP 주소 중 하나는 Azure Virtual Network에 속하지만 다른 IP 주소는 VPN Gateway 또는 ExpressRoute를 통해 Azure Virtual Network에 연결된 고객 네트워크(사이트)에 속합니다.
1. P2S - (지점 및 사이트 간) IP 주소 중 하나는 Azure Virtual Network에 속하지만 다른 IP 주소는 VPN Gateway를 통해 Azure Virtual Network에 연결된 고객 네트워크(사이트)에 속합니다.
1. AzurePublic - IP 주소 중 하나는 Azure Virtual Network에 속하지만 다른 IP 주소는 Microsoft가 소유한 Azure 내부 공용 IP 주소에 속합니다. 고객 소유 공용 IP 주소는 이 흐름 유형의 일부가 아닙니다. 예를 들어 Azure 서비스(스토리지 엔드포인트)로 트래픽을 전송하는 고객 소유 VM은 이 흐름 유형으로 분류됩니다.
1. ExternalPublic - IP 주소 중 하나는 Azure Virtual Network에 속하지만 다른 IP 주소는 Azure에 없고, “FlowIntervalStartTime_t”와 “FlowIntervalEndTime_t” 사이의 처리 간격에 트래픽 분석이 사용하는 ASC 피드에서 악성으로 보고되지 않은 공용 IP입니다.
1. MaliciousFlow - IP 주소 중 하나는 Azure Virtual Network에 속하지만 다른 IP 주소는 Azure에 없고, “FlowIntervalStartTime_t”와 “FlowIntervalEndTime_t” 사이의 처리 간격에 트래픽 분석이 사용하는 ASC 피드에서 악성으로 보고된 공용 IP입니다.
1. UnknownPrivate - IP 주소 중 하나는 Azure Virtual Network에 속하지만 다른 IP 주소는 RFC 1918에 정의된 대로 개인 IP 범위에 속하며 트래픽 분석을 통해 고객 소유 사이트 또는 Azure Virtual Network에 매핑할 수 없습니다.
1. 알 수 없음 – 흐름의 IP 주소 중 하나를 Azure의 고객 토폴로지 및 온-프레미스(사이트)와 매핑할 수 없습니다.
1. 일부 필드 이름의 앞에 \_s 또는 \_d가 추가됩니다. 두 문자는 원본과 대상을 나타내는 것이 아니라 각각 문자열과 10진수 데이터 형식을 나타냅니다.

### <a name="next-steps"></a>다음 단계
자주 묻는 질문과 대답을 확인하려면 [트래픽 분석 FAQ](traffic-analytics-faq.yml)를 참조하세요. 기능에 대한 자세한 내용은 [트래픽 분석 설명서](traffic-analytics.md)를 참조하세요.
