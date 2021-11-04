---
title: Azure 개발/테스트 구독 관리 및 모니터링
description: Azure의 클라우드 환경의 유연성을 사용 하 여 Azure 개발/테스트 구독을 관리 하세요. 응용 프로그램 및 서비스의 가용성과 성능을 극대화 하는 데 도움이 되는 Azure Monitor에 대해서도 설명 합니다.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 22fa6c747d7c15cebe1d36228965c3119fea25ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102951"
---
# <a name="managing-azure-devtest-subscriptions"></a>Azure DevTest 구독 관리

Azure 개발/테스트 구독을 관리 하는 것이 중요 합니다. 비용을 관리 하 고, 프로덕션 단계를 모니터링 하 고, 배포 하는 리소스와 구성 하는 프로세스는 구독 관리의 최상위 우선 순위입니다.  

Azure의 클라우드 환경에서는 비용 및 워크 로드를 유연 하 게 관리할 수 있습니다. 온-프레미스 관리를 통해 비용을 관리 하는 기능을 사용할 수 있습니다.  

구독 및 리소스 그룹 수준에 포함 된 서비스는 비용이 0 이며 리소스 자체에만 비용이 듭니다.  

![Azure 조직 및 거 버 넌 스 다이어그램](media/how-to-manage-monitor-devtest/orgs-and-governance.png "Azure 조직 및 거 버 넌 스.")

운영 비용 관점에서 관리 하는 경우 사용한 만큼만 요금을 지불 하면 됩니다. 배포 하는 동안 비용을 관리 하는 데 도움이 되는 Azure 개발/테스트 구독에는 몇 가지 도구가 있습니다.  

## <a name="monitoring-through-a-different-lens"></a>다른 렌즈를 통한 모니터링

[Azure Monitor](../../azure-monitor/overview.md) 는 응용 프로그램 및 서비스에 대 한 가용성 및 성능을 최대화 하는 데 도움이 됩니다. 클라우드 및 온-프레미스 환경에서 데이터를 수집, 분석 및 작동 하기 위한 종합적인 솔루션을 제공 합니다. 응용 프로그램의 작동 방식을 이해 합니다. Azure Monitor는 응용 프로그램 및 응용 프로그램에 영향을 주는 리소스에 영향을 주는 문제를 사전에 식별 합니다.  

Azure 내에서 모니터링을 사용 하 여 출시 시간을 단축 하 고 프로덕션 서비스에서 성능 데이터를 확인 합니다. 메트릭, 로그 및 추적을 집계 하 고 분석할 수 있습니다. 모니터링을 통해 경고를 발생 시키고 알림을 보내거나 자동화 된 솔루션을 호출할 수도 있습니다.  

Azure Monitor를 사용 하 여 개발/테스트 혜택을 통해 앱의 시간을 시장에 최적화 하 고 해당 응용 프로그램을 제공할 수 있습니다.  

모니터링을 사용 하면 새 응용 프로그램 및 기존 응용 프로그램을 사용 하 여 개발/테스트 혜택을 최대화할 수 있습니다.  

- Net New 녹색 필드 응용 프로그램을 사용 하는 사전 프로덕션 – 새 앱을 사용 하면 프로덕션 환경에서 사용 하는 사전 프로덕션 환경에서 log analytics 또는 스마트 경고를 사용 하 여 사용자 지정 메트릭을 만들고 사용할 수 있습니다. 모니터링을 사용 하 여 프로덕션 서비스에 대 한 성능 데이터를 구체화 합니다.  
- 기존 응용 프로그램을 사용 하 여 프로덕션 후 – 새 기능을 배포 하거나 API를 사용 하 여 기존 앱에 새 기능을 추가 하는 경우 사전 프로덕션에서이 기능을 배포 하 고 모니터링을 조정 하 여 초기에 올바른 데이터 피드를 확인할 수 있습니다. 새 기능의 사전 프로덕션에서 추적 기능을 사용 하면 가시성을 명확히 알 수 있으며 프로덕션 후에 전체 모니터링 시스템과이 모니터링을 혼합할 수 있습니다. 최신 성능 데이터를 기존 로그와 통합 하 여 모니터링이 사용 되도록 합니다.  
- 프로덕션이 아닌 배포의 여러 단계를 모니터링 하는 것은 프로덕션 중에 모니터링 합니다. 프로덕션 및 사후 프로덕션에서 비용을 관리 하 고 지출을 분석 합니다.  

## <a name="cost-management"></a>Cost Management

[Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) 를 사용 하면 비즈니스 작업의 기술적 성능을 크게 향상 시킬 수 있습니다. 또한 조직 자산을 관리하는 데 필요한 비용과 오버헤드를 줄일 수 있습니다.  

모니터링을 사용 하면 서비스를 배포 하기 전에 사전 프로덕션 환경에서 비용 분석 도구를 사용 하 여 프로덕션 비용을 예측 하 고 잠재적으로 비용을 절감할 수 있도록 현재 사전 프로덕션 계산에 대 한 분석을 실행할 수 있습니다.  

## <a name="performance-management"></a>성능 관리

모니터링 및 비용 관리 외에도, 예상 되는 워크 로드에 대 한 사전 프로덕션 계산을 성능 테스트 합니다.  

사전 프로덕션에서는 부하에 따라 확장 하도록 크기 조정을 설정할 수 있습니다. 부하 상태에서 개발/테스트 환경에서 응용 프로그램을 테스트 하면 더 나은 비용 분석 및 모니터링 수치를 얻을 수 있습니다. 더 높거나 낮은 규모로 시작 해야 하는지 여부를 보다 잘 이해할 수 있습니다.  

Load 및 감 분석은 사전 프로덕션에 다른 데이터를 제공 하므로 출시 시간 및 응용 프로그램 제공을 계속 최적화할 수 있습니다.  

응용 프로그램 또는 서비스를 사용 하 여 로드 및 감 테스트를 수행할 때 확장 또는 축소 하는 방법은 워크 로드에 따라 달라 집니다. Azure에서 앱의 크기를 조정 하는 방법에 대해 자세히 알아볼 수 있습니다.  

- [Azure App Service에서 앱 강화](../../app-service/manage-scale-up.md)  
- [Azure에서 자동 스케일링 시작](../../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json)  

페이지 보기, 애플리케이션 요청 및 예외를 포함하여 자세한 정보를 수집하려면 [Application Insights](../../azure-monitor/app/app-insights-overview.md)를 사용하여 애플리케이션에 대한 모니터링을 사용하도록 설정합니다.  

## <a name="azure-automation"></a>Azure Automation

Azure [automation](../../automation/automation-intro.md) 은 azure 및 비 azure 환경에서 일관 된 관리를 지 원하는 클라우드 기반 자동화 및 구성 서비스를 제공 합니다. 이 도구를 사용 하면 작업 및 리소스의 배포, 작업 및 서비스 해제 중에 제어할 수 있습니다. Azure Automation 항상 켜져 있습니다. 기존 리소스와 함께 작동 합니다. Azure Automation를 사용 하 여 요청 시 리소스 또는 구독을 만들 수 있습니다. 사용한 양만큼만 요금을 지급합니다.  

예: 개발/테스트 프로덕션 배포를 수행 하는 경우 모든 리소스와 단계를 항상 실행 해야 합니다. 다른 경우에는 1 년에 한 번만 업데이트 하 고 실행 해야 합니다.  

이 시나리오에서는 Azure Automation 중요 합니다. 앱 개발의 새로운 라운드를 진행 하 고 첫 번째 끌어오기 요청 (PR)을 제출 하면 자동화 작업을 시작할 수 있습니다. 작업은 ARM (Azure Resource Manager) 템플릿을 통해 인프라를 코드로 배포 하 여 사전 프로덕션 중에 Azure 개발/테스트 구독에 모든 리소스를 만듭니다.  

## <a name="azure-resource-manager"></a>Azure Resource Manager

[ARM (Azure Resource Manager) 템플릿은](../../azure-resource-manager/templates/overview.md) 인프라를 Azure 솔루션에 대 한 코드로 구현 합니다. 템플릿은 프로젝트의 인프라 및 구성을 정의 합니다. 배포를 자동화할 수 있습니다.  

사전 프로덕션 환경을 업데이트 하 고 비용을 추적 하려는 횟수 만큼 구성을 배포할 수 있습니다. Azure Automation를 사용 하 여 필요에 따라 ARM 템플릿을 실행 하 고 삭제할 수 있습니다.  

1 년에 두 번만 서비스 또는 리소스를 업데이트 해야 하는 경우 DevOps 도구를 사용 하 여 ARM 템플릿을 배포 합니다. Automation 작업에서 리소스를 해제 하 고 필요에 따라 다시 배포할 수 있습니다.  
