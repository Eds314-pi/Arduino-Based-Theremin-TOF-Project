#include <Wire.h>
#include <VL53L0X.h>
#include <MozziGuts.h>
#include <Oscil.h>
#include <tables/sin2048_int8.h>
#include <LiquidCrystal_I2C.h>
#include <VL53L0X.h>  


#define shut 8
#define shut1 10
#define length 125
#define CONTROL_RATE 64
#define max 600
#define min 50

#define Rbut 2
#define Pbut 3

//TOF sensor
VL53L0X sensor;
VL53L0X sensor1;

// TWO oscillators (live + playback)
Oscil <SIN2048_NUM_CELLS, AUDIO_RATE> liveOsc(SIN2048_DATA);
Oscil <SIN2048_NUM_CELLS, AUDIO_RATE> playOsc(SIN2048_DATA);

//LED screen
LiquidCrystal_I2C lcd(0x27,16,2);
struct Note
{
  int pitch;
};
//recording
Note recording[length];

int index = 0;
bool Recording = false;
bool playBack = false;
bool playing = false;

unsigned long startTime = 0;
unsigned long last = 0;

unsigned long messageTime=0;
const unsigned long displayTime=3000;

unsigned long rPress = 0;
unsigned long pPress=0;
const unsigned long PressTime = 200;


void setup()
{
  Serial.begin(9600);
  Wire.begin(); 
  lcd.init(); 
  lcd.backlight(); 
  pinMode(Pbut,INPUT); 
  pinMode(Rbut,INPUT); 
  pinMode(shut, OUTPUT); 
  pinMode(shut1,OUTPUT); 
  //Turns off both sensors to set addresses apart 
  digitalWrite(shut, LOW); 
  digitalWrite(shut1,LOW); 
  delay(10); 
  //sets sensor 1 
  digitalWrite(shut,HIGH); 
  delay(10); 
  if (!sensor.init()) 
{
  Serial.println("Sensor 0 failed to init!");
  while (1); // stop here
}
  sensor.setAddress(0x30); 
  sensor.startContinuous(64);
  //sets sensor2 
  digitalWrite(shut1,HIGH); 
  delay(10); 
  if (!sensor1.init()) {
  Serial.println("Sensor 1 failed to init!");
  while (1); // stop here
}
  sensor1.setAddress(0x31); 
  sensor1.startContinuous(64);

  startMozzi(CONTROL_RATE); 
  Serial.println("Found sketch started"); 
  lcd.setCursor(0,0); 
  lcd.print("Hello User"); 
  delay(3000); 
  lcd.clear();
}

//Resets sensor in order to prevent lockups
void resetSensor()
{
 // Turn both off
  digitalWrite(shut, LOW);
  digitalWrite(shut1, LOW);
  delay(10);

  // Turn on sensor first
  digitalWrite(shut, HIGH);
  delay(10);
  sensor.init();
  sensor.setAddress(0x30);
  sensor.startContinuous(64);

  // Then sensor 1
  digitalWrite(shut1, HIGH);
  delay(10);
  sensor1.init();
  sensor1.setAddress(0x31);
  sensor1.startContinuous(64);
}

//begins the recording process
void startRecording()
{
  Serial.println("Recording started");
  lcd.setCursor(0,0);
  lcd.print("Recording");
  lcd.setCursor(0, 1);
  lcd.print("started");
  messageTime=millis();
  Recording = true;
  index = 0;
}

//records notes played over a shot interval
void record(int freq, int volume)
{
  if (index < length)
  {
    recording[index].pitch = (freq >= 20 && freq <= 2000) ? freq : 0;
    recording[index].pitch=recording[index].pitch/volume;
    index++;
  }
  else
  {
    Recording = false;
    index = 0;
    Serial.println("Recording complete");
    lcd.setCursor(0,0);
    lcd.print("Recording");
    lcd.setCursor(0, 1);
    lcd.print("complete");
    messageTime=millis();
  }
}

//Begins playing back the recording
void startPlayBack()
{
  Serial.println("Playback started");
  playBack = true;
  index = 0;
  playing = false;
  lcd.setCursor(0,0);
  lcd.print("Playback ");
  lcd.setCursor(0,1);
  lcd.print("started");
  messageTime=millis();
}

void playRecording()
{
  if (!playing)
  {
   if (index >= length)
    {
        playBack = false;
        playOsc.setFreq(0);
        index = 0;
        Serial.println("Playback finished");
        lcd.setCursor(0,0);
        lcd.print("Playback ");
        lcd.setCursor(0,1);
        lcd.print("finished");
        messageTime=millis();
        return;
    }

    int pitch = recording[index].pitch;
    playOsc.setFreq(pitch);
    index++;
}
}

//handles console inputs for all triggers also handles button inputs
void handleCommand(char cmd)
{
  switch (cmd)
  {
    case 'r': startRecording(); break;
    case 'p': startPlayBack(); break;
    case 's': resetSensor(); break;
    default: Serial.print("Unkown command"); break;
  }
}

double volume=0;
unsigned long lastSensor1Read = 0;
const unsigned long sensor1Interval = 50; // milliseconds between readings
int distance1 = 0;

//Handles all code in loop
void updateControl()
{
  unsigned long now = millis();
  unsigned long delta = now - last;
  last = now;
  if(digitalRead(Rbut)==HIGH && millis()-rPress>PressTime)
  {
    rPress=millis();
    Serial.println("RecordPressed");
    handleCommand('r');
  }

  if(digitalRead(Pbut)==HIGH && millis()-pPress>PressTime)
  {
    pPress=millis();
    Serial.println("PlatBackPressed");
    handleCommand('p');
  }

  if (Serial.available())
  {
    char cmd=Serial.read();
    if(cmd!='\n' && cmd!='\r')
    {
    handleCommand(cmd);
    }
  }

  //actual data read by TOF
  int distance = sensor.readRangeContinuousMillimeters();
  if (sensor.timeoutOccurred() || distance >= 8190 || distance==-1)
  {
    liveOsc.setFreq(0);
  }

  int freq=0;
  if (distance >=min && distance <= max)
  {
    freq = map(distance, min, max, 2000, 20);
  }
  
  int distance1=sensor1.readRangeContinuousMillimeters();
  if (sensor1.timeoutOccurred() || distance1 >= 8190 || distance1==-1)
  {
    volume=0;
  }
  if (distance1 >=min && distance1 <= max)
  {
    volume = (max - distance1) * (2.5 / (float)(max - min));
    volume = constrain(volume, 0.0, 2.5);
  }

  if (!playBack)
  {
    liveOsc.setFreq(freq);
  }

  if (Recording)
  {
    record(freq, volume);
  }

  if (playBack)
  {
    playRecording();
  }
  
  if(millis()-messageTime>=displayTime)
  {
    lcd.clear();
    messageTime=millis();
  }
}

//Plays audio
int updateAudio()
{
  return ((liveOsc.next()*volume + playOsc.next()));
}


void loop()
{
  audioHook();
}
