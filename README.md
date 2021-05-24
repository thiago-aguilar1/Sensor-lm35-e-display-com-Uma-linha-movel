# Sensor-lm35-e-display-com-uma-linha-movel
Termômetro Arduino - teste inicial com sensor lm35 e display lcd 16 x 2 , scrollando apenas a linha inferior. 

Teste / Rascunho inicial.

Usando plataforma Arduino.

Usando conexão I2C no display para o Arduino.

Para esse rascunho, os componentes necessários foram:
- Arduino UNO;
- Módulo I2C;
- Display LCD 16x2;
- Sensor de temperatura LM35.

Aqui tá um teste inicial do LCD 16 x 2, fazendo apenas a linha inferior se movimentar. 
A linha superior do LCD mostra a temmperatura atual medida, e a linha inferior mostra as medidas máximas e mínimas já registradas até então.


Código:

```C++

#include <Wire.h>  
#include <LiquidCrystal_I2C.h>   


LiquidCrystal_I2C lcd(0x27, 2, 1, 0, 4, 5, 6, 7, 3, POSITIVE);  //ENDEREÇO DO I2C E DEMAIS INFORMAÇÕES

int lm35 = A2;


float celsius = 0.00;

float amostra[10];  //Array para precisão na medição

int i;  //contador do medidor de termperatura

float maxTemp = -100.0, minTemp = 100.0;




byte grau[8] = {B00110, B01001, B00110, B00000, B00000, B00000, B00000, B00000,};



//Configuracoes LCD 16x2  
int screenWidth = 16;  
int screenHeight = 2;  



int comecoVisivel = 0;
int corretor = 0;


String linhaEstatica, linhaEstaticaParte1, linhaEstaticaParte2, linhaEstaticaParte3;
String linhaMovel, linhaMovelParte1, linhaMovelParte2, linhaMovelParte3, linhaMovelParte4, linhaMovelParte5, linhaMovelParte6;

int posicaoEstaticaGrau, posicaoMovelGrau1, posicaoMovelGrau2;

int direcao = 1;



   
void setup() 
{  
  pinMode(lm35, OUTPUT);
  
  //Inicializacao do display  
  lcd.begin(screenWidth,screenHeight);  
  lcd.setBacklight(HIGH);    //LIGA O BACKLIGHT (LUZ DE FUNDO)
  lcd.createChar(1, grau);

  
}  


   
void loop() 
{ 
  //Loop que faz a leitura da temperatura 10 vezes
  for (i = 0; i < 10; i++)
  {
    amostra[i] = ( 5.0 * analogRead(lm35) * 100.0) / 1024.0;
    celsius = celsius + amostra[i];
    delay(80);
  }
 
  
  celsius = celsius / 10.0;    //Divide a variavel celsius por 10, para obter precisão na medição

  
  if (celsius > maxTemp) {
    maxTemp = celsius;
  }


  if (celsius < minTemp) {
    minTemp = celsius ;
  }




 /* String linhaEstatica = "  Temp: " + String(celsius,2) + " C";*/

  linhaEstaticaParte1 = "  Temp: ";
  linhaEstaticaParte2 = String(celsius,2);
  linhaEstaticaParte3 = " C";
  linhaEstatica = linhaEstaticaParte1 + linhaEstaticaParte2 + linhaEstaticaParte3;


  posicaoEstaticaGrau = linhaEstaticaParte1.length() + linhaEstaticaParte2.length();
  



  
  /*  String linhaMovel = "  Max: " + String(maxTemp, 2) + " C" + "  Min: " + String(minTemp,2) + " C  ";  */
 
  linhaMovelParte1 = " Max: ";
  linhaMovelParte2 = String(maxTemp,2);
  linhaMovelParte3 = " C";

  linhaMovelParte4 = " Min: ";
  linhaMovelParte5 = String(minTemp,2);
  linhaMovelParte6 = " C ";

  linhaMovel = linhaMovelParte1 + linhaMovelParte2 + linhaMovelParte3 + linhaMovelParte4 + linhaMovelParte5 + linhaMovelParte6;

  posicaoMovelGrau1 = linhaMovelParte1.length() + linhaMovelParte2.length(); 
  posicaoMovelGrau2 = linhaMovelParte1.length() + linhaMovelParte2.length() + linhaMovelParte3.length() + linhaMovelParte4.length() + linhaMovelParte5.length();



  
  lcd.setCursor(0, 0);   
  lcd.print(linhaEstatica); 
  lcd.setCursor(posicaoEstaticaGrau, 0);
  lcd.write(1);            


  lcd.setCursor(0, 1);   
  lcd.print(linhaMovel.substring(comecoVisivel)); 
  lcd.setCursor(posicaoMovelGrau1+corretor, 1);
  lcd.write(1);
  lcd.setCursor(posicaoMovelGrau2+corretor, 1);
  lcd.write(1);

  



  if(comecoVisivel == linhaMovel.length() - screenWidth){
    direcao = 0;
    delay(500);
  }
  
  if(comecoVisivel == 0){
    direcao = 1;
    delay(500);
  }




  if(direcao){
    comecoVisivel++;
    corretor--;  
  }
  else{
    comecoVisivel--;
    corretor++;  
  }
  
 
}  

```





