---
title: Azure Portal에서 Azure IoT Hub 디바이스 프로비전 서비스에 대한 디바이스 등록 관리
description: Azure Portal에서 디바이스 프로비전 서비스(DPS)에 대한 디바이스 등록을 관리하는 방법
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: e5e9caea0ff8514c633f20f7baa7c501f0ac379d
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276343"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Azure Portal을 사용하여 디바이스 등록을 관리하는 방법

*디바이스 등록* 은 특정 시점에 Azure IoT Hub 디바이스 프로비전 서비스에 등록할 수 있는 단일 디바이스 또는 디바이스 그룹의 레코드를 만듭니다. 등록 레코드에는 해당 등록의 일부로 해당 디바이스에 대한 초기 구성을 포함합니다. 이 구성에는 디바이스를 할당할 IoT 허브 또는 허브 집합에서 허브를 구성하는 할당 정책이 포함됩니다. 이 문서에서는 프로비전 서비스에 대한 디바이스 등록을 관리하는 방법을 보여줍니다.


## <a name="create-a-device-enrollment"></a>디바이스 등록 만들기

프로비전 서비스를 사용하여 두 가지 방법으로 디바이스를 등록할 수 있습니다.

* **등록 그룹** 은 특정 증명 메커니즘을 공유하는 디바이스의 그룹 항목입니다. 원하는 초기 구성을 공유하는 다수의 디바이스 또는 동일한 테넌트로 이동하는 디바이스에 대한 등록 그룹을 사용하는 것이 좋습니다. [대칭 키](concepts-symmetric-key-attestation.md) 또는 [x.509 인증서](concepts-x509-attestation.md)를 사용하는 디바이스는 지원됩니다. 

    대칭 키를 사용하여 등록 그룹을 만들고 사용하는 방법에 대한 단계별 지침은 [대칭 키를 사용한 디바이스 프로비전](how-to-legacy-device-symm-key.md) 자습서를 참조하세요.

    다음 단계를 사용하여 포털에서 디바이스 그룹에 대한 등록 그룹을 만들 수 있습니다.

    1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스** 를 클릭합니다.  
    1. 리소스 목록에서 디바이스를 등록하려는 Device Provisioning Service를 클릭합니다.  
    1. 프로비전 서비스에서 **등록 관리** 를 클릭한 다음 위쪽에서 **등록 그룹 추가** 단추를 클릭합니다.  
     
        ![포털에서 등록 그룹](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. "등록 그룹 추가" 패널이 표시되면, 등록 목록 항목에 대한 정보를 입력하고 **저장** 을 클릭합니다.  
     
        [![포털을 사용하여 등록 그룹 추가](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | 필드 | 설명 |
        | :--- | :--- |
        | **그룹 이름** | 디바이스 그룹의 필수 이름입니다. |
        | **증명 유형** | 디바이스에서 사용하는 증명 방법에 따라 증명 형식에 대한 **인증서** 또는 **대칭 키** 를 클릭합니다. |
        | **인증서 종류** | 인증서 증명을 사용하는 경우 사용 가능합니다. 디바이스 인증서에 서명된 인증서를 기반으로 **CA 인증서** 또는 **중간** 을 선택합니다. |
        | **기본 인증서** | 루트 CA 인증서를 사용하여 디바이스 인증서에 서명하는 경우 루트 CA 인증서의 [소유 증명을 완료](how-to-verify-certificates.md)해야 합니다. 그런 다음 디바이스 그룹에 대한 **기본 인증서** 로 이를 선택할 수 있습니다.<br><br>중간 인증서를 사용하여 디바이스 인증서에 서명하는 경우, 중간 인증서를 업로드하는 데 사용할 수 있는 업로드 단추를 사용할 수 있습니다. 중간에 서명된 인증서에도 해당 인증서에 대한 [소유 증명](how-to-verify-certificates.md)이 있어야 합니다. |

        
    

* **개별 등록** 은 IoT Hub에 할당될 수 있는 단일 디바이스에 대한 항목입니다. [대칭 키](concepts-symmetric-key-attestation.md), [X.509 클라이언트 인증서](concepts-x509-attestation.md)및 [TPM 증명](concepts-tpm-attestation.md)을 사용하는 디바이스가 지원됩니다. 

    다음 단계를 사용하여 포털에서 개별 등록을 만들 수 있습니다.

    1. Azure Portal에 로그인하고 왼쪽 메뉴에서 **모든 리소스** 를 클릭합니다.
    1. 리소스 목록에서 디바이스를 등록하려는 Device Provisioning Service를 클릭합니다.
    1. 프로비전 서비스에서 **등록 관리** 를 클릭한 다음 위쪽에서 **개별 등록 추가** 단추를 클릭합니다.   

       [![포털의 개별 등록 추가](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. "등록 추가" 패널이 표시되면, 개별 등록 항목에 대한 정보를 입력하고 **저장** 을 클릭합니다. 
     
        [![포털의 개별 등록](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | 필드 | 설명 |
        | :--- | :--- |
        | **메커니즘** | 디바이스에서 사용할 증명 방법에 따라 증명 메커니즘에 대한 **X.509**, **TPM**, **대칭 키** 를 클릭합니다. |
        | 증명 설정 | 대칭 키 또는 X.509 인증서를 사용하여 개별 등록을 만들고 사용하는 방법에 대한 단계별 지침은 [빠른 시작:시뮬레이션된 대칭 키 디바이스 프로비전](quick-create-simulated-device-symm-key.md#create-a-device-enrollment) 또는 [빠른 시작:X.509 인증서 디바이스 프로비저닝을 참조하세요.](quick-create-simulated-device-x509.md#create-a-device-enrollment)<br><br>TPM 증명을 사용하여 개별 등록을 만들고 사용하는 방법에 대한 단계별 지침은 [시뮬레이션된 TPM 디바이스 프로비전](quick-create-simulated-device-tpm.md#create-a-device-enrollment-entry) 샘플 중 하나를 참조하세요.|
        | **IoT Hub 디바이스 ID** |  이 ID는 디바이스를 나타냅니다. 따라서 디바이스 ID에 대한 규칙을 따라야 합니다. 자세한 내용은 [디바이스 ID 속성](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)을 참조하세요.<br><br>X.509 클라이언트 인증서를 사용하는 경우, 이 텍스트는 등록을 위해 업로드하는 디바이스 인증서의 주체 이름이어야 합니다. 해당 주체 이름은 디바이스 ID에 대한 규칙을 따라야 합니다.|
            


## <a name="update-an-enrollment-entry"></a>등록 항목 업데이트
다음 단계를 사용하여 포털에서 기존 등록 항목을 업데이트할 수 있습니다.

1. Azure Portal Device Provisioning Service를 열고 등록 **관리를 클릭합니다.** 
1. 수정하려는 등록 항목으로 이동합니다. 디바이스 등록에 대한 요약 정보를 열 수 있는 항목을 클릭합니다. 
1. 이 페이지에서는 디바이스 ID뿐만 아니라 디바이스가 연결되어야 하는 IoT Hub와 같은 보안 형식 및 자격 증명 이외의 항목을 수정할 수 있습니다. 또한 초기 디바이스 쌍 상태를 수정할 수 있습니다. 
1. 완료되면 **저장** 을 클릭하여 디바이스 등록을 업데이트합니다. 

    ![포털에서 등록 업데이트](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>디바이스 등록 제거
디바이스가 IoT Hub에 사용자를 프로비전할 필요가 없는 경우 다음 단계를 사용하여 포털에서 관련된 등록 항목을 제거할 수 있습니다.

1. Azure Portal Device Provisioning Service를 열고 등록 **관리를 클릭합니다.** 
1. 제거하려는 등록 항목으로 이동하여 선택합니다. 
1. 맨 위에 있는 **삭제** 를 클릭한 후 확인하라는 메시지가 나타나면 **예** 를 선택합니다. 
1. 작업이 완료되면 디바이스 등록의 목록에서 제거된 항목이 표시됩니다. 
 
    ![포털에서 등록 제거](./media/how-to-manage-enrollments/remove-enrollment.png)