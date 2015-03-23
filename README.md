<p style="float: left;"><strong><img alt="" src="//cdn.shopify.com/s/files/1/0100/6632/files/Speaker_Wide_large.jpg?18024901213548002621" style="float: left;" width="315" height="317" /></strong>Here's a quick project using <strong>Pulse Sensor </strong>with an LED and<strong> a speaker</strong> to make a nice fading blink and<strong> 'hospital grade' beep</strong> with each pulse of<strong> your heartbeat</strong>.</p>
<div><iframe width="640" height="390" src="https://www.youtube.com/embed/G7v-ekaPkDE" frameborder="0" allowfullscreen=""></iframe></div>
<p> </p>
<p>Here's a list of materials for this tutorial.</p>
<ul>
<ul>
<li>Pulse Sensor Amped</li>
<li>Arduino UNO</li>
<li>8 ohm Speaker</li>
<li>Red LED</li>
<li>1K Resistor</li>
<li>470 ohm Resistor</li>
<li>10uF Capacitor</li>
<li>A breadboard and some jumper wires</li>
</ul>
</ul>
<p style="text-align: center;"><img alt="" src="//cdn.shopify.com/s/files/1/0100/6632/files/Speaker_CU_grande.jpg?4712366587572406487" style="float: none;" /></p>
<p>Our Pulse Sensor Arduino code is designed to accurately find the moment of heart beat in the pulse waveform for accurate BPM calculation (go <a href="http://pulsesensor.myshopify.com/pages/pulse-sensor-amped-arduino-v1dot1" target="_blank" title="Code Walk Through">here</a> for an in-depth discussion of our code). In order to do this, we are using one of the hardware timers on the ATmega328 (Arduino UNO's 'brain') to sample sensor data at exactly 50Hz (we sample every 2mS). The default Timer we use is Timer2, but Timer2 is also used  by the tone() command in Arduino. So we have to make a small change to the code to use Timer1 instead of Timer2. This code is located in the Interrupt tab in the Arduino sketch.</p>
<table width="100%">
<tbody>
<tr>
<td>
<p> <span style="color: #ff0000;">Original Pulse Sensor Amped Code</span></p>
</td>
<td><span style="color: #0000ff;">Modified Pulse Sensor Amped Code</span></td>
</tr>
<tr>
<td>
<div><meta charset="utf-8" /></div>
<p><span style="color: #ff0000;">void InterruptSetup(){</span></p>
<p><span style="color: #ff0000;">// Initializes Timer2 to throw an interrupt every 2mS.</span></p>
<p><span style="color: #ff0000;">TCCR2A = 0x02;</span></p>
<p><span style="color: #ff0000;">TCCR2B = 0x06; </span></p>
<div><meta charset="utf-8" /></div>
<p><span style="color: #ff0000;">OCR2A = 0X7C; </span></p>
<div><meta charset="utf-8" /></div>
<p><span style="color: #ff0000;">TIMSK2 = 0x02;</span></p>
<p><span style="color: #ff0000;"><span class="pl-s3">sei</span>();</span></p>
<p><span style="color: #ff0000;">}</span></p>
</td>
<td>
<p><span style="color: #0000ff;">void InterruptSetup(){</span></p>
<p><span style="color: #0000ff;">// Initializes Timer1 to throw an interrupt every 2mS.</span></p>
<div><meta charset="utf-8" /></div>
<p><span style="color: #0000ff;">TCCR1A = 0x00;</span></p>
<div><meta charset="utf-8" /></div>
<p><span style="color: #0000ff;">TCCR1B = 0x11; </span></p>
<div><meta charset="utf-8" /></div>
<p><span style="color: #0000ff;">TCCR1C = 0x00; </span></p>
<div><meta charset="utf-8" /></div>
<p><span style="color: #0000ff;">TIMSK1 = 0x01;</span></p>
<div><meta charset="utf-8" /></div>
<p><span style="color: #0000ff;">ICR1 = <span class="pl-c1">16000</span>;</span></p>
<div><meta charset="utf-8" /></div>
<p><span style="color: #0000ff;"><span class="pl-s3">sei</span>();</span></p>
<p><span style="color: #0000ff;">}</span></p>
</td>
</tr>
<tr>
<td>
<div><meta charset="utf-8" /></div>
<span style="color: #ff0000;">ISR(TIMER2_COMPA_vect)​</span></td>
<td>
<div><meta charset="utf-8" /></div>
<span style="color: #0000ff;"> <span class="pl-en">ISR</span>(TIMER1_OVF_vect)</span></td>
</tr>
</tbody>
</table>
<p> </p>
<p>Now that we've switched the Timer that we're using, we can use the Arduino tone() command and make a nice little beep when the heart beats! Let's lash up the circuit!</p>
<p>Here's a schematic of what we're going to do</p>
<p><img src="//cdn.shopify.com/s/files/1/0100/6632/files/Speaker_LED_schem_large.jpg?13495638317578253834" /></p>
<p>The LED is connected to Arduino pin 5. This pin will make the LED turn on when your heart beats, then it will fade softly for a nice effect. The code that does this is already a part of the Pulse Sensor Amped Arduino code. It is important to use at the very <em>least</em> a 470 ohm resistor. You can use up to 1K, but any less than 470 may result in weird behavior. </p>
<p> </p>
<p>The 8ohm speaker is a standard device that you can pick up almost anywhere, or you can hack them out of old equipment if you like :). The 1K resistor in between pin 2 and the speaker controls the volume of the beeps. Try different values for this resistor to increase or decrease the volume. The 10uF capacitor is there to protect the speaker from DC current which could damage it. Make sure to use the 10uF capacitor!</p>
<p> </p>
<p>Find the code we are using for this tutorial on our github <a href="https://github.com/WorldFamousElectronics/PulseSensor_Speaker" target="_blank" title="Pulse Sensor Speaker Code">here</a>. Here's how we modified the code to make this work:</p>
<p> First, we create an alias for pin 2, where we connect the speaker, above the <span style="color: #ff9900;">void <strong>setup</strong></span>() routine</p>
<p><span style="color: #ff9900;">int</span> Speaker = 2;</p>
<p>Then in the loop, we turn on the speaker when Arduino finds the heartbeat. Here's the loop:</p>
<p> </p>
<div style="padding-left: 30px;"><span style="color: #ff9900;">void <strong>loop</strong></span>(){</div>
<div style="padding-left: 60px;">sendDataToProcessing(<span style="color: #0000ff;">'S'</span>, Signal); <span style="color: #999999;">// send Processing the raw Pulse Sensor data</span></div>
<div style="padding-left: 60px;"><span style="color: #ff9900;"> if</span> (QS == <span style="color: #ff9900;">true</span>){ <span style="color: #999999;">// Quantified Self flag is true when arduino finds a heartbeat</span></div>
<div style="padding-left: 90px;">fadeRate = 255; <span style="color: #999999;">// Set 'fadeRate' Variable to 255 to fade LED with pulse</span></div>
<div style="padding-left: 90px;">sendDataToProcessing(<span style="color: #0000ff;">'B'</span>,BPM); <span style="color: #999999;">// send heart rate with a 'B' prefix</span></div>
<div style="padding-left: 90px;">sendDataToProcessing('Q',IBI); <span style="color: #999999;">// send time between beats with a 'Q' prefix</span></div>
<div style="padding-left: 90px;">QS = false; <span style="color: #999999;">// reset the Quantified Self flag for next time</span></div>
<div style="padding-left: 90px;"><span style="color: #ff9900;"> tone</span>(speaker,1047); <span style="color: #999999;">// tone (pin, frequency)</span></div>
<div style="padding-left: 60px;">}<br /> <br /> ledFadeToBeat(); <span style="color: #999999;">// this function fades the LED</span><br /> <span style="color: #ff9900;">delay</span>(20); <span style="color: #999999;">// take a break</span></div>
<p style="padding-left: 30px;">}</p>
<p> </p>
<p>The QS variable is set to <span style="color: #ff9900;">true</span><span> when Arduino finds the heart beat. So in our conditional <span style="color: #ff9900;">if</span> statement is where we turn on the speaker. I just arbitrarily chose 1047 as the frequency of the tone. You can change this around to fine a tone you like. Now that the tone is on, we will need to turn it off soon! I'm using the ledFadeToBeat() function to do this. Notice that when QS is true, the variable fadeRate is set to 255. That is the maximum value for our PWM signal, which is what we're using to fade the LED.</span></p>
<p> </p>
<div style="padding-left: 30px;"><span><span style="color: #ff9900;">void</span> ledFadeToBeat(){</span></div>
<div style="padding-left: 60px;"><span> fadeRate -= 15; <span style="color: #999999;">// set LED fade value</span></span></div>
<div style="padding-left: 60px;"><span> fadeRate = <span style="color: #ff9900;">constrain</span>(fadeRate,0,255); <span style="color: #999999;">// keep LED fade value from going into negative </span></span></div>
<div style="padding-left: 60px;"><span><span style="color: #ff9900;"> if</span>(fadeRate == 0){     <span style="color: #999999;">﻿   // when the fade is done</span></span></div>
<div style="padding-left: 90px;"><span><span style="color: #ff9900;">noTone(</span>speaker);  <span style="color: #999999;">// turn off the tone</span></span></div>
<div style="padding-left: 60px;"><span>}</span></div>
<div style="padding-left: 60px;"><span><span style="color: #ff9900;"> analogWrite</span>(fadePin,fadeRate);<span style="color: #999999;"> // fade LED</span></span></div>
<div style="padding-left: 30px;"><span> }</span></div>
<div style="padding-left: 30px;"></div>
<div>The function above gets called once per loop, and the loop has a delay of 20 milliseconds. The fadeRate variable starts at 255, and gets 15 subtracted from it once per loop. That means the LED fades and the beep lasts for (255/15)x20 = 340 milliseconds. Below is a photo of the circuit on a breadboard, and a video to show that it actually works! Brilliant!</div>
<div></div>
<div></div>
<div></div>
<div></div>
<div></div>
<div></div>