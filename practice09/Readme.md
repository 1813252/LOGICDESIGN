# Lab09

## 실습내용 
### **ir_rx**

#### **submodule1**  : ir_rx =32bit data를 출력하는  적외선 리모콘 

#### **submodule2** : fnd_dec= 리모콘 신호를 6자리수로 출력 

#### **submodule3** : led_disp =led화면으로 출력 

### 결과   #**wave form 과 fpga 작동 사진**

#### modelsim의 waveform
![](https://github.com/1813252/LOGICDESIGN/blob/master/practice09/practice10_wave.png)
```verilog 
parameter	IDLE		= 2'b00	 
parameter	LEADCODE	= 2'b01	;	// 9ms high 4.5ms low
parameter	DATACODE	= 2'b10	;	// Custom & Data Code
parameter	COMPLETE	= 2'b11	;	// 32-bit data
```



#### 8을 누를때 
![](https://github.com/1813252/LOGICDESIGN/blob/master/practice09/button8.jpg)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTgxMDc2NDY5NiwyNDMyNzE2NzRdfQ==
-->
