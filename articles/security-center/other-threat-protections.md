---
title: Azure Security Center의 추가 위협 방지
description: Azure Security Center에서 Azure Defender 이외에 사용 가능한 다른 위협 방지에 대해 알아보기
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/12/2021
ms.author: memildin
ms.openlocfilehash: c2bed032510e278663a1d1d9f10043eaa6e9b0db
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858199"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Azure Security Center의 추가 위협 방지
또한 Azure Security Center는 기본 제공 [Azure Defender 보호](azure-defender.md)뿐만 아니라 다음과 같은 위협 방지 기능도 제공합니다.

> [!TIP]
> Security Center의 위협 방지 기능을 사용하도록 설정하려면 해당 워크로드가 포함된 구독에서 Azure Defender를 사용하도록 설정해야 합니다.

## <a name="threat-protection-for-azure-network-layer"></a>Azure 네트워크 계층에 대한 위협 방지 <a name="network-layer"></a>
Security Center 네트워크 계층 분석은 Azure 핵심 라우터에서 수집하는 패킷 헤더인 [IPFIX 데이터](https://en.wikipedia.org/wiki/IP_Flow_Information_Export) 샘플을 기반으로 합니다. 이 데이터 피드를 기반으로 하여 Security Center에서 기계 학습 모델을 사용하여 악성 트래픽 활동을 식별하고 플래그를 지정합니다. 또한 Security Center에서 Microsoft 위협 인텔리전스 데이터베이스를 사용하여 IP 주소를 보강합니다.

일부 네트워크 구성에서는 Security Center에서 의심스러운 네트워크 활동에 대한 경고를 생성하지 않도록 제한합니다. Security Center에서 네트워크 경고를 생성하려면 다음을 확인합니다.
- 가상 머신에서 공용 IP 주소를 사용하거나 가상 머신이 공용 IP 주소가 있는 부하 분산 장치에 있습니다.
- 가상 머신의 네트워크 송신 트래픽은 외부 IDS 솔루션에서 차단되지 않습니다.

Azure 네트워크 계층 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-azurenetlayer)를 참조하세요.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB용 위협 방지(미리 보기)<a name="cosmos-db"></a>

Azure Cosmos DB 경고는 Azure Cosmos DB 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도로 인해 생성됩니다.

자세한 내용은 다음을 참조하세요.

* [Azure Cosmos DB용 지능형 위협 방지(미리 보기)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB에 대한 위협 방지 경고 목록(미리 보기)](alerts-reference.md#alerts-azurecosmos)

## <a name="display-azure-security-center-recommendations-in-microsoft-cloud-app-security"></a>Microsoft Cloud App Security에서 Azure Security Center 권장 사항 표시 <a name="azure-mcas"></a>

Microsoft Cloud App Security는 로그 수집, API 커넥터 및 역방향 프록시를 포함한 다양한 배포 모드를 지원하는 CASB(클라우드 액세스 보안 브로커)입니다. 다양한 가시성, 데이터 이동 제어 및 정교한 분석을 제공하여 모든 Microsoft 및 타사 클라우드 서비스에서의 사이버 위협을 식별하고 대처합니다.

Microsoft Cloud App Security를 사용하도록 설정하고 Security Center의 설정 내에서 통합을 선택한 경우 Security Center의 강화 권장 사항은 추가 구성 없이 Cloud App Security에 표시됩니다.

> [!NOTE]
> Security Center는 보안과 관련된 고객 데이터를 리소스와 동일한 지역에 저장합니다. Microsoft에서 Security Center를 해당 리소스의 지역에 아직 배포하지 않은 경우 데이터는 미국에 저장됩니다. Microsoft Cloud App Security를 사용하도록 설정되는 경우 이 정보는 Microsoft Cloud App Security의 지리적 위치 규칙에 따라 저장됩니다. 자세한 내용은 [비지역 서비스에 대한 데이터 저장](https://azuredatacentermap.azurewebsites.net/)을 참조하세요.


## <a name="stream-security-alerts-from-other-microsoft-services"></a>다른 Microsoft 서비스의 보안 경고 스트림 <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-security-center"></a>Security Center에서 Azure WAF 경고 표시 <a name="azure-waf"></a>

Azure Application Gateway는 일반적인 악용 및 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호하는 WAF(웹 애플리케이션 방화벽)를 제공합니다.

일반적으로 알려진 취약성을 악용하여 웹 애플리케이션을 공격하는 악의적인 사례가 점점 늘어나고 있습니다. Application Gateway WAF는 Open Web Application Security Project의 핵심 규칙 집합 3.0 또는 2.2.9를 기반으로 합니다. WAF는 새로운 취약성으로부터 보호하기 위해 자동으로 업데이트됩니다. 

Azure WAF에 대한 라이선스가 있는 경우 추가 구성 없이 WAF 경고가 Security Center로 스트림됩니다. WAF에서 생성되는 경고에 대한 자세한 내용은 [웹 애플리케이션 방화벽 CRS 규칙 그룹 및 규칙](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)을 참조하세요.


### <a name="display-azure-ddos-protection-alerts-in-security-center"></a>Security Center에서 Azure DDoS Protection 경고 표시 <a name="azure-ddos"></a>

DDoS(분산 서비스 거부) 공격은 쉽게 실행되는 것으로 알려져 있습니다. 특히 애플리케이션을 클라우드로 이동하는 경우 매우 중요한 보안 문제가 됩니다. DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 생성합니다. DDoS 공격은 인터넷을 통해 연결할 수 있는 모든 엔드포인트를 대상으로 할 수 있습니다.

DDoS 공격을 방어하려면 Azure DDoS Protection 라이선스를 구매하고 애플리케이션 디자인 모범 사례를 따릅니다. DDoS Protection은 다양한 서비스 계층을 제공합니다. 자세한 내용은 [Azure DDoS Protection 개요](../ddos-protection/ddos-protection-overview.md)를 참조하세요.

Azure DDoS Protection을 사용하도록 설정한 경우 추가 구성 없이 DDoS 경고가 Security Center로 스트리밍됩니다. DDoS Protection에서 생성되는 경고에 대한 자세한 내용은 [경고 참조 테이블](alerts-reference.md#alerts-azureddos)을 참조하세요.


## <a name="next-steps"></a>다음 단계
이러한 위협 방지 기능의 보안 경고에 대한 자세한 내용은 다음 문서를 참조하세요.

* [모든 Azure Security Center 경고 참조 표](alerts-reference.md)
* [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
* [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
* [Security Center 데이터 연속 내보내기](continuous-export.md)
