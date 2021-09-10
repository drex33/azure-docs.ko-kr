---
title: Azure Sentinel에서 Notebook을 사용한 보안 헌팅
description: 이 문서에서는 Azure Sentinel 헌팅 기능과 Notebook을 사용하는 방법을 설명합니다.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/24/2021
ms.openlocfilehash: 7b1202648db3aed73ea040fb60610879a6816dd9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536181"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Jupyter Notebook을 사용하여 보안 위협 헌팅

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel의 기반은 데이터 저장소입니다. 데이터 저장소는 고성능 쿼리와 동적 스키마를 결합하고 대규모 데이터 볼륨으로 확장합니다. Azure Portal과 모든 Azure Sentinel 도구는 공통 API를 사용하여 데이터 저장소에 액세스합니다. 동일한 API를 [Jupyter](https://jupyter.org/) Notebook 및 Python과 같은 외부 도구에도 사용할 수 있습니다. 포털에서 다수의 일반적인 작업을 수행할 수 있지만 Jupyter는 데이터로 수행할 수 있는 작업의 범위를 확장합니다. 기계 학습, 시각화 및 데이터 분석을 위해 방대한 라이브러리 컬렉션과 전체 프로그래밍 기능을 결합합니다. 이러한 특성 덕분에 Jupyter는 보안 조사 및 헌팅에 맞는 강력한 도구가 될 수 있습니다.

예를 들어 Notebook을 사용하여 다음을 수행합니다.

- 몇 가지 Python 기계 학습 기능과 같이 Azure Sentinel에 기본 제공되지 않는 분석을 수행합니다.
- 사용자 지정 타임라인, 프로세스 트리와 같이 Azure Sentinel에 기본 제공되지 않는 데이터 시각화를 만듭니다.
- 온-프레미스 데이터 세트와 같은 Azure Sentinel 외부 데이터 원본을 통합합니다.

Jupyter 환경이 Azure Portal에 통합되어 있어서 Notebook을 쉽게 만들고 실행하여 데이터를 분석할 수 있습니다. *Kqlmagic* 라이브러리는 Azure Sentinel에서 쿼리를 가져와서 Notebook 내에서 직접 실행할 수 있는 수단을 제공합니다. 쿼리는 [Kusto 쿼리 언어](https://kusto.azurewebsites.net/docs/kusto/query/index.html)를 사용합니다. 일부 Microsoft 보안 분석가가 개발한 몇 가지 Notebook이 Azure Sentinel에 패키징되어 있습니다. 이러한 Notebook 중 일부는 특정 시나리오에 맞게 빌드되어 있어서 그대로 사용할 수 있습니다. 그 외 Notebook은 기술과 기능을 설명하는 샘플로 제공되며, 사용자가 Notebook에서 사용하기 적합하게 수정하거나 복사할 수 있습니다. Azure Sentinel Community GitHub에서 다른 Notebook을 가져올 수도 있습니다.

통합 Jupyter 환경에서는 [Azure Notebooks](https://notebooks.azure.com/)를 사용하여 Notebook을 저장, 공유 및 실행합니다. 컴퓨터나 Azure Databricks 같은 다른 JupyterHub 환경의 Jupyter와 Python 환경이 있는 경우 로컬에서도 Notebook을 실행할 수 있습니다.


## <a name="notebook-components"></a>Notebook 구성 요소

Notebook의 두 가지 구성 요소는 다음과 같습니다.
- 쿼리와 코드를 입력 및 실행하고, 실행 결과가 표시되는 **브라우저 기반 인터페이스**
- 코드 자체의 구문 분석과 실행을 담당하는 **‘커널’** 

Azure Sentinel Notebook의 커널은 Azure VM(가상 머신)에서 실행됩니다. Notebook에 복잡한 기계 학습 모델이 포함되어 있는 경우 더 강력한 가상 머신을 활용할 수 있는 라이선스 옵션이 몇 가지 있습니다.

Azure Sentinel Notebook은 *pandas*, *matplotlib*, *bokeh* 등 인기 있는 다양한 Python 라이브러리를 사용합니다. 그 외 다음과 같은 영역을 지원하는 다양한 Python 패키지를 선택할 수 있습니다.

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 수치 계산
- 기계 학습 및 딥 러닝

[msticpy](https://github.com/Microsoft/msticpy/)라는 패키지에 오픈 소스 Jupyter 보안 도구도 릴리스되었습니다. 이 패키지는 포함된 많은 Notebook에서 사용됩니다. Msticpy 도구는 구체적으로 헌팅 및 조사를 위한 Notebook을 만드는 데 도움이 되도록 설계되었으며, 당사는 새로운 기능과 개선을 위해 적극적으로 노력하고 있습니다. 자세한 내용은 [MSTIC Jupyter 및 Python 보안 도구 설명서](https://msticpy.readthedocs.io/)를 참조하세요.

[Azure Sentinel Community GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel)는 Microsoft가 구축하거나 커뮤니티가 제공하는 향후 Azure Sentinel Notebook을 위한 공간입니다.


## <a name="manage-access-to-azure-sentinel-notebooks"></a>Azure Sentinel Notebook에 대한 액세스 관리

Notebook을 사용하려면 먼저 사용자 역할에 따라 올바른 권한이 있어야 합니다.

Azure Sentinel Notebook은 Azure ML([Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md)) 플랫폼에서 실행되므로 Azure Sentinel 작업 영역과 [Azure ML 작업 영역](../machine-learning/concept-workspace.md) 둘 다에 대한 적절한 액세스 권한이 있어야 합니다.

|사용 권한  |설명  |
|---------|---------|
|**Azure Sentinel 권한**     |   다른 Azure Sentinel 리소스와 마찬가지로 Azure Sentinel Notebook 블레이드에서 Notebook에 액세스하려면 Azure Sentinel 읽기 권한자, Azure Sentinel 응답자 또는 Azure Sentinel 기여자 역할이 필요합니다. <br><br>자세한 내용은 [Azure Sentinel의 권한](roles.md)을 참조하세요.|
|**Azure Machine Learning 권한**     | Azure Machine Learning 작업 영역은 Azure 리소스입니다. 다른 Azure 리소스와 마찬가지로 새 Azure Machine Learning 작업 영역을 만들 때 기본 역할이 제공됩니다. 작업 영역에 사용자를 추가하고 이러한 기본 제공 역할 중 하나에 할당할 수 있습니다. 자세한 내용은 [Azure Machine Learning 기본 역할](../machine-learning/how-to-assign-roles.md)과 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요. <br><br>   **중요**: Azure에서 역할 액세스의 범위를 여러 수준으로 지정할 수 있습니다. 예를 들어 작업 영역에 대한 소유자 액세스 권한이 있는 사용자에게 작업 영역을 포함하는 리소스 그룹에 대한 소유자 액세스 권한이 없을 수 있습니다. 자세한 내용은 [Azure RBAC 작동 방식](../role-based-access-control/overview.md)을 참조하세요. <br><br>Azure ML 작업 영역의 소유자는 작업 영역에 대한 역할을 추가 및 제거하고 사용자에게 역할을 할당할 수 있습니다. 자세한 내용은 다음을 참조하세요.<br>    - [Azure Portal](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Azure CLI](../role-based-access-control/role-assignments-cli.md)<br>   - [REST API](../role-based-access-control/role-assignments-rest.md)<br>    - [Azure Resource Manager 템플릿](../role-based-access-control/role-assignments-template.md)<br> - [Azure Machine Learning CLI ](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>기본 제공 역할이 충분하지 않은 경우 사용자 지정 역할도 만들 수 있습니다. 사용자 지정 역할에는 해당 작업 영역에서 읽기, 쓰기, 삭제 및 컴퓨팅 리소스 권한이 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용하도록 설정할 수 있습니다. 자세한 내용은 [사용자 지정 역할 만들기](../machine-learning/how-to-assign-roles.md#create-custom-role)를 참조하세요. |
|     |         |


## <a name="create-an-azure-ml-workspace-from-azure-sentinel"></a>Azure Sentinel에서 Azure ML 작업 영역 만들기

이 절차에서는 Azure Sentinel에서 Azure Sentinel Notebook에 대한 Azure ML 작업 영역을 만드는 방법을 설명합니다.

**작업 영역을 만들려면 다음을 수행합니다.**

1. Azure Portal에서 **Azure Sentinel** > **위협 관리** > **Notebook** 으로 이동한 다음, **새 AML 작업 영역 만들기** 를 선택합니다.

1. 다음 세부 정보를 입력하고 **다음** 을 선택합니다.

    |필드|설명|
    |--|--|
    |**구독**|사용할 Azure 구독을 선택합니다.|
    |**리소스 그룹**|구독의 기존 리소스 그룹을 사용하거나 이름을 입력하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유합니다.|
    |**작업 영역 이름**|작업 영역을 식별하는 고유한 이름을 입력합니다. 이름은 리소스 그룹 전체에서 고유해야 합니다. 다른 사용자가 만든 작업 영역과 구별되고 기억하기 쉬운 이름을 사용하세요.|
    |**지역**|사용자 및 데이터 리소스와 가장 가까운 위치를 선택하여 작업 영역을 만듭니다.|
    |**스토리지 계정**| 스토리지 계정은 작업 영역의 기본 데이터 저장소로 사용됩니다. 새 Azure Storage 리소스를 만들거나 구독의 기존 리소스를 선택할 수 있습니다.|
    |**KeyVault**| 키 자격 증명 모음은 작업 영역에 필요한 비밀과 기타 중요한 정보를 저장하는 데 사용됩니다. 새 Azure Key Vault 리소스를 만들거나 구독의 기존 리소스를 선택할 수 있습니다.|
    |**애플리케이션 인사이트**| 작업 영역은 Azure Application Insights를 사용하여 배포된 모델에 대한 모니터링 정보를 저장합니다. 새 Azure Application Insights 리소스를 만들거나 구독의 기존 리소스를 선택할 수 있습니다.|
    |**컨테이너 레지스트리**| 컨테이너 레지스트리는 교육과 배포에서 사용되는 docker 이미지를 등록하는 데 사용됩니다. 비용을 최소화하기 위해 첫 번째 이미지를 빌드한 후에만 새 Azure Container Registry 리소스가 생성됩니다. 또는 지금 리소스를 만들도록 선택하거나, 구독의 기존 리소스를 선택하거나, 컨테이너 레지스트리를 사용하지 않으려는 경우 **없음** 을 선택할 수 있습니다.|
    | | |

1. **네트워킹** 탭에서 작업 영역을 연결하는 데 퍼블릭 엔드포인트를 사용할지, 아니면 프라이빗 엔드포인트를 사용할지 선택합니다. Azure Sentinel 작업 영역에 퍼블릭 엔드포인트가 있는 경우, 네트워크 통신의 잠재적인 문제를 방지하기 위해 Azure ML 작업 영역에도 퍼블릭 엔드포인트를 사용하는 것이 좋습니다. 완료되면 **검토 + 만들기** 를 선택합니다.

1. **검토 + 만들기** 탭에서 정보를 검토하여 올바른지 확인한 다음, **만들기** 를 선택하여 작업 영역 배포를 시작합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Azure Sentinel에서 Machine Learning 작업 영역 검토 + 만들기":::

    클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다. 이 시간 동안 작업 영역 **개요** 페이지에는 현재 배포 상태가 표시되고, 배포가 완료되면 업데이트됩니다.

1. 배포가 완료되면 Azure Sentinel **Notebook** 으로 돌아간 다음, 새 Azure ML 작업 영역에서 Notebook을 시작할 수 있습니다.

    여러 Notebook이 있는 경우 Notebook을 시작할 때 사용할 기본 AML 작업 영역을 선택해야 합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="Notebook의 기본 AML 작업 영역 선택":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Azure ML 작업 영역에서 Notebook 시작

AML 작업 영역을 만든 후 Azure Sentinel에서 Azure ML 작업 영역의 Notebook을 시작합니다.

**Notebook을 시작하려면 다음을 수행합니다.**

1. Azure Portal에서 **Azure Sentinel** > **위협 관리** > **Notebook** 으로 이동하면 Azure Sentinel이 제공하는 Notebook을 볼 수 있습니다.

    > [!TIP]
    > **Notebook** 페이지 맨 위에서 **가이드 및 피드백** 을 선택하여 오른쪽 창에 더 많은 리소스와 지침을 표시합니다.

1. Notebook을 선택하여 해당 설명, 필수 데이터 형식, 데이터 원본을 확인합니다.

    사용할 Notebook을 찾았으면 **Notebook 저장** 을 선택하여 사용자 고유의 작업 영역에 복제합니다.

    필요에 따라 이름을 편집합니다. 작업 영역에 Notebook이 이미 있는 경우 기존 Notebook을 덮어쓰거나 새로 만들 수 있습니다.

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="Notebook을 저장하여 사용자 고유의 작업 영역에 복제":::

1. Notebook을 저장한 후에는 **Notebook 저장** 단추가 **Notebook 시작** 으로 변경됩니다. **Notebook 시작** 을 선택하여 AML 작업 영역에서 엽니다.

    예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="AML 작업 영역에서 Notebook 시작":::

1. 페이지 맨 위에서 Notebook 서버에 사용할 **컴퓨팅** 인스턴스를 선택합니다.

    컴퓨팅 인스턴스가 없는 경우 [새로 만듭니다](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio). 컴퓨팅 인스턴스가 중지된 경우 인스턴스를 시작해야 합니다. 자세한 내용은 [Azure Machine Learning 스튜디오에서 Notebook 실행](../machine-learning/how-to-run-jupyter-notebooks.md)을 참조하세요.

    사용자가 만든 컴퓨팅 인스턴스만 보고 사용할 수 있습니다. 사용자 파일은 VM과 별도로 저장되고 작업 영역의 모든 컴퓨팅 인스턴스 간에 공유됩니다.

    > [!TIP]
    > Notebook을 테스트하기 위해 새 컴퓨팅 인스턴스를 만드는 경우 **범용** 범주를 사용하여 컴퓨팅 인스턴스를 만듭니다.

1. Notebook 서버를 만들고 시작한 후에는 Notebook 셀 실행을 시작할 수 있습니다. 각 셀에서 **실행** 아이콘을 선택하여 Notebook 코드를 실행합니다.

    자세한 내용은 [명령 모드 바로 가기](../machine-learning/how-to-run-jupyter-notebooks.md)를 참조하세요.

1. Notebook 커널을 다시 시작하고 Notebook 셀을 처음부터 다시 실행해야 하는 경우 **커널 작업** > **커널 다시 시작** 을 선택합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="Notebook 커널을 다시 시작합니다.":::

## <a name="troubleshooting"></a>문제 해결

Notebook 관련 문제가 발생하는 경우 [Azure Machine Learning Notebook 문제 해결](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [**Azure Log Analytics의 자격 증명 검사**](https://www.youtube.com/watch?v=OWjXee8o04M) 및 **단계별 조사 - 경고 처리** 와 같은 몇 가지 Notebook 템플릿을 검색하여 위협 헌팅과 조사에 Notebook을 사용하는 방법을 자세히 알아봅니다. 

    Azure Sentinel > **Notebook** > **템플릿** 탭에서 Notebook 템플릿을 찾습니다.

- [Azure Sentinel GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel-Notebooks)에서 추가 템플릿을 찾습니다.

    - [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) 디렉터리에는 의도한 출력을 표시하는 데 사용할 수 있는 데이터와 함께 저장된 샘플 Notebook이 포함되어 있습니다.

    - [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) 디렉터리에는 기본 Python 버전 설정, Notebook에서 Azure Sentinel 책갈피 만들기 등의 개념을 설명하는 Notebook이 포함되어 있습니다.

    자세한 내용은 [Azure ML Notebooks 및 Azure Sentinel 시작](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) 가이드를 참조하세요.

> [!NOTE]
> [Azure Sentinel GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel-Notebooks)에서 공유되는 Notebook은 고유한 Notebook을 개발할 때 사용할 수 있는 유용한 도구, 그림, 코드 샘플로 제공됩니다.
>
> 여러분의 피드백을 환영합니다. 제안, 기능 요청, Notebook 기여, 버그 보고, 기존 Notebook에 대한 개선 및 추가 사항 등 무엇이든 괜찮습니다. [Azure Sentinel 커뮤니티 GitHub](https://github.com/Azure/Azure-Sentinel)로 이동하여 문제를 작성하거나, 포크하거나, 기여 자료를 업로드할 수 있습니다.
>

자세한 내용은 다음을 참조하세요.

- [웨비나: Azure Sentinel Notebook 기본 사항](https://www.youtube.com/watch?v=rewdNeX6H94)
- [자습서: Azure Sentinel Notebooks - 시작](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks)
- [위협 요소를 사전에 헌팅하기](hunting.md)
- [헌팅 시 책갈피를 사용하여 흥미로운 정보 저장하기](bookmarks.md)
- [Jupyter, msticpy 및 Azure Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
- [자습서: Azure ML 스튜디오를 벗어나지 않고 Jupyter Notebook 편집 및 실행](https://www.youtube.com/watch?v=AAj-Fz0uCNk)
