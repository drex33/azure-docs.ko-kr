---
title: Azure Portal IoT 커넥터 배포 - Azure Healthcare API
description: 이 문서에서는 Azure Portal IoT 커넥터를 배포하는 방법을 알아봅니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/13/2021
ms.author: jasteppe
ms.openlocfilehash: 064c904b33317d72adbef771353a98a947438252
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003215"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>Azure Portal IoT 커넥터 배포

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 빠른 시작에서는 Azure Portal IoT 커넥터를 배포하는 방법을 알아봅니다. IoT 커넥터를 구성하면 디바이스 메시지에 Azure Event Hub를 사용하여 IoT(사물 인터넷)에서 전자 의료 기록 교환(FHIR&#174;) 서비스로 데이터를 수집할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

Azure Healthcare API에서 IoT 커넥터 인스턴스를 만드는 단계를 시작하기 전에 다음 필수 구성을 완료해야 합니다.

* [Azure 계정](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [Azure Portal 배포된 리소스 그룹](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [Azure Portal 배포된 Event Hubs 네임스페이스 및 이벤트 허브](../../event-hubs/event-hubs-create.md)
* [Azure Healthcare API에 배포된 작업 영역](../workspace-overview.md)  
* [Azure Healthcare API에 배포된 FHIR 서비스](../fhir/fhir-portal-quickstart.md) 


## <a name="deploy-iot-connector"></a>IoT 커넥터 배포 

1. [Azure Portal](https://portal.azure.com)로그인한 다음 **검색** 창 필드에 Healthcare API 작업 영역 리소스 이름을 입력합니다.
 
   ![검색 표시줄 필드에 작업 영역 리소스 이름을 입력하는 스크린샷.](media/select-workspace-resource-group.png#lightbox)

2. **IoT 커넥터 블레이드를** 선택합니다.

   ![IoT 커넥터 블레이드의 스크린샷.](media/iot-connector-blade.png#lightbox)

3. 다음으로, **IoT 커넥터 추가를** 선택합니다.

   ![IoT 커넥터 추가 스크린샷.](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>데이터를 수집하도록 IoT 커넥터 구성

기본 **사항** 탭의 인스턴스 세부 **정보** 에서 필수 필드를 완료합니다.

![IoT 인스턴스 구성 세부 정보의 스크린샷.](media/basics-instance-details.png#lightbox)

1. **IoT 커넥터 이름 를** 입력합니다.

   **IoT 커넥터 이름은** IoT 커넥터의 이름입니다. IoT Connector 고유한 이름을 입력합니다. 예를 들어 이름을 로 지정할 수 `healthdemo-iot` 있습니다.

2. 이벤트 **허브 이름** 를 입력합니다.

   이벤트 허브 이름은 배포한 **Event Hubs 인스턴스의** 이름입니다. 

   Azure Event Hubs 대한 자세한 내용은 [빠른 시작: Azure Portal 사용하여 이벤트 허브 만들기를](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)참조하세요.

3. 소비자 그룹 를 **입력합니다.**

   소비자 그룹 이름은 **검색** 표시줄을 사용하여 배포한 Event Hubs 인스턴스로 이동하고  **소비자 그룹** 블레이드를 선택하여 찾습니다.

   ![소비자 그룹 이름의 스크린샷.](media/consumer-group-name.png#lightbox)

   소비자 그룹에 대한 자세한 내용은 [Azure Event Hubs 기능 및 용어를](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers)참조하세요.

4. **정규화된 네임스페이스의 이름을 입력합니다.**

    **정규화된 네임스페이스는** Event Hubs 네임스페이스의 **개요** 페이지에 있는 **호스트 이름입니다.**

    ![정규화된 네임스페이스의 스크린샷.](media/event-hub-hostname.png#lightbox)  

    Event Hubs 네임스페이스에 대한 자세한 내용은 Azure Event Hubs 문서의 기능 및 용어에서 [네임스페이스를](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) 참조하세요.

5. **다음: 디바이스 매핑을** 선택합니다. 
  
## <a name="configure-device-mapping-properties"></a>디바이스 매핑 속성 구성

> [!TIP]
> IoMT 커넥터 데이터 매퍼는 디바이스의 입력 데이터를 정규화하기 위한 매핑 구성을 시각화한 다음 FHIR 리소스로 변환하는 오픈 소스 도구입니다. 개발자는 이 도구를 사용하여 디바이스 및 FHIR 대상 매핑을 편집 및 테스트하고 데이터를 내보내 Azure Portal IoT 커넥터에 업로드할 수 있습니다. 또한 이 도구를 사용하면 개발자가 디바이스의 디바이스 및 FHIR 대상 매핑 구성을 이해할 수 있습니다.
>
> 자세한 내용은 오픈 소스 설명서를 참조하세요.
>
> [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [디바이스 콘텐츠 매핑](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. 디바이스 **매핑** 탭에서 IoT 커넥터와 연결된 디바이스 매핑 JSON 코드를 입력합니다.

   ![디바이스 매핑 구성의 스크린샷.](media/configure-device-mapping.png#lightbox)

2. **다음: 대상 >** 선택하여 IoT 커넥터와 연결된 대상 속성을 구성합니다.

## <a name="configure-fhir-destination-mapping-properties"></a>FHIR 대상 매핑 속성 구성

**대상** 탭에서 IoT 커넥터와 연결된 대상 속성을 입력합니다.

   ![대상 속성 구성의 스크린샷.](media/configure-destination-properties.png#lightbox)

1. **FHIR 서비스의** Azure 리소스 ID를 입력합니다.

   **FHIR 서버** 이름(FHIR **서비스라고도** 함)은 **검색** 표시줄을 사용하여 배포한 FHIR 서비스로 이동하고 **속성** 블레이드를 선택하여 찾습니다. **리소스 ID** 문자열을 복사하여 **FHIR 서버** 텍스트 필드에 붙여넣습니다.

    ![FHIR 서버 이름 입력의 스크린샷.](media/fhir-service-resource-id.png#lightbox) 

2. 대상 **이름** 을 입력합니다.

   **대상 이름은 대상의** 이름입니다. 대상의 고유한 이름을 입력합니다. 예를 들어 이름을 로 지정할 수 `iotmedicdevice` 있습니다.

3. **확인 유형** 에 대해 **만들기** 또는 **조회를** 선택합니다.

    > [!NOTE]
    > IoT 커넥터 대상이 FHIR 서비스에 유효한 관찰 리소스를 만들려면 FHIR 서버에 디바이스 리소스와 환자 리소스가 **있어야 합니다.** 따라서 관찰에서 데이터를 만든 디바이스와 데이터를 측정한 환자를 제대로 참조할 수 있습니다. IoT 커넥터가 디바이스 및 환자 리소스를 해결하는 데 사용할 수 있는 두 가지 모드가 있습니다.

   **만들기**

     IoT 커넥터 대상은 이벤트 허브 메시지에 포함된 디바이스 식별자를 사용하여 FHIR 서버에서 디바이스 리소스를 검색하려고 합니다. 또한 이벤트 허브 메시지에 포함된 환자 식별자를 사용하여 FHIR 서버에서 환자 리소스를 검색하려고 시도합니다. 리소스를 찾을 수 없는 경우 이벤트 허브 메시지에 포함된 식별자만 포함하는 새 리소스(디바이스, 환자 또는 둘 다)가 만들어집니다. **만들기** 옵션을 사용하는 경우 디바이스 매핑에서 디바이스 식별자와 환자 식별자를 모두 구성할 수 있습니다. 즉, IoT Connector 대상이 **만들기** 모드인 경우 FHIR 서버에 디바이스 및 환자 리소스를 **추가하지 않고** 정상적으로 작동할 수 있습니다.

   **조회**

     IoT 커넥터 대상은 이벤트 허브 메시지에 포함된 디바이스 식별자를 사용하여 FHIR 서버에서 디바이스 리소스를 검색하려고 합니다. 디바이스 리소스를 찾을 수 없으면 오류가 발생하며 데이터가 처리되지 않습니다. **조회가** 제대로 작동하려면 이벤트 허브 메시지에 포함된 디바이스 식별자와 일치하는 식별자가 있는 디바이스 리소스가 **있어야** 하며, 디바이스 리소스에도 존재하는 환자 리소스에 대한 참조가 **있어야 합니다.** 즉, IoT 커넥터 대상이 조회 모드인 경우 데이터를 처리하려면 먼저 디바이스 및 환자 리소스를 FHIR 서버에 **추가해야 합니다.**

   자세한 내용은 오픈 소스 설명서 [FHIR 대상 매핑을 참조하세요.](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping)

4. **대상 매핑** 아래에서 코드 편집기 내에 JSON 코드를 입력합니다.

   매퍼 도구에 대한 자세한 내용은 [IoMT 커넥터 데이터 매퍼 도구를 참조하세요.](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)

5. **검토 + 만들기를** 선택하거나 태그를 구성하려는 경우 **다음: 태그 >** 선택할 수 있습니다.  

## <a name="optional-configure-tags"></a>(선택 사항) 태그 구성

태그는 리소스를 분류하는 데 사용되는 이름 및 값 쌍입니다. 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성을 참조하세요.](../../azure-resource-manager/management/tag-resources.md)

**태그** 탭에서 IoT 커넥터와 연결된 태그 속성을 입력합니다.

   ![태그 속성의 스크린샷.](media/tag-properties.png#lightbox)
 
1. **이름** 을 입력합니다.
2. 값 을 **입력합니다.**
3. **검토 + 만들기** 를 선택합니다.

   아래 이미지에 표시된 것과 같은 **유효성 검사 성공** 메시지가 표시됩니다. 

   ![유효성 검사 성공 메시지의 스크린샷.](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > IoT 커넥터가 유효성을 검사하지 않은 경우 유효성 검사 실패 메시지를 검토하고 문제를 해결합니다. 구성한 각 IoT 커넥터 탭에서 속성을 검토하는 것이 좋습니다.

4. 다음 **만들기** 를 선택합니다.

   새로 배포된 IoT 커넥터가 Azure 리소스 그룹 내에 표시됩니다.

   ![Azure 최근 리소스 목록에 나열된 배포된 IoT 커넥터의 스크린샷.](media/azure-resources-iot-connector-deployed.png#lightbox)  

    이제 IoT 커넥터가 배포되었으므로 Event Hub 및 FHIR 서비스에 액세스할 수 있는 권한을 할당하는 단계를 안내합니다. 

## <a name="granting-iot-connector-access"></a>IoT 커넥터 액세스 권한 부여

IoT 커넥터가 제대로 작동하려면 Event Hub 및 FHIR 서비스에 대한 액세스 권한을 부여해야 합니다. 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>이벤트 허브에서 IoT 커넥터에 액세스

1. Azure **리소스 그룹** 목록에서 **Event Hubs 네임스페이스의** 이름을 선택합니다.

2. 액세스 **제어(IAM)** 블레이드를 선택한 **다음, + 추가를** 선택합니다.   

   ![Event Hubs 네임 스페이스에 대 한 액세스 제어의 스크린샷](media/access-control-blade-add.png#lightbox)

3. **역할 할당 추가** 를 선택합니다.

   ![역할 할당 추가의 스크린샷](media/event-hub-add-role-assignment.png#lightbox)
 
4. **역할** 을 선택한 다음 **Azure Event Hubs 데이터 수신기** 를 선택 합니다.

   ![역할 할당 필수 필드 추가의 스크린샷](media/event-hub-add-role-assignment-fields.png#lightbox)

   Azure Event Hubs 데이터 수신기 역할은이 역할을 할당 하는 IoT 커넥터에서이 이벤트 허브의 데이터를 받을 수 있도록 허용 합니다.

   응용 프로그램 역할에 대 한 자세한 내용은 [의료 api (미리 보기)에 대 한 인증 & 권한 부여](.././authentication-authorization.md)를 참조 하세요.

5. 에 대 한 **액세스 할당** 을 선택 하 고 기본 옵션인 선택 된 **사용자, 그룹 또는 서비스 주체** 를 그대로 유지 합니다.

6. **선택** 필드에서 IoT 커넥터에 대 한 보안 주체를 입력 합니다.  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   IoT 커넥터를 배포 하면 관리 id가 만들어집니다. 관리 되는 식별 이름은 작업 영역 이름, 리소스 유형 (IoT 커넥터) 및 IoT 커넥터의 이름에 대 한 연결입니다.

7. **저장** 을 선택합니다.

   역할 할당이 이벤트 허브에 성공적으로 추가 된 후 알림이 "역할 할당 추가" 라는 텍스트와 함께 녹색 확인 표시를 표시 합니다.  이 메시지는 IoT 커넥터가 이제 이벤트 허브에서 읽을 수 있음을 나타냅니다.

   ![추가 된 역할 할당 메시지의 스크린샷](media/event-hub-added-role-assignment.png#lightbox)

Event Hubs 리소스에 대 한 액세스를 제작 하는 방법에 대 한 자세한 내용은 [Azure Active Directory로 액세스 권한 부여](../../event-hubs/authorize-access-azure-active-directory.md)를 참조 하세요.  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>FHIR 서비스에서 IoT 커넥터 액세스

1. **Azure 리소스 그룹 목록** 에서 **fhir 서비스** 의 이름을 선택 합니다.
 
2. **액세스 제어 (IAM)** 블레이드를 선택 하 고 **+ 추가** 를 선택 합니다. 

3. **역할 할당 추가** 를 선택합니다.

  ![FHIR 서비스에 대 한 역할 할당 추가의 스크린샷](media/fhir-service-add-role-assignment.png#lightbox)

4. **역할** 을 선택한 다음, **Fhir 데이터 기록기** 를 선택 합니다.

   FHIR 데이터 기록기 역할은 IoT 커넥터가 작동 하기 위해 사용 하는 읽기 및 쓰기 권한을 제공 합니다. IoT 커넥터가 별도의 리소스로 배포 되기 때문에 FHIR 서비스는 IoT 커넥터에서 요청을 받습니다. FHIR 서비스에서 요청을 수행 하는 사용자를 모르는 경우 또는 할당 된 역할이 없는 경우 요청을 권한 없음으로 거부 합니다.

   응용 프로그램 역할에 대 한 자세한 내용은 [의료 api (미리 보기)에 대 한 인증 & 권한 부여](.././authentication-authorization.md)를 참조 하세요.

5. **선택** 필드에서 IoT 커넥터에 대 한 보안 주체를 입력 합니다.  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. **저장** 을 선택합니다.

   ![역할 할당 메시지가 추가 된 FHIR 서비스의 스크린샷](media/fhir-service-added-role-assignment.png#lightbox)

   FHIR 서비스에 역할을 할당 하는 방법에 대 한 자세한 내용은 [fhir 서비스에 대 한 AZURE RBAC 구성](../fhir/configure-azure-rbac-for-fhir.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Portal에 IoT 커넥터를 배포 하는 방법을 알아보았습니다. IoT 커넥터에 대 한 개요는를 참조 하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 개요](iot-connector-overview.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.