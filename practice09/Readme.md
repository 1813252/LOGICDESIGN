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
		seq_rx <= {seq_rx[0], ir_rx}; //{전상태,들어오는 신호}
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
			2'b00	: cnt_l <= cnt_l + 1;
			2'b01	: begin
				cnt_l <= 16'd0;
				cnt_h <= 16'd0;
			end
			2'b11	: cnt_h <= cnt_h + 1;
		endcase
	end
end
```


#### 8을 누를때 
![](https://github.com/1813252/LOGICDESIGN/blob/master/practice09/button8.jpg)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTgxMDc2NDY5NiwyNDMyNzE2NzRdfQ==
-->
