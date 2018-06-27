	#include <stdlib.h>
	#include <Math.h>
	#include <LiquidCrystal.h>
	LiquidCrystal lcd(7, 6, 5, 4, 3, 2); 
	//*****************************************************
	String intToString(int32_t value, byte dig_num) {
	char temp[12];
	for (int8_t i = 0; i < 12; i++) { temp[i] = 0; }
	//**********************
	for (int8_t i = dig_num; i > 0; i--)
	{
    temp[i - 1] = 48 + (value % 10);
    value = value / 10;
	}
	return String(temp);
	}
	//*****************************************************
	String floatToString(float value, byte dig_num, byte precision = 2) {
	char temp[12];
	int32_t value1;
	uint8_t j = 0;
	//**********************
	for (int8_t i = 0; i < 12; i++) { temp[i] = 0; }
	//**********************
	for (int8_t i = 0; i < precision; i++) { value = value * 10.0; }
	value1 = value;
	//**********************
	for (int8_t i = dig_num + precision; i > 0; i--)
	{
    temp[i - 1] = 48 + (value1 % 10);
    value1 = value1 / 10.0;
	}
	//**********************
	for (int8_t i = dig_num + precision; i > dig_num; i--)
	{
    temp[i] = temp[i - 1];
    j = i;
	}
	temp[j - 1] = '.';

	return String(temp);
	}

	class Energy_meter
	{
	public:
	Energy_meter(uint8_t pin_volt, uint8_t pin_amp, float volt_div, float 
	amp_div, uint8_t scan = 1)
	{
    this->pin_amp = pin_amp;
    this->pin_volt = pin_volt;
    this->volt_div = volt_div;
    this->amp_div = amp_div;
    this->scan = scan;
	}
	//***********volt_read()************
	float volt_read()
	{
    for (int16_t far = 0; far < 100; far++) {
        float value = analogRead(pin_volt);
        if (volt < value) { volt = value; }
    }
    volt = volt / volt_div;
    return volt;
	}
	//**********amp_read()*************
	float amp_read()
	{
    amp = 0;
    //*****************reading loop*****************
    for (int8_t far1 = 0; far1 < scan; far1++) {
        for (int16_t far = 0; far < 1000; far++) {
            float value = analogRead(pin_amp);
            if (amp < value) { amp = value; }
        }
    }
    //**************amp calibration***************
    amp = amp / amp_div;
    return amp;
	}
	//***********************
	void  read()
	{
    volt_read(); amp_read(); ap = volt * amp;
    watt = ap;
    //**************
	}
	//*****************unit read****************
	float volt = 0, amp = 0, watt = 0, ap = 0;
	float volt_div, amp_div;
	private:
	uint8_t  scan = 1, pin_volt, pin_amp;
	};

	//***********meter 1*****************

	Energy_meter Line1(A0, A3, 331.0 / 229.0, 1.0 / 1.0, 3);
	Energy_meter Line2(A1, A4, 326.0 / 229.0, 1.0 / 1.0, 3);
	Energy_meter Line3(A2, A5, 320.0 / 229.0, 1.0 / 1.0, 3);
	//**********************define**************
	void setup()
	{   
	//***********************lcd init**********************
	lcd.begin(20, 4);// set up the LCD's number of columns and rows:
	lcd.print("Init....");// Print a message to the LCD:
	delay(500);
	lcd.clear();
	}
	//********************main loop star**************
	void loop()
	{
	//********volt amp**********
	Line1.read(); Line2.read(); Line3.read();
	lcd.begin(20, 4);
	//***************************
	lcd.setCursor(0, 0); lcd.print("V=");
	lcd.print(intToString(Line1.volt, 3));
	//**********
	lcd.setCursor(7, 0); lcd.print("V=");
	lcd.print(intToString(Line2.volt, 3));
	//**********
	lcd.setCursor(14, 0); lcd.print("V=");
	lcd.print(intToString(Line3.volt, 3));
	//***************************
	lcd.setCursor(0, 1); lcd.print("A=");
	lcd.print(intToString(Line1.amp,2));
	//**********
	lcd.setCursor(7, 1); lcd.print("A=");
	lcd.print(intToString(Line2.amp,2));
	//**********
	lcd.setCursor(14, 1); lcd.print("A=");
	lcd.print(intToString(Line3.amp,2));
	//***************************
	float ap = Line1.ap + Line2.ap + Line3.ap;
	lcd.setCursor(0, 3); lcd.print("AP=");
	lcd.print(intToString(ap, 5));
	
	Serial.begin(9600);

	Serial.print(intToString(Line1.volt, 3));
	Serial.print(' ');
	Serial.print(intToString(Line2.volt, 3));
	Serial.print(' ');
	Serial.print(intToString(Line3.volt, 3));
	Serial.print(' ');
	Serial.print(intToString(Line1.amp,2));
	Serial.print(' ');
	Serial.print(intToString(Line2.amp,2));
	Serial.print(' ');
	Serial.print(intToString(Line3.amp, 2));
	Serial.print(' ');
	Serial.println(intToString(ap, 5));
	
	delay(2000);
	}
