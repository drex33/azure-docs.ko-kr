---
title: Azure Sentinel의 헌팅 기능 | Microsoft Docs
description: Azure Sentinel의 기본 제공 헌팅 쿼리를 사용하여 데이터에서 문제를 찾을 수 있는 적절한 질문을 안내합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2021
ms.author: yelevin
ms.openlocfilehash: 8d93c98da0144d7081803abad174aa09a0087ccb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014052"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Azure Sentinel을 사용하여 위협 헌팅

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> 리소스 간 쿼리 환경 및 **헌팅 대시보드** 에 대한 업그레이드(아래 표시된 항목 참조)는 현재 **미리 보기** 로 지원됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

보안 분석가 및 조사자는 보안 위협을 사전에 찾기를 원하지만, 다양한 시스템과 보안 어플라이언스는 이해할 수 있는 이벤트로 구문 분석하고 필터링하기 어려울 수 있는 수많은 데이터를 생성합니다. Azure Sentinel은 조직의 데이터 원본에서 보안 위협을 헌팅하는 강력한 헌팅 검색 및 쿼리 도구입니다. 보안 분석가가 보안 앱 또는 예약된 분석 규칙에 의해 검색되지 않은 새 변칙을 사전에 확인하는 데 도움이 되도록 Azure Sentinel의 기본 제공 헌팅 쿼리는 네트워크에 이미 있는 데이터의 문제를 찾기 위한 적절한 질문을 안내합니다. 

예를 들어 하나의 기본 제공 쿼리는 인프라에서 실행되는 가장 일반적이지 않은 프로세스에 대한 데이터를 제공합니다. 실행될 때마다 경고를 받고 싶지는 않을 것입니다. 오류가 전혀 없을 수 있습니다. 그러나 가끔 쿼리를 살펴보고 비정상적인 것이 있는지 확인하고 싶을 수도 있습니다.

## <a name="use-built-in-queries"></a>기본 제공 쿼리 사용

[헌팅 대시보드](#use-the-hunting-dashboard-public-preview)는 사용자가 테이블과 쿼리 언어의 사용을 시작하고 익숙해질 수 있도록 설계된 미리 만들어진 쿼리 예제를 제공합니다. 쿼리는 프로세스 생성, DNS 이벤트 또는 기타 이벤트 유형 등과 같은 로그 테이블에 저장된 데이터에 대해 실행됩니다.

기본 제공 헌팅 쿼리는 지속적으로 Microsoft 보안 연구원에 의해 개발되고, 새 쿼리를 추가하고, 기존 쿼리를 미세 조정하여 새 검색을 찾고 새 공격의 개시에 대한 헌팅을 시작할 위치를 파악할 수 있는 진입점을 제공합니다.

다음 작업을 수행하기 위해 손상 전, 도중 및 후에 쿼리를 사용합니다.

- **인시던트가 발생하기 전**: 검색을 기다리는 것만으로는 충분하지 않습니다. 일주일에 한 번 이상 작업 영역으로 수집하는 데이터와 관련된 위협 수준 쿼리를 실행하여 사전 조치를 취합니다.

    사전 대응형 헌팅의 결과는 손상이 진행 중인지 확인하거나, 사용자 환경에서 위험에 노출되고 주의가 필요한 좀 더 약한 영역을 나타낼 수 있는 이벤트에 대한 조기 인사이트를 제공합니다.

- **손상 도중**: [라이브 스트림](livestream.md)을 사용하여 특정 쿼리를 지속적으로 실행하고 나타나는 결과를 제공합니다. 위협 행위자의 다음 동작을 파악하고, 조사가 끝난 후에 손상이 실제로 끝났는지 확인하는 데 도움을 주기 위해 특정 손상이 아직 발생하고 있는지 여부를 확인해야 하는 경우처럼 사용자 이벤트를 적극적으로 모니터링해야 할 때 라이브 스트림을 사용합니다.

- **손상 후**: 손상이나 인시던트가 발생한 후에는 나중에 비슷한 인시던트를 방지하기 위해 검사 및 인사이트를 개선해야 합니다.

    - 기존 쿼리를 수정하거나 새 쿼리를 만들어 손상이나 인시던트에 얻은 인사이트를 바탕으로 조기 감지를 지원합니다.

    - 가능한 공격에 대한 수준 높은 인사이트를 제공하는 헌팅 쿼리를 발견하거나 만든 후 쿼리를 기준으로 사용자 지정 검색 규칙을 만들고, 해당 인사이트를 보안 인시던트 응답자에게 경고로 제공할 수도 있습니다.

        쿼리의 결과를 확인하고 **새 경고 규칙** > **Azure Sentinel 경고 만들기** 를 선택합니다. **분석 규칙 마법사** 를 사용하여 쿼리를 기준으로 새 규칙을 만들 수 있습니다. 자세한 내용은 [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)를 참조하세요.


> [!TIP]
> - 이제 퍼블릭 미리 보기에서 Azure Data Explorer에 저장된 데이터에 대해 헌팅 및 라이브 스트림 쿼리를 만들 수도 있습니다. 자세한 내용은 Azure Monitor 설명서의 [리소스 간 쿼리 생성](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md)에 대한 세부 정보를 참조하세요.
>
> - [Azure Sentinel GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Hunting%20Queries)와 같은 커뮤니티 리소스를 사용하여 추가 쿼리 및 데이터 원본을 찾을 수 있습니다.
>

## <a name="use-the-hunting-dashboard-public-preview"></a>헌팅 대시보드 사용(퍼블릭 미리 보기)

헌팅 대시보드를 사용하면 모든 쿼리 또는 선택한 하위 집합을 한 번 선택하여 실행할 수 있습니다. Azure Sentinel 포털에서 **헌팅** 을 선택합니다.

표시되는 표에는 Microsoft의 보안 분석가 팀에서 작성한 모든 쿼리와 사용자가 만들었거나 수정한 추가 쿼리가 표시됩니다. 각 쿼리는 헌팅하는 것에 대한 설명 및 실행되는 데이터의 종류를 제공합니다. 해당 템플릿은 다양한 전략에 따라 그룹화됩니다. 오른쪽의 아이콘은 초기 액세스, 지속성, 반출 등의 위협 유형을 분류합니다.

:::image type="content" source="media/hunting/hunting-start.png" alt-text="Azure Sentinel에서 헌팅 시작" lightbox="media/hunting/hunting-start.png":::

헌팅 대시보드를 사용하여 24시간 동안의 결과 수, 급증 또는 결과 수 변경을 확인하여 헌팅을 시작할 위치를 식별합니다. 즐겨찾기, 데이터 원본, MITER ATT&CK 전술 또는 기술, 결과 또는 결과 델타별로 정렬하고 필터링합니다. 연결된 데이터 원본이 아직 필요한 쿼리를 확인하고, 이러한 쿼리를 사용하도록 설정하는 방법에 대한 권장 사항을 확인합니다.

다음 표에서는 헌팅 대시보드에서 사용할 수 있는 자세한 작업을 설명합니다.

|작업  |설명  |
|---------|---------|
|**쿼리가 사용자 환경에 적용되는 방식 확인**     |   **모든 쿼리 실행(미리 보기)** 단추를 선택하거나 각 행의 왼쪽에 있는 확인란을 사용하여 쿼리 하위 집합을 선택한 다음 **선택한 쿼리 실행(미리 보기)** 단추를 선택합니다. <br><br>쿼리를 실행하는 데는 선택한 쿼리 수, 시간 범위 및 쿼리되는 데이터의 양에 따라 몇 초에서 몇 분 정도 걸릴 수 있습니다.      |
|**결과를 반환한 쿼리 보기**    |      쿼리 실행이 완료되면 **결과** 필터를 사용하여 결과를 반환한 쿼리를 확인합니다. <br>- 쿼리를 정렬하여 최대 결과 또는 최소 결과를 얻은 쿼리를 확인합니다. <br>- **결과** 필터에서 *해당 없음* 을 선택하여 환경에서 활성화되지 않은 쿼리를 확인합니다. <br>- 이 쿼리를 활성화하는 데 필요한 데이터 원본을 보려면 *해당 없음* 옆에 있는 정보 아이콘(**i**) 위로 마우스를 가져갑니다.  |
|**데이터의 급증 식별**     |   **결과 델타** 를 정렬하거나 필터링하여 데이터 급증을 식별합니다. <br><br>이를 통해 지난 24시간의 결과와 이전 24~48시간의 결과를 비교하여 볼륨의 큰 차이를 강조 표시합니다.     |
|**MITRE Att&CK 전술에 매핑된 쿼리 보기**     | 표 상단의 **MITRE ATT&CK 전술 표시줄** 에는 각 MITER ATT&CK 전술에 매핑된 쿼리 수가 나열됩니다. 전술 표시줄은 적용된 현재 필터 집합에 따라 동적으로 업데이트됩니다. <br><br>이를 통해 주어진 결과 수, 높은 결과 델타, *해당 없음* 결과 또는 기타 필터 집합을 기준으로 필터링할 때 표시되는 MITER ATT&CK 전술을 쉽게 확인할 수 있습니다.        |
|**기술에 매핑된 쿼리 보기**     | 쿼리를 MITER ATT&CK 기술에 매핑할 수도 있습니다. **기술** 필터를 사용하여 MITER ATT&CK 기술별로 필터링하거나 정렬할 수 있습니다. 쿼리를 열면 기술을 선택하여 MITER ATT&CK의 기술 설명을 확인할 수 있습니다.        |
|**즐겨찾기에 쿼리 저장**     |   즐겨찾기에 저장된 쿼리는 **헌팅** 페이지에 액세스할 때마다 자동으로 실행됩니다. 사용자 고유의 헌팅 쿼리를 만들거나 기존 헌팅 쿼리 템플릿을 복제하고 사용자 지정할 수 있습니다.      |
|**쿼리 실행**     |   헌팅 쿼리 세부 정보 페이지에서 **쿼리 실행** 을 선택하여 헌팅 페이지에서 직접 쿼리를 실행합니다. 일치 항목 수는 **결과** 열의 표에 표시됩니다. 헌팅 쿼리 및 일치 항목의 목록을 검토합니다.     |
|**기본 쿼리 검토**     | 쿼리 세부 정보 창에서 기본 쿼리를 빠르게 검토합니다. **쿼리 결과 보기** 링크(쿼리 창 아래) 또는 **결과 보기** 단추(창 하단)를 클릭하여 결과를 볼 수 있습니다. 쿼리는 **로그**(Log Analytics) 블레이드에서 열리고 쿼리 아래에서 쿼리와 일치하는 항목을 검토할 수 있습니다.         |
|     |         |


## <a name="create-your-own-bookmarks"></a>사용자 고유의 책갈피 만들기

헌팅 및 조사 프로세스 중에 비정상적이거나 의심스러워 보일 수 있는 쿼리 결과가 나타날 수 있습니다. 이러한 항목에 책갈피를 지정하면 나중에 조사를 위해 인시던트를 만들거나 보강하는 경우 다시 참조할 수 있습니다.

- 결과에서 유지할 행에 대해 확인란을 표시하고 **책갈피 추가** 를 선택합니다. 이렇게 하면 표시된 각 행에 대해 행 결과, 결과를 만든 쿼리와 함께 사용자, 호스트 및 IP 주소를 추출하기 위한 엔터티 매핑이 포함된 책갈피 레코드가 만들어집니다. 각 책갈피에 고유한 태그 및 메모를 추가할 수 있습니다.

- 기본 **헌팅** 페이지에서 **책갈피** 탭을 클릭하여 책갈피가 지정된 모든 결과를 확인합니다. 태그를 책갈피에 추가하여 필터링을 위해 분류합니다. 예를 들어 공격 캠페인을 조사하는 경우 캠페인에 대한 태그를 만들고 관련 책갈피에 태그를 지정한 다음 캠페인을 기반으로 모든 책갈피를 필터링할 수 있습니다.

- 책갈피를 선택한 다음, 세부 정보 창에서 **조사** 를 클릭하여 조사 환경을 연 후 책갈피가 지정된 단일 결과를 조사합니다.

    하나 이상의 책갈피에서 인시던트를 만들거나 기존 인시던트에 하나 이상의 책갈피를 추가할 수도 있습니다. 사용하려는 책갈피의 왼쪽에 있는 확인란을 선택한 다음, **인시던트 작업** > **새 인시던트 만들기** 또는 **기존 인시던트에 추가** 를 선택합니다. 그런 다음, 다른 인시던트와 마찬가지로 인시던트를 심사하고 조사할 수 있습니다.


> [!TIP]
> 책갈피는 중요한 주요 이벤트를 표시하는 데 사용할 수 있으며 상당히 심각하므로 조사를 반드시 진행해야 하는 경우 인시던트로 에스컬레이션해야 합니다. 잠재적 근본 원인, 손상 표시 또는 기타 주의할 만한 이벤트 등을 책갈피를 통해 제기해야 합니다.
>

자세한 내용은 [헌팅에 책갈피 사용](bookmarks.md)을 참조하세요.

## <a name="use-notebooks-to-power-investigations"></a>Notebook을 사용하여 조사 강화

헌팅 및 조사가 더 복잡해지면 Azure Sentinel Notebook을 사용하여 기계 학습, 시각화 및 데이터 분석을 통해 활동을 향상시킵니다.

Notebook은 완전한 조사를 수행할 수 있는 자체 커널로 완성된 일종의 가상 샌드박스를 제공합니다. Notebook에는 원시 데이터, 해당 데이터에서 실행하는 코드, 결과 및 시각화가 포함될 수 있습니다. 조직에서 다시 사용할 수 있도록 다른 사용자와 공유할 수 있도록 Notebook을 저장합니다.

Notebook은 헌팅 또는 조사가 너무 커서 쉽게 기억하지 못하거나, 세부 정보를 보거나, 쿼리와 결과를 저장해야 하는 경우에 유용할 수 있습니다. Notebook을 만들고 공유할 수 있도록 Azure Sentinel Azure Sentinel Notebook 페이지에 직접 통합된 오픈 소스 대화형 개발 및 데이터 조작 환경인 [Jupyter](https://jupyter.org) **Notebook을** 제공합니다.

자세한 내용은 다음을 참조하세요.

- [Jupyter Notebook을 사용하여 보안 위협 헌팅](notebooks.md)
- [Jupyter Project 설명서](https://jupyter.org/documentation)
- [Jupyter 소개 설명서.](https://jupyter.readthedocs.io/en/latest/tryjupyter.html)
- [The Infosec Jupyter Book](https://infosecjupyterbook.com)
- [실제 Python 자습서](https://realpython.com)

다음 표에서는 Juypter 노트북을 사용 하 여 Azure 센티널의 프로세스에 도움이 되는 몇 가지 방법을 설명 합니다.

|메서드  |설명  |
|---------|---------|
|**데이터 지 속성, 반복성 및 역 추적**     |  많은 쿼리와 결과 집합을 사용 하 여 작업 하는 경우에는 일부 데드 끝이 있을 가능성이 높습니다. 유지할 쿼리와 결과를 결정 하 고 단일 보고서에 유용한 결과를 누적 하는 방법을 결정 해야 합니다. <br><br> Jupyter 노트북을 사용 하 여 쿼리 및 데이터를 저장 하 고, 변수를 사용 하 여 다른 값 이나 날짜로 쿼리를 다시 실행 하거나, 쿼리를 저장 하 여 이후 조사에서 다시 실행 합니다.       |
|**스크립팅 및 프로그래밍**     |    Jupyter 노트북을 사용 하 여 다음을 포함 하 여 쿼리에 프로그래밍을 추가 합니다. <br><br>- [KQL (kusto Query Language)](/azure/kusto/query/) 또는 SQL 같은 *선언적* 언어를 통해 논리를 매우 복잡 한 단일 문으로 인코딩할 수 있습니다.<br>- 일련의 단계로 논리를 실행 하는 *절차* 프로그래밍 언어 <br><br>논리를 단계로 분할 하면 중간 결과를 확인 및 디버그 하 고, 쿼리 언어에서 사용할 수 없는 기능을 추가 하 고, 나중에 처리 하는 단계에서 부분 결과를 다시 사용할 수 있습니다.     |
|**외부 데이터에 대 한 링크**     | Azure 센티널 테이블에는 대부분의 원격 분석 및 이벤트 데이터가 있지만 Jupyter 노트북은 네트워크를 통해 또는 파일에서 액세스할 수 있는 모든 데이터에 연결할 수 있습니다. Jupyter 노트북을 사용 하면 다음과 같은 데이터를 포함할 수 있습니다. <br><br>-사용자가 소유 하지 않은 외부 서비스의 데이터 (예: 지리적 위치 데이터 또는 위협 인텔리전스 원본)<br>사용자의 조직 내에만 저장 되는 중요 한 데이터 (예: 인적 자원 데이터베이스 또는 상위 가치의 자산 목록)<br>-클라우드로 아직 마이그레이션하지 않은 데이터입니다.        |
|**전문화 된 데이터 처리, 기계 학습 및 시각화 도구**     | Jupyter 노트북은 추가 시각화, 기계 학습 라이브러리, 데이터 처리 및 변환 기능을 제공 합니다. <br><br>예를 들어 다음 [Python](https://python.org) 기능과 함께 Jupyter 노트북을 사용 합니다.<br>- 데이터 처리, 정리 및 엔지니어링을 위한 [pandas](https://pandas.pydata.org/)<br>- 시각화에 대 한 [Matplotlib](https://matplotlib.org), [HoloViews](https://holoviews.org)및 [Plotly](https://plot.ly)<br>- 고급 숫자 및 과학적 처리를 위한 [NumPy](https://www.numpy.org) 및 [SciPy](https://www.scipy.org)<br>- [scikit-](https://scikit-learn.org/stable/index.html) machine learning에 대해 알아보기<br>- 심층 학습을 위한 [TensorFlow](https://www.tensorflow.org/), [PyTorch](https://pytorch.org)및 [keras](https://keras.io/)<br><br>**팁**: jupyter 노트북은 다중 언어 커널을 지원 합니다. *매직* 를 사용 하면 다른 언어를 사용 하 여 개별 셀을 실행할 수 있으므로 동일한 노트북 내에서 언어를 혼합할 수 있습니다. 예를 들어 PowerShell 스크립트 셀을 사용 하 여 데이터를 검색 하 고, Python에서 데이터를 처리 하 고, JavaScript를 사용 하 여 시각화를 렌더링할 수 있습니다.        |
|     |         |

### <a name="mstic-jupyter-and-python-security-tools"></a>MSTIC, Jupyter 및 Python 보안 도구

[Microsoft 위협 인텔리전스 센터 (MSTIC)](https://msrc-blog.microsoft.com/tag/mstic/) 는 여러 microsoft 플랫폼에 대 한 보안 검색을 작성 하 고 위협 식별 및 조사를 수행 하는 microsoft 보안 분석가 및 엔지니어 팀입니다.

MSTIC는 Jupyter 노트북에서 정보 보안 조사 및 사냥을 위한 라이브러리인 [MSTICPy](https://github.com/Microsoft/msticpy)를 빌드 했습니다. MSTICPy는 노트북 생성 속도를 높이는 재사용 가능한 기능을 제공 하 고 사용자가 Azure 센티널에서 노트북을 보다 쉽게 읽을 수 있도록 합니다.

예를 들어, MSTICPy는 다음을 수행할 수 있습니다.

- 여러 원본에서 로그 데이터를 쿼리 합니다.
- 위협 인텔리전스, geolocations 및 Azure 리소스 데이터를 사용 하 여 데이터를 보강 합니다.
- 로그에서 활동 표시기 (IoA)를 추출 하 고 인코딩된 데이터의 압축을 풉니다.
- 비정상적인 세션 검색 및 시계열 분해와 같은 정교한 분석을 수행 합니다.
- 대화형 타임 라인, 프로세스 트리 및 다차원 모핑 차트를 사용 하 여 데이터를 시각화 합니다.

또한 MSTICPy에는 쿼리 시간 경계를 설정 하 고, 목록에서 항목을 선택 하 고 표시 하 고, 노트북 환경을 구성 하는 위젯 등의 몇 가지 시간 절약 노트 도구가 포함 되어 있습니다.

자세한 내용은 다음을 참조하세요.

- [MSTICPy 설명서](https://msticpy.readthedocs.io/en/latest/)
- [자습서: Azure 센티널에서 Jupyter 노트북 및 MSTICPy 시작 하기](notebook-get-started.md)
- [Azure 센티널의 Jupyter 노트북 및 MSTICPy에 대 한 고급 구성](notebooks-msticpy-advanced.md)


## <a name="useful-operators-and-functions"></a>유용한 연산자 및 함수

헌팅 쿼리는 기본 제공된 [KQL(Kusto Query Language)](/azure/data-explorer/kusto/query/)로, 헌팅을 다음 수준으로 가져오는 데 필요한 성능과 유연성을 제공하는 IntelliSense 언어를 포함하는 강력한 쿼리 언어입니다.

이 언어는 분석 규칙의 쿼리와 Azure Sentinel의 다른 곳에 있는 쿼리에서 사용하는 언어와 동일합니다. 자세한 내용은 [쿼리 언어 참조](../azure-monitor/logs/get-started-queries.md)를 참조하세요.

Azure Sentinel 헌팅 쿼리에서는 다음과 같은 연산자를 특히 유용하게 사용할 수 있습니다.

- **where** - 조건자를 충족하는 행의 하위 집합으로 테이블을 필터링합니다.

- **summarize** - 입력 테이블의 콘텐츠를 집계하는 테이블을 생성합니다.

- **join** - 두 테이블의 행을 병합하여 각 테이블에서 지정된 열의 값을 일치시키는 방식으로 새 테이블을 만듭니다.

- **count** - 입력 레코드 집합의 레코드 수를 반환합니다.

- **top** - 지정한 열을 기준으로 정렬된 처음 N개 레코드를 반환합니다.

- **limit** - 최대 지정된 수의 행을 반환합니다.

- **project** - 포함하거나, 이름을 바꾸거나 또는 삭제할 열을 선택하고 새로운 컴퓨팅된 열을 삽입합니다.

- **extend** - 계산된 열을 만들고 결과 집합에 추가합니다.

- **makeset** - 해당 그룹에서 가져오는 고유 값 집합의 동적(JSON) 배열을 반환합니다.

- **find** - 테이블 집합 사이에서 조건자와 일치하는 행을 찾습니다.

- **adx()(미리 보기)** - 이 함수는 Azure Sentinel 헌팅 환경 및 Log Analytics에서 Azure Data Explorer 데이터 원본에 대한 리소스 간 쿼리를 수행합니다. 자세한 내용은 [Azure Monitor를 사용하여 Azure Data Explorer 리소스 간 쿼리](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md)를 참조하세요.

## <a name="save-a-query"></a>쿼리 저장

쿼리를 만들거나 수정하고 사용자의 쿼리로 저장하거나 동일한 테넌트에 있는 사용자와 공유합니다.

:::image type="content" source="./media/hunting/save-query.png" alt-text="쿼리 저장" lightbox="./media/hunting/save-query.png":::

**새 쿼리를 만들려면**

1. **새 쿼리** 를 선택합니다.

1. 모든 빈 필드를 채우고 **만들기** 를 선택합니다.

    :::image type="content" source="./media/hunting/new-query.png" alt-text="새 쿼리" lightbox="./media/hunting/new-query.png":::

**기존 헌팅 쿼리를 복제하고 수정합니다.**

1. 수정하려는 테이블에서 헌팅 쿼리를 선택합니다.

1. 수정하려는 쿼리 줄에서 줄임표(...)를 선택하고 **쿼리 복제** 를 선택합니다.

    :::image type="content" source="./media/hunting/clone-query.png" alt-text="쿼리 복제" lightbox="./media/hunting/clone-query.png":::

1. 쿼리를 수정하고 **만들기** 를 선택합니다.

    :::image type="content" source="./media/hunting/custom-query.png" alt-text="사용자 지정 쿼리" lightbox="./media/hunting/custom-query.png":::



## <a name="sample-query"></a>샘플 쿼리

일반적인 쿼리는 테이블 이름으로 시작하며 그 뒤에 로 구분된 일련의 \|연산자가 있습니다.

위의 예제에서 필요에 따라 테이블 이름 SecurityEvent로 시작하고 파이프된 요소를 추가합니다.

1. 이전 7일 동안의 레코드만을 검토하는 시간 필터를 정의합니다.

1. 이벤트 ID 4688만 표시하도록 쿼리에 필터를 추가합니다.

1. cscript.exe 인스턴스만 포함하는 명령줄의 쿼리에 필터를 추가합니다.

1. 검색하려는 열만 프로젝션하고 결과를 1000으로 제한한 다음 **쿼리 실행** 을 선택합니다.

1. 녹색 삼각형을 클릭하고 쿼리를 실행합니다. 쿼리를 테스트하고 실행하여 비정상적인 동작을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel로 헌팅 조사를 실행하는 방법에 대해 알아보았습니다. 

자세한 내용은 다음을 참조하세요.

- [Notebook을 사용하여 자동화된 헌팅 캠페인 실행](notebooks.md)
- [헌팅 시 책갈피를 사용하여 흥미로운 정보 저장하기](bookmarks.md)

[사용자 지정 커넥터](create-custom-connector.md)를 사용한 [Zoom 모니터링](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) 시, 사용자 지정 분석 규칙을 사용하는 예제를 알아봅니다.
