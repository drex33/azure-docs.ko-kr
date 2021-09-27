---
title: Azure Migrate의 검색, 평가 및 종속성 분석에 대한 질문
description: Azure Migrate의 검색, 평가 및 종속성 분석과 관련된 일반적인 질문에 대한 답변을 얻습니다.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: fbcea71558a59d624faf73647498fb38ecf6ccc1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755443"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>검색, 평가 및 종속성 분석 - 일반적인 질문

이 문서에서는 Azure Migrate의 검색, 평가 및 종속성 분석과 관련된 일반적인 질문에 대답합니다. 다른 질문이 있는 경우 다음 리소스를 확인하세요.

- Azure Migrate에 대한 [일반적인 질문](resources-faq.md)
- [Azure Migrate 어플라이언스](common-questions-appliance.md)에 대한 질문
- [서버 마이그레이션](common-questions-server-migration.md)에 대한 질문
- [Azure Migrate 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureMigrate )에서 질문과 대답 보기

## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure Migrate를 사용하여 검색하고 평가하는 데 지원되는 지역은 어떻게 되나요?

[퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>하나의 어플라이언스에서 검색할 수 있는 서버의 수는 어떻게 되나요?

VMware 환경에서 최대 10,000개의 서버, Hyper-V 환경에서 최대 5,000개의 서버, 단일 어플라이언스를 사용하여 최대 1,000개의 물리적 서버를 검색할 수 있습니다. 더 많은 서버가 있는 경우 [Hyper-V 평가 스케일링](scale-hyper-v-assessment.md), [VMware 평가 스케일링](scale-vmware-assessment.md) 및 [물리적 서버 평가 스케일링](scale-physical-assessment.md)을 참조하세요.

## <a name="how-do-i-choose-the-assessment-type"></a>평가 유형을 선택하려면 어떻게 해야 하나요?

- Azure VM으로 마이그레이션하기 위해 온-프레미스 [VMware](how-to-set-up-appliance-vmware.md) 및 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 환경의 서버와 [물리적 서버](how-to-set-up-appliance-physical.md)를 평가하려면 **Azure VM 평가** 를 사용합니다. [자세한 내용](concepts-assessment-calculation.md)
- Azure SQL Database 또는 Azure SQL Managed Instance로 마이그레이션하기 위해 VMware 환경의 온-프레미스 SQL Server를 평가하려면 **Azure SQL** 평가 유형을 사용합니다. [자세한 내용](concepts-assessment-calculation.md)
- Azure App Service로 마이그레이션하기 위해 VMware 환경의 IIS 웹 서버에서 실행되는 온-프레미스 ASP.NET 웹앱을 평가하려는 경우 **Azure App Service** 평가 유형을 사용합니다. [자세한 내용](concepts-assessment-calculation.md)
- 이 평가 유형을 사용하여 [AVS(Azure VMware Solution)](../azure-vmware/introduction.md)로 마이그레이션하기 위해 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md)을 평가하려면 **AVS(Azure VMware Solution)** 평가를 사용합니다. [자세한 정보](concepts-azure-vmware-solution-assessment-calculation.md)
- 두 가지 유형의 평가를 실행하는 경우에만 VMware 컴퓨터에서 공통 그룹을 사용할 수 있습니다. Azure Migrate에서 AVS 평가를 처음 실행하는 경우 새 VMware 컴퓨터 그룹을 만드는 것이 좋습니다.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>내 Azure VM 및/또는 AVS 평가 보고서에서 일부/모든 서버에 대한 성능 데이터가 누락된 이유는 무엇인가요?

"성능 기반" 평가의 경우 Azure Migrate 어플라이언스에서 온-프레미스 서버에 대한 성능 데이터를 수집할 수 없으면 평가 보고서 내보내기에 'PercentageOfCoresUtilizedMissing' 또는 'PercentageOfMemoryUtilizedMissing'이 표시됩니다. 다음을 확인하세요.

- 평가를 만드는 동안 서버의 전원이 켜져 있는지 확인합니다.
- 메모리 카운터만 누락되었지만 Hyper-V 환경의 서버를 평가하려고 하는지 확인합니다. 이 시나리오에서는 서버에서 동적 메모리를 사용하도록 설정하고 최신 변경 내용을 반영하도록 평가를 '다시 계산'합니다. 서버에서 동적 메모리를 사용하도록 설정한 경우에만 어플라이언스에서 Hyper-V 환경의 서버에 대한 메모리 사용률 값을 수집할 수 있습니다.

- 모든 성능 카운터가 누락된 경우 443 포트(HTTPS)에서 아웃바운드 연결이 허용되는지 확인합니다.

    > [!Note]
    > 성능 카운터가 누락된 경우 Azure Migrate: 서버 평가는 할당된 온-프레미스 코어 수/메모리로 대체하고 이에 따라 적절한 VM 크기를 권장합니다.

## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>내 Azure SQL 평가에서 일부/모든 SQL 인스턴스/데이터베이스에 대한 성능 데이터가 누락된 이유는 무엇인가요?

성능 데이터가 수집되는지 확인하려면 다음을 확인하세요.

- 평가를 만드는 동안 SQL Server의 전원이 켜져 있는지 확인합니다.
- Azure Migrate에서 SQL 에이전트의 연결 상태가 '연결됨'인지 확인하고, 마지막 하트비트를 확인합니다. 
- 검색된 SQL 인스턴스 블레이드에서 모든 SQL 인스턴스에 대한 Azure Migrate 연결 상태가 '연결됨'인지 확인합니다.
- 모든 성능 카운터가 누락된 경우 포트 443(HTTPS)에서 아웃바운드 연결이 허용되는지 확인합니다.

성능 카운터가 누락된 경우 Azure SQL 평가에서 해당 인스턴스/데이터베이스에 대해 가장 작은 Azure SQL 구성을 권장합니다.

## <a name="why-confidence-rating-is-not-available-for-azure-app-service-assessments"></a>Azure App Service 평가에 신뢰 등급을 사용할 수 없는 이유는 무엇인가요?

성능 데이터는 Azure App Service 평가를 위해 캡처되지 않으므로 이 평가 유형에 대한 신뢰 등급이 표시되지 않습니다. Azure App Service 평가는 계산 중에 웹앱의 구성 데이터를 고려합니다.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>내 평가의 신뢰 등급이 낮은 이유는 무엇인가요?

신뢰 등급은 평가를 계산하는 데 필요한 [사용 가능한 데이터 요소](./concepts-assessment-calculation.md#ratings)의 백분율을 기준으로 "성능 기반" 평가에 대해 계산됩니다. 아래에는 평가의 신뢰 등급이 낮아질 수 있는 이유가 나와 있습니다.

- 평가를 작성하는 기간 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 일주일로 설정하여 평가를 만드는 경우 검색 시작 후 일주일 이상 기다려야 데이터 요소가 수집됩니다. 이 기간 동안 기다릴 수 없으면 성능 기간을 더 짧은 기간으로 변경하고 평가를 **다시 계산** 하세요.
- 평가는 일부 또는 모든 서버에 대한 성능 데이터를 평가 기간에 수집할 수 없습니다. 높은 신뢰 등급의 경우 다음을 확인하세요. 
    - 평가 기간에 서버의 전원이 켜져 있는지 확인합니다.
    - 443 포트에서 아웃바운드 연결이 허용되는지 확인합니다.
    - Hyper-V 서버의 경우 동적 메모리가 사용하도록 설정되어 있는지 확인합니다.
    - Azure Migrate에서 에이전트의 연결 상태가 '연결됨'인지 확인하고, 마지막 하트비트를 확인합니다.
    - Azure SQL 평가의 경우 검색된 SQL 인스턴스 블레이드에서 모든 SQL 인스턴스에 대한 Azure Migrate 연결 상태가 “연결됨”인지 확인합니다.

    신뢰 등급의 최신 변경 내용을 반영하려면 평가를 **다시 계산** 하세요.

- Azure VM 및 AVS 평가의 경우 검색이 시작된 후 일부 서버만 만들어졌습니다. 예를 들어 지난 한 달 동안의 성능 기록에 대한 평가를 만들고 있지만 불과 일주일 전에 환경에서 만든 서버가 거의 없는 경우입니다. 이 경우 새 서버의 성능 데이터는 전체 기간에 사용할 수 없으며 신뢰 등급은 낮습니다. [자세한 정보](./concepts-assessment-calculation.md#confidence-ratings-performance-based)
- Azure SQL 평가의 경우 검색을 시작한 후 일부 SQL 인스턴스 또는 데이터베이스가 만들어졌습니다. 예를 들어 지난 한 달 동안의 성능 기록에 대한 평가를 만들고 있지만 불과 일주일 전에 환경에서 만든 SQL 인스턴스 또는 데이터베이스가 거의 없는 경우입니다. 이 경우 새 서버의 성능 데이터는 전체 기간에 사용할 수 없으며 신뢰 등급은 낮습니다. [자세한 정보](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="the-number-of-azure-vm-or-avs-assessments-on-the-discovery-and-assessment-tool-are-incorrect"></a>검색 및 평가 도구의 Azure VM 또는 AVS 평가 수가 잘못되었습니다.

 이를 수정하려면 전체 평가 수를 클릭하여 모든 평가로 이동하여 Azure VM 또는 AVS 평가를 다시 계산합니다. 그러면 검색 및 평가 도구에 해당 평가 유형에 대한 올바른 개수가 표시됩니다.

## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>새로운 Azure SQL 평가를 사용하려고 합니다.

VMware 환경에서 실행되는 SQL Server 인스턴스 및 데이터베이스를 검색하고 평가하는 기능은 현재 미리 보기로 제공됩니다. [이 자습서](tutorial-discover-vmware.md)를 시작합니다. 기존 프로젝트에서 이 기능을 사용해 보려면 이 문서의 [필수 구성 요소](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

## <a name="i-want-to-try-out-the-new-azure-app-service-assessment"></a>새로운 Azure App Service 평가를 사용하려고 합니다.

VMware 환경에서 실행되는 .NET 웹앱의 검색 및 평가가 이제 미리 보기로 제공됩니다. [이 자습서](tutorial-discover-vmware.md)를 시작합니다. 기존 프로젝트에서 이 기능을 사용해 보려면 이 문서의 [필수 구성 요소](how-to-discover-sql-existing-project.md)를 완료했는지 확인하세요.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Azure SQL 평가를 만들 때 일부 서버를 볼 수 없습니다.

- Azure SQL 평가는 SQL 인스턴스가 검색된 서버에서만 실행할 수 있습니다. 평가할 서버와 SQL 인스턴스가 표시되지 않는 경우 검색이 완료될 때까지 기다렸다가 평가를 만드세요.
- 평가를 만드는 동안 이전에 만든 그룹이 표시되지 않는 경우 그룹에서 VMware가 아닌 서버 또는 SQL 인스턴스가 없는 서버를 제거하세요.
- Azure Migrate에서 Azure SQL 평가를 처음 실행하는 경우 새 서버 그룹을 만드는 것이 좋습니다.

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-app-service-assessment"></a>Azure App Service 평가를 만들 때 일부 서버를 볼 수 없습니다.

- Azure App Service 평가는 웹 서버 역할이 검색된 서버에서만 수행할 수 있습니다. 평가할 서버가 표시되지 않는 경우 검색이 완료될 때까지 기다렸다가 평가를 만드세요.
- 평가를 만드는 동안 이전에 만든 그룹이 표시되지 않는 경우 그룹에서 VMware가 아닌 서버나 웹앱이 없는 서버를 제거하세요.
- Azure Migrate에서 Azure App Service 평가를 처음 실행하는 경우 새 서버 그룹을 만드는 것이 좋습니다.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>내 인스턴스에 대한 준비 상태가 계산되는 방법을 알아보려고 합니다.

대상 Azure SQL 배포 형식(Azure SQL Database 또는 Azure SQL Managed Instance)을 사용하여 기능 호환성 검사를 수행한 후에 SQL 인스턴스에 대한 준비 상태가 계산되었습니다. [자세히 알아보기](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="i-want-to-understand-how-was-the-readiness-for-my-web-apps-is-computed"></a>내 웹앱에 대한 준비 상태가 컴퓨팅되는 방법을 알아보려고 합니다.

웹앱에 대한 준비 상태는 일련의 기술 검사를 실행하여 웹앱이 Azure App Service에서 성공적으로 실행될지 여부를 확인하여 컴퓨팅됩니다. 검사에 대한 설명은 [여기](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Readiness-Checks)를 참조하세요.

## <a name="why-is-my-web-app-marked-as-ready-with-conditions-or-not-ready-in-my-azure-app-service-assessment"></a>Azure App Service 평가에서 내 웹앱이 조건부 준비 완료 또는 준비되지 않음으로 표시되는 이유는 무엇인가요?

이 문제는 지정된 웹앱에 대해 하나 이상의 기술 검사가 실패한 경우에 발생할 수 있습니다. 웹앱의 준비 상태를 클릭하면 실패한 검사의 세부 정보와 수정 방법을 확인할 수 있습니다.

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>모든 SQL 인스턴스에 대한 준비 상태가 '알 수 없음'으로 표시되는 이유는 무엇인가요?

검색이 최근에 시작되었지만 여전히 진행 중인 경우 일부 또는 모든 SQL 인스턴스에 대한 준비 상태가 '알 수 없음'으로 표시될 수 있습니다. 어플라이언스가 환경을 프로파일링하도록 어느 정도 기다린 후 평가를 다시 계산하는 것이 좋습니다.
SQL 검색은 24시간마다 한 번씩 수행되며, 최신 구성 변경 내용이 반영될 때까지 최대 하루를 기다려야 할 수 있습니다.

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>일부 SQL 인스턴스에 대한 준비 상태가 '알 수 없음'으로 표시되는 이유는 무엇인가요?

이는 다음과 같은 경우에 발생할 수 있습니다.

- 검색이 아직 진행 중입니다. 어플라이언스가 환경을 프로파일링하도록 어느 정도 기다린 후 평가를 다시 계산하는 것이 좋습니다.
- 오류 및 알림 블레이드에서 수정해야 하는 몇 가지 검색 문제가 있습니다.

SQL 검색은 24시간마다 한 번씩 수행되며, 최신 구성 변경 내용이 반영될 때까지 최대 하루를 기다려야 할 수 있습니다.

## <a name="my-assessment-is-in-outdated-state"></a>내 평가가 오래됨 상태입니다.

### <a name="azure-vmavs-assessment"></a>Azure VM/AVS 평가

평가된 그룹에 있는 서버에 대한 온-프레미스 변경 내용이 있는 경우 평가는 '오래됨'으로 표시됩니다. 아래 속성 중에서 수행된 하나 이상의 변경으로 인해 평가가 "오래됨"으로 표시될 수 있습니다.

- 프로세서 코어 수
- 할당된 메모리
- 부팅 유형 또는 펌웨어
- 운영 체제 이름, 버전 및 아키텍처
- 디스크 수
- 네트워크 어댑터 수
- 디스크 크기 변경(할당된 GB)
- NIC 속성 업데이트. 예: Mac 주소 변경, IP 주소 추가 등

평가의 최신 변경 내용을 반영하도록 평가를 **다시 계산** 하세요.

### <a name="azure-sql-assessment"></a>Azure SQL 평가

평가된 그룹에 있는 온-프레미스 SQL 인스턴스 및 데이터베이스에 변경 내용이 있는 경우 평가는 **오래됨** 상태로 표시됩니다.

- SQL 인스턴스가 추가되었거나 서버에서 제거되었습니다.
- SQL 데이터베이스가 추가되었거나 SQL 인스턴스에서 제거되었습니다.
- 20%를 초과하여 변경된 SQL 인스턴스의 총 데이터베이스 크기
- 프로세서 코어 수 및/또는 할당된 메모리 변경

평가의 최신 변경 내용을 반영하도록 평가를 **다시 계산** 하세요.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>특정 대상 배포 유형을 권장하는 이유는 무엇인가요?

Azure Migrate는 SQL 인스턴스와 호환되는 특정 Azure SQL 배포 유형을 권장합니다. Microsoft 권장 대상으로 마이그레이션하면 전반적인 마이그레이션 노력이 줄어듭니다. 이 Azure SQL 구성(SKU)은 SQL 인스턴스 및 관리하는 데이터베이스의 성능 특성을 고려한 후 권장되었습니다. 여러 Azure SQL 구성이 적합한 경우에는 가장 비용 효율적인 것이 좋습니다. [자세히 알아보기](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>SQL 인스턴스가 Azure SQL DB 및 Azure SQL MI에 대해 준비된 경우 어떤 배포 대상을 선택해야 하나요?

인스턴스가 Azure SQL DB 및 Azure SQL MI 둘 다에 대해 준비된 경우 Azure SQL 구성의 예상 비용이 낮은 대상 배포 유형을 선택하는 것이 좋습니다.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>내 인스턴스가 Azure SQL 평가에서 'Azure VM에 대해 잠재적으로 준비됨'으로 표시되는 이유는 무엇인가요?

이는 평가 속성에서 선택한 대상 배포 유형이 **권장됨** 이고 SQL 인스턴스가 Azure SQL Database 및 Azure SQL Managed Instance에 대해 준비되지 않은 경우에 발생할 수 있습니다. 사용자는 Azure Migrate에서 **Azure VM** 평가 유형으로 평가를 만들어 인스턴스가 실행되는 서버가 Azure VM으로 마이그레이션할 준비가 되었는지 확인하는 것이 좋습니다.
사용자는 Azure Migrate에서 평가 유형을 **Azure VM** 으로 사용하는 평가를 만들어 인스턴스가 실행되는 서버에서 Azure VM으로 대신 마이그레이션할 준비가 되었는지 확인하는 것이 좋습니다.

- Azure Migrate의 Azure VM 평가는 현재 리프트 앤 시프트 방식에 집중하고 있으며, Azure 가상 머신의 SQL 인스턴스 및 데이터베이스 실행에 대한 특정 성능 메트릭은 고려하지 않습니다.
- 서버에서 Azure VM 평가를 실행하는 경우 서버에서 실행되는 모든 인스턴스에 대해 권장되는 크기 및 예상 비용을 계산하고 서버 마이그레이션 도구를 사용하여 Azure VM으로 마이그레이션할 수 있습니다. 마이그레이션하기 전에 Azure Virtual Machines의 SQL Server에 대한 [성능 지침을 검토](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md)합니다.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>인스턴스가 평가의 일부인 경우에도 내 평가에서 일부 데이터베이스를 볼 수 없습니다.

Azure SQL 평가는 온라인 상태에 있는 데이터베이스만 포함합니다. 데이터베이스가 다른 상태에 있는 경우 평가는 이러한 데이터베이스에 대한 준비, 크기 조정 및 비용 계산을 무시합니다. 이러한 데이터베이스를 평가하려는 경우에는 데이터베이스의 상태를 변경하고 잠시 후 평가를 다시 계산하세요.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Azure VM과 Azure SQL Database/Azure SQL Managed Instance에서 내 SQL 인스턴스를 실행하는 비용을 비교하려고 합니다.

**Azure SQL** 평가에 사용된 것과 동일한 그룹에서 **Azure VM** 유형을 사용하여 평가를 만들 수 있습니다. 그런 다음 두 보고서를 나란히 비교할 수 있습니다. 그러나 Azure Migrate의 Azure VM 평가는 현재 리프트 앤 시프트에 초점을 맞춘 후 Azure 가상 머신에서 SQL 인스턴스 및 데이터베이스를 실행하는 특정 성능 메트릭을 고려하지 않습니다. 서버에서 Azure VM 평가를 실행하는 경우 서버에서 실행되는 모든 인스턴스에 대해 권장되는 크기 및 예상 비용을 계산하고 서버 마이그레이션 도구를 사용하여 Azure VM으로 마이그레이션할 수 있습니다. 마이그레이션하기 전에 Azure Virtual Machines의 SQL Server에 대한 [성능 지침을 검토](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md)합니다.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>내 Azure SQL 평가의 스토리지 비용이 0입니다.

Azure SQL Managed Instance의 경우 처음 32GB/인스턴스/월 스토리지에 대해 추가되는 스토리지 비용은 없지만, 32GB 단위로 증분되는 스토리지에 대해서는 스토리지 비용이 추가됩니다. [자세한 내용](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>AVS(Azure VMware Solution) 평가를 만들 때 일부 그룹이 표시되지 않습니다.

- VMware 컴퓨터만 있는 그룹에서 AVS 평가를 수행할 수 있습니다. AVS 평가를 수행하려는 경우 그룹에서 VMware가 아닌 컴퓨터를 모두 제거합니다.
- Azure Migrate에서 AVS 평가를 처음 실행하는 경우 새 VMware 컴퓨터 그룹을 만드는 것이 좋습니다.

## <a name="queries-regarding-ultra-disks"></a>울트라 디스크에 대한 쿼리

### <a name="can-i-migrate-my-disks-to-ultra-disk-using-azure-migrate"></a>Azure Migrate를 사용하여 내 디스크를 울트라 디스크로 마이그레이션할 수 있나요?

아니요. 현재 Azure Migrate와 Azure Site Recovery 모두 울트라 디스크로의 마이그레이션을 지원하지 않습니다. 울트라 디스크의 배포 단계는 [여기](../virtual-machines/disks-enable-ultra-ssd.md?tabs=azure-portal#deploy-an-ultra-disk)에서 확인하세요.

### <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>내 울트라 디스크에 프로비저닝된 IOPS와 처리량이 온-프레미스 IOPS와 처리량보다 많은 이유는 무엇인가요?

[공식 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)에 따라 울트라 디스크는 프로비저닝된 크기, 프로비저닝된 IOPS, 프로비저닝된 처리량을 기준으로 청구됩니다. 제공된 예제에서는 다음과 같이 처리됩니다.

20,000IOPS와 1,000MB/초로 200GiB Ultra Disk를 프로비저닝하고 20시간 후에 삭제한 경우 256GiB의 디스크 크기 제안에 매핑되고 20시간의 256GiB, 20,000IOPS, 1,000MB/초에 대한 요금이 청구됩니다.

프로비저닝할 IOPS =(검색된 처리량) *1024/256

### <a name="does-the-ultra-disk-recommendation-consider-latency"></a>울트라 디스크 권장 사항은 대기 시간을 고려하나요?

아니요, 현재 디스크 크기, 총 처리량, 총 IOPS만 크기 조정과 비용 계산에 사용됩니다.

### <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>M 시리즈가 울트라 디스크를 지원하는 것을 확인할 수 있지만 울트라 디스크가 권장된 내 평가에서는 “이 위치에 대한 VM을 찾을 수 없습니다.”라고 표시됩니다.

울트라 디스크를 지원하는 모든 VM 크기가 모든 울트라 디스크 지원 지역에 존재하는 것은 아니기 때문에 이런 상황이 발생할 수 있습니다. 대상 평가 지역을 변경하여 이 서버의 VM 크기를 가져옵니다.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Azure Government에서 일부 VM 유형과 크기가 표시되지 않습니다.

평가 및 마이그레이션에 지원되는 VM 유형과 크기는 Azure Government 위치의 가용성에 따라 달라집니다. VM 유형은 Azure Government에서 [검토 및 비교](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)할 수 있습니다.

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>서버 크기가 변경되었습니다. 평가를 다시 실행할 수 있나요?

Azure Migrate 어플라이언스는 온-프레미스 환경에 대한 정보를 지속적으로 수집합니다.  평가는 온-프레미스 서버의 특정 시점 스냅샷입니다. 평가하려는 서버에서 설정을 변경하는 경우 다시 계산 옵션을 사용하여 평가를 최신 변경 내용으로 업데이트하세요.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>다중 테넌트 환경에서 서버를 검색하려면 어떻게 할까요?

- **VMware**: 환경이 테넌트 간에 공유되고 다른 테넌트의 구독에서 테넌트의 서버를 검색하지 않으려는 경우 검색하려는 서버에만 액세스할 수 있는 VMware vCenter Server 자격 증명을 만듭니다. 그러면 Azure Migrate 어플라이언스에서 검색을 시작할 때 해당 자격 증명을 사용합니다.
- **Hyper-V**: 검색에서 Hyper-V 호스트 자격 증명을 사용합니다. 서버에서 동일한 Hyper-V 호스트를 공유하는 경우 현재 검색을 분리할 수 있는 방법이 없습니다.  

## <a name="do-i-need-vcenter-server"></a>vCenter Server가 필요한가요?

예, vCenter Server가 VMware 환경에 있어야 Azure Migrate에서 검색을 수행할 수 있습니다. Azure Migrate는 vCenter Server에서 관리되지 않는 ESXi 호스트의 검색을 지원하지 않습니다.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Azure VM 평가의 크기 조정 옵션은 무엇인가요?

온-프레미스 크기 조정을 사용하는 경우 Azure Migrate는 평가를 위해 서버 성능 데이터를 고려하지 않습니다. Azure Migrate는 온-프레미스 구성을 기반으로 하여 VM 크기를 평가합니다. 성능 기반 크기 조정을 사용하는 경우 크기 조정은 사용률 데이터를 기반으로 합니다.

예를 들어 온-프레미스 서버에 50% CPU 사용률 및 50% 메모리 사용률의 4개 코어 및 8GB 메모리가 있는 경우 다음과 같습니다.

- 온-프레미스 크기 조정의 경우 4개 코어 및 8GB 메모리가 있는 Azure VM SKU를 권장합니다.
- 성능 기반 크기 조정의 경우 사용률을 고려하므로 2개 코어 및 4GB 메모리가 있는 VM SKU를 권장합니다.

마찬가지로 디스크 기반 크기 조정은 크기 조정 조건 및 스토리지 유형에 따라 달라집니다.

- 크기 조정 기준이 “성능 기반”이고 스토리지 유형이 자동인 경우 Azure Migrate는 대상 디스크 유형(표준, 프리미엄 또는 울트라 디스크)을 식별할 때 디스크의 IOPS 및 처리량 값을 고려합니다.
- 크기 조정 기준이 “온-프레미스로”이고 스토리지 유형이 프리미엄인 경우 Azure Migrate는 온-프레미스 디스크의 크기를 기준으로 프리미엄 디스크 SKU를 권장합니다. 크기 조정이 as-on-premises이고 스토리지 유형이 표준, 프리미엄 또는 울트라 디스크인 경우 동일한 논리가 디스크 크기 조정에 적용됩니다.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>성능 기록 및 사용률이 Azure VM 평가의 크기 조정에 영향을 주나요?

예, 성능 기록 및 사용률은 Azure VM 평가의 크기 조정에 영향을 줍니다.

### <a name="performance-history"></a>성능 기록

성능 기반 크기 조정의 경우에만 Azure Migrate는 온-프레미스 컴퓨터의 성능 기록을 수집한 다음, 이를 사용하여 Azure에서 VM 크기 및 디스크 유형을 권장합니다.

1. 어플라이언스에서 온-프레미스 환경을 지속적으로 프로파일링하여 20초마다 실시간 사용률 데이터를 수집합니다.
2. 어플라이언스에서 수집된 20초 샘플을 롤업하고, 이를 사용하여 15분마다 단일 데이터 요소를 만듭니다.
3. 데이터 요소를 만들기 위해 어플라이언스에서 모든 20초 샘플에서 최고 값을 선택합니다.
4. 어플라이언스에서 데이터 요소를 Azure에 보냅니다.

### <a name="utilization"></a>사용률

Azure에서 평가를 만들 때 Azure Migrate는 설정된 성능 기간 및 성능 기록 백분위수 값에 따라 유효 사용률 값을 계산한 다음, 이를 크기 조정에 사용합니다.

예를 들어 성능 기간을 1일로 설정하고 백분위수 값을 95번째 백분위수로 설정하면 Azure Migrate는 수집기에서 지난 1일 동안 보낸 15분 샘플 요소를 오름차순으로 정렬합니다. 유효 사용률로 95번째 백분위수 값을 선택합니다.

95번째 백분위수 값을 사용하면 이상값이 무시됩니다. Azure Migrate에서 99번째 백분위수를 사용하는 경우 이상값이 포함될 수 있습니다. 이상값을 누락하지 않고 해당 기간의 최고 사용량을 선택하려면 99번째 백분위수를 사용하도록 Azure Migrate를 설정합니다.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>가져오기 기반 평가는 검색 원본을 어플라이언스로 사용하는 평가와 어떻게 다른가요?

가져오기 기반 Azure VM 평가는 CSV 파일을 사용하여 Azure Migrate로 가져온 컴퓨터에서 만든 평가입니다. 4개의 필드(서버 이름, 코어 수, 메모리 및 운영 체제)만 가져와야 합니다. 참고할 사항은 다음과 같습니다.

 - 부팅 유형 매개 변수에 대한 가져오기 기반 평가에서 준비 상태 조건은 비교적 엄격하지 않습니다. 부팅 유형이 제공되지 않으면 컴퓨터에서 BIOS 부팅 유형을 사용하고 해당 컴퓨터가 **조건부로 준비됨** 으로 표시되지 않은 것으로 가정합니다. 검색 원본을 어플라이언스로 사용하는 평가에서 부팅 유형이 누락된 경우 준비 상태는 **조건부로 준비됨** 으로 표시됩니다. 준비 상태 계산의 이러한 차이는 가져오기 기반 평가가 수행될 때 마이그레이션 계획의 초기 단계에서 사용자에게 컴퓨터에 대한 모든 정보가 없을 수 있기 때문입니다.
 - 성능 기반 가져오기 평가는 사용자가 제공하는 사용률 값을 사용하여 크기 조정을 적절하게 계산합니다. 사용자가 사용률 값을 제공하므로 평가 속성에서 **성능 기록** 및 **백분위수 사용률** 옵션이 사용하지 않도록 설정됩니다. 검색 원본을 어플라이언스로 사용하는 평가에서 선택된 백분위수 값은 어플라이언스가 수집한 성능 데이터에서 선택됩니다.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>가져오기 기반 AVS 평가에서 제안된 마이그레이션 도구가 '알 수 없음'으로 표시되는 이유는 무엇인가요?

CSV 파일을 통해 가져온 컴퓨터의 경우 AVS 평가의 기본 마이그레이션 도구를 알 수 없습니다. 그러나 VMware 컴퓨터의 경우 VMware HCX(하이브리드 클라우드 확장) 솔루션을 사용하는 것이 좋습니다. [자세한 정보](../azure-vmware/install-vmware-hcx.md).

## <a name="what-is-dependency-visualization"></a>종속성 시각화란?

종속성 시각화를 사용하면 마이그레이션할 서버 그룹을 더 확실하게 평가할 수 있습니다. 종속성 시각화는 평가를 실행하기 전에 컴퓨터 종속성을 교차 확인합니다. 이는 Azure로 마이그레이션할 때 예기치 않은 중단을 방지하는 데 도움이 됩니다. Azure Migrate는 Azure Monitor의 서비스 맵 솔루션을 사용하여 종속성 시각화를 사용하도록 설정합니다. [자세히 알아보기](concepts-dependency-visualization.md).

> [!NOTE]
> 에이전트 기반 종속성 분석은 Azure Government에서 사용할 수 없습니다. 에이전트 없는 종속성 분석은 사용할 수 있습니다.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>에이전트 기반 및 에이전트 없음의 차이점은 무엇인가요?

다음 표에는 에이전트 없는 시각화와 에이전트 기반 시각화의 차이점이 요약되어 있습니다.

**요구 사항** | **에이전트 없음** | **에이전트 기반**
--- | --- | ---
지원 | 이 옵션은 현재 미리 보기에 있으며 VMware 환경의 서버에서만 사용할 수 있습니다. 지원되는 운영 체제를 [검토](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)하세요. | GA(일반 공급)
에이전트 | 에이전트를 교차 확인하려는 컴퓨터에 설치할 필요가 없습니다. | 분석하려는 각 온-프레미스 컴퓨터에 설치할 에이전트([MMA(Microsoft Monitoring Agent)](../azure-monitor/agents/agent-windows.md) 및 [종속성 에이전트](../azure-monitor/agents/agents-overview.md#dependency-agent))입니다. 
사전 요구 사항 | 필수 구성 요소 및 배포 요구 사항을 [검토](concepts-dependency-visualization.md#agentless-analysis)하세요. | 필수 구성 요소 및 배포 요구 사항을 [검토](concepts-dependency-visualization.md#agent-based-analysis)하세요.
Log Analytics | 필수 아님. | Azure Migrate는 종속성 시각화에 대한 [Azure Monitor 로그](../azure-monitor/logs/log-query-overview.md)의 [서비스 맵](../azure-monitor/vm/service-map.md) 솔루션을 사용합니다. [자세히 알아보기](concepts-dependency-visualization.md#agent-based-analysis).
작동 방식 | 종속성 시각화에 사용하도록 설정된 컴퓨터에서 TCP 연결 데이터를 캡처합니다. 검색 후 5분 간격으로 데이터를 수집합니다. | 컴퓨터에 설치된 서비스 맵 에이전트는 각 프로세스의 TCP 프로세스 및 인바운드/아웃바운드 연결에 대한 데이터를 수집합니다.
데이터 | 원본 컴퓨터 서버 이름, 프로세스, 애플리케이션 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 애플리케이션 이름 및 포트입니다. | 원본 컴퓨터 서버 이름, 프로세스, 애플리케이션 이름입니다.<br/><br/> 대상 컴퓨터 서버 이름, 프로세스, 애플리케이션 이름 및 포트입니다.<br/><br/> 연결 수, 대기 시간 및 데이터 전송 정보가 수집되어 Log Analytics 쿼리에 사용할 수 있습니다. 
시각화 | 단일 서버의 종속성 맵은 1시간에서 30일 사이의 기간으로 표시됩니다. | 단일 서버의 종속성 맵입니다.<br/><br/> 맵은 1시간 기간으로만 표시됩니다.<br/><br/> 서버 그룹의 종속성 맵입니다.<br/><br/> 맵 보기에서 그룹의 서버를 추가 및 제거합니다.
데이터 내보내기 | 지난 30일 간의 데이터는 CSV 형식으로 다운로드할 수 있습니다. | 데이터는 Log Analytics를 사용하여 쿼리할 수 있습니다.

## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>에이전트 없는 종속성 분석을 위해 어플라이언스를 배포해야 하나요?

예, [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포해야 합니다.

## <a name="do-i-pay-for-dependency-visualization"></a>종속성 시각화에 대한 비용을 지불해야 하나요?

아니요. [Azure Migrate 가격 책정](https://azure.microsoft.com/pricing/details/azure-migrate/)에 대해 자세히 알아보세요.

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>에이전트 기반 종속성 시각화를 위해 무엇을 설치해야 하나요?

에이전트 기반 종속성 시각화를 사용하려면 에이전트를 평가하려는 각 온-프레미스 컴퓨터에 다운로드하고 설치합니다.

- [MMA(Microsoft Monitoring Agent)](../azure-monitor/agents/agent-windows.md)
- [종속성 에이전트](../azure-monitor/agents/agents-overview.md#dependency-agent)
- 인터넷에 연결되지 않은 컴퓨터가 있는 경우 Log Analytics 게이트웨이를 다운로드하여 해당 컴퓨터에 설치합니다.

이러한 에이전트는 에이전트 기반 종속성 시각화를 사용하는 경우에만 필요합니다.

## <a name="can-i-use-an-existing-workspace"></a>기존 작업 영역을 사용해도 되나요?

예, 에이전트 기반 종속성 시각화의 경우 기존 작업 영역을 마이그레이션 프로젝트에 연결하여 종속성 시각화에 사용할 수 있습니다.

## <a name="can-i-export-the-dependency-visualization-report"></a>종속성 시각화 보고서를 내보낼 수 있나요?

아니요. 에이전트 기반 시각화의 종속성 시각화 보고서는 내보낼 수 없습니다. 그러나 Azure Migrate는 서비스 맵을 사용하며, [서비스 맵 REST API](/rest/api/servicemap/machines/listconnections)를 사용하여 종속성을 JSON 형식으로 검색할 수 있습니다.

## <a name="can-i-automate-agent-installation"></a>에이전트 설치를 자동화할 수 있나요?

에이전트 기반 종속성 시각화의 경우 다음을 수행합니다.

- [스크립트를 사용하여 종속성 에이전트를 설치](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent)합니다.
- MMA의 경우 [명령줄 또는 자동화](../azure-monitor/agents/log-analytics-agent.md#installation-options)를 사용하거나 [스크립트](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)를 사용합니다.
- 스크립트 외에도 Microsoft Endpoint Configuration Manager 및 Intigua와 같은 배포 도구를 사용하여 에이전트를 배포할 수 있습니다.

## <a name="what-operating-systems-does-mma-support"></a>MMA에서 지원하는 운영 체제는 무엇인가요?

- [MMA에서 지원하는 Windows 운영 체제](../azure-monitor/agents/log-analytics-agent.md#installation-options) 목록을 확인하세요.
- [MMA에서 지원하는 Linux 운영 체제](../azure-monitor/agents/log-analytics-agent.md#installation-options) 목록을 확인하세요.

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>1시간을 초과하는 기간 동안의 종속성을 시각화할 수 있나요?

에이전트 기반 시각화의 경우 최대 1시간 동안의 종속성을 시각화할 수 있습니다. 기록의 특정 날짜(최대 1개월)까지 이전으로 돌아갈 수 있지만 시각화의 최대 기간은 1시간입니다. 예를 들어 종속성 맵의 기간을 사용하여 어제의 종속성을 볼 수 있지만 1시간 기간의 종속성만 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용하여 더 긴 기간 동안의 [종속성 데이터를 쿼리](./how-to-create-group-machine-dependencies.md)할 수 ​​있습니다.

에이전트 없는 시각화의 경우 1시간에서 30일 사이의 기간에 있는 단일 서버의 종속성 맵을 볼 수 있습니다.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>10개가 넘는 서버가 있는 그룹에 대한 종속성을 시각화할 수 있나요?

최대 10개의 서버가 있는 그룹에 대한 [종속성을 시각화](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)할 수 있습니다. 10개가 넘는 서버가 있는 그룹이 있으면 그룹을 더 작은 그룹으로 분할한 다음, 종속성을 시각화하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[Azure Migrate 개요](migrate-services-overview.md)를 참조하세요.