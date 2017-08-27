#include "LPC214x.h"
#include "Timer.h"
#include "Uart.h"
#include "Lcd.h"
#include "cc2500_lib_arm.c"
#include "Adc.c"



UINT16 tempcount;
UINT8 GpsData[19];
UINT8 CR = 0x0D;        /////enter for at commands
UINT8 LF = 0x0A;       /////   new line
UINT8 CZ = 26;			/// ctrl Z
UINT8 quote = 34;			/// ctrl Z

UINT8 number1[10]={"9049649825"}; 
UINT16 lathex,longhex,j,i;
UINT8	x_value[5],y_value[5];
UINT16	x_hex,y_hex;


void read_x(void)
{
	x_hex = ADC0Read( 1 );		   ///0.27	 greater than 1 ms  change can b sensed
	tempcount = x_hex;
	x_value[0] =	(tempcount%10) + 48;
	tempcount = tempcount/10;
	x_value[1] =	(tempcount%10) + 48;		///lsb
	tempcount = tempcount/10;
	x_value[2] =	(tempcount%10) + 48;		//
	tempcount = tempcount/10;
	x_value[3] =	(tempcount%10) + 48;		///msb
	}


void read_y(void)
{
	y_hex = ADC0Read( 2 );		   ///0.27	 greater than 1 ms  change can b sensed
	tempcount = x_hex;
	y_value[0] =	(tempcount%10) + 48;
	tempcount = tempcount/10;
	y_value[1] =	(tempcount%10) + 48;		///lsb
	tempcount = tempcount/10;
	y_value[2] =	(tempcount%10) + 48;		//
	tempcount = tempcount/10;
	y_value[3] =	(tempcount%10) + 48;		///msb
	}

void read_gps(void)
	{
	while(1)	  
	{
	 UART1Count = 0;
	 while(UART1Count ==0);
	 if(UART1Buffer[0] == 'R')
		 {
	  	UART1Count = 0;
	  	while(UART1Count != 40);
	  	LCD_Clear();
	  	LCD_Cursor(1,1);	
		LCD_DisplayCharacter('L');	
		LCD_DisplayCharacter('A');	
		LCD_DisplayCharacter('T');	
		LCD_DisplayCharacter(':');	
		j=0;
		for(i = 16;i<25;i++)
		{
		GpsData[j] = UART1Buffer[i];
		LCD_DisplayCharacter(UART1Buffer[i]);	
		j++;
		}
		lathex = (((GpsData[5]-48)*1000)+((GpsData[6]-48)*100)+((GpsData[7]-48)*10)+((GpsData[8]-48)*1));
		LCD_Cursor(2,1);	
		LCD_DisplayCharacter('L');	
		LCD_DisplayCharacter('N');	
		LCD_DisplayCharacter('G');	
		LCD_DisplayCharacter(':');	
		for(i = 28;i<38;i++)
		{
		GpsData[j] = UART1Buffer[i];
		LCD_DisplayCharacter(UART1Buffer[i]);	
		j++;
		}
		longhex = (((GpsData[15]-48)*1000)+((GpsData[16]-48)*100)+((GpsData[17]-48)*10)+((GpsData[18]-48)*1));
		break;
	 }}
	 }

void init_gsm(void)
	{
	UART0Send("A",1);Delay(200);UART0Send("T",1);Delay(200);UART0Send("Z",1);Delay(200);UART0Send(&CR,1);Delay(1000);
	UART0Send("A",1);Delay(200);UART0Send("T",1);Delay(200);UART0Send("Z",1);Delay(200);UART0Send(&CR,1);Delay(1000);
	UART0Send("A",1);Delay(200);UART0Send("T",1);Delay(200);UART0Send("E",1);Delay(200);UART0Send("0",1);Delay(200);UART0Send(&CR,1);Delay(1000);
	UART0Send("A",1);Delay(200);UART0Send("T",1);Delay(200);UART0Send("+",1);Delay(200);UART0Send("C",1);Delay(200);UART0Send("N",1);Delay(200);UART0Send("M",1);Delay(200);UART0Send("I",1);Delay(200);UART0Send("=",1);Delay(200);UART0Send("1",1);Delay(200);UART0Send(&CR,1);Delay(1000);
	UART0Send("A",1);Delay(200);UART0Send("T",1);Delay(200);UART0Send("+",1);Delay(200);UART0Send("C",1);Delay(200);UART0Send("M",1);Delay(200);UART0Send("G",1);Delay(200);UART0Send("F",1);Delay(200);UART0Send("=",1);Delay(200);UART0Send("1",1);Delay(200);UART0Send(&CR,1);Delay(1000);
	}

void sendsms1(void)
 	{
	LCD_Clear();
	LCD_DisplayStringCentered(1,"Sending SmS");

	init_gsm();
	UART0Send("AT+CMGS=",8);
	UART0Send(&quote,1);
	UART0Send(&number1[0],10);
	UART0Send(&quote,1);
	UART0Send(&CR,1);
	Delay(1000);
	UART0Send("Accident",8);
	UART0Send(&CR,1);
	Delay(1000);
	//UART0Send(&dummy[0],25);
	UART0Send("LAT:",4);
	UART0Send(&UART1Buffer[16],9);
	UART0Send("LONG:",5);
	UART0Send(&UART1Buffer[28],10);
	UART0Send(&CR,1);
	UART0Send(&CZ,1);
	LCD_Clear();
	LCD_DisplayStringCentered(1,"SmS SENT");
	Delay(1000);
	Delay(1000);
	Delay(1000);
   }


  void delete_sms(UINT8 loc)
	{
	UART0Send("AT+CMGD=",8);
	UART0Send(&loc,1);
	UART0Send(&CR,1);
	Delay(1000);
 	}


int main( void )
	{
   	PINSEL0 = 0x00000000;																				
	PINSEL1 = 0x00000000;
	PINSEL2 = 0x00000000;
   	IODIR0 = 0xFFFFFFFF;
	IODIR1 = 0xFFFFFFFF;

	Init_Timer( 0 );	Enable_Timer( 0 );
	Init_Timer( 1 );	Enable_Timer( 1 );

    UART0Init(9600);

	UART1Init(4800);


	ADCInit( ADC_CLK );	

	Delay(1000);

	LCD_Init();
	LCD_Clear();
	LCD_DisplayStringCentered(1,"cc2500 init");
	init_cc2500();
	LCD_DisplayStringCentered(1,"cc2500 test");
	
	//check rf request
	//check accident

	while(1)
		{
	
	//	CHECK_RF_REQUEST();
	
		UINT8 jhjhj;
		jhjhj = rxRecvPacket(data,&packetLength);

	   	if (jhjhj == RX_OK)
			{
			LCD_Cursor(2,1);
			LCD_DisplayCharacter(data[1]);
			LCD_DisplayCharacter(data[2]);
			LCD_DisplayCharacter(data[3]);
			LCD_DisplayCharacter(data[4]);
			LCD_DisplayCharacter(data[5]);
			if((data[1] == '1') &&(data[2] == '1') )
			{
			rf_transmitt(data[1],data[2]);
			}
			else if((data[1] == '1') &&(data[2] == 'T') )
				{
				rf_transmitt(data[1],data[2]);
				
				}
		else if((data[1] == '1') &&(data[2] == 'S') )
				{
			
				LCD_DisplayStringCentered(1,"SCHOOL AHEAD");			
				LCD_DisplayStringCentered(2,"DRIVE SLOWLY");			
				 Delay(2000);
				
				}

	

//			while(1);
			}
		else
			{
			LCD_Clear();
			RX_LED_OFF;
			LCD_DisplayStringCentered(1,"ERROR");
	
			}	  /// rf check ends here

///check accelero for accident

		read_x();
		read_y();

		LCD_Clear();
		LCD_Cursor(1,1);

		LCD_DisplayCharacter('X');
		LCD_DisplayCharacter(':');

		LCD_DisplayCharacter(x_value[2]);
		LCD_DisplayCharacter(x_value[1]);
		LCD_DisplayCharacter(x_value[0]);

		LCD_DisplayCharacter(' ');


		LCD_DisplayCharacter('Y');
		LCD_DisplayCharacter(':');
		LCD_DisplayCharacter(y_value[2]);
		LCD_DisplayCharacter(y_value[1]);
		LCD_DisplayCharacter(y_value[0]);
		LCD_DisplayCharacter(' ');


		if(x_hex >= 600)
		{
		read_gps();
		sendsms1();			
		}
		
		}
		}

