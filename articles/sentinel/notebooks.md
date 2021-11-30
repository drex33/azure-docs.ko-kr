---
title: Microsoft 센티널에서 보안을 위한 전자 필기장 사용
description: 이 문서에서는 Microsoft 센티널 구하기 기능에서 노트북을 사용 하는 방법을 설명 합니다.
author: batamig
ms.author: bagol
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.date: 11/14/2021
ms.openlocfilehash: a646e4d6c528e6a0a0e8098be2a4da931d2baaa9
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133268175"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter Notebook을 사용하여 보안 위협 헌팅

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft 센티널의 기초가 데이터 저장소입니다. 고성능 쿼리, 동적 스키마 및 크기 조정 기능을 대규모 데이터 볼륨으로 결합 합니다. Azure Portal 및 모든 Microsoft 센티널 도구는 공용 API를 사용 하 여이 데이터 저장소에 액세스 합니다.

동일한 API를 [Jupyter](https://jupyter.org/) Notebook 및 Python과 같은 외부 도구에도 사용할 수 있습니다. 포털에서 다수의 일반적인 작업을 수행할 수 있지만 Jupyter는 데이터로 수행할 수 있는 작업의 범위를 확장합니다. 기계 학습, 시각화 및 데이터 분석을 위해 방대한 라이브러리 컬렉션과 전체 프로그래밍 기능을 결합합니다. 이러한 특성 덕분에 Jupyter는 보안 조사 및 헌팅에 맞는 강력한 도구가 될 수 있습니다.

예를 들어 Notebook을 사용하여 다음을 수행합니다.

- Microsoft 센티널에서 제공 하지 않는 분석 (예: 일부 Python 기계 학습 기능)을 **수행** 합니다.
- 사용자 지정 타임 라인 및 프로세스 트리와 같이 Microsoft 센티널에서 기본 제공 되지 않는 **데이터 시각화를 만듭니다** .
- Microsoft 센티널 외부의 **데이터 원본** (예: 온-프레미스 데이터 집합)을 통합 합니다.

Jupyter 환경이 Azure Portal에 통합되어 있어서 Notebook을 쉽게 만들고 실행하여 데이터를 분석할 수 있습니다. *Kqlmagic* 라이브러리는 Microsoft 센티널에서 [KQL](https://kusto.azurewebsites.net/docs/kusto/query/index.html) 쿼리를 가져와서 노트북 내에서 직접 실행할 수 있는 붙이기를 제공 합니다.

일부 Microsoft 보안 분석가에서 개발한 몇 가지 노트북은 Microsoft 센티널로 패키지 됩니다.

- 이러한 Notebook 중 일부는 특정 시나리오에 맞게 빌드되어 있어서 그대로 사용할 수 있습니다.
- 그 외 Notebook은 기술과 기능을 설명하는 샘플로 제공되며, 사용자가 Notebook에서 사용하기 적합하게 수정하거나 복사할 수 있습니다.

[Microsoft 센티널 GitHub 리포지토리에서](https://github.com/Azure/Azure-Sentinel-Notebooks/)아직 다른 전자 필기장을 가져올 수도 있습니다.

## <a name="notebook-components"></a>Notebook 구성 요소

Notebook의 두 가지 구성 요소는 다음과 같습니다.

- 쿼리와 코드를 입력 및 실행하고, 실행 결과가 표시되는 **브라우저 기반 인터페이스**
- 코드 자체의 구문 분석과 실행을 담당하는 **‘커널’** 

Microsoft Sentinel Notebook의 커널은 Azure VM(가상 머신)에서 실행됩니다. Notebook에 복잡한 기계 학습 모델이 포함되어 있는 경우 더 강력한 가상 머신을 사용할 수 있는 몇 가지 라이선스 옵션이 있습니다.

Microsoft 센티널 노트북은 *pandas*, *matplotlib*, *빛 망 울* 등 널리 사용 되는 다양 한 Python 라이브러리를 사용 합니다. 그 외 다음과 같은 영역을 지원하는 다양한 Python 패키지를 선택할 수 있습니다.

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 수치 계산
- 기계 학습 및 딥 러닝

복잡 하 고 반복적인 코드를 노트북 셀에 입력 하거나 붙여 넣을 필요가 없도록 대부분의 Python 노트북은 *패키지* 라는 타사 라이브러리를 사용 합니다. 노트북에서 패키지를 사용 하려면 패키지를 설치 하 고 가져와야 합니다. Azure ML 컴퓨팅에는 가장 일반적인 패키지가 미리 설치 되어 있습니다. 패키지 또는 패키지의 관련 부분 (예: 모듈, 파일, 함수 또는 클래스)을 가져와야 합니다.

Microsoft 센티널 노트북은 데이터 검색, 분석, 보강 및 시각화를 위한 사이버 보안 도구의 컬렉션인 [MSTICPy](https://github.com/Microsoft/msticpy/)이라는 Python 패키지를 사용 합니다. 

MSTICPy 도구는 새로운 기능 및 개선 사항에 대 한 노트를 만드는 데 도움이 되도록 특별히 설계 되었습니다. 자세한 내용은 다음을 참조하세요.

- [MSTIC Jupyter 및 Python 보안 도구 설명서](https://msticpy.readthedocs.io/)
- [자습서: Microsoft 센티널에서 Jupyter 노트북 및 MSTICPy 시작 하기](notebook-get-started.md)
- [Microsoft 센티널의 Jupyter 노트북 및 MSTICPy에 대 한 고급 구성](notebooks-msticpy-advanced.md)

[microsoft 센티널 GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel-Notebooks/) 는 microsoft에서 빌드하고 커뮤니티에서 제공 하는 향후 microsoft 센티널 노트북의 위치입니다.

## <a name="manage-access-to-microsoft-sentinel-notebooks"></a>Microsoft 센티널 노트북에 대 한 액세스 관리

Microsoft 센티널에서 Jupyter 노트북을 사용 하려면 먼저 사용자 역할에 따라 올바른 권한이 있어야 합니다.

microsoft 센티널에서 jupyterlab 또는 jupyter 클래식으로 microsoft 센티널 노트북을 실행할 수 있지만 노트북은 [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md) (Azure ML) 플랫폼에서 실행 됩니다. microsoft 센티널에서 노트북을 실행 하려면 microsoft 센티널 작업 영역 및 [Azure ML 작업 영역](../machine-learning/concept-workspace.md)에 대 한 적절 한 액세스 권한이 있어야 합니다.

|사용 권한  |설명  |
|---------|---------|
|**Microsoft 센티널 사용 권한**     |   다른 Microsoft 센티널 리소스와 같이 microsoft 센티널 노트북 블레이드에서 노트북에 액세스 하려면 microsoft 센티널 Reader, Microsoft 센티널 응답자 또는 Microsoft 센티널 참여자 역할이 필요 합니다. <br><br>자세한 내용은 [Microsoft 센티널의 권한](roles.md)을 참조 하세요.|
|**Azure Machine Learning 권한**     | Azure Machine Learning 작업 영역은 Azure 리소스입니다. 다른 Azure 리소스와 마찬가지로 새 Azure Machine Learning 작업 영역을 만들 때 기본 역할이 제공됩니다. 작업 영역에 사용자를 추가하고 이러한 기본 제공 역할 중 하나에 할당할 수 있습니다. 자세한 내용은 [Azure Machine Learning 기본 역할](../machine-learning/how-to-assign-roles.md)과 [Azure 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요. <br><br>   **중요**: Azure에서 역할 액세스의 범위를 여러 수준으로 지정할 수 있습니다. 예를 들어 작업 영역에 대한 소유자 액세스 권한이 있는 사용자에게 작업 영역을 포함하는 리소스 그룹에 대한 소유자 액세스 권한이 없을 수 있습니다. 자세한 내용은 [Azure RBAC 작동 방식](../role-based-access-control/overview.md)을 참조하세요. <br><br>Azure ML 작업 영역의 소유자는 작업 영역에 대한 역할을 추가 및 제거하고 사용자에게 역할을 할당할 수 있습니다. 자세한 내용은 다음을 참조하세요.<br>    - [Azure Portal](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Azure CLI](../role-based-access-control/role-assignments-cli.md)<br>   - [REST API](../role-based-access-control/role-assignments-rest.md)<br>    - [Azure Resource Manager 템플릿](../role-based-access-control/role-assignments-template.md)<br> - [Azure Machine Learning CLI ](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>기본 제공 역할이 충분하지 않은 경우 사용자 지정 역할도 만들 수 있습니다. 사용자 지정 역할에는 해당 작업 영역에서 읽기, 쓰기, 삭제 및 컴퓨팅 리소스 권한이 있을 수 있습니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준 또는 특정 구독 수준에서 역할을 사용하도록 설정할 수 있습니다. 자세한 내용은 [사용자 지정 역할 만들기](../machine-learning/how-to-assign-roles.md#create-custom-role)를 참조하세요. |
|     |         |

## <a name="create-an-azure-ml-workspace-from-microsoft-sentinel"></a>Microsoft 센티널에서 Azure ML 작업 영역 만들기

이 절차에서는 microsoft 센티널 노트북에 대해 microsoft 센티널에서 Azure ML 작업 영역을 만드는 방법에 대해 설명 합니다.

**작업 영역을 만들려면 다음을 수행합니다.**

공용 또는 개인 끝점을 사용 하는지 여부에 따라 다음 탭 중 하나를 선택 합니다.

- 네트워크 통신의 잠재적인 문제를 방지 하기 위해 Microsoft 센티널 작업 영역에 있는 경우 *공용 끝점* 을 사용 하는 것이 좋습니다.
- 가상 네트워크에서 Azure ML 작업 영역을 사용 하려면 *개인 끝점* 을 사용 합니다.

# <a name="public-endpoint"></a>[공용 엔드포인트](#tab/public-endpoint)

1. Azure Portal에서 **Microsoft 센티널**  >  **Threat management**  >  **노트북** 으로 이동한 다음 **새 AML 작업 영역 만들기** 를 선택 합니다.

1. 다음 세부 정보를 입력하고 **다음** 을 선택합니다.

    |필드|Description|
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

1. **네트워킹** 탭에서 **공용 끝점 (모든 네트워크)** 을 선택 합니다.

    **고급** 또는 **태그** 탭에서 관련 설정을 정의한 다음 **검토 + 만들기** 를 선택 합니다.

1. **검토 + 만들기** 탭에서 정보를 검토하여 올바른지 확인한 다음, **만들기** 를 선택하여 작업 영역 배포를 시작합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Microsoft 센티널에서 Machine Learning 작업 영역을 검토 하 고 만듭니다.":::

    클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다. 이 시간 동안 작업 영역 **개요** 페이지에는 현재 배포 상태가 표시되고, 배포가 완료되면 업데이트됩니다.


# <a name="private-endpoint"></a>[프라이빗 엔드포인트](#tab/private-endpoint)

이 절차의 단계는 관련 된 Azure Machine Learning 설명서의 특정 문서를 참조 합니다. 자세한 내용은 [보안 Azure ML 작업 영역을 만드는 방법](/azure/machine-learning/tutorial-create-secure-workspace)을 참조 하세요.

1.  VNet 내에서 VM 점프 상자를 만듭니다. VNet은 퍼블릭 인터넷의 액세스를 제한하므로 VNet 뒤에 있는 리소스에 연결하는 방법으로 점프 상자가 사용됩니다.

1.  점프 상자에 액세스 한 다음 Microsoft 센티널 작업 영역으로 이동 합니다. [Azure 방호](/azure/bastion/bastion-overview) 를 사용 하 여 VM에 액세스 하는 것이 좋습니다.

1. Microsoft 센티널에서 **위협 관리**  >  **노트북** 을 선택 하 고 **새 AML 작업 영역 만들기** 를 선택 합니다.

1. 다음 세부 정보를 입력하고 **다음** 을 선택합니다.

    |필드|Description|
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

1. **네트워킹** 탭에서 **개인 끝점** 을 선택 합니다. VM 점프 상자에 있는 것과 동일한 VNet을 사용 해야 합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/create-private-endpoint.png" alt-text="Microsoft 센티널에서 개인 끝점 만들기 페이지의 스크린샷" lightbox="media/notebooks/create-private-endpoint.png":::

1. **고급** 또는 **태그** 탭에서 관련 설정을 정의한 다음 **검토 + 만들기** 를 선택 합니다.

1. **검토 + 만들기** 탭에서 정보를 검토하여 올바른지 확인한 다음, **만들기** 를 선택하여 작업 영역 배포를 시작합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Microsoft 센티널에서 Machine Learning 작업 영역을 검토 하 고 만듭니다.":::

    클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다. 이 시간 동안 작업 영역 **개요** 페이지에는 현재 배포 상태가 표시되고, 배포가 완료되면 업데이트됩니다.

1.  Azure Machine Learning studio의 **계산** 페이지에서 새 계산을 만듭니다. **고급 설정** 탭에서 VM 점프 상자에 사용 했던 것과 동일한 VNet을 선택 해야 합니다. 자세한 내용은 [Azure Machine Learning 계산 인스턴스 만들기 및 관리](/azure/machine-learning/how-to-create-manage-compute-instance?tabs=python)를 참조 하세요.

1.  방화벽 뒤에서 Azure ML에 액세스 하도록 네트워크 트래픽을 구성 합니다. 자세한 내용은 [인바운드 및 아웃 바운드 네트워크 트래픽 구성](/azure/machine-learning/how-to-access-azureml-behind-firewall?tabs=ipaddress%2Cpublic)을 참조 하세요.

다음 단계 중 하나를 진행 합니다.

- **개인 링크만 있는 경우**: 이제 다음 방법 중 하나를 통해 노트북에 액세스할 수 있습니다.

    - Microsoft 센티널에서 Azure Machine Learning로 노트북 복제 및 시작
    - 수동으로 Azure Machine Learning 업로드 노트북
    - Azure Machine learning 터미널에서 [Microsoft 센티널 노트북 GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel-Notebooks) 를 복제 합니다.

- **서로 다른 VNET을 사용 하는 다른 개인 링크가 있는 경우** 다음을 수행 합니다.

    1. Azure Portal에서 Azure Machine Learning 작업 영역의 리소스 그룹으로 이동한 후 **privatelink.api.azureml.ms** 및 **privatelink.notebooks.azure.ms** 라는 **사설 DNS 영역** 리소스를 검색 합니다.  예를 들면 다음과 같습니다.

        :::image type="content" source="media/notebooks/select-private-dns-zone.png" alt-text="선택한 개인 DNS 영역 리소스의 스크린샷" lightbox="media/notebooks/select-private-dns-zone.png":::

    1. **Privatelink.api.azureml.ms** 및 **privatelink.notebooks.azure.ms** 를 포함 하 여 각 리소스에 대해 가상 네트워크 링크를 추가 합니다.

        **가상 네트워크 링크** 추가 > 리소스를 선택  >  합니다. 자세한 내용은 [가상 네트워크 연결](/azure/dns/private-dns-getstarted-portal)을 참조하세요.

자세한 내용은 다음을 참조하세요.

- [보안 작업 영역을 사용 하는 경우 네트워크 트래픽 흐름](/azure/machine-learning/concept-secure-network-traffic-flow)
- [VNet(가상 네트워크)을 사용하여 Azure Machine Learning 작업 영역 리소스 보호](/azure/machine-learning/how-to-network-security-overview)

---

배포가 완료 되 면 Microsoft 센티널 **노트북** 으로 돌아가서 새 Azure ML 작업 영역에서 노트북을 실행할 수 있습니다.

여러 Notebook이 있는 경우 Notebook을 시작할 때 사용할 기본 AML 작업 영역을 선택해야 합니다. 예를 들면 다음과 같습니다.

:::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="Notebook의 기본 AML 작업 영역 선택":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Azure ML 작업 영역에서 Notebook 시작

AML 작업 영역을 만든 후 Microsoft 센티널의 Azure ML 작업 영역에서 노트북을 시작 하기 시작 합니다.

> [!NOTE]
> GitHub 기본 제공 정적 노트북 렌더러와 같은 정적 문서로 노트북을 볼 수 있습니다. 그러나 노트북에서 코드를 실행 하려면 노트북을 Jupyter 커널 이라는 백 엔드 프로세스에 연결 해야 합니다. 커널은 코드를 실행 하 고 코드가 만드는 모든 변수와 개체를 저장 합니다. 브라우저는이 데이터에 대 한 뷰어입니다.
>
> azure ML에서 커널은 azure ML 컴퓨팅 라는 가상 머신에서 실행 됩니다. 계산 인스턴스는 한 번에 많은 전자 필기장의 실행을 지원할 수 있습니다.
>

**Microsoft 센티널에서 노트북을 시작 하려면**:

1. Azure Portal에서 microsoft   >  센티널이 제공 하는 노트북을 볼 수 있는 microsoft 센티널 **Threat management**  >  **노트북** 으로 이동 합니다.

    > [!TIP]
    > **Notebook** 페이지 맨 위에서 **가이드 및 피드백** 을 선택하여 오른쪽 창에 더 많은 리소스와 지침을 표시합니다.

1. Notebook을 선택하여 해당 설명, 필수 데이터 형식, 데이터 원본을 확인합니다.

    사용할 Notebook을 찾았으면 **Notebook 저장** 을 선택하여 사용자 고유의 작업 영역에 복제합니다.

    필요에 따라 이름을 편집합니다. 노트북이 이미 작업 영역에 있는 경우 기존 노트북을 덮어쓰거나 새 노트북을 만들 수 있습니다.

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="Notebook을 저장하여 사용자 고유의 작업 영역에 복제":::

1. Notebook을 저장한 후에는 **Notebook 저장** 단추가 **Notebook 시작** 으로 변경됩니다. **Notebook 시작** 을 선택하여 AML 작업 영역에서 엽니다.

    예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="AML 작업 영역에서 Notebook 시작":::

1. 페이지 맨 위에서 Notebook 서버에 사용할 **컴퓨팅** 인스턴스를 선택합니다.

    컴퓨팅 인스턴스가 없는 경우 [새로 만듭니다](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio). 컴퓨팅 인스턴스가 중지된 경우 인스턴스를 시작해야 합니다. 자세한 내용은 [Azure Machine Learning 스튜디오에서 Notebook 실행](../machine-learning/how-to-run-jupyter-notebooks.md)을 참조하세요.

    사용자가 만든 컴퓨팅 인스턴스만 보고 사용할 수 있습니다. 사용자 파일은 VM과 별도로 저장되고 작업 영역의 모든 컴퓨팅 인스턴스 간에 공유됩니다.

    > [!TIP]
    > Notebook을 테스트하기 위해 새 컴퓨팅 인스턴스를 만드는 경우 **범용** 범주를 사용하여 컴퓨팅 인스턴스를 만듭니다.
    >
    > 커널은 Azure ML 창의 오른쪽 위에도 표시됩니다. 필요한 커널을 선택하지 않은 경우 드롭다운 목록에서 다른 버전을 선택합니다.
    >

1. Notebook 서버를 만들고 시작한 후에는 Notebook 셀 실행을 시작할 수 있습니다. 각 셀에서 **실행** 아이콘을 선택하여 Notebook 코드를 실행합니다.

    자세한 내용은 [명령 모드 바로 가기](../machine-learning/how-to-run-jupyter-notebooks.md)를 참조하세요.

1. Notebook이 중단되거나 다시 시작하려는 경우 커널을 다시 시작하고 처음부터 Notebook 셀을 다시 실행하면 됩니다. **커널 작업**  >  **커널 다시 시작을** 선택합니다. 예를 들면 다음과 같습니다.

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="Notebook 커널을 다시 시작합니다.":::

    > [!IMPORTANT]
    > 커널을 다시 시작하면 모든 변수 및 기타 상태가 초기화됩니다. 다시 시작한 후 초기화 및 인증 셀을 다시 실행해야 합니다.
    >

## <a name="run-code-in-your-notebook"></a>Notebook에서 코드 실행

Notebook에서:

- **Markdown** 셀에는 HTML 및 정적 이미지를 포함한 텍스트가 있습니다.
- **코드** 셀에는 코드가 포함됩니다. 코드 셀을 선택한 후 셀 왼쪽의 **재생** 아이콘을 선택하거나 **Shift+Enter를** 눌러 셀에서 코드를 실행합니다.

> [!IMPORTANT]
> 항상 Notebook 코드 셀을 순서대로 실행합니다. 셀을 건너뛰면 오류가 발생할 수 있습니다.
>

예를 들어 Notebook에서 다음 코드 셀을 실행합니다.

```python
# This is your first code cell. This cell contains basic Python code.

# You can run a code cell by selecting it and then selecting
# the Play button to the left of the cell, or by pressing SHIFT+ENTER.
# Code output displays below the code.

print("Congratulations, you just ran this code cell")

y = 2 + 2

print("2 + 2 =", y)

```

위에 표시된 샘플 코드는 다음 출력을 생성합니다.

```python
Congratulations, you just ran this code cell

2 + 2 = 4
```

Notebook 코드 셀 내에 설정된 변수는 셀 간에 유지되므로 셀을 함께 묶을 수 있습니다. 예를 들어 다음 코드 셀은 이전 셀의 값을 사용합니다. `y`

```python
# Note that output from the last line of a cell is automatically
# sent to the output cell, without needing the print() function.

y + 2
```

출력은 다음과 같습니다.

```output
6
```

## <a name="download-all-microsoft-sentinel-notebooks"></a>모든 Microsoft Sentinel Notebook 다운로드

이 섹션에서는 Git을 사용하여 [Microsoft Sentinel Notebook 내의 Microsoft Sentinel GitHub 리포지토리에서](https://github.com/Azure/Azure-Sentinel-Notebooks/)사용할 수 있는 모든 Notebook을 Azure ML 작업 영역으로 직접 다운로드하는 방법을 설명합니다.

Microsoft Sentinel Notebook을 Azure ML 작업 영역에 저장하면 쉽게 업데이트할 수 있습니다.

1. Microsoft Sentinel Notebook에서 빈 셀에 다음 코드를 입력한 다음 셀을 실행합니다.

    ```python
    !git clone https://github.com/Azure/Azure-Sentinel-Notebooks.git azure-sentinel-nb
    ```

    GitHub 리포지토리 콘텐츠의 복사본은 Azure ML 작업 영역의 사용자 폴더에 있는 **azure-Sentinel-nb** 디렉터리에 만들어집니다.

1. 이 폴더에서 작업 디렉터리로 원하는 Notebook을 복사합니다.

1. GitHub 최근 변경 내용으로 Notebook을 업데이트하려면 다음을 실행합니다.

    ```python
    !cd azure-sentinel-nb && git pull
    ```

## <a name="troubleshooting"></a>문제 해결

일반적으로 Notebook은 커널을 원활하게 만들거나 연결하므로 수동으로 변경할 필요가 없습니다. 오류가 발생하거나 Notebook이 실행되고 있지 않은 경우 커널의 버전과 상태를 확인해야 할 수 있습니다.

Notebook 관련 문제가 발생하는 경우 [Azure Machine Learning Notebook 문제 해결](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting)을 참조하세요.

### <a name="force-caching-for-user-accounts-and-credentials-between-notebook-runs"></a>Notebook 실행 간에 사용자 계정 및 자격 증명에 대한 캐싱 강제

기본적으로 사용자 계정 및 자격 증명은 동일한 세션의 경우에도 Notebook 실행 간에 캐시되지 않습니다.

**세션 기간 동안 캐싱을 강제로 수행하려면:**

1. Azure CLI 사용하여 인증합니다. 빈 Notebook 셀에서 다음 코드를 입력하고 실행합니다.

    ```python
    !az login
    ```

    다음과 같은 출력이 표시됩니다.

    ```python
    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the 9-digit device code to authenticate.
    ```

1. 출력에서 9자 토큰을 선택하고 복사한 `devicelogin` 다음, URL을 선택하여 표시된 페이지로 이동합니다. 

1. 대화 상자에 토큰을 붙여넣고 메시지가 표시되면 계속 로그인합니다.

    로그인이 성공적으로 완료되면 다음과 같은 출력이 표시됩니다.

    ```python
    Subscription <subscription ID> 'Sample subscription' can be accessed from tenants <tenant ID>(default) and <tenant ID>. To select a specific tenant when accessing this subscription, use 'az login --tenant TENANT_ID'.

> [!NOTE]
> The following tenants don't contain accessible subscriptions. Use 'az login --allow-no-subscriptions' to have tenant level access.
>
> ```
> <tenant ID> 'foo'
><tenant ID> 'bar'
>[
> {
>    "cloudName": "AzureApp",
>    "homeTenantId": "<tenant ID>",
>    "id": "<ID>",
>    "isDefault": true,
>    "managedByTenants": [
>    ....
>```
>
### Error: *Runtime dependency of PyGObject is missing*

If the *Runtime dependency of PyGObject is missing* error appears when you load a query provider, try troubleshooting using the following steps:

1. Proceed to the cell with the following code and run it:

    ```python
    qry_prov = QueryProvider("AzureSentinel")
    ```

    누락된 Python 종속성( )을 나타내는 다음 메시지와 유사한 경고가 표시됩니다. `pygobject`

    ```output
    Runtime dependency of PyGObject is missing.

    Depends on your Linux distribution, you can install it by running code similar to the following:
    sudo apt install python3-gi python3-gi-cairo gir1.2-secret-1

    If necessary, see PyGObject's documentation: https://pygobject.readthedocs.io/en/latest/getting_started.html

    Traceback (most recent call last):
      File "/anaconda/envs/azureml_py36/lib/python3.6/site-packages/msal_extensions/libsecret.py", line 21, in <module>
    import gi  # https://github.com/AzureAD/microsoft-authentication-extensions-for-python/wiki/Encryption-on-Linux
    ModuleNotFoundError: No module named 'gi'
    ```

1. Microsoft Sentinel Notebooks [GitHub](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/HowTos/aml-compute-setup.sh) 리포지토리에 있는 aml-compute-setup.sh 스크립트를 사용하여 `pygobject` 컴퓨팅 인스턴스의 모든 Notebook 및 Anaconda 환경에 를 자동으로 설치합니다.

> [!TIP]
> Notebook에서 다음 코드를 실행하여 이 경고를 해결할 수도 있습니다.
>
> ```python
> !conda install --yes -c conda-forge pygobject
> ```
>


## <a name="next-steps"></a>다음 단계

Azure Synapse 빅 데이터 분석과 Notebook 환경을 통합합니다. 자세한 내용은 [notebooks와 Azure Synapse 통합(공개 미리 보기)](notebooks-with-synapse.md)을 참조하세요.

[Microsoft Sentinel GitHub 리포지토리에서](https://github.com/Azure/Azure-Sentinel-Notebooks) 공유되는 다른 Notebook은 고유한 Notebook을 개발할 때 사용할 수 있는 유용한 도구, 일러스트레이션 및 코드 샘플로 사용할 수 있습니다.

여러분의 피드백을 환영합니다. 제안, 기능 요청, Notebook 기여, 버그 보고, 기존 Notebook에 대한 개선 및 추가 사항 등 무엇이든 괜찮습니다. [Microsoft Sentinel GitHub 리포지토리로](https://github.com/Azure/Azure-Sentinel) 이동하여 문제를 만들거나 포크하고 기여를 업로드합니다.

- [**Azure Log Analytics의 자격 증명 검사**](https://www.youtube.com/watch?v=OWjXee8o04M) 및 단계별 조사 - 경고 처리와 같은 일부 Notebook 템플릿을 탐색하여 위협 헌팅 및 조사에 Notebook을 사용하는 방법에 대해 자세히 **알아봅니다.** 

    Microsoft Sentinel > Notebook 템플릿 탭에서 더 많은 **Notebook 템플릿을** 찾습니다.  >  

- [Microsoft Sentinel GitHub 리포지토리에서](https://github.com/Azure/Azure-Sentinel-Notebooks) **더 많은 Notebook을 찾습니다.**

  - [`Sample-Notebooks`](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks)디렉터리에는 의도한 출력을 표시하는 데 사용할 수 있는 데이터와 함께 저장되는 샘플 Notebook이 포함되어 있습니다.

  - [`HowTos`](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos)디렉터리에는 기본 Python 버전 설정, Notebook에서 Microsoft Sentinel 책갈피 만들기 등의 개념을 설명하는 Notebook이 포함되어 있습니다.

자세한 내용은 다음을 참조하세요.

- [자습서: Microsoft Sentinel에서 Jupyter Notebook 및 MSTICPy 시작](notebook-get-started.md)
- [자습서: Microsoft Sentinel Notebooks -](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks) 시작(비디오)
- [자습서: Azure ML Studio를 벗어나지 않고 Jupyter Notebook 편집 및](https://www.youtube.com/watch?v=AAj-Fz0uCNk) 실행(비디오)
- [웹 세미나: Microsoft Sentinel Notebook 기본 사항](https://www.youtube.com/watch?v=rewdNeX6H94)
- [위협 요소를 사전에 헌팅하기](hunting.md)
- [헌팅 시 책갈피를 사용하여 흥미로운 정보 저장하기](bookmarks.md)
- [Jupyter, msticpy 및 Microsoft Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
