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
   printf("DUMMY VEH 1");

	_delay_ms(100);
	USARTInit();
	init_cc2500();

	while (TRUE)
        {

	
		
		jhjhj = rxRecvPacket(data,&packetLength);
	   	if (jhjhj == RX_OK)
			{
			LCDGotoXY(0,1);
			LCDsendChar ( data[1] );
			LCDsendChar ( data[2] );
			LCDsendChar ( ' ' );
			LCDsendChar ( ' ' );
			LCDsendChar ( ' ' );
			

			if((data[1] == '1') && (data[2] == '2'))
				{
				rf_transmitt('1','2');
				}
			RX_LED_OFF;
			}
		else
			{
			LCDGotoXY(0,1);
	   	    printf("ERROR");
			RX_LED_OFF;
			}
		
		
	
		
		}
	return 0;
	}
