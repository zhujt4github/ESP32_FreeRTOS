#### ESP32仿真网站

https://wokwi.com/

#### 2种延时宏用法:

1. ```c
   vTaskDelay(1000 / portTICK_PERIOD_MS);
   ```

2. ```c
   vTaskDelay( pdMS_TO_TICKS(1000) );
   ```

#### MUTEX

mutual exclusion 互斥现象的缩写

#### 多核心任务创建

```c
xPortGetCoreID() //获取当前任务运行的核心
```

```c
xTaskCreate() //由系统选择运行核心，优先选择core0
```

```c
xTaskCreatePinnedToCore() //指派任务给指定核心
```

#### u8g2库使用

1. 头文件包含

   ```c 
   #inlcude <u8g2lib.h>
   ```

2. 结构体声明

   ```c
   // 使用硬件默认的IIC接口
   U8G2_SSD1306_128X64_NONAME_F_HW_I2C u8g2(U8G2_R0, /* reset=*/ U8X8_PIN_NONE);
   // 使用映射的硬件IIC接口
   U8G2_SSD1306_128X64_NONAME_F_HW_I2C u8g2(U8G2_R0, /* reset=*/ U8X8_PIN_NONE, /* clock=*/ 16, /* data=*/ 17);
   // 使用软件模拟IIC带复位pin
   U8G2_SSD1306_128X64_NONAME_F_SW_I2C u8g2(U8G2_R0, /* clock=*/ 13, /* data=*/ 11, /* reset=*/ 8);
   // 使用软件模拟IIC没有复位pin
   U8G2_SSD1306_128X64_NONAME_F_SW_I2C u8g2(U8G2_R0, /* clock=*/ SCL, /* data=*/ SDA, /* reset=*/ U8X8_PIN_NONE);
   /*--------------------------------------------------------------------------------*/
   // 使用软件模拟4线SPI
   U8G2_SSD1306_128X64_NONAME_F_4W_SW_SPI u8g2(U8G2_R0, /* clock=*/ 13, /* data=*/ 11, /* cs=*/ 10, /* dc=*/ 9, /* reset=*/ 8);
   // 使用硬件4线SPI
   U8G2_SSD1306_128X64_NONAME_F_4W_HW_SPI u8g2(U8G2_R0, /* cs=*/ 10, /* dc=*/ 9, /* reset=*/ 8);
   // 使用软件模拟3线SPI
   U8G2_SSD1306_128X64_NONAME_F_3W_SW_SPI u8g2(U8G2_R0, /* clock=*/ 13, /* data=*/ 11, /* cs=*/ 10, /* reset=*/ 8);
   // 使用硬件3线SPI
   U8G2_SSD1306_128X64_NONAME_F_3W_HW_SPI u8g2(U8G2_R0, /* cs=*/ 10, /* reset=*/ 8);
   ```

   结构体各部分说明

   ```c
   /*
   U8G2	库名称
   SSD1306	控制芯片
   128X64	分辨率
   NONAME	型号
   F		缓存区大小  可设置1，2，F
   3W		几线制		可是之3，4
   HW_SPI	控制协议	可设置 HW_SPI,SW_SPI,HW_IIC,SW_IIC
   
   U8G2_R0	旋转角度 可设置 U8G2_R0,U8G2_R1,U8G2_R2,U8G2_R3,U8G2_MIRROR,U8G2_MIRROR_VERTICAL
   */
   ```

3. 初始化

   ```c
   void setup(void){
       u8g2.begin();
   }
   ```

4. full buffer, page buffer, u8x8 mode

   ##### full buffer

   - Fast
   - All graphics procedures can be used
   - Requires a lot of RAM

   ```c
   void loop(void) {
     u8g2.clearBuffer();
     u8g2.setFont(u8g2_font_ncenB14_tr);
     u8g2.drawStr(0,20,"Hello World!");
     u8g2.sendBuffer();
   }
   ```

   ##### page buffer(picture loop)

   - All graphics procedures can be used
   - Only a little bit of RAM is required
   - Slow

   ```c
   void loop(void) {
     u8g2.firstPage();
     do {
       u8g2.setFont(u8g2_font_ncenB14_tr);
       u8g2.drawStr(0,24,"Hello World!");
     } while ( u8g2.nextPage() );
   }
   ```

   ##### U8x8 character mode

   - Fast
   - No RAM required
   - No graphics possible
   - Not available for all displays

   ```c
   void loop(void) {
     u8x8.setFont(u8x8_font_chroma48medium8_r);
     u8x8.drawString(0,1,"Hello World!");
   }
   ```

   

#### TFT_eSPI 库的使用

##### 驱动 ESP32 TTGO T-Display V1.1

1. 在Arduino的库管理器中添加TFT_eSPI库。

2. 在该库的路径下（C:\Users\xx\Documents\Arduino\libraries\TFT_eSPI）打开User_Setup.h文件。

   注释掉`#define ILI9341_DRIVER       // Generic driver for common displays`

3. 打开`User_Setup_Select.h`,

   取消注释`#include <User_Setups/Setup25_TTGO_T_Display.h>    // Setup file for ESP32 and TTGO T-Display ST7789V SPI bus TFT`

   真正的配置文件是`User_Setups/Setup25_TTGO_T_Display.h`

4. 编写测试程序

   ```c
   #include <TFT_eSPI.h>
   TFT_eSPI tft = TFT_eSPI();  // Invoke custom library
   void setup() {
     // put your setup code here, to run once:
     tft.init();
     tft.fillScreen(TFT_BLACK);
     // Set "cursor" at top left corner of display (0,0) and select font 4
     tft.setCursor(0, 0, 4);
     // Set the font colour to be white with a black background
     tft.setTextColor(TFT_WHITE, TFT_BLACK);
     // We can now plot text on screen using the "print" class
     tft.println("Hello TFT");
     tft.println("White text");
     tft.setTextColor(TFT_RED, TFT_BLACK);
     tft.println("Red text");
     tft.setTextColor(TFT_GREEN, TFT_BLACK);
     tft.println("Green text");
     tft.setTextColor(TFT_BLUE, TFT_BLACK);
     tft.println("Blue text");
   }
   
   void loop() {
     // put your main code here, to run repeatedly:
   
   }
   ```


##### 显示中文

TFT_eSPI库中自带一个将字体文件转换成c文件的程序。

路径如下：`TFT_eSPI ->Tools-->Create_Smooth_Font-->Create_font `

在这个目录下有3个文件

- [ ] data : 存放我们的字体文件,用ttf结尾的,这个文件可在电脑自带系统找到，路径如下：C:\Windows\Fonts。找到喜欢的字体后将其复制到data文件夹下.

- [ ] FontFiles : 存放我们制作出来的字库文件,制作出来后是vlw结尾的 .

- [ ] Create_font.pde : 官方提供的示例程序,通过该程序来制作字库文件。 

**使用processing创建字库**

使用processing打开Create_font.pde文件（https://processing.org/ 下载processing软件,并且安装）。并修改几个地方如下:

```c
...........
String fontName = "STXINGKA";  // Manually crop the filename length later after creation if needed//定义存放在data文件夹中的字体文件的文件名
                               // Note: SPIFFS does NOT accept underscore in a filename!
String fontType = ".ttf"; //定义存放在data文件夹中的字体文件的后缀
//String fontType = ".otf";

// Define the font size in points for the TFT_eSPI font file
int  fontSize = 50;//自定义字库的大小，中文应不低于16，太小了就模糊看不清了。

// Font size to use in the Processing sketch display window that pops up (can be different to above)
int displayFontSize = 50;	//这个是程序运行后弹出对话框，输出字库里的所有字符。

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
// Next we specify which unicode blocks from the the Basic Multilingual Plane (BMP) are included in the final font file. //
// Note: The ttf/otf font file MAY NOT contain all possible Unicode characters, refer to the fonts online documentation. //
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

static final int[] unicodeBlocks = {//输如Unicode编码编码的起始码值和结束码值
  // The list below has been created from the table here: https://en.wikipedia.org/wiki/Unicode_block
  // Remove // at start of lines below to include that unicode block, different code ranges can also be specified by
  // editting the start and end-of-range values. Multiple lines from the list below can be included, limited only by
  // the final font file size!
  //0x0021, 0x007E, //Basic Latin, 128, 128, Latin (52 characters), Common (76 characters)
};

// Here we specify particular individual Unicodes to be included (appended at end of selected range)
static final int[] specificUnicodes = {//输入需要生成字库的汉字的码值
0x64b8,0x8d77,0x8896,0x5b50,0x52a0,0x6cb9,0x5e72,0xff01//撸起袖子加油干！
};
//中文转unciode网站 http://tool.chinaz.com/tools/unicode.aspx
```

每个汉字对应的unicode码值可以通过在线转换工具获取，然后将转换后的/u替换为0x即可。

完成修改后，点击运行，弹出对话框显示自定义库中的所有字符,同时在FontFiles文件夹中生成一个STXINGKA50.vlw文件，存放我们制作出来的字库文件。

**使用在线转换工具将vlw文件转换成Arduino使用的字库文件**

通过https://tomeko.net/online_tools/file_to_hex.php?lang=zh，将vlw文件转换成Arduin使用的字库文件xxxFont.h

将生成的16进制数据按照如下格式放到`font_50.h`中

```c
#include <pgmspace.h>
const uint8_t  font_50[] PROGMEM = {
//把生成的16进制数据放到这里面
};
```

**应用实例**

```c
#include <TFT_eSPI.h>
#include "font_50.h"//包含字库
TFT_eSPI tft = TFT_eSPI();  // 引脚请自行配置tft_espi库中的 User_Setup.h文件

void setup()
{
  //tft液晶屏初始化设置
  tft.init();
  tft.setRotation(0);
  tft.fillScreen(0x0000);
  tft.setTextColor(TFT_RED, TFT_BLUE);//红色汉字，蓝色背景
  tft.loadFont(font_50); //指定tft屏幕对象载入font_50字库
  tft.drawString("撸起袖子",0,100); //在坐标0,0位置输出汉字,就可以在tft显示出来了
  tft.drawString("加油干！",0,150);
  tft.unloadFont(); //释放字库文件,节省资源
}
void loop(){
  while(1){
    delay(2000);
  }
}
```

#### 看门狗

说明：看门狗是针对Task任务的

> ​    Arduion-ESP32 默认在 Core 0 的 IDLE 任务开启了看门狗 时间为 5000 ticks = 5秒。
>
> ​    Core 0 和 Core 1 都运行了 FreeRTOS的IDLE任务，优先级为 0。IDLE任务是用于清理被删除任务的内存。
>
> ​    Core 1 loopBack任务就是Arduino的 setup 和 loop 优先级为 1。

看门狗功能需要引进头文件`#include "esp_task_wdt.h" `

**添加看门狗**  `esp_task_wdt_add(NULL)`

**移除看门狗**  `esp_task_wdt_delete(NULL)`

**喂食**             `esp_task_wdt_reset()`
**关闭看门狗**  `disableCore0WDT()`
