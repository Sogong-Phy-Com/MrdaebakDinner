# UML 상태 다이어그램 - 복잡한 클래스

## 선택된 복잡한 클래스 목록

### 1. Order
**복잡한 클래스로 선정된 이유:**
- 상태 변화가 4개 이상 존재: status (pending, cooking, out_for_delivery, delivered, cancelled), paymentStatus (pending, paid, failed), adminApprovalStatus (PENDING, APPROVED, REJECTED, CANCELLED)
- 외부 이벤트로 인해 상태 전이가 여러 갈래로 나뉨 (관리자 승인/거부, 결제 성공/실패, 직원 작업 등)
- 다른 도메인 객체와 상호작용하여 라이프사이클이 달라짐 (InventoryReservation, DeliverySchedule, EmployeeWorkAssignment)

### 2. OrderChangeRequest
**복잡한 클래스로 선정된 이유:**
- 상태 변화가 5개 존재: REQUESTED, APPROVED, REJECTED, PAYMENT_FAILED, REFUND_FAILED
- 외부 이벤트로 인해 상태 전이가 여러 갈래로 나뉨 (관리자 승인/거부, 결제/환불 성공/실패)
- 다른 도메인 객체와 상호작용하여 라이프사이클이 달라짐 (Order, PaymentService, InventoryService)

---

## Class: Order

(복잡한 클래스로 선정된 이유: 상태가 6개 이상이며 이벤트 기반 전이가 많음)

### State List

**주문 상태 (status):**
- Pending: 주문이 생성되어 관리자 승인 대기 중
- Cooking: 관리자 승인 후 조리 시작
- OutForDelivery: 조리 완료 후 배달 시작
- Delivered: 배달 완료
- Cancelled: 주문 취소

**결제 상태 (paymentStatus):**
- Pending: 결제 대기
- Paid: 결제 완료
- Failed: 결제 실패

**관리자 승인 상태 (adminApprovalStatus):**
- PENDING: 관리자 승인 대기
- APPROVED: 관리자 승인 완료
- REJECTED: 관리자 거부
- CANCELLED: 주문 취소로 인한 승인 취소

### UML (PlantUML)

@startuml OrderStateDiagram
!theme plain
skinparam state {
  BackgroundColor #E8F4F8
  BorderColor #333333
  FontSize 11
}

title Order State Diagram - Mr. DaeBak Dinner Service

[*] --> Pending: createOrder()\n[action: set paymentStatus=pending,\nadminApprovalStatus=PENDING]

state "Pending" as Pending {
  state "AdminApproval_PENDING" as AdminPending
  state "AdminApproval_APPROVED" as AdminApproved
}

Pending : adminApprovalStatus = PENDING
Pending : status = pending
Pending : paymentStatus = pending

Pending --> AdminPending: [initial]

AdminPending --> AdminApproved: adminApproveOrder()\n[guard: inventory available]\n[action: create InventoryReservation,\ncreate DeliverySchedule]

AdminPending --> Cancelled: adminRejectOrder()\n[action: process refund,\nrelease resources]

AdminApproved --> Cancelled: cancelOrder()\n[guard: before cooking starts]\n[action: release InventoryReservation,\ncancel DeliverySchedule]

state "Cooking" as Cooking {
  Cooking : status = cooking
  Cooking : adminApprovalStatus = APPROVED
}

AdminApproved --> Cooking: startCooking()\n[guard: adminApprovalStatus == APPROVED\nstatus == pending]\n[action: consume InventoryReservation,\nreduce capacity_per_window]

state "OutForDelivery" as OutForDelivery {
  OutForDelivery : status = out_for_delivery
  OutForDelivery : adminApprovalStatus = APPROVED
}

Cooking --> OutForDelivery: startDelivery()\n[guard: status == cooking]

state "Delivered" as Delivered {
  Delivered : status = delivered
  Delivered : adminApprovalStatus = APPROVED
}

OutForDelivery --> Delivered: completeDelivery()\n[guard: status == out_for_delivery]\n[action: notify customer]

Cooking --> Cancelled: cancelOrder()\n[guard: after cooking started]\n[action: inventory already consumed,\ncancel DeliverySchedule]

OutForDelivery --> Cancelled: cancelOrder()\n[guard: during delivery]\n[action: inventory already consumed]

state "Cancelled" as Cancelled {
  Cancelled : status = cancelled
}

Cancelled --> [*]

Delivered --> [*]

state "Payment States" as PaymentStates {
  state "PaymentPending" as PaymentPending
  state "PaymentPaid" as PaymentPaid
  state "PaymentFailed" as PaymentFailed
}

PaymentPending: paymentStatus = pending
PaymentPaid: paymentStatus = paid
PaymentFailed: paymentStatus = failed

PaymentPending --> PaymentPaid: processPayment()\n[action: set paymentMethod]

PaymentPending --> PaymentFailed: paymentFailure()\n[action: handle payment error]

PaymentFailed --> PaymentPaid: retryPayment()\n[action: retry payment processing]

note right of Pending
  주문 생성 시:
  - paymentStatus = "pending"
  - adminApprovalStatus = "PENDING"
  - status = "pending"
end note

note right of AdminApproved
  관리자 승인 후:
  - InventoryReservation 생성 (consumed=false)
  - DeliverySchedule 생성
  - 주문 수정 가능 (배달 1일 전까지)
end note

note right of Cooking
  조리 시작 시:
  - InventoryReservation.consumed = true
  - capacity_per_window 차감
  - 실제 재고 소비
end note

note right of Delivered
  배달 완료:
  - 최종 상태
  - 재고는 이미 소비됨
end note

note right of Cancelled
  취소 가능 시점:
  1. 관리자 거부 시
  2. 승인 후 조리 전
  3. 조리 시작 후 (재고는 이미 소비됨)
  4. 배달 중 (재고는 이미 소비됨)
end note

@enduml

---

## Class: OrderChangeRequest

(복잡한 클래스로 선정된 이유: 상태가 5개이며 이벤트 기반 전이가 많고, 결제/환불 프로세스와 연동됨)

### State List

- REQUESTED: 고객이 변경 요청을 제출한 상태
- APPROVED: 관리자가 변경 요청을 승인하고 결제/환불이 성공한 상태
- REJECTED: 관리자가 변경 요청을 거부한 상태
- PAYMENT_FAILED: 관리자 승인 후 추가 결제가 실패한 상태
- REFUND_FAILED: 관리자 승인 후 환불이 실패한 상태

### UML (PlantUML)

@startuml OrderChangeRequestStateDiagram
!theme plain
skinparam state {
  BackgroundColor #E8F4F8
  BorderColor #333333
  FontSize 11
}

title OrderChangeRequest State Diagram - Mr. DaeBak Dinner Service

[*] --> REQUESTED: createChangeRequest()\n[guard: order.canModify() == true]\n[action: validate inventory,\ncalculate price difference,\ncalculate change fee]

state "REQUESTED" as REQUESTED {
  REQUESTED : status = REQUESTED
  REQUESTED : requestedAt = now()
}

REQUESTED --> REQUESTED: updateChangeRequest()\n[guard: status in ACTIVE_REQUEST_STATUSES]\n[action: recalculate price,\nrevalidate inventory]

REQUESTED --> APPROVED: approve()\n[guard: inventory available,\nextraChargeAmount == 0]\n[action: replace InventoryReservation,\nupdate Order items,\nupdate Order details,\ncancel DeliverySchedule]

REQUESTED --> APPROVED: approve()\n[guard: inventory available,\nextraChargeAmount > 0,\npayment successful]\n[action: chargeAdditionalAmount(),\nreplace InventoryReservation,\nupdate Order items]

REQUESTED --> APPROVED: approve()\n[guard: inventory available,\nextraChargeAmount < 0,\nrefund successful]\n[action: refundAmount(),\nreplace InventoryReservation,\nupdate Order items]

REQUESTED --> PAYMENT_FAILED: approve()\n[guard: inventory available,\nextraChargeAmount > 0,\npayment failed]\n[action: set status = PAYMENT_FAILED,\nsave admin comment]

REQUESTED --> REFUND_FAILED: approve()\n[guard: inventory available,\nextraChargeAmount < 0,\nrefund failed]\n[action: set status = REFUND_FAILED,\nsave admin comment]

REQUESTED --> REJECTED: reject()\n[guard: status in ACTIVE_REQUEST_STATUSES]\n[action: set rejectedAt = now(),\nsave admin comment]

state "PAYMENT_FAILED" as PAYMENT_FAILED {
  PAYMENT_FAILED : status = PAYMENT_FAILED
  PAYMENT_FAILED : requires retry
}

PAYMENT_FAILED --> REQUESTED: updateChangeRequest()\n[guard: customer wants to retry]\n[action: allow modification]

PAYMENT_FAILED --> APPROVED: retryPayment()\n[guard: payment successful]\n[action: chargeAdditionalAmount(),\nreplace InventoryReservation,\nupdate Order items]

PAYMENT_FAILED --> REJECTED: reject()\n[guard: admin rejects]\n[action: set rejectedAt = now()]

state "REFUND_FAILED" as REFUND_FAILED {
  REFUND_FAILED : status = REFUND_FAILED
  REFUND_FAILED : requires retry
}

REFUND_FAILED --> REQUESTED: updateChangeRequest()\n[guard: customer wants to retry]\n[action: allow modification]

REFUND_FAILED --> APPROVED: retryRefund()\n[guard: refund successful]\n[action: refundAmount(),\nreplace InventoryReservation,\nupdate Order items]

REFUND_FAILED --> REJECTED: reject()\n[guard: admin rejects]\n[action: set rejectedAt = now()]

state "APPROVED" as APPROVED {
  APPROVED : status = APPROVED
  APPROVED : approvedAt = now()
  APPROVED : final state
}

state "REJECTED" as REJECTED {
  REJECTED : status = REJECTED
  REJECTED : rejectedAt = now()
  REJECTED : final state
}

APPROVED --> [*]

REJECTED --> [*]

note right of REQUESTED
  활성 상태 (ACTIVE_REQUEST_STATUSES):
  - REQUESTED
  - PAYMENT_FAILED
  - REFUND_FAILED
  
  활성 상태일 때만 수정 가능
end note

note right of APPROVED
  승인 시 프로세스:
  1. 재고 검증
  2. 가격 차이 계산 (extraChargeAmount)
  3. extraChargeAmount > 0: 추가 결제
  4. extraChargeAmount < 0: 환불
  5. extraChargeAmount == 0: 변경만 수행
  6. 재고 예약 교체
  7. 주문 정보 업데이트
end note

note right of PAYMENT_FAILED
  추가 결제 실패:
  - 상태는 PAYMENT_FAILED로 유지
  - 고객이 재시도 가능
  - 관리자가 거부 가능
end note

note right of REFUND_FAILED
  환불 실패:
  - 상태는 REFUND_FAILED로 유지
  - 고객이 재시도 가능
  - 관리자가 거부 가능
end note

note right of REJECTED
  거부 시:
  - 변경 요청 거부
  - 원래 주문은 그대로 유지
  - 추가 액션 없음
end note

@enduml

---

## 전체 상태 흐름 요약 설명

### Order 상태 흐름

주문(Order)은 세 가지 독립적인 상태 차원을 가집니다:
1. **주문 상태(status)**: pending → cooking → out_for_delivery → delivered 또는 cancelled
2. **결제 상태(paymentStatus)**: pending → paid 또는 failed
3. **관리자 승인 상태(adminApprovalStatus)**: PENDING → APPROVED 또는 REJECTED/CANCELLED

**주요 흐름:**
- 주문 생성 시 모든 상태가 초기값(pending/PENDING)으로 설정됩니다.
- 관리자가 주문을 승인하면 adminApprovalStatus가 APPROVED로 변경되고, InventoryReservation과 DeliverySchedule이 생성됩니다.
- 관리자가 주문을 거부하면 주문이 취소되고 자동 환불이 처리됩니다.
- 승인된 주문에서 조리 직원이 조리를 시작하면 status가 cooking으로 변경되고, 재고가 실제로 소비됩니다.
- 조리 완료 후 배달 직원이 배달을 시작하면 status가 out_for_delivery로 변경됩니다.
- 배달 완료 시 status가 delivered로 변경되며, 이는 최종 상태입니다.
- 언제든지 주문을 취소할 수 있지만, 조리 시작 후에는 재고가 이미 소비되어 환불이 제한될 수 있습니다.

**상호작용하는 객체:**
- InventoryReservation: 주문 승인 시 예약 생성, 조리 시작 시 소비
- DeliverySchedule: 주문 승인 시 생성, 배달 시작 시 활성화
- EmployeeWorkAssignment: 직원 작업 할당과 연결

### OrderChangeRequest 상태 흐름

변경 요청(OrderChangeRequest)은 단일 상태 차원을 가지며, 다음과 같은 흐름을 따릅니다:

**주요 흐름:**
- 고객이 변경 요청을 제출하면 REQUESTED 상태가 됩니다.
- REQUESTED, PAYMENT_FAILED, REFUND_FAILED는 활성 상태로, 이 상태일 때만 수정이 가능합니다.
- 관리자가 변경 요청을 승인하려고 시도하면, 가격 차이(extraChargeAmount)에 따라:
  - extraChargeAmount > 0: 추가 결제가 필요하며, 결제 성공 시 APPROVED, 실패 시 PAYMENT_FAILED
  - extraChargeAmount < 0: 환불이 필요하며, 환불 성공 시 APPROVED, 실패 시 REFUND_FAILED
  - extraChargeAmount == 0: 변경만 수행하며 바로 APPROVED
- PAYMENT_FAILED 또는 REFUND_FAILED 상태에서는 재시도가 가능하며, 성공 시 APPROVED로 전환됩니다.
- 관리자가 변경 요청을 거부하면 REJECTED 상태가 되며, 이는 최종 상태입니다.
- APPROVED 상태에서는 원래 주문의 정보(메뉴, 배달 시간, 주소 등)가 변경 요청의 내용으로 업데이트됩니다.

**상호작용하는 객체:**
- Order: 변경 요청이 승인되면 주문 정보가 업데이트됨
- PaymentService: 추가 결제 또는 환불 처리
- InventoryService: 기존 재고 예약 취소 및 새로운 재고 예약 생성

