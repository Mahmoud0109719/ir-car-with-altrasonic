// Left Motor
#define LM_En 3
#define LM_F 4
#define LM_B 10
int LSpeed;
// Right Motor
#define RM_En 5
#define RM_F 7
#define RM_B 6
int RSpeed;
int MotorSpeed = 200;
// IR Sensors
#define LeftIRSensor 11
#define RightIRSensor 12
#define midIRSensor 13
// UltraSonic Sensor
#define UltraSonicDelay 150
#define echoPin 8
#define trigPin 9
#define DistanceLimit 20
// Car Important Variables
#define TurningTime 1000
// Variables
bool RightIR_read, LeftIR_read, midIR_read;
float US_timer;
char direction;
float distance;
float duration;
void setup() {
pinMode(trigPin, OUTPUT);
pinMode(echoPin, INPUT);
Serial.begin(9600);
// Left Motor Initialization
pinMode(LM_En, OUTPUT);
pinMode(LM_F, OUTPUT);
pinMode(LM_B, OUTPUT);
// Right Motor Initialization
pinMode(RM_En, OUTPUT);
pinMode(RM_F, OUTPUT);
pinMode(RM_B, OUTPUT);
// IR Sensors
pinMode(LeftIRSensor, INPUT);
pinMode(RightIRSensor, INPUT);
pinMode(midIRSensor, INPUT);
// Initialize going forward
direction = 'F';
move(direction);
// Timers to control sensors
US_timer = millis();
}
void loop() {
// CHECKING FOR OBSTACLE USING ULTRASONIC SENSOR
if (millis() - US_timer >= UltraSonicDelay) {
// Send sound waves
digitalWrite(trigPin, LOW);
delayMicroseconds(5);
digitalWrite(trigPin, HIGH);
delayMicroseconds(10);
digitalWrite(trigPin, LOW);
// Calculate the distance
duration = pulseIn(echoPin, HIGH);
distance = duration * 0.035 / 2;
// Make a decision
if (distance < DistanceLimit) {
// Right
direction = 'R';
move(direction);
delay(TurningTime);
// Left
direction = 'L';
move(direction);
delay(TurningTime-300);
// Forward
direction = 'F';
move(direction);
delay(TurningTime - 100);
// Left
direction = 'L';
move(direction);
delay(TurningTime - 300);
// Forward
direction = 'F';
// while(true) {
// RightIR_read = digitalRead(RightIRSensor);
// LeftIR_read = digitalRead(LeftIRSensor);
// if (RightIR_read == HIGH || LeftIR_read == HIGH) {
// break;
// }
// }
}
// Print distance on Serial
Serial.print("Distance: ");
Serial.print(distance);
Serial.println(" cm");
US_timer = millis();
}
RightIR_read = digitalRead(RightIRSensor);
LeftIR_read = digitalRead(LeftIRSensor);
midIR_read = digitalRead(midIRSensor);
if ((RightIR_read == HIGH && LeftIR_read == HIGH) || (midIR_read == HIGH && 
LeftIR_read == LOW && RightIR_read == LOW)) {
direction = 'F';
}
else if (RightIR_read == HIGH && LeftIR_read == LOW) {
direction = 'L';
}
else if (RightIR_read == LOW && LeftIR_read == HIGH) {
direction = 'R';
}
move(direction);
}
// Car Functions
void move(char D) {
switch (D) {
case 'L':
digitalWrite(LM_F, HIGH);
digitalWrite(LM_B, LOW);
digitalWrite(RM_F, HIGH);
digitalWrite(RM_B, LOW);
RSpeed = MotorSpeed;
LSpeed = 50;
break;
case 'R':
digitalWrite(RM_F, HIGH);
digitalWrite(RM_B, LOW);
digitalWrite(LM_F, HIGH);
digitalWrite(LM_B, LOW);
RSpeed = 50;
LSpeed = MotorSpeed;
break;
case 'F':
digitalWrite(RM_F, HIGH);
digitalWrite(RM_B, LOW);
digitalWrite(LM_F, HIGH);
digitalWrite(LM_B, LOW);
RSpeed = MotorSpeed;
LSpeed = MotorSpeed;
break;
case 'S':
digitalWrite(RM_F, LOW);
digitalWrite(RM_B, LOW);
digitalWrite(LM_F, LOW);
digitalWrite(LM_B, LOW);
RSpeed = MotorSpeed;
LSpeed = MotorSpeed;
break;
}
analogWrite(RM_En, RSpeed);
analogWrite(LM_En, LSpeed);
}
