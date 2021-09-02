---
title: Azure Percept Studio에서 코드 없는 비전 솔루션 만들기
description: Azure Percept Studio에서 코드 없는 비전 솔루션을 만들고 Azure Percept DK에 배포하는 방법에 대해 알아봅니다.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: a8d1ce721c1bd67895661be25ac0bab3c230839b
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227199"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Azure Percept Studio에서 코드 없는 비전 솔루션 만들기

Azure Percept Studio를 사용하면 코딩 없이 사용자 지정 컴퓨터 비전 솔루션을 빌드하고 배포할 수 있습니다. 이 문서에서는 다음을 수행합니다.

- [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)에서 비전 프로젝트 만들기
- Devkit를 사용하여 학습 이미지 수집
- [Custom Vision](https://www.customvision.ai/)에서 학습 이미지에 레이블 지정
- 사용자 지정 개체 감지 또는 분류 모델 학습
- Devkit에 모델 배포
- 재학습을 설정하여 모델 개선

이 자습서는 AI 경험이 거의 또는 전혀 없는 개발자 및 Azure Percept를 막 시작한 개발자에게 적합합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(Devkit)
- [Azure 구독](https://azure.microsoft.com/free/)
- Azure Percept DK 설정 환경: devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결

## <a name="create-a-vision-prototype"></a>비전 프로토타입 만들기

1. 브라우저를 시작하고 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)로 이동합니다.

1. 개요 페이지에서 **데모 및 자습서** 탭을 클릭합니다. :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Azure Percept Studio 개요 화면." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. **비전 자습서 및 데모** 에서 **비전 프로토타입 만들기** 를 클릭합니다.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Azure Percept Studio 데모 및 자습서 화면" lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. **새 Azure Percept Custom Vision 프로토타입** 페이지에서 다음을 수행합니다.

    1. **프로젝트 이름** 상자에 비전 프로토타입의 이름을 입력합니다.

    1. **프로젝트 설명** 상자에 비전 프로토타입에 대한 설명을 입력합니다.

    1. **디바이스 유형** 드롭다운 메뉴에서 **Azure Percept DK** 를 선택합니다.

    1. **리소스** 드롭다운 메뉴에서 리소스를 선택하거나 **새 리소스 만들기** 를 클릭합니다. 새 리소스를 만들기로 선택한 경우 **만들기** 창에서 다음을 수행합니다.
        1. 새 리소스의 이름을 입력합니다.
        1. Azure 구독을 선택합니다.
        1. 리소스 그룹을 선택하거나 새로 만듭니다.
        1. 기본 설정 영역을 선택합니다.
        1. 가격 책정 계층을 선택합니다(S0 권장).
        1. 창의 맨 아래에서 **만들기** 를 클릭합니다.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="리소스 만들기 창":::

    1. **프로젝트 형식** 에서 비전 프로젝트가 개체 감지 또는 이미지 분류 중 무엇을 수행할지 선택합니다. 프로젝트 형식에 대한 자세한 내용을 보려면 **선택 안내** 를 클릭하세요.

    1. **최적화** 의 경우 정확도, 낮은 네트워크 대기 시간 또는 둘 다의 균형 중 무엇을 위해 프로젝트를 최적화할지 선택합니다.

    1. **만들기** 단추를 클릭합니다.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Custom Vision 프로토타입 화면을 만듭니다.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>디바이스를 프로젝트에 연결하고 이미지 캡처

비전 솔루션을 만든 후에는 Devkit 및 해당 IoT Hub를 비전 솔루션에 추가해야 합니다.

1. Devkit의 전원을 켭니다.

1. **IoT Hub** 드롭다운 메뉴에서 OOBE 중에 Devkit가 연결된 IoT 허브를 선택합니다.

1. **디바이스** 드롭다운 메뉴에서 해당 Devkit를 선택합니다.

다음으로, AI 모델 학습을 위한 이미지를 로드하거나 이미지를 캡처해야 합니다. 태그 유형별 이미지를 30개 이상 업로드하는 것이 좋습니다. 예를 들어 개와 고양이 감지기를 빌드하려면 개 이미지 30개와 고양이 이미지 30개 이상을 업로드해야 합니다. Devkit의 비전 SoM을 사용하여 이미지를 캡처하려면 다음을 수행합니다.

1. **이미지 캡처** 창에서 **디바이스 스트림 보기** 를 선택하여 비전 SoM 비디오 스트림을 살펴봅니다.

1. 비디오 스트림을 살펴보고 비전 SoM 카메라가 학습 사진을 찍기에 알맞게 정렬되었는지 확인합니다. 필요에 맞게 조정합니다.

1. **이미지 캡처** 창에서 **사진 찍기** 를 클릭합니다.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="이미지 캡처 화면":::

1. 또는 **자동 이미지 캡처** 상자를 선택하여 한 번에 많은 양의 이미지를 수집하도록 자동화된 이미지 캡처를 설정합니다. **캡처 속도** 에서 원하는 이미징 속도를 선택하고 **대상** 에서 수집할 총 이미지 수를 선택합니다. **자동 캡처 설정** 을 클릭하여 자동화된 이미지 캡처 프로세스를 시작합니다.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="자동화된 이미지 캡처 드롭다운 메뉴":::

사진이 충분하면 화면 아래쪽에서 **다음: 이미지에 태그 지정 및 모델 학습** 을 클릭합니다. 모든 이미지는 [Custom Vision](https://www.customvision.ai/)에 저장됩니다.

> [!NOTE]
> 학습 이미지를 Custom Vision에 직접 업로드하도록 선택하면 이미지 파일 크기가 6MB를 초과할 수 없습니다.

## <a name="tag-images-and-train-your-model"></a>이미지에 태그 지정 및 모델 학습

모델을 학습시키기 전에 이미지에 레이블을 추가합니다.

1. **이미지에 태그 지정 및 모델 학습** 페이지의 **Custom Vision에서 프로젝트 열기** 를 클릭합니다.

1. **Custom Vision** 페이지 왼쪽의 **태그** 아래에서 **태그 없음** 을 클릭하여 이전 단계에서 방금 수집한 이미지를 살펴봅니다. 태그가 없는 이미지를 하나 이상 선택합니다.

1. **이미지 세부 정보** 창에서 태그 지정을 시작할 이미지를 클릭합니다. 프로젝트 형식으로 개체 감지를 선택한 경우 태그를 지정할 특정 개체 주위에 [경계 상자](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images)를 그려야 합니다. 필요에 따라 경계 상자를 조정합니다. 개체 태그를 입력하고 **+** 를 클릭하여 태그를 적용합니다. 예를 들어 매장 선반에 재고 보충이 필요할 때 알려주는 비전 솔루션을 만드는 경우에는 빈 선반의 이미지에 "Empty Shelf" 태그를 추가하고 재고가 가득 찬 선반의 이미지에 "Full Shelf" 태그를 추가합니다. 태그가 없는 모든 이미지에 대해 반복합니다.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Custom Vision의 이미지 태그 지정 화면":::

1. 이미지에 태그를 지정한 후 창의 오른쪽 위 모서리에 있는 **X** 아이콘을 클릭합니다. **태그** 아래에서 **태그가 지정됨** 을 클릭하여 새로 태그가 지정된 모든 이미지를 표시합니다.

1. 이미지에 레이블이 지정되면 AI 모델을 학습시킬 준비가 된 것입니다. 이렇게 하려면 페이지 위쪽에 있는 **학습** 을 클릭합니다. 모델을 학습시키려면 태그 유형별 이미지가 15개 이상 있어야 합니다(30개 이상 사용하는 것이 좋음). 학습은 일반적으로 30분 정도 걸리지만 이미지 집합이 매우 크면 시간이 오래 걸릴 수 있습니다.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="강조 표시된 학습 단추로 학습 이미지 선택":::

1. 학습이 완료되면 화면에 모델 성능이 표시됩니다. 이러한 결과를 평가하는 방법에 대한 자세한 내용은 [모델 평가 설명서](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector)를 참조하세요. 학습 후에는 추가 이미지에 대해 [모델을 테스트](../cognitive-services/custom-vision-service/test-your-model.md)하고 필요에 따라 다시 학습시킬 수도 있습니다. 모델을 학습시킬 때마다 새 반복으로 저장됩니다. 모델 성능을 향상시키는 방법에 대한 자세한 내용은 [Custom Vision 설명서](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)를 참조하세요.

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="모델 학습 결과":::

    > [!NOTE]
    > Custom Vision에서 추가 이미지에 대해 모델을 테스트하도록 선택하면 테스트 이미지 파일 크기가 4MB를 초과할 수 없습니다.

모델 성능에 만족하면 브라우저 탭을 닫아서 Custom Vision을 닫습니다.

## <a name="deploy-your-ai-model"></a>AI 모델 배포

1. Azure Percept Studio 탭으로 돌아가서 화면 아래쪽에 있는 **다음: 평가 및 배포** 를 클릭합니다.

1. **평가 및 배포** 창에 선택한 모델 반복의 성능이 표시됩니다. **모델 반복** 드롭다운 메뉴 아래에서 Devkit에 배포할 반복을 선택하고 화면 아래쪽에 있는 **모델 배포** 를 클릭합니다.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="모델 배포 화면" lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. 모델을 배포한 후 디바이스의 비디오 스트림을 살펴보고 모델 추론이 작동하는지 확인합니다.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="작동 중인 헤드폰 감지기를 보여주는 디바이스 스트림":::

이 창을 닫은 후 언제든지 Azure Percept Studio 홈페이지의 **AI 프로젝트** 에서 **비전** 을 클릭하고 비전 프로젝트의 이름을 선택하면 비전 프로젝트로 돌아가서 편집할 수 있습니다.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="비전 프로젝트 페이지" lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>재학습을 설정하여 모델 개선

모델을 학습하여 디바이스에 배포한 후에는 더 많은 학습 데이터를 캡처하도록 재학습 매개 변수를 설정하여 모델 성능을 개선할 수 있습니다. 이 기능은 확률 범위에 따라 이미지를 캡처하는 기능을 제공하여 학습된 모델의 성능을 개선하는 데 사용됩니다. 예를 들어 확률이 낮을 때만 학습 이미지를 캡처하도록 디바이스를 설정할 수 있습니다. 더 많은 이미지를 추가하고 학습 데이터를 분산하는 방법에 대한 몇 가지 [추가 지침](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)은 다음과 같습니다.

1. 재학습을 설정하려면 **프로젝트** 로 돌아간 다음 **프로젝트 요약** 으로 이동합니다.
1. **이미지 캡처** 탭에서 **자동 이미지 캡처**, **재학습 설정** 을 차례로 선택합니다.
1. **자동 이미지 캡처** 상자를 선택하여 한 번에 많은 이미지를 수집하도록 하는 자동화된 이미지 캡처를 설정합니다.
1. **캡처 속도** 에서 원하는 이미징 속도를 선택하고 **대상** 에서 수집할 총 이미지 수를 선택합니다.
1. **재학습 설정** 섹션에서 더 많은 학습 데이터를 캡처할 반복을 선택한 다음 확률 범위를 선택합니다. 확률을 만족하는 이미지만 프로젝트에 업로드됩니다.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="이미지 캡처.":::

## <a name="clean-up-resources"></a>리소스 정리

이 자습서용으로 새 Azure 리소스를 만들었지만 비전 솔루션을 더 이상 개발하거나 사용하지 않으려면 다음 단계를 수행하여 리소스를 삭제하세요.

1. [Azure 포털](https://ms.portal.azure.com/)로 이동합니다.
1. **모든 리소스** 를 클릭합니다.
1. 이 자습서에서 만든 리소스 옆에 있는 확인란을 클릭합니다. 리소스 종류는 **Cognitive Services** 로 나열됩니다.
1. 화면 위쪽의 **삭제** 아이콘을 클릭합니다.

## <a name="video-walkthrough"></a>연습 동영상

위에서 설명한 단계에 대한 시각적 연습은 다음 비디오를 참조하세요.

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>다음 단계

다음으로, Azure Percept Studio의 추가 비전 솔루션 기능에 대한 자세한 내용은 비전 방법 문서를 확인하세요.

<!--
Add links to how-to articles and oobe article.
-->