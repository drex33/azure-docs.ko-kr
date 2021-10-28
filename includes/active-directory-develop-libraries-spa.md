---
ms.openlocfilehash: e64446e138b2606f8b868a707d21c6b0c4be6d46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909598"
---
| 언어 / 프레임워크 | 프로젝트 설정<br/>GitHub                                                                                                    | 패키지                                                                      | 가져오기<br/>시작됨                             | 사용자 로그인                                         | 웹 API 액세스                                                 | GA(일반 공급) *또는*<br/>공개 미리 보기<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [자습서](../articles/active-directory/develop/tutorial-v2-angular-auth-code.md) | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | — | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/maintenance/msal-angularjs)<sup>3</sup>         | [msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | 퍼블릭 미리 보기                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [자습서](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
|JavaScript|[MSAL.js 1.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [msal-core](https://www.npmjs.com/package/@azure/msal-core)    | — | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [msal-react](https://www.npmjs.com/package/@azure/msal-react)         | [자습서](../articles/active-directory/develop/tutorial-v2-react.md) | ![라이브러리에서 사용자 로그인에 대한 ID 토큰을 요청할 수 있습니다.][y] | ![라이브러리는 보호된 웹 API에 대한 액세스 토큰을 요청할 수 있습니다.][y] | GA                          |

<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> [Microsoft Azure 미리 보기의 추가 사용 약관][preview-tos]은 *공개 미리 보기* 로 제공되는 라이브러리에 적용됩니다.

<sup>2</sup> PCKE에만 사용하는 [인증 코드 흐름][auth-code-flow](권장). 

<sup>3</sup> [암시적 부여 흐름][implicit-flow] 전용.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md
