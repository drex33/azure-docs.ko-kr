---
title: Azure Migrate 정보
description: Azure Migrate 서비스에 대해 알아봅니다.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: b67239a973f3ba435aeeb7021d8d4f2faea9c98f
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015638"
---
# <a name="about-azure-migrate"></a>Azure Migrate 정보

이 문서에서는 Azure Migrate 서비스에 대한 간단한 개요를 제공합니다.

Azure Migrate는 온-프레미스 서버, 인프라, 애플리케이션 및 데이터를 평가하고 Azure로 마이그레이션할 수 있는 중앙 허브를 제공합니다. 다음 기능을 제공합니다.

- **통합 마이그레이션 플랫폼**: Azure로의 마이그레이션을 시작, 실행 및 추적하는 단일 포털입니다.
- **도구 범위**: 평가 및 마이그레이션에 사용할 도구 범위입니다. Azure Migrate 도구에는 Azure Migrate: 검색 및 평가 및 Azure Migrate: 서버 마이그레이션이 포함됩니다. Azure Migrate는 다른 Azure 서비스, 도구 및 ISV(독립 소프트웨어 공급업체) 제품과도 통합됩니다.
- **평가 및 마이그레이션**: Azure Migrate 허브에서 다음을 평가하고 마이그레이션할 수 있습니다.
    - **서버, 데이터베이스 및 웹앱**: 웹앱 및 SQL Server 인스턴스를 포함한 온-프레미스 서버를 평가하고, 이를 Azure 가상 머신 또는 AVS(Azure VMware Solution)(미리 보기)로 마이그레이션합니다.
    - **데이터베이스**: 온-프레미스 데이터베이스를 평가하고 Azure SQL Database 또는 SQL Managed Instance로 마이그레이션합니다.
    - **웹 애플리케이션**: 온-프레미스 웹 애플리케이션을 평가하고 Azure App Service로 마이그레이션합니다.
    - **가상 데스크톱**: 온-프레미스 VDI(가상 데스크톱 인프라)를 평가하고 Azure의 Windows Virtual Desktop으로 마이그레이션합니다.
    - **Data**: Azure Data Box 제품을 사용하여 대량의 데이터를 Azure로 빠르고 비용 효율적으로 마이그레이션합니다.

## <a name="integrated-tools"></a>통합 도구

Azure Migrate 허브에는 다음 도구가 포함되어 있습니다.

**도구** | **평가 및 마이그레이션** | **세부 정보**
--- | --- | ---
**Azure Migrate: 검색 및 평가** | SQL 및 웹앱을 포함한 서버를 검색하고 평가합니다. | Azure로 마이그레이션하기 위한 준비 과정으로 VMware, Hyper-V 및 물리적 서버가 실행되는 온-프레미스 서버를 검색하고 평가합니다.
**Azure Migrate: Server Migration** | 서버 마이그레이션 | VMware VM, Hyper-V VM, 물리적 서버, 기타 가상화된 서버 및 퍼블릭 클라우드 VM을 Azure로 마이그레이션합니다.
**데이터 Migration Assistant** | Azure SQL Database, Azure SQL Managed Instance 또는 SQL Server를 실행하는 Azure VM으로 마이그레이션할 SQL Server 데이터베이스를 평가합니다. | Data Migration Assistant는 SQL Server를 평가하는 독립 실행형 도구입니다. 마이그레이션을 차단하는 잠재적인 문제를 정확히 찾아내는 데 도움이 됩니다. 이는 지원되지 않는 기능, 마이그레이션 후 유용할 수 있는 새로운 기능, 데이터베이스 마이그레이션의 올바른 경로를 식별합니다. [자세히 알아보기](/sql/dma/dma-overview).
**Azure Database Migration Service** | 온-프레미스 데이터베이스를 SQL Server, Azure SQL Database 또는 SQL Managed Instances를 실행하는 Azure VM으로 마이그레이션합니다. | Database Migration Service에 대해 [자세히 알아보기](../dms/dms-overview.md)
**Movere** | 서버 평가 | Movere에 대해 [자세히 알아보세요](#movere).
**Web App Migration Assistant** | 온-프레미스 웹앱을 평가하고 Azure로 마이그레이션합니다. |  Azure App Service Migration Assistant는 Azure App Service으로 마이그레이션하기 위한 온-프레미스 웹 사이트를 평가하는 독립 실행형 도구입니다.<br/><br/> 이 Migration Assistant를 사용하여 .NET 및 PHP 웹앱을 Azure로 마이그레이션합니다. Azure App Service Migration Assistant에 대해 [자세히 알아보세요](https://appmigration.microsoft.com/).
**Azure Data Box** | 오프라인 데이터를 마이그레이션합니다. | Azure Data Box 제품을 사용하여 대량의 오프라인 데이터를 Azure로 이동합니다. [자세히 알아보기](../databox/index.yml).

> [!NOTE]
> Azure Government에 있는 경우 외부 통합 도구 및 ISV 제품은 데이터를 Azure Migrate에 보낼 수 없습니다. 도구는 독립적으로 사용할 수 있습니다.

## <a name="isv-integration"></a>ISV 통합

Azure Migrate는 여러 ISV 제품과 통합됩니다. 

**ISV**    | **기능**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 서버를 마이그레이션합니다.
[Cloudamize](https://www.cloudamize.com/platform) | 서버를 평가합니다.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 서버를 평가하고 마이그레이션합니다.
[Device42](https://docs.device42.com/) | 서버를 평가합니다.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI를 평가합니다.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 서버를 마이그레이션합니다.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 서버를 평가합니다.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 서버 및 데이터베이스를 평가합니다.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | 서버를 마이그레이션합니다.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Azure Migrate: 검색 및 평가 도구

Azure Migrate: 검색 및 평가 도구는 Azure로 마이그레이션할 온-프레미스 VMware VM, Hyper-V VM 및 물리적 서버를 검색하고 평가합니다.

이 도구는 다음과 같은 작업을 수행합니다.

- **Azure 준비 상태**: 온-프레미스 서버, SQL Server 및 웹앱을 Azure로 마이그레이션할 준비가 되었는지 평가합니다.
- **Azure 크기 조정**: 마이그레이션 후 Azure VM/Azure SQL 구성 크기/Azure VMware Solution 노드 수의 크기를 추정합니다.
- **Azure 비용 예측**: Azure에서 온-프레미스 서버를 실행하기 위한 비용을 예측합니다.
- **종속성 분석**: 상호 종속적 서버를 Azure로 이동하기 위한 서버 간 종속성 및 최적화 전략을 식별합니다. [종속성 분석](concepts-dependency-visualization.md)을 통한 검색 및 평가에 대해 자세히 알아보세요.

검색 및 평가는 온-프레미스에 배포하는 간단한 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용합니다.

- 이 어플라이언스는 VM 또는 물리적 서버에서 실행됩니다. 다운로드한 템플릿을 사용하여 쉽게 설치할 수 있습니다.
- 어플라이언스는 온-프레미스 서버를 검색합니다. 또한 서버 메타데이터 및 성능 데이터를 Azure Migrate에 지속적으로 보냅니다.
- 어플라이언스 검색은 에이전트 없이 수행됩니다. 검색된 서버에는 아무것도 설치되지 않습니다.
- 어플라이언스 검색 후 검색된 서버를 그룹으로 수집하고, 각 그룹에 대한 평가를 실행할 수 있습니다.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: 서버 마이그레이션 도구

Azure Migrate: 서버 마이그레이션 도구는 서버를 Azure로 마이그레이션하는 데 도움이 됩니다.

**마이그레이션** | **세부 정보**
--- | ---
온-프레미스 VMware VM | 에이전트 없는 또는 에이전트 기반 마이그레이션을 사용하여 VM을 Azure로 마이그레이션합니다.<br/><br/> 에이전트 없는 마이그레이션의 경우 서버 마이그레이션은 서버 검색 및 평가를 위해 검색 및 평가 도구에서 사용하는 것과 동일한 어플라이언스를 사용합니다.<br/><br/> 에이전트 기반 마이그레이션의 경우 마이그레이션은 복제 어플라이언스를 사용합니다.
온-프레미스 Hyper-V VM | VM을 Azure로 마이그레이션합니다.<br/><br/> 서버 마이그레이션은 마이그레이션을 위해 Hyper-V 호스트에 설치된 공급자 에이전트를 사용합니다.
온-프레미스 물리적 서버 또는 다른 클라우드에서 호스팅되는 서버 | 물리적 서버를 Azure로 마이그레이션할 수 있습니다. 또한 다른 가상화된 서버와 VM을 마이그레이션하기 위해 물리적 서버로 처리하여 다른 퍼블릭 클라우드에서 마이그레이션할 수도 있습니다. 서버 마이그레이션은 마이그레이션에 복제 어플라이언스를 사용합니다.

## <a name="selecting-assessment-and-migration-tools"></a>평가 및 마이그레이션 도구 선택

Azure Migrate 허브에서 평가 또는 마이그레이션에 사용할 도구를 선택하고 프로젝트에 추가합니다. ISV 도구 또는 Movere를 추가하는 경우 다음을 수행합니다.

- 시작하려면 도구 지침에 따라 라이선스를 얻거나 평가판에 가입합니다. 각 ISV 또는 도구는 도구 라이선스를 지정합니다.
- 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 도구 지침에 따라 연결합니다.
- 프로젝트 내의 모든 도구에서 마이그레이션을 추적합니다.

## <a name="movere"></a>Movere

Movere는 SaaS(Software as a Service) 플랫폼입니다. 이는 하루 만에 전체 IT 환경을 정확하게 제시하여 비즈니스 인텔리전스를 향상시킵니다. 조직과 기업은 성장하고, 변화하고, 디지털적으로 최적화됩니다. 이에 따라 Movere는 플랫폼, 애플리케이션 또는 지리에 관계 없이 환경을 보고 제어하는 데 필요한 신뢰도를 제공합니다.

Movere는 Movere를 [인수](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)했으며 더 이상 독립 실행형 제품으로 판매되지 않습니다. Movere는 Microsoft 솔루션 평가 및 Microsoft 클라우드 경제 프로그램을 통해 사용할 수 있습니다. Movere에 대해 [자세히 알아보세요](https://www.movere.io).

기본 제공 마이그레이션 서비스인 Azure Migrate도 살펴보는 것이 좋습니다. Azure Migrate는 클라우드로 마이그레이션을 간소화하는 중앙 허브를 제공합니다. 허브는 물리적 및 가상 서버, 데이터베이스, 애플리케이션 등의 워크로드를 포괄적으로 지원합니다. 엔드투엔드 가시성을 통해 검색, 평가 및 마이그레이션 전체에서 진행 상황을 쉽게 추적할 수 있습니다.

Azure 및 파트너 ISV 도구가 기본 제공되는 Azure Migrate는 다음과 같은 다양한 기능을 제공합니다.

- 가상 및 물리적 서버 검색.
- 성능 기반 크기 조정.
- 비용 계획.
- 가져오기 기반 평가.
- 에이전트 없는 애플리케이션의 종속성 분석.

시작하는 데 전문가의 도움이 필요한 경우 Microsoft는 숙련된 [Azure Expert 관리형 서비스 공급자](https://azure.microsoft.com/partners)를 통해 안내합니다. [Azure Migrate 웹 사이트](https://azure.microsoft.com/services/azure-migrate/)를 확인하세요.

## <a name="azure-migrate-versions"></a>Azure Migrate 버전

Azure Migrate 서비스에는 다음과 같은 두 가지 버전이 있습니다.

- **현재 버전**: 이 버전을 사용하여 프로젝트를 만들고, 온-프레미스 서버를 검색하고, 평가 및 마이그레이션을 오케스트레이션합니다. 이 버전의 새로운 기능에 대해 [자세히 알아보세요](whats-new.md).
- **이전 버전**: 클래식 Azure Migrate라고도 하는 이전 버전의 Azure Migrate는 VMware가 실행되는 온-프레미스 서버의 평가만 지원합니다. 클래식 Azure Migrate는 2024년 2월에 만료됩니다. 2024년 2월 이후에는 클래식 버전의 Azure Migrate는 더 이상 지원되지 않으며 클래식 프로젝트의 인벤토리 메타데이터가 삭제됩니다. 이전 버전의 프로젝트나 구성 요소를 새 버전으로 업그레이드할 수 없습니다. [새 프로젝트를 만들고](create-manage-projects.md) [평가 및 마이그레이션 도구를 추가](./create-manage-projects.md)해야 합니다. 사용 가능한 평가 및 마이그레이션 도구를 사용하는 방법을 이해하려면 자습서를 사용하세요. 클래식 프로젝트에 연결된 Log Analytics 작업 영역이 있는 경우 클래식 프로젝트를 삭제한 후에 현재 버전의 프로젝트에 연결할 수 있습니다.

    기존 프로젝트에 액세스하려면 Azure Portal에서 **Azure Migrate** 를 검색하고 선택합니다. **Azure Migrate** 대시보드에는 알림 및 이전 프로젝트에 액세스할 수 있는 링크가 있습니다.

## <a name="next-steps"></a>다음 단계

- [VMware VM](./tutorial-discover-vmware.md), [Hyper-V VM](./tutorial-discover-hyper-v.md) 또는 [물리적 서버](./tutorial-discover-physical.md)를 평가하는 자습서를 사용해 보세요.
- Azure Migrate에 대한 [질문과 대답을 검토합니다](resources-faq.md).