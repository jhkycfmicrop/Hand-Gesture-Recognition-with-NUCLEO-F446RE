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

//22nd APR
1. the ADXL337 macam failed, cant read any data. not sure is broken or solder problem, see dont have any short connection. if got multimeter jiu hao le
2. re design the code, now left reading data problem
3. refer the code in ReadData_with_simple_AnalogRead.txt

//25th APR
1. the accelerometer seems working after testing using UNO.
2. what i observed is that when put on the flat table the XYZ value are consistently at returning the same value.
3. but when I shake it or put it on a higher place, the value will fluctuate then go back to the same value consistently.
4. now need to figure out how to initialize the analog pin properly in NUCLEO-F446RE.
5. I am thinking maybe we need to use the concept of ADC instead of just analog read in STM32. maybe UNO got auto ADC??? that is what I thought

//28th APR
1. it is working now if using ADC instead of just AnalogRead.
2. but the value acquired is not 0 to 1023. it is 1500 to 2500, seems like the same as using UNO. Not sure what is the reason.
3. refer code in ReadData_with_ADC.txt

![image](https://user-images.githubusercontent.com/55950816/116252847-869e6480-a7a2-11eb-81b9-3718e669c62f.png)


//Current problem
1. the raw data acquired ranged from 1500 to 2500 instead of 0 - 1023.

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



