---
title: Speech Studio 역할 기반 액세스 제어 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Studio 통해 Speech Service에 액세스 역할을 할당하는 방법을 알아봅니다.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: eur
ms.openlocfilehash: 4b84d73b242aba0b46674005211dcb6de77d69e4
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509385"
---
# <a name="azure-role-based-access-control-in-speech-studio"></a>Speech Studio Azure 역할 기반 액세스 제어 

Speech Studio Azure 리소스에 대한 개별 액세스를 관리하기 위한 권한 부여 시스템인 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원합니다. Azure RBAC를 사용하여 Speech Studio 작업에 대한 다양한 수준의 권한을 다른 팀 멤버에게 할당할 수 있습니다. Azure RBAC에 대한 자세한 내용은 [AZURE RBAC 설명서](../../role-based-access-control/overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 계정 및 Speech 리소스를 Speech Studio 로그인해야 합니다. Speech Studio [개요를](speech-studio-overview.md)참조하세요.

## <a name="manage-role-assignments-for-speech-resources"></a>Speech 리소스에 대한 역할 할당 관리

Azure 음성 리소스에 대한 액세스 권한을 부여하려면 Azure Portal Azure RBAC 도구를 통해 역할 할당을 추가합니다. 

몇 분 이내에 선택한 범위에서 선택한 역할이 대상에 할당됩니다. 이 단계에 대한 도움말은 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md?tabs=current)을 참조하세요.

## <a name="supported-built-in-roles-in-speech-studio"></a>Speech Studio 지원되는 기본 제공 역할

역할 정의는 사용 권한 컬렉션입니다. 권한에 대한 고유한 사용자 지정 요구 사항이 없는 경우 다음과 같은 권장 기본 제공 역할을 사용합니다.

| **기본 제공 역할** | **리소스 키를 나열할 수 있는 권한** | **Custom Speech 작업에 대한 권한** | **Custom Voice 작업에 대한 권한**| **다른 기능에 대한 권한** |
| ---| ---| ---| ---| --|
|**소유자** |예 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |모든 권한 |
|**기여자** |예 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |모든 권한 |
|**Cognitive Service 기여자** |예 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |모든 권한 |
|**Cognitive Service 사용자** |예 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |모든 권한 |
|**Cognitive Service Speech 기여자** |아니요 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |프로젝트/데이터/모델/엔드포인트를 만들거나 편집하거나 삭제할 수 있는 권한을 포함하여 프로젝트에 대한 모든 권한 |모든 권한 |
|**Cognitive Service Speech 사용자** |아니요 |프로젝트 / 데이터 세트 / 모델 / 엔드포인트를 볼 수 있습니다. 만들 수 없음, 편집, 삭제 |프로젝트 / 데이터 세트 / 모델 / 엔드포인트를 볼 수 있습니다. 만들 수 없음, 편집, 삭제 |모든 권한 |
|**Cognitive Services 데이터 판독기(미리 보기)** |아니요 |프로젝트 / 데이터 세트 / 모델 / 엔드포인트를 볼 수 있습니다. 만들 수 없음, 편집, 삭제 |프로젝트 / 데이터 세트 / 모델 / 엔드포인트를 볼 수 있습니다. 만들 수 없음, 편집, 삭제 |모든 권한 |

또는 [고유한 사용자 지정 역할을 만들](../../role-based-access-control/custom-roles.md)수 있습니다. 예를 들어 사용자 지정 음성 모델을 엔드포인트에 배포할 수 없는 사용자 지정 음성 데이터 세트를 업로드할 수 있는 권한이 있는 사용자 지정 역할을 만들 수 있습니다.

> [!NOTE]
> Speech Studio 키 기반 인증을 지원합니다. 리소스 키()를 나열할 수 있는 권한이 있는 역할은 `Microsoft.CognitiveServices/accounts/listKeys/action` 먼저 리소스 키로 인증되며 Azure Portal 키 인증을 사용하는 한 Speech Studio 작업에 대한 모든 권한을 갖습니다. 서비스 관리자가 키 인증을 사용하지 않도록 설정하면 해당 역할이 Studio에 대한 모든 액세스 권한을 잃게 됩니다.

> [!NOTE]
> 하나의 리소스를 여러 역할로 할당하거나 상속할 수 있으며, 이 리소스에 대한 액세스의 최종 수준은 작업 수준에서 모든 역할의 권한 조합입니다.

## <a name="next-steps"></a>다음 단계

[미사용 데이터의 Speech Service 암호화에](./speech-encryption-of-data-at-rest.md)대해 자세히 알아보세요.
