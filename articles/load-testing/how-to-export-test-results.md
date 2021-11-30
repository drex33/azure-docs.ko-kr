---
title: 보고를 위한 부하 테스트 결과 내보내기
titleSuffix: Azure Load Testing
description: 타사 도구에서 사용하기 위해 Azure Load Testing에서 부하 테스트 결과를 내보내는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: cb19826892b9e56add47bde124a828b2eedeac52
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305609"
---
# <a name="export-test-results-in-azure-load-testing-preview-for-use-in-third-party-tools"></a>타사 도구에서 사용하기 위해 Azure Load Testing 미리 보기에서 테스트 결과 내보내기

이 문서에서는 Azure Portal Azure Load Testing 미리 보기에서 테스트 결과를 다운로드하는 방법을 알아봅니다. 타사 도구에서 보고하는 데 이러한 결과를 사용할 수 있습니다.

테스트 결과에는 각 애플리케이션 요청에 대한 세부 정보가 포함된 CSV(쉼표로 구분된) 파일이 포함됩니다. 또한 Apache JMeter 대시보드를 로컬로 실행하기 위한 모든 파일이 포함됩니다.

:::image type="content" source="media/how-to-export-test-results/apache-jmeter-dashboard.png" alt-text="다운로드한 Apache JMeter 대시보드를 보여 주는 스크린샷.":::

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 계정을 만듭니다.  
- 완료된 테스트 실행이 있는 Azure Load Testing 리소스입니다. Azure 부하 테스트 리소스를 만들어야 하는 경우 [부하 테스트 만들기 및 실행을](./quickstart-create-and-run-load-test.md)참조하세요.  

## <a name="access-and-download-load-test-results"></a>부하 테스트 결과 액세스 및 다운로드

이 섹션에서는 Azure Portal Azure Load Testing 결과 파일을 검색하고 다운로드합니다.

1. [Azure Portal](https://portal.azure.com)Azure Load Testing 리소스로 이동합니다.

1. **테스트를** 선택하여 테스트 목록을 본 다음, 테스트를 선택합니다.

    :::image type="content" source="media/how-to-export-test-results/test-list.png" alt-text="Azure 부하 테스트 리소스에 대한 테스트 목록을 보여 주는 스크린샷.":::  

   >[!TIP]
   > 검색 상자 및 시간 **범위** 필터를 사용하여 테스트 수를 제한할 수 있습니다.

1. 테스트 실행에서 **...를** 선택한 다음, **결과 파일 다운로드를** 선택합니다.

    :::image type="content" source="media/how-to-export-test-results/test-run-page-download.png" alt-text="부하 테스트 실행의 로그를 다운로드하는 방법을 보여 주는 스크린샷":::  
    > [!NOTE]
    > 테스트 실행은 결과 파일을 다운로드할 수 있도록 **완료,** **중지 또는** **실패** 상태여야 합니다.

    이제 브라우저에서 테스트 결과를 압축된 폴더로 다운로드하기 시작합니다.

1. 또는 테스트 실행 세부 정보 페이지에서 테스트 결과를 다운로드할 수 있습니다. **다운로드를** 선택한 다음, **결과** 를 선택합니다.
    :::image type="content" source="media/how-to-export-test-results/dashboard-download.png" alt-text="테스트 실행 세부 정보 페이지에서 테스트 결과를 다운로드하는 방법을 보여 주는 스크린샷":::

1. zip 도구를 사용하여 폴더의 압축을 풀고 테스트 결과에 액세스할 수 있습니다.

    :::image type="content" source="media/how-to-export-test-results/test-results-zip.png" alt-text="테스트 결과 zip 파일 콘텐츠를 보여 주는 스크린샷.":::  

    *testreport.csv* 파일에는 부하 테스트 중에 테스트 엔진이 실행한 개별 요청이 포함되어 있습니다. Zip 파일에도 포함된 Apache JMeter 대시보드는 그래프에 이 파일을 사용합니다.

## <a name="next-steps"></a>다음 단계

- 테스트 결과를 비교하는 방법에 대한 자세한 내용은 [여러 테스트 결과 비교를 참조하세요.](./how-to-compare-multiple-test-runs.md)

- 성능 테스트 자동화에 대한 자세한 내용은 [자동화된 성능 테스트 구성을](./tutorial-cicd-azure-pipelines.md) 참조하세요.
