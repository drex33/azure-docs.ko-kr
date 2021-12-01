---
title: 짧은 코드에 적용
titleSuffix: An Azure Communication Services quickstart
description: 짧은 코드를 적용하는 방법에 대해 알아봅니다.
author: prakulka
manager: shahen
services: azure-communication-services
ms.author: prakulka
ms.date: 11/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 04cf399517cd02b5fe0af1730aa19cd3be55680f
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133385368"
---
# <a name="quickstart-apply-for-a-short-code"></a>빠른 시작: 짧은 코드에 적용

[!INCLUDE [Short code eligibility notice](../../includes/public-preview-include.md)]

[!INCLUDE [Short code eligibility notice](../../includes/public-preview-include-short-code-eligibility.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [활성 Communication Services 리소스](../create-communication-resource.md)

## <a name="get-a-short-code"></a>짧은 코드 얻기
짧은 코드 프로비저닝을 시작하려면 [Azure Portal](https://portal.azure.com)Communication Services 리소스로 이동합니다.

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Communication Services 리소스의 기본 페이지를 보여주는 스크린샷":::

## <a name="apply-for-a-short-code"></a>짧은 코드에 적용
리소스 메뉴에서 짧은 코드 블레이드로 이동하고 "Get" 단추를 클릭하여 짧은 코드 프로그램 간략한 애플리케이션 마법사를 시작합니다. 

짧은 코드 블레이드의 마법사는 프로그램에 대한 일련의 질문과 통신 사업자가 짧은 코드 프로그램을 검토하고 승인하는 데 도움이 되는 콘텐츠에 대한 설명을 안내합니다. 프로그램 간략한 애플리케이션을 작성하는 방법에 대한 자세한 지침은 [프로그램 간략한 채우기 지침을](../../concepts/sms/program-brief-guidelines.md)확인하세요.

짧은 코드 프로그램 간략한 등록에는 사용자 환경(예: 작업 호출, 옵트인, 옵트아웃 및 메시지 흐름) 및 회사에 대한 정보를 포함하여 메시징 프로그램에 대한 세부 정보가 필요합니다. 이 정보는 이동 통신 사업자가 프로그램이 CTIA(셀룰러 통신 산업 연결) 지침 및 규정 요구 사항을 충족하는지 확인하는 데 도움이 됩니다. 간단한 코드 Program Brief 애플리케이션은 다음 4개 섹션으로 구성됩니다.
 
### <a name="program-details"></a>프로그램 세부 정보 
먼저 프로그램 이름을 제공하고 전화 번호를 프로비전할 국가/지역을 선택해야 합니다. 

:::image type="content" source="./media/apply-for-short-code/program-details.png" alt-text="프로그램 세부 정보 섹션을 보여주는 스크린샷.":::

#### <a name="select-short-code-type"></a>짧은 코드 형식 선택
짧은 코드 구성은 다음 두 단계로 구분됩니다.
- 짧은 코드 형식 선택
- 짧은 코드 기능 선택

두 개의 짧은 코드 형식인 Random 및 Vanity 중에서 선택할 수 있습니다. 임의의 짧은 코드를 선택하면 미국 CSCA(Common Short Codes Association)에서 임의로 선택한 짧은 코드가 표시됩니다. 베니티 짧은 코드를 선택하는 경우 프로그램에 사용하려는 베니티 짧은 코드의 우선 순위 목록을 입력해야 합니다. 목록의 첫 번째 짧은 코드를 임대할 수 없는 경우 목록의 대체 항목이 사용됩니다. 예: 234567, 234578, 234589. [US Short Codes 디렉터리](https://usshortcodedirectory.com/)에서 사용 가능한 짧은 코드 목록을 조회할 수 있습니다.

숫자 유형을 선택하면 메시지 유형 및 대상 날짜를 선택할 수 있습니다.  운송업체에 짧은 코드 등록에는 일반적으로 8~12주가 걸리므로 이 등록 기간을 고려하여 이 대상 날짜를 선택해야 합니다.

> [!Note]
> Azure Communication Service는 현재 SMS만 지원합니다. MMS 시작에 대한 [로드맵을](https://github.com/Azure/Communication/blob/master/roadmap.md) 확인하세요.

#### <a name="enter-program-information"></a>프로그램 정보 입력
이 섹션에서는 프로그램 되풀이, 메시징 콘텐츠, 프로그램의 유형 및 설명, 개인 정보 취급 방침 및 프로그램 약관과 같은 프로그램에 대한 세부 정보를 제공해야 합니다.

:::image type="content" source="./media/apply-for-short-code/program-details-contd.png" alt-text="계속된 프로그램 세부 정보 섹션을 보여주는 스크린샷.":::

### <a name="contact-details"></a>연락처 세부 정보
이 섹션에서는 최종 사용자에게 프로그램에 대한 도움이나 지원이 필요한 경우 회사 및 고객 관리에 대한 정보를 제공해야 합니다. 

:::image type="content" source="./media/apply-for-short-code/contact-details.png" alt-text="연락처 세부 정보 섹션을 보여주는 스크린샷.":::

### <a name="volume-details"></a>볼륨 세부 정보
이 섹션에서는 월별 사용자당 전송하려는 메시지 수를 예측하고 프로그램의 일부로 예상되는 트래픽 급증을 공개해야 합니다.

:::image type="content" source="./media/apply-for-short-code/volume-details.png" alt-text="볼륨 세부 정보 섹션을 보여주는 스크린샷.":::

### <a name="template-information"></a>템플릿 정보
이 섹션에서는 옵트인, 옵트아웃 및 기타 메시지 흐름과 관련된 샘플 메시지를 캡처합니다.

:::image type="content" source="./media/apply-for-short-code/templates-1.png" alt-text="템플릿 세부 정보 섹션을 보여주는 스크린샷":::

:::image type="content" source="./media/apply-for-short-code/templates-2.png" alt-text="템플릿 세부 정보 섹션을 보여주는 스크린샷.":::

완료되면 제공된 프로그램 간략한 정보를 검토하고 Azure Portal을 통해 완료된 애플리케이션을 제출합니다. 
 
이제 이 프로그램 간략한 내용은 검토를 위해 Azure Communication Services 서비스 데스크로 자동으로 전송됩니다. 특히 서비스 데스크는 승인을 위해 모든 미국 이동 통신 사업자에게 보내기 전에 제공된 정보가 올바른 형식인지 확인하려고 합니다. 이동 통신 사업자는 일반적으로 8~12주 정도 걸릴 수 있는 짧은 코드 프로그램의 세부 정보를 검토합니다. 이동 통신 사업자가 프로그램 요약을 승인하면 이메일을 통해 알림을 받게 됩니다. 이제 메시징 프로그램에 대한 이 짧은 코드에서 메시지 보내기 및 받기를 시작할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결
일반적인 질문 및 문제:
- 짧은 코드 구매는 미국에서만 지원됩니다. 전화 번호를 구매하려면 다음 사항을 확인하세요.
  - 연결된 Azure 구독 청구 주소가 미국에 있습니다. 지금은 리소스를 다른 구독으로 이동할 수 없습니다.
  - Communication Services 리소스가 미국 데이터 위치로 프로비저닝됩니다. 지금은 리소스를 다른 데이터 위치로 이동할 수 없습니다.
- 짧은 코드 릴리스는 현재 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [짧은 코드 프로그램 간략한 애플리케이션을 채우기 위한 지침 확인](../../concepts/sms/program-brief-guidelines.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [SMS SDK](../../concepts/sms/sdk-features.md) 숙지