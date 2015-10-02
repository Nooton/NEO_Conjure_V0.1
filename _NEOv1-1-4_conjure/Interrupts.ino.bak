// Interrupts.ino
///////////////////////////////////
///////////////////////////////////
///////////////////////////////////
// 57 68 61 74 20 77 65 20 68 61 76 65 20 64 6f 6e 65 20 66 6f 72 20 6f 75 72 73 65 6c 76 65 73 20 61 6c 6f 6e 65 20 64 69 65 73 20 77 69 74 68 20 75 73 3b 20 77 68 61 74 20 77 65 20 68 61 76 65 20 64 6f 6e 65 20 66 6f 72 20 6f 74 68 65 72 73 20 61 6e 64 20 74 68 65 20 77 6f 72 6c 64 20 72 65 6d 61 69 6e 73 20 61 6e 64 20 69 73 20 69 6d 6d 6f 72 74 61 6c 2e 0a


volatile uint8_t PushCounter[]= 
{
	0, 4, 8, 12, 16, 20,24	,28	,32	,36	,40	,44	,48	,52
	,56	,60	,64	,68	,72	,76	,80	,84	,88	,92	,96	,100	,104	,108	,112	,116	,120	,124
	,128	,132	,136	,140	,144	,148	,151	,156	,160	,164	,168	,172	,176
	,180	,184	,188	,192	,196	,200	,204	,208	,212	,216	,220	,224	,228
	,232	,236	,240	, 244	, 248	,252	, 255
};




void pushInterrupt()
{
	;
}





ISR(TIMER2_COMPA_vect) // TIMER2 INTERRUPT @ 61HZ / 16.40ms
{
	if (AUTOCHANGE == 1)
	{// if AUTOCHANGE
		if (Mode != 0)
		{
			BPM_Counter++;	
		} 
		
		
		if (BPM_Counter > BPM_Factor)
		{// if BPM_Counter
			
			//Serial.print(BPM_Counter);
			//Serial.print("  ");  Serial.print(BPM_Factor); Serial.println(F("  CHANGE"));
			BPM_Counter = 0;
			MaxUserModes = UserCmodes[CurrentUserBundle][0];
			CurrentUserMode++;
			if (CurrentUserMode > MaxUserModes) { CurrentUserMode = 1;}
			if (MaxUserModes==1)
				{ Mode = 0; }
			else { Mode = (UserCmodes[CurrentUserBundle][CurrentUserMode]); }
			GLOBALCHANGE = 0;
			Click = 0;
			ToBorNotToB = 0;
			STATE = 0;
			VARIATION = 0;// Reset Change
		}// fi BPM_Counter
		
	}// fi AUTOCHANGE
	
	
	
	if (digitalRead (BUTTON) == LOW) // if we are pushing pushing
	{ // if digitalRead
		Int0count++;
		Pushed = 1;
		
		
		//////////////////////////////////////////////////////////////////////////////////////////////
		//                STATE 1 == MODES                                                          //
		//////////////////////////////////////////////////////////////////////////////////////////////
		
		if (STATE == 1) // If STATE = 1 / Modes State
		{
			if ( (Int0count > 3) && (Int0count < HOLD_OFF ) ) // CLICK > 0.05 seconds
			{
				PrintMaster++;
				if (PrintMaster > 3) {AsteriscMaster++; PrintMaster = 0;}
				if (Int0count == 4) { Serial.print(F("|")); PrintMaster=0; AsteriscMaster=1;}
				if (Int0count == PushCounter[AsteriscMaster])
					{ Serial.print(F("*"));}
				
				Click = 1;
				COLORCHANGE_FLAG = 0;
				ToBorNotToB = 1;
			}
	
			//////////////////////////////////////////////////////////////
	
			if ( (Int0count >= HOLD_OFF ) && (Int0count < HOLD_CONJURE ) ) // OFF
			{
				PrintMaster++;
				if (PrintMaster > 3) {AsteriscMaster++; PrintMaster=0;}
				if (Int0count == HOLD_OFF) 
				{
					#if defined(VISUAL) 
						Serial.print(F("|"));
					#endif 
					PrintMaster=0; AsteriscMaster=8;
				}
				#if defined (VISUAL)
					if (Int0count == PushCounter[AsteriscMaster])
					{
						Serial.print(F("*"));
					}
				#endif
				
				AnalogBlank(); //All Off
				Click = 0;
				ToBorNotToB = 0;
				Armed = 0;
				ONNS = 0; //HeartBeat flag
				COLORCHANGE_FLAG = 0;
				PrintMaster = 0;
				WhatDmode = Mode; //save the current mode
				OFF_MODE = 1;
			}
	
			//////////////////////////////////////////////////////////////
						
			if ( (Int0count >= HOLD_CONJURE ) && (Int0count < HOLD_BYPASS1 ) ) // CONJURE MODE
			{
				OFF_MODE = 0; //Turn the chip back on.
				if (Int0count == HOLD_CONJURE) { BlinkyCount = 0; }
				if  (Mode == 0) /// OFF MODE
				{
					ToBorNotToB = 1;
					if  ((Zflipped) && (Int0count > HOLD_CONJURELOOP))
					{
						Int0count = HOLD_CONJURE; // Reset
						CurrentUserBundle++;
						if (CurrentUserBundle > 4) { CurrentUserBundle = 0; }
					}
					else if  ((!Zflipped) && (Int0count > HOLD_CONJURELOOP))
					{
						Int0count = HOLD_CONJURE; // Reset
						CurrentOffCounter++;
						if (CurrentOffCounter > 4) { CurrentOffCounter = 0; }
					}
					Int0count++;
				}
				else
				{
					PrintMaster++;
					if (PrintMaster > 3) {AsteriscMaster++; PrintMaster=0; }
					if (Int0count == HOLD_CONJURE) { Serial.print(F("|")); PrintMaster=0; AsteriscMaster=23;}
					#if defined(VISUAL)
						if (Int0count == PushCounter[AsteriscMaster])
						{ 
							Serial.print(F("*"));
						}
					#endif
					
					if (BlinkyCount > 6) { BlinkyCount = 0; }
					BlinkyCount++;
					if (BlinkyCount == 1) { Blinky(0,1,0,200); }
					if (BlinkyCount == 3) { Blinky(0,0,0,50); }
					CONJUREMODE_FLAG = true;
				}
			}
			
			//////////////////////////////////////////////////////////////
			
			if ( ( (Int0count >= HOLD_BYPASS1) && (Int0count < HOLD_COLORCHANGE) )
			/*if*/	|| ( (Int0count >= HOLD_BYPASS2) && (Int0count < HOLD_RESET) ) ) // CHIPSET BYPASS
			{
				if ( (Int0count == HOLD_BYPASS1) || (Int0count == HOLD_BYPASS2) )
					{ BlinkyCount = 0; } //Reset Count		
				if (BlinkyCount > 6) { BlinkyCount = 0; }
				BlinkyCount++;
				if (BlinkyCount == 1) { Blinky(1,1,1,5); }
				if (BlinkyCount == 3) { Blinky(0,0,0,50); }
				CONJUREMODE_FLAG = false;
				COLORCHANGE_FLAG = 0; //UNCOMMENT THIS
				Int0count++; //BYPASS ACCELERATION
			}
			
			//////////////////////////////////////////////////////////////
			
			if ( (Int0count >= HOLD_COLORCHANGE ) && (Int0count < HOLD_BYPASS2 ) ) // VARIATION COLOR SET
			{
				if  (Int0count == HOLD_COLORCHANGE) { BlinkyCount = 0; } //Reset Count
				
				PrintMaster++;
				if (PrintMaster > 3) { AsteriscMaster++; PrintMaster=0; }
				if (Int0count == HOLD_COLORCHANGE) { Serial.print(F("|")); PrintMaster=0; AsteriscMaster=23; }
				#if defined(VISUAL)
					if (Int0count == PushCounter[AsteriscMaster])
					{
						Serial.print(F("*"));
					}
				#endif
				
				if (BlinkyCount > 6) { BlinkyCount = 0; }
				BlinkyCount++;
				if (BlinkyCount == 1) { Blinky(1,1,0,200); }
				if (BlinkyCount == 3) { Blinky(0,0,0,50); }
				COLORCHANGE_FLAG = 1;
				//Serial.println(F(" > COLOR CHANGE INTERRUPT")); //DEBUGGGGGGGGGGGGGGGGGGGGGGGGG
			}
			
			//////////////////////////////////////////////////////////////
			
			//REPEAT CHIPSET BYPASS
			
			//////////////////////////////////////////////////////////////
			
			if ( (Int0count >= HOLD_RESET) && (Int0count < HOLD_FUCKEDUP) ) // FACTORY RESET
			{
				if (Int0count == HOLD_RESET)
					{ BlinkyCount = 0; } //Reset Count
				else if (BlinkyCount > 6)
					{ BlinkyCount = 0; }
				BlinkyCount++;
				
				switch (BlinkyCount)
				{
					case 2:
						Blinky(1,0,0,500);
						break;
					
					case 4:
						Blinky(0,0,1,500);
						break;
				}
				MODERESET_FLAG = 1;	
			}
			
			//////////////////////////////////////////////////////////////
			
			if (Int0count >= HOLD_FUCKEDUP) //OH, CRAP I SCREWED UP... GO BACK TO BLANK
			{
				#if defined(VISUAL)
					Serial.println();
					Serial.println(F("> OH, CRAP I SCREWED UP... NEVER MIND!"));
				#endif
				BlinkyCount = 0; //Reset Count
				AnalogBlank(); //All Off
				MODERESET_FLAG = 0;
				Click = 0;
				ToBorNotToB = 0;
				Mode = 0;
			}
		}
		
		
		//////////////////////////////////////////////////////////////////////////////////////////////
		///////    STATE 2 == COLOR VARIATION    /////////////////////////////////////////////////////
		//////////////////////////////////////////////////////////////////////////////////////////////
		
		
		if (STATE == 2) /// Color Change
		{// if STATE 2 / Color Change
			//////////////////////////////////////////////////////////////
			//////////////////////////////////////////////////////////////
			
	
				//////////////////////////////////////////////////////////////
				if ( (Int0count > 3) && (Int0count < 32 ) ) // CLICK 0.05 sec to 0.5 sec
				{ // if Click
				Click = 1;
				shadeFlag = 0;
				
				
					PrintMaster++;
					if (PrintMaster > 3) {AsteriscMaster++; PrintMaster = 0;}
					if (Int0count == 4) {PrintMaster=0; AsteriscMaster=1;}
					if ((VirtualPreview) && (Int0count == PushCounter[AsteriscMaster]))
					{ 
						#if defined(VISUAL)
						Serial.print(F("*"));
							#endif
					}
				
				//Int0count++;
				ToBorNotToB = 1;
				}// fi Click
	
				//////////////////////////////////////////////////////////////
									
				if ( (Int0count > 31 ) && (Int0count < 151 ) ) // OFF 0.5 sec to 1.5 sec
				{ // if Click
				
					if (!VirtualPreview)
					{
						if (Int0count == 32) {ONCE_SETUP_COLOR = 1; }
						Int0count++;
						shadeFlag = 1;
						CSHADE=0;
						ONNS = 0; // HeartBeat flag
						SaveDaColor = 1;
					}
					else
					{// if VirtualPreview
						ToBorNotToB = 0;
						
						PrintMaster++;
						if (PrintMaster > 3) {AsteriscMaster++; PrintMaster=0;}
						if (Int0count == 32) 
						{
							#if defined(VISUAL)
								 Serial.print(F("|"));
							#endif
								PrintMaster=0; AsteriscMaster=8;
							 }
						if ((VirtualPreview) && (Int0count == PushCounter[AsteriscMaster]))
						{ 
							#if defined(VISUAL)
							Serial.print(F("*"));
							#endif
						}
							
						SaveDmode = 1;	
						
					}// fi VirtualPreview
					
				Click = 0;
				}// fi Click
	
	//////////////////////////////////
				if ( (Int0count > 150 ) && (Int0count < 270 ) ) // VARIATION COLOR SET 1.5 sec to 2 sec
				{ // if Click
					
					if (!VirtualPreview)
					{// if !VirtualPreview
						if (Int0count == 151) {ONCE_SETUP_COLOR = 1; }
						CSHADE=1;
						Int0count++;
						SaveDaColor = 1;
					}// fi !VirtualPreview
					else
					{// else if VirtualPreview
						
						PrintMaster++;
						if (PrintMaster > 3) {AsteriscMaster++; PrintMaster=0;}
						if (Int0count == 151) 
						{
							 #if defined(VISUAL)
							 Serial.print(F("|"));
							 #endif 
							 PrintMaster=0; AsteriscMaster=38;
							}
						if ((VirtualPreview) && (Int0count == PushCounter[AsteriscMaster]))
						{
							#if defined(VISUAL) 
							Serial.print(F("*"));
							#endif
						}
	
						if ((VirtualPreview) && (Int0count == 269) )
						{
							#if defined(VISUAL)
							Serial.print(F("**|"));
							#endif
						}
						
						DeleteColor = 1;									
						SaveDmode = 0;	
						
					}// esle if VirtualPreview
					
					Click = 0;
				}// fi Click
	
	//////////////////////////////////	
			
				if ( (Int0count > 269 ) && (Int0count < 389 ) ) // OFF 2 sec to 2.5 sec
				{ // if Click
					
					if (!VirtualPreview)
					{
						if (Int0count == 270) {ONCE_SETUP_COLOR = 1;}
						CSHADE=2;
						Int0count++;
						SaveDaColor = 1;
					}
					else
					{
						Int0count = 270;
						goto  OutOfHereNow;
					}
					
					Click = 0;
				}// fi Click	
				
	//////////////////////////////////
			
				if ( (Int0count > 388 ) && (Int0count < 509 ) ) // OFF 2.5 sec to 4.5 sec
				{ // if Click
					
					if (!VirtualPreview)
					{
						if (Int0count == 389) {ONCE_SETUP_COLOR = 1; }
						CSHADE=3;
						Int0count++;
						SaveDaColor = 1;
					}
					else
					{
						SaveDmode = 1;
					}
					
					Click = 0;
				}// fi Click
	
	
	//////////////////////////////////											
				if ( (Int0count > 508 )) // OFF 0.5 sec to 1.5 sec
				{ // if Click
				Int0count = 4;
				CSHADE=0;
				Click = 0;
				}// fi Click
	
	
	
				
				
			//////////////////////////////////////////////////////////////
			//////////////////////////////////////////////////////////////
			
		}// if STATE 2 / Color Change
	
	}// fi digitalRead
	else { Int0count = 0; }
	
	OutOfHereNow:
	; 
}



