void PushYed(void)
{
	if (Pushed) // If Pushed and released
	{
		// DISABLE INTERRUPTS HERE
		noInterrupts();	  
		
		//debug(0);  
		AnalogBlank(); 
		delay(40);//debouncing
		
		if (digitalRead (BUTTON) == HIGH) // Button not pressed / State 1/ Modes
		{ // if Pushed && Button Not Pressed

			Armed = 1;
			
			if (Click)
			{ // if Click  
				if (CONJUREMODE_ENABLED)
				{
					Serial.print(F("CurrentUserMode = ")); Serial.println(CurrentUserMode);
					if (Mode == 0)
						{ Mode = UserCmodes[CurrentUserBundle][CONJUREMODE_MODE]; }
					else { Mode = 0; }
				}
				else
				{
					MaxUserModes = UserCmodes[CurrentUserBundle][0];
					CurrentUserMode++;
					if (CurrentUserMode > MaxUserModes) { CurrentUserMode = 1; }
					if (MaxUserModes==1)
						{ Mode = 0; }
					else { Mode = (UserCmodes[CurrentUserBundle][CurrentUserMode]); }
				}
				
				GLOBALCHANGE = 0;
				ToBorNotToB = 0;
				BPM_Counter = 0;
				Click = 0;
				STATE = 0;
				VARIATION = 0; // Reset Change
				eeCheck();
				EEPROM.write(2,CurrentUserBundle);eeCheck();
				EEPROM.write(3,CurrentUserMode);eeCheck();
			}
			
			if (OFF_MODE)
			{
				//Serial.println(F(">inside OFF MODE ")); // DEBUGGGGGGGGGGGGGGGGGGGGGGGGG	
				Mode = 0;
				STATE = 0;
				COLORCHANGE_FLAG = 0;
				VARIATION = 0; // Reset Change
				OFF_MODE = 0;
			}
			
			if (CONJUREMODE_FLAG)
			{
				if (CONJUREMODE_ENABLED)
					{ 
						CONJUREMODE_ENABLED = false;
						CONJUREMODE_MODE = 0;
						eeCheck();
						EEPROM.write(488,0);
						eeCheck();
					}
				else
				{
					CONJUREMODE_ENABLED = true;
					CONJUREMODE_MODE = CurrentUserMode;
					eeCheck();
					EEPROM.write(488,CONJUREMODE_MODE);
					eeCheck();
				}
				CONJUREMODE_FLAG = false;
			}
			
			if (COLORCHANGE_FLAG)
			{
				//Serial.println(F(" > INSIDE COLORCHANGE_FLAG")); // DEBUGGGGGGGGGGGGGGGGGGGGGGGGG
				STATE = 2; // Change To Color Mode Change  
				VARIATION = 0; // Reset Change
				ToBorNotToB = false;
				JUSTONCE = 1;
				COLORCHANGE_FLAG = 0;
			}
			
			if (MODERESET_FLAG)
			{// if MODERESET_FLAG
				//Serial.println(F(" > INSIDE MODERESET_FLAG"));// DEBUGGGGGGGGGGGGGGGGGGGGGGGGG
				// DO ALL THE CHANGES HERE AFTER INTERRUPTS DESABLED
				MODERESET_FLAG = 0;
				noInterrupts();	
				//> GRAB PGM TO FLASH
				PROGset = (Mode-1); // Mode Set on Progmem
				PROGMEMbank = 0; // Bank on Progmem
				EEbank = ((Mode*36)-16); // Base EEPROM Address
				while (PROGMEMbank < 36)// 
				{
					eeCheck();
					EEPROM.write((EEbank), (pgm_read_word_near(&(ModeSetFLASH[PROGset][PROGMEMbank]))) );
					eeCheck();
					ModeSetSRAM[PROGset][PROGMEMbank] = pgm_read_word_near(&(ModeSetFLASH[PROGset][PROGMEMbank]));
					EEbank++;
					PROGMEMbank++;
				}
				
				Serial.println();
				Serial.println(F("> FACTORY RESET FOR THIS MODE"));	 
				BLINKPWM(150,0,150 ,300,10);
				
				STATE = 1;
				VARIATION = 0; // Reset Change
				ToBorNotToB = false;
				COLORCHANGE_FLAG = 0;
				GLOBALCHANGE = 0;
				interrupts();
			}
			
			Int0count = 0;
			Pushed = 0;
		}
		
		// ENABLE INTERRUPTS HERE
		interrupts();
	}
	
	
	
	if (color > ColorMaxCount[VARIATION])
		{ color = 0; }
}


///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////


void PushYcolors(void)
{// void PushYed
	


	CCreturnAndSave();
	
	if (SaveDmode)
	{// SaveDmode
		Blinky(0,100,0,25);
		Blinky(0,0,0,800);
	}// SaveDmode
	
	if (DeleteColor)
	{// DeleteColor
		Blinky(100,0,0,25);
		Blinky(0,0,0,700);
	}// DeleteColor
		
		
		
	if (SaveDmode && (digitalRead (BUTTON) == HIGH))// Lets save EEPROM HERE
	{// if SaveDmode && Not Pushed
		
		// DISABLE INTERRUPTS HERE			
		noInterrupts();	
					
		for (k = 0; k < 13; k++)
		{// for k
			osmPWM_Plane(255,255,255,5);
			osmPWM_Plane(0,0,0,250);
		}// rof k
				
		//// SAVING MODES SETTINGS TO EEPROM	
		
		
				////////////////////////////////////
				byte ChangeToSet;
				if (VARIATION == 0) {ChangeToSet = 0;}	else { ChangeToSet = 18;}
				
		k=0;
				ModeMult = ((Mode * 36) - 16 + ChangeToSet);
		while (k < 19)// was 18
				{// for k
					eepromWrite( (ModeMult+k) , (ModeSetSRAM [ModeToChange][(k+ChangeToSet)]) ); // Save EEPROM Mode*20
					k++;
				}// rof k
		
				//////////////////////////////////
		// ENABLE INTERRUPTS HERE
		interrupts();
		
		SavedLast();


	}// fi SaveDmode && Not Pushing
	
	
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////	

									
	if (Pushed) // flag: 1 Yes, 0 No was/is pushed
	{// if Pushed
		//debug(0);
		AnalogBlank();
		delay(40);//debouncing
		
		 ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////
		if (digitalRead (BUTTON) == HIGH) // Button not pressed / State 1/ Modes
		{ // if Pushed && Button Not Pressed
			


				if (Click)
				{ // if Click

							if (VirtualPreview)
							{// VirtualPreview
									ColorToSave++;
									if (ColorToSave > 7) // ColorToSave is shifted (was 8)
									{
										#if defined(VISUAL)
											Serial.println();
											PrintThis(16,0,0,0); PrintThis(0,10,0,0);
											Serial.print(F(" MAX AMOUNT REACHED"));PrintThis(1,9,0,1);
											Serial.println();
											Serial.print(F("> MODE # ")); Serial.print(Mode); Serial.println(F(" SAVED"));
											PrintThis(1,69,0,1);
										#endif
										//STATE = 1;
										//JUSTONCE = 1;
										SavedLast();
										goto GetOut;
					 
									} // if this, we are done here with color programming
				
									else
									{
										#if defined(VISUAL)
											Serial.println();
											PrintThis(5,0,0,0);
											Serial.println(F("|CLICKED|"));
											Serial.println();
										#endif
										;
									}
					
				
									ONCE_SETUP_COLOR = 1;// @ ColorMode.ino / ONCE_SETUP_COLOR
																	ColorMaxCount[VARIATION] = ColorToSave;
									
									
									GetOut:
									
									CurrentColor = 0;
									Serial.println();
									VirtualPreview = 0;
							}// VirtualPreview
			

								else
								
							{// no VirtualPreview
									CurrentColor++;
									if (CurrentColor > 32) { CurrentColor = 0; } // we loop the colors 
									ONCE_SETUP_COLOR = 1; // @ ColorMode.ino / ONCE_SETUP_COLOR
							}// no VirtualPreview
								
					CSHADE = 0;// added
					Click = 0;//ADDED

				}// fi Click
							
//////////////////////////////////////////////////////////////		

				if (SaveDaColor)
				{ // if Off
					ToBorNotToB = 1;
					ONCE_SAVE_COLOR = 1;
					SaveDaColor = 0;
				}// fi Off
							
//////////////////////////////////////////////////////////////									
									
							
				if (SaveDmode)
				{// if SaveDmode
								
					for (k = 0; k < 6; k++)
					{// for k
						osmPWM_Plane(255,255,255,20);
						osmPWM_Plane(0,0,0,300);
					}// rof k
										
								
					CSHADE = 0;
								
					ToBorNotToB = 0;
					STATE = 1;
					JUSTONCE = 1;
					VirtualPreview = 0;
					SaveDmode = 0;
				}// fi SaveDmode

//////////////////////////////////////////////////////////////	

				if (DeleteColor)
				{ // if DeleteColor
					ColorMaxCount[VARIATION] = ColorToSave;
					CSHADE = 0;
					CurrentColor = 0;
					ONCE_SETUP_COLOR = 1;
					ToBorNotToB =1;
					VirtualPreview = 0;
					
					#if defined(VISUAL)
						Serial.println();
						PrintThis(44,0,0,0); Serial.print(F("|"));PrintThis(0,7,0,0);Serial.print(F(" COLOR DELETED"));PrintThis(1,7,0,0);Serial.println(F("|"));
						Serial.println();
					#endif
					
					VirtualPreview = 0;
					DeleteColor = 0;
				}// fi DeleteColor
							
//////////////////////////////////////////////////////////////
				Int0count = 0;
				Pushed = 0;
				} // fi Pushed && Button Not Pressed

	}// fi Pushed
	
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
	

	
		if (color > ColorMaxCount[VARIATION])
		{// if colorSet
			color = 0;
		}// fi colorSet
		

}// diov PushYed






void SavedLast(void)
{
	#if defined(VISUAL)
	
	Serial.println();
	PrintThis(13,0,0,0); Serial.print(F("|"));PrintThis(0,12,0,0);Serial.print(F(" SAVED"));PrintThis(1,11,0,0);Serial.println(F("|"));
	Serial.println();Serial.println();
	
	
	Serial.print(F("> MODE # ")); Serial.print(Mode); Serial.print(F(" / PRIME "));
	if (VARIATION == 0) {Serial.print(F("A")); PrintThis(3,0,0,0); SetModeExtras(0,0,PrimeA); }// (byte LNstart, byte LNend, byte Message)	
	else {Serial.print(F("B")); PrintThis(3,0,0,0); SetModeExtras(0,0,PrimeB); }// (byte LNstart, byte LNend, byte Message)	
		
	Serial.println(F(" SAVED"));
	PrintThis(0,85,0,1);
	
	#endif
	ToBorNotToB = 0;
	if (VARIATION == 0)
	{
		if (PMMAselect>0)
		{
		STATE = 2;
		VARIATION = 1;
		}
		else
		{
		STATE = 1;
		VARIATION = 0;
		}

	}
	else
	{
		STATE = 1;
		VARIATION = 0;
	}
	CurrentColor = 0;
	JUSTONCE = 1;
	VirtualPreview = 0;
	SaveDmode = 0;
}
