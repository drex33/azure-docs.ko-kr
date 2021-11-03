---
title: Azure 비정상 스튜디오를 사용 하 여 비정상 실험 만들기 및 실행
description: 10 분 내에 비정상 스튜디오 실험을 만들고 실행 하는 단계를 이해 합니다.
services: chaos-studio
author: prashabora
ms.topic: article
ms.date: 10/21/2021
ms.author: prashabora
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: a5635307b3b736f535dd21f54b3c2b1ec326e5b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102762"
---
# <a name="quickstart-create-and-run-a-chaos-experiment-using-azure-chaos-studio"></a>빠른 시작: Azure 비정상 스튜디오를 사용 하 여 비정상 실험 만들기 및 실행 
VM 종료 서비스-direct 실험을 사용 하 여 비정상 스튜디오를 시작 하 여 실제 서비스에서 해당 오류에 대 한 복원 력을 향상 하세요. 

## <a name="prerequisites"></a>사전 요구 사항
- [Azure Portal](https://portal.azure.com)을 엽니다.
- 가상 머신 - 가상 컴퓨터가 없는 경우 [다음 단계를 수행 하](../virtual-machines/linux/quick-create-portal.md)여 가상 컴퓨터를 만들 수 있습니다.

## <a name="enable-chaos-studio-on-the-virtual-machine-you-created"></a>만든 가상 머신에서 비정상 스튜디오 사용
1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. 검색 창에서 비정상 상태 **스튜디오 (미리 보기)** 를 검색 합니다.
3. **대상** 을 클릭 하 고 만든 VM으로 이동 합니다.

4. 만든 VM 옆의 확인란을 선택 하 고 **대상 사용** 을 클릭 한 다음 드롭다운 메뉴에서 **서비스 직접 대상 사용** 을 클릭 합니다.

   ![Azure Portal의 대상 뷰](images/quickstart-virtual-machine-enabled.png)

5. 선택한 리소스가 성공적으로 사용 하도록 설정 되었음을 나타내는 알림이 표시 됩니다.
   
   ![대상을 보여 주는 알림 설정 성공](images/tutorial-service-direct-targets-enable-confirm.png)

## <a name="create-an-experiment"></a>실험 만들기

1. **실험** 을 클릭 합니다.                
   ![실험으로 이동](images/quickstart-left-experiment.png)

2. **실험 추가** 를 클릭 합니다.

   ![Azure Portal에서 실험 추가](images/add-an-experiment.png)

3. 비정상 실험을 배포 하려는 **구독**, **리소스 그룹** 및 **위치** 를 입력 합니다. 실험에 **이름을** 지정 합니다. 다음을 클릭 합니다 **. 실험 디자이너 >**

   ![실험 기본 사항 추가](images/quickstart-service-direct-add-basics.png)

4. 이제 비정상 스튜디오 실험 디자이너에 있습니다. **단계** 및 **분기** 에 친숙 한 이름을 지정 하 고 **오류 추가** 를 클릭 합니다.

   ![실험 디자이너](images/quickstart-service-direct-add-designer.png)

5. 드롭다운에서 **VM 종료** 를 선택 하 고 마지막으로 실패 한 시간 (분)으로 **기간** 을 입력 합니다. 

   ![오류 속성](images/quickstart-service-direct-add-fault.png)

6. **다음: 대상 리소스 >** 를 클릭 합니다.
   ![대상 추가](images/quickstart-service-direct-add-targets.png)

7. **추가** 를 클릭합니다.

   ![Addt](images/quickstart-add-target.png)

8. 실험이 정확한 지 확인 하 고 **검토 + 만들기**, **만들기** 를 차례로 클릭 합니다.

   ![실험 만들기](images/quickstart-review-and-create.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>가상 컴퓨터에 대 한 실험 권한 제공
1. 가상 머신으로 이동 하 고 **Access control (IAM)을 클릭 합니다.** 
    ![ 역할 할당 추가](images/quickstart-access-control.png)
2. **추가** 를 클릭합니다.

   ![추가 단추](images/add.png)

3. **역할 할당 추가** 클릭

   ![역할 할당 추가 단추](images/add-role-assignment.png)

4. **가상 컴퓨터 참가자** 를 검색 하 고 역할을 선택 합니다. **다음** 을 클릭합니다.

   ![VM에 대 한 역할을 선택 합니다.](images/quickstart-virtual-machine-contributor.png)
5. **멤버 선택** 을 클릭 하 고 실험 이름을 검색 합니다. 실험을 선택 하 고 **선택** 을 클릭 합니다. 
   ![실험 선택](images/quickstart-select-experiment-role-assignment.png)
 
6. **검토 + 할당** , **검토 + 할당** 을 차례로 클릭 합니다.



## <a name="run-the-chaos-experiment"></a>비정상 실험 실행

1. Azure Portal 열기:
    * 계정을 사용 하는 경우 @microsoft.com [이 링크를 클릭](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true)합니다.
    * 외부 계정을 사용 하는 경우 [이 링크를 클릭](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}})합니다.
2. 실험 이름 옆의 확인란을 선택 하 고 **실험 시작** 을 클릭 합니다.
    ![실험 시작](images/quickstart-experiment-start.png)

3. **예** 를 클릭 하 여 비정상 실험을 시작 하 시겠습니까를 확인 합니다.

    ![실험을 시작할 것인지 확인 합니다.](images/start-experiment-confirmation.png)
4. 필드 실험의 실행 상태에 대 한 자세한 보기를 보려면 실험 이름을 클릭 합니다.


## <a name="clean-up-resources"></a>리소스 정리

1. 실험 이름 옆의 확인란을 선택 하 고 **삭제** 를 클릭 합니다.

   ![삭제할 실험 선택](images/quickstart-delete-experiment.png)

2. **예** 를 클릭 하 여 실험을 삭제할 것임을 확인 합니다.

3. Azure Portal 검색 창에서 만든 VM을 검색 합니다.

   ![VM 선택](images/quickstart-cleanup.png)

4. 리소스에 대 한 요금이 부과 되지 않도록 하려면 **삭제** 를 클릭 합니다.

   ![VM 삭제](images/quickstart-cleanup-virtual-machine.png)


## <a name="next-steps"></a>다음 단계
이제 VM 종료 서비스 직접 실험을 실행 했으므로 다음을 수행할 준비가 되었습니다.
- [에이전트 기반 오류를 사용 하는 실험 만들기](chaos-studio-tutorial-agent-based.md)
