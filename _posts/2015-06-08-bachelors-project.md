---
layout:     post
title:      Bachelors Project
date:       2015-06-08 16:21:19
summary:    My Bachelors Thesis: Implement a framework to create an easy interface for using different inclination and acceleration sensors on the Raspberry Pi
categories: Raspberry_Pi Bachelor Uni
---

## Microcontroller based sensor system for construction monitoring
### About my Bachelors Project
For the last ten month or so I have been working with a sensor system to monitor inclinations and movements of structures. The system is based on [Raspberry Pis](https://www.raspberrypi.org/) that can be wirelessly connected to communicate.

Most of my work was to implement the controlling code for a new sensor prototype manifactured by a partnering company from Berlin. There was code when I started but it had many flaws and I cant believe it produced reliable measurements at any point in time.    
I also worked with different sensors, testing their capabilities, the data rate, the resolution, reliability and so on. We work with sensors costing 2€ ranging to 500€. So depending on the task, we can choose a differet system.

Everytime I worked on a new Sensor, I had to rewrite the complete thing to work with the new sensor and its specific requirements. And sometimes when writing a new programm using a sensor alsready in use, I had to adapt its interface, write a wrapper around functions and so on. So we had many different approaches and specific combinations. What we do not have is an easy way to exchange the sensor without having to redo the whole code.   
What we want to have is the infrastructure to plug in a different sensor, change one config file and the code works as before, even if the new sensor is bought after the program has been deployed. In C++ language this is a case for inheritance and dynamic loading of .so files.   
At the same time we do not want to loose the specific capabilities of the individual sensors. There are sensor that support measuring 3 axes at the same time and / or a 3 axis gyroscope. Other sensors include temperature and I cannot even predict what else could be measured with this system. So we have to keep the system flexibel enough to extend it and use specialities of special sensors without interfering with the possibility to use a general sensor interface in a program.

There are many people working on projects related to this sensoring system. Some testing new sensors and some using the sensors to measure things, visualize the results and implement real life solutions. I want to connect all of them on one point, so one can use the work of the others without having to rewrite any code.

### Idea for the interface
The interface we want to use should look like this in its most basic form:

{% highlight c++ %}
main() 
{
    Config conf = createConfigFromFile(someFile);
    Sensor sensor = Sensor(config); //the config contains everything to initialize the correct sensor

    while ( measurementRunning() )
        {
          Record rec = sensor.getRecord();
	  float acceleration = rec.acceleration;
	  long timestamp = rec.timestamp;
	  // ...
	}
}

{% endhighlight %}

But there should also be the option to use the specific capabilities of the sensor:

{% highlight c++ %}
main() 
{
    Config conf = createConfigFromFile(someFile);
    Sensor sensor = Sensor(config); //the config contains everything to initialize the correct sensor

    if (sensor.hasTemperature() ) 
    {
      Record rec = sensor.getRecord(sensor.TIMESTAMP , sensor.X , sensor.TEMPERATURE ); //define values to return
      float temperature = rec.temperature;
    }

    if (sensor.getAxis() == 3 )
    {
      Record rec = sensor.getRecord( sensor.TIMESTAMP , sensor.X , sensor.Y , sensor.Y);
      float resulting =  Math.sqrt(rec.x*rec.x + rec.y*rec.y + rec.z*rec.z);
    }

}
{% endhighlight %}

### Some Classes
These are some classes that I probably will use to accompish this:
* abstract Sensor: Mask for minimal interface.
    * each Sensor will have to implement this class
* abstract Config: Has at least to include a Type field so the correct sensor can be initialised with it.
    * each Sensor will have to implement its required config object inluding the output into a human readable text format, probably JSON
* Record
    * there will be a "base" Record that can be used but it will only contain the bare minimum. If a sensor whould provide more data, it has to implment its own Record with all necessary fields.

### State of work
I am only beginning with this project, so except for some first ideas and talks with computer science students, not much has happend yet. But I have an idea how it could look and work in the end. 

Currently I am in the phase where I search the web if someone already did something like this, that could be an inspiration or even a base for my work. Until now, I have not found something similar yet.
