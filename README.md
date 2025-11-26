# Servo-Snake
Basic Servo Snake code
/***************************************************
  This is an example for Adafruit 16-channel PWM & Servo driver
  This code will run 8 servos as a robotic snake with forward locomotion.
  Servos 1, 3, 5, and 7 are mounted vertically.
  Servos 2, 4, 6, and 8 are mounted horizontally.
  
  These drivers use I2C to communicate, 2 pins are required to interface.

****************************************************/

#include <Wire.h>
#include <Adafruit_PWMServoDriver.h>

// Called this way, it uses the default address 0x40
Adafruit_PWMServoDriver pwm = Adafruit_PWMServoDriver();

#define SERVOMIN  150 // This is the 'minimum' pulse length count (out of 4096)
#define SERVOMAX  600 // This is the 'maximum' pulse length count (out of 4096)
#define SERVO_FREQ 50 // Analog servos run at ~50 Hz updates

#define NUM_SERVOS 8 // Total number of servos

// Function to convert degrees to pulse length
int angleToPulse(int angle) {
  return map(angle, 0, 180, SERVOMIN, SERVOMAX);
}

void setup() {
  Serial.begin(9600);
  Serial.println("Robotic Snake Forward Locomotion");

  pwm.begin();
  pwm.setOscillatorFrequency(27000000);
  pwm.setPWMFreq(SERVO_FREQ);  // Analog servos run at ~50 Hz updates

  delay(10);

  // Move all servos to the initial position (90 degrees)
  for (int servoNum = 0; servoNum < NUM_SERVOS; servoNum++) {
    pwm.setPWM(servoNum, 0, angleToPulse(90));
  }

  // Wait a moment to let all servos reach the initial position
  delay(1000);
}

void loop() {
  // Simple forward locomotion wave pattern
  for (int i = 0; i < 360; i += 2) {
    // Vertically mounted servos: 1, 3, 5, 7 (servoNum = 0, 2, 4, 6)
    for (int servoNum = 0; servoNum < NUM_SERVOS; servoNum += 2) {
      int phaseShift = servoNum * 45; // Phase shift increases with servo number
      int angle = 90 + 5 * sin((i + phaseShift) * PI / 180); // Reduced amplitude to 5 degrees
      pwm.setPWM(servoNum, 0, angleToPulse(angle));
    }
    
    // Horizontally mounted servos: 2, 4, 6, 8 (servoNum = 1, 3, 5, 7)
    for (int servoNum = 1; servoNum < NUM_SERVOS; servoNum += 2) {
      int phaseShift = servoNum * 45; // Phase shift increases with servo number
      int angle = 90 + 5 * sin((i + phaseShift + 90) * PI / 180); // Reduced amplitude to 5 degrees
      pwm.setPWM(servoNum, 0, angleToPulse(angle));
    }

    delay(20); // Adjust delay to control speed of the wave
  }
}
