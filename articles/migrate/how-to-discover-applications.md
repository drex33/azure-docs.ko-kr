---
title: Azure Migrate를 사용하여 온-프레미스 서버에서 소프트웨어 인벤토리 검색
description: Azure Migrate 검색 및 평가를 사용하여 온-프레미스 서버에서 소프트웨어 인벤토리를 검색하는 방법을 알아봅니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: c337229a8e9a4fa98e80c955c317813950c51bfb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535935"
---
# <a name="discover-installed-software-inventory-web-apps-and-sql-server-instances-and-databases"></a>설치된 소프트웨어 인벤토리, 웹앱 및 SQL Server 인스턴스와 데이터베이스 검색

이 문서에서는 Azure Migrate: 검색 및 평가 도구를 사용하여 설치된 소프트웨어 인벤토리, 웹앱 및 VMware에서 실행 중인 서버의 SQL Server 인스턴스와 데이터베이스를 검색하는 방법을 설명합니다.

소프트웨어 인벤토리를 수행하면 워크로드용 Azure에 대한 마이그레이션 경로를 식별하고 조정하는 데 도움이 됩니다. 소프트웨어 인벤토리는 Azure Migrate 어플라이언스를 사용하여 제공된 서버 자격 증명으로 검색을 수행합니다. 이 데이터를 수집하기 위해 서버에 에이전트가 설치되지 않습니다.

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate: 검색 및 평가 도구가 추가된 [프로젝트를 만들었는지](./create-manage-projects.md) 확인합니다.
- [VMware 요구 사항](migrate-support-matrix-vmware.md#vmware-requirements)을 검토하여 소프트웨어 인벤토리를 수행합니다.
- 어플라이언스를 설정하기 전에 [어플라이언스 요구 사항](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements)을 검토합니다.
- 서버에서 소프트웨어 인벤토리를 시작하기 전에 [애플리케이션 검색 요구 사항](migrate-support-matrix-vmware.md#software-inventory-requirements)을 검토합니다.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 배포 및 구성

1. Azure Migrate 어플라이언스 배포 [요구 사항을 검토](migrate-appliance.md#appliance---vmware)합니다.
2. 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 Azure URL을 검토합니다.
3. 검색 및 평가 중에 어플라이언스에서 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---vmware)합니다.
4. 어플라이언스에 대한 포트 액세스 요구 사항에 [유의](migrate-support-matrix-vmware.md#port-access-requirements)하세요.
5. [Azure Migrate 어플라이언스를 배포](how-to-set-up-appliance-vmware.md)하여 검색을 시작합니다. 어플라이언스를 배포하려면 OVA 템플릿을 다운로드하고 VMware로 가져와 vCenter Server에서 실행되는 서버를 만듭니다. 어플라이언스를 배포한 후에는 프로젝트에 등록하고 검색을 시작하도록 구성해야 합니다.
6. 어플라이언스를 구성할 때는 어플라이언스 구성 관리자에서 다음을 지정해야 합니다.
    - 연결할 vCenter Server의 세부 정보.
    - VMware 환경에서 서버를 검색하도록 범위가 지정된 vCenter Server 자격 증명.
    - 서버 자격 증명으로, 도메인/Windows(비도메인)/Linux(비도메인) 자격 증명일 수 있습니다. 자격 증명을 제공하는 방법과 처리하는 방법에 대해 [자세히 알아보세요](add-server-credentials.md).

## <a name="verify-permissions"></a>권한 확인

- 검색 및 평가를 위해 [vCenter Server 읽기 전용 계정을 만들어야](./tutorial-discover-vmware.md#prepare-vmware) 합니다. 읽기 전용 계정이 서버와 상호 작용하여 소프트웨어 인벤토리를 수행하기 위해서는 **가상 머신** > **게스트 작업** 에 대해 사용된 권한이 있어야 합니다.
- 애플리케이션 검색을 위해 어플라이언스 구성 관리자에서 여러 도메인 및 비도메인(Windows/Linux) 자격 증명을 추가할 수 있습니다. Windows 서버에 게스트 사용자 계정이 필요하고, 모든 Linux 서버에 대한 정규/일반 사용자 계정(sudo가 아닌 액세스)이 필요합니다. 자격 증명을 제공하는 방법 및 자격 증명을 처리하는 방법을 [자세히 알아보세요](add-server-credentials.md).

### <a name="add-credentials-and-initiate-discovery"></a>자격 증명 추가 및 검색 시작

1. 어플라이언스 구성 관리자를 열고 어플라이언스의 필수 구성 요소 확인 및 등록을 완료합니다.
2. **자격 증명 관리 및 검색 원본** 패널로 이동합니다.
1.  **1단계: vCenter Server 자격 증명 제공** 에서 **자격 증명 추가** 를 클릭하여 어플라이언스가 vCenter Server에서 실행 중인 서버를 검색하는 데 사용할 vCenter Server 계정에 대한 자격 증명을 제공합니다.
1. **2단계: vCenter Server 세부 정보 제공** 에서 **검색 원본 추가** 를 클릭하여 드롭다운에서 자격 증명 이름을 선택하고, vCenter Server 인스턴스 :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="어플라이언스 구성 관리자의 vCenter Server 세부 정보에 대한 패널 3":::의 **IP 주소/FQDN** 을 지정합니다.
1. **3단계: 서버 자격 증명을 제공하여 소프트웨어 인벤토리, 에이전트 없는 종속성 분석, SQL Server 인스턴스 및 데이터베이스 검색, VMware 환경에서 ASP.NET 웹앱 검색을 수행합니다.** **자격 증명 추가** 를 클릭하여 소프트웨어 인벤토리를 시작하기 위한 여러 서버 자격 증명을 제공합니다.
1. **검색 시작** 을 선택하여 vCenter Server 검색을 시작합니다.

 vCenter Server 검색이 완료되면 어플라이언스는 설치된 애플리케이션, 역할 및 기능(소프트웨어 인벤토리)의 검색을 시작합니다. 소요 시간은 검색할 서버 수에 따라 달라집니다. 서버가 500개인 경우 검색된 인벤토리가 Azure Migrate 포털에 표시될 때까지 약 1시간이 걸립니다.

## <a name="review-and-export-the-inventory"></a>인벤토리 검토 및 내보내기

소프트웨어 인벤토리가 완료된 후 Azure Portal에서 인벤토리를 검토하고 내보낼 수 있습니다.

1. **Azure Migrate - 서버, 데이터베이스 및 웹앱** > **Azure Migrate: 검색 및 평가** 에서 표시된 수를 클릭하면 **검색된 서버** 페이지가 열립니다.

    > [!NOTE]
    > 또한 이 단계에서는 검색된 서버에 대한 종속성 분석을 사용하도록 설정하여 평가하려는 서버 전체에서 종속성을 시각화할 수 있습니다. 종속성 분석에 대해 [자세히 알아보세요](concepts-dependency-visualization.md).

2. **소프트웨어 인벤토리** 열에서 표시된 수를 클릭하여 검색된 애플리케이션, 역할 및 기능을 검토합니다.
4. 인벤토리를 내보내려면 **검색된 서버** 에서 **소프트웨어 인벤토리 내보내기** 를 클릭합니다.

소프트웨어 인벤토리는 Excel 형식으로 내보내고 다운로드됩니다. **소프트웨어 인벤토리** 시트에는 모든 서버에서 검색된 모든 앱이 표시됩니다.

## <a name="discover-sql-server-instances-and-databases"></a>SQL Server 인스턴스 및 데이터베이스 검색

- 또한 소프트웨어 인벤토리는 VMware 환경에서 실행 중인 SQL Server 인스턴스를 식별합니다.
- 어플라이언스 구성 관리자에서 Windows 인증 또는 SQL Server 인증 자격 증명을 제공하지 않은 경우, 어플라이언스가 해당 자격 증명을 사용하여 각 SQL Server 인스턴스에 연결할 수 있도록 자격 증명을 추가합니다.

    > [!NOTE]
    > 어플라이언스는 네트워크에 대한 가시성이 확보된 SQL Server에만 연결할 수 있지만, 소프트웨어 인벤토리 자체에는 네트워크에 대한 가시성이 필요하지 않을 수 있습니다.

일단 연결되면 어플라이언스는 SQL Server 인스턴스 및 데이터베이스의 구성 및 성능 데이터를 수집합니다. SQL Server 구성 데이터는 24시간마다 업데이트되고, 성능 데이터는 30초마다 캡처됩니다. 따라서 SQL Server 인스턴스의 속성과 데이터베이스 상태, 호환성 수준 등의 데이터베이스에 대한 변경이 포털에서 업데이트되는 데 최대 24 시간이 걸릴 수 있습니다.

## <a name="discover-aspnet-web-apps"></a>ASP.NET 웹앱 검색

소프트웨어 인벤토리는 검색된 서버의 기존 웹 서버 역할을 식별합니다. 서버에서 웹 서버 역할을 사용한 것으로 확인되면 Azure Migrate는 서버에서 웹앱 검색을 수행합니다.
사용자는 어플라이언스에서 도메인과 비도메인 자격 증명을 모두 추가할 수 있습니다. 사용된 계정에 원본 서버에 대한 로컬 관리자 권한이 있는지 확인합니다. Azure Migrate는 각 서버에 자격 증명을 자동으로 매핑하므로 자격 증명을 수동으로 매핑하지 않아도 됩니다. 가장 중요한 점은 해당 자격 증명이 Microsoft로 전송되지 않고 원본 환경에서 실행되는 어플라이언스에 남아 있다는 것입니다.
어플라이언스가 연결되면 IIS 웹 서버 및 ASP.NET 웹앱에 대한 구성 데이터를 수집합니다. 웹앱 구성 데이터는 24시간마다 한 번 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

- 검색한 서버에 대한 [평가를 만듭니다](how-to-create-assessment.md).
- Azure App Service로 마이그레이션할 [웹앱 평가](how-to-create-azure-app-service-assessment.md)
