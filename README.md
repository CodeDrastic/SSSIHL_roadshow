# SSSIHL_roadshow
  
This repository shares my experience and learning in the VLSI Chip Design "Design Chip at $0 " conducted by VLSI System Development at SSSIHL

# Procedure
  Two software were installed
* Slack(for interaction and sharing screenshots of the process)
* Virtual box(to create a virtual os Ubuntu 18.04)
# using ubuntu 

  * We first start by opening ubuntu via virtual box.

![Screenshot 2024-12-17 140541](https://github.com/user-attachments/assets/3f3498cd-a1a1-4ec2-901a-d967e7ecd51a)

  * A new terminal window was opened,
     and the following code was typed to install gedit.
 
       *  ` sudo apt install gedit`
 
![Screenshot 2024-12-17 140853](https://github.com/user-attachments/assets/6a10b01b-ed55-42fc-ba3a-adb175987583)

 
  * using the installed gedit tool as follows
 
![Screenshot 2024-12-17 141005](https://github.com/user-attachments/assets/04a0a95f-ab17-4543-b8c1-9a74f33de796)


  * The following code was written and stored in .c format file

```
#include<stdio.h>
int main(){
    int sum = 0, i, n;
    printf("Enter the value of n = ");
    scanf("%d",&n);
    for(i = 1;i <= n;i++){
       sum = sum + i;
    }
    printf("The Sum of numbers from 1 to %d is %d\n",n,sum);
    return 0;
}
```


![Screenshot 2024-12-17 142133](https://github.com/user-attachments/assets/b5fbf5f2-9e4e-4ce7-8a0c-6d602e4ceb74)

* This following code gives the sum from one to (any chosen number of your choice)
 * These two comands in terminal to run the code
   * `gcc {file name}.c`
   * `./a.out`

  ![Screenshot 2024-12-17 142904](https://github.com/user-attachments/assets/4e460bd8-7456-470e-ab87-34c735486f57)

#Using RISC-V architecture
 The two codes required to do so are listed as follows;-
 * ``riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o {filename}.o {filename}.c``
 * ``iscv64-unknown-elf-objdump -d {filename}.o``

  The results were as follows
  
![Screenshot 2024-12-17 143436](https://github.com/user-attachments/assets/c9cc9f70-1f59-48e4-97a8-424c9334ecd8)



# Openlane
 Openlane a opensource software was used to preview and design the chip virtually and can even be used to optimise the chip
 *Firstly the current working directory was changed to Openlane using the 
   following code along with opening the docker .
   `cd Desktop/work/tools/openlane_working_dir/openlane`
   `package require openlane 0.9`
   `prep -design picorv32a`
   
   
   ![Screenshot 2024-12-17 143656](https://github.com/user-attachments/assets/276fa607-c0ef-4d0e-b606-05fbacb48e54)
   ![Screenshot 2024-12-17 143805](https://github.com/user-attachments/assets/839f4582-caba-4459-b235-45942bd3c186)

* The following command was run to sythesize the digital I.C.
  ` run_synthesis`
  and the results are as follows 


![Screenshot 2024-12-17 144116](https://github.com/user-attachments/assets/f11d9684-69eb-4496-8db9-6d7f190645da)

* Then  starting the floorplan stage using the following command
* `run_floorplan`
* the output of which is given as
  
![Screenshot 2024-12-17 144214](https://github.com/user-attachments/assets/5334cb62-34b3-45dd-a0d1-991d7e1d641d)

* Then opening a tab in terminal the following code run
`eog designs/picorv32a/runs/{press tab as it varies from device to device}/results/floorplan/picorv32a.floorplan.def.png`
* This results in opening a png of the floorplan of the I.C.
   

![Screenshot 2024-12-17 150416](https://github.com/user-attachments/assets/40c9d4d8-e06b-4ac6-8b40-0aad6cfba804)

* Then the placement stage was run using the following command .
* `run_placement`
* Which results in a output as given in the following screenshot
  
![Screenshot 2024-12-17 150524](https://github.com/user-attachments/assets/c4a0efb7-ec47-4bb9-adb8-3a113d7f65ff)


 * The img of the floorplan was viewed by the following command
 * `eog designs/picorv32a/runs/{press tab as it varies from device to device}/results/placement/picorv32a.placement.def.png`

![Screenshot 2024-12-17 150808](https://github.com/user-attachments/assets/2a0519a3-40dd-4135-a225-bb85aab6f0c4)

* Then we check for  Clock Tree Synthesis (CTS) to check the clock distribution of the I.C.
  `run_cts`
  ![Screenshot 2024-12-17 151054](https://github.com/user-attachments/assets/289fae0c-32a7-464d-b0af-197aa1c091f7)

  *Checking the routing of the virtual I.C. which creates the physical interconnections that make up the logic and clock paths of the design.
  `run_routing`
![Screenshot 2024-12-17 152915](https://github.com/user-attachments/assets/c8f5738e-b3d9-42e8-9e44-fea5e7577219)

Hence we have tested the chip and it can be send to a manufacturer.

# Using platformer to run and upload a code in VSD Squadron
 # a)Blinkink light with varing time.
  * First the following code was run and uploaded in the chip.
```
    #include <ch32v00x.h>
#include <debug.h>

#define BLINKY_GPIO_PORT GPIOD
#define BLINKY_GPIO_PIN GPIO_Pin_6
#define BLINKY_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)

void NMI_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void HardFault_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void Delay_Init(void);
void Delay_Ms(uint32_t n);

int main(void)
{
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
	SystemCoreClockUpdate();
	Delay_Init();

	GPIO_InitTypeDef GPIO_InitStructure = {0};

	BLINKY_CLOCK_ENABLE;
	GPIO_InitStructure.GPIO_Pin = BLINKY_GPIO_PIN;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(BLINKY_GPIO_PORT, &GPIO_InitStructure);

	uint8_t ledState = 0;
	while (1)
	{
		GPIO_WriteBit(BLINKY_GPIO_PORT, BLINKY_GPIO_PIN, ledState);
		ledState ^= 1; // invert for the next run
		Delay_Ms(100);
	}
}

void NMI_Handler(void) {}
void HardFault_Handler(void)
{
	while (1)
	{
	}
}
```


https://github.com/user-attachments/assets/5b24fc55-3443-4d01-bd77-ea2342d61509


* here the ``Delay_Ms(100)`` its value was changed to produce diffrent timing of blinking.
# b)Making the led slowly fade in and out
 * The following code was used
 ```
#include "debug.h"
#define TIME_PERIOD 1000
#define PRESC       0
#define PULSE       632
#define STEP_SIZE   10
volatile u16 val;
volatile u8 dir;
void TIM1_PWMOut_Init(void){
    GPIO_InitTypeDef GPIO_InitStructure={0};
    TIM_OCInitTypeDef TIM_OCInitStructure={0};
    TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure={0};
    //RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOC | RCC_APB2Periph_TIM1, ENABLE );
    RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOD | RCC_APB2Periph_AFIO, ENABLE );
    RCC_APB1PeriphClockCmd( RCC_APB1Periph_TIM2, ENABLE );
    GPIO_PinRemapConfig(GPIO_FullRemap_TIM2, ENABLE);
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init( GPIOD, &GPIO_InitStructure);
    TIM_TimeBaseInitStructure.TIM_Period = TIME_PERIOD;
    TIM_TimeBaseInitStructure.TIM_Prescaler = PRESC;
    TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;
    TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInit( TIM2, &TIM_TimeBaseInitStructure);
    TIM_OCInitStructure.TIM_OCMode = TIM_OCMode_PWM2;
    TIM_OCInitStructure.TIM_OutputState = TIM_OutputState_Enable;
    TIM_OCInitStructure.TIM_Pulse = PULSE;
    TIM_OCInitStructure.TIM_OCPolarity = TIM_OCPolarity_High;
    TIM_OC3Init( TIM2, &TIM_OCInitStructure );
    TIM_CtrlPWMOutputs(TIM2, ENABLE );
    //TIM_OC3PreloadConfig( TIM1, TIM_OCPreload_Disable );
    //TIM_ARRPreloadConfig( TIM1, ENABLE );
    TIM_Cmd( TIM2, ENABLE );
}
int main(void)
{
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
    SystemCoreClockUpdate();
    Delay_Init();
    TIM1_PWMOut_Init();
    val = 0;
    dir = 0;
    // Loop
    while(1){
        val += (dir) ? -STEP_SIZE : STEP_SIZE;
        TIM_SetCompare3(TIM2, val);
        dir ^= (val == 1000 || val == 0) ? 1 : 0;
        Delay_Ms(15);
    }
}

```

  

https://github.com/user-attachments/assets/27aa936f-813e-4bd9-8496-afc8fa7783d8


  
