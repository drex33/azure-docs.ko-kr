---
title: 표준 논리 앱 워크플로에 대 한 SQL 저장소 설정
description: 단일 테 넌 트 Azure Logic Apps에 논리 앱 (표준) 워크플로 아티팩트, 상태 및 실행 기록을 저장 하는 SQL 데이터베이스를 설정 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2c88bc4adc49dfb5ddad3ccfd8f20267fc5a420d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103688"
---
# <a name="set-up-sql-database-storage-for-standard-logic-apps-in-single-tenant-azure-logic-apps-preview"></a>단일 테 넌 트 Azure Logic Apps에서 표준 논리 앱에 대 한 SQL 데이터베이스 저장소 설정 (미리 보기)

> [!IMPORTANT]
> 이 기능은 미리 보기로 제공되고 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

**논리 앱 (표준)** 리소스 종류를 선택 하 여 단일 테 넌 트 Azure Logic Apps, App Service Environment v3 또는 Azure 외부에서 실행 되는 워크플로를 만드는 경우 워크플로 관련 아티팩트, 상태 및 런타임 데이터를 저장 하는 Azure Storage 계정도 만들어야 합니다. 그러나 논리 앱 워크플로의 런타임 환경, 처리량, 크기 조정, 성능 및 관리를 더 유연 하 게 제어 하려는 경우에는 워크플로 관련 저장소 트랜잭션에 대해 Azure Storage 대신 SQL Storage 공급자를 사용할 수 있습니다.

이 문서에서는 fsazure Storage 대신 Azure Logic Apps의 기본 저장소 공급자로 SQL 저장소를 사용 하 고 Azure Portal에서 논리 앱을 만드는 동안 또는 Visual Studio Code에서 논리 앱을 배포 하는 동안 저장소 사용에 대 한 SQL를 설정 하는 방법을 간략하게 설명 합니다.

논리 앱을 처음 사용하는 경우 다음 문서를 참조하세요.

- [Azure Logic Apps란?](logic-apps-overview.md)
- [Azure Logic Apps의 단일 테넌트 및 다중 테넌트와 통합 서비스 환경](single-tenant-overview-compare.md)

<a name="why-sql"></a>

## <a name="why-use-sql"></a>SQL를 사용 하는 이유

단일 테 넌 트 Azure Logic Apps 사용할 수 있는 대체 저장소 옵션인 SQL는 다음과 같은 이점을 제공 합니다.

| 이점 | 설명 |
|---------|-------------|
| **식을** | SQL에는 가상 컴퓨터, PaaS (Platform as a Service) 및 컨테이너를 비롯 한 다양 한 폼 요소가 있습니다. 논리 앱 워크플로를 실행할 수 있는 거의 모든 위치에서 SQL 데이터베이스를 실행할 수 있습니다. |
| **제어** | SQL는 특정 기간이 나 특정 작업에 대 한 데이터베이스 처리량, 성능 및 크기 조정에 대해 세부적인 제어를 제공 합니다. SQL 가격은 CPU 사용량과 처리량을 기반으로 하며, 비용은 각 작업을 기반으로 하는 Azure Storage 보다 예측 가능한 가격 책정을 제공 합니다. |
| **기존 자산 사용** | Microsoft 도구에 익숙한 경우 최신 통합에 대 한 자산을 SQL와 함께 사용할 수 있습니다. Azure 하이브리드 혜택을 통해 기존 온-프레미스 배포 및 최신 클라우드 구현에서 자산을 재사용할 수 있습니다. 또한 SQL SQL Server Management Studio (SSMS), 명령줄 인터페이스 및 sdk와 같은 완벽 하 고 잘 지원 되는 도구를 제공 합니다. |
| **호환성** | SQL는 중복성으로 백업, 복원, 장애 조치 (failover) 및 빌드 하는 Azure Storage 보다 많은 옵션을 제공 합니다. 다른 엔터프라이즈 응용 프로그램과 동일한 엔터프라이즈급 메커니즘을 논리 앱의 저장소에 적용할 수 있습니다. |
|||

<a name="when-use-sql"></a>

## <a name="when-to-use-sql"></a>사용 해야 하는 경우 SQL

다음 표에서는 SQL 사용할 수 있는 몇 가지 이유를 설명 합니다.

| 시나리오 | 저장소 공급자 권장 |
|----------|----------------------------|
| Azure에서 논리 앱 워크플로를 실행 하 고 저장소 처리량 및 성능에 대 한 제어를 향상 시킬 수 있습니다. | Azure Storage 저장소 공급자로 SQL 사용 하 여 처리량 및 성능을 미세 조정 하는 도구를 제공 하지 않습니다. |
| [Azure Arc를 Logic Apps 사용 하도록 설정](azure-arc-enabled-logic-apps-overview.md)하 여 온-프레미스에서 논리 앱 워크플로를 실행 하는 것이 좋습니다. | SQL 사용 하 여 저장소 공급자로 SQL 데이터베이스를 호스트할 위치 (예: 가상 컴퓨터의 온-프레미스, 컨테이너 또는 다중 클라우드)를 선택할 수 있습니다. 통합 하려는 시스템에 가까운 논리 앱 워크플로를 실행 하는 것이 좋습니다. 그렇지 않으면 클라우드의 종속성을 줄일 수 있습니다.   |
| 예측 가능한 저장소 비용이 필요 합니다. | 크기 조정 비용을 더 세밀 하 게 제어 하려면 저장소 공급자로 SQL를 사용 합니다. SQL 비용은 각 compute 및 초당 입/출력 작업 (IOPs)을 기반으로 합니다. Azure Storage 비용은 0으로 크기를 조정 하는 작은 작업에 대해 더 효율적으로 작동할 수 있는 작업 수를 기반으로 합니다. |
| Azure Storage를 통해 SQL를 사용 하는 것이 좋습니다. | SQL는 잘 알려져 있고 신뢰할 수 있는 에코 시스템으로 논리 앱에서 동일한 거 버 넌 스 및 관리 작업을 적용 하는 데 사용할 수 있습니다. |
| 기존 SQL 환경을 다시 사용 하려고 합니다. | 다시 사용 하거나 클라우드로 현대화 하는 SQL 라이선스를 이미 소유 하 고 있는 경우 저장소 공급자로 SQL를 사용 합니다. 논리 앱 통합에 대 한 Azure 하이브리드 혜택을 사용 하고자 할 수도 있습니다. |
| 기타 등등 | Azure Storage를 기본 저장소 공급자로 사용 합니다. |
|||

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정 및 활성 구독. 아직 없는 경우 [무료 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

- 논리 앱과 함께 사용할 SQL 환경입니다. 그러나 환경을 설정 하기 전에 다음 단계를 완료 합니다.

  1. SQL server 인스턴스를 만듭니다.

     지원 되는 형식으로는 [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads), [azure SQL database](https://azure.microsoft.com/products/azure-sql/database/), [azure SQL Managed Instance](https://azure.microsoft.com/products/azure-sql/managed-instance/)등이 있습니다.

     - SQL 서버가 지원 되는 유형 중 하나를 사용 하 여 Azure에서 호스트 되는 경우 다음 사용 권한을 설정 해야 합니다.

       1. [Azure Portal](https://portal.azure.com)에서 SQL 서버 리소스로 이동 합니다.

       1. 리소스 탐색 메뉴의 **보안** 에서 **방화벽 및 가상 네트워크** 를 선택 합니다.

       1. 열리는 창의 **Azure 서비스 및 리소스에서이 서버에 액세스할 수 있도록 허용** 에서 **예** 를 선택 합니다.

       1. 변경 내용을 저장합니다.

     - SQL 서버가 azure에서 호스팅되지 않는 경우 서버에서 방화벽 또는 네트워크 설정에 따라 azure 서비스 및 리소스에서 서버 및 데이터베이스에 액세스할 수 있는지 확인 합니다.

     - 로컬 개발에 SQL Express를 사용 하는 경우 기본 명명 된 인스턴스에 연결  `localhost\SQLExpress` 합니다.

  1. 기존 데이터베이스를 만들거나 사용 합니다.

     SQL Storage 공급자를 설정 하려면 먼저 사용할 수 있는 데이터베이스가 있어야 합니다.

  1. 이제이 문서에서 [SQL 환경을 설정 하는 단계](#set-up-sql-environment) 를 수행할 수 있습니다.

- 로컬 개발의 경우 로컬 컴퓨터에 [Visual Studio Code](https://code.visualstudio.com/Download) 설치 되어 있습니다.

  > [!NOTE]
  > [Azure Functions Core Tools에 대 한 최신 버전](https://github.com/Azure/azure-functions-core-tools/releases) 을 설치 하 여 Microsoft Installer (MSI) 버전을 사용 하 여 SQL 지원을 보장 해야 `func-cli-X.X.XXXX-x*.msi` 합니다. Visual Studio Code 설치 요구 사항에 대 한 자세한 내용은 [Visual Studio Code에서 단일 테 넌 트 Azure Logic Apps (표준)를 사용 하 여 통합 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md)를 검토 하세요.

<a name="set-up-sql-environment"></a>

## <a name="set-up-your-sql-environment"></a>SQL 환경 설정

1. SQL Storage 공급자를 설정 하기 전에 필수 [구성 요소](#prerequisites)에서 필요한 단계를 완료 합니다.

1. SQL 서버에 대 한 사용 권한을 설정 합니다.

   현재 SQL Storage 공급자는 연결 문자열에서 SQL 인증을 지원 합니다. 로컬 개발 및 테스트에 Windows 인증을 사용할 수도 있습니다. 현재는 Azure Active Directory (Azure AD) 및 관리 되는 id에 대 한 지원을 사용할 수 없습니다.

   대상 SQL 데이터베이스에서 워크플로 관련 아티팩트를 만들고 관리할 수 있는 권한이 있는 id를 사용 해야 합니다. 예를 들어 관리자는 이러한 아티팩트를 만들고 관리 하는 데 필요한 모든 권한을 갖습니다. 다음 목록에서는 단일 테 넌 트 Azure Logic Apps 런타임에서 사용자가 제공 하는 SQL 연결 문자열을 사용 하 여 만들려고 하는 아티팩트에 대해 설명 합니다. SQL 연결 문자열에 사용 된 id에 다음과 같은 아티팩트를 만드는 데 필요한 권한이 있는지 확인 합니다.

   - , 및 스키마를 만들고 삭제  `dt` 합니다.  `dc`  `dq`
   - 이러한 스키마에서 테이블을 추가, 변경 및 삭제 합니다.
   - 이러한 스키마의 사용자 정의 테이블 형식을 추가, 변경 및 삭제 합니다.

   대상 사용 권한에 대 한 자세한 내용은 [데이터베이스 엔진에서 SQL 서버 사용 권한](/sql/relational-databases/security/permissions-database-engine)을 검토 하십시오.

1. SQL 커넥트입니다.

   - SQL 데이터베이스에서 개발에 필요한 액세스를 허용 하는지 확인 합니다.

   - Azure SQL database를 사용 하는 경우 다음 요구 사항을 완료 합니다.

     - 로컬 개발 및 테스트를 위해 로컬 컴퓨터의 IP 주소에서 연결을 명시적으로 허용 합니다.  [Azure SQL Server에서 IP 방화벽 규칙을 설정할](../azure-sql/database/network-access-controls-overview.md#ip-firewall-rules)수 있습니다.

     - [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스가 제공 된 연결 문자열을 사용 하 여 [Azure 서비스를 허용](../azure-sql/database/network-access-controls-overview.md#allow-azure-services)하는 SQL 데이터베이스에 액세스 하도록 허용 합니다.

     - 시나리오에 필요한 다른 [SQL 데이터베이스 네트워크 액세스 제어](../azure-sql/database/network-access-controls-overview.md)를 설정   합니다.

   - azure SQL Managed Instance를 사용 하는 경우 보안 된 공용 끝점을 통해 azure 서비스 ( `logicapp` )에서 [SQL 데이터베이스에 연결](../azure-sql/managed-instance/public-endpoint-overview.md)하도록 허용 합니다.

<a name="set-up-sql-logic-app-creation-azure-portal"></a>

## <a name="set-up-sql-during-creation-in-the-azure-portal"></a>를 만드는 동안 SQL 설정 Azure Portal

Azure에서 **논리 앱 (표준)** 리소스 유형을 사용 하 여 논리 앱을 만들 때 저장소 공급자로 SQL를 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정을 사용하여 로그인합니다.

1. Azure Portal 검색 상자에 `logic apps`를 입력하고, **논리 앱** 을 선택합니다.

   !["논리 앱" 검색 용어와 "논리 앱" 범주가 선택 된 Azure Portal 검색 상자를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/find-logic-app-resource-template.png)

1. **Logic Apps** 페이지에서 **추가** 를 선택합니다.

1. **논리 앱 만들기** 페이지의 **기본** 탭에서 논리 앱 리소스에 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 논리 앱에 사용할 Azure 구독입니다. |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 논리 앱 및 관련 리소스를 만드는 Azure 리소스 그룹입니다. 이 리소스 이름은 모든 Azure 지역에서 고유해야 하고 문자, 숫자, 하이픈( **-** ), 밑줄( **_** ), 괄호( **()** ) 및 마침표( **.** )만 포함할 수 있습니다. <p><p>이 예제에서는 `Fabrikam-Workflows-RG`라는 리소스 그룹을 만듭니다. |
   | **형식** | 예 | **Standard** | 이 논리 앱 리소스 종류는 단일 테 넌 트 Azure Logic Apps 환경에서 실행 되며 [표준 사용량, 청구 및 가격 책정 모델](logic-apps-pricing.md#standard-pricing)을 사용 합니다. |
   | **논리 앱 이름** | 예 | <*logic-app-name*> | 논리 앱에 사용할 이름입니다. 이 리소스 이름은 모든 Azure 지역에서 고유해야 하고 문자, 숫자, 하이픈( **-** ), 밑줄( **_** ), 괄호( **()** ) 및 마침표( **.** )만 포함할 수 있습니다. <p><p>이 예제에서는 `Fabrikam-Workflows`라는 논리 앱을 만듭니다. <p><p>**참고**: 논리 앱 `.azurewebsites.net` **(표준)** 리소스는 Azure Functions 확장성 모델을 사용 하 고 Azure Functions 런타임에서 확장으로 호스트 되는 단일 테 넌 트 Azure Logic Apps 런타임으로 구동 되기 때문에 논리 앱의 이름은 자동으로 접미사를 가져옵니다. Azure Functions는 동일한 앱 명명 규칙을 사용 합니다. |
   | **게시** | 예 | <*deployment-environment*> | 논리 앱의 배포 대상입니다. 기본적으로 **워크플로** 는 단일 테넌트 Azure Logic Apps에 배포하도록 선택됩니다. Azure에서 첫 번째 워크플로를 추가해야 하는 빈 논리 앱 리소스를 만듭니다. <p><p>**참고**: 현재 **Docker 컨테이너** 옵션을 사용하려면 Azure Arc 지원 Kubernetes 클러스터에서 [*사용자 지정 위치*](../azure-arc/kubernetes/conceptual-custom-locations.md)가 필요하며 [Azure Arc 지원 Logic Apps(미리 보기)](azure-arc-enabled-logic-apps-overview.md)와 함께 사용할 수 있습니다. 논리 앱, 사용자 지정 위치 및 클러스터의 리소스 위치는 모두 동일해야 합니다. |
   | **지역** | 예 | <*Azure-region*> | 리소스 그룹 및 리소스를 만들 때 사용할 위치입니다. 이 예제에서는 샘플 논리 앱을 Azure에 배포하고 **미국 서부** 를 사용합니다. <p>- **Docker 컨테이너** 를 선택한 경우 사용자 지정 위치를 선택 합니다. <p>-먼저 존재 해야 하는 [ASEv3](../app-service/environment/overview.md) 리소스에 배포 하려면 **지역** 목록에서 해당 환경 리소스를 선택 합니다. |
   |||||

   다음 예에서는 **기본** 탭을 사용 하 여 **논리 앱 만들기** 페이지를 보여 줍니다.

   !["기본 사항" 탭을 사용 하 여 Azure Portal 및 "논리 앱 만들기" 페이지를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-logic-app-resource-portal.png)

1. 준비가 되 면 **다음: 호스팅** 을 선택 합니다. **호스팅** 탭에서 논리 앱에 사용할 수 있도록 스토리지 솔루션 및 호스팅 계획에 관한 다음 정보를 제공합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **스토리지 유형** | Yes | **SQL 및 Azure Storage** | 워크플로 관련 아티팩트 및 데이터에 사용 하려는 저장소 유형입니다. <p><p>-사용자 지정 위치를 사용자의 지역으로 이전에 선택한 경우 **SQL** 를 선택 합니다. <p><p>-이전에 Azure 지역 또는 ASEv3 위치를 선택한 경우 **SQL 및 Azure Storage** 를 선택 합니다. <p><p>**참고**: Azure 지역에 배포 하는 경우 Azure Logic Apps 플랫폼에서 논리 앱의 구성에 대 한 일회성 호스트를 완료 하는 데 사용 되는 Azure Storage 계정이 계속 필요 합니다. 워크플로의 정의, 상태, 실행 기록 및 기타 런타임 아티팩트가 SQL 데이터베이스에 저장 됩니다. <p><p>Azure Arc 클러스터에서 호스트 되는 사용자 지정 위치에 대 한 배포의 경우 저장소 공급자로 SQL 필요 합니다. |
   | **스토리지 계정** | 예 | <*Azure-storage-account-name*> | 스토리지 트랜잭션에 사용할 [Azure Storage 계정](../storage/common/storage-account-overview.md)입니다. <p><p>이 리소스 이름은 모든 지역에서 고유해야 하며 숫자와 소문자만 포함된 3-24자여야 합니다. 기존 계정을 선택하거나 새 계정을 만듭니다. <p><p>이 예제에서는 `fabrikamstorageacct`라는 스토리지 계정을 만듭니다. |
   | **연결 문자열 SQL** | Yes | <*sql-연결 문자열*> | 현재 SQL 연결 문자열은 OAuth 또는 관리 되는 id 인증을 지원 하지 않고 SQL 인증만 지원 합니다. <p><p>**참고**: Azure Portal는이 문자열의 유효성을 검사 하지 않으므로 올바른 연결 문자열을 입력 해야 합니다. |
   | **플랜 유형** | 예 | <호스팅 계획> | 논리 앱을 배포하는 데 사용할 호스팅 계획입니다. <p><p>자세한 내용은 [호스팅 계획과 가격 책정 계층](logic-apps-pricing.md#standard-pricing)을 참조하세요. |
   | **Windows 플랜** | 예 | <*plan-name*> | 사용할 계획 이름입니다. 기존 계획 이름을 선택하거나 새 계획의 이름을 입력합니다. <p><p>이 예제에서는 `Fabrikam-Service-Plan`이름을 사용합니다. |
   | **SKU 및 크기** | 예 | <*pricing-tier*> | 논리 앱에 사용할 [가격 책정 계층](../app-service/overview-hosting-plans.md)입니다. 선택한 내용에 따라 논리 앱과 워크플로에서 사용하는 가격 책정, 컴퓨팅, 메모리 및 스토리지에 영향이 있습니다. <p><p>기본 가격 책정 계층을 변경하려면 **크기 변경** 을 선택합니다. 그런 다음, 필요한 워크로드에 따라 다른 가격 책정 계층을 선택할 수 있습니다. <p><p>자세한 내용은 [호스팅 계획과 가격 책정 계층](logic-apps-pricing.md#standard-pricing)을 참조하세요. |
   |||||

   다음 예제에서는 **호스팅** 탭을 사용 하 여 **논리 앱 만들기** 페이지를 보여 줍니다.

   !["호스팅" 탭을 사용 하 여 Azure Portal 및 "논리 앱 만들기" 페이지를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/set-up-sql-storage-details.png)

1. [Azure Portal에서 단일 테 넌 트 Azure Logic Apps (Standard)를 사용 하 여 통합 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)에서 남은 만들기 단계를 완료 합니다.

완료 되 면 새 논리 앱 리소스 및 워크플로가 Azure에 라이브 되 고 SQL 데이터베이스를 저장소 공급자로 사용 합니다.

<a name="set-up-sql-local-dev"></a>

## <a name="set-up-sql-for-local-development-in-visual-studio-code"></a>Visual Studio Code에서 로컬 개발을 위한 SQL 설정

다음 단계에서는 Visual Studio Code에서 로컬 개발 및 테스트를 위한 저장소 공급자로 SQL를 설정 하는 방법을 보여 줍니다.

1. 단일 테 넌 트 Azure Logic Apps를 사용 하도록 개발 환경을 설정 합니다.

   1. Azure Logic Apps (표준) 확장을 사용 하 여 Visual Studio Code 작업을 수행 하기 위한 [필수 구성 요소](create-single-tenant-workflows-visual-studio-code.md#prerequisites) 를 충족 합니다.

   1. [Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#set-up) Azure Logic Apps (표준) 확장을 사용 하도록 설정 합니다.

   1. Visual Studio Code에서 [Azure 계정에 연결](create-single-tenant-workflows-visual-studio-code.md#connect-azure-account) 하 고 [빈 논리 앱 프로젝트를 만듭니다](create-single-tenant-workflows-visual-studio-code.md#create-project).

1. Visual Studio Code에서 탐색기 창을 엽니다 (아직 열려 있지 않은 경우).

1. 탐색기 창의 논리 앱 프로젝트 루트에서 모든 프로젝트의 파일 및 폴더 아래에 있는 빈 영역 위로 마우스 포인터를 이동 하 고 바로 가기 메뉴를 연 다음 **논리 앱 프로젝트에 SQL 저장소 사용** 을 선택 합니다.

   ![빈 영역에서 프로젝트 루트, 열린 바로 가기 메뉴 및 "논리 앱 프로젝트에 대 한 SQL 저장소 사용"이 선택 되어 있는 Visual Studio Code, 탐색기 창 및 마우스 포인터를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/use-sql-storage-logic-app-project.png)

1. 메시지가 표시 되 면 SQL 연결 문자열을 입력 합니다. 로컬 SQL Express 인스턴스 또는 보유 하 고 있는 다른 SQL 데이터베이스를 사용 하도록 선택할 수 있습니다.

   ![Visual Studio Code 및 SQL 연결 문자열 프롬프트를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

   확인 후 Visual Studio Code 프로젝트의 **로컬 설정 json** 파일에 다음 설정이 만들어집니다. 언제 든 지이 설정을 업데이트할 수 있습니다.  

   ![Visual Studio Code, 논리 앱 프로젝트를 보여 주고 SQL 연결 문자열 설정을 사용 하 여 "로컬. s s a. i n s" 파일을 여는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/local-settings-json-file.png)

<a name="set-up-sql-logic-app-deployment-visual-studio-code"></a>

## <a name="set-up-sql-during-deployment-from-visual-studio-code"></a>배포 하는 동안 SQL 설정 Visual Studio Code

Visual Studio Code에서 Azure로 논리 앱 프로젝트를 직접 게시할 수 있습니다. 이 작업은 **논리 앱 (표준)** 리소스 유형을 사용 하 여 논리 앱 프로젝트를 배포   합니다.

- Azure에서 프로젝트를 새 **논리 앱 (표준)** 리소스로 게시 하 고 SQL를 저장소 공급자로 사용 하려면 앱을 게시할 때 SQL 연결 문자열을 입력 합니다. 전체 단계는 [새 논리 앱 배포에 대 한 SQL 설정](#deploy-new-logic-app-visual-studio-code)을 참조 하세요.

- 이미 SQL 설정을 설정한 경우 Azure에서 이미 배포 된 **논리 앱 (표준)** 리소스에 논리 앱 프로젝트를 게시할 수 있습니다. 이 작업은 기존 논리 앱을 덮어씁니다.

  > [!NOTE]
  > 로컬 SQL Express는 Azure에서 배포 되 고 호스트 되는 논리 앱에서 작동 하지 않습니다.

<a name="deploy-new-logic-app-visual-studio-code"></a>

### <a name="set-up-sql-for-new-logic-app-standard-resource-deployment"></a>새 논리 앱 (표준) 리소스 배포에 대 한 SQL 설정

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 선택합니다.

1.  **Azure: Logic Apps (표준)**   창 도구 모음에서 **논리 앱에 배포** 를 선택 합니다.

   !["Azure: Logic Apps (표준)" 창이 표시 되 고 "논리 앱에 배포" 아이콘이 선택 된 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/deploy-to-logic-app.png)

1. 프롬프트가 표시되면 논리 앱 배포에 사용할 Azure 구독을 선택합니다.

1. Visual Studio Code 열리는 목록에서 **Azure advanced에서 새 논리 앱 만들기 (표준)** 에 대 한 고급 옵션을 선택 해야 합니다. 그렇지 않으면 SQL를 설정 하 라는 메시지가 표시 되지 않습니다.

   !["Azure 고급에서 새 논리 앱 만들기 (표준)"를 선택 하는 배포 옵션을 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-create-logic-app-advanced.png)

1. 메시지가 표시 되 면 **논리 앱 (표준)** 리소스에 사용할 이름인 새 논리 앱에 대 한 전역적으로 고유한 이름을 제공   합니다. 이 예에서는  `Fabrikam-Workflows-App` 를 사용 합니다.

   ![논리 앱에 사용할 전역적으로 고유한 이름을 확인 하는 메시지를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-logic-app-name.png)

1. 논리 앱의 위치를 선택 합니다. 입력을 시작 하 여 목록을 필터링 할 수도 있습니다.

   - Azure에 배포 하려면 배포 하려는 Azure 지역을 선택 합니다. 이전에 App Service Environment v3 (ASEv3) 리소스를 만들고 해당 리소스를 배포 하려는 경우 ASEv3를 선택 합니다.

   - Azure Arc 사용 Logic Apps에 배포 하려면 이전에 구성 된 사용자 지정 위치를 선택 합니다.

   다음 예제에서는 **미국 서 부** 로 필터링 된 위치 목록을 보여줍니다.

   ![사용 가능한 Azure 지역 및 Azure Arc 배포에 대 한 사용자 지정 위치를 사용 하 여 배포 위치를 선택 하 라는 메시지를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-location.png)

1. 새 논리 앱에 대 한 호스팅 계획 유형을 선택 합니다.

   1. 앱의 위치로 ASEv3을 선택한 경우 **App Service 계획** 을 선택 하 고 ASEv3 리소스를 선택 합니다. 그렇지 않은 경우 **워크플로 표준** 을 선택 합니다.

      ![' 워크플로 표준 ' 또는 ' App Service 요금제 '를 선택 하 라는 메시지를 표시 하는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/select-hosting-plan.png)

   1. 계획 이름을 만들거나 기존 계획을 선택합니다.

      이 예에서는 기존 계획을 사용할 수 없으므로 **새 App Service 계획 만들기** 를 선택 합니다.

      !["새 App Service 계획 만들기"를 선택 하 여 호스팅 계획의 이름을 만들라는 메시지를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-app-service-plan.png)

1. 호스팅 계획 이름을 입력한 다음, 선택한 계획의 가격 책정 계층을 선택합니다.

   자세한 내용은 [호스팅 계획과 가격 책정 계층](logic-apps-pricing.md#standard-pricing)을 참조하세요.

1. Azure 리소스 그룹을 입력 하 라는 메시지가 표시 되 면 최적의 성능을 위해 배포에 대 한 프로젝트와 동일한 Azure 리소스 그룹을 선택 합니다.

   > [!NOTE]
   > 다른 리소스 그룹을 만들거나 사용할 수 있지만 그러면 성능에 영향을 줄 수 있습니다. 다른 리소스 그룹을 만들거나 선택하고 확인 프롬프트가 표시된 후 취소하면 배포도 취소됩니다.

1. 논리 앱에 대 한 저장소 계정을 선택 하 라는 메시지가 표시 되 면 다음 옵션 중 하나를 선택 합니다.

   - 이전에 사용자 지정 위치를 선택한 경우 **SQL** 옵션을 선택 합니다.

   - Azure에 배포 하려면 **SQL 및 Azure Storage** 옵션을 선택 합니다.

     > [!NOTE]
     > 이 옵션은 Azure 배포에만 필요 합니다. Azure에서 Azure Logic Apps 플랫폼에서 논리 앱의 구성에 대 한 일회성 호스팅을 완료 하려면 Azure Storage 필요 합니다. 진행 중인 워크플로 상태, 실행 기록 및 기타 런타임 아티팩트가 SQL 데이터베이스에 저장 됩니다.
     >
     > Azure Arc 클러스터에서 호스트 되는 사용자 지정 위치에 대 한 배포의 경우 저장소 공급자로 SQL 필요 합니다.  

1. 메시지가 표시 되 면 **새 저장소 계정 만들기** 또는 기존 저장소 계정 (사용 가능한 경우)을 선택 합니다.

   !["Azure: Logic Apps(표준)" 창과 스토리지 계정을 만들거나 선택하라는 프롬프트를 보여주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/create-storage.png)

1. SQL 저장소 확인 프롬프트에서 **예** 를 선택 합니다. 연결 문자열 프롬프트에서 SQL 연결 문자열을 입력 합니다.

   > [!NOTE]
   > Visual Studio Code에서이 문자열의 유효성을 검사 하지 않으므로 올바른 연결 문자열을 입력 해야 합니다.

   ![Visual Studio Code 및 SQL 연결 문자열 프롬프트를 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/enter-sql-connection-string.png)

1. [새 논리 앱에 게시 (표준) 리소스](create-single-tenant-workflows-visual-studio-code.md#publish-new-logic-app)에서 남은 배포 단계를 완료 합니다.

완료 되 면 새 논리 앱 리소스 및 워크플로가 Azure에 라이브 되 고 SQL 데이터베이스를 저장소 공급자로 사용 합니다.

## <a name="validate-deployments"></a>배포 유효성 검사

**논리 앱 (표준)** 리소스를 Azure에 배포한 후 설정이 올바른지 여부를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스를 엽니다.

1. 리소스 탐색 메뉴의 **설정** 아래에서 **구성** 을 선택 합니다.

1. **구성** 창의 **응용 프로그램 설정** 에서, **node.js 앱 설정** 을 찾고 SQL 연결 문자열이 표시 되 고 올바른지 확인 합니다.

1. SQL 환경에서 ' dt ' 및 ' dq '로 시작 하는 스키마 이름을 사용 하 여 SQL 테이블이 생성 되었는지 확인 합니다.

예를 들어 다음 스크린샷은 단일 워크플로를 사용 하 여 논리 앱 리소스에 대해 만들어진 단일 테 넌 트 Azure Logic Apps 런타임 테이블을 보여 줍니다.

![단일 테 넌 트 Azure Logic Apps 런타임으로 만든 SQL 테이블을 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-tables-sql.png)

또한 단일 테 넌 트 Azure Logic Apps 서비스는 사용자 정의 테이블 형식을 만듭니다. 예를 들어 다음 스크린샷은 단일 워크플로를 사용 하 여 논리 앱 리소스에 대해 생성 된 단일 테 넌 트 Azure Logic Apps 런타임에 대 한 사용자 정의 테이블 형식을 보여 줍니다.

![단일 테 넌 트 Azure Logic Apps 런타임으로 만든 SQL 사용자 정의 테이블 형식을 보여 주는 스크린샷](./media/set-up-sql-db-storage-single-tenant-standard-workflows/runtime-created-user-defined-tables-sql.png)

## <a name="next-steps"></a>다음 단계

- [Visual Studio Code에서 단일 테 넌 트 Azure Logic Apps (Standard)를 사용 하 여 통합 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md)
- [단일 테넌트 Azure Logic Apps에서 논리 앱에 대한 호스트 및 앱 설정 편집](edit-app-settings-host-settings.md)
