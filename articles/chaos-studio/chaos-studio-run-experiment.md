---
title: Azure 비정상 스튜디오에서 비정상 실험 실행 및 관리
description: Azure 비정상 스튜디오에서 비정상 실험을 시작, 중지, 세부 정보를 확인 하 고 기록 하는 방법에 대해 알아봅니다.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06abbe99467af52cb7867d31f31d4aa2d727d0ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053078"
---
# <a name="run-and-manage-an-experiment-in-azure-chaos-studio"></a>Azure의 비정상 스튜디오에서 실험 실행 및 관리

비정상 실험을 사용 하 여 제어 되는 환경에서 오류를 발생 시켜 응용 프로그램이 오류에 탄력적으로 대처할 수 있는지 확인할 수 있습니다. 이 문서에서는 이전에 만든 비정상 실험을 사용 하는 방법에 대 한 개요를 제공 합니다.

## <a name="start-an-experiment"></a>실험 시작

1. [Azure Portal](https://portal.azure.com)을 엽니다.

2. 검색 창에서 비정상 상태 **스튜디오 (미리 보기)** 를 검색 합니다.

3. **실험** 을 클릭 합니다. 실험 목록 보기는 대량 실험을 시작, 중지 또는 삭제 하거나 새 실험을 만들 수 있습니다.

    ![포털의 실험 목록](images/run-experiment-list.png)

4. 실험을 클릭 합니다. 실험 개요 페이지에서는 실험을 시작, 중지 및 편집 하 고, 리소스에 대 한 필수 세부 정보를 보고, 기록을 볼 수 있습니다. **시작** 단추를 클릭 한 다음 **확인** 을 클릭 하 여 실험을 시작 합니다.

    ![실험 시작](images/run-experiment-start.png)

5. 실험 상태에 *PreProcessingQueued*, *Waitingtostart* 및 finally *Running* 이 표시 됩니다.

## <a name="view-experiment-history-and-details"></a>실험 기록 및 세부 정보 보기

1. 실험을 실행 한 후에는 **기록** 에서 현재 실행에 대 한 **세부 정보** 를 클릭 하 여 자세한 상태와 오류를 확인 합니다.

    ![실행 기록](images/run-experiment-history.png)

2. 실험 세부 정보 보기에는 각 단계, 분기 및 오류의 실행 상태가 표시 됩니다. 오류를 클릭 합니다.

    ![실험 세부 정보](images/run-experiment-details.png)

3. 오류 세부 정보에는 실패 했거나 성공 하거나 원인을 포함 하 여 오류 실행에 대 한 추가 정보가 표시 됩니다. 실험을 실행 하는 동안 오류가 발생 하면 여기에 디버깅 정보가 표시 됩니다.

    ![오류 세부 정보](images/run-experiment-fault.png)

## <a name="edit-experiment"></a>실험 편집

1. 실험 개요로 돌아가서 **편집** 단추를 클릭 합니다.

    ![실험 편집](images/run-edit.png)

2. 실험을 만드는 데 사용한 것과 동일한 실험 디자이너입니다. 단계, 분기 및 오류를 추가 하거나 제거 하 고 오류 매개 변수 및 대상을 편집할 수 있습니다. 오류를 편집 하려면 **오류 옆에 있는 ...를** 클릭 합니다.

    ![오류 편집](images/run-edit-ellipses.png)

3. 편집이 완료 되 면 **저장** 을 클릭 합니다. 저장 하지 않고 변경 내용을 취소 하려면 오른쪽 위에 있는 **닫기 (X)** 단추를 클릭 합니다.
  ![실험 저장](images/run-edit-save.png)

> [!WARNING]
> 실험에 대상을 추가한 경우 실험 id의 대상 리소스에 대 한 역할 할당을 추가 해야 합니다.

## <a name="delete-experiment"></a>실험 삭제
1. 실험 목록으로 돌아가서 삭제 하려는 실험을 선택 합니다. 실험 목록 위의 도구 모음에서 **삭제** 를 클릭 합니다. 화면 해상도에 따라 삭제 옵션을 보려면 줄임표 (...)를 클릭 해야 할 수도 있습니다.

    ![실험 삭제](images/run-delete.png)

2. **예** 를 클릭 하 여 리소스 삭제를 확인 합니다.

3. 또는 실험을 열고 도구 모음에서 **삭제** 단추를 클릭할 수 있습니다.
