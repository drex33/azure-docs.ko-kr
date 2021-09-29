---
title: 용어집 용어를 만들고, 가져오며, 내보내는 방법
description: Azure Purview에서 용어를 만들고, 가져오며, 내보내는 방법을 알아봅니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: dfb522e5231072e427c14dfe1c8c16889dd3ff9e
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214182"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>용어집 용어를 만들고, 가져오며, 내보내는 방법

이 문서에서는 .csv 파일을 사용하여 Azure Purview 데이터 카탈로그에 비즈니스 용어집 용어를 만들고, 이를 가져오고 내보내는 방법을 설명합니다.

## <a name="create-a-new-term"></a>새 용어 만들기

다음 단계를 완료하여 새 용어집 용어를 만듭니다.

1. 홈페이지의 왼쪽 탐색 창에서 **데이터 카탈로그를** 선택한 다음, 페이지 가운데에 있는 **용어집 관리** 단추를 선택합니다.

    :::image type="content" source="media/how-to-create-import-export-glossary/find-glossary.png" alt-text="용어집이 강조 표시된 데이터 카탈로그의 스크린샷." border="true":::

2. **용어집 용어** 페이지에서 **+ 새 용어** 를 선택합니다. **시스템 기본값** 템플릿이 선택된 상태로 페이지가 열립니다. 용어집 용어를 만들 템플릿을 선택하고 **계속** 을 선택합니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="새 용어 만들기의 스크린샷." border="true":::

3. 새 용어에 이름을 지정합니다. 이 이름은 카탈로그에서 고유해야 합니다. 용어 이름은 대/소문자를 구분합니다. 따라서 카탈로그에 **Sample** 및 **sample** 용어를 모두 포함할 수 있습니다.

4. **정의** 를 추가합니다.

5. 용어에 대한 **상태** 를 설정합니다. 새 용어는 기본적으로 **초안** 상태입니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/overview-tab.png" alt-text="상태 선택 스크린샷.":::

   이러한 상태 표식은 용어와 연결된 메타데이터입니다. 현재 각 용어에 대해 다음 상태를 설정할 수 있습니다.

   - **초안**: 이 용어는 아직 공식적으로 구현되지 않았습니다.
   - **승인됨**: 이 용어는 공식/표준/승인된 용어입니다.
   - **만료됨**: 이 용어는 더 이상 사용되지 않습니다.
   - **경고**: 이 용어에는 주의가 필요합니다.

6. **리소스** 와 **머리글자어** 를 추가합니다. 용어가 계층 구조의 일부인 경우 개요 탭의 **부모** 에서 부모 용어를 추가할 수 있습니다.

7. 관련 탭에서 **동의어** 와 **관련 용어** 를 추가합니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="새 용어 > 관련 탭 스크린샷." border="true":::

8. 필요에 따라 **연락처** 탭을 선택하여 용어에 전문가 및 관리자를 추가합니다.

9. **만들기** 를 선택하여 용어를 만듭니다.

## <a name="import-terms-into-the-glossary"></a>용어를 용어집으로 가져오기

Azure Purview 데이터 카탈로그는 용어를 용어집으로 가져올 수 있도록 템플릿 .csv 파일을 제공합니다.

카탈로그에서 용어를 가져올 수 있습니다. 파일에서 중복된 용어는 덮어씁니다.

용어 이름은 대/소문자를 구분합니다. 예를 들어 `Sample` 및 `saMple` 모두 동일한 용어집에 있을 수 있습니다.

### <a name="to-import-terms-follow-these-steps"></a>용어를 가져오려면 다음 단계를 수행합니다.

1. **용어집 용어** 페이지에서 **용어 가져오기** 를 선택합니다.

2. 용어 템플릿 페이지가 열립니다. 용어 템플릿과 가져올 .csv 파일을 일치시킵니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="용어집 용어 페이지, 용어 가져오기 단추 스크린샷.":::

3. csv 템플릿을 다운로드하고 이를 사용하여 추가하고자 하는 용어를 입력합니다.

   > [!Important]
   > 시스템에서는 템플릿에서 사용할 수 있는 열을 가져오는 작업만 지원합니다. "시스템 기본" 템플릿에는 모든 기본 특성이 포함됩니다.
   > 하지만 사용자 지정 용어 템플릿에서는 템플릿에 정의된 기본 특성 및 추가 사용자 지정 특성을 사용할 수 있습니다. 따라서 .csv 파일은 선택한 용어 템플릿에 따라 전체 열의 수와 열 이름이 다릅니다. 업로드 후 파일에 문제가 있는지 검토할 수 있습니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="용어집 용어 페이지, 가져올 파일 선택 스크린샷.":::

4. .csv 파일 입력을 완료한 후에는 가져올 파일을 선택하고 **확인** 을 선택합니다.

5. 시스템에서 파일을 업로드하고 모든 용어를 카탈로그에 추가합니다.
 
   > [!Important]
   > 관리자 및 전문가의 이메일 주소는 AAD 그룹에 있는 사용자의 기본 이메일 주소여야 합니다. 대체 이메일, 사용자 계정 이름 및 AAD가 아닌 이메일은 아직 지원되지 않습니다. 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>사용자 지정 특성을 포함하여 용어집에서 용어 내보내기

선택한 용어가 동일한 용어 템플릿에 속하는 한 용어집에서 용어를 내보낼 수 있을 것입니다.

1. 용어집에서 **내보내기** 단추는 기본적으로 사용 설정되지 않습니다. 내보내고자 하는 용어를 선택한 후에는 선택한 용어가 동일한 템플릿에 속하면 **내보내기** 단추가 사용 설정됩니다.

2. **내보내기** 를 선택하여 선택한 용어를 다운로드합니다.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-export.png" lightbox="media/how-to-create-import-export-glossary/select-term-template-for-export.png" alt-text="용어집 용어 페이지, 내보낼 파일 선택 스크린샷.":::

   > [!Important]
   > 계층 구조에 있는 용어가 다른 용어 템플릿에 속하는 경우 가져오기를 위해 이를 다른 .csv 파일로 분할해야 합니다. 또한 현재 가져오기 프로세스를 사용한 용어의 부모 업데이트는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

[자습서: 용어집 용어 만들기 및 가져오기](tutorial-import-create-glossary-terms.md)를 따라 자세히 알아보세요.
