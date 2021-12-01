---
title: 포함 파일
description: 포함 파일
services: load-testing
author: ntrogh
ms.service: load-testing
ms.author: nicktrog
ms.custom: include file
ms.topic: include
ms.date: 11/30/2021
ms.openlocfilehash: 70a5342285bba1afc11f776d923997b11eee6a13
ms.sourcegitcommit: 8152290a8817d0882035f7f3f1fd56b80f87dcda
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133316109"
---
1. Azure 구독에 대한 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 포털의 왼쪽 위 모서리에 있는 메뉴 단추를 선택한 다음 **+ 리소스 만들기** 를 선택 합니다.

    :::image type="content" source="media/azure-load-testing-create-in-portal/create-resource.png" alt-text="+ 리소스 만들기를 보여주는 스크린샷.":::

1. 검색 표시줄을 사용 하 여 **Azure 부하 테스트** 를 찾을 수 있습니다.

1. **Azure 부하 테스트** 를 선택 합니다.

1. **Azure 부하 테스트** 창에서 **만들기** 를 선택 합니다.

    :::image type="content" source="media/azure-load-testing-create-in-portal/create-azure-load-testing.png" alt-text="Azure 부하 테스트 만들기 페이지를 보여 주는 스크린샷":::

1. 새 Azure 부하 테스트 리소스를 구성 하려면 다음 정보를 제공 합니다.

    |필드  |설명  |
    |---------|---------|
    |구독     | 이 Azure 부하 테스트 리소스에 사용 하려는 Azure 구독을 선택 합니다.         |
    |리소스 그룹     | 기존 리소스 그룹을 선택 하거나 **새로 만들기** 를 선택한 다음 새 리소스 그룹에 대 한 고유한 이름을 입력 합니다.        |
        |이름     | Azure 부하 테스트 리소스를 식별 하는 고유한 이름을 입력 합니다.<br>이름에는 \\ /"" []: \|<>+ =;,? * @ & 또는 공백과 같은 특수 문자를 사용할 수 없습니다. 이름은 ' _ '로 시작 하거나 '. ' 또는 '-'로 끝날 수 없습니다. 길이는 1 ~ 007e; 64 자 사이 여야 합니다.     |
    |위치     | Azure 부하 테스트 리소스를 호스트할 지리적 위치를 선택 합니다.        |

    :::image type="content" source="media/azure-load-testing-create-in-portal/create-azure-load-testing-basics.png" alt-text="Azure 부하 테스트 리소스를 만들 때의 기본 탭을 보여 주는 스크린샷":::

    > [!NOTE]
    > 필요에 따라 **태그** 탭에서 자세한 내용을 구성할 수 있습니다. 태그는 여러 리소스 및 리소스 그룹에 동일한 태그를 적용 하 여 리소스를 범주화 하 고 통합 된 청구를 볼 수 있는 이름/값 쌍입니다.

1. 리소스 구성을 완료 한 후 **검토 + 만들기** 를 선택 합니다.

1. 모든 구성 설정을 검토 하 고 **만들기** 를 선택 하 여 Azure 부하 테스트 리소스의 배포를 시작 합니다. 
    
    프로세스가 완료 되 면 배포 성공 메시지가 표시 됩니다.

1. 새 리소스를 보려면 **리소스로 이동** 을 선택 합니다.
    
    :::image type="content" source="media/azure-load-testing-create-in-portal/create-azure-load-testing-goto-resource.png" alt-text="배포 완료 화면을 보여 주는 스크린샷":::

1. Azure 부하 테스트 리소스 페이지에서 **Access Control (IAM)** 을 선택 하 고 **역할 할당 추가** 를 선택 합니다.

    Azure 부하 테스트 리소스에서 부하 테스트를 관리 하려면 올바른 액세스 권한이 있어야 합니다.

    :::image type="content" source="media/azure-load-testing-create-in-portal/load-test-access-control.png" alt-text="액세스 제어를 구성 하는 방법을 보여 주는 스크린샷":::

    **부하 테스트 참가자** 또는 **부하 테스트 소유자** 역할을 Azure 계정에 할당 해야 합니다. 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당](/azure/role-based-access-control/role-assignments-portal)을 참조 하세요.

    :::image type="content" source="media/azure-load-testing-create-in-portal/add-role-assignment.png" alt-text="역할 할당 화면을 보여 주는 스크린샷":::

    > [!IMPORTANT]
    > Azure 역할을 할당 하려면 `Microsoft.Authorization/roleAssignments/write` [사용자 액세스 관리자](/azure/role-based-access-control/built-in-roles#user-access-administrator) 또는 [소유자](/azure/role-based-access-control/built-in-roles#owner)와 같은 사용 권한이 있어야 합니다.
    > 계정에 대해 역할 할당을 활성화 하는 데 몇 분 정도 걸릴 수 있습니다. 업데이트 된 사용 권한을 반영 하도록 사용자 인터페이스에 대 한 웹 페이지를 새로 고칩니다.
