# Arduino Code
#include <Servo.h>. 

const int trigPin = 2;
const int echoPin = 3;


long duration;
int distance;
Servo myServo;


void setup() {
  
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT); 
  Serial.begin(9600);
  myServo.attach(9); 
  
}


void loop() {

  
  
  for(int i=15;i<=165;i++){  
  myServo.write(i);
  delay(30);
  distance = calculateDistance();
  
  Serial.print(i); 
  Serial.print(","); 
  Serial.print(distance);
  Serial.print(".");
  }

  
 
  for(int i=165;i>15;i--){  
  myServo.write(i);
  delay(30);
  distance = calculateDistance();
  Serial.print(i);
  Serial.print(",");
  Serial.print(distance);
  Serial.print(".");
  }
}



int calculateDistance(){ 
  
  digitalWrite(trigPin, LOW); 
  delayMicroseconds(2);
 
  digitalWrite(trigPin, HIGH); 
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH); 
  distance= duration*0.034/2;
  return distance;

}


-------------------------------------------------------------------------------------------------------------------------------------

# Processing code 
import processing.serial.*;  

Serial port; 
int[] distances; 
int numSteps = 180; 
float stepAngle; 

void setup() {
  size(600, 600); 
  background(0); 
  strokeWeight(2); 
  stroke(0, 255, 0); 
  
  distances = new int[numSteps];
  for (int i = 0; i < numSteps; i++) {
    distances[i] = 0;
  }

  stepAngle = PI / numSteps;
   
  port = new Serial(this, "COM4", 9600); 
  port.bufferUntil('\n');
}

void draw() {
  background(0); 
  
  translate(width / 2, height); 
  rotate(-PI / 2); 
  
  // Draw the radar grid
  drawRadarGrid();
  
  import processing.serial.*; 
import java.awt.event.KeyEvent;
import java.io.IOException;
Serial myPort; 
String angle="";
String distance="";
String data="";
String noObject;
float pixsDistance;
int iAngle, iDistance;
int index1=0;
int index2=0;
PFont orcFont;
void setup() {
  
 size (1920, 1080);
 smooth();
 myPort = new Serial(this,"COM4", 9600); 
 myPort.bufferUntil('.'); 
 orcFont = loadFont("PalatinoLinotype-BoldItalic-48.vlw");
}
void draw() {
  
  fill(98,245,31);
  textFont(orcFont);
  
  fill(0,4); 
  rect(0, 0, width, 1010); 
  
  fill(98,245,31);   
  drawLine();
  drawObject();
  drawText();
}
void serialEvent (Serial myPort) { 
  
  data = myPort.readStringUntil('.');
  data = data.substring(0,data.length()-1);
  
  index1 = data.indexOf(","); 
  angle= data.substring(0, index1); 
  distance= data.substring(index1+1, data.length());
  iAngle = int(angle);
  iDistance = int(distance);
}
void drawRadar() {
  pushMatrix();
  translate(960,1000); 
  noFill();
  strokeWeight(2);
  stroke(98,245,31);
  // draws the arc lines
  arc(0,0,1800,1800,PI,TWO_PI);
  arc(0,0,1400,1400,PI,TWO_PI);
  arc(0,0,1000,1000,PI,TWO_PI);
  arc(0,0,600,600,PI,TWO_PI);
  // draws the angle lines
  line(-960,0,960,0);
  line(0,0,-960*cos(radians(30)),-960*sin(radians(30)));
  line(0,0,-960*cos(radians(60)),-960*sin(radians(60)));
  line(0,0,-960*cos(radians(90)),-960*sin(radians(90)));
  line(0,0,-960*cos(radians(120)),-960*sin(radians(120)));
  line(0,0,-960*cos(radians(150)),-960*sin(radians(150)));
  line(-960*cos(radians(30)),0,960,0);
  popMatrix();
}
void drawObject() {
  pushMatrix();
  translate(960,1000); 
  strokeWeight(9);
  stroke(255,10,10); 
  pixsDistance = iDistance*22.5; 
  // limiting the range to 40 cms
  if(iDistance<40){
   
  line(pixsDistance*cos(radians(iAngle)),-pixsDistance*sin(radians(iAngle)),950*cos(radians(iAngle)),-950*sin(radians(iAngle)));
  }
  popMatrix();
}
void drawLine() {
  pushMatrix();
  strokeWeight(9);
  stroke(30,250,60);
  translate(960,1000);
  line(0,0,950*cos(radians(iAngle)),-950*sin(radians(iAngle))); 
}
void drawText() { 
  
  pushMatrix();
  if(iDistance>40) {
  noObject = "Out of Range";
  }
  else {
  noObject = "In Range";
  }
  fill(0,0,0);
  noStroke();
  rect(0, 1010, width, 1080);
  fill(98,245,31);
  textSize(25);
  text("10cm",1180,990);
  text("20cm",1380,990);
  text("30cm",1580,990);
  text("40cm",1780,990);
  textSize(40);
  text("Object: " + noObject, 240, 1050);
  text("Angle: " + iAngle +" °", 1050, 1050);
  text("Distance: ", 1380, 1050);
  if(iDistance<40) {
  text("        " + iDistance +" cm", 1400, 1050);
  }
  textSize(25);
  fill(98,245,60);
  translate(961+960*cos(radians(30)),982-960*sin(radians(30)));
  rotate(-radians(-60));
  text("30°",0,0);
  resetMatrix();
  translate(954+960*cos(radians(60)),984-960*sin(radians(60)));
  rotate(-radians(-30));
  text("60°",0,0);
  resetMatrix();
  translate(945+960*cos(radians(90)),990-960*sin(radians(90)));
  rotate(radians(0));
  text("90°",0,0);
  resetMatrix();
  translate(935+960*cos(radians(120)),1003-960*sin(radians(120)));
  rotate(radians(-30));
  text("120°",0,0);
  resetMatrix();
  translate(940+960*cos(radians(150)),1018-960*sin(radians(150)));
  rotate(radians(-60));
  text("150°",0,0);
  popMatrix(); 
}
