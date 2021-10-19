---
title: Azure SQL Database로 관리 및 개발할 랩 설정 | Azure Lab Services
description: Azure SQL Database를 사용하여 관리 및 개발할 랩을 설정하는 방법을 알아봅니다.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: c39229354d7628c2279465cc1029001e29a711fd
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176513"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>SQL Server로 관리 및 개발하기 위한 랩 설정

이 문서에서는 Azure Lab Services에서 기본 SQL Server 관리 및 개발 클래스에 대한 랩을 설정하는 방법을 설명합니다.  데이터베이스 개념은 대학의 대다수 컴퓨터 공학 전공에서 학습하는 입문 과정 중 하나입니다. SQL(구조적 쿼리 언어)은 국제 표준입니다.  SQL은 데이터베이스의 콘텐츠 추가, 액세스 및 관리를 포함하여 데이터베이스 관리와 관련된 표준 언어입니다.  이는 빠른 처리, 검증된 안정성, 편의성 및 사용의 유연성으로 알려져 있습니다.

이 문서에서는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)및 [Azure Data Studio](https://github.com/microsoft/azuredatastudio)를 사용하여 랩에서 가상 머신 템플릿을 설정하는 방법을 보여줍니다.  이 랩에서는 전체 랩에서 하나의 공유 [SQL Server 데이터베이스](../azure-sql/database/sql-database-paas-overview.md)를 사용합니다. [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)는 Azure에서 제공하는 PaaS(Platform As a Service) 데이터베이스 엔진입니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정에 대한 자습서](./tutorial-setup-lab-account.md)를 참조하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명된 설정을 사용합니다. Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Marketplace 이미지 지정](specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
| Marketplace 이미지 | 랩 계정 내에서 사용하려면 'Windows 10 Enterprise N(x64)의 Visual Studio 2019 Community(최신 릴리스)' 이미지를 사용하도록 설정합니다. |

### <a name="shared-resource-configuration"></a>공유 리소스 구성

Lab Services에서 공유 리소스를 사용하려면 먼저 가상 네트워크와 리소스 자체를 만들어야 합니다.  가상 네트워크를 만들어 랩에 연결하려면 [Azure Lab Services에서 공유 리소스를 사용하여 랩을 만드는 방법](how-to-create-a-lab-with-shared-resource.md)을 따르세요.  Lab Services 외부의 리소스는 별도로 청구되며 랩 예상 비용에 포함되지 않습니다.

>[!WARNING]
>랩을 만들기 전에 랩에 대한 공유 리소스를 설정해야 합니다.  랩을 만들기 *전에* vnet이 [랩 계정으로 피어링](how-to-connect-peer-virtual-network.md)되지 않는 경우 랩은 공유 리소스에 액세스할 수 없습니다.

네트워킹 측면을 처리했으므로 이제 SQL Server 데이터베이스를 만들 수 있습니다.  Azure SQL Database에 대한 가장 빠른 배포 옵션인 [단일 데이터베이스](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)를 만들려고 합니다.  다른 배포 옵션의 경우 [탄력적 풀](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), [관리되는 인스턴스](../azure-sql/managed-instance/instance-create-quickstart.md)또는 [SQL 가상 머신](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md)을 만듭니다.

1. Azure Portal 메뉴에서 **새 리소스 만들기** 를 선택합니다.
2. **SQL Database** 를 선택하고 **만들기** 단추를 클릭합니다.
3. **SQL Database 만들기** 양식의 **기본 사항** 탭에서 데이터베이스에 대한 리소스 그룹을 선택합니다.  *sqldb-rg* 를 사용해보겠습니다.
4. **데이터베이스 이름** 으로 *classlabdb* 를 입력합니다.
5. **서버** 설정에서 **새로 만들기** 를 클릭하여 데이터베이스를 보관할 새 서버를 만듭니다.
6. **새 서버** 플라이아웃에서 서버 이름을 입력합니다.  여기서는 *classlabdbserver* 를 사용하겠습니다.  서버 이름은 전역적으로 고유해야 합니다.
7. **서버 관리자 로그인** 에 *azureuser* 를 입력합니다.
8. 기억할 수 있는 암호를 입력합니다.  암호는 8자 이상이어야 하며 특수 문자를 포함해야 합니다.
9. 위치로 **지역** 을 선택합니다.  가능하면 랩 계정 및 피어링 vnet과 동일한 위치를 입력하여 대기 시간을 최소화합니다.
10. **확인** 을 클릭하여 **SQL Database 만들기** 양식으로 돌아갑니다.
11. **컴퓨팅 + 스토리지** 설정에서 **데이터베이스 구성** 링크를 클릭합니다.
12. 클래스에 필요한 경우 데이터베이스 설정을 수정합니다.  프로비전된 옵션 및 서버리스 옵션 중에서 선택할 수 있습니다.  이 예제에서는 최대 vCore가 4이고 최소 vCore가 1인 자동 확장 서버리스 옵션을 사용합니다. 자동 일시 정지 설정은 최소 1시간으로 유지됩니다. **적용** 을 클릭합니다.
13. **다음: 네트워킹** 단추를 클릭합니다.
14. 네트워킹 탭에서 **연결 방법** 에 대한 프라이빗 엔드포인트를 선택합니다.
15. **프라이빗 엔드포인트** 섹션에서  **추가** 를 클릭합니다.
16. **프라이빗 엔드포인트 만들기** 플라이아웃에서 랩 계정으로 피어링된 가상 네트워크와 동일한 리소스 그룹을 선택합니다.
17. **위치** 에서 가상 네트워크와 동일한 위치를 선택합니다.
18. **이름** 에 *labsql-endpt* 를 입력합니다.
19. 대상 하위 리소스를 SqlServer로 설정된 상태를 유지합니다.
20. **가상 네트워크** 에서 랩 계정에 피어링된 동일한 가상 네트워크를 선택합니다.
21. **서브넷** 에서 엔드포인트를 호스트할 서브넷을 선택합니다.  엔드포인트에 할당되는 IP는 해당 서브넷에 할당된 범위를 벗어납니다.
22. **개인 DNS와 통합** 을 **아니요** 로 설정합니다. 간단히 하기 위해 개인 DNS 영역 또는 자체 DNS 서버를 통해 Azure의 DNS를 사용합니다.
23. **확인** 을 클릭합니다.
24. **다음: 추가 설정** 을 선택하여 사용자 지정 설정을 더 많이 구성합니다.
25. **기존 데이터 사용** 설정에서 **샘플** 을 선택합니다.  데이터베이스를 만들 때 AdventureWorksLT 데이터베이스의 데이터가 사용됩니다.
26. **검토 + 만들기** 를 클릭합니다.
27. **만들기** 를 클릭합니다.

SQL Database 배포가 완료되면 랩을 만들고 랩 템플릿 머신에 소프트웨어를 설치할 수 있습니다.

### <a name="lab-settings"></a>랩 설정

클래스룸 랩을 설정하는 경우 아래 표의 설정을 사용합니다. 클래스룸 랩을 만드는 방법에 대한 자세한 내용은 [클래스룸 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
| Virtual Machine 크기 | 중간. 이 크기는 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다. |
| 가상 머신 이미지 | Windows 10 Enterprise N(x64)의 Visual Studio 2019 Community(최신 릴리스) |

이제 랩을 만들었으므로 필요한 소프트웨어로 템플릿 머신을 수정하겠습니다.

## <a name="visual-studio"></a>Visual Studio

위에서 선택한 이미지에는 [Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/)가 포함됩니다.  모든 워크로드 및 도구 집합이 이미지에 이미 설치되어 있습니다.  Visual Studio Installer를 사용하여 원하는 [선택적 도구를 설치](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true)할 수 있습니다.  [Visual Studio에 로그인](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019&preserve-view=true#how-to-sign-in-to-visual-studio)하여 Community Edition의 잠금을 해제합니다.

Visual Studio에는 SSDT(SQL Server Data Tools)를 포함하는 **데이터 저장 및 처리** 도구 세트가 포함되어 있습니다.  SSDT 기능에 대한 자세한 내용은 [SQL Server Data Tools 개요](/sql/ssdt/sql-server-data-tools)를 참조하세요.  클래스에 대한 공유 SQL Server에 대한 연결이 성공적인지 확인하려면 [데이터베이스에 연결 및 기존 개체 찾아보기](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects)를 참조하세요. 메시지가 표시되면 SQL Server 인스턴스에 연결할 수 있는 [허용된 컴퓨터 목록](../azure-sql/database/firewall-configure.md)에 템플릿 머신 IP를 추가합니다.

Visual Studio는 **웹 및 클라우드** 및 **데스크톱 및 모바일** 워크로드를 비롯한 여러 워크로드를 지원합니다.  이러한 워크로드는 모두 데이터 원본으로 SQL Server을 지원합니다. SQL Server에 ASP.NET Core를 사용하는 방법에 대한 자세한 내용은 [Azure App Service에서 ASP.NET Core 및 SQL Database 앱 빌드](../app-service/tutorial-dotnetcore-sqldb-app.md) 자습서를 참조하세요.  [System.Data.SqlClient](/dotnet/api/system.data.sqlclient) 라이브러리를 사용하여 [Xamarin](/xamarin) 앱에서 SQL Database에 연결합니다.

## <a name="install-azure-data-studio"></a>Azure Data Studio 설치

[Azure Data Studio](https://github.com/microsoft/azuredatastudio)는 Windows, macOS 및 Linux에서 온-프레미스 및 클라우드 데이터 플랫폼의 제품군을 사용하는 데이터 전문가를 위한 다중 데이터베이스 플랫폼 간 데스크톱 환경입니다.

1. [Windows용 Azure Data Studio *시스템* 설치 프로그램](https://go.microsoft.com/fwlink/?linkid=2127432)을 다운로드합니다. 지원되는 다른 운영 체제에 대한 설치 프로그램을 찾으려면 [Azure Data Studio](/sql/azure-data-studio/download) 다운로드 페이지로 이동합니다.
2. **사용권 계약** 페이지에서 **동의함** 을 선택합니다. **다음** 을 클릭합니다.
3. **대상 위치 선택** 페이지에서 **다음** 을 클릭합니다.
4. **시작 메뉴 폴더 선택** 페이지에서 **다음** 을 클릭합니다.
5. **추가 작업 선택** 페이지에서 바탕 화면 아이콘을 원하는 경우 **바탕 화면 아이콘 만들기** 를 선택합니다.  **다음** 을 클릭합니다.
6. **설치 준비 완료** 페이지에서 **다음** 을 클릭합니다.
7. 설치 프로그램이 실행될 때까지 기다립니다.  **Finish** 를 클릭합니다.

Azure Data Studio가 설치되었으므로 Azure SQL Database에 대한 연결을 설정하겠습니다.

1. Azure Data Studio의 **시작** 페이지에서 **새 연결** 링크를 클릭합니다.
2. **연결 세부 정보** 상자에 필요한 정보를 입력합니다.
    - **서버** 를 *classlabdbserver.database.windows.net* 으로 설정합니다.
    - **사용자** 이름을 *azureuser* 로 설정합니다.
    - **암호** 를 데이터베이스를 만드는 데 사용되는 암호로 설정합니다.
    - **암호 저장** 을 선택합니다.
    - **데이터베이스** 에 대해 *classlab db* 를 선택합니다.
3. **연결** 을 클릭합니다.

## <a name="install-sql-server-management-studio"></a>SQL Server Management Studio를 설치합니다.

[SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)는 SQL 인프라를 관리하기 위한 통합 환경입니다.  SSMS는 데이터베이스 관리자가 데이터 인프라를 배포, 모니터링 및 업그레이드하는 데 사용하는 도구입니다.

1. [SQL Server Management Studio를 다운로드](https://aka.ms/ssmsfullsetup)합니다. 다운로드한 후 설치 프로그램을 시작합니다.
2. **시작** 페이지에서 **설치** 를 클릭합니다.
3. **설치 완료** 페이지에서 **닫기** 를 클릭합니다.
4. SQL Server Management Studio를 시작합니다.  
5. **종속성 구성 프로세스** 페이지에서 **닫기** 를 클릭합니다.

SSMS가 설치되지 않은 경우 [SQL Server를 연결 및 쿼리](/sql/ssms/tutorials/connect-query-sql-server)할 수 있습니다. 연결을 설정할 때 다음 값을 사용합니다.

- 서버 유형: 데이터베이스 엔진
- 서버 이름: *classlabdbserver.database.windows.net*
- 인증: SQL Server 인증
- 로그인: *azureuser*
- 암호: 데이터베이스를 만드는 데 사용하는 암호

## <a name="cost-estimate"></a>예상 비용

이 수업의 가능한 예상 비용을 살펴보겠습니다. 예상치에는 SQL Server 실행 비용은 포함되지 않습니다.  데이터베이스 가격 책정에 대한 현재 세부 정보는 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database)을 참조하세요.

25명의 학생이 있는 수업을 예로 들겠습니다. 예약된 수업 시간은 20시간입니다. 예약된 수업 시간 외에도 숙제 또는 과제에 사용하도록 각 학생에게 10시간이 할당됩니다. 선택한 가상 머신 크기는 랩 단위가 42개인 중간 크기입니다.

다음 예제는 이 수업의 가능한 예상 비용입니다.

학생 25명\*(예약 시간 20시간 \+ 할당 시간 10시간) * 시간당 0.42 USD = 315.00 USD

>[!IMPORTANT]
>예상 비용은 예제 용도로만 사용됩니다. 가격 책정에 대한 최신 세부 정보는 [Azure Lab Services 가격](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [템플릿 만들기, 관리, 게시](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)