---
title: LUIS 역할 기반 액세스 제어
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용 하 여 LUIS 리소스에 액세스 제어를 추가 하는 방법을 알아봅니다.
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: 5d7469e8eb31acb1a9fdeece78affc1d12e33727
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718453"
---
# <a name="luis-role-based-access-control"></a>LUIS 역할 기반 액세스 제어

LUIS는 azure 리소스에 대 한 개별 액세스를 관리 하기 위한 권한 부여 시스템용 azure RBAC (역할 기반 액세스 제어)를 지원 합니다. Azure RBAC를 사용 하 여 LUIS authoring 리소스에 대해 다른 팀 구성원에 게 다른 수준의 사용 권한을 할당 합니다. 자세한 내용은 [AZURE RBAC 설명서](../../role-based-access-control/index.yml) 를 참조 하세요.

## <a name="add-role-assignment-to-language-understanding-authoring-resource"></a>Language Understanding 제작 리소스에 역할 할당 추가

Azure RBAC는 Language Understanding 제작 리소스에 할당할 수 있습니다. Azure 리소스에 대한 액세스 권한을 부여하려면 역할 할당을 추가합니다.
1. [Azure Portal](https://ms.portal.azure.com/)에서 **모든 서비스** 를 선택합니다. 
2. **Cognitive Services** 를 선택 하 고 특정 Language Understanding 제작 리소스로 이동 합니다.
   > [!NOTE]
   > 또한 전체 리소스 그룹, 구독 또는 관리 그룹에 대해 Azure RBAC를 설정할 수 있습니다. 원하는 범위 수준을 선택 하 고 원하는 항목으로 이동 하 여이 작업을 수행 합니다. 예를 들어 **리소스 그룹** 을 선택한 다음 특정 리소스 그룹으로 이동 합니다.

1. 왼쪽 탐색 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. **추가** 를 선택한 다음 **역할 할당 추가** 를 선택 합니다.
1. 다음 화면의 **역할** 탭에서 추가할 역할을 선택합니다.
1. **구성원** 탭에서 사용자, 그룹, 서비스 주체 또는 관리 ID를 선택합니다.
1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.

몇 분 이내에 선택한 범위에서 선택한 역할이 대상에 할당됩니다. 이 단계에 대한 도움말은 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="security"></a>보안 

LUIS는 Azure Active Directory (AAD) 인증을 지원 합니다. 자세한 내용은 [Azure Active Directory를 사용 하 여 인증](../authentication.md#authenticate-with-azure-active-directory)을 참조 하세요.

## <a name="luis-role-types"></a>LUIS 역할 유형

다음 표를 사용 하 여 LUIS 응용 프로그램에 대 한 액세스 요구 사항을 확인 합니다.

이러한 사용자 지정 역할은 작성 (Language Understanding 제작) 및 예측 리소스 (Language Understanding)에만 적용 됩니다.

### <a name="cognitive-services-luis-reader"></a>Cognitive Services LUIS 판독기

LUIS 응용 프로그램의 유효성을 검사 하 고 검토 해야 하는 사용자입니다. 일반적으로 테스터는 프로젝트를 배포 하기 전에 응용 프로그램이 제대로 작동 하 고 있는지 확인 해야 합니다. 응용 프로그램의 자산 (길이 발언, 의도, 엔터티)을 검토 하 여 수행 해야 하는 변경 내용에 대해 앱 개발자에 게 알려야 할 수 있지만 직접 액세스할 수는 없습니다.


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
        * 길이 발언 읽기
        * 의도 
        * 엔터티
        * 응용 프로그램 테스트
    :::column-end:::
    :::column span="":::
      모든 GET Api: 
        * [LUIS 프로그래밍 v 3.0-미리 보기](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [LUIS 프로그래밍 v2.0 Api](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)

      모든 Api: 
        * [LUIS 끝점 Api v2.0](https://chinaeast2.dev.cognitive.azure.cn/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
        * [LUIS 끝점 Api v 3.0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8)
        * [LUIS 끝점 Api v 3.0-미리 보기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a9459a1fe8fa44c28dd8)

      모든 Batch 테스트 웹 Api
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-writer"></a>Cognitive Services LUIS writer

대규모 팀의 협력자로 LUIS 응용 프로그램을 빌드 및 수정 하는 사용자입니다. 협력자는 어떤 방식으로든 LUIS 응용 프로그램을 수정 하 고, 이러한 변경을 학습 하 고, 포털에서 변경 내용을 검증/테스트할 수 있습니다. 그러나이 응용 프로그램을 프로덕션 환경에 실수로 반영할 수 있으므로이 사용자는 런타임에이 응용 프로그램을 배포 하는 데 액세스할 수 없습니다. 또한 응용 프로그램을 삭제 하거나 예측 리소스 및 끝점 설정을 변경할 수 없습니다 (예측 리소스 할당 또는 할당 취소, 끝점 공개). 이렇게 하면이 역할이 프로덕션 환경에서 현재 사용 중인 응용 프로그램을 변경 하지 않도록 제한 합니다. 또한이 리소스에서 새 응용 프로그램을 만들 수 있지만 언급 된 제한 사항이 있습니다.

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
      * Cognitive Services LUIS 판독기의 모든 기능입니다. 

      추가할 수 있는 기능: 
        * 발언
        * 의도
        * 엔터티
    :::column-end:::
    :::column span="":::
      * LUIS reader의 모든 Api

      모든 POST, PUT 및 DELETE Api:

        * [LUIS 프로그래밍 v 3.0-미리 보기](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)
        * [LUIS 프로그래밍 v2.0 Api](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2d)

        제외
        * [애플리케이션 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c39)
        * [다른 LUIS authoring Azure 리소스에 앱 이동](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/apps-move-app-to-another-luis-authoring-azure-resource)
        * [응용 프로그램 게시](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c3b)
        * [ 애플리케이션 설정 업데이트](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58aeface39e2bb03dcd5909e)
        * [응용 프로그램에 LUIS azure 계정 할당](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32228e8473de116325515)
        * [응용 프로그램에서 할당 된 LUIS azure 계정 제거](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5be32554f8591db3a86232e1)
    :::column-end:::
:::row-end:::

### <a name="cognitive-services-luis-owner"></a>Cognitive Services LUIS 소유자

이러한 사용자는 프로덕션 환경에서 LUIS 응용 프로그램에 대 한 게이트 키퍼입니다. 기본 함수에 대 한 모든 액세스 권한이 있어야 하므로 응용 프로그램의 모든 항목을 볼 수 있고 제작 및 런타임 환경에 대 한 모든 변경 내용을 편집 하기 위해 직접 액세스할 수 있습니다.

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
      * Cognitive Services LUIS Writer의 모든 기능
      * 모델 배포
      * 애플리케이션 삭제
    :::column-end:::
    :::column span="":::
      * LUIS에 사용할 수 있는 모든 Api
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>다음 단계

* [Azure 리소스 관리](./luis-how-to-azure-subscription.md?branch=pr-en-us-171715&tabs=portal#authoring-resource)