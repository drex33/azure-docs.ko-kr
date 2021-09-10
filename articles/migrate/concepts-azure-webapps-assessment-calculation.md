---
title: Azure Migrate 검색 및 평가 도구의 Azure App Service 평가
description: Azure Migrate 검색 및 평가 도구의 Azure App Service 평가에 대해 알아보기
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: ec83b7a8de449a73dc227941f864ac7cd8495e68
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566570"
---
# <a name="assessment-overview-migrate-to-azure-app-service"></a>평가 개요(Azure App Service로 마이그레이션)

이 문서에서는 [Azure Migrate: 검색 및 평가 도구](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)를 사용하여 VMware 환경에서 Azure App Service로 온-프레미스 ASP.NET 웹앱 마이그레이션에 대한 평가를 대략적으로 설명합니다.

## <a name="whats-an-assessment"></a>평가란 무엇인가요?
검색 및 평가 도구를 사용한 평가는 데이터의 특정 시점 스냅샷으로, 온-프레미스 서버, 데이터베이스, 웹앱을 Azure로 호스트하기 위한 준비 상태를 측정하고 비용 정보를 제공합니다.

## <a name="types-of-assessments"></a>평가 형식

Azure Migrate: 검색 및 평가 도구를 사용하여 만들 수 있는 평가에는 네 가지 유형이 있습니다.

**평가 유형** | **세부 정보**
--- | ---
**Azure VM** | 온-프레미스 서버를 Azure 가상 머신으로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 형식을 사용하여 Azure VM으로 마이그레이션하기 위한 [VMware](how-to-set-up-appliance-vmware.md) 및 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 환경과 [물리적 서버](how-to-set-up-appliance-physical.md)에서 의 온-프레미스 서버를 평가할 수 있습니다.
**Azure SQL** | 온-프레미스 SQL Server를 VMware 환경에서 Azure SQL Database 또는 Azure SQL Managed Instance로 마이그레이션하기 위한 평가.
**Azure App Service** | IIS 웹 서버에서 실행되는 온-프레미스 ASP.NET 웹앱을 VMware 환경에서 Azure App Service로 마이그레이션하기 위한 평가입니다.
**AVS(Azure VMware 솔루션)** | 온-프레미스 서버를 [AVS(Azure VMware 솔루션)](../azure-vmware/introduction.md)로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용하여 AVS(Azure VMware 솔루션)로 마이그레이션하기 위한 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md)을 평가할 수 있습니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

Azure App Service 평가는 다음과 같은 하나의 크기 조정 기준을 제공합니다.

**크기 조정 기준** | **세부 정보** | **Data**
--- | --- | ---
**구성 기반** | 수집된 구성 데이터를 기반으로 권장 사항을 만드는 평가 | Azure App Service 평가에서는 평가 계산을 고려하여 구성 데이터만 사용합니다. 웹앱에 대한 성능 데이터는 수집하지 않습니다.

## <a name="how-do-i-assess-my-on-premises-aspnet-web-apps"></a>온-프레미스 ASP.NET 웹앱은 어떻게 평가하나요?

경량 Azure Migrate 어플라이언스에서 수집한 구성 데이터를 사용하여 온-프레미스 웹앱을 평가할 수 있습니다. 어플라이언스는 온-프레미스 웹앱을 검색하고 구성 데이터를 Azure Migrate로 보냅니다. [자세한 내용](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>어떻게 어플라이언스를 사용하여 평가할까요?

온-프레미스 서버를 발견하기 위해 Azure Migrate 어플라이언스를 배포하는 경우 다음 단계를 수행합니다.

1. Azure Migrate와 함께 사용할 Azure 및 온-프레미스 환경을 설정합니다.
2. 첫 번째 평가를 위해 Azure Migrate 프로젝트를 만듭니다. Azure Migrate: 검색 및 평가 도구가 프로젝트에 기본적으로 추가됩니다.
3. 경량 Azure Migrate 어플라이언스를 배포합니다. 어플라이언스는 지속적으로 온-프레미스 서버를 검색하고 구성 및 성능 데이터를 Azure Migrate에 보냅니다. 어플라이언스를 VM 또는 물리적 서버로 배포합니다. 평가하려는 서버에는 아무것도 설치할 필요가 없습니다.

어플라이언스가 검색을 시작한 후에는 평가하려는 서버(웹앱 호스트)를 그룹으로 수집하고 평가 유형이 **Azure App Service** 인 그룹에 대한 평가를 실행할 수 있습니다.

[ASP.NET 웹앱](tutorial-assess-webapps.md)을 평가하기 위한 자습서에 따라 이러한 단계를 시도해 보세요.

## <a name="what-properties-are-used-to-customize-the-assessment"></a>평가를 사용자 지정하는 데 사용되는 속성은 무엇인가요?

다음은 Azure App Service 평가 속성에 포함된 항목입니다.

**속성** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 지역입니다. Azure App Service 구성 및 비용 권장 사항은 사용자가 지정한 위치를 기반으로 합니다.
**격리 필요** | 더 빠른 프로세서, SSD 스토리지가 있는 Dv2 시리즈 VM을 사용하여 Azure 데이터 센터의 프라이빗 및 전용 환경에서 웹앱을 실행하고 표준 플랜에 비해 메모리 대 코어 비율을 두 배로 늘리려면 예를 선택합니다.
**예약 인스턴스** | 평가의 비용 예측이 고려할 수 있도록 예약 인스턴스를 지정합니다.<br/><br/> 예약 인스턴스 옵션을 선택하면 “할인율(%)”을 지정할 수 없습니다.
**제품** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. 평가는 해당 제품에 대한 비용을 추정합니다.
**통화** | 계정의 청구 통화입니다.
**할인(%)** | Azure 제품에 적용되는 구독별 할인입니다. 기본 설정은 0%입니다.
**EA 구독** | 비용 예측 시 EA(기업계약) 구독을 사용하도록 지정합니다. 구독에 적용되는 할인을 고려합니다. <br/><br/> 예약 인스턴스, 할인율(%), VM 작동 시간 속성을 기본 설정으로 놔둡니다.

Azure Migrate를 사용하여 평가를 만들기 위해 [모범 사례를 검토](best-practices-assessment.md)합니다.

## <a name="calculate-readiness"></a>준비 상태 계산

### <a name="azure-app-service-readiness"></a>Azure App Service 준비 상태

웹앱의 Azure App Service 준비 상태는 웹앱의 온-프레미스 구성과 Azure App Service 간의 기능 호환성 검사를 기반으로 합니다.

1. Azure App Service 평가는 호환성 문제를 식별하기 위해 웹앱 구성 데이터를 고려합니다.
1. 호환성 문제가 발견되지 않은 경우 준비 상태는 대상 배포 유형에 대해 **준비 완료** 로 표시됩니다.
1. 특정 대상 배포 유형으로의 마이그레이션을 차단하지 않는 중요하지 않은 호환성 문제(예: 성능 저하 또는 지원되지 않는 기능)가 있는 경우 준비 상태는 **조건과 함께 준비 완료**(하이퍼링크)로 표시되고 **경고** 세부 정보와 권장 수정 지침이 함께 표시됩니다.
1. 특정 대상 배포 유형으로의 마이그레이션을 차단할 수 있는 호환성 문제가 있는 경우 준비 상태는 **이슈** 세부 정보 및 권장되는 수정 지침을 나타내면서 **준비 안 됨** 으로 표시됩니다.
1. 검색이 아직 진행 중이거나 웹앱에 검색 문제가 있는 경우 평가에서 해당 웹앱의 준비 상태를 파악할 수 없으므로 준비 상태는 **알 수 없음** 으로 표시됩니다.

## <a name="calculate-sizing"></a>크기 계산

### <a name="azure-app-service-sku"></a>Azure App Service SKU

평가에서 구성 데이터를 기반으로 준비 상태를 결정하면 Azure App Service에서 앱을 실행하는 데 적합한 Azure App Service SKU가 결정됩니다.
프리미엄 플랜은 프로덕션 워크로드용이며 전용 가상 머신 인스턴스에서 실행됩니다. 각 인스턴스는 여러 개의 애플리케이션과 도메인을 지원할 수 있습니다. 격리 플랜은 프라이빗 전용 Azure 환경에 앱을 호스트하며 온-프레미스 네트워크와의 보안 연결이 필요한 앱에 적합합니다.

> [!NOTE]
> 현재 Azure Migrate는 I1, P1v2, P1v3 SKU만 권장합니다. Azure App Service에서는 더 많은 SKU를 사용할 수 있습니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/app-service/windows/).

### <a name="azure-app-service-plan"></a>Azure App Service 요금제

App Service의 앱은 항상 [App Service 요금제](/azure/app-service/overview-hosting-plans)에서 실행됩니다. App Service 계획은 실행할 웹앱에 대한 컴퓨팅 리소스 세트를 정의합니다. 대략적으로 요금제/SKU는 아래 표에 따라 결정됩니다.

**격리 필요** | **예약 인스턴스** | **App Service 요금제/SKU**
--- | --- | ---
예  | 예 | I1
예  | 아니요  | I1
아니요  | 예 | P1v3
아니요  | 아니요 | P1v2

### <a name="azure-app-service-cost-details"></a>Azure App Service 비용 정보

[App Service 요금제](/azure/app-service/overview-hosting-plans)는 사용하는 컴퓨팅 리소스에 대해 [요금](https://azure.microsoft.com/pricing/details/app-service/windows/)을 부과합니다. App Service에서는 웹앱이 아닌 App Service 요금제에 따라 요금을 지불합니다. 하나 이상의 앱은 동일한 컴퓨팅 리소스(또는 동일한 App Service 계획)에서 실행하도록 구성될 수 있습니다. App Service 계획에 정의된 대로 이 App Service 계획에 추가한 모든 앱은 이러한 컴퓨팅 리소스에서 실행됩니다.
비용을 최적화하기 위해 Azure Migrate 평가는 권장되는 각 App Service 요금제에 여러 웹앱을 할당합니다. 각 요금제 인스턴스에 할당되는 웹앱 수는 아래 표와 같습니다.

**App Service 계획** | **App Service 요금제당 웹앱 수**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

> [!NOTE]
> 언제든지 App Service 계획을 확장하고 축소할 수 있습니다. [자세히 알아보기](/azure/app-service/overview-hosting-plans#what-if-my-app-needs-more-capabilities-or-features/).

## <a name="next-steps"></a>다음 단계
- 평가를 만드는 모범 사례를 [검토합니다](best-practices-assessment.md). 
- [Azure App Service 평가](how-to-create-azure-app-service-assessment.md)를 실행하는 방법을 알아보세요.