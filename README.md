

# 1.Line tracer
>Project 개요
* 적외선 센서를 활용한 차선 이탈 방지 시스템 구현과 STM32의 PWM을 통한 DC모터 제어 실습 
> PWM(Pulse Width Modulation) 이란?

* 펄스폭 변조로 만들어지는 신호로서 , 한 주기동안 H와 L의 비율(Duty rate)를 변화시켜 변조하는 방식이다.
* ![pwm](https://user-images.githubusercontent.com/112140633/201796057-af78ded9-30cd-4a9f-89f9-28633fce9719.png)
>1KHz 주기의 PWM 생성 하기	
* Hz - 주파수의 단위이다. 1Hz는 1초에 1번의 클럭을 뜻한다.
* Prescaler - 흔히 분주비라 불리며 타이머의 클럭을 나눠주는 역할이다.
* Counter Period - 타이머의 한 주기를 결정하는 값. counter가 증가하다 Period 값이 되면 0으로 초기화 후 Prescaler의 값이 1 증가한다.
* Counter  - 1 clock에 count 1
	![설정 1](https://user-images.githubusercontent.com/112140633/201798381-bcc011f2-2455-43d8-a45a-5e8a92a3ef06.png)
![설정 2](https://user-images.githubusercontent.com/112140633/201798388-20c55fed-a742-4b65-b914-0830d8069cd4.png)

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
```
 * Line Tracer 동작 화면
 
 ![ezgif com-gif-maker (3)](https://user-images.githubusercontent.com/112140633/201580315-3b72cb94-cbe6-4a14-abdd-e909fd8cacfb.gif)
 
 >개선 사항 및 아쉬운 점
  * DC motor 4개를 구동하기에 9v Battery의 전류가 부족하여 원하는 동작을 수행하지 못함.
  * 낮은 전류로 인해 방향 전환 시 매끄럽지 않다.
  * 9v Battery 2개를 사용하여 전류를 높여 방향 전환 시 정상적인 작동을 확인하였다.
