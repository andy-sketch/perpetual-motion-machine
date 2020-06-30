# perpetual motion machine

犀浦一霸
========
代码到实物课 犀浦一霸小组

团队介绍
========
本组名称为犀浦一霸，由张恒源，王均楷，高枫，梁傲泷，龚祥五名成员组成。五人都是2019级大一新生，
从一无所知到慢慢清晰，不断挑战自我，互相配合，共同成长。

人员分工
======
控制与电路 张恒源 高枫

3d建模： 王均楷

UI设计   梁傲泷 龚祥

设计思考
========
疫情期间，出入各种场合均需要出示健康码，而本小组考虑到许多老人并不能够熟练地使用手机并且出示健康码，
便想到制作一款便于携带的小挂件能够随时简单的显示健康码，灵活轻便，有利于老人更好地适应疫情期间的生活。
除此之外，该装置还可以具有天气预报等简单功能，方便老人的日常生活。

材料
======
ESP32(oled墨水屏)  
![image](https://github.com/andy-sketch/perpetual-motion-machine/blob/master/images/IMG_20200630_184802.jpg)   

外设展示
=======
![image](https://github.com/andy-sketch/perpetual-motion-machine/blob/master/images/27A51970C41FAAD1358EC1D81E7D9AC3.png)  
![image](https://github.com/andy-sketch/perpetual-motion-machine/blob/master/images/461C8AC4D8935CDB4637BDF96AFEABDC.png)    
![image](https://github.com/andy-sketch/perpetual-motion-machine/blob/master/images/D019F2FBBC0818A30D6ADBF69D43A1B5.png)  

程序代码
======

(操作系统)
theme: jekyll-theme-cayman  
#include <Arduino.h>  
#include <U8g2lib.h>  
#include <Wire.h>  

#define UP_KEY      D6 //gpio12  
#define M_KEY       D5 //gpio14  
#define DOWN_KEY    D7 //gpio13  

//读取键值  
#define keyup     digitalRead(UP_KEY)  
#define keyenter  digitalRead(M_KEY)  
#define keydown   digitalRead(DOWN_KEY)  

uint8_t func_index=0;  
void (*current_operation_index)();  
void fun1();  
void fun2();  
void fun3();  
void fun4();  
void page_1_to_4();  

typedef struct  
{  
  uint8_t current;  
  uint8_t up;//向上翻索引号  
  uint8_t down;//向下翻索引号  
  uint8_t enter;//确认索引号  
  void (*current_operation)();  
}key_table;  

key_table table[7]=  
{  
  {0,3,1,0,(*fun1)},  
  {1,0,2,1,(*fun2)},  
  {2,1,3,2,(*fun3)},  
  {3,2,0,3,(*fun4)},  
};  

U8G2_SSD1306_128X64_NONAME_F_HW_I2C u8g2(U8G2_R0, /* reset=*/ U8X8_PIN_NONE);  

void setup(void)  
{  
  u8g2.begin();  
  u8g2.enableUTF8Print();  
  ESP.wdtEnable(5000);  
}  

void loop(void)  
{  
  
  if((keyup==0)||(keydown==0)||(keyenter==0))  
  {  
    delay(10);//消抖  
    if(keyup==0)  
    {
    func_index=table[func_index].up;    //向上翻  
    while(!keyup)ESP.wdtFeed();//  
   }  
    if(keydown==0)  
    {  
      func_index=table[func_index].down;    //向下翻  
      while(!keydown)ESP.wdtFeed();//  
    }  
    if(keyenter==0)  
   {  
      func_index=table[func_index].enter;    //确认  
      while(!keyenter)ESP.wdtFeed();//  
   }  
  }  
   current_operation_index=table[func_index].current_operation;  
  (*current_operation_index)();//执行当前操作函数  
  ESP.wdtFeed();//  
}  

void fun1(){  
  u8g2.clearBuffer();  
  page_1_to_4();  
  //显示光标  
  u8g2.setFont(u8g2_font_open_iconic_all_1x_t);  
  u8g2.drawGlyph(5, 16*(1%4==0? 4:1%4)-4,118);  
  u8g2.sendBuffer();  
}  
void fun2(){  
  u8g2.clearBuffer();  
  page_1_to_4();  
  //显示光标  
  u8g2.setFont(u8g2_font_open_iconic_all_1x_t);  
  u8g2.drawGlyph(5, 16*(2%4==0? 4:2%4)-4,118);  
  u8g2.sendBuffer();  
}  
void fun3(){  
  u8g2.clearBuffer();  
  page_1_to_4();  
  //显示光标  
  u8g2.setFont(u8g2_font_open_iconic_all_1x_t);  
  u8g2.drawGlyph(5, 16*(3%4==0? 4:3%4)-4,118);  
  u8g2.sendBuffer();  
}  
void fun4(){  
  u8g2.clearBuffer();  
  page_1_to_4();  
  //显示光标  
  u8g2.setFont(u8g2_font_open_iconic_all_1x_t);  
  u8g2.drawGlyph(5, 16*(4%4==0? 4:4%4)-4,118);  
  u8g2.sendBuffer();  
}  
void page_1_to_4(){  
  u8g2.setFont(u8g2_font_wqy14_t_gb2312a);  
  u8g2.setCursor(20, 16*1-2);  
  u8g2.print(" 1.桌面设置");  
  u8g2.setCursor(20, 16*2-2);  
  u8g2.print(" 2.屏幕设置");  
  u8g2.setCursor(20, 16*3-2);
  u8g2.print(" 3.显示健康码");  
  u8g2.setCursor(20, 16*4-2);
  u8g2.print(" 4.智能配网");  
}

（健康码)  
![image](https://github.com/andy-sketch/perpetual-motion-machine/blob/master/images/B5526F45E3C4C5F5E8DD753F22A9F523.png)  
![image](https://github.com/andy-sketch/perpetual-motion-machine/blob/master/images/FB293080490170D529A06A052250DCC0.png)  
由于法律和隐私问题，我们无法获得开放的api，故此部分暂时无法完成
