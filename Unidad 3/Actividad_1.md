ACTIVIDAD 1 

1. Introducción
    El ADC permite a un microcontrolador ARM Cortex-M capturar señales del mundo real (voltajes, temperatura, sonido) convirtiéndolas de analógico a digital. Es fundamental para sistemas de monitoreo y control.
2. Señal Analógica
    Continúa en el tiempo y en amplitud. Ejemplos: voltajes, ondas sonoras, variaciones de temperatura.
3. Cuantización
    Aproxima la amplitud continua a niveles discretos (2ⁿ niveles para n bits). A mayor número de bits, más fiel la representación.
4. Muestreo
    Se toman muestras periódicas según una frecuencia de muestreo (fs). Según Nyquist, fs ≥ 2·f_max de la señal original para evitar aliasing.
    Errores en la conversión
    Cuantización: diferencia entre valor real y nivel digital (±½ LSB).
    Alias: muestrear por debajo de Nyquist provoca distorsión.
    Ruido de cuantización, timing jitter, linealidad imperfecta.

5. Tipos de ADC
    Flash: muy rápido (con comparadores paralelos), alto coste y consumo.
    Rampa/Escalera: sencillo, preciso pero lento (mide el tiempo que tarda una rampa en alcanzar la señal).
    SAR (Aproximaciones sucesivas): equilibrado en velocidad y precisión, común en microcontroladores.
7. Parámetros clave
    Resolución (n bits → 2ⁿ niveles).
    Valor LSB = V_REF/(2ⁿ–1).
    Rango dinámico y error de cuantización ±½ LSB.
8. ADC en STM32F4
    Hasta 3 módulos, 12 bits SAR, 5.33 Msps.
    Modos: single-shot, continuo, escaneo, discontinuo, inyectado.
    Triggers: software o externos; soporte DMA e interrupciones.
9. Reloj del ADC
Debe quedar ≤ 36 MHz. Se obtiene de APB2 con prescaler (p.ej. 72 MHz/4 → 18 MHz).


```c
#include "main.h"
#include <stdio.h>
#include "stm32f4xx.h" // <--- IMPORTANTE para ITM_SendChar

ADC_HandleTypeDef hadc1;

void SystemClock_Config(void);
static void MX_GPIO_Init(void);
static void MX_ADC1_Init(void);

int main(void)
{
  HAL_Init();
  SystemClock_Config();
  MX_GPIO_Init();
  MX_ADC1_Init();

  uint32_t adc_value;

  while (1)
  {
    HAL_ADC_Start(&hadc1);
    if (HAL_ADC_PollForConversion(&hadc1, HAL_MAX_DELAY) == HAL_OK)
    {
      adc_value = HAL_ADC_GetValue(&hadc1);
      printf("ADC Value: %lu\r\n", adc_value);  // Salida por ITM/SWV
    }
    HAL_Delay(500);
  }
}

// Redefinir _write para que printf funcione sobre ITM
int _write(int file, char *ptr, int len) {
    for (int i = 0; i < len; i++) {
        ITM_SendChar(*ptr++);
    }
    return len;
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
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
  RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSI;
  RCC_OscInitStruct.PLL.PLLM = 16;
  RCC_OscInitStruct.PLL.PLLN = 192;
  RCC_OscInitStruct.PLL.PLLP = RCC_PLLP_DIV2;
  RCC_OscInitStruct.PLL.PLLQ = 4;
  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK) Error_Handler();

  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
                              |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV4;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV4;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV16;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_0) != HAL_OK) Error_Handler();
}

static void MX_ADC1_Init(void)
{
  ADC_ChannelConfTypeDef sConfig = {0};

  hadc1.Instance = ADC1;
  hadc1.Init.ClockPrescaler = ADC_CLOCK_SYNC_PCLK_DIV2;
  hadc1.Init.Resolution = ADC_RESOLUTION_12B;
  hadc1.Init.ScanConvMode = DISABLE;
  hadc1.Init.ContinuousConvMode = ENABLE;
  hadc1.Init.DiscontinuousConvMode = DISABLE;
  hadc1.Init.ExternalTrigConvEdge = ADC_EXTERNALTRIGCONVEDGE_NONE;
  hadc1.Init.ExternalTrigConv = ADC_SOFTWARE_START;
  hadc1.Init.DataAlign = ADC_DATAALIGN_RIGHT;
  hadc1.Init.NbrOfConversion = 1;
  hadc1.Init.DMAContinuousRequests = DISABLE;
  hadc1.Init.EOCSelection = ADC_EOC_SINGLE_CONV;
  if (HAL_ADC_Init(&hadc1) != HAL_OK) Error_Handler();

  sConfig.Channel = ADC_CHANNEL_0;
  sConfig.Rank = 1;
  sConfig.SamplingTime = ADC_SAMPLETIME_15CYCLES;
  if (HAL_ADC_ConfigChannel(&hadc1, &sConfig) != HAL_OK) Error_Handler();
}

static void MX_GPIO_Init(void)
{
  __HAL_RCC_GPIOA_CLK_ENABLE();
}

void Error_Handler(void)
{
  __disable_irq();
  while (1) { }
}
