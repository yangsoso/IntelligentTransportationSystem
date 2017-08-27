#include <stdio.h>
#include <avr/io.h>
#include <util/delay.h>
#include "cc2500_lib_avr.c"
#include "usart.c"
#include "lcd_lib.c"


uint8_t noofveh,i;
uint8_t vehid[10];


//adjust LCDsendChar() function for stream
static int LCDsendstream(char c, FILE *stream);
//----set output stream to LCD-------
static FILE lcd_str = FDEV_SETUP_STREAM(LCDsendstream, NULL, _FDEV_SETUP_WRITE);


static int LCDsendstream(char c , FILE *stream)
{
LCDsendChar(c);
return 0;
}


int main(void)
	{
	  stdout = &lcd_str;
  
   	LCDinit();
   LCDGotoXY(0,0);
   printf("TOLL UNIT");

	_delay_ms(100);
	USARTInit();
	init_cc2500();


	while (TRUE)
        {
		rf_transmitt('1','T',0);
		
		jhjhj = rxRecvPacket(data,&packetLength);
	   	
		if (jhjhj == RX_OK)
			{
			LCDGotoXY(14,1);
			LCDsendChar ( data[1] );
			LCDsendChar ( data[2] );
			if((data[1] == '1') && (data[2] == 'T'))
				{
				USART_WriteChar('M');	
				USART_WriteChar('H');	
				USART_WriteChar('1');	
				USART_WriteChar('2');	
				USART_WriteChar('C');	
				USART_WriteChar('F');	
				USART_WriteChar('7');	
				USART_WriteChar('7');	
				USART_WriteChar('3');	
				USART_WriteChar('4');	
				USART_WriteChar(0x0d);	
				USART_WriteChar('A');	
				USART_WriteChar('B');	
				USART_WriteChar('C');	
				USART_WriteChar('D');	
				USART_WriteChar(' ');	
				USART_WriteChar('T');	
				USART_WriteChar('O');	
				USART_WriteChar(' ');	
				USART_WriteChar('X');
				USART_WriteChar('Y');	
				USART_WriteChar('Z');
				USART_WriteChar(0x0d);	
				USART_WriteChar('2');
				USART_WriteChar('0');	
				USART_WriteChar('0');
				USART_WriteChar(' ');	
				USART_WriteChar('R');
				USART_WriteChar('S');	
				USART_WriteChar('/');
				USART_WriteChar('-');	
				USART_WriteChar(0x0d);
				}
			_delay_ms(1000);
			RX_LED_OFF;
			}
		else
			{
			LCDGotoXY(14,1);
	   	    printf("E ");
			RX_LED_OFF;
			}
		
		
		
		
		}
	return 0;
	}
