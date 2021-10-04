---
title: 방화벽 뒤에서 번역 - Translator
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator는 도메인 이름 또는 IP 필터링을 사용하여 방화벽 뒤에서 번역할 수 있습니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: afb0e616f01342c94734155e96367f0b453e313a
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401867"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Translator를 사용하여 IP 방화벽 뒤에서 번역하는 방법

Translator는 도메인 이름 또는 IP 필터링을 사용하여 방화벽 뒤에서 번역할 수 있습니다. 사람들이 선호하는 방법은 도메인 이름 필터링입니다. 여전히 IP 필터링이 필요한 경우 [서비스 태그를 사용 하 여 ip 주소 정보](../../virtual-network/service-tags-overview.md#service-tags-on-premises)를 가져오는 것이 좋습니다. 번역기 "CognitiveServicesManagement" 서비스 태그 아래에 있습니다. 

특정 IP 필터링 된 방화벽 뒤에서 Microsoft 번역기를 실행 **하지 않는 것이 좋습니다** . 나중에 공지 없이 설치가 중단될 수 있습니다.

2021 9 월 21 일 번역기 지리적 끝점의 IP 주소는 다음과 같습니다.

|Geography|기준 URL(지리적 엔드포인트)|IP 주소|
|:--|:--|:--|
|미국|api-nam.cognitive.microsofttranslator.com|20.42.6.144, 20.49.96.128, 40.80.190.224, 40.64.128.192|
|유럽|api-eur.cognitive.microsofttranslator.com|20.50.1.16, 20.38.87.129|
|아시아 태평양|api-apc.cognitive.microsofttranslator.com|40.80.170.160, 20.43.132.96, 20.37.196.160, 20.43.66.16|
