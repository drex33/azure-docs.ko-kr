---
title: 조직 외부에서 공유(Azure Portal) - Azure Data Share 빠른 시작
description: 이 빠른 시작에서는 Azure Data Share를 사용하여 고객 및 파트너와 데이터를 공유하는 방법을 알아봅니다.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: quickstart
ms.date: 10/30/2020
ms.custom: mode-portal
ms.openlocfilehash: 9090ed56fa6d8240a627a7ab9e8a0ba539c488cc
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133075436"
---
# <a name="quickstart-share-data-using-azure-data-share-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Data Share를 사용하여 데이터 공유

이 빠른 시작에서는 Azure Portal을 사용하여 스토리지 계정에서 데이터를 공유하도록 새 Azure Data Share를 설정하는 방법을 알아봅니다.

## <a name="prerequisites"></a>전제 조건

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 수신자 Azure 로그인 이메일 주소(이메일 별칭을 사용하면 작동하지 않음).
* 원본 Azure 데이터 저장소가 데이터 공유 리소스를 만드는 데 사용하는 것과 다른 Azure 구독에 있는 경우 Azure 데이터 저장소가 있는 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다. 

### <a name="share-from-a-storage-account"></a>스토리지 계정에서 공유

* Azure Storage 계정: 아직 없는 경우 [Azure Storage 계정](../storage/common/storage-account-create.md)을 만들 수 있습니다.
* 스토리지 계정에 쓸 수 있는 권한으로, *Microsoft.Storage/storageAccounts/write* 에 있습니다. 이 권한은 **기여자** 역할에 있습니다.
* 스토리지 계정에 역할 할당을 추가할 수 있는 권한입니다. 이 권한은 *Microsoft.Authorization/role assignments/write* 에 있습니다. 이 권한은 **소유자** 역할에 있습니다. 

## <a name="create-a-data-share-account"></a>Data Share 계정 만들기

Azure 리소스 그룹에서 Azure Data Share 리소스를 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 포털의 왼쪽 상단 모서리에서 **리소스 만들기** 단추(+)를 선택합니다.

1. *Data Share* 를 검색합니다.

1. **Data Share** 를 선택하고 **만들기** 를 선택합니다.

1. 다음 정보를 사용하여 Azure Data Share 리소스의 기본 세부 정보를 채웁니다. 

   **설정** | **제안 값** | **필드 설명**
   |---|---|---|
   | Subscription | 사용자의 구독 | 데이터 공유 계정에 사용할 Azure 구독을 선택합니다.|
   | 리소스 그룹 | *test-resource-group* | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
   | 위치 | *미국 동부 2* | 데이터 공유 계정에 대한 지역을 선택합니다.
   | 이름 | *datashareaccount* | 데이터 공유 계정의 이름을 지정합니다. |

1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 데이터 공유 계정을 프로비저닝합니다. 새 데이터 공유 계정을 프로비저닝하는 데 일반적으로 2분 정도 걸립니다.

1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

## <a name="create-a-share"></a>공유 만들기

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

1. 공유하려는 개체로 이동하여 '데이터 세트 추가'를 선택합니다. 

   ![SelectDatasets](./media/select-datasets.png "데이터 세트 선택")    

1. 받는 사람 탭에서 '+ 받는 사람 추가'를 선택하여 데이터 소비자의 이메일 주소를 입력합니다.

   ![AddRecipients](./media/add-recipient.png "수신자 추가") 

1. **계속** 을 선택합니다.

1. 스냅샷 공유 유형을 선택한 경우 데이터 소비자에게 데이터 업데이트를 제공하도록 스냅샷 일정을 구성할 수 있습니다. 

   ![EnableSnapshots](./media/enable-snapshots.png "스냅샷 사용") 

1. 시작 시간과 되풀이 간격을 선택합니다. 

1. **계속** 을 선택합니다.

1. 검토 + 만들기 탭에서 패키지 콘텐츠, 설정, 받는 사람 및 동기화 설정을 검토합니다. **만들기** 를 선택합니다.

이제 Azure Data Share가 생성되었고 Data Share의 받는 사람이 초대를 수락할 준비가 되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

리소스가 더 이상 필요하지 않으면 **Data Share 개요** 페이지로 이동하고 **삭제** 를 선택하여 제거합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Data Share를 만드는 방법을 알아보았습니다. 데이터 소비자가 데이터 공유를 수락하고 받는 방법을 알아보려면, [데이터 수락 및 받기](subscribe-to-data-share.md) 자습서를 계속 진행하세요. 
