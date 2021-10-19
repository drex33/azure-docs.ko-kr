---
title: FHIR 서비스에서 원본 간 리소스 공유 구성
description: 이 문서에서는 FHIR 서비스에서 원본 간 리소스 공유를 구성 하는 방법을 설명 합니다.
author: matjazl
ms.author: zxue
ms.date: 08/03/2021
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: e322b1fb848f9156b1d00d2d1ae051ef62536c4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778889"
---
# <a name="configure-cross-origin-resource-sharing-in-fhir-service"></a>FHIR 서비스에서 원본 간 리소스 공유 구성

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

Azure 의료 Api의 FHIR 서비스 (FHIR 서비스 라고 함)는 [CORS (크로스-원본 자원 공유](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing))를 지원 합니다. CORS를 사용 하면 한 도메인 (원본)의 응용 프로그램이 도메인 간 요청 이라고 하는 다른 도메인의 리소스에 액세스할 수 있도록 설정을 구성할 수 있습니다.

CORS는 RESTful API를 다른 도메인으로 호출 해야 하는 단일 페이지 앱에서 자주 사용 됩니다.

FHIR 서비스에서 CORS 설정을 구성 하려면 다음 설정을 지정 합니다.

- 원본 **(액세스 제어-원본)**. FHIR 서비스에 대 한 원본 간 요청을 수행할 수 있는 도메인 목록입니다. 각 도메인 (원본)은 별도의 줄에 입력 해야 합니다. 별표 (*)를 입력 하 여 모든 도메인의 호출을 허용할 수 있지만 보안 위험이 있으므로 권장 하지 않습니다.

- **헤더 (액세스 제어-허용 헤더)**. 원본 요청에 포함 될 헤더 목록입니다. 모든 헤더를 허용 하려면 별표 (*)를 입력 합니다.

- **메서드 (액세스 제어-허용 메서드)**. API 호출에서 허용 되는 메서드 (PUT, GET, POST 등)입니다. 모든 메서드에 대해 **모두 선택** 을 선택 합니다.

- **최대 기간 (액세스 제어-최대 사용 기간)**. 액세스 제어-헤더 및 액세스 제어-허용-메서드에 대 한 실행 전 요청 결과를 캐시 하는 값 (초)입니다.

- **자격 증명을 허용 합니다 (액세스 제어-자격 증명 허용)**. CORS 요청은 [CSRF (교차 사이트 요청 위조)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 공격을 방지 하기 위해 일반적으로 쿠키를 포함 하지 않습니다. 이 설정을 선택 하면 쿠키와 같은 자격 증명을 포함 하도록 요청을 수행할 수 있습니다. 이미 원본이 별표 (*)로 설정 된 경우에는이 설정을 구성할 수 없습니다.

![CORS (크로스-원본 자원 공유) 설정](media/cors/cors.png)

>[!NOTE]
>다른 도메인 원본에 대해 다른 설정을 지정할 수 없습니다. 모든 설정 (**헤더**, **메서드**, **최대 사용 기간** 및 **자격 증명 허용**)은 원본 설정에 지정 된 모든 원본에 적용 됩니다.