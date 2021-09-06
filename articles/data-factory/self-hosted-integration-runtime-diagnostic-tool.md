---
title: 자체 호스팅 통합 런타임 진단 도구
description: 자체 호스팅 통합 런타임용 진단 도구
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
- devx-track-azurepowershell
ms.date: 07/28/2021
ms.openlocfilehash: b8aa070759ea29c9c2853d0c8a6af184bc169569
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536609"
---
# <a name="diagnostic-tool-for-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임용 진단 도구
자체 호스팅 통합 런타임은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 컴퓨팅 인프라입니다. 자체 호스팅 통합 런타임을 설치하려면 온-프레미스 머신이나 개인 네트워크 내부의 가상 머신이 필요합니다. 네트워크, 방화벽, 종속성 또는 OS 관련 문제와 같은 온-프레미스 머신의 문제를 조사하는 것이 어려운 경우도 있습니다. 이 문서에서는 온-프레미스 환경에서 발생하는 문제를 해결하는 새로운 진단 도구에 대해 설명합니다.

이 도구는 자체 호스팅 통합 런타임 머신에서 일련의 테스트 시나리오를 실행합니다. 모든 시나리오에는 일반적인 문제에 대한 일반적인 상태 검사 사례가 있습니다. 문제가 발생하는 경우 고객은 "문제 해결" 기능을 트리거할 수 있습니다. 이 도구는 고객 환경 정보를 수집하고 상태 검사 사례를 실행합니다. 

## <a name="get-started"></a>시작 
진단 도구를 실행하여 가능한 문제를 진단하는 방법에는 두 가지가 있습니다.

- 온-프레미스 머신에 자체 호스팅 통합 런타임을 설치하는 경우 Configuration Manager에서 문제 해결 기능에 액세스할 수 있습니다. 문제가 발생하는 경우 **문제 해결** 을 선택하여 진단 도구를 실행합니다. 또한 설치 후에 **진단** 탭에서 동일한 옵션을 선택할 수도 있습니다.

   :::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/troubleshoot-ui.png" alt-text="Runtime Configuration Manager를 사용하여 문제를 해결하는 방법을 보여 주는 스크린샷":::
   
- 또한 명령줄에서 진단 도구를 시작할 수도 있습니다.

   ```console
   dmgcmd.exe -ts [AUTH_KEY]
   ```

## <a name="diagnostic-result"></a>진단 결과
실행 결과 및 세부 정보 로그 메시지는 HTML 보고서로 생성됩니다. 오류를 검토하고 보고서에서 제안된 완화 방법 또는 공용 문서 URL을 가져올 수 있습니다.

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-process.png" alt-text="진단 프로세스를 보여 주는 스크린샷":::

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-report.png" alt-text="진단 결과 보고서를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory의 통합 런타임 개념](./concepts-integration-runtime.md)을 검토합니다.

- [Azure Portal에서 자체 호스팅 통합 런타임을 만드는](./create-self-hosted-integration-runtime.md) 방법을 알아봅니다.
