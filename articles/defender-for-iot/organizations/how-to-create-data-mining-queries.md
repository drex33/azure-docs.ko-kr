---
title: 데이터 마이닝 보고서 만들기
description: 프로토콜, 펌웨어 버전 또는 프로그래밍 명령과 같은 다양한 계층에서 네트워크 디바이스에 대한 포괄적이고 세부적인 정보를 생성합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 4da8e9b25c700641f2e6ee49bf44f2e380386897
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284059"
---
# <a name="sensor-data-mining-queries"></a>센서 데이터 마이닝 쿼리

## <a name="about-sensor-data-mining-queries"></a>센서 데이터 마이닝 쿼리 정보

데이터 마이닝 도구를 사용하면 다양한 계층에서 네트워크 디바이스에 대한 포괄적이고 세부적인 정보를 생성할 수 있습니다. 예를 들어 다음에 대한 쿼리를 생성할 수 있습니다.

- 기간

- 인터넷에 연결

- 포트

- 프로토콜

- 펌웨어 버전

- 프로그래밍 명령

- 디바이스 비활성

필터를 기반으로 보고서를 세부적으로 조정할 수 있습니다. 예를 들어 펌웨어가 업데이트된 특정 서브넷을 쿼리할 수 있습니다.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="활성 디바이스 목록.":::

다양한 도구를 사용하여 쿼리를 관리할 수 있습니다. 예를 들어 내보내고 저장할 수 있습니다.

> [!NOTE]
> 관리자 및 보안 분석가는 데이터 마이닝 옵션에 액세스할 수 있습니다.

### <a name="dynamic-updates"></a>동적 업데이트

사용자가 만드는 데이터 마이닝 쿼리는 열 때마다 동적으로 업데이트됩니다. 예를 들면 다음과 같습니다.

- 6월 1일에 디바이스의 펌웨어 버전에 대한 보고서를 만들고 6월 10일에 다시 보고서를 열면 이 보고서는 6월 10일에 해당하는 정보로 업데이트됩니다.

- 6월 1일에 지난 30일 동안 검색된 새 디바이스를 감지하는 보고서를 만들고 6월 30일에 보고서를 열면 지난 30일에 대한 결과가 표시됩니다.

### <a name="data-mining-use-cases"></a>데이터 마이닝 사용 사례

쿼리를 사용하여 다양한 보안 팀의 광범위한 보안 요구 사항을 처리할 수 있습니다.

- **SOC 인시던트 응답**: 즉각적인 인시던트 대응을 처리할 수 있도록 실시간으로 보고서를 생성합니다. 예를 들어 데이터 마이닝은 패치를 요구하는 디바이스 목록에 대한 보고서를 생성할 수 있습니다.

- **포렌식**: 조사 보고서에 기록된 데이터를 기반으로 보고서를 생성합니다.

- **IT 네트워크 무결성**: 전반적인 네트워크 보안을 개선하는 데 도움이 되는 보고서를 생성합니다. 예를 들어 취약한 인증 자격 증명이 있는 디바이스를 나열하는 보고서를 생성할 수 있습니다.

- **표시 유형**: 모든 쿼리 항목을 포함하는 보고서를 생성하여 네트워크의 모든 기본 매개 변수를 볼 수 있습니다.

- **일보안** 안전하지 않은 상태(예: 프로그램 및 원격 상태)에서 PLC를 검색하여 보안을 향상시킵니다.

## <a name="data-mining-storage"></a>데이터 마이닝 스토리지

디바이스를 삭제하는 경우를 제외하고 데이터 마이닝 정보는 계속 저장됩니다. 데이터 마이닝 결과를 안전한 외부 서버로 내보내고 저장할 수 있습니다. 또한 센서는 시스템 연속성 및 데이터 유지를 위해 매일 자동 백업을 수행합니다.

## <a name="predefined-data-mining-queries"></a>미리 정의된 데이터 마이닝 쿼리

다음과 같이 미리 정의된 쿼리를 사용할 수 있습니다. 해당 쿼리는 실시간으로 생성됩니다.

- **CVE**: 지난 24시간 이내에 알려진 취약점이 감지된 디바이스 목록입니다.

- **제외된 CVE**: 수동으로 제외된 모든 CVE의 목록입니다. VA 보고서 및 공격 벡터에서 더 정확한 결과를 얻으려면 CVE를 포함하거나 제외하여 수동으로 CVE 목록을 사용자 지정할 수 있습니다.

- **인터넷 활동**: 인터넷에 연결된 디바이스입니다.

- **비활성 디바이스**: 지난 7일 동안 통신하지 않은 디바이스입니다.

- **활성 디바이스**: 지난 24시간 이내에 활성화된 네트워크 디바이스입니다.

- **원격 액세스**: 원격 세션 프로토콜을 통해 통신하는 디바이스입니다.

- **프로그래밍 명령**: 산업용 프로그래밍을 보내는 디바이스입니다.

해당 보고서는 **보고서** 화면에서 자동으로 액세스할 수 있으며, 여기서는 RO 사용자 및 다른 사용자가 해당 보고서를 볼 수 있습니다. RO 사용자는 데이터 마이닝 보고서에 액세스할 수 없습니다.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="데이터 마이닝 화면입니다.":::

## <a name="create-a-data-mining-query"></a>데이터 마이닝 쿼리 만들기

데이터 마이닝 보고서를 만들려면 다음을 수행합니다.

1. 측면 메뉴에서 **데이터 마이닝** 을 선택합니다. 미리 정의된 제안 보고서가 자동으로 표시됩니다.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="왼쪽 창에서 데이터 마이닝을 선택합니다.":::

2. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::를 선택합니다.

3. **새 보고서** 를 선택하고 보고서를 정의합니다.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="이 화면을 작성하여 새 보고서를 만듭니다.":::

   다음 매개 변수를 사용할 수 있습니다.

   - 보고서 이름 및 설명을 입력합니다.

   - 범주에서 다음 중 하나를 선택합니다.

      - **범주(모두)** - 네트워크 내 모든 디바이스에 대한 모든 보고서 결과를 표시합니다.

      - **일반** - 표준 범주를 선택합니다.

   - 관심 있는 특정 보고서 매개 변수를 선택합니다.

   - 정렬 순서(**정렬 기준**)를 선택합니다. 활동 또는 범주를 기준으로 결과를 정렬합니다.

   - **보고서** 페이지에서 액세스할 수 있는 보고서로 보고서 결과를 저장하려면 **보고서 페이지에 저장** 을 선택합니다. 이렇게 하면 만든 보고서를 RO 사용자가 실행할 수 있습니다.

   - **필터(추가)** 를 선택하여 필터를 추가합니다. 와일드카드 요청이 지원됩니다.

   - 디바이스 그룹(디바이스 맵에서 정의됨)을 지정합니다.

   - IP 주소를 지정합니다.

   - 이름을 지정합니다.

   - MAC 주소를 지정합니다.

4. **저장** 을 선택합니다. **데이터 마이닝** 페이지에 보고서 결과가 열립니다.

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="데이터 마이닝 페이지에 표시되는 보고서 결과입니다.":::

### <a name="manage-data-mining-reports"></a>데이터 마이닝 보고서 관리

다음 표에서는 데이터 마이닝에 대한 관리 옵션을 설명합니다.

| 아이콘 이미지 | Description |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | 보고서 매개 변수를 편집합니다. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | PDF로 내보냅니다. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |CSV로 내보냅니다. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | 마지막으로 수정한 날짜와 같은 추가 정보를 표시합니다. 이 기능을 사용하여 쿼리 결과 스냅샷을 만들 수 있습니다. 예를 들어 팀 리더 또는 SOC 분석가의 추가 조사를 위해 이 작업을 수행해야 할 수도 있습니다. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | **보고서** 페이지에 표시하거나 **보고서** 페이지를 숨깁니다. :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="보고서를 숨기거나 표시합니다."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | 보고서를 삭제합니다. |

#### <a name="create-customized-directories"></a>사용자 지정 디렉터리 만들기 

범주 디렉터리를 만들어 데이터 마이닝 쿼리에 대한 광범위한 정보를 구성할 수 있습니다. 예를 들어 프로토콜 또는 위치 디렉터리를 만들 수 있습니다.

새 디렉터리를 만들려면 다음을 수행합니다.

1. :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::를 선택하여 새 디렉터리를 추가합니다.

2. **새 디렉터리** 를 선택하여 새 디렉터리 양식을 표시합니다.

3. 새 디렉터리의 이름을 지정합니다.

4. 필요한 보고서를 새 디렉터리로 끕니다. 언제든지 보고서를 주 보기로 다시 끌 수 있습니다.

5. 새 디렉터리를 마우스 오른쪽 단추로 클릭하여 열거나, 편집하거나, 삭제합니다.

#### <a name="create-snapshots-of-report-results"></a>보고서 결과의 스냅샷 만들기

추가 조사를 위해 특정 쿼리 결과를 저장해야 할 수도 있습니다. 예를 들어 다양한 보안 팀과 결과를 공유해야 할 수 있습니다.

스냅샷은 보고서 결과 내에 저장되며 동적 쿼리를 생성하지 않습니다.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="스냅샷":::

스냅샷을 만들려면 다음을 수행합니다.

1. 필요한 보고서를 엽니다.

2. 정보 아이콘 :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false":::을 선택합니다.

3. **새로 만들기** 를 선택합니다.

4. 스냅샷의 이름을 입력하고 **저장** 을 선택합니다.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="스냅샷을 만듭니다.":::

#### <a name="customize-the-cve-list"></a>CVE 목록 사용자 지정

다음과 같이 수동으로 CVE 목록을 사용자 지정할 수 있습니다.

  - CVE 포함/제외

  - CVE 점수 변경

CVE 보고서에서 수동 변경을 수행하려면 다음을 수행합니다.

1.  측면 메뉴에서 **데이터 마이닝** 을 선택합니다.

2. **데이터 마이닝** 창의 왼쪽 위에서 :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::를 선택합니다. 그런 다음 **새로 보고서** 를 선택합니다.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="새 보고서를 만듭니다.":::

3. 왼쪽 창에서 다음 옵션 중 하나를 선택합니다.

   - **알려진 취약점**: 두 옵션을 선택하고 보고서에서 하나에는 CVE를, 다른 하나에는 제외된 CVE를 포함하는 두 테이블에 결과를 표시합니다.

   - **CVE**: 모든 CVE의 목록을 표시하려면 이 옵션을 선택합니다.

   - **제외된 CVE**: 제외된 모든 CVE의 목록을 표시하려면 이 옵션을 선택합니다.

4. **이름** 및 **설명** 정보를 입력하고 **저장** 을 선택합니다. 새 보고서가 **데이터 마이닝** 창에 표시됩니다.

5. CVE를 제외하려면 CVE에 대한 데이터 마이닝 보고서를 엽니다. 모든 CVE가 있는 목록이 표시됩니다.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="CVE 보고서.":::

6. 목록에서 항목을 선택할 수 있도록 설정하려면 :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: 및 사용자 지정하려는 CVE를 선택합니다. **작업** 모음이 아래쪽에 표시됩니다.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="데이터 마이닝 작업 모음 스크린샷.":::

7. 제외하려는 CVE를 선택한 다음 **레코드 삭제** 를 선택합니다. 선택한 CVE는 CVE 목록에 표시되지 않으며 제외된 CVE 목록을 생성하는 경우 해당 목록에 표시됩니다.

8. CVE 목록에 제외된 CVE를 포함하려면 제외된 CVE에 대한 보고서를 생성하고 해당 목록에서 CVE 목록에 다시 포함하려는 항목을 삭제합니다.

9. 점수를 변경하려는 CVE를 선택한 후 **CVE 점수 업데이트** 를 선택합니다.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="CVE 점수를 업데이트합니다.":::

10. 새 점수를 입력하고 **확인** 을 선택합니다. 업데이트된 점수가 선택한 CVE에 표시됩니다.



## <a name="sensor-reports-based-on-data-mining"></a>데이터 마이닝을 기반으로 하는 센서 보고서

**보고서** 옵션에서 액세스할 수 있는 일반 보고서는 미리 정의된 데이터 마이닝 보고서입니다. 해당 보고서는 데이터 마이닝에서 사용할 수 있는 동적 쿼리가 아니라 데이터 마이닝 쿼리 결과를 정적으로 나타낸 것입니다.

데이터 마이닝 쿼리 결과는 읽기 전용 사용자에게 제공되지 않습니다. 읽기 전용 사용자가 데이터 마이닝 쿼리에 의해 생성된 정보에 액세스할 수 있도록 하려는 관리자 및 보안 분석가는 정보를 보고서로 저장해야 합니다.

보고서는 데이터 마이닝 쿼리 결과에 의해 생성된 정보를 반영합니다. 여기에는 보고서 보기에서 사용할 수 있는 기본 데이터 마이닝 보고서가 포함됩니다. 또한 관리자 및 보안 분석가는 사용자 지정 데이터 마이닝 쿼리를 생성하여 보고서로 저장할 수 있습니다. 보고서는 RO 사용자도 사용할 수 있습니다.

보고서를 생성하려면 다음을 수행합니다.

1. 측면 메뉴에서 **보고서** 를 선택합니다.

2. 표시할 필수 보고서를 선택합니다. **사용자 지정** 또는 **자동 생성된** 보고서(예: **프로그래밍 명령** 및 **원격 액세스**)를 선택할 수 있습니다.

3. 화면 오른쪽 위에 있는 아이콘 중 하나를 선택하여 보고서를 내보낼 수 있습니다.

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: PDF 파일로 내보냅니다.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: CSV 파일로 내보냅니다.

> [!NOTE] 
> RO 사용자는 자신이 만든 보고서만 볼 수 있습니다.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="생성할 보고서를 선택합니다.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="원격 액세스 보고서가 생성되었습니다.":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>데이터 마이닝 보고서를 기반으로 하는 온-프레미스 관리 콘솔 보고서

온-프레미스 관리 콘솔을 사용하여 연결된 각 센서에 대한 보고서를 생성할 수 있습니다. 보고서는 수행되는 센서 데이터 마이닝 쿼리를 기반으로 합니다.

다음과 같은 보고서를 생성할 수 있습니다.

- **활성 디바이스(지난 24시간)** : 24시간 이내에 네트워크 활동이 있는 디바이스 목록을 제공합니다.

- **비활성 디바이스(지난 7일)** : 지난 7일 동안 네트워크 활동이 없는 디바이스 목록을 제공합니다.

- **프로그래밍 명령**: 지난 24시간 이내에 프로그래밍 명령을 보낸 디바이스의 목록을 표시합니다.

- **원격 액세스**: 지난 24시간 이내에 원격 소스가 액세스한 디바이스 목록을 제공합니다.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="보고서 보기 스크린샷.":::

온-프레미스 관리 콘솔에서 센서를 선택하면 해당 센서에 구성된 모든 사용자 지정 보고서가 보고서 목록에 표시됩니다. 각 센서에 대해 기본 보고서 또는 해당 센서에 구성된 사용자 지정 보고서를 생성할 수 있습니다.

보고서를 생성하려면 다음을 수행합니다.

1. 왼쪽 창에서 **보고서** 를 선택합니다. **보고서** 창이 나타납니다.

2. **센서** 드롭다운 목록에서 보고서를 생성할 센서를 선택합니다.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="센서 보기 스크린샷.":::

3. 드롭다운 목록에서 생성할 보고서를 선택합니다.

4. 보고서 결과의 PDF를 만들려면 :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false":::을 선택합니다.
