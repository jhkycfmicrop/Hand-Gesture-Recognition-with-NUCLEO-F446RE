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

//25th APR
1. the accelerometer seems working after testing using UNO.
2. what i observed is that when put on the flat table the XYZ value are consistently at returning the same value.
3. but when I shake it or put it on a higher place, the value will fluctuate then go back to the same value consistently.
4. now need to figure out how to initialize the analog pin properly in NUCLEO-F446RE.
5. I am thinking maybe we need to use the concept of ADC instead of just analog read in STM32. maybe UNO got auto ADC??? that is what I thought

//28th APR
1. it is working now if using ADC instead of just AnalogRead.
2. but the value acquired is not 0 to 1023. it is 1500 to 2500, seems like the same as using UNO. Not sure what is the reason.
![image](https://user-images.githubusercontent.com/55950816/116252847-869e6480-a7a2-11eb-81b9-3718e669c62f.png)


//Current problem
1. ADXL seems failed, cant read any data from it

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


```
// To try out
// this is the code designed for Arduino C. Need to study to transform it into STM C
//re designed the code
/* USER CODE BEGIN Header */
/**
  ******************************************************************************
  * @file           : main.c
  * @brief          : Main program body
  ******************************************************************************
  * @attention
  *
  * <h2><center>&copy; Copyright (c) 2021 STMicroelectronics.
  * All rights reserved.</center></h2>
  *
  * This software component is licensed by ST under BSD 3-Clause license,
  * the "License"; You may not use this file except in compliance with the
  * License. You may obtain a copy of the License at:
  *                        opensource.org/licenses/BSD-3-Clause
  *
  ******************************************************************************
  */
/* USER CODE END Header */
/* Includes ------------------------------------------------------------------*/
#include "main.h"

/* Private includes ----------------------------------------------------------*/
/* USER CODE BEGIN Includes */
#include <stdio.h>
#include <string.h>
/* USER CODE END Includes */

/* Private typedef -----------------------------------------------------------*/
/* USER CODE BEGIN PTD */

/* USER CODE END PTD */

/* Private define ------------------------------------------------------------*/
/* USER CODE BEGIN PD */
/* USER CODE END PD */

/* Private macro -------------------------------------------------------------*/
/* USER CODE BEGIN PM */

/* USER CODE END PM */

/* Private variables ---------------------------------------------------------*/
UART_HandleTypeDef huart2;

/* USER CODE BEGIN PV */

/* USER CODE END PV */

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
static void MX_GPIO_Init(void);
static void MX_USART2_UART_Init(void);
/* USER CODE BEGIN PFP */
float mapf(float x, uint8_t in_min, uint8_t in_max, int out_min, int out_max)
{
  return ((((x - in_min) * (out_max - out_min)) / (in_max - in_min)) + out_min);
}
/* USER CODE END PFP */

/* Private user code ---------------------------------------------------------*/
/* USER CODE BEGIN 0 */

/* USER CODE END 0 */

/**
  * @brief  The application entry point.
  * @retval int
  */
int main(void)
{
  /* USER CODE BEGIN 1 */
	uint8_t buf[12];
	uint8_t scale = 3;
	uint8_t i = 1;
	
	float scaleX, scaleY, scaleZ, rawX, rawY, rawZ;

  /* USER CODE END 1 */

  /* MCU Configuration--------------------------------------------------------*/

  /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
  HAL_Init();

  /* USER CODE BEGIN Init */

  /* USER CODE END Init */

  /* Configure the system clock */
  SystemClock_Config();

  /* USER CODE BEGIN SysInit */

  /* USER CODE END SysInit */

  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  MX_USART2_UART_Init();
  /* USER CODE BEGIN 2 */

  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
		//strcpy((char*)buf, "Hello\r\n");
		
		
		rawX = HAL_GPIO_ReadPin(A0_GPIO_Port, A0_Pin);
		rawY = HAL_GPIO_ReadPin(A1_GPIO_Port, A1_Pin);
		rawZ = HAL_GPIO_ReadPin(A2_GPIO_Port, A2_Pin);
		
		scaleX = mapf(rawX, 0, 1023, -3, 3);
		scaleY = mapf(rawY, 0, 1023, -3, 3);
		scaleZ = mapf(rawZ, 0, 1023, -3, 3);
		
		sprintf((char*)buf, "Reading %d\r\nX = %.2f \r\nY = %.2f \r\nZ = %.2f \r\n\n", i, rawX, rawY, rawZ);
		
		//sprintf((char*)buf, "Reading %d\r\nX = %.2f g\r\nY = %.2f g\r\nZ = %.2f g\r\n\n", i, scaleX, scaleY, scaleZ);
		i += 1;
		//sprintf((char*)buf, "Y = %.2f g\r\n", scaleY);
		//sprintf((char*)buf, "Z = %.2f g\r\n", scaleZ);
		
		//send out buffer (motion)
		HAL_UART_Transmit(&huart2, buf, strlen((char*)buf), HAL_MAX_DELAY);
		
		HAL_Delay(500);
    /* USER CODE END WHILE */
		

    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}

/**
  * @brief System Clock Configuration
  * @retval None
  */
void SystemClock_Config(void)
{
  RCC_OscInitTypeDef RCC_OscInitStruct = {0};
  RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

  /** Configure the main internal regulator output voltage
  */
  __HAL_RCC_PWR_CLK_ENABLE();
  __HAL_PWR_VOLTAGESCALING_CONFIG(PWR_REGULATOR_VOLTAGE_SCALE3);
  /** Initializes the RCC Oscillators according to the specified parameters
  * in the RCC_OscInitTypeDef structure.
  */
  RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSI;
  RCC_OscInitStruct.HSIState = RCC_HSI_ON;
  RCC_OscInitStruct.HSICalibrationValue = RCC_HSICALIBRATION_DEFAULT;
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
  RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSI;
  RCC_OscInitStruct.PLL.PLLM = 16;
  RCC_OscInitStruct.PLL.PLLN = 336;
  RCC_OscInitStruct.PLL.PLLP = RCC_PLLP_DIV4;
  RCC_OscInitStruct.PLL.PLLQ = 2;
  RCC_OscInitStruct.PLL.PLLR = 2;
  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
  {
    Error_Handler();
  }
  /** Initializes the CPU, AHB and APB buses clocks
  */
  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
                              |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV2;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_2) != HAL_OK)
  {
    Error_Handler();
  }
}

/**
  * @brief USART2 Initialization Function
  * @param None
  * @retval None
  */
static void MX_USART2_UART_Init(void)
{

  /* USER CODE BEGIN USART2_Init 0 */

  /* USER CODE END USART2_Init 0 */

  /* USER CODE BEGIN USART2_Init 1 */

  /* USER CODE END USART2_Init 1 */
  huart2.Instance = USART2;
  huart2.Init.BaudRate = 115200;
  huart2.Init.WordLength = UART_WORDLENGTH_8B;
  huart2.Init.StopBits = UART_STOPBITS_1;
  huart2.Init.Parity = UART_PARITY_NONE;
  huart2.Init.Mode = UART_MODE_TX_RX;
  huart2.Init.HwFlowCtl = UART_HWCONTROL_NONE;
  huart2.Init.OverSampling = UART_OVERSAMPLING_16;
  if (HAL_UART_Init(&huart2) != HAL_OK)
  {
    Error_Handler();
  }
  /* USER CODE BEGIN USART2_Init 2 */

  /* USER CODE END USART2_Init 2 */

}

/**
  * @brief GPIO Initialization Function
  * @param None
  * @retval None
  */
static void MX_GPIO_Init(void)
{
  GPIO_InitTypeDef GPIO_InitStruct = {0};

  /* GPIO Ports Clock Enable */
  __HAL_RCC_GPIOC_CLK_ENABLE();
  __HAL_RCC_GPIOH_CLK_ENABLE();
  __HAL_RCC_GPIOA_CLK_ENABLE();
  __HAL_RCC_GPIOB_CLK_ENABLE();

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_RESET);

  /*Configure GPIO pin : B1_Pin */
  GPIO_InitStruct.Pin = B1_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_IT_FALLING;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  HAL_GPIO_Init(B1_GPIO_Port, &GPIO_InitStruct);

  /*Configure GPIO pins : A0_Pin A1_Pin A2_Pin */
  GPIO_InitStruct.Pin = A0_Pin|A1_Pin|A2_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_ANALOG;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);

  /*Configure GPIO pin : LD2_Pin */
  GPIO_InitStruct.Pin = LD2_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(LD2_GPIO_Port, &GPIO_InitStruct);

}

/* USER CODE BEGIN 4 */

/* USER CODE END 4 */

/**
  * @brief  This function is executed in case of error occurrence.
  * @retval None
  */
void Error_Handler(void)
{
  /* USER CODE BEGIN Error_Handler_Debug */
  /* User can add his own implementation to report the HAL error return state */
  __disable_irq();
  while (1)
  {
  }
  /* USER CODE END Error_Handler_Debug */
}

#ifdef  USE_FULL_ASSERT
/**
  * @brief  Reports the name of the source file and the source line number
  *         where the assert_param error has occurred.
  * @param  file: pointer to the source file name
  * @param  line: assert_param error line source number
  * @retval None
  */
void assert_failed(uint8_t *file, uint32_t line)
{
  /* USER CODE BEGIN 6 */
  /* User can add his own implementation to report the file name and line number,
     ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */
  /* USER CODE END 6 */
}
#endif /* USE_FULL_ASSERT */

/************************ (C) COPYRIGHT STMicroelectronics *****END OF FILE****/
```
