In today's lab we learned how to utilize the serial monitor to send and receive
characters from the Arduino IDE to the STM32 Discovery board. This was a perfect
demonstration of how the messaging process works on the Discovery board.

Once again I had to use the following process to upload my code to the Discovery
board.
Go into the Project Explorer and right click:
    Clean project>Build Project>Target>Program chip
Then click the black reset button on the board and your most recent code should run.

Additionally to access the serial monitor through the Arduino IDE I performed the
following to refresh the serial monitor with every new build of my code:
    Tools>Serial Monitor
    
The following is the final code I used for the LED delay for each character send:

```
int main(void)
{
	HAL_Init();
	/* Configure the System clock to have a frequency of 80 MHz */
	SystemClock_Config();

	/* Configure UART4 */
	Configure_USART();

	/* Configure the User LED */
	BSP_LED_Init(LED2);
	BSP_LED_Off(LED2);

	uint32_t PrevTicks = HAL_GetTick();
	uint32_t CurrentTicks = HAL_GetTick();

	char* startText= "\n{\"Action\":\"Debug\",\"Info\":\"Testing UART4\"}\n";
	SendCharArrayUSART4(startText,strlen(startText));
	int strcnt = 0;
	int timelapse = 0;
	/* Infinite loop */
	while(1){
		CurrentTicks = HAL_GetTick();
		if (haveString){
				strcnt = strlen(receivedString);
				timelapse = strcnt*500;
				BSP_LED_On(LED2);
				PrevTicks = HAL_GetTick();
				SendCharArrayUSART4(receivedString,strcnt);
				SendCharArrayUSART4("\n",strlen("\n"));
				haveString = 0;
		}
		else{
			if((CurrentTicks-PrevTicks) >= timelapse){
				BSP_LED_Off(LED2);
				strcnt = 0;
			}
		}
	}
}
```
