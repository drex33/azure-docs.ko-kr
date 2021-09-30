---
title: Microsoft AppSource 파트너 센터 Power BI 시각적 개체 제공 기술 구성 설정
description: Microsoft AppSource 파트너 센터 Power BI 시각적 개체 제공 기술 구성을 설정하는 방법을 알아봅니다.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: 94f1a884eb2fbb10ced8c343d0408845f0ff34ba
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273025"
---
# <a name="set-up-power-bi-visual-offer-technical-configuration"></a>Power BI 시각적 개체 제안 기술 구성 설정

기술 **구성** 탭에서 Power BI 시각적 개체 제안에 필요한 파일을 제공합니다.

:::image type="content" source="media/power-bi-visual/technical-configuration.png" alt-text="파트너 센터 기술 구성 페이지를 표시합니다.":::

## <a name="pbiviz-package"></a>PBIVIZ 패키지

[필요한](/power-bi/developer/visuals/package-visual) 모든 메타데이터를 포함하는 PBIVIZ 패키지에 Power BI 시각적 개체를 패키징합니다.

- 시각적 개체 이름
- 표시 이름
- GUID(아래 참고 참조)
- 버전(아래 참고 참조)
- 설명
- 작성자 이름 및 전자 메일

> [!NOTE]
> 시각적 개체를 업데이트하거나 다시 제출하는 경우:
> - GUID는 동일하게 유지되어야 합니다.
> - 버전 번호는 패키지 업데이트 간에 증가해야 합니다.

## <a name="sample-pbix-report-file"></a>샘플 PBIX 보고서 파일

시각적 개체 제안을 소개하려면 사용자가 시각적 개체에 익숙해지도록 도와줍니다. 시각적 개체가 사용자에게 제공하는 값을 강조 표시하고 사용 및 서식 옵션의 예를 제공합니다. 끝에 팁, 요령 및 피해야 할 사항이 있는 "힌트" 페이지를 추가합니다. 샘플 PBIX 보고서 파일은 외부 연결 없이 오프라인으로 작동해야 합니다.

> [!NOTE]
> - PBIX 보고서는 PBIVIZ와 동일한 버전의 시각적 개체를 사용해야 합니다.
> - PBIX 보고서 파일은 외부 연결 없이 오프라인으로 작동해야 합니다.

왼쪽 탐색 메뉴에서 **제안 관리** 탭으로 건너뛰기 전에 **초안 저장을** 선택합니다.

## <a name="next-steps"></a>다음 단계

- [제안 관리](power-bi-visual-manage-names.md)
