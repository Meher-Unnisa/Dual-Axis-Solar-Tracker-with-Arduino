>Smart solar tracker simulation  using Arduino Uno
A smart solar tracking system that uses four LDR sensors and two servo motors to automatically follow the sun's movement. The Arduino continuously detects the brightest light source and adjusts the solar panel position on both horizontal and vertical axes, improving solar energy efficiency and power generation.
#include <Servo.h>

Servo servoX;   // Horizontal movement
Servo servoY;   // Vertical movement

int LDR1 = A0;
int LDR2 = A1;
int LDR3 = A2;
int LDR4 = A3;

int posX = 90;   // Initial horizontal position
int posY = 90;   // Initial vertical position

int threshold = 30;   // Deadband threshold
int offsetX = 0;      // Calibration offset for X
int offsetY = 0;      // Calibration offset for Y

void setup() {
  servoX.attach(9);
  servoY.attach(8);

  servoX.write(posX + offsetX);
  servoY.write(posY + offsetY);

  Serial.begin(9600);
}

void loop() {
  // Read LDR values
  int s1 = analogRead(LDR1);
  int s2 = analogRead(LDR2);
  int s3 = analogRead(LDR3);
  int s4 = analogRead(LDR4);

  // Average readings
  int top = (s1 + s2) / 2;
  int bottom = (s3 + s4) / 2;
  int left = (s1 + s3) / 2;
  int right = (s2 + s4) / 2;

  // Differences
  int vertical = top - bottom;
  int horizontal = left - right;

  // Vertical movement (Y-axis)
  if (abs(vertical) > threshold) {
    posY += vertical / 50;   // proportional adjustment
  }

  // Horizontal movement (X-axis)
  if (abs(horizontal) > threshold) {
    posX += horizontal / 50; // proportional adjustment
  }

  // Constrain servo angles
  posX = constrain(posX, 0, 180);
  posY = constrain(posY, 0, 180);

  // Write to servos with calibration offsets
  servoX.write(posX + offsetX);
  servoY.write(posY + offsetY);

  // Debugging output
  Serial.print("Top: "); Serial.print(top);
  Serial.print(" Bottom: "); Serial.print(bottom);
  Serial.print(" Left: "); Serial.print(left);
  Serial.print(" Right: "); Serial.println(right);

  Serial.print("posX: "); Serial.print(posX);
  Serial.print(" posY: "); Serial.println(posY);

  delay(100);
}
