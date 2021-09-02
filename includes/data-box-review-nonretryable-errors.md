---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/07/2021
ms.author: alkohli
ms.openlocfilehash: c8a749a1b0e14859b0eb2c81b4bfeafd1dba0913
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867960"
---
다시 시도할 수 없는 오류를 검토하고 주문을 계속하려면 다음을 수행합니다.

1. Azure Portal에서 주문을 엽니다.  

   다시 시도할 수 없는 오류가 파일 업로드를 방해하고 있는 경우 다음 알림이 표시됩니다. 현재 주문 상태는 **데이터 복사가 완료되었으나 오류가 발생했습니다. 디바이스의 데이터 삭제가 보류되었습니다.** 입니다.

   ![Azure Portal에서 업로드하는 동안 복사 오류 알림](media/data-box-review-nonretryable-errors/copy-errors-in-upload-01.png)

   **데이터 복사 세부 정보** 의 **로그 경로 복사** 를 기록해 둡니다. 데이터 복사 로그의 오류를 검토합니다.

   > [!NOTE]
   > [!INCLUDE [data-box-copy-logs-behind-firewall](data-box-copy-logs-behind-firewall.md)]

2. **디바이스 지우기 확인** 을 선택하여 검토 패널을 엽니다.

   ![Azure Portal의 업로드 오류에 대한 패널 검토 및 진행](media/data-box-review-nonretryable-errors/copy-errors-in-upload-02.png)

3. 이전에 기록해 둔 로그 복사 경로를 사용하여 데이터 복사 로그의 오류를 검토합니다. 필요한 경우 **닫기** 를 선택하여 경로를 다시 표시할 수 있습니다. 

   네트워크 전송 또는 새 가져오기 순서를 통해 다른 업로드를 시도하기 전에 구성 문제를 수정해야 합니다. <!--For guidance, see [Review copy errors in uploads from Azure Data Box and Azure Data Box Heavy devices](../articles/databox/data-box-troubleshoot-data-upload.md). - To make the Include, I needed to move this reference out of the main procedure.-->

4. 오류를 검토한 후 데이터 지우기를 계속할 준비가 되었음을 나타내려면 확인란을 선택합니다. 그런 다음 **계속** 을 선택합니다.

   ![데이터 지우기를 진행할 준비가 되었는지 확인합니다.](media/data-box-review-nonretryable-errors/copy-errors-in-upload-03.png)

   디바이스에서 데이터가 안전하게 지워지면 주문 상태가 **복사가 완료되었지만 오류 발생** 상태로 업데이트됩니다.

   ![완료되었지만 오류가 발생한 Data Box의 가져오기 순서 상태 표시](media/data-box-review-nonretryable-errors/copy-errors-in-upload-04.png)

   조치를 취하지 않으면 14일 후에 주문이 자동으로 완료됩니다.

