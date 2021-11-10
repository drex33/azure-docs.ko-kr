---
title: LUIS 역할 기반 액세스 제어
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 LUIS 리소스에 액세스 제어를 추가하는 방법을 알아봅니다.
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 2c1f9d8783ca293898ff77f851ac928c1b94413e
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137880"
---
# <a name="luis-role-based-access-control"></a>LUIS 역할 기반 액세스 제어

LUIS는 Azure 리소스에 대한 개별 액세스를 관리하기 위한 권한 부여 시스템인 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원합니다. Azure RBAC를 사용하여 LUIS 작성 리소스에 대해 서로 다른 수준의 권한을 다른 팀 멤버에게 할당합니다. 자세한 내용은 [Azure RBAC 설명서를](/azure/role-based-access-control/) 참조하세요.

## <a name="add-role-assignment-to-language-understanding-authoring-resource"></a>Language Understanding Authoring 리소스에 역할 할당 추가

Azure RBAC는 Language Understanding Authoring 리소스에 할당할 수 있습니다. Azure 리소스에 대한 액세스 권한을 부여하려면 역할 할당을 추가합니다.
1. [Azure Portal](https://ms.portal.azure.com/)에서 **모든 서비스** 를 선택합니다. 
2. **Cognitive Services** 선택하고 특정 Language Understanding 작성 리소스로 이동합니다.
   > [!NOTE]
   > 또한 전체 리소스 그룹, 구독 또는 관리 그룹에 대해 Azure RBAC를 설정할 수 있습니다. 원하는 범위 수준을 선택한 다음, 원하는 항목으로 이동하여 이 작업을 수행합니다. 예를 들어 **리소스 그룹을** 선택한 다음 특정 리소스 그룹으로 이동합니다.

1. 왼쪽 탐색 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. **추가를** 선택한 **다음, 역할 할당 추가를** 선택합니다.
1. 다음 화면의 **역할** 탭에서 추가할 역할을 선택합니다.
1. **구성원** 탭에서 사용자, 그룹, 서비스 주체 또는 관리 ID를 선택합니다.
1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.

몇 분 이내에 선택한 범위에서 선택한 역할이 대상에 할당됩니다. 이 단계에 대한 도움말은 [Azure Portal을 사용하여 Azure 역할 할당](/azure/role-based-access-control/role-assignments-portal)을 참조하세요.

## <a name="security"></a>보안 

LUIS는 AAD(Azure Active Directory) 인증을 지원합니다. 자세한 내용은 [Azure Active Directory 인증을](/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)참조하세요.

## <a name="luis-role-types"></a>LUIS 역할 유형

다음 표를 사용하여 LUIS 애플리케이션에 대한 액세스 요구 사항을 확인합니다.

이러한 사용자 지정 역할은 작성(Language Understanding Authoring)에만 적용되며 예측 리소스(Language Understanding)에는 적용되지 않습니다.

### <a name="cognitive-services-luis-reader"></a>Cognitive Services LUIS 판독기

LUIS 애플리케이션의 유효성을 검사하고 검토해야 하는 사용자이며, 일반적으로 프로젝트를 배포하기 전에 애플리케이션이 제대로 수행되는지 확인하는 테스터입니다. 애플리케이션의 자산(발화, 의도, 엔터티)을 검토하여 앱 개발자에게 변경해야 하는 변경 내용을 알리지만 직접 액세스할 수는 없습니다.


:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **API 액세스**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        * 발화 읽기
        * 의도 
        * 엔터티
        * 애플리케이션 테스트
    :::column-end:::
    :::column span="":::
      아래의 모든 GET API: 
        * [LUIS 프로그래밍 방식 v3.0-preview](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [LUIS 프로그래밍 방식 v2.0 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

      아래의 모든 API: 
        * [LUIS 엔드포인트 API v2.0](https://chinaeast2.dev.cognitive.azure.cn/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
        * [LUIS 엔드포인트 API v3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8)
        * [LUIS 엔드포인트 API v3.0-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a9459a1fe8fa44c28dd8)

      모든 Batch 테스트 웹 API
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-writer"></a>Cognitive Services LUIS 작성기

더 큰 팀의 공동 작업자로서 LUIS 애플리케이션 빌드 및 수정을 담당하는 사용자입니다. 협력자는 어떤 방식으로든 LUIS 애플리케이션을 수정하고, 해당 변경 내용을 학습시키고, 포털에서 해당 변경 내용의 유효성을 검사/테스트할 수 있습니다. 그러나 이 사용자는 실수로 프로덕션 환경의 변경 내용을 반영할 수 있기 때문에 이 애플리케이션을 런타임에 배포할 수 없습니다. 또한 애플리케이션을 삭제하거나 해당 예측 리소스 및 엔드포인트 설정을 변경할 수 없습니다(예측 리소스 할당 또는 할당 취소, 엔드포인트 공용 만들기). 이렇게 하면 이 역할이 프로덕션 환경에서 현재 사용 중인 애플리케이션을 변경할 수 없습니다. 또한 이 리소스 아래에 새 애플리케이션을 만들 수도 있지만 언급된 제한 사항도 있습니다.

:::row:::
    :::column span="":::
        **Capabilities**
    :::column-end:::
    :::column span="":::
        **API 액세스**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * COGNITIVE SERVICES LUIS 판독기 아래의 모든 기능 

      다음을 추가할 수 있습니다. 
        * 발언
        * 의도
        * 엔터티
    :::column-end:::
    :::column span="":::
      * LUIS 판독기 아래의 모든 API

      아래의 모든 POST, PUT 및 DELETE API:

        * [LUIS 프로그래밍 방식 v3.0-preview](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [LUIS 프로그래밍 방식 v2.0 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2d)

        제외:
        * [애플리케이션 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c39)
        * [다른 LUIS 작성 Azure 리소스로 앱 이동](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/apps-move-app-to-another-luis-authoring-azure-resource)
        * [애플리케이션 게시](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c3b)
        * [ 애플리케이션 설정 업데이트](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58aeface39e2bb03dcd5909e)
        * [애플리케이션에 LUIS Azure 계정 할당](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32228e8473de116325515)
        * [애플리케이션에서 할당된 LUIS Azure 계정 제거](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32554f8591db3a86232e1)
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-owner"></a>Cognitive Services LUIS 소유자

이러한 사용자는 프로덕션 환경에서 LUIS 애플리케이션의 게이트키퍼입니다. 모든 기본 함수에 대한 모든 권한이 있어야 하므로 애플리케이션의 모든 내용을 볼 수 있고 작성 및 런타임 환경 모두에 대한 변경 내용을 편집할 수 있는 직접 액세스 권한이 있어야 합니다.

:::row:::
    :::column span="":::
        **기능**
    :::column-end:::
    :::column span="":::
        **API 액세스**
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
      * Cognitive Services LUIS 기록기 아래의 모든 기능
      * 모델 배포
      * 애플리케이션 삭제
    :::column-end:::
    :::column span="":::
      * LUIS에 사용할 수 있는 모든 API
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>다음 단계

* [Azure 리소스 관리](/azure/cognitive-services/luis/luis-how-to-azure-subscription?branch=pr-en-us-171715&tabs=portal#authoring-resource)
