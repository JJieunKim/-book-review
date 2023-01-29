## 2 ARMv8 레지스터
AArch64는 64비트 크기의 범용 레지스터 31개(X0~X30)를 제공함.
(W0~W30의 이름을 사용하면 하위 32비트만 사용)
하나의 단일 레지스터 세트를 모든 익셉션 레벨에서 사용 가능함.

### 2.1 프로세서 상태 레지스터(PSTATE)
PSTATE는 프로세서의 상태정보를 제공하며 세가지로 구성됨.
 - AArch64 전용
 - AArch32 전용
 - 공통 속성
PSTATE 필드 중에 NZCV, DAIF 필드는 EL0에서도 접근 가능하며, 그 외 비트는 EL1 이상에서만 접근 가능.
익셉션 발생 -> PSTATE 갱신 -> 몇몇 비트가 SPSR_ELn 레지스터에 복사됨 -> 익셉션에서 복귀할 때 SPSR_ELn레지스터가 PSTATE로 복사됨.

### 2.2 AArch64 특수 목적 레지스터
1) PC (Program Counter) : 다음에 실행할 인스트럭션의 위치를 저장.
2) ELR (Exception Link Register) : 익셉션 복귀 시 돌아갈 실행 위치를 저장. 프로세서에 의해 현재 익셉션 레벨에 해당하는 레지스터 값이 PC에 복사됨.
3) SPSR (Saved Process Status Register) : 특정 시점의 프로세서 사태를 저장. 익셉션이 발생하면 프로세서에 의해 프로세서 상태가 PSTATE로부터 SPSR에 저장됨. 익셉션에서 복귀할 때는 프로세서에 의해 SPSR에서 PSTATE로 복원됨.
 * ELR, SPSR은 복귀할 익셉션 레벨이 없는 EL0을 제외한 익셉션 레벨마다 존재함.
4) SP (Stack Pointer) : 스택의 현재 위치. 익셉션 레벨마다 존재함. 

### 2.3 시스템 레지스터
AArch64에서 시스템 설정은 각 시스템 레지스터를 통해 제어되며, MSR와 MRS 인스트럭션을 사용하여 접근할 수 있다. 
eg)
MRS x0, TTBR_EL1	/* TTBR0_EL1레지스터의 값을 x0레지스터로 읽어올 때 */
MSR TTBR0_EL1, x0	/* x0레지스터의 값을 TTBR0_EL1 레지스터에 쓸 때 */

### 2.4 ABI의 레지스터 사용 규칙
ABI(Application Binary Interface) : 아키텍처마다 바이너리들이 상호 동작하기 위한 규칙.
AAPCS64(Procedure Call Standard for the ARM 64-bit architecture) : ABI중에 함수 호출을 다룸. 어셈블리와 C함수 사이의 인터페이스, C함수 호출 등에 관해 다룸. X0~X30 레지스터들이 있음.
* 함수 호출 시 파라미터는 최대 8개까지 레지스터(x0~x7)을 통해 전달됨. AArch32는 파라미터를 4개까지 레지스터를 통해 저장하고 초과하는 파라미터는 스택을 통해 전달함. 외부 메모리에 위치한 스택 접근을 줄이기 위해 레지스터 개수만큼만 쓰는게 효율적.

### MEMO
