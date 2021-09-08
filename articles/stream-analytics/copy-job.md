---
title: 지역 간에 Azure Stream Analytics 작업 복사, 백업 및 이동
description: 이 문서에서는 Azure Stream Analytics 작업을 이동, 복사 및 백업하는 방법을 설명 합니다.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/11/2019
ms.openlocfilehash: 7b3d203d3d34612b3495fa3eceaf13e357eaa2be
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829409"
---
# <a name="copy-back-up-and-move-your-azure-stream-analytics-jobs-between-regions"></a>지역 간에 Azure Stream Analytics 작업 복사, 백업 및 이동

Visual Studio Code 또는 Visual Studio를 사용하여 배포된 Azure Stream Analytics 작업을 복사하거나 백업할 수 있습니다. 작업을 다른 지역으로 복사해도 마지막 출력 시간이 복사되지 않습니다. 따라서 복사된 작업을 시작할 때 [**마지막으로 중지된 시간**](./start-job.md#start-options) 옵션을 사용할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에
* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [Azure Portal](https://portal.azure.com/)에 로그인합니다.

* [Visual Studio Code용 Azure Stream Analytics 확장](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) 또는 [Visual Studio용 Azure Stream Analytics 도구](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension)를 설치합니다.  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Visual Studio Code 활동 표시줄에서 **Azure** 아이콘을 클릭한 다음, **Stream Analytics 노드** 를 확장합니다. 작업은 구독 아래에 표시되어야 합니다.

   ![Stream Analytics Explorer 열기](./media/vscode-explore-jobs/open-explorer.png)

2. 작업을 로컬 프로젝트로 내보내려면 Visual Studio Code의 **Stream Analytics Explorer** 에서 내보낼 작업을 찾습니다. 그런 다음, 프로젝트에 대한 폴더를 선택합니다.

    ![Visual Studio Code에서 ASA 작업 찾기](./media/vscode-explore-jobs/export-job.png)

    사용자가 선택하고 현재 작업 영역에 추가된 폴더로 프로젝트를 내보냅니다.

3. 다른 이름을 사용하여 작업을 다른 지역 또는 백업에 게시하려면 쿼리 편집기(\*.asaql)에서 **게시할 구독에서 선택** 을 선택하고 지침을 따릅니다.

    ![Visual Studio Code에서 Azure에 게시](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. [배포된 Azure Stream Analytics 작업을 프로젝트에 내보내기 지침](./stream-analytics-vs-tools.md#export-jobs-to-a-project)을 따릅니다.

2. 쿼리 편집기에서 \*.asaql 파일을 열고 스크립트 편집기에서 **Azure에 제출** 을 선택한 다음, 지침에 따라 새 이름을 사용하여 작업을 다른 지역 또는 백업에 게시합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio Code를 사용하여 Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
* [빠른 시작: Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)