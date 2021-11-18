---
title: Studio의 네트워크 데이터 액세스
titleSuffix: Azure Machine Learning
description: 작업 영역 또는 저장소가 가상 네트워크에 있는 경우 Azure Machine Learning studio에서 데이터 액세스를 작동 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/08/2021
ms.openlocfilehash: c30b88361b4b617b092995cb8ddc0138ffcc8240
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706595"
---
# <a name="network-data-access-with-azure-machine-learning-studio"></a>Azure Machine Learning studio를 사용 하 여 네트워크 데이터 액세스

데이터 액세스는 복잡 하며, 많은 부분이 있는 것을 인식 하는 것이 중요 합니다. 예를 들어 Azure Machine Learning studio에서 데이터에 액세스 하는 것은 SDK를 사용 하는 것과 다릅니다. 로컬 개발 환경에서 SDK를 사용 하는 경우 클라우드의 데이터에 직접 액세스 하 게 됩니다. Studio를 사용 하는 경우 클라이언트에서 데이터 저장소에 직접 액세스 하는 것이 아닙니다. 스튜디오는 사용자 대신 작업 영역을 사용 하 여 데이터에 액세스 합니다.

> [!IMPORTANT]
> 이 문서의 정보는 Azure Machine Learning 솔루션에 필요한 인프라를 만드는 Azure 관리자를 위한 것입니다.

> [!TIP]
> 스튜디오에서는 VNet의 다음 데이터 저장소 형식 에서만 데이터를 읽을 수 있습니다.
>
> * Azure Storage 계정(BLOB 및 파일)
> * Azure Data Lake Storage Gen1
> * Azure Data Lake Storage Gen2
> * Azure SQL Database

## <a name="data-access"></a>데이터 액세스

일반적으로 studio의 데이터 액세스에는 다음 검사가 포함 됩니다.

1. Who에 액세스 하 고 있습니까?
    - 저장소 유형에 따라 다양 한 유형의 인증이 있습니다. 예를 들어 계정 키, 토큰, 서비스 주체, 관리 id 및 사용자 id가 있습니다.
    - 사용자 id를 사용 하 여 인증을 수행 하는 경우 저장소에 액세스 하려는 사용자 *를 알고 있어야* 합니다.
2. 사용 권한이 있나요?
    - 자격 증명이 올바른지 확인 합니다. 그렇다면 서비스 주체, 관리 id 등이 저장소에 대해 필요한 권한을 갖고 있나요? Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용 권한을 부여 합니다.
    - 저장소 계정 [판독기](../role-based-access-control/built-in-roles.md#reader) 는 저장소의 메타 데이터를 읽습니다.
    - [Storage blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 는 blob 컨테이너 내에서 데이터를 읽습니다.
    - [참가자](../role-based-access-control/built-in-roles.md#contributor) 는 저장소 계정에 대 한 쓰기 액세스를 허용 합니다.
    - 저장소 유형에 따라 더 많은 역할이 필요할 수 있습니다.
3. 어디에서 액세스할 수 있나요?
    - 사용자: 클라이언트 IP 주소는 VNet/서브넷 범위 입니까?
    - 작업 영역: 작업 영역 공용 입니까 아니면 VNet/서브넷에 개인 끝점이 있나요?
    - Storage: 저장소에서 공용 액세스를 허용 하거나, 서비스 끝점 또는 개인 끝점을 통해 액세스를 제한 하나요?
4. 어떤 작업을 수행 하 고 있습니까?
    - 데이터 저장소/데이터 집합에 대 한 CRUD (만들기, 읽기, 업데이트 및 삭제) 작업은 Azure Machine Learning에 의해 처리 됩니다.
    - 데이터 액세스 호출 (예: 미리 보기 또는 스키마)은 기본 저장소로 이동 하며 추가 권한이 필요 합니다.
5. 이 작업이 실행 되는 위치 Azure 구독에서 리소스를 계산 하거나 Microsoft 구독에서 호스트 되는 리소스를 계산 하 시겠습니까?
    - "프로필 생성" 옵션을 제외 하 고 데이터 집합 및 데이터 저장소 서비스에 대 한 모든 호출은 __Microsoft 구독__ 에 호스트 된 리소스를 사용 하 여 작업을 실행 합니다.
    - 데이터 집합에 대 한 "프로필 생성" 옵션을 포함 하는 작업과 __구독의__ 계산 리소스에서 실행 하 고 해당 데이터에 액세스 합니다. 따라서 계산 id에는 작업을 제출 하는 사용자의 id가 아닌 저장소에 대 한 권한이 필요 합니다.

다음 다이어그램에서는 데이터 액세스 호출의 일반적인 흐름을 보여 줍니다. 이 예제에서는 사용자가 계산 리소스를 사용 하지 않고 machine learning 작업 영역을 통해 데이터 액세스를 호출 하려고 합니다.

:::image type="content" source="./media/concept-network-data-access/data-access-flow.svg" alt-text="데이터에 액세스할 때의 논리 흐름 다이어그램":::

## <a name="azure-storage-account"></a>Azure Storage 계정

Azure Machine Learning studio에서 Azure Storage 계정을 사용 하는 경우 저장소 계정에 대 한 다음 Azure RBAC 역할에 작업 영역의 관리 되는 id를 추가 해야 합니다.

* [Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)
* 저장소 계정이 개인 끝점을 사용 하 여 VNet에 연결 하는 경우 관리 id에 저장소 계정 개인 끝점에 대 한 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader) 역할을 부여 해야 합니다.

자세한 내용은 [Azure Virtual Network에서 Azure Machine Learning studio 사용](how-to-enable-studio-virtual-network.md)을 참조 하세요.

VNet에서 작업 영역에 Azure Storage 계정을 사용할 때의 제한 사항에 대 한 자세한 내용은 다음 섹션을 참조 하세요.
### <a name="using-an-existing-storage-account"></a>기존 저장소 계정 사용

작업 영역을 만들 때 기존 저장소 계정을 __기본 저장소__ 로 사용 하는 경우 `azureml-filestore` 파일 저장소의 폴더가 자동으로 만들어지지 않습니다. 이 폴더는 [Automl](concept-automated-ml.md) 실험을 제출할 때 필요 합니다.

이 문제를 방지 하려면 작업 영역을 만들 때 기본 저장소를 만들도록 허용 하거나 작업 영역을 만들 때 기존 저장소 계정이 VNet에 __있지__ 않은지 확인 Azure Machine Learning 합니다. Azure Storage 계정으로 네트워킹에 대 한 자세한 내용은 [가상 네트워크를 사용 하 여 Azure Storage 계정 구성](../storage/common/storage-network-security.md)을 참조 하세요.

### <a name="azure-storage-firewall"></a>Azure Storage 방화벽

Azure Storage 계정이 가상 네트워크 뒤에 있는 경우 일반적으로 저장소 방화벽을 사용 하 여 클라이언트에서 인터넷을 통해 직접 연결할 수 있습니다. 그러나 studio를 사용 하는 경우 저장소 계정에 연결 하는 클라이언트가 아닙니다. 요청을 수행 하는 Azure Machine Learning 서비스입니다. 서비스의 IP 주소가 문서화 되지 않고 자주 변경 됩니다. __저장소 방화벽을 사용 하도록 설정 하면 스튜디오에서 VNet 구성의 저장소 계정에 액세스할 수 없습니다__.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1을 데이터 저장소로 사용하는 경우 POSIX 스타일 액세스 제어 목록만 사용할 수 있습니다. 다른 보안 주체와 마찬가지로 리소스에 작업 영역 관리 id 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md)를 참조하세요.

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2를 데이터 저장소로 사용하는 경우 Azure RBAC 및 POSIX 스타일 ACL(액세스 제어 목록)을 모두 사용하여 가상 네트워크 내부의 데이터 액세스를 제어할 수 있습니다.

**azure RBAC를 사용 하려면**' azure Virtual Network에서 Azure Machine Learning studio 사용 ' 문서에 있는 [데이터 저장소: Azure Storage 계정](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account) 섹션의 단계를 따르세요. Data Lake Storage Gen2은 Azure Storage을 기반으로 하므로 Azure RBAC를 사용 하는 경우에도 동일한 단계가 적용 됩니다.

**Acl을 사용 하려면** 다른 보안 주체와 마찬가지로 작업 영역의 관리 id에 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [파일 및 디렉터리에 대한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조하세요.

## <a name="azure-sql-database"></a>Azure SQL Database

관리 ID를 사용하여 Azure SQL Database에 저장된 데이터에 액세스하려면 관리 ID에 매핑되는 SQL 포함된 사용자를 만들어야 합니다. 외부 공급자에서 사용자를 만드는 방법에 대한 자세한 내용은 [Azure AD ID에 매핑된 포함된 사용자 만들기](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조하세요.

SQL 포함된 사용자를 만든 후에는 [GRANT T-SQL 명령](/sql/t-sql/statements/grant-object-permissions-transact-sql)사용하여 해당 사용자에게 권한을 부여합니다.

### <a name="deny-public-network-access"></a>퍼블릭 네트워크 액세스 거부

Azure SQL Database에서 __공용 네트워크 액세스 거부__ 를 사용 하 여 데이터베이스에 대 한 공용 액세스를 차단할 수 있습니다. 이 옵션을 사용 하는 경우 SQL Database에 대 한 액세스를 __지원 하지__ 않습니다. Azure Machine Learning studio에서 SQL Database를 사용 하는 경우 데이터 액세스는 항상 SQL Database에 대 한 공용 끝점을 통해 수행 됩니다.

## <a name="next-steps"></a>다음 단계

네트워크에서 studio를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Azure Virtual Network에서 Azure Machine Learning studio 사용](how-to-enable-studio-virtual-network.md)을 참조 하세요.