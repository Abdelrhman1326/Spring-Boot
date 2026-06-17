_**The Observer Pattern defines a one-to-many dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically.**_

The Observer Pattern is used when multiple objects need to react to changes in another object.

Examples
- Weather station updates displays
- GitHub push triggers webhooks
- Stock price updates dashboards
- YouTube upload notifies subscribers

Instead of observers constantly asking:

> “Did something change?”

…the subject pushes updates to them.

This replaces **polling** with **event-driven communication**.

---
# Core Components

## 1. Subject (Publisher)

The object being observed.

Responsibilities:
- Maintain state
- Register/unregister observers
- Notify observers

``` java
public interface Subject {    
	void registerObserver(Observer o);    
	void removeObserver(Observer o);    
	void notifyObservers();
}
```

---

## 2. Observer

Objects interested in state changes.

Responsibilities:
- Subscribe to subject
- Receive updates
- React

``` java
public interface Observer {    
	void update(float temp, float humidity, float pressure);
}
```

---

## 3. DisplayElement

Specific to the Weather Station example.

Only display-related observers need this.

``` java
public interface DisplayElement {
	void display();
}
```

---
# Complete Code — Weather Station Example

## WeatherData (Concrete Subject)

``` java
import java.util.ArrayList;

public class WeatherData implements Subject {
    private ArrayList<Observer> observers;

    private float temperature;
    private float humidity;
    private float pressure;

    public WeatherData() {
        observers = new ArrayList<>();
    }

    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        int i = observers.indexOf(o);

        if (i >= 0) {
            observers.remove(i);
        }
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature, humidity, pressure);
        }
    }

    public void measurementsChanged() {
        notifyObservers();
    }

    public void setMeasurements(float temperature,
                                float humidity,
                                float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;

        measurementsChanged();
    }
}
```

Flow:

``` r
							New Weather Data
							        ↓
							setMeasurements()
							        ↓
							notifyObservers()
							        ↓
						   All observers update
```

---

## Current Conditions Display

Shows current weather:

``` java
public class CurrentConditionsDisplay implements Observer, DisplayElement {

    private float temperature;
    private float humidity;
    private Subject weatherData;

    public CurrentConditionsDisplay(Subject weatherData) {
        this.weatherData = weatherData;
        weatherData.registerObserver(this);
    }

    @Override
    public void update(float temp, float humidity, float pressure) {
        this.temperature = temp;
        this.humidity = humidity;
        display();
    }

    @Override
    public void display() {
        System.out.println(
            "Current conditions: " +
            temperature + "F degrees and " +
            humidity + "% humidity"
        );
    }
}
```

---

## StatisticsDisplay

Tracks average/min/max temperature.

``` java
public class StatisticsDisplay implements Observer, DisplayElement {

    private float maxTemp = 0.0f;
    private float minTemp = 200;
    private float tempSum = 0.0f;
    private int numReadings;

    public StatisticsDisplay(Subject weatherData) {
        weatherData.registerObserver(this);
    }

    @Override
    public void update(float temp, float humidity, float pressure) {
        tempSum += temp;
        numReadings++;

        if (temp > maxTemp) maxTemp = temp;
        if (temp < minTemp) minTemp = temp;

        display();
    }

    @Override
    public void display() {
        System.out.println(
            "Avg/Max/Min temperature = " +
            (tempSum / numReadings) +
            "/" + maxTemp +
            "/" + minTemp
        );
    }
}
```

---

## ForecastDisplay

Predicts weather using pressure.

``` java
public class ForecastDisplay implements Observer, DisplayElement {

    private float currentPressure = 29.92f;
    private float lastPressure;

    public ForecastDisplay(Subject weatherData) {
        weatherData.registerObserver(this);
    }

    @Override
    public void update(float temp, float humidity, float pressure) {
        lastPressure = currentPressure;
        currentPressure = pressure;
        display();
    }

    @Override
    public void display() {
        if (currentPressure > lastPressure) {
            System.out.println("Improving weather on the way!");
        } else if (currentPressure == lastPressure) {
            System.out.println("More of the same");
        } else {
            System.out.println("Watch out for cooler weather");
        }
    }
}
```

---

## Main Program

``` java
public class WeatherStation {

    public static void main(String[] args) {

        WeatherData weatherData = new WeatherData();

        CurrentConditionsDisplay currentDisplay =
                new CurrentConditionsDisplay(weatherData);

        StatisticsDisplay statisticsDisplay =
                new StatisticsDisplay(weatherData);

        ForecastDisplay forecastDisplay =
                new ForecastDisplay(weatherData);

        weatherData.setMeasurements(80, 65, 30.4f);
        weatherData.setMeasurements(82, 70, 29.2f);
        weatherData.setMeasurements(78, 90, 29.2f);
    }
}
```

---
# Push vs Pull Models

## Push Model

Subject sends all data directly.

``` java
observer.update(temp, humidity, pressure);
```

Pros:
- Simple
- Efficient for small systems

Cons:
- Observer may receive unnecessary data

---

## Pull Model

Subject sends itself.

``` java
observer.update(this);
```

Observer asks for what it needs.

Example:

``` java
weatherData.getTemperature();
```

Pros:
- Flexible

Cons:
- Slightly more coupling

---
# Design Principles

## 1. Identify the aspects of your application that vary and separate them from what stays the same

### Core Idea

Separate frequently changing parts from stable parts.

Weather Station:
- **Stable:** Weather sensor logic
- **Changing:** Displays / observers

Why?
If display logic is mixed with sensor logic, every new display forces modifications to core code.

By separating them:
- sensor evolves independently
- displays evolve independently

This reduces maintenance cost.

---

## 2. Program to an interface, not an implementation

### Core Idea
Depend on abstractions, not concrete classes.

Bad:
``` java
ArrayList<CurrentConditionsDisplay>
```

Good:
``` java
ArrayList<Observer>
```

Why?
Because `WeatherData` should not care whether the observer is:
- phone display
- TV display
- logger
- webhook

It only cares that the object supports:
``` java
update()
```

Benefits:
- flexibility
- polymorphism
- easy extension

---
## 3. Favor composition over inheritance

### Core Idea
Build objects by combining smaller objects instead of inheriting everything.

Observer uses composition:
``` java
WeatherData HAS-A List<Observer>
```

Not inheritance.

This allows runtime behavior changes:
``` java
registerObserver(...)removeObserver(...)
```

Benefits:
- more flexible design
- runtime extensibility
- less rigid class hierarchies

---

## 4. Strive for loosely coupled designs between objects that interact

### Core Idea
Objects should know as little as possible about each other.

Bad (tight coupling):
``` java
class WeatherData {
	PhoneDisplay phone;
	TVDisplay tv;
}
```

Problems:
- subject depends on concrete observers
- adding observers requires subject changes
- changes ripple through system

Good (loose coupling):
``` java
ArrayList<Observer>
```

Now subject only knows:
``` java
observer.update()
```

It doesn’t care what observer actually is.
Benefits:
- easier maintenance
- easier testing
- easier extension
- fewer cascading bugs

This is the **main principle** of the Observer chapter.

---
# Summary

``` r
						  Subject changes state
									↓
							notifyObservers()
									↓
						 Observers receive update
									↓
						 Each reacts independently
```

Observer solves:
- wasteful polling
- duplicated update-check logic
- tight coupling

It provides:
- automatic synchronization
- loose coupling
- extensibility
- clean architecture