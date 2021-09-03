---
title: OCP GTM에서 Microsoft AppSource용 파트너 센터로 공동 판매 솔루션 마이그레이션
description: OCP GTM에서 파트너 센터로 공동 판매 솔루션을 마이그레이션합니다(Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 07/13/2021
ms.openlocfilehash: 6ca8c75e5cf6f5290736cf01a20db63a8fb2d077
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389086"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>공동 판매 솔루션을 OCP GTM에서 상업용 Marketplace로 마이그레이션

Microsoft는 게시 환경을 바꾸고 있습니다. [상업용 Marketplace](overview.md)는 이미 Microsoft와 관계를 관리하고 있는 파트너 센터에서 제품의 제작과 관리를 중앙화하여 Microsoft의 3개의 채널을 통해서 공동 판매를 위한 간편한 제품 게시 서비스를 제공합니다.

상업용 Marketplace에 등록된 Microsoft 파트너는 다음 활동이 가능합니다.

- 제품을 중앙에 게시하고 Microsoft 직접 고객, 파트너 및 판매자 채널에 공동 판매하세요.
- 제품이 제품의 기능에 부합하는 수백만 명의 클라우드 고객에게 도달하기 위해 올바른 온라인 매장([Microsoft AppSource](https://appsource.microsoft.com) 또는 [Azure Marketplace](https://azure.microsoft.com))에 있는지 확인하세요.
- 비즈니스 목표와 일치하는 제품을 공동 판매하기 위한 자체 게시 환경을 운용하세요.
- 이미 Microsoft와 계약을 맺어 공동 판매 기회를 관리하고 있는 파트너 센터에서 제품 게시를 맞춰보세요.
- [Marketplace Rewards](marketplace-rewards.md)의 잠금을 해제합니다.

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Microsoft와 공동 판매를 지속하기 위한 필수 구성 요소

활성화된 Microsoft 파트너 네트워크 멤버 자격이 있고 파트너 센터의 상업용 Marketplace에 등록되어 있는지 확인하세요.

- Microsoft 파트너 네트워크에 [무료로](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 조인하세요. 파트너는 독점적 리소스, 프로그램, 도구 및 연결에 액세스하여 비즈니스를 성장시킬 수 있습니다.
- 상업용 Marketplace 계정이 없는 경우 [지금 등록](create-account.md)해서 Microsoft와 지속적으로 공동 판매를 하고 전체 게시 환경에 액세스하세요.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>공동 판매를 바로 수행할 수 있는 상태를 실현하기 위한 업데이트 게시

솔루션이 Microsoft 판매자와 파트너에 노출되려면 [공동 판매 준비 요구 사항](./co-sell-overview.md)을 충족해야 합니다. Microsoft 판매자가 공동 판매 인센티브를 받으려면 [인센티브 자격 요구 사항](./co-sell-overview.md)을 충족해야 합니다. 파트너 센터의 공동 판매 탭에서 요구 사항을 충족하세요(이 문서 뒷부분의 [이 이미지](#action-2-merge) 참조).

> [!NOTE]
> 상업용 Marketplace에서 솔루션은 게시 환경 전체에서 “제품”으로 간주됩니다.

상업용 Marketplace에 등록한 후에는 OCP GTM에서 솔루션 마이그레이션을 준비합니다. Microsoft는 조직에 OCP GTM 솔루션의 단일 뷰를 제공하는 디지털 마이그레이션 인터페이스를 만들었습니다. 이 인터페이스에서 상용 마켓플레이스에 이미 게시되고 라이브된 유사한 제품에 솔루션을 병합하거나 삭제하는 작업을 수행할 수 있습니다.

OCP GTM에서 솔루션을 가져오기 전에 다음 단계를 수행합니다.

1. 회사의 [게시자 목록](https://go.microsoft.com/fwlink/?linkid=2165704)에 접속합니다. 게시자에는 게시 액세스 권한이 있는 계정 소유자, 관리자 및 개발자가 포함됩니다. 자세한 정보는 [파트너 센터 사용자 역할](user-roles.md)에서 알아보세요.
2. 해당 역할들로만 솔루션을 편집 및 게시할 수 있으므로 나열된 연락처 중 하나를 통해 *관리자* 또는 *개발자* 로서 상업용 Marketplace에 [사용자를 추가](https://go.microsoft.com/fwlink/?linkid=2166003)하도록 요청합니다.
3. 개발자와 협력하여 OCP GTM 계정에서 상업용 Marketplace로 솔루션을 이동합니다.
4. 수행할 작업을 결정합니다.
    1. OCP GTM에 파트너 센터로 마이그레이션할 솔루션이 있는 경우 조회 파이프라인, 참고 자료, 공동 판매 상태 및 성과급을 유지하려면 다음 두 가지 시나리오 중에서 선택할 수 있습니다.
        - 상용 마켓플레이스에 비슷한 제품이 없는 경우
            - 1단계. [제품 만들기](#step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps)
            - 2단계. [상용 마켓플레이스에 게시](#scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps)
            - 3단계. [OCP GTM의 솔루션을 새로 게시된 마켓플레이스 제품에 병합하도록 요청](#begin-the-migration-of-your-solutions-from-ocp-gtm)
        - 상용 마켓플레이스에 이미 유사한 제품이 있는 경우
            - 1단계. [OCP GTM의 솔루션을 게시된 마켓플레이스 제품에 병합하도록 요청](#begin-the-migration-of-your-solutions-from-ocp-gtm)
    1. OCP GTM에 더 이상 관련이 없는 솔루션이 있는 경우 이 솔루션을 삭제합니다.

> [!IMPORTANT]
> 2021년 5월 17일에 아래 마이그레이션 옵션이 제거되었습니다.  이전에 마이그레이션 옵션을 사용했고 상용 마켓플레이스에 초안 제품으로 마이그레이션한 제품이 있는 경우 상용 마켓플레이스에서 게시 프로세스를 완료하는 것이 중요합니다.  
> - [제품을 검토하고 Microsoft 상용 마켓플레이스에 게시하는 방법 | Microsoft Docs](./review-publish-offer.md)에 대해 자세히 알아보기
> - [상용 마켓플레이스 교육 데크 게시](https://assetsprod.microsoft.com/mpn/en-us/guidance-and-permissions-needed-to-publish-an-offer-and-co-sell-with-ms.pptm)
:::image type="content" source="media/co-sell-migrate/ready-to-migrate.png" alt-text="이 솔루션을 상용 마켓플레이스로 마이그레이션":::

## <a name="begin-the-migration-of-your-solutions-from-ocp-gtm"></a>OCP GTM에서 솔루션 마이그레이션 시작

1. [여기](https://go.microsoft.com/fwlink/?linkid=2165807)에서 마이그레이션을 시작합니다.
2. **개요** 페이지를 선택하고 **여기를 클릭하여 시작하세요**.

    :::image type="content" source="media/co-sell-migrate/welcome-overveiw.png" alt-text="개요 페이지 표시":::

3. 마이그레이션을 시작하려면 MPN ID와 연결된 모든 솔루션을 표시하는 **솔루션** 탭을 선택합니다.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="파트너 센터 개요 페이지, 솔루션 탭.":::

    > [!NOTE]
    > 이 탭은 상업용 Marketplace로의 마이그레이션이 보류 중인 솔루션이 없는 경우에 표시됩니다. Microsoft와 공동 판매를 계속하려면 마이그레이션된 솔루션이 상업용 Marketplace에 게시되었는지 확인하세요.

    도구 설명을 검토해서 솔루션 상태에 대해 자세히 알아보세요. 모든 솔루션의 대기 중인 작업은 **작업** 아래에 나열됩니다.<a name="beginmigration"></a>

4. 마이그레이션할 솔루션의 **마이그레이션 시작**(위 이미지 참조)을 선택하고 다음 옵션 중 하나를 선택합니다.

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="마이그레이션에 대한 세 가지 옵션.":::

### <a name="merge-solution"></a>솔루션 병합

상용 마켓플레이스에서 기존에 게시된 라이브 제품에 OCP GTM 솔루션을 병합할 경우 어떤 가치를 얻을 수 있나요?

- 현재 OCP GTM의 제품과 공동 판매 중인 파트너는 솔루션을 상용 마켓플레이스로 마이그레이션하여 기능을 활용하고 게시 환경을 단순화하는 것이 좋습니다.

OCP GTM에 파트너 센터로 마이그레이션하려는 솔루션이 있는 경우 두 가지 시나리오를 선택할 수 있습니다. 이 병합 프로세스가 끝난 후에도 제품은 조회 파이프라인, 참고 자료, 공동 판매 상태 및 성과급을 유지합니다.

#### <a name="step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps"></a>1단계: 상용 마켓플레이스에 유사한 제품이 없습니다. 다음 단계를 수행하세요.

OCP GTM의 솔루션을 병합할 제품이 상용 마켓플레이스에 아직 없는 경우 먼저 상용 마켓플레이스에서 제품을 만든 후 게시해야 합니다(공동 판매 상태, 성과급 및 조회 파이프라인이 유지됨).

1. 상용 마켓플레이스에서 초안 제품 만들기

   1. **+ 새 제품** 선택

        :::image type="content" source="media/co-sell-migrate/new-offer.png" alt-text="새 제품 표시":::

   2. 각 탭에서 필요한 정보를 입력합니다. • **자세한 정보** 링크 및 도구 설명은 요구 사항 및 세부 정보를 안내합니다.
         • 필요하다면 **CSP를 통해 재판매** 페이지(왼쪽 탐색 메뉴)를 완료해서 CSP(클라우드 솔루션 공급자) 프로그램을 통해 재판매합니다.

        :::image type="content" source="media/co-sell-migrate/offer-setup-nav.png" alt-text="개요 옵션이 강조 표시된 제품 설치 페이지 표시":::
   3. **초안 저장** 을 선택합니다.
        - 제품을 게시하기 전에 입력해야 하는 정보에 대한 자세한 지침은 적절한 [게시 가이드](./publisher-guide-by-offer-type.md)를 참조하세요.
        - 제품 선택 및 구성을 완료하려면 제품 유형에 대한 해당 문서의 자격 요구 사항을 검토합니다.
        - 솔루션이 제품 유형 및 구성에 매핑되는 방법에 대한 예는 각 온라인 스토어별 게시 패턴을 검토하세요.
        - [목록에 제품 추가 모범 사례 - Microsoft 상용 마켓플레이스 | Microsoft Docs](./gtm-offer-listing-best-practices.md)

    > [!TIP]
    > **Microsoft와 공동 판매** 탭에서는 데이터를 *입력하지 않는 것* 이 좋습니다. 시간을 절약하기 위해 병합 프로세스 중에 OCP GTM에 기존 참고 자료와 함께 이 데이터가 자동으로 채워집니다.

    병합이 완료되면 Microsoft와 공동 판매 탭으로 돌아가서 필요에 따라 업데이트할 수 있습니다. 자세한 내용은 [상업용 Marketplace 제품에 대한 공동 판매 구성](./co-sell-configure.md)을 참조하세요.
1. 완료되면 **검토 및 게시** 를 선택합니다.

     :::image type="content" source="media/co-sell-migrate/co-sell-with-ms.png" alt-text="옵션이 강조 표시되어 Microsoft와 공동 판매가 표시됨":::
1. 제출된 모든 정보를 검토한 후 **게시** 를 선택하여 인증 검토를 위한 제품 초안을 제출합니다. [인증 단계에 대해 자세히 알아보세요](./review-publish-offer.md). :::image type="content" source="media/co-sell-migrate/review-and-publish.png" alt-text="검토 및 게시 페이지를 표시합니다.":::
1. 개요 탭에서 제출 상태를 추적합니다.

    :::image type="content" source="media/co-sell-migrate/offer-overview-tab.png" alt-text="개요 탭 표시":::
1. 인증 검토가 완료되면 사용자에게 알려드립니다. 실행 가능한 피드백을 제공받았다면 이를 해결하고 **게시** 를 선택해 재인증을 시작합니다.

1. 제품이 인증을 통과하면 제공된 링크를 사용하여 제품을 미리 보고 원하는 대로 최종 조정을 할 수 있습니다. 준비가 되면 **실시간 전송**(위 단추 참조)을 선택하여 제품을 관련 상업용 Marketplace 상점에 게시합니다.

1. **아래의 시나리오 2를 계속 진행하여 병합 프로세스를 완료합니다.**

#### <a name="scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps"></a>시나리오 2: 상용 마켓플레이스에 유사한 제품이 있습니다. 다음 단계를 수행하세요.

솔루션이 이미 게시되어 상용 마켓플레이스에 있고, OCP GTM의 솔루션과 상용 마켓플레이스의 제품이 단일 제품으로 병합되는 경우 이 옵션을 선택합니다. 이렇게 하면 중복 제품이 생성되지 않습니다.

1. 기존 제품을 식별합니다.
   1. **상용 마켓플레이스의 기존 제품과 유사** 를 선택합니다([위의](#beginmigration) **마이그레이션 시작** 이미지 참조).
   1. **작업 1** 탭은 OCP GTM 솔루션을 연결할 수 있는 라이브 상업용 Marketplace 제품을 보여 줍니다. 제품이 있다면 목록에서 라이브인 제품을 선택합니다. 선택할 제품 목록이 없는 경우 Microsoft AppSource 또는 Azure Marketplace에서 고객 연결 주소(URL)를 입력합니다.
   1. **계속** 을 선택합니다.
        [![병합 프로세스의 작업 1 탭입니다.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
1. 병합을 요청합니다.
    1. **작업 2** 탭에서는 식별한 것과 함께 OCP GTM 솔루션을 병합하도록 요청하는 지침을 보여 줍니다. 병합을 요청하려면 **저장 및 기술 지원 서비스에 문의** 를 선택하세요. 브라우저에서 파트너 지원 페이지가 열립니다. 
    <a id="action-2-merge"></a>
        [![병합 프로세스의 작업 2 탭](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. **문제 세부 정보 제공** 을 선택하고 다음을 입력합니다.
        1. 제품 유형
        1. Offer name
        1. OCP 솔루션 ID
        1. 기존 목록의 URL
    1. **제출** 을 선택합니다. 파트너 지원 팀은 영업일 2일 이내에 사용자에게 답변을 드립니다.
    1. 파트너 지원은 성공적인 병합을 보장하고 라이브 제품으로 게시되도록 사용자와 협력합니다.

### <a name="discard-this-solution"></a>이 솔루션을 삭제합니다

고객의 요구를 충족하는 고품질 제품으로 상업용 Marketplace에서의 현재 존재감 및 검색 가능성을 최적화하기 위해 솔루션의 실행 가능성 및 관련성을 평가하는 것이 좋습니다.

OCP GTM 솔루션의 솔루션이 더 이상 상관이 없는 경우 이 옵션을 선택합니다. 삭제를 확인하라는 메시지가 표시되며 나중에 삭제를 취소할 수도 있습니다.

1. **이 솔루션을 삭제합니다**([위의](#beginmigration) **작업 필수** 이미지 참조)을 선택하고 **계속** 합니다.
2. **삭제** 를 선택합니다.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="삭제를 확인합니다.":::

3. 삭제를 취소하려면 **삭제 취소** 를 선택합니다.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="삭제 취소 링크입니다.":::

### <a name="additional-help"></a>추가 도움말

 추가적인 도움이 필요하다면 **도움말 보기** 탭을 선택하여 파트너 지원 팀에 문의하세요.

:::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="도움말 보기 탭의 지원 링크.":::

## <a name="next-steps"></a>다음 단계

- [CSP 파트너를 통한 재판매](cloud-solution-providers.md)
- [상업용 Marketplace 제품에 대한 공동 판매 구성](./co-sell-configure.md)
- 해당 [FAQ](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) 보기(PDF)
