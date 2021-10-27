---
title: Azure Migrate 서버 평가에서 가져온 CSV 파일을 사용하여 온-프레미스 서버 평가
description: Azure Migrate 서버 평가에서 가져온 CSV 파일을 사용하여 Azure로 마이그레이션할 온-프레미스 서버를 검색하는 방법에 대해 설명합니다.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: 7edd739b13e91ec517b7e59219e1e4234c596252
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994122"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>자습서: 가져온 CSV 파일을 사용하여 서버 평가

Azure로 마이그레이션하는 과정의 일환으로 온-프레미스 인벤토리 및 워크로드를 검색합니다.

이 자습서에서는 Azure Migrate: 검색 및 평가 도구에서 가져온 쉼표로 구분된 값(CSV) 파일을 사용하여 온-프레미스 머신을 평가하는 방법을 보여 줍니다. 

CSV 파일을 사용하는 경우 서버를 검색하고 평가하기 위해 Azure Migrate 어플라이언스를 설정하지 않아도 됩니다. 파일에서 공유하는 데이터를 제어할 수 있으며, 대부분의 데이터는 선택 사항입니다. 이 방법은 다음과 같은 경우에 유용합니다.

- 어플라이언스를 배포하기 전에 빠른 초기 평가를 만들려고 합니다.
- 조직에 Azure Migrate 어플라이언스를 배포할 수 없습니다.
- 온-프레미스 서버에 대한 액세스를 허용하는 자격 증명을 공유할 수 없습니다.
- 보안 제약 조건 때문에 어플라이언스가 수집한 데이터를 Azure에 보낼 수 없습니다.

> [!NOTE]
> CSV 파일을 사용하여 가져온 서버는 마이그레이션할 수 없습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure 계정 설정
> * Azure Migrate 프로젝트 설정
> * CSV 파일 준비
> * 파일 가져오기
> * 서버 평가

> [!NOTE]
> 이 자습서에서는 시나리오를 시도할 수 있는 가장 빠른 경로를 보여 주며, 가능한 경우 기본 옵션을 사용합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- 단일 CSV 파일 및 Azure Migrate 프로젝트에서 최대 2만 개의 서버를 추가할 수 있습니다. 
- CSV 파일에 지정된 운영 체제 이름은 [지원되는 이름](#supported-operating-system-names)을 포함하고 일치해야 합니다.

## <a name="prepare-an-azure-user-account"></a>Azure 사용자 계정 준비

Azure Migrate 프로젝트를 만들려면 다음이 포함된 계정이 필요합니다.

- Azure 구독에 대한 기여자 또는 소유자 권한
- Azure Active Directory 앱을 등록할 수 있는 권한.

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다. 구독 소유자가 아닌 경우 다음과 같이 소유자와 협력하여 권한을 할당합니다.

1. Azure Portal에서 "구독"을 검색하여 **서비스** 에서 **구독** 을 선택합니다.

    ![Azure 구독을 검색하는 검색 상자](./media/tutorial-discover-import/search-subscription.png)

2. **구독** 페이지에서 Azure Migrate 프로젝트를 만들려는 구독을 선택합니다.
3. 구독에서 **액세스 제어(IAM)**  > **액세스 확인** 을 선택합니다.
4. **액세스 확인** 에서 관련 사용자 계정을 검색합니다.
5. **역할 할당 추가** 에서 **추가** 를 선택합니다.

    ![사용자 계정을 검색하여 액세스를 확인하고 역할을 할당합니다.](./media/tutorial-discover-import/azure-account-access.png)

6. **역할 할당 추가** 에서 기여자 또는 소유자 역할을 선택하고 계정(이 예제에서는 azmigrateuser)을 선택합니다. 그런 다음 **저장** 을 선택합니다.

    ![계정에 역할을 할당하는 역할 할당 추가 페이지를 엽니다.](./media/tutorial-discover-import/assign-role.png)

7. 포털에서 사용자를 검색하고 **서비스** 에서 **사용자** 를 선택합니다.
8. **사용자 설정** 에서 Azure AD 사용자가 애플리케이션을 등록할 수 있는지 확인합니다(기본적으로 **예** 로 설정됨).

    ![사용자 설정에서 사용자가 Active Directory 앱을 등록할 수 있는지 확인](./media/tutorial-discover-import/register-apps.png)

## <a name="set-up-a-project"></a>프로젝트 설정

Azure Migrate 프로젝트가 없는 경우 새 Azure Migrate 프로젝트를 설정합니다.

1. Azure Portal > **모든 서비스** 에서 **Azure Migrate** 를 검색합니다.
2. **서비스** 아래에서 **Azure Migrate** 를 선택합니다.
3. **개요** 에서 **프로젝트 만들기** 를 선택합니다.
5. **프로젝트 만들기** 에서 Azure 구독 및 리소스 그룹을 선택합니다. 리소스 그룹이 없는 경우 리소스 그룹을 만듭니다.
6. **프로젝트 세부 정보** 에서 프로젝트 이름과 이 프로젝트를 만들려는 지역을 지정합니다. [퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

   ![프로젝트 이름 및 지역 상자](./media/tutorial-discover-import/new-project.png)  
    > [!Note]
    > **고급** 구성 섹션을 사용하여 프라이빗 엔드포인트 연결로 Azure Migrate 프로젝트를 만듭니다. [자세한 정보](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity)

7. **만들기** 를 선택합니다.
8. Azure Migrate 프로젝트가 배포될 때까지 몇 분 정도 기다립니다.

**Azure Migrate : 서버 평가** 도구는 기본적으로 새 프로젝트에 추가됩니다.

![기본적으로 추가된 서버 평가 도구를 보여 주는 페이지](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>CSV 준비

CSV 템플릿을 다운로드하고, 여기에 서버 정보를 추가합니다.

### <a name="download-the-template"></a>템플릿 다운로드

1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 평가** 에서 **검색** 을 선택합니다.
2. **머신 검색** 에서 **CSV를 사용하여 가져오기** 를 선택합니다.
3. **다운로드** 를 선택하여 CSV 템플릿을 다운로드합니다. 또는 [직접 다운로드](https://go.microsoft.com/fwlink/?linkid=2109031)할 수 있습니다.

    ![CSV 템플릿 다운로드](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>서버 정보 추가

서버 데이터를 수집하여 CSV 파일에 추가합니다.

- 데이터를 수집하려면 VMware vSphere 또는 CMDB(구성 관리 데이터베이스)와 같은 온-프레미스 서버를 관리하는 데 사용하는 도구에서 데이터를 내보낼 수 있습니다.
- 예제 데이터를 검토하려면 [예제 파일](https://go.microsoft.com/fwlink/?linkid=2108405)을 다운로드합니다.

다음 표에서는 채울 파일 필드를 요약하여 보여 줍니다.

**필드 이름** | **필수** | **세부 정보**
--- | --- | ---
**서버 이름** | 예 | FQDN(정규화된 도메인 이름)을 지정하는 것이 좋습니다.
**IP 주소** | 예 | 서버 주소
**코어 수** | 예 | 서버에 할당된 프로세서 코어의 수
**메모리** | 예 | 서버에 할당된 총 RAM 크기(MB)
**OS 이름** | 예 | 서버 운영 체제 <br/> [이](#supported-operating-system-names) 목록의 이름과 일치하거나 이를 포함하는 운영 체제 이름은 평가에서 인식됩니다.
**OS 버전** | 예 | 서버 운영 체제 버전
**OS 아키텍처** | 예 | 서버 OS 아키텍처 <br/> 유효한 값은 x64, x86, amd64, 32 비트 또는 64 비트입니다.
**디스크 수** | 예 | 개별 디스크 세부 정보가 제공되는 경우 필요하지 않습니다.
**디스크 1 크기**  | 예 | 최대 디스크 크기(GB)<br/>템플릿에 [열을 추가](#add-multiple-disks)하여 추가 디스크에 대한 세부 정보를 추가할 수 있습니다. 최대 20개의 디스크를 추가할 수 있습니다.
**디스크 1 읽기 작업** | 예 | 초당 디스크 읽기 작업
**디스크 1 쓰기 작업** | 예 | 초당 디스크 쓰기 작업
**디스크 1 읽기 처리량** | 예 | 초당 디스크에서 읽은 데이터(MB/초)
**디스크 1 쓰기 처리량** | 예 | 초당 디스크에 쓴 데이터(MB/초)
**CPU 사용률** | 예 | 사용된 CPU 백분율
**메모리 사용률** | 예 | 사용된 RAM 백분율
**총 디스크 읽기 작업** | 예 | 초당 디스크 읽기 작업 수
**총 디스크 쓰기 작업** | 예 | 초당 디스크 쓰기 작업 수
**총 디스크 읽기 처리량** | 예 | 초당 디스크에서 읽은 데이터(MB/초)
**총 디스크 쓰기 처리량** | 예 | 초당 디스크에 쓴 데이터(MB/초)
**네트워크 입력 처리량** | 예 | 서버에서 받은 데이터(MB/초)
**네트워크 출력 처리량** | 예 | 서버에서 전송한 데이터(MB/초)
**펌웨어 유형** | 예 | 서버 펌웨어 값은 "BIOS" 또는 "UEFI"일 수 있습니다.
**MAC 주소**| 예 | 서버 MAC 주소

### <a name="add-operating-systems"></a>운영 체제 추가

평가는 특정 운영 체제 이름을 인식합니다. 지정하는 이름은 [지원되는 이름](#supported-operating-system-names) 목록의 문자열 중 하나와 정확히 일치해야 합니다.

### <a name="add-multiple-disks"></a>여러 디스크 추가

템플릿은 첫 번째 디스크의 기본 필드를 제공합니다. 최대 20개의 디스크에 대해 비슷한 열을 추가할 수 있습니다.

예를 들어 두 번째 디스크의 모든 필드를 지정하려면 다음 열을 추가합니다.

- 디스크 2 크기
- 디스크 2 읽기 작업 수
- 디스크 2 쓰기 작업 수
- 디스크 2 읽기 처리량
- 디스크 2 쓰기 처리량

## <a name="import-the-server-information"></a>서버 정보 가져오기

정보가 CSV 템플릿에 추가되면 CSV 파일을 서버 평가로 가져옵니다.

1. Azure Migrate의 **머신 검색** 에서 완료된 템플릿으로 이동합니다.
2. **가져오기** 를 선택합니다.
3. 가져오기 상태가 표시됩니다.
    - 상태에 경고가 표시되면 해당 경고를 해결하거나 이를 해결하지 않고 계속 진행할 수 있습니다.
    - 평가 정확도를 높이기 위해 경고에서 제시한 대로 서버 정보를 향상시킵니다.
    - 경고를 확인하고 해결하려면 **경고 정보 .CSV 다운로드** 를 선택합니다. 이 작업에서는 경고가 포함된 CSV를 다운로드합니다. 경고를 검토하고, 필요에 따라 문제를 해결합니다.
    - 상태에 오류가 표시되어 가져오기 상태가 **실패** 이면 가져오기를 계속하기 전에 해당 오류를 해결해야 합니다.
        1. 이제 오류 세부 정보가 포함된 CSV를 다운로드합니다.
        1. 필요에 따라 오류를 검토하고 해결합니다.
        1. 수정된 파일을 다시 업로드합니다.
4. 가져오기 상태가 **완료됨** 이면 서버 정보를 가져온 것입니다. 가져오기 프로세스가 완료되지 않은 것으로 보이는 경우 새로 고칩니다.

## <a name="update-server-information"></a>서버 정보 업데이트

동일한 **서버 이름** 을 사용하여 서버 데이터를 다시 가져와 서버 정보를 업데이트할 수 있습니다. **서버 이름** 필드는 수정할 수 없습니다. 서버 삭제는 현재 지원되지 않습니다.

## <a name="verify-servers-in-the-portal"></a>포털에서 서버 확인

검색 후 Azure Portal에서 해당 서버가 표시되는지 확인하려면 다음을 수행합니다.

1. Azure Migrate 대시보드를 엽니다.
2. **Azure Migrate - 서버** > **Azure Migrate: 서버 평가** 페이지에서 **검색된 서버** 의 수를 표시하는 아이콘을 선택합니다.
3. **가져오기 기반** 탭을 선택합니다.

## <a name="supported-operating-system-names"></a>지원되는 운영 체제 이름

CSV에 제공된 운영 체제 이름은 포함하고 일치해야 합니다. 그렇지 않은 경우에는 평가할 수 없습니다. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>macOS X 10<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>윈도우 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="assessment-considerations"></a>평가 고려 사항

- CSV 파일을 사용하여 가져오기를 수행하는 경우 성능 기반 크기 조정을 선택하면 지정하는 성능 값(CPU 사용률, 메모리 사용률, 디스크 IOPS 및 처리량)이 사용됩니다. 성능 기록과 백분위수 정보를 제공할 수 없습니다. 
- Azure VM 및 Azure VMware Solution 평가에서 정확한 OS 적합성/준비 상태를 얻으려면 해당 열에 운영 체제 버전 및 아키텍처를 입력하세요.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure Migrate 프로젝트를 만들었습니다.
> * 가져온 CSV 파일을 사용하여 서버를 검색했습니다.
이제 [Azure VM으로 VMware VM 마이그레이션](./tutorial-assess-vmware-azure-vm.md)에 대한 평가를 실행합니다.