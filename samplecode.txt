// include the library code:
#include <LiquidCrystal.h>
const int InSwitch = 2;
const int OutSwitch = 3;

const int rs = 12, en = 11, d4 = 10, d5 = 9, d6 = 8, d7 = 7;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
int LCDUpdate;
const char Protocol[10][4]={"NoIO","ANAL","I2C ","SPI ","UART","PAR ","CAN ","LIN "};
const char Ascii[]={'0','1','2','3','4','5','6','7','8','9','A','B','C','D','E','F'};
unsigned int In,Out;
void WelcomeMsg();
void Display();
void Keypad();
void Convert();
void setup() {
Serial.begin(9600);
lcd.begin(16,2);
pinMode(InSwitch, INPUT);
pinMode(OutSwitch, INPUT);
In=Out=0;
WelcomeMsg();
}

void loop() {
Keypad();
Display();
Convert();
}
void Convert()
{if(In==1)//Analog is the Input
{ int sensorValue = analogRead(A0);
  if(Out==5)//Parallel Output
  {int D1,D2,D3,D4;
   // D1=sensorValue&0x000f;D2=sensorValue>>4&0x000f;D3=sensorValue>>8&0x000f;D4=sensorValue>>12&0x000f;
    D1=sensorValue%10;
    D4=sensorValue/1000;
    D3=(sensorValue-D4*1000-D1)/100;
    D2=(sensorValue-(D4*1000+D1+D3*100))/10;
    lcd.setCursor(12, 0);lcd.print(Ascii[D4]);
    lcd.setCursor(13, 0);lcd.print(Ascii[D3]);
    lcd.setCursor(14, 0);lcd.print(Ascii[D2]);
    lcd.setCursor(15, 0);lcd.print(Ascii[D1]);
  }
  if(Out==4)//UART Output
  {
  int sensorValue = analogRead(A0);
  // print out the value you read:
   Serial.println(sensorValue);
   }
  
}
}
void Keypad()
{if(digitalRead(InSwitch)==HIGH){delay(500);In=In+1;if(In>7){In=0;}LCDUpdate=1;Display();}
 if(digitalRead(OutSwitch)==HIGH){delay(500);Out=Out+1;if(Out>7){Out=0;}LCDUpdate=1;Display();}
}
void Display(){
if(LCDUpdate)
  {
  lcd.setCursor(3, 1);lcd.print(Protocol[In][0]);lcd.print(Protocol[In][1]);lcd.print(Protocol[In][2]);lcd.print(Protocol[In][3]);
  lcd.setCursor(12, 1);lcd.print(Protocol[Out][0]);lcd.print(Protocol[Out][1]);lcd.print(Protocol[Out][2]);lcd.print(Protocol[Out][3]);
  LCDUpdate=0;
  }
}
void WelcomeMsg(){
  lcd.setCursor(3, 0);lcd.print("Universal");lcd.setCursor(2, 1);lcd.print("Protocl Conv.");
  delay(1000); lcd.clear(); 
  lcd.setCursor(0, 0); lcd.print("ProConv.P:");
  lcd.setCursor(0, 1); lcd.print("IN:"); lcd.setCursor(8, 1);
  lcd.print("OUT:"); delay(1000); 
  lcd.setCursor(3, 1);lcd.print(Protocol[In][0]);lcd.print(Protocol[In][1]);lcd.print(Protocol[In][2]);lcd.print(Protocol[In][3]);
  lcd.setCursor(12, 1);lcd.print(Protocol[Out][0]);lcd.print(Protocol[Out][1]);lcd.print(Protocol[Out][2]);lcd.print(Protocol[Out][3]);
  LCDUpdate=0;
  }
