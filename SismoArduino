//Liberias
#include "DualMC33926MotorShield.h"
DualMC33926MotorShield md;

//Recibe floats del serial 
const byte numChars = 32;
char receivedChars[numChars];
char tempChars[numChars];        // arreglo temporal

int contador = 0;

double floatFromPC = 0.0;

boolean newData = false;

//Variables para el motor
float escala=0;
float velocidad=0;
float desplazamiento=0;
float velocidadmax=0;
float velocidadmin=0;

//============

void setup() {
    Serial.begin(9600);
    Serial.println("Arduino listo para recibir datos ");
    Serial.println();
}

//============

void loop() {
    recvWithStartEndMarkers();
    if (newData == true) {
        strcpy(tempChars, receivedChars);
            // esta es una copia temporal para proteger los datos originales
            
        parseData();
        showParsedData();
        newData = false;
    }
}

//============

void recvWithStartEndMarkers() {
    static boolean recvInProgress = false;
    static byte ndx = 0;
    char startMarker = ';';
    char endMarker = ';';
    char rc;
   

    while (Serial.available() > 0 && newData == false) {
        rc = Serial.read();

        if (recvInProgress == true) {
            if (rc != endMarker) {
                receivedChars[ndx] = rc;
                ndx++;
                if (ndx >= numChars) {
                    ndx = numChars - 1;
                }
            }
            else {
                //receivedChars[ndx] = '/0'; // terminate the string
                recvInProgress = false;
                ndx = 0;
                newData = true;
                
            }
        }

        else if (rc == startMarker) {
            recvInProgress = true;
        }
    }
}

//============

void parseData() {      
    char * strtokIndx; 

    strtokIndx = strtok(tempChars, ";");
    floatFromPC = atof(strtokIndx);     

    datos_motor(floatFromPC);
}

//============

void stopIfFault()
{
if (md.getFault())
{
Serial.println("fault");
while(1);
}
}

//============

void datos_motor(float dato){
  if (contador==0){
    escala=dato;
    contador=contador+1;
  }
  else if (contador==1){
    velocidadmax=dato;
    contador=contador+1;
  }
  else if (contador==2){
    velocidadmin=dato;
    contador=contador+1;
  }
  else if (contador==3){
    velocidad=dato;
    contador=contador+1;
  }
  else if (contador==4){
    desplazamiento=dato;
    contador=contador-1;
    motor(escala,velocidad,desplazamiento,velocidadmax,velocidadmin);
  }
}

//============

void motor(float esc, float vel, float despl, float vmax, float vmin){
  float vel_motor;
  float vel_max;
  float vel_min;
  float despl_motor;
  float tiempo=0;

//se multiplica por 10 para pasar a milímetros y entre la escala
  vel_max=10*vmax/esc;
  vel_min=10*vmin/esc;
  vel_motor=10*vel/esc; 
  despl_motor=10*despl/esc;
  tiempo=abs(despl/vel)*100;
  
  vel_motor=map(vel_motor,vel_min,vel_max,-5,5);
  vel_motor=map(vel_motor,vel_min,vel_max,-400,400);
  vel_motor=constrain(vel_motor,-400,400);

  md.setM1Speed(vel_motor);
  delay(tiempo);
  md.setM1Speed(0);
  stopIfFault();
  }

//============
void showParsedData() {
    Serial.println(floatFromPC);
}
