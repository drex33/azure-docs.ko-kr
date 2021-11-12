---
title: Microsoft Defender for IoT에서 지원하는 프로토콜
description: Microsoft Defender for IoT에서 지원하는 프로토콜에 대해 알아봅니다.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: ebfd983baf2e925fda66c5fc23edc380420db591
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305963"
---
# <a name="support-for-iot-ot-ics-and-scada-protocols"></a>IoT, OT, ICS 및 SCADA 프로토콜 지원

Microsoft Defender for IoT는 개방형 상호 운용 가능한 OT(Operation Technology) 사이버 보안 플랫폼을 제공합니다. Defender for IoT는 다양한 위치에 배포되며 모든 산업 수직 및 지역에서 복잡하고 까다로운 OT 환경에서 배포를 통해 IoT, IT 및 ICS 위험을 줄입니다.

## <a name="supported-protocols"></a>지원되는 프로토콜

Microsoft Defender for IoT는 다양한 기업에서 광범위한 프로토콜을 지원하며, 모든 산업 부문, 엔터프라이즈 네트워크 및 BMS(건물 관리 시스템) 환경에서 산업 자동화 장비를 포함합니다. 사용자 지정 또는 독점 프로토콜의 경우 Microsoft는 사용자 지정 프로토콜 분석기를 플러그 인으로 쉽게 개발, 테스트 및 배포할 수 있는 SDK를 제공합니다. SDK는 프로토콜을 디자인하는 방법과 같은 독점 정보를 공개하거나 중요한 정보를 포함할 수 있는 PCAP를 공유하지 않고 이 모든 작업을 수행합니다. 지원되는 프로토콜은 다음과 같습니다.

### <a name="supported-protocols-passive-monitoring"></a>지원되는 프로토콜(수동 모니터링)

이 섹션에서는 수동 모니터링을 사용하여 검색되는 프로토콜을 나열합니다.

**ABB:** IEC61850 MMS(ABB 확장 포함)

**ASHRAE:** BACnet, BACnet BACapp, BACnet BVLC

**2018년 3월 1** AMS(ADS), Twincat 

**Cisco:** CAPWAP 컨트롤, CAPWAP 데이터, CDP, LWAPP

**DNP. org:**  DNP3

**Emerson:** DeltaV, Emerson OpenBSI/BSAP,인터 DCS ADMD, 히버 DCS DPUSTAT, 히로 DCS SSRPC

**Emerson Fischer:**  ROC

**Eurocontrol:** 아스테릭스

**GE:**  표시자(System 1), EGD, GSM(GE MarkVI 및 MarkVIe), SRTP(GE)

**Honeywell:** ENAP, Experion DCS CDA, Experion DCS재배포

**IEC:** Codesys V3,재단 TASE.2/IEC-60870, IEC60870-5(IEC104/101), IEC60870-5-103(캡슐화된 직렬), IEC61850 GOOSE, IEC61850 MMS, IEC61850 SMV(SAMPLED-VALUES), LonTalk(LonWorks)

**IEEE**: LLC, STP, VLAN

**IETF:** ARP, DCE RPC, DNS, FTP(FTP_ADAT, FTP_DATA), GSSAPI(RFC2743), HTTP, ICMP, IPv4, IPv6, LLDP, MDNS, RFS, NTLM(NTLMSSP 인증 프로토콜), RPC, SMB/찾아보기/NBDGM, SMB/CIFS, SNMP, SPNEGO(RFC4178), SSH, Syslog, Telnet, TFTP, TPKT, UDP

**ISO:** CLNP(ISO 8473), COTP(ISO 8073), ISO 산업 프로토콜, MQTT(IEC 20922)

**의료:** ASTM, HL7

**Microsoft:** Horizon 커뮤니티 분석자, Horizon 전용 분석기(고객이 개발함). 자세한 내용은 [Horizon 전용 프로토콜 배포자](references-horizon-sdk.md) 를 참조하세요.

**Mitsubishi:** Melsoft /Melsec(Mitsubishi Electric)

**Omron:** 지 느 러 미

**Oracle:** TDS, TNS

**Rockwell Automation:** ENIP, EtherNet/IP CIP(Rockwell 확장 포함), 이더넷/IP CIP FW 버전 27 이상

**2018년 10월** Modbus/TCP, Modbus TCP–Unity Extensions, OASYS(일반전기 텔반) 

**2018년 10월 20일, 2016년 10월** 20일 Foxstation Evo, Foxstation I/A, Trident, TriGP, TriStation

**히터 일렉트릭/한정자:** Modbus RTU

**2016년 3월 20일, 2016년** Wonderware Suitelink

**Siemens:** CAMP, PCS7, PCS7 WinCC – 한, Profinet DCP, Profinet Realtime, Siemens PHD, Siemens S7, Siemens S7-Plus, Siemens S7-Plus, Siemens SICAM, Siemens WinCC

**Toshiba:** Toshiba 컴퓨터 링크

**Yokogawa:** Centum ODEQ(Centum/ ProSafe DCS), HIS Equalize, Vnet/IP

### <a name="supported-protocols-active-monitoring"></a>지원되는 프로토콜(활성 모니터링)

이 섹션에서는 활성 검색을 사용하여 검색된 프로토콜(예: ping sweep 및 쿼리)을 나열합니다.

**IETF:** Ping Sweep, SNMP 네트워크 레이아웃 쿼리, SNMP 쿼리

**Microsoft:** Windows WMI 쿼리(req. WMI/WinRM): 하드웨어, BIOS, 버전, 소프트웨어, 패치  

**Rockwell Automation:**  ENIP 쿼리, ENIP 검색, 이더넷/IP CIP(CIP 쿼리)

**Siemens:** Siemens S7

## <a name="quickly-add-support-for-proprietary-restricted-protocols"></a>독점적인 제한된 프로토콜에 대한 지원을 빠르게 추가

디지털화로 인해 수십억 개의 IoT 디바이스가 배포되고 IT 네트워크와 OT 네트워크 간의 연결이 크게 증가합니다. 즉, 산업 제어 시스템에 대한 위험한 사이버 공격의 위험을 포함하여 공격 표면이 증가하고 있습니다.

공격 표면을 줄이기 위해 Horizon Protocol SDK를 사용하면 IoT 및 ICS 환경에서 사용되는 모든 프로토콜을 빠르고 안전하게 지원할 수 있습니다.

Horizon은 고객 및 기술 파트너를 위해 다음과 같은 솔루션을 제공합니다.

- 공용, 독점, 사용자 지정 프로토콜 또는 모든 표준에서 벗어난 프로토콜에 대한 무제한적이고 완벽한 지원

- 새로운 차원의 DPI 개발 유연성 및 범위

- Defender for IoT 플랫폼 버전으로 업그레이드할 필요 없이 OT 표시 여부 및 제어 능력을 대폭 확장하는 도구

- 중요한 정보 유출 없이 독점 개발할 수 있도록 지원하는 보안 기능

Horizon SDK를 사용하면 트래픽에서 DPI(심층 패킷 검사)를 사용하도록 설정하고 실시간으로 위협을 검색하는 플러그 인을 작성할 수 있습니다. Horizon SDK를 사용하면 추가 사용자 지정도 가능합니다. 예를 들어 Horizon SDK를 사용하면 자산 공급업체, 파트너 또는 플랫폼 소유자가 경고, 이벤트 및 프로토콜 매개 변수에 대한 텍스트를 지역화하고 사용자 지정할 수 있습니다.

[![Horizon SDK를 사용하면 IoT 및 ICS 환경에서 사용되는 프로토콜의 100%를 빠르게 지원할 수 있습니다.](media/concept-supported-protocols/sdk-horizon.png)](media/concept-supported-protocols/sdk-horizon-expanded.png#lightbox)

## <a name="collaborate-with-the-horizon-community"></a>Horizon 커뮤니티와 공동 작업

프로토콜 지원을 위한 디지털 변환 및 업계 전반의 협업을 선도하는 커뮤니티에 참여합니다. Horizon ICS 커뮤니티는 중요한 인프라, 건물 관리, 생산 라인, 운송 시스템 및 기타 산업 리더의 도메인 전문가를 위한 지식 공유를 허용합니다.

커뮤니티는 자습서, 토론 포럼, 강사가 진행하는 교육, 교육 백서, 웨비나 등을 제공합니다.

커뮤니티에 참여하도록 초대합니다. <horizon-community@microsoft.com>

## <a name="next-steps"></a>다음 단계

Horizon 전용 [프로토콜 배포자](references-horizon-sdk.md)에 대해 자세히 알아봅니다.

[Horizon API를](references-horizon-api.md)확인하세요.
