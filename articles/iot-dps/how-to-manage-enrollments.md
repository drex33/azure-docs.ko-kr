---
title: Azure Portal에서 Azure IoT Hub 디바이스 프로비전 서비스에 대한 디바이스 등록 관리
description: Azure Portal에서 디바이스 프로비전 서비스(DPS)에 대한 디바이스 등록을 관리하는 방법
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/25/2021
ms.topic: how-to
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: 7f0fdcf8a3e51405861082b3c2ad8a6385f5a6e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843395"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Azure Portal을 사용하여 디바이스 등록을 관리하는 방법

*장치 등록* 은 특정 시점에 Azure IoT 허브 장치 프로 비전 서비스 (DPS)에 등록할 수 있는 단일 장치 또는 장치 그룹의 레코드를 만듭니다. 등록 레코드에는 해당 등록의 일부로 해당 디바이스에 대한 초기 구성을 포함합니다. 구성에 포함 된 장치는 장치를 할당할 IoT 허브 또는 허브 집합에서 허브를 구성 하는 할당 정책입니다. 이 문서에서는 프로비전 서비스에 대한 디바이스 등록을 관리하는 방법을 보여줍니다.

Azure IoT Device Provisioning 서비스는 다음과 같은 두 가지 등록을 지원합니다.

* [등록 그룹](concepts-service.md#enrollment-group): 여러 관련 디바이스를 등록하는 데 사용됩니다.
* [개별 등록](concepts-service.md#individual-enrollment): 단일 디바이스를 등록하는 데 사용됩니다.

## <a name="create-an-enrollment-group"></a>등록 그룹 만들기

 등록 그룹은 일반적인 증명 메커니즘을 공유 하는 장치 그룹에 대 한 항목입니다. 초기 구성을 공유 하는 많은 수의 장치 또는 동일한 테 넌 트로 이동 하는 장치의 경우 등록 그룹을 사용 하는 것이 좋습니다. [대칭 키](concepts-symmetric-key-attestation.md) 또는 [x.509 인증서](concepts-x509-attestation.md) 증명을 사용 하는 장치는 지원 됩니다.

### <a name="create-a-symmetric-key-enrollment-group"></a>대칭 키 등록 그룹 만들기

대칭 키를 사용 하 여 등록 그룹을 만들고 사용 하려면 [대칭 키를 사용 하 여 장치 프로 비전](how-to-legacy-device-symm-key.md) 자습서를 참조 하세요.

대칭 키 등록 그룹을 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 장치를 등록 하려는 장치 프로 비전 서비스를 선택 합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. 페이지 맨 위에서 **+ 등록 그룹 추가** 를 선택 합니다.

6. **등록 그룹 추가** 페이지에서 다음 정보를 입력 합니다.

   | 필드 | 설명 |
    | :--- | :--- |
    | **그룹 이름** | 장치 그룹의 이름입니다.|
    | **증명 유형** |**대칭 키** 를 선택 합니다.|
    | **키 자동 생성** |이 확인란을 선택합니다.|
    | **허브에 디바이스를 할당할 방법 선택** |특정 허브에 할당할 수 있도록 *정적 구성을* 선택 합니다.|
    | **이 그룹을 할당할 수 있는 IoT 허브 선택** |허브 중 하나를 선택합니다.|

    나머지 필드는 기본값으로 둡니다.

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-symm-key.png" alt-text="대칭 키 증명에 대 한 등록 그룹을 추가 합니다.":::

7. **저장** 을 선택합니다.

### <a name="create-a-x509-certificate-enrollment-group"></a>X.509 인증서 등록 그룹 만들기

X.509 인증서 등록 그룹을 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 장치를 등록 하려는 장치 프로 비전 서비스를 선택 합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. 페이지 맨 위에서 **+ 등록 그룹 추가** 를 선택 합니다.

6. **등록 그룹 추가** 페이지에서 다음 정보를 입력 합니다.

    | 필드 | 설명 |
    | :--- | :--- |
    | **그룹 이름** | 장치 그룹의 이름입니다.|
    | **증명 유형** |**인증서** 를 선택합니다.|
    | **인증서 종류** | 장치 인증서에 서명 된 인증서를 기반으로 **CA 인증서** 또는 **중간** 을 선택 합니다.|
    | **기본 인증서**| CA 인증서를 사용 하 여 장치 인증서에 서명 하는 경우 루트 CA 인증서는 [소유 증명을 소유](how-to-verify-certificates.md) 해야 합니다. 중간 인증서를 사용하여 디바이스 인증서에 서명하는 경우, 중간 인증서를 업로드하는 데 사용할 수 있는 업로드 단추를 사용할 수 있습니다. 중간에 서명 된 인증서에도 [소유](how-to-verify-certificates.md) 증명이 있어야 합니다.

    나머지 필드는 기본값으로 둡니다.

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-cert.png" alt-text="CA 인증서 증명에 대 한 등록 그룹을 추가 합니다.":::

7. **저장** 을 선택합니다.

## <a name="create-an-individual-enrollment"></a>개별 등록 만들기

개별 등록은 IoT hub에 할당 될 수 있는 단일 장치에 대 한 항목입니다. [대칭 키](concepts-symmetric-key-attestation.md), [X.509 클라이언트 인증서](concepts-x509-attestation.md)및 [TPM 증명](concepts-tpm-attestation.md)을 사용하는 디바이스가 지원됩니다.

### <a name="create-a-symmetric-key-individual-enrollment"></a>대칭 키 개별 등록 만들기

>[!TIP]
>대칭 키로 개별 등록을 만들고 사용 하는 방법에 대 한 자세한 지침은 [빠른 시작: 시뮬레이션 된 대칭 키 장치 프로 비전](quick-create-simulated-device-symm-key.md#create-a-device-enrollment)을 참조 하세요.

대칭 키 개별 등록을 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 장치를 등록 하려는 장치 프로 비전 서비스를 선택 합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. 페이지 위쪽에서 **+ 개별 등록 추가** 를 선택합니다.

6. **등록 추가** 패널에서 다음 정보를 입력합니다.

    | 필드 | 설명 |
    | :--- | :--- |
    | **메커니즘** | *대칭 키* 선택 |
    | **키 자동 생성** |이 확인란을 선택합니다. |
    | **등록 ID** | 고유한 등록 ID를 입력 합니다.|
    | **IoT Hub 디바이스 ID** |  이 ID는 디바이스를 나타냅니다. 따라서 디바이스 ID에 대한 규칙을 따라야 합니다. 자세한 내용은 [장치 id 속성] (을 참조 하세요. /iot-hub/iot-hub-devguide-identity-registry. 장치 ID가 지정 되지 않은 상태로 남아 있으면 등록 ID가 사용 됩니다.|
    | **허브에 디바이스를 할당할 방법 선택** |특정 허브에 할당할 수 있도록 *정적 구성을* 선택 합니다.|
    | **이 그룹을 할당할 수 있는 IoT 허브 선택** |허브 중 하나를 선택합니다.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-symm-key.png" alt-text="대칭 키 증명에 대 한 개별 등록을 추가 합니다.":::

7. **저장** 을 선택합니다.

### <a name="create-a-x509-certificate-individual-enrollment"></a>X.509 인증서 개별 등록 만들기

>[!TIP]
>X.509 인증서를 사용 하 여 개별 등록을 만들고 사용 하는 방법에 대 한 자세한 지침은 [빠른 시작: x.509 인증서 장치 프로 비전](quick-create-simulated-device-x509.md#create-a-device-enrollment)을 참조 하세요.

X.509 인증서 개별 등록을 만들려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 장치를 등록 하려는 장치 프로 비전 서비스를 선택 합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. 페이지 위쪽에서 **+ 개별 등록 추가** 를 선택합니다.

6. **등록 추가** 패널에서 다음 정보를 입력합니다.

    | 필드 | 설명 |
    | :--- | :--- |
    | **메커니즘** | *X.509* 선택 |
    | **기본 인증서 .pem 또는 .cer 파일** | 리프 인증서를 생성할 수 있는 인증서를 업로드. .cer 파일을 선택하는 경우 base-64로 인코딩된 인증서만 허용됩니다. |
    | **IoT Hub 디바이스 ID** |  이 ID는 디바이스를 나타냅니다. 따라서 디바이스 ID에 대한 규칙을 따라야 합니다. 자세한 내용은 [디바이스 ID 속성](.을 참조하세요. /iot-hub/iot-hub-devguide-identity-registry. 디바이스 ID는 등록을 위해 업로드하는 디바이스 인증서의 주체 이름이어야 합니다. 해당 주체 이름은 디바이스 ID에 대한 규칙을 따라야 합니다.|
    | **허브에 디바이스를 할당할 방법 선택** |특정 허브에 할당할 수 있도록 *정적 구성을* 선택합니다.|
    | **이 그룹을 할당할 수 있는 IoT 허브 선택** |허브 중 하나를 선택합니다.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-cert.png" alt-text="X.509 인증서 증명에 대한 개별 등록을 추가합니다.":::

7. **저장** 을 선택합니다.

### <a name="create-a-tpm-individual-enrollment"></a>TPM 개별 등록 만들기

>[!TIP]
>TPM 증명을 사용하여 개별 등록을 만들고 사용하는 방법에 대한 자세한 지침은 [시뮬레이션된 TPM 디바이스 프로비전](quick-create-simulated-device-tpm.md#create-a-device-enrollment-entry) 샘플 중 하나를 참조하세요.

TPM 개별 등록을 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 디바이스를 등록할 Device Provisioning Service를 선택합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. 페이지 위쪽에서 **+ 개별 등록 추가** 를 선택합니다.

6. **등록 추가** 패널에서 다음 정보를 입력합니다.

    | 필드 | 설명 |
    | :--- | :--- |
    | **메커니즘** | *TPM* 선택 |
    | **인증 키** | TPM 디바이스의 고유 인증 키입니다. |
    | **등록 ID** | 고유한 등록 ID를 입력합니다.|
    | **IoT Hub 디바이스 ID** |  이 ID는 디바이스를 나타냅니다. 따라서 디바이스 ID에 대한 규칙을 따라야 합니다. 자세한 내용은 [디바이스 ID 속성](.을 참조하세요. /iot-hub/iot-hub-devguide-identity-registry. 디바이스 ID를 지정하지 않은 상태로 두면 등록 ID가 사용됩니다.|
    | **허브에 디바이스를 할당할 방법 선택** |특정 허브에 할당할 수 있도록 *정적 구성을* 선택합니다.|
    | **이 그룹을 할당할 수 있는 IoT 허브 선택** |허브 중 하나를 선택합니다.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-tpm.png" alt-text="TPM 증명에 대한 개별 등록을 추가합니다.":::

7. **저장** 을 선택합니다.

## <a name="update-an-enrollment-entry"></a>등록 항목 업데이트

기존 등록 항목을 업데이트하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 디바이스를 등록할 Device Provisioning Service를 선택합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. 수정하려는 등록 항목을 선택합니다.

6. 등록 항목 세부 정보 페이지에서 보안 유형 및 자격 증명을 제외한 모든 항목을 업데이트할 수 있습니다.

7. 완료되면 **저장을** 선택합니다.

## <a name="remove-a-device-enrollment"></a>디바이스 등록 제거

등록 항목을 제거하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.

3. 디바이스를 등록할 Device Provisioning Service를 선택합니다.

4. **설정** 메뉴에서 **등록 관리** 를 선택합니다.

5. 제거하려는 등록 항목을 선택합니다. 

6. 페이지 위쪽에서 **삭제** 를 선택합니다.

7. 확인하라는 프롬프트가 나타나면 **예** 를 선택합니다.

8. 작업이 완료되면 디바이스 등록 목록에서 항목 hav가 제거된 것을 볼 수 있습니다.
