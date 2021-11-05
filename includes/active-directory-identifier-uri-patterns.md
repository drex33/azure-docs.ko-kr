---
author: madansr7
ms.author: saumadan
ms.date: 10/06/2021
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 133b4d8c1d3c294943ad6224c3d1436b63921462
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050581"
---
다음 API 및 HTTP 체계 기반 애플리케이션 ID URI 형식이 지원됩니다. 표 다음 목록에 설명된 대로 자리 표시자 값을 바꿉니다.

| 지원되는 애플리케이션 ID <br/> URI 형식 | 앱 ID URI의 예 |
|--|--|
| _api://\<appId\>_ | _api://fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<appId\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _api://\<tenantId\>/\<string\>_ | _api://a8573488-ff46-450a-b09a-6eca0c6a02dc/api_ |
| _api://\<string\>/<appId\>_ | _api://productapi/fc4d2d73-d05a-4a9b-85a8-4f2b3a5f38ed_ |
| _https://\<tenantIdDomain\>.onmicrosoft.com/\<string\>_ | _`https://contoso.onmicrosoft.com/productsapi`_  |
| _https://\<verifiedCustomDomain\>/\<string\>_ |  _`https://contoso.onmicrosoft.com/productsapi`_ |
| _https://\<string\>.\<verifiedCustomDomain\>_ |  _`https://product.contoso.onmicrosoft.com`_ |
| _https://\<string\>.\<verifiedCustomDomain\>/\<string\>_ | _`https://product.onmicrosoft.com/productsapi`_   |


- _\<appId\>_ - 애플리케이션 개체의 애플리케이션 ID(appId) 속성입니다.
- _\<string\>_ - 호스트 또는 API 패스 세그먼트의 문자열 값입니다.
- _\<tenantId\>_ - 모든 Azure Azure AD 테넌트는 _\<tenantId\>.onmicrosoft.com_ 형식의 초기 도메인 2개와 함께 제공됩니다. 여기서 _\<tenantID\>_ 는 초기 도메인 이름 테넌트 만들기 시 지정한 테넌트 작성자 및 Azure 내에서 테넌트를 나타내기 위해 Azure에서 만든 GUID입니다.
- _\<verifiedCustomDomain\>_ - Azure AD 테넌트에 대해 구성된 [확인된 사용자 지정 도메인](../articles/active-directory/fundamentals/add-custom-domain.md)입니다.

