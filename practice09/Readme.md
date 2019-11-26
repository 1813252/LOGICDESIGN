# Lab09

## 실습내용 
### **ir_rx**

#### **submodule1**  : ir_rx =32bit data를 출력하는  적외선 리모콘 

#### **submodule2** : fnd_dec= 리모콘 신호를 6자리수로 출력 

#### **submodule3** : led_disp =led화면으로 출력 

### 결과   #**wave form 과 fpga 작동 사진**

#### modelsim의 waveform
![](https://github.com/1813252/LOGICDESIGN/blob/master/practice09/practice10_wave.png)

:Sequentail Rx Bits
```verilog 
wire		ir_rx		;
assign		ir_rx = ~i_ir_rxb; //신호가 반대로 들어오므로 

reg	[1:0]	seq_rx				;
always @(posedge clk_1M or negedge rst_n) begin
	if(rst_n == 1'b0) begin
		seq_rx <= 2'b00;
	end else begin
		seq_rx <= {seq_rx[0], ir_rx}; //{현상태,들어오는 신호}
  end
 end
```
:Count Signal Polarity (High&Low) 
```verilog
reg	[15:0]	cnt_h		;
reg	[15:0]	cnt_l		;
always @(posedge clk_1M or negedge rst_n) begin
	if(rst_n == 1'b0) begin
		cnt_h <= 16'd0;
		cnt_l <= 16'd0;
	end else begin
		case(seq_rx)
			2'b00	: cnt_l <= cnt_l + 1; //'00'일때는 count_low+1 
			
			2'b01	: begin //'01'일때 다시셈 
				cnt_l <= 16'd0; 
				cnt_h <= 16'd0;
			end
			2'b11	: cnt_h <= cnt_h + 1; //'11'일때 count_high+1
			
		endcase
	end
end
```
:State Machine 
```verilog 
always @(posedge clk_1M or negedge rst_n) begin
	if(rst_n == 1'b0) begin
		state <= IDLE; //리셋되면 00으로 됨 
		cnt32 <= 6'd0; //32bit data를 세는 새로운 변수 
	end else begin
		case (state)
			IDLE: begin //00일때
				state <= LEADCODE; 
				cnt32 <= 6'd0; 
			end
			LEADCODE: begin //'01'일때 leadcode구간
				if (cnt_h >= 8500 && cnt_l >= 4000) begin //high가 9ms 보다 길게 들어오고 low가 4.5ms 보다 짧게 들어올때
					state <= DATACODE; //leadercode가 끝남 
				end else begin
					state <= LEADCODE; //그전까진 leadercode 
				end
			end
			DATACODE: begin //'10'일때 datacode구간
				if (seq_rx == 2'b01) begin 
					cnt32 <= cnt32 + 1;  //32bit들어오는지 셈 
				end else begin
					cnt32 <= cnt32; 
				end
				if (cnt32 >= 6'd32 && cnt_l >= 1000) begin //32bit 다 들어오고 
					state <= COMPLETE;
				end else begin
					state <= DATACODE;
				end
			end
			COMPLETE: state <= IDLE; //11일때
		endcase
```

:32bit Custom & Data Code 

```verilog
case (state)
	DATACODE: begin
		if (cnt_l >= 1000) begin
				data[32-cnt32] <= 1'b1;  //low가 1.69ms일때는 bit1
			end else begin
			        data[32-cnt32] <= 1'b0; //low가 0.565ms일때는 bit0
			end
```




#### 8을 누를때 
![](https://github.com/1813252/LOGICDESIGN/blob/master/practice09/button8.jpg)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTgxMDc2NDY5NiwyNDMyNzE2NzRdfQ==
-->
