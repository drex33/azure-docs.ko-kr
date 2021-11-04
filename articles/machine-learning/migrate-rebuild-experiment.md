---
title: 'ML Studio(클래식): Azure Machine Learning으로 마이그레이션 - 실험 다시 빌드'
description: Azure Machine Learning 디자이너에서 Studio(클래식) 실험을 다시 빌드합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 10/21/2021
ms.openlocfilehash: fd0bb1394ce2ce8b8cf8d58ece5834f08e538eaa
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556201"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Azure Machine Learning 디자이너에서 Studio(클래식) 실험 다시 빌드

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

이 문서에서는 Azure Machine Learning에서 ML 스튜디오(클래식) 실험을 다시 빌드하는 방법에 대해 알아봅니다. Studio(클래식)에서 마이그레이션하는 방법에 대한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조하세요.

Studio(클래식) **실험** 은 Azure Machine Learning의 **파이프라인** 과 유사합니다. 하지만 Azure Machine Learning 파이프라인은 SDK를 구동하는 동일한 백엔드를 기반으로 합니다. 따라서 기계 학습 개발에는 끌어서 놓기 디자이너나 코드 중심 SDK, 이상 두 가지 옵션이 있습니다.

SDK를 사용한 파이프라인 빌드에 대한 자세한 내용은 [Azure Machine Learning 파이프라인이란](concept-ml-pipelines.md#building-pipelines-with-the-python-sdk)을 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md#create-a-workspace).
- 마이그레이션할 Studio(클래식) 실험.
- Azure Machine Learning으로 [데이터 세트를 업로드](migrate-register-dataset.md)합니다.

## <a name="rebuild-the-pipeline"></a>파이프라인 다시 빌드

[Azure Machine Learning으로 데이터 세트를 마이그레이션](migrate-register-dataset.md)한 이후 실험을 다시 만들 준비가 됩니다.

Azure Machine Learning에서 시각적 그래프를 **파이프라인 초안** 이라고 합니다. 이 섹션에서는 클래식 실험을 파이프라인 초안으로 다시 만듭니다.

1. Azure Machine Learning 스튜디오([ml.azure.com](https://ml.azure.com))로 이동
1. 왼쪽 탐색 창에서 **디자이너** > **사용하기 쉬운 미리 작성된 모듈** 을 선택합니다. ![새 파이프라인을 만드는 방법을 보여주는 스크린샷.](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. 디자이너 구성 요소를 사용해 실험을 수동으로 다시 빌드합니다.
    
    대체 모듈을 찾으려면 [모듈 매핑 테이블](migrate-overview.md#studio-classic-and-designer-component-mapping)을 참조하세요. 대부분의 Studio(클래식) 모듈은 디자이너에서 동일한 버전을 사용합니다.

    > [!Important]
    > 실험에서 R 스크립트 실행 모듈을 사용하는 경우 실험을 마이그레이션하기 위한 추가 단계를 수행해야 합니다. 자세한 내용은 [R 스크립트 마이그레이션 모듈](migrate-execute-r-script.md)을 참조하세요.

1. 매개 변수를 조정합니다.
    
    각 모듈을 선택하고 모듈 설정 패널에서 오른쪽으로 매개 변수를 조정합니다. 매개 변수를 사용하여 Studio(클래식) 실험의 기능을 다시 만듭니다. 각 모듈에 대한 자세한 내용은 [모듈 참조](./component-reference/component-reference.md)를 참조하세요.

## <a name="submit-a-run-and-check-results"></a>실행 제출 및 결과 확인

Studio(클래식) 실험을 다시 만든 후에는 **파이프라인 실행** 을 제출해야 합니다.

작업 영역에 연결된 **컴퓨팅 대상** 에서 파이프라인 실행이 이루어집니다. 전체 파이프라인에 대한 기본 컴퓨팅 대상을 설정하거나 모듈 단위로 컴퓨팅 대상을 지정할 수 있습니다.

파이프라인 초안에서 실행을 제출하면 **파이프라인 실행** 으로 전환됩니다. 각 파이프라인 실행이 기록되고 Azure Machine Learning에 로그됩니다.

전체 파이프라인에 대한 기본 컴퓨팅 대상을 설정하려면 다음을 수행합니다.
1. 파이프라인 이름 옆에 있는 **기어 아이콘** 을 선택합니다. ![디자이너의 기어 아이콘](./media/tutorial-designer-automobile-price-train-score/gear-icon.png)
1. **컴퓨팅 대상 선택** 을 선택합니다.
1. 기존 컴퓨팅을 선택하거나 화면의 지시에 따라 새 컴퓨팅을 만듭니다.

컴퓨팅 대상이 설정되었으므로 파이프라인 실행을 제출할 수 있습니다.

1. 캔버스 위쪽에서 **제출** 을 선택합니다.
1. **새로 만들기** 를 선택하여 새 실험을 만듭니다.
    
    실험은 유사한 파이프라인 실행을 함께 구성합니다. 파이프라인을 여러 번 실행하는 경우 연속 실행에 대해 동일한 실험을 선택할 수 있습니다. 이는 로깅 및 추적에 유용합니다.
1. 실험 이름을 입력합니다. 그런 다음, **제출** 을 선택합니다.

    첫 번째 실행은 최대 20분 정도 걸릴 수 있습니다. 기본 컴퓨팅 설정의 최소 노드 크기는 0입니다. 즉, 디자이너가 유휴 상태가 된 후에 리소스를 할당해야 합니다. 노드가 이미 할당되었기 때문에 반복되는 실행은 시간이 덜 걸립니다. 실행 시간을 단축하기 위해 최소 노드 크기가 1 이상의 컴퓨팅 리소스를 만들 수 있습니다.

실행이 완료되면 각 모듈의 결과를 확인할 수 있습니다.

1. 출력을 보려는 모듈을 마우스 오른쪽 단추로 클릭합니다.
1. **시각화**, **출력 보기** 또는 **로그 보기** 중 하나를 선택합니다.

    - **시각화**: 결과 데이터 세트를 미리 봅니다.
    - **출력 보기**: 출력 스토리지 위치에 대한 링크를 엽니다. 링크를 사용하여 출력을 탐색하거나 다운로드합니다. 
    - **로그 보기**: 드라이버 및 시스템 로그를 봅니다. **70_driver_log** 를 사용하여 오류 및 예외와 같은 사용자 제출 스크립트와 관련된 정보를 볼 수 있습니다.

> [!IMPORTANT]
> 디자이너 구성 요소는 Studio(클래식)의 C# 패키지와 비교하여 오픈 소스 Python 패키지를 사용합니다. 그 결과 모듈 출력이 디자이너와 Studio(클래식) 사이에서 약간 다를 수 있습니다. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning의 Studio(클래식) 실험을 다시 빌드하는 방법에 대해 알아보았습니다. 다음 단계는 [Azure Machine Learning에서 웹 서비스를 다시 빌드](migrate-rebuild-web-service.md)하는 것입니다.


Studio(클래식) 마이그레이션 시리즈의 다른 문서를 참조하세요.

1. [마이그레이션 개요](migrate-overview.md)입니다.
1. [마이그레이션 데이터세트](migrate-register-dataset.md)
1. **Studio(클래식) 학습 파이프라인을 다시 빌드** 합니다.
1. [Studio(클래식) 웹 서비스를 다시 빌드](migrate-rebuild-web-service.md)합니다.
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. [R 스크립트 실행을 마이그레이션](migrate-execute-r-script.md)합니다.