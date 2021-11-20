---
title: Azure 개발/테스트 구독을 통해 안정성 및 성능 관리
description: 개발/테스트 구독을 통해 애플리케이션에 안정성을 구축합니다.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 32d748b5ef67aab8db82c9d6cffe46ecf4fbce01
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132863331"
---
# <a name="reliability-management"></a>안정성 관리  

프로덕션 서비스는 개발/테스트 구독에 있지 않지만 Azure 개발/테스트 구독의 다른 단계를 사용하여 프로덕션에서 안정성을 보장할 수 있습니다.  

조직 개발/테스트 구독을 사용하는 경우 다음을 수행할 방법을 결정해야 합니다.  

- 컨트롤 데이터  
- 보안 및 액세스 제어  
- 해당 프로덕션 시스템의 작동 시간 관리  

일반적으로 프로덕션 전에 서로 다른 배포 단계(공유, QA, 통합, 스테이징 및 장애 조치)가 있습니다. 회사에서 이러한 단계를 정의하는 방법에 따라 조직의 개발/테스트 구독 사용이 변경 될 수 있습니다.  

고객 관련 애플리케이션과 같은 중요 업무용 서비스를 실행하는 경우 개발/테스트 구독을 사용하지 마세요. 개발/테스트 구독은 재무적으로 백업되는 SLA를 전달하지 않습니다. 이러한 구독은 사전 프로덕션 테스트 및 개발을 위한 것입니다.  

## <a name="site-reliability-engineering-sre"></a>SRE(사이트 안정성 엔지니어링)  

안정성 엔지니어링 및 관리에 대해 자세히 알아보려면 조직에서 시스템, 서비스 및 제품에서 적절한 안정성을 지속 가능하게 달성할 수 있도록 도와주는 엔지니어링 분야인 사이트 안정성 관리를 고려합니다.  

SRE와 DevOps 차이는 여전히 필드에서 논의 중입니다. 대체로 합의된 차이점 중 일부는 다음과 같습니다.  

- SRE는 안정성에 초점을 맞춘 엔지니어링 분야입니다. DevOps 개발 및 운영 조직과 관련된 사일로를 세분화하기 위해 등장한 문화적 이동입니다.  
- SRE는 "I'm a site reliability engineer (SRE)"에서와 같이 역할의 이름이 될 수 있습니다. DevOps 수 없습니다.  
- SRE는 규범적인 경향이 있습니다. DevOps 의도적으로 그렇지 않습니다. 지속적인 통합/지속적인 업데이트의 거의 보편적인 채택과 Agile 원칙은 가장 가까운 DevOps 옵니다.  

SRE의 사례에 대해 자세히 알아보려면 다음 링크를 확인하세요.  

- [컨텍스트의 SRE](/learn/modules/intro-to-site-reliability-engineering/3-sre-in-context)  
- [주요 SRE 원칙 및 사례: 선순환](/learn/modules/intro-to-site-reliability-engineering/4-key-principles-1-virtuous-cycles)  
- [주요 SRE 원칙 및 사례: SRE의 인간 쪽](/learn/modules/intro-to-site-reliability-engineering/5-key-principles-2-human-side-of-sre)  
- [SRE를 시작](/learn/modules/intro-to-site-reliability-engineering/6-getting-started)  

## <a name="service-level-agreements"></a>서비스 수준 약정  

Enterprise 개발/테스트는 애플리케이션의 개발 및 테스트 전용입니다. 구독을 사용하면 재무적으로 백업되는 SLA가 전달되지 않습니다.  

## <a name="learn-to-use-different-types-of-devtest-subscriptions"></a>다양한 유형의 개발/테스트 구독을 사용하는 방법 알아보기  

[Visual Studio 구독자, Enterprise](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) [개발/테스트 구독](https://azure.microsoft.com/offers/ms-azr-0148p/)또는 PAYG(종량제 개발/테스트 [구독)에](https://azure.microsoft.com/offers/ms-azr-0023p/) 대한 월간 Azure 크레딧이 필요한 경우 개인 또는 팀에 적합한 제안을 쉽게 찾을 수 있습니다.  

## <a name="managing-individual-credit-subscriptions"></a>개별 크레딧 구독 관리  

Visual Studio Azure 크레딧은 개별 개발/테스트 및 내부 루프 개발에 대한 개별 혜택입니다. 개발자 간에 크레딧을 풀할 수 없습니다. 크레딧 구독은 여전히 Azure 구독이지만 특정 Azure 제품입니다. 그룹 및 팀 내에서 작업할 수 있도록 다른 Azure 구독을 관리하는 것과 동일한 방식으로 신용 구독을 관리합니다. 신용 카드로 개별 지출 한도를 제거하거나 엔터프라이즈 개발/테스트 구독이 회사에서 선택한 조달 방법으로 가는 경우 제거할 수 있습니다.  

개발자 내부 루프 활동은 종종 크레딧을 사용하지만 종량제 등 엔터프라이즈 또는 조직 Azure 개발/테스트 구독으로 전환합니다. 이러한 방식으로 DevOps 프로세스를 수행하면 개별 크레딧 구독을 내부 루프할 수 있습니다. DevOps 외부 루프에서 비프로덕션 대상은 엔터프라이즈 개발/테스트에 있습니다. prod는 prod로 이동합니다.  

크레딧 구독, 엔터프라이즈 개발/테스트 구독 및 PAYG 구독을 관리하고 각각 고유한 계층이 있는 [관리 그룹을](../../governance/management-groups/how-to/protect-resource-hierarchy.md) 사용하여 개발자를 분할합니다.  

## <a name="using-your-organization-azure-devtest-offers"></a>조직 Azure 개발/테스트 제안 사용  

조직 Azure 개발/테스트 구독이 필요한 경우 두 가지 제안 중에서 선택할 수 있습니다.  

- [PAYG(종량제) 개발/테스트(0023P)](https://azure.microsoft.com/offers/ms-azr-0023p/)  
- [Enterprise 개발/테스트(0148P)](https://azure.microsoft.com/offers/ms-azr-0148p/)  

각 할인에는 자체 할인이 제공되며 Visual Studio 구독이 필요합니다.  

각 구독 제안을 사용하면 미리 구성된 가상 머신을 사용하여 클라우드의 개발/테스트 환경에서 팀을 가동하고 실행할 수 있습니다. 여러 Azure 구독을 만들고 한 계정에서 관리합니다. 격리된 환경과 다른 프로젝트 또는 팀에 대한 별도의 청구서를 유지할 수 있습니다.  

Enterprise 개발/테스트 구독에는 EA(기업계약)가 필요합니다. 종량제 개발/테스트 구독에는 EA가 필요하지 않지만 기업계약 계정과 함께 사용할 수 있습니다.  

## <a name="why-would-i-use-payg-offers-vs-enterprise-devtest-offers"></a>PAYG 제안과 Enterprise 개발/테스트 제안을 사용하는 이유는 무엇인가요?  

PAYG 개발/테스트 제안은 Visual Studio 구독자로 사용하기에 적합할 수 있습니다. 개별 사용에 대한 크레딧 구독과 달리 PAYG 제안은 팀 개발에 적합하며 한 구독 내에서 여러 사용자를 가질 수 있습니다. 다음과 같은 경우 PAYG 개발/테스트 제안이 적합할 수 있습니다.  

- 기업계약이 없습니다. 이 경우 Visual Studio 라이선스를 사용하여 PAYG 계정만 만들 수 있습니다.  
- 기업계약을 만들지만 조직의 계약을 사용하지 않는 구독을 설정해야 합니다. 고유한 구독이 필요한 고유한 프로젝트가 있거나 프로젝트 또는 팀에 대해 별도로 청구되는 격리된 환경을 만들 수 있습니다.  
- ID를 격리된 상태로 유지하는 것이 좋습니다. 데이터, 리소스 및 앱에 대한 액세스를 보호하기 위해 특정 ID를 다른 ID와 별도로 유지해야 할 수 있습니다.  
