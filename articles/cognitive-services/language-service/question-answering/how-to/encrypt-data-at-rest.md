---
title: 미사용 데이터 암호화에 대한 사용자 지정 질문 답변
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리하는 암호화 키를 제공하며 CMK(고객 관리형 키)라고 하는 고유한 키로 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 사용자 지정 질문 답변에 대한 미사용 데이터 암호화와 CMK를 사용하도록 설정하고 관리하는 방법을 다룹니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: egeaney
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 8570a1160eddbed5b372daa529339ea3672482cb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103036"
---
# <a name="custom-question-answering-encryption-of-data-at-rest"></a>미사용 데이터 암호화에 대한 사용자 지정 질문 답변

질문에 대한 답변은 데이터가 클라우드에 유지될 때 자동으로 암호화되어 조직의 보안 및 규정 준수 목표를 충족하는 데 도움이 됩니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK(고객 관리형 키)라고 하는 사용자 고유의 키를 사용하여 구독을 관리하는 옵션도 있습니다. CMK는 액세스 제어를 만들고, 회전시키고, 사용하지 않도록 설정하고, 철회할 수 있는 훨씬 더 큰 유연성을 제공합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다. CMK가 구독에 대해 구성된 경우 이중 암호화가 제공되어 두 번째 보호 계층을 제공하는 한편 Azure Key Vault를 통해 암호화 키를 제어할 수 있습니다.

질문 답변은 [Azure Search 에서 CMK 지원을](../../../../search/search-security-manage-encryption-keys.md)사용하고 제공된 CMK를 자동으로 연결하여 Azure Search 인덱스에 저장된 데이터를 암호화합니다.

> [!IMPORTANT]
> Azure Search Service 리소스는 2019년 1월 이후에 만들어졌어야 하며 체험(공유) 계층에 있을 수 없습니다. 고객 관리형 키는 Azure Portal에서 구성할 수 없습니다.

## <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용

다음 단계에 따라 CMK를 사용하도록 설정합니다.

1.  사용자 지정 질문 대답을 사용하도록 설정된 언어 리소스의 **암호화** 탭으로 이동합니다.
2.  **고객 관리형 키** 옵션을 선택합니다. [고객 관리형 키의](../../../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) 세부 정보를 제공하고 **저장을** 선택합니다.

> [!div class="mx-imgBorder"]
> ![질문 답변 CMK](../media/encrypt-data-at-rest/question-answering-cmk.png)
   
3.  성공적으로 저장되면 CMK가 Azure Search 인덱스에 저장된 데이터를 암호화하는 데 사용됩니다.

> [!IMPORTANT]
> 기술 자료를 만들기 전에 새 Azure Cognitive Search 서비스에서 CMK를 설정하는 것이 좋습니다. 기존 기술 자료를 사용하여 언어 리소스에서 CMK를 설정하면 해당 기술 자료에 대한 액세스 권한이 손실될 수 있습니다. Azure Cognitive Search의 [암호화된 콘텐츠 작업](../../../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content)에 대해 자세히 알아보세요.

> [!NOTE]
> 고객 관리형 키를 사용하는 기능을 요청하려면 [Cognitive Services 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성하여 제출합니다.

## <a name="regional-availability"></a>국가별 가용성

고객 관리형 키는 모든 Azure Search 지역에서 사용할 수 있습니다.

## <a name="encryption-of-data-in-transit"></a>전송 중 데이터 암호화

언어 스튜디오 포털은 사용자의 브라우저에서 실행됩니다. 모든 작업은 각 Cognitive Service API에 대한 직접 호출을 트리거합니다. 따라서 질문 답변은 전송 중인 데이터를 준수합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault의 CMK를 사용하여 Azure Search에서 암호화](../../../../search/search-security-manage-encryption-keys.md)
* [저장 데이터 암호화](../../../../security/fundamentals/encryption-atrest.md)
* [Azure Key Vault에 대해 자세히 알아보기](../../../../key-vault/general/overview.md)
