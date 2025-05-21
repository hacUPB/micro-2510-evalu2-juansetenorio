ACTIVIDAD 2 I2C


```c
#include "main.h"
#include "core_cm4.h"
#include <stdio.h>
#include <string.h>

#define SHT30_I2C_ADDR (0x44 << 1)

I2C_HandleTypeDef hi2c1;
float temperature = 0.0f;
float humidity = 0.0f;

void SystemClock_Config(void);
static void MX_GPIO_Init(void);
static void MX_I2C1_Init(void);
HAL_StatusTypeDef SHT30_ReadTempHum(float *temperature, float *humidity);
uint8_t SHT30_CalcCRC(uint8_t *data);
void ITM_Printf(const char *str);

void ITM_Printf(const char *str) {
    while (*str) {
        ITM_SendChar(*str++);
    }
}

uint8_t SHT30_CalcCRC(uint8_t *data)
{
    uint8_t crc = 0xFF;
    for (int i = 0; i < 2; i++) {
        crc ^= data[i];
        for (int j = 0; j < 8; j++) {
            crc = (crc & 0x80) ? (crc << 1) ^ 0x31 : (crc << 1);
        }
    }
    return crc;
}

HAL_StatusTypeDef SHT30_ReadTempHum(float *temperature, float *humidity)
{
    uint8_t cmd[] = {0x2C, 0x06};
    uint8_t rx[6] = {0};

    if (HAL_I2C_Master_Transmit(&hi2c1, SHT30_I2C_ADDR, cmd, 2, HAL_MAX_DELAY) != HAL_OK)
        return HAL_ERROR;

    HAL_Delay(15);

    if (HAL_I2C_Master_Receive(&hi2c1, SHT30_I2C_ADDR, rx, 6, HAL_MAX_DELAY) != HAL_OK)
        return HAL_ERROR;

    char raw[64];
    sprintf(raw, "RAW: T: %02X %02X CRC:%02X | H: %02X %02X CRC:%02X\r\n",
            rx[0], rx[1], rx[2], rx[3], rx[4], rx[5]);
    ITM_Printf(raw);

    if (rx[2] != SHT30_CalcCRC(&rx[0]) || rx[5] != SHT30_CalcCRC(&rx[3])) {
        ITM_Printf("CRC incorrecto\r\n");
        return HAL_ERROR;
    }

    uint16_t rawTemp = (rx[0] << 8) | rx[1];
    uint16_t rawHum  = (rx[3] << 8) | rx[4];

    *temperature = -45 + 175 * ((float)rawTemp / 65535.0f);
    *humidity    = 100 * ((float)rawHum / 65535.0f);

    return HAL_OK;
}

int main(void)
{
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();
    MX_I2C1_Init();

    if (HAL_I2C_IsDeviceReady(&hi2c1, SHT30_I2C_ADDR, 2, HAL_MAX_DELAY) == HAL_OK) {
        ITM_Printf("SHT30 detectado\r\n");
    } else {
        ITM_Printf("SHT30 NO detectado\r\n");
    }

    char msg[64];

    while (1)
    {
        if (SHT30_ReadTempHum(&temperature, &humidity) == HAL_OK)
        {
            int t_ent = (int)temperature;
            int t_dec = (int)((temperature - t_ent) * 100);
            int h_ent = (int)humidity;
            int h_dec = (int)((humidity - h_ent) * 100);

            sprintf(msg, "Temp: %d.%02d C | Hum: %d.%02d %%RH\r\n",
                    t_ent, t_dec, h_ent, h_dec);
            ITM_Printf(msg);
        }
        else
        {
            ITM_Printf("Error leyendo SHT30\r\n");
        }

        HAL_Delay(1000);
    }
}

void SystemClock_Config(void)
{
    RCC_OscInitTypeDef RCC_OscInitStruct = {0};
    RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

    __HAL_RCC_PWR_CLK_ENABLE();
    __HAL_PWR_VOLTAGESCALING_CONFIG(PWR_REGULATOR_VOLTAGE_SCALE1);
    RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSI;
    RCC_OscInitStruct.HSIState = RCC_HSI_ON;
    RCC_OscInitStruct.HSICalibrationValue = RCC_HSICALIBRATION_DEFAULT;
    RCC_OscInitStruct.PLL.PLLState = RCC_PLL_NONE;

    if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
        Error_Handler();

    RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK | RCC_CLOCKTYPE_SYSCLK |
                                  RCC_CLOCKTYPE_PCLK1 | RCC_CLOCKTYPE_PCLK2;
    RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_HSI;
    RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
    RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV1;
    RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

    if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_0) != HAL_OK)
        Error_Handler();
}

static void MX_I2C1_Init(void)
{
    hi2c1.Instance = I2C1;
    hi2c1.Init.ClockSpeed = 100000;
    hi2c1.Init.DutyCycle = I2C_DUTYCYCLE_2;
    hi2c1.Init.OwnAddress1 = 0;
    hi2c1.Init.AddressingMode = I2C_ADDRESSINGMODE_7BIT;
    hi2c1.Init.DualAddressMode = I2C_DUALADDRESS_DISABLE;
    hi2c1.Init.OwnAddress2 = 0;
    hi2c1.Init.GeneralCallMode = I2C_GENERALCALL_DISABLE;
    hi2c1.Init.NoStretchMode = I2C_NOSTRETCH_DISABLE;

    if (HAL_I2C_Init(&hi2c1) != HAL_OK)
        Error_Handler();
}

static void MX_GPIO_Init(void)
{
    __HAL_RCC_GPIOB_CLK_ENABLE();
}

void Error_Handler(void)
{
    __disable_irq();
    while (1) {}
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
