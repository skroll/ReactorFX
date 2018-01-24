# (FX)cellent Reactor
[![Build Status](https://travis-ci.org/shadskii/FXcellent-Reactor.svg?branch=master)](https://travis-ci.org/shadskii/FXcellent-Reactor)
[![codecov](https://codecov.io/gh/shadskii/FXcellent-Reactor/branch/master/graph/badge.svg)](https://codecov.io/gh/shadskii/FXcellent-Reactor)

This lightweight library allows for simple integration between [Project Reactor](https://projectreactor.io/) and 
[JavaFX](https://docs.oracle.com/javase/8/javafx/get-started-tutorial/jfx-overview.htm). FXcellent Reactor provides fluent 
factories to create `Flux` for the propagation of events from JavaFX Controls and Observables.

## Events
In JavaFX actions from external sources are propagated through [Events.](https://docs.oracle.com/javase/8/javafx/api/javafx/event/Event.html) 
These Events can be emitted from `Node`, `Scene`, `MenuItem`, and `Window`. FXcellent Reactor provides simple, fluent, and consistent 
factories for the creation of Fluxes from these sources. You can create Fluxes from these by using `FxFlux.from()` and 
passing the source and `EventType` to listen to. `FxFlux.from()` provides overloaded factories so that omitting the 
`EventType` will result in a `Flux` that 
 listens for `ActionEvents`.
 
 ###### Events From A Control
 ```java
 Button btn = new Button("Hey I'm A Button!");
 Flux<Event> buttonEvents = FxFlux.from(btn)
                                  .subscribeOn(FxSchedulers.platform())
                                  .publishOn(anotherScheduler);
 ```
 ###### Events From A Scene
 ```java
 Scene scene = new Scene(new Label("Hey I'm A Label!"));
 Flux<MouseEvent> mouseEvents = FxFlux.from(scene, MouseEvent.MOUSE_CLICKED)
                                  .subscribeOn(FxSchedulers.platform())
                                  .publishOn(anotherScheduler);
 ``` 
 
 ###### Events From A Window
  ```java
  Flux<WindowEvent> windowEvents = FxFlux.from(primaryStage, WindowEvent.WINDOW_HIDING)
                                   .subscribeOn(FxSchedulers.platform())
                                   .publishOn(anotherScheduler);
  ``` 

## ObservableValue
Updates of any JavaFX `ObservableValue` can be emitted onto a `Flux` by using the factory `FxFlux.from(ObservableValue<T> observableValue)` 
which creates a `Flux` that emits the initial value of the observable followed by any subsequent changes to the Observable. Often the
initial value of an `ObservableValue` is null. The reactive streams specification disallows null values in a sequence so these 
null values are not emitted.

```java
SimpleObjectProperty<String> observable = new SimpleObjectProperty<>();
Flux<String> flux = FxFlux.from(observable); 
```

Changes from `ObservableValue`s can also be emitted as a `Change` which is a pairing of the old value and the new value. 
This `Flux` can be produced from the factory `FxFlux.fromChangesOf(ObservableValue<T> observableValue)`. 
```java
SimpleObjectProperty<String> observable = new SimpleObjectProperty<>();
Flux<Change<String>> flux = FxFlux.fromChangesOf(observable)
                                  .filter(change -> "Hello".equals(change.getOldValue()))
                                  .filter(change -> "World".equals(change.getNewValue()));
```


## JavaFX Scheduler
JavaFX controls are required to be updated on the JavaFX Application Thread. `FxSchedulers.platform()` is a 
[Scheduler](https://projectreactor.io/docs/core/release/api/) that provides a way to easily Schedule tasks on the 
JavaFX Thread. Using this scheduler makes it possible to JavaFX controls using Reactive Streams.

```java
ProgressBar p1 = new ProgressBar();

Flux.interval(Duration.ofMillis(1000))
    .map(l -> l/100.0)
    .publishOn(FxSchedulers.platform())
    .subscribe(p1::setProgress);
```


## JavaFX Collections Support
FXCellent Reactor also provides fluent factories for creating a `Flux` from any of the collection introduced in JavaFX.
#### ObservableList
```java
fromList(ObservableList<T> source)
```

```java
fromListAdditions(ObservableList<T> source)
```

```java
fromListRemovals(ObservableList<T> source)
```

#### ObservableMap
```java
fromMap(ObservableMap<T,V> source)
```

```java
fromMapAdditions(ObservableMap<T,V> source)
```

```java
fromMapRemovals(ObservableMap<T,V> source)
```

#### ObservableSet
```java
fromSet(ObservableSet<T> source)
```

```java
fromSetAdditions(ObservableSet<T> source)
```

```java
fromSetRemovals(ObservableSet<T> source)
```

#### ObservableArray
```java
fromIntegerArray(ObservableIntegerArray<T> source)
```

```java
fromFloatArray(ObservableFloatArray<T> source)
```

```java
fromArrayChanges(ObservableIntegerArray<T> source)
```
```java
fromArrayChanges(ObservableFloatArray<T> source)
```

<br />

