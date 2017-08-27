#include <stdio.h>
#include <avr/io.h>
#include <avr/interrupt.h>
#include <util/delay.h>
#include "lcd_lib.c"       
#include "cc2500_lib_avr.c"

#define SetBit(x,y) (x|=(1<<y))
#define ClrBit(x,y) (x&=~(1<<y))

#define FOSC 8000000// Clock Speed
#define BAUD 9600
#define MYUBRR FOSC/16/BAUD-1

#define GREEN_LED1_PORT 				PORTA
#define GREEN_LED1_PIN  				2
#define GREEN_LED1_ON    				ClrBit(GREEN_LED1_PORT, GREEN_LED1_PIN)
#define GREEN_LED1_OFF    				SetBit(GREEN_LED1_PORT, GREEN_LED1_PIN)

#define GREEN_LED2_PORT 				PORTA
#define GREEN_LED2_PIN  				5
#define GREEN_LED2_ON    				ClrBit(GREEN_LED2_PORT, GREEN_LED2_PIN)
#define GREEN_LED2_OFF    				SetBit(GREEN_LED2_PORT, GREEN_LED2_PIN)

#define GREEN_LED3_PORT 				PORTD
#define GREEN_LED3_PIN  				7
#define GREEN_LED3_ON    				ClrBit(GREEN_LED3_PORT, GREEN_LED3_PIN)
#define GREEN_LED3_OFF    				SetBit(GREEN_LED3_PORT, GREEN_LED3_PIN)

#define GREEN_LED4_PORT 				PORTB
#define GREEN_LED4_PIN  				7
#define GREEN_LED4_ON    				ClrBit(GREEN_LED4_PORT, GREEN_LED4_PIN)
#define GREEN_LED4_OFF    				SetBit(GREEN_LED4_PORT, GREEN_LED4_PIN)

#define RED_LED1_PORT 					PORTA
#define RED_LED1_PIN 	 				0
#define RED_LED1_ON    					ClrBit(RED_LED1_PORT, RED_LED1_PIN)
#define RED_LED1_OFF    				SetBit(RED_LED1_PORT, RED_LED1_PIN)

#define RED_LED2_PORT 					PORTA
#define RED_LED2_PIN  					3
#define RED_LED2_ON    					ClrBit(RED_LED2_PORT, RED_LED2_PIN)
#define RED_LED2_OFF    				SetBit(RED_LED2_PORT, RED_LED2_PIN)

#define RED_LED3_PORT 					PORTA
#define RED_LED3_PIN  					6
#define RED_LED3_ON    					ClrBit(RED_LED3_PORT, RED_LED3_PIN)
#define RED_LED3_OFF    				SetBit(RED_LED3_PORT, RED_LED3_PIN)

#define RED_LED4_PORT 					PORTB
#define RED_LED4_PIN  					5
#define RED_LED4_ON    					ClrBit(RED_LED4_PORT, RED_LED4_PIN)
#define RED_LED4_OFF   					SetBit(RED_LED4_PORT, RED_LED4_PIN)

#define YELLO_LED1_PORT 				PORTA
#define YELLO_LED1_PIN  				1
#define YELLO_LED1_ON    				ClrBit(YELLO_LED1_PORT, YELLO_LED1_PIN)
#define YELLO_LED1_OFF    				SetBit(YELLO_LED1_PORT, YELLO_LED1_PIN)

#define YELLO_LED2_PORT 				PORTA
#define YELLO_LED2_PIN  				4
#define YELLO_LED2_ON    				ClrBit(YELLO_LED2_PORT, YELLO_LED2_PIN)
#define YELLO_LED2_OFF    				SetBit(YELLO_LED2_PORT, YELLO_LED2_PIN)

#define YELLO_LED3_PORT 				PORTA
#define YELLO_LED3_PIN  				7
#define YELLO_LED3_ON    				ClrBit(YELLO_LED3_PORT, YELLO_LED3_PIN)
#define YELLO_LED3_OFF    				SetBit(YELLO_LED3_PORT, YELLO_LED3_PIN)

#define YELLO_LED4_PORT 				PORTB
#define YELLO_LED4_PIN  				6
#define YELLO_LED4_ON    				ClrBit(YELLO_LED4_PORT, YELLO_LED4_PIN)
#define YELLO_LED4_OFF   				SetBit(YELLO_LED4_PORT, YELLO_LED4_PIN)

uint8_t i=0,j,data2[200],last_data;

uint16_t greendelay = 2000;

uint8_t EASTCOUNT;WESTCOUNT,NORTHCOUNT,SOUTHCOUNT;


uint16_t  Edelay,Wdelay,Ndelay,Sdelay;


//adjust LCDsendChar() function for stream
static int LCDsendstream(char c, FILE *stream);
//----set output stream to LCD-------
static FILE lcd_str = FDEV_SETUP_STREAM(LCDsendstream, NULL, _FDEV_SETUP_WRITE);

void delay1s(void);
void init(void);
//*****************************************************************************
//
//  Set LCD stream function 
//
//*****************************************************************************
static int LCDsendstream(char c , FILE *stream)
{
LCDsendChar(c);
return 0;
}

//*****************************************************************************
//
//  init AVR
//
//*****************************************************************************




void CALCULATE_DELAY1(void)
	{
	if(EASTCOUNT== 0)
		{
		Edelay = 2000;
		}
	else if(EASTCOUNT== 1)
		{
		Edelay = 4000;
		}
	else if(EASTCOUNT== 2)
		{
		Edelay = 6000;
		}

	else if(EASTCOUNT== 3)
		{
		Edelay = 8000;
		}


	if(WESTCOUNT== 0)
		{
		Wdelay = 2000;
		}
	else if(WESTCOUNT== 1)
		{
		Wdelay = 4000;
		}
	else if(WESTCOUNT== 2)
		{
		Wdelay = 6000;
		}

else if(WESTCOUNT== 3)
		{
				Wdelay = 8000;
		}


	if(SOUTHCOUNT== 0)
		{
		Sdelay = 2000;
		}
	else if(SOUTHCOUNT== 1)
		{
		Sdelay = 4000;
		}
	else if(SOUTHCOUNT== 2)
		{
		Sdelay = 6000;
		}
	else if(SOUTHCOUNT== 3)
		{
		Sdelay = 8000;
		}



	if(NORTHCOUNT== 0)
		{
		Ndelay = 2000;
		}
	else if(NORTHCOUNT== 1)
		{
		Ndelay = 4000;
		}
	else if(NORTHCOUNT== 2)
		{
		Ndelay = 6000;
		}
	else if(NORTHCOUNT== 3)
		{
		Ndelay = 8000;
		}

	_delay_ms(Edelay);
	
	}

void CALCULATE_DELAY2(void)
	{
		if(EASTCOUNT== 0)
		{
		Edelay = 2000;
		}
	else if(EASTCOUNT== 1)
		{
		Edelay = 4000;
		}
	else if(EASTCOUNT== 2)
		{
		Edelay = 6000;
		}

	else if(EASTCOUNT== 3)
		{
		Edelay = 8000;
		}


	if(WESTCOUNT== 0)
		{
		Wdelay = 2000;
		}
	else if(WESTCOUNT== 1)
		{
		Wdelay = 4000;
		}
	else if(WESTCOUNT== 2)
		{
		Wdelay = 6000;
		}

else if(WESTCOUNT== 3)
		{
				Wdelay = 8000;
		}


	if(SOUTHCOUNT== 0)
		{
		Sdelay = 2000;
		}
	else if(SOUTHCOUNT== 1)
		{
		Sdelay = 4000;
		}
	else if(SOUTHCOUNT== 2)
		{
		Sdelay = 6000;
		}
	else if(SOUTHCOUNT== 3)
		{
		Sdelay = 8000;
		}



	if(NORTHCOUNT== 0)
		{
		Ndelay = 2000;
		}
	else if(NORTHCOUNT== 1)
		{
		Ndelay = 4000;
		}
	else if(NORTHCOUNT== 2)
		{
		Ndelay = 6000;
		}
	else if(NORTHCOUNT== 3)
		{
		Ndelay = 8000;
		}

	_delay_ms(Wdelay);
	
	}
void CALCULATE_DELAY3(void)
	{
		if(EASTCOUNT== 0)
		{
		Edelay = 2000;
		}
	else if(EASTCOUNT== 1)
		{
		Edelay = 4000;
		}
	else if(EASTCOUNT== 2)
		{
		Edelay = 6000;
		}

	else if(EASTCOUNT== 3)
		{
		Edelay = 8000;
		}


	if(WESTCOUNT== 0)
		{
		Wdelay = 2000;
		}
	else if(WESTCOUNT== 1)
		{
		Wdelay = 4000;
		}
	else if(WESTCOUNT== 2)
		{
		Wdelay = 6000;
		}

else if(WESTCOUNT== 3)
		{
				Wdelay = 8000;
		}


	if(SOUTHCOUNT== 0)
		{
		Sdelay = 2000;
		}
	else if(SOUTHCOUNT== 1)
		{
		Sdelay = 4000;
		}
	else if(SOUTHCOUNT== 2)
		{
		Sdelay = 6000;
		}
	else if(SOUTHCOUNT== 3)
		{
		Sdelay = 8000;
		}



	if(NORTHCOUNT== 0)
		{
		Ndelay = 2000;
		}
	else if(NORTHCOUNT== 1)
		{
		Ndelay = 4000;
		}
	else if(NORTHCOUNT== 2)
		{
		Ndelay = 6000;
		}
	else if(NORTHCOUNT== 3)
		{
		Ndelay = 8000;
		}

	_delay_ms(Ndelay);
	
	}
void CALCULATE_DELAY4(void)
	{
		if(EASTCOUNT== 0)
		{
		Edelay = 2000;
		}
	else if(EASTCOUNT== 1)
		{
		Edelay = 4000;
		}
	else if(EASTCOUNT== 2)
		{
		Edelay = 6000;
		}

	else if(EASTCOUNT== 3)
		{
		Edelay = 8000;
		}


	if(WESTCOUNT== 0)
		{
		Wdelay = 2000;
		}
	else if(WESTCOUNT== 1)
		{
		Wdelay = 4000;
		}
	else if(WESTCOUNT== 2)
		{
		Wdelay = 6000;
		}

else if(WESTCOUNT== 3)
		{
				Wdelay = 8000;
		}


	if(SOUTHCOUNT== 0)
		{
		Sdelay = 2000;
		}
	else if(SOUTHCOUNT== 1)
		{
		Sdelay = 4000;
		}
	else if(SOUTHCOUNT== 2)
		{
		Sdelay = 6000;
		}
	else if(SOUTHCOUNT== 3)
		{
		Sdelay = 8000;
		}



	if(NORTHCOUNT== 0)
		{
		Ndelay = 2000;
		}
	else if(NORTHCOUNT== 1)
		{
		Ndelay = 4000;
		}
	else if(NORTHCOUNT== 2)
		{
		Ndelay = 6000;
		}
	else if(NORTHCOUNT== 3)
		{
		Ndelay = 8000;
		}

	_delay_ms(Sdelay);
	
	}


void Signal(void)
	{	
	GREEN_LED1_ON;RED_LED1_OFF;YELLO_LED1_OFF;GREEN_LED2_OFF;RED_LED2_ON;YELLO_LED2_OFF;GREEN_LED3_OFF;RED_LED3_ON;YELLO_LED3_OFF;GREEN_LED4_OFF;RED_LED4_ON;YELLO_LED4_OFF;
	CALCULATE_DELAY1();
	GREEN_LED1_OFF;RED_LED1_OFF;YELLO_LED1_ON;GREEN_LED2_OFF;RED_LED2_ON;YELLO_LED2_OFF;GREEN_LED3_OFF;RED_LED3_ON;YELLO_LED3_OFF;GREEN_LED4_OFF;RED_LED4_ON;YELLO_LED4_OFF;
	_delay_ms(3000);
	GREEN_LED1_OFF;RED_LED1_ON;YELLO_LED1_OFF;GREEN_LED2_ON;RED_LED2_OFF;YELLO_LED2_OFF;GREEN_LED3_OFF;RED_LED3_ON;YELLO_LED3_OFF;GREEN_LED4_OFF;RED_LED4_ON;YELLO_LED4_OFF;
	CALCULATE_DELAY2();
	GREEN_LED1_OFF;RED_LED1_ON;YELLO_LED1_OFF;GREEN_LED2_OFF;RED_LED2_OFF;YELLO_LED2_ON;GREEN_LED3_OFF;RED_LED3_ON;YELLO_LED3_OFF;GREEN_LED4_OFF;RED_LED4_ON;YELLO_LED4_OFF;
	_delay_ms(3000);
	GREEN_LED1_OFF;RED_LED1_ON;YELLO_LED1_OFF;GREEN_LED2_OFF;RED_LED2_ON;YELLO_LED2_OFF;GREEN_LED3_ON;RED_LED3_OFF;YELLO_LED3_OFF;GREEN_LED4_OFF;RED_LED4_ON;YELLO_LED4_OFF;
	CALCULATE_DELAY3();
	GREEN_LED1_OFF;RED_LED1_ON;YELLO_LED1_OFF;GREEN_LED2_OFF;RED_LED2_ON;YELLO_LED2_OFF;GREEN_LED3_OFF;RED_LED3_OFF;YELLO_LED3_ON;GREEN_LED4_OFF;RED_LED4_ON;YELLO_LED4_OFF;
	_delay_ms(3000);
	GREEN_LED1_OFF;RED_LED1_ON;YELLO_LED1_OFF;GREEN_LED2_OFF;RED_LED2_ON;YELLO_LED2_OFF;GREEN_LED3_OFF;RED_LED3_ON;YELLO_LED3_OFF;GREEN_LED4_ON;RED_LED4_OFF;YELLO_LED4_OFF;
	CALCULATE_DELAY4();
	GREEN_LED1_OFF;RED_LED1_ON;YELLO_LED1_OFF;GREEN_LED2_OFF;RED_LED2_ON;YELLO_LED2_OFF;GREEN_LED3_OFF;RED_LED3_ON;YELLO_LED3_OFF;GREEN_LED4_OFF;RED_LED4_OFF;YELLO_LED4_ON;
	_delay_ms(3000);
	}




ISR(INT0_vect)
	{
	RX_LED_ON;
	halRfReadFifo(data,5 );
    halRfStrobe(CC2500_SFRX);
	
	if ((data[0] == CC2500_SOURCEADDR_value)  &&  (data[1] == 'S')  )
	    {
	//	LCDGotoXY(0,0);
	//	LCDsendChar(data[1]);
	//	LCDsendChar(data[2]);
	//	LCDsendChar(data[3] + 48);

		if(data[2] == 'E')
			{
			EASTCOUNT = data[3];
			}
		else if(data[2] == 'W')
			{
			WESTCOUNT = data[3];
			}
		else if(data[2] == 'N')
			{
			NORTHCOUNT = data[3];
			}
		else if(data[2] == 'S')
			{
			SOUTHCOUNT = data[3];
			}
		LCDGotoXY(5,0);
	LCDsendChar(EASTCOUNT+48);
	LCDGotoXY(13,0);
	LCDsendChar(WESTCOUNT+48);
	LCDGotoXY(6,1);
	LCDsendChar(NORTHCOUNT+48);
	LCDGotoXY(14,1);
	LCDsendChar(SOUTHCOUNT+48);


       	}

	
	halRfStrobe(CC2500_SRX);
	RX_LED_OFF;
	}




int main(void)
{
	
	stdout = &lcd_str;
	LCDinit();//init LCD bit, dual line, cursor right
	LCDclr();//clears LCD
	LCDGotoXY(0,0);//Cursor Home
	printf("RF TEST");
	delay1s();
	
	DDRA = 255;
	DDRC = 255;
	DDRB = 255;
	DDRD = 0xfc;//|= 0b11111100;



	
	init_cc2500();


	LCDGotoXY(0,0);
	printf("Semi Autonomous");
	LCDGotoXY(0,1);
	printf("    Vehicle    ");



    halRfStrobe(CC2500_SRX);
	PACKET_SENT_INPUT;
	GICR |= (1<<6);   ///int 0 enable
	MCUCR |= (1<<ISC01) ;  ///Falling edge triggered 
	sei();
	

//	rf_transmitt('1');


	LCDGotoXY(0,0);
	printf("EAST:   WEST:   ");
	LCDGotoXY(0,1);
	printf("NORTH:  SOUTH:   ");
	EASTCOUNT = 0;WESTCOUNT = 0;NORTHCOUNT=0; SOUTHCOUNT = 0;

	LCDGotoXY(5,0);
	LCDsendChar(EASTCOUNT+48);
	LCDGotoXY(13,0);
	LCDsendChar(WESTCOUNT+48);
	LCDGotoXY(6,1);
	LCDsendChar(NORTHCOUNT+48);
	LCDGotoXY(14,1);
	LCDsendChar(SOUTHCOUNT+48);

	while(1)
	{
	Signal();
	}

	

		
}
