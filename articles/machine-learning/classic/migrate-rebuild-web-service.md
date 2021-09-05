---
title: 'ML Studio(클래식): Azure Machine Learning으로 마이그레이션 - 웹 서비스 다시 빌드'
description: Azure Machine Learning에서 파이프라인 엔드포인트로 Studio(클래식) 웹 서비스 다시 빌드
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: a00a348eafff7e6d49669de45c3f458905f33b70
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580155"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Azure Machine Learning에서 Studio(클래식) 웹 서비스 다시 빌드

이 문서에서는 [Azure Machine Learning](../index.yml)의 **엔드포인트** 로 Studio(클래식) 웹 서비스를 다시 빌드하는 방법에 대해 알아봅니다.

Azure Machine Learning 파이프라인 엔드포인트를 사용하여 예측을 만들거나 모델을 다시 학습하거나 제네릭 파이프라인을 실행할 수 있습니다. REST 엔드포인트를 사용하면 어떤 플랫폼에서든 파이프라인을 실행할 수 있습니다. 

이 문서는 Azure Machine Learning 마이그레이션 시리즈에 대한 Studio(클래식)의 일부입니다. Azure Machine Learning으로 마이그레이션하는 방법에 대한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조하세요.

> [!NOTE]
> 이 마이그레이션 시리즈는 끌어서 놓기 디자이너를 집중적으로 다룹니다. 프로그래밍 방식으로 모델을 배포하는 방법에 대한 자세한 내용은 [Azure에서 기계 학습 모델 배포](../how-to-deploy-and-where.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](../how-to-manage-workspace.md#create-a-workspace).
- Azure Machine Learning 교육 파이프라인. 자세한 내용은 [Azure Machine Learning에서 Studio(클래식) 실험 다시 빌드](migrate-rebuild-experiment.md)를 참조하세요.

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>실시간 엔드포인트와 파이프라인 엔드포인트

Studio(클래식) 웹 서비스는 Azure Machine Learning의 **엔드포인트** 로 대체되었습니다. 다음 표를 통해 사용할 엔드포인트 유형을 선택합니다.

|Studio(클래식) 웹 서비스| Azure Machine Learning 대체
|---|---|
|웹 서비스 요청/응답(실시간 예측)|실시간 엔드포인트|
|Batch 웹 서비스(일괄 처리 예측)|파이프라인 엔드포인트|
|다시 학습 웹 서비스(다시 학습)|파이프라인 엔드포인트| 


## <a name="deploy-a-real-time-endpoint"></a>실시간 엔드포인트 배포

Studio(클래식)에서는 **웹 서비스 요청/응답** 을 사용하여 실시간 예측에 대한 모델을 배포했습니다. Azure Machine Learning에서는 **실시간 엔드포인트** 를 사용합니다.

Azure Machine Learning에서 모델을 배포하는 방법에는 여러 가지가 있습니다. 가장 간단한 방법 중 하나는 디자이너를 사용하여 배포 프로세스를 자동화하는 것입니다. 모델을 실시간 엔드포인트로 배포하려면 다음 단계를 사용합니다.

1. 완료된 교육 파이프라인을 한 번 이상 실행합니다.
1. 실행이 완료된 후 캔버스의 맨 위에서 **유추 파이프라인 만들기** > **실시간 유추 파이프라인** 을 선택합니다.

    ![실시간 유추 파이프라인 만들기](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    디자이너가 학습 파이프라인을 실시간 유추 파이프라인으로 변환합니다. Studio(클래식)에서도 비슷한 변환이 수행됩니다.

    디자이너에서 변환 단계는 [학습된 모델을 Azure Machine Learning 작업 영역에도 등록](../how-to-deploy-and-where.md#registermodel)합니다.

1. **제출** 을 선택하여 실시간 유추 파이프라인을 실행하고 성공적으로 실행되는지 확인합니다.

1. 유추 파이프라인을 확인한 후 **배포** 를 선택합니다.

1. 엔드포인트의 이름과 컴퓨팅 형식을 입력합니다.

    다음 표에서는 디자이너의 배포 컴퓨팅 옵션에 대해 설명합니다.

    | 컴퓨팅 대상 | 사용 대상 | Description | 생성 |
    | ----- |  ----- | ----- | -----  |
    |[AKS(Azure Kubernetes Service)](../how-to-deploy-azure-kubernetes-service.md) |실시간 유추|대규모 프로덕션 배포. 빠른 응답 시간 및 서비스 자동 크기 조정| 사용자가 생성함. 자세한 내용은 [컴퓨팅 대상 만들기](../how-to-create-attach-compute-studio.md#inference-clusters)를 참조하세요. |
    |[Azure Container Instances ](../how-to-deploy-azure-container-instance.md)|테스트 또는 개발 | 48GB 미만의 RAM이 필요한 소규모 CPU 기반 워크로드| Azure Machine Learning에 의해 자동으로 생성됨

### <a name="test-the-real-time-endpoint"></a>실시간 엔드포인트 테스트

배포가 완료되면 자세한 내용을 확인하고 엔드포인트를 테스트할 수 있습니다.

1. **엔드포인트** 탭으로 이동합니다.
1. 엔드포인트를 선택합니다.
1. **테스트** 탭을 선택합니다.
    
    ![엔드포인트 테스트 단추가 있는 엔드포인트 탭을 보여 주는 스크린샷](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>일괄 처리 예측 또는 다시 학습을 위한 파이프라인 엔드포인트 게시

또한 학습 파이프라인을 사용하여 실시간 엔드포인트 대신 **파이프라인 엔드포인트** 를 만들 수 있습니다. **파이프라인 엔드포인트** 를 사용하여 일괄 처리 예측 또는 다시 학습을 수행할 수 있습니다.

파이프라인 엔드포인트가 Studio(클래식) **일괄 처리 실행 엔드포인트** 및 **다시 학습 웹 서비스** 를 대체합니다.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>일괄 처리 예측을 위한 파이프라인 엔드포인트 게시

일괄 처리 예측 엔드포인트 게시는 실시간 엔드포인트와 비슷합니다.

일괄 처리 예측을 위해 파이프라인 엔드포인트를 게시하려면 다음 단계를 사용합니다.

1. 완료된 교육 파이프라인을 한 번 이상 실행합니다.

1. 실행이 완료된 후 캔버스의 맨 위에서 **유추 파이프라인 만들기** > **일괄 처리 유추 파이프라인** 을 선택합니다.

    ![학습 파이프라인에서 유추 파이프라인 만들기 단추를 보여 주는 스크린샷](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    디자이너가 학습 파이프라인을 일괄 처리 유추 파이프라인으로 변환합니다. Studio(클래식)에서도 비슷한 변환이 수행됩니다.

    디자이너에서 이 단계는 [학습된 모델을 Azure Machine Learning 작업 영역에도 등록](../how-to-deploy-and-where.md#registermodel)합니다.

1. **제출** 을 선택하여 일괄 처리 유추 파이프라인을 실행하고 성공적으로 완료되는지 확인합니다.

1. 유추 파이프라인을 확인한 후 **게시** 를 선택합니다.
 
1. 새 파이프라인 엔드포인트를 만들거나 기존 파이프라인 엔드포인트를 선택합니다.
    
    새 파이프라인 엔드포인트는 파이프라인에 대한 새 REST 엔드포인트를 만듭니다. 

    기존 파이프라인 엔드포인트를 선택하는 경우 기존 파이프라인을 덮어쓰지 않습니다. 대신 Azure Machine Learning에서 엔드포인트의 각 파이프라인 버전을 지정합니다. REST 호출에서 실행할 버전을 지정할 수 있습니다. REST 호출에서 버전을 지정하지 않는 경우에도 기본 파이프라인을 설정해야 합니다.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>다시 학습을 위한 파이프라인 엔드포인트 게시

다시 학습을 위한 파이프라인 엔드포인트를 게시하려면 모델을 학습하는 파이프라인 초안이 이미 있어야 합니다. 학습 파이프라인을 빌드하는 방법에 대한 자세한 내용은 [Studio(클래식) 실험 다시 빌드](migrate-rebuild-experiment.md)를 참조하세요.

다시 학습을 위해 파이프라인 엔드포인트를 다시 사용하려면 입력 데이터 세트에 대한 **파이프라인 매개 변수** 를 만들어야 합니다. 이렇게 하면 모델을 다시 학습할 수 있도록 학습 데이터 세트를 동적으로 설정할 수 있습니다.

다시 학습 파이프라인 엔드포인트를 게시하려면 다음 단계를 사용합니다.

1. 학습 파이프라인을 한 번 이상 실행합니다.
1. 실행이 완료되면 데이터 세트 모듈을 선택합니다.
1. 모듈 세부 정보 창에서 **파이프라인 매개 변수로 설정** 을 선택합니다.
1. "InputDataset"와 같은 설명적인 이름을 제공합니다.    

    ![파이프라인 매개 변수를 만드는 방법을 보여 주는 스크린샷](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    이렇게 하면 입력 데이터 세트에 대한 파이프라인 매개 변수가 생성됩니다. 학습을 위해 파이프라인 엔드포인트를 호출하는 경우 새 데이터 세트를 지정하여 모델을 다시 학습시킬 수 있습니다.

1. **게시** 를 선택합니다.

    ![학습 파이프라인에서 게시 단추를 강조 표시하는 스크린샷](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Studio에서 파이프라인 엔드포인트 호출

일괄 처리 유추나 다시 학습 파이프라인 엔드포인트를 만든 후 브라우저에서 직접 엔드포인트를 호출할 수 있습니다.

1. **파이프라인 탭** 으로 이동하고 **파이프라인 엔드포인트** 를 선택합니다.
1. 실행할 파이프라인 엔드포인트를 선택합니다.
1. **제출** 을 선택합니다.

    **제출** 을 선택한 후 파이프라인 매개 변수를 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Machine Learning의 Studio(클래식) 웹 서비스를 다시 빌드하는 방법에 대해 알아보았습니다. 다음 단계는 [웹 서비스와 클라이언트 앱을 통합](migrate-rebuild-integrate-with-client-app.md)하는 것입니다.


Studio(클래식) 마이그레이션 시리즈의 다른 문서를 참조하세요.

1. [마이그레이션 개요](migrate-overview.md)입니다.
1. [마이그레이션 데이터세트](migrate-register-dataset.md)
1. [Studio(클래식) 학습 파이프라인을 다시 빌드](migrate-rebuild-experiment.md)합니다.
1. **Studio(클래식) 웹 서비스를 다시 빌드** 합니다.
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. [R 스크립트 실행을 마이그레이션](migrate-execute-r-script.md)합니다.
