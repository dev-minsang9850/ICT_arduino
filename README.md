# 덕영고등학교 & 차세대융합기술원 차세대융합기술 ICT융합기술 메이커 제작 교육

### 자세한 사항은 [Github Link](https://github.com/dev-minsang9850/ICT_arduino) 에 PDF 파일을 참고하세요
## 기본 준비물
	1. 아두이노 나노 보드
	2. mini 5pin 케이블
## Windows 사용자 필수!!
CH340G 드라이브 필수 설치 ->  [깃허브 사이트 참고](https://github.com/yangjipsa/touchlabs_bouncebot)

1. 모터테스트 코드
	``` C++ 
	/*
	 * ==========================================
	 *  Bounce Bot - Motor Driver Test
	 * ==========================================
	 *  Author   : yangjipsa
	 *  Company  : TouchLabs (touchlabs.kr)
	 *  Version  : 1.0
	 *  Date     : 2026-06-16
	 *  Product  : Bounce Bot
	 *  Purpose  : Test code to control two DC motors
	 *             using two L9110S motor drivers.
	 *             Tests forward and backward rotation.
	 *  Board    : Arduino Nano
	 *  Pin Map  : Left  Motor - IA -> D10, IB -> D9
	 *             Right Motor - IA -> D6,  IB -> D5 (physical)
	 *  Note     : Motors are mounted symmetrically.
	 *             Right motor IA/IB are swapped in software
	 *             so both motors share the same drive logic.
	 * ------------------------------------------
	 *  Copyright (c) 2026 TouchLabs.
	 *  All rights reserved.
	 *
	 *  This code is the property of TouchLabs and is
	 *  provided for the Bounce Bot product. Unauthorized
	 *  copying, distribution, or modification is prohibited.
	 * ==========================================
	 */
	
	// Left motor driver pins
	int L_IA = 10;   // Left motor IA
	int L_IB = 9;    // Left motor IB
	
	// Right motor driver pins (swapped in software for mirrored mount)
	int R_IA = 5;    // Physically wired to IB (D5)
	int R_IB = 6;    // Physically wired to IA (D6)
	
	int motorSpeed = 200;   // Motor speed (0 - 255)
	
	void setup()
	{
	  Serial.begin(9600);    // Start serial communication
	
	  pinMode(L_IA, OUTPUT);
	  pinMode(L_IB, OUTPUT);
	  pinMode(R_IA, OUTPUT);
	  pinMode(R_IB, OUTPUT);
	}
	
	void loop()
	{
	  // ---------- Forward ----------
	  Serial.println("Forward");
	  analogWrite(L_IA, motorSpeed);   // Left motor forward
	  digitalWrite(L_IB, LOW);
	  analogWrite(R_IA, motorSpeed);   // Right motor forward
	  digitalWrite(R_IB, LOW);
	  delay(2000);
	
	  // ---------- Backward ----------
	  Serial.println("Backward");
	  digitalWrite(L_IA, LOW);
	  analogWrite(L_IB, motorSpeed);   // Left motor backward
	  digitalWrite(R_IA, LOW);
	  analogWrite(R_IB, motorSpeed);   // Right motor backward
	  delay(2000);
	}
	```
2. 스위치테스트 코드
	 ``` c++ 
	 /*
	 * ==========================================
	 *  Bounce Bot - Bumper Sensor Test
	 * ==========================================
	 *  Author   : yangjipsa
	 *  Company  : TouchLabs (touchlabs.kr)
	 *  Version  : 1.0
	 *  Date     : 2026-06-16
	 *  Product  : Bounce Bot
	 *  Purpose  : Test code to read left/right bumper
	 *             switch states and print them to
	 *             the Serial Monitor.
	 *  Board    : Arduino Nano
	 *  Pin Map  : Switch Left -> D8, Switch Right -> D7
	 * ------------------------------------------
	 *  Copyright (c) 2026 TouchLabs.
	 *  All rights reserved.
	 *
	 *  This code is the property of TouchLabs and is
	 *  provided for the Bounce Bot product. Unauthorized
	 *  copying, distribution, or modification is prohibited.
	 * ==========================================
	 */
	
	int SWL = 8;   // Left bumper switch pin
	int SWR = 7;   // Right bumper switch pin
	
	void setup()
	{
	  Serial.begin(9600);     // Start serial communication
	  pinMode(SWL, INPUT);    // Set left switch as input
	  pinMode(SWR, INPUT);    // Set right switch as input
	}
	
	void loop()
	{
	  // Read bumper switch states
	  int BumperL = digitalRead(SWL);
	  int BumperR = digitalRead(SWR);
	
	  // Print the values to the Serial Monitor
	  Serial.print("Bumper L :");
	  Serial.print(BumperL);
	  Serial.print(", Bumper R :");
	  Serial.println(BumperR);
	
	  delay(100);   // Wait 0.1 second
	}
	 ```
3. 바운스로봇 코드
	``` C++
	/*
	 * ==========================================
	 *  Bounce Bot - Collision Avoidance Robot
	 * ==========================================
	 *  Author   : yangjipsa
	 *  Company  : TouchLabs (touchlabs.kr)
	 *  Version  : 1.1
	 *  Date     : 2026-06-16
	 *  Product  : Bounce Bot
	 *  Purpose  : Drive forward and avoid obstacles using
	 *             two front bumper switches.
	 *             - Left bumper hit  -> back up, turn right
	 *             - Right bumper hit -> back up, turn left
	 *  Board    : Arduino Nano
	 *  Pin Map  : Bumper Left  -> D8,  Bumper Right -> D7
	 *             Left  Motor - IA -> D10, IB -> D9
	 *             Right Motor - IA -> D6,  IB -> D5 (physical)
	 *  Note     : Bumper switches use pull-up, so pressed = LOW.
	 *             Motors are mounted symmetrically, so the right
	 *             motor IA/IB are swapped in software.
	 * ------------------------------------------
	 *  Copyright (c) 2026 TouchLabs.
	 *  All rights reserved.
	 *
	 *  This code is the property of TouchLabs and is
	 *  provided for the Bounce Bot product. Unauthorized
	 *  copying, distribution, or modification is prohibited.
	 * ==========================================
	 */
	
	// Bumper switch pins
	int SWL = 8;     // Left bumper switch
	int SWR = 7;     // Right bumper switch
	
	// Left motor driver pins
	int L_IA = 10;
	int L_IB = 9;
	
	// Right motor driver pins (swapped in software for mirrored mount)
	int R_IA = 5;    // Physically wired to IB (D5)
	int R_IB = 6;    // Physically wired to IA (D6)
	
	// ----- Settings (tune these) -----
	int motorSpeedL = 100;   // Motor speed (0 - 255)
	int motorSpeedR = 120;   // Motor speed (0 - 255)
	int backTime   = 300;   // Reverse time (ms)
	int turn90Time = 200;   // Time to rotate about 90 degrees (ms)
	int pauseTime  = 200;   // Brief stop time (ms)
	
	void setup()
	{
	  Serial.begin(9600);
	
	  pinMode(SWL, INPUT);
	  pinMode(SWR, INPUT);
	  pinMode(L_IA, OUTPUT);
	  pinMode(L_IB, OUTPUT);
	  pinMode(R_IA, OUTPUT);
	  pinMode(R_IB, OUTPUT);
	
	  stop();
	}
	
	void loop()
	{
	  int bumperL = digitalRead(SWL);
	  int bumperR = digitalRead(SWR);
	
	  if (bumperL == LOW) 
	  {
	    // Left bumper hit -> obstacle on front-left
	    Serial.println("Left hit -> turn right");
	    stop();        
	    delay(pauseTime);
	    
	    backward();    
	    delay(backTime);
	    
	    stop();        
	    delay(pauseTime);
	    
	    turnRight();   
	    delay(turn90Time);
	    
	    stop();        
	    delay(pauseTime);
	  }
	  else if (bumperR == LOW) 
	  {
	    // Right bumper hit -> obstacle on front-right
	    Serial.println("Right hit -> turn left");
	    stop();        
	    delay(pauseTime);
	    
	    backward();    
	    delay(backTime);
	   
	    stop();        
	    delay(pauseTime);
	    
	    turnLeft();    
	    delay(turn90Time);
	    
	    stop();        
	    delay(pauseTime);
	  }
	  else 
	  {
	    // No obstacle -> keep going forward
	    forward();
	  }
	}
	
	// ---------- Movement functions ----------
	
	void forward()
	{
	  analogWrite(L_IA, motorSpeedL);   
	  digitalWrite(L_IB, LOW);
	  analogWrite(R_IA, motorSpeedR);   
	  digitalWrite(R_IB, LOW);
	}
	
	void backward()
	{
	  digitalWrite(L_IA, LOW);   
	  analogWrite(L_IB, motorSpeedL);
	  digitalWrite(R_IA, LOW);   
	  analogWrite(R_IB, motorSpeedR);
	}
	
	void turnRight()   // Left wheel forward, right wheel backward
	{
	  analogWrite(L_IA, motorSpeedL);   
	  digitalWrite(L_IB, LOW);
	  digitalWrite(R_IA, LOW);         
	  analogWrite(R_IB, motorSpeedR);
	}
	
	void turnLeft()    // Left wheel backward, right wheel forward
	{
	  digitalWrite(L_IA, LOW);   
	  analogWrite(L_IB, motorSpeedL);
	  analogWrite(R_IA, motorSpeedR);   
	  digitalWrite(R_IB, LOW);
	}
	
	void stop()
	{
	  digitalWrite(L_IA, LOW);   
	  digitalWrite(L_IB, LOW);
	  digitalWrite(R_IA, LOW);   
	  digitalWrite(R_IB, LOW);
	}
	```

