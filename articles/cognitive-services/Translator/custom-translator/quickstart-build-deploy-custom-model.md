---
title: '빠른 시작: 사용자 지정 모델 빌드, 배포 및 사용 - Custom Translator'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Custom Translator를 사용하여 번역 시스템을 구축하는 단계별 프로세스를 안내합니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 12/09/2019
ms.author: lajanuar
ms.topic: quickstart
ms.openlocfilehash: fec2ae2f5f06303d48df77a34bcf41e4ab9e83f5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130252234"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>빠른 시작: 번역을 위한 사용자 지정 모델 빌드, 배포 및 사용

이 문서에서는 Custom Translator를 사용하여 번역 시스템을 구축하는 단계별 지침을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Custom Translator](https://portal.customtranslator.azure.ai) 포털을 사용하려면 [Microsoft 계정](https://signup.live.com) 또는 [Azure AD 계정](../../../active-directory/fundamentals/active-directory-whatis.md)(Azure에 호스트되는 조직 계정)으로 로그인해야 합니다.

2. Azure Portal 통해 Translator Text API 구독. Custom Translator에서 작업 영역과 연결할 Translator Text API 구독 키가 필요합니다. [Translator Text API에 등록하는 방법](../translator-how-to-signup.md)을 참조하세요.

3. 위의 두 가지가 모두 있는 경우 [사용자 지정 변환기](https://portal.customtranslator.azure.ai) 포털에 로그인하여 작업 영역, 프로젝트, 파일 업로드 및 모델 만들기/배포를 만듭니다.

번역 및 사용자 지정 번역에 대한 개요를 읽고, 몇 가지 팁을 알아보고, [Azure AI 기술 블로그](https://techcommunity.microsoft.com/t5/azure-ai/customize-a-translation-to-make-sense-in-a-specific-context/ba-p/2811956)에서 시작 비디오를 시청할 수 있습니다. 

[YouTube](https://www.youtube.com/watch?v=TykB6WDTkRc&t=3s)에서 Custom Translator 연습 비디오 전체를 처음부터 끝까지 볼 수도 있습니다.

>[!Note]
>Custom Translator는 [활성화된 VNET](../../../api-management/api-management-using-with-vnet.md) 내에서 생성된 Translator Text API 리소스에 대한 작업 영역 생성을 지원하지 않습니다.

## <a name="create-a-workspace"></a>작업 영역 만들기

첫 사용자인 경우 서비스 약관에 동의하여 작업 영역을 만들고, 작업 영역을 Microsoft Translator Text API 구독과 연결하라는 메시지가 표시됩니다.

![작업 영역 만들기](media/quickstart/terms-of-service.png)
![작업 영역 만들기 이미지 1](media/quickstart/create-workspace-1.png)
![작업 영역 만들기 이미지 2](media/quickstart/create-workspace-2.png)
![작업 영역 만들기 이미지 3](media/quickstart/create-workspace-3.png)
![작업 영역 만들기 이미지 4](media/quickstart/create-workspace-4.png)
![작업 영역 만들기 이미지 5](media/quickstart/create-workspace-5.png)
![작업 영역 만들기 이미지 6](media/quickstart/create-workspace-6.png)

이후에 Custom Translator 포털 방문 시 작업 영역을 관리하고, 추가 작업 영역을 만들고, Microsoft Translator Text API 구독 키를 작업 영역에 연결하고, 공동 소유자를 추가하고, 구독 키를 변경할 수 있는 설정 페이지로 이동합니다.

## <a name="create-a-project"></a>프로젝트 만들기

Custom Translator 포털 방문 페이지에서 [새 프로젝트]를 클릭합니다. 대화 상자에서 원하는 프로젝트 이름, 언어 쌍 및 범주와 기타 관련 필드를 입력할 수 있습니다. 그런 다음, 프로젝트를 저장합니다. 자세한 내용은 [프로젝트 만들기](how-to-create-project.md)를 참조하세요.

![프로젝트 만들기](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>문서 업로드

다음으로, [교육](training-and-model.md#training-document-type-for-custom-translator), [튜닝](training-and-model.md#tuning-document-type-for-custom-translator) 및 [테스트](training-and-model.md#testing-dataset-for-custom-translator) 문서 집합을 업로드합니다. 두 [병렬](what-are-parallel-documents.md) 및 콤보 문서를 모두 업로드할 수 있습니다. [사전](what-is-dictionary.md)도 업로드할 수 있습니다.

두 문서 탭 또는 특정 프로젝트 페이지의 문서를 업로드할 수 있습니다.

![문서 업로드](media/quickstart/ct-how-to-upload.png)

문서를 업로드할 때 문서 유형(교육, 튜닝 또는 테스트)과 언어 쌍을 선택합니다. 병렬 문서를 업로드할 때 문서 이름을 추가로 지정해야 합니다. 자세한 내용은 [문서 업로드](how-to-upload-document.md)를 참조하세요.

## <a name="create-a-model"></a>모델 만들기

필수 문서가 모두 업로드되면 그 다음 단계는 모델 빌드입니다.

앞에서 만든 프로젝트를 선택합니다. 업로드한 모든 문서가 이 프로젝트와 언어 쌍을 공유하는 것을 볼 수 있습니다. 모델에 포함할 문서를 선택합니다. [교육](training-and-model.md#training-document-type-for-custom-translator), [튜닝](training-and-model.md#tuning-document-type-for-custom-translator) 및 [테스트](training-and-model.md#testing-dataset-for-custom-translator) 데이터를 선택할 수도 있고, 교육 데이터만 선택하고 Custom Translator가 자동으로 모델의 튜닝 및 테스트 세트를 빌드하게 할 수도 있습니다.

![모델 만들기](media/quickstart/ct-how-to-train.png)

원하는 문서를 선택했으면 모델 만들기 단추를 클릭하여 모델을 만들고 교육을 시작합니다. 교육 상태 및 교육한 모델에 대한 모든 상세 정보를 모델 탭에서 볼 수 있습니다.

자세한 내용은 [모델 만들기](how-to-train-model.md)를 참조하세요.

## <a name="analyze-your-model"></a>모델 분석

교육이 성공적으로 완료되면 결과를 검사합니다. BLEU 점수는 번역 품질을 나타내는 메트릭의 하나입니다. "테스트" 탭으로 이동한 후 "시스템 결과"를 클릭하여 사용자 지정 모델을 사용한 번역을 테스트 집합에 제공된 번역과 수동으로 비교할 수도 있습니다. 이러한 몇 가지 번역을 수동으로 검사하면 시스템에서 생성된 번역 품질을 높이는 좋은 아이디어가 떠오를 수 있습니다. 자세한 내용은 [시스템 테스트 결과](how-to-view-system-test-results.md)를 참조하세요.

## <a name="deploy-a-trained-model"></a>학습된 모델 배포

학습된 모델을 배포할 준비가 완료되면 "배포" 단추를 클릭합니다. 프로젝트당 하나의 모델을 배포할 수 있으며, [상태] 열에서 배포 상태를 볼 수 있습니다. 자세한 내용은 [모델 배포](how-to-view-system-test-results.md#deploy-a-model)를 참조하세요.

![학습된 모델 배포](media/quickstart/ct-how-to-deploy.png)

## <a name="swap-deployed-model"></a>배포된 모델 교환

배포된 모델을 프로젝트 내에서 다른 모델과 교환하려면 원하는 모델 옆에 표시된 "교환" 단추를 클릭합니다. 교환 프로세스가 진행되는 동안 배포된 모델은 번역 요청을 처리하는 데 계속 사용할 수 있습니다. 

![배포된 모델 교환](media/quickstart/ct-how-to-swap-model.png)

## <a name="use-a-deployed-model"></a>배포된 모델 사용

배포된 모델은 [CategoryID를 검사하여 Microsoft Translator Text API V3](../reference/v3-0-translate.md?tabs=curl)를 통해 액세스할 수 있습니다. Translator Text API에 대한 자세한 내용은 [API 참조](../reference/v3-0-reference.md) 웹 페이지에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Custom Translator 작업 영역으로 이동하여 프로젝트를 관리](workspace-and-project.md)하는 방법을 알아보세요.
