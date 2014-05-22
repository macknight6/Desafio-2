Desafio-2
=========

proyecto en el cual habia que crear un jugo donde se recogen monedas 

//Llamado de Librerias
import processing.serial.*;// Llamo a la libreria Serial

Serial myport;      // Creo el objeto myport, sobre el que se configura la adquisicion de datos mediante puerto serie
int bt=0;  //Flag que indica que dato se esta recibiendo(temp,ldr,memoria temp o memoria ldr)

int Xpos;               // Coordenada actual horizontal en el grafico
int YposLDR=340;        // Coordenada actual vertical del dato LDR
int YposTEMP=340;       // Coordenada actual vertical del dato Temp

int lastXpos;       // Coordenada previa horizontal en el grafico
int lastYposLDR;    // Coordenada previa vertical del dato LDR 
int lastYposTEMP;   // Coordenada previa vertical del dato Temp

int limdergrafico=540; // Borde derecho del area del grafico
int limizqgrafico=140; // Borde izquierdo del area del grafico
int limsupgrafico=400; // Borde superior del area del grafico
int liminfgrafico=340; // Borde inferior del area del grafico

// Los siguientes datos son los que se muestran en el grafico y son recibidos por puerto serie desde arduino.
int direccionLDR;      // Variable donde se almacena  la direccion de memoria 
                       // donde se esta escribiendo o leyendo el dato de la LDR.
int direccionTEMP;     // Variable donde se almacena  la direccion de memoria 
                       // donde se esta escribiendo o leyendo el dato de la TEMP.
int valorLDR;          //Variable donde se almacena el dato correspondiente al valor medido por la LDR  
int valorTEMP;         //Variable donde se almacena el dato correspondiente al valor medido por el sensor de temp. 


//----------------- setup -----------------------------------------------------
void setup () {
  
  size(550, 400); // Tamaño de la Ventana      
  
  println(Serial.list());   // Muestra la lista de los puertos disponibles
  myport = new Serial(this, Serial.list()[0], 9600);  //Se setea el objeto myport
  

  background(255);                // Se configura el fondo blanco. OJO: ya que la declaracion del fonde se hace en setup, esta se realiza solo
                                  //al inicio del ciclo, permitiendo de esta forma, realizar el grafico, de lo contrario se borrarian los 
                                  //puntos graficados a cada ciclo del draw.
  Xpos = limdergrafico+1;         //Se setea la posicion horizontal fuera del grafico(borde derecho), de esta forma al recibir el primer dato 
                                  //esta posicion regresa al inicio del grafico(borde izquierdo)
}

//----------------- draw ------------------------------------------------------
void draw () 
{
  rectangulos(); //Llama a la funcion rectangulo, que permite que los datos numericos no se sobre escriban.
  grafico();     //Llama a la funcion grafico, que como su nombre lo dice, se encarga de graficar los datos.
  Textos();      //Llama a la funcion Textos, la cual se encarga de "imprimir" los textos en la interfaz.
  valores();     //Llama a la funcion Valores, la cual se encarga de "imprimir" los textos en la interfaz.
}

//----------------- serialEvent -----------------------------------------------

void serialEvent (Serial myPort) {
  
 String inString = myPort.readStringUntil('\n');//Se lee  el puerto serie hasta que se detecta un salto de linea
 
 if (inString != null) // Si es que hay datos entrantes
 { 
    inString = trim(inString);// se remueven los espacion en blanco al inicio y final de la cadena recibida
                              // estos forman parte del protocolo de la transmision
 
  if(bt==0) //Si el flag es cero, entonces se esta recibiendo un dato de Temperatura
  {
    float inDataTEMP = float(inString); //El dato entrante se almacena en la variable inDataTEMP
    valorTEMP=int(inDataTEMP);//Convierto el valor de float a int y almaceno la informacion en valorTEMP, el cual sera mostrado numericamente en la interfaz
    float inDataTEMPmp = map(inDataTEMP, 10, 50, 0, 300); //Mapeo el dato recibido segun las dimensiones de la ventana del grafico.
                                                          //Se considerara que los datos de temperatura se encuentran entre 10 y 50 °C solamente.
    
    YposTEMP= int(liminfgrafico-inDataTEMPmp); //Se calcula la coordenada Y(vertical) correspondiente  a Temp para graficarlo.
    println(YposTEMP); // muesto en la ventana de comunicacion serial el dato Temp.
    bt=1; //El proximo dato que se leera corresponde a direccion de memoria del dato Temp recibido.
    stroke(127,34,0);     //Color de la linea correspondiente a Temp en el grafico
    strokeWeight(3);      //Ancho de la linea correspondiente a Temp en el grafico
    line(lastXpos, lastYposTEMP, Xpos, YposTEMP);//Uno la coordenada anterior con la nueva mediante una linea.
    noStroke();
    lastYposTEMP= YposTEMP; //La coordenada actual de temp, ahora pasa a ser la coordenada anterior luego de ser graficada.
     
  }
  
    else if(bt==1)//Si el flag es 1, se esta recibiendo  un dato de Direccion de memoria que indica donde se almacena el dato de temperatura
    {
        direccionTEMP = int(inString); //Direccion de memoria Temp
        bt=2; //Actualizo el flag, el proximo dato  corresponde a un dato de la LDR
     
     }
  
    else if(bt==2)//Si el flag es cero, entonces se esta recibiendo un dato de LDR
    {

    float inDataLDR = float(inString);//El dato entrante se almacena en la variable inDataLDR
    valorLDR=int(inDataLDR);//Convierto el valor de float a int y almaceno la informacion en valorLDR, el cual sera mostrado numericamente en la interfaz
    float inDataLDRmp = map(inDataLDR, 0, 255, 0, 300); //Mapeo el dato recibido segun las dimensiones de la ventana del grafico.
                                                         //Se considerara que los datos de LDR varian entre 0 y 255.
    
    YposLDR= int(liminfgrafico-inDataLDRmp);//Se calcula la coordenada Y(vertical) correspondiente  a LDR para graficarlo.
    println(inDataLDR);// muesto en la ventana de comunicacion serial el dato Temp.
    bt=3; //El proximo dato que se leera corresponde a direccion de memoria del dato LDR recibido.
    stroke(127,34,255);     
    strokeWeight(3);     
    line(lastXpos, lastYposLDR, Xpos, YposLDR);
    noStroke();
    lastYposLDR= YposLDR;//La coordenada actual de LDR, ahora pasa a ser la coordenada anterior luego de ser graficada.
    
    lastXpos= Xpos;    //La coordenada X actual , ahora pasa a ser la coordenada anterior luego de ser graficada.
    Xpos=Xpos+10;     // Incrementa la coordenada horizontal en 10 pixeles
    println(Xpos);
    
    }
    
     else if(bt==3)
     {
        direccionLDR = int(inString); 
        bt=0; //el próximo byte que leeré sera de Temp.
     }
    
    if (Xpos > limdergrafico) // Si se sale del grafico, se regresa al borde izquierdo del grafico
    {   
      background(255);      // Limpia la ventana para borrar lo graficado
      Xpos = 140;           // regresa al borde izquierdo del grafico (inicio)
      lastXpos= Xpos;       // Posiciona la coordenada X en el borde derecho2
      lastYposLDR= YposLDR;     //Mantiene los ultimos datos de LDR y temp como valores iniciales.
      lastYposTEMP= YposTEMP;
    } 

  }
}

void grafico()
{
    stroke(0);
    strokeWeight(2);
    noFill();
    rect(140,40,400,300);
    strokeWeight(1);
    line(140,115,540,115);
    line(140,190,540,190);
    line(140,265,540,265); 
    stroke(0);
}

void Textos()
{
    fill(0,102,153);
    textSize(20);
    textAlign(LEFT);
    textSize(20);
    text("Grafico de datos LDR y Temp.",20,20);
    textSize(15);
    text("Grupo 6",310,20);
    textSize(12);
    text("Temp:",200,360);
    textSize(12);
    text("DirMem:",290,360);
    textSize(12);
    text("LDR:",200,380);
    textSize(12);
    text("DirMem:",290,380); 
    
   //Lineas
    textSize(12);
    text("10°C/0 LDR",20,345); 
    textSize(12);
    text("20°C/63.75 LDR",20,270);
    textSize(12);
    text("30°C/127.5 LDR",20,195);
    textSize(12);
    text("40°C/191.25 LDR",20,120);
    textSize(12);
    text("50°C/255 LDR",20,45);

}

 void valores()
 {
    textSize(12);
    text(valorLDR,240,380);
    textSize(12);
    text(hex(direccionLDR),340,380);
    textSize(12);
    text(valorTEMP,240,360);
    textSize(12);
    text(hex(direccionTEMP),340,360);
 }
 
 void rectangulos()
 {
    noStroke();
    fill(255);
    rect(238,345,45,17);
    rect(238,370,45,17);  
    rect(340,345,220,17);
    rect(340,370,220,17);  
 }
 
 
