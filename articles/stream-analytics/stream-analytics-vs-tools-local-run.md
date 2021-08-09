---
title: Visual Studio에서 로컬로 Azure Stream Analytics 쿼리 테스트
description: 이 문서에서는 Visual Studio용 Azure Stream Analytics 도구를 사용하여 쿼리를 로컬로 테스트하는 방법을 설명합니다.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: b856826761f355e896cae48aa4a6fb62f5947e0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019927"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Visual Studio를 사용하여 로컬로 Stream Analytics 쿼리 테스트

Visual Studio용 Azure Stream Analytics 도구를 사용하여 샘플 데이터 또는 [라이브 데이터](stream-analytics-live-data-local-testing.md)로 Stream Analytics 작업을 로컬로 테스트할 수 있습니다. 

이 [빠른 시작](stream-analytics-quick-create-vs.md)을 통해 Visual Studio를 사용하여 Stream Analytics 작업을 만드는 방법을 알아보세요.

## <a name="test-your-query"></a>쿼리 테스트

Azure Stream Analytics 프로젝트에서 **Script.asaql** 을 두 번 클릭하여 편집기에서 스크립트를 엽니다. 쿼리를 컴파일하여 구문 오류가 있는지 확인할 수 있습니다. 쿼리 편집기는 IntelliSense, 구문 색 지정 및 오류 마커를 지원합니다.

![쿼리 편집기](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>로컬 입력 추가

로컬 정적 데이터에 대해 쿼리 유효성을 검사하려면 입력을 마우스 오른쪽 단추로 클릭하고 **로컬 입력 추가** 를 선택합니다.
   
![로컬 입력 추가 메뉴 옵션을 강조 표시하는 스크린샷](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
팝업 창에서 로컬 경로의 샘플 데이터를 선택하고 **저장** 을 선택합니다.
   
![로컬 입력 추가](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
**local_EntryStream.json** 파일이 입력 폴더에 자동으로 추가됩니다.
   
![로컬 입력 폴더의 파일 목록](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
쿼리 편집기에서 **로컬로 실행** 을 선택합니다. 또는 F5 키를 눌러도 됩니다.
   
![로컬로 실행](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Visual Studio에서 바로 출력을 테이블 형식으로 볼 수 있습니다.

![테이블 형식의 출력](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

콘솔 출력에서 출력 경로를 찾을 수 있습니다. 아무 키를 눌러 결과 폴더를 엽니다.
   
![로컬 실행](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
로컬 폴더에서 결과를 확인합니다.
   
![로컬 폴더 결과](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>샘플 입력
입력 원본에서 로컬 파일로 샘플 입력 데이터를 수집할 수도 있습니다. 입력 구성 파일을 마우스 오른쪽 단추로 클릭하고 **샘플 데이터** 를 선택합니다. 

![데이터 샘플링](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Event Hubs 또는 IoT Hub에서만 데이터 스트리밍을 샘플링할 수 있습니다. 다른 입력 원본은 지원되지 않습니다. 팝업 대화 상자에서 로컬 경로를 입력하여 샘플 데이터를 저장하고 **샘플** 을 선택합니다.

![샘플 데이터 구성](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
**출력** 창에서 진행률을 볼 수 있습니다. 

![샘플 데이터 출력](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)
* [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트(미리 보기)](stream-analytics-live-data-local-testing.md)
* [Stream Analytics 도구를 사용하여 지속적으로 통합 및 개발](stream-analytics-tools-for-visual-studio-cicd.md)
