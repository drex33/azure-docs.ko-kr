---
title: Azure 체험 계정으로 Azure Database for PostgreSQL - 유연한 서버를 사용해 보세요.
description: Azure 체험 계정을 사용하여 Azure Database for PostgreSQL - 유연한 서버를 배포하는 방법에 대한 참고 자료입니다.
author: shreyaaithal
ms.author: shaithal
ms.service: postgresql
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5645a872c0dbb5d1b80e6856b173b75d758a23ab
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122780164"
---
# <a name="use-an-azure-free-account-to-try-azure-database-for-postgresql---flexible-server-for-free"></a>Azure 체험 계정으로 Azure Database for PostgreSQL - 유연한 서버를 사용해 보세요.

Azure Database for PostgreSQL - 유연한 서버(미리 보기)는 클라우드에서 고가용성 PostgreSQL 데이터베이스를 실행,·관리,·스케일링하는 데 사용하는 관리되는 서비스입니다. Azure 체험 계정을 사용하면 유연한 서버를 **12개월**  동안 사용할 수 있으며 최대 **월간 한도** 는 다음과 같습니다.
- **750시간** 의 **버스트 가능한 B1MS** 인스턴스(매달 데이터베이스 인스턴스를 지속적으로 실행하는 데 충분한 시간입니다).
- **32GB의 스토리지 및 32GB의 백업 스토리지**.

이 문서에서는 [Azure 체험 계정](https://azure.microsoft.com/free/)을 사용하여 유연한 서버를 만들고 사용하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> Azure Database for PostgreSQL 유연한 서버는 현재 공개 미리 보기로 제공됩니다.


## <a name="prerequisites"></a>전제 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Azure 체험 계정 체험 계정이 없으면 시작하기 전에 [계정을 만듭니다](https://azure.microsoft.com/free/). 


## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버 만들기

1. Azure 체험 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
    
    기본 보기는 서비스 대시보드입니다.

1. PostgreSQL 유연한 서버 데이터베이스를 만들려면 다음과 같이 **Azure Database for PostgreSQL** 를 검색하여 선택합니다.

    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-postgresql.png" alt-text="Azure Database for PostgreSQL를 검색하고 선택하는 방법을 보여 주는 스크린샷":::


    또는 **체험 서비스** 를 검색하여 이동한 다음, 목록에서 **Azure Database for PostgreSQL** 타일을 선택합니다.


    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-all-postgresql.png" alt-text="Azure Portal의 모든 체험 서비스 목록(PostgreSQL이 강조 표시되어 있음)을 보여 주는 스크린샷":::

1. **만들기** 를 선택합니다.

1. **Azure Database for PostgreSQL 배포 옵션 선택** 페이지에서 배포 옵션으로 **유연한 서버(미리 보기)** 를 선택합니다.
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-postgresql-deployment-option.png" alt-text="선택할 수 있는 유연한 서버 배포 옵션을 보여 주는 스크린샷":::

1. 새 **유연한 서버** 의 기본 설정을 입력합니다.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/basic-settings-postgresql.png" alt-text="유연한 서버를 만들기 위한 기본 설정 보여 주는 스크린샷":::

    |설정    |제안 값    |Description |
    |-------|------|------|
    |Subscription   |구독 이름 |평가판 Azure 구독을 선택합니다.|
    |Resource group |리소스 그룹    |새 리소스 그룹 또는 구독의 기존 리소스 그룹을 입력합니다.|
    |서버 이름    |mydemoserver-pgsql |유연한 서버를 식별하는 고유한 이름을 지정합니다. 사용자가 제공한 서버 이름에 postgres.database.azure.com 도메인 이름이 추가됩니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3~63자여야 합니다. |
    |지역 |사용자와 가장 가까운 지역   |목록에서 위치를 선택하되 되도록 사용자에게 가장 가까운 위치를 선택합니다.|
    |워크로드 유형  |개발    |평가판에는 **개발** 워크로드를 선택합니다. 프로덕션 워크로드의 경우 요구 사항에 따라 중소형 또는 대형을 선택할 수 있습니다. |
    |가용성 영역  |기본 설정 없음  |애플리케이션(Azure VM, 가상 머신 확장 집합 또는 AKS 인스턴스에 호스트됨)이 특정 가용성 영역에서 프로비저닝된 경우 같은 가용성 영역에 유연한 서버를 생성합니다. 애플리케이션 및 데이터베이스를 같이 배치하면 모든 영역에서 네트워크 대기 시간을 줄어 성능이 향상됩니다. ‘기본 설정 없음’을 선택하면 기본 AZ가 선택됩니다.|
    |PostgreSQL 버전 |최신 주 버전   |다른 특정 요구 사항이 없는 한 최신 PostgreSQL 주 버전을 사용하세요.|
    |고가용성  |기본값    |고가용성 옵션을 선택하지 않은 상태로 둡니다.|
    |관리자 사용자 이름 |myadmin    |서버에 연결할 때 사용할 로그인 계정을 만듭니다. 관리 사용자 이름은 **pg_** 로 시작할 수 없으며, **azure_superuser, azure_pg_admin, admin, administrator, root, guest,** **public** 을 관리 사용자 이름으로 쓸 수 없습니다. |
    |암호   |사용자 암호  |서버 관리자 계정의 암호를 지정합니다. 암호는 8~128자여야 합니다. 또한 4개 범주(영어 대문자, 영어 소문자, 숫자(0~9), 영숫자가 아닌 문자(!, $, #, % 등)) 중 3개의 문자를 포함해야 합니다.|

1. **컴퓨팅 + 스토리지**  설정을 위해서는 **개발** 워크로드 형식을 선택한 후 기본값이 채워진 상태를 유지합니다. 
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage-default-postgresql.png" alt-text="컴퓨팅 + 스토리지 설정의 기본값을 보여 주는 스크린샷":::

    **서버 구성** 을 **컴퓨팅 + 스토리지** 설정을 검토하고 사용자 지정하도록 선택합니다.
    
    버스트 가능한 B1MS 인스턴스(vCore 1~2개)를 선택하고, 32GB 이하의 스토리지를 포함하도록 지정하고, 나머지 옵션에 대한 기본 설정을 유지합니다.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage-postgresql.png" alt-text="B1MS SKU 및 32GB 스토리지를 선택하기 위한 컴퓨팅 + 스토리지 구성 서버 블레이드를 보여 주는 스크린샷":::

    **저장** 을 선택하여 구성을 완료합니다.

1.  **네트워킹** 탭을 선택하여 서버에 도달하는 방법을 구성합니다.

    Azure Database for PostgreSQL - 유연한 서버를 통해 연결하는 방법은 다음 두 가지입니다.
    - 퍼블릭 액세스(허용된 IP 주소)
    - 프라이빗 액세스(VNet 통합)
    
    퍼블릭 액세스를 사용하는 경우 서버에 대한 액세스는 방화벽 규칙에 추가하는 허용된 IP 주소로 제한됩니다. 특정 IP 주소 또는 범위에 대한 방화벽을 여는 규칙을 만들지 않는 한 이 방법은 외부 애플리케이션과 도구에서 서버 및 서버의 모든 데이터베이스에 연결하지 못하도록 방지합니다.
    
    프라이빗 액세스(VNet 통합)를 사용하는 경우 서버에 대한 액세스는 가상 네트워크로 제한됩니다. 연결 방법에 대한 자세한 내용은 네트워킹 개요를 참조하세요.
    
    이 자습서에서는 서버에 연결할 때 퍼블릭 액세스를 사용하도록 설정합니다.

1. **네트워킹** 탭에서 **연결 방법** 을 **퍼블릭 액세스** 로 선택합니다.

1. **방화벽 규칙** 에 대해서는 **현재 클라이언트 IP 주소 추가** 를 선택합니다.
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/networking-postgresql.png" alt-text="선택할 네트워킹 옵션을 표시하고 현재 클라이언트 IP 주소 추가 단추를 강조 표시하는 스크린샷":::

1. **검토 + 만들기** 를 선택하여 유연한 서버 구성을 검토합니다.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/review-create-postgresql.png" alt-text="검토 + 만들기 블레이드를 보여 주는 스크린샷":::

    >[!IMPORTANT]
    >Azure 체험 계정을 사용하는 경우 서비스 사용량이 월간 한도 이내이면(사용량 정보를 확인하려면 아래의 [**체험 서비스 사용량 모니터링 및 추적**](#monitor-and-track-free-services-usage) 섹션 참조), 서비스 요금이 청구되지 않습니다. 현재 체험 서비스에 대한 **비용 요약** 환경을 개선하기 위해 노력하고 있습니다. 

1. **만들기** 를 선택하여 서버를 프로비전합니다.

    프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

1.  배포 프로세스를 모니터링하려면 도구 모음에서 **알림**(벨 아이콘)을 클릭합니다.

    배포가 완료되면 **대시보드에 고정** 을 선택하여 유연한 서버에 대한 타일을 Azure Portal 대시보드에 만들 수 있습니다. 이 타일은 서버의 **개요** 페이지에 대한 바로 가기입니다. **리소스로 이동** 옵션을 선택하면 서버의 **개요** 페이지가 열립니다.

    기본적으로 **postgres** 데이터베이스가 서버 아래에 만들어집니다. postgres 데이터베이스는 사용자, 유틸리티 및 타사 애플리케이션에서 사용하는 기본 데이터베이스입니다. (다른 기본 데이터베이스는 **azure_maintenance** 입니다. 해당 기능은 사용자 작업으로부터 관리되는 서비스 프로세스를 구분하는 것입니다. 이 데이터베이스에 액세스할 수 없습니다.)


## <a name="connect-and-query"></a>연결 및 쿼리

리소스 그룹에 Azure Database for PostgreSQL 유연한 서버를 만들었으므로, 다음 연결 및 쿼리 빠른 시작을 사용하여 서버 및 쿼리 데이터베이스에 연결할 수 있습니다.
- [psql](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql)
- [Azure CLI](connect-azure-cli.md)
- [Python](connect-python.md)
- [Java](connect-java.md)
- [.NET](connect-csharp.md)
- [VNET의 서버에 연결](quickstart-create-connect-server-vnet.md)


## <a name="monitor-and-track-free-services-usage"></a>체험 서비스 사용량 모니터링 및 추적

체험 서비스 한도를 초과하는 경우 외에는 Azure Database for PostgreSQL 유연한 서버 서비스에 대한 요금이 Azure 체험 계정에 청구되지 않습니다. 한도를 벗어나지 않으려면 Azure Portal로 체험 서비스 사용량을 추적하고 모니터링합니다.

1.  Azure Portal에서 **구독** 을 검색하고 Azure 체험 계정 - **평가판**  구독을 선택합니다.
1.  **개요** 페이지에서 아래로 스크롤하여 **사용량별 상위 서비스** 타일을 표시한 다음, **모든 체험 서비스 보기** 를 선택합니다.

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-usage-overview.png" alt-text="평가판 구독 개요 페이지를 보여 주고 모든 체험 서비스 보기를 강조 표시하는 스크린샷":::

1. **Azure Database for PostgreSQL – 유연한 서버** 에 대한 아래 측정기를 통해 사용량을 추적합니다.

    |미터  |Description    |월간 한도|
    |-------|---------|-------|
    |Azure Database for PostgreSQL, 유연한 서버 버스트 가능한 BS 시리즈 컴퓨팅, B1MS   |컴퓨팅 사용량(실행 시간)을 추적합니다.    |월간 750시간 - 버스트 가능한 B1MS 컴퓨팅 계층|
    |Azure Database for PostgreSQL, 유연한 서버 스토리지, 저장된 데이터    |프로비저닝된 데이터 스토리지를 매월 사용된 GB 단위로 추적합니다.  |매월 32GB|

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-tracking.png" alt-text="모든 체험 서비스에 대한 Azure Portal의 사용량 정보 보기 및 추적 정보 블레이드를 보여 주는 스크린샷":::

    - 미터: 사용 중인 서비스에 대한 측정 단위를 식별합니다.
    - 사용/한도: 현재 월의 사용량 및 측정기의 제한입니다.
    - 상태: 서비스의 사용량 상태입니다. 사용량에 따라, 다음 상태 중 하나일 수 있습니다.
    - 사용 중이 아님: 측정기를 사용하지 않았거나 측정기의 사용량이 청구 시스템에 연결되지 않았습니다.
    - \<Date\>에 초과됨: \<Date\>의 측정기 제한을 초과했습니다.
    - 초과할 가능성이 낮음: 측정기의 제한을 초과할 가능성이 낮습니다.
    - \<Date\>에 초과: \<Date\>에 측정기 제한에 도달할 가능성이 높습니다.

    >[!IMPORTANT]
    >Azure 체험 계정을 사용하면 30일 동안 사용할 수 있는 $200의 크레딧도 얻게 됩니다. 이 기간 동안의 사용량이 월간 체험 서비스 금액을 초과하면 크레딧에서 차감됩니다.
    >처음 30일이 종료되거나 $200 크레딧을 사용한 후에는(먼저 발생하는 경우가 적용됨) 월간 체험 서비스 금액 외에 사용한 만큼만 지불합니다. 30일 후에도 체험 서비스를 계속 이용하려면 종량제 가격 책정으로 이동합니다. 종량제로 전환하지 않으면 $200 크레딧을 초과하는 Azure 서비스를 구매할 수 없으며, 결국에는 계정 및 서비스가 사용 중지됩니다.
    >자세한 내용은 [**Azure 체험 계정 FAQ**](https://azure.microsoft.com/free/free-account-faq/)를 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리
    
개발, 테스트 또는 예측 가능한 시간 제한 프로덕션 워크로드에 유연한 서버를 사용하는 경우에는, 요청이 있을 때 서버를 시작하고 중지하여 사용을 최적화합니다. 서버를 중지한 후에는 더 이른 시간 안에 다시 시작하지 않는 한 7일 동안 해당 상태로 유지됩니다. 자세한 내용은 서버 시작/중지로 TCO 절감을 참조하세요. 유연한 서버가 중지되면 컴퓨팅 사용량이 없지만 스토리지 사용량은 계속 고려됩니다.

또는 이러한 리소스가 나중에 필요하지 않을 경우에는 리소스 그룹을 삭제하거나 PostgreSQL 서버만 삭제하여 리소스를 삭제할 수 있습니다.

- 리소스 그룹을 삭제하려면 다음 단계를 완료합니다.
    1. Azure Portal에서 **리소스 그룹** 을 검색하고 선택합니다.
    1. 리소스 그룹 목록에서 삭제하려는 리소스 그룹의 이름을 선택합니다.
    1. 리소스 그룹의 **개요** 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
    1. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.
    
- PostgreSQL 서버를 삭제하려면 서버의 **개요** 페이지에서 **삭제** 를 선택합니다.
 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[App Service 및 PostgreSQL를 사용하여 Django 앱 배포](tutorial-django-app-service-postgres.md)

