# 생성한 NLB
![NLB](https://user-images.githubusercontent.com/79209568/169247844-d4eaf830-a26c-48a2-a03e-789238742151.png)

- 각 AZ 영역에 탄력적인 IP, ELP-1, ELP-2를 지정
- 아파치 서버 인스턴스는 AZ-a 영역에 public 서브넷 내에 존재

| **NLB** | **EC2** | **결과** |
|:---:|:---:|:---:|
| T**arget Group** | **Secret Group-인바운드규칙** | **-** |
| EC2 인스턴스 | 0.0.0.0/0 | **성공** EIP-1 private IP (10.0.3.247) |
| - | - | - |
| EC2 private IP | 0.0.0.0/0 | **실패** (Time out) |
| EC2 private IP | 내 아이피 | **실패** (Time out) |
| - | - | - |
| 1. EC2 private IP | EIP-1 private IP, EIP-2 private IP | **성공** EIP-1 private IP (10.0.3.247) |
| 2. EC2 private IP | EIP-1 private IP | **성공** EIP-1 private IP (10.0.3.247) |
| 3. EC2 private IP | EIP-2 private IP + 교차 영역 속성 O | **성공** EIP-1 private IP (10.0.3.247) |
