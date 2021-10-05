import RPi.GPIO as GPIO
import time
dac=[26, 19, 13, 6, 5, 11, 9, 10]
bits = len(dac)
levels = 2**bits
maxVoltage = 3.3
troykaModule = 17
comparator = 4                                                                                              

def decimal2binary(decimal):
    return [int(bit) for bit in bin(decimal)[2:].zfill(bits)]

def bin2dac(value):
    signal = decimal2binary(value)
    GPIO.output(dac, signal)
    return signal  
GPIO.setmode(GPIO.BCM)
GPIO.setup(dac, GPIO.OUT, initial = GPIO.LOW)
GPIO.setup(troykaModule,GPIO.OUT, initial=GPIO.HIGH)
GPIO.setup(comparator, GPIO.IN)
def abc():
    value=0
    for steps1 in range(7, -1, -1):
        deltav=2**steps1
        signal=bin2dac(deltav+value)
        voltage = (deltav+value)/levels * maxVoltage
        time.sleep(0.007)
        comparator = GPIO.input(4)
        if comparator ==1:
            value=value+deltav
    return value       
try:
    while True:
       a=abc()
       print ("Digital value:  ={:^3} -> {}, Analog value = {:.2f}V".format(a, bin2dac(a), a / levels * maxVoltage))
        
except KeyboardInterrupt:
    print("The programm was stopped by the keyboard")
else:
    print("No exceptions")
finally:
    GPIO.output(dac, GPIO.LOW)
    GPIO.cleanup(dac)
    print ("end")