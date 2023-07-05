```
#include <IRremote.h> 
#include <LiquidCrystal.h> 
#include <Servo.h>
#define Tecla_Uno 0xEF10BF00
#define Tecla_Dos 0xEE11BF00
#define Tecla_Tres 0xED12BF00
#define Tecla_Cuatro 0xEB14BF00
#define Tecla_Cinco 0xEA15BF00
#define teclaInicio 0xFF00BF00
#define TeclaPausa 0xFD02BF00
#define Led_Red1 12
#define Led_Green 10
#define IR 11

int lecturaSensor,conversor,lecturaSensor1,estado;
bool bandera = false;
char mensaje;


LiquidCrystal lcd(4,5,6, 7, 8, 9); //defino los pines a los cuales esta conectado (rs,e(enable),d4,d5,d6,d7)
IRrecv irrecv(IR); 
decode_results codigo;
Servo PinControlServo;


void setup()
{
  Serial.begin(9600);     
  IrReceiver.begin(IR, DISABLE_LED_FEEDBACK); 
  pinMode(Led_Green, OUTPUT);
  pinMode(Led_Red1, OUTPUT); 
  lcd.begin(16, 2);//columnas y filas
  PinControlServo.attach(3);
}



void loop() 
{
  
 	verificarCasos();
  	
   	if (bandera == true){
    comenzarCodigo(bandera);
    }
    else
    {
    	lcd.clear();
    }
  	
    delay (500);
}



void verificarCasos()
{
	if(IrReceiver.decode()) 
  	{ 
       Serial.println(IrReceiver.decodedIRData.decodedRawData, HEX);  
     	switch(IrReceiver.decodedIRData.decodedRawData)
     	{
          case teclaInicio:
          	bandera = true;
          break;
          case TeclaPausa:
          	bandera = false;
        	break;
          case Tecla_Uno:
          		lcd.clear();
           		lcd.setCursor(0,0);
              lcd.print("INVIERNO");
          	break;
           case Tecla_Dos:
          		lcd.clear();
           		lcd.setCursor(0,0);
              lcd.print("VERANO");
          	break;
           case Tecla_Tres:
            	lcd.clear();	
           		lcd.setCursor(0,0);
              lcd.print("OTONIO");
          	break;
           case Tecla_Cuatro:
         		 lcd.clear();
           		lcd.setCursor(0,0);
              lcd.print("PRIMAVERA");
          	break;
          
        }
   		 IrReceiver.resume();
 	 } 
}



void comenzarCodigo(bool bandera)
{
     	 
 			mostrarTemperatura();
            /*convierte lecturaSensor en un numerode -40 a 125*/	
            if (conversor > 60)
            {
                lcd.setCursor(0,0);
                lcd.print("INCENDIOOO!!!!!!");
                darGiroServo(0,180,900);
                prendeYApagaLed(Led_Red1,1000,1);
               lcd.clear();//Limpio el lcd
            }
            else
            {
              
              prendeYApagaLed(Led_Green,1000,1);
              
            }
  
}
 

void darGiroServo(int primerGiro ,int segundoGiro, int tiempo)
{
   	//	funcion que hace dar vueltas a mi servo en determinado tiempo 
     PinControlServo.write(primerGiro);
     delay(tiempo);
     PinControlServo.write(segundoGiro);
     delay(tiempo);
}


void mostrarTemperatura()
  // imprimo en el lcd la temperatura que recibe el sensor
{
  	lecturaSensor = analogRead(A0); //el sensor recibe la temperatura
  	conversor = map(lecturaSensor,20,358,-40,125);
	mensajeTemperatura(conversor);
}


void mensajeTemperatura(int conversor)
{
  lcd.setCursor(0,1);
  lcd.print("temperatura: ");
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
}
```