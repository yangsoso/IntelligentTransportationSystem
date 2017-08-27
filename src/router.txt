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



void calculate_no_of_vehicles(void)
	{
	noofveh = 0;
	for(i=0;i<10;i++)
		{
		if(vehid[i] == 1)
			{
			noofveh++;		
			}
		}
	
	}



int main(void)
	{
	  stdout = &lcd_str;
  
   	LCDinit();
   LCDGotoXY(0,0);
   printf("ROAD SIDE UNIT");

	_delay_ms(100);
	USARTInit();
	init_cc2500();

   LCDGotoXY(0,1);
   printf("No of Veh:%d   ",noofveh);

for(i=0;i<10;i++)
	{
	
	vehid[i] = 0;
	
	}

	while (TRUE)
        {

		LCDGotoXY(15,0);
		LCDsendChar ( '1' );
	
		rf_transmitt('1','1',0);
		jhjhj = rxRecvPacket(data,&packetLength);
	   	if (jhjhj == RX_OK)
			{
			LCDGotoXY(14,1);
			LCDsendChar ( data[1] );
			LCDsendChar ( data[2] );
			if((data[1] == '1') && (data[2] == '1'))
				{
				vehid[0] = 1;
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
		
		
		calculate_no_of_vehicles();
		LCDGotoXY(0,1);
		printf("No of Veh:%d   ",noofveh);
		
		
		LCDGotoXY(15,0);
		LCDsendChar ( '2' );
	
		rf_transmitt('1','2',0);
		jhjhj = rxRecvPacket(data,&packetLength);
	   	if (jhjhj == RX_OK)
			{
			LCDGotoXY(14,1);
			LCDsendChar ( data[1] );
			LCDsendChar ( data[2] );
			if((data[1] == '1') && (data[2] == '2'))
				{
				vehid[1] = 1;
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
		
		
		calculate_no_of_vehicles();
		LCDGotoXY(0,1);
		printf("No of Veh:%d   ",noofveh);
		


		LCDGotoXY(15,0);
		LCDsendChar ( '3' );
	
		rf_transmitt('1','3',0);
		jhjhj = rxRecvPacket(data,&packetLength);
	   	if (jhjhj == RX_OK)
			{
			LCDGotoXY(14,1);
			LCDsendChar ( data[1] );
			LCDsendChar ( data[2] );
			if((data[1] == '1') && (data[2] == '3'))
				{
				vehid[2] = 1;
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
		
		
		calculate_no_of_vehicles();
		LCDGotoXY(0,1);
		printf("No of Veh:%d   ",noofveh);

				


		LCDGotoXY(15,0);
		LCDsendChar ( '4' );
		
		ClrBit(DDRC,0);
		ClrBit(DDRC,1);
		ClrBit(DDRC,2);
		ClrBit(DDRC,3);

		SetBit(PORTC,0);
		SetBit(PORTC,1);
		SetBit(PORTC,2);
		SetBit(PORTC,3);

		if(bit_is_clear(PINC,0))
			{
			rf_transmitt('S','E',noofveh);	
			}
		else if(bit_is_clear(PINC,1))
			{
			rf_transmitt('S','W',noofveh);
			}
		else if(bit_is_clear(PINC,2))
			{
			rf_transmitt('S','N',noofveh);
			}
		else if(bit_is_clear(PINC,3))
			{
			rf_transmitt('S','S',noofveh);
			}


		

		_delay_ms(1000);
		
		
		}
	return 0;
	}
