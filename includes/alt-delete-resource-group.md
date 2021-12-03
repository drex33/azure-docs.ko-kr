---
title: 포함 파일
description: 포함 파일
services: load-testing
ms.service: load-testing
ms.custom: include file
ms.topic: include
author: ntrogh
ms.author: nicktrog
ms.date: 11/30/2021
ms.openlocfilehash: 78457f88b3b1781fe9b80cb565822591a0270f70
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133510522"
---
>[!IMPORTANT]
>다른 Azure 부하 테스트 자습서 및 방법 문서에 대해 만든 Azure Load Testing 리소스를 다시 사용할 수 있습니다. 

만든 리소스를 사용할 계획이 없는 경우 추가 요금이 발생하지 않도록 해당 리소스를 삭제합니다. 다른 리소스 그룹에 샘플 애플리케이션을 배포한 경우 다음 단계를 반복할 수 있습니다.

* Azure Portal에서 다음을 수행합니다.
    1. 왼쪽 위 모서리에서 메뉴 단추를 선택한 다음, **리소스 그룹** 을 선택합니다.
 
    1. 목록에서 만든 리소스 그룹을 선택합니다.

    1. **리소스 그룹 삭제** 를 선택합니다.

       ![Azure Portal에서 리소스 그룹을 삭제하기 위해 선택한 항목의 스크린샷](./media/alt-delete-resource-group/delete-resources.png)

    1. 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제** 를 선택합니다.

* 또는 Azure CLI 사용할 수 있습니다.
   
   ```azurecli
   az group delete --name <yourresourcegroup>
   ```
   리소스 그룹을 삭제하면 리소스 그룹 내의 모든 리소스가 삭제됩니다. 
