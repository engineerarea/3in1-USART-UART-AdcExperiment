/////# 3in1-AdcExperiment



#include "stm32f10x.h"                  // Device header
#include "stm32f10x_usart.h"            // Keil::Device:StdPeriph Drivers:USART
#include "stm32f10x_adc.h"              // Keil::Device:StdPeriph Drivers:ADC

void GpioConfig(void);
void UartConfig(void);
void UartTransmit(char *string);

char test[23]="<<Hello World>>";
uint16_t Data=0;
uint16_t AdcValue=0;
char message[20];


void GpioConfig(){
	
GPIO_InitTypeDef  GpioStructure;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOA,ENABLE);
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOB,ENABLE);
	
	///PA9 TX pin
	GpioStructure.GPIO_Mode=GPIO_Mode_AF_PP;
	GpioStructure.GPIO_Pin=GPIO_Pin_9;
	GpioStructure.GPIO_Speed=GPIO_Speed_50MHz;
	
	GPIO_Init(GPIOA,&GpioStructure);

	
	///PA10 RX pin
	
	GpioStructure.GPIO_Mode=GPIO_Mode_AF_PP;
	GpioStructure.GPIO_Pin=GPIO_Pin_10;
	GPIO_Init(GPIOA,&GpioStructure);

//	///led OUT
//	GpioStructure.GPIO_Mode=GPIO_Mode_Out_PP;
//	GpioStructure.GPIO_Pin=GPIO_Pin_0;
//	GpioStructure.GPIO_Speed=GPIO_Speed_50MHz;
//	
//   GPIO_Init(GPIOB,&GpioStructure);



	GpioStructure.GPIO_Mode=GPIO_Mode_AIN;
	GpioStructure.GPIO_Pin=GPIO_Pin_1;
	GPIO_Init(GPIOA,&GpioStructure);
}

void UartConfig(){
	USART_InitTypeDef  UartStructure;
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1,ENABLE);
	
	UartStructure.USART_BaudRate=9600;
	UartStructure.USART_HardwareFlowControl=USART_HardwareFlowControl_None;
	UartStructure.USART_Mode=USART_Mode_Tx|USART_Mode_Tx;
	UartStructure.USART_Parity=USART_Parity_No;
	UartStructure.USART_StopBits=1;
	UartStructure.USART_WordLength=USART_WordLength_8b;
	
	
	
	USART_Init(USART1,&UartStructure);
	USART_Cmd(USART1,ENABLE);

}

void UartTransmit(char *string){
	while(*string){
	
	while(!(USART1->SR & 0x00000040))
		USART_SendData(USART1,*string);
	  string++;
	
	}
		


}
void AdcConfig(){

ADC_InitTypeDef  AdcStructure;
	
	
	RCC_APB2PeriphClockCmd(RCC_APB2Periph_ADC1,ENABLE);
	
	AdcStructure.ADC_ContinuousConvMode=ENABLE;
	AdcStructure.ADC_DataAlign=ADC_DataAlign_Right;	
	AdcStructure.ADC_ExternalTrigConv=ADC_ExternalTrigConv_None;
	AdcStructure.ADC_Mode=ADC_Mode_Independent;
	AdcStructure.ADC_NbrOfChannel=1;
	AdcStructure.ADC_ScanConvMode=DISABLE;
	
	
	ADC_Init(ADC1,&AdcStructure);
	ADC_Cmd(ADC1,ENABLE);
	
}

uint16_t ReadAdc(){

  ADC_RegularChannelConfig(ADC1,ADC_Channel_1,1,ADC_SampleTime_55Cycles5);

	ADC_SoftwareStartConvCmd(ADC1,ENABLE);
  
	while(ADC_GetFlagStatus(ADC1,ADC_FLAG_EOC)==RESET);
	

	return ADC_GetConversionValue(ADC1);

}


void delay(uint32_t Time){

while(Time--);
}


int main(){
	
	GpioConfig();
	UartConfig();

while(1){
	
	Data=ReadAdc();
	sprintf(message,"Adc value is %d\r\n",Data);
	UartTransmit(message);
	Delay(3600);
	
	
	
	
	
	
///UartTransmit(test); /////It is only use PA9 and TX mode
//	
//	
//	Data=USART_ReceiveData(USART1);   /////its use Tx and Rx and usart mode should modeRx|mode Tx;   also use Pa9 and Pa10
//	
//	if(Data==1)
//	GPIO_SetBits(GPIOB,GPIO_Pin_0);
//	if(Data==0)
//	GPIO_ResetBits(GPIOB,GPIO_Pin_0);
	

}

}
