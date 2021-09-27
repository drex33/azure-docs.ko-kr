---
title: Azure Data Box에서 데이터를 내보내기 위한 자습서 | Microsoft Docs
description: 배포 필수 구성 요소 및 Azure Data Box에서 데이터를 내보내는 방법 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 09/21/2021
ms.author: alkohli
ms.custom: contperf-fy22q1
ms.openlocfilehash: db5b98170446e93737fd625671f5351cc11da337
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591922"
---
# <a name="tutorial-create-export-order-for-azure-data-box"></a>자습서: Azure Data Box에 대한 내보내기 주문 만들기

Azure Data Box는 Azure에서 사용자의 위치로 데이터를 이동할 수 있는 하이브리드 솔루션입니다. 이 자습서에서는 Azure Data Box에 대한 내보내기 주문을 만드는 방법을 설명합니다. 내보내기 주문을 만드는 주요 이유는 온-프레미스 스토리지가 손상되고 백업을 복원해야 하는 경우 재해 복구를 위해서입니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 내보내기에 대한 필수 조건
> * Data Box에 대해 내보내기 주문
> * 내보내기 주문 추적
> * 내보내기 주문 취소

## <a name="prerequisites"></a>사전 요구 사항

디바이스를 주문하기 전에 Data Box 서비스 및 디바이스에 대해 다음 필수 구성 요소를 완료합니다.

### <a name="for-service"></a>서비스의 경우

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Azure Data Box에서 사용할 수 있는 기존 리소스 그룹이 있는지 확인합니다.

* 데이터를 내보내려는 Azure Storage 계정이 [Data Box에 대해 지원되는 스토리지 계정](data-box-system-requirements.md#supported-storage-accounts)에 설명된 대로 지원되는 스토리지 계정 유형 중 하나인지 확인합니다.

### <a name="for-device"></a>디바이스의 경우

시작하기 전에 다음 사항을 확인합니다.

* 호스트 컴퓨터를 데이터 센터 네트워크에 연결해야 합니다. Azure Data Box의 데이터를 이 컴퓨터로 복사합니다. [Azure Data Box 시스템 요구 사항](data-box-system-requirements.md)에 설명된 대로 호스트 컴퓨터는 지원되는 운영 체제를 실행해야 합니다.

* 데이터 센터에는 고속 네트워크가 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용할 수 있지만 복사 속도에 영향을 미칩니다.

## <a name="order-data-box-for-export"></a>Data Box에 대해 내보내기 주문

디바이스를 주문하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 다음 URL에서 로그인합니다. [https://portal.azure.com](https://portal.azure.com)

2. **+ 리소스 만들기** 를 선택하고 *Azure Data Box* 를 검색합니다. **Azure Data Box** 를 선택합니다.

   ![리소스 만들기](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-resource.png)

3. **만들기** 를 선택합니다.

   ![Azure Data Box 리소스 만들기](media/data-box-deploy-export-ordered/azure-data-box-export-order-create-data-box-resource.png)

4. 해당 지역에서 Azure Data Box 서비스를 사용할 수 있는지 확인합니다. 다음 정보를 입력하거나 선택하고 **적용** 을 클릭합니다.

    |설정  |값  |
    |---------|---------|
    |전송 형식     | **Azure로 내보내기** 를 선택합니다.        |
    |Subscription     | Data Box 서비스에 대한 EA, CSP 또는 Azure 스폰서쉽 구독을 선택합니다. <br> 구독은 대금 청구 계정에 연결됩니다.       |
    |Resource group     |    기존 리소스 그룹을 선택합니다. <br> 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다.         |
    |원본 Azure 지역    |    현재 데이터가 있는 Azure 지역을 선택합니다.         |
    |대상 국가     |     디바이스를 배송하려는 국가를 선택합니다.        |

   ![Data Box 설정 선택](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-box-settings.png)

5. **Data Box** 를 선택합니다. 단일 주문의 최대 사용 가능한 용량은 80TB입니다. 더 큰 데이터 크기에 대해 여러 개의 주문을 만들 수 있습니다.

   ![Data Box 용량 선택](media/data-box-deploy-export-ordered/azure-data-box-export-order-capacity.png)

6. **주문** 에서 **기본** 주문 세부 정보를 지정합니다. 다음 정보를 입력하거나 선택합니다.

    |설정  |값  |
    |---------|---------|
    |Subscription     | 이전에 선택한 내용에 따라 구독이 자동으로 채워집니다.|
    |Resource group | 이전에 선택한 리소스 그룹입니다. |
    |주문 이름 내보내기     |  주문을 추적하는 데 친숙한 이름을 입력합니다. <br> 이 이름은 2~24자 사이의 문자, 숫자 및 하이픈일 수 있습니다. <br> 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.      |

    ![내보내기 주문 기본 사항](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-order-name.png)

    **다음: 데이터 선택** 을 선택하여 계속 진행합니다.

7. **데이터 선택** 에서 **스토리지 계정 및 내보내기 형식 추가** 를 선택합니다.

    ![스토리지 계정 및 내보내기 형식 추가](media/data-box-deploy-export-ordered/azure-data-box-export-order-basics-add-storage.png)

8. **내보내기 옵션 선택** 에서 내보내기 옵션 세부 정보를 지정합니다. 다음 정보를 입력하거나 선택한 다음, **추가** 를 선택합니다.

    |설정  |값  |
    |---------|---------|
    |스토리지 계정     | 데이터를 내보낼 Azure Storage 계정입니다. |
    |내보내기 형식     | **모든 개체** 및 **XML 파일 사용** 에서 내보낼 데이터 형식을 지정합니다.<ul><li> **모든 개체** - **전송 옵션** 에 대한 선택 항목에 따라 작업에서 모든 데이터를 내보내도록 지정합니다.</li><li> **XML 파일 사용** – 스토리지 계정에서 내보낼 Blob 및/또는 파일의 경로 및 접두사 세트를 포함하는 XML 파일을 지정합니다. XML 파일은 선택한 스토리지 계정의 컨테이너에 있어야 하고, 파일 공유에서 선택하는 기능은 현재 지원되지 않습니다. 파일은 비어 있지 않은 .xml 파일이어야 합니다.</li></ul>        |
    |전송 옵션     |  **모두 선택**, **모든 Blob** 및 **모든 파일** 중에서 데이터 전송 옵션을 지정합니다. <ul><li> **모두 선택** - 모든 Blob 및 Azure Files을 내보내도록 지정합니다. Blob만 지원하는 스토리지 계정(Blob Storage 계정)을 사용하는 경우 **모든 파일** 옵션을 선택할 수 없습니다.</li><li> **모든 Blob** - 블록 및 페이지 Blob만 내보내도록 지정합니다.</li><li> **모든 파일** - Blob을 제외한 모든 파일을 내보내도록 지정합니다. 스토리지 계정 유형(GPv1 및 GPv2, 프리미엄 스토리지 또는 Blob Storage)은 내보낼 수 있는 데이터의 형식을 결정합니다. 자세한 내용은 [내보내기에 대해 지원되는 스토리지 계정](../import-export/storage-import-export-requirements.md#supported-storage-types)을 참조하세요.</li></ul>         |
    |자세한 정보 로그 포함     | 성공적으로 내보낸 모든 파일의 목록을 포함하는 자세한 로그 파일이 필요한지 여부를 나타냅니다. 내보내기 순서에 대 한 복사 로그 및 자세한 로그에 대 한 자세한 내용은 [로그 보기](data-box-export-logs.md#view-logs-during-data-copy)를 참조 하세요. |

    > [!NOTE]
    > **내보내기 유형** 설정에 **xml 파일 사용** 을 선택 하는 경우 xml 파일에 유효한 경로 및/또는 접두사가 포함 되어 있는지 확인 해야 합니다. XML 파일을 생성하고 제공해야 합니다. 파일이 잘못되었거나 지정된 경로와 일치하는 데이터가 없는 경우에는 주문이 부분 데이터로 종료되거나 데이터를 내보내지 않습니다. 지침은 [XML 파일 만들기](#create-xml-file)를 참조 하세요.

    컨테이너에 XML 파일을 추가하는 방법에 대한 자세한 내용은 [XML 파일을 사용하여 주문 내보내기](data-box-deploy-export-ordered.md#export-order-using-xml-file)를 참조하세요.

   ![내보내기 옵션 선택](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

   XML 입력의 예를 보려면 [xml 파일 만들기](#create-xml-file) 를 참조 하세요.

9. **데이터 선택** 에서 설정을 검토하고 **다음: 보안>** 을 선택하여 계속합니다.

   ![내보내기 주문, 데이터 선택](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-selection.png)

    **보안** 화면에서는 사용자 고유의 암호화 키를 사용하고 이중 암호화를 사용하도록 선택할 수 있습니다.

    **보안** 화면의 모든 설정은 선택 사항입니다. 설정을 변경하지 않으면 기본 설정이 적용됩니다.

    ![Data Box 가져오기 주문 마법사의 보안 화면](media/data-box-deploy-export-ordered/data-box-export-security-01.png)

10. 사용자 고유의 고객 관리형 키를 사용하여 새 리소스에 대한 잠금 해제 암호를 보호하려면 **암호화 유형** 을 펼칩니다.

    Azure Data Box에 대한 고객 관리형 키 구성은 선택 사항입니다. 기본적으로 Data Box는 Microsoft 관리형 키를 사용하여 잠금 해제 암호를 보호합니다.

    고객 관리형 키는 디바이스의 데이터가 암호화되는 방식에 영향을 주지 않습니다. 키는 디바이스 잠금 해제 암호를 암호화하는 데만 사용됩니다.

    고객 관리형 키를 사용하지 않으려면 16단계로 건너뜁니다.

    ![암호화 유형 설정을 보여 주는 보안 화면](./media/data-box-deploy-export-ordered/customer-managed-key-01.png)

11. 키 유형으로 **고객 관리형 키** 를 선택합니다. 그런 다음, **Key Vault 및 키 선택** 을 선택합니다.
   
    ![보안 화면, 고객 관리형 키 설정](./media/data-box-deploy-export-ordered/customer-managed-key-02.png)

12. **Azure Key Vault에서 키 선택** 화면에서 구독이 자동으로 채워집니다.

    - **키 자격 증명 모음** 의 드롭다운 목록에서 기존 키 자격 증명 모음을 선택할 수 있습니다.

      ![Azure Key Vault에서 키 선택 화면](./media/data-box-deploy-export-ordered/customer-managed-key-03.png)

    - **새로 만들기** 를 선택하여 새로운 키 자격 증명 모음을 만들 수도 있습니다. **키 자격 증명 모음 만들기** 화면에서 리소스 그룹 및 키 자격 증명 모음 이름을 입력합니다. **일시 삭제** 및 **보호 제거** 를 사용하도록 설정되어 있는지 확인합니다. 다른 모든 기본값을 적용하고, **검토 + 만들기** 를 선택합니다.

      ![새 Azure Key Vault 설정 만들기](./media/data-box-deploy-export-ordered/customer-managed-key-04.png)

      키 자격 증명 모음에 대한 정보를 검토하고, **만들기** 를 선택합니다. 키 자격 증명 모음 만들기가 완료될 때까지 몇 분 정도 기다립니다.

      ![새 Azure Key Vault 검토 화면](./media/data-box-deploy-export-ordered/customer-managed-key-05.png)

13. **Azure Key Vault에서 키 선택** 화면에서 Key Vault의 기존 키를 선택할 수 있습니다.

    ![Azure Key Vault에서 기존 키 선택](./media/data-box-deploy-export-ordered/customer-managed-key-06.png)

    새 키를 만들려면 **새로 만들기** 를 선택합니다. RSA 키를 사용해야 합니다. 크기는 2048 이상일 수 있습니다. 새 키에 대한 이름을 입력하고, 다른 기본값을 적용한 다음, **만들기** 를 선택합니다.

      ![새 키 만들기 옵션](./media/data-box-deploy-export-ordered/customer-managed-key-07.png)

      키가 키 자격 증명 모음에 만들어지면 알림이 표시됩니다.

14. 사용할 키의 **버전** 을 선택한 다음, **선택** 을 선택합니다.

      ![키 자격 증명 모음에 새로 생성된 키](./media/data-box-deploy-export-ordered/customer-managed-key-08.png)

    새 키 버전을 만들려면 **새로 만들기** 를 선택합니다.

    ![새 키 버전을 만들기 위한 대화 상자 열기](./media/data-box-deploy-export-ordered/customer-managed-key-08-a.png)

    **새 키 만들기** 화면에서 새 키 버전의 설정을 선택하고 **만들기** 를 선택합니다.

    ![새로운 키 버전 만들기](./media/data-box-deploy-export-ordered/customer-managed-key-08-b.png)

    **보안** 화면의 **암호화 유형** 설정에 키 자격 증명 모음 및 키가 표시됩니다.

    ![고객 관리형 키에 대한 키 및 키 자격 증명 모음](./media/data-box-deploy-export-ordered/customer-managed-key-09.png)

15. 이 리소스에 대한 액세스를 관리하는 데 사용할 사용자 ID를 선택합니다. **사용자 ID 선택** 을 선택합니다. 오른쪽 패널에서 사용할 구독 및 관리 ID를 선택합니다. 그런 다음, **선택** 을 선택합니다.

    사용자가 할당한 관리 ID는 여러 리소스를 관리하는 데 사용할 수 있는 독립 실행형 Azure 리소스입니다. 자세한 내용은 [관리 ID 유형](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요.  

    새 관리 ID를 만들어야 하는 경우 [Azure Portal을 사용하여 사용자가 할당한 관리 ID에 역할 만들기, 나열, 삭제 또는 할당](../../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)의 지침을 따릅니다.
    
    ![사용자 ID 선택](./media/data-box-deploy-export-ordered/customer-managed-key-10.png)

    사용자 ID는 **암호화 유형** 설정에 표시됩니다.

    이제 **암호화 유형** 설정을 축소할 수 있습니다.

    ![암호화 유형 설정에 표시된 선택한 사용자 ID](./media/data-box-deploy-export-ordered/customer-managed-key-11.png)

16. 소프트웨어 기반 이중 암호화를 사용하도록 설정하려면 **이중 암호화(매우 안전한 환경용)** 를 확장하고 **주문에 이중 암호화 사용** 을 선택합니다. 

    소프트웨어 기반 암호화는 Data Box 데이터의 AES-256비트 암호화와 함께 수행됩니다.

    > [!NOTE]
    > 이 옵션을 사용하도록 설정하면 주문 처리 및 데이터 복사 시간이 더 길어질 수 있습니다. 주문을 만든 후에는 이 옵션을 변경할 수 없습니다.

    ![데이터 상자 가져오기, 이중 암호화를 위한 보안 화면](media/data-box-deploy-export-ordered/azure-data-box-export-order-security-double-encryption.png)

    완료되면 **다음: 연락처 정보** 를 선택하여 계속 진행합니다.

11. **연락처 세부 정보** 에서 **+ 배송 주소 추가** 를 선택하여 배송 정보를 입력합니다.

    ![배송 주소 추가](media/data-box-deploy-export-ordered/azure-data-box-export-order-add-shipping-address.png)

12. **배송 주소 추가** 에 사용자의 성과 이름, 회사의 이름과 우편 주소 및 유효한 전화 번호를 입력합니다. **유효성 검사** 를 선택합니다. 서비스에서 서비스 가용성을 위해 배송 주소의 유효성을 검사합니다. 지정한 배송 주소에 대해 서비스를 사용할 수 있으면 해당 알림을 받게 됩니다.

    ![배송 주소 유효성 검사](media/data-box-deploy-export-ordered/azure-data-box-export-order-validate-shipping-address.png)

    자체 관리형 배송을 사용할 수 있는 지역에서 주문하는 경우 이 옵션을 선택할 수 있습니다. 자체 관리형 배송에 대한 자세한 내용은 [자체 관리형 배송 사용](data-box-portal-customer-managed-shipping.md)을 참조하세요.

13. 배송 정보가 성공적으로 확인되면 **배송 주소 추가** 을 선택합니다.

14. **연락처 세부 정보** 에서 배송 주소 및 메일 주소를 검토합니다. 서비스에서는 주문 상태에 대한 모든 업데이트와 관련된 이메일 알림을 지정한 이메일 주소로 보냅니다.

    그룹의 관리자가 떠나는 경우에도 계속 알림을 받으려면 그룹 이메일을 사용하는 것이 좋습니다.

    ![연락처 세부 정보](media/data-box-deploy-export-ordered/azure-data-box-export-order-contact-details.png)

15. **다음: 검토 + 주문>** 을 선택합니다. 주문 작성을 진행하려면 약관에 동의해야 합니다.

16. **주문** 을 선택합니다. 주문을 만드는 데 몇 분 정도 걸립니다.

    ![주문 커밋](media/data-box-deploy-export-ordered/azure-data-box-select-export-order-commit-order.png)

## <a name="export-order-using-xml-file"></a>XML 파일을 사용한 내보내기 주문

**XML 파일 사용** 을 선택하는 경우 내보낼 특정 컨테이너 및 Blob(페이지 및 블록)을 지정할 수 있습니다. 아래 단계에서는 XML 파일을 사용하여 데이터를 내보내는 방법을 보여 줍니다. XML 파일을 생성하려면 [XML 파일 만들기의](#create-xml-file)지침을 따르세요.

XML 파일을 사용하여 데이터를 내보내려면 다음을 수행합니다.

1. **내보내기 형식** 에서 **XML 파일 사용** 을 선택합니다. 내보내려는 특정 Blob 및 Azure 파일을 지정하는 XML 파일입니다. XML 파일을 추가하려면 **여기를 클릭하여 XML 파일 선택** 을 선택합니다.

     ![내보내기 옵션 선택, XML 선택](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-select-xml-option.png)

2. **+ 컨테이너** 를 선택하여 컨테이너를 만듭니다.

    ![내보내기 옵션 선택, 컨테이너](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-containers-option.png)

3. Azure Portal의 오른쪽에서 팝아웃되는 **새 컨테이너** 탭에서 컨테이너의 이름을 추가합니다. 이름은 소문자여야 하고 숫자 및 대시 '-'를 포함할 수 있습니다. 그런 다음, 드롭다운 목록 상자에서 **퍼블릭 액세스 수을** 을 선택합니다. 다른 사용자가 데이터에 액세스하지 못하도록 하려면 **프라이빗(비익명 액세스)** 을 선택하는 것이 좋습니다. 컨테이너 액세스 수준에 대한 자세한 내용은 [컨테이너 액세스 권한](../storage/blobs/anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)을 참조하세요.

   ![내보내기 옵션 선택, 새 컨테이너 설정](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-container-settings.png)

4. **만들기** 를 선택합니다.

   ![내보내기 옵션을 선택하고 새 컨테이너를 만듭니다.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-create-container.png)

   컨테이너가 성공적으로 생성되면 다음과 같은 메시지가 표시됩니다.

   ![컨테이너를 만들었습니다.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-container-success.png)

5. 만든 컨테이너를 선택하고 두 번 클릭합니다.

   ![컨테이너 세부 정보 보기](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-view-container-details.png)

6. 컨테이너를 두 번 클릭하면 컨테이너 속성 보기가 표시됩니다. 이제 내보낼 Blob 및/또는 Azure Files 목록이 포함된 XML 파일을 첨부(또는 해당 파일로 이동)하려고 합니다. **업로드** 를 선택합니다.

   ![컨테이너에 blob 업로드](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-blob-to-container.png)

7. 컨테이너에 XML 파일을 추가했습니다. 이 XML에서 지정한 Blob 및 Azure Files만 내보내집니다.

   ![컨테이너에 추가된 XML 파일](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-added-to-container.png)

## <a name="create-xml-file"></a>XML 파일 만들기

XML 파일을 사용 하 여 내보낼 blob 및 파일을 선택 하는 경우 다음 지침에 따라 XML 파일을 만듭니다.
- **샘플 XML 파일 탭:** 각 태그의 예제를 사용 하 여 샘플 XML 파일을 복사 합니다.
- **XML 파일 개요 탭:** XML 파일에 대 한 태그 요구 사항을 검토 합니다.
- **접두사 예 탭:** 여러 blob 및 내보내기 파일을 선택 하는 유효한 접두사의 예를 참조 하세요.

### <a name="sample-xml-file"></a>[샘플 XML 파일](#tab/sample-xml-file)

이 샘플 XML 파일에는 Data Box 내보내기 순서로 내보낼 blob 및 파일을 선택 하는 데 사용 되는 각 XML 태그의 예가 포함 되어 있습니다. 

- XML 파일 요구 사항은 **xml 파일 개요** 탭으로 이동 합니다.
- 유효한 blob 및 파일 접두사의 추가 예를 보려면 **접두사 예** 탭으로 이동 하세요.

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!--BlobList selects individual blobs (BlobPath) and multiple blobs (BlobPathPrefix) in Blob storage for export.-->
   <BlobList>
      <BlobPath>/container1/blob.txt</BlobPath> <!-- Exports /container1/blob.txt -->
      <BlobPathPrefix>/container2/</BlobPathPrefix> <!--Exports all blobs in container2 -->
      <BlobPathPrefix>/container</BlobPathPrefix>  <!-- Exports all containers beginning with prefix: "container" -->
      <BlobPathPrefix>/container1/2021Q2</BlobPathPrefix> <!-- Exports all blobs in container1 with prefix: "2021Q2" -->
   </BlobList>
   
   <!--AzureFileList selects individual files (FilePath) and multiple files (FilePathPrefix) in Azure File storage for export.-->
   <AzureFileList>
      <FilePath>/fileshare1/file.txt</FilePath> <!-- Exports /fileshare1/file.txt -->
      <FilePathPrefix>/fileshare1/</FilePath> <!-- Exports all directories and files in fileshare1 -->
      <FilePathPrefix>/fileshare</FilePathPrefix> <!-- Exports all directories and files in any fileshare with prefix: "fileshare" -->
      <FilePathPrefix>/fileshare2/contosowest</FilePathPrefix> <!-- Exports all directories and files in fileshare2 with prefix: "contosowest" -->
   </AzureFileList>
```

### <a name="xml-file-overview"></a>[XML 파일 개요](#tab/xml-file-overview)

내보내기 순서로 XML 파일을 생성 하는 경우 다음 지침을 따르세요. 태그 형식이 잘못 되 면 내보내기 오류가 발생할 수 있습니다.

내보내기 순서를 저장할 때 XML 파일을 업로드 하는 단계는 [xml 파일을 사용 하 여 주문 내보내기](#export-order-using-xml-file)를 참조 하세요.

#### <a name="path-vs-prefix"></a>경로와 접두사 비교

XML 파일에서 XML 태그를 올바르게 구성 하려면 경로와 접두사의 차이점을 이해 해야 합니다.

* *경로* 는 단일 blob 또는 파일을 선택 하 고 필터링 합니다.
* *접두사* 는 여러 blob 또는 여러 파일을 선택 하 고 필터링 합니다.

올바른 형식의 접두사 예를 보려면 **접두사 예** 탭으로 이동 하세요.

#### <a name="tag-usage"></a>태그 사용법

다음 XML 태그는 Data Box 내보내기 순서로 XML 파일에 사용 됩니다.

| xml 태그           |Description |
|-------------------|------------|
|`<BlobList>`       |&lt;Blobpath &gt; 및 &lt; blobpathprefix 태그의 부모 태그 &gt; 입니다.|
|`<BlobPath>`       |단일 blob을 선택 합니다. |
|`<BlobPathPrefix>` |공통 접두사를 사용 하 여 blob을 선택 합니다. 예를 들어 **접두사 예** 탭으로 이동 합니다.|
|`<AzureFileList>`  |&lt;FilePath &gt; 및 FilePathPrefix 태그의 부모 태그 &lt; &gt; 입니다.|
|`<FilePath>`       |단일 파일을 선택 합니다. |
|`<FilePathPrefix>` |공통 접두사를 사용 하 여 파일을 선택 합니다. 예를 들어 **접두사 예** 탭으로 이동 합니다.|

컨텍스트에서 태그를 보려면 **샘플 XML 파일** 탭으로 이동 합니다.

#### <a name="xml-tag-requirements"></a>XML 태그 요구 사항

* 모든 XML 태그는 대/소문자를 구분 하므로 위의 표에 있는 태그와 정확히 일치 해야 합니다.
* 여는 태그와 닫는 태그가 일치해야 합니다.
* XML 태그나 형식이 잘못 되 면 데이터 내보내기 오류가 발생할 수 있습니다.
* Blob 접두사 또는 파일 접두사가 잘못 된 경우 데이터를 내보낼 수 없습니다. 유효한 접두사의 예를 보려면 **접두사 예** 탭으로 이동 하세요.

### <a name="prefix-examples"></a>[접두사 예](#tab/prefix-examples)

이러한 샘플 경로는 여러 blob 또는 내보내기 파일을 선택 하는 접두사를 생성 하는 다양 한 방법을 보여 줍니다.

#### <a name="valid-blob-path-prefixes"></a>유효한 blob 경로 접두사

아래 샘플 경로를 사용 &lt; &gt; 하 여 Azure blob storage에서 내보내기를 위해 여러 blob을 선택 합니다.

|Blob 경로 접두사        |Description                                                                     |태그 예                         |
|------------------------|--------------------------------------------------------------------------------|------------------------------------|
|/                       |저장소 계정의 모든 blob을 내보냅니다.                                       |`<BlobPathPrefix>/</BlobPathPrefix>`|
|/$root/                 |루트 컨테이너의 모든 blob을 내보냅니다.                                        |`<BlobPathPrefix>/$root/</BlobPathPrefix>`|
|container2            |컨테이너 **container2** 모든 blob을 내보냅니다.                              |`<BlobPathPrefix>/container1/</BlobPathPrefix>`|
|/컨테이너          |접두사 **컨테이너로** 시작 하는 모든 컨테이너의 모든 blob을 내보냅니다.      |`<BlobPathPrefix>/containers</BlobPathPrefix>`|
|/container1/2021Q2      |**2021Q2** 접두사로 시작 하는 container **container1** 의 모든 blob을 내보냅니다.|`<BlobPathPrefix>/container1/2021Q2</BlobPathPrefix>`|

내보낼 *단일* blob을 선택 하려면 &lt; &gt; 컨테이너 경로 및 blob 이름을 포함 하는 blobpath 태그를 사용 합니다. 예를 들어 **container1** 컨테이너에서 **blob.txt** 을 선택 하려면 다음 태그를 사용 `<BlobPath>/container1/blob.txt</BlobPath>` 합니다.

#### <a name="valid-file-path-prefixes"></a>유효한 파일 경로 접두사

아래 샘플 경로는 &lt; &gt; 내보내기에 대 한 여러 Azure Files를 선택 하기 위해 FilePathPrefix 태그와 함께 사용 됩니다.

|파일 경로 접두사        |Description                                                                                          |태그 예|
|------------------------|-----------------------------------------------------------------------------------------------------|-----------|
|/                       |저장소 계정의 모든 파일 및 디렉터리를 내보냅니다. |`<FilePathPrefix>/</FilePath>Prefix`|
|/fileshare1/            |**Fileshare1** 이라는 공유의 모든 파일 및 디렉터리를 내보냅니다.                                                 |`<FilePathPrefix>/fileshare1/</FilePath>Prefix`|
|/파일 공유              |**접두사 파일 공유로** 시작 하는 파일 공유의 모든 파일 및 디렉터리를 내보냅니다. |`<FilePathPrefix>/fileshare</FilePath>Prefix`|
|/fileshare2/contosowest |**Contosowest** 접두사로 시작 하는 파일 공유 **fileshare2** 의 모든 파일 및 디렉터리를 내보냅니다.|`<FilePathPrefix>/fileshare1/contosowest</FilePath>Prefix`|

내보낼 *단일* 파일을 선택 하려면 &lt; &gt; 공유 경로 및 파일 이름과 함께 FilePath 태그를 사용 합니다. 예를 들어 **fileshare1** 에서 **file.txt** 를 선택 하려면 다음 태그를 사용 합니다.`<FilePath>/fileshare1/file.txt</FilePath>`

---

## <a name="track-the-order"></a>주문 추적

주문이 완료되면 Azure Portal에서 주문 상태를 추적할 수 있습니다. Data Box 주문, **개요** 로 차례로 이동하여 상태를 확인합니다. 포털에서는 해당 주문을 **주문됨** 상태로 표시합니다.

디바이스 준비가 완료되면 선택한 스토리지 계정에서 데이터 복사가 시작됩니다. Portal에서는 **데이터 복사 진행 중** 중 상태의 주문을 표시합니다.

![Data Box 내보내기 주문, 데이터 복사 진행 중](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-in-progress.png)

Data Box는 원본 스토리지 계정에서 데이터를 복사합니다. 데이터 복사가 완료되면 Data Box가 잠기고 Portal에서 **복사 완료됨** 상태의 주문을 표시합니다.

![Data Box 내보내기 주문, 데이터 복사 완료](media/data-box-deploy-export-ordered/azure-data-box-export-order-data-copy-complete.png)

Azure Storage에서 Data Box로 데이터 내보내기가 실패할 수 있습니다. 이러한 Blob 내보내기는 지원되지 않으므로 Blob이 보관 Blob이 아닌지 확인합니다. 

> [!NOTE]
> 보관 Blob의 경우 Azure Storage 계정에서 Data Box로 내보내기 전에 해당 Blob을 리하이드레이션해야 합니다. 자세한 내용은 [보관 Blob을 리하이드레이션]( ../storage/blobs/storage-blob-rehydration.md)을 참조하세요.

디바이스를 사용할 수 없는 경우 알림이 수신됩니다. 디바이스를 사용할 수 있으면 Microsoft에서는 배송할 디바이스를 확인하고 배송을 준비합니다. 디바이스를 준비하는 동안 다음 작업이 수행됩니다.

* 디바이스와 연결된 각 스토리지 계정에 대해 SMB 공유가 생성됩니다.
* 각 공유에 대한 사용자 이름 및 암호와 같은 액세스 자격 증명이 생성됩니다.
* 디바이스가 잠겨 있으며 디바이스 잠금 해제 암호를 사용해야만 액세스할 수 있습니다. 암호를 검색하려면 Azure Portal 계정에 로그인하고 **디바이스 세부 정보** 를 선택해야 합니다.

그러면 Microsoft에서는 디바이스를 준비하고 지역 배송업체를 통해 발송합니다. 디바이스가 배송되면 추적 번호를 받게 됩니다. 포털에서 해당 작업을 **발송됨** 상태로 표시합니다.

![Data Box 내보내기 주문이 발송됨](media/data-box-deploy-export-ordered/azure-data-box-export-order-dispatched.png)

자체 관리형 배송을 선택한 경우 데이터 센터에서 디바이스를 선택할 준비가 되면 다음 단계를 포함하는 메일 알림을 받게 됩니다. 자체 관리형 배송과 관련된 자세한 내용은 [자체 관리형 배송](data-box-portal-customer-managed-shipping.md)을 참조하세요.

![픽업 준비가 완료된 자체 관리형 배송](media/data-box-deploy-export-ordered/azure-data-box-export-order-ready-for-pickup.png)

## <a name="cancel-the-order"></a>주문 취소

이 주문을 취소하려면 Azure Portal에서 **개요** 로 이동하고, 명령 모음에서 **취소** 를 선택합니다.

주문이 수행된 후에도 주문 처리가 시작되기 전까지는 취소할 수 있습니다.

취소된 주문을 삭제하려면 **개요** 로 이동하고, 명령 모음에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
>
> * 내보내기에 대한 필수 조건
> * Data Box에 대해 내보내기 주문
> * 내보내기 주문 추적
> * 내보내기 주문 취소

Data Box를 설정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box 설정](./data-box-deploy-set-up.md)
