---
title: TLS/SSL IP 주소 변경 준비
description: TLS/SSL IP 주소를 변경하려는 경우 앱이 변경 후 작업을 계속할 수 있도록 수행할 작업에 대해 알아봅니다.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 3712931f73463ec1a799f003b82197752a735136
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895286"
---
# <a name="how-to-prepare-for-a-tlsssl-ip-address-change"></a>TLS/SSL IP 주소 변경을 준비하는 방법

Azure App Service 앱의 TLS/SSL IP 주소가 변경된다는 알림을 받은 경우 이 문서의 지침을 따라 기존 TLS/SSL IP 주소를 릴리스하고 새 주소를 할당합니다.

## <a name="release-tlsssl-ip-addresses-and-assign-new-ones"></a>TLS/SSL IP 주소를 릴리스하고 새 주소 할당

1.  [Azure Portal](https://portal.azure.com)을 엽니다.

2.  왼쪽 탐색 메뉴에서 **App Service** 를 선택합니다.

3.  목록에서 App Service 앱을 선택합니다.

4.  **설정** 헤더 아래의 왼쪽 탐색에서 **SSL 설정** 을 클릭합니다.

1. TLS/SSL 바인딩 섹션에서 호스트 이름 레코드를 선택합니다. 열린 편집기의 **SSL 형식** 드롭 다운 메뉴에서 **SNI SSL** 를 선택하고 **바인딩 추가** 를 클릭합니다. 작업 성공 메시지가 표시되는 경우 기존 IP 주소가 릴리스되었습니다.

6.  **SSL 바인딩** 섹션에서 인증서를 사용하여 동일한 호스트 이름 레코드를 다시 선택합니다. 이번에는 열린 편집기의 **SSL 형식** 드롭 다운 메뉴에서 **IP 기반 SSL** 를 선택하고 **바인딩 추가** 를 클릭합니다. 작업 성공 메시지가 표시되는 경우 새 IP 주소를 획득했습니다.

7.  A 레코드(IP 주소를 직접 가리키는 DNS 레코드)가 도메인 등록 포털(타사 DNS 공급자 또는 Azure DNS)에서 구성되는 경우 기존 IP 주소를 새로 생성된 주소로 바꿉니다. 다음 섹션의 지침을 따르면 새 IP 주소를 찾을 수 있습니다.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Azure Portal에서 새 SSL IP 주소 찾기

1.  몇 분 정도 기다린 다음, [Azure Portal](https://portal.azure.com)을 엽니다.

2.  왼쪽 탐색 메뉴에서 **App Service** 를 선택합니다.

3.  목록에서 App Service 앱을 선택합니다.

4.  **설정** 헤더 아래의 왼쪽 탐색에서 **속성** 을 클릭하고 **가상 IP 주소** 라는 섹션을 찾습니다.

5. IP 주소를 복사하고 도메인 레코드 또는 IP 메커니즘을 다시 구성합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure에서 시작한 IP 주소 변경을 준비하는 방법을 설명합니다. Azure App Service의 IP 주소에 대한 자세한 내용은 [Azure App Service에서 인바운드 및 아웃바운드 IP 주소](overview-inbound-outbound-ips.md)를 참조하세요.
