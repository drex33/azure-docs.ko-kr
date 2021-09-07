---
title: Azure Stack에서 Azure Stream Analytics 실행
description: Azure Stream Analytics Edge 작업을 만들고 Azure IoT Edge 런타임을 통해 Azure Stack Hub에 배포합니다.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 9cfb98e4d221ee0e8aae7f96ce241269bd56616d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566341"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Azure Stack에서 Azure Stream Analytics 실행

Azure Stream Analytics를 Azure IoT Edge 모듈로 Azure Stack Hub에서 실행할 수 있습니다. 모든 Azure Stack Hub 사용에서 확인되는 사용자 지정 URL을 허용하여 Azure Stack Hub 구독에서 실행되는 Azure Blob Storage, Azure Event Hubs 및 Azure IoT Hub와 상호 작용할 수 있도록 하는 구성이 IoT Edge 모듈에 추가되었습니다.

Azure Stack의 Stream Analytics를 사용하면 고유한 개인 자율 클라우드에서 스트림 처리용의 진정한 하이브리드 아키텍처를 구축할 수 있습니다. 일관된 Azure 서비스 온-프레미스를 사용하여 클라우드를 클라우드 네이티브 앱에 연결하거나 연결을 끊을 수 있습니다. 

이 문서에서는 IoT Hub 또는 Event Hubs에서 Azure Stack Hub 구독의 다른 이벤트 허브 또는 Blob Storage로 데이터를 스트리밍한 후 Stream Analytics를 사용하여 처리하는 방법을 보여 줍니다.

## <a name="set-up-environments"></a>환경 설정

Stream Analytics는 Azure Stack Hub의 하이브리드 서비스입니다. 이 모듈은 Azure에서 구성되었지만 Azure Stack Hub에서 실행할 수 있는 IoT Edge 모듈입니다.

Azure Stack Hub나 IoT Edge를 처음 사용하는 경우 다음 지침에 따라 환경을 설정하세요.

### <a name="prepare-the-azure-stack-hub-environment"></a>Azure Stack Hub 환경 준비

Azure Stack Hub 구독을 만듭니다. 자세한 내용은 [Azure Stack Hub 구독을 만드는 방법에 대한 자습서](/azure-stack/user/azure-stack-subscribe-services/)를 참조하세요.

사용자의 서버에서 Azure Stack Hub를 평가하려는 경우 ASDK(Azure Stack Development Kit)를 사용할 수 있습니다. ASDK에 대한 자세한 내용은 [ASDK 개요](/azure-stack/asdk/)를 참조하세요.

### <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

Azure Stack Hub에서 Stream Analytics를 실행하려면 디바이스에 IoT Edge 런타임이 있어야 하고 Azure Stack Hub에 대한 네트워크 연결이 있거나 Azure Stack Hub에서 실행되는 VM을 사용해야 합니다. IoT Edge 런타임을 사용하면 Stream Analytics Edge 작업을 Azure Stack Hub에서 실행되는 Azure Storage 및 Azure Event Hubs와 통합할 수 있습니다. 자세한 내용은 [IoT Edge의 Azure Stream Analytics](stream-analytics-edge.md)를 참조하세요. 

네트워크를 통해 Azure Stack Hub 리소스에 액세스하는 기능 외에도, IoT Edge 디바이스(또는 VM)는 Azure 퍼블릭 클라우드의 IoT Hub에 액세스하여 Stream Analytics 모듈을 구성할 수 있어야 합니다. 

다음 문서에서는 디바이스 또는 VM에서 IoT Edge 런타임을 설정하는 방법을 보여줍니다.

* [Windows에 Azure IoT Edge 런타임 설치](../iot-edge/how-to-install-iot-edge.md)
* [Debian 기반 Linux 시스템에서 Azure IoT Edge 런타임 설치](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Azure Stream Analytics Edge 작업 만들기

Stream Analytics Edge 작업은 IoT Edge 디바이스에 배포된 컨테이너에서 실행됩니다. 이 작업은 다음 두 부분으로 구성됩니다.
* 작업 정의를 담당하는 클라우드 부분: 사용자가 클라우드의 입력, 출력, 쿼리 및 기타 설정(예: 잘못된 이벤트)을 정의합니다.
* IoT 디바이스에서 실행되는 모듈입니다. Stream Analytics 엔진을 포함하며, 클라우드에서 작업 정의를 수신합니다.

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

IoT Edge 디바이스에서 실행되는 Stream Analytics 작업을 만들 때 디바이스에서 호출할 수 있는 방식으로 작업을 저장해야 합니다. 기존 Azure Storage 계정을 사용하거나 새로 만들 수 있습니다.
1. Azure Portal에서 **리소스 만들기** > **Storage** > **Storage 계정 - BLOB, 파일, 테이블, 큐** 로 이동합니다.
1. 다음 값을 입력하여 스토리지 계정을 만듭니다.

   | 필드 | 값 |
   | --- | --- |
   | Name | 스토리지 계정의 고유한 이름을 입력합니다. |
   | 위치 | 가까운 위치를 선택합니다.|
   | Subscription | IoT Hub와 동일한 구독을 선택합니다.|
   | 리소스 그룹 | [IoT Edge 빠른 시작](../iot-edge/quickstart.md) 및 자습서에서 만드는 모든 테스트 리소스에 동일한 리소스 그룹을 사용합니다. 예를 들어 **IoTEdgeResources** 를 사용합니다. |

1. 다른 필드는 기본값을 유지하고 **만들기** 를 선택합니다.


### <a name="create-a-new-job"></a>새 작업 만들기

1. Azure Portal에서 **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업** 으로 이동합니다.
1. 다음 값을 입력하여 스토리지 계정을 만듭니다.

   | 필드 | 값 |
   | --- | --- |
   | 작업 이름 | 작업의 이름을 입력합니다. **IoTEdgeJob** 을 예로 들 수 있습니다. |
   | Subscription | IoT Hub와 동일한 구독을 선택합니다.|
   | 리소스 그룹 | [IoT Edge 빠른 시작](../iot-edge/quickstart.md) 및 자습서에서 만드는 모든 테스트 리소스에 동일한 리소스 그룹을 사용합니다. 예를 들어 **IoTEdgeResources** 를 사용합니다. |
   | 위치 | 가까운 위치를 선택합니다. |
   | 호스팅 환경 | **Edge** 를 선택합니다. |

1. **만들기** 를 선택합니다.

### <a name="configure-your-job"></a>작업 구성

Azure Portal에서 Stream Analytics 작업을 만든 후에는 통과하는 데이터에 대해 실행되는 입력, 출력 및 쿼리를 사용하여 작업을 구성할 수 있습니다. IoT 허브 또는 Azure Stack Hub 구독에 있는 이벤트 허브의 입력을 수동으로 지정할 수 있습니다.

1. Azure Portal에서 Stream Analytics 작업으로 이동합니다.
1. **구성** 아래에서 **스토리지 계정 설정** 을 선택하고 이전 단계에서 만든 스토리지 계정을 선택합니다.
   > [!div class="mx-imgBorder"]
   > [ ![작업 스토리지 계정 설정을 보여주는 스크린샷.](media/on-azure-stack/storage-account-settings.png) ](media/on-azure-stack/storage-account-settings.png#lightbox)
1. **작업 토폴로지** 에서 **입력** > **스트림 입력 추가** 를 선택합니다.
1. 드롭다운 목록에서 **IoT Hub**, **이벤트 허브** 또는 **에지 허브** 를 선택합니다. 
1. 입력이 이벤트 허브이거나 Azure Stack Hub 구독의 IoT 허브인 경우 다음과 같이 정보를 수동으로 입력하세요.

   #### <a name="event-hub"></a>이벤트 허브

   | 필드 | 값 |
   | --- | --- |
   | 입력 별칭 | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
   | Service Bus 네임스페이스 | 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 네임스페이스도 만듭니다. *sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com* 을 예로 들 수 있습니다. |
   | 이벤트 허브 이름 | 입력으로 사용할 이벤트 허브의 이름입니다. |
   | 이벤트 허브 정책 이름 | 이벤트 허브에 대한 액세스를 제공하는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 허브 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다. |
   | 이벤트 허브 정책 키 | 이벤트 허브에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. 이벤트 허브 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다. 이벤트 허브 설정에서 찾을 수 있습니다. |
   | 이벤트 허브 소비자 그룹(선택 사항) | 각 Stream Analytics 작업마다 고유한 소비자 그룹을 사용합니다. 이 문자열은 이벤트 허브에서 데이터를 수집하는 데 사용할 소비자 그룹입니다. 소비자 그룹이 지정되지 않으면 Stream Analytics 작업에서 $Default 소비자 그룹을 사용합니다. |
   | 파티션 수 | 파티션 수는 이벤트 허브의 파티션 수입니다. |

   > [!div class="mx-imgBorder"]
   > [ ![이벤트 허브 입력을 보여주는 스크린샷.](media/on-azure-stack/event-hub-input.png) ](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT hub

   | 필드 | 값 |
   | --- | --- |
   | 입력 별칭 | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
   | IoT Hub | 입력으로 사용할 IoT Hub의 이름입니다. (예: *<IoT Hub Name>.shanghai.azurestack.corp.microsoft.com*) |
   | 공유 액세스 정책 이름 | IoT Hub에 대한 액세스를 제공하는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
   | 공유 액세스 정책 키 | IoT Hub에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. IoT Hub 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다. |
   | 소비자 그룹(선택 사항) | 각 Stream Analytics 작업마다 서로 다른 소비자 그룹을 사용합니다. 소비자 그룹은 IoT 허브에서 데이터를 수집하는 데 사용됩니다. Stream Analytics에서는 달리 지정하지 않는 한 $Default 소비자 그룹을 사용합니다. |
   | 파티션 수 | 파티션 수는 이벤트 허브의 파티션 수입니다. |

   > [!div class="mx-imgBorder"]
   > [ ![IoT Hub 입력을 보여주는 스크린샷.](media/on-azure-stack/iot-hub-input.png) ](media/on-azure-stack/iot-hub-input.png#lightbox)

1. 다른 필드의 기본값을 그대로 두고 **저장** 을 선택합니다.
1. **작업 토폴로지** 에서 **출력** 을 열고 **추가** 를 선택합니다.
1. 드롭다운 목록에서 **Blob Storage**, **이벤트 허브** 또는 **에지 허브** 를 선택합니다.
1. 출력이 이벤트 허브이거나 Azure Stack Hub 구독의 Blob Storage인 경우 다음과 같이 정보를 수동으로 입력하세요.

   #### <a name="event-hub"></a>이벤트 허브

   | 필드 | 값 |
   | --- | --- |
   | 출력 별칭 | 쿼리에서 쿼리 출력을 이 이벤트 허브로 보내는 데 사용되는 식별 이름입니다. |
   | Service Bus 네임스페이스 | 메시징 엔터티 세트에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 Service Bus 네임스페이스도 만들었습니다. *sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com* 을 예로 들 수 있습니다. |
   | 이벤트 허브 이름 | 이벤트 허브 출력의 이름입니다. |
   | 이벤트 허브 정책 이름 | 이벤트 허브의 **구성** 탭에서 만들 수 있는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
   | 이벤트 허브 정책 키 | 이벤트 허브 네임스페이스에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |

   > [!div class="mx-imgBorder"]
   > [ ![이벤트 허브 출력을 보여주는 스크린샷.](media/on-azure-stack/event-hub-output.png) ](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Storage 

   | 필드 | 값 |
   | --- | --- |
   | 출력 별칭 | 쿼리 출력을 이 Blob Storage로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
   | 스토리지 계정 | 출력을 보내는 스토리지 계정의 이름입니다. (예: *<Storage Account Name>.blob.shanghai.azurestack.corp.microsoft.com*). |
   | Storage 계정 키 | 스토리지 계정과 연결된 비밀 키입니다. Blob Storage 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다. |

> [!NOTE]
> Parquet 형식은 Azure Stack Hub의 에지 작업에 대해 지원되지 않습니다. **최소** 행 및 **최대** 시간에 대해 **0** 을 사용하거나 비워 둡니다.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Azure Stack에 연결된 VM 또는 디바이스에 Stream Analytics 배포

1. Azure Portal에서 IoT Hub를 엽니다. **IoT Edge** 로 이동하고, 이 배포의 대상으로 사용할 디바이스 또는 VM을 선택합니다.
1. **모듈 설정**  >  **+ 추가** 를 선택하고 **Azure Stream Analytics 모듈** 을 선택합니다. 
1. 구독 및 사용자가 만든 Steam Analytics Edge 작업을 선택합니다. **저장** 을 선택한 후 **다음:경로** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > [ ![모듈 추가를 보여주는 스크린샷.](media/on-azure-stack/edge-modules.png) ](media/on-azure-stack/edge-modules.png#lightbox)

1. **검토 + 만들기 >** 를 선택합니다.
1. **검토 + 만들기** 단계에서 **만들기** 를 선택합니다. 
   > [!div class="mx-imgBorder"]
   > [ ![매니페스트를 보여주는 스크린샷.](media/on-azure-stack/module-content.png) ](media/on-azure-stack/module-content.png#lightbox)
1. 모듈이 목록에 추가되었는지 확인합니다.
   > [!div class="mx-imgBorder"]
   > [ ![배포 페이지를 보여주는 스크린샷.](media/on-azure-stack/edge-deployment.png) ](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>다음 단계
- [IoT Edge의 Azure Stream Analytics](./stream-analytics-edge.md)
- [Stream Analytics Edge 작업 개발](/stream-analytics-query/stream-analytics-query-language-reference)
