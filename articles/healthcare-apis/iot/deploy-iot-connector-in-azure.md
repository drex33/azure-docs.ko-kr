---
title: Azure Portal에서 IoT 커넥터 배포-Azure 의료 Api
description: 이 문서에서는 Azure Portal에 IoT 커넥터를 배포 하는 방법을 알아봅니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: 5d55af3165991c8a6d16596ae9d4e8f9f4e8e3e9
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132812355"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>Azure Portal에서 IoT 커넥터 배포

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 빠른 시작에서는 Azure Portal IoT connector를 배포 하는 방법을 알아봅니다. IoT 커넥터를 구성 하면 장치 메시지에 대 한 Azure Event Hub를 사용 하 여 사물 인터넷 (IoT)에서 빠른 의료 (의료 상호 운용성&#174; 리소스) 서비스로 데이터를 수집할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 의료 Api에서 IoT 커넥터 인스턴스를 만드는 단계를 시작 하기 전에 다음과 같은 필수 구성 요소를 완료 해야 합니다.

* [Azure 계정](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [Azure Portal에 배포 된 리소스 그룹](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [Azure Portal에 배포 된 Event Hubs 네임 스페이스 및 이벤트 허브](../../event-hubs/event-hubs-create.md)
* [Azure 의료 Api에 배포 된 작업 영역](../healthcare-apis-quickstart.md)  
* [Azure 의료 Api에 배포 된 FHIR 서비스](../fhir/fhir-portal-quickstart.md) 

## <a name="deploy-iot-connector"></a>IoT 커넥터 배포 

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 **검색** 창 필드에 의료 api 작업 영역 리소스 이름을 입력 합니다.
 
   ![검색 창 필드에 작업 영역 리소스 이름을 입력 하는 스크린샷](media/select-workspace-resource-group.png#lightbox)

2. **IoT 커넥터 배포** 를 선택 합니다.

   ![IoT 커넥터 블레이드의 스크린샷.](media/iot-connector-blade.png#lightbox)

3. 그런 다음, **IoT 커넥터 추가** 를 선택 합니다.

   ![IoT 커넥터 추가의 스크린샷](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>데이터를 수집 하도록 IoT 커넥터 구성

**기본** 정보 탭 아래에서 **인스턴스 세부 정보** 아래에 필수 필드를 입력 합니다.

![IoT 인스턴스 정보 구성의 스크린샷](media/basics-instance-details.png#lightbox)

1. **IoT 커넥터 이름을** 입력 합니다.

   Iot 커넥터 **이름은** iot 커넥터에 대 한 친숙 한 이름입니다. IoT Connector에 대 한 고유한 이름을 입력 합니다. 예를 들어 이름을로 지정할 수 있습니다 `healthdemo-iot` .

2. **이벤트 허브 이름을** 입력 합니다.

   이벤트 허브 이름은 배포한 **Event Hubs 인스턴스의** 이름입니다. 

   Azure Event Hubs에 대 한 자세한 내용은 [빠른 시작: Azure Portal를 사용 하 여 이벤트 허브 만들기](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)를 참조 하세요.

3. **소비자 그룹** 을 입력 합니다.

   소비자 그룹 이름은 **검색** 표시줄을 사용 하 여 배포 된 Event Hubs 인스턴스로 이동 하 고,  **소비자 그룹** 블레이드를 선택 하 여 찾을 수 있습니다.

   ![소비자 그룹 이름의 스크린샷.](media/consumer-group-name.png#lightbox)

   소비자 그룹에 대 한 자세한 내용은 [Azure Event Hubs의 기능 및 용어](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers)를 참조 하세요.

4. 정규화 된 **네임 스페이스** 의 이름을 입력 합니다.

    정규화 된 **네임 스페이스** 는 Event Hubs 네임 스페이스의 **개요** 페이지에 있는 **호스트 이름** 입니다.

    ![정규화 된 네임 스페이스의 스크린샷](media/event-hub-hostname.png#lightbox)  

    Event Hubs 네임 스페이스에 대 한 자세한 내용은 Azure Event Hubs 문서의 기능 및 용어에서 [네임 스페이스](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) 를 참조 하세요.

5. **다음: 장치 매핑** 을 선택 합니다. 
  
## <a name="configure-device-mapping-properties"></a>장치 매핑 속성 구성

> [!TIP]
> IoMT 커넥터 데이터 매퍼는 장치 입력 데이터를 정규화 하는 매핑 구성을 시각화 하는 오픈 소스 도구 이며,이를 FHIR 리소스로 변환 합니다. 개발자는이 도구를 사용 하 여 장치 및 FHIR 대상 매핑을 편집 및 테스트 하 고 Azure Portal의 IoT 커넥터에 업로드할 데이터를 내보낼 수 있습니다. 또한 개발자는이 도구를 사용 하 여 장치의 장치 및 FHIR 대상 매핑 구성을 이해할 수 있습니다.
>
> 자세한 내용은 오픈 소스 설명서를 참조 하세요.
>
> [IoMT 커넥터 데이터 매퍼](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [장치 콘텐츠 매핑](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. **장치 매핑** 탭에서 IoT 커넥터와 연결 된 장치 매핑 JSON 코드를 입력 합니다.

   ![장치 매핑 구성의 스크린샷](media/configure-device-mapping.png#lightbox)

2. **다음: 대상 >** 를 선택 하 여 IoT 커넥터와 연결 된 대상 속성을 구성 합니다.

## <a name="configure-fhir-destination-mapping-properties"></a>FHIR 대상 매핑 속성 구성

**대상** 탭에서 IoT 커넥터에 연결 된 대상 속성을 입력 합니다.

   ![대상 속성 구성의 스크린샷](media/configure-destination-properties.png#lightbox)

1. **Fhir 서비스** 의 AZURE 리소스 ID를 입력 합니다.

   Fhir **서버** 이름 ( **fhir 서비스** 라고도 함)은 **검색** 표시줄을 사용 하 여 배포 된 Fhir 서비스로 이동 하 고 **속성** 블레이드를 선택 하 여 찾을 수 있습니다. **리소스 ID** 문자열을 복사 하 여 **Fhir 서버** 텍스트 필드에 붙여넣습니다.

    ![FHIR 서버 이름 입력의 스크린샷](media/fhir-service-resource-id.png#lightbox) 

2. **대상 이름을** 입력 합니다.

   대상 **이름은** 대상의 이름입니다. 대상에 대 한 고유한 이름을 입력 합니다. 예를 들어 이름을로 지정할 수 있습니다 `iotmedicdevice` .

3. **확인 유형** 에 대해 **만들기** 또는 **조회** 를 선택 합니다.

    > [!NOTE]
    > IoT 커넥터 대상이 FHIR 서비스에서 유효한 관찰 리소스를 만들려면 FHIR 서버에 장치 리소스 및 환자 리소스가 **있어야** 합니다. 따라서 관찰은 데이터를 만든 장치와 데이터를 측정 한 환자를 제대로 참조할 수 있습니다. IoT 커넥터는 장치 및 환자 리소스를 확인 하는 데 사용할 수 있는 두 가지 모드가 있습니다.

   **만들기**

     IoT 커넥터 대상은 이벤트 허브 메시지에 포함 된 장치 식별자를 사용 하 여 FHIR 서버에서 장치 리소스를 검색 하려고 합니다. 또한 이벤트 허브 메시지에 포함 된 환자 식별자를 사용 하 여 FHIR 서버에서 환자 리소스를 검색 하려고 합니다. 두 리소스 중 하나를 찾을 수 없으면 이벤트 허브 메시지에 포함 된 식별자만 포함 하는 새 리소스 (장치, 환자 또는 둘 다)가 생성 됩니다. **만들기** 옵션을 사용 하는 경우 장치 매핑에서 장치 식별자와 환자 식별자를 모두 구성할 수 있습니다. 즉, IoT Connector 대상이 **만들기** 모드인 경우 fhir 서버에 장치 및 환자 리소스를 추가 **하지 않고** 정상적으로 작동할 수 있습니다.

   **조회**

     IoT 커넥터 대상은 이벤트 허브 메시지에 포함 된 장치 식별자를 사용 하 여 FHIR 서버에서 장치 리소스를 검색 하려고 합니다. 장치 리소스를 찾을 수 없는 경우 오류가 발생 하 고 데이터가 처리 되지 않습니다. **조회가** 제대로 작동 하려면 이벤트 허브 메시지에 포함 된 장치 식별자와 일치 하는 식별자를 가진 장치 리소스가 **있어야** 하 고, 장치 리소스에도 존재 하는 환자 리소스에 대 한 **참조가 있어야 합니다** . 즉, IoT 커넥터 대상이 조회 모드에 있을 때 데이터를 처리 하려면 먼저 FHIR 서버에 장치 및 환자 리소스를 추가 **해야** 합니다.

   자세한 내용은 오픈 [소스 설명서를 참조 하세요.](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping)

4. **대상 매핑** 아래에서 코드 편집기 내부에 JSON 코드를 입력 합니다.

   맵 편집기 도구에 대 한 자세한 내용은 [IoMT Connector Data Mapper tool](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)을 참조 하세요.

5. **검토 + 만들기** 를 선택 하거나 태그를 구성 하려는 경우 **다음: 태그 >** 를 선택할 수 있습니다.  

## <a name="optional-configure-tags"></a>필드 태그 구성

태그는 리소스를 분류 하는 데 사용 되는 이름/값 쌍입니다. 태그에 대 한 자세한 내용은 [태그를 사용 하 여 Azure 리소스 및 관리 계층 구조 구성](../../azure-resource-manager/management/tag-resources.md)을 참조 하세요.

**태그** 탭에서 IoT 커넥터와 연결 된 태그 속성을 입력 합니다.

   ![태그 속성의 스크린샷](media/tag-properties.png#lightbox)
 
1. **이름** 을 입력합니다.
2. **값** 을 입력 합니다.
3. **검토 + 만들기** 를 선택합니다.

   아래 이미지에 표시 된 것과 같은 **유효성 검사 성공** 메시지가 표시 됩니다. 

   ![유효성 검사 성공 메시지의 스크린샷](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > IoT 커넥터의 유효성을 검사 하지 않은 경우 유효성 검사 오류 메시지를 검토 하 고 문제를 해결 합니다. 구성한 각 IoT 커넥터 탭의 속성을 검토 하는 것이 좋습니다.

4. 다음 **만들기** 를 선택합니다.

   새로 배포 된 IoT 커넥터가 Azure 리소스 그룹 페이지 내에 표시 됩니다.

   ![Azure 최근 리소스 목록에 나열 된 배포 된 IoT 커넥터의 스크린샷](media/azure-resources-iot-connector-deployed.png#lightbox)  

    이제 IoT 커넥터가 배포 되었으므로 이벤트 허브 및 FHIR 서비스에 액세스할 수 있는 권한을 할당 하는 단계를 안내 하겠습니다. 

## <a name="granting-iot-connector-access"></a>IoT 커넥터 액세스 권한 부여

IoT 커넥터가 제대로 작동 하도록 하려면 이벤트 허브 및 FHIR 서비스에 대 한 액세스 권한을 부여 받아야 합니다. 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>이벤트 허브에서 IoT 커넥터 액세스

1. **Azure 리소스 그룹** 목록에서 **Event Hubs 네임 스페이스** 의 이름을 선택 합니다.

2. **액세스 제어 (IAM)** 블레이드를 선택 하 고 **+ 추가** 를 선택 합니다.   

   ![Event Hubs 네임스페이스의 액세스 제어 스크린샷](media/access-control-blade-add.png#lightbox)

3. **역할 할당 추가** 를 선택합니다.

   ![역할 할당 추가 스크린샷.](media/event-hub-add-role-assignment.png#lightbox)
 
4. **역할을** 선택한 다음, **Azure Event Hubs Data Receiver** 를 선택합니다.

   ![역할 할당 필수 필드 추가 스크린샷.](media/event-hub-add-role-assignment-fields.png#lightbox)

   Azure Event Hubs 데이터 수신기 역할을 사용하면 이 역할이 할당되는 IoT 커넥터가 이 Event Hub에서 데이터를 받을 수 있습니다.

   애플리케이션 역할에 대한 자세한 내용은 [의료 API에 대한 인증 & 권한 부여(미리 보기)를 참조하세요.](.././authentication-authorization.md)

5. **에 대한 액세스 할당을** 선택하고 기본 **옵션인 사용자, 그룹 또는 서비스 주체** 를 유지합니다.

6. **선택** 필드에 IoT 커넥터의 보안 주체를 입력합니다.  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   IoT 커넥터를 배포하면 관리 ID가 만들어집니다. 관리되는 식별 이름은 작업 영역 이름, 리소스 종류(IoT 커넥터) 및 IoT 커넥터의 이름을 연결한 것입니다.

7. **저장** 을 선택합니다.

   역할 할당이 이벤트 허브에 성공적으로 추가되면 알림에 "역할 할당 추가" 텍스트와 함께 녹색 확인 표시가 표시됩니다.  이 메시지는 이제 IoT 커넥터가 이벤트 허브에서 읽을 수 있음을 나타냅니다.

   ![추가된 역할 할당 메시지의 스크린샷.](media/event-hub-added-role-assignment.png#lightbox)

Event Hubs 리소스에 대한 액세스 권한 작성에 대한 자세한 내용은 Azure Active Directory [사용하여 액세스 권한 부여를](../../event-hubs/authorize-access-azure-active-directory.md)참조하세요.  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>FHIR 서비스에서 IoT 커넥터에 액세스

1. Azure **리소스 그룹 목록에서**  **FHIR 서비스의** 이름을 선택합니다.
 
2. 액세스 **제어(IAM)** 블레이드를 선택한 **다음, + 추가를** 선택합니다. 

3. **역할 할당 추가** 를 선택합니다.

  ![FHIR 서비스에 대한 역할 할당 추가 스크린샷](media/fhir-service-add-role-assignment.png#lightbox)

4. **역할** 를 선택한 다음 **FHIR 데이터 기록기** 를 선택합니다.

   FHIR 데이터 기록기 역할은 IoT 커넥터가 작동하는 데 사용하는 읽기 및 쓰기 액세스를 제공합니다. IoT 커넥터는 별도의 리소스로 배포되므로 FHIR 서비스는 IoT 커넥터에서 요청을 받습니다. FHIR 서비스가 요청을 하는 사람을 모를 경우 또는 할당된 역할이 없는 경우 요청을 권한 없는 것으로 거부합니다.

   애플리케이션 역할에 대한 자세한 내용은 [의료 API에 대한 인증 & 권한 부여(미리 보기)를 참조하세요.](.././authentication-authorization.md)

5. **선택** 필드에 IoT 커넥터의 보안 주체를 입력합니다.  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. **저장** 을 선택합니다.

   ![역할 할당 메시지가 추가된 FHIR 서비스의 스크린샷.](media/fhir-service-added-role-assignment.png#lightbox)

   FHIR 서비스에 역할을 할당하는 자세한 내용은 [Azure RBAC 구성을](.././configure-azure-rbac.md)참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Portal IoT 커넥터를 배포하는 방법을 배웠습니다. IoT 커넥터에 대한 개요는 다음을 참조하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 개요](iot-connector-overview.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
