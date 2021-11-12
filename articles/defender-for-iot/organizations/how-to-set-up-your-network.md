---
title: 네트워크 설정
description: Azure Defender for IoT 어플라이언스에 사용할 수 있도록 네트워크를 성공적으로 설정하는 데 필요한 솔루션 아키텍처, 네트워크 준비, 필수 구성 요소 및 기타 정보에 대해 알아봅니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 1e41cc1f8e491e92d2fe4896b90e61b4cd3fe835
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278819"
---
# <a name="about-microsoft-defender-for-iot-network-setup"></a>IoT 용 Microsoft Defender 네트워크 설정 정보

IoT 용 Microsoft Defender는 연속 ICS 위협 모니터링 및 장치 검색을 제공 합니다. 이 플랫폼은 다음 구성 요소를 포함합니다.

**Defender for IoT 센서:** 센서는 패시브(에이전트 없는) 모니터링을 사용하여 ICS 네트워크 트래픽을 수집합니다. 패시브/비침입적인 이 센서는 OT 및 IoT 네트워크와 디바이스 성능에 어떠한 영향도 주지 않습니다. 센서는 SPAN 포트 또는 네트워크 TAP에 연결하고 즉시 네트워크 모니터링을 시작합니다. 검색은 센서 콘솔에 표시됩니다. 여기에서 네트워크 맵, 디바이스 인벤토리, 광범위한 보고서의 검색 항목을 보고, 조사하고, 분석할 수 있습니다. 예를 들면 위험 평가 보고서, 데이터 마이닝 쿼리, 공격 벡터가 있습니다.

**Defender for IoT 온-프레미스 관리 콘솔**: 온-프레미스 관리 콘솔은 모든 네트워크 디바이스의 통합 보기를 제공합니다. 이 콘솔은 시설 전체의 주요 OT 및 IoT 위험 지표와 경고의 실시간 보기를 제공합니다. SOC 워크플로 및 플레이북과 긴밀하게 통합되어 있어서 위협 완화 작업의 우선 순위와 위협의 사이트 간 상관 관계를 쉽게 파악할 수 있습니다.

**Azure Portal의 IoT 용 Defender:** IoT 용 Defender 응용 프로그램은 솔루션 어플라이언스를 구매 하 고, 소프트웨어를 설치 및 업데이트 하 고, TI 패키지를 업데이트 하는 데 도움이 됩니다.

이 문서에서는 Azure Defender for IoT 어플라이언스에 사용할 수 있도록 네트워크를 성공적으로 설정하는 데 도움이 되는 솔루션 아키텍처, 네트워크 준비, 필수 구성 요소 등에 대한 정보를 제공합니다. 이 문서의 정보는 OT 및 IoT 네트워크를 운영하고 관리한 경험이 있는 독자를 대상으로 합니다. 자동화 엔지니어, 공장 관리자, OT 네트워크 인프라 서비스 공급자, 사이버 보안 팀, CISO 또는 CIO를 예로 들 수 있습니다.

도움이나 지원이 필요하면 [Microsoft 지원](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에 문의하세요.

## <a name="on-site-deployment-tasks"></a>온사이트 배포 작업

사이트 배포 작업에는 다음이 포함됩니다.

- [사이트 정보 수집](#collect-site-information)

- [구성 워크스테이션 준비](#prepare-a-configuration-workstation)

- [인증서 설정](#set-up-certificates)

- [구성 워크스테이션 준비](#prepare-a-configuration-workstation)

- [랙 설치 계획](#plan-rack-installation)

### <a name="collect-site-information"></a>사이트 정보 수집

다음과 같은 사이트 정보를 기록합니다.

- 센서 관리 네트워크 정보.

- 사이트 네트워크 아키텍처.

- 물리적 환경.

- 시스템 통합.

- 계획된 사용자 자격 증명.

- 구성 워크스테이션.

- SSL 인증서(선택 사항이지만 권장됨).

- SMTP 인증(선택 사항). 인증에 SMTP 서버를 사용하려면 서버에 필요한 자격 증명을 준비합니다.

- DNS 서버(선택 사항). DNS 서버의 IP 및 호스트 이름을 준비합니다.

중요한 사이트 정보의 자세한 목록과 설명은 [배포 검사 목록](#predeployment-checklist)을 참조하세요.

#### <a name="successful-monitoring-guidelines"></a>성공적인 모니터링 지침

각 프로덕션 네트워크에서 어플라이언스를 연결할 최적의 위치를 찾으려면 다음 절차를 따르는 것이 좋습니다.

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="프로덕션 네트워크 설정의 다이어그램 예제.":::

### <a name="prepare-a-configuration-workstation"></a>구성 워크스테이션 준비

다음을 포함하여 Windows 워크스테이션을 준비합니다.

- 센서 관리 인터페이스와 연결

- 지원되는 브라우저

- PuTTY와 같은 터미널 소프트웨어

필요한 방화벽 규칙이 워크스테이션에서 열려 있는지 확인합니다. 자세한 내용은 [네트워크 액세스 요구 사항](#network-access-requirements)을 참조하세요.

#### <a name="supported-browsers"></a>지원되는 브라우저

센서 및 온-프레미스 관리 콘솔 웹 애플리케이션을 지원하는 브라우저는 다음과 같습니다.

- Microsoft Edge(최신 버전)

- Safari(최신 버전, Mac만 해당)

- Chrome(최신 버전)

- Firefox(최신 버전)

지원 되는 브라우저에 대 한 자세한 내용은 [권장 브라우저](../../azure-portal/azure-portal-supported-browsers-devices.md#recommended-browsers)를 참조 하세요.

### <a name="set-up-certificates"></a>인증서 설정

센서 및 온-프레미스 관리 콘솔 설치 다음에 로컬 자체 서명 된 인증서가 생성 되어 센서 웹 응용 프로그램에 액세스 하는 데 사용 됩니다. IoT 용 Defender에 처음으로 로그인 할 때 관리자 사용자에 게 SSL/TLS 인증서를 제공 하 라는 메시지가 표시 됩니다. 또한이 인증서와 기타 시스템 인증서의 유효성을 검사 하는 옵션도 자동으로 설정 됩니다. 자세한 내용은 [인증서 정보](how-to-deploy-certificates.md) 를 참조 하세요.

### <a name="network-access-requirements"></a>네트워크 액세스 요구 사항

조직 보안 정책에서 다음에 대한 액세스를 허용하는지 확인합니다.

#### <a name="user-access-to-the-sensor-and-management-console"></a>센서 및 관리 콘솔에 대 한 사용자 액세스

| 프로토콜 | 전송 | 입/출력 | 포트 | 사용됨 | 목적 | 원본 | 대상 |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | 입/출력 | 443 | 센서 및 온-프레미스 관리 콘솔 웹 콘솔에 액세스 합니다. | 웹 콘솔에 대한 액세스 | 클라이언트 | 센서 및 온-프레미스 관리 콘솔 |
| SSH | TCP | 입/출력 | 22 | CLI | CLI에 액세스 합니다. | 클라이언트 | 센서 및 온-프레미스 관리 콘솔 |

#### <a name="sensor-access-to-azure-portal"></a>Azure Portal에 대 한 센서 액세스

| 프로토콜 | 전송 | 입/출력 | 포트 | 사용됨 | 목적 | 원본 | 대상 |
|--|--|--|--|--|--|--|--|
| HTTPS/Websocket | TCP | 입/출력 | 443 | Azure Portal에 대 한 센서 액세스를 제공 합니다. 필드 프록시를 통해 액세스 권한을 부여할 수 있습니다. | Azure Portal에 액세스 | 센서 | Azure portal |

#### <a name="sensor-access-to-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에 대 한 센서 액세스

| 프로토콜 | 전송 | 입/출력 | 포트 | 사용됨 | 목적 | 원본 | 대상 |
|--|--|--|--|--|--|--|--|
| SSL | TCP | 입/출력 | 443 | 온-프레미스 관리 콘솔에 대 한 센서 액세스를 제공 합니다. | 센서와 온-프레미스 관리 콘솔 간의 연결 | 센서 | 온-프레미스 관리 콘솔 |
| NTP | UDP | 입/출력 | 123 | 시간 동기화 | NTP를 온-프레미스 관리 콘솔에 연결 합니다. | 센서 | 온-프레미스 관리 콘솔 |

#### <a name="additional-firewall-rules-for-external-services-optional"></a>외부 서비스에 대 한 추가 방화벽 규칙 (선택 사항)

IoT 용 Defender에 대 한 추가 서비스를 허용 하려면 이러한 포트를 엽니다.

| 프로토콜 | 전송 | 입/출력 | 포트 | 사용됨 | 목적 | 원본 | 대상 |
|--|--|--|--|--|--|--|--|
| HTTP | TCP | 아웃 | 80 | 인증서를 업로드할 때 인증서 유효성 검사를 위한 CRL 다운로드입니다. | CRL 서버에 대 한 액세스 | 센서 및 온-프레미스 관리 콘솔 | CRL 서버 |
| LDAP | TCP | 입/출력 | 389 | Active Directory | 액세스 권한이 있는 사용자를 Active Directory 관리 하 여 시스템에 로그인 할 수 있습니다. | 온-프레미스 관리 콘솔 및 센서 | LDAP 서버 |
| LDAPS | TCP | 입/출력 | 636 | Active Directory | 액세스 권한이 있는 사용자를 Active Directory 관리 하 여 시스템에 로그인 할 수 있습니다. | 온-프레미스 관리 콘솔 및 센서 | LDAPS 서버 |
| [SNMP](how-to-set-up-snmp-mib-monitoring.md) | UDP | 아웃 | 161 | 모니터링 | 센서 상태를 모니터링 합니다. | 온-프레미스 관리 콘솔 및 센서 | SNMP 서버 |
| SMTP | TCP | 아웃 | 25 | Email | 경고 및 이벤트에 대 한 전자 메일을 보내기 위해 고객의 메일 서버를 여는 데 사용 됩니다. | 센서 및 온-프레미스 관리 콘솔 | 메일 서버 |
| Syslog | UDP | 아웃 | 514 | LEEF | 온-프레미스 관리 콘솔에서 Syslog 서버로 전송 되는 로그입니다. | 온-프레미스 관리 콘솔 및 센서 | Syslog 서버 |
| DNS | TCP/UDP | 입/출력 | 53 | DNS | DNS 서버 포트입니다. | 온-프레미스 관리 콘솔 및 센서 | DNS 서버 |
| [WMI](how-to-configure-windows-endpoint-monitoring.md) | TCP/UDP | 아웃 | 135, 1025-65535 | 모니터링 | Windows 엔드포인트 모니터링. | 센서 | 관련 네트워크 요소 |
| 터널링 | TCP | In(다음 안에) | 9000 </br></br> 포트 443 외에 </br></br> 센서 또는 최종 사용자가 온-프레미스 관리 콘솔에 액세스할 수 있도록 허용합니다. </br></br> 센서에서 온-프레미스 관리 콘솔로 포트 22. | 모니터링 | 터널링 | 센서 | 온-프레미스 관리 콘솔 |
| Proxy (프록시) | TCP/UDP | 입/출력 | 443 | Proxy (프록시) | 센서를 프록시 서버에 연결하려면 | 온-프레미스 관리 콘솔 및 센서 | 프록시 서버 |

### <a name="plan-rack-installation"></a>랙 설치 계획

랙 설치를 계획하려면:

1. 어플라이언스 네트워크 설정을 위한 모니터와 키보드를 준비합니다.

1. 어플라이언스를 위한 랙 공간을 할당합니다.

1. 어플라이언스에 사용할 수 있는 AC 전원을 준비합니다.

1. 관리를 네트워크 스위치에 연결할 LAN 케이블을 준비합니다.

1. 스위치 SPAN(미러) 포트를 연결하기 위해 LAN 케이블을 준비하고 Defender for IoT 어플라이언스에 대한 네트워크 탭을 준비합니다.

1. 아키텍처 검토 세션에 설명된 대로 미러링된 스위치에서 SPAN 포트를 구성 및 연결하고 유효성을 검사합니다.

1. 구성된 SPAN 포트를 Wireshark를 실행하는 컴퓨터에 연결하고 포트가 올바르게 구성되었는지 확인합니다.

1. 모든 관련 방화벽 포트를 엽니다.

## <a name="about-passive-network-monitoring"></a>패시브 네트워크 모니터링 정보

어플라이언스는 스위치 미러 포트(SPAN 포트) 또는 네트워크 TAP를 통해 여러 원본에서 트래픽을 수신합니다. 관리 포트는 온-프레미스 관리 콘솔 또는 Azure Portal Defender for IoT에 연결된 비즈니스, 회사 또는 센서 관리 네트워크에 연결됩니다.

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="포트 미러링이 포함된 관리되는 스위치의 다이어그램.":::

### <a name="purdue-model"></a>Purdue 모델

다음 섹션에서는 Purdue 수준을 설명합니다.

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Purdue 모델의 다이어그램.":::

#### <a name="level-0-cell-and-area"></a>수준 0: 셀 및 영역  

수준 0은 기본 제조 공정에 관련된 다양한 센서, 작동기, 디바이스로 구성됩니다. 이러한 디바이스는 다음과 같은 산업용 자동화 및 제어 시스템의 기본 기능을 수행합니다.

- 모터 구동.

- 변수 측정.
- 출력 설정.
- 도장, 용접, 벤딩 등의 주요 기능 수행.

#### <a name="level-1-process-control"></a>수준 1: 공정 제어

수준 1은 수준 0 디바이스와 통신하는 것이 주요 기능인 제조 공정을 제어 및 조작하는 포함된 컨트롤러로 구성됩니다. 개별 제조에서 이러한 디바이스는 PLC(Programmable Logic Controller) 또는 RTU(Remote Telemetry Unit)입니다. 제조 공정에서 기본 컨트롤러는 DCS(분산 제어 시스템)라고 합니다.

#### <a name="level-2-supervisory"></a>수준 2: 감독

수준 2는 생산 시설 영역의 런타임 감독 및 운영과 관련된 시스템과 기능을 나타냅니다. 여기에는 다음이 포함됩니다. 

- 운영자 인터페이스 또는 HMI

- 경보 또는 경고 시스템

- 프로세스 히스토리안 및 배치 관리 시스템

- 제어실 워크스테이션

이러한 시스템은 수준 1의 PLC 및 RTU와 통신합니다. 경우에 따라서 사이트 또는 엔터프라이즈(수준 4 및 수준 5) 시스템 및 애플리케이션과 통신하거나 데이터를 공유합니다. 이러한 시스템은 주로 표준 컴퓨팅 장비 및 운영 체제(Unix 또는 Microsoft Windows)를 기반으로 합니다.

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>수준 3 및 3.5: 사이트 수준 및 산업용 경계 네트워크

사이트 수준은 최고 수준의 산업 자동화 및 제어 시스템을 나타냅니다. 이 수준에 존재하는 시스템 및 애플리케이션은 사이트 전체의 산업 자동화 및 제어 기능을 관리합니다. 수준 0부터 3까지는 사이트 운영에 중요한 것으로 간주됩니다. 이 수준에 존재하는 시스템과 기능에는 다음이 포함될 수 있습니다.

- 생산 보고(예: 주기 시간, 품질 지수, 예측 유지 관리)

- 플랜트 히스토리안

- 자세한 생산 일정 예약

- 사이트 수준 운영 관리

- 디바이스 및 재료 관리

- 패치 시작 서버

- 파일 서버

- 산업용 도메인, Active Directory, 터미널 서버

이러한 시스템은 생산 영역과 통신하고 엔터프라이즈(수준 4 및 수준 5) 시스템 및 애플리케이션과 데이터를 공유합니다.  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>수준 4 및 5: 비즈니스 및 엔터프라이즈 네트워크

수준 4 및 수준 5는 중앙 집중식 IT 시스템과 기능이 있는 사이트 또는 엔터프라이즈 네트워크를 나타냅니다. IT 조직은 이 수준의 서비스, 시스템, 애플리케이션을 직접 관리합니다.

## <a name="planning-for-network-monitoring"></a>네트워크 모니터링 계획

다음 예는 최적의 센서 모니터링 및 배치를 위한 고려 사항과 함께 다양한 산업 제어 네트워크 유형의 토폴로지를 보여 줍니다.

### <a name="what-should-be-monitored"></a>무엇을 모니터링해야 하나요?

계층 1과 2를 통과하는 트래픽을 모니터링해야 합니다.

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>Defender for IoT 어플라이언스를 무엇에 연결해야 하나요?

Defender for IoT 어플라이언스는 계층 1과 계층 2(일부 경우에는 계층 3) 사이의 산업용 통신을 보는 관리되는 스위치에 연결해야 합니다.

다음 다이어그램은 일반적으로 SOC와 MSSP가 운영하는 광범위한 사이버 보안 에코시스템이 있는 다중 계층 다중 테넌트 네트워크를 추상화한 것입니다.

일반적으로 NTA 센서는 OSI 모델의 계층 0~3에 배포됩니다.

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="OCI 모델의 다이어그램.":::

#### <a name="example-ring-topology"></a>예: 링 토폴로지

링 네트워크는 각 스위치나 노드가 정확히 2개의 다른 스위치에 연결되어 트래픽의 단일 연속 경로를 형성하는 토폴로지입니다.

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="링 토폴로지의 다이어그램.":::

#### <a name="example-linear-bus-and-star-topology"></a>예: 선형 버스 및 별 모양 토폴로지

별 모양 네트워크에서는 모든 호스트가 중앙 허브에 연결됩니다. 가장 간단한 형태에서는 중앙 허브 하나가 메시지를 전송하기 위한 통로 역할을 합니다. 다음 예에서 하위 스위치는 모니터링되지 않으며 이러한 스위치에 대해 로컬로 유지되는 트래픽은 표시되지 않습니다. 디바이스는 ARP 메시지에 따라 식별할 수 있지만 연결 정보가 누락됩니다.

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="선형 버스 및 별 모양 토폴로지의 다이어그램.":::

#### <a name="multisensor-deployment"></a>다중 센서 배포

다음은 여러 센서를 배포하기 위한 몇 가지 권장 사항입니다.

| **Number** | **미터** | **종속성** | **센서 수** |
|--|--|--|--|
| 스위치 간 최대 거리 | 80미터 | 준비된 이더넷 케이블 | 2개 이상 |
| OT 네트워크 수 | 2개 이상 | 물리적 연결 없음 | 2개 이상 |
| 스위치 수 | RSPAN 구성 사용 가능 | 케이블 거리 기준으로 로컬 SPAN이 센서에 가까운 최대 8개의 스위치 | 2개 이상 |

#### <a name="traffic-mirroring"></a>트래픽 미러링  

트래픽 분석을 위해 관련 정보만 보려면 Defender for IoT 플랫폼을 스위치의 미러링 포트 또는 산업용 ICS 및 SCADA 트래픽만 포함하는 TAP에 연결해야 합니다.

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="설정에 이 스위치를 사용하세요.":::

다음 방법을 사용하여 스위치 트래픽을 모니터링할 수 있습니다.

- [스위치 SPAN 포트](#switch-span-port)

- [원격 SPAN(RSPAN)](#remote-span-rspan)

- [액티브 및 패시브 집계 TAP](#active-and-passive-aggregation-tap)

SPAN과 RSPAN은 Cisco 용어입니다. 다른 브랜드의 스위치는 기능이 비슷해도 다른 용어를 사용할 수 있습니다.

#### <a name="switch-span-port"></a>스위치 SPAN 포트

스위치 포트 분석기는 해당 스위치의 인터페이스에서 동일한 스위치의 인터페이스로 로컬 트래픽을 미러링합니다. 다음은 몇 가지 고려 사항입니다.

- 관련 스위치가 포트 미러링 기능을 지원하는지 확인합니다.  

- 미러링 옵션은 기본적으로 사용하지 않도록 설정되어 있습니다.

- 연결된 데이터가 없는 경우에도 모든 스위치의 포트를 구성하는 것이 좋습니다. 그러지 않으면 악의적 디바이스가 모니터링되지 않는 포트에 연결될 수 있고 센서에서 경고되지 않습니다.

- 브로드캐스트 또는 멀티캐스트 메시징을 활용하는 OT 네트워크에서 RX(수신) 전송만 미러링하도록 스위치를 구성합니다. 이렇게 하지 않으면 멀티캐스트 메시지가 활성 포트 수만큼 반복되고 대역폭이 증가합니다.

다음 구성 예제는 참조용이며, iOS를 실행하는 Cisco 2960 스위치(24포트)를 기반으로 합니다. 일반적인 예일 뿐이므로 지침으로 사용하지 마세요. 다른 Cisco 운영 체제와 기타 브랜드 스위치의 미러 포트는 다르게 구성됩니다.

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="SPAN 포트 구성 터미널의 다이어그램.":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="SPAN 포트 구성 모드의 다이어그램.":::

##### <a name="monitoring-multiple-vlans"></a>다중 VLAN 모니터링

Defender for IoT를 사용하면 네트워크에 구성된 VLAN을 모니터링할 수 있습니다. Defender for IoT 시스템의 구성은 필요하지 않습니다. 사용자는 네트워크의 스위치가 Defender for IoT에 VLAN 태그를 보내도록 구성되어 있는지 확인해야 합니다.

다음 예제는 Defender for IoT에서 VLAN 모니터링을 사용하도록 Cisco 스위치에서 구성해야 하는 필수 명령을 보여 줍니다.

**monitor session**: 이 명령은 VLAN을 SPAN 포트로 보내는 프로세스를 담당합니다.

- monitor session 1 source interface Gi1/2

- monitor session 1 filter packet type good Rx

- monitor session 1 destination interface fastEthernet1/1 encapsulation dot1q

**monitor trunk port F.E. Gi1/1**: VLAN이 트렁크 포트에서 구성됩니다.

- interface GigabitEthernet1/1

- switchport trunk encapsulation dot1q

- switchport mode trunk

#### <a name="remote-span-rspan"></a>원격 SPAN(RSPAN)

원격 SPAN 세션은 여러 분산된 원본 포트에서 전용 원격 VLAN으로 트래픽을 미러링합니다.  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="원격 SPAN의 다이어그램.":::

그런 다음 VLAN의 데이터는 여러 스위치의 트렁크 포트를 통해 실제 대상 포트를 포함하는 특정 스위치로 배달됩니다. 이 포트는 Defender for IoT 플랫폼에 연결됩니다.  

##### <a name="more-about-rspan"></a>RSPAN에 대한 추가 정보

- RSPAN은 SPAN이 스위치 사이에서 모니터링하는 트래픽을 전달하기 위해 특수 VLAN이 필요한 고급 기능입니다. 모든 스위치에서 RSPAN이 지원되지는 않습니다. 스위치가 RSPAN 기능을 지원하는지 확인하세요.

- 미러링 옵션은 기본적으로 사용하지 않도록 설정되어 있습니다.

- 원본 스위치와 대상 스위치 사이의 트렁크 포트에서 원격 VLAN을 허용해야 합니다.

- 동일한 RSPAN 세션을 연결하는 모든 스위치는 동일한 공급업체의 스위치여야 합니다.

> [!NOTE]
> 스위치 간에 원격 VLAN을 공유하는 트렁크 포트는 미러 세션 원본 포트로 정의되지 않아야 합니다.
>
> 원격 VLAN은 미러된 세션 대역폭 크기만큼 트렁크 포트에서 대역폭을 늘립니다. 스위치의 트렁크 포트가 이를 지원하는지 확인하세요.  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="원격 VLAN의 다이어그램.":::

#### <a name="rspan-configuration-examples"></a>RSPAN 구성 예제

RSPAN: Cisco catalyst 2960(24포트)을 기반으로 합니다.

**원본 스위치 구성 예제:**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="RSPAN 구성의 스크린샷.":::

1. 전역 구성 모드에 진입합니다.

1. 전용 VLAN을 만듭니다.

1. VLAN을 RSPAN VLAN으로 식별합니다.

1. ‘터미널 구성’ 모드로 돌아갑니다.

1. 24개 포트 모두를 세션 원본으로 구성합니다.

1. RSPAN VLAN을 세션 대상으로 구성합니다.

1. 권한 있는 EXEC 모드로 돌아갑니다.

1. 포트 미러링 구성을 확인합니다.

**대상 스위치 구성 예제:**

1. 전역 구성 모드에 진입합니다.

1. RSPAN VLAN을 세션 원본으로 구성합니다.

1. 실제 포트 24개를 세션 대상으로 구성합니다.

1. 권한 있는 EXEC 모드로 돌아갑니다.

1. 포트 미러링 구성을 확인합니다.

1. 구성을 저장합니다.

#### <a name="active-and-passive-aggregation-tap"></a>액티브 및 패시브 집계 TAP

액티브 또는 패시브 집계 TAP는 네트워크 케이블에 인라인으로 설치되며, RX와 TX 모두를 모니터링 센서에 복제합니다.

터미널 액세스 지점(TAP)은 네트워크 트래픽이 포트 A에서 포트 B로, 포트 B에서 포트 A로 이동하는 것을 허용하는 하드웨어 디바이스입니다. TAP는 네트워크 무결성을 손상시키지 않고 트래픽 흐름 양쪽의 정확한 복사본을 연속적으로 만듭니다. 일부 TAP는 원하는 경우 스위치 설정을 사용하여 전송 및 수신 트래픽을 집계합니다. 집계가 지원되지 않는 경우 각 TAP는 두 개의 센서 포트를 사용하여 송신 및 수신 트래픽을 모니터링합니다.

TAP는 다양한 이유로 유용합니다. 하드웨어 기반이며 손상될 수 없습니다. 이러한 메시지는 손상 된 메시지를 포함 하 여 모든 트래픽을 전달 하며,이는 스위치를 자주 삭제 합니다. 프로세서를 구별하지 않으므로 패킷 타이밍이 정확합니다. 즉, 스위치는 미러링된 패킷의 타이밍에 영향을 줄 수 있는 미러 기능을 낮은 우선 순위의 작업으로 처리합니다. TAP는 포렌식 용도로 최고의 디바이스입니다.

TAP 집계는 포트 모니터링에도 사용할 수 있습니다. 이러한 디바이스는 프로세서 기반이며, 하드웨어 TAP만큼 본질적으로 안전하지 않습니다. 정확한 패킷 타이밍을 반영하지 않을 수 있습니다.

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="액티브 및 패시브 TAP의 다이어그램.":::

##### <a name="common-tap-models"></a>일반적인 TAP 모델

이러한 모델은 호환성 테스트를 거쳤습니다. 다른 공급업체 및 모델도 호환될 수 있습니다.

| 이미지 | 모델 |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Garland P1GCCAS의 스크린샷."::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="IXIA TPA2-CU3의 스크린샷."::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="US Robotics USR 4503의 스크린샷."::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>특수 TAP 구성

| Garland TAP | US Robotics TAP |
| ----------- | --------------- |
| 점퍼가 다음과 같이 설정되어 있는지 확인합니다.<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="US Robotics 스위치의 스크린샷.":::| 집계 모드가 활성 상태인지 확인합니다. |

## <a name="deployment-validation"></a>배포 유효성 검사

### <a name="engineering-self-review"></a>엔지니어링 자체 검토  

OT 및 ICS 네트워크 다이어그램 검토는 모니터링할 가장 관련성 높은 트래픽을 얻을 수 있는 최선의 연결 지점을 정의하는 가장 효율적인 방법입니다.

사이트 엔지니어는 사이트 네트워크의 형태를 압니다. 로컬 네트워크 및 운영 팀과 함께 검토 세션을 수행하면 일반적으로 기대치를 명확히 설명하고 어플라이언스를 연결할 최적의 위치를 정의할 수 있습니다.

관련 정보:

- 알려진 디바이스 목록(스프레드시트)  

- 예상 디바이스 수  

- 공급업체 및 산업 프로토콜

- 포트 미러링 옵션을 사용할 수 있는지 확인하기 위한 스위치 모델

- 스위치를 관리하는 주체(예: IT) 및 이 주체가 외부 리소스인지에 관한 정보

- 사이트의 OT 네트워크 목록

#### <a name="common-questions"></a>일반적인 질문

- 구현의 전체적인 목표는 무엇인가요? 완전한 인벤토리와 정확한 네트워크 맵이 중요한가요?

- ICS에 다중 또는 중복 네트워크가 있나요? 모든 네트워크가 모니터링되고 있나요?

- ICS와 엔터프라이즈(비즈니스) 네트워크 간에 통신이 있나요? 이러한 통신이 모니터링되고 있나요?

- 네트워크 디자인에서 VLAN이 구성되어 있나요?

- ICS의 유지 관리는 어떻게 수행하나요? 고정 디바이스를 사용하나요 임시 디바이스를 사용하나요?

- 모니터링되는 네트워크 어디에 방화벽이 설치되어 있나요?

- 모니터링되는 네트워크에 라우팅이 있나요?

- 모니터링되는 네트워크에서 어떤 OT 프로토콜이 활성 상태인가요?

- 이 스위치에 연결하면 HMI와 PLC 간의 통신을 볼 수 있나요?

- ICS 스위치와 엔터프라이즈 방화벽 간의 실제 거리는 어떻게 되나요? 

- 관리되지 않는 스위치를 관리되는 스위치로 바꾸거나 네트워크 TAP를 옵션으로 사용할 수 있나요?

- 네트워크에 직렬 통신이 있나요? 그렇다면 네트워크 다이어그램에 표시하세요.

- Defender for IoT 어플라이언스를 해당 스위치에 연결해야 하는 경우 해당 캐비닛에 사용 가능한 물리적 랙 공간이 있나요?

#### <a name="other-considerations"></a>기타 고려 사항

Defender for IoT 어플라이언스의 용도는 계층 1과 2의 트래픽을 모니터링하는 것입니다.

일부 아키텍처에서는 계층 3에 OT 트래픽이 있는 경우 Defender for IoT 어플라이언스가 이 계층도 모니터링합니다. 사이트 아키텍처를 검토하고 스위치를 모니터링할지 여부를 결정할 때는 다음 변수를 고려하세요.

- 이 스위치 모니터링의 비용/편익과 중요도를 비교하면 어떻게 되나요?

- 스위치가 관리되지 않는 경우 더 상위 수준의 스위치에서 오는 트래픽을 모니터링할 수 있나요?

  ICS 아키텍처가 링 토폴로지인 경우 이 링의 스위치 하나만 모니터링해야 합니다.

- 이 네트워크의 보안 또는 운영 위험은 무엇인가요?

- 스위치의 VLAN을 모니터링할 수 있나요? 모니터링 가능한 다른 스위치에 이 VLAN이 표시되나요?

#### <a name="technical-validation"></a>기술 유효성 검사

스위치 SPAN(또는 미러) 포트에서 기록된 트래픽(PCAP 파일) 샘플을 수신하면 다음 작업에 도움이 될 수 있습니다.

- 스위치가 올바르게 구성되었는지 확인합니다.

- 스위치를 통과하는 트래픽이 모니터링에 적합한지(OT 트래픽) 확인합니다.

- 이 스위치의 대역폭 및 예상 디바이스 수를 식별합니다.

Wireshark 애플리케이션을 통해 이미 구성된 SPAN 포트에 노트북을 연결하여 샘플 PCAP 파일(2~3분)을 기록할 수 있습니다.

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="SPAN 포트에 연결된 노트북의 스크린샷.":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="샘플 PCAP 파일 기록의 스크린샷.":::

#### <a name="wireshark-validation"></a>Wireshark 유효성 검사

- *유니캐스트 패킷* 이 기록 트래픽에 있는지 확인합니다. 유니캐스트는 한 주소에서 다른 주소로 전송합니다. 대부분의 트래픽이 ARP 메시지인 경우 스위치 설정이 잘못된 것입니다.

- **통계** > **프로토콜 계층 구조** 로 이동합니다. 산업용 OT 프로토콜이 있는지 확인합니다.

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Wireshark 유효성 검사의 스크린샷.":::

## <a name="troubleshooting"></a>문제 해결

다음 섹션을 문제 해결에 사용하세요.

- [웹 인터페이스를 사용하여 연결할 수 없음](#cant-connect-by-using-a-web-interface)

- [어플라이언스가 응답하지 않음](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>웹 인터페이스를 사용하여 연결할 수 없음

1. 연결하려는 컴퓨터가 어플라이언스와 동일한 네트워크에 있는지 확인합니다.

2. GUI 네트워크가 센서의 관리 포트에 연결되어 있는지 확인합니다.

3. 어플라이언스 IP 주소를 ping합니다. ping에 대한 응답이 없는 경우:

    1. 모니터와 키보드를 어플라이언스에 연결합니다.

    1. **지원** 사용자 및 암호를 사용하여 로그인합니다.

    1. **network list** 명령을 사용하여 현재 IP 주소를 확인합니다.

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="network list 명령의 스크린샷.":::

4. 네트워크 매개 변수가 잘못 구성된 경우 다음 절차를 사용하여 변경합니다.

    1. **network edit-settings** 명령을 사용합니다.

    1. 관리 네트워크 IP 주소를 변경하려면 **Y** 를 선택합니다.

    1. 서브넷 마스크를 변경하려면 **Y** 를 선택합니다.

    1. DNS를 변경하려면 **Y** 를 선택합니다.

    1. 기본 게이트웨이 IP 주소를 변경하려면 **Y** 를 선택합니다.

    1. 입력 인터페이스를 변경(센서 전용)하려면 **Y** 를 선택합니다.

    1. 브리지 인터페이스에서 **N** 을 선택합니다.

    1. 설정을 적용하려면 **Y** 를 선택합니다.

5. 다시 시작한 후 사용자 지원에 연결하고 **network list** 명령을 사용하여 매개 변수가 변경되었는지 확인합니다.

6. ping하고 GUI에서 다시 연결해 보세요.

### <a name="appliance-is-not-responding"></a>어플라이언스가 응답하지 않음

1. 모니터와 키보드를 어플라이언스에 연결하거나 PuTTY를 사용하여 CLI에 원격으로 연결합니다.

2. 지원 자격 증명을 사용하여 로그인합니다.

3. **system sanity** 명령을 사용하여 모든 프로세스가 실행 중인지 확인합니다.

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="system sanity 명령의 스크린샷.":::

기타 문제는 [Microsoft 지원](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에 문의하세요.

## <a name="predeployment-checklist"></a>배포 전 검사 목록

배포 전 검사 목록을 사용하여 네트워크 설정에 필요한 중요한 정보를 검색하고 검토합니다.

### <a name="site-checklist"></a>사이트 검사 목록

사이트 배포 전에 이 목록을 검토합니다.

| **#** | **태스크 또는 작업** | **상태** | **설명** |
|--|--|--|--|
| 1 | 어플라이언스를 주문합니다. | ☐ |  |
| 2 | 네트워크의 서브넷 목록을 준비합니다. | ☐ |  |
| 3 | 프로덕션 네트워크의 VLAN 목록을 제공합니다. | ☐ |  |
| 4 | 네트워크의 스위치 모델 목록을 제공합니다. | ☐ |  |
| 5 | 산업 장비의 공급업체 및 프로토콜 목록을 제공합니다. | ☐ |  |
| 6 | 센서의 네트워크 세부 정보(IP 주소, 서브넷, D-GW, DNS)를 제공합니다. | ☐ |  |
| 7 | 타사 스위치 관리 | ☐ |  |
| 8 | 필요한 방화벽 규칙 및 액세스 목록을 만듭니다. | ☐ |  |
| 9 | 포트 모니터링을 위해 스위치에 스패닝 포트를 만들거나 원하는 대로 네트워크 TAP를 구성합니다. | ☐ |  |
| 10 | 센서 어플라이언스를 위한 랙 공간을 준비합니다. | ☐ |  |
| 11 | 직원을 위한 워크스테이션을 준비합니다. | ☐ |  |
| 12 | Defender for IoT 랙 디바이스를 위한 키보드, 모니터, 마우스를 제공합니다. | ☐ |  |
| 13 | 어플라이언스를 랙에 설치하고 케이블을 연결합니다. | ☐ |  |
| 14 | 배포를 지원할 사이트 리소스를 할당합니다. | ☐ |  |
| 15 | Active Directory 그룹 또는 로컬 사용자를 만듭니다. | ☐ |  |
| 16 | 교육 (자가 학습)을 설정 합니다. | ☐ |  |
| 17 | 실행하거나 취소합니다. | ☐ |  |
| 18 | 배포 날짜를 예약합니다. | ☐ |  |


| **날짜** | **참고** | **배포 날짜** | **참고** |
|--|--|--|--|
| Defender for IoT |  | 사이트 이름* |  |
| 속성 |  | 속성 |  |
| 위치 |  | 위치 |  |

#### <a name="architecture-review"></a>아키텍처 검토

산업용 네트워크 다이어그램의 개요를 통해 Defender for IoT 장비의 적절한 위치를 정의할 수 있습니다.

1. **글로벌 네트워크 다이어그램** - 산업용 OT 환경의 글로벌 네트워크 다이어그램을 확인합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/how-to-set-up-your-network/backbone-switch.png" alt-text="글로벌 네트워크용 산업용 OT 환경의 다이어그램.":::

    > [!NOTE]
    > Defender for IoT 어플라이언스는 스위치의 포트 간 트래픽을 확인하는 하위 수준 스위치에 연결되어야 합니다.  

1. **커밋된 디바이스** - 모니터링할 대략적인 디바이스 수를 제공합니다. Azure Portal에서 IoT 용 Defender에 구독을 온 보 딩 하는 경우이 정보가 필요 합니다. 온보딩 프로세스 중에 1,000단위로 디바이스 수를 입력하라는 메시지가 표시됩니다.

1. **(선택 사항) 서브넷 목록** - 프로덕션 네트워크에 대한 서브넷 목록 및 설명(선택 사항)을 제공합니다.

    |  **#**  | **서브넷 이름** | **설명** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

1. **VLAN** - 프로덕션 네트워크의 VLAN 목록을 제공합니다.

    | **#** | **VLAN 이름** | **설명** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

1. **스위치 모델 및 미러링 지원** - 스위치에 포트 미러링 기능이 있는지 확인하려면 Defender for IoT 플랫폼이 연결되어야 하는 스위치 모델 번호를 제공합니다.

    | **#** | **스위치** | **모델** | **트래픽 미러링 지원(SPAN, RSPAN 또는 없음)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

1. **타사 스위치 관리** - 타사에서 스위치를 관리하나요? Y 또는 N 

    ‘예’라면 어느 회사인가요? __________________________________ 

    해당 회사의 정책은 무엇인가요? __________________________________ 

    예를 들면 다음과 같습니다.

    - Siemens

    - Rockwell Automation – 이더넷 또는 IP

    - Emerson – DeltaV, Ovation

1. **직렬 연결** - 직렬 연결을 통해 통신하는 디바이스가 네트워크에 있나요? Yes 또는 No

    ‘예’라면 직렬 통신 프로토콜을 지정합니다. ________________

    ‘예’인 경우 직렬 프로토콜과 통신하는 디바이스와 디바이스의 위치를 네트워크 다이어그램에 표시합니다.

    *표시된 직렬 연결을 사용하여 네트워크 다이어그램 추가*

1. **서비스 품질** - 서비스 품질(QoS)의 경우 센서의 기본 설정은 1.5Mbps입니다. 이를 변경할지 여부를 지정합니다. ________________

   사업부(BU): ________________

1. **센서** - 사이트 장비의 사양

    센서 어플라이언스는 네트워크 어댑터를 통해 스위치 SPAN 포트에 연결됩니다. 센서 어플라이언스는 다른 전용 네트워크 어댑터를 통해 관리할 수 있도록 고객의 회사 네트워크에 연결되어 있습니다.

    회사 네트워크에 연결될 센서 NIC의 주소 세부 정보를 제공합니다.

    | 항목 | 어플라이언스 1 | 어플라이언스 2 | 어플라이언스 3 |
    |--|--|--|--|
    | 어플라이언스 IP 주소 |  |  |  |
    | 서브넷 |  |  |  |
    | 기본 게이트웨이 |  |  |  |
    | DNS |  |  |  |
    | 호스트 이름 |  |  |  |

1. **iDRAC/iLO/서버 관리**

    | 항목 | 어플라이언스 1 | 어플라이언스 2 | 어플라이언스 3 |
    |--|--|--|--|
    | 어플라이언스 IP 주소 |  |  |  |
    | 서브넷 |  |  |  |
    | 기본 게이트웨이 |  |  |  |
    | DNS |  |  |  |

1. **온-프레미스 관리 콘솔**

    | 항목 | Active | 패시브(HA를 사용하는 경우) |
    |--|--|--|
    | IP 주소 |  |  |
    | 서브넷 |  |  |
    | 기본 게이트웨이 |  |  |
    | DNS |  |  |

1. **SNMP**  

    | 항목 | 세부 정보 |
    |--|--|
    | IP |  |
    | IP 주소 |  |
    | 사용자 이름 |  |
    | 암호 |  |
    | 인증 유형 | MD5 또는 SHA |
    | 암호화 | DES 또는 AES |
    | 비밀 키 |  |
    | SNMP v2 커뮤니티 문자열 |

1. **온-프레미스 관리 콘솔 SSL 인증서**

    SSL 인증서를 사용할 계획입니까? Yes 또는 No

    ‘예’라면 인증서를 생성하는 데 사용할 서비스는 무엇인가요? 인증서에 포함할 특성(예: 도메인 또는 IP 주소)은 무엇인가요?

1. **SMTP 인증**

    SMTP를 사용하여 이메일 서버에 경고를 전달할 계획입니까? Yes 또는 No

    ‘예’라면 어떤 인증 방법을 사용하려 하나요?  

1. **Active Directory 또는 로컬 사용자**

    Active Directory 관리자에게 문의하여 Active Directory 사이트 사용자 그룹을 만들거나 로컬 사용자를 만듭니다. 사용자가 배포 날짜에 준비되어 있어야 합니다.

1. 네트워크의 IoT 디바이스 유형

    | 디바이스 유형 | 네트워크의 디바이스 수 | 평균 대역폭 |
    | --------------- | ------ | ----------------------- |
    | 카메라 | |
    | X선 기계 | |
    |  |  |
    |  |  |
    |  |  |
    |  |  |
    |  |  |
    |  |  |
    |  |  |
    |  |  |

## <a name="next-steps"></a>다음 단계

[Defender for IoT 설치 정보](how-to-install-software.md)
