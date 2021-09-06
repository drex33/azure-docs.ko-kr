---
title: Azure Virtual Desktop 관리 문제 - Azure
description: Azure Virtual Desktop의 일반적인 관리 문제와 이 문제를 해결하는 방법입니다.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 529cf643cb68bc806aef991142e68f42fccc0d42
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112420697"
---
# <a name="management-issues"></a>관리 문제

이 문서에서는 일반적인 관리 오류를 설명하고 이를 해결하는 방법에 대한 제안을 제공합니다.

## <a name="common-management-errors"></a>일반적인 관리 오류

다음 표에는 관리 관련 문제로 인해 나타나는 오류 메시지와 이 문제를 해결하는 방법에 대한 제안이 나와 있습니다.

|오류 메시지|추천 솔루션|
|---|---|
|등록 키 생성 실패 |등록 토큰을 만들 수 없습니다. 더 짧은 만료 시간(1시간에서 1개월 사이)으로 다시 만들어 보세요. |
|등록 키 삭제 실패|등록 토큰을 삭제할 수 없습니다. 다시 삭제해 보세요. 여전히 작동하지 않는 경우 PowerShell을 사용하여 토큰이 아직 있는지 확인합니다. 있는 경우 PowerShell을 사용하여 삭제합니다.|
|세션 호스트 드레이닝 모드를 변경하지 못함 |VM에서 드레이닝 모드를 변경할 수 없습니다. VM 상태를 확인합니다. VM을 사용할 수 없는 경우 드레이닝 모드를 변경할 수 없습니다.|
|사용자 세션의 연결을 끊지 못함 |VM에서 사용자의 연결을 끊을 수 없습니다. VM 상태를 확인합니다. VM을 사용할 수 없는 경우 사용자 세션의 연결을 끊을 수 없습니다. VM을 사용할 수 있는 경우 사용자 세션 상태를 확인하여 연결이 끊어졌는지 확인합니다. |
|세션 호스트 내의 모든 사용자를 로그오프하지 못함 |사용자를 VM에서 로그아웃할 수 없습니다. VM 상태를 확인합니다. 사용할 수 없는 경우 사용자를 로그아웃할 수 없습니다. 사용자 세션 상태를 확인하여 이미 로그아웃되었는지 확인합니다. PowerShell을 사용하여 강제로 로그아웃할 수 있습니다. |
|애플리케이션 그룹에서 사용자의 할당을 취소하지 못함|사용자에 대한 앱 그룹의 게시를 취소할 수 없습니다. Azure AD에서 사용자가 사용할 수 있는지 확인합니다. 사용자가 앱 그룹이 게시된 사용자 그룹에 일부인지 확인합니다. |
|사용 가능한 위치를 검색하는 동안 오류가 발생했습니다. |호스트 풀 만들기 마법사에서 사용되는 VM의 위치를 확인합니다. 해당 위치에서 이미지를 사용할 수 없는 경우 해당 위치에 이미지를 추가하거나 다른 VM 위치를 선택합니다. |

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>오류: 앱 그룹에 사용자 할당을 추가할 수 없음

앱 그룹에 사용자를 할당한 후 Azure Portal에 "세션 종료" 또는 "인증 문제 발생 - 확장 Microsoft_Azure_WVD"라는 경고가 표시됩니다. 그러면 할당 페이지가 로드되지 않고, 그 후에 페이지는 Azure Portal(예: Azure Monitor, Log Analytics, Service Health 등) 전체에서 로드가 중지됩니다.

이 문제는 일반적으로 조건부 액세스 정책에 문제가 있기 때문에 나타납니다. Azure Portal은 SharePoint Online에 종속된 Microsoft Graph에 대한 토큰을 가져오려고 합니다. 고객에게는 사용자가 데이터 스토리지에 액세스하는 데 필요한 사용 약관에 동의해야 하는 "Microsoft Office 365 데이터 스토리지 사용 약관"이라는 조건부 액세스 정책이 있습니다. 그러나 아직 로그인하지 않았으므로 Azure Portal에서 토큰을 가져올 수 없습니다.

이 문제를 해결하려면 Azure Portal에 로그인하기 전에 먼저 관리자가 SharePoint에 로그인하고 사용 약관에 동의해야 합니다. 그 후에는 평상시와 같이 Azure Portal에 로그인할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

진단 기능으로 사용자를 식별할 수 있는 일반적인 오류 시나리오를 검토하려면 [ID 및 진단 문제](diagnostics-role-service.md#common-error-scenarios)를 참조하세요.
