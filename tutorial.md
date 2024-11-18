# My Tutorial
### @activities true
### @explicitHints true

# Sistem de udare automat

## Sistem de alarma pentru umiditatea solului
### Introducere
Pentru aceasta activitate vom masura umiditatea solului si vom reda un sunet de alarma cand acesta este prea uscat.
De asemenea, vom afisa o fata trista daca solul este uscat si una zambitoare daca solul este ud.
Vom folosi kit-ul kitronik_smart_greenhouse.

### Step 1
The first thing to do is to set up the ZIP LEDs and create the status LEDs range.
From the ``||kitronik_smart_greenhouse.ZIP LEDs||`` section of the ``||kitronik_smart_greenhouse.Greenhouse||`` category, add the ``||variables:set zipLEDs to||`` ``||kitronik_smart_greenhouse.Smart Greenhouse with 8 ZIP LEDs||`` block to the ``||basic:on start||`` section, followed by the ``||variables:set statusLEDs||`` block.

#### ~ tutorialhint
```blocks
let zipLEDs = kitronik_smart_greenhouse.createGreenhouseZIPDisplay(8)
let statusLEDs = zipLEDs.statusLedsRange()
```

### Step 2
Next, create a new variable called ``||variables:soilHue||``. This will be used to set and store the ZIP LED colour hue based on the soil moisture reading from the sensor.  
In the ``||basic:forever||`` loop, ``||variables:set tempHue to||`` the ``||math:map||`` block, which can be found in the ``||math:Math||`` category.

#### ~ tutorialhint
```blocks
basic.forever(function () {
    soilHue = Math.map(0, 0, 1023, 0, 4)
})
```

### Step 3
In the ``||math:map||`` block, put the ``||kitronik_smart_greenhouse.Analog read P1||`` block in the first slot - this can be found in the ``||kitronik_smart_greenhouse.Inputs/Outputs||`` section of the ``||kitronik_smart_greenhouse.Greenhouse||`` category. Then, set ``||math:from low 0 high 1023 to low 35 high 150||`` to give a desert sand colour for a low moisture reading and a watery colour for a high moisture reading.

#### ~ tutorialhint
```blocks
basic.forever(function () {
    soilHue = Math.map(kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1), 0, 1023, 35, 150)
})
```

### Step 4
Now that the soil moisture hue has been set and stored, the ZIP LED colour can now be set.  
After the ``||variables:set soilHue||`` block, add a  ``||kitronik_smart_greenhouse.ZIP LED 2 to hue||`` ``||variables:soilHue||``. Finally, put a ``||variables:statusLEDs||``  ``||kitronik_smart_greenhouse.show||`` block at the end of the ``||basic:forever||`` loop.

#### ~ tutorialhint
```blocks
let statusLEDs: kitronik_smart_greenhouse.greenhouseZIPLEDs = null
basic.forever(function () {
    soilHue = Math.map(kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1), 0, 1023, 35, 150)
    statusLEDs.setZipLedColor(2, kitronik_smart_greenhouse.hueToRGB(soilHue))
    statusLEDs.show()
})
```

### Step 5
Displaying the soil moisture has now been set up, but it isn't being used to control anything yet.  
After the ``||kitronik_smart_greenhouse.show||`` block, add in an ``||logic:if else||`` block from the ``||logic:Logic||`` category. Check whether the measured soil moisture value is less than 400 (this value can be changed depending on how much water particular plants need, a lower value means drier soil).  
The ``||logic:if||`` statement should read: ``||logic:if||`` ``||kitronik_smart_greenhouse.Analog read P1||`` ``||logic:≤ 400 then||``. 

#### ~ tutorialhint
```blocks
let statusLEDs: kitronik_smart_greenhouse.greenhouseZIPLEDs = null
basic.forever(function () {
    soilHue = Math.map(kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1), 0, 1023, 35, 150)
    statusLEDs.setZipLedColor(2, kitronik_smart_greenhouse.hueToRGB(soilHue))
    statusLEDs.show()
    if (kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1) <= 400) {
        
    } else {
        
    }
})
```

### Step 6
The ``||logic:if||`` statement is set up to check whether the soil is too dry, so now an action needs to be taken if this condition is met.  
Audio is going to be used, so to set this up, add in the ``||kitronik_smart_greenhouse.set music pin for buzzer||`` block to the ``||basic:on start||`` section. This block is in the top level of the ``||kitronik_smart_greenhouse.Greenhouse||`` cateogry.  
From the ``||music:Music||`` category, add a ``||music:start melody||`` block inside the ``||logic:if||`` statement, select a tune from the drop-down list and set the the repeat to ``||music:forever||``. After this, place a 2 second ``||basic:pause||``, followed by a ``||music:stop melody all||`` block. The audio alarm is now complete.

#### ~ tutorialhint
```blocks
kitronik_smart_greenhouse.setBuzzerPin()
let zipLEDs = kitronik_smart_greenhouse.createGreenhouseZIPDisplay(3)
let statusLEDs = zipLEDs.statusLedsRange()
basic.forever(function () {
    if (kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1) <= 400) {
        music.startMelody(music.builtInMelody(Melodies.BaDing), MelodyOptions.Forever)
        basic.pause(2000)
        music.stopMelody(MelodyStopOptions.All)
    } else {
        
    }
})
```

### Step 7
The final stage of the alarm is to add some visual indicators as well. Inside the ``||logic:if||`` statement, just before the ``||music:start melody||`` block, add in a ``||basic:show icon||`` block with a sad face selected, and add another of these blocks inside the ``||logic:else||`` sectin, but this time with a happy face.  
Now, the micro:bit will be happy when the plant has enough water and sad when it's too dry.

#### ~ tutorialhint
```blocks
basic.forever(function () {
    if (kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1) <= 400) {
        basic.showIcon(IconNames.Sad)
        music.startMelody(music.builtInMelody(Melodies.BaDing), MelodyOptions.Forever)
        basic.pause(2000)
        music.stopMelody(MelodyStopOptions.All)
    } else {
        basic.showIcon(IconNames.Happy)
    }
})
```

### Step 8
The soil moisture alarm is now complete, so click ``|Download|`` and transfer the code to the Environmental Control Board.  
Try moving the Mini Prong around the planting area to see when the alarm is triggered.

## Sistem automat de udare
### Introducere
Minisera are acum un sistem de alarma pentru umiditatea solului.
In continuare vom realiza sistemul de udare automat.

### Step 1
In order to automate the watering process, all that needs to happen is for the water pump to be switched on in the same place in the code as the alarm is triggered. However, the water water needs to be added to the planting area carefully to stop it spilling. To do this, the pump will actually need to be switched on and off in several short bursts.  
Start by adding a ``||loops:repeat||`` loop to the ``||logic:if||`` section, just after the ``||music:stop melody||`` block. Set the number of repeats to **3**.

#### ~ tutorialhint
```blocks
basic.forever(function () {
    if (kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1) <= 400) {
        basic.showIcon(IconNames.Sad)
        music.startMelody(music.builtInMelody(Melodies.BaDing), MelodyOptions.Forever)
        basic.pause(2000)
        music.stopMelody(MelodyStopOptions.All)
        for (let index = 0; index < 3; index++) {
            
        }
    } else {
        basic.showIcon(IconNames.Happy)
    }
})
```

### Step 2
Next, from the ``||kitronik_smart_greenhouse.Inputs/Outputs||`` section of the ``||kitronik_smart_greenhouse.Greenhouse||`` category, add a ``||kitronik_smart_greenhouse.turn high power P13 ON||`` block inside the ``||loops:repeat||`` loop. After this, add a 100ms ``||basic:pause||``, then turn **OFF** ``||kitronik_smart_greenhouse.high power P13||``, and finally add a 1 second ``||basic:pause||`` at the end. This section of code will now turn the water pump on for 100ms, turn it off for 1 second, and repeat this 3 times.

#### ~ tutorialhint
```blocks
basic.forever(function () {
    if (kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1) <= 400) {
        basic.showIcon(IconNames.Sad)
        music.startMelody(music.builtInMelody(Melodies.BaDing), MelodyOptions.Forever)
        basic.pause(2000)
        music.stopMelody(MelodyStopOptions.All)
        for (let index = 0; index < 3; index++) {
            kitronik_smart_greenhouse.controlHighPowerPin(kitronik_smart_greenhouse.HighPowerPins.pin13, kitronik_smart_greenhouse.onOff(true))
            basic.pause(100)
            kitronik_smart_greenhouse.controlHighPowerPin(kitronik_smart_greenhouse.HighPowerPins.pin13, kitronik_smart_greenhouse.onOff(false))
            basic.pause(1000)
        }
    } else {
        basic.showIcon(IconNames.Happy)
    }
})
```

### Step 3
At the moment, the program checks the soil moisture level constantly, but when water is poured into a pot it doesn't immediately soak through all the soil evenly. To leave time for the mositure level to actually change, the program needs to increase the time between each measurement. To do this, add a 10000ms ``||basic:pause||`` after the ``||logic:if else||`` block in the ``||basic:forever||`` loop.

#### ~ tutorialhint
```blocks
let statusLEDs: kitronik_smart_greenhouse.greenhouseZIPLEDs = null
basic.forever(function () {
    soilHue = Math.map(kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1), 0, 1023, 35, 150)
    statusLEDs.setZipLedColor(2, kitronik_smart_greenhouse.hueToRGB(soilHue))
    statusLEDs.show()
    if (kitronik_smart_greenhouse.readIOPin(kitronik_smart_greenhouse.PinType.analog, kitronik_smart_greenhouse.IOPins.p1) <= 400) {
        basic.showIcon(IconNames.Sad)
        music.startMelody(music.builtInMelody(Melodies.BaDing), MelodyOptions.Forever)
        basic.pause(2000)
        music.stopMelody(MelodyStopOptions.All)
        for (let index = 0; index < 5; index++) {
            kitronik_smart_greenhouse.controlHighPowerPin(kitronik_smart_greenhouse.HighPowerPins.pin13, kitronik_smart_greenhouse.onOff(true))
            basic.pause(1000)
            kitronik_smart_greenhouse.controlHighPowerPin(kitronik_smart_greenhouse.HighPowerPins.pin13, kitronik_smart_greenhouse.onOff(false))
            basic.pause(2000)
        }
    } else {
        basic.showIcon(IconNames.Happy)
    }
    basic.pause(10000)
})
```

### Step 4
CODING COMPLETE! Click ``|Download|`` and transfer the code to the Environmental Control Board.  
Wait for the soil to dry out and see the pump automatically water the plant.