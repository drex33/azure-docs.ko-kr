---
title: Azure Portal 설정 및 기본 설정 관리
description: 기본 구독/디렉터리, 시간 초과, 메뉴 모드, 대비, 테마, 알림, 언어/지역 등과 같은 Azure Portal 설정을 변경합니다.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 285afaaf51f28b0fa53a9df2a9fa232b01d3fe24
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528921"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Azure Portal 설정 및 기본 설정 관리

사용자의 기본 설정에 맞게 Azure Portal 기본 설정을 변경할 수 있습니다.

대부분의 설정은 전체 페이지 헤더의 오른쪽 상단 섹션에 있는 **설정** 메뉴에서 사용할 수 있습니다.

:::image type="content" source="media/set-preferences/settings-top-header.png" alt-text="전체 페이지 헤더의 설정 아이콘을 보여 주는 스크린샷.":::

> [!NOTE]
> 모든 사용자를 이 항목에 설명된 최신 설정 환경으로 이동하는 중입니다. 이전 환경에 대한 자세한 내용은 [Azure Portal 설정 및 기본 설정 관리(이전 버전)](original-preferences.md)를 참조하세요.

## <a name="directories--subscriptions"></a>디렉터리 + 구독

**디렉터리 + 구독** 페이지에서 디렉터리를 관리하고 구독 필터를 설정할 수 있습니다.

### <a name="switch-and-manage-directories"></a>디렉터리 전환 및 관리

**디렉터리** 섹션에 **현재 디렉터리**(현재 로그인되어 있음)가 표시됩니다.

**시작 디렉터리** 는 Azure Portal에 로그인할 때 기본 디렉터리를 표시합니다. 다른 시작 디렉터리를 선택하려면 **변경** 을 선택하여 이 옵션을 변경할 수 있는 [모양 + 시작 보기](#appearance--startup-views) 페이지로 이동합니다.

액세스 권한이 있는 전체 디렉터리 목록을 보려면 **모든 디렉터리** 를 선택합니다.

디렉터리를 즐겨찾기로 표시하려면 별표 아이콘을 선택합니다. 이러한 디렉터리는 **즐겨찾기** 섹션에 나열됩니다.

다른 디렉터리로 전환하려면 작업할 디렉터리를 선택한 다음 해당 행에서 **전환** 단추를 선택합니다.

:::image type="content" source="media/set-preferences/settings-directories-subscriptions-default-filter.png" alt-text="디렉터리 설정 창을 보여 주는 스크린샷.":::

### <a name="subscription-filters"></a>구독 필터

Azure Portal에 로그인할 때 기본적으로 필터링되는 구독을 선택할 수 있습니다. 작업하는 구독의 기본 목록이 있지만 다른 구독을 사용하는 경우에 유용할 수도 있습니다.

사용자 지정 필터를 사용하려면 **고급 필터** 를 선택합니다. 계속하기 전에 확인하라는 메시지가 표시됩니다.

:::image type="content" source="media/set-preferences/settings-advanced-filters-enable.png" alt-text="고급 필터에 대한 확인 대화 상자를 보여 주는 스크린샷.":::

그러면 여러 구독 필터를 만들고 관리할 수 있는 **고급 필터** 페이지가 사용하도록 설정됩니다. 현재 선택한 모든 구독은 다시 사용할 수 있는 가져온 필터로 저장됩니다. 고급 필터 사용을 중지하려면 토글을 다시 선택하여 기본 구독 보기를 복원합니다. 만든 모든 사용자 지정 필터는 저장되어 나중에 **고급 필터** 를 사용하도록 설정하면 사용할 수 있습니다.

:::image type="content" source="media/set-preferences/settings-advanced-filters-disable.png" alt-text="고급 필터를 사용하지 않도록 설정하기 위한 확인 대화 상자를 보여 주는 스크린샷.":::

## <a name="advanced-filters"></a>고급 필터

**고급 필터** 페이지에서 구독 필터를 만들기, 수정 또는 삭제할 수 있습니다.

:::image type="content" source="media/set-preferences/settings-advanced-filters.png" alt-text="고급 필터 화면을 보여 주는 스크린샷.":::

**기본** 필터는 액세스 권한이 있는 모든 구독을 표시합니다. 이 필터는 다른 필터가 없거나 활성 필터에 구독이 포함되지 않은 경우에 사용됩니다.

이전에 선택한 모든 구독을 포함하는 **가져온 필터** 라는 필터를 볼 수도 있습니다.

현재 사용 중인 필터를 변경하려면 **고급 필터** 드롭다운 상자에서 해당 필터를 선택합니다. 또한 **고급 필터 수정** 을 선택하여 **고급 필터** 페이지로 이동하여 필터를 만들기, 수정 및 삭제할 수 있습니다.

### <a name="create-a-filter"></a>필터 만들기

새 필터를 만들려면 **필터 만들기** 를 선택합니다. 최대 10개의 필터를 만들 수 있습니다.

각 필터에는 문자, 숫자 및 하이픈만 포함하는 8~50자 길이의 고유한 이름이 있어야 합니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create.png" alt-text="필터 만들기 옵션을 보여 주는 스크린샷.":::

필터 이름을 지정한 후 하나 이상의 조건을 입력합니다. **필터 유형** 필드에서 **구독 이름**, **구독 ID** 또는 **구독 상태** 를 선택합니다. 그런 다음 연산자를 선택하고 필터링할 값을 입력합니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create-operators.png" alt-text="필터 만들기를 위한 연산자 목록을 보여 주는 스크린샷":::.

조건 추가가 완료되면 **만들기** 를 선택합니다. 그러면 필터가 **활성 필터** 의 목록에 나타납니다.

### <a name="modify-or-delete-a-filter"></a>필터 수정 또는 삭제

해당 필터 행에서 연필 아이콘을 선택하여 기존 필터를 수정하거나 이름을 바꿀 수 있습니다. 변경한 다음 **적용** 을 선택합니다.

> [!NOTE]
> 현재 활성 상태인 필터를 수정하고 변경으로 인해 구독이 0개이면 **기본** 필터가 대신 활성 상태가 됩니다. 구독이 포함되지 않은 필터는 활성화할 수 없습니다.

필터를 삭제하려면 해당 필터 행에서 휴지통 아이콘을 선택합니다. **기본** 필터 또는 현재 활성화된 필터는 삭제할 수 없습니다.

## <a name="appearance--startup-views"></a>모양 + 시작 보기

**모양 + 시작 보기** 창에는 두 개의 섹션이 있습니다. **모양** 섹션에서는 메뉴 동작, 색상 테마, 고대비 테마 사용 여부를 선택할 수 있고 **시작 보기** 섹션에서는 먼저 Azure Portal에 로그인할 때 표시되는 항목에 대한 옵션을 설정할 수 있습니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-appearance.png" alt-text="모양 + 시작 보기의 모양 섹션을 보여 주는 스크린샷":::.

### <a name="set-menu-behavior"></a>메뉴 동작 설정

**메뉴 동작** 섹션에서 기본 Azure Portal 메뉴의 동작 방식을 선택할 수 있습니다.

- **플라이아웃**: 필요할 때까지 메뉴가 숨겨집니다. 왼쪽 상단의 메뉴 아이콘을 선택하여 메뉴를 열거나 닫을 수 있습니다.
- **고정됨**: 메뉴가 항상 표시됩니다. 메뉴를 축소하면 더 많은 작업 공간을 제공할 수 있습니다.

### <a name="choose-a-theme-or-enable-high-contrast"></a>테마 선택 또는 고대비 사용

선택한 테마는 Azure Portal에 표시되는 배경과 글꼴 색에 영향을 줍니다. **테마** 섹션에서 사전 설정된 4가지 색상 테마 중 하나를 선택할 수 있습니다. 각 썸네일을 선택하여 가장 적합한 테마를 찾습니다.

또는 **고대비 테마** 섹션에서 테마를 선택할 수 있습니다. 이러한 테마를 사용하면 특히 시각 장애가 있는 경우 Azure Portal을 더 쉽게 읽을 수 있습니다. 흰색 또는 검은색 고대비 테마를 선택하면 다른 테마 선택이 무시됩니다.

### <a name="startup-page"></a>시작 페이지

Azure Portal에 처음 로그인할 때 표시되는 페이지에 대해 다음 옵션 중 하나를 선택합니다.

- **홈**: 인기 있는 Azure 서비스에 대한 바로 가기, 가장 최근에 사용한 리소스 목록, 도구, 문서 등에 대한 유용한 링크가 있는 홈페이지를 표시합니다.
- **대시보드**: 가장 최근에 사용한 대시보드를 표시합니다. 대시보드를 사용자 지정하여 자신만의 작업 영역을 만들 수 있습니다. 예를 들어, 프로젝트, 작업 또는 역할에 중점을 둔 대시보드를 빌드할 수 있습니다. 자세한 내용은 [Azure Portal에서 대시보드 만들기 및 공유](azure-portal-dashboards.md)를 참조하세요.

### <a name="startup-directory"></a>시작 디렉터리

Azure Portal에 처음 로그인할 때 작업할 디렉터리에 대해 다음 옵션 중 하나를 선택합니다.

- **마지막으로 방문한 디렉터리에 로그인**: Azure Portal에 로그인하면 마지막으로 작업한 디렉터리에서 시작됩니다.
- **디렉터리 선택**: 디렉터리 중 하나를 선택하려면 이 옵션을 선택합니다. 이전에 다른 디렉터리에서 작업했더라도 Azure Portal에 로그인할 때마다 해당 디렉터리에서 시작합니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-startup-views.png" alt-text="모양 + 시작 보기의 시작 섹션을 보여 주는 스크린샷.":::

## <a name="language--region"></a>언어 + 지역

날짜 및 통화와 같은 데이터가 Azure Portal에 표시되는 방식에 영향을 줄 언어 및 지역 형식을 선택합니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-language-region.png" alt-text="언어 + 지역 설정 창을 보여 주는 스크린샷.":::

> [!NOTE]
> 이러한 언어 및 지역별 설정은 Azure Portal에만 영향을 줍니다. 새 탭 또는 창에서 열리는 설명서 링크는 브라우저의 설정을 사용하여 표시할 언어를 결정합니다.

### <a name="language"></a>언어

드롭다운 목록을 사용하여 사용 가능한 언어 목록에서 선택합니다. 이 설정은 Azure Portal 전반에 걸쳐 텍스트에 표시되는 언어를 제어합니다.

### <a name="regional-format"></a>사용지역 언어

Azure Portal에 날짜, 시간, 숫자 및 통화가 표시되는 방식을 제어하는 옵션을 선택합니다.

**지역 형식** 드롭다운 목록에 표시되는 옵션은 **언어** 에 대해 선택한 옵션에 따라 변경됩니다. 예를 들어 언어로 **영어** 를 선택한 다음 지역 형식으로 **영어(미국)** 를 선택하면 통화가 미국 달러 단위로 표시됩니다. 사용자의 언어로 **영어** 를 선택한 다음 지역 형식으로 **영어(유럽)** 를 선택하면 통화가 유로 단위로 표시됩니다.

**적용** 을 선택하여 언어 및 지역 형식 설정을 업데이트합니다.

## <a name="my-information"></a>내 정보

**내 정보** 페이지에서 Azure 서비스, 청구, 지원 또는 보안 문제에 대한 업데이트에 사용되는 이메일 주소를 업데이트할 수 있습니다. Microsoft Azure 및 기타 제품 및 서비스에 대한 추가 이메일을 수신하거나 수신 거부할 수도 있습니다.

**내 정보** 페이지 상단에 설정 내보내기, 복원 또는 삭제 옵션이 표시됩니다.

:::image type="content" source="media/set-preferences/settings-my-information.png" alt-text="내 정보 설정 페이지의 스크린샷.":::

### <a name="export-user-settings"></a>사용자 설정 내보내기

사용자 지정 설정에 대한 정보는 Azure에 저장됩니다. 다음 사용자 데이터를 내보낼 수 있습니다.

- Azure Portal의 프라이빗 대시보드
- 즐겨찾는 구독 또는 디렉터리와 같은 사용자 설정
- 테마 및 기타 사용자 지정 포털 설정

설정을 삭제하려는 경우 설정을 내보내고 검토하는 것이 좋습니다. 대시보드를 다시 빌드하거나 설정을 다시 실행하는 것은 시간이 많이 걸릴 수 있습니다.

포털 설정을 내보내려면 설정 **개요** 창 상단에서 **설정 내보내기** 를 선택합니다. 그러면 사용자 설정 데이터가 포함된 *.json* 파일이 만들어집니다.

사용자 설정의 동적 특성과 데이터 손상의 위험 때문에, *.json* 파일에서 설정을 가져올 수 없습니다.

### <a name="restore-default-settings"></a>기본 설정 복원

Azure Portal 설정을 변경한 후 취소하려면 설정 **개요** 창 상단에서 **기본 설정 복원** 을 선택합니다. 이 작업을 확인하는 메시지가 나타납니다. 이렇게 하면 Azure Portal 설정에 대한 모든 변경 사항이 손실됩니다. 이 옵션은 대시보드 사용자 지정에는 영향을 주지 않습니다.

### <a name="delete-user-settings-and-dashboards"></a>사용자 설정 및 대시보드 삭제

사용자 지정 설정에 대한 정보는 Azure에 저장됩니다. 다음 사용자 데이터를 삭제할 수 있습니다.

- Azure Portal의 프라이빗 대시보드
- 즐겨찾는 구독 또는 디렉터리와 같은 사용자 설정
- 테마 및 기타 사용자 지정 포털 설정

설정을 삭제하기 전에 설정을 내보내고 검토하는 것이 좋습니다. [대시보드](azure-portal-dashboards.md)를 다시 빌드하거나 사용자 지정 설정을 다시 실행하는 것은 시간이 많이 걸릴 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

포털 설정을 삭제하려면 **내 정보** 페이지 상단에서 **모든 설정 및 프라이빗 대시보드 삭제** 를 선택합니다. 삭제를 확인하라는 메시지가 표시됩니다. 그렇게 하면 모든 설정 사용자 지정이 기본 설정으로 돌아가고 모든 프라이빗 대시보드가 손실됩니다.

## <a name="signing-out--notifications"></a>로그아웃 + 알림

이 창에서 팝업 알림 및 세션 시간 제한을 관리할 수 있습니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-notifications.png" alt-text="로그아웃 + 알림 창을 보여 주는 스크린샷.":::

### <a name="signing-out"></a>로그아웃 중

비활성화 시간 제한 설정은 워크스테이션의 보안을 잊은 경우 리소스를 무단 액세스로부터 보호하는 데 도움이 됩니다. 잠시 유휴 상태가 되면 Azure Portal 세션에서 자동으로 로그아웃됩니다. 개별적으로 시간 제한 설정을 변경할 수 있습니다. 관리자인 경우 디렉터리의 모든 사용자에 대해 디렉터리 수준에서 설정할 수 있습니다.

### <a name="change-your-individual-timeout-setting-user"></a>개별 시간 제한 설정 변경(사용자)

**비활성 시 로그아웃** 옆의 드롭다운 메뉴에서 유휴 상태인 경우 Azure Portal 세션이 로그아웃되는 기간을 선택합니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive.png" alt-text="사용자 시간 제한 설정 옵션을 보여 주는 스크린샷.":::

**적용** 을 선택하여 변경 내용을 저장합니다. 그 후 포털 세션 동안 비활성 상태이면 Azure Portal은 설정한 기간 후에 로그아웃됩니다.

관리자가 비활성 시간 제한 정책을 사용하도록 설정한 경우 디렉터리 수준 설정보다 짧은 경우에도 사용자가 직접 설정할 수 있습니다. 이렇게 하려면 **디렉터리 비활동 시간 제한 정책 재정의** 를 선택한 다음 **값 재정의** 에 대한 시간 간격을 입력합니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-user.png" alt-text="디렉터리 비활성 시간 제한 정책 재정의 설정을 보여 주는 스크린샷.":::

### <a name="change-the-directory-timeout-setting-admin"></a>디렉터리 시간 제한 설정 변경(관리자)

[전역 관리자 역할](../active-directory/roles/permissions-reference.md#global-administrator)의 관리자는 세션이 로그아웃되기 전에 최대 유휴 시간을 적용할 수 있습니다. 비활성 시간 제한 설정은 디렉터리 수준에서 적용됩니다. 이 설정은 새 세션에 적용됩니다. 이미 로그인한 사용자에게는 즉시 적용되지 않습니다. 디렉터리에 대한 자세한 내용은 [Active Directory Domain Services 개요](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)를 참조하세요.

전역 관리자이고 Azure Portal의 모든 사용자에 대해 유휴 시간 제한 설정을 적용하려면 **디렉터리 수준 유휴 시간 제한 사용** 을 선택하여 설정을 켭니다. 그런 다음 세션이 자동으로 로그아웃되기 전에 사용자가 비활성 상태일 수 있는 최대 시간인 **시간** 및 **분** 을 입력합니다. **적용** 을 선택하면 이 설정이 디렉터리의 모든 사용자에게 적용됩니다.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-admin.png" alt-text="디렉터리 수준 유휴 시간 제한 옵션을 보여 주는 스크린샷":::.

비활성 시간 제한 정책이 올바르게 설정되었는지 확인하려면 전체 페이지 헤더에서 **알림** 을 선택하고 성공 알림이 나열되는지 확인합니다.

:::image type="content" source="media/set-preferences/confirmation.png" alt-text="성공적인 비활성 시간 제한 정책에 대한 알림을 보여 주는 스크린샷.":::

### <a name="enable-or-disable-pop-up-notifications"></a>팝업 알림 사용 또는 사용 안 함

알림은 현재 세션과 관련된 시스템 메시지입니다. 현재 크레딧 잔액 표시, 마지막 작업 확인 또는 리소스가 만들어진 시기 알림 등의 정보를 제공합니다. 팝업 알림이 설정되면 화면의 상단 모퉁이에 메시지가 잠깐 표시됩니다.

팝업 알림을 사용하거나 사용하지 않도록 설정하려면 **팝업 알림 사용** 을 선택하거나 선택 취소합니다.

현재 세션 중에 수신된 모든 알림을 읽으려면 전역 헤더에서 **알림** 을 선택합니다.

:::image type="content" source="media/set-preferences/read-notifications.png" alt-text="전체 헤더의 알림 아이콘을 보여 주는 스크린샷.":::

이전 세션에서 알림을 보려면 활동 로그에서 이벤트를 찾습니다. 자세한 내용은 [활동 로그 보기](../azure-monitor/essentials/activity-log.md#view-the-activity-log)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 바로 가기 키 알아보기](azure-portal-keyboard-shortcuts.md)
- [지원되는 브라우저 및 디바이스 보기](azure-portal-supported-browsers-devices.md)
- [즐겨찾기 추가, 제거 및 다시 정렬](azure-portal-add-remove-sort-favorites.md)
- [사용자 지정 대시보드 만들기 및 공유](azure-portal-dashboards.md)
- [Azure Portal 방법 동영상 시청](azure-portal-video-series.md)
