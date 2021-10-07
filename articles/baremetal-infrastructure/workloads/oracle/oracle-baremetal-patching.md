---
title: Oracle용 BareMetal에 대한 패치 고려 사항
description: Oracle용 BareMetal Infrastructure의 운영 체제/커널 패치 고려 사항에 관해 알아봅니다.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 10/06/2021
ms.openlocfilehash: c842ad56e6777817e5db84e51a4d1302aebf5a63
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129657495"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Oracle용 BareMetal에 대한 패치 고려 사항

이 문서에서는 Oracle용 BareMetal Infrastructure에 대한 중요한 OS(운영 체제)/커널 패치 고려 사항을 살펴보겠습니다.

적절한 네트워크 성능 및 시스템 안정성을 위해 OS별 버전의 eNIC 및 fNIC 드라이버를 설치합니다. 다음 호환성 표를 참조하세요. 

서버는 호환되는 버전이 있는 고객에게 제공됩니다. 그러나 OS/커널 패치를 하는 동안 드라이버는 기본 드라이버 버전으로 롤백될 수 있습니다. 따라서 OS/커널 패치 후에 올바른 드라이버 버전이 실행 중인지 확인해야 합니다.

| OS 공급업체 | OS 패키지 버전 | 펌웨어 버전 | eNIC 드라이버 | fNIC 드라이버 |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>다음 단계

Oracle용 BareMetal의 이더넷 구성에 관해 알아봅니다.

> [!div class="nextstepaction"]
> [Oracle용 BareMetal의 이더넷 구성](oracle-baremetal-ethernet.md)

