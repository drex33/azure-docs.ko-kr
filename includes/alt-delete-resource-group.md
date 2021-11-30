---
title: 포함 파일
description: 포함 파일
services: load-testing
ms.service: load-testing
ms.custom: include file
ms.topic: include
author: j-martens
ms.author: jmartens
ms.date: 9/04/2021
ms.openlocfilehash: 9bc19d2a1edcf83959a26e1b606abfa8dee2fb9d
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133304128"
---
>[!IMPORTANT]
>다른 Azure 부하 테스트 자습서 및 방법 문서에 대 한 필수 구성 요소로 만든 리소스를 사용할 수 있습니다. 

사용자가 만든 리소스를 사용 하지 않으려는 경우에는 추가 요금이 발생 하지 않도록 해당 리소스를 삭제 합니다.

* Azure Portal에서 다음을 수행합니다.
    1. 왼쪽 위 모서리에서 메뉴 단추를 선택 하 고 **리소스 그룹** 을 선택 합니다.
 
    1. 목록에서 만든 리소스 그룹을 선택합니다.

    1. **리소스 그룹 삭제** 를 선택합니다.

       ![Azure Portal에서 리소스 그룹을 삭제하기 위해 선택한 항목의 스크린샷](./media/alt-delete-resource-group/delete-resources.png)

    1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

* 또는 Azure CLI를 사용할 수 있습니다.
   
   ```azurecli
   az group delete --name <yourresourcegroup>
   ```
   리소스 그룹을 삭제 하면 그 안에 포함 된 모든 리소스가 삭제 됩니다. 
