# PS1_2021_Spinean

#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
int sec=00;
int min=20;
int hr=16;
int i=0;
int j=0;

void setup() {
lcd.begin(16,2);
 adc_init();

}
void adc_init(void) 
{
ADCSRA|=((1<<ADPS2)|(1<<ADPS1)|(1<<ADPS0));
ADMUX |= (1<<REFS0);
ADCSRA |= (1<<ADEN); 
ADCSRA |= (1<<ADSC); 
}

uint16_t read_adc(uint8_t channel) 
{
ADMUX &= 0xF0; //set input A0 to A5
ADMUX |= channel;//select channel A0 to A5
ADCSRA |= (1<<ADSC); //start conversion
while (ADCSRA & (1<<ADSC)); //wait while adc conversion are not updated
return ADC; //read and return
}
float voltage=0;
float temperatureCelsius;
int reading;


void loop() {

lcd.setCursor(0,0);
lcd.print("Temp: ");
lcd.setCursor(6,0);

reading=read_adc(0);
voltage=reading*5.0; 
voltage/=1024.0;
temperatureCelsius=(voltage-0.5)*100;
lcd.print(temperatureCelsius);

lcd.setCursor(0,1);
lcd.print("Ora:");
lcd.setCursor(5,1);
lcd.print(hr);
lcd.setCursor(7,1);
lcd.print(":");
lcd.print(min);
lcd.setCursor(10,1);
lcd.print(":");
lcd.print(sec);
sec=sec+1;
delay(1000);

if(sec == 60){
  sec=00;
  min=min+1; 
}else;
if(min == 60){
  min=0;
  hr=hr+1;
}else;

lcd.clear();
}
