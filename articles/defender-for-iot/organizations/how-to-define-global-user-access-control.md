---
title: 전역 사용자 액세스 제어 정의
description: 대규모 조직의 경우 사용자 권한은 복잡할 수 있으며 표준 사이트 및 영역 구조 외에도 전역 조직 구조에 의해 결정될 수 있습니다.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 70437ceeadba0b1c8af9559037ccf718d7a251d4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283831"
---
# <a name="define-global-access-control"></a>전역 액세스 제어 정의

대규모 조직의 경우 사용자 권한은 복잡할 수 있으며 표준 사이트 및 영역 구조 외에도 전역 조직 구조에 의해 결정될 수 있습니다.

전역적이고 보다 복잡한 사용자 액세스 권한에 대한 요구를 지원하기 위해, 비즈니스 단위, 지역 및 사이트를 기반으로 하는 전역 비즈니스 토폴로지를 만들 수 있습니다. 그런 다음 해당 엔터티에 대해 사용자 액세스 권한을 정의할 수 있습니다.

비즈니스 토폴로지용 액세스 도구를 사용하면 조직이 Microsoft Defender for IoT 플랫폼에서 디바이스를 관리하고 분석하는 위치를 더 잘 제어하여 제로 트러스트 전략을 구현할 수 있습니다.

## <a name="about-access-groups"></a>액세스 그룹 정보

전역 액세스 제어는 사용자 액세스 그룹을 만드는 방법으로 설정됩니다. 액세스 그룹은 어떤 사용자가 특정 비즈니스 엔터티에 액세스할 수 있는지에 관한 규칙으로 구성됩니다. 그룹을 통해 관련 비즈니스 단위, 지역, 사이트의 특정 사용자 규칙에 대한 Defender for IoT에 대한 보기 및 구성을 제어할 수 있습니다.

예를 들어 한 Active Directory 그룹의 보안 분석가가 모든 서유럽 자동차 및 유리 제작 라인에 액세스하고, 한 지역의 플라스틱 라인에 액세스하도록 허용할 수 있습니다.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="보안 분석가 Active Directory 그룹의 다이어그램입니다.":::

액세스 그룹을 만들기 전에 다음을 수행하는 것이 좋습니다.

- 비즈니스 토폴로지를 신중하게 설정합니다. 비즈니스 토폴로지에 대한 자세한 내용은 [사이트 맵 보기 작업](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)을 참조하세요.

- 만드는 액세스 그룹에 어떤 사용자를 연결할지 계획합니다. 사용자를 액세스 그룹에 할당하는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

  - **Active Directory 그룹에 대한 그룹 할당**: 온-프레미스 관리 콘솔과 통합되도록 Active Directory 인스턴스를 설정했는지 확인하세요.
  
  - **로컬 사용자 할당**: 사용자를 만들었는지 확인하세요. 자세한 내용은 [사용자 정의](how-to-create-and-manage-users.md#define-users)를 참조하세요.

관리 사용자는 액세스 그룹에 할당할 수 없습니다. 관리 사용자는 기본적으로 모든 비즈니스 토폴로지 엔터티에 액세스할 수 있습니다.

## <a name="create-access-groups"></a>액세스 그룹 만들기

이 섹션에서는 액세스 그룹을 만드는 방법을 설명합니다. 처음으로 그룹을 만들면 해당 그룹에 대한 기본 전역 비즈니스 단위 및 지역이 생성됩니다. 첫 번째 그룹을 정의할 때 해당 기본 엔터티를 편집할 수 있습니다.

그룹을 만들려면 다음을 수행합니다.

1. 온-프레미스 관리 콘솔의 측면 메뉴에서 **액세스 그룹** 을 선택합니다.

2. :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::를 선택합니다. **액세스 그룹 추가** 대화 상자에서 액세스 그룹의 이름을 입력합니다. 콘솔은 64자까지 지원합니다. 다른 그룹과 쉽게 구분할 수 있는 그룹 이름을 할당합니다.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="액세스 그룹을 만들 수 있는 액세스 그룹 추가 대화 상자입니다.":::

3. **Active Directory 그룹 할당** 옵션이 표시되면 해당 액세스 그룹에 Active Directory 사용자 그룹 하나를 할당할 수 있습니다.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="액세스 그룹 만들기 대화 상자에서 Active Directory 그룹을 할당합니다.":::

   이 옵션이 표시되지 않고, 액세스 그룹에 Active Directory 그룹을 포함하려는 경우 **시스템 설정** 을 선택합니다. **통합** 창에서 그룹을 정의합니다. Active Directory 구성에 표시된 대로 정확하게 그룹 이름을 소문자로 입력합니다.

5. **사용자** 창에서 그룹에 필요한 만큼 사용자를 할당합니다. 다른 그룹에 사용자를 할당할 수도 있습니다. 해당 방식으로 작업하는 경우 액세스 그룹 및 규칙을 만들어 저장한 다음 **사용자** 창에서 사용자를 그룹에 할당해야 합니다.

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="사용자의 역할을 관리하고 필요에 따라 할당합니다.":::

6. 비즈니스 토폴로지의 액세스 요구 사항에 따라 **‘이름’에 대한 규칙 추가** 대화 상자에서 규칙을 만듭니다. 여기에 표시되는 옵션은 **기업 보기** 및 **사이트 관리** 창에서 디자인한 토폴로지를 기반으로 합니다. 

   그룹당 하나 이상의 규칙을 만들 수 있습니다. 여러 사이트에서 복잡한 액세스 세분성을 사용하여 작업하는 경우 그룹당 두 개 이상의 규칙을 만들어야 할 수도 있습니다. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="시스템에 규칙을 추가합니다.":::

만든 규칙은 **액세스 그룹 추가** 대화 상자에 나타납니다. 여기에서 규칙을 삭제하거나 편집할 수 있습니다.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="이 창에서 모든 액세스 그룹을 보고 편집합니다.":::

### <a name="about-rules"></a>규칙 정보

규칙을 만들 때는 다음 정보에 유의하세요.

- 액세스 그룹에 여러 규칙이 포함된 경우 규칙 논리는 모든 규칙을 집계합니다. 예를 들어 규칙은 OR 논리가 아닌 AND 논리를 사용합니다.

- 규칙을 성공적으로 적용시키려면 **사이트 관리** 창에서 영역에 센서를 할당해야 합니다.

- 요소는 규칙당 하나만 할당할 수 있습니다. 예를 들어 각 규칙에 대해 하나의 비즈니스 단위, 하나의 지역, 하나의 사이트를 할당할 수 있습니다. 예를 들어 한 Active Directory 그룹의 사용자가 다른 지역의 다른 비즈니스 단위에 대한 액세스 권한을 갖도록 하려는 경우 그룹에 대해 더 많은 규칙을 만들어야 합니다.

- 엔터티 변경 사항이 규칙 논리에 영향을 주는 경우 해당 규칙은 삭제됩니다. 토폴로지 엔터티 변경 사항이 규칙 논리에 영향을 주는 경우 그러한 모든 규칙이 삭제됩니다. 액세스 그룹은 유지되지만 사용자는 온-프레미스 관리 콘솔에 로그인할 수 없게 됩니다. 사용자에게는 로그인하려면 관리자에게 문의하라는 알림이 표시됩니다.

- 비즈니스 단위 또는 지역이 선택되지 않은 경우 사용자는 정의된 모든 비즈니스 단위 및 지역에 액세스할 수 있습니다.

## <a name="see-also"></a>참고 항목

[Defender for IoT 콘솔 사용자 정보](how-to-create-and-manage-users.md)
