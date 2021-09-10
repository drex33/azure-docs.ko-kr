---
title: Azure Stack Edge Pro GPU 스토리지 계정 관리 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Stack Edge Pro GPU에서 스토리지 계정을 관리하는 방법에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/13/2021
ms.author: alkohli
ms.openlocfilehash: 3c5cb61bf7450ea4668c6368d75615cdf43c2c40
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122530263"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro-gpu"></a>Azure Portal을 사용하여 Azure Stack Edge Pro GPU에서 Edge 스토리지 계정 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro에서 Edge 스토리지 계정을 관리하는 방법에 대해 설명합니다. Azure Portal 또는 로컬 웹 UI를 통해 Azure Stack Edge Pro GPU를 관리할 수 있습니다. Azure Portal를 사용하여 디바이스에 Edge 스토리지 계정을 추가하거나 삭제할 수 있습니다.

## <a name="about-edge-storage-accounts"></a>Edge 스토리지 계정 정보

SMB, NFS 또는 REST 프로토콜을 통해 Azure Stack Edge Pro GPU 디바이스에서 데이터를 전송할 수 있습니다. REST API를 사용하여 Blob 스토리지에 데이터를 전송하려면 디바이스에 Edge 스토리지 계정을 만들어야 합니다. 

Azure Stack Edge Pro GPU 디바이스에 추가하는 Edge 스토리지 계정은 Azure Storage 계정에 매핑됩니다. Edge 스토리지 계정에 기록된 모든 데이터는 클라우드에 자동으로 푸시됩니다.

아래의 다이어그램에서는 두 가지 유형의 계정과 각 계정에서 Azure로 데이터를 이동하는 방법을 자세히 설명합니다.

![Blob 스토리지 계정 관련 다이어그램](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Edge 스토리지 계정 추가
> * Edge 스토리지 계정 삭제


## <a name="add-an-edge-storage-account"></a>Edge 스토리지 계정 추가

Edge 스토리지 계정을 만들려면 다음 절차를 수행합니다.

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Edge 스토리지 계정 삭제

Edge 스토리지 계정을 삭제하려면 다음 단계를 수행합니다.

1. 리소스에서 **구성 > 스토리지 계정** 으로 이동합니다. 스토리지 계정 목록에서 삭제할 스토리지 계정을 선택합니다. 상단의 명령 모음에서 **스토리지 계정 삭제** 를 선택합니다.

    ![스토리지 계정 목록으로 이동](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. **스토리지 계정 삭제** 블레이드에서 삭제할 스토리지 계정을 확인하고 **삭제** 를 선택합니다.

    ![스토리지 계정 확인 및 삭제](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

스토리지 계정 목록이 삭제를 반영하도록 업데이트됩니다.


## <a name="add-delete-a-container"></a>컨테이너 추가, 삭제

관련 스토리지 계정의 컨테이너를 추가하거나 삭제할 수도 있습니다.

컨테이너를 추가하려면 다음 단계를 수행합니다.

1. 관리할 스토리지 계정을 선택합니다. 상단의 명령 모음에서 **+ 컨테이너 추가** 를 선택합니다.

    ![컨테이너를 추가할 스토리지 계정 선택](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. 컨테이너 이름을 입력합니다. 이 컨테이너는 이 계정에 매핑된 Azure 스토리지 계정뿐만 아니라 Edge 스토리지 계정에서도 만들어집니다. 

    ![Edge 컨테이너 추가](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

새로 추가된 컨테이너를 반영하도록 컨테이너 목록이 업데이트됩니다.

![업데이트된 컨테이너 목록](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

이제 이 목록에서 컨테이너를 선택하고 상단의 명령 모음에서 **+ 컨테이너 삭제** 를 선택할 수 있습니다. 

![컨테이너 삭제](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>스토리지 키 동기화

각 Azure Storage 계정에는 스토리지 계정에 액세스하는 경우 인증에 사용되는 두 개의 512비트 스토리지 액세스 키가 있습니다. Azure Stack Edge 디바이스가 클라우드 스토리지 서비스 공급자(이 경우 Azure)에 액세스할 때 이러한 두 키 중 하나를 제공해야 합니다.

Azure 관리자는 Azure Storage 서비스를 통해 스토리지 계정에 직접 액세스하여 액세스 키를 다시 생성하거나 변경할 수 있습니다. Azure Stack Edge 서비스와 디바이스에는 이 변경 내용이 자동으로 표시되지 않습니다.
 
Azure Stack Edge에 변경 사실을 알리려면 Azure Stack Edge 서비스에 액세스하고 스토리지 계정에 액세스한 다음, 액세스 키를 동기화해야 합니다. 그러면 서비스는 최신 키를 가져오고 해당 키를 암호화하여 디바이스에 암호화된 키를 보냅니다. 디바이스가 새 키를 가져오면 Azure Storage 계정으로 데이터를 계속 전송할 수 있습니다. 
 
디바이스에 새 키를 제공하려면 Azure Portal에 액세스하고 스토리지 액세스 키를 동기화합니다. 다음과 같은 단계를 수행합니다. 

1. 리소스에서 관리하려는 스토리지 계정을 선택합니다. 상단 명령 모음에서 **스토리지 키 동기화** 를 선택합니다.

    ![스토리지 키 동기화 선택](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

    ![스토리지 키 동기화 선택 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 사용자를 관리](azure-stack-edge-gpu-manage-users.md)하는 방법을 알아봅니다.
