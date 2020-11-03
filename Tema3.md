# PS1_2021_Spinean
#include <LiquidCrystal.h>

#define BUTTON_OK 6
#define BUTTON_CANCEL 7
#define BUTTON_PREV 8
#define BUTTON_NEXT 9

enum Buttons {
  EV_OK,
  EV_CANCEL,
  EV_NEXT,
  EV_PREV,
  EV_NONE,
  EV_MAX_NUM
};

enum Menus {
  MENU_MAIN = 0,
  MENU_KP,
  MENU_TEMP,
  MENU_MAX_NUM
};

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
double temp = 36.6;
double kp = 20;
float temp_q = 0;
Menus scroll_menu = MENU_MAIN;
Menus current_menu =  MENU_MAIN;

void state_machine(enum Menus menu, enum Buttons button);
Buttons GetButtons(void);
void print_menu(enum Menus menu);

typedef void (state_machine_handler_t)(void);

void print_menu(enum Menus menu)
{
  lcd.clear();
  switch(menu)
  {
    case MENU_KP:
    	lcd.print("KP = ");
    	lcd.print(kp);
    	break;
    case MENU_TEMP:
    	lcd.print("TEMP = ");
    	lcd.print(temp);
    	break;
    case MENU_MAIN:
    default:
    	lcd.print("PS 2020");
   		break;
  }
  if(current_menu != MENU_MAIN)
  {
  	lcd.setCursor(0,1);
  	lcd.print("modifica");
  }
}

void enter_menu(void)
{
  current_menu = scroll_menu;
}

void go_home(void)
{
  scroll_menu = MENU_MAIN;
  current_menu = scroll_menu;
}

void go_next(void)
{
  scroll_menu = (Menus) ((int)scroll_menu + 1);
  scroll_menu = (Menus) ((int)scroll_menu % MENU_MAX_NUM);
}

void go_prev(void)
{
  scroll_menu = (Menus) ((int)scroll_menu - 1);
  scroll_menu = (Menus) ((int)scroll_menu % MENU_MAX_NUM);
}

void save_kp(void)
{
}

void inc_kp(void)
{
  kp++;
}

void dec_kp(void)
{
  kp--;
}

void save_temp(void)
{
}

void inc_temp(void)
{
  temp++;
}

void dec_temp(void)
{
  temp--;
}


state_machine_handler_t* sm[MENU_MAX_NUM][EV_MAX_NUM] = 
{ //events: OK , CANCEL , NEXT, PREV
  {enter_menu, go_home, go_next, go_prev},  // MENU_MAIN
  {go_home, go_home, inc_kp, dec_kp},       // MENU_KP
  {go_home, go_home, inc_temp, dec_temp},   // MENU_TEMP
};

void state_machine(enum Menus menu, enum Buttons button)
{
  sm[menu][button]();
}

Buttons GetButtons(void)
{
  enum Buttons ret_val = EV_NONE;
  if (digitalRead(BUTTON_OK))
  {
    ret_val = EV_OK;
  }
  else if (digitalRead(BUTTON_CANCEL))
  {
    ret_val = EV_CANCEL;
  }
  else if (digitalRead(BUTTON_NEXT))
  {
    ret_val = EV_NEXT;
  }
  else if (digitalRead(BUTTON_PREV))
  {
    ret_val = EV_PREV;
  }
  Serial.print(ret_val);
  return ret_val;
}

void setup()
{
  Serial.begin(9600);
  lcd.begin(16,2);
  pinMode(6, INPUT);
  digitalWrite(6, LOW); // pull-down
    pinMode(7, INPUT);
  digitalWrite(7, LOW); // pull-down
    pinMode(8, INPUT);
  digitalWrite(8, LOW); // pull-down
    pinMode(9, INPUT);
  digitalWrite(9, LOW); // pull-down
}

void loop()
{
  volatile Buttons event = GetButtons();
  if (event != EV_NONE)
  {
    state_machine(current_menu, event);
  }
    print_menu(scroll_menu);
    delay(1000);
}
