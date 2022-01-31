#include <Servo.h> //подключаем библитотеку
#include <NewPing.h> //подключаем библитотеку
#define TRIGGER_PIN  12 // устанавливаем номера пинов для датчика растояния
#define ECHO_PIN     11
#define MAX_DISTANCE 200
NewPing sonar(TRIGGER_PIN, ECHO_PIN, MAX_DISTANCE);
Servo servo;
int Sl, Sr, flag=0, dist; // объявляем необходимые переменные для датчиков линии, растояния

void zahv(){  //
  dist = sonar.ping_cm(); // считываем показания с дальномера
  Serial.print(dist);
  Serial.println("cm");
  if(dist!=0 && dist<5){ // если предмет ближе 5см, то останавливаемся и захватываем его
    digitalWrite(8, LOW);//R
    digitalWrite(7, LOW);
    digitalWrite(9, LOW);//L
    digitalWrite(10, LOW);
    flag=1;
    servo.write(95);  // закрываем захват
    delay (1000);
  }
}

void setup () {
  pinMode(4, INPUT); // настраиваем необходимые пины на вход и выход
  pinMode(5, INPUT);
  Serial.begin(9600);
  pinMode(9, OUTPUT);
  pinMode(10, OUTPUT);

  pinMode(7, OUTPUT);
  pinMode(8, OUTPUT);

  servo.attach(6);
  servo.write(130); // открываем захват
  delay (1000);
}

void loop(){
  Sr=digitalRead(4); // считываем показания с датчиков линии
  Sl=digitalRead(5);
  if(Sl == Sr){ // когда оба датчика видят белое, вращать двумя моторами
    digitalWrite(8, LOW);//R
    digitalWrite(7, HIGH);
    digitalWrite(9, LOW);//L
    digitalWrite(10, HIGH);
    if(!flag) zahv(); // вызов процедуры zahv, когда flag=1 это означает, что уже захватили объект
   }
   
  if(!Sl && Sr){ // когда правый видит линию, врщать левым
    digitalWrite(9, LOW);//L
    digitalWrite(10, HIGH);
    if(!flag) zahv(); // вызов процедуры zahv, когда flag=1 это означает, что уже захватили объект
  }else{ // иначе стоп
    digitalWrite(9, LOW);//L
    digitalWrite(10, LOW);
    if(!flag) zahv(); // вызов процедуры zahv, когда flag=1 это означает, что уже захватили объект
  }
  if(Sl && !Sr){ // когда левый видит линию, врщать правым
    digitalWrite(8, LOW);
    digitalWrite(7, HIGH);
    if(!flag) zahv(); // вызов процедуры zahv, когда flag=1 это означает, что уже захватили объект
  }else{ // иначе стоп
    digitalWrite(7, LOW);
    digitalWrite(8, LOW);
    if(!flag) zahv();  // вызов процедуры zahv, когда flag=1 это означает, что уже захватили объект  
  }
}
