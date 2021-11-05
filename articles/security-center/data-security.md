---
title: 클라우드 데이터 보안을 위한 Microsoft Defender | Microsoft Docs
description: Microsoft Defender for Cloud에서 데이터를 관리하고 보호하는 방법을 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 21e9ccfad8364bffd28dac9346b74c1180397437
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445277"
---
# <a name="microsoft-defender-for-cloud-data-security"></a>클라우드 데이터 보안을 위한 Microsoft Defender

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

고객이 위협을 방지, 탐지 및 대응하는 데 도움이 되도록 Microsoft Defender for Cloud에서 구성 정보, 메타데이터, 이벤트 로그 등을 포함한 보안 관련 데이터를 수집하고 처리합니다. Microsoft는 코딩부터 서비스에 이르기까지 엄격한 규정 준수 및 보안 지침을 따릅니다.

이 문서에서는 Defender for Cloud에서 데이터를 관리하고 보호하는 방법을 설명합니다.

## <a name="data-sources"></a>데이터 원본
Defender for Cloud에서는 다음 소스의 데이터를 분석하여 보안 상태에 대한 가시성을 제공하고, 취약점을 식별하고, 위험 완화 방법을 권장하고, 활성 위협을 감지합니다.

- **Azure 서비스**: 해당 서비스의 리소스 공급자와 통신하여 배포한 Azure 서비스의 구성에 대한 정보를 사용합니다.
- **네트워크 트래픽**: 원본/대상 IP/포트, 패킷 크기 및 네트워크 프로토콜과 같은 Microsoft의 인프라에서 샘플링된 네트워크 트래픽 메타데이터를 사용합니다.
- **파트너 솔루션**: 방화벽 및 맬웨어 방지 솔루션과 같은 통합된 파트너 솔루션의 보안 경고를 사용합니다.
- **컴퓨터**: 컴퓨터의 Windows 이벤트, 감사 로그 및 syslog 메시지와 같은 보안 이벤트에 대한 구성 세부 정보 및 일반 정보를 사용합니다.

## <a name="data-protection"></a>데이터 보호

### <a name="data-segregation"></a>데이터 분리
데이터는 서비스 전체에서 각 구성 요소에 논리적으로 별도로 유지됩니다. 모든 데이터에는 조직별로 태그가 지정됩니다. 이 태그는 데이터 수명 주기 동안 유지되며 서비스의 각 계층에서 적용됩니다.

### <a name="data-access"></a>데이터 액세스
Microsoft 직원은 보안 추천 사항을 제공하고 잠재적 보안 위협을 조사하기 위해 Azure 서비스에서 수집하거나 분석한 정보에 액세스할 수 있습니다. 이러한 정보에는 프로세스 만들기 이벤트 및 기타 아티팩트가 포함되며, 실수로 컴퓨터의 고객 데이터 또는 개인 데이터가 포함될 수도 있습니다. 

Microsoft는 [Microsoft Online Services 데이터 보호 추록](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880)을 준수합니다. 즉, Microsoft는 고객 데이터를 사용하거나 광고 또는 유사한 상업적 목적에서 정보를 파생하지 않습니다. 필요에 따라 고객 데이터를 사용하여 해당 서비스를 제공하도록 호환할 수 있는 목적으로 Azure 서비스를 제공합니다. 고객 데이터에 대한 모든 권한을 유지합니다.

### <a name="data-use"></a>데이터 사용
Microsoft는 여러 테넌트에 발생하는 패턴 및 위협 인텔리전스를 사용하여 방지 및 검색 기능을 향상시킵니다. [개인정보처리방침](https://privacy.microsoft.com/privacystatement)에 설명된 개인정보처리방침 약정에 따라 수행합니다.

## <a name="manage-data-collection-from-machines"></a>컴퓨터에서 데이터 수집 관리
Azure에서 Defender for Cloud를 사용하는 경우 각 Azure 구독에 대해 데이터 수집이 활성화됩니다. Defender for Cloud에서 구독에 대한 데이터 수집을 사용할 수도 있습니다. 데이터 수집을 사용하면 Defender for Cloud는 지원되는 모든 기존 Azure 가상 머신과 생성된 모든 새 가상 머신에 Log Analytics 에이전트를 프로비전합니다.

Log Analytics 에이전트는 다양한 보안 관련 구성 및 이벤트를 검사하여 [ETW(Windows용 이벤트 추적)](/windows/win32/etw/event-tracing-portal)로 보냅니다. 또한 운영 체제는 컴퓨터를 실행하는 동안 이벤트 로그 이벤트를 발생시킵니다. 이러한 데이터의 예: 운영 체제 유형 및 버전, 운영 체제 로그(Windows 이벤트 로그), 프로세스 실행, 컴퓨터 이름, IP 주소, 로그인된 사용자 및 테넌트 ID입니다. Log Analytics 에이전트는 이벤트 로그 항목 및 ETW 추적을 읽고, 분석을 위해 작업 영역에 복사합니다. 또한 Log Analytics 에이전트를 사용하면 프로세스 만들기 이벤트 및 명령줄 감사를 수행할 수 있습니다.

Microsoft Defender for Cloud의 향상된 보안 기능을 사용하지 않는 경우 보안 정책의 가상 머신에서 데이터 수집을 사용하지 않도록 설정할 수도 있습니다. 향상된 보안 기능에서 보호하는 구독에는 데이터 수집이 필요합니다. VM 디스크 스냅샷 및 아티팩트 컬렉션은 데이터 수집이 사용하지 않도록 설정된 경우에도 여전히 사용하도록 설정됩니다.

컴퓨터에서 수집한 데이터가 저장되는 작업 영역과 지역을 지정할 수 있습니다. 기본값은 다음 표에 나와 있는 것처럼 가장 가까운 작업 영역에 있는 컴퓨터에서 수집된 데이터를 저장하는 것입니다.

| VM 지역                                      | 작업 영역 지역  |
|---------------------------------------------|----------------|
| 미국, 브라질, 남아프리카 공화국         | 미국  |
| Canada                                      | Canada         |
| 유럽(영국 제외)           | 유럽         |
| United Kingdom                              | United Kingdom |
| 아시아(인도, 대한민국, 일본, 중국 제외) | 아시아 태평양   |
| 한국                                       | 아시아 태평양   |
| 인도                                       | 인도          |
| 일본                                       | 일본          |
| 중국                                       | 중국          |
| 오스트레일리아                                   | 오스트레일리아      |
|                                             |                |

> [!NOTE]
> **Microsoft Defender for Storage** 는 관련 Azure 리소스의 위치에 따라 지역적으로 아티팩트를 저장합니다. [Microsoft Defender for Storage 소개](defender-for-storage-introduction.md)에서 자세히 알아보세요.


## <a name="data-consumption"></a>데이터 사용
고객은 다음 데이터 스트림에서 Defender for Cloud 관련 데이터에 액세스할 수 있습니다.

| STREAM                                                                                | 데이터 형식                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)                       | 모든 보안 경고, 승인된 Defender for Cloud [Just-In-Time](just-in-time-access-usage.md) 액세스 요청 및 [적응형 애플리케이션 제어](adaptive-application-controls.md)에서 생성된 모든 경고.|
| [Azure Monitor 로그](../azure-monitor/data-platform.md)                      | 모든 보안 경고.                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | 보안 경고, 보안 권장 사항, 취약성 평가 결과, 보안 점수 정보, 규정 준수 확인 상태 등.                                                                       |
| [Microsoft Defender for Cloud REST API](/rest/api/securitycenter/) | 보안 경고, 보안 권장 사항 등.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Defender for Cloud에서 데이터 관리하고 보호하는 방법을 알아봅니다. 

Microsoft Defender for Cloud에 대한 자세한 내용은 [Microsoft Defender for Cloud란?](defender-for-cloud-introduction.md)을 참조하세요
