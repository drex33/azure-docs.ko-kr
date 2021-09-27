---
title: Azure Data Lake Storage Gen1에 저장된 데이터 보호 | Microsoft Docs
description: 그룹 및 액세스 제어 목록을 사용하여 Azure Data Lake Storage Gen1의 데이터를 보호하는 방법에 대해 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 03/26/2018
ms.author: normesta
ms.openlocfilehash: 8e2127f8ea144693dcfdf09e1dbad5f075e4646c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620771"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1에 저장된 데이터 보호
Azure Data Lake Storage Gen1의 데이터 보호는 3단계로 이루어집니다.  사용자 및 보안 그룹의 데이터에 대한 액세스를 완전히 가능하게 하려면 Azure RBAC(Azure 역할 기반 액세스 제어) 및 ACL(액세스 제어 목록) 모두를 설정해야 합니다.

1. Azure Active Directory(Azure AD)에 보안 그룹을 만들어 시작합니다. 이러한 보안 그룹은 Azure Portal에서 Azure 역할 기반 액세스 제어(Azure RBAC)를 구현하는 데 사용됩니다. 자세한 내용은 [Azure RBAC](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
2. Azure AD 보안 그룹을 Data Lake Storage Gen1 계정에 할당합니다. 이는 포털에서 Data Lake Storage Gen1 계정에 대한 액세스 및 포털이나 API에서의 관리 작업을 제어합니다.
3. Azure AD 보안 그룹을 Data Lake Storage Gen1 파일 시스템에 ACL(액세스 제어 목록)로 할당합니다.
4. 또한 Data Lake Storage Gen1의 데이터에 액세스할 수 있는 클라이언트에 대한 IP 주소 범위를 설정할 수 있습니다.

이 문서에서는 Azure 포털을 사용하여 위의 작업을 수행하는 방법에 대한 지침을 제공합니다. Data Lake Storage Gen1이 계정 및 데이터 수준에서 보안을 구현하는 방법에 대한 자세한 내용은 [Azure Data Lake Storage Gen1의 보안](data-lake-store-security-overview.md)을 참조하세요. Data Lake Storage Gen1에서 ACL의 구현 방식에 대한 자세한 내용은 [Data Lake Storage Gen1의 액세스 제어 개요](data-lake-store-access-control.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
이 자습서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Data Lake Storage Gen1 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.

## <a name="create-security-groups-in-azure-active-directory"></a>Azure Active Directory의 보안 그룹 만들기
Azure AD 보안 그룹을 만드는 방법 및 사용자를 그룹에 추가하는 방법에 대한 지침은 [Azure Active Directory의 보안 그룹 관리](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

> [!NOTE] 
> Azure Portal을 사용하여 Azure AD에서 그룹에 사용자 및 다른 그룹을 추가할 수 있습니다. 그러나 서비스 사용자를 그룹에 추가하기 위해서는 [Azure AD PowerShell 모듈](../active-directory/enterprise-users/groups-settings-v2-cmdlets.md)을 사용하세요.
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>사용자 또는 보안 그룹을 Data Lake Storage Gen1 계정에 할당
사용자 또는 보안 그룹을 Data Lake Storage Gen1 계정에 할당하는 경우 Azure Portal 및 Azure Resource Manager API를 사용하여 계정의 관리 작업에 대한 액세스를 제어합니다. 

1. Data Lake Storage Gen1 계정을 엽니다. 왼쪽 창에서 **모든 리소스** 를 클릭한 다음, 모든 리소스 블레이드에서 사용자 또는 보안 그룹을 할당하려는 계정 이름을 클릭합니다.

2. Data Lake Storage Gen1 계정 블레이드에서 **액세스 제어(IAM)** 를 클릭합니다. 기본적으로 블레이드에 구독 소유자가 소유자로 나열됩니다.
   
    ![보안 그룹을 Azure Data Lake Storage Gen1 계정에 할당](./media/data-lake-store-secure-data/adl.select.user.icon1.png "보안 그룹을 Azure Data Lake Storage Gen1 계정에 할당")

3. **액세스 제어(IAM)** 블레이드에서 **추가** 를 클릭하여 **권한 추가** 블레이드를 엽니다. **권한 추가** 블레이드에서 사용자/그룹에 대한 **역할** 을 선택합니다. Azure Active Directory에서 이전에 만든 보안 그룹을 찾아서 선택합니다. 검색할 사용자와 그룹이 많을 경우 **선택** 텍스트 상자를 사용하여 그룹 이름을 필터링합니다. 
   
    ![사용자에 대한 역할 추가](./media/data-lake-store-secure-data/adl.add.user.1.png "사용자에 대한 역할 추가")
   
    **소유자** 및 **참가자** 역할은 데이터 레이크 계정에 다양한 관리 기능에 대한 액세스를 제공합니다. Data Lake의 데이터와 상호 작용하지만 계정 관리 정보를 봐야 하는 사용자는 **독자** 역할에 추가할 수 있습니다. 이러한 역할의 범위는 Data Lake Storage Gen1 계정에 관련된 관리 작업으로 제한됩니다.
   
    데이터 작업의 경우 개별 파일 시스템 권한은 사용자가 수행할 수 있는 권한을 정의합니다. 따라서 리더 역할을 가진 사용자는 계정에 연결된 관리 설정만 볼 수 있지만 잠재적으로 할당된 파일 시스템 권한을 기반으로 데이터를 읽고 쓸 수 있습니다. Data Lake Storage Gen1 파일 시스템 권한은 [ACL로 보안 그룹을 Azure Data Lake Storage Gen1 파일 시스템에 할당](#filepermissions)에 설명되어 있습니다.

    > [!IMPORTANT]
    > **소유자** 역할만 파일 시스템 액세스가 자동으로 설정됩니다. **참가자**, **독자** 및 기타 모든 역할의 경우 ACL이 있어야 폴더 및 파일에 대해 모든 수준의 액세스를 가능하게 할 수 있습니다.  **소유자** 역할은 슈퍼 사용자 파일 및 폴더 권한을 제공하며 ACL을 통해 재정의할 수 없습니다. Azure RBAC 정책이 데이터 액세스에 매핑되는 방식에 대한 자세한 내용은 [계정 관리를 위한 Azure RBAC](data-lake-store-security-overview.md#azure-rbac-for-account-management)를 참조하세요.

4. **권한 추가** 블레이드에 나열되지 않은 그룹/사용자를 추가하려면 **선택** 텍스트 상자에 해당 사용자의 이메일 주소를 입력한 다음, 목록에서 선택하여 초대할 수 있습니다.
   
    ![보안 그룹 추가](./media/data-lake-store-secure-data/adl.add.user.2.png "보안 그룹 추가")
   
5. **저장** 을 클릭합니다. 아래와 같이 추가된 보안 그룹을 확인해야 합니다.
   
    ![추가된 보안 그룹](./media/data-lake-store-secure-data/adl.add.user.3.png "추가된 보안 그룹")

6. 이제 사용자/보안 그룹은 Data Lake Storage Gen1 계정에 대한 액세스를 가집니다. 특정 사용자에게 액세스를 제공하려는 경우 보안 그룹에 추가할 수 있습니다. 마찬가지로 사용자에 대한 액세스를 취소하려는 경우 보안 그룹에서 제거할 수 있습니다. 또한 여러 보안 그룹을 계정에 할당할 수 있습니다. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>사용자 또는 보안 그룹을 ACL로 Data Lake Storage Gen1 파일 시스템에 할당
사용자/보안 그룹을 Data Lake Storage Gen1 파일 시스템에 할당하여 Data Lake Storage Gen1에 저장된 데이터에 대한 액세스 제어를 설정합니다.

1. Data Lake Storage Gen1 계정 블레이드에서 **데이터 탐색기** 를 클릭합니다.
   
    ![데이터 탐색기를 통해 데이터 보기](./media/data-lake-store-secure-data/adl.start.data.explorer.png "데이터 탐색기를 통해 데이터 보기")
2. **데이터 탐색기** 블레이드에서 ACL을 구성할 폴더를 클릭한 다음, **액세스** 를 클릭합니다. 파일에 ACL을 지정하려면 먼저 파일을 클릭하여 미리 본 다음, **파일 미리 보기** 블레이드에서 **액세스** 를 클릭해야 합니다.
   
    ![Data Lake Storage Gen1 파일 시스템에서 ACL 설정](./media/data-lake-store-secure-data/adl.acl.1.png "Data Lake Storage Gen1 파일 시스템에서 ACL 설정")
3. 루트에 이미 할당된 소유자 및 할당된 사용 권한이 **액세스** 블레이드에 나열됩니다. 액세스 ACL을 추가하려면 **추가** 아이콘을 클릭합니다.
    > [!IMPORTANT]
    > 단일 파일에 액세스 권한을 설정한다고 해서 해당 파일에 대한 사용자/그룹 액세스가 반드시 허용되는 것은 아닙니다. 할당된 사용자/그룹이 파일 경로에 액세스할 수 있어야 합니다. 자세한 내용과 예제는 [사용 권한과 관련된 일반적인 시나리오](data-lake-store-access-control.md#common-scenarios-related-to-permissions)를 참조하세요.
   
    ![표준 및 사용자 지정 액세스 나열](./media/data-lake-store-secure-data/adl.acl.2.png "표준 및 사용자 지정 액세스 나열")
   
   * **소유자** 및 **모든 사람** 은 UNIX 스타일의 액세스를 제공합니다. 여기서는 세 가지 사용자 클래스(소유자, 그룹 및 기타)에 대해 읽기, 쓰기, 실행(rwx)을 지정합니다.
   * **할당된 권한** 은 파일 소유자 또는 그룹 외에 구체적으로 명명된 사용자 또는 그룹에 대한 권한을 설정할 수 있는 POSIX ACL에 해당합니다. 
     
     자세한 내용은 [HDFS ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists)을 참조하세요. Data Lake Storage Gen1에서 ACL의 구현 방식에 대한 자세한 내용은 [Data Lake Storage Gen1의 액세스 제어](data-lake-store-access-control.md)를 참조하세요.
4. **추가** 아이콘을 클릭하여 **권한 할당** 블레이드를 엽니다. 이 블레이드에서 **사용자 또는 그룹 선택** 을 클릭한 다음, **사용자 또는 그룹 선택** 블레이드에서 Azure Active Directory에서 이전에 만든 보안 그룹을 검색합니다. 검색할 그룹이 많을 경우 위쪽의 텍스트 상자를 사용하여 그룹 이름을 필터링합니다. 추가하려는 그룹을 클릭한 다음 **선택** 을 클릭합니다.
   
    ![그룹 추가](./media/data-lake-store-secure-data/adl.acl.3.png "그룹 추가")
5. **권한 선택** 을 클릭하고, 권한을 재귀적으로 적용할지 여부와 권한을 액세스 ACL, 기본 ACL 또는 둘 모두로 할당할지 여부를 선택합니다. **확인** 을 클릭합니다.
   
    ![권한 선택 옵션이 호출된 권한 할당 블레이드와 확인 옵션이 호출된 권한 선택 블레이드의 스크린샷](./media/data-lake-store-secure-data/adl.acl.4.png "그룹에 권한 할당")
   
    Data Lake Storage Gen1의 사용 권한 및 기본/액세스 ACL에 대한 자세한 내용은 [Data Lake Storage Gen1에서 액세스 제어](data-lake-store-access-control.md)를 참조하세요.
6. **권한 선택** 블레이드에서 **확인** 을 클릭하면 새로 추가된 그룹 및 관련 사용 권한이 **액세스** 블레이드에 나열됩니다.
   
    ![데이터 엔지니어링 옵션이 호출된 액세스 블레이드의 스크린샷](./media/data-lake-store-secure-data/adl.acl.5.png "그룹에 권한 할당")
   
   > [!IMPORTANT]
   > 현재 릴리스에서는 **할당된 권한** 에 최대 28개의 항목을 포함할 수 있습니다. 28명 이상의 사용자를 추가하려는 경우 보안 그룹을 만들어서 사용자를 보안 그룹에 추가하고 Data Lake Storage Gen1 계정을 위한 해당 보안 그룹에 대한 액세스를 제공해야 합니다.
   > 
   > 
7. 필요한 경우 그룹을 추가한 후에 액세스 권한을 수정할 수도 있습니다. 보안 그룹에 대한 해당 권한을 제거하거나 할당할지 여부에 따라 각 권한 형식(읽기, 쓰기, 실행)에 대한 확인란을 취소하거나 선택합니다. **저장** 을 클릭하여 변경 내용을 저장하거나 **취소** 를 클릭하여 변경 내용을 취소합니다.

## <a name="set-ip-address-range-for-data-access"></a>데이터 액세스에 대한 IP 주소 범위를 설정합니다.
Data Lake Storage Gen1을 사용하면 데이터 저장소에 대한 액세스를 네트워크 수준에서 추가로 잠글 수 있습니다. 방화벽을 설정하고 IP 주소를 지정하며 신뢰할 수 있는 클라이언트에 대한 범위를 정의할 수 있습니다. 사용하도록 설정되면 정의된 범위 내에 있는 IP 주소를 보유한 클라이언트만 저장소에 연결할 수 있습니다.

![방화벽 설정 및 IP 액세스](./media/data-lake-store-secure-data/firewall-ip-access.png "방화벽 설정 및 IP 액세스")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 계정에 대한 보안 그룹 제거
Data Lake Storage Gen1 계정에서 보안 그룹을 제거하는 경우 Azure Portal 및 Azure Resource Manager API를 사용하여 계정의 관리 작업에 대한 액세스만 변경합니다.  

데이터에 대한 액세스는 변경되지 않고 액세스 ACL에서 계속 관리됩니다.  여기에 대한 예외는 소유자 역할의 사용자/그룹입니다.  소유자 역할에서 제거된 사용자/그룹은 더 이상 슈퍼 사용자가 아니며 해당 액세스는 액세스 ACL 설정으로 돌아갑니다. 

1. Data Lake Storage Gen1 계정 블레이드에서 **액세스 제어(IAM)** 를 클릭합니다. 
   
    ![보안 그룹을 Data Lake Storage Gen1 계정에 할당](./media/data-lake-store-secure-data/adl.select.user.icon.png "보안 그룹을 Data Lake Storage Gen1 계정에 할당")
2. **액세스 제어(IAM)** 블레이드에서 제거하려는 보안 그룹을 클릭합니다. **제거** 를 클릭합니다.
   
    ![제거된 보안 그룹](./media/data-lake-store-secure-data/adl.remove.group.png "제거된 보안 그룹")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1 파일 시스템에서 보안 그룹 ACL 제거
Data Lake Storage Gen1 파일 시스템에서 보안 그룹 ACL을 제거하면 Data Lake Storage Gen1 계정의 데이터에 대한 액세스가 변경됩니다.

1. Data Lake Storage Gen1 계정 블레이드에서 **데이터 탐색기** 를 클릭합니다.
   
    ![Data Lake Storage Gen1 계정에서 디렉터리 만들기](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Data Lake Storage Gen1 계정에서 디렉터리 만들기")
2. **데이터 탐색기** 블레이드에서 ACL을 제거할 폴더를 클릭한 다음, **액세스** 를 클릭합니다. 파일에 대한 ACL을 제거하려면 먼저 파일을 클릭하여 미리 본 다음, **파일 미리 보기** 블레이드에서 **액세스** 를 클릭해야 합니다. 
   
    ![Data Lake Storage Gen1 파일 시스템에서 ACL 설정](./media/data-lake-store-secure-data/adl.acl.1.png "Data Lake Storage Gen1 파일 시스템에서 ACL 설정")
3. **액세스** 블레이드에서 제거하려는 보안 그룹을 클릭합니다. **액세스 세부 정보** 블레이드에서 **제거** 를 클릭합니다.
   
    ![데이터 엔지니어링 옵션이 호출된 액세스 블레이드와 제거 옵션이 호출된 액세스 세부 정보 블레이드를 보여 주는 스크린샷](./media/data-lake-store-secure-data/adl.remove.acl.png "그룹에 권한 할당")

## <a name="see-also"></a>참고 항목
* [Azure Data Lake Storage Gen1 개요](data-lake-store-overview.md)
* [Azure Storage Blob에서 Data Lake Storage Gen1로 데이터 복사](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Storage Gen1에서 Azure Data Lake Analytics 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1에서 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
* [PowerShell을 사용하여 Data Lake Storage Gen1 시작](data-lake-store-get-started-powershell.md)
* [.NET SDK를 사용하여 Data Lake Storage Gen1 시작](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1에 대한 진단 로그 액세스](data-lake-store-diagnostic-logs.md)
