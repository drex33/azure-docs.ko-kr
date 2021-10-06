---
title: 포함 파일
description: 포함 파일
services: purview
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: include
ms.custom: include file
ms.date: 09/10/2021
ms.openlocfilehash: 8b2b6cf87380c0c89327a7cf0afd5331a140f836
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212491"
---
## <a name="prerequisites"></a>필수 구성 요소

* Azure를 구독하지 않았으면 시작하기 전에 [무료 구독](https://azure.microsoft.com/free/)을 만듭니다.

* 구독과 연결된 [Azure Active Directory 테넌트](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Azure에 로그인하는 데 사용하는 사용자 계정은 *기여자* 나 *소유자* 역할의 구성원 또는 Azure 구독의 *관리자* 여야 합니다. 구독에 있는 권한을 보려면 [Azure Portal](https://portal.azure.com)로 이동하고 오른쪽 위 모서리에 있는 사용자 이름을 선택하고 추가 옵션에 대한 " **...** " 아이콘을 선택한 다음, **내 권한** 을 선택합니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다.

* **스토리지 계정** 또는 **이벤트 허브 네임스페이스** 만들기를 방해하는 [Azure 정책](../../governance/policy/overview.md)이 없습니다. Purview는 생성될 때 관리형 스토리지 계정 및 이벤트 허브를 배포합니다. 차단 정책이 있고 그대로 유지해야 하는 경우 [Purview 예외 태그 가이드](../create-purview-portal-faq.md)에 따라 Purview 계정의 예외를 만드는 단계를 수행합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.
