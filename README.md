# SW_Logic_Circuits

## 1. 설계목적
Verilog HDL을 이용하여 음료수 자판기를 직접 설계함으로써 조합 논리회로의 이해도를 높인다.

## 2. 설계방법
Verilog HDL을 이용하여 자판기를 제어하는 조합 논리회로를 구현한다.

## 3. 설계내용
600원 짜리와 700원 짜리 음료수를 판매하는 자판기이다. 이 자판기는 100원 짜리 동전 7개와 500원 짜리 동전 1개를 각각 입력 받을 수 있다. 최대 1200원의 돈을 입력받을 수 있다. 만약 거스름돈이 발생할 경우에는 거스름돈을 100원 짜리 동전으로 반환하게 되는데 입력한 금액과 선택한 상품에 따라 잔돈은 1개에서 6개까지 반환될 수 있다.

※ 단, 입력한 동전의 금액이 음료수의 값 보다 작을 때에는 음료수를 선택할 수 없으며 반환되는 거스름돈도 없다고 가정한다.

입력 받은 돈을 표시하는 신호는 S, N3, N2, N1, N0이다.

![1](https://user-images.githubusercontent.com/58457978/70213829-a7b18000-177d-11ea-95f8-8ce067478882.png)

이 때, N2, N1, N0는 입력 받은 100원 짜리 동전의 개수에 해당하는 2진수를 나타내고,
N3는 입력 받은 500원짜리 동전의 개수를 나타낸다.

예제)
N3 N2 N1 N0 = 0011 이면 300원을 나타냄
N3 N2 N1 N0 = 1011 이면 800원을 나타냄
N3 N2 N1 N0 = 1101 이면 1000원을 나타냄
N3 N2 N1 N0 = 1111 이면 1200원을 나타냄
그리고 S는 음료수 선택이다.
S = 0 이면 600원짜리 음료수 선택
S = 1 이면 700원짜리 음료수 선택
출력을 표시하는 신호는 G0, G1, C2, C1, C0이다.

![2](https://user-images.githubusercontent.com/58457978/70213828-a7b18000-177d-11ea-8ca4-e0643be9d643.png)

출력은 상품을 나타내는 신호 G0, G1이 있고, 반환되는 잔돈으로 100원짜리 동전의 개수를 이진수로 나타내는 신호 C2, C1, C0이 있다. 
그리고 G0 = 1이면 600원 짜리 음료가 출력되고 G1 = 1이면 700원 짜리 음료수가 출력된다. G1, G0 두 신호가 한꺼번에 1이 되는 경우는 없다. C2C1C0 = 000이면 반환되는 거스름돈이 없음을 의미하고, C2C1C0=001, 010, 011, 100, 101, 110이면 각각 거스름돈이 1개, 2개, 3개, 4개, 5개, 6개임을 의미한다. 

## 4. 내용
### 카노맵을 통해 구한 논리값
#### 1. G0 = 1 (S = 0)
G0 = ~S&((N2&N1)|(N3&N2)|(N3&N1)|(N3&N0))
간략화 G0 = ~S&((N2&N1)|(N3&(N2|N1|N0)))

#### 2. G1 = 1 (S = 1)
G1 = S&((N3&N2)|(N3&N1)|(N2&N1&N0))
간략화 G1 = S&((N3&(N2|N1))|(N2&N1&N0))

#### 3. C2 = 1
C2 = ((N3&N2&(~S&N0))|(N3&N2&N1))
간략화 C2 = N3&N2&((~S&N0)|N1)

#### 4. C1 = 1
C1 = ((N3&~S&N1&N0)|(N3&N2&~N1&S)|(N3&N2&~N1&~S))
간략화 C1 = N3&((~S&N1&N0)|(N2&~N1&(S|~N0)))

#### 5. C0 = 1
C0 =  (~S&N3&N1&~N0)|(S&N3&N1&N0)|(S&N3&N2&N0)|(~S&N3&N2&~N1&~N0)|(~S&~N3&N2&N1&N0)


## 5. 코드
<pre><code>module vending_machine(S,N3,N2,N1,N0,G0,G1,C2,C1,C0);

input S,N3,N2,N1,N0;
output G0,G1,C2,C1,C0;

assign G0 = ~S&((N2&N1)|(N3&(N2|N1|N0)));
assign G1 = S&((N3&(N2|N1))|(N2&N1&N0));
assign C2 = N3&N2&((~S&N0)|N1);
assign C1 = N3&((~S&N1&N0)|(N2&~N1&(S|~N0)));
assign C0 =  (~S&N3&N1&~N0)|(S&N3&N1&N0)|(S&N3&N2&N0)|(~S&N3&N2&~N1&~N0)|(~S&~N3&N2&N1&N0);

endmodule</code></pre>

## 6. 결과
### 1. 
![image](https://user-images.githubusercontent.com/58457978/70213968-e6dfd100-177d-11ea-855b-e92d199f9b45.png)
S=N3=N2=N1=N0=1
넣은 돈 : 1200원
음료수 : 700원짜리 음료수
거스름돈 : 1200원 – 700원 = 500원

### 2.
![image](https://user-images.githubusercontent.com/58457978/70213971-e8a99480-177d-11ea-86f6-2a71589fab39.png)
S=N3=N2=1, N1=N0=0
넣은 돈 : 900원
음료수 : 700원짜리 음료수
거스름돈 : 900원 – 700원 = 200원
