---
title: 기존 Azure Migrate 프로젝트에서 SQL Server 인스턴스 검색
description: 기존 Azure Migrate 프로젝트에서 SQL Server 인스턴스를 검색하는 방법에 대해 알아봅니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 38169324211a22012426b895d66bc6d0015f5587
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536334"
---
# <a name="discover-web-apps-and-sql-server-instances-in-an-existing-project"></a>기존 프로젝트에서 웹앱 및 SQL Server 인스턴스 검색

이 문서에서는 Azure SQL 평가 기능 미리 보기 및/또는 Azure App Service 평가 기능 미리 보기 전에 만들어진 [Azure Migrate](./migrate-services-overview.md) 프로젝트에서 웹앱, SQL Server 인스턴스 및 데이터베이스를 검색하는 방법을 설명합니다.

온-프레미스 컴퓨터에서 실행되는 ASP.NET 웹앱 및 SQL Server 인스턴스 및 데이터베이스를 검색하면 Azure에 대한 마이그레이션 경로를 식별하고 조정할 수 있습니다. Azure Migrate 어플라이언스는 Windows OS 도메인 또는 비도메인 자격 증명을 사용하여 이 검색을 수행하거나 대상 서버에서 실행되는 SQL Server 인스턴스 및 데이터베이스에 액세스할 수 있는 SQL Server 인증 자격 증명을 사용합니다.
이 검색 프로세스는 에이전트 없이 대상 서버에 설치되지 않습니다.

## <a name="before-you-start"></a>시작하기 전에

- 다음 사항을 확인하세요.
    - 해당 지역에 대한 SQL 및 웹앱 평가 기능 발표 전에 [Azure Migrate 프로젝트](./create-manage-projects.md)를 만들었습니다.
    - 프로젝트에 [Azure Migrate: 검색 및 평가](./how-to-assess.md) 도구를 추가합니다.
- [앱 검색 지원 및 요구 사항](./migrate-support-matrix-vmware.md#vmware-requirements)을 검토합니다.
-  앱 검색을 실행하는 서버에 PowerShell 버전 2.0 이상이 설치되어 있고 VMware 도구(10.2.0 이상)가 설치되어 있는지 확인합니다.
- Azure Migrate 어플라이언스 배포 [요구 사항](./migrate-appliance.md)을 검토합니다.
- 구독에 리소스를 만드는 데 [필요한 역할](./create-manage-projects.md#verify-permissions)이 있는지 확인합니다.
- 어플라이언스에서 인터넷에 액세스할 수 있는지 확인

## <a name="enable-discovery-of-aspnet-web-apps-and-sql-server-instances-and-databases"></a>ASP.NET 웹앱 및 SQL Server 인스턴스 및 데이터베이스 검색을 사용하도록 설정

1. Azure Migrate 프로젝트에서 다음 중 하나에 해당합니다.
    - 허브 타일에서 **사용 안 함** 을 선택하거나 :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="SQL 및 웹앱 검색을 사용하지 않는 Azure Migrate 허브 타일":::을 선택합니다.
    - 서버 검색 페이지의 SQL 인스턴스 또는 웹앱 열에서 **사용 안 함** 을 선택합니다. :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="SQL 및 웹앱 검색을 사용하지 않는 검색된 Azure Migrate 서버 블레이드":::
2. ASP.NET 웹앱과 SQL Server 인스턴스 및 데이터베이스를 검색하려면 다음 단계를 따릅니다.
    - **업그레이드** 를 선택하여 필요한 리소스를 만듭니다.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Azure Migrate 어플라이언스를 업그레이드하는 단추":::
    - 어플라이언스에서 실행 중인 서비스가 최신 버전으로 업데이트되었는지 확인합니다. 이렇게 하려면 어플라이언스 서버에서 어플라이언스 구성 관리자를 시작하고 설치 사전 요구 사항 패널에서 어플라이언스 서비스 보기를 선택합니다.
        - 어플라이언스 및 해당 구성 요소가 자동으로 업데이트됩니다. :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="어플라이언스 버전 확인":::
    - 어플라이언스 구성 관리자의 자격 증명 및 검색 원본 관리 패널에서 검색할 데이터베이스와 데이터베이스 SQL Server에 대한 Sysadmin 액세스 권한이 있는 도메인 또는 SQL Server 인증 자격 증명을 추가합니다.
    - ASP.NET 웹앱 검색은 사용된 계정에 서버에 대한 로컬 관리자 권한이 있는 한 도메인 및 비도메인 Windows OS 자격 증명 모두에서 작동합니다.
    [여기](./tutorial-discover-vmware.md#start-continuous-discovery)에 강조 표시된 대로 어플라이언스의 자동 자격 증명 매핑 기능을 활용할 수 있습니다.

    다음 몇 가지 사항을 주의해야 합니다.
    - 소프트웨어 인벤토리가 이미 사용하도록 설정되어 있는지 확인하거나 도메인 또는 도메인이 아닌 자격 증명을 제공하여 동일하게 설정합니다. SQL Server 인스턴스 및 ASP.NET 웹앱을 검색하려면 소프트웨어 인벤토리를 수행해야 합니다.
    - 어플라이언스는 추가될 때 AD를 사용하여 도메인 자격 증명의 유효성을 검사하려고 합니다. 어플라이언스 서버에 자격 증명과 연결된 AD 서버에 대한 네트워크 회선이 있는지 확인합니다. 비도메인 자격 증명 및 SQL Server 인증과 관련된 자격 증명은 검증되지 않습니다.

3. 원하는 자격 증명이 추가되면 검색 시작을 선택하여 검색을 시작합니다.

> [!Note]
>Azure App Service 또는 Azure SQL에 대한 평가를 생성하기 전에 웹앱 및 SQL 검색이 잠시 동안 실행되도록 허용합니다. 웹앱 및 SQL Server 인스턴스 및 데이터베이스의 검색을 완료할 수 없는 경우 해당 인스턴스는 평가 보고서에서 **알 수 없음** 으로 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL 평가](./how-to-create-azure-sql-assessment.md)를 만드는 방법을 알아보기
- [Azure SQL 평가](./concepts-azure-sql-assessment-calculation.md)에 대해 자세히 알아보기
- [Azure App Service 평가](./how-to-create-azure-app-service-assessment.md)를 만드는 방법 알아보기
- [Azure App Service 평가](./concepts-azure-webapps-assessment-calculation.md)에 대해 자세히 알아보기