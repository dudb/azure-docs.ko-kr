---
title: "Azure Data Lake Store에 대한 질문과 대답 | Microsoft Docs"
description: "Azure Data Lake Store와 관련된 문제를 해결 또는 완화에 대한 지침"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf7fd555-3e30-43ce-b28c-c3ad0a241fdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 84d644e07036f511e685b0fd020cdf8c310fd969


---
# <a name="frequently-asked-questions-for-azure-data-lake-store"></a>Azure Data Lake Store에 대한 질문과 대답
이 문서에서는 Azure Data Lake Store와 관련된 FAQ에 대해 알아봅니다.

## <a name="how-do-i-handle-disaster-recovery-and-data-corruption-for-my-azure-data-lake-store-account"></a>내 Azure Data Lake Store 계정에 대한 재해 복구와 데이터 손상을 어떻게 처리하나요?
Azure Data Lake Store 계정의 데이터는 자동화된 복제본을 통해 지역 내에서 일시적인 하드웨어 장애에 대한 복원력이 있습니다. 이렇게 하면 Azure Data Lake Store SLA를 충족하는 내구성 및 고가용성을 사용할 수 있습니다. 드물게 발생하는 전체 지역 가동 중단 또는 삭제 실수로부터 데이터를 보호하는 방법에 대한 몇 가지 지침이 있습니다.

### <a name="disaster-recovery-guidance"></a>재해 복구 지침
모든 고객은 자체적으로 재해 복구 계획을 준비하는 것이 중요합니다. 재해 복구 계획을 빌드하려면 아래 Azure 설명서를 참조하세요. 여기에는 고유한 계획을 직접 만들 수 있는 리소스가 있습니다.

* [Azure 응용 프로그램에 대한 재해 복구 및 고가용성](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Azure 복구력 기술 지침](../resiliency/resiliency-technical-guidance.md)

#### <a name="best-practices"></a>모범 사례
재해 복구 계획의 요구에 맞게 정렬된 주파수를 사용하여 다른 지역의 다른 Data Lake Store 계정에 중요한 데이터를 복사하는 것이 좋습니다. [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) 또는 [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)를 포함하여 데이터를 복사하는 다양한 메서드가 있습니다. Azure Data Factory는 반복적으로 데이터 이동 파이프라인을 만들고 배포하기 위한 유용한 서비스입니다.

지역 가동 중단이 발생하는 경우 데이터가 복사된 지역에 있는 데이터에 액세스할 수 있습니다.[Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)를 모니터링하여 전세계의 Azure 서비스 상태를 확인할 수 있습니다.

### <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>데이터 손상 또는 삭제 실수 복구 지침
Azure Data Lake Store가 자동화된 복제본을 통해 데이터 복원력을 제공하는 반면 이렇게 하더라도 응용 프로그램(또는 개발자/사용자)의 데이터를 손상시키거나 실수로 삭제하지 않도록 방지하지 않습니다.

#### <a name="best-practices"></a>모범 사례
삭제 실수를 방지하려면 [Data Lake Store 보안 기능](data-lake-store-security-overview.md)을 사용하여 Data Lake Store 계정에 대한 올바른 액세스 정책을 먼저 설정하는 것이 좋습니다. 다른 Data Lake Store 계정, 폴더 또는 Azure 구독에서 [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) 또는 [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)를 사용하여 중요한 데이터의 복사본을 정기적으로 만드는 것이 좋습니다.  데이터 손상 또는 삭제 인시던트로부터 복구하는 데 사용할 수 있습니다. Azure Data Factory는 반복적으로 데이터 이동 파이프라인을 만들고 배포하기 위한 유용한 서비스입니다.

조직에서는 해당 Azure Data Lake Store 계정에 대한 [진단 로깅](data-lake-store-diagnostic-logs.md)을 활성화하여 파일을 삭제하거나 업데이트할 수 있는 사용자에 대한 정보를 제공하는 데이터 액세스 감사 추적을 수집할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)




<!--HONumber=Nov16_HO2-->


