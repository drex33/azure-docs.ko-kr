---
title: 애플리케이션 프록시 쿠키 설정 - Azure Active Directory
description: Azure AD(Azure Active Directory)에는 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 액세스할 수 있는 액세스 권한과 세션 쿠키가 있습니다. 이 문서에서는 쿠키 설정을 사용 및 구성하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: c72ce281697b23092530571114dafb3aece5d0d1
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990086"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory에서 온-프레미스 애플리케이션에 액세스하기 위한 쿠키 설정

Azure AD(Azure Active Directory)에는 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 액세스할 수 있는 액세스 권한과 세션 쿠키가 있습니다. 애플리케이션 프록시 쿠키 설정을 사용하는 방법을 알아봅니다. 

## <a name="what-are-the-cookie-settings"></a>쿠키 설정이란?

[애플리케이션 프록시](application-proxy.md)는 다음 액세스 및 세션 쿠키 설정을 사용합니다.

| 쿠키 설정 | 기본값 | Description | 권장 사항 |
| -------------- | ------- | ----------- | --------------- |
| HTTP 전용 쿠키 사용 | **아니요** | **예** 는 애플리케이션 프록시가 HTTP 응답 헤더에 HTTPOnly 플래그를 포함하도록 허용합니다. 이 플래그는 추가 보안 이점을 제공합니다. 예를 들어 CSS(클라이언트 쪽 스크립팅)에서 쿠키를 복사 또는 수정하지 못하게 방지합니다.<br></br><br></br>HTTP 전용 설정이 지원되기 전에는 애플리케이션 프록시가 보안 TLS 채널을 통해 쿠키를 암호화 및 전송하여 수정을 방지했습니다. | 추가 보안 이점이 있는 **예** 를 사용하세요.<br></br><br></br>세션 쿠키에 액세스해야 하는 클라이언트 또는 사용자 에이전트에는 **아니요** 를 사용하세요. 예를 들어 애플리케이션 프록시를 통해 원격 데스크톱 게이트웨이 서버에 연결하는 RDP 또는 MTSC 클라이언트에는 **아니요** 를 사용합니다.|
| 보안 쿠키 사용 | **아니요** | **예** 를 사용하면 애플리케이션 프록시가 HTTP 응답 헤더에 보안 플래그를 포함할 수 있습니다. 보안 쿠키는 HTTPS 같은 TLS 보안 채널을 통해 쿠키를 전송하여 보안을 강화합니다. 이렇게 하면 쿠키가 일반 텍스트로 전송되기 때문에 권한 없는 사람이 쿠키를 볼 수 없습니다. | 추가 보안 이점이 있는 **예** 를 사용하세요.|
| 영구적 쿠키 사용 | **아니요** | **예** 를 사용하면 웹 브라우저를 닫을 때 액세스 쿠키가 만료되지 않도록 애플리케이션 프록시를 설정할 수 있습니다. 액세스 토큰이 만료될 때까지 또는 사용자가 수동으로 영구적 쿠키를 삭제할 때까지 유지됩니다. | 사용자가 인증된 상태로 유지되는 보안 위험이 있으므로 **아니요** 를 사용하세요.<br></br><br></br>프로세스 간에 쿠키를 공유할 수 없는 구형 애플리케이션에만 **예** 를 사용하는 것이 좋습니다. 영구적 쿠키를 사용하는 대신 프로세스 간 공유 쿠키를 처리하도록 애플리케이션을 업데이트하는 것이 좋습니다. 예를 들어 사용자가 SharePoint 사이트의 탐색기 보기에서 Office 문서를 열 수 있도록 허용하려면 영구적 쿠키가 필요합니다. 영구적 쿠키를 사용하지 않는 경우 브라우저, 탐색기 프로세스 및 Office 프로세스 간에 액세스 쿠키가 공유되지 않으면 이 작업이 실패할 수 있습니다. |

## <a name="samesite-cookies"></a>SameSite 쿠키
Chrome 80 버전부터 Chromium을 활용하는 브라우저에서 [SameSite](https://web.dev/samesite-cookies-explained) 특성을 지정하지 않는 쿠키는 **SameSite=Lax** 로 설정된 것처럼 처리됩니다. SameSite 특성은 쿠키를 동일한 사이트 컨텍스트로 제한하는 방법을 선언합니다. Lax로 설정하면 쿠키는 동일한 사이트 요청 또는 최상위 탐색으로만 전송됩니다. 그러나 애플리케이션 프록시는 사용자가 세션 중에 올바르게 로그인한 상태를 유지하려면 이러한 쿠키를 타사 컨텍스트에서 보존해야 합니다. 이로 인해, 이러한 변경으로 인한 부정적인 영향을 방지하기 위해 애플리케이션 프록시 액세스 및 세션 쿠키를 업데이트하고 있습니다. 업데이트는 다음과 같습니다.

* **SameSite** 특성을 **None** 으로 설정합니다. 이를 통해 애플리케이션 프록시 액세스 및 세션 쿠키를 타사 컨텍스트에서 제대로 보낼 수 있습니다.
* **보안 쿠키 사용** 설정을 기본값으로 **예** 를 사용하도록 설정합니다. 또한 Chrome에는 보안 플래그를 지정하기 위해 쿠키가 필요합니다. 그렇지 않으면 거부됩니다. 이 변경 내용은 애플리케이션 프록시를 통해 게시된 모든 기존 애플리케이션에 적용됩니다. 애플리케이션 프록시 액세스 쿠키는 항상 보안으로 설정되어 있으며 HTTPS를 통해서만 전송됩니다. 이 변경 내용은 세션 쿠키에만 적용됩니다.

애플리케이션 프록시 쿠키에 대한 이러한 변경 내용은 Chrome 80 릴리스 날짜 이전의 다음 몇 주 동안 롤아웃됩니다.

또한 백 엔드 애플리케이션에 타사 컨텍스트에서 사용할 수 있어야 하는 쿠키가 있는 경우 이러한 쿠키에 대해 SameSite = None을 사용하도록 애플리케이션을 변경하여 명시적으로 옵트인해야 합니다. 애플리케이션 프록시는 Set-Cookie 헤더를 해당 URL로 변환하고 백 엔드 애플리케이션에서 설정한 이러한 쿠키에 대한 설정을 준수합니다.



## <a name="set-the-cookie-settings---azure-portal"></a>쿠키 설정 - Azure Portal
Azure Portal을 사용하여 쿠키 설정을 지정하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. **Azure Active Directory** > **Enterprise 애플리케이션** > **모든 애플리케이션** 으로 이동합니다.
3. 쿠키 설정을 사용할 애플리케이션을 선택합니다.
4. **애플리케이션 프록시** 를 클릭합니다.
5. **추가 설정** 아래에서 쿠키 설정을 **예** 또는 **아니요** 로 지정합니다.
6. **저장** 을 클릭하여 변경 내용을 적용합니다. 

## <a name="view-current-cookie-settings---powershell"></a>현재 쿠키 설정 보기 - PowerShell

애플리케이션 현재 쿠키 설정을 확인하려면 다음 PowerShell 명령을 사용합니다.  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>쿠키 설정 지정 - PowerShell

다음 PowerShell 명령에서 ```<ObjectId>```는 애플리케이션의 ObjectId입니다. 

**Http 전용 쿠키** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**보안 쿠키**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**영구 쿠키**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
