---
title: Azure Blob Storage 및 Azure Data Lake Storage에서 데이터 공유 및 수신
description: Azure Blob Storage 및 Azure Data Lake Storage에서 데이터를 공유하고 수신하는 방법을 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 09/10/2021
ms.openlocfilehash: 61189ba63c072f2c08acc55047d494b076288953
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743258"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>Azure Blob Storage 및 Azure Data Lake Storage에서 데이터 공유 및 수신

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure Data Share는 스토리지 계정에서의 스냅샷 기반 공유를 지원합니다. 이 문서에서는 Azure Blob Storage, Azure Data Lake Storage Gen1 및 Azure Data Lake Storage Gen2에서 데이터를 공유하고 받는 방법을 설명합니다.

Azure Data Share는 Azure Data Lake Gen1 및 Azure Data Lake Gen2에서 파일, 폴더 및 파일 시스템을 공유하도록 지원합니다. 또한 Azure Blob Storage에서 blob, 폴더 및 컨테이너를 공유할 수 있도록 지원합니다. 블록, 추가 또는 페이지 Blob을 공유할 수 있으며 블록 Blob으로 수신됩니다. 이 원본에서 공유하는 데이터는 Azure Data Lake Gen2 또는 Azure Blob Storage에서 받을 수 있습니다.

스냅샷 기반 공유에서 파일 시스템, 컨테이너 또는 폴더가 공유되는 경우 데이터 소비자는 공유 데이터의 전체 복사본을 만들도록 선택할 수 있습니다. 또는 증분 스냅샷 기능을 사용하여 새 파일이나 업데이트된 파일만 복사할 수 있습니다. 증분 스냅샷 기능은 파일의 마지막 수정 시간을 기반으로 합니다. 

스냅샷을 생성하는 동안 같은 이름의 기존 파일을 덮어씁니다. 원본에서 삭제된 파일은 대상에서 삭제되지 않습니다. 원본의 비어 있는 하위 폴더는 대상에 복사되지 않습니다. 

## <a name="share-data"></a>데이터 공유

Azure Data Share를 사용하여 데이터를 공유하려면 다음 섹션의 정보를 사용하세요. 
### <a name="prerequisites-to-share-data"></a>데이터 공유를 위한 필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 수신자의 Azure 로그인 메일 주소를 찾습니다. 수신자의 메일 별칭은 사용자의 목적에 맞지 않습니다.
* 원본 Azure 데이터 저장소가 Data Share 리소스를 만드는 데 사용하는 것과 다른 Azure 구독에 있는 경우 Azure 데이터 저장소가 있는 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다. 

### <a name="prerequisites-for-the-source-storage-account"></a>원본 스토리지 계정의 필수 구성 요소

* Azure Storage 계정. 아직 계정이 없으면 [하나를 만듭니다](../storage/common/storage-account-create.md).
* 스토리지 계정에 대한 쓰기 권한. 쓰기 권한은 *Microsoft.Storage/storageAccounts/write* 에 있으며, 기여자 역할에 포함되어 있습니다.
* 스토리지 계정에 역할 할당을 추가할 권한. 이 권한은 *Microsoft.Authorization/role assignments/write* 에 있으며, 소유자 역할에 포함되어 있습니다. 

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="create-a-data-share-account"></a>Data Share 계정 만들기

Azure 리소스 그룹에서 Azure Data Share 리소스를 만듭니다.

1. 포털의 왼쪽 상단 모서리에 있는 메뉴를 연 다음, **리소스 만들기**(+)를 선택합니다.

1. *Data Share* 를 검색합니다.

1. **Data Share** 를 선택하고 **만들기** 를 선택합니다.

1. 다음과 같은 Azure Data Share 리소스의 기본 세부 정보를 제공합니다. 

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 데이터 공유 계정에 맞는 Azure 구독을 선택합니다.|
    | 리소스 그룹 | *test-resource-group* | 기존 리소스 그룹을 사용하거나 리소스 그룹을 만듭니다. |
    | 위치 | *미국 동부 2* | 데이터 공유 계정에 대한 지역을 선택합니다.
    | 이름 | *datashareaccount* | 데이터 공유 계정의 이름을 지정합니다. |
    | | |

1. **검토 + 만들기** > **만들기** 를 선택하여 데이터 공유 계정을 프로비저닝합니다. 새 데이터 공유 계정을 프로비저닝하는 데 일반적으로 2분 정도 걸립니다. 

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

### <a name="create-a-share"></a>공유 만들기

1. 데이터 공유 **개요** 페이지로 이동합니다.

   :::image type="content" source="./media/share-receive-data.png" alt-text="데이터 공유 개요를 보여 주는 스크린샷":::

1. **Start sharing your data**(데이터 공유 시작)를 선택합니다.

1. **만들기** 를 선택합니다.   

1. 공유에 관한 세부 정보를 제공합니다. 이름, 공유 유형, 공유 콘텐츠의 설명 및 사용 약관(선택 사항)을 지정합니다. 

    ![데이터 공유 세부 정보를 보여 주는 스크린샷](./media/enter-share-details.png "데이터 공유 세부 정보를 입력합니다.") 

1. **계속** 을 선택합니다.

1. 공유에 데이터 세트를 추가하려면 **데이터 세트 추가** 를 선택합니다. 

    ![공유에 데이터 세트를 추가하는 방법을 보여 주는 스크린샷](./media/datasets.png "데이터 세트.")

1. 추가할 데이터 세트 형식을 선택합니다. 데이터 세트 형식 목록은 이전 단계에서 스냅샷 기반 공유를 선택했는지 아니면 내부 공유를 선택했는지에 따라 달라집니다. 

    ![데이터 세트 형식을 선택할 위치를 보여 주는 스크린샷](./media/add-datasets.png "데이터 세트를 추가합니다.")    

1. 공유할 개체로 이동합니다. 그런 다음 **데이터 세트 추가** 를 선택합니다. 

    ![공유할 개체를 선택하는 방법을 보여 주는 스크린샷.](./media/select-datasets.png "데이터 세트를 선택합니다.")    

1. **수신자** 탭에서 **수신자 추가** 를 선택하여 데이터 소비자의 메일 주소를 추가합니다. 

    ![수신자 메일 주소를 추가하는 방법을 보여 주는 스크린샷.](./media/add-recipient.png "수신자를 추가합니다.") 

1. **계속** 을 선택합니다.

1. 스냅샷 공유 형식을 선택한 경우 데이터 소비자의 데이터를 업데이트하도록 스냅샷 일정을 설정할 수 있습니다. 

    ![스냅샷 일정 설정을 보여 주는 스크린샷.](./media/enable-snapshots.png "스냅샷을 사용으로 설정합니다.") 

1. 시작 시간과 되풀이 간격을 선택합니다. 

1. **계속** 을 선택합니다.

1. **검토 + 만들기** 탭에서 패키지 콘텐츠, 설정, 수신자 및 동기화 설정을 검토합니다. 그런 다음 **생성** 를 선택합니다.

이제 Azure 데이터 공유를 만들었습니다. 데이터 공유의 수신자는 초대를 수락할 수 있습니다. 

## <a name="receive-data"></a>데이터 수신

다음 섹션에서는 공유 데이터를 수신하는 방법을 설명합니다.
### <a name="prerequisites-to-receive-data"></a>데이터를 수신하기 위한 필수 구성 요소
데이터 공유 초대를 수락하기 전에 다음과 같은 필수 구성 요소가 있는지 확인합니다. 

* Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure의 초대. 메일 제목은 “ *\<yourdataprovider\@domain.com>* 의 Azure Data Share 초대”여야 합니다.
* 다음에서 등록된 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration).
    * Data Share 리소스를 만들 Azure 구독.
    * 대상 Azure 데이터 저장소가 있는 Azure 구독.

### <a name="prerequisites-for-a-target-storage-account"></a>대상 스토리지 계정의 필수 구성 요소

* Azure Storage 계정. 아직 계정이 없는 경우 [계정 만들기](../storage/common/storage-account-create.md)를 수행합니다. 
* 스토리지 계정에 대한 쓰기 권한. 이 권한은 *Microsoft.Storage/storageAccounts/write* 에 있으며, 기여자 역할에 포함되어 있습니다. 
* 스토리지 계정에 역할 할당을 추가할 권한. 이 할당은 *Microsoft.Authorization/role assignments/write* 에 있으며, 소유자 역할에 포함되어 있습니다.  

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="open-an-invitation"></a>초대 열기

메일에서 초대를 열거나 Azure Portal에서 직접 초대를 열 수 있습니다.

1. 메일에서 초대를 열려면 받은 편지함에서 데이터 공급자의 초대를 확인합니다. Microsoft Azure의 초대는 " *\<yourdataprovider\@domain.com>* 에서 보낸 Azure Data Share 초대"라는 제목으로 전송됩니다. **초대 보기** 를 선택하여 Azure에서 초대를 확인합니다. 

   Azure Portal에서 초대를 열려면 *Data Share 초대* 를 검색합니다. Data Share 초대 목록이 표시됩니다.

   테넌트 게스트 사용자인 경우 처음으로 Data Share 초대를 보기 전에 테넌트 이메일 주소를 확인하라는 메시지가 표시됩니다. 확인되면 12개월 동안 유효합니다.

   ![Azure Portal의 초대 목록을 보여 주는 스크린샷.](./media/invitations.png "초대 목록.") 

1. 보려는 공유를 선택합니다. 

### <a name="accept-an-invitation"></a>초대 수락
1. **사용 약관** 을 포함하여 모든 필드를 검토합니다. 약관에 동의하면 확인란을 선택합니다. 

   ![사용 약관 영역을 보여 주는 스크린샷.](./media/terms-of-use.png "사용 약관.") 

1. **대상 데이터 공유 계정** 에서 Data Share를 배포할 대상의 구독 및 리소스 그룹을 선택합니다. 그 후, 다음 필드를 입력합니다.

   * Data Share 계정이 없는 경우 **데이터 공유 계정** 필드에서 **새로 만들기** 를 선택합니다. 그렇지 않으면 데이터 공유를 수락할 기존 Data Share 계정을 선택합니다. 

   * **받은 공유 이름** 필드에서 데이터 공급자가 지정한 기본값을 그대로 두거나 받은 공유에 새 이름을 지정할 수 있습니다. 

1. **수락 및 구성** 을 선택합니다. 공유 구독이 생성됩니다. 

   ![구성 옵션을 허용할 위치를 보여 주는 스크린샷.](./media/accept-options.png "수락 옵션") 

    받은 공유가 Data Share 계정에 표시됩니다. 

    초대를 수락하지 않으려면 **거부** 를 선택합니다. 

### <a name="configure-a-received-share"></a>수신된 공유 구성
이 섹션의 단계를 따라 데이터를 받을 위치를 구성합니다.

1. **데이터 세트** 탭에서 대상을 할당하려는 데이터 세트 옆의 확인란을 선택합니다. **대상에 매핑** 을 선택하여 대상 데이터 저장소를 선택합니다. 

   ![대상에 매핑하는 방법을 보여 주는 스크린샷.](./media/dataset-map-target.png "대상에 매핑합니다.") 

1. 데이터의 대상 데이터 저장소를 선택합니다. 수신된 데이터의 파일과 경로 및 이름이 같은 대상 데이터 저장소의 파일을 겹쳐씁니다. 

   ![대상 스토리지 계정을 선택할 위치를 보여 주는 스크린샷.](./media/map-target.png "대상 스토리지.") 

1. 스냅샷 기반 공유의 경우 데이터 공급자가 스냅샷 일정을 사용하여 정기적으로 데이터를 업데이트하는 경우 **스냅샷 일정** 탭에서 일정을 사용으로 설정할 수 있습니다. 스냅샷 일정 옆에 있는 상자를 선택합니다. 그런 다음 **사용** 을 선택합니다. 첫 번째 예약된 스냅샷은 예약 시간의 1분 이내에 시작되고 후속 스냅샷은 예약 시간(초) 이내에 시작됩니다.

   ![스냅샷 일정을 사용하도록 설정하는 방법을 보여 주는 스크린샷.](./media/enable-snapshot-schedule.png "스냅샷 일정을 사용으로 설정합니다.")

### <a name="trigger-a-snapshot"></a>스냅샷 트리거
이 섹션의 단계는 스냅샷 기반 공유에만 적용됩니다.

1. **세부 정보** 탭에서 스냅샷을 트리거할 수 있습니다. 탭에서 **스냅샷 트리거** 를 선택합니다. 데이터의 전체 스냅샷 또는 증분 스냅샷을 트리거하도록 선택할 수 있습니다. 데이터를 데이터 공급자로부터 처음 받는 경우 **전체 복사본** 을 선택합니다. 스냅샷이 실행 중이면 이전 스냅샷이 완료될 때까지 후속 스냅샷이 시작되지 않습니다.

   ![트리거 스냅샷 선택 항목을 보여 주는 스크린샷.](./media/trigger-snapshot.png "스냅샷을 트리거합니다.") 

1. 마지막 실행 상태가 *성공* 인 경우 대상 데이터 저장소로 이동하여 받은 데이터를 확인합니다. **데이터 세트** 를 선택하고 대상 경로 링크를 선택합니다. 

   ![소비자 데이터 세트 매핑을 보여 주는 스크린샷.](./media/consumer-datasets.png "소비자 데이터 세트 매핑.") 

### <a name="view-history"></a>기록 보기
스냅샷 기반 공유에서만 스냅샷의 기록을 볼 수 있습니다. 기록을 보려면 **기록** 탭을 엽니다. 여기에서 지난 30일 동안 생성된 모든 스냅샷의 기록을 볼 수 있습니다. 

## <a name="storage-snapshot-performance"></a>스토리지 스냅샷 성능
스토리지 스냅샷 성능은 공유 데이터의 파일 수와 크기에 대한 다양한 요인의 영향을 받습니다. 항상 고유한 성능 테스트를 수행하는 것이 좋습니다. 다음은 성능에 영향을 주는 몇 가지 예제 요소입니다.

* 원본과 대상 데이터 저장소에 대한 동시 액세스.  
* 원본과 대상 데이터 저장소의 위치. 
* 증분 스냅샷의 경우 공유 데이터 세트의 파일 수는 마지막으로 성공한 스냅샷 이후 마지막으로 수정된 시간을 통해 파일 목록을 찾는 데 걸리는 시간에 영향을 줄 수 있습니다. 


## <a name="next-steps"></a>다음 단계
Azure Data Share 서비스를 사용하여 스토리지 계정에서 데이터를 공유하고 수신하는 방법을 알아보았습니다. 다른 데이터 소스에서 공유하는 방법에 관해 알아보려면 [지원되는 데이터 저장소](supported-data-stores.md)를 참조하세요.
