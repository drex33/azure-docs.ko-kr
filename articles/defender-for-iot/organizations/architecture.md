---
title: 에이전트 없는 솔루션 아키텍처란
description: Microsoft Defender for IoT 에이전트 없는 아키텍처 및 정보 흐름에 대해 알아봅니다.
ms.topic: overview
ms.date: 11/09/2021
ms.openlocfilehash: 06178eadd4b246fd0399b4835f05b52a96520740
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484470"
---
# <a name="microsoft-defender-for-iot-architecture"></a>Microsoft Defender for IoT 아키텍처

이 문서에서는 Defender for IoT 에이전트 없는 솔루션의 기능적 시스템 아키텍처를 설명합니다. Microsoft Defender for IoT는 사용자 환경의 요구 사항에 맞는 두 가지 기능 세트, 조직을 위한 에이전트 없는 솔루션 및 디바이스 빌더를 위한 에이전트 기반 솔루션을 제공합니다.

## <a name="agentless-solution-architecture-for-organizations"></a>조직을 위한 에이전트 없는 솔루션 아키텍처
### <a name="defender-for-iot-components"></a>Defender for IoT 구성 요소

Defender for IoT는 Azure 클라우드와 온-프레미스 구성 요소에 모두 연결됩니다. 이 솔루션은 여러 원격 위치가 포함된 방대하고 지리적으로 분산된 환경에서 우수한 확장성을 제공하도록 설계되었습니다. 이 솔루션을 사용하면 국가, 지역, 사업부 또는 영역별로 다중 계층 분산 아키텍처를 구현할 수 있습니다. 

Microsoft Defender for IoT에는 다음과 같은 구성 요소가 포함되어 있습니다. 

**클라우드 연결 배포**

- Microsoft Defender for IoT 센서 VM 또는 어플라이언스
- 클라우드를 관리하고 Microsoft Sentinel에 통합하는 데 사용되는 Azure Portal
- 로컬 사이트 관리용 온-프레미스 관리 콘솔
- 포함된 보안 에이전트(옵션)

**에어-갭(오프라인) 배포**

- Microsoft Defender for IoT 센서 VM 또는 어플라이언스
- 로컬 사이트 관리용 온-프레미스 관리 콘솔

:::image type="content" source="./media/architecture/defender-iot-security-architecture-v3.png" alt-text="Defender for IoT 아키텍처":::

### <a name="microsoft-defender-for-iot-sensors"></a>Microsoft Defender for IoT 센서

Defender for IoT 센서는 네트워크 디바이스를 검색하고 지속적으로 모니터링합니다. 이 센서는 IoT 및 OT 디바이스에서 수동(에이전트 없는) 모니터링을 사용하여 ICS 네트워크 트래픽을 수집합니다. 
 
IoT 및 OT 네트워크용으로 특수 제작된 에이전트 없는 기술은 네트워크에 연결되는 동안 IoT 및 OT에 대한 심층적인 가시성을 제공합니다. 비침습적 NTA(네트워크 트래픽 분석) 접근 방식을 사용하기 때문에 네트워크 및 네트워크 디바이스의 성능에 전혀 영향을 주지 않습니다. 
 
특허받은 IoT 및 OT 인식 동작 분석과 Layer-7 DPI(심층 패킷 검사)를 적용하므로 기존의 서명 기반 솔루션을 뛰어넘어 비정상 또는 무단 활동을 기반으로 하는 지능형 IoT 및 OT 위협(예: 파일리스 맬웨어)을 즉시 탐지할 수 있습니다. 
  
Defender for IoT 센서는 SPAN 포트 또는 네트워크 TAP에 연결하고 IoT 및 OT 네트워크 트래픽의 DPI를 즉시 수행하기 시작합니다. 
 
데이터 수집, 처리, 분석 및 경고는 센서에서 직접 수행됩니다. 이와 같은 프로세스 덕분에 메타데이터만 관리 콘솔로 전송되므로 대역폭이 낮거나 연결 대기 시간이 긴 위치에 적합합니다.

센서에는 5개의 분석 검색 엔진이 포함되어 있습니다. 엔진은 실시간 트래픽과 미리 기록된 트래픽의 분석 결과에 따라 경고를 트리거합니다. 다음과 같은 엔진을 사용할 수 있습니다. 

#### <a name="protocol-violation-detection-engine"></a>프로토콜 위반 검색 엔진
프로토콜 위반 검색 엔진은 ICS 프로토콜 사양을 위반하는 패킷 구조 및 필드 값의 사용(예: Modbus 예외, 사용되지 않는 함수 코드 경고의 시작)을 식별합니다.

#### <a name="policy-violation-detection-engine"></a>정책 위반 검색 엔진
기계 학습을 사용하는 정책 위반 검색 엔진은 특정 함수 코드의 무단 사용, 특정 개체에 대한 액세스, 디바이스 구성 변경 등과 같이 기준 동작과 다른 모든 것을 사용자에게 알립니다. 예를 들어 DeltaV 소프트웨어 버전이 변경되면 권한 없는 PLC 프로그래밍 경고가 발생합니다. 구체적으로 말하자면, 정책 위반 검색 엔진은 IFSM(Industrial Finite State Modeling)이라는 특허받은 기술을 사용하여 ICS 네트워크를 상태 및 전환의 결정적 시퀀스로 모델링합니다. 네트워크 기준을 세우는 데 필요한 플랫폼의 학습 기간이 원래 OT 네트워크가 아닌 IT용으로 개발된 일반적인 수학적 방식 또는 분석보다 짧도록, 정책 위반 검색 엔진은 ICS 네트워크의 기준을 설정합니다.

#### <a name="industrial-malware-detection-engine"></a>산업용 맬웨어 탐지 엔진
산업용 맬웨어 탐지 엔진은 Conficker, Black Energy, Havex, WannaCry, NotPetya, Triton 등의 알려진 맬웨어의 존재를 나타내는 동작을 식별합니다. 

#### <a name="anomaly-detection-engine"></a>변칙 검색 엔진
변칙 검색 엔진은 일상적이지 않은 M2M(컴퓨터 간) 통신 및 동작을 탐지합니다. ICS 네트워크를 상태 및 전환의 결정적 시퀀스로 모델링하므로, 플랫폼의 학습 기간이 원래 OT가 아닌 IT용으로 개발된 일반적인 수학적 방식 또는 분석보다 짧아야 합니다. 또한 변칙을 더 빠르게 탐지하면서도 가양성을 최소화합니다. 변칙 검색 엔진은 과도한 SMB 로그인 시도와 PLC 스캔 탐지 경고를 제공합니다.

#### <a name="operational-incident-detection"></a>운영 인시던트 검색
운영 인시던트 검색 엔진은 장비 오류의 초기 신호를 나타낼 수 있는 간헐적 연결과 같은 운영 문제를 검색합니다. 디바이스 연결이 끊어지고(응답하지 않음) Siemens S7 stop PLC 명령이 경고를 보낸 상황을 예로 들 수 있습니다.

### <a name="management-consoles"></a>관리 콘솔
하이브리드 환경에서 Microsoft Defender for IoT를 관리하는 작업은 다음 두 가지 관리 포털을 통해 수행됩니다. 
- 센서 콘솔
- 온-프레미스 관리 콘솔
- Azure Portal

### <a name="sensor-console"></a>센서 콘솔
센서 검색 내용은 네트워크 맵, 디바이스 인벤토리 및 광범위한 범위의 보고서(예: 위험 평가 보고서, 데이터 마이닝 쿼리 및 공격 벡터)에서 살펴보고 조사하고 분석할 수 있는 센서 콘솔에 표시됩니다. 이 콘솔을 사용하여 센서 엔진에서 탐지한 위협을 확인 및 처리하고, 파트너 시스템에 정보를 전달하고, 사용자를 관리하는 등의 작업이 가능합니다.

:::image type="content" source="./media/architecture/sensor-console-v2.png" alt-text="Defender for IoT 센서 콘솔":::

### <a name="on-premises-management-console"></a>온-프레미스 관리 콘솔
온-프레미스 관리 콘솔을 사용하면 SOC(보안 운영 센터) 운영자는 여러 센서에서 집계된 경고를 단일 대시보드에서 관리 및 분석하고 OT 네트워크 상태를 전체적으로 볼 수 있습니다.

이 아키텍처는 SOC 수준에서 네트워크에 대한 종합적인 통합 보기, 최적화된 경고 처리 및 운영 네트워크 보안 제어 기능을 제공하며, 의사 결정 및 위험 관리를 정상적으로 유지합니다.

다중 테넌트, 모니터링, 데이터 분석 및 중앙 집중식 센서 원격 제어 외에도 관리 콘솔은 각 원격 어플라이언스에 사용되는 추가적인 시스템 유지 관리 도구(예: 경고 제외)와 원하는 대로 사용자 지정할 수 있는 보고 기능을 제공합니다. 이 아키텍처는 사이트 수준 및 영역 수준의 로컬 관리 기능과 SOC 내의 글로벌 관리 기능을 모두 지원합니다.

복구에 필요한 모든 구성 파일의 백업을 정기적으로 수신하는 백업 콘솔을 제공하는 고가용성 구성이 필요한 경우 관리 콘솔을 배포할 수 있습니다. 주 콘솔에 오류가 발생하면 로컬 사이트 관리 어플라이언스는 중단 없이 가용성을 유지하기 위해 자동으로 장애 조치(failover)되어 백업 콘솔과 동기화됩니다.

SOC 워크플로 및 Runbook과 긴밀하게 통합되어 있어서 위협 완화 작업 및 사이트 간 상관 관계의 우선 순위를 쉽게 파악할 수 있습니다.

- 전체론적 접근 방식 - 장치 관리, 위험 및 취약성 관리를 모두 처리할 수 있는 통합된 단일 플랫폼과 인시던트 대응이 가능한 위협 모니터링을 통해 복잡성을 줄입니다.

- 집계 및 상관 관계 – 모든 사이트에서 수집된 데이터와 경고를 표시, 집계 및 분석합니다.

- 모든 센서 제어 – 단일 위치에서 모든 센서를 구성하고 모니터링합니다.

   :::image type="content" source="media/updates/alerts-and-site-management-v2.png" alt-text="모든 경고 및 정보를 관리합니다.":::

### <a name="azure-portal"></a>Azure portal

Azure의 Azure Portal에 있는 Defender for IoT는 다음을 지원하는 데 사용됩니다.

- 솔루션 어플라이언스 구매

- 소프트웨어 설치 및 업데이트

- Azure에 센서 등록

- 위협 인텔리전스 패키지 업데이트

## <a name="next-steps"></a>다음 단계

[Defender for IoT FAQ](resources-frequently-asked-questions.md)

