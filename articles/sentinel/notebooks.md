---
title: Azure Sentinel에서 Notebook을 사용한 보안 헌팅
description: 이 문서에서는 Azure Sentinel 헌팅 기능과 Notebook을 사용하는 방법을 설명합니다.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 05/19/2021
ms.openlocfilehash: 1746398147e8f504b9e0c6aafd65507ef6a4b464
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749600"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Jupyter Notebook을 사용하여 보안 위협 헌팅

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel의 기반은 데이터 저장소입니다. 데이터 저장소는 고성능 쿼리와 동적 스키마를 결합하고 대규모 데이터 볼륨으로 확장합니다. Azure Portal과 모든 Azure Sentinel 도구는 공통 API를 사용하여 데이터 저장소에 액세스합니다. 동일한 API를 [Jupyter](https://jupyter.org/) Notebook 및 Python과 같은 외부 도구에도 사용할 수 있습니다. 포털에서 다수의 일반적인 작업을 수행할 수 있지만 Jupyter는 데이터로 수행할 수 있는 작업의 범위를 확장합니다. 기계 학습, 시각화 및 데이터 분석을 위해 방대한 라이브러리 컬렉션과 전체 프로그래밍 기능을 결합합니다. 이러한 특성 덕분에 Jupyter는 보안 조사 및 헌팅에 맞는 강력한 도구가 될 수 있습니다.

![예제 Notebook](./media/notebooks/sentinel-notebooks-map.png)

Jupyter 환경이 Azure Portal에 통합되어 있어서 Notebook을 쉽게 만들고 실행하여 데이터를 분석할 수 있습니다. *Kqlmagic* 라이브러리는 Azure Sentinel에서 쿼리를 가져와서 Notebook 내에서 직접 실행할 수 있는 수단을 제공합니다. 쿼리는 [Kusto 쿼리 언어](https://kusto.azurewebsites.net/docs/kusto/query/index.html)를 사용합니다. 일부 Microsoft 보안 분석가가 개발한 몇 가지 Notebook이 Azure Sentinel에 패키징되어 있습니다. 이러한 Notebook 중 일부는 특정 시나리오에 맞게 빌드되어 있어서 그대로 사용할 수 있습니다. 그 외 Notebook은 기술과 기능을 설명하는 샘플로 제공되며, 사용자가 Notebook에서 사용하기 적합하게 수정하거나 복사할 수 있습니다. Azure Sentinel Community GitHub에서 다른 Notebook을 가져올 수도 있습니다.

통합 Jupyter 환경에서는 [Azure Notebooks](https://notebooks.azure.com/)를 사용하여 Notebook을 저장, 공유 및 실행합니다. 컴퓨터에 Python 환경과 Jupyter가 있거나 Azure Databricks와 같은 기타 JupterHub 환경인 경우에는 이러한 Notebook을 로컬에서 실행할 수도 있습니다.

Notebook의 두 가지 구성 요소는 다음과 같습니다.

- 쿼리와 코드를 입력 및 실행하고, 실행 결과가 표시되는 브라우저 기반 인터페이스
- 코드 자체의 구문 분석 및 실행을 담당하는 *커널*

Azure Sentinel Notebook의 커널은 Azure VM(가상 머신)에서 실행됩니다. Notebook에 복잡한 기계 학습 모델이 포함되어 있는 경우 더 강력한 가상 머신을 활용할 수 있는 라이선스 옵션이 몇 가지 있습니다.

Azure Sentinel Notebook은 pandas, matplotlib, bokeh 등 널리 사용되는 다양한 Python 라이브러리를 사용합니다. 그 외 다음과 같은 영역을 지원하는 다양한 Python 패키지를 선택할 수 있습니다.

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 수치 계산
- 기계 학습 및 딥 러닝

[msticpy](https://github.com/Microsoft/msticpy/)라는 패키지에 오픈 소스 Jupyter 보안 도구도 릴리스되었습니다. 이 패키지는 포함된 많은 Notebook에서 사용됩니다. Msticpy 도구는 구체적으로 헌팅 및 조사를 위한 Notebook을 만드는 데 도움이 되도록 설계되었으며, 당사는 새로운 기능과 개선을 위해 적극적으로 노력하고 있습니다. 자세한 내용은 [MSTIC Jupyter 및 Python 보안 도구 설명서](https://msticpy.readthedocs.io/)를 참조하세요.

[Azure Sentinel Community GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel)는 Microsoft가 구축하거나 커뮤니티가 제공하는 향후 Azure Sentinel Notebook을 위한 공간입니다.

Notebook을 사용하려면 먼저 Azure ML(Machine Learning) 작업 영역을 만들어야 합니다.

## <a name="create-an-azure-ml-workspace"></a>Azure ML 작업 영역 만들기

1. Azure Portal에서 **Azure Sentinel** > **위협 관리** > **Notebooks** 로 이동한 다음, **Notebook 시작** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Notebook을 시작하여 Azure ML 작업 영역 시작](./media/notebooks/sentinel-notebooks-launch.png)

1. **AzureML 작업 영역** 에서 **새로 만들기** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![작업 영역 만들기](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. **Machine Learning** 페이지에서 다음 정보를 입력한 후 **검토 + 만들기** 를 선택합니다.

    |필드|Description|
    |--|--|
    |Subscription|사용할 Azure 구독을 선택합니다.|
    |Resource group|구독의 기존 리소스 그룹을 사용하거나 이름을 입력하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유합니다. 이 예제에서는 **AzureMLRG** 를 사용합니다.|
    |작업 영역 이름|작업 영역을 식별하는 고유한 이름을 입력합니다. 이 예제에서는 **testworkspace1** 을 사용합니다. 이름은 리소스 그룹 전체에서 고유해야 합니다. 다른 사용자가 만든 작업 영역과 구별되고 기억하기 쉬운 이름을 사용하세요.|
    |지역|사용자 및 데이터 리소스와 가장 가까운 위치를 선택하여 작업 영역을 만듭니다.|
    |Workspace Edition|이 예제에서 작업 영역 형식으로 **기본** 을 선택합니다. 작업 영역 형식(기본 및 엔터프라이즈)에 따라 액세스 및 가격을 결정하는 기능이 달라집니다.|

    > [!div class="mx-imgBorder"]
    > ![작업 영역 세부 정보 제공](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. 정보를 검토하고 올바른지 확인한 다음 **만들기** 를 선택하여 작업 영역의 배포를 시작합니다.

    > [!div class="mx-imgBorder"]
    > ![작업 영역 세부 정보 검토](./media/notebooks/sentinel-notebooks-azureml-review.png)

    **개요** 페이지에 현재 배포 상태가 표시되는 동안 클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![작업 영역 배포](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

배포가 완료되면 새 Azure ML 작업 영역에서 Notebook을 시작할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![작업 영역 배포 성공](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Azure ML 작업 영역을 사용하여 노트북 시작

1. Azure Portal에서 **Azure Sentinel** > **위협 관리** > **Notebook** 으로 이동하면 Azure Sentinel이 제공하는 Notebook을 볼 수 있습니다.

    > [!TIP]
    > **가이드 및 피드백** 을 선택하여 Notebook에 대한 추가 도움말과 지침이 포함된 창을 엽니다.
    > ![Notebook 가이드 보기](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. 개별 Notebook을 선택하여 각각의 설명, 필요한 데이터 형식 및 데이터 원본을 봅니다.

    > [!div class="mx-imgBorder"]
    > ![Notebook 세부 정보 보기](./media/notebooks/sentinel-azure-notebooks-view.png)

1. 사용할 Notebook을 선택한 다음 **Notebook 시작** 을 선택하여 Azure Sentinel 작업 영역에 연결되는 새 Azure Notebooks 프로젝트로 Notebook을 복제하고 구성합니다. 프로세스가 완료되면 실행을 위해 Azure Notebooks 내에서 Notebook이 열립니다.

    > [!div class="mx-imgBorder"]
    > ![Notebook 선택](./media/notebooks/sentinel-azure-notebooks-select.png)

1. AzureML 작업 영역에서 Azure ML 작업 영역을 선택한 다음 **시작** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Notebook 시작](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. 컴퓨팅 인스턴스를 시작합니다. 컴퓨팅 인스턴스가 없는 경우 다음을 수행합니다.
    1. 더하기 기호(+)를 선택하여 **컴퓨팅 인스턴스 만들기** 마법사를 시작합니다.

        > [!div class="mx-imgBorder"]
        > ![컴퓨팅 인스턴스 시작 마법사](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. **가상 컴퓨터 선택** 페이지에서 필요한 정보를 입력한 후 **다음** 을 선택합니다.

        > [!div class="mx-imgBorder"]
        > ![컴퓨팅 인스턴스 VM 선택](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

    1. **설정 구성** 페이지에서 필요한 정보를 입력한 후 **만들기** 를 선택합니다.
    
        > [!div class="mx-imgBorder"]
        > ![컴퓨팅 인스턴스 설정 구성](./media/notebooks/sentinel-azure-notebooks-compute-settings.png)

1. Notebook 서버를 만든 후 각 셀 내에서 실행 아이콘을 선택하여 Notebook에서 코드를 실행합니다.

    > [!div class="mx-imgBorder"]
    > ![Notebook 실행](./media/notebooks/sentinel-azure-notebooks-run.png)

권장 사항:

- Azure Sentinel에서 데이터를 쿼리하는 방법에 대한 간략한 소개는 [Azure ML Notebooks 및 Azure Sentinel 시작](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) 가이드를 참조하세요.

- [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) GitHub 하위 폴더에서 추가 샘플 Notebook을 찾을 수 있습니다. 이러한 샘플 Notebook은 데이터와 함께 저장되어 있기 때문에 의도한 출력을 쉽게 볼 수 있습니다. 이러한 Notebook은 [nbviewer](https://nbviewer.jupyter.org/)에서 보는 것이 좋습니다.

- [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) GitHub 하위 폴더에는 기본 Python 버전 설정, DSVM 구성, Notebook에서 Azure Sentinel 책갈피 만들기 등을 설명하는 Notebook이 포함되어 있습니다.

제공된 Notebook은 자체 Notebook을 개발할 때 사용할 수 있는 코드 샘플과 실례 및 유용한 도구로 사용할 수 있습니다.

여러분의 피드백을 환경합니다. 제안, 기능 요청, Notebook 기여, 버그 보고, 기존 Notebook에 대한 개선 및 추가 사항 등 무엇이든 괜찮습니다. [Azure Sentinel 커뮤니티 GitHub](https://github.com/Azure/Azure-Sentinel)로 이동하여 문제를 작성하거나, 포크하거나, 기여 자료를 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 Jupyter Notebook 사용을 시작하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협 요소를 사전에 헌팅하기](hunting.md)
- [헌팅 시 책갈피를 사용하여 흥미로운 정보 저장하기](bookmarks.md)
- [Jupyter, msticpy 및 Azure Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
