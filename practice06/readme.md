# Lab 06
## 실습 내용
### **7 – Segment Display Decoder (개별)**
#### **Submodule 1** : 0~9의 값을 갖는 4bit 입력 신호를 받아 7bit FND  segment  값 출력

module	fnd_dec(o_seg,
		i_num);



output	[6:0]	o_seg		;	// {o_seg_a, o_seg_b, ... , o_seg_g}

input	[3:0]	i_num		;

reg [6:0] o_seg;


always @(i_num)  begin

case (i_num) 

	4'd0 : {o_seg} = 7'b1111110;
	4'd1 : {o_seg} = 7'b0110000;
	4'd2 : {o_seg} = 7'b1101101;
	4'd3 : {o_seg} = 7'b1111001;

	4'd4 : {o_seg} = 7'b0110011;
	4'd5 : {o_seg} = 7'b1011011;
	4'd6 : {o_seg} = 7'b1011111;
	4'd7 : {o_seg} = 7'b1110000;

	4'd8 : {o_seg} = 7'b1111111;
	4'd9 : {o_seg} = 7'b1110011;

default : {o_seg} = 7'b0000000;


    
endcase

end 

endmodule


#### **Submodule 2** : 0~59의 값을 갖는 6bit 입력 신호를 받아 십의 자리 수와 일의 자리 수를 각각 4bit으로 출력


module	double_fig_sep(
				o_left,
				o_right,
				i_double_fig);

output	[3:0]	o_left		;
output	[3:0]	o_right		;

input	[5:0]	i_double_fig;
 
assign	o_left = i_double_fig / 10;
assign	o_right  = i_double_fig % 10;

endmodule


#### **Top Module** : 저번 시간에 만든 second counter  및 Submodule 1/2를 이용하여 실습 장비의 LED에 맞는 Display Module 설계
module	led_disp(
				o_seg,
				o_seg_dp,
				o_seg_enb,
				i_six_digit_seg,
				i_six_dp,
				clk,
				rst_n);

output	[5:0]	o_seg_enb		;
output			o_seg_dp		;
output	[6:0]	o_seg			;

input	[41:0]	i_six_digit_seg	;
input	[5:0]	i_six_dp		;
input			clk				;
input			rst_n			;

		
wire	gen_clk		;

nco		u_nco(		.o_gen_clk	( gen_clk		),
				.i_nco_num	(32'd5000000),
				.clk		( clk			),
				.rst_n		( rst_n			));

reg		[3:0]	cnt_common_node	;
always @(posedge gen_clk or negedge rst_n) begin
	if(rst_n == 1'b0) begin
		cnt_common_node		<= 32'd0;
	end else begin
		if(cnt_common_node >= 4'd5) begin
			cnt_common_node 	<= 4'd0;
		end else begin
			cnt_common_node <= cnt_common_node + 1'b1;
		end
	end
end

reg		[5:0]	o_seg_enb		;
always @(cnt_common_node) begin
	case (cnt_common_node)
		4'd0 : o_seg_enb = 6'b111110;
		4'd1 : o_seg_enb = 6'b111101;
		4'd2 : o_seg_enb = 6'b111011;
		4'd3 : o_seg_enb = 6'b110111;
		4'd4 : o_seg_enb = 6'b101111;
		4'd5 : o_seg_enb = 6'b011111;
	endcase
end

reg				o_seg_dp		;
always @(cnt_common_node) begin
	case (cnt_common_node)
		4'd0 : o_seg_dp = i_six_dp[0];
		4'd1 : o_seg_dp = i_six_dp[1];
		4'd2 : o_seg_dp = i_six_dp[2];
		4'd3 : o_seg_dp = i_six_dp[3];
		4'd4 : o_seg_dp = i_six_dp[4];
		4'd5 : o_seg_dp = i_six_dp[5];
	endcase
end

reg		[6:0]	o_seg			;
always @(cnt_common_node) begin
	case (cnt_common_node)
		4'd0 : o_seg = i_six_digit_seg[6:0];
		4'd1 : o_seg = i_six_digit_seg[13:7];
		4'd2 : o_seg = i_six_digit_seg[20:14];
		4'd3 : o_seg = i_six_digit_seg[27:21];
		4'd4 : o_seg = i_six_digit_seg[34:28];
		4'd5 : o_seg = i_six_digit_seg[41:35];
	endcase
end

endmodule

### FPGA 실습 (팀) : 6개의 LED 중 가장 오른쪽 2개의 LED에 1초간격으로 0~59까지 증가하는 Counter 값 Display
: NCO(Numerical Controlled Oscillator) 입력 바꿔서 4초 간격으로 증가하는 코드 테스트



```verilog
wire [41:0] carry;

assign carry= { {4{7'b0000000}},left_1,right_1 };
```
               
                 
endmodule

## 퀴즈 ### 아래 코드 일부를 수정하여 다음을 구하시오 

```verilog 
wire  [41:0] six_digit_seg; 
assign       six_digit_seg = { 4{7'b0000000}, seg_left, seg_right } 
``` 
> Q1 - 고정 LED (왼쪽 4개) AAAA 출력 

: `AA_AA_00`, `AA_AA_01`, `AA_AA_02`, … 순으로 LED 변경

```verilog
assign       six_digit_seg = { 4{7'b0000000}, seg_left, seg_right }
```

> Q2 - 고정 LED 없이 2개의 LED 단위로 1초 Counter 값 표시 

: `00_00_00`, `01_01_01`, `02_02_02`, … 순으로 LED 변경

```verilog
assign       six_digit_seg = { 4{7'b0000000}, seg_left, seg_right }
```
## 결과 ### **Top Module 의 DUT/TestBench Code 및 Waveform 검증**

![]()

### **FPGA 동작 사진 (3개- 일반, Q1, Q2)**
![]()
![]()
![]()


