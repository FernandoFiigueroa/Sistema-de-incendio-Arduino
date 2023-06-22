```
#include <IRremote.h> 
#include <LiquidCrystal.h> 
#include <Servo.h>
#define Tecla_Uno 0xFF00BF00
#define remoto 11
#define Led_Red1 12
#define Led_Green 10
#define IR 11


Servo PinControlServo; //le doy una nombre a la variable del objeto Servo
LiquidCrystal lcd(4,5,6, 7, 8, 9); //defino los pines a los cuales esta conectado (rs,e(enable),d4,d5,d6,d7)

   
void setup()
{
  pinMode(Led_Red1, OUTPUT);
  pinMode(Led_Green, OUTPUT);
  PinControlServo.attach(3); //conecto el servo motor al pin 3
  Serial.begin(9600);
  lcd.begin(16, 2); //inicia la interfaz del lcd, determina sus dimensiones(ANCHO, ALTO) 
  IrReceiver.begin(IR, DISABLE_LED_FEEDBACK); 
}


void loop()
{	
	 
      int lecturaSensor = analogRead(A0); //el sensor recibe la temperatura
      int conversor;
      conversor = map(lecturaSensor,20,358,-40,125); //convierte lecturaSensor en un numero de -40 a 125
      funcionPrincipal(conversor);
      Serial.println(conversor);
      delay(1000);	
}


void funcionPrincipal(int conversor)
{
//mi funcion principal, dependiendo de la temperatura del conversor me ubico en la estacion del año y hago
// que mi servomotor gire
     if (conversor <= 0)
      {
        lcd.setCursor(0,0); //ubicados en el eje X y Y
        lcd.print("INVIERNO");
        darGiroServo(180,0,6000,1);

      }
      else if (conversor > 0 && conversor < 20)
      {
        lcd.setCursor(0,0);
        lcd.print("OTONIO");
        mostrarTemperatura(conversor);
          darGiroServo(0,90,5000,1);

      }
      else if (conversor >= 20 && conversor < 30)
      {
        lcd.setCursor(0,0);
        lcd.print("PRIMAVERA");
        mostrarTemperatura(conversor);
        darGiroServo(0,90,4000,1);

      }
      else if (conversor >= 30 && conversor < 50)
      {
        lcd.setCursor(0,0);
        lcd.print("VERANO");
        mostrarTemperatura(conversor);
        darGiroServo(0,180,3000,1);
      }

      else if (conversor > 60)
      {

          lcd.setCursor(0,0);
          lcd.print("!!!INCENDIOOO");
          mostrarTemperatura(conversor);	
          darGiroServo(0,200,500,0);		
      }
}
    
    
void darGiroServo(int primerGiro ,int segundoGiro, int tiempo,int bandera)
{
   //funcion que hace dar vueltas a mi servo en determinado tiempo 
 	 PinControlServo.write(primerGiro);
     delay(tiempo);
     PinControlServo.write(segundoGiro);
     delay(tiempo);
  if (bandera == 1)
  {
    prendeYApagaLed(Led_Green,1000,1);
  }
  else
  {
   prendeYApagaLed(Led_Red1,1000,1);
  }
}

void mostrarTemperatura(int conversor)
  // imprimo en el lcd la temperatura que recibe el sensor
{
    lcd.setCursor(0,1);
    lcd.print("temperatura : ");
   lcd.setCursor(13,1); //
    lcd.print(conversor);
}


void prendeYApagaLed(int led, int tiempo, int cantidadRepeticiones)
{
  //prendo el led la cantidad de tiempo y repeticion segun lo pase por parametro
  for (int i=0; i < cantidadRepeticiones;i++)
  {
  	digitalWrite(led,HIGH);
    delay(tiempo);
    digitalWrite(led, LOW);
    delay(0);
  }
}```