---
title: Azure Marketplace의 SaaS 제품에 대한 기술 세부 정보 추가
description: Azure Marketplace의 SaaS(software as a service) 제품에 대한 기술 세부 정보를 제공합니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 7efbb49565027ed7b982d37e99a27e1d66c59bf5
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728098"
---
# <a name="add-technical-details-for-a-saas-offer"></a>SaaS 제품에 대한 기술 세부 정보 추가

이 문서에서는 Microsoft 상업용 Marketplace를 솔루션에 연결하는 데 도움이 되는 기술 세부 정보를 입력하는 방법을 설명합니다. 고객이 구매 및 관리하기로 선택한 경우 이 연결을 통해 고객을 위한 제품을 프로비저닝할 수 있습니다. 이러한 설정에 대한 자세한 내용은 [기술 정보](plan-saas-offer.md#technical-information)를 참조하세요.

> [!NOTE]
> 트랜잭션을 독립적으로 처리하도록 선택하면 이 옵션이 표시되지 않습니다. 대신, [SaaS 제품을 판매하는 방법](create-new-saas-offer-marketing.md)으로 건너뜁니다.

## <a name="technical-configuration"></a>기술 구성

**기술 구성** 탭에서는 상업용 Marketplace에서 SaaS 애플리케이션 또는 솔루션과 통신하는 데 사용하는 기술 세부 정보를 정의합니다. 

- **방문 페이지 URL**(필수) - 고객이 상업용 Marketplace에서 제품을 구매하고 새로 만든 SaaS 구독에서 구성 프로세스를 트리거한 후 이동하게 될 SaaS 웹 사이트 URL(예: `https://contoso.com/signup`)을 정의합니다.

  > [!IMPORTANT]
  > 방문 페이지를 가동하여 연중무휴 실행해야 합니다. 이것이 상업용 Marketplace에서 이루어지는 SaaS 제품의 신규 구매 또는 제품 활성 구독의 구성 요청에 대한 통지를 받을 수 있는 유일한 방법입니다.

- **Connection webhook** (필수) – Microsoft에서 사용자에 게 전송 해야 하는 모든 비동기 이벤트 (예: SaaS 구독이 취소 됨)에 대해 [연결 webhook URL을 제공](./partner-center-portal/pc-saas-fulfillment-api-v2.md#implementing-a-webhook-on-the-saas-service)해야 합니다. Microsoft는 이 URL을 호출하여 파트너에게 이벤트를 알립니다.

  > [!IMPORTANT]
  > 웹후크는 상업용 Marketplace를 통해 구매한 고객의 SaaS 구독의 업데이트에 대한 알림을 받을 수 있는 유일한 방법이기 때문에 연중무휴 운영해야 합니다.

- **Azure Active Directory 테넌트 ID**(필수) - Azure AD(Azure Active Directory) 앱에 대한 테넌트 ID를 찾으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동합니다. **표시 이름** 열에서 앱을 선택합니다. 그런 다음, 나열된 **디렉터리(테넌트) ID** 번호를 찾습니다(예: `50c464d3-4930-494c-963c-1e951d15360e`).

- **Azure Active Directory 애플리케이션 ID**(필수) - [애플리케이션 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 찾으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동합니다. **표시 이름** 열에서 앱을 선택합니다. 그런 다음, 나열된 애플리케이션(클라이언트) ID 번호를 찾습니다(예: `50c464d3-4930-494c-963c-1e951d15360e`).

다음 탭(플랜 개요)으로 진행하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [SaaS 제품에 대한 플랜을 만듭니다](create-new-saas-offer-plans.md).