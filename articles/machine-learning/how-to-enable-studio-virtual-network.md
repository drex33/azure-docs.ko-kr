---
title: 가상 네트워크에서 Azure Machine Learning 스튜디오 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오를 구성하여 가상 네트워크 내부에 저장된 데이터에 액세스하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 07/13/2021
ms.custom: contperf-fy20q4, tracking-python, security
ms.openlocfilehash: 6a10384757552108aefc3dd828bf0fd7ddce2f82
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427498"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure 가상 네트워크에서 Azure Machine Learning 스튜디오 사용

이 문서에서는 가상 네트워크에서 Azure Machine Learning 스튜디오를 사용하는 방법에 대해 알아봅니다. 스튜디오에는 AutoML, 디자이너, 데이터 레이블 지정과 같은 기능이 포함되어 있습니다. 

일부 스튜디오 기능은 가상 네트워크에서 기본적으로 사용하지 않도록 설정되어 있습니다. 해당 기능을 다시 사용하도록 설정하려면 스튜디오에서 사용하려는 스토리지 계정에 대해 관리 ID를 사용하도록 설정해야 합니다. 

다음 작업은 가상 네트워크에서 기본적으로 사용하지 않도록 설정되어 있습니다.

* 스튜디오에서 데이터 미리 보기.
* 디자이너에서 데이터 시각화
* 디자이너에서 모델 배포
* AutoML 실험 제출
* 레이블 지정 프로젝트 시작.

스튜디오는 가상 네트워크의 다음 데이터 저장소 형식에서 데이터를 읽을 수 있도록 지원합니다.

* Azure Storage 계정(BLOB 및 파일)
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> - 가상 네트워크 내에 저장된 데이터에 대한 액세스 권한을 스튜디오에 제공합니다.
> - 가상 네트워크 내의 리소스에서 스튜디오에 액세스합니다.
> - 스튜디오가 스토리지 보안에 미치는 영향을 이해합니다.

> [!TIP]
> 이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.
>
> * [Virtual Network 개요](how-to-network-security-overview.md)
> * [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
> * [학습 환경 보호](how-to-secure-training-vnet.md)
> * [보안 유추 환경](how-to-secure-inferencing-vnet.md)
> * [사용자 지정 DNS 사용](how-to-custom-dns.md)
> * [방화벽 사용](how-to-access-azureml-behind-firewall.md)


## <a name="prerequisites"></a>사전 요구 사항

+ 일반적인 가상 네트워크 시나리오 및 아키텍처를 이해하려면 [네트워크 보안 개요](how-to-network-security-overview.md)를 참조하세요.

+ 사용할 기존 가상 네트워크 및 서브넷.

+ 기본 [프라이빗 엔드포인트를 사용하는 Azure Machine Learning 작업 영역](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)입니다.

+ [가상 네트워크에 추가된 기존 Azure 스토리지 계정](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts).

## <a name="limitations"></a>제한 사항

### <a name="azure-storage-account"></a>Azure Storage 계정

AutoML 실험을 제출하는 데 필요한 `azureml-filestore` 폴더를 기본 파일 저장소가 자동으로 만들지 않는 알려진 문제가 있습니다. 해당 문제는 사용자가 작업 영역을 만드는 동안 기존 파일 저장소를 가져와 기본 파일 저장소로 설정할 때 발생합니다.

문제를 방지할 수 있는 두 가지 옵션이 있습니다. 1) 작업 영역을 만들 때 자동으로 만들어지는 기본 파일 저장소를 사용합니다. 2) 고유한 파일 저장소를 가져오려면 작업 영역을 만드는 동안 파일 저장소가 VNet 외부에 있는지 확인합니다. 작업 영역을 만든 후 가상 네트워크에 스토리지 계정을 추가합니다.

문제를 해결하려면 가상 네트워크에서 파일 저장소 계정을 제거한 다음 가상 네트워크에 다시 추가합니다.
## <a name="datastore-azure-storage-account"></a>데이터 저장소: Azure Storage 계정

Azure Blob 및 File Storage에 저장된 데이터에 대한 액세스를 사용하도록 설정하려면 다음 단계를 사용합니다.

> [!TIP]
> 작업 영역의 기본 스토리지 계정에는 첫 번째 단계가 필요하지 않습니다. 다른 모든 단계는 VNet 뒤에 있는 *모든* 스토리지 계정에 필요하며 기본 스토리지 계정을 포함하여 작업 영역에서 사용됩니다.

1. **스토리지 계정이 작업 영역의 *기본* 스토리지인 경우 이 단계를 건너뛰세요**. 기본값이 아닌 경우 Blob Storage에서 데이터를 읽을 수 있도록 Azure Storage 계정에 대한 **작업 영역 관리 ID에 '스토리지 Blob 데이터 읽기 권한자' 역할을 부여** 합니다.

    자세한 내용은 [Blob 데이터 읽기 권한자](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 기본 제공 역할을 참조하세요.

1. **스토리지 프라이빗 엔드포인트에 대한 '읽기 권한자' 역할을 작업 영역 관리 ID에 부여합니다**. 스토리지 서비스가 __프라이빗 엔드포인트__ 를 사용하는 경우 작업 영역 관리 ID에 프라이빗 엔드포인트에 대한 **읽기 권한자** 액세스 권한을 부여합니다. Azure AD의 작업 영역 관리 ID는 Azure Machine Learning 작업 영역과 이름이 같습니다.

    > [!TIP]
    > 스토리지 계정에는 여러 개의 프라이빗 엔드포인트가 있을 수 있습니다. 예를 들어 하나의 스토리지 계정에는 Blob 및 파일 스토리지에 대해 별도의 프라이빗 엔드포인트가 있을 수 있습니다. 두 엔드포인트에 관리 ID를 추가합니다.

    자세한 내용은 [판독기](../role-based-access-control/built-in-roles.md#reader) 기본 제공 역할을 참조하세요.

   <a id='enable-managed-identity'></a>
1. **기본 스토리지 계정에 관리 ID 인증을 사용하도록 설정합니다**. 각 Azure Machine Learning 작업 영역에는 작업 영역을 만들 때 정의되는 두 개의 기본 스토리지 계정인 기본 Blob 스토리지 계정과 기본 파일 스토리지 계정이 있습니다. **데이터 저장소** 관리 페이지에서 새 기본값을 설정할 수도 있습니다.

    ![기본 데이터 저장소를 확인할 수 있는 위치를 보여주는 스크린샷](./media/how-to-enable-studio-virtual-network/default-datastores.png)

    다음 표에서는 작업 영역 기본 스토리지 계정에 대해 관리 ID 인증을 사용해야 하는 이유에 대해 설명합니다.

    |스토리지 계정  | 참고  |
    |---------|---------|
    |작업 영역 기본 Blob 스토리지| 디자이너의 모델 자산을 저장합니다. 디자이너에서 모델을 배포하려면 해당 스토리지 계정에서 관리 ID 인증을 사용하도록 설정합니다. <br> <br> 관리 ID를 사용하도록 구성된 기본이 아닌 데이터 저장소를 사용하는 경우 디자이너 파이프라인을 시각화하고 실행할 수 있습니다. 그러나 기본 데이터 저장소에서 관리 ID를 사용하도록 설정하지 않고 학습된 모델을 배포하려고 하면 어떤 데이터 저장소를 사용 중이든 배포가 실패합니다.|
    |작업 영역 기본 파일 저장소| AutoML 실험 자산을 저장합니다. AutoML 실험을 제출하려면 해당 스토리지 계정에서 관리 ID 인증을 사용하도록 설정합니다. |

1. **관리 ID 인증을 사용하도록 데이터 저장소 구성**. [서비스 엔드포인트](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts)나 [프라이빗 엔드포인트](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts)를 사용하여 가상 네트워크에 Azure 스토리지 계정을 추가한 후 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md) 인증을 사용하도록 데이터 저장소를 구성해야 합니다. 그러면 스튜디오에서 스토리지 계정의 데이터에 액세스할 수 있습니다.

    Azure Machine Learning은 [데이터 저장소](concept-data.md#datastores)를 사용하여 스토리지 계정에 연결합니다. 새 데이터 저장소를 생성할 때 관리 ID 인증을 사용하도록 데이터 저장소를 구성하려면 다음 단계를 따르세요.

    1. 스튜디오에서 __데이터 저장소__ 를 선택합니다.

    1. 기존 데이터 저장소를 업데이트하려면 데이터 저장소를 선택하고 __자격 증명 업데이트__ 를 선택합니다.

        새 데이터 저장소를 만들려면 __+ 새 데이터 저장소__ 를 선택합니다.

    1. 데이터 저장소 설정에서 __Azure Machine Learning 스튜디오의 데이터 미리 보기 및 프로파일링에 작업 영역 관리 ID 사용__ 에 대해 __예__ 를 선택합니다.

        ![작업 영역 관리 ID를 사용하도록 설정하는 방법을 보여주는 스크린샷](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

    해당 단계는 Azure RBAC를 사용하여 새 스토리지 서비스에 작업 영역 관리 ID를 __읽기 권한자__ 로 추가합니다. __읽기 권한자__ 액세스를 사용하면 작업 영역에서 리소스를 볼 수 있지만 변경할 수는 없습니다.

## <a name="datastore-azure-data-lake-storage-gen1"></a>데이터 저장소: Azure Data Lake Storage Gen1 사용

Azure Data Lake Storage Gen1을 데이터 저장소로 사용하는 경우 POSIX 스타일 액세스 제어 목록만 사용할 수 있습니다. 다른 보안 원칙과 마찬가지로 리소스에 작업 영역 관리 ID 액세스 권한을 할당할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md)를 참조하세요.

## <a name="datastore-azure-data-lake-storage-gen2"></a>데이터 저장소: Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2를 데이터 저장소로 사용하는 경우 Azure RBAC 및 POSIX 스타일 ACL(액세스 제어 목록)을 모두 사용하여 가상 네트워크 내부의 데이터 액세스를 제어할 수 있습니다.

**Azure RBAC를 사용하려면** [Blob 데이터 판독기](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할에 작업 영역 관리 ID를 추가합니다. 자세한 내용은 [Azure 역할 기반 액세스 제어](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)를 참조하세요.

**ACL을 사용하려면** 다른 보안 원칙과 마찬가지로 작업 영역 관리 ID에 액세스 권한을 할당하면 됩니다. 자세한 내용은 [파일 및 디렉터리에 대한 액세스 제어 목록](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)을 참조하세요.

## <a name="datastore-azure-sql-database"></a>데이터 저장소: Azure SQL 데이터베이스

관리 ID를 사용하여 Azure SQL Database에 저장된 데이터에 액세스하려면 관리 ID에 매핑되는 SQL 포함된 사용자를 만들어야 합니다. 외부 공급자에서 사용자를 만드는 방법에 대한 자세한 내용은 [Azure AD ID에 매핑된 포함된 사용자 만들기](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조하세요.

SQL 포함된 사용자를 만든 후에는 [GRANT T-SQL 명령](/sql/t-sql/statements/grant-object-permissions-transact-sql)사용하여 해당 사용자에게 권한을 부여합니다.

## <a name="intermediate-module-output"></a>중간 모듈 출력

Azure Machine Learning 디자이너 중간 모듈 출력을 사용하는 경우 디자이너의 모든 모듈에 대한 출력 위치를 지정할 수 있습니다. 보안, 로깅 또는 감사를 위해 중간 데이터 세트를 별도의 위치에 저장하는 데 사용할 수 있습니다. 출력을 지정하려면 다음 단계를 사용합니다.

1. 출력을 지정하려는 모듈을 선택합니다.
1. 오른쪽에 표시되는 모듈 설정 창에서 **출력 설정** 을 선택합니다.
1. 각 모듈 출력에 사용할 데이터 저장소를 지정합니다.

가상 네트워크의 중간 스토리지 계정에 대한 액세스 권한이 있는지 확인합니다. 액세스 권한이 없으면 파이프라인이 실패합니다.

또한 출력 데이터를 시각화하려면 중간 스토리지 계정에 [관리 ID 인증을 사용하도록 설정](#enable-managed-identity)합니다.
## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>VNet 내의 리소스에서 스튜디오에 액세스

가상 네트워크 내의 리소스(예: 컴퓨팅 인스턴스 또는 가상 머신)에서 스튜디오에 액세스하는 경우 가상 네트워크에서 스튜디오로의 아웃바운드 트래픽을 허용해야 합니다. 

예를 들어 NSG(네트워크 보안 그룹)를 사용하여 아웃바운드 트래픽을 제한하는 경우 __AzureFrontDoor.Frontend__ 의 __서비스 태그__ 대상에 규칙을 추가합니다.

## <a name="firewall-settings"></a>방화벽 설정

Azure Storage 계정과 같은 일부 저장소 서비스에는 해당 특정 서비스 인스턴스의 퍼블릭 엔드포인트에 적용되는 방화벽 설정이 있습니다. 일반적으로 이 설정을 사용하면 공용 인터넷에서 특정 IP 주소의 액세스를 허용하거나 허용하지 않을 수 있습니다. Azure Machine Learning 스튜디오를 사용하는 경우에는 __지원되지 않습니다__. Azure Machine Learning SDK 또는 CLI를 사용할 때 지원됩니다.

> [!TIP]
> Azure Firewall 서비스를 사용하는 경우 Azure Machine Learning 스튜디오가 지원됩니다. 자세한 내용은 [방화벽 뒤에 작업 영역 사용](how-to-access-azureml-behind-firewall.md)을 참조하세요.
## <a name="next-steps"></a>다음 단계

이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.

* [Virtual Network 개요](how-to-network-security-overview.md)
* [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
* [학습 환경 보호](how-to-secure-training-vnet.md)
* [보안 유추 환경](how-to-secure-inferencing-vnet.md)
* [사용자 지정 DNS 사용](how-to-custom-dns.md)
* [방화벽 사용](how-to-access-azureml-behind-firewall.md)
