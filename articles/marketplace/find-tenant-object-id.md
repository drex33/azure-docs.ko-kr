---
title: Azure Marketplace에서 테넌트 ID, 개체 ID, 파트너 연결 정보 찾기
description: Azure Marketplace 구독 ID의 테넌트 ID, 개체 ID 및 파트너 연결 세부 정보를 찾습니다.
ms.service: marketplace
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 10/09/2020
ms.openlocfilehash: a71a60bd4f12a46456c3a2d0725642118b2acc40
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065296"
---
# <a name="find-tenant-id-object-id-and-partner-association-details"></a>테넌트 ID, 개체 ID, 파트너 연결 정보 찾기

이 문서에서는 테넌트 ID, 개체 ID, 파트너 연결 정보를 해당 구독 ID와 함께 찾는 방법을 설명합니다.

Azure Cloud Shell에서 디버깅 지원에 사용할 수 있도록 이러한 항목의 스크린샷이 필요한 경우 [디버깅을 위한 테넌트, 개체, 파트너 ID 연결 찾기](#find-ids-for-debugging)를 참조하세요.

>[!Note]
> 구독 소유자에게만 이러한 단계를 수행할 수 있는 권한이 있습니다.

## <a name="find-tenant-id"></a>테넌트 ID 찾기

1. [Azure Portal](https://ms.portal.azure.com/)로 이동합니다.
2. **Azure Active Directory** 를 선택합니다.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-ad.png" alt-text="Azure Portal Azure Active Directory 아이콘입니다.":::

3. **개요** 를 선택합니다. 테넌트 ID는 **기본 정보** 에 표시되어야 합니다.

    :::image type="content" source="media/tenant-and-object-id/select-groups-1.png" alt-text="Azure Portal 그룹을 선택합니다.":::

## <a name="find-subscriptions-and-roles"></a>구독 및 역할 찾기

1. Azure Portal로 이동하여 위의 1단계와 2단계에서 설명한 대로 **Azure Active Directory** 를 선택합니다.
2. **구독** 을 선택합니다.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-subscriptions-1.png" alt-text="Azure Portal 구독 아이콘입니다.":::

3. 구독 및 역할 보기

    :::image type="content" source="media/tenant-and-object-id/subscriptions-screen-1.png" alt-text="Azure Portal 구독 화면":::

## <a name="find-partner-id"></a>파트너 ID 찾기

1. 이전 섹션에서 설명한 대로 구독 페이지로 이동합니다.
2. 구독을 선택합니다.
3. **청구** 에서 **파트너 정보** 를 선택합니다.

    :::image type="content" source="media/tenant-and-object-id/menu-partner-information.png" alt-text="왼쪽 탐색 메뉴의 파트너 정보":::

## <a name="find-user-object-id"></a>사용자 찾기(개체 ID)

1. 적절한 관리 권한을 사용하여 원하는 테넌트의 계정으로 [Office 365 관리 포털](https://portal.office.com/adminportal/home)에 로그인합니다.
2. 왼쪽 메뉴에서 아래쪽의 **관리 센터** 섹션을 펼친 다음, Azure Active Directory 옵션을 선택하면 새 브라우저 창에 관리 콘솔이 열립니다.
3. **사용자** 를 선택합니다.
4. 원하는 사용자를 찾거나 검색한 다음, 계정 이름을 선택하여 사용자 계정의 프로필 정보를 확인합니다.
5. 개체 ID는 오른쪽의 ID 섹션에 있습니다.

    :::image type="content" source="media/tenant-and-object-id/azure-ad-admin-center.png" alt-text="Azure Active Directory 관리 센터":::

6. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택한 다음, **역할 할당** 을 선택하여 **역할 할당** 을 찾습니다.

    :::image type="content" source="../role-based-access-control/media/role-assignments-portal/rg-role-assignments.png" alt-text="Azure 리소스에 대한 역할 할당":::


## <a name="find-ids-for-debugging"></a>디버깅을 위한 ID 찾기

이 섹션에서는 디버깅을 위해 테넌트, 개체, 파트너 ID 연결을 찾는 방법을 설명합니다.

1. [Azure Portal](https://ms.portal.azure.com/)로 이동합니다.
2. 오른쪽 상단에 있는 PowerShell 아이콘을 선택하여 Azure Cloud Shell을 엽니다.

    :::image type="content" source="media/tenant-and-object-id/icon-azure-cloud-shell-1.png" alt-text="화면 오른쪽 상단에 있는 PowerShell 아이콘":::

3. **PowerShell** 을 선택합니다.

    :::image type="content" source="media/tenant-and-object-id/icon-powershell.png" alt-text="PowerShell 링크를 선택합니다.":::

4. **구독** 상자를 선택하여 파트너가 연결된 구독 상자를 선택한 다음, **스토리지 만들기** 를 선택합니다. 일회성 작업이므로 스토리지가 이미 설정되어 있는 경우 다음 단계로 진행합니다.

    :::image type="content" source="media/tenant-and-object-id/create-storage-window.png" alt-text="스토리지 만들기 단추를 선택합니다.":::

5. 스토리지를 만들거나 스토리지가 이미 있으면 Azure Cloud Shell 창이 열립니다.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-1.png" alt-text="Azure Cloud Shell 창":::

6. 파트너 연결 세부 정보의 경우, 다음 명령을 통해 확장을 설치합니다.<br>`az extension add --name managementpartner`.<br>확장이 이미 설치되어 있는 경우 Azure Cloud Shell에 다음과 같은 메시지가 나타납니다.

    :::image type="content" source="media/tenant-and-object-id/cloud-shell-window-2.png" alt-text="확장이 이미 설치되어 있음을 보여 주는 Azure Cloud Shell 창":::

7. 다음 명령을 사용하여 파트너 세부 정보를 확인합니다.<br>`az managementpartner show --partner-id xxxxxx`<br>예: `az managementpartner show --partner-id 4760962`.<br>다음과 유사한 명령 결과의 스크린샷을 찍습니다.

    :::image type="content" source="media/tenant-and-object-id/partner-id-sample-screenshot.png" alt-text="파트너 ID를 보기 위한 이전 명령의 결과를 보여 주는 샘플 화면":::

>[!NOTE]
>여러 구독에 스크린샷이 필요한 경우 다음 명령을 사용하여 구독 간에 전환합니다.<br>`az account set --subscription "My Demos"`

## <a name="next-steps"></a>다음 단계

- [지원되는 국가 및 지역](sell-from-countries.md)