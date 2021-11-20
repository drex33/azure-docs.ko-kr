---
title: 스튜디오의 네트워크 데이터 액세스
titleSuffix: Azure Machine Learning
description: 작업 영역 또는 스토리지가 가상 네트워크에 있을 때 Azure Machine Learning Studio에서 데이터 액세스가 작동하는 방식을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/19/2021
ms.openlocfilehash: c7a11c7ca4256ffc0dc70eb7c10c4b6a6a9e9dac
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132869956"
---
# <a name="network-data-access-with-azure-machine-learning-studio"></a>Azure Machine Learning Studio를 사용하여 네트워크 데이터 액세스

데이터 액세스는 복잡하며 많은 부분이 있다는 것을 인식하는 것이 중요합니다. 예를 들어 Azure Machine Learning Studio에서 데이터에 액세스하는 것은 SDK를 사용하는 것과 다릅니다. 로컬 개발 환경에서 SDK를 사용하는 경우 클라우드의 데이터에 직접 액세스하게 됩니다. Studio를 사용하는 경우 항상 클라이언트에서 데이터 저장소에 직접 액세스하는 것은 아닙니다. Studio는 작업 영역을 사용하여 사용자 대신 데이터에 액세스합니다.

> [!IMPORTANT]
> 이 문서의 정보는 Azure Machine Learning 솔루션에 필요한 인프라를 만드는 Azure 관리자를 위한 것입니다.

> [!TIP]
> Studio는 VNet에서 다음 데이터 저장소 형식의 데이터 읽기만 지원합니다.
>
> * Azure Storage 계정(BLOB 및 파일)
> * Azure Data Lake Storage Gen1
> * Azure Data Lake Storage Gen2
> * Azure SQL Database

## <a name="data-access"></a>데이터 액세스

일반적으로 스튜디오의 데이터 액세스에는 다음과 같은 검사가 포함됩니다.

1. Who 액세스하고 있나요?
    - 스토리지 유형에 따라 여러 가지 유형의 인증이 있습니다. 예를 들어 계정 키, 토큰, 서비스 주체, 관리 ID 및 사용자 ID가 있습니다.
    - 사용자 ID를 사용하여 인증을 만드는 경우 스토리지에 액세스하려는 *사용자를* 알고 있는 것이 중요합니다.
2. 사용 권한이 있나요?
    - 자격 증명이 올바른가요? 그렇다면 서비스 주체, 관리 ID 등에 스토리지에 필요한 권한이 있나요? 권한은 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 부여됩니다.
    - 스토리지 계정 [읽기는](../role-based-access-control/built-in-roles.md#reader) 스토리지의 메타데이터를 읽습니다.
    - [Storage Blob 데이터 판독기는](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Blob 컨테이너 내에서 데이터를 읽습니다.
    - [기여자는](../role-based-access-control/built-in-roles.md#contributor) 스토리지 계정에 대한 쓰기 액세스를 허용합니다.
    - 스토리지 유형에 따라 더 많은 역할이 필요할 수 있습니다.
3. 액세스 위치는 어디인가요?
    - 사용자: 클라이언트 IP 주소가 VNet/서브넷 범위에 있나요?
    - 작업 영역: 작업 영역이 공용인가요 아니면 VNet/서브넷에 프라이빗 엔드포인트가 있나요?
    - Storage: 스토리지에서 공용 액세스를 허용하나요, 아니면 서비스 엔드포인트 또는 프라이빗 엔드포인트를 통한 액세스를 제한하나요?
4. 어떤 작업이 수행되고 있나요?
    - 데이터 저장소/데이터 세트에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업은 Azure Machine Learning 처리됩니다.
    - 데이터 액세스 호출(예: 미리 보기 또는 스키마)은 기본 스토리지로 이동하고 추가 권한이 필요합니다.
5. 여기서 이 작업은 실행되고 있습니다. Azure 구독의 컴퓨팅 리소스 또는 Microsoft 구독에서 호스트되는 리소스는 무엇인가요?
    - 데이터 세트 및 데이터 저장소 서비스에 대한 모든 호출("프로필 생성" 옵션 제외)은 __Microsoft 구독에서__ 호스트되는 리소스를 사용하여 작업을 실행합니다.
    - 데이터 세트에 대한 "프로필 생성" 옵션을 포함한 작업은 __구독의__ 컴퓨팅 리소스에서 실행되고 해당 위치에서 데이터에 액세스합니다. 따라서 컴퓨팅 ID에는 작업을 제출하는 사용자의 ID가 아닌 스토리지에 대한 권한이 필요합니다.

다음 다이어그램은 데이터 액세스 호출의 일반적인 흐름을 보여줍니다. 이 예제에서 사용자는 컴퓨팅 리소스를 사용하지 않고 기계 학습 작업 영역을 통해 데이터 액세스 호출을 수행하려고 합니다.

:::image type="content" source="./media/concept-network-data-access/data-access-flow.svg" alt-text="데이터에 액세스할 때의 논리 흐름 다이어그램":::

## <a name="azure-storage-account"></a>Azure Storage 계정

Azure Machine Learning Studio에서 Azure Storage 계정을 사용하는 경우 스토리지 계정에 대한 다음 Azure RBAC 역할에 작업 영역의 관리 ID를 추가해야 합니다.

* [Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)
* 스토리지 계정이 프라이빗 엔드포인트를 사용하여 VNet에 연결하는 경우 관리 ID에 스토리지 계정 프라이빗 엔드포인트에 대한 [읽기](../role-based-access-control/built-in-roles.md#reader) 권한자 역할을 부여해야 합니다.

자세한 내용은 [Azure Virtual Network Azure Machine Learning Studio 사용을](how-to-enable-studio-virtual-network.md)참조하세요.

VNet의 작업 영역에서 Azure Storage 계정을 사용하는 경우의 제한에 대한 자세한 내용은 다음 섹션을 참조하세요.

### <a name="azure-storage-firewall"></a>Azure Storage 방화벽

Azure Storage 계정이 가상 네트워크 뒤에 있는 경우 일반적으로 스토리지 방화벽을 사용하여 클라이언트가 인터넷을 통해 직접 연결할 수 있도록 할 수 있습니다. 그러나 Studio를 사용하는 경우 스토리지 계정에 연결하는 클라이언트가 아닙니다. 요청을 만드는 Azure Machine Learning 서비스입니다. 서비스의 IP 주소는 문서화되지 않으며 자주 변경됩니다. __스토리지 방화벽을 사용하도록 설정하면 스튜디오가 VNet 구성 에서 스토리지 계정에 액세스할 수 없습니다.__

### <a name="azure-storage-endpoint-type"></a>Azure Storage 엔드포인트 유형

작업 영역에서 프라이빗 엔드포인트를 사용하고 스토리지 계정도 VNet에 있는 경우 Studio를 사용할 때 추가 유효성 검사 요구 사항이 있습니다.

* 스토리지 계정이 서비스 __엔드포인트__ 를 사용하는 경우 작업 영역 프라이빗 엔드포인트 및 스토리지 서비스 엔드포인트는 VNet의 동일한 서브넷에 있어야 합니다.
* 스토리지 계정이 프라이빗 __엔드포인트__ 를 사용하는 경우 작업 영역 프라이빗 엔드포인트 및 스토리지 서비스 엔드포인트는 동일한 VNet에 있어야 합니다. 이 경우 서로 다른 서브넷에 있을 수 있습니다.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1을 데이터 저장소로 사용하는 경우 POSIX 스타일 액세스 제어 목록만 사용할 수 있습니다. 다른 보안 주체와 마찬가지로 리소스에 작업 영역의 관리 ID 액세스를 할당할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md)를 참조하세요.

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2를 데이터 저장소로 사용하는 경우 Azure RBAC 및 POSIX 스타일 ACL(액세스 제어 목록)을 모두 사용하여 가상 네트워크 내부의 데이터 액세스를 제어할 수 있습니다.

**Azure RBAC를 사용하려면 'Azure** Virtual Network Azure Machine Learning Studio 사용' 문서의 [데이터 저장소:](how-to-enable-studio-virtual-network.md#datastore-azure-storage-account) Azure Storage 계정 섹션의 단계를 따릅니다. Data Lake Storage Gen2는 Azure Storage 기반으로 하므로 Azure RBAC를 사용할 때 동일한 단계가 적용됩니다.

**ACL을 사용하려면** 다른 보안 주체와 마찬가지로 작업 영역의 관리 ID에 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [파일 및 디렉터리에 대한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조하세요.

## <a name="azure-sql-database"></a>Azure SQL Database

관리 ID를 사용하여 Azure SQL Database에 저장된 데이터에 액세스하려면 관리 ID에 매핑되는 SQL 포함된 사용자를 만들어야 합니다. 외부 공급자에서 사용자를 만드는 방법에 대한 자세한 내용은 [Azure AD ID에 매핑된 포함된 사용자 만들기](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조하세요.

SQL 포함된 사용자를 만든 후에는 [GRANT T-SQL 명령](/sql/t-sql/statements/grant-object-permissions-transact-sql)사용하여 해당 사용자에게 권한을 부여합니다.

### <a name="deny-public-network-access"></a>퍼블릭 네트워크 액세스 거부

Azure SQL Database __공용 네트워크 액세스 거부를__ 사용하면 데이터베이스에 대한 공용 액세스를 차단할 수 있습니다. 이 옵션을 사용하는 경우 SQL Database 액세스를 __지원하지 않습니다.__ Azure Machine Learning Studio에서 SQL Database 사용하는 경우 데이터 액세스는 항상 SQL Database 대한 공용 엔드포인트를 통해 이루어집니다.

## <a name="next-steps"></a>다음 단계

네트워크에서 studio를 사용하도록 설정하는 자세한 내용은 [Azure Virtual Network Azure Machine Learning Studio 사용을](how-to-enable-studio-virtual-network.md)참조하세요.