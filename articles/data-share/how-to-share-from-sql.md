---
title: Azure SQL Database 및 Azure Synapse Analytics에서 데이터 공유 및 수신
description: Azure SQL Database 및 Azure Synapse Analytics에서 데이터 공유 및 수신 방법 알아보기
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 09/10/2021
ms.openlocfilehash: 83da2d9c1c242e49aac28067d1c315e1e382f2ef
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063649"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database 및 Azure Synapse Analytics에서 데이터 공유 및 수신

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure Data Share는 스냅샷 기반 공유 Azure SQL Database 및 Azure Synapse Analytics를 지원합니다. 이 문서에서는 이 원본에서 데이터를 공유하고 수신하는 방법을 설명합니다.

Azure Data Share에서는 Azure SQL Database 및 Azure Synapse Analytics(이전에는 Azure SQL DW라고 함)에서 테이블과 뷰를 공유하고 Azure Synapse Analytics(작업 영역) 전용 SQL 풀에서 테이블을 공유하도록 지원합니다. Azure Synapse Analytics(작업 영역) 서버리스 SQL 풀에서 공유하는 기능은 현재 지원되지 않습니다. 데이터 소비자는 데이터를 csv 또는 parquet 파일로 Azure Data Lake Storage Gen2 또는 Azure Blob Storage에 가져오거나, 테이블로 Azure SQL Database 및 Azure Synapse Analytics에 가져오도록 허용할 수 있습니다.

Azure Data Lake Store Gen2 또는 Azure Blob Storage에 데이터를 가져오도록 허용하는 경우 전체 스냅샷이 대상 파일의 콘텐츠(이미 있는 경우)를 덮어씁니다.
데이터가 SQL 테이블로 수신되고 대상 테이블이 아직 없는 경우 Azure Data Share는 원본 스키마를 사용하여 SQL 테이블을 만듭니다. 이름이 같은 대상 테이블이 이미 있으면 삭제되고 최신 전체 스냅샷으로 덮어씁니다. 증분 스냅샷은 현재 지원되지 않습니다.

## <a name="share-data"></a>데이터 공유

### <a name="prerequisites-to-share-data"></a>데이터 공유를 위한 필수 구성 요소

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 수신자 Azure 로그인 이메일 주소(이메일 별칭을 사용하면 작동하지 않음).
* 원본 Azure 데이터 저장소가 데이터 공유 리소스를 만드는 데 사용하는 것과 다른 Azure 구독에 있는 경우 Azure 데이터 저장소가 있는 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다. 

### <a name="prerequisites-for-sql-source"></a>SQL 원본의 필수 구성 요소
다음은 SQL 원본에서 데이터를 공유하기 위한 필수 구성 요소 목록입니다. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)에서 공유하기 위한 필수 구성 요소


Azure Active Directory 인증을 사용하여 데이터를 공유하려면 필수 구성 요소 목록은 다음과 같습니다.

* 공유하려는 테이블 및 보기를 포함하는 Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)
* SQL 서버에 데이터베이스를 쓸 수 있는 권한으로, *Microsoft.Sql/servers/databases/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다.
* SQL Server **Azure Active Directory 관리자**
* SQL Server Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 SQL 서버로 이동합니다. 왼쪽 탐색에서 *방화벽 및 가상 네트워크* 를 선택합니다.
    1. *Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용* 에 대해 **예** 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 

SQL 인증을 사용하여 데이터를 공유하려면 다음은 필수 구성 요소 목록입니다. [단계별 데모](https://youtu.be/hIE-TjJD8Dc)에 따라 필수 구성 요소를 구성할 수 있습니다.

* 공유하려는 테이블 및 보기를 포함하는 Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)
* SQL 서버에 데이터베이스를 쓸 수 있는 권한으로, *Microsoft.Sql/servers/databases/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다.
* Data Share 리소스의 관리 ID가 데이터베이스에 액세스할 수 있는 권한입니다. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 SQL 서버로 이동하고 자신을 **Azure Active Directory 관리자** 로 설정합니다.
    1. [쿼리 편집기](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) 또는 Azure Active Directory 인증을 사용하는 SQL Server Management Studio를 사용하여 Azure SQL Database/Data Warehouse에 연결합니다. 
    1. 다음 스크립트를 실행하여 Data Share 리소스 관리 ID를 db_datareader로 추가합니다. SQL Server 인증이 아닌 Active Directory를 사용하여 연결해야 합니다. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.  

* 공유하려는 테이블 및/또는 보기를 탐색하고 선택할 수 있는 **'db_datareader'** 가 포함된 Azure SQL Database 사용자입니다. 

* SQL Server Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 SQL 서버로 이동합니다. 왼쪽 탐색에서 *방화벽 및 가상 네트워크* 를 선택합니다.
    1. *Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용* 에 대해 **예** 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Azure Synapse Analytics(작업 영역) SQL 풀에서 공유하기 위한 필수 구성 요소

* 공유하려는 테이블이 있는 Azure Synapse Analytics(작업 영역) 전용 SQL 풀입니다. 현재 보기 공유는 지원되지 않습니다. 서버리스 SQL 풀에서 공유는 현재 지원되지 않습니다.
* *Microsoft.Synapse/workspaces/sqlPools/write* 에 있는 Synapse 작업 영역의 SQL 풀에 쓸 수 있는 권한입니다. 이 권한은 **기여자** 역할에 있습니다.
* Data Share 리소스의 관리 ID가 Synapse 작업 영역 SQL 풀에 액세스할 수 있는 권한입니다. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다. 왼쪽 탐색에서 SQL Active Directory 관리자를 선택하고 자신을 **Azure Active Directory 관리자** 로 설정합니다.
    1. Synapse Studio를 열고 왼쪽 탐색에서 *관리* 를 선택합니다. 보안에서 *액세스 제어* 를 선택합니다. 자신에게 **SQL 관리자** 또는 **작업 영역 관리자** 역할을 할당합니다.
    1. Synapse Studio의 왼쪽 탐색에서 *개발* 을 선택합니다. SQL 풀에서 다음 스크립트를 실행하여 Data Share 리소스 관리 ID를 db_datareader로 추가합니다. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.  

* Synapse 작업 영역 Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다. 왼쪽 탐색에서 *방화벽* 을 선택합니다.
    1. **Azure 서비스 및 리소스가 이 작업 영역에 액세스할 수 있도록 허용** 하려면 *켜기* 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="create-a-data-share-account"></a>Data Share 계정 만들기

Azure 리소스 그룹에서 Azure Data Share 리소스를 만듭니다.

1. 포털의 왼쪽 상단 모서리에 있는 메뉴 단추를 선택한 다음, **리소스 만들기**(+)를 선택합니다.

1. *Data Share* 를 검색합니다.

1. Data Share를 선택하고 **만들기** 를 선택합니다.

1. 다음 정보를 사용하여 Azure Data Share 리소스의 기본 세부 정보를 채웁니다. 

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 데이터 공유 계정에 사용할 Azure 구독을 선택합니다.|
    | 리소스 그룹 | *test-resource-group* | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
    | 위치 | *미국 동부 2* | 데이터 공유 계정에 대한 지역을 선택합니다.
    | 이름 | *datashareaccount* | 데이터 공유 계정의 이름을 지정합니다. |
    | | |

1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 데이터 공유 계정을 프로비저닝합니다. 새 데이터 공유 계정을 프로비저닝하는 데 일반적으로 2분 정도 걸립니다. 

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

### <a name="create-a-share"></a>공유 만들기

1. Data Share 개요 페이지로 이동합니다.

    ![데이터 공유](./media/share-receive-data.png "데이터 공유") 

1. **Start sharing your data**(데이터 공유 시작)를 선택합니다.

1. **만들기** 를 선택합니다.   

1. 공유에 대한 세부 정보를 입력합니다. 이름, 공유 유형, 공유 콘텐츠의 설명 및 사용 약관(선택 사항)을 지정합니다. 

    ![EnterShareDetails](./media/enter-share-details.png "공유 세부 정보 입력") 

1. **계속** 을 선택합니다.

1. 공유에 데이터 세트를 추가하려면 **데이터 세트 추가** 를 선택합니다. 

    ![공유에 데이터 세트 추가](./media/datasets.png "데이터 세트")

1. 추가하려는 데이터 세트 형식을 선택합니다. 이전 단계에서 선택한 공유 유형(스냅샷 또는 내부)에 따라 다른 데이터 세트 유형 목록이 표시됩니다. 

    ![AddDatasets](./media/add-datasets.png "데이터 세트 추가")    

1. SQL Server 또는 Synapse 작업 영역을 선택합니다. AAD 인증을 사용 중이고 **Data Share를 사용하여 사용자 대신 위의 ‘사용자 만들기’ SQL 스크립트를 실행 가능** 확인란이 나타나면 확인란을 선택합니다. SQL 인증을 사용하는 경우 자격 증명을 제공하고 전제 조건의 단계에 따라 화면에 표시되는 스크립트를 실행합니다. 그러면 SQL DB에서 읽을 수 있는 권한이 Data Share 리소스에 부여됩니다. 

   **다음** 을 선택하여 공유하려는 개체로 이동하여 ‘데이터 세트 추가’를 선택합니다. Azure SQL Database 및 Azure Synapse Analytics(이전에는 Azure SQL DW라고 함)에서 테이블과 뷰를 선택하거나 Azure Synapse Analytics(작업 영역) 전용 SQL 풀에서 테이블을 선택할 수 있습니다. 

    ![SelectDatasets](./media/select-datasets-sql.png "데이터 세트 선택")    

1. 받는 사람 탭에서 '+ 받는 사람 추가'를 선택하여 데이터 소비자의 이메일 주소를 입력합니다. 메일 주소는 수신자의 Azure 로그인 메일이어야 합니다.

    ![AddRecipients](./media/add-recipient.png "수신자 추가") 

1. **계속** 을 선택합니다.

1. 스냅샷 공유 유형을 선택한 경우 데이터 소비자에게 데이터 업데이트를 제공하도록 스냅샷 일정을 구성할 수 있습니다. 

    ![EnableSnapshots](./media/enable-snapshots.png "스냅샷 사용") 

1. 시작 시간과 되풀이 간격을 선택합니다. 

1. **계속** 을 선택합니다.

1. 검토 + 만들기 탭에서 패키지 콘텐츠, 설정, 받는 사람 및 동기화 설정을 검토합니다. **만들기** 를 선택합니다.

이제 Azure Data Share가 생성되었고 Data Share의 받는 사람이 초대를 수락할 준비가 되었습니다. 

## <a name="receive-data"></a>데이터 수신

### <a name="prerequisites-to-receive-data"></a>데이터를 수신하기 위한 필수 구성 요소
데이터 공유 초대를 수락하려면 먼저 아래에 나열된 여러 Azure 리소스를 프로비저닝해야 합니다. 

모든 필수 조건이 충족되었는지 확인한 후에 데이터 공유 초대를 수락합니다. 

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터 공유 초대: " **<yourdataprovider@domain.com>** 이(가) 보낸 Azure Data Share 초대"라는 제목이 있는 Microsoft Azure의 초대입니다.
* 데이터 공유 리소스를 만들 Azure 구독과 대상 Azure 데이터 저장소가 있는 Azure 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다.

### <a name="prerequisites-for-target-storage-account"></a>대상 스토리지 계정의 필수 구성 요소
Azure Storage로 데이터를 수신하도록 선택하는 경우 아래는 필수 구성 요소 목록입니다.

* Azure Storage 계정: 아직 없는 경우 [Azure Storage 계정](../storage/common/storage-account-create.md)을 만들 수 있습니다. 
* 스토리지 계정에 쓸 수 있는 권한으로, *Microsoft.Storage/storageAccounts/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다. 
* 스토리지 계정에 Data Share 리소스의 관리 ID 역할 할당을 추가할 수 있는 권한입니다. 이 권한은 *Microsoft.Authorization/role assignments/write* 에 있습니다. 이 권한은 **소유자** 역할에 있습니다.  

### <a name="prerequisites-for-sql-target"></a>SQL 대상의 필수 구성 요소
Azure SQL Database, Azure Synapse Analytics로 데이터를 받도록 선택하는 경우 아래는 필수 구성 요소 목록입니다. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW)로 데이터를 받기 위한 필수 구성 요소

사용자가 SQL Server의 **Azure Active Directory 관리자** 인 SQL Server로 데이터를 수신하기 위한 필수 구성 요소 목록은 다음과 같습니다.

* Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW).
* SQL 서버에 데이터베이스를 쓸 수 있는 권한으로, *Microsoft.Sql/servers/databases/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다.
* SQL Server Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 SQL 서버로 이동합니다. 왼쪽 탐색에서 *방화벽 및 가상 네트워크* 를 선택합니다.
    1. *Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용* 에 대해 **예** 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 
    
사용자가 **Azure Active Directory 관리자** 가 아닌 SQL Server로 데이터를 수신하기 위한 필수 구성 요소 목록은 다음과 같습니다. [단계별 데모](https://youtu.be/aeGISgK1xro)에 따라 필수 구성 요소를 구성할 수 있습니다.

* Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL DW).
* SQL 서버의 데이터베이스를 쓸 수 있는 권한으로, *Microsoft.Sql/servers/databases/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다. 
* Data Share 리소스의 관리 ID가 Azure SQL Database 또는 Azure Synapse Analytics에 액세스할 수 있는 권한입니다. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 SQL 서버로 이동하고 자신을 **Azure Active Directory 관리자** 로 설정합니다.
    1. [쿼리 편집기](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) 또는 Azure Active Directory 인증을 사용하는 SQL Server Management Studio를 사용하여 Azure SQL Database/Data Warehouse에 연결합니다. 
    1. 다음 스크립트를 실행하여 Data Share Managed Identity를 'db_datareader, db_datawriter, db_ddladmin'으로 추가합니다. SQL Server 인증이 아닌 Active Directory를 사용하여 연결해야 합니다. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.         

* SQL Server Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal의 SQL 서버에서 *방화벽 및 가상 네트워크* 로 이동합니다.
    1. *Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용* 에 대해 **예** 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Azure Synapse Analytics(작업 영역) SQL 풀로 데이터를 받기 위한 필수 구성 요소

* Azure Synapse Analytics(작업 영역) 전용 SQL 풀. 서버리스 SQL 풀로 데이터를 받는 것은 현재 지원되지 않습니다.
* *Microsoft.Synapse/workspaces/sqlPools/write* 에 있는 Synapse 작업 영역의 SQL 풀에 쓸 수 있는 권한입니다. 이 권한은 **기여자** 역할에 있습니다.
* Data Share 리소스의 관리 ID가 Synapse 작업 영역 SQL 풀에 액세스할 수 있는 권한입니다. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다. 왼쪽 탐색에서 SQL Active Directory 관리자를 선택하고 자신을 **Azure Active Directory 관리자** 로 설정합니다.
    1. Synapse Studio를 열고 왼쪽 탐색에서 *관리* 를 선택합니다. 보안에서 *액세스 제어* 를 선택합니다. 자신에게 **SQL 관리자** 또는 **작업 영역 관리자** 역할을 할당합니다.
    1. Synapse Studio의 왼쪽 탐색에서 *개발* 을 선택합니다. SQL 풀에서 다음 스크립트를 실행하여 Data Share 리소스 관리 ID를 'db_datareader, db_datawriter, db_ddladmin'으로 추가합니다. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.  

* Synapse 작업 영역 Firewall 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal에서 Synapse 작업 영역으로 이동합니다. 왼쪽 탐색에서 *방화벽* 을 선택합니다.
    1. **Azure 서비스 및 리소스가 이 작업 영역에 액세스할 수 있도록 허용** 하려면 *켜기* 를 클릭합니다.
    1. **+클라이언트 IP 추가** 를 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다.
    1. **저장** 을 클릭합니다. 

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="open-invitation"></a>초대 열기

1. 이메일 또는 Azure Portal에서 직접 초대를 열 수 있습니다. 

   이메일에서 초대를 열려면 받은 편지함에서 데이터 공급자의 초대를 확인합니다. 이 초대는 Microsoft Azure에서 **<yourdataprovider@domain.com>이(가) 보낸 Azure Data Share 초대** 라는 제목으로 보낸 것입니다. **초대 보기** 를 클릭하여 Azure에서 초대를 확인합니다. 

   Azure Portal에서 직접 초대를 열려면 Azure Portal에서 **데이터 공유 초대** 를 검색합니다. 그러면 데이터 공유 초대 목록으로 이동됩니다.

   테넌트의 게스트 사용자인 경우 처음으로 Data Share 초대를 보기 전에 테넌트 이메일 주소를 확인하라는 메시지가 표시됩니다. 확인되면 12개월 동안 유효합니다.

   ![초대 목록](./media/invitations.png "초대 목록") 

1. 확인하려는 공유를 선택합니다. 

### <a name="accept-invitation"></a>초대 수락
1. **사용 약관** 을 포함하여 모든 필드를 검토해야 합니다. 사용 약관에 동의하는 경우 해당 확인란을 선택하여 동의함을 표시해야 합니다. 

   ![사용 약관](./media/terms-of-use.png "사용 약관") 

1. *대상 데이터 공유 계정* 에서 Data Share를 배포할 대상의 구독 및 리소스 그룹을 선택합니다. 

   기존 Data Share 계정이 없는 경우 **데이터 공유 계정** 필드에서 **새로 만들기** 를 선택합니다. 그렇지 않으면 데이터 공유를 수락할 기존 Data Share 계정을 선택합니다. 

   **받은 공유 이름** 필드에 대해 데이터 공급자가 지정한 기본값을 그대로 두거나 받은 공유에 대해 새 이름을 지정할 수 있습니다. 

   사용 약관에 동의하고 받은 공유를 관리할 데이터 공유 계정을 지정했으면 **수락 및 구성** 을 선택합니다. 공유 구독이 만들어집니다. 

   ![수락 옵션](./media/accept-options.png "수락 옵션") 

   그러면 Data Share 계정에서 받은 공유로 이동합니다. 

   초대를 수락하지 않으려면 *거부* 를 선택합니다. 

### <a name="configure-received-share"></a>수신된 공유 구성
아래 단계에 따라 데이터를 받을 위치를 구성합니다.

1. **데이터 세트** 탭을 선택합니다. 대상을 할당하려는 데이터 세트 옆에 있는 상자를 선택합니다. **+ 대상에 매핑** 을 선택하여 대상 데이터 저장소를 선택합니다. 

   ![대상에 매핑](./media/dataset-map-target.png "대상에 매핑") 

1. 데이터를 가져올 대상 데이터 저장소를 선택합니다. 경로와 이름이 동일한 대상 데이터 저장소에 있는 모든 데이터 파일 또는 테이블을 덮어씁니다. SQL 대상으로 데이터를 수신하고 **Data Share에서 사용자를 대신하여 위의 ‘사용자 만들기’ SQL 스크립트를 실행하도록 허용** 확인란이 나타나면 확인란을 선택합니다. 그렇지 않으면 화면에 표시되는 스크립트를 실행하기 위한 필수 구성 요소의 지침에 따릅니다. 그러면 대상 SQL DB에 대한 Data Share 리소스 쓰기 권한이 제공됩니다.

   ![대상 스토리지 계정](./media/dataset-map-target-sql.png "대상 데이터 저장소") 

1. 스냅샷 기반 공유의 경우 데이터 공급자가 데이터에 정기적인 업데이트를 제공하기 위해 스냅샷 일정을 만든 경우 **스냅샷 일정** 탭을 선택하여 스냅샷 일정을 사용하도록 설정할 수도 있습니다. 스냅샷 일정 옆의 확인란을 선택하고 **+ 사용** 을 선택합니다. 첫 번째 예약된 스냅샷은 예약 시간의 1분 이내에 시작되고 후속 스냅샷은 예약 시간(초) 이내에 시작됩니다.

   ![스냅샷 일정 사용](./media/enable-snapshot-schedule.png "스냅샷 일정 사용")

### <a name="trigger-a-snapshot"></a>스냅샷 트리거
이 단계는 스냅샷 기반 공유에만 적용됩니다.

1. **세부 정보** 탭 다음에 **스냅샷 트리거** 를 선택하여 스냅샷을 트리거할 수 있습니다. 여기서는 데이터의 전체 또는 증분 스냅샷을 트리거할 수 있습니다. 데이터를 데이터 공급자로부터 처음 받는 경우 전체 복사본을 선택합니다. SQL 원본의 경우 전체 스냅샷만 지원됩니다. 스냅샷이 실행 중이면 이전 스냅샷이 완료될 때까지 후속 스냅샷이 시작되지 않습니다.

   ![스냅샷 트리거](./media/trigger-snapshot.png "스냅샷 트리거") 

1. 마지막 실행 상태가 *성공* 인 경우 대상 데이터 저장소로 이동하여 받은 데이터를 확인합니다. **데이터 세트** 를 선택하고 대상 경로에서 링크를 클릭합니다. 

   ![소비자 데이터 세트](./media/consumer-datasets.png "소비자 데이터 세트 매핑") 

### <a name="view-history"></a>기록 보기
이 단계는 스냅샷 기반 공유에만 적용됩니다. 스냅샷의 기록을 보려면 **기록** 탭을 선택합니다. 여기서는 지난 30일 동안 생성된 모든 스냅샷의 기록을 확인할 수 있습니다. 

## <a name="supported-data-types"></a>지원되는 데이터 형식
SQL 원본의 데이터를 공유하는 경우 스냅샷 프로세스 중에 다음과 같이 SQL Server 데이터 형식에서 Azure Data Share 중간 데이터 형식으로의 매핑이 사용됩니다. 

| SQL Server 데이터 형식 | Azure Data Share 중간 데이터 형식 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |부울 |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM 특성(varbinary(max)) |Byte[] |
| Float |Double |
| 이미지 |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |String |

>[!NOTE]
> 1. 10진수 중간 형식으로 매핑되는 데이터 형식의 경우 스냅샷은 현재 최대 28개의 전체 자릿수를 지원합니다. 전체 자릿수가 28개를 초과하는 데이터가 있으면 문자열로 변환하는 것이 좋습니다. 
> 1.  Azure SQL 데이터베이스에서 Azure Synapse Analytics로 데이터를 공유하는 경우 모든 데이터 형식이 지원되는 것은 아닙니다. 자세한 내용은 [전용 SQL 풀의 테이블 데이터 형식](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md)을 참조하세요. 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted 또는 동적 데이터 마스킹
현재 Azure Data Share는 Always Encrypted가 구성된 Azure SQL 데이터베이스를 지원하지 않습니다. 

동적 데이터 마스킹을 사용하는 원본 SQL 테이블의 경우 데이터는 수신자 측에 마스킹된 것으로 나타납니다.

## <a name="sql-snapshot-performance"></a>SQL 스냅샷 성능
SQL 스냅샷 성능은 여러 가지 요인의 영향을 받습니다. 항상 고유한 성능 테스트를 수행하는 것이 좋습니다. 다음은 성능에 영향을 주는 몇 가지 예제 요소입니다.

* 원본 또는 대상 데이터 저장소 IOPS (초당 입력/출력 작업 수) 및 대역폭.
* 원본과 대상 SQL 데이터 저장소의 하드웨어 구성(예: vCores, 메모리, DWU). 
* 원본과 대상 데이터 저장소에 대한 동시 액세스. 같은 SQL 데이터 저장소에서 여러 테이블과 뷰를 공유하거나 같은 SQL 데이터 저장소에 여러 테이블과 뷰를 수신하는 경우 성능에 영향을 미칩니다.
* 원본 및 대상 데이터 저장소와 원본 및 대상 데이터 저장소의 위치 간의 네트워크 대역폭
* 공유 되는 테이블 및 뷰의 크기입니다. SQL 스냅숏 공유는 전체 테이블의 전체 복사본을 만듭니다. 시간이 지남에 따라 테이블 크기가 증가 하는 경우 스냅숏은 더 오래 걸립니다. 

증분 업데이트가 필요한 규모가 많은 테이블의 경우 저장소 계정으로 업데이트를 내보내고 저장소 계정의 증분 공유 기능을 활용 하 여 성능을 향상 시킬 수 있습니다.

## <a name="troubleshoot-sql-snapshot-failure"></a>SQL 스냅샷 실패 문제 해결
스냅샷 실패의 가장 일반적인 원인은 Data Share에 원본 또는 대상 데이터 저장소에 대한 권한이 없기 때문입니다. 원본 또는 대상 Azure SQL Database 또는 Azure Synapse Analytics(이전에는 Azure SQL DW라고 함)에 Data Share 권한을 부여하려면 Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결할 때 제공된 SQL 스크립트를 실행해야 합니다. 추가 SQL 스냅샷 실패 문제를 해결하려면 [스냅샷 실패 문제 해결](data-share-troubleshoot.md#snapshots)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Share 서비스를 사용하여 SQL 원본에서 데이터를 공유하고 수신하는 방법을 알아보았습니다. 다른 데이터 소스에서 공유하는 방법에 관해 자세히 알아보려면 [지원되는 데이터 저장소](supported-data-stores.md)로 이동하세요.
