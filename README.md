## Network 환경

https://www.tecracer.com/blog/2023/11/centralized-traffic-filtering-using-aws-network-firewall.html

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/89712d40-f9f4-4891-b608-fe549e023fbf/d9a797bb-2b9f-4264-906a-07fb4a4c7e01/image.png)

![centralized-network-filtering-network-firewall-traffic-flow.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/89712d40-f9f4-4891-b608-fe549e023fbf/421853a8-86a4-4137-9424-61686f62f9b5/centralized-network-filtering-network-firewall-traffic-flow.png)

구축 과정

1. AWS CLI, terraform 설치
2. `aws configure`
    1. IAM secret key pub key 입력
    2. region:  `us-east-1a`로 수정
3. 테라폼 파일 값 수정
    1. `terraform.tfvars`
        1. us-east-1a 1b 로 수정
        2. Private VPC Subnet 수정
4. AWS Service Quota 늘리기
    1. VPC max num 5 -> 20
    2. EC2 EIP 5 -> 20
5. `terraform init,` `terraform plan`, `terraform apply`
6. 끝나고 무조건 `terraform destory`!!!

### Problem 1. On-premise ↔ Spoke VPC A 연결 불가 해결

---

현 상황: On-premise에서 Spoke VPC A (Workload VPC) 연결 불가

문제:

**On-premise에서 “client”라는 이름의 EC2 Instance에서의 모든 접근을 허용하도록 Firewall Policy를 구성해보기.**

(Terraform 변수로 해결말고 직접 Policy를 추가해보기)

### Problem 2. Spoke VPC B(WORKLOAD) 추가

---

Spoke VPC B라는 워크로드를 생성해 Inspection VPC를 거치지 않고 자체적인 Firewall endpoint와 Policy를 가지게끔 구현

**구현 조건** (사진은 10.2.0.0/16으로 되어있음 → 10.4.0.0이 맞음)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/89712d40-f9f4-4891-b608-fe549e023fbf/e3f5792a-34f8-4b75-815b-8da2c3bff82f/image.png)

1. 10.4.0.0/16 대역으로 생성
2. Firewall Subnet : 10.4.16.0/28
3. Public Subnet : 10.4.1.0/24
    1. ALB or NLB to Private Subnet
4. Private Subnet : 10.4.2.0/24
5. TGW Subnet : 10.4.0.0/28
    1. TGW Gateway ENI
6. IGW

→ Egress는 TGW를 통해 Egress VPC를 통해 나가게끔 구현

→ Ingress는 자체 IGW를 통해 Firewall Subnet을 통해 Public Sub과 Private Subnet까지 연결시켜보기

### IF YOU WANT MORE>>>

https://www.tecracer.com/blog/2023/08/hybrid-dns-resolution-using-route-53-endpoints.html

https://www.tecracer.com/blog/2023/08/multiple-site-to-site-vpn-connections-in-aws-hub-and-spoke-topology.html

https://www.tecracer.com/blog/2023/06/build-a-site-to-site-ipsec-vpn-with-public-encryption-domain.html

https://www.tecracer.com/blog/