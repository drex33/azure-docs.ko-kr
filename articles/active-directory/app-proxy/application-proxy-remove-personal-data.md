---
title: 개인 데이터 제거 - Azure Active Directory 애플리케이션 프록시
description: Azure Active Directory 애플리케이션 프록시에 대해 설치된 커넥터에서 개인 데이터를 제거합니다.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: harshja
ms.openlocfilehash: 0379d6c4b017348e35fafb41a95cea9c3fa0ceb8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988775"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시에 대한 개인 데이터 제거

Azure Active Directory 애플리케이션 프록시에서는 디바이스에 커넥터를 설치해야 합니다. 즉 디바이스에 개인 데이터가 있을 수 있습니다. 이 문서에서는 개인 정보 보호 수준을 높이기 위해 이러한 개인 데이터를 삭제하는 단계를 제공합니다.

## <a name="where-is-the-personal-data"></a>개인 데이터의 위치

애플리케이션 프록시는 다음 로그 유형에 개인 데이터를 쓸 수 있습니다.

- 커넥터 이벤트 로그
- Windows 이벤트 로그

## <a name="remove-personal-data-from-windows-event-logs"></a>Windows 이벤트 로그에서 개인 데이터 제거

Windows 이벤트 로그에 대한 데이터 보존을 구성하는 방법은 [이벤트 로그에 대한 설정](https://technet.microsoft.com/library/cc952132.aspx)을 참조하세요. Windows 이벤트 로그에 대한 자세한 내용은 [Windows 이벤트 로그 사용](/windows/win32/wes/using-windows-event-log)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>커넥터 이벤트 로그에서 개인 데이터 제거

애플리케이션 프록시 로그에 개인 데이터가 없게 하기 위해 다음을 수행할 수 있습니다.

- 필요한 경우 데이터를 삭제하거나 확인, 또는
- 로깅 해제

다음 섹션을 사용하여 커넥터 이벤트 로그에서 개인 데이터를 제거합니다. 커넥터가 설치된 모든 디바이스에 대해 제거 프로세스를 완료해야 합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>특정 데이터 보기 또는 내보내기

특정 데이터를 보거나 내보내려면 각각의 커넥터 이벤트 로그에서 관련 항목을 검색합니다. 로그는 `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`에 있습니다.

로그는 텍스트 파일이므로 [findstr](/windows-server/administration/windows-commands/findstr)을 사용하여 사용자와 관련한 텍스트 항목을 검색할 수 있습니다.  

개인 데이터를 찾으려면 UserID에 대 한 로그 파일을 검색합니다.

Kerberos 제한 위임을 사용하는 애플리케이션이 기록한 개인 데이터를 찾으려면 다음 사용자 이름 형식의 구성 요소를 검색합니다.

- 온-프레미스 사용자 계정 이름
- UPN(사용자 계정 이름)의 사용자 이름 부분
- 온-프레미스 사용자 계정 이름의 사용자 이름 부분
- 온-프레미스 SAM(보안 계정 관리자) 계정 이름

### <a name="delete-specific-data"></a>특정 데이터 삭제

특정 데이터를 삭제하려면

1. Microsoft Azure AD 애플리케이션 프록시 커넥터 서비스를 다시 시작하여 새 로그 파일을 생성합니다. 새 로그 파일이 있으면 기존 로그 파일을 삭제하거나 수정할 수 있습니다. 
1. 앞서 설명한 [특정 데이터 보기 또는 내보내기](#view-or-export-specific-data) 프로세스에 따라 삭제해야 하는 정보를 찾습니다. 모든 커넥터 로그를 검색합니다.
1. 관련 로그 파일을 삭제하거나, 개인 데이터가 포함된 필드를 선별적으로 삭제합니다. 이전 로그 파일이 더 이상 필요하지 않으면 모두 삭제할 수도 있습니다.

### <a name="turn-off-connector-logs"></a>커넥터 로그 해제

커넥터 로그가 개인 데이터를 포함하지 않게 하는 한 가지 옵션은 로그 생성을 끄는 것입니다. 커넥터 로그 생성을 중지하려면 `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`에서 다음의 강조 표시된 부분을 제거합니다.

![제거 강조 표시된 코드와 함께 코드 조각 표시](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>다음 단계

애플리케이션 프록시에 대한 개요는 [온-프레미스 애플리케이션에 보안 원격 액세스를 제공하는 방법](application-proxy.md)을 참조하세요.