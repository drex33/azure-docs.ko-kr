---
title: Microsoft Defender for Cloud의 통합 문서 갤러리
description: 통합 Azure Monitor 통합 문서 갤러리를 사용 하 여 클라우드 데이터에 대 한 다양 한 대화형 보고서를 만드는 방법을 알아봅니다.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 07c253efdb5f029d6df5e508cd690d2b47d40934
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056003"
---
# <a name="create-rich-interactive-reports-of-defender-for-cloud-data"></a>클라우드 데이터를 위해 Defender의 풍부한 대화형 보고서 만들기

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Monitor 통합 문서](../azure-monitor/visualize/workbooks-overview.md)는 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공합니다. 이를 통해 Azure에서 여러 데이터 원본을 탭하여 통합된 대화형 환경으로 결합할 수 있습니다.

통합 문서는 Azure 데이터 시각화를 위한 풍부한 기능 집합을 제공합니다. 각 시각화 형식에 대한 자세한 예제는 [시각화 예제 및 설명서](../azure-monitor/visualize/workbooks-text-visualizations.md)를 참조하세요. 

클라우드 용 Microsoft Defender 내에서 기본 제공 통합 문서에 액세스 하 여 조직의 보안 상태를 추적할 수 있습니다. 또한 사용자 지정 통합 문서를 빌드하여 Defender의 클라우드 또는 다른 지원 되는 데이터 원본에 대 한 광범위 한 데이터를 볼 수 있습니다.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="시간 경과에 따른 보안 점수 통합 문서.":::

## <a name="availability"></a>가용성

| 양상                          | 세부 정보                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:                  | GA(일반 공급)                                                                                                                    |
| 가격 책정:                        | Free                                                                                                                                         |
| 필요한 역할 및 권한: | 통합 문서를 저장하려면 대상 리소스 그룹에 대해 최소 [통합 문서 기여자](../role-based-access-control/built-in-roles.md#workbook-contributor) 권한이 있어야 합니다. |
| 클라우드:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud의 통합 문서 갤러리

통합 된 Azure 통합 문서 기능을 사용 하면 클라우드 용 Microsoft Defender를 사용 하 여 고유한 사용자 지정 대화형 통합 문서를 간편 하 게 빌드할 수 있습니다. 또한 Defender for Cloud에는 사용자 지정을 위해 다음 통합 문서를 준비 하는 갤러리가 포함 되어 있습니다.

- [‘시간 경과에 따른 보안 점수’ 통합 문서](#use-the-secure-score-over-time-workbook) - 구독의 점수와 리소스에 대한 권장 사항의 변경 내용 추적
- [‘시스템 업데이트’ 통합 문서](#use-the-system-updates-workbook) - 리소스, OS, 심각도 등에 따라 누락된 시스템 업데이트 보기
- [‘취약성 평가 결과’ 통합 문서](#use-the-vulnerability-assessment-findings-workbook) - Azure 리소스의 취약성 검사 결과 보기
- [‘시간에 따른 규정 준수’ 통합 문서](#use-the-compliance-over-time-workbook) - 선택한 규정 또는 산업 표준을 준수하는 구독의 상태 보기 

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Microsoft Defender for Cloud의 기본 제공 통합 문서 갤러리입니다.":::

제공된 통합 문서 중 하나를 선택하거나 직접 만듭니다.

> [!TIP]
> **편집** 단추를 사용하여 제공된 통합 문서 중에서 하나를 선택하여 원하는 대로 사용자 지정할 수 있습니다. 편집이 완료되면 **저장** 을 선택하면 변경 내용이 새 통합 문서에 저장됩니다.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="제공된 통합 문서를 편집하여 특정 요구에 맞게 사용자 지정":::

### <a name="use-the-secure-score-over-time-workbook"></a>‘시간 경과에 따른 보안 점수’ 통합 문서 사용

이 통합 문서는 Log Analytics 작업 영역의 보안 점수 데이터를 사용합니다. [Azure Portal의 클라우드 페이지에 대해 Defender에서 연속 내보내기 구성](continuous-export.md?tabs=azure-portal)에 설명 된 대로 연속 내보내기 도구에서 해당 데이터를 내보내야 합니다.

연속 내보내기를 설정할 때 **스트리밍 업데이트** 및 **스냅샷** 으로 내보내기 빈도를 설정합니다.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="시간에 따른 보안 점수 통합 문서에 대해 연속 내보내기 구성의 내보내기 빈도 설정에서 두 옵션을 모두 선택해야 합니다.":::

> [!NOTE]
> 스냅샷은 매주 내보낼 수 있으므로 이 통합 문서의 데이터를 보려면 먼저 내보낼 첫 번째 스냅샷을 한 주 이상 기다려야 합니다.

> [!TIP]
> 조직 전체에서 연속 내보내기를 구성하려면 [대규모 연속 내보내기 구성](continuous-export.md?tabs=azure-policy)에서 설명하는 제공된 Azure Policy 'DeployIfNotExist' 정책을 사용합니다.

시간에 따른 보안 점수 통합 문서에는 선택한 작업 영역에 보고하는 구독에 대한 5개의 그래프가 있습니다.

|그래프  |예제  |
|---------|---------|
|**지난 주 및 월의 점수 추세**<br>이 섹션을 사용하여 구독의 점수에 대한 현재 점수와 일반적인 추세를 모니터링합니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="기본 제공 통합 문서의 보안 점수 추세":::|
|**선택한 모든 구독에 대한 집계 점수**<br>선택한 시간 범위에서 임의의 날짜에 집계된 점수를 확인하려면 추세 선에서 임의의 지점 위로 마우스를 가져갑니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="선택한 모든 구독에 대한 집계 점수":::|
|**비정상 리소스의 권장 사항**<br>이 표에서는 선택한 기간 동안 비정상으로 변경된 대부분의 리소스를 가진 권장 사항을 심사하는 데 도움이 됩니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="비정상 리소스의 권장 사항":::|
|**특정 보안 컨트롤의 점수**<br>클라우드의 보안 제어를 위한 Defender는 권장 사항의 논리적 그룹화입니다. 이 차트에서는 모든 컨트롤의 주간 점수를 한눈에 보여 줍니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="선택한 기간 동안 보안 컨트롤의 점수":::|
|**리소스 변경**<br>선택한 기간 동안 변경된 상태(정상, 비정상 또는 적용되지 않음)가 있는 대부분의 리소스에 대한 권장 사항이 여기에 나열됩니다. 목록에서 권장 사항을 선택하면 특정 리소스를 나열하는 새 테이블이 열립니다.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="상태가 변경된 대부분의 리소스에 대한 권장 사항":::|
|||

### <a name="use-the-system-updates-workbook"></a>‘시스템 업데이트’ 통합 문서 사용

이 통합 문서는 “시스템 업데이트를 머신에 설치해야 합니다.”라는 보안 권장 사항을 기반으로 합니다.

이 통합 문서는 미해결 업데이트가 있는 머신을 식별하는 데 도움이 됩니다.

다음에 따라 선택한 구독에 대한 상황을 볼 수 있습니다.

- 미해결 업데이트가 포함된 리소스 목록
- 리소스에서 누락된 업데이트 목록

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="누락 된 업데이트 보안 권장 사항을 기반으로 하는 클라우드 시스템 업데이트 통합 문서에 대 한 Defender":::

### <a name="use-the-vulnerability-assessment-findings-workbook"></a>‘취약성 평가 결과’ 통합 문서 사용

클라우드에 대 한 Defender에는 컴퓨터에 대 한 취약성 스캐너, 컨테이너 레지스트리의 컨테이너 및 SQL 서버가 포함 되어 있습니다.

이러한 스캐너 사용에 대해 자세히 알아봅니다.

- [Microsoft 위협 및 취약성 관리에서 취약점 찾기](deploy-vulnerability-assessment-tvm.md)
- [통합 Qualys 스캐너를 통해 취약성 찾기](deploy-vulnerability-assessment-vm.md)
- [취약성에 대한 레지스트리 이미지 스캔](defender-for-container-registries-usage.md)
- [SQL 리소스에서 취약성 검색](defender-for-sql-on-machines-vulnerability-assessment.md)

각 리소스 종류에 대 한 결과는 별도의 권장 사항에서 보고 됩니다.

- [가상 컴퓨터의 취약성](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1195afff-c881-495e-9bc5-1486211ae03f) (Microsoft 위협 및 취약성 관리, integrated Qualys 스캐너 및 구성 된 [byol VA 솔루션](deploy-vulnerability-assessment-byol-vm.md)의 결과 포함)을 재구성 해야 합니다.
- [Azure Container Registry 이미지의 취약성을 수정해야 함(Qualys 제공)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/dbd0cb49-b563-45e7-9724-889e799fa648)
- [SQL 데이터베이스가 발견한 취약성을 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/82e20e14-edc5-4373-bfc4-f13121257c37)
- [컴퓨터의 SQL Server는 발견한 취약성을 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/f97aa83c-9b63-4f9a-99f6-b22c4398f936)

이 통합 문서는 이러한 결과를 수집하고 심각도, 리소스 종류, 범주별로 구성합니다.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Defender for Cloud의 취약성 평가 결과 보고서":::


### <a name="use-the-compliance-over-time-workbook"></a>‘시간에 따른 규정 준수’ 통합 문서 사용

클라우드용 Microsoft Defender는 리소스 구성을 업계 표준, 규정 및 벤치마크의 요구 사항과 지속적으로 비교합니다. 기본 제공 표준에는 NIST SP 800-53, SWIFT CSP CSCF v2020, 캐나다 연방 PBMM, HIPAA HITRUST 등이 포함됩니다. 규정 준수 대시보드를 사용하여 조직과 관련된 특정 표준을 선택할 수 있습니다. [규정 준수 대시보드의 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)에서 자세히 알아보세요.

이 통합 문서는 대시보드에 추가 된 다양 한 표준으로 시간에 따른 준수 상태를 추적 합니다.

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-select-standards.png" alt-text="시간에 따른 규정 준수 보고서를 위한 표준을 선택합니다.":::

보고서의 개요 영역에서 표준을 선택하면 아래쪽 창에 좀 더 자세한 분석이 표시됩니다.

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="특정 표준과 관련된 변경 내용에 대한 자세한 분석":::

권장 사항 수준까지 드릴다운을 계속하여 각 컨트롤을 통과하거나 실패한 리소스를 볼 수 있습니다. 

> [!TIP]
> 보고서의 각 패널에 대해 “Excel로 내보내기” 옵션을 사용하여 데이터를 Excel 내보낼 수 있습니다.
>
> :::image type="content" source="media/custom-dashboards-azure-workbooks/export-workbook-data.png" alt-text="규정 준수 통합 문서 데이터를 Excel로 내보내기":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>다른 통합 문서 갤러리에서 통합 문서 가져오기

다른 Azure 서비스에서 통합 문서를 빌드하고 클라우드 통합 문서 갤러리로 Microsoft Defender로 이동 하려는 경우:

1. 대상 통합 문서를 엽니다.

1. 도구 모음에서 **편집** 을 선택합니다.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Azure Monitor 통합 문서 편집":::

1. 도구 모음에서 **</>** 를 선택하여 고급 편집기를 입력합니다.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="고급 편집기를 시작하여 갤러리 템플릿 JSON 코드 가져오기":::

1. 통합 문서의 갤러리 템플릿 JSON을 복사합니다.

1. Defender for Cloud에서 통합 문서 갤러리를 열고 메뉴 모음에서 **새로** 를 선택합니다.
1. **</>** 를 선택하여 고급 편집기를 입력합니다.
1. 전체 갤러리 템플릿 JSON에 붙여 넣습니다.
1. **적용** 을 선택합니다.
1. 도구 모음에서 **다른 이름으로 저장** 을 선택합니다.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="통합 문서를 Defender for Cloud의 갤러리에 저장합니다.":::

1. 통합 문서를 저장하는 데 필요한 세부 정보를 입력합니다.
   1. 통합 문서의 이름
   2. 원하는 지역
   3. 구독, 리소스 그룹 및 공유를 적절하게 입력

저장된 통합 문서를 **최근에 수정된 통합 문서** 범주에서 찾을 수 있습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 제공 보고서와 고유한 사용자 지정 대화형 보고서를 빌드하는 옵션이 있는 클라우드용 Defender의 통합 Azure Monitor 통합 문서 페이지에 대해 설명했습니다.

- [Azure Monitor 통합 문서](../azure-monitor/visualize/workbooks-overview.md)에 대해 자세히 알아봅니다
- 기본 제공 통합 문서가 Cloud용 Defender의 권장 사항에서 데이터를 가져옵니다. [보안 권장 사항 - 참조 가이드](recommendations-reference.md)에서 다양한 보안 권장 사항에 대해 알아봅니다.