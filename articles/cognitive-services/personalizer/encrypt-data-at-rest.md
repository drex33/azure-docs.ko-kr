---
title: 미사용 데이터의 Personalizer 서비스 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리하는 암호화 키를 제공하며, CMK(고객 관리형 키)라고 하는 고유한 키를 사용하여 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 Personalizer의 미사용 데이터 암호화와 CMK를 활성화하고 관리하는 방법을 설명합니다.
author: jeffmend
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jeffme
ms.openlocfilehash: d6d4aaa9b6a04614fdae4f794da9153a03031727
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829760"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>미사용 데이터의 Personalizer 서비스 암호화

Personalizer 서비스는 데이터를 클라우드에 저장할 때 데이터를 자동으로 암호화합니다. Personalizer 서비스 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정에 부합하는 데 도움이 됩니다.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 고객 관리형 키는 E0 가격 책정 계층에서만 사용할 수 있습니다. 고객 관리형 키를 사용하는 기능을 요청하려면 [Personalizer Service 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)을 작성하여 제출합니다. 요청 상태를 다시 들으려면 영업일 기준 3-5일이 소요됩니다. 요청에 따라 큐에 배치되고 공간이 확보되면 승인될 수 있습니다. Personalizer 서비스에서 CMK 사용이 승인되면 새 Personalizer 리소스를 만들고 E0을 가격 책정 계층으로 선택해야 합니다. E0 가격 계층을 사용하여 Personalizer 리소스가 만들어지면 Azure Key Vault를 사용하여 관리 ID를 설정할 수 있습니다.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>다음 단계

* [Personalizer Service 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대해 자세히 알아보기](../../key-vault/general/overview.md)