

# 1.Line tracer
>Project 개요
* 적외선 센서를 활용한 차선 이탈 방지 시스템 구현과 STM32의 PWM을 통한 DC모터 제어 실습 
 * 사용 한 센서
   * TRTC5000
   * L298N
   * DC 모터
   * STM32F103
 *Line tracer 배선도
 ![linetracer 배선도](https://user-images.githubusercontent.com/112140633/201577422-80908d26-f0df-44dd-bc38-181ea5014ec2.png)
 ```C
 //line tracer 주요 동작 함수
 
 //TRTC5000 Sensor의 감지를 통한 DC 모터 동작 제어
 
 if(HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_4) == GPIO_PIN_RESET &&
	  HAL_GPIO_ReadPin(GPIOB, GPIO_PIN_0) == GPIO_PIN_SET &&
	  HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_1) == GPIO_PIN_RESET &&
	  HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_0) == GPIO_PIN_RESET)
	  {
	  		Moter_Left();
	  }

if(HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_4) == GPIO_PIN_SET &&
	  		HAL_GPIO_ReadPin(GPIOB, GPIO_PIN_0) == GPIO_PIN_RESET &&
	  		HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_1) == GPIO_PIN_RESET &&
	  		HAL_GPIO_ReadPin(GPIOC, GPIO_PIN_0) == GPIO_PIN_RESET)
	  		{
	  			Moter_Left_2();
	  		}
        
 //좌회전 모터 제어 함수
 
 void Moter_Left()
{

	//Moter_front_L
	__HAL_TIM_SET_COMPARE(&htim2,TIM_CHANNEL_1,0);           //왼쪽 front DC모터 pulse 0 변환
	HAL_GPIO_WritePin(GPIOA, GPIO_PIN_10, GPIO_PIN_RESET);
	HAL_GPIO_WritePin(GPIOB, GPIO_PIN_5, GPIO_PIN_SET);

	//Moter_rear_L
	__HAL_TIM_SET_COMPARE(&htim2,TIM_CHANNEL_2,0);          //왼쪽 rear DC모터 pulse 0 변환
	HAL_GPIO_WritePin(GPIOB, GPIO_PIN_4, GPIO_PIN_RESET);
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_8, GPIO_PIN_SET);

    //Moter_front_R
	__HAL_TIM_SET_COMPARE(&htim3,TIM_CHANNEL_3,999);        //오른 쪽 front DC모터 pulse 999 변환
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, GPIO_PIN_RESET);
    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_7, GPIO_PIN_SET);

    //Moter_rear_R                    
	__HAL_TIM_SET_COMPARE(&htim3,TIM_CHANNEL_4,999);        //오른 쪽 rear DC모터 pulse 999 변환
	HAL_GPIO_WritePin(GPIOB, GPIO_PIN_6, GPIO_PIN_RESET);
	HAL_GPIO_WritePin(GPIOA, GPIO_PIN_7, GPIO_PIN_SET);

}
 //역회전을 통한 큰 각도의 회전
void Moter_Left_2()
{

	//Moter_front_L
	__HAL_TIM_SET_COMPARE(&htim2,TIM_CHANNEL_1,700);          //왼쪽 front DC 모터의 역회전
	HAL_GPIO_WritePin(GPIOA, GPIO_PIN_10, GPIO_PIN_SET);
	HAL_GPIO_WritePin(GPIOB, GPIO_PIN_5, GPIO_PIN_RESET);

	//Moter_rear_L
	__HAL_TIM_SET_COMPARE(&htim2,TIM_CHANNEL_2,700);          //왼쪽 rear DC 모터의 역회전
	HAL_GPIO_WritePin(GPIOB, GPIO_PIN_4, GPIO_PIN_SET);
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_8, GPIO_PIN_RESET);

    //Moter_front_R
	__HAL_TIM_SET_COMPARE(&htim3,TIM_CHANNEL_3,999);          //오른쪽 fornt 모터 정회전 pulse 999
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, GPIO_PIN_RESET);
    HAL_GPIO_WritePin(GPIOC, GPIO_PIN_7, GPIO_PIN_SET);

    //Moter_rear_R
	__HAL_TIM_SET_COMPARE(&htim3,TIM_CHANNEL_4,999);          //오른 쪽 rear 모터 정회전 pulse 999
	HAL_GPIO_WritePin(GPIOB, GPIO_PIN_6, GPIO_PIN_RESET);
	HAL_GPIO_WritePin(GPIOA, GPIO_PIN_7, GPIO_PIN_SET);

}
 
 * Line Tracer 동작 화면
 
 ![ezgif com-gif-maker (3)](https://user-images.githubusercontent.com/112140633/201580315-3b72cb94-cbe6-4a14-abdd-e909fd8cacfb.gif)
