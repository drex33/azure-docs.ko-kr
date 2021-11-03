---
title: Azure 개발/테스트 구독으로 안정성 및 성능 관리
description: 개발/테스트 구독을 통해 응용 프로그램에 안정성을 빌드 하세요.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: fe34eb3b5fff32ab59c72c90041b095a82806e9b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103709"
---
# <a name="reliability-management"></a>안정성 관리  

프로덕션 서비스는 개발/테스트 구독에 있지 않지만 Azure 개발/테스트 구독에서 다른 단계를 사용 하 여 프로덕션 환경에서 안정성을 보장할 수 있습니다.  

조직 개발/테스트 구독을 사용 하는 경우 다음을 수행 하는 방법을 결정 해야 합니다.  

- 컨트롤 데이터  
- 보안 및 액세스 제어  
- 프로덕션 시스템의 작동 시간 관리  

일반적으로 프로덕션-공유, QA, 통합, 스테이징 및 장애 조치 (failover) 전에 수행 하는 다양 한 배포 단계가 있습니다. 회사에서 이러한 단계를 정의 하는 방법에 따라 조직 개발/테스트 구독의 사용이 변경 될 수 있습니다.  

고객 지향 응용 프로그램과 같은 중요 업무용 서비스를 실행 하는 경우 개발/테스트 구독을 사용 하지 마세요. 개발/테스트 구독은 재정적 지원 SLA를 수행 하지 않습니다. 이러한 구독은 사전 프로덕션 테스트 및 개발을 위한 것입니다.  

## <a name="site-reliability-engineering-sre"></a>SRE(사이트 안정성 엔지니어링)  

안정성 엔지니어링 및 관리에 대 한 자세한 내용은 사이트 안정성 관리-조직 sustainably 시스템, 서비스 및 제품에서 적절 한 안정성을 달성할 수 있도록 하는 엔지니어링 분야를 고려 합니다.  

sre와 DevOps은 필드에서 아직 논의 되 고 있습니다. 몇 가지 차이점에 대해 크게 합의 된 일부는 다음과 같습니다.  

- SRE는 안정성에 초점을 맞춘 엔지니어링 분야입니다. DevOps은 개발 및 운영 조직과 관련 된 사일로를 분리 하기 위해 소개에서 가져온 문화권 이동입니다.  
- SRE는 "SRE (사이트 안정성 엔지니어)"와 마찬가지로 역할의 이름일 수 있습니다. DevOps 수 없습니다.  
- SRE는 규정을 갖습니다. DevOps은 의도적으로 그렇지 않습니다. 지속적인 통합/지속적인 업데이트 및 Agile 원리를 거의 유니버설 하 게 채택 하는 것이 가장 가까운 DevOps입니다.  

SRE의 방법에 대 한 자세한 내용을 보려면 다음 링크를 확인 하세요.  

- [컨텍스트의 SRE](/learn/modules/intro-to-site-reliability-engineering/3-sre-in-context.md)  
- [키 SRE 원리 및 방법: 고리가 만들어집니다 주기](/learn/modules/intro-to-site-reliability-engineering/4-key-principles-1-virtuous-cycles.md)  
- [키 SRE 원리 및 사례: 사용자 측](/learn/modules/intro-to-site-reliability-engineering/5-key-principles-2-human-side-of-sre.md)  
- [SRE 시작](/learn/modules/intro-to-site-reliability-engineering/6-getting-started.md)  

## <a name="service-level-agreements"></a>서비스 수준 약정  

Enterprise 개발/테스트는 응용 프로그램 개발 및 테스트에만 사용 됩니다. 구독 사용은 재정적 지원 SLA를 수행 하지 않습니다.  

## <a name="learn-to-use-different-types-of-devtest-subscriptions"></a>다양 한 형식의 개발/테스트 구독을 사용 하는 방법을 알아봅니다.  

Visual Studio 구독자, [Enterprise 개발/테스트 구독](https://azure.microsoft.com/offers/ms-azr-0148p/)또는 [종량제 개발/테스트 구독](https://azure.microsoft.com/offers/ms-azr-0023p/) (PAYG) [에 대 한 월간 Azure 크레딧이](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)필요한 지 여부에 관계 없이 개인 또는 팀에 대해 작동 하는 제품을 쉽게 찾을 수 있습니다.  

## <a name="managing-individual-credit-subscriptions"></a>개별 크레딧 구독 관리  

Visual Studio Azure 크레딧은 개별 개발/테스트 및 내부 루프 개발을 위한 개별 혜택입니다. 개발자 간에 크레딧을 풀링할 수 없습니다. 신용 구독은 여전히 Azure 구독 이지만 특정 Azure 제품입니다. 다른 Azure 구독을 관리 하는 것과 동일한 방식으로 신용 구독을 관리 하 여 그룹 및 팀 내에서 작업을 수행할 수 있습니다. 신용 카드를 사용 하 여 개별 지출 한도를 제거 하거나, 엔터프라이즈 개발/테스트 구독이 회사에서 선택한 조달 방법으로 이동 하는 경우  

개발자 내부 루프 작업은 종종 크레딧을 사용 하지만 종 량 제을 포함 하 여 엔터프라이즈 또는 조직 Azure 개발/테스트 구독으로 전환 합니다. 이러한 방식으로 DevOps 프로세스를 진행할 때 개별 크레딧 구독과 내부 루프를 사용할 수 있습니다. DevOps 외부 루프에서 비프로덕션 대상은 enterprise Dev/Test-prod는 prod로 이동 합니다.  

신용 구독, 엔터프라이즈 개발/테스트 구독 및 PAYG 구독을 관리 하 고 각각 고유한 계층을 갖는 [관리 그룹](../../governance/management-groups/how-to/protect-resource-hierarchy.md) 을 사용 하 여 개발자를 분할 합니다.  

## <a name="using-your-organization-azure-devtest-offers"></a>조직 Azure 개발/테스트 제품 사용  

Azure 개발/테스트 구독 조직이 필요한 경우 두 가지 제품을 선택할 수 있습니다.  

- [종 량 제 (PAYG) 개발/테스트 (0023P)](https://azure.microsoft.com/offers/ms-azr-0023p/-)  
- [Enterprise 개발/테스트 (Ms-azr-0148p)](https://azure.microsoft.com/offers/ms-azr-0148p/)  

각각은 고유한 할인 집합과 함께 제공 되며 Visual Studio 구독이 필요 합니다.  

각 구독 제품을 사용 하면 미리 구성 된 가상 컴퓨터를 사용 하 여 클라우드에서 개발/테스트 환경으로 팀을 가동 하 고 실행할 수 있습니다. 여러 Azure 구독을 만들고 한 계정에서 관리 합니다. 격리 된 환경과 다른 프로젝트 또는 팀에 대 한 별도의 청구서를 유지 관리할 수 있습니다.  

Enterprise 개발/테스트 구독에는 EA (기업 계약)가 필요 합니다. 종량제 개발/테스트 구독은 EA가 필요 하지 않지만 기업 계약 계정과 함께 사용할 수 있습니다.  

## <a name="why-would-i-use-payg-offers-vs-enterprise-devtest-offers"></a>PAYG 제공 vs Enterprise 개발/테스트 혜택을 사용 하는 이유는 무엇 인가요?  

PAYG 개발/테스트 제안은 Visual Studio 구독자로 사용 하기에 적합 한 것일 수 있습니다. 개별 사용에 대 한 크레딧 구독과 달리 PAYG 제안은 팀 개발에 적합 하며 한 구독 내에서 여러 사용자를 가질 수 있습니다. PAYG 개발/테스트 제안은 다음과 같은 경우에 적합 합니다.  

- 기업 계약이 없습니다. 이 경우 Visual Studio 라이선스를 사용 하 여 PAYG 계정만 만들 수 있습니다.  
- 기업 계약을 만드는 중이지만 조직의 계약을 사용 하지 않는 구독을 설정 해야 합니다. 고유한 구독이 필요 하거나 프로젝트 또는 팀에 대해 별도로 청구 되는 격리 된 환경을 만드는 고유한 프로젝트가 있을 수 있습니다.  
- Id를 격리 된 상태로 유지 하는 것이 좋습니다. 데이터, 리소스 및 앱에 대 한 액세스를 보호 하기 위해 특정 id가 다른 id와 별도로 유지 되어야 할 수도 있습니다.  
