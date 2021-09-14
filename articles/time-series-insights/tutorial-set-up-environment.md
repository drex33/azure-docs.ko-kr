---
title: '자습서: Gen2 환경 설정 - Azure Time Series Insights Gen2 | Microsoft Docs'
description: '자습서: Azure Time Series Insights Gen2에서 환경을 설정하는 방법에 대해 알아봅니다.'
author: riserrad
ms.author: riserrad
ms.workload: big-data
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: seodec18
ms.openlocfilehash: 7a6562592bb7c0ae37784429d6a36eaf4ade4957
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449318"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>자습서: Azure Time Series Insights Gen2 환경 설정

이 자습서에서는 Azure Time Series Insights Gen2 *PAYG(종량제)* 환경을 만드는 프로세스를 단계별로 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Azure Time Series Insights Gen2 환경을 만듭니다.
> * IoT Hub에 Azure Time Series Insights Gen2 환경 연결
> * Azure Time Series Insights Gen2 환경으로 데이터를 스트리밍하도록 솔루션 가속기 샘플 실행
> * 데이터에 대한 기본 분석을 수행합니다.
> * 시계열 모델 형식 및 계층 구조 정의 및 인스턴스와 연결

>[!TIP]
> [IoT 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators)는 사용자 지정 IoT 솔루션의 개발 속도를 높이는 데 사용할 수 있는 미리 구성된 엔터프라이즈급 솔루션을 제공합니다.

아직 Azure 구독이 없는 경우 [평가판 Azure 구독](https://azure.microsoft.com/free/)에 등록합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 적어도 Azure 구독에 대한 **기여자** 역할이 있어야 합니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

* [Azure Portal](#create-an-azure-time-series-insights-gen2-environment) 또는 [CLI](how-to-create-environment-using-cli.md)를 사용하여 환경을 만듭니다.

## <a name="create-a-device-simulation"></a>디바이스 시뮬레이션 만들기

이 섹션에서는 Azure IoT Hub 인스턴스로 데이터를 보내는 세 개의 시뮬레이션된 디바이스를 만듭니다.

1. [Azure IoT 솔루션 가속기 페이지](https://www.azureiotsolutions.com/Accelerators)로 이동합니다. Azure 계정을 사용하여 로그인한 다음, **디바이스 시뮬레이션** 을 선택합니다.

   [![Azure IoT 솔루션 가속기 페이지](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-set-up-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. 아래쪽으로 스크롤하여 [개요](https://github.com/Azure/azure-iot-pcs-device-simulation#overview) 및 [시작](https://github.com/Azure/azure-iot-pcs-device-simulation#getting-started) 섹션을 읽습니다.

1. 시작 섹션의 [배포 지침](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/deployment/README.md)을 따릅니다.

   이 프로세스는 완료하는 데 최대 20분이 소요됩니다.

1. 배포가 완료되면 시뮬레이션에 대한 URL이 제공됩니다. 나중에 다시 돌아올 수 있으므로 이 페이지는 그대로 열어 두세요.

   >[!IMPORTANT]
   > 솔루션 가속기를 아직 입력하지 마세요! 나중에 다시 돌아올 수 있으므로 이 웹 페이지는 그대로 열어 두세요.

   [![디바이스 시뮬레이션 솔루션 프로비저닝 완료](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png)](media/tutorial-set-up-environment/iot-solution-accelerator-ready.png#lightbox)

1. 이제 Azure Portal에서 새로 만든 리소스를 검사합니다. **리소스 그룹** 페이지에서 ARM 템플릿 매개 변수 파일에 제공한 `solutionName`을 사용하여 새 리소스 그룹이 만들어졌는지 확인합니다. 디바이스 시뮬레이션을 위해 만든 리소스를 기록해 둡니다.

   [![디바이스 시뮬레이션 리소스](media/tutorial-set-up-environment/device-sim-solution-resources.png)](media/tutorial-set-up-environment/device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Azure Time Series Insights Gen2 환경 만들기

이 섹션에서는 Azure Time Series Insights Gen2 환경을 만들고 [Azure Portal](https://portal.azure.com/)을 사용하여 IoT 솔루션 가속기에서 만든 IoT Hub에 연결하는 방법에 대해 설명합니다.

1. Azure 구독 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 위에서 **+ 리소스 만들기** 를 선택합니다.
1. **사물 인터넷** 범주를 선택한 다음, **Time Series Insights** 를 선택합니다.

   [![Time Series Insights 환경 리소스 선택](media/tutorial-set-up-environment/create-new-environment.png)](media/tutorial-set-up-environment/create-new-environment.png#lightbox)

1. **Time Series Insights 환경 만들기** 창의 **기본 사항** 탭에서 다음 매개 변수를 설정합니다.

    | 매개 변수 | 작업 |
    | --- | ---|
    | **환경 이름** | Azure Time Series Insights Gen2 환경에 대한 고유 이름을 입력합니다. |
    | **구독** | Azure Time Series Insights Gen2 환경을 만들려는 구독을 입력합니다. 디바이스 시뮬레이터에서 만든 나머지 IoT 리소스와 동일한 구독을 사용하는 것이 좋습니다. |
    | **리소스 그룹** | Azure Time Series Insights Gen2 환경 리소스에 대해 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스에 대한 컨테이너입니다. 디바이스 시뮬레이터에서 만든 다른 IoT 리소스와 동일한 리소스 그룹을 사용하는 것이 좋습니다. |
    | **위치** | Azure Time Series Insights Gen2 환경에 사용할 데이터 센터 영역을 선택합니다. 대기 시간이 늘어나지 않도록, 디바이스 시뮬레이터에서 만든 IoT 허브와 동일한 영역에 Azure Time Series Insights Gen2 환경을 만드는 것이 가장 좋습니다. |
    | **계층** |  **Gen2(L1)** 를 선택합니다. Azure Time Series Insights Gen2 제품에 대한 SKU입니다. |
    | **시계열 ID 속성 이름** | 시계열 인스턴스를 고유하게 식별하는 값을 포함하는 속성의 이름을 입력합니다. **속성 이름** 상자에 시계열 ID로 입력한 값은 나중에 변경할 수 없습니다. 이 자습서에서는 **_iothub-connection-device-id_** 를 입력합니다. 복합 시계열 ID를 비롯한 시계열 ID에 대한 자세한 내용은 [시계열 ID 선택 모범 사례](./how-to-select-tsid.md)를 참조하세요. |
    | **스토리지 계정 이름** | 새 스토리지 계정의 전역적으로 고유한 이름을 입력합니다.|
    | **스토리지 계정 종류** | 새 스토리지 계정의 스토리지 종류를 선택합니다. StorageV2 권장|
    | **스토리지 계정 복제** | 새 스토리지 계정의 스토리지 종류를 선택합니다. 선택한 위치에 따라 LRS, GRS 및 ZRS 중에서 선택할 수 있습니다. 이 자습서에서는 LRS를 선택할 수 있습니다.|
    | **계층 구조 네임스페이스** |StorageV2 스토리지 종류를 선택하면 이 옵션을 선택할 수 있습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 이 자습서에서는 기본 *사용 안 함* 상태로 둘 수 있습니다.|
    |**웜 저장소 사용**|**예** 를 선택하여 웜 저장소를 사용하도록 설정합니다. 환경을 만든 후에도 이 설정을 사용하지 않도록 설정했다가 다시 사용하도록 설정할 수 있습니다. |
    |**데이터 보존 기간(일)**|기본 옵션인 7일을 선택합니다. |

    [![새로운 Azure Time Series Insights 환경 구성.](media/tutorial-set-up-environment/environment-configuration.png)](media/tutorial-set-up-environment/environment-configuration.png#lightbox)
    [![새로운 Azure Time Series Insights 환경 구성을 계속 진행합니다.](media/tutorial-set-up-environment/environment-configuration2.png)](media/tutorial-set-up-environment/environment-configuration2.png#lightbox)

1. 완료되면 **다음: 이벤트 원본** 을 클릭합니다.

   [![환경에 대한 시계열 ID를 구성합니다.](media/tutorial-set-up-environment/time-series-id-selection.png)](media/tutorial-set-up-environment/time-series-id-selection.png#lightbox)

1. **이벤트 원본** 탭에서 다음 매개 변수를 설정합니다.

   | 매개 변수 | 작업 |
   | --- | --- |
   | **이벤트 원본을 만드시겠습니까?** | **예** 를 선택합니다.|
   | **원본 유형** | **IoT Hub** 를 선택합니다. |
   | **이름** | 이벤트 원본 이름에 대한 고유 값을 입력합니다. |
   | **허브 선택** | **기존 항목 선택** 을 선택합니다. |
   | **구독** | 디바이스 시뮬레이터에 사용한 구독을 선택합니다. |
   | **IoT Hub 이름** | 디바이스 시뮬레이터에 대해 만든 IoT Hub 이름을 선택합니다. |
   | **Iot Hub 액세스 정책** | **iothubowner** 를 선택합니다. |
   | **IoT Hub 소비자 그룹** | **새로 만들기** 를 선택하고, 고유한 이름을 입력한 다음, **+ 추가** 를 선택합니다. 소비자 그룹은 Azure Time Series Insights Gen2에서 고유한 값이어야 합니다. |
   | **시작 옵션** | **지금 시작** 을 선택합니다. |
   | **타임스탬프 속성** | 이 값을 사용하여 들어오는 원격 분석 데이터의 **Timestamp**(타임스탬프) 속성을 식별합니다. 이 자습서에서는 이 상자를 비워 둡니다. 이 시뮬레이터는 Azure Time Series Insights Gen2가 기본값을 지정한 IoT Hub에서 들어오는 타임스탬프를 사용합니다. |

   :::image type="content" source="media/tutorial-set-up-environment/configure-event-source.png" alt-text="만든 IoT 허브를 이벤트 원본으로 구성합니다" lightbox="media/tutorial-set-up-environment/configure-event-source.png":::.

1. **검토 + 만들기** 를 선택합니다.

   :::image type="content" source="media/tutorial-set-up-environment/environment-confirmation.png" alt-text="만들기 단추가 있는 + 만들기 페이지를 검토합니다" lightbox="media/tutorial-set-up-environment/environment-confirmation.png":::.

   다음과 같이 배포 상태를 검토할 수 있습니다.

   [![배포가 완료되었다는 알림.](media/tutorial-set-up-environment/deployment-notification.png)](media/tutorial-set-up-environment/deployment-notification.png#lightbox)

1. 배포 세부 정보를 확장합니다.

## <a name="stream-data"></a>스트림 데이터

Azure Time Series Insights Gen2 환경을 배포했으므로, 분석할 데이터 스트리밍을 시작합니다.

1. 솔루션 가속기 배포가 완료되면 URL이 제공됩니다.

1. URL을 클릭하여 디바이스 시뮬레이션을 시작합니다.

1. **+ 새 시뮬레이션** 을 선택합니다.

    1. **시뮬레이션 설정** 페이지가 로드되면 필수 매개 변수를 입력합니다.

        | 매개 변수 | 작업 |
        | --- | --- |
        | **이름** | 시뮬레이터에 사용할 고유한 이름을 입력합니다. |
        | **설명** | 정의를 입력합니다. |
        | **시뮬레이션 기간** | **무기한 실행** 으로 설정합니다. |
        | **디바이스 모델** | \+ **디바이스 유형 추가** 를 클릭합니다. <br />**Name**: **엘리베이터** 를 입력합니다. <br />**수량**: **3** 을 입력합니다. <br /> 나머지 기본값은 그대로 둡니다. |
        | **대상 IoT Hub** | **사전 프로비전된 IoT Hub 사용** 으로 설정합니다. |

        [![매개 변수를 구성하고 시작합니다.](media/tutorial-set-up-environment/launch-solution-accelerator.png)](media/tutorial-set-up-environment/launch-solution-accelerator.png#lightbox)

    1. **시뮬레이션 시작** 을 선택합니다. 디바이스 시뮬레이션 대시보드에는 **활성 디바이스** 및 **총 메시지** 가 표시됩니다.

        [![Azure IoT 시뮬레이션 대시보드.](media/tutorial-set-up-environment/see-active-devices-and-messages.png)](media/tutorial-set-up-environment/see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>데이터 분석

이 섹션에서는 [Azure Time Series Insights Gen2 탐색기](./concepts-ux-panels.md)를 사용하여 시계열 데이터에 대해 기본 분석을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 리소스 페이지에서 URL을 선택하여 Azure Time Series Insights Gen2 탐색기로 이동합니다.

    [![Azure Time Series Insights Gen2 탐색기 URL.](media/tutorial-set-up-environment/select-explorer-url.png)](media/tutorial-set-up-environment/select-explorer-url.png#lightbox)

1. Azure Time Series Insights Gen2 탐색기에는 화면 위쪽에 걸쳐 있는 막대가 표시됩니다. 이 막대가 가용성 선택기입니다. 2m을 두 개 이상 선택하고, 필요한 경우 선택기 핸들을 선택하고 좌우로 끌어 시간 프레임을 확장합니다.

1. **시계열 인스턴스** 가 왼쪽에 표시됩니다.

    [![부모가 없는 인스턴스 목록.](media/tutorial-set-up-environment/explorer-unparented-instances.png)](media/tutorial-set-up-environment/explorer-unparented-instances.png#lightbox)

1. 첫 번째 시계열 인스턴스를 선택합니다. 그런 다음, **온도 표시** 를 선택합니다.

    [![평균 온도를 표시하는 메뉴 명령으로 선택한 시계열 인스턴스.](media/tutorial-set-up-environment/select-instance-and-temperature.png)](media/tutorial-set-up-environment/select-instance-and-temperature.png#lightbox)

    시계열 차트가 표시됩니다. **간격** 을 **30** 일로 변경합니다.

1. 이 차트에 표시된 것처럼, 세 시계열 인스턴스를 모두 볼 수 있도록 다른 두 시계열 인스턴스에도 이전 단계를 반복합니다.

    [![모든 시계열에 대한 차트.](media/tutorial-set-up-environment/explorer-add-three-instances.png)](media/tutorial-set-up-environment/explorer-add-three-instances.png#lightbox)

1. 오른쪽 위 모서리에서 시간 범위 선택기를 선택합니다. 여기서 특정 시작 및 종료 시간을 밀리초 단위로 선택할 수도 있고, 미리 구성된 옵션(예: **최근 30분**) 중에 선택할 수도 있습니다. 기본 표준 시간대를 변경할 수도 있습니다.

    [![시간 범위를 최근 30분으로 설정합니다.](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png)](media/tutorial-set-up-environment/explorer-thirty-minute-time-range.png#lightbox)

    **최근 30분** 동안 솔루션 가속기 진행률이 Azure Time Series Insights Gen2 탐색기에 표시됩니다.

## <a name="define-and-apply-a-model"></a>모델 정의 및 적용

이 섹션에서는 데이터를 구성하는 모델을 적용합니다. 모델을 완료하려면 형식, 계층 구조 및 인스턴스를 정의합니다. 데이터 모델링에 대한 자세한 내용은 [시계열 모델](./concepts-model-overview.md)을 참조하세요.

1. 탐색기에서 **모델** 탭을 선택합니다.

   [![탐색기의 모델 탭을 봅니다.](media/tutorial-set-up-environment/select-model-view.png)](media/tutorial-set-up-environment/select-model-view.png#lightbox)

   **형식** 탭에서 **+ 추가** 를 선택합니다.

1. 다음 매개 변수를 입력합니다.

    | 매개 변수 | 작업 |
    | --- | ---|
    | **이름** | **엘리베이터** 를 입력합니다. |
    | **설명** | **엘리베이터의 유형 정의** 를 입력합니다. |

1. 다음으로, **변수** 탭을 선택합니다.

    1. **+ 변수 추가** 를 선택하고 엘리베이터 유형의 첫 번째 변수로 다음 값을 입력합니다. 총 세 개의 변수를 작성해야 합니다.

        | 매개 변수 | 작업 |
        | --- | --- |
        | **이름** | **평균 온도** 를 입력합니다. |
        | **종류** | **숫자** 를 선택합니다. |
        | **값** | 다음과 같은 사전 설정 중에서 선택합니다. **온도(Double)** 를 선택합니다. <br /> 참고: Azure Time Series Insights Gen2에서 이벤트를 받기 시작한 후 **값** 이 자동으로 채워지는 데 몇 분이 걸릴 수 있습니다.|
        | **집계 작업** | **고급 옵션** 을 확장합니다. <br /> **평균** 을 선택합니다. |

    1. **적용** 을 선택합니다. 그런 다음, **+ 변수 추가** 를 다시 선택하고 다음 값을 설정합니다.

        | 매개 변수 | 작업 |
        | --- | --- |
        | **이름** | **평균 진동** 을 입력합니다. |
        | **종류** | **숫자** 를 선택합니다. |
        | **값** | 다음과 같은 사전 설정 중에서 선택합니다. **진동(두 배)** 를 선택합니다. <br /> 참고: Azure Time Series Insights Gen2에서 이벤트를 받기 시작한 후 **값** 이 자동으로 채워지는 데 몇 분이 걸릴 수 있습니다.|
        | **집계 작업** | **고급 옵션** 을 확장합니다. <br /> **평균** 을 선택합니다. |

    1. **적용** 을 선택합니다. 그런 다음, **+ 변수 추가** 를 다시 선택하고, 세 번째 변수와 마지막 변수에 대한 다음 값을 설정합니다.

        | 매개 변수 | 작업 |
        | --- | --- |
        | **이름** | **층** 을 입력합니다. |
        | **종류** | **범주** 를 선택합니다. |
        | **값** | 다음과 같은 사전 설정 중에서 선택합니다. **층(두 배)** 을 선택합니다. <br /> 참고: Azure Time Series Insights Gen2에서 이벤트를 받기 시작한 후 **값** 이 자동으로 채워지는 데 몇 분이 걸릴 수 있습니다.|
        | **범주** | **레이블**: **값** <br /> 저층: 1,2,3,4 <br /> 중층: 5,6,7,8,9 <br /> 고층: 10,11,12,13,14,15 |
        | **기본 범주** | **알 수 없음** 을 입력합니다. |

        [![유형 변수를 추가합니다.](media/tutorial-set-up-environment/add-type-variables.png)](media/tutorial-set-up-environment/add-type-variables.png#lightbox)

    1. **적용** 을 선택합니다.
    1. **저장** 을 선택합니다. 세 개의 변수가 생성되어 표시됩니다.

        [![형식을 추가한 후 모델 뷰에서 해당 형식을 검토합니다.](media/tutorial-set-up-environment/add-type-and-view.png)](media/tutorial-set-up-environment/add-type-and-view.png#lightbox)

1. **계층** 탭을 선택합니다. 그런 다음, **+ 추가** 를 선택합니다.

   1. **계층 구조 편집** 창에서 다음 매개 변수를 설정합니다.

        | 매개 변수 | 작업 |
        | --- | ---|
        | **이름** | **위치 계층 구조** 를 입력합니다. |
        |**수준**| 첫 번째 수준의 이름으로 **국가** 를 입력합니다.<br />**+ 수준 추가** 를 선택합니다.<br />두 번째 수준에 **도시** 를 입력하고, **+ 수준 추가** 를 선택합니다.<br />세 번째 및 마지막 수준의 이름으로 **건물** 을 입력합니다. |

   1. **저장** 을 선택합니다.

        [![모델 뷰에서 새 계층 구조를 표시합니다.](media/tutorial-set-up-environment/add-hierarchy-and-view.png)](media/tutorial-set-up-environment/add-hierarchy-and-view.png#lightbox)

1. **인스턴스** 로 이동합니다.

    1. 맨 오른쪽의 **작업** 에서 연필 아이콘을 선택하여 첫 번째 인스턴스를 다음 값으로 편집합니다.

        | 매개 변수 | 작업 |
        | --- | --- |
        | **형식** | **엘리베이터** 를 선택합니다. |
        | **이름** | **엘리베이터 1** 을 입력합니다.|
        | **설명** | **엘리베이터 1의 인스턴스** 를 입력합니다. |

    1. **인스턴스 필드** 로 이동하여 다음 값을 입력합니다.

        | 매개 변수 | 작업 |
        | --- | --- |
        | **계층 구조** | **위치 계층 구조** 를 선택합니다. |
        | **국가** | **미국** 을 입력합니다. |
        | **도시** | **시애틀** 을 입력합니다. |
        | **빌딩** | **Space Needle** 을 입력합니다. |

    1. **저장** 을 선택합니다.

1. 다음 값을 사용하는 동안 다른 두 인스턴스에 대해 이전 단계를 반복합니다.

    **엘리베이터 2:**

    | 매개 변수 | 작업 |
    | --- | --- |
    | **형식** | **엘리베이터** 를 선택합니다. |
    | **이름** | **엘리베이터 2** 를 입력합니다.|
    | **설명** | **엘리베이터 2의 인스턴스** 를 입력합니다. |
    | **계층 구조** | **위치 계층 구조** 를 선택합니다. |
    | **국가** | **미국** 을 입력합니다. |
    | **도시** | **시애틀** 을 입력합니다. |
    | **빌딩** | **태평양 과학 센터** 를 입력합니다. |

    **엘리베이터 3**:

    | 매개 변수 | 작업 |
    | --- | --- |
    | **형식** | **엘리베이터** 를 선택합니다. |
    | **이름** | **엘리베이터 3** 을 입력합니다.|
    | **설명** | **엘리베이터 3의 인스턴스** 를 입력합니다. |
    | **계층 구조** | **위치 계층 구조** 를 선택합니다. |
    | **국가** | **미국** 을 입력합니다. |
    | **도시** | **뉴욕** 을 입력합니다. |
    | **빌딩** | **엠파이어 스테이트 빌딩** 을 입력합니다. |

    [![업데이트된 인스턴스를 봅니다.](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png)](media/tutorial-set-up-environment/iot-solution-accelerator-instances.png#lightbox)

1. 다시 **분석** 탭으로 이동하여 차트 창을 봅니다. **위치 계층 구조** 아래에서 모든 계층 수준을 펼쳐 시계열 인스턴스를 표시합니다.

    [![차트 뷰에서 모든 계층을 봅니다.](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png)](media/tutorial-set-up-environment/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. **태평양 과학 센터** 아래에서 시계열 인스턴스 **엘리베이터 2** 를 선택한 다음, **평균 온도 표시** 를 선택합니다.

1. 동일한 인스턴스 **엘리베이터 2** 에서 **층 표시** 를 선택합니다.

    범주 변수를 사용하여 엘리베이터가 고층, 저층 및 중층에서 소요한 시간을 확인할 수 있습니다.

    [![계층 및 데이터를 사용하여 엘리베이터 2를 시각화합니다.](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png)](media/tutorial-set-up-environment/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>리소스 정리

이제 자습서를 완료했으므로 만든 리소스를 정리합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **모든 리소스** 를 선택하고 Azure Time Series Insights Gen2 리소스 그룹을 찾습니다.
1. **삭제** 를 선택하여 전체 리소스 그룹(및 그 안에 포함된 모든 리소스)을 삭제하거나 각 리소스를 개별적으로 제거합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

* 디바이스 시뮬레이션 가속기 만들기 및 사용
* Azure Time Series Insights Gen2 PAYG 환경 만들기
* IoT Hub에 Azure Time Series Insights Gen2 환경 연결
* Azure Time Series Insights Gen2 환경으로 데이터를 스트리밍하도록 솔루션 가속기 샘플 실행
* 데이터에 대한 기본 분석 수행
* 시계열 모델 형식 및 계층 구조 정의 및 인스턴스와 연결

이제 고유한 Azure Time Series Insights Gen2 환경을 만드는 방법을 알아보았으므로 Azure Time Series Insights Gen2의 주요 개념에 대해 알아봅니다.

Azure Time Series Insights Gen2 수집에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 데이터 수집 개요](./concepts-ingestion-overview.md)

Azure Time Series Insights Gen2 스토리지에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 데이터 스토리지](./concepts-storage.md)

시계열 모델에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 데이터 모델링](./concepts-model-overview.md)

Power BI에 환경 연결에 대한 자세한 정보:

> [!div class="nextstepaction"]
> [Power BI에서 Azure Time Series Insights Gen2의 데이터 시각화](./how-to-connect-power-bi.md)
