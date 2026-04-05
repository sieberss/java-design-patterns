---
shortTitle: Prototype
category: Creational
language: de
tag: 
  - Gang of Four
  - Instantiation
  - Object composition
  - Polymorphism
---

## Alternativbezeichnung 

* Klon

## Zweck

Das Prototyp-Pattern spezifiziert die Art von Objekten, die erzeugt werden sollen,
durch eine prototypische Instanz. Neue Instanzen werden durch Klonen des Objekts erzeugt.

## Detaillierte Erklärung

Reales Beispiel

> Stellen Sie sich einen Hersteller von Designmöbeln vor. Wenn eine Bestellung eintrifft, wird
> nicht von Grund auf ein ganz neues Design entwickelt. Stattdessen werden von den beliebtesten
> Designs Prototypen vorgehalten. Bei einer Bestellung zu einem bestimmten Design wird der
> zugehörige Prototyp kopiert, ggfs. mit speziellen Anpassungen gemäß Kundenwunsch.
> Dieser Ansatz spart Zeit und Ressourcen, weil die Grundkonstruktion und die Designdetails
> bereits vorhanden sind, sodass Bestellungen schnell und in gleichbleibender Qualität erledigt werden können.
> quality.
>
> Wie bei den Möbelstücken dienen auch bei Java-Objekten die Prototypen als Vorlage, um effizient neue Objekte erzeugen zu können.

In einfachen Worten

> Erzeuge ein neues Objekt durch Klonen eines existierenden.

Wikipedia sagt:

> Prototype ist ein Erzeugungsmuster in der Softwareentwicklung.
> Es wird verwendet, wenn der Typ eines zu erzeugenden Objekts durch eine prototypische Instanz vorgegeben ist,
> die dann geklont wird.

Ablaufdiagramm

![Prototype sequence diagram](./etc/prototype-sequence-diagram.png)

## Programmbeispiel

Folgende Implementation in Java wird empfohlen. 
Zunächst schreibt man eine Schnittstelle mit einer Methode zum Klonen.
In diesem Beispiel ist dies die abstrakte Klasse `Prototype` mit ihrer `copy`-Methode.

```java
public abstract class Prototype<T> implements Cloneable {
    public T copy() {
        return (T) super.clone();
    }
}
```

Das Beispiel enthält eine Hierarchie verschiedener Kreaturen. Betrachten wir exemplarisch die Klassen
`Beast` and `OrcBeast`.

```java
public abstract class Beast extends Prototype<Beast> {
  public Beast(Beast source) {}
}

public class OrcBeast extends Beast {

  private final String weapon;

  public OrcBeast(OrcBeast orcBeast) {
    super(orcBeast);
    this.weapon = orcBeast.weapon;
  }

  @Override
  public String toString() {
    return "Orcish wolf attacks with " + weapon;
  }
}
```

In dieser Erklärung beschränken wir uns auf die Grundlagen. Der komplette Beispielcode
enthält auch die
Basisklassen `Mage` und `Warlord` und es gibt spezielle Implementationen für Elfen and Orks.

Um das Prototyp-Pattern voll auszunutzen, schreiben wir das Interface `HeroFactory` und die implementierende Klasse
`HeroFactoryImpl` zur Erzeugung verschiedener Arten von Kreaturen.

```java
public interface HeroFactory {
  Mage createMage();
  Warlord createWarlord();
  Beast createBeast();
}
```

```java
public class HeroFactoryImpl implements HeroFactory {

  private final Mage mage;
  private final Warlord warlord;
  private final Beast beast;

  public Mage createMage() {
    return mage.copy();
  }

  public Warlord createWarlord() {
    return warlord.copy();
  }

  public Beast createBeast() {
    return beast.copy();
  }
}
```

Nun können wir sehen, wie mit dem Pattern neue Kreaturen durch das Klonen vorhandener Instanzen erschaffen werden.

```java
public static void main(String[] args) {
    var factory = new HeroFactoryImpl(
            new ElfMage("cooking"),
            new ElfWarlord("cleaning"),
            new ElfBeast("protecting")
    );
    var mage = factory.createMage();
    var warlord = factory.createWarlord();
    var beast = factory.createBeast();
    LOGGER.info(mage.toString());
    LOGGER.info(warlord.toString());
    LOGGER.info(beast.toString());

    factory = new HeroFactoryImpl(
            new OrcMage("axe"),
            new OrcWarlord("sword"),
            new OrcBeast("laser")
    );
    mage = factory.createMage();
    warlord = factory.createWarlord();
    beast = factory.createBeast();
    LOGGER.info(mage.toString());
    LOGGER.info(warlord.toString());
    LOGGER.info(beast.toString());
}
```

Hier die Konsolenausgabe

```
08:36:19.012 [main] INFO com.iluwatar.prototype.App -- Elven mage helps in cooking
08:36:19.013 [main] INFO com.iluwatar.prototype.App -- Elven warlord helps in cleaning
08:36:19.014 [main] INFO com.iluwatar.prototype.App -- Elven eagle helps in protecting
08:36:19.014 [main] INFO com.iluwatar.prototype.App -- Orcish mage attacks with axe
08:36:19.014 [main] INFO com.iluwatar.prototype.App -- Orcish warlord attacks with sword
08:36:19.014 [main] INFO com.iluwatar.prototype.App -- Orcish wolf attacks with laser
```

## Verwendung
* Wenn die zu instanziierenden Klassen erst zur Laufzeit festgelegt werden, etwa durch dynamisches Laden.
* Zur vermeidung einer Klassenhierarchie von Fabriken parallel zur Klassenhierarchie von Produkten.
* Wenn Instanzen einer Klasse nur wenige verschiedene Zustände haben, kann es bequemer sein, Prototypen
zu klonen, als jedesmal die Klasse mit passendem Zustand zu instanziieren.
* Wenn das Erzeugen von Objekten aufwendiger ist als Klonen.
* Wenn erst zur Laufzeit bestimmt wird, welche konkreten Klassen instanziiert werden müssen.

## Echte Java-Anwendungen
* Die `Object.clone()`-Methode is eine klassische Implementation des Prototyp-Patterns.
* GUI Libraries verwenden oft Prototypes für Buttons, Fenster, und andere Widgets.
* In der Spieleentwicklung können viele Objekte (wie Gegner-Charaktere) mit ähnlichen Attributen erzeugt werden.

## Vor- und Nachteile
Vorteile:

* Verbirgt die Komplexitäten der Objektinstanziierung
* Reduziert die Zahl der Klassen
* Erlaubt das Hinzufügen und Entfernen von Objekten zur Laufzeit

Nachteile:

* Implementation eines möglicherweise komplexen Klon-Mechanismus erforderlich
* Requires implementing a cloning mechanism which might be complex.
* Deep Cloning kann Schwierigkeiten bei der korrekten Implementation bereiten,
  insbesondere bei komplexen Objektgraphen mit zirkulären Referenzen

## Verwandte Patterns

* [Abstract Factory](https://java-design-patterns.com/patterns/abstract-factory/): Beide betreffen das Erzeugen von Objekten,
* aber Abstract Factory verwendet dafür Factory-Methoden statt Klonen.
* [Singleton](https://java-design-patterns.com/patterns/singleton/): Singleton kann Prototyp zur Objekterzeugung verwenden, wenn die einzige Instanz geklont werden darf.
* [Composite](https://java-design-patterns.com/patterns/composite/): Prototypen werden oft innerhalb von Composites verwendet, um Komponentenbäume dynamisch kreieren zu können.

## Quellen

* [Design Patterns: Elements of Reusable Object-Oriented Software](https://amzn.to/3w0pvKI)
* [Effective Java](https://amzn.to/4cGk2Jz)
* [Head First Design Patterns: Building Extensible and Maintainable Object-Oriented Software](https://amzn.to/49NGldq)
* [Java Design Patterns: A Hands-On Experience with Real-World Examples](https://amzn.to/3yhh525)
