---
title: "사이트 복구에서 장애 조치(failover) | Microsoft Docs"
description: "Azure Site Recovery는 가상 컴퓨터 및 실제 서버의 복제, 장애 조치 및 복구를 조정합니다. Azure로 또는 보조 데이터 센터로 장애 조치에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 31d80e58071383aee22772e224c90289a610c0b3


---
# <a name="failover-in-site-recovery"></a>사이트 복구에서 장애 조치
Azure Site Recovery 서비스는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터는 Azure 또는 보조 온-프레미스 데이터 센터로 복제할 수 있습니다. 빠른 개요를 알아보려면 [Azure Site Recovery란?](site-recovery-overview.md)

## <a name="overview"></a>개요
이 문서는 사이트 복구로 보호되는 가상 컴퓨터 및 물리적 서버의 복구(장애 조치 및 장애 복구)를 위한 정보와 지침을 제공합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="types-of-failover"></a>장애 조치(Failover) 유형
가상 컴퓨터 및 물리적 서버에 대한 보호가 설정되고 복제가 적용된 후에는 필요에 따라 장애 조치(Failover)를 실행할 수 있습니다. 사이트 복구는 다양한 유형의 장애 조치를 지원합니다.

| **장애 조치(Failover)** | **실행하는 시기** | **세부 정보** | **프로세스** |
| --- | --- | --- | --- |
| **테스트 장애 조치** |복제 전략의 유효성을 검사하거나 재해 복구 훈련과 수행을 실행합니다. |데이터 손실 또는 가동 중지 시간 없음<br/><br/>복제에 영향 없음<br/><br/>프로덕션 환경에 영향 없음 |장애 조치 시작<br/><br/>장애 조치(Failover) 후에 테스트 가상 컴퓨터가 네트워크에 연결되는 방법 지정<br/><br/> **작업** 탭에서 진행 상황을 추적합니다. 테스트 컴퓨터는 보조 위치에서 생성 및 시작<br/><br/>Azure - Azure Portal에서 컴퓨터에 연결<br/><br/>보조 사이트 - 동일한 호스트와 클라우드에 있는 컴퓨터에 액세스<br/><br/>테스트를 완료하고 테스트 장애 조치 설정을 자동으로 정리 |
| **계획된 장애 조치** |규정 준수 요구 사항에 맞게 실행<br/><br/>계획된 유지 보수에 대한 실행<br/><br/>데이터의 장애 조치를 실행하여 예상된 전원 오류나 심각한 일기 예보와 같이 알려진 중단 시 실행되는 워크로드를 유지합니다.<br/><br/>기본에서 보조로 장애 조치 후 장애 복구 실행 |데이터 손실 없음<br/><br/>기본 서버에서 가상 컴퓨터를 종료하고 보조 위치에 가져오는 데 걸리는 시간 동안 가동 중지 시간이 발생합니다.<br/><br/>대상 컴퓨터가 장애 조치(failover) 후 원본 컴퓨터가 되면 가상 컴퓨터가 영향을 받습니다. |장애 조치 시작<br/><br/> **작업** 탭에서 진행 상황을 추적합니다. 원본 컴퓨터 종료됨<br/><br/>보조 위치에서 복제본 컴퓨터 시작<br/><br/>Azure - Azure Portal에서 복제본 컴퓨터에 연결<br/><br/>보조 사이트 - 동일한 호스트와 동일한 클라우드에 있는 컴퓨터에 액세스<br/><br/>장애 조치(failover) 커밋 |
| **계획되지 않은 장애 조치** |정전 또는 바이러스 공격 등의 예기치 않은 사고로 인해 기본 사이트에 액세스하지 못할 경우 이 유형의 장애 조치(failover)를 반응적인 방법으로 실행합니다. <br/><br/> 기본 사이트를 사용할 수 없는 경우에도 계획되지 않은 장애 조치(failover)를 수행할 수 있습니다. |복제 빈도 설정에 따른 데이터 손실<br/><br/>데이터가 동기화된 마지막 시간에 따라 최신 상태가 됩니다. |장애 조치 시작<br/><br/> **작업** 탭에서 진행 상황을 추적합니다. 옵션으로 가상 컴퓨터의 종료와 최신 데이터의 동기화 시도<br/><br/>보조 위치에서 복제본 컴퓨터 시작<br/><br/>Azure - Azure Portal에서 복제본 컴퓨터에 연결<br/><br/>동일한 호스트와 동일한 클라우드에 있는 컴퓨터에 보조 사이트 액세스<br/><br/>장애 조치(failover) 커밋 |

지원되는 장애 조치 유형은 배포 시나리오에 따라 달라집니다.

| **장애 조치 방향** | **테스트 장애 조치** | **계획된 장애 조치** | **계획되지 않은 장애 조치** |
| --- | --- | --- | --- |
| 보조 VMM 사이트에 기본 VMM 사이트 |지원됨 |지원됨 |지원됨 |
| 기본 VMM 사이트에 보조 VMM 사이트 |지원됨 |지원됨 |지원됨 |
| 클라우드-클라우드(단일 VMM 서버) |지원됨 |지원됨 |지원됨 |
| Azure에 VMM 사이트 |지원됨 |지원됨 |지원됨 |
| VMM 사이트에 Azure |지원되지 않음 |지원됨 |지원되지 않음 |
| Azure에 Hyper-V 사이트 |지원됨 |지원됨 |지원됨 |
| Hyper-V에 Azure 사이트 |지원되지 않음 |지원됨 |지원되지 않음 |
| Azure에 VMware 사이트 |지원됨(향상된 시나리오)<br/><br/>  지원되지 않음(레거시 시나리오) |지원되지 않음 |지원됨 |
| Azure에 물리적 서버 |지원됨(향상된 시나리오)<br/><br/>  지원되지 않음(레거시 시나리오) |지원되지 않음 |지원됨 |

## <a name="failover-and-failback"></a>장애 조치 및 장애 복구
배포에 따라 가상 컴퓨터를 보조 온-프레미스 사이트나 Azure에 장애 조치합니다. Azure로 장애 조치하는 컴퓨터는 Azure 가상 컴퓨터로 생성됩니다. 단일 가상 컴퓨터 또는 실제 서버 또는 복구 계획을 장애 조치할 수 있습니다. 복구 계획은 보호된 가상 컴퓨터나 실제 서버가 포함된 하나 이상의 정렬된 그룹으로 구성되어 있습니다. 이러한 계획은 속한 그룹에 따라 장애 조치되는 여러 컴퓨터의 장애 조치를 오케스트레이션합니다. [자세한](site-recovery-create-recovery-plans.md) 복구 계획을 알아봅니다.

장애 조치가 완료된 후 컴퓨터가 다음과 같이 보조 위치에서 실행됩니다.

* Azure로 장애 조치하는 경우 장애 조치 후 컴퓨터는 보호되지 않거나 기본 또는 보조 위치에 복제되지 않습니다. Azure에서 가상 컴퓨터는 복원력은 제공하지만 복제는 제공하지 않는 지리적으로 복제된 저장소에 저장됩니다.
* 보조 사이트에 계획되지 않은 장애 조치를 했다면 장애 조치 후에 보조 위치 내 장애 조치 컴퓨터가 보호 또는 복제되지 않습니다.
* 보조 사이트에 계획된 장애 조치를 했다면 장애 조치 후에 보조 위치 내 장애 조치 컴퓨터가 보호됩니다.

### <a name="failback-from-secondary-site"></a>보조 사이트로부터 장애 복구
보조 사이트로부터 기본 사이트로 장애 복구는 기본 사이트에서 보조 사이트로 장애 조치와 동일한 프로세스를 사용합니다. 기본에서 보조 데이터 센터로의 장애 조치가 커밋 및 완료된 후에 기본 사이트를 사용할 수 있게 되면 역방향 복제를 시작할 수 있습니다. 역방향 복제는 델타 데이터를 동기화하여 보조 사이트와 기본 사이트 사이에 복제를 시작합니다. 역방향 복제는 가상 컴퓨터를 보호된 상태로 가져오지만 보조 데이터 센터는 여전히 활성 위치입니다. 기본 사이트에서 활성 위치로 만들기 위해 다른 역방향 복제 후에 보조에서 기본으로 계획된 장애 조치를 시작합니다.

### <a name="failback-from-azure"></a>Azure로부터 장애 복구
Azure로 장애 조치한 경우 가상 컴퓨터는 가상 컴퓨터에 대한 Azure 복구 기능으로 보호됩니다. 원래 기본 사이트를 활성 위치로 만들려면 계획된 장애 조치를 실행합니다. 원래 사이트를 사용할 수 없는 경우 원래 위치 또는 대체 위치로 장애 복구할 수 있습니다. 기본 위치에 대한 장애 복구 후 다시 복제를 시작하려면 역방향 복제를 시작 합니다.

### <a name="failover-considerations"></a>장애 조치 시 고려 사항
* **장애 조치 후 IP 주소**— 기본적으로 장애 조치한 컴퓨터는 원본 컴퓨터와 다른 IP 주소를 갖습니다. 동일한 IP 주소를 유지하는 경우 다음을 참조하십시오.
  * **보조 사이트**—보조 사이트로 장애 조치하고 IP 주소를 유지하고자 한다면 [이 문서](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) 를 읽으십시오. ISP에서 지원하는 경우 공용 IP 주소를 유지할 수 있습니다.
  * **Azure**—Azure로 장애 조치하는 경우 가상 컴퓨터 속성의 **구성** 탭에서 할당하고자 하는 IP 주소를 지정할 수 있습니다. Azure에 장애 조치 후 공용 IP 주소를 유지할 수 없습니다. 내부 주소로 사용되는 비 RFC 1918 주소 공간을 유지할 수 있습니다.
* **부분 장애 조치**— 전체 사이트가 아닌 사이트의 일부를 장애 조치하려는 경우 다음을 유의하십시오.

  * **보조 사이트**—기본 사이트의 일부를 보조 사이트에 장애 조치하고 기본 사이트로 다시 연결하고자 한다면 사이트 간 VPN 연결을 사용하여 보조 사이트에서 장애 조치한 응용프로그램을 기본 사이트에서 실행 중인 인프라 구성 요소에 연결하십시오. 전체 서브넷을 장애 조치할 경우 가상 컴퓨터 IP 주소를 유지할 수 있습니다. 부분 서브넷을 장애 조치할 경우 서브넷을 사이트 사이에서 분할할 수 없으므로 가상 컴퓨터 IP 주소를 유지할 수 없습니다.
  * **Azure**—Azure에 부분 사이트를 장애 조치하고 기본 사이트에 다시 연결하고자 한다면 사이트 간 VPN을 사용하여 Azure에서 장애 조치한 응용프로그램을 기본 사이트에서 실행 중인 인프라 구성 요소에 연결할 수 있습니다. 전체 서브넷을 장애 조치할 경우 가상 컴퓨터 IP 주소를 유지할 수 있음을 기억하십시오. 부분 서브넷을 장애 조치할 경우 서브넷을 사이트 사이에서 분할할 수 없으므로 가상 컴퓨터 IP 주소를 유지할 수 없습니다.
* **드라이브 문자**—장애 조치 후 가상 컴퓨터에서 드라이브 문자를 유지하려는 경우 가상 컴퓨터에 대한 SAN 정책을 **켜기**로 설정할 수 있습니다. 가상 컴퓨터 디스크는 자동으로 온라인 상태가 됩니다. [자세히 알아보기](https://technet.microsoft.com/library/gg252636.aspx).
* **클라이언트 요청 라우팅**—사이트 복구는 Azure 트래픽 관리자와 함께 작동하여 장애 조치 후 응용프로그램에 대한 클라이언트 요청을 라우팅합니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행
테스트 장애 조치를 실행하면 테스트 복제 컴퓨터에 대한 네트워크 설정을 선택하라는 메시지가 나타납니다. 다양한 옵션이 있습니다.  

| **테스트 장애 조치 옵션** | **설명** | **장애 조치 검사** | **세부 정보** |
| --- | --- | --- | --- |
| **Azure에 장애 조치 — 네트워크 없이** |대상 Azure 네트워크를 선택하지 마십시오. |Azure에서 예상한 대로 가상 컴퓨터가 시작되는지 테스트하는 장애 조치 검사 |복구 계획의 모든 테스트 가상 컴퓨터가 단일 클라우드 서비스에 추가되고 서로 연결할 수 있습니다.<br/><br/>컴퓨터는 장애 조치 후 Azure 네트워크에 연결되지 않습니다.<br/><br/>사용자는 공용 IP 주소와 테스트 컴퓨터에 연결할 수 있습니다. |
| **Azure에 장애 조치 — 네트워크 사용** |대상 Azure 네트워크를 선택합니다. |장애 조치가 테스트 컴퓨터가 네트워크에 연결되어 있는지 확인합니다. |Azure 프로덕션 네트워크에서 격리된 Azure 네트워크를 만들고 복제된 가상 컴퓨터에 대한 인프라를 예상대로 작동하도록 구성합니다.<br/><br/>테스트 가상 컴퓨터의 서브넷은 장애 조치(failover)된 가상 컴퓨터의 서브넷을 기반으로 하며 계획되거나 계획되지 않은 장애 조치(failover)가 발생할 경우 연결됩니다. |
| **보조 VMM 사이트로 장애 조치 — 네트워크 없이** |VM 네트워크를 선택 하지 마십시오. |장애 조치(failover)에서 테스트 컴퓨터가 생성되었는지 확인<br/><br/>복제 가상 컴퓨터가 존재하는 호스트와 동일한 호스트에 테스트 가상 컴퓨터가 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다. |<p>장애 조치된 컴퓨터는 네트워크에 연결되지 않습니다.<br/><br/>컴퓨터는 생성된 후에 VM 네트워크에 연결될 수 있습니다. |
| **보조 VMM 사이트로 장애 조치—네트워크 사용** |기존 VM 네트워크 선택 |장애 조치가 가상 컴퓨터 만들어졌는지 확인합니다. |복제 가상 컴퓨터가 존재하는 호스트와 동일한 호스트에 테스트 가상 컴퓨터가 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다.<br/><br/>프로덕션 네트워크에서 격리된 VM 네트워크 만들기<br/><br/>권장되는 VLAN 기반 네트워크를 사용하는 경우 이 목적을 위해 VMM에서 별도의 논리 네트워크(프로덕션에 사용되지 않음)를 만드십시오. 이 논리 네트워크는 테스트 장애 조치(failover)의 목적으로 VM 네트워크를 만드는 데 사용됩니다.<br/><br/>논리 네트워크는 가상 컴퓨터를 호스트하는 모든 Hyper-V 서버에 있는 하나 이상의 네트워크 어댑터에 연결해야 합니다.<br/><br/>VLAN 논리 네트워크의 경우 논리 네트워크에 추가한 네트워크 사이트는 격리되어야 합니다.<br/><br/>Windows 네트워크 가상화 기반의 논리 네트워크를 사용한다면 Azure Site Recovery가 격리된 VM 네트워크를 자동으로 생성합니다. |
| **보조 VMM 사이트로 장애 조치—네트워크 만들기** |임시 테스트 네트워크가 **논리 네트워크** 에서 지정한 설정 및 관련 네트워크 사이트를 기반으로 자동 생성됩니다. |장애 조치가 가상 컴퓨터 만들어졌는지 확인합니다. |복구 계획에서 VM 네트워크를 두 개 이상 사용할 경우 이 옵션을 사용합니다. Windows 네트워크 가상화 네트워크를 사용하는 경우 이 옵션을 사용하여 복제 가상 컴퓨터의 네트워크에서 동일한 설정(서브넷 및 IP 주소 풀)으로 VM 네트워크를 자동으로 만들 수 있습니다. 테스트 장애 조치 완료 후 이러한 VM 네트워크는 자동으로 정리됩니다.</p><p>복제 가상 컴퓨터가 존재하는 호스트와 동일한 호스트에 테스트 가상 컴퓨터가 생성됩니다. 복제본 가상 컴퓨터가 있는 클라우드에는 추가되지 않습니다. |

> [!NOTE]
> 테스트 장애 조치(failover) 중에 가상 컴퓨터에 제공된 IP 주소는 이 IP 주소가 테스트 장애 조치(failover) 네트워크에서 사용할 수 있는 경우, 계획되거나 계획되지 않은 장애 조치(failover)를 수행할 때 얻게 되는 IP 주소와 동일합니다. 동일한 IP 주소를 테스트 장애 조치(failover) 네트워크에서 사용할 수 없는 경우 가상 컴퓨터는 테스트 장애 조치(failover) 네트워크에서 사용 가능한 다른 IP 주소를 받게 됩니다.
>
>

### <a name="run-a-test-failover-from-on-premises-to-azure"></a>온-프레미스로부터 Azure에 테스트 장애 조치 실행
이 절차는 복구 계획에 대한 테스트 장애 조치를 실행하는 방법을 설명합니다. 또는 **가상 컴퓨터** 탭에서 단일 컴퓨터에 대한 장애 조치를 실행할 수 있습니다.

1. **복구 계획** > *recoveryplan_name*을 선택합니다.  **장애 조치(Failover)** > **Test 장애 조치(Failover)**에서 의견이나 질문을 게시합니다.
2. **장애 조치 테스트 확인** 페이지에서 장애 조치 후에 복제본 컴퓨터를 Azure 네트워크에 어떻게 연결할지를 지정합니다.
3. Azure에 장애 조치하고 클라우드에 대해 데이터 암호화를 사용하는 경우 **암호화 키** 에서 공급자 설치 중에 데이터 암호화를 활성화할 때 발행된 인증서를 선택합니다.
4. **작업** 탭에서 장애 조치 진행 상황을 추적합니다. Azure 포털에서 테스트 복제본 컴퓨터가 표시되어야 합니다.
5. 온-프레미스 사이트에서 Azure 내의 복제본 컴퓨터에 액세스하여 가상 컴퓨터에 RDP 연결을 시작할 수 있습니다. 가상 컴퓨터에 대해 포트 3389가 끝점에서 열려 있어야 합니다.
6. 완료되면 장애 조치가 **테스트 완료** 단계에 도달하면 **테스트 완료**를 클릭하여 마칩니다.
7. **참고** 에서 테스트 장애 조치와 연관된 모든 관측 내용을 기록하고 저장합니다.
8. **테스트 장애 조치가 완료됨** 을 클릭하면 테스트 환경이 자동으로 정리됩니다. 이것이 완료되면 테스트 장애 조치가 C**omplete** 상태를 표시합니다.

> [!NOTE]
> 테스트 장애 조치가 2주 이상 지속된다면 강제로 완료됩니다. 테스트 장애 조치 중에 자동으로 생성된 모든 요소 또는 가상 컴퓨터가 삭제됩니다.
>
>

### <a name="run-a-test-failover-from-a-primary-on-premises-site-to-a-secondary-on-premises-site"></a>기본 온-프레미스 사이트에서 보조 온-프레미스 사이트로 테스트 장애 조치(failover)를 실행합니다.
도메인 컨트롤러 복사본 생성 및 테스트 환경에 테스트 DHCP 및 DNS 서버 배치와 같은 여러 가지 일을 해야 테스트 장애 조치(failover)를 실행할 수 있습니다. 다음과 같은 몇 가지 방법으로 이를 수행할 수 있습니다.

* 기존 네트워크를 사용하여 테스트 장애 조치를 실행하려는 경우 해당 네트워크에서 Active Directory, DHCP 및 DNS를 준비합니다.
* VM 네트워크를 자동으로 생성하는 옵션을 사용하여 테스트 장애 조치를 실행하고자 한다면 복구 계획 내 그룹 1 앞에 수동 단계를 추가하고 테스트 장애 조치를 실행하기 전에 자동으로 생성된 네트워크에 인프라 리소스를 추가합니다.

#### <a name="things-to-note"></a>주의할 사항
* 보조 사이트에 복제 시, 복제 컴퓨터에서 사용되는 네트워크 유형이 테스트 장애 조치에 사용되는 논리 네트워크 유형과 일치해야 하는 것은 아니지만 일부 조합이 작동하지 않을 수 있습니다. 복제본에서 DHCP 및 VLAN 기반 격리를 사용하는 경우 복제본에 대한 VM 네트워크는 고정 IP 주소 풀이 필요하지 않습니다. 따라서 사용 가능한 주소 풀이 없으므로 테스트 장애 조치에 대한 Windows 네트워크 가상화의 사용이 작동하지 않습니다. 또한 복제본 네트워크가 격리 없음이고 테스트 네트워크가 Windows 네트워크 가상화인 경우 테스트 장애 조치가 작동하지 않습니다. 이것은 격리 없는 네트워크에 Windows 네트워크 가상화 네트워크를 만드는 데 필요한 서브넷이 없기 때문입니다.
* 장애 조치 후에 복제본 가상 컴퓨터가 매핑된 VM 네트워크에 연결된 방식은 VM네트워크가 VMM 콘솔에서 구성된 방식에 따라 다릅니다.
  * **격리 없음 또는 VLAN 격리로 구성된 VM 네트워크**—VM 네트워크에 대해 DHCP가 지정되어 있다면 복제본 가상 컴퓨터가 관련된 논리 네트워크 내 네트워크 사이트에 대해 지정된 설정을 사용하여 VLAN ID에 연결됩니다. 가상 컴퓨터는 사용 가능한 DHCP 서버로부터 IP 주소를 수신합니다. 대상 VM 네트워크에 대해 정의된 고정 IP 주소 풀이 필요 없습니다. VM 네트워크에 대해 고정 IP 주소 풀이 사용되는 경우 복제본 가상 컴퓨터가 관련된 논리 네트워크 내 네트워크 사이트에 대해 지정된 설정을 사용하여 VLAN ID에 연결됩니다. 가상 컴퓨터는 VM 네트워크에 대해 지정된 풀로부터 IP 주소를 수신합니다. 고정 IP 주소 풀이 대상 VM 네트워크에 정의되어 있다면 IP 주소 할당이 실패합니다. 보호 및 복구에 사용하려는 원본과 대상 VMM 서버 모두에 IP 주소 풀을 만들어야 합니다.
  * **Windows 네트워크 가상화를 사용한 VM 네트워크**—VM 네트워크가 이 설정으로 구성된 경우 원본 VM 네트워크가 DHCP 또는 고정 IP 주소 풀을 사용하도록 구성되었는지 여부에 상관 없이 고정 풀이 대상 VM 네트워크에 대해 정의되어야 합니다. DHCP를 정의하는 경우 대상 VMM 서버가 DHCP 서버 역할을 하며, 대상 VM 네트워크에 대해 정의된 풀로부터 IP 주소를 제공합니다. 고정 IP 주소 풀의 사용이 원본 서버에 대해 정의 되어 있다면 대상 VMM 서버가 풀로부터 IP 주소를 할당합니다. 두 경우 모두, 고정 IP 주소 풀이 정의되어 있지 않으면 IP 주소 할당이 실패합니다.

#### <a name="run-test"></a>테스트 실행
이 절차는 복구 계획에 대한 테스트 장애 조치를 실행하는 방법을 설명합니다. 또는 **가상 컴퓨터** 탭에서 단일 컴퓨터 또는 실제 서버에 대한 장애 조치를 실행할 수 있습니다.

1. **복구 계획** > *recoveryplan_name*을 선택합니다.  **장애 조치(Failover)** > **Test 장애 조치(Failover)**에서 의견이나 질문을 게시합니다.
2. **테스트 장애 조치 확인** 페이지에서 테스트 장애 조치 후에 가상 컴퓨터를 어떻게 네트워크에 연결할지를 지정합니다.
3. **작업** 탭에서 장애 조치 진행 상황을 추적합니다. 장애 조치가 **테스트 완료** 단계에 도달하면 **테스트 완료**를 클릭하여 테스트 장애 조치를 완료합니다.
4. **참고** 를 클릭하여 테스트 장애 조치(Failover)와 연관된 항목을 기록한 후 저장합니다.
5. 완료되면 가상 컴퓨터가 성공적으로 시작되는지 확인합니다.
6. 가상 컴퓨터가 성공적으로 시작되는지 확인한 후 격리된 환경을 정리하기 위해 테스트 장애 조치(Failover)를 완료합니다. 자동으로 VM 네트워크를 만들도록 선택한 경우 정리 작업에서 모든 테스트 가상 컴퓨터 및 테스트 네트워크를 삭제합니다.

> [!NOTE]
> 테스트 장애 조치가 2주 이상 지속된다면 강제로 완료됩니다. 테스트 장애 조치 중에 자동으로 생성된 모든 요소 또는 가상 컴퓨터가 삭제됩니다.
>
>

#### <a name="prepare-dhcp"></a>DHCP 준비
테스트 장애 조치에 사용되는 가상 컴퓨터가 DHCP를 사용하는 경우 테스트 장애 조치의 목적으로 생성된 격리된 네트워크에 테스트 DHCP 서버를 만들어야 합니다.

### <a name="prepare-active-directory"></a>Active Directory 준비
응용프로그램 테스트를 위해 테스트 장애 조치를 실행하려면 테스트 환경에서 프로덕션 Active Directory 환경의 복사본이 필요합니다. 자세한 내용은 [Active Directory의 테스트 장애 조치(failover) 시 고려 사항](site-recovery-active-directory.md#test-failover-considerations) 섹션을 살펴보세요. 

### <a name="prepare-dns"></a>DNS 준비
다음과 같이 테스트 장애 조치에 대한 DNS 서버를 준비합니다.

* **DHCP**—가상 컴퓨터에서 DHCP를 사용하는 경우 테스트 DNS의 IP 주소를 테스트 DHCP 서버에서 업데이트해야 합니다. 네트워크 유형의 Windows 네트워크 가상화를 사용하는 경우 VMM 서버가 DHCP 서버로 작동합니다. 따라서 테스트 장애 조치(failover) 네트워크에서 DNS의 IP 주소가 업데이트되어야 합니다. 이 경우 가상 컴퓨터가 관련 DNS 서버에 자체 등록됩니다.
* **고정 주소**—가상 컴퓨터가 고정 IP 주소를 사용하는 경우 테스트 장애 조치(failover)에서 테스트 DNS 서버의 IP 주소를 업데이트해야 합니다. 테스트 가상 컴퓨터의 IP 주소로 DNS를 업데이트해야 할 수 있습니다. 이러한 용도로 다음 예제 스크립트를 사용할 수 있습니다.

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## <a name="run-a-planned-failover-primary-to-secondary"></a>계획된 장애 조치 실행(기본에서 보조)
 이 절차는 복구 계획에 대해 계획된 장애 조치를 실행하는 방법을 설명합니다. 또는 **가상 컴퓨터** 탭에서 단일 가상 컴퓨터에 대한 장애 조치를 실행할 수 있습니다.

1. 시작하기 전에 장애 조치하려는 모든 가상 컴퓨터가 초기 복제를 완료했는지 확인합니다.
2. **복구 계획** > *recoveryplan_name*을 선택합니다. **장애 조치(Failover)** > **Planned 장애 조치(Failover)**에서 의견이나 질문을 게시합니다.
3. **계획된 장애 조치 확인 **페이지에서 원본 및 대상 위치를 선택합니다. 장애 조치 방향을 유의하십시오.

   * 이전 장애 조치가 예상대로 작동하며 모든 가상 컴퓨터 서버가 원본 또는 대상 위치에 있는 경우 장애 조치 방향의 세부 정보는 정보 제공 전용입니다.
   * 원본과 대상 위치 모두에서 가상 컴퓨터가 활성화 되어있는 경우에는 **방향 변경** 단추가 나타납니다. 이 단추를 사용하여 장애 조치가 발생하는 위치를 변경 및 지정합니다.
4. Azure에 장애 조치하고 클라우드에 대해 데이터 암호화를 사용하는 경우 **암호화 키** 에서 VMM 서버에서 공급자 설치 중에 데이터 암호화를 활성화할 때 발행된 인증서를 선택합니다.
5. 계획된 장애 조치를 시작할 때 첫 번째 단계는 데이터 손실을 방지하기 위해 가상 컴퓨터를 종료 하는 것입니다. **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다. 장애 조치 시 (가상 컴퓨터 또는 복구 계획에 포함된 스크립트에서) 오류가 발생하는 경우 복구 계획의 계획된 장애 조치가 중단됩니다. 장애 조치를 다시 시작할 수 있습니다.
6. 복제본 가상 컴퓨터가 만들어진 후에는 커밋 대기중 상태입니다. **커밋** 을 클릭하여 장애 조치를 커밋합니다.
7. 복제가 완료되면 보조 위치에서 가상 컴퓨터가 시작됩니다.

## <a name="run-an-unplanned-failover"></a>계획되지 않은 장애 조치 실행
이 절차는 복구 계획에 대한 계획되지 않은 장애 조치를 실행하는 방법을 설명합니다. 또는 **가상 컴퓨터** 탭에서 단일 컴퓨터 또는 실제 서버에 대한 장애 조치를 실행할 수 있습니다.

1. **복구 계획** > *recoveryplan_name*을 선택합니다.  **장애 조치(Failover)** > **Unplanned 장애 조치(Failover)**에서 의견이나 질문을 게시합니다.
2. **계획되지 않은 장애 조치 확인 **페이지에서 원본 및 대상 위치를 선택합니다. 장애 조치 방향을 유의하십시오.

   * 이전 장애 조치가 예상대로 작동하며 모든 가상 컴퓨터 서버가 원본 또는 대상 위치에 있는 경우 장애 조치 방향의 세부 정보는 정보 제공 전용입니다.
   * 원본과 대상 위치 모두에서 가상 컴퓨터가 활성화 되어있는 경우에는 **방향 변경** 단추가 나타납니다. 이 단추를 사용하여 장애 조치가 발생하는 위치를 변경 및 지정합니다.
3. Azure에 장애 조치하고 클라우드에 대해 데이터 암호화를 사용하는 경우 **암호화 키** 에서 VMM 서버에서 공급자 설치 중에 데이터 암호화를 활성화할 때 발행된 인증서를 선택합니다.
4. **가상 컴퓨터 종료 및 최신 데이터 동기화** 를 선택하여 사이트 복구가 보호된 가상 컴퓨터를 종료하고 데이터를 동기화하여 최신 버전의 데이터가 장애 조치되도록 지정해야 합니다. 이 옵션을 선택하지 않거나 시도가 성공하지 못하면 장애 조치가 가상 컴퓨터에 대해 사용 가능한 최신 복구 지점에서 시작됩니다.
5. **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다. 계획되지 않은 장애 조치 중에 오류가 발생해도 복구 계획은 완료될 때까지 실행됩니다.
6. 가상 컴퓨터는 장애 조치 후에 **커밋 보류 중** 상태입니다. **커밋** 을 클릭하여 장애 조치를 커밋합니다.
7. 여러 복구 지점을 사용하여 복제를 설정하는 경우 복구 지점 변경에서 최신이 아닌 복구 지점을 사용하도록 선택할 수 있습니다(기본적으로 최신이 사용됨). 커밋한 후에 추가 복구 지점이 제거됩니다.
8. 복제가 완료되면 보조 위치에서 가상 컴퓨터가 시작되고 실행 됩니다. 그러나 보호되거나 복제하지는 않습니다. 기본 사이트를 동일한 기본 인프라를 사용하여 다시 사용할 수 있을 때 **역방향 복제** 를 클릭하여 역방향 복제를 시작합니다. 이렇게 하면 모든 데이터가 기본 사이트로 다시 복제되며 가상 컴퓨터가 장애 조치를 위해 다시 준비됩니다. 계획되지 않은 장애 조치 후 역방향 복제가 데이터 전송 시 오버헤드를 발생시킵니다. 전송이 클라우드에 대한 초기 복제 설정을 위해 구성된 동일한 방법을 사용합니다.

## <a name="failback-from-secondary-to-primary"></a>보조에서 기본으로 장애 복구
 기본에서 보조 위치로 장애 조치 후 복제된 가상 컴퓨터는 사이트 복구에 의해 보호되지 않으며 보조 위치가 이제 기본 역할을 합니다. 이들 절차를 따라 원래 기본 사이트로 장애 복구합니다. 이 절차는 복구 계획에 대해 계획된 장애 조치를 실행하는 방법을 설명합니다. 또는 **가상 컴퓨터** 탭에서 단일 가상 컴퓨터에 대한 장애 조치를 실행할 수 있습니다.

1. **복구 계획** > *recoveryplan_name*을 선택합니다. **장애 조치(Failover)** > **Planned 장애 조치(Failover)**에서 의견이나 질문을 게시합니다.
2. **계획된 장애 조치 확인 **페이지에서 원본 및 대상 위치를 선택합니다. 장애 조치 방향을 유의하십시오. 기본으로부터 장애 조치가 예상대로 작동하고 모든 가상 컴퓨터가 보조 위치에 있다면 이것은 정보 제공용입니다.
3. Azure로부터 장애 복구하는 경우 **데이터 동기화**:에서 설정을 선택합니다.

   * **장애 조치(failover) 전에 데이터 동기화(델타 변경만 동기화)**—이 옵션은 가상 컴퓨터를 종료하지 않고 동기화하므로 가상 컴퓨터에 대한 가동 중지 시간을 최소화합니다. 이 에이전트는 다음을 수행합니다.
     * 1 단계: Azure에서 가상 컴퓨터의 스냅숏을 찍고 온-프레미스 Hyper-V 호스트에 복사합니다. 컴퓨터가 Azure에서 계속 실행됩니다.
     * 2단계: 새로운 변경 사항이 발생할 수 있도록 Azure에서 가상 컴퓨터를 종료합니다. 온-프레미스 서버에 전송되는 최종 변경 집합 및 온-프레미스 가상 컴퓨터가 시작됩니다.

    - **장애 조치 중에만 데이터 동기화(전체 다운로드)**—오랜 시간 동안 Azure에서 실행한 경우 이 옵션을 사용합니다. 대부분의 디스크가 변경되었고 체크섬 계산에 시간을 소요하지 않으므로 이 옵션이 더 빠릅니다. 디스크의 다운로드를 수행합니다. 온-프레미스 가상 컴퓨터가 삭제되었을 때도 유용합니다.

    > [AZURE.NOTE] Azure를 한 달 이상 실행했거나 온-프레미스 VM이 삭제된 경우 이 옵션을 사용하는 것이 좋습니다. 이 옵션은 체크섬 계산을 수행하지 않습니다.

1. Azure에 장애 조치하고 클라우드에 대해 데이터 암호화를 사용하는 경우 **암호화 키** 에서 VMM 서버에서 공급자 설치 중에 데이터 암호화를 활성화할 때 발행된 인증서를 선택합니다.
2. 기본적으로 마지막 복구 지점을 사용하지만 **복구 지점 변경** 에서 다른 복구 지점을 지정할 수 있습니다.
3. 확인 표시를 클릭하여 장애 복구를 시작합니다.  **작업** 탭에서 장애 조치 진행 상황을 따를 수 있습니다.
4. 초기 데이터 동기화가 완료되고 Azure에서 가상 컴퓨터를 종료할 준비가 되었을 때 장애 조치 전에 데이터를 동기화하는 옵션을 선택했다면 **작업** > <planned failover job name>**완전한 장애 조치**에서 의견이나 질문을 게시합니다. 그러면 Azure 컴퓨터가 종료되고 마지막 변경 내용이 온-프레미스 가상 컴퓨터에 전송된 다음 시작됩니다.
5. 이제 가상 컴퓨터에 로그인하여 예상대로 사용 가능한지 확인할 수 있습니다.
6. 가상 컴퓨터가 커밋 보류 상태입니다. **커밋** 을 클릭하여 장애 조치를 커밋합니다.
7. 이제 장애 복구를 완료하려면 **역방향 복제** 를 클릭하여 기본 사이트에 있는 가상 컴퓨터의 보호를 시작하십시오.

## <a name="failback-to-an-alternate-location"></a>대체 위치로 장애 복구
[Hyper-V 사이트와 Azure](site-recovery-hyper-v-site-to-azure.md) 사이에서 보호를 배포했다면 Azure로부터 대체 온-프레미스 위치로 장애 복구할 수 있습니다. 이것은 새 온-프레미스 하드웨어를 설정해야 하는 경우 유용합니다. 그 방법은 다음과 같습니다.

1. 새 하드웨어를 설정하는 경우 서버에서 Windows Server 2012 R2 및 Hyper-V역할을 설치 합니다.
2. 원본 서버에 있던 동일한 이름의 가상 네트워크 스위치를 만듭니다.
3. 장애 복구하려는 **보호된 항목** -> **보호 그룹** -> <ProtectionGroupName> -> <VirtualMachineName>을 선택하고 **계획된 장애 복구**를 선택합니다.
4.  계획된 장애 조치 확인 select 존재하지 않는 경우 온-프레미스 가상 컴퓨터 만들기에서 의견이나 질문을 게시합니다.
5. **호스트 이름** 에서 가상 컴퓨터를 배치하려는 새 Hyper-V 호스트 서버를 선택합니다.
6. 데이터 동기화에서 **장애 조치 전에 데이터 동기화**옵션을 선택합니다. 이 옵션은 가상 컴퓨터를 종료하지 않고 동기화하므로 가상 컴퓨터에 대한 가동 중지 시간을 최소화합니다. 이 에이전트는 다음을 수행합니다.

   * 1 단계: Azure에서 가상 컴퓨터의 스냅숏을 찍고 온-프레미스 Hyper-V 호스트에 복사합니다. 컴퓨터가 Azure에서 계속 실행됩니다.
   * 2단계: 새로운 변경 사항이 발생할 수 있도록 Azure에서 가상 컴퓨터를 종료합니다. 온-프레미스 서버에 전송되는 최종 변경 집합 및 온-프레미스 가상 컴퓨터가 시작됩니다.
7. 확인 표시를 클릭하여 장애 조치(장애 복구)를 시작합니다.
8. 초기 동기화가 완료되고 Azure에서 가상 컴퓨터를 종료할 준비가 되면 **작업** > <planned failover job> > **완전한 장애 조치**에서 의견이나 질문을 게시합니다. 그러면 Azure 컴퓨터가 종료되고 최신 변경 내용이 온-프레미스 가상 컴퓨터에 전송된 다음 시작됩니다.
9. 온-프레미스 가상 컴퓨터에 로그인하여 모든 것이 예상대로 작동하는지 확인합니다. 그 다음 **커밋** 을 클릭하여 장애 조치를 완료합니다.
10. **역방향 복제** 를 클릭하여 온-프레미스 가상 컴퓨터의 보호를 시작합니다.

    > [!NOTE]
    > 데이터 동기화 단계에 있는 동안 장애 복구 작업을 취소하면 온-프레미스 VM이 손상된 상태가 됩니다. 데이터 동기화가 Azure VM 디스크에서 온-프레미스 데이터 디스크로 최신 데이터를 복사하기 때문이며 동기화가 완료될 때까지 디스크 데이터가 일관된 상태가 아닐 수 있습니다. 데이터 동기화를 취소한 후 온-프레미스 VM이 부팅되면 부팅되지 않을 수 있습니다. 장애 조치(Failover)를 다시 트리거하여 데이터 동기화를 완료합니다.
    >
    >



<!--HONumber=Nov16_HO3-->


