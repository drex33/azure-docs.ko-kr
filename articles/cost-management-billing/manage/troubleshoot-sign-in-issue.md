---
title: Azure 구독 로그인 문제 해결
description: Azure Portal에 로그인할 수 없는 문제를 해결하는 데 도움이 됩니다.
services: cost-management-billing
author: jasonwhowell
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 07/16/2021
ms.author: jasonh
ms.openlocfilehash: 2ddd5f3fdb974eadba617afcd6f27d047534031b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040892"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Azure 구독 로그인 문제 해결

이 가이드는 Azure Portal에 로그인할 수 없는 문제를 해결하는 데 도움이 됩니다.

> [!NOTE]
> 새 Azure 계정을 가입하는 데 문제가 있는 경우 [Azure 구독 가입 문제 해결](./troubleshoot-azure-sign-up.md)을 참조하세요.

## <a name="page-hangs-in-the-loading-status"></a>페이지가 로딩 상태에서 중지됨

인터넷 브라우저 페이지가 응답하지 않는 경우 Azure Portal에 액세스할 수 있을 때까지 다음 각 단계를 시도해 보세요.

- 페이지를 새로 고칩니다.
- 다른 인터넷 브라우저를 사용합니다.
- 브라우저에 프라이빗 검색 모드를 사용합니다.

   - **Edge:** **설정**(프로필 그림 옆에 있는 점 세 개)을 열고 **새 InPrivate 창** 을 선택한 다음, [Azure Portal](https://portal.azure.com/)을 찾아 로그인합니다. 
   - **Chrome:** **Incognito** 모드를 선택합니다.
   - **Safari:** **File**, **New Private Window** 를 차례로 선택합니다.

- 캐시를 제거하고 인터넷 쿠키를 삭제합니다.

   - **Edge:** **설정** 을 열고 **개인 정보 및 서비스** 를 선택합니다. **검색 데이터 지우기** 의 단계를 따릅니다. **검색 기록**, **다운로드 기록**, **캐시된 이미지 및 파일** 을 선택하고 **삭제** 를 선택합니다.
   - **Chrome:** **Settings** 를 선택하고, **개인 정보 및 보안** 에서 **인터넷 사용 기록 삭제** 를 선택합니다.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>다른 사용자로 자동으로 로그인됨

인터넷 브라우저에서 둘 이상의 사용자 계정을 사용 중인 경우 이 문제가 발생할 수 있습니다.

이 문제를 해결하려면 다음 방법 중 하나를 사용해 보세요.

- 캐시의 선택을 취소하고 인터넷 쿠키를 삭제합니다.

   - **Edge:** **설정** 을 열고 **개인 정보 및 서비스** 를 선택합니다. **검색 데이터 지우기** 의 단계를 따릅니다. **검색 기록**, **다운로드 기록**, **쿠키**, **캐시된 이미지 및 파일** 을 선택하고 **삭제** 를 선택합니다.
   - **Chrome:** **Settings** 를 선택하고, **개인 정보 및 보안** 에서 **인터넷 사용 기록 삭제** 를 선택합니다.
- 브라우저 설정을 기본값으로 초기화합니다.
- 브라우저에 대해 프라이빗 검색 모드를 사용합니다. 
   - **Edge:** **설정**(프로필 그림 옆에 있는 점 세 개)을 열고 **새 InPrivate 창** 을 선택한 다음, [Azure Portal](https://portal.azure.com/)을 찾아 로그인합니다. 
   - **Chrome:** **Incognito** 모드를 선택합니다.
   - **Safari:** **File**, **New Private Window** 를 차례로 선택합니다.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>로그인할 수 있지만 구독을 찾을 수 없다는 오류가 표시됨

잘못된 디렉터리에서 선택했거나 사용자 계정에 충분한 권한이 없는 경우 이 문제가 발생합니다.

**시나리오:** [Azure Portal](https://portal.azure.com/)에 로그인하는 동안 오류 발생

이 문제를 해결하려면

- 오른쪽 위 모서리에서 계정을 선택하여 올바른 Azure 디렉터리를 선택했는지 확인합니다.
- 올바른 Azure 디렉터리를 선택했으나 여전히 오류 메시지가 표시되면 계정을 [소유자로 추가](./add-change-subscription-administrator.md)합니다.

## <a name="additional-help-resources"></a>추가 도움말 리소스

Azure 청구 및 구독에 대한 기타 문제 해결 문서

- [거부된 카드](./troubleshoot-declined-card.md)
- [구독 등록 문제](./troubleshoot-azure-sign-up.md)
- [구독을 찾을 수 없음](./no-subscriptions-found.md)
- [Enterprise 비용 보기 사용 안 함](./enterprise-mgmt-grp-troubleshoot-cost-view.md)
- [Azure Billing 설명서](../index.yml)

## <a name="contact-us-for-help"></a>도움이 필요한 경우 문의처

질문이 있거나 도움이 필요하지만 Azure Portal 로그인할 수 없는 경우 [지원 요청을 만듭니다](https://support.microsoft.com/oas/?prid=15470).