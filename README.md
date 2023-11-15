# Bootloader with Application 

Following steps used in creating above applicaiton work in Nucleo Board

-  basics of the linker scrip
-  how to place variables in RAM and FLASH 
-  how to create functions in RAM and FLASH 
-  how to create a super simple bootloader 
-  how to offset the interrupt vector and create an application to run in a different memory region 
-  how to debug bootloader and application 
-  how to share an API between bootloader and application


## 1. Create : New Project
  In windows 11 Machine,  Choose CUBEIDE application and click on it
  
### 1.1 Open CubeIDE
When STM32CubeIDE is started the first time, the Information Center opens.The Information Center enables the user to quickly reach information about the product and how to use it. The Board selector  tabs can be selected at the top of the window. Use the first tab to create a project for a specific device.

![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/b323e9d6-7997-4d5e-a0d4-6fbbdcaecbda)

###  1.2 Choose Board and IC part Number

In Board selector give the Commercial Part Number - NUCLEO-L496ZG-P.select Nucleo-L496ZG-P in the  Board list item.

  ![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/07b08d01-ff1e-4072-9d2c-38b3687d9dec)
  
###  1.3 Create  New Folder and Provide Name
To create a Project, go to [File]>[New]>[C/C++ Project]. This opens the window displayed in the below figure.

![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/d2a434c8-c47f-406b-80bf-3461c94f6807)

### 1.4 Finish
Project folder is created click on next -> finish.

##  2. Resource Allocation

### 2.1 Pin B7 is allocated for LED ( GPIO output pin)
After creating a new project it opens Pinout & Configuration in that select GPIO PB7 pin as LED (GPIO_out) as per the datasheet.
PB7- LED [GPIO_OUT]

### 2.2 UART 3 is allocated ( Asynch mode)
In Pin configuratiuon Click on connectivity select USART 3 as an asynchronous mode as per the data sheet.
PC4 - USART 3_TX [Asynchronous Mode]
PC5 - USART 3_RX

![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/cb3ae49d-d59c-46a8-9699-1d4839253a49)

## 3. Code Generation and Build
Once you created the project and enabled the GPIO (PB7) and UART3 (PC4 and PC5), now we will have the auto generated code.It will open the main.c file in this working space we have to implement our code.
—> In main.c use the printf that will be redirected to the UART3.

![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/17545e33-d311-4759-a65c-864271655c87)

As refer to above figure we have separate binaries for boot loader and its application each binary as its own vector table.we have placed the bootloader at 0x00000000 and application into some other location.when the microcontroller powers up ,it will run the bootloader first as it sits in 0x00000000 location.It takes the stack address from 0x00000000 and stores that to the msp register.Then it reads the reset handler’s address and goes to the reset handler of the bootloader .it does some operations .once it has finished its job it will call the main function.After that it calls the applications vector table and we have to update the MSP with the application’s stack address.Then it takes the applications reset handler address and calls the reset handler in the reset handler,it does the same operation that was explained and calls the main function.

We have created one variable for the Bootloader version. And, we have created the goto_application function which calls the application reset handler.We know the vector table address (0x08040000) of the application. In that address, stack memory’s starting address will be present. Then in the ( 0x08040000 + 4) address, the application reset handler’s address will be present. Now we have the application reset the handler's address. Then using the function pointer, we can call the application’s reset handler. Simple right. Before jumping to the application,turn off the Green LED (PB0).

Once you have done this, then we have to tell the controller that the vector table address is changed to 0x08040000. To do that, go to the application source code -> core -> Src -> system_stm32_f7xx.c.

Create a file
1. ELF File creation
2. Bin File Creation

## 4.  Connect Target Nucleo Board with STlink ( for Flash )

Lets Flash the bootloader program using ST-LINK utility software. Open the ST-LINK utility software connect it to nucleo board.
1).Convert .elf to binary file  —Ref:/dev/doc/handy.
2).Open the STM32 ST link utility software, erase the complete chip
3).See the flash section 0x08000000 it is empty
4).Flash the bootloader 
5).Check the serial terminal, we got bootloader print not the application because application memory is empty, so application has not been called.
6).Goto application file & flash, at that time clear serial terminal.

## 5.  Flash Boot Loader Application on Nucleo Board

Hardware connection - Connect Nucleo-L496ZG-P Board to the PC through USB cable and ST-LINK V2 as shown in the figure.
![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/2c8a8fc7-17f0-46e1-9c76-a671fcc36106)

![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/20332309-64af-4296-8afb-188c56d3b948)

![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/32048083-c8c1-46cc-ac23-d481a4b361ee)

## 6.  Connect Nucleo Board with PC ( for Powersupply alone )
After Flash it connect Nucleo board with PC through USB cable the LED should turn on. As shown in the below figure.

![image](https://github.com/DLinIoTedge/Harpy/assets/144312729/682e8a06-3fb4-4aa3-aa07-2eb0c7675cfa)

