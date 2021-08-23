---
title: Cognitive Services에 대한 고객 관리형 키
titleSuffix: Cognitive Services
description: Azure Portal을 사용하여 Azure Key Vault로 고객 관리형 키를 구성하는 방법을 알아봅니다. 고객 관리형 키를 사용하면 액세스 제어를 만들고, 회전하고, 비활성화하고, 철회할 수 있습니다.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2021
ms.author: egeaney
ms.openlocfilehash: 4ffafa5618de85a0d47a5cf8317aae6d6b5d6987
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567975"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>Cognitive Services용 Azure Key Vault를 사용하여 고객 관리형 키 구성

Cognitive Services용 Azure Key Vault에서 고객 관리형 키를 사용하도록 설정하는 프로세스는 제품마다 다릅니다. 다음 링크를 사용하여 서비스별 지침을 따르세요.

## <a name="vision"></a>Vision

* [미사용 데이터의 Custom Vision 암호화](../custom-vision-service/encrypt-data-at-rest.md)
* [미사용 데이터의 Face Services 암호화](../face/encrypt-data-at-rest.md)
* [미사용 데이터의 Form Recognizer 암호화](../../applied-ai-services/form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>언어

* [미사용 데이터의 Language Understanding 서비스 암호화](../LUIS/encrypt-data-at-rest.md)
* [미사용 데이터의 QnA Maker 암호화](../QnAMaker/encrypt-data-at-rest.md)
* [미사용 데이터의 Translator 암호화](../translator/encrypt-data-at-rest.md)

## <a name="speech"></a>음성

* [미사용 데이터의 음성 암호화](../speech-service/speech-encryption-of-data-at-rest.md)

## <a name="decision"></a>의사 결정

* [미사용 데이터의 Content Moderator 암호화](../Content-Moderator/encrypt-data-at-rest.md)
* [미사용 데이터의 Personalizer 암호화](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault란](../../key-vault/general/overview.md)?
* [Cognitive Services 고객 관리형 키 요청 양식](https://aka.ms/cogsvc-cmk)