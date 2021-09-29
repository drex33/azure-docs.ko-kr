---
title: 미사용 데이터의 Content Moderator 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 Content Moderator 암호화.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 7beb615307b602789b845e10cee28ca402acf9e9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217185"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>미사용 데이터의 Content Moderator 암호화

Content Moderator는 데이터가 클라우드에 유지될 때 자동으로 암호화하여 조직의 보안 및 규정 준수 목표를 충족하는 데 도움을 줍니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리형 키는 E0 가격 책정 계층에서만 사용할 수 있습니다. 고객 관리형 키를 사용하는 기능을 요청하려면 [Content Moderator 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성하여 제출합니다. 요청 상태에 대한 답변을 받는 데는 영업일 기준 약 3~5일이 소요됩니다. 요청에 따라 공간이 확보되면 큐에 배치되고 승인될 수 있습니다. Content Moderator 서비스에서 CMK 사용이 승인되면 새 Content Moderator 리소스를 만들고 E0을 가격 책정 계층으로 선택해야 합니다. E0 가격 계층을 사용하여 Content Moderator 리소스가 만들어지면 Azure Key Vault를 사용하여 관리 ID를 설정할 수 있습니다.

모든 Azure 지역에서 고객 관리 키를 사용할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Content Moderator 팀에 데이터 암호화 사용

Content Moderator 검토 팀에 데이터 암호화를 사용하도록 설정하려면 [빠른 시작: 웹에서 Content Moderator 사용해보기](quick-start.md#create-a-review-team)를 참조하세요.  

> [!NOTE]
> Content Moderator E0 가격 책정 계층과 함께 _리소스 ID_ 를 제공해야 합니다.

## <a name="next-steps"></a>다음 단계

* CMK를 지원하는 서비스의 전체 목록은 [Cognitive Services용 고객 관리형 키](../encryption/cognitive-services-encryption-keys-portal.md)를 참조하세요.
* [Azure Key Vault란](../../key-vault/general/overview.md)?
* [Cognitive Services 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)