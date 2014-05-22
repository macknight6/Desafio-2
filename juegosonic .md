Desafio-2
=========

proyecto en el cual habia que crear un jugo donde se recogen monedas 


/**
*coneccion de pines del acelerometro ADXL335
 * analog 0: accelerometer self test
 * analog 1: z-axis
 * analog 2: y-axis
 * analog 3: x-axis
 * analog 4: ground
 * analog 5: vcc
 * Codigo para el Desafio No.2
 * 
 */

// Configuracion de pines a utilizar
const int groundpin = 18;             // pin de entrada analoga 4 -- ground
const int powerpin = 19;              // pin de entrada analoga 5 -- voltage
const int ypin = A2;                  // eje X del acelerometro
int logico= 0; // estado logico del boton
//pin del pulsador
int boton =3;
// variable de multiplexacion
int escribir;

//valores minimos y maximos
//se obtienen calibrando el sensor
int minY = 119;
int maxY = 381;

//para sostener los valores calulados
double y;
//variables del tiempo
unsigned long time_1;
unsigned long deltaT;
long previousMillis = 0;


void setup()
{
  // iniciamos comunicacion serial
  Serial.begin(9600);
  /*
  Usando las entradas analogas provee alimentacion y ground, esto es posible si conectamos directamente
  el breakaout del acelerometro al arduino
  */
  pinMode(groundpin, OUTPUT);
  pinMode(powerpin, OUTPUT);
  digitalWrite(groundpin, LOW);
  digitalWrite(powerpin, HIGH);
  pinMode(boton, INPUT);
  time_1=millis();
}

void loop()
{
  unsigned long currentMillis = millis();
  if ( (millis() - time_1) > 100)
  {
    //lee los valores
    int yRead = analogRead(ypin);
    //mapea los datos enviados por el acelerometro
    int yAng = map(yRead, minY, maxY,0, 100);
    // si se presiona el boton uno envia un 1
    if ( digitalRead(boton) == HIGH){
      logico = 1;

    }
    else
    {
      logico=0 ;   
    }
    escribir= yAng*10+logico; // se multiplexan los datos para enviarlos a processing
    Serial.print(escribir); // si se desea visualizar los datos en el monitor serial del arduino
    Serial.print(",");
    Serial.println(logico);
    time_1=millis();

  }
}



