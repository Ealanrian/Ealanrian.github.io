---
layout: post
title: .cseg error in assembly
date: 2011-04-18 20:04
author: admin
comments: true
categories: [Assembly, atmega32, Avr, Embeded, Embeded, School, Tech]
---
Today i had an error in AVR studio about a .cseg overlap. namely Overlap in .cseg: addr=0x1c conflicts with 0x1c:0x1d.
After passing this error through Google I wasn't able to find a solution only that is was related to a memory address being reused.
Since this problem was part of a school assignment due in a few days I decided to consult my teacher. Apparently fellow students had had the same problem.
When using assembly on an ATMega32 cpu do not change the interrupt table. This will cause a .cseg error after the break more info.

<!--more-->

Changing:
<span class="notranslate">
<pre lang="asm">
.org 0x0000 		rjmp init			; Spring bij opstart naar de regel met de label INIT
.org INT2addr       rjmp snoozeInt 		; External Interrupt2 Vector Address (op port B2)
.org OC1Aaddr		rjmp incSeconde		; go to ISR Timer_interrupt
.org UDREaddr		rjmp send			; Data register is empty again and will go to send
.org 0x0016  		rjmp Tim0_ovf		; Timer 0 overflow interrupt
.org 0x0014			rjmp Tim0_comp      ; Timer 0 compare interrupt</pre></span>
To:﻿﻿﻿
<span class="notranslate">
<pre lang="asm">.org 0x0000           rjmp init            ; Spring bij opstart naar de regel met de label INIT
.org INT2addr       rjmp snoozeInt         ; External Interrupt2 Vector Address (op port B2)
.org OC1Aaddr        rjmp incSeconde        ; go to ISR Timer_interrupt
.org OVF0addr        rjmp Tim0_comp
.org OC0addr          rjmp Tim0_ovf        ; Timer 0 overflow interrupt
.org UDREaddr        rjmp send            ; Data register is empty again and will go to send</pre></span>
The error disappeared. not that the udre interrupt was before the timer0 interrupt.

&nbsp;

&nbsp;

&nbsp;
