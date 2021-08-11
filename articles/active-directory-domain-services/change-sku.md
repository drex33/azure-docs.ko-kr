---
title: Azure AD Domain Services의 SKU 변경 | Microsoft Docs
description: 비즈니스 요구 사항이 변경되는 경우 Azure AD Domain Services 관리되는 도메인의 SKU 계층에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 2bdf660d57f4fa8cb3a804ff55028dc442f96b8b
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110786139"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>기존 Azure Active Directory Domain Services 관리되는 도메인의 SKU 변경

Azure AD DS(Azure Active Directory Domain Services)에서 사용할 수 있는 성능 및 기능은 SKU 유형을 기반으로 합니다. 이러한 기능 차이에는 백업 빈도 또는 최대 단방향 아웃바운드 포리스트 트러스트의 수 등이 있습니다.

관리되는 도메인을 만들 때 SKU를 선택할 수 있으며, 관리되는 도메인을 배포한 후 비즈니스 요구 사항이 변경됨에 따라 SKU를 위 또는 아래로 전환할 수 있습니다. 비즈니스 요구 사항의 변경에는 더 자주 백업해야 하는 필요성 또는 추가 포리스트 트러스트를 만들어야 하는 필요성 등이 있습니다. 다른 SKU의 제한 및 가격 책정에 대한 자세한 내용은 [Azure AD DS SKU 개념][concepts-sku] 및 [Azure AD DS SKU 가격 책정][pricing] 페이지를 참조하세요.

이 문서에서는 Azure Portal을 사용하여 기존 Azure AD DS 관리되는 도메인의 SKU를 변경하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서의 단계를 완료하여 [관리되는 도메인을 만들고 구성][create-azure-ad-ds-instance]합니다.

## <a name="sku-change-limitations"></a>SKU 변경 제한 사항

관리되는 도메인을 배포한 후 SKU를 위아래로 변경할 수 있습니다. 그러나 리소스 포리스트를 사용하고 Azure AD DS에서 온-프레미스 AD DS 환경으로의 단방향 아웃바운드 포리스트 트러스트를 만든 경우 SKU 변경 작업에 대한 몇 가지 제한 사항이 있습니다. *프리미엄* 및 *엔터프라이즈* SKU는 만들 수 있는 트러스트의 수에 대한 제한을 정의합니다. 현재 구성된 것 보다 낮은 최대 제한이 있는 SKU로는 변경할 수 없습니다.

예를 들면 다음과 같습니다.

* ‘표준’ SKU로 변경할 수 없습니다. Azure AD DS 리소스 포리스트는 ‘표준’ SKU를 지원하지 않습니다. 
* 또는 *프리미엄* SKU에서 7개의 포리스트 트러스트를 만든 경우 *엔터프라이즈* SKU로 낮춰 변경할 수 없습니다. *엔터프라이즈* SKU는 최대 5개의 트러스트를 지원합니다.

이러한 제한에 대한 자세한 내용은 [Azure AD DS SKU 기능 및 제한][concepts-sku]을 참조하세요.

## <a name="select-a-new-sku"></a>새 SKU 선택

Azure Portal을 사용하여 관리되는 도메인의 SKU를 변경하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다. 목록에서 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. Azure AD DS 페이지의 왼쪽에 있는 메뉴에서 **설정 > SKU** 를 선택합니다.

    ![Azure Portal에서 Azure AD DS 관리되는 도메인의 SKU 메뉴 옵션을 선택합니다.](media/change-sku/overview-change-sku.png)

1. 드롭다운 메뉴에서 관리되는 도메인에 대해 원하는 SKU를 선택합니다. 리소스 포리스트가 있는 경우 *엔터프라이즈* SKU 이상에서만 포리스트 트러스트를 사용할 수 있으므로 *표준* SKU는 선택할 수 없습니다.

    드롭다운 메뉴에서 원하는 SKU를 선택하고 **저장** 을 선택합니다.

    ![Azure Portal의 드롭다운 메뉴에서 필요한 SKU를 선택합니다.](media/change-sku/change-sku-selection.png)

SKU 유형을 변경하는 데 1~2분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

리소스 포리스트가 있고 SKU 변경 후 추가 트러스트를 만들려면 [Azure AD DS에서 온-프레미스 도메인에 아웃바운드 포리스트 트러스트 만들기][create-trust]를 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
