---
title: Microsoft Azure Data Box Disk에 대한 빠른 시작 | Microsoft Docs
description: 이 빠른 시작을 사용하여 Azure Portal에서 Azure Data Box Disk를 신속하게 배포
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.custom: mode-other
ms.openlocfilehash: 11a870c3461758c6310a5253a2d9efcccdca4c74
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061902"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Data Box Disk 배포

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Data Box Disk를 배포하는 방법을 설명합니다. 단계는 신속하게 주문을 만들고, 디스크를 수신하고, 압축을 풀고, 연결하고 Azure에 업로드하도록 디스크에 데이터를 복사하는 방법을 포함합니다.

자세한 단계별 배포 및 추적 지침은 [자습서: Azure Data Box Disk 주문](data-box-disk-deploy-ordered.md)을 참조하세요. 

Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true)을 만듭니다.

::: zone-end

::: zone target="chromeless"

이 가이드에서는 Azure Portal에서 Azure Data Box Disk를 사용하는 단계를 안내합니다. 이 가이드는 다음과 같은 질문에 대답하는 데 도움이 됩니다.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에

- Azure Data Box 서비스에 대한 구독이 활성화되어 있는지 확인합니다. 이 서비스에 대한 구독을 활성화하려면 [서비스를 등록](https://aka.ms/azuredataboxfromdiskdocs)합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs)에서 Azure Portal에 로그인합니다.

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **필수 구성 요소 검토**: 디스크 및 케이블, 운영 체제 및 기타 소프트웨어의 수를 확인합니다.
> - **연결 및 잠금 해제**: 디바이스를 연결하고 디스크를 잠금 해제하여 데이터를 복사합니다.
> - **디스크에 데이터 복사 및 유효성 검사**: 디스크에 데이터를 미리 생성된 폴더로 복사합니다.
> - **디스크 반환**: 데이터가 스토리지 계정에 업로드되는 Azure 데이터 센터에 디스크를 반환합니다.
> - **Azure에서 데이터 확인**: 원본 데이터 서버에서 데이터를 삭제하기 전에 스토리지 계정에 데이터가 업로드되었는지 확인합니다.

::: zone-end


::: zone target="docs"

## <a name="order"></a>주문

### <a name="portal"></a>[포털](#tab/azure-portal)

이 단계에는 약 5분 정도가 걸립니다.

1. Azure Portal에서 새 Azure Data Box 리소스를 만듭니다. 
2. 이 서비스에 대해 활성화된 구독을 선택하고 **가져오기** 로 전송 유형을 선택합니다. 데이터가 있는 **원본 국가** 및 데이터 전송에 대한 **Azure 대상 지역** 을 제공합니다.
3. **Data Box Disk** 를 선택합니다. 최대 솔루션 용량은 35TB이며 더 큰 데이터 크기에 대해 여러 디스크 주문을 만들 수 있습니다.  
4. 주문 세부 정보 및 배송 정보를 입력합니다. 해당 지역에서 서비스를 사용할 수 있는 경우 알림 이메일 주소를 제공하고, 요약을 검토한 다음, 주문을 만듭니다.

주문이 만들어지면 배송을 위해 디스크가 준비됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이러한 Azure CLI 명령을 사용하여 Data Box Disk 작업을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. [az group create](/cli/azure/group#az_group_create) 명령을 실행하여 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다.

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용하여 스토리지 계정을 만들거나 기존 스토리지 계정을 사용합니다.

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. [az databox job create](/cli/azure/databox/job#az_databox_job_create) 명령을 실행하여 SKU DataBoxDisk를 통해 Data Box 작업을 만듭니다.

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. 연락처 이름과 이메일을 변경하는 이 예제에서와 같이 [az databox job update](/cli/azure/databox/job#az_databox_job_update)를 실행하여 작업을 업데이트합니다.

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   [az databox job show](/cli/azure/databox/job#az_databox_job_show) 명령을 실행하여 작업에 대한 정보를 가져옵니다.

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   [az databox job list]( /cli/azure/databox/job#az_databox_job_list) 명령을 사용하여 리소스 그룹에 대한 모든 Data Box 작업을 확인합니다.

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   [az databox job cancel](/cli/azure/databox/job#az_databox_job_cancel) 명령을 실행하여 작업을 취소합니다.

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   [az databox job delete](/cli/azure/databox/job#az_databox_job_delete) 명령을 실행하여 작업을 삭제합니다.

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. [az databox job list-credentials]( /cli/azure/databox/job#az_databox_job_list_credentials) 명령을 사용하여 Data Box 작업에 대한 자격 증명을 나열합니다.

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

주문을 작성하고 나면 디바이스가 배송 가능하도록 준비됩니다.

---

## <a name="unpack"></a>압축 풀기

이 단계에는 약 5분 정도가 걸립니다.

Data Box Disk는 UPS Express 상자에 배송됩니다. 상자를 열고 상자에 다음이 있는지 확인합니다.

- 완충재로 포장된 1~5개의 USB 디스크
- 디스크당 1개의 연결 케이블
- 반송 배송을 위한 포장용 레이블

## <a name="connect-and-unlock"></a>연결 및 잠금 해제

이 단계에는 약 5분 정도가 걸립니다.

1. 포함된 케이블을 사용하여 지원되는 버전을 실행하는 Windows/Linux 컴퓨터에 디스크를 연결합니다. 지원되는 OS 버전에 대한 자세한 내용은 [Azure Data Box Disk 시스템 요구 사항](data-box-disk-system-requirements.md)으로 이동합니다. 
2. 디스크의 잠금을 해제하려면:

    1. Azure Portal에서 **일반 &gt; 디바이스 세부 정보** 로 이동하여 암호를 가져옵니다.
    2. 디스크에 데이터 디스크를 복사하는 데 사용되는 컴퓨터에서 운영 체제별 Data Box Disk 잠금 해제 도구를 다운로드하고 추출합니다. 
    3. Data Box Disk 잠금 해제 도구를 실행하고 지원 암호를 제공합니다. 디스크를 다시 삽입하려면 잠금 해제 도구를 다시 실행하고 암호를 제공합니다. **BitLocker 대화 상자 또는 BitLocker 키를 사용하여 디스크를 잠금 해제하지 마세요.** 디스크 잠금 해제 방법에 대한 자세한 내용은 [Windows 클라이언트에서 디스크 잠금 해제](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) 또는 [Linux 클라이언트에서 디스크 잠금 해제](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)로 이동하세요.
    4. 디스크에 할당한 드라이브 문자가 도구에 의해 표시됩니다. 디스크 드라이브 문자를 기록해 둡니다. 이후 단계에서 사용됩니다.

## <a name="copy-data-and-validate"></a>데이터 복사 및 유효성 검사

이 작업을 완료하는 시간은 데이터 크기에 따라 달라집니다.

1. 드라이브에 *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* 및 *DataBoxDiskImport* 폴더가 포함됩니다. 블록 Blob으로 가져와야 하는 데이터를 *BlockBlob* 폴더로 끌어서 놓아 복사합니다. 마찬가지로 VHD/VHDX와 같은 데이터를 *PageBlob* 폴더로 끌어다 놓고 해당 데이터를 *AzureFile* 로 끌어다 놓습니다. Managed Disks로 업로드하려는 VHD를 *ManagedDisk* 아래의 폴더에 복사합니다.

    *BlockBlob* 및 *PageBlob* 폴더 아래에 각 하위 폴더에 대한 Azure Storage 계정에 컨테이너가 만들어집니다. *AzureFile* 아래의 하위 폴더에 대해 파일 공유가 생성됩니다.

    *BlockBlob* 및 *PageBlob* 폴더 아래의 모든 파일은 Azure Storage 계정 아래의 기본 컨테이너 `$root`로 복사됩니다. *AzureFile* 내의 폴더로 파일을 복사합니다. *AzureFile* 폴더로 직접 복사된 모든 파일에 오류가 발생하고 블록 blob으로 업로드됩니다.

    > [!NOTE]
    > - 모든 컨테이너, Blob 및 파일은 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)을 준수해야 합니다. 이러한 규칙을 따르지 않는 경우 Azure로 데이터 업로드에 실패합니다.
    > - 파일이 블록 Blob에 대해 4.75TiB, 페이지 Blob에 대해 8TiB 및 Azure Files에 대해 1TiB를 초과하지 않도록 합니다.

2. **(선택 사항이지만 권장됨)** 복사가 완료되면 적어도 *DataBoxDiskImport* 폴더에 제공된 `DataBoxDiskValidation.cmd`를 실행하고 옵션 1을 선택하여 파일이 유효한지 검사하는 것이 좋습니다. 또한, 시간이 허락할 경우 옵션 2를 사용하여 유효성 검사를 위한 체크섬도 생성하는 것이 좋습니다(데이트 크기에 따라 시간이 걸릴 수 있음). 이러한 단계는 Azure로 데이터를 업로드할 때 오류가 발생할 가능성을 최소화합니다.
3. 드라이브는 제거해도 됩니다.

## <a name="ship-to-azure"></a>Azure에 배송

이 단계는 완료하는 데 약 5~7분이 걸립니다.

1. 원래 패키지에 모든 디스크를 함께 배치합니다. 포함된 포장용 레이블을 사용합니다. 레이블이 손상되거나 분실된 경우 포털에서 다운로드합니다. **개요** 로 이동하고 명령 모음에서 **포장용 레이블 다운로드** 를 클릭합니다.
2. 배송 지점에 봉인된 패키지를 전달합니다.  

Data Box Disk 서비스가 이메일 알림을 보내고 Azure Portal에서 작업 상태를 업데이트합니다.

## <a name="verify-your-data"></a>데이터 확인

이 작업을 완료하는 시간은 데이터 크기에 따라 달라집니다.

1. Data Box 디스크가 Azure 데이터 센터 네트워크에 연결되면 Azure에 데이터 업로드가 자동으로 시작됩니다.
2. Azure Data Box 서비스는 Azure Portal을 통해 데이터 복사가 완료되었음을 알립니다.
    
    1. 모든 오류에 대한 오류 로그를 확인하고 적절한 조치를 수행합니다.
    2. 원본에서 데이터를 삭제하기 전에 데이터 스토리지 계정에 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 단계를 완료하려면 2-3분이 걸립니다.

정리하려면 Data Box 주문을 취소한 다음 삭제할 수 있습니다.

- 주문이 처리되기 전에 Azure Portal에서 Data Box 주문을 취소할 수 있습니다. 주문이 처리되면 주문을 취소할 수 없습니다. 완료된 단계에 도달할 때까지 주문이 진행됩니다.

    주문을 취소하려면 **개요** 로 이동하고 명령 모음에서 **취소** 를 클릭합니다.  

- 상태가 Azure Portal에서 **완료됨** 또는 **취소됨** 으로 표시되면 주문을 삭제할 수 있습니다.

    주문을 삭제하려면 **개요** 로 이동하고 명령 모음에서 **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 데이터를 Azure로 가져올 수 있도록 Azure Data Box Disk를 배포했습니다. Azure Data Box Disk 관리에 대해 자세히 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [Azure Portal을 사용하여 Data Box Disk 관리](data-box-portal-ui-admin.md)

::: zone-end
