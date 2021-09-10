---
title: Azure Static Web Apps에서 사용자 지정 도메인 설정
description: 무료 SSL/TLS 인증서를 사용하여 사용자 지정 도메인을 Azure Static Web Apps에 매핑하는 방법에 대해 알아봅니다.
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: buhollan
ms.openlocfilehash: 07da31c48d70f25a2364b4af242bc0d1331d7036
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567354"
---
# <a name="set-up-a-custom-domain-with-free-certificate-in-azure-static-web-apps"></a>Azure Static Web Apps에서 무료 인증서를 사용하여 사용자 지정 도메인 설정

기본적으로 Azure Static Web Apps는 자동 생성된 도메인 이름을 제공합니다. 이 문서에서는 Azure Static Web Apps 애플리케이션에 사용자 지정 도메인 이름을 매핑하는 방법을 보여 줍니다.

## <a name="free-ssltls-certificate"></a>무료 SSL/TLS 인증서

Azure Static Web Apps가 자동으로 생성된 도메인 이름 및 사용자가 추가할 수 있는 사용자 지정 도메인에 대한 무료 SSL/TLS 인증서를 자동으로 제공합니다.

## <a name="walkthrough-video"></a>연습 비디오

> [!VIDEO https://channel9.msdn.com/Shows/5-Things/Configuring-a-custom-domain-with-Azure-Static-Web-Apps/player?format=ny]

## <a name="prerequisites"></a>사전 요구 사항

- 구매한 도메인 이름
- 도메인의 DNS 구성 속성에 대한 액세스

## <a name="dns-configuration-options"></a>DNS 구성 옵션

애플리케이션에 사용할 수 있는 몇 가지 유형의 DNS 구성이 있습니다.

| 시나리오                                                                                 | 예제                                | 도메인 유효성 검사 메서드 | DNS 레코드 종류 |
| ---------------------------------------------------------------------------------------- | -------------------------------------- | ------------------------ | --------------- |
| [root/apex 도메인 추가](#add-domain-using-txt-record-validation)                        | `mydomain.com`, `example.co.uk`        | TXT                      | ALIAS           |
| [하위 도메인 추가](#add-domain-using-cname-record-validation)                             | `www.mydomain.com`, `foo.mydomain.com` | CNAME                    | CNAME           |
| [현재 사용 중인 하위 도메인 이전](#add-domain-using-txt-record-validation) | `www.mydomain.com`, `foo.mydomain.com` | TXT                      | CNAME           |

## <a name="add-domain-using-cname-record-validation"></a>CNAME 레코드 유효성 검사를 사용하여 도메인 추가

CNAME 레코드 유효성 검사는 사용자 지정 도메인을 추가하는 데 권장되는 방법이지만 하위 도메인에 대해서만 작동합니다. 루트 도메인(`mydomain.com`)을 추가하려면 [TXT 레코드 유효성 검사를 사용하여 도메인 추가](#add-domain-using-txt-record-validation)로 건너뛴 다음 [ALIAS 레코드 만들기](#create-an-alias-record)로 이동하세요.

> [!IMPORTANT]
> 하위 도메인이 현재 라이브 사이트에 연결되어 있고 이를 정적 웹앱으로 전송할 준비가 되지 않은 경우 [TXT 레코드 유효성 검사](#add-domain-using-txt-record-validation)를 사용하세요.

### <a name="enter-your-subdomain"></a>하위 도메인을 입력하세요.

1. [Azure Portal](https://portal.azure.com)에서 Static Web App을 엽니다.

1. 메뉴에서 **사용자 지정 도메인** 을 선택합니다.

1. **추가** 단추를 선택합니다.

1. _도메인 이름_ 필드에 하위 도메인을 입력합니다. 프로토콜 없이 입력했는지 확인합니다. 예들 들어 `www.mydomain.com`입니다.

   :::image type="content" source="media/custom-domain/add-subdomain.png" alt-text="입력 상자에 사용자 지정 하위 도메인이 표시된 도메인 화면 추가":::

1. **다음** 단추를 선택하여 _유효성 검사 + 구성_ 단계로 이동합니다.

### <a name="configure-cname-with-your-domain-provider"></a>도메인 공급자로 CNAME 구성

도메인 공급자를 사용하여 CNAME을 구성해야 합니다. Azure DNS가 권장되지만 이러한 단계는 모든 도메인 공급자와 함께 작동합니다.

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

1. _호스트 이름 레코드 유형_ 드롭다운 목록에서 **CNAME** 이 선택되었는지 확인합니다.

1. **복사** 아이콘을 선택하여 _값_ 필드의 값을 클립 보드에 복사합니다.

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="유효성 검사 + 선택한 CNAME을 보여 주는 화면 추가 및 윤곽선 복사 아이콘":::

1. 별도의 브라우저 탭 또는 창에서 Azure Portal에서 Azure DNS 영역을 엽니다.

1. **+ 레코드 집합** 단추를 선택합니다.

1. 다음 값을 사용하여 새 **CNAME** 레코드 집합을 만듭니다.

   | 설정          | 값                                     |
   | ---------------- | ----------------------------------------- |
   | 이름             | `www`와 같은 하위 도메인             |
   | Type             | CNAME                                     |
   | 별칭 레코드 집합 | 아니요                                        |
   | TTL              | 기본값대로 유지                    |
   | TTL 단위         | 기본값대로 유지                    |
   | Alias            | 클립보드에서 도메인 이름 붙여넣기 |

1. **확인** 을 선택합니다.

   :::image type="content" source="media/custom-domain/azure-dns-cname.png" alt-text="이름, 유형 및 별칭 필드가 강조 표시된 Azure DNS 레코드 집합 화면":::

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

# <a name="other-dns"></a>[기타 DNS](#tab/other-dns)

1. _호스트 이름 레코드 유형_ 드롭다운 목록에서 **CNAME** 이 선택되었는지 확인합니다.

1. **복사** 아이콘을 선택하여 _값_ 필드의 값을 클립 보드에 복사합니다.

   :::image type="content" source="media/custom-domain/copy-cname.png" alt-text="유효성 검사 + 선택한 CNAME을 보여 주는 화면 추가 및 윤곽선 복사 아이콘":::

1. 별도의 브라우저 탭 또는 창에서 도메인 공급자의 웹 사이트에 로그인합니다.

1. DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자마다 고유한 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조하세요. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 레이블이 지정된 사이트 영역을 찾습니다.

1. 종종 계정 정보를 확인한 다음 **내 도메인** 과 같은 링크를 검색하여 DNS 레코드 페이지를 찾을 수 있습니다. 해당 페이지로 이동한 다음, **영역 파일**, **DNS 레코드** 또는 **고급 구성** 과 유사한 링크를 찾습니다.

   다음 스크린샷은 DNS 레코드 페이지의 예입니다.

   :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="샘플 DNS 공급자 구성":::

1. 다음 값을 사용하여 새 **CNAME** 레코드를 만듭니다.

   | 설정             | 값                                     |
   | ------------------- | ----------------------------------------- |
   | Type                | CNAME                                     |
   | 호스트                | `www`와 같은 하위 도메인             |
   | 값               | 클립보드에서 도메인 이름 붙여넣기 |
   | TTL(적용 가능한 경우) | 기본값대로 유지                    |

1. DNS 공급자를 사용하여 변경 내용을 저장합니다.

[!INCLUDE [validate CNAME](../../includes/static-web-apps-validate-cname.md)]

---

## <a name="add-domain-using-txt-record-validation"></a>TXT 레코드 유효성 검사를 사용하여 도메인 추가

Azure는 TXT 레코드를 사용하여 도메인 소유의 유효성을 검사합니다. 다음 중 하나를 수행할 때 유용합니다.

1. 루트 도메인(예: `mydomain.com`)을 구성하려고 합니다. 루트 도메인을 구성하는 ALIAS 레코드를 만들려면 도메인을 소유하고 있는지 확인해야 합니다.

1. 가동 중지 시간 없이 하위 도메인을 전송하려고 합니다. TXT 레코드 유효성 검사 메서드를 사용하면 도메인을 소유하고 있고 정적 웹앱이 해당 도메인에 대한 인증서를 발급하는 프로세스를 거치도록 유효성을 검사할 수 있습니다. 그런 다음 언제든지 CNAME 레코드를 사용하여 정적 웹앱을 가리키도록 도메인을 전환할 수 있습니다.

#### <a name="enter-your-domain"></a>도메인 입력

1. [Azure Portal](https://portal.azure.com)에서 Static Web App을 엽니다.

1. 메뉴에서 **사용자 지정 도메인** 을 선택합니다.

1. **추가** 단추를 선택합니다.

1. _도메인 이름_ 필드에 루트 도메인(예: `mydomain.com`) 또는 하위 도메인(예: `www.mydomain.com`)을 입력합니다.

   :::image type="content" source="media/custom-domain/add-domain.png" alt-text="입력 상자에 사용자 지정 도메인을 보여 주는 도메인 추가 화면":::

1. **다음** 단추를 클릭하여 _확인 + 구성_ 단계로 이동합니다.

#### <a name="configure-txt-record-with-your-domain-provider"></a>도메인 공급자를 사용하여 TXT 레코드 구성

도메인 공급자를 사용하여 TXT 레코드를 구성해야 합니다. Azure DNS가 권장되지만 이러한 단계는 모든 도메인 공급자와 함께 작동합니다.

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

1. "호스트 이름 레코드 유형" 드롭다운이 "TXT"로 설정되어 있는지 확인합니다.

1. **코드 생성** 단추를 선택합니다.

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="코드 생성 단추가 강조 표시된 사용자 지정 화면 추가":::

   이 작업은 고유한 코드를 생성합니다.이 코드는 처리하는 데 최대 1분 정도 걸릴 수 있습니다.

1. 코드 옆의 클립보드 아이콘을 선택하여 값을 클립보드에 복사합니다.

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="코드 복사 단추가 강조 표시된 사용자 지정 도메인 화면 추가":::

1. 별도의 브라우저 탭 또는 창에서 Azure Portal에서 Azure DNS 영역을 엽니다.

1. **+ 레코드 집합** 단추를 선택합니다.

1. 다음 값을 사용하여 새 **TXT** 레코드 세트를 만듭니다.

   | 설정  | 값                                                                           |
   | -------- | ------------------------------------------------------------------------------- |
   | Name     | `@`(루트 도메인의 경우) 또는 `_dnsauth.<YOUR_SUBDOMAIN>`(하위 도메인의 경우) 입력         |
   | Type     | TXT                                                                             |
   | TTL      | 기본값대로 유지                                                          |
   | TTL 단위 | 기본값대로 유지                                                          |
   | 값    | 클립보드의 코드 붙여넣기                                              |

1. **확인** 을 선택합니다.

   :::image type="content" source="media/custom-domain/azure-dns-txt.png" alt-text="이름, 유형 및 값 필드가 강조 표시된 Azure DNS 레코드 집합 화면":::

[!INCLUDE [validate TXT record](../../includes/static-web-apps-validate-txt.md)]

# <a name="other-dns"></a>[기타 DNS](#tab/other-dns)

1. "호스트 이름 레코드 유형" 드롭다운이 "TXT"로 설정되어 있는지 확인합니다.

1. **코드 생성** 단추를 선택합니다.

   :::image type="content" source="media/custom-domain/generate-code.png" alt-text="코드 생성 단추가 강조 표시된 사용자 지정 화면 추가":::

   이 작업은 고유한 코드를 생성합니다.이 코드는 처리하는 데 최대 1분 정도 걸릴 수 있습니다.

1. 코드 옆의 클립보드 아이콘을 선택하여 값을 클립보드에 복사합니다.

   :::image type="content" source="media/custom-domain/copy-code.png" alt-text="코드 복사 단추가 강조 표시된 사용자 지정 도메인 화면 추가":::

1. 별도의 브라우저 탭 또는 창에서 도메인 공급자의 웹 사이트에 로그인합니다.

1. DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자마다 고유한 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조하세요. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 레이블이 지정된 사이트 영역을 찾습니다.

   > [!NOTE]
   > 종종 계정 정보를 확인한 다음 **내 도메인** 과 같은 링크를 검색하여 DNS 레코드 페이지를 찾을 수 있습니다. 해당 페이지로 이동한 다음, **영역 파일**, **DNS 레코드** 또는 **고급 구성** 과 유사한 링크를 찾습니다.

1. 다음 값을 사용하여 새 **TXT** 레코드를 만듭니다.

   | 설정             | 값                                                                        |
   | ------------------- | ---------------------------------------------------------------------------- |
   | Type                | TXT                                                                          |
   | 호스트                | `@`(루트 도메인의 경우) 또는 `_dnsauth.<YOUR_SUBDOMAIN>`(하위 도메인의 경우) 입력      |
   | 값               | 클립보드의 코드 붙여넣기                                           |
   | TTL(적용 가능한 경우) | 기본값대로 유지                                                       |

> [!NOTE]
> 일부 DNS 공급자는 루트 도메인(예: mydomain.com)에 대해 "@" to indicate a root domain or they change the "@"와 다른 규칙을 자동으로 사용합니다. 이는 예상된 것이며 유효성 검사 프로세스는 계속 작동합니다.

[!INCLUDE [create repository from template](../../includes/static-web-apps-validate-txt.md)]

---

## <a name="create-an-alias-record"></a>별칭 레코드 만들기

ALIAS 레코드는 하나의 도메인을 다른 도메인에 매핑합니다. 특히 루트 도메인(예: `mydomain.com`)에 사용됩니다. 이 섹션에서는 루트 도메인을 정적 웹앱의 자동 생성된 URL에 매핑하는 ALIAS 레코드를 만듭니다.

# <a name="azure-dns"></a>[Azure DNS](#tab/azure-dns)

> [!IMPORTANT]
> Azure DNS 영역은 정적 웹앱과 동일한 구독에 있어야 합니다.

1. Azure Portal 도메인의 Azure DNS 영역을 엽니다.

1. **+ 레코드 집합** 단추를 선택합니다.

1. 다음 값을 사용하여 새 **A** 레코드 집합을 만듭니다.

   | 설정          | 값                              |
   | ---------------- | ---------------------------------- |
   | Name             | @                                  |
   | Type             | A – IPv4 주소의 별칭 레코드   |
   | 별칭 레코드 집합 | 예                                |
   | 별칭 형식       | Azure 리소스                     |
   | Subscription     | \<Your Subscription>               |
   | Azure 리소스   | \<Your Static Web App>             |
   | TTL              | 기본값대로 유지             |
   | TTL 단위         | 기본값대로 유지             |

1. **확인** 을 선택합니다.

   :::image type="content" source="media/custom-domain/azure-dns-alias.png" alt-text="이름, 유형, 별칭 및 리소스 필드가 강조 표시된 Azure DNS 레코드 집합 화면":::

이제 루트 도메인이 구성되었으므로 DNS 공급자가 전 세계에 변경 내용을 전파하는 데 몇 시간이 걸릴 수 있습니다.

# <a name="other-dns"></a>[기타 DNS](#tab/other-dns)

> [!IMPORTANT]
> 도메인 제공 업체는 [ALIAS](../dns/dns-alias.md) 또는 ANAME 레코드, 또는 CNAME 평면화를 지원해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Static Web App을 엽니다.

1. 메뉴에서 **사용자 지정 도메인** 을 선택합니다.

1. 사용자 지정 도메인 화면에서 정적 웹앱의 자동 생성된 URL을 복사합니다.

   :::image type="content" source="media/custom-domain/auto-generated.png" alt-text="URL 복사 아이콘이 강조 표시된 정적 웹앱의 개요 페이지":::

1. 도메인 공급자의 웹 사이트에 로그인합니다.

1. DNS 레코드를 관리하기 위한 페이지를 찾습니다. 각 도메인 공급자마다 고유한 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조하세요. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 레이블이 지정된 사이트 영역을 찾습니다.

   > [!NOTE]
   > 종종 계정 정보를 확인한 다음 **내 도메인** 과 같은 링크를 검색하여 DNS 레코드 페이지를 찾을 수 있습니다. 해당 페이지로 이동한 다음, **영역 파일**, **DNS 레코드** 또는 **고급 구성** 과 유사한 링크를 찾습니다.

1. 다음 값을 사용하여 새 **ALIAS** 레코드를 만듭니다.

   | 설정             | 값                                                          |
   | ------------------- | -------------------------------------------------------------- |
   | Type                | ALIAS 또는 ANAME(사용할 수 없는 경우 CNAME 사용)                    |
   | 호스트                | @                                                              |
   | 값               | 클립보드에서 도메인 이름 붙여넣기                      |
   | TTL(적용 가능한 경우) | 기본값대로 유지                                         |

> [!IMPORTANT]
> 도메인 공급자가 별칭 또는 ANAME 레코드 유형을 제공하지 않는 경우에는 CNAME 유형을 대신 사용합니다. 많은 공급자가 CNAME 레코드 유형 및 "CNAME 평면화"라는 기능을 통해 ALIAS 레코드 유형과 동일한 기능을 제공합니다.

이제 루트 도메인이 구성되었으므로 DNS 공급자가 전 세계에 변경 내용을 전파하는 데 몇 시간이 걸릴 수 있습니다.

---

## <a name="redirect-requests-to-a-default-domain"></a>기본 도메인으로 요청 리디렉션

자동으로 생성된 도메인과 사용자가 구성한 사용자 지정 도메인을 사용하여 정적 웹앱에 액세스할 수 있습니다. 필요에 따라 모든 트래픽을 기본 도메인으로 리디렉션하도록 앱을 구성할 수 있습니다.

### <a name="set-a-default-domain"></a>기본 도메인 설정

사용자 지정 도메인을 앱의 기본 도메인으로 지정하면 다른 도메인에 대한 요청이 기본 도메인으로 자동 리디렉션됩니다. 하나의 사용자 지정 도메인만 기본값으로 설정할 수 있습니다.

사용자 지정 도메인을 기본값으로 설정하려면 아래 단계를 따르세요.

1. Azure portal에서 정적 웹앱을 연 상태에서 메뉴에 있는 **사용자 지정 도메인** 을 선택합니다.

1. 기본 도메인으로 구성하려는 사용자 지정 도메인을 선택합니다.

1. **기본값 설정** 을 선택합니다.

   :::image type="content" source="media/custom-domain/set-default.png" alt-text="사용자 지정 도메인을 기본값으로 설정":::

1. 작업이 완료되면 테이블을 새로 고쳐 도메인이 "기본값"으로 표시되어 있는지 확인합니다.

### <a name="unset-a-default-domain"></a>기본 도메인 설정 해제

기본 도메인으로 리디렉션되는 도메인을 중지하려면 아래 단계를 수행합니다.

1. Azure portal에서 정적 웹앱을 연 상태에서 메뉴에 있는 **사용자 지정 도메인** 을 선택합니다.

1. 기본값으로 구성한 사용자 지정 도메인을 선택합니다.

1. **기본값 설정 해제** 를 선택합니다.

1. 작업이 완료되면 테이블을 새로 고쳐 "기본값"으로 표시된 도메인이 없는지 확인합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 설정 구성](application-settings.md)
