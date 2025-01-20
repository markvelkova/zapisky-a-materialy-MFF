# C# zápisky 8. přednáška

## klíčové slovo base - doplnění
* vygenerujeme nevirtuální volání metody předka

```
class X {
    public virtual void ReactOnInsult() {
        kill all humans
    }
}

class Y : X {
    public override void ReactOnInsult() {
        base.ReactOnInsult();
        kill all dwarves
    }
}
class Z : X {
    public override void ReactOnInsult() {
        base.ReactOnInsult();
        start new robotic civilization
    }
}
```
* `Z.ReactOnInsult()` udělá všechno
* co kdybychom chtěli jen vyhladit lidi a založit civilizaci
```
class X {
    protected void KillAllHumans() {}
    public virtual void ReactOnInsult() {
        KillAllHumans()
    }
}
```
* teď to není součástí veřejného kontraktu, vidí to jen potomci

## virtuální metody v konstruktoru
```
class A {
    public A() {
        m();
    }
    public virtual void m() {
        ...
    }
}

class B : A {
    public B() {
        ...
    }
    public override void m2() {
        ...
    }
}
```
`B b = new B()` - zinicializují se věci, volá se konstruktor předka nejprve, i v něm se virtuální metoda volá virtuálně, v tomto případě v proměnné typu B se volá B.m(), potom se volá tělo konstruktoru B
* ale! fieldy inicializované v těle konstruktoru jsou ve chvíli volání B.m() ještě nepřipravené
* dávat na to pozor

## návrhový vzor factory

```
class Apple {
	public int Energy { get; init; }

	public void Eat() {
        Console.WriteLine($"Apple eaten, player got {Energy} units of energy.");
    }
}
```
```
class AppleFactory {
	private int _appleEnergy;

	public AppleFactory(int appleEnergy) {
		_appleEnergy = appleEnergy;
	}

	public Apple Create() {
		return new Apple { Energy = _appleEnergy };
	}
}
```
```
class Tree {
	private AppleFactory _factory;

	public Tree(AppleFactory factory) {
		_factory = factory;
	}

	public List<Apple> Harvest() {
		return new List<Apple> {
			_factory.Create(),
			_factory.Create(),
			_factory.Create()
		};
	}
}
```
```
static void HarvestAndEatAllApples(Tree tree) {
	var apples = tree.Harvest();
	foreach (Apple apple in apples) {
		apple.Eat();
	}
    Console.WriteLine();
}
```
```
var goodAppleFactory = new AppleFactory(10);
var goodTree = new Tree(goodAppleFactory);
HarvestAndEatAllApples(goodTree);

var badAppleFactory = new AppleFactory(2);
var badTree = new Tree(badAppleFactory);
HarvestAndEatAllApples(badTree);
```
* hezká dekompozice, strom řeší, kolik jablek, factory samotnou produkci, jablko, co se s ním dá dělat

update - chceme zlatá jablka
---
```
class Apple {
	public int Energy { get; init; }

	public virtual void Eat() {
		Console.WriteLine($"Apple eaten, player got {Energy} units of energy.");
	}
}

class GoldenApple : Apple {
	public override void Eat() {
		base.Eat();
		Console.WriteLine("Golden apple increased player's luck.");
	}
}

class AppleFactory {
	private int _applesDispensed = 0;
	private int _appleEnergy;

	public AppleFactory(int appleEnergy) {
		_appleEnergy = appleEnergy;
	}

	public Apple Create() {
		if ((_applesDispensed++) % 2 == 0) {
			return new GoldenApple { Energy = _appleEnergy };
		} else {
			return new Apple { Energy = _appleEnergy };
		}
	}
}
```
* strom se neměnil, hlavní program netřeba měnit

co kdybychom si chtěli udělat knihovnu?
---
* a.dll by obsahovala ty objekty
* b.dll program by obsahoval hlavní program, funkci na harvest

před updatem
* na úrovni CIL se .Eat() volá nevirtuálně, JIT volá přímo

po updatu
* na úrovni CIL se .Eat() volá virtuálně, JIT volá nepřímo

ale kdyby to byl nugetový balíček a nebylo by to provázané
* chceme jen spravit jednu knihovnu, za týden, vydáme novou verzi programu už s tím
* vnutíme jim novou knihovnu s virtuálním voláním
* ale .Eat() volá přeložený program vůči původní knihovně, **volá se nevirtuálně**, problém
* překladač to řeší tím, že **i pro nevirtuální generuje callvirt pro všechno**, JIT to potom spravuje jen když je potřeba
*překladač do CIL vygeneruje call jen pro explicitní volání base

## operátor "=>" 
**první varianta**
* pro přehlednost
* kratší zápis metod s krátkým tělem - jen return, jen jeden příkaz
```    
    public int getL() => 5
    public void do() => Console.WriteLine(...)
    public void do() {
        Console.WriteLine(...)
    }
```  
**druhá varianta**
* zkrácení getteru a setteru
```  
    private int _l;
    public int L {
        get => return _l
        set => _l = value
    }
```  
* pokud chci jen getter - **zákeřnost**
```  
    public int ReadOnlyL => _l  je to vlastnost, vrácení hodnoty
    vs
    public int L = _l   přiřadí se tam jen v konstruktoru, je to field
```  
    
**třetí varianta**
* lambda výrazy, ale to někdy později

---
## property vs method
property 
* něco mám
* podstatné jméno
* rychlé
* neměla by mít observable side effects (nemělo by se tam volat něco, co inkrementuje nějaké počítadlo někde nebo pošle data uživateli atd)

method
* něco potřebuju
* sloveso v rozkazovacím způsobu
* může být pomalé 

když je vlastnost bool
* sloveso (is, has, should etc) + podstatné jméno
```  
struct Vector {
    public double X {get; init;}
    public double Y {get; init;}
    1)
    public double Length => Math.Sqrt... //nejsou side effecty, ale pomalé
    2)
    private double _length;
    public double Length
    get {
        if (nemáme) _length = Math. ...
        return ?_length;
    }
    //tohle je side effect, ale není observable zvnějšku, je to implementace, je to ok
}
``` 

## odbočka - reprezentace absence hodnoty
* nullable, ale zvětšuje už tak velká floatová čísla a třeba v nich už máme připravené kombinace speciální
* místo nullable - třeba double má kombinaci double.NaN not a number ALE!!! 
* je jich hodně různých, nekontrolovat ==, ale is

## životnosti proměnných
* pokud se nepřekrývají, JIT jim může vyrobit společné místo - alokace se udělá před cyklem jen jednou, ušetří se
* může to místo pak ještě recyklovat pro další proměnné, ušetří nejen čas, ale i paměť - nemusíme se bát mít krátkou životnost proměnných -> nebojte se používát lokální proměnné v cyklech

## vlastnosti jako kontrakt interfacu k datům
```
interface I1 {
	public int X { get; set; }
	public int Y { get; }
}
```
* nejsou to automaticky implementované vlastnosti
* říká, co minimálně musí být
```
class A : I1 { 
	public int X { get; set; } //autoimplementovaná
	public int Y { get => 42; } //explicitně implementovaná
}

class B : I1 {
	public int X { get; set; }
	public int Y { get; set; } 
	//můžeme klidně přidat setter navíc, nemůžeme ho ale použít v I1 proměnné
}
```

## virtuální vlastnosti
```
class A {
	private int _x;

	public virtual int X {
		get {
			return _x;
		}
		set {
			_x = value;
		}
	}

	public virtual int Y { get; set; }

	public virtual int Z { get => 1; }
}
```
* každý getter i setter má vlastní záznam ve VMT nezávisle na sobě
```
class B : A {
	public override int X {
		get => base.X;
		set {
			if (value <= 0) {
				...
			}
			base.X = value;
		}
	}

	public override int Y {
		set {
			if (value <= 0) {
				...
			}
			base.Y = value;
		}
	}

	public override int Z { get => 2; }	// sem nemůžeme přidat setter
}
```
* pokud má virtuální vlastnost jen getter nebo setter,nemůžeme overridem to chybějící přidat

## viditelnost memberů typů
**`public`**
* vidí to všichni v daném assembly i v přilinkovaných

**`private`**
* vidí to všechno, co je součástí třídy, třeba kromě metod i vnořené třídy
* jiné top-level typy to nevidí, ať v tomhle nebo v jiném zdrojáku
* potomci s tím nemohou pracovat, ač to zdědili

**`protected`** 
* vidí to všechno ve třídě, potomci nezávisle na tom, v jaké assembly jsou
* můžeme to v těch potomcích ale volat jen na proměnných svého typu

**`internal`**
* vidí to všechno v té stejné assembly

můžu kombinovat - **`protected internal`**

> to, že něco vidím, znamená, že můžu vyrobit proměnnou, jestli s tím můžu pracovat je něco jiného, závisí to na viditelnostech metod





