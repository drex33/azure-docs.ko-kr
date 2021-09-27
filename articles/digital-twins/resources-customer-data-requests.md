---
title: Azure Digital Twins의 고객 데이터 요청 기능
titleSuffix: Azure Digital Twins
description: 이 문서에서는 Azure Digital Twins에서 개인 데이터를 내보내고 삭제하는 프로세스를 보여 줍니다.
author: baanders
ms.author: baanders
ms.date: 9/14/2021
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: ef68e552d57df0fc202b9d75ae76065e3119ccff
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642798"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Azure Digital Twins 고객 데이터 요청 기능

Azure Digital Twins는 비즈니스 환경의 보안 디지털 표현을 만들기 위한 개발자 플랫폼입니다. 표현은 사용자가 선택한 데이터 원본의 라이브 상태 데이터에 따라 결정됩니다.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Azure Digital Twins에서 *디지털 트윈* 이라는 디지털 표현은 실제 환경에서 엔터티를 나타내며 식별자와 연결됩니다. Microsoft는 어떤 정보도 유지 관리하지 않으며 식별자와 사용자의 상관 관계를 허용하는 데이터에 액세스할 수 없습니다. 

Azure Digital Twins의 많은 디지털 트윈은 개인 엔터티를 직접 나타내지 않습니다. 일반적으로 표시되는 개체는 사무실 회의실이거나 공장 바닥일 수 있습니다. 그러나 사용자는 일부 엔터티를 개인적으로 식별 가능한 것으로 간주할 수 있으며, 재량에 따라 디지털 트윈을 개인과 연결하는 자체 자산 또는 인벤토리 추적 방법을 유지할 수 있습니다. Azure Digital Twins는 디지털 트윈과 연결된 모든 데이터를 개인 데이터인 것처럼 관리 및 저장합니다.

데이터 주체 요청에서 참조될 수 있는 개인 데이터를 확인, 내보내기 및 삭제하기 위해 Azure Digital Twins 관리자는 사용자 및 역할에 대해 [Azure Portal](https://portal.azure.com/) 또는 디지털 트윈에 대해 [Azure DIGITAL Twins REST API](/rest/api/azure-digitaltwins/)를 사용할 수 있습니다. Azure Portal 및 REST API는 사용자가 이러한 데이터 주체 요청을 처리하는 다양한 방법을 제공합니다.

## <a name="identifying-customer-data"></a>고객 데이터 식별

Azure Digital Twins는 *개인 데이터* 를 관리자 및 사용자와 연결된 데이터로 간주합니다. 

Azure Digital Twins는 환경에 대한 액세스 권한이 있는 사용자의 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) **개체 ID** 를 저장합니다. Azure Portal의 Azure Digital Twins는 사용자 이메일 주소를 표시하지만 이러한 이메일 주소는 Azure Digital Twins 내에 저장되지 않습니다. Azure Active Directory 개체 ID를 사용하여 Azure Active Directory에서 동적으로 조회됩니다.

## <a name="deleting-customer-data"></a>고객 데이터 삭제

Azure Digital Twins 관리자는 Azure Portal을 사용하여 사용자와 관련된 데이터를 삭제할 수 있습니다. 또한 Azure Digital Twins REST API를 사용하여 개별 디지털 트윈에서 삭제 작업을 수행할 수 있습니다. 사용할 수 있는 API에 대한 자세한 내용은 [Azure Digital Twins REST API 설명서](/rest/api/azure-digitaltwins/)를 참조하세요.

## <a name="exporting-customer-data"></a>고객 데이터 내보내기

Azure Digital Twins는 디지털 트윈과 관련된 데이터를 저장합니다. 사용자는 REST API를 통해 이 데이터를 검색하고 볼 수 있으며 복사 및 붙여넣기를 사용하여 이 데이터를 내보낼 수 있습니다. 

사용자 역할 및 역할 할당을 포함한 고객 데이터는 Azure Portal에서 선택, 복사 및 붙여넣을 수 있습니다. 

## <a name="links-to-additional-documentation"></a>추가 설명서에 대한 링크

Azure Digital Twins 서비스 API의 전체 목록은 [Azure Digital Twins REST API 설명서](/rest/api/azure-digitaltwins/)를 참조하세요.