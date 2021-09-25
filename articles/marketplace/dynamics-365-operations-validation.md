---
title: Microsoft AppSource(Azure Marketplace)에 있는 Dynamics 365 Operations 제품의 기능 유효성 검사
description: Microsoft AppSource(Azure Marketplace)에 있는 Dynamics 365 Operations 제품의 유효성을 기능적으로 검사합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: vamahtan
ms.author: vamahtan
ms.date: 05/19/2021
ms.openlocfilehash: 6dfe65d9cdee33bf4f153347da4d64438acfcf03
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586005"
---
# <a name="dynamics-365-for-operations-functional-validation"></a>Dynamics 365 for Operations 기능 유효성 검사

[파트너 센터](https://go.microsoft.com/fwlink/?linkid=2166002)에서 Dynamics 365 for Operations 제품을 게시하려면 두 가지 기능 유효성 검사가 필요합니다.

- 기본 기능을 보여 주는 Dynamics 365 환경의 데모 비디오를 업로드합니다.
- 솔루션의 [LCS](https://lcs.dynamics.com/)(Lifecycle Services) 환경을 보여 주는 스크린샷을 제공합니다.

> [!NOTE]
> 후속 재인증 게시는 데모가 필요하지 않습니다. 자세히 알아보려면 [AppSource 정책 문서](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops)를 참조하세요.

## <a name="how-to-validate"></a>유효성 검사 방법

기능 유효성 검사에는 두 가지 옵션이 있습니다.

- PST(태평양 표준시) 업무 시간 동안 30분가량의 컨퍼런스 통화를 개최하여 [LCS](https://lcs.dynamics.com/) 환경 및 솔루션을 시연하고 녹화합니다.
- 파트너 센터에서 [상용 마켓플레이스](https://go.microsoft.com/fwlink/?linkid=2165290)로 이동하고 제품 추가 콘텐츠 탭에서 데모 비디오 URL 및 LCS 스크린샷을 업로드합니다.

Microsoft 인증 팀은 비디오와 파일을 검토한 후 솔루션을 승인하거나 다음 단계에 대해 메일을 전송합니다.

> [!NOTE]
> 만들고 있는 솔루션/제안은 커넥터 인 경우에는 커넥터를 호출 하는 동안 작동 하거나 아래 나열 된 비디오 업로드 옵션 중 하나를 사용 합니다.  

### <a name="option-1-30-minute-conference-call"></a>옵션 1: 30분가량의 컨퍼런스 통화

최종 검토 통화를 예약하려면 태평양 표준시를 기준으로 오전 8시와 오후 5시 사이의 가능한 시간 슬롯과 제품 이름에 대해 [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com)에 문의하세요.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>옵션 2: 데모 비디오 및 LCS 스크린샷 업로드

1. 비디오를 녹화하고 원하는 호스팅 사이트에 주소를 업로드합니다. 다음 지침을 따릅니다.

    - Microsoft 인증 팀에서 볼 수 있습니다.
    - 20분 미만입니다.
    - Dynamics 365 환경에 솔루션의 핵심 기능 주요 내용을 최대 3개까지 포함합니다.

    > [!NOTE]
    > 지침을 준수하는 경우 기존 마케팅 비디오를 사용할 수 있습니다.

2. 게시할 제품 또는 솔루션과 일치하는 [LCS](https://lcs.dynamics.com/) 환경의 다음 스크린샷을 캡처합니다. 인증 팀이 텍스트를 읽을 수 있을 만큼 명확해야 합니다. 스크린샷을 JPG 파일로 저장합니다.

    1. **LCS** > **비즈니스 프로세스 모델러** > **프로젝트 라이브러리** 로 이동합니다. 모든 프로세스 단계의 스크린샷을 캡처합니다. 다음과 같이 **다이어그램** 및 **검토됨** 열을 포함합니다.

       :::image type="content" source="media/dynamics-365-operations/project-library.png" alt-text="프로젝트 라이브러리 창을 표시합니다.":::

      2. **LCS** > **솔루션 관리** > **테스트 솔루션 패키지** 로 이동합니다. 다음 예에 표시된 패키지 개요 및 콘텐츠를 포함하는 스크린샷을 캡처합니다.

    | 필드 | 이미지 |
    | --- | --- |
    | 패키지 개요 | [ ![“패키지 개요” 창을 보여 주는 스크린샷.](media/dynamics-365-operations/package-overview-45.png)](media/dynamics-365-operations/package-overview.png#lightbox) |
    | <ul><li>솔루션 승인자</li></ul> | [![패키지 개요 화면](media/dynamics-365-operations/solution-approvers-45.png)](media/dynamics-365-operations/solution-approvers.png#lightbox) |
    | 패키지 내용<ul><li>모델</li><li>소프트웨어 배포 가능 패키지</li></ul> | [![패키지 콘텐츠 화면 1](media/dynamics-365-operations/package-contents-1-45.png)](media/dynamics-365-operations/package-contents-1.png#lightbox) |
    | <ul><li>GER 구성</li><li>데이터베이스 백업</li></ul><br>**GER 구성** 섹션에는 아티팩트가 필요하지 않습니다. | [![패키지 콘텐츠 화면 2](media/dynamics-365-operations/package-contents-2-45.png)](media/dynamics-365-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI 보고서 모델</li><li>BPM 아티팩트</li></ul><br>아티팩트는 **Power BI** 섹션에서 필요하지 않습니다. | [![패키지 콘텐츠 화면 3](media/dynamics-365-operations/package-contents-3-45.png)](media/dynamics-365-operations/package-contents-3.png#lightbox) |
    | <ul><li>데이터 패키지 처리</li><li>솔루션 라이선스 계약 및 개인정보처리방침</li></ul><br>**GER 구성** 및 **Power BI 보고서 모델** 섹션은 운영 제품에 포함할 수 있는 선택 사항입니다. | [![패키지 콘텐츠 화면 4](media/dynamics-365-operations/package-contents-4-45.png)](media/dynamics-365-operations/package-contents-4.png#lightbox) |

    LCS 포털의 각 섹션에 대해 자세히 알아보려면 [LCS 사용자 가이드](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide)를 참조하세요.

3. 파트너 센터에 업로드합니다.

    1. 데모 비디오 주소 및 스크린샷을 포함하는 텍스트 문서를 만들거나 스크린샷을 별도의 JPG 파일로 저장합니다.
    2. 제품의 **보조 콘텐츠** 탭에 있는 [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2165290)에서 .zip 파일에 텍스트와 이미지를 추가합니다.

    [![프로젝트 라이브러리 창을 표시합니다.](media/dynamics-365-operations/supplemental-content.png)](media/dynamics-365-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>다음 단계

- 제품 생성을 시작하려면 [Microsoft Dynamics 365 제품 계획](marketplace-dynamics-365.md)을 참조하세요.
- 제품을 만들었으면 [검토 및 게시](dynamics-365-review-publish.md)할 차례입니다.
