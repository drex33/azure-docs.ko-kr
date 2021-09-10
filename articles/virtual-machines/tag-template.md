---
title: 템플릿을 사용하여 VM에 태그를 만드는 방법
description: 템플릿을 사용하여 가상 머신에 태그를 지정하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: 92be32279b812d4a8ff3baad3e3ccc881fe20049
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695328"
---
# <a name="tagging-a-vm-using-a-template"></a>템플릿을 사용하여 VM 태그 지정

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 문서에서는 Resource Manager 템플릿을 사용하여 Azure에서 VM에 태그를 만드는 방법을 설명합니다. 태그는 리소스 또는 리소스 그룹에 직접 배치할 수 있는 사용자 정의 키/값 쌍입니다. Azure는 현재 리소스 및 리소스 그룹당 최대 50개의 태그를 지원합니다. 태그를 만들 때 리소스에 배치하거나 기존 리소스에 추가할 수 있습니다.

[이 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-tags)은 Compute(Virtual Machine), Storage(Storage 계정) 및 네트워크(공용 IP 주소, Virtual Network 및 네트워크 인터페이스) 리소스에 태그를 배치합니다. 이 템플릿은 Windows VM에 대한 것이지만 Linux VM에도 적용할 수 있습니다.

**템플릿 링크** 에서 [Azure에 배포](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-tags)단추를 클릭합니다. 이렇게 하면 이 템플릿을 배포할 수 있는 [Azure 포털](https://portal.azure.com/) 로 이동합니다.

![태그가 포함된 간단한 배포](./media/tag/deploy-to-azure-tags.png)

이 템플릿에는 *부서*, *애플리케이션* 및 *만든 사람* 태그가 포함되어 있습니다. 다른 태그 이름을 사용하려는 경우 템플릿에서 직접 이러한 태그를 추가/편집할 수 있습니다.

![템플릿의 Azure 태그](./media/tag/azure-tags-in-a-template.png)

여기서 볼 수 있듯이 태그는 콜론(:)으로 구분된 키/값 쌍으로 정의됩니다. 다음 형식으로 태그를 정의해야 합니다.

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

편집을 마친 후 선택한 태그와 함께 템플릿 파일을 저장합니다.

이어서 **매개 변수 편집** 섹션에서 태그의 값을 입력할 수 있습니다.

![Azure 포털에서 태그 편집](./media/tag/edit-tags-in-azure-portal.png)

**만들기** 를 클릭하여 태그 값과 함께 이 템플릿을 배포합니다.

### <a name="next-steps"></a>다음 단계

- Azure 리소스에 태그를 지정하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md) 및 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.
- 태그를 사용하여 Azure 리소스의 사용을 관리하는 방법을 알아보려면 [Azure 청구서 이해](../cost-management-billing/understand/review-individual-bill.md)를 참조하세요.
