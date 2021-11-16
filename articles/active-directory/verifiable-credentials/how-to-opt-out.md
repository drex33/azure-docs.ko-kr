---
title: Azure Active Directory 확인 가능한 자격 증명 옵트아웃(미리 보기)
description: 확인 가능한 자격 증명 미리 보기를 옵트아웃하는 방법 알아보기
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: e46537a2bf0a9db08ec9baad810d5915450ee8ea
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006815"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>확인 가능한 자격 증명 옵트아웃(미리 보기)

이 문서의 내용

- 옵트아웃해야 하는 이유입니다.
- 필요한 단계.
- 데이터는 어떻게 되나요?
- 기존의 확인 가능한 자격 증명에 영향을 줍니다.

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 확인 가능한 자격 증명 등록을 완료합니다.

## <a name="potential-reasons-for-opting-out"></a>옵트아웃의 잠재적 이유

현재로서는 도메인 정보를 수정할 수 없습니다. 따라서 실수를 하거나 변경하려는 경우 옵트아웃하고 다시 시작하는 것 외에 사용할 다른 옵션은 없습니다.

## <a name="the-steps-required"></a>필요한 단계

1. Azure Portal에서 확인 가능한 자격 증명을 검색합니다.
2. 왼쪽 메뉴에서 **설정** 을 선택합니다.
3. **조직 다시 설정** 섹션에서 **모든 자격 증명 삭제 및 미리 보기 옵트아웃** 을 선택합니다.

   ![설정 조직 다시 설정](media/how-to-opt-out/settings-reset.png)

4. 경고 메시지를 읽고 계속하려면 **삭제 및 옵트아웃** 을 선택합니다.

   ![설정 삭제 및 옵트아웃](media/how-to-opt-out/delete-and-opt-out.png)

이제 확인 가능한 자격 증명 미리 보기에서 옵트아웃할 수 있습니다. 내부적으로 발생하는 상황을 이해하기 위해 계속 읽으세요.

## <a name="what-happens-to-your-data"></a>데이터는 어떻게 되나요?

Azure Active Directory 확인 가능한 자격 증명 서비스의 옵트아웃을 완료하면 다음 작업이 수행됩니다.

- Key Vault의 DID 키가 [일시 삭제](../../key-vault/general/soft-delete-overview.md)됩니다.
- 발급자 개체가 데이터베이스에서 삭제됩니다.
- 테넌트 식별자가 데이터베이스에서 삭제됩니다. 
- 모든 계약 개체가 데이터베이스에서 삭제됩니다.

옵트아웃이 발생하면 DID를 복구하거나 DID에 대한 작업을 수행할 수 없습니다. 이 단계는 단방향 작업이며 다시 옵트인해야 하므로 새 DID가 생성됩니다.  

## <a name="effect-on-existing-verifiable-credentials"></a>기존의 확인 가능한 자격 증명에 영향을 줍니다.

이미 발급된 모든 확인 가능한 자격 증명은 계속 존재합니다. DID는 ION을 통해 확인 가능한 상태로 유지되므로 암호화가 무효화되지 않습니다.
그러나 신뢰 당사자가 상태 API를 호출하면 항상 오류 메시지를 다시 받게 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 테넌트](get-started-verifiable-credentials.md)에서 확인 가능한 자격 증명 설정