original credit goes to https://github.com/JMathiszig-Lee

using this to test Eleveld calcs against a reference

# PyTCI

A python package for Target Controlled Infusions. 

Spawned from the NHS Hack Day project https://github.com/JMathiszig-Lee/Propofol, this splits out useful code into a package and updates it to python3

[![Build Status][image-1]][1]
[![Coverage Status][image-2]][2]

# Installation
if using pip
```python
pip install PyTCI
```
if using pipenv (you should, it's great)
```python
pipenv install PyTCI
```
# Usage
PyTCI currently supports the following:

**Body Mass equations:**
* BMI
* Ideal body weight (Devine)
* Adjusted body weight
* James Equation
* Boer
* Hume(1966)
* Hume(1971)
* Janmahasation(2005)

example:
```python
>>> from PyTCI.weights import leanbodymass
>>> leanbodymass.hume66(180, 60 'm')
51.2
```
# Models:
**Propofol**
* Schnider
* Marsh
* Kataria
* Paedfusor

**Remifentanil**
* Minto

**Alfentanil**
* Maitre

example:
```python
>>> from PyTCI.models import propofol
>>> patient = propofol.Eleveld(40, 70, 170, 'm')
>>> patient.v2
24
```

the class methods `give_drug` and `wait_time` can he used to model propofol kinetics

example:
```python
>>> from PyTCI.models import propofol
>>> patient = propofol.Eleveld(40, 70, 170, 'm')
>>> patient.give_drug(200)
>>> patient.x1
9.746588693957115
>>> patient.wait_time(60)
>>> patient.x1
7.438318565317236
```

**Infusions**

Infusions are currently only implemented for propofol

The two methods available are `effect_bolus` and `plasma_infusion`

Effect bolus returns the bolus (in mg) needed over 10 seconds to achieve the desired effect site concentration. It's input is the desired target in ug/ml and returns the bolus needed in mg
```python
>>> patient = propofol.Eleveld(40, 70, 190, 'm')
>>> patient.effect_bolus(6)
95.1
```
the function uses a simple search to find a dose that gets within 2% of the desired concentration 


Plasma\_infusion takes desired plasma concentration(ug/ml) and desired time (seconds) and returns a python list of the required infusions rates every ten seconds in the format [(mg per second)],(resulting plasma concentration)]
```python
>>> patient.plasma_infusion(3, 60)
[(1.4015583251674446, 2.999999999999999), (0.20276642700021327, 2.9999999999999996), (0.1897043393887194, 2.9999999999999987), (0.1792243459524349, 3.000000000000001), (0.17081505252896076, 3.0000000000000013), (0.16406634920010446, 3.000000000000002)]
>>> 
```



The built in models inherit from a parent class.
You can define your own models and use the same functions to see how yours performs
```python
class MyNewModel(Propofol):
     def __init__(self, desired, arguments):
        #my custom code to generate volumes and constants
        self.v1 = a_constant * weight
        self.v2 = a_constant * lean_body_mass
        etc... etc...

        #if you want to work with clearances rate constants must be generated
        self.from_clearances(self)

        #finally set up model 
        self.setup(self)

```

[1]:	https://travis-ci.org/JMathiszig-Lee/PyTCI
[2]:	https://coveralls.io/github/JMathiszig-Lee/PyTCI?branch=master

[image-1]:	https://travis-ci.org/JMathiszig-Lee/PyTCI.svg?branch=master
[image-2]:	https://coveralls.io/repos/github/JMathiszig-Lee/PyTCI/badge.svg?branch=master
