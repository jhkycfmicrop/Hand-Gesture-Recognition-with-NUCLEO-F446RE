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


//1st MAY
1. the Nucleo-f446re is not the default supported device from edge impulse, so the only way i see is data forwarder: https://docs.edgeimpulse.com/docs/cli-data-forwarder#example-arduino
2. there are only Arduino, Zephr, MBED os example to relay the device to edge impulse. Need to translate Arduino C code to nucleo supported code. 
3. With that, the way that nucleo read Accelerometer value need to be tuned. 
4. havent tried that code yet. will try that tomorrow.


//2nd MAY
1. I think the main thing we need to focus now is figure out where should we run this thing to get the board to connect to EI
2. kinda frustrated as the documentation is kinda cacat abt this
3. was thinking maybe we need a virtual machine to run this, then i go take a look the section "Edge Impulse for Linux", that simply to use Edge impulse on UNIX.
4. then take a look the document to connect EI supported devices to EI, they prepared the respective EI firmware for the development boards, once the board uploaded with the firmware, they can launch a command prompt that controlling the boards and run those commands!!! 
5. probably need to contact senior to ask how they connect to EI

![image](https://user-images.githubusercontent.com/55950816/116813202-3f8ae780-ab85-11eb-9223-c243855e6c22.png)


//3rd MAY
1. finally able to connect to EI with the nucleo board

![image](https://user-images.githubusercontent.com/55950816/116877066-04091f80-ac50-11eb-97e7-cce6d0f88ab7.png)

2. proceed to sample data




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



