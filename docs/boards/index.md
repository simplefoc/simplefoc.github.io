---
layout: default
title: <span class="simple">Simple<span class="foc">FOC</span> Boards</span>
description: "SimpleFOC boards"
nav_order: 3
permalink: /boards
has_children: true
has_toc: false
toc: true
---

# <span class="simple">Simple<span class="foc">FOC</span> Boards</span>

One of the goals of the  <span class="simple">Simple<span class="foc">FOC</span>project</span> is to develop low-cost easy to use BLDC and Stepper driver boards compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>and completely open source! Therefore, <span class="simple">Simple<span class="foc">FOC</span></span> team members have developed a set of boards, designed specifically for ease of use, to help you kickstart your FOC journey. In addition to being easy to use, the goal of these boards is serve as a reference design for the community to build upon. And finally, even though some of these boards are available in our [shop](https://www.simplefoc.com/shop), our docs provide a lot of documentation and step-by-step guides on how to fabricate the boards yourself.


The <span class="simple">Simple<span class="foc">FOC</span></span> board range includes boards with an integrated microcontroller, Arduino-compatible shields, and compact driver boards. The table below is a quick guide, follow the board links for full specifications, setup, and fabrication information.

<style>
.boards-overview table{font-size:.85em;}
.boards-overview th,.boards-overview td{padding:6px 6px !important;min-width:0 !important;}
.boards-overview .btn{font-size:.9em;padding:2px 6px;}
</style>

<div class="boards-overview" markdown="1">

|  | [SimpleFOC-microspora](#microspora) | [SimpleFOCShield](#simplefocshield) | [SimpleFOC DriveShield](#driveshield) | [SimpleFOCMini](#simplefocmini) | [QtFOC](#qtfoc) | [SimpleFOCNano](#simplefocnano) |
| --- | --- | --- | --- | --- | --- | --- |
| Board | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOC-microspora"/> | <img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOCShield"/> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/main/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOC DriveShield"/> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOCMini"/> | <img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/top.png" style="width:100%;height:90px;object-fit:contain" alt="QtFOC"/> | <img src="extras/Images/nanoshield.png" style="width:100%;height:90px;object-fit:contain" alt="SimpleFOCNano"/> |
| Format | Integrated controller | Arduino shield | Arduino shield | Compact driver | XIAO / QT Py shield | Arduino Nano shield |
| Key hardware | STM32G431, DRV8316, MT6701 encoder, CAN | DRV8316, inline current sensing, voltage sensing | DRV8320H, inline current sensing | DRV8316, three-phase low-side current sensing | DRV8316, two-phase low-side current sensing, 5V buck for the MCU | DRV8313, supply-voltage sensing, 8V buck for the Nano |
| Intended use | Compact standalone BLDC and stepper control | Low and mid-power FOC with an external MCU (R3 headers) | Higher-current applications with an external MCU (R3 headers) | Small BLDC or stepper setups with an external MCU | Tiny all-in-one setups with a XIAO or QT Py | Small BLDC (gimbal) or stepper setups with an Arduino Nano |
| |[Find out more](microspora_landing){: .btn .btn-docs}|[Find out more](arduino_simplefoc_shield_showcase){: .btn .btn-docs}|[Find out more](driveshield){: .btn .btn-docs}|[Find out more](simplefocmini){: .btn .btn-docs}|[Find out more](qtfoc){: .btn .btn-docs}|[Find out more](simplefocnano){: .btn .btn-docs}|

</div>

## Which board should I use?

Answer a couple of questions and we'll point you to the board that fits your project.

<style>
.bq{border:1px solid #c0bfca;border-radius:8px;padding:16px;margin:1em 0 1.5em;max-width:760px;}
.bq-step{font-size:.8em;color:#686969;text-transform:uppercase;letter-spacing:.05em;margin-bottom:4px;}
.bq-q{font-size:1.15em;font-weight:500;margin-bottom:12px;}
.bq-opts{display:grid;grid-template-columns:repeat(auto-fill,minmax(210px,1fr));gap:8px;}
.bq-opt{text-align:left;border:1px solid #c0bfca;border-radius:6px;background:#fff;padding:10px 12px;cursor:pointer;font:inherit;color:inherit;line-height:1.3;}
.bq-opt:hover,.bq-opt:focus-visible{border-color:#44a8fa;box-shadow:0 2px 8px rgba(68,168,250,.2);outline:none;}
.bq-opt small{display:block;color:#686969;margin-top:3px;}
.bq-res{display:flex;gap:16px;align-items:center;flex-wrap:wrap;}
.bq-res img{width:160px;height:120px;object-fit:contain;}
.bq-res .bq-name{font-size:1.3em;font-weight:500;}
.bq-res p{margin:.4em 0;}
.bq-nav{margin-top:12px;font-size:.9em;}
.bq-nav button{background:none;border:none;color:#44a8fa;cursor:pointer;padding:0;margin-right:16px;font:inherit;}
.bq-nav{display:flex;flex-wrap:wrap;align-items:center;gap:4px;}
.bq-other{margin-left:auto;color:#686969 !important;}
</style>
<div class="bq" id="board-quiz"><noscript>Enable JavaScript to use the board finder, or see the comparison tables below.</noscript></div>
<script>
(function(){
var R='https://raw.githubusercontent.com/simplefoc/';
var B={
 microspora:{n:'SimpleFOC-microspora',img:R+'SimpleFOC-microspora/main/images/top.png',why:'Microcontroller, driver, magnetic encoder and CAN on one 33 x 34mm board, for small and medium motors. Plug in USB and a motor and you are ready to go.',spec:'STM32G431 · DRV8316 · 5-35V · 8A peak · BLDC and stepper',docs:'microspora_landing',cmp:'#microspora'},
 shield:{n:'SimpleFOCShield v4.1',img:R+'Arduino-SimpleFOCShield/master/images/top.png',why:'Stacks on your board, configured with solder pads, with inline current sensing. Stack two for two motors.',spec:'DRV8316 · up to 35V · 3-5A continuous, 8A peak · BLDC and stepper',docs:'arduino_simplefoc_shield_showcase',cmp:'#simplefocshield'},
 drive:{n:'SimpleFOC DriveShield',img:R+'SimpleFOC-DriveShield/main/images/top.png',why:'Same footprint and configuration as the Shield, with a gate driver and MOSFETs for bigger motors.',spec:'DRV8320H · 8-30V · 20A continuous, 30A peak · BLDC and stepper',docs:'driveshield',cmp:'#driveshield'},
 qtfoc:{n:'QtFOC',img:R+'QtFOC/master/images/top.png',why:'Plug in your XIAO or QT Py, the board powers it from the motor supply. SPI sensor connector and low-side current sensing.',spec:'DRV8316 · 8-35V · 8A peak · BLDC and stepper',docs:'qtfoc',cmp:'#qtfoc'},
 nano:{n:'SimpleFOCNano',img:'extras/Images/nanoshield.png',why:'Stacks with any Arduino Nano and powers it from the motor supply. SPI and STEMMA QT connectors for sensors.',spec:'DRV8313 · 8-30V · 2.5A · BLDC and stepper',docs:'simplefocnano',cmp:'#simplefocnano'},
 mini:{n:'SimpleFOCMini v2.3',img:R+'SimpleFOCMini/main/images/top.png',why:'A tiny driver you wire to any microcontroller, with low-side current sensing on all three phases.',spec:'DRV8316 · 5-35V · 8A peak · BLDC and stepper',docs:'simplefocmini',cmp:'#simplefocmini'},
 hp:{n:'Other high-performance boards',why:'Our boards go up to about 20A continuous. For bigger motors, have a look at the high-performance drivers and all-in-one boards that work with the SimpleFOC library, such as the ODrive-based boards.',docs:'bldc_drivers#high-performance-boards',l1:'High-performance boards',cmp:'supported_hardware',l2:'All supported hardware'},
 hp_aio:{n:'A high-performance all-in-one board',why:'Our all-in-one board, the microspora, is made for small and medium motors. For bigger motors, see the high-performance all-in-one boards that work with the SimpleFOC library, such as the B-G431B-ESC1 or the ODrive-based boards. Or use the DriveShield with a microcontroller board of your choice.',docs:'bldc_drivers#all-in-one-boards-1',l1:'All-in-one boards',cmp:'#driveshield',l2:'DriveShield'}
};
var WIRE='The DriveShield has Arduino UNO headers, but you can also wire it to any microcontroller: it needs 3 PWM pins, an enable pin and two analog pins for the current sensing.';
var Q={
 start:{q:'What motor do you want to drive?',o:[
  ['Small','Gimbal motors, small steppers: up to ~2.5A, phase resistance above ~5Ω','mcu_s'],
  ['Medium','Small drone motors, NEMA 17 steppers: up to ~5A','mcu_m'],
  ['Big','Drone motors, robot joints: 5-20A, phase resistance below ~1Ω','mcu_b'],
  ['Very big','More than 20A continuous','r:hp'],
  ['Not sure','Check the motor datasheet for the rated current, or start with a small or medium motor','mcu_m']]},
 mcu_s:{q:'Which microcontroller do you want to use?',o:[
  ['None, I want everything on one board','The quickest way to get a motor turning','r:microspora'],
  ['Arduino UNO, Nucleo-64 or similar','Any board with Arduino UNO headers','r:shield'],
  ['Seeed XIAO or Adafruit QT Py','','r:qtfoc'],
  ['Arduino Nano','Any Nano: classic, Every, 33, RP2040, ESP32','r:nano'],
  ['Something else','I will connect it with wires','r:mini']]},
 mcu_m:{q:'Which microcontroller do you want to use?',o:[
  ['None, I want everything on one board','The quickest way to get a motor turning','r:microspora'],
  ['Arduino UNO, Nucleo-64 or similar','Any board with Arduino UNO headers','r:shield'],
  ['Seeed XIAO or Adafruit QT Py','','r:qtfoc'],
  ['Arduino Nano','Any Nano: classic, Every, 33, RP2040, ESP32','r:mini:The Nano shield is limited to 2.5A. The Mini handles up to 8A peak and can be wired to your Nano.'],
  ['Something else','I will connect it with wires','r:mini']]},
 mcu_b:{q:'Which microcontroller do you want to use?',o:[
  ['None, I want everything on one board','','r:hp_aio'],
  ['Arduino UNO, Nucleo-64 or similar','Any board with Arduino UNO headers','r:drive'],
  ['Seeed XIAO, QT Py or Arduino Nano','','r:drive:'+WIRE],
  ['Something else','I will connect it with wires','r:drive:'+WIRE]]}
};
var el=document.getElementById('board-quiz'),hist=[];
function esc(t){var d=document.createElement('div');d.textContent=t;return d.innerHTML;}
function nav(){return '<div class="bq-nav">'+(hist.length>1?'<button data-a="back">← Back</button>':'')+(hist.length>1?'<button data-a="restart">Start over</button>':'')+'<a class="bq-other" href="supported_hardware">None of these fit? See other supported hardware →</a></div>';}
function show(key){
 if(key.indexOf('r:')===0){var p=key.split(':'),b=B[p[1]],note=p.slice(2).join(':');
  el.innerHTML='<div class="bq-step">Our suggestion</div><div class="bq-res">'+(b.img?'<img src="'+b.img+'" alt="">':'')+'<div style="flex:1;min-width:220px"><div class="bq-name">'+esc(b.n)+'</div><p>'+esc(b.why)+'</p>'+(note?'<p><i>'+esc(note)+'</i></p>':'')+(b.spec?'<p><small>'+esc(b.spec)+'</small></p>':'')+'<a class="btn btn-primary" href="'+b.docs+'">'+(b.l1||'Read more')+'</a> <a class="btn btn-docs" href="'+b.cmp+'">'+(b.l2||'Compare')+'</a></div></div>'+nav();
 } else {var s=Q[key],h='<div class="bq-step">Question '+hist.length+'</div><div class="bq-q">'+esc(s.q)+'</div><div class="bq-opts">';
  s.o.forEach(function(o){h+='<button class="bq-opt" data-go="'+o[2].replace(/"/g,'&quot;')+'">'+esc(o[0])+(o[1]?'<small>'+esc(o[1])+'</small>':'')+'</button>';});
  el.innerHTML=h+'</div>'+nav();}
}
function go(k){hist.push(k);show(k);}
el.addEventListener('click',function(e){var t=e.target.closest('button');if(!t)return;
 if(t.dataset.go){go(t.dataset.go);}
 else if(t.dataset.a==='back'){hist.pop();show(hist[hist.length-1]);}
 else if(t.dataset.a==='restart'){hist=[];go('start');}});
go('start');
})();
</script>

<blockquote class="info" markdown="1"><p class="heading">None of these boards fit your project?</p>
The <span class="simple">Simple<span class="foc">FOC</span>library</span> works with many other driver boards and microcontrollers: 3PWM and 6PWM BLDC drivers, stepper drivers, gimbal controllers, ODrive-like boards and more. See the [supported hardware](supported_hardware) section for drivers, microcontrollers, motors and sensors, and the [community forum](https://community.simplefoc.com/) for community designs.

[Supported hardware](supported_hardware){: .btn .btn-docs} [BLDC drivers](bldc_drivers){: .btn .btn-docs} [Stepper drivers](stepper_drivers){: .btn .btn-docs}
</blockquote>

The sections below compare similar boards side by side.



## Integrated controller boards

### <span class="simple">Simple<span class="foc">FOC</span>-microspora</span> <small>v1.7</small> - <small>[Find out more](microspora_landing)</small> {#microspora}
{: .no_toc }

![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=blue)

<img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-microspora/main/images/top.png" class="width40" alt="SimpleFOC-microspora board"/>

SimpleFOC-microspora combines a motor driver and microcontroller on one compact board. It is designed for low-power BLDC and stepper projects that benefit from an integrated controller, onboard magnetic encoder, and CAN connectivity.

- **MCU**: STM32G431CBU6
- **Motor driver**: DRV8316, 5-35V supply, up to 8A
- **Sensing**: MT6701 magnetic encoder, three-phase current sensing, and supply-voltage sensing
- **Connectivity**: CAN daisy-chain connectors, SPI, and a multi-purpose I2C/encoder/GPIO connector
- **Size**: 33 x 34 mm
- **Open hardware**: [EasyEDA project](https://oshwlab.com/the.skuric/microspora-simplefoc-antun)
- **Firmware and examples**: [SimpleFOC Microspora firmware](https://github.com/simplefoc/microspora_simplefoc_firmware)
- **Availability**: [Makerfabs](https://www.makerfabs.com/simplefoc-microspora.html)

[Read more about the Microspora board](microspora_landing){: .btn .btn-docs}

## Shield boards

These boards are designed to be compatible with the Arduino UNO R3 headers, enabling an easy to start experience with the <span class="simple">Simple<span class="foc">FOC</span>library</span> and the Arduino IDE. The boards can be used with any board with the standard Arduino headers, such as the Arduino MEGA, STM32 Nucleo boards, Adafruit Metro, ESP32 D1 R3, Arudino UNO R4 and many others. This format enables user to easily exchange the microcontrollers and find the best solution for their application. The boards are fully open-source and the fabrication files are available in the respective repositories, as well as detailed guides on how to fabricate the boards yourself. 

| Feature | <span id="simplefocshield"></span>[SimpleFOCShield v4.1](arduino_simplefoc_shield_showcase) | <span id="driveshield"></span>[SimpleFOC DriveShield v1.8](driveshield) |
| --- | --- | --- |
| Board | <img src="https://raw.githubusercontent.com/simplefoc/Arduino-SimpleFOCShield/master/images/top.png" style="height:250px" alt="SimpleFOCShield" /> | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOC-DriveShield/main/images/top.png" style="height:250px" alt="SimpleFOC DriveShield" /> |
| Motor driver | DRV8316 | DRV8320H gate driver with BSZ0904NSI MOSFETs |
| Current sensing | INA240 inline sensor; ±6.6A (3.3V) or ±10A (5V) measurement range | INA240 inline sensor; ±40A measurement range |
| Maximum current | 3-5A continuous; 8A peak | 20A continuous; 30A peak measured |
| Maximum input voltage | 35V | 30V |
| Onboard regulator | 8V regulator | 8V regulator (v1.8+) |
| Stackable | Yes | Yes |
| Encoder/Hall pullups | 3.3kΩ, configurable | 3.3kΩ, configurable |
| I2C pullups | 4.7kΩ, configurable | 4.7kΩ, configurable |
| Supply voltage sensing | Yes | - |
| Configurable pinout | Soldering pads | Soldering pads |
| Arduino headers | UNO, MEGA, STM32 Nucleo, and compatible boards | UNO, MEGA, STM32 Nucleo, and compatible boards |
| Additional connectors | STEMMA QT and SPI | STEMMA QT and SPI |
| Board size | 56 x 53 mm | 56 x 53 mm |
| Open-Source | [GitHub](https://github.com/simplefoc/Arduino-SimpleFOCShield) | [GitHub and fabrication files](https://github.com/simplefoc/SimpleFOC-DriveShield)|
|**Available**: | [SimpleFOC shop](https://simplefoc.com/simplefoc_shield_product), Makerfabs (coming soon) | [Makerfabs](https://www.makerfabs.com/simplefoc-driveshield.html)|

The DriveShield made by Makerfabs uses 2oz outer copper layers, and the board has room for a 23 x 23mm heatsink for currents above 15A. See its [thermal measurements](driveshield#thermal-performance).

[Read more about SimpleFOC Shield board](arduino_simplefoc_shield_showcase){: .btn .btn-docs} [Read more about the DriveShield](driveshield){: .btn .btn-docs}

## Mini boards

This is a set of miniature boards designed to be small, low-cost, and easy to use. They are intended for low power applications and are designed to be compatible with the <span class="simple">Simple<span class="foc">FOC</span>library</span>. The boards are created as minimal working examples and are intended to be used as a reference design for the community to build upon. The boards are fully open-source and the fabrication files are available in the respective repositories, as well as detailed guides on how to fabricate the boards yourself. 

The Mini is a standalone driver you wire to any microcontroller. QtFOC and SimpleFOCNano are boards that a small microcontroller board plugs into: a Seeed Studio XIAO or Adafruit QT Py for QtFOC, an Arduino Nano for SimpleFOCNano. Both power the microcontroller from the motor supply and have JST connectors for the sensors.

| Feature | <span id="simplefocmini"></span>[SimpleFOCMini v2.3](simplefocmini) | <span id="qtfoc"></span>[QtFOC v1.6](qtfoc) | <span id="simplefocnano"></span>[SimpleFOCNano](simplefocnano) |
| --- | --- | --- | --- |
| Board | <img src="https://raw.githubusercontent.com/simplefoc/SimpleFOCMini/main/images/top.png" style="height:200px" alt="SimpleFOCMini" /> | <img src="https://raw.githubusercontent.com/simplefoc/QtFOC/master/images/top.png" style="height:200px" alt="QtFOC" /> | <img src="extras/Images/nanoshield.png" style="height:200px" alt="SimpleFOCNano" /> |
| Format | Standalone driver, wired to any MCU | Driver shield for Seeed Studio XIAO / Adafruit QT Py | Driver shield for Arduino Nano |
| Motors | BLDC and stepper | BLDC and stepper | BLDC and stepper |
| Motor driver | DRV8316, 3-PWM | DRV8316, 3-PWM | DRV8313, 3-PWM + 3 enable |
| Power supply | 5-35V | 8-35V | 8-30V |
| Maximum current | 8A peak | 8A peak | 2.5A per phase |
| Current sensing | Three-phase low-side, 150mV/A | Two-phase low-side, 150mV/A | - |
| Supply voltage sensing | Yes | Yes, `A2` | Yes, `A0` |
| Power output | 3.3V LDO for the sensor, up to 20mA | 5V buck for the MCU, up to 200mA | 8V buck to the Nano's `VIN` |
| Sensor connector | - | SPI (JST SH, 6-pin) | SPI and STEMMA QT (JST SH) |
| Board size | 24 x 25 mm | 25 x 45 mm | Arduino Nano size |
| Open-Source | [GitHub](https://github.com/simplefoc/SimpleFOCMini), [EasyEDA](https://oshwlab.com/the.skuric/simplefocmini_copy_copy) | [GitHub](https://github.com/simplefoc/QtFOC), [EasyEDA](https://oshwlab.com/the.skuric/simplefocmini_copy_copy_copy_copy) | [GitHub](https://github.com/simplefoc/SimpleFOCNano) (KiCad) |
|**Available**: | [Makerfabs](https://www.makerfabs.com/simplefocmini.html) | Makerfabs (coming soon), or [fabricate it yourself](mini_fabrication) | [Tindie](https://www.tindie.com/products/smallrobots/simplefoc-nano-shield/) |

[Read more about Mini board](simplefocmini){: .btn .btn-docs} [Read more about QtFOC](qtfoc){: .btn .btn-docs} [Read more about SimpleFOCNano](simplefocnano){: .btn .btn-docs}
