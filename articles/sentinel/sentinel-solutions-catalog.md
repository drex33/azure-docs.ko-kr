---
title: Microsoft Sentinel 콘텐츠 허브 카탈로그 | Microsoft Docs
description: 이 문서에서는 현재 사용 가능한 Microsoft Sentinel 콘텐츠 허브 패키지를 표시하고 자세히 설명합니다.
author: batamig
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: bdd1a6a1796d9ecba9b6ca50a44ed8bc1eb748cc
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132934633"
---
# <a name="microsoft-sentinel-content-hub-catalog"></a>Microsoft Sentinel 콘텐츠 허브 카탈로그

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Microsoft Sentinel 콘텐츠 허브 환경은 모든 개별 솔루션 패키지와 마찬가지로 현재 **미리 보기로** 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Microsoft Sentinel 솔루션은](sentinel-solutions.md) 단일 배포 단계를 통해 작업 영역에서 데이터 커넥터, 통합 문서, 분석 및 자동화와 같은 Microsoft Sentinel 콘텐츠를 획득하는 통합 방법을 제공합니다.

이 문서에서는 작업 영역에 배포할 수 있는 기본 제공(기본 제공), 주문형 Microsoft Sentinel 데이터 커넥터 및 솔루션을 나열합니다. 솔루션을 배포하면 데이터 커넥터, 플레이북, 통합 문서 또는 규칙과 같은 포함된 보안 콘텐츠가 Microsoft Sentinel의 관련 영역에 포함됩니다. 

자세한 내용은 중앙에서 Microsoft Sentinel 콘텐츠 및 솔루션 검색 [및 배포를 참조하세요.](sentinel-solutions-deploy.md)

## <a name="domain-solutions"></a>도메인 솔루션

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Microsoft Insider Risk Management** |[데이터 커넥터,](data-connectors-reference.md#microsoft-365-insider-risk-management-irm-preview)통합 문서, 분석 규칙, 헌팅 쿼리 |보안 - 참가자 위협 | Microsoft|
| **클라우드용 Microsoft MITRE ATT&CK 솔루션**| 통합 문서, 분석 규칙, 헌팅 쿼리|보안 - 위협 방지, 보안 - 기타 |Microsoft |
| **Microsoft Sentinel Deception** | [통합 문서, 분석 규칙, 감시 목록](monitor-key-vault-honeytokens.md)  | 보안 - 위협 방지  |Microsoft |
| 제로 트러스트(TIC3.0) |[통합 문서](https://techcommunity.microsoft.com/t5/public-sector-blog/announcing-the-azure-sentinel-zero-trust-tic3-0-workbook/ba-p/2313761) |ID, 보안 - 기타 |Microsoft  |
| | | | |

## <a name="arista-networks"></a>Arista Networks

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Arista** Networks(깨기 보안) |데이터 커넥터, 통합 문서, 분석 규칙 | 보안 - 네트워크 |[Arista - 깨기 보안](https://awakesecurity.com/) |
| | | | |


## <a name="armorblox"></a>Armorblox

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Armorblox - Sentinel** |데이터 커넥터 | 보안 - 위협 방지 |[Armorblox](https://www.armorblox.com/contact/) |
| | | | |




## <a name="azure"></a>Azure

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Sentinel용 Azure Firewall 솔루션**| [데이터 커넥터,](data-connectors-reference.md#azure-firewall)통합 문서, 분석 규칙, 플레이북, 헌팅 쿼리, 사용자 지정 논리 앱 커넥터 |보안 - 네트워크 보안, 네트워킹 | 커뮤니티|
|**SQL PaaS용 Microsoft Sentinel**     |  [데이터 커넥터,](data-connectors-reference.md#azure-sql-databases)통합 문서, 분석 규칙, 플레이북, 헌팅 쿼리     | 애플리케이션        |      커뮤니티   |
|**Microsoft Sentinel 교육 랩** |통합 문서, 분석 규칙, 플레이북, 헌팅 쿼리 | 학습 및 자습서 |Microsoft |
|**Azure SQL** | [데이터 커넥터,](data-connectors-reference.md#azure-sql-databases)통합 문서, 분석, 플레이북, 헌팅 쿼리  | 애플리케이션 |Microsoft  |
| | | | |


## <a name="box"></a>Box

|이름   |Includes  |범주 |지원 요소  |
|------------------|---------|---------|---------|
|**Box 솔루션**| 데이터 커넥터, 통합 문서, 분석 규칙, 헌팅 쿼리, 파서 |  Storage, 애플리케이션  | Microsoft|
| | | | |


## <a name="check-point"></a>Check Point

|이름   |Includes  |범주 |지원 요소  |
|------------------|---------|---------|---------|
|**microsoft Sentinel 솔루션 Check Point**   |[데이터 커넥터,](data-connectors-reference.md#check-point)플레이북, 사용자 지정 논리 앱 커넥터  | 보안 - AUTOMATION(SOAR) | [검사점](https://www.checkpoint.com/support-services/contact-support/)|
| | | | |


## <a name="cisco"></a>시스코

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Cisco ACI** |데이터 커넥터, 파서 |보안 – 네트워크 |Microsoft |
|**Cisco ASA** |[데이터 커넥터,](data-connectors-reference.md#cisco-asa)플레이북, 사용자 지정 논리 앱 커넥터 |보안 – AUTOMATION(SOAR) |Microsoft |
|**Cisco Duo 보안** |데이터 커넥터, 파서 | ID|Microsoft |
|**Cisco ISE**  |데이터 커넥터, 통합 문서, 분석 규칙, 플레이 북, 구하기 쿼리, 파서, 사용자 지정 논리 앱 커넥터 |네트워킹, 보안-기타 | Microsoft |
|**Cisco Meraki** |[데이터 커넥터](data-connectors-reference.md#cisco-meraki-preview), 플레이 북, 사용자 지정 논리 앱 커넥터 |보안-네트워크 |Microsoft |
|**Cisco Secure Email Gateway/ESA** |데이터 커넥터, 파서 |보안 위협 방지 |Microsoft |
|**Cisco StealthWatch** |데이터 커넥터, 파서 |보안-네트워크 | Microsoft|
|**Cisco Umbrella** |[데이터 커넥터](data-connectors-reference.md#cisco-umbrella-preview), 통합 문서, 분석 규칙, 플레이 북, 구하기 쿼리, 파서, 사용자 지정 논리 앱 커넥터 |보안-클라우드 보안 |Microsoft |
|**Cisco WSA(Web Security Appliance)** | 데이터 커넥터, 파서|보안-네트워크 |Microsoft |
| | | | |


## <a name="cloudflare"></a>Cloudflare


|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Cloudflare 솔루션**|데이터 커넥터, 통합 문서, 분석 규칙, 구하기 쿼리, 파서| 보안-네트워크, 네트워킹 |Microsoft |
| | | | |


## <a name="contrast-security"></a>대비 보안



|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Contrast Microsoft 센티널 솔루션 보호**|데이터 커넥터, 통합 문서, 분석 규칙 |보안 위협 방지 |Microsoft  |
| | | | |

## <a name="crowdstrike"></a>Crowdstrike


|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**CrowdStrike Falcon Endpoint Protection 솔루션**| 데이터 커넥터, 통합 문서, 분석 규칙, 플레이 북, 파서| 보안 위협 방지| Microsoft|
| | | | |

## <a name="digital-guardian"></a>디지털 보호자


|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**디지털 보호자** |데이터 커넥터, 파서 |보안-Information Protection |Microsoft |
| | | |

## <a name="falconforce"></a>False

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**비-금 금요일 콘텐츠-없음** |Analytics 규칙 |사용자 동작 (UEBA), 보안-참가자 위협 | [False](https://www.falconforce.nl/en/)|
| | | |

## <a name="fireeye-nx-network-security"></a>FireEye NX (네트워크 보안)

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**FireEye NX (네트워크 보안)** |데이터 커넥터, 파서 |보안-네트워크| Microsoft|
| | | |

## <a name="flare-systems-firework"></a>플레어 시스템 불꽃놀이

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**플레어 시스템 불꽃놀이** |데이터 커넥터 |보안 위협 방지 |Microsoft|
| | | |

## <a name="forescout"></a>Forescout

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Forescout** |데이터 커넥터, 파서 |보안-네트워크 | Microsoft|
| | | |

## <a name="fortinet-fortigate"></a>Fortinet Fortigate

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Fortinet Fortigate** |[데이터 커넥터,](data-connectors-reference.md#fortinet)플레이북, 사용자 지정 논리 앱 커넥터|보안 - AUTOMATION(SOAR) | Microsoft|
| | | |


## <a name="google"></a>Google

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**DNS 솔루션 Google Cloud Platform** |데이터 커넥터, 파서 |클라우드 공급자, 네트워킹 |Microsoft |
|**Google Cloud Platform Cloud Monitoring 솔루션**|데이터 커넥터, 파서 |클라우드 공급자 | Microsoft|
|**Google Cloud Platform ID 및 액세스 관리 솔루션**|데이터 커넥터, 통합 문서, 분석 규칙, 플레이북, 헌팅 쿼리, 파서, 사용자 지정 논리 앱 커넥터|클라우드 공급자, ID |Microsoft |
| | | | |


## <a name="hyas"></a>HYAS

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Microsoft Sentinel용 HYAS Insight 솔루션 갤러리**| 플레이북| 보안 - 위협 인텔리전스, 보안 - 자동화(SOAR) |Microsoft |
| | | | |

## <a name="imperva"></a>Imperva

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Imperva Cloud WAF(공식적으로** Imperva Incapsula)| [데이터 커넥터](data-connectors-reference.md#imperva-waf-gateway-preview), 파서| 보안 - 네트워크 | Microsoft|
| | | | |

## <a name="infoblox"></a>InfoBlox

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**InfoBlox Threat Defense/InfoBlox Cloud Data Connector**| [데이터 커넥터,](data-connectors-reference.md#infoblox-network-identity-operating-system-nios-preview)통합 문서, 분석 규칙| 보안 - 위협 방지 | Microsoft|
| | | | |


## <a name="ironnet"></a>IronNet

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**IronNet CyberSecurity Iron Defense - Microsoft Sentinel** | |보안 - 네트워크 |Microsoft |
| | | |



## <a name="juniper"></a>Juniper

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Juniper IDP** |데이터 커넥터, 파서|보안 - 네트워크 |Microsoft |
| | | | |


## <a name="kaspersky"></a>Kaspersky

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**카퍼 스키 바이러스 백신** |데이터 커넥터, 파서   | 보안 - 위협 방지|Microsoft |
| | | | |


## <a name="lookout"></a>Lookout

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Microsoft Sentinel용 Lookout Mobile Threat Defense**| [데이터 커넥터](data-connectors-reference.md#lookout-mobile-threat-defense-preview)|보안 - 네트워크 |[Lookout](https://www.lookout.com/support) |
| | | |

## <a name="mcafee"></a>McAfee

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**McAfee ePolicy Orchestrator 솔루션**| 데이터 커넥터, 통합 문서, 분석 규칙, 플레이북, 헌팅 쿼리, 파서, 사용자 지정 논리 앱 커넥터| 보안 - 위협 방지| Microsoft |
|**McAfee 네트워크 보안 플랫폼** 솔루션(Intrushield) + 바이러스 백신 정보(T1 - 논리 앱) |데이터 커넥터, 통합 문서, 분석 규칙, 헌팅 쿼리, 파서 |보안 - 위협 방지 | Microsoft|
| | | | |

## <a name="microsoft"></a>Microsoft

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Microsoft Sentinel 4 Microsoft Dynamics 365**     |   [데이터 커넥터,](data-connectors-reference.md#dynamics-365)통합 문서, 분석 규칙 및 헌팅 쿼리 |      애플리케이션   |Microsoft         |
|**microsoft Sentinel for Teams**     | 데이터 커넥터, 분석 규칙, 플레이북, 헌팅 쿼리      |   애플리케이션      |    커뮤니티     |
| | | | |


## <a name="oracle"></a>Oracle


|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Oracle 클라우드 인프라** |데이터 커넥터, 파서 | 클라우드 공급자 | Microsoft|
|**Oracle Database 감사 솔루션** | 데이터 커넥터, 통합 문서, 분석 규칙, 헌팅 쿼리, 파서| 애플리케이션|Microsoft |
| | | | |

## <a name="palo-alto"></a>Palo Alto

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Palo Alto PAN-OS**|[데이터 커넥터,](#palo-alto)플레이북, 사용자 지정 논리 앱 커넥터 |보안 - SOAR(자동화), 보안 - 네트워크 |Microsoft |
|**Palo Alto Prisma 솔루션**|[데이터 커넥터,](#palo-alto)통합 문서, 분석 규칙, 헌팅 쿼리, 파서 |보안 - 클라우드 보안 |Microsoft |
| | | | |

## <a name="ping-identity"></a>Ping Identity

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**PingFederate 솔루션** |데이터 커넥터, 통합 문서, 분석 규칙, 헌팅 쿼리, 파서| ID|Microsoft |
| | | | |

## <a name="proofpoint"></a>Proofpoint

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Proofpoint POD 솔루션** |[데이터 커넥터,](data-connectors-reference.md#proofpoint-on-demand-pod-email-security-preview)통합 문서, 분석 규칙, 헌팅 쿼리, 파서| 보안 - 위협 방지|Microsoft |
|**Proofpoint TAP 솔루션** | 통합 문서, 분석 규칙, 플레이북, 사용자 지정 논리 앱 커넥터|보안 - AUTOMATION(SOAR), 보안 - 위협 방지 |Microsoft |
| | | |

## <a name="qualys"></a>Qualys

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Qualys VM 솔루션** |통합 문서, 분석 규칙 |보안 - 취약성 관리 |Microsoft |
| | | | |

## <a name="rapid7"></a>Rapid7

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Rapid7 InsightVM CloudAPI 솔루션** |데이터 커넥터, 파서|보안 - 취약성 관리 |Microsoft |
| | | | |

## <a name="reversinglabs"></a>ReversingLabs

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**ReversingLabs TitaniumCloud 파일 보강 솔루션**|플레이북 |보안 - 위협 인텔리전스 |[ReversingLabs](https://support.reversinglabs.com/hc/en-us) |
| | | | |

## <a name="riskiq"></a>RiskIQ

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**RiskIQ 보안 인텔리전스 플레이북**|플레이북 |보안 - 위협 인텔리전스, 보안 - 자동화(SOAR) |[RiskIQ](https://www.riskiq.com/integrations/microsoft/) |
| | | | |

## <a name="rsa"></a>RSA

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**RSA SecurID** |데이터 커넥터, 파서 |보안-기타, Id |Microsoft |
| | | |



## <a name="sap"></a>SAP

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**SAP용 Continuous Threat Monitoring**|[데이터 커넥터](sap-deploy-solution.md), [통합 문서, 분석 규칙, watchlists](sap-solution-security-content.md) | 애플리케이션  |커뮤니티 |
| | | | |

## <a name="semperis"></a>Semperis

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Semperis**|데이터 커넥터, 통합 문서, 분석 규칙, 파서 | 보안 위협 방지, Id  |[Semperis](https://www.semperis.com/contact-us/) |
| | | | |

## <a name="senserva-pro"></a>Pro

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Microsoft 센티널의 제안** |데이터 커넥터, 통합 문서, 분석 규칙, 구하기 쿼리 |규정 준수 |[초](https://www.senserva.com/contact/) |
| | | | |


## <a name="sonrai-security"></a>Sonrai 보안

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Sonrai 보안-Microsoft 센티널** |데이터 커넥터, 통합 문서, 분석 규칙   | 규정 준수|Sonrai 보안 |
| | | | |

## <a name="slack"></a>Slack

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Slack Audit 솔루션**|데이터 커넥터, 통합 문서, 분석 규칙, 구하기 쿼리, 파서 |애플리케이션| Microsoft|
| | | | |


## <a name="sophos"></a>Sophos

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Sophos Endpoint Protection 솔루션** |데이터 커넥터, 파서| 보안 위협 방지 |Microsoft |
|**Sophos XG Firewall 솔루션**| 통합 문서, 분석 규칙, 파서 |보안-네트워크 |Microsoft |
| | | | |


## <a name="symantec"></a>Symantec

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Symantec 끝점**|데이터 커넥터, 통합 문서, 분석 규칙, 플레이 북, 구하기 쿼리, 파서| 보안 위협 방지|Microsoft |
|**Symantec ProxySG 솔루션**|통합 문서, 분석 규칙 |보안-네트워크 |Symantec |
| | | | |

## <a name="tenable"></a>Tenable

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Tenable에 대 한 Nessus 스캐너/IO VM 보고서 사용**  | 데이터 커넥터, 파서| 보안 취약성 관리| Microsoft |
| | | | |


## <a name="trend-micro"></a>Trend Micro

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Trend Micro Apex One 솔루션**  | 데이터 커넥터, 구하기 쿼리, 파서| 보안 위협 방지|Microsoft |
| | | | |




## <a name="ubiquiti"></a>Ubiquiti

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Ubiquiti UniFi 솔루션**|데이터 커넥터, 통합 문서, 분석 규칙, 구하기 쿼리, 파서 |보안-네트워크 |Microsoft |
| | | | |


## <a name="varmour"></a>vArmour

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**vArmour 응용 프로그램 컨트롤러 및 Microsoft 센티널 솔루션**|데이터 커넥터, 통합 문서, 분석 규칙 |IT 운영 |[vArmour](https://www.varmour.com/contact-us/) |
| | | | |

## <a name="vectra"></a>Vectra

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Vectra Stream 솔루션** |데이터 커넥터, 구하기 쿼리, 파서 |보안-네트워크 |Microsoft |
| | | |


## <a name="vmware"></a>VMware

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**VMware Carbon Black 솔루션**|통합 문서, 분석 규칙| 보안 위협 방지| Microsoft|
| | | | |

## <a name="zeek-network"></a>Zeek 네트워크

|이름    |Includes  |범주 |지원 요소  |
|---------|---------|---------|---------|
|**Microsoft 센티널 용 Corelight**|데이터 커넥터, 통합 문서, 분석 규칙, 구하기 쿼리, 파서 | IT 운영, 보안-네트워크 | [Zeek 네트워크](https://support.corelight.com/)|
| | | | |


## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널 솔루션과 이러한 솔루션을 찾아서 배포 하는 방법에 대해 알아보았습니다.

- [Microsoft 센티널 솔루션](sentinel-solutions.md)에 대해 자세히 알아보세요.
- [Microsoft 센티널 솔루션을 찾아서 배포](sentinel-solutions-deploy.md)합니다.
