# Java 9 : conf meetup
## Part 1 : in Engine 3D
moteur 3D Java.
OpenGL c’est bien, **mais c’est compliqué**.
In comes **JMonkey**, c’est plus simple.

github.com/mazerty/java-kuuban
[GitHub - jMonkeyEngine/jmonkeyengine: A complete 3D game development suite written purely in Java.](https://github.com/jMonkeyEngine/jmonkeyengine)

## Part 2 : Java 9 modules
### Introduction, contexte
* _Rémi Forax_ -> contributeur Java -> new String(_charset_); c’est lui
* JSR 292,335, 376, Amber/Valhalla (Java 10)
* Sun se plante -> Java passe open source -> nouveau bytecode dans Java 7 (_invokedynamic_ )
* introduction des lambda dans Java, enfin utilisation d’_invokedynamic_
* in comes Java 9, et les modules
	* 2 prototypes existaient, aucun fonctionne
	* problème du découpage de l’existant
		* modularisation du JDK en premier lieu
* Accélération du rythme de release Java (~6 mois)
### Pourquoi les modules?
* Pas de notion de container dans Java 8-
	* Spring/JEE ont ces notions
* classpath
	* scan linéaire des jars
	* jar hell (versions différentes de même library)
	* compile meta ≠ runtime metadata
	* outils de build utilisent système de dépendance
		* JVM devrait faire ça aussi
* Need to downscale
	* rt.jar = 66Mb (OpenJDK 8)
	* Guava = 2.3Mb
	* Spring Context = 1.1Mb
* Security
	* No encapsulation
	* if there’s a bug in part of the jdk you don’t use -> update anyway
### Pourquoi c’est long?
* Runtime : JBoss, OSGi, JEE
-> Compatibilité avec les deux aspects <-
* Compile: Maven, Gradle, Ant, Groovy
* problème des versions
	* On les enlèves

### Squelette d’un module
**foo/module-info.java**
module foo {
	requires java.base;
	requires bar;
	exports foo;
}

requires = dependencies
exports = define packages visible by other modules

### Modular jar
plain old jar + module-info.class at the root
**consistency** -> module-info.java for compiler
			-> module-info.class for _VM_
dependency graph computed by compiler/VM (acyclic graph)
-> class path _should_ not be needed anymore
	except for legacy compatibility

### Java 9 compatibility issues
Split package = 2 different jars w/ same package
-> disallowed by Java 9, 1 pack == 1 module
* Access to non exported package forbidden (e.g. com.sun.*)
	* guard against reflection (setAccessible fails)
* JDK downscale
	* useless packages : corba / transaction / javax.xml.ws
	-> will be removed later, non exported first
* illegal access allowed with —illegal-access=<flag> (_deny, permit, warn / debug_) -> removed later

### Command lines
* —add-modules
* —upgrade-module-path
* —patch-module-path
* —add-reads module=another
* —add-exports
* —add-opens

**jdeps**
* option —jdk-internal -> analyse statique des dépendances utilisées, permet d’identifier des classes qui être supprimé

### Let’s module
-> non transitive dependencies by default
* transitive require (distance of 1 only)
* static require
	* present at compile time but not necessarily at runtime

### Layout on disk
* Legacy -> 1 project = 1 module (use —source-path)
	* bad compilation perf since graph dependency is computed for each module
* multi-module -> 1 project = several modules (use —module-source-path)
	* **Javac** only for now
	* Perf improv w/ Maven/Gradle as java understand modules now

module can be versioned as metadata (propagated by Maven) but is not taken into accounts by **javac**
You can annotate modules (not instructions inside)

### Automatic modules
-> JVM emulates module from jars.
* **problem**, how to name those jars?
	* property Automatic-Module-Name in Manifest
	* if not, can use jar name without version (Maven plugin to rename it properly)
#### Run
Works either with class path (—class-path) 
or module path (—module-path, —module)
	-> VM check if dependency graph is correct (slower start time)

### Better encapsulation
* separate api from imply (≠ modules)
	* **problem**: can use imply without api
	* **solution**: can export package to a specific module (export _foo_ to _bar_)
* Avantage: meilleure visibilité de la sécurité
	* sun.misc.Unsafe : allow to bypass VM when accessing ByteBuffers
	* 2 Unsafe in Java 9
		* sun.misc.Unsafe (legacy)
			* Apple/Netflix use it to access data on heap directly
			* module _jdk.unsupported_ (**shame**)
		* jdk.internal.misc (exported to java.base only)

#### Deep reflection
Changing final fields, accessing private members and such
* now invalid, VM disallows this by default
	* in contrary to shallow reflection, e.g. accessing public methods, listing classes
	* module/packages must be marked as open to allow deep reflection 
	* automatic modules are open by default
	* framework that  uses deep reflection (IoC, Jackson, Hibernate, etc…) may open it themselves to run
### Module service
* dependency graph doesn’t allow cycles
	* drivers jdbc need those tho
	* impl -> interface, but interface has getAllDrivers() that depends on impl
	* **solution**: runtime -> service loader (SPI)
* directive provide _Interface_ with _Impl_
	* interface declares uses _Interface_ to check if any impl exists

### Going native
**JIT** vs **native**
* _jlink_
	* create custom runtime image from a set of module
		* can package jdk and your app together for instance
	* similar to fat jar, but specific to a platform
	* doesn’t work w/ automatic modules (cuz class path)
* _jaotc_ (experimental)
	* compiler ahead of time
	* create a shared lib from a module
	* non-tiered mode = slow program with low-latency
	* tiered mode = same + add profiling information for JIT

### Benchmark
App w/ 200-300 loc
#### Start
* tips => static blocks slow down your app at startup (avoid them)
* JDK9 + modules -> 209 ms
* Jlink -> 193
* Jlink + aot -> 145 (w/ lto 139…. only gen used methods in .so)
* Jlink + aot + tiered -> 190

#### size
* JDK8 -> 360Mb 
* JDK9 -> 497Mb (w/ Jlink + w/o)
* Jlink -> 67Mb
* Jlink + aot -> 475
* Jlink + lto -> 161 (+tiered 192)
* Jlink + minimal -> 40 (minimal vm, not recommended unless raspberry pi)
	* no debug tools for instance

### Goals of Jigsaw
* reliable configuration, no more class path
* downscale and re-use
* strong encapsulation
	* security yum yum
* closed world -> allow AOT opt optimization

github.com/forax/moduletools


### Q&A
* Automatic modules do not accept to export package already export by existing modules (class path is one big module for the VM)
* specification fail: if modules have same package, but only one is exported, it is not allowed by JVM (JVM doesn’t understand the module concept yet)







#meetup #dev/java