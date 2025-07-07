#AT90 Microcontroller Lab Climate control free running mode

Exercise 1 – Free running mode

Write a C program for the AT90 that accomplishes the following tasks:

1. The ADC is configured to free running mode, 8-bit resolution, input from potentiometer 1
(ADC2).

2. The LEDs (PORTC) are all turned on

3. When potentiometer 1 increases, LEDs will be turned off from left to right.

4. When potentiometer 1 decreases, LEDs will be turned on from right to left.

5. The program continues from step 3.

/*
*	GccApplication1.c
 *
*	Created: 27/03/2025 12:04:30 pm
*	Author : 

*/ 
#define F_CPU 8000000L

#include <avr/io.h>

#include <util/delay.h>

// Function to initialize ADC

/*
*ADC Initialization (ADC_init)

*The ADC reference voltage is set to AVCC (5V).

*The ADC is set to free-running mode, meaning it continuously samples data.

*The prescaler is set to 128, which is needed for an 8 MHz clock to achieve a stable ADC conversion.

*The ADC input is set to ADC2, meaning it reads values from the potentiometer connected to that channel.

*The ADC is configured to 8-bit resolution, using left-adjusted results.

*The ADC conversion starts immediately

*/
void ADC_init() {

// Set ADC reference to AVCC (5V) ADMUX = (1 << REFS0);

// Set ADC to free running mode

// Enable ADC, set prescaler to 128 for 16MHz clock

ADCSRA = (1 << ADEN) | (1 << ADATE) | (1 << ADPS2) | (1 << ADPS1) | (1 << ADPS0);

// Select ADC2 (potentiometer 1)

ADMUX &= ~(0x1F);  // Clear previous channel selection

ADMUX |= 2;        // Select ADC2

// Set 8-bit resolution (left-adjusted result)

ADMUX |= (1 << ADLAR);

// Start first conversion

ADCSRA |= (1 << ADSC);
}

/*

*Main Function (main)

*PORTC is configured as output, controlling LEDs.

*Initially, all LEDs are turned on (PORTC = 0xFF).

*The ADC is initialized to start free-running mode.

*The while loop continuously reads values from the ADC and adjusts the LEDs accordingly.

*/ int main() {

// Configure PORTC as output for LEDs

DDRC = 0xFF;  // Set all PORTC pins as outputs

// Initialize all LEDs on

PORTC = 0xFF;

// Initialize ADC ADC_init();

// Previous ADC value to track changes uint8_t prev_adc_value = 0;
while (1) {

// Read 8-bit ADC value (left-adjusted) uint8_t current_adc_value = ADCH;

// Check if potentiometer value has changed if (current_adc_value != prev_adc_value) {

// Compare with previous value to determine direction if (current_adc_value > prev_adc_value) {

// Potentiometer increased - turn off LEDs from left to right

// Use a right shift to turn off LEDs
PORTC >>= 1;

// Ensure at least one LED remains on if (PORTC == 0x00) {
PORTC = 0x01;  // Keep rightmost LED on
}

} else {

// Potentiometer decreased - turn on LEDs from right to left

// Use a left shift to turn on LEDs
PORTC <<= 1;

// Wrap around if all LEDs are off if (PORTC == 0x00) {
PORTC = 0x01;  // Start with rightmost LED
}

// Ensure we don't exceed 8 LEDs if (PORTC > 0xFF) {
PORTC = 0xFF;  // Cap at all LEDs on
}
}
// Update previous ADC value prev_adc_value = current_adc_value;
}

// Small delay to prevent too rapid updates
_delay_ms(50);
}
return 0;
}
