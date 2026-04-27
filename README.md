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

