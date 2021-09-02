---
title: Azure DDoS Protection 표준 개요
description: 애플리케이션 설계 모범 사례와 결합된 Azure DDoS Protection 표준이 DDoS 공격에 대한 방어 기능을 제공하는 방법을 알아보세요.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 2cee2aed6922a5a5d3f457c9f14fb54c07b18cd3
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310490"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection 표준 개요

DDoS(배포된 서비스 거부) 공격은 고객이 애플리케이션을 클라우드로 전환하게 만드는 가장 큰 가용성 및 보안 문제 중 일부입니다. DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 생성합니다. 인터넷을 통해 공개적으로 도달 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다.

Azure의 모든 속성은 추가 비용 없이 Azure의 인프라 DDoS(기본) Protection을 통해 보호됩니다. 전역 배포 Azure 네트워크의 규모 및 용량은 항상 작동하는 트래픽 모니터링 및 실시간 위험 완화를 통해 일반적인 네트워크 레이어 공격을 방어합니다. DDoS Protection 기본에는 사용자 구성 또는 애플리케이션 변경이 필요하지 않습니다. DDoS Protection 기본을 통해 Azure DNS 같은 PaaS 서비스를 포함한 모든 Azure 서비스를 보호할 수 있습니다.

애플리케이션 설계 모범 사례와 결합된 Azure DDoS Protection 표준은 DDoS 공격으로부터 방어하기 위해 향상된 DDoS 완화 기능을 제공합니다. 가상 네트워크에서 특정 Azure 리소스를 보호하도록 자동으로 조정됩니다. 보호는 새 가상 네트워크 또는 기존 가상 네트워크에서 간단하게 설정할 수 있으며 애플리케이션 또는 리소스를 변경할 필요가 없습니다. 로깅, 경고 및 원격 분석을 포함하여 기본 서비스에 비해 여러 가지 장점이 있습니다. 

![Azure DDoS Protection Service 비교](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS 보호는 고객 데이터를 저장하지 않습니다.

## <a name="features"></a>기능

- **네이티브 플랫폼 통합:** 기본적으로 Azure에 통합됩니다. Azure Portal을 통해 구성을 포함합니다. DDoS Protection 표준은 사용자의 리소스 및 리소스 구성을 인식합니다.
- **턴키 보호:** 간소화된 구성으로 DDoS Protection 표준이 활성화되는 즉시 가상 네트워크의 모든 리소스를 바로 보호할 수 있습니다. 작업 또는 사용자 정의가 필요하지 않습니다. 
- **트래픽 항시 모니터링:** DDoS 공격의 징후를 찾기 위해 애플리케이션 트래픽 패턴이 24시간 매일(주 7일) 모니터링됩니다. DDoS Protection 표준은 공격이 감지되는 즉시 자동으로 공격을 완화시킵니다.
- **적응형 튜닝:** 지능형 트래픽 프로파일링으로 시간별 애플리케이션 트래픽을 학습하고, 사용자의 서비스에 가장 적합한 프로필을 업데이트합니다. 트래픽이 시간이 지남에 따라 변경되면서 프로필이 조정됩니다.
- **다중 계층화된 보호:** WAF(웹 애플리케이션 방화벽)를 사용하여 배포된 경우 DDoS Protection 표준은 네트워크 계층(Azure DDoS Protection 표준에서 제공하는 계층 3 및 4)과 애플리케이션 계층(WAF에서 제공하는 계층 7)에서 모두 보호합니다. WAF 제품에는 Azure [pplication Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)와 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)에서 사용할 수 있는 타사 웹 애플리케이션 방화벽 제품이 포함됩니다.
- **광범위한 완화 규모:** 가장 큰 규모로 알려진 DDoS 공격으로부터 시스템을 보호할 수 있는 글로벌 역량으로 60가지 공격을 완화할 수 있습니다.
- **공격 분석:** 공격 진행 중에 5분 단위로 세부 보고서를 가져오고, 공격이 종료된 후에는 전체 요약을 가져옵니다. 스트림 완화 흐름은 공격 진행 중에 거의 실시간 모니터링을 위해 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) 또는 오프라인 SIEM(보안 정보 및 이벤트 관리) 시스템에 기록됩니다.
- **공격 메트릭:** Azure Monitor를 통해 각 공격으로부터 요약된 메트릭에 액세스할 수 있습니다.
- **공격 경고:** 기본 제공되는 공격 메트릭을 사용하여 공격 시작 및 중지 시와 공격이 진행되는 동안 경고를 구성할 수 있습니다. 경고는 Microsoft Azure Monitor 로그, Splunk, Azure Storage, 이메일 및 Azure Portal과 같은 작업 소프트웨어에 통합됩니다.
- **DDoS 신속 대응**: 공격 조사 및 분석에 관한 도움을 받으려면 DRR(DDoS Protectionㅍ신속 대응) 팀에 참여하세요. 자세히 알아보려면 [DDoS 신속 대응](ddos-rapid-response.md)을 참조하세요.
- **비용 보장:** 문서화된 DDoS 공격의 결과로 발생하는 리소스 비용에 대한 데이터 전송 및 애플리케이션 확장 서비스 크레딧을 받습니다.

## <a name="pricing"></a>가격 책정

DDoS 보호 계획에는 최대 100개의 공용 IP 주소를 포함하는 월 $2,944의 고정 요금이 부과됩니다. 추가 리소스에 대한 보호에는 리소스당 월 $30가 추가로 부과됩니다.

테넌트에서 단일 DDoS 보호 계획을 여러 구독에서 사용할 수 있으므로 둘 이상의 DDoS 보호 계획을 만들 필요가 없습니다.

Azure DDoS Protection 표준 가격 책정에 대해 자세히 알아보려면 [Azure DDoS Protection 표준 가격 책정](https://azure.microsoft.com/pricing/details/ddos-protection/)을 참조하세요.

## <a name="reference-architectures"></a>참조 아키텍처

DDoS Protection 표준은 [가상 네트워크에 배포되는 서비스](../virtual-network/virtual-network-for-azure-services.md)를 위해 설계되었습니다. 다른 서비스에는 기본 DDoS Protection 기본 서비스가 적용됩니다. 지원되는 아키텍처에 대해 자세히 알아보려면 [DDoS Protection 참조 아키텍처](./ddos-protection-reference-architectures.md)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [DDoS Protection 계획 만들기](manage-ddos-protection.md)