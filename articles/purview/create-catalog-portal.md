---
title: '빠른 시작: Azure Portal에서 Azure Purview 계정 만들기'
description: 이 빠른 시작에서는 Azure Purview 계정을 만들고, 사용을 시작할 수 있는 권한을 구성하는 방법을 설명합니다.
author: nayenama
ms.author: nayenama
ms.date: 08/18/2021
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-portal
ms.openlocfilehash: 4f0ef5010a0862b1fa5514d83f6570eefa2c4e10
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102536"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Purview 계정 만들기

Azure Purview는 데이터 환경을 관리하는 데 도움이 되는 통합 데이터 거버넌스 도구입니다. 이 빠른 시작에서는 Azure Portal에서 Azure Purview 계정을 만들고 Purview에서 데이터 분류, 보안 및 검색 프로세스를 시작하기 위한 단계에 대해 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Azure 로그인에 사용되는 사용자 계정은 Azure 구독의 기여자, 소유자 또는 관리자여야 합니다.

* 사용자 고유의 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* **스토리지 계정** 또는 **이벤트 허브 네임스페이스** 만들기를 방해하는 [Azure 정책](../governance/policy/overview.md)이 없습니다. Purview는 생성될 때 관리형 스토리지 계정 및 이벤트 허브를 배포합니다. 차단 정책이 존재하고, 이를 유지해야 하는 경우, [Purview 예외 태그 가이드](create-purview-portal-faq.md)에 따라 환경을 준비합니다

## <a name="create-an-azure-purview-account"></a>Azure Purview 계정 만들기

1. [Azure Portal](https://portal.azure.com)에서 **Purview 계정** 페이지로 이동합니다.

    :::image type="content" source="media/create-catalog-portal/purview-accounts-page.png" alt-text="Azure Portal의 Purview 계정 페이지를 보여주는 스크린샷":::

1. **만들기** 를 선택하여 새 Azure Purview 계정을 만듭니다.

   :::image type="content" source="media/create-catalog-portal/select-create.png" alt-text="Azure Portal에서 Azure Purview가 강조 표시된 만들기 단추 스크린샷입니다.":::
  
      또는 대신 마켓플레이스로 이동하고 **Azure Purview** 를 검색하고 **만들기** 를 선택합니다.

     :::image type="content" source="media/create-catalog-portal/search-marketplace.png" alt-text="Azure 마켓플레이스에서 만들기 단추가 강조 표시된 Azure Purview를 보여주는 스크린샷입니다.":::

1. 새로운 Purview 계정 만들기 페이지의 **기본** 탭에서 Purview 계정을 만들려는 Azure 구독을 선택합니다.

1. 기존 **리소스 그룹** 을 선택하거나 Purview 계정을 저장할 새 그룹을 만듭니다.

    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group) 문서를 참조하세요.

1. **Purview 계정 이름** 을 입력합니다. 공백과 기호는 허용되지 않습니다.
    Purview 계정의 이름은 전역적으로 고유해야 합니다. 다음 오류가 표시되면 Purview 계정의 이름을 변경하고 다시 만들어 보세요.

    :::image type="content" source="media/create-catalog-portal/name-error.png" alt-text="이미 사용 중인 계정 이름으로 Purview 계정 만들기 화면을 보여주는 스크린샷입니다. 오류 메시지가 강조 표시되어 있습니다.":::

1. **위치** 를 선택합니다.
    Purview를 지원하는 위치만 목록에 표시됩니다. 선택한 위치는 Purview 계정 및 메타 데이터가 저장되는 지역입니다. 소스는 다른 지역에 저장될 수 있습니다.

      > [!Note]
      > Azure Purview는 지역 간 계정 이동을 지원하지 않으므로, 올바른 지역에 배포해야 합니다. 이에 대한 자세한 내용은 [리소스에 대한 이동 작업 지원](../azure-resource-manager/management/move-support-resources.md)을 참조하세요.

1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다. 만들기를 완료하는 데 몇 분 정도 걸립니다. 새로 만든 Azure Purview 계정 인스턴스는 **Purview 계정** 페이지에서 목록에 표시됩니다.

    :::image type="content" source="media/create-catalog-portal/create-resource.png" alt-text="검토 + 만들기 단추가 강조 표시된 Purview 계정 만들기 화면을 보여주는 스크린샷":::

## <a name="open-purview-studio"></a>Purview Studio 열기

Azure Purview 계정이 생성된 후 Purview Studio를 사용하여 계정을 액세스 및 관리합니다. Purview Studio는 두 가지 방법으로 열 수 있습니다.

* [Azure Portal](https://portal.azure.com)에서 Purview 계정을 엽니다. 개요 페이지에서 "Purview Studio 열기" 타일을 선택합니다.
    :::image type="content" source="media/create-catalog-portal/open-purview-studio.png" alt-text="Purview Studio 타일이 강조 표시된 Purview 계정 개요 페이지를 보여주는 스크린샷입니다.":::

* 또는 [https://web.purview.azure.com](https://web.purview.azure.com)으로 이동하고, Purview 계정을 선택하고, 작업 영역에 로그인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Purview 계정을 만들고 Purview Studio를 통해 계정에 액세스하는 방법을 배웠습니다.

Purview Studio를 탐색하고, 컬렉션을 만들고, Purview에 대해 액세스 권한을 부여하는 방법을 알아보려면 다음 문서를 참조하세요.

* [Purview Studio 사용](use-purview-studio.md)
* [컬렉션 만들기](quickstart-create-collection.md)
* [Azure Purview 계정에 사용자 추가](catalog-permissions.md)
