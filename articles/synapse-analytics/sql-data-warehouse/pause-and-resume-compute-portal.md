---
title: '빠른 시작: Azure Portal을 통해 전용 SQL 풀에서 컴퓨팅 일시 중지 및 다시 시작'
description: Azure Portal을 사용하여 전용 SQL 풀에 대한 컴퓨팅을 일시 중지하고 비용을 절감할 수 있습니다. 데이터 웨어하우스를 사용할 준비가 되면 컴퓨팅을 다시 시작합니다.
services: synapse-analytics
author: julieMSFT
ms.author: jrasnick
manager: craigg
ms.reviewer: igorstan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom: seo-lt-2019, azure-synapse, mode-portal
ms.openlocfilehash: a24c0327d2f19888de483c5b5f48c05d28f215c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022249"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>빠른 시작: Azure Portal을 통해 전용 SQL 풀에서 컴퓨팅 일시 중지 및 다시 시작

Azure Portal을 사용하여 전용 SQL 풀 컴퓨팅 리소스를 일시 중지하고 다시 시작할 수 있습니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="before-you-begin"></a>시작하기 전에

[만들기 및 연결 - 포털](../quickstart-create-sql-pool-portal.md)을 사용하여 **mySampleDataWarehouse** 라는 전용 SQL 풀을 만듭니다. 

## <a name="pause-compute"></a>컴퓨팅 일시 중지

비용을 줄이기 위해 필요에 따라 컴퓨팅 리소스를 일지 중지 및 다시 시작할 수 있습니다. 예를 들어, 밤 시간과 주말에 데이터베이스를 사용하지 않으려면 해당 시간에 일시 중지했다가 주간에 다시 시작할 수 있습니다.
 
>[!NOTE]
>데이터베이스 일시 중지 중에는 컴퓨팅 리소스 요금이 부과되지 않습니다. 그러나 스토리지에 대한 비용은 계속 청구됩니다. 

전용 SQL 풀을 일시 중지하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **전용 SQL 풀** 페이지로 이동하여 SQL 풀을 엽니다. 
3. **상태** 가 **온라인** 입니다.

    ![온라인 컴퓨팅](././media/pause-and-resume-compute-portal/compute-online.png)

4. 전용 SQL 풀을 일시 중지하려면 **일시 중지** 단추를 클릭합니다. 
5. 계속할지를 묻는 확인 질문이 나타납니다. **예** 를 클릭합니다.
6. 몇 분 정도 기다린 다음, **상태** 는 **일시 중지** 가 됩니다.

    ![스크린샷은 상태 값이 일시 중지인 샘플 데이터 웨어하우스의 Azure Portal을 보여줍니다.](./media/pause-and-resume-compute-portal/pausing.png)

7. 일시 중지 작업이 완료되면 상태는 **일시 중지됨** 이 되고 옵션 단추는 **다시 시작** 이 됩니다.
8. 이제 전용 SQL 풀에 대한 컴퓨팅 리소스는 오프라인 상태입니다. 서비스를 다시 시작할 때까지 컴퓨팅에 대한 요금이 청구되지 않습니다.

    ![오프라인 컴퓨팅](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>컴퓨팅 다시 시작

전용 SQL 풀을 다시 시작하려면 다음 단계를 따르세요.

1. **전용 SQL 풀** 페이지로 이동하여 SQL 풀을 엽니다.
3. **mySampleDataWarehouse** 페이지에서 **상태** 는 **일시 중지됨** 이 됩니다.

    ![오프라인 컴퓨팅](././media/pause-and-resume-compute-portal/compute-offline.png)

1. SQL 풀을 다시 시작하려면 **다시 시작** 을 클릭합니다. 
1. 시작할지를 묻는 확인 질문이 나타납니다. **예** 를 클릭합니다.
1. **상태** 가 **다시 시작 중** 이 됩니다.

    ![스크린샷은 시작 단추가 선택되고 상태 값을 다시 시작하는 샘플 데이터 웨어하우스의 Azure Portal을 보여줍니다.](./media/pause-and-resume-compute-portal/resuming.png)

1. SQL 풀이 다시 온라인 상태가 되면 **온라인** 상태가 되고 옵션 단추가 **일시 중지** 가 됩니다.
1. SQL 풀에 대한 컴퓨팅 리소스는 현재 온라인 상태이고 서비스를 사용할 수 있습니다. 컴퓨팅에 대한 요금이 다시 청구됩니다.

    ![온라인 컴퓨팅](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>리소스 정리

데이터 웨어하우스 단위 및 전용 SQL 풀에 저장된 데이터에 대해 요금이 청구됩니다. 이러한 컴퓨팅 및 스토리지 리소스에 대한 요금이 별도로 청구됩니다. 

- 스토리지에 데이터를 유지하려는 경우 컴퓨팅을 일시 중지합니다.
- 앞으로 요금이 부과되지 않게 하려면 전용 SQL 풀을 삭제하면 됩니다. 

필요에 따라 다음 단계에 따라 리소스를 정리합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 전용 SQL 풀을 선택합니다.

    ![리소스 정리](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. 컴퓨팅을 일시 중지하려면 **일시 중지** 단추를 클릭합니다. 

1. 컴퓨팅 또는 스토리지에 대한 요금이 청구되지 않도록 전용 SQL 풀을 제거하려면 **삭제** 를 클릭합니다.



## <a name="next-steps"></a>다음 단계

전용 SQL 풀에 대한 컴퓨팅을 일시 중지하고 다시 시작했습니다. [전용 SQL 풀에 데이터를 로드](./load-data-from-azure-blob-storage-using-copy.md)하는 방법에 대해 자세히 알아보려면 다음 문서를 계속 진행하세요. 컴퓨팅 기능 관리에 대한 자세한 내용은 [컴퓨팅 관리 개요](sql-data-warehouse-manage-compute-overview.md) 문서를 참조하세요.
