---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: kengaderdus
ms.openlocfilehash: 02aff100840f80a24210df7d653f2fed0059d9a5
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038140"
---
#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/) 

1. **앱 등록** 을 선택한 다음, API에 대한 액세스 권한이 있어야 하는 웹 애플리케이션을 선택합니다. 예를 들어 *webapp1* 과 같습니다.
1. **관리** 아래에서 **API 권한** 을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가** 를 선택합니다.
1. **내 API** 탭을 선택합니다.
1. 웹 애플리케이션에 액세스 권한을 부여할 API를 선택합니다. 예를 들어 *webapi1* 과 같습니다.
1. **권한** 아래에서 **데모** 를 확장한 다음, 앞에서 정의한 범위를 선택합니다. *demo.read* 및 *demo.write* 가 예입니다.
1. **권한 추가** 를 선택합니다.
1. **(테넌트 이름)에 대한 관리자 동의 허용** 을 선택합니다.
1. 계정을 선택하라는 메시지가 표시되면 현재 로그인된 관리자 계정을 선택하거나 Azure AD B2C 테넌트에서 최소한 *클라우드 애플리케이션 관리자* 역할이 할당된 계정으로 로그인합니다.
1. **예** 를 선택합니다.
1. **새로 고침** 을 선택한 다음, 두 범위 모두 **상태** 아래에 "...에 대해 허용됨"이 표시되는지 확인합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. **애플리케이션(레거시)** 을 선택한 다음, API에 대한 액세스 권한이 있어야 하는 웹 애플리케이션을 선택합니다. 예를 들어 *webapp1* 과 같습니다.
1. **API 액세스** 를 선택한 다음, **추가** 를 선택합니다.
1. **API 선택** 드롭다운에서 웹 애플리케이션에 액세스 권한을 부여할 API를 선택합니다. 예를 들어 *webapi1* 과 같습니다.
1. **범위 선택** 드롭다운에서 이전에 정의한 범위를 선택합니다. *demo.read* 및 *demo.write* 가 예입니다.
1. **확인** 을 선택합니다.
