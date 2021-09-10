---
title: Azure Migrate를 사용하여 평가할 서버 그룹화 | Microsoft Docs
description: Azure Migrate 서비스를 사용하여 평가를 실행하기 전에 서버를 그룹화하는 방법을 설명합니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 9ffa031a9058eb6864298c5c9387402955a84968
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567188"
---
# <a name="create-a-group-for-assessment"></a>평가를 위한 그룹 만들기

이 문서에서는 Azure Migrate: 검색 및 평가를 사용하여 평가를 수행하기 위해 서버 그룹을 만드는 방법을 설명합니다.

[Azure Migrate](migrate-services-overview.md)를 사용하면 Azure로 쉽게 마이그레이션할 수 있습니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

## <a name="grouping-servers"></a>서버 그룹화

서버를 그룹으로 수집하여 Azure로의 마이그레이션에 적합한지를 평가하고 이들에 대한 Azure 크기 조정 및 비용 예측을 확보합니다. 다음 몇 가지 방법으로 그룹을 만들 수 있습니다.

- 함께 마이그레이션할 서버를 아는 경우에는 Azure Migrate에서 그룹을 수동으로 만들 수 있습니다.
- 함께 그룹화할 서버에 대해 잘 모르는 경우에는 Azure Migrate의 종속성 시각화 기능을 사용하여 그룹을 만들 수 있습니다. 

> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

## <a name="create-a-group-manually"></a>수동으로 그룹 만들기

[평가를 만들면서](how-to-create-assessment.md) 동시에 그룹을 만들 수 있습니다.

평가를 만들면서 동시에 수동으로 그룹을 만들려면 다음을 수행합니다.

1. Azure Migrate 프로젝트 > **개요** 에서 **서버 평가 및 마이그레이션** 을 클릭합니다. **Azure Migrate: 검색 및 평가** 에서 **그룹** 을 클릭합니다.
    - Azure Migrate: 검색 및 평가 도구를 아직 추가하지 않은 경우 클릭하여 추가합니다. [자세히 알아보기](how-to-assess.md).
    - Azure Migrate 프로젝트를 아직 만들지 않은 경우 [자세히 알아보세요](./create-manage-projects.md).

    ![그룹 선택](./media/how-to-create-a-group/select-groups.png)

2. **그룹** 아이콘을 클릭합니다.
3. **그룹 만들기** 에서 그룹 이름을 지정하고 **어플라이언스 이름** 에서 서버 검색에 사용하고 있는 Azure Migrate 어플라이언스를 선택합니다.
4. 서버 목록에서 그룹에 추가하려는 서버를 선택하고 **만들기** 를 선택합니다.

    ![그룹 만들기](./media/how-to-create-a-group/create-group.png)

이제 [Azure VM 평가](how-to-create-assessment.md), [AVS(Azure VMware Solution) 평가](how-to-create-azure-vmware-solution-assessment.md), [Azure SQL 평가](how-to-create-azure-sql-assessment.md), [Azure App Service 평가](how-to-create-azure-app-service-assessment.md)를 만들 때 이 그룹을 사용할 수 있습니다.

## <a name="refine-a-group-with-dependency-mapping"></a>종속성 매핑을 사용하여 그룹 구체화

종속성 매핑은 서버 전체의 종속성을 시각화하는 데 도움이 됩니다. 더 높은 수준의 신뢰도로 서버 그룹을 평가하려는 경우 일반적으로 종속성 매핑을 사용합니다.

- 이는 평가를 실행하기 전에 서버 종속성을 교차 확인하는 데 도움이 됩니다.
- 또한 모든 요소를 확인했는지 검토하여 마이그레이션이 진행되는 동안 예기치 않은 중단을 방지할 수 있기 때문에, Azure로의 마이그레이션을 효과적으로 계획하는 데에도 도움이 됩니다.
- 함께 마이그레이션해야 하는 모든 상호 종속적인 시스템을 검색하고, 실행 중인 시스템이 여전히 사용자에게 서비스를 제공하고 있는지 아니면 마이그레이션 대신에 서비스를 해제하는 것이 적합한지를 파악할 수 있습니다.

[종속성 매핑 설정](how-to-create-group-machine-dependencies.md)을 이미 완료했으며 기존 그룹을 구체화하려는 경우 다음을 수행합니다.

1. **서버** 탭의 **Azure Migrate: 검색 및 평가** 타일에서 **그룹** 을 클릭합니다.
2. 구체화하려는 그룹을 클릭합니다.
    - 종속성 매핑을 아직 설정하지 않았다면 **종속성** 열에 **설치 필요** 상태가 표시됩니다. 종속성을 시각화하려는 각 서버에 대해 **설치 필요** 를 클릭합니다. 서버 종속성을 매핑하려면 각 서버에 에이전트 두 개를 설치해야 합니다. [자세히 알아보기](how-to-create-group-machine-dependencies.md).

        ![종속성 매핑 추가](./media/how-to-create-a-group/add-dependency-mapping.png)

    - 이미 종속성 매핑을 설정했다면 그룹 페이지에서 **종속성 보기** 를 클릭하여 그룹 종속성 맵을 엽니다.

3. **종속성 보기** 를 클릭하면 그룹 종속성 맵에 다음이 표시됩니다.

    - 종속성 에이전트가 설치된 그룹의 모든 서버로 혹은 해당 서버로부터 연결된 인바운드(클라이언트) 및 아웃바운드(서버) TCP 연결
    - 종속성 에이전트가 설치되어 있지 않은 종속 서버는 포트 번호별로 그룹화됩니다.
    - 종속성 에이전트가 설치된 종속성 서버는 별도의 상자에 표시됩니다.
    - 서버 내에서 실행되는 프로세스입니다. 각 서버 상자를 확장하여 프로세스를 확인합니다.
    - 서버 속성(FQDN, 운영 체제, MAC 주소 포함) 각 서버 상자를 클릭하여 세부 정보를 확인합니다.

4. 선택한 시간 간격의 종속성을 확인하려면 시작 및 종료 날짜 또는 기간을 지정하여 시간 범위(기본값: 1시간)를 수정합니다.

    > [!NOTE]
    > 시간 범위는 최대 1시간까지 가능합니다. 더 긴 시간 범위가 필요한 경우 [Azure Monitor를 사용하여 종속성 데이터 쿼리하기](how-to-create-group-machine-dependencies.md)를 사용하여 더 긴 기간을 확보합니다.

5. 그룹에 추가하거나 그룹에서 제거하려는 종속성을 확인한 후에 그룹을 수정할 수 있습니다. Ctrl+클릭을 사용하여 그룹에 서버를 추가하거나 그룹에서 서버를 제거합니다.

    - 검색된 서버만 추가할 수 있습니다.
    - 서버를 추가하거나 제거하면 그룹에 대한 이전의 평가가 무효화됩니다.
    - 그룹을 수정할 때 필요에 따라 새 평가를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

[종속성 매핑](how-to-create-group-machine-dependencies.md)을 설정하고 사용하여 신뢰도가 높은 그룹을 만드는 방법을 알아봅니다.