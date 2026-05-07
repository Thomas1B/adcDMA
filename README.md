# STM ADC with DMA 


Simple ADC with DMA example for STM32F446RE.

Reads 2 pots and the internal temperature sensor.

## ADC & DMA Settings
1. Pick what ADCs you want to use.
2. Under ADC# > Parameter Settings:
3. Set "Scan Conversion Mode" and "Continous Scan Mode" to enabled.
4. Set "Number Of Conversions" to the number of ADCs your are reading.
5. Update each Rank with an ADC Channel and Sampling time (use excel helper script).
6. Under DMA Settings, add the ADC you are using.
7. Under Parameter Settings, set "DMA Continuous Requests" to enabled.

## Code Setup

In USER BEGIN Includes
```C
#include <stdio.h>
```

In USER CODE BEGIN PV
```C
volatile uint16_t adcBuffer[3]; // Buffer to hold ADC values for 3 channels
```

In USER CODE BEGIN 0
```C
float analogToTemperature(unsigned int rawVal) {
	// Convert raw ADC value from the internal temperature sensor into °C
	// Formula:
	//  - rawVal / 4095.0   → normalize 12-bit ADC (0..4095) to 0..1
	//  - * 3.3             → convert to voltage (assuming Vref = 3.3V)
	//  - - 0.76            → subtract voltage at 25 °C (typical = 0.76 V)
	//  - / 0.0025          → divide by slope (2.5 mV/°C)
	//  - + 25              → add reference temperature (25 °C)
	return ((((rawVal / 4095.0) * 3.3) - 0.76) / 0.0025) + 25;
}

float analogToVoltage(unsigned int rawVal) {
	// Convert the raw ADC value to voltage
	// Assuming a 12-bit ADC with a reference voltage of 3.3V
	return ((float) rawVal) * 3.3 / 4095;
}
```

In USER CODE BEGIN 2
```C
HAL_ADC_Start_DMA(&hadc1, (uint32_t*) adcBuffer, 3); // Start ADC in DMA mode with a buffer of size 3
```

In USER CODE BEGIN 3
```C
float pot0Voltage = analogToVoltage(adcBuffer[0]);
float pot1Voltage = analogToVoltage(adcBuffer[1]);
float temperature = analogToTemperature(adcBuffer[2]);

printf(
    "pot0Voltage: %.2f V, pot1Voltage: %.2f V, temperature: %.2f °C\r\n",
    pot0Voltage, pot1Voltage, temperature);
HAL_Delay(1000); // Delay for 1 second before printing values again
```

In USER CODE BEGIN 4
```C
int __io_putchar(int ch) {
	ITM_SendChar(ch);
	return ch;
}
```
This allows printing to the SMV Data Console in debug mode.

