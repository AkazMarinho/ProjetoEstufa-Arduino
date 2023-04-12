#include <LiquidCrystal.h>

//Declaração de variaveis LCD
const int rs = 6, en = 7, d4 = 5, d5 = 4, d6 = 3, d7 = 2; //nomeação das portas do arduino
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
//declaracao lampadas
int lampada = 10;
//declaracao motores
int MOT1 = 8;
int MOT2 = 9;
//declaracao fotoresistor
float LDR1 = A1;
float LDR2 = A2;
int valorLDR1 = 0;
int valorLDR2 = 0;
//declaracao do led
int ledVermelho = 11;
int ledAmarelo = 13;
//declaracao botao
int botao = 12;
int alternativa = 0;
int estadoLed = 0;
//declaracao temperatura
int temperatura = A0;
int temp = 0;

////////////////////////////////////////////
void setup(){
  //lcd
  lcd.begin(16, 2);
  Serial.begin(9600);
  //lampada
  pinMode(lampada, OUTPUT);
  //motor
  pinMode(MOT1, OUTPUT);
  pinMode(MOT2, OUTPUT);
  //fotorresistor
  pinMode(LDR1, INPUT);
  pinMode(LDR2, INPUT);
  //declaracao led
  pinMode(ledVermelho, OUTPUT);
  pinMode(ledAmarelo, OUTPUT);
  //declaracao botao
  pinMode(botao, INPUT_PULLUP);
  //declaracao temperatura
  pinMode(temperatura, INPUT);
  

}
////////////////////////////////////////////
void loop(){
  
   medicaotemperatura();
   selecao();

}
/////////////////////////////////////////////
void medicaotemperatura(){
 
  //Calculo Sensor de Temp.
  float tensao = analogRead(temperatura) * 5.0 / 1023;
  float temp = round((tensao - .5)*100);
  //Serial.println(temp);
  lcd.setCursor(0, 0);
  lcd.print("Temp: ");
  lcd.print(temp);
  lcd.write(B10110010);
  lcd.print("C");
  
  if(temp < 30){
   digitalWrite(MOT1, LOW);
   digitalWrite(MOT2, LOW);
  }
 if(temp >= 30 && temp<45){
   digitalWrite(MOT1, HIGH);
   digitalWrite(MOT2, LOW);
  }
 if(temp >= 45){
   digitalWrite(MOT1, HIGH);
   digitalWrite(MOT2, HIGH);
   }
  
}
///////////////////////////////////////////////
void interno(){
 
    if(valorLDR1 <= 500){
      digitalWrite(ledAmarelo, HIGH);
      digitalWrite(lampada, HIGH);

    } 
    else{

      digitalWrite(ledAmarelo, LOW);
      digitalWrite(lampada, LOW);

    }
  
}
/////////////////////////////////////////////////
void externo(){

if(valorLDR2 <= 500){
    
   	digitalWrite(ledVermelho, LOW);
     digitalWrite(lampada, HIGH);
    
    
  	} 
  	else{
    
    digitalWrite(ledVermelho, HIGH);
    digitalWrite(lampada, LOW);
    
  	}
  
}
////////////////////////////////////////////////////
void selecao(){
  if(digitalRead(botao) == LOW) // Se o botão for pressionado
  {
    estadoLed = !estadoLed; // troca o estado do LED
    alternativa++;
    while(digitalRead(botao) == LOW);
    delay(100);       
  }    
  else if(alternativa >= 6){
     
    alternativa = 0;

  }
  
   if(alternativa % 2 == 0){
  
    interno();
    valorLDR1 = analogRead(LDR1);
    Serial.println("interno");
    Serial.println(valorLDR1);
  
  }else{
  
    externo();
    valorLDR2 = analogRead(LDR2);
    Serial.println("externo");
    Serial.println(valorLDR2);
     digitalWrite(ledAmarelo, LOW);
    
   }
  
}