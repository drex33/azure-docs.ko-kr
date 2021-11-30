---
title: Azure 부하 테스트에서 Apache JMeter 로그 다운로드
titleSuffix: Azure Load Testing
description: Azure Portal에서 Azure 부하 테스트 로그를 다운로드 하 여 Apache JMeter 스크립트 문제를 해결 하는 방법에 대해 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: 2faeaee94b800284c9df6f0e6158820f906faf01
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305622"
---
# <a name="troubleshoot-jmeter-problems-by-downloading-azure-load-testing-preview-logs"></a>Azure 부하 테스트 미리 보기 로그를 다운로드 하 여 JMeter 문제 해결

Azure Portal에서 Azure 부하 테스트 미리 보기 로그를 다운로드 하 여 Apache JMeter 스크립트 문제를 해결 하는 방법에 대해 알아봅니다.

부하 테스트를 실행 하는 경우 Azure 부하 테스트 테스트 엔진은 Apache JMeter 테스트 스크립트를 실행 합니다. Apache JMeter 로그는 테스트 실행 중에 발생 하는 문제 뿐만 아니라 JMX 파일에서 발생 하는 문제를 식별 하는 데 도움이 될 수 있습니다. 예를 들어 응용 프로그램 엔드포인트를 사용할 수 없거나 JMX 파일에 잘못 된 자격 증명이 포함 되어 있을 수 있습니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 계정을 만듭니다.  
- 완료 된 테스트 실행을 포함 하는 Azure 부하 테스트 리소스가 필요 합니다. Azure 부하 테스트 리소스를 만들어야 하는 경우 [부하 테스트 만들기 및 실행](./quickstart-create-and-run-load-test.md)을 참조 하세요.  

## <a name="access-and-download-logs-for-your-load-test"></a>부하 테스트에 대 한 로그 액세스 및 다운로드  

이 섹션에서는 Azure Portal에서 Azure 부하 테스트 로그를 검색 하 고 다운로드 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure 부하 테스트 리소스로 이동 합니다.

1. 테스트 **를 선택 하** 여 테스트 목록을 확인 한 다음 테스트를 선택 합니다.

    :::image type="content" source="media/how-to-find-download-logs/test-list.png" alt-text="Azure 부하 테스트 리소스에 대 한 테스트 목록을 보여 주는 스크린샷":::  

   >[!TIP]
   > 검색 상자와 **시간 범위** 필터를 사용 하 여 테스트 수를 제한할 수 있습니다.

1. 테스트 실행 세부 정보 페이지를 보려면 목록에서 테스트 실행을 선택 합니다.

    :::image type="content" source="media/how-to-find-download-logs/test-run.png" alt-text="부하 테스트에 대 한 테스트 실행을 선택 하는 방법을 보여 주는 스크린샷":::  

1. 대시보드에서 **다운로드**, **로그** 를 차례로 선택 합니다.  

    :::image type="content" source="media/how-to-find-download-logs/logs.png" alt-text="테스트 결과 페이지에서 부하 테스트 로그를 다운로드 하는 방법을 보여 주는 스크린샷":::  

    이제 브라우저가 실행 로그를 압축 된 폴더로 다운로드 하기 시작 합니다.

1. 모든 zip 도구를 사용 하 여 폴더의 압축을 풀고 로깅 정보에 액세스할 수 있습니다.

    :::image type="content" source="media/how-to-find-download-logs/jmeter-log.png" alt-text="JMeter 로그 파일 콘텐츠를 보여 주는 스크린샷":::  

## <a name="next-steps"></a>다음 단계

- 테스트 결과를 비교 하는 방법에 대 한 자세한 내용은 [여러 테스트 결과 비교](./how-to-compare-multiple-test-runs.md)를 참조 하세요.

- 성능 테스트 자동화에 대 한 자세한 내용은 [자동화 된 성능 테스트 구성](./tutorial-cicd-azure-pipelines.md) 을 참조 하세요.
