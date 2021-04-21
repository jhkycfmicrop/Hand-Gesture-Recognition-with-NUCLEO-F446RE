# Hand-Gesture-Recognition-with-NUCLEO-F446RE


//log

//20th APR
1. ADXL337 soldered with pins, not tested yet.
2. attempted to perform hello world on ADXL337, code not working
3. unable to launch serial monitor

//21st APR
1. refer to this website for a more complete explanation on ADXL337 programming: https://learn.sparkfun.com/tutorials/adxl337-and-adxl377-accelerometer-hookup-guide/all
2. Max scale for ADXL337 os +- 3g. It will return 0 - 1023.
3. in order to check the value (we us debuggers to read the value) serial monitor is needed, putty is a good choice, https://www.youtube.com/watch?v=isOekyygpR8
4. can proceed to test out the ADCL337 functionality.





//Problem n Solution
1. ADXL337 reading calculation
>> ADXL337 will return value ranged 0 to 1023, scaled tham with scale = +- 3 as that is the max range got ADXL337
>> scaledX = mapf(rawX, 0, 1023, -scale, scale);
>> https://learn.sparkfun.com/tutorials/adxl337-and-adxl377-accelerometer-hookup-guide/all

2. no serial monitor option in Kail
>> there should be a serial monitor, but putty is a good option

3. Need to turn motion into text, a LCD Shield is needed
>> https://randomnerdtutorials.com/25-arduino-shields/
>> RM 10 +-



// To try out
// this is the code designed for Arduino C. Need to study to transform it into STM C
int scale = 3;
void setup()
{
  // Initialize serial communication at 115200 baud
  Serial.begin(115200);
}

void loop()
{
  // Get raw accelerometer data for each axis
  int rawX = analogRead(A0);
  int rawY = analogRead(A1);
  int rawZ = analogRead(A2);
  
   float scaledX, scaledY, scaledZ; // Scaled values for each axis
  if (micro_is_5V) // microcontroller runs off 5V
  {
    scaledX = mapf(rawX, 0, 675, -scale, scale); // 3.3/5 * 1023 =~ 675
  }
  else // microcontroller runs off 3.3V
  {
    scaledX = mapf(rawX, 0, 1023, -scale, scale);
  }
  
  // Print out raw X,Y,Z accelerometer readings
  Serial.print("X: "); Serial.println(rawX);

  // Print out scaled X,Y,Z accelerometer readings
  Serial.print("X: "); Serial.print(scaledX); Serial.println(" g");
  
  delay(2000);
}
