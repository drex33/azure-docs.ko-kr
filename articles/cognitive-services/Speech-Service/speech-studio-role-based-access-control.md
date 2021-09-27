---
title: Speech Studio의 역할 기반 액세스 제어-음성 서비스
titleSuffix: Azure Cognitive Services
description: Speech Studio를 통해 음성 서비스에 액세스 역할을 할당 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: pafarley
ms.openlocfilehash: 72d2acd25381af654fb098d81a7cdc4ec0bbce22
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128705701"
---
# <a name="azure-role-based-access-control-in-speech-studio"></a>Speech Studio의 Azure 역할 기반 access control 

Speech Studio는 azure 리소스에 대 한 개별 액세스를 관리 하기 위한 권한 부여 시스템용 azure RBAC (역할 기반 액세스 제어)를 지원 합니다. Azure RBAC를 사용 하 여 다른 팀 멤버에 게 Speech Studio 작업에 대해 서로 다른 수준의 사용 권한을 할당할 수 있습니다. Azure RBAC에 대한 자세한 내용은 [AZURE RBAC 설명서](/azure/role-based-access-control/overview)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 계정 및 음성 리소스를 사용 하 여 Speech Studio에 로그인 해야 합니다. [Speech Studio 개요](speech-studio-overview.md)를 참조 하세요.

## <a name="manage-role-assignments-for-speech-resources"></a>음성 리소스에 대 한 역할 할당 관리

Azure speech 리소스에 대 한 액세스 권한을 부여 하려면 Azure Portal에서 Azure RBAC 도구를 통해 역할 할당을 추가 합니다. 

몇 분 이내에 선택한 범위에서 선택한 역할이 대상에 할당됩니다. 이 단계에 대한 도움말은 [Azure Portal을 사용하여 Azure 역할 할당](/azure/role-based-access-control/role-assignments-portal?tabs=current)을 참조하세요.

## <a name="supported-built-in-roles-in-speech-studio"></a>Speech Studio에서 지원 되는 기본 제공 역할

역할 정의는 사용 권한 컬렉션입니다. 사용 권한에 대 한 고유한 사용자 지정 요구 사항이 없는 경우에는 다음과 같은 기본 제공 역할을 사용 합니다.

| **기본 제공 역할** | **리소스 키를 나열할 수 있는 권한** | **Custom Speech 작업에 대 한 사용 권한** | **사용자 지정 음성 작업에 대 한 사용 권한**| **기타 기능에 대 한 사용 권한** |
| ---| ---| ---| ---| --|
|**소유자** |Yes |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |모든 권한 |
|**기여자** |Yes |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |모든 권한 |
|**인식 서비스 기여자** |Yes |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |모든 권한 |
|**인식 서비스 사용자** |Yes |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |모든 권한 |
|**인식 서비스 음성 참가자** |No |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |프로젝트/데이터/모델/끝점을 만들거나 편집 하거나 삭제할 수 있는 권한을 포함 하 여 프로젝트에 대 한 모든 권한 |모든 권한 |
|**인식 서비스 음성 사용자** |No |프로젝트/데이터 집합/모델/끝점을 볼 수 있습니다. 만들기, 편집, 삭제할 수 없습니다. |프로젝트/데이터 집합/모델/끝점을 볼 수 있습니다. 만들기, 편집, 삭제할 수 없습니다. |모든 권한 |
|**데이터 판독기 Cognitive Services (미리 보기)** |No |프로젝트/데이터 집합/모델/끝점을 볼 수 있습니다. 만들기, 편집, 삭제할 수 없습니다. |프로젝트/데이터 집합/모델/끝점을 볼 수 있습니다. 만들기, 편집, 삭제할 수 없습니다. |모든 권한 |

또는 [사용자 지정 역할을 직접 만들](/azure/role-based-access-control/custom-roles)수 있습니다. 예를 들어 사용자 지정 음성 모델을 끝점에 배포 하는 기능 없이 사용자 지정 음성 데이터 집합을 업로드할 수 있는 권한이 있는 사용자 지정 역할을 만들 수 있습니다.

> [!NOTE]
> Speech Studio는 키 기반 인증을 지원 합니다. 리소스 키를 나열할 수 있는 권한이 있는 역할 ( `Microsoft.CognitiveServices/accounts/listKeys/action` )은 먼저 리소스 키를 사용 하 여 인증 되며, Azure Portal에서 키 인증을 사용 하는 경우에는 Speech Studio 작업에 대 한 모든 권한을 갖습니다. 서비스 관리자가 키 인증을 사용 하지 않도록 설정한 경우 해당 역할은 Studio에 대 한 모든 액세스 권한을 잃게 됩니다.

> [!NOTE]
> 한 리소스는 여러 역할을 사용 하 여 할당 되거나 상속 될 수 있으며,이 리소스에 대 한 최종 액세스 수준은 작업 수준의 모든 역할 사용 권한을 조합한 것입니다.

## <a name="next-steps"></a>다음 단계

휴지 상태의 데이터에 대 한 [음성 서비스 암호화](/azure/cognitive-services/speech-service/speech-encryption-of-data-at-rest)에 대해 자세히 알아보세요.