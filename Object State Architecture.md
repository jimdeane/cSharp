# Object State Architecture
_Object state architecture_ is the "shape," "format," "internal structure" or "memory layout" of an object. It's the difference between a _stateless_ object and one with state. It's the difference between a "value" type with no internal structure other than its bits and a "structured" type with individually-addressable slots ("members" or "fields" or "instance variables.") It's the difference between an object whose slots are accessed by _index_ (e.g., an array) or one whose slots are accessed by _name_ (e.g, a struct or record.)

In essence, _object state architecture_ defines the _structure_ of an object and the way the bits of its value are interpreted. In other words, it defines both the _syntactic structure_ of the bits/bytes that represent the value of an object, and also defines the _semantics_ of the bits/bytes that have the specified _syntactic structure._

You should note that, when discussing either Essence# or platform-neutral programming concepts, that we do **not** use the term 'object' with the same semantics as are commonly used by .Net and/or the implementers of most statically-typed programming languages. We use the term in the same sense as was intended by the person who _coined_ the term _object-oriented:_ [Dr. Alan Kay](http://www.purl.org/stefan_ram/pub/doc_kay_oop_en). 

By "object," we mean a _value_ that is used to perform computations by means of sending messages to it.  In Essence#, **all** values are _objects_ in this sense, even in the case of "primitive" CLR values that those who implemented and documented the CLR label as "not objects." That's why, in the context of Essence#, even values such as **true,** **false** or "null" are in fact properly considered to be objects.

And that's why the differences between an array, the value **false** and the value **nil** ("null") is properly described as a difference of _object state architecture._

Essence# supports a finite set of different object state architectures, each of which has a unique enumeration constant in the C# code that implements Essence#, and a different well-known Symbol value when Essence# code refers to it. Generally, the only context where the object state architecture matters when writing Essence# code is when one is configuring a class.  One of the most important attributes of an Essence# class is called is _instance architecture,_ which simply means the object state architecture of the instances it instantiates (or in some cases, of the instances whose methods it defines or publishes.)

To set or change the fundamental shape or structure of the objects that a class instantiates, send it the message **#instanceArchitecture:** _objectStateArchitecture,_ where _objectStateArchitecture_ is a Symbol specifying one of the following well-known values (and also see the documentation on [classes](classes) for more details):

## General objects
Most of the classes that Essence# developers define themselves will have one of the following _instance architectures:_

* **Abstract:** A class whose instance architecture is #Abstract cannot have any instances.
* **Stateless:** The instances of a class whose instance architecture is #Stateless cannot have any state at all. For example, the class Object.
* **NamedSlots:**	The instances of a class whose instance architecture is #NamedSlots can have named instance variables ("fields" in CLR-speak.) The instance variables ("fields") are dynamically typed; they work as though they had the C# type "Dynamic." (Note that there are more specific object state architectures that can also have named instance variables. So #NamedSlots is just the most abstract or general case.)	
* **IndexedObjectSlots:** The instances of a class whose instance architecture is #IndexedObjectSlots can have any number of indexable slots--including none at all. They can also _optionally_ have named instance variables. In both cases, the slots work as though they had the C# type "Dynamic." Such objects are the Essence# equivalent of C# object arrays.
* **IndexedByteSlots:**	The instances of a class whose instance architecture is #IndexedByteSlots can have any number of indexable slots--including none at all--where each slot is physically stored as an unsigned 8-bit value. They can also _optionally_ have named instance variables. Such objects are the Essence# equivalent of C# byte arrays.
* **IndexedCharSlots:**	The instances of a class whose instance architecture is #IndexedCharSlots can have any number of indexable slots--including none at all--where each slot is physically stored as a Unicode character value. They can also _optionally_ have named instance variables. Such objects are the Essence# equivalent of C# char arrays. The object state architecture of instances of the Essence# String class is #IndexedCharSlots.
* **IndexedHalfWordSlots:**	The instances of a class whose instance architecture is #IndexedHalfWordSlots can have any number of indexable slots--including none at all--where each slot is physically stored as an unsigned 16-bit value. They can also _optionally_ have named instance variables. Such objects are the Essence# equivalent of C# ushort arrays.
* **IndexedWordSlots:**	The instances of a class whose instance architecture is #IndexedWordSlots can have any number of indexable slots--including none at all--where each slot is physically stored as an unsigned 32-bit value. They can also _optionally_ have named instance variables. Such objects are the Essence# equivalent of C# uint arrays.
* **IndexedLongWordSlots:** The instances of a class whose instance architecture is #IndexedLongWordSlots can have any number of indexable slots--including none at all--where each slot is physically stored as an unsigned 64-bit value. They can also _optionally_ have named instance variables. Such objects are the Essence# equivalent of C# ulong arrays.
* **IndexedSinglePrecisionSlots:**	The instances of a class whose instance architecture is #IndexedLongWordSlots can have any number of indexable slots--including none at all--where each slot is physically stored as 32-bit IEEE floating point value. They can also _optionally_ have named instance variables. Such objects are the Essence# equivalent of C# float arrays.
* **IndexedDoublePrecisionSlots:**	 The instances of a class whose instance architecture is #IndexedLongWordSlots can have any number of indexable slots--including none at all--where each slot is physically stored as 64-bit IEEE floating point value. They can also _optionally_ have named instance variables. Such objects are the Essence# equivalent of C# double arrays.
* **IndexedQuadPrecisionSlots:**	The instances of a class whose instance architecture is #IndexedLongWordSlots can have any number of indexable slots--including none at all--where each slot is physically stored as 128-bit floating point value, using a CLR-specific format. They can also _optionally_ have named instance variables. Such objects are the Essence# equivalent of C# decimal arrays.
							
## System objects
The _object state architectures_ in this section specify objects where there is some necessary dependency of the Essence# runtime system on the internal format, shape or structure of the objects. Although some of them allow ad-hoc named instance variables to be defined, some do not. In any case, other than for any optional named instance variables, the internal fields or other structure of the objects is private to the Essence# runtime system, and for that reason is not directly accessible to code written in Essence#.

Except for the special case of #HostSystemObject, there is by default only one Essence# class whose instance architecture corresponds to any of the following object state architectures. But that's only the default situation.  Having multiple, different classes with any of the following instance architectures is perfectly legal:

* **Symbol:** An Essence# _Symbol_ is an immutable character string, such that there is only ever one instance with the same characters; symbol instances also have some system reflective/introspective behaviors and usages. Instances may optionally have programmer-accessible named instance variables.
* **Message:** A Message instance specifies a message that was or could be sent, along with the message arguments, if any. Instances are created by the run time system when and as needed, although application code may also create and use instances.  Message instances cannot have programmer-accessible named instance variables.
* **MessageSend:** A MessageSend serves as a polymorphic inline cache that is directly accessible in Essence# code. It is especially useful in situations where the message to be sent cannot be known at the time the code that uses it is compiled.
* **Association:**	An Association is conceptually the same thing as a CLR KeyValuePair. Associations cannot have programmer-accessible named instance variables.
* **BindingReference:**	A BindingReference is a specialized type of of Association used by Namespaces. BindingReferences cannot have programmer-accessible named instance variables.
* **IdentityDictionary:**	IdentityDictionary instances act as "dictionaries" that map keys to values. An IdentityDictionary compares keys using object identity. Instances may optionally have programmer-accessible named instance variables.
* **Dictionary:**	Dictionary instances act as "dictionaries" that map keys to values. A Dictionary compares keys based on the logical or conceptual value of the keys. Instances may optionally have programmer-accessible named instance variables.
* **Namespace:**	Namespace instances serve as dynamic namespaces at runtime (See the documentation on [namespaces](namespaces) for a far more detailed description]).  Instances may optionally have programmer-accessible named instance variables.
* **Pathname:**	A Pathname instance serves as a hierarchical key whose elements are Strings. It's used for identifying namespaces, file pathnames, URLs, etc.  Instances may optionally have programmer-accessible named instance variables.
* **Block:**	A block is an anonymous function with full closure semantics. The implementation uses CLR delegates. Blocks cannot have programmer-accessible named instance variables.
* **Method:** A method is executable code that runs in the context of a specific class, with full access to the internal state of the distinguished object that receives the message that invokes the method. Methods cannot have programmer-accessible named instance variables.
* **Behavior:** A Behavior is a proto-class. There can actually be instances--it's not abstract. Instances may optionally have programmer-accessible named instance variables. (See the documentation on [classes](classes) for a more detailed description]).
* **Class:**	A Class is a full Essence# class which is a subclass of Behavior, is an instance of a Metaclass, and whose instances (if it's allowed to have any) can be an object (value) of any type. The term 'class' is usually intended to refer to an (indirect) instance of the class Class, but technically can refer to any Object that can create instances of itself, such as a Behavior or a Metclass (i.e., any instance of Behavior or anything that inherits from Behavior.) Instances may optionally have programmer-accessible named instance variables.  (See the documentation on [classes](classes) for a more detailed description]).
* **Metaclass:**	A Metaclass is an Essence# class which is a direct subclass of the (Essence#) class Behavior. A Metaclass is an instance of the class Metaclass, and its instances must be Classes. A Metaclass can have only one instance which is called either the _canonical instance_ or the _sole instance._ Note that the superclass of the Metaclass of any root Behavior (e.g., the metaclass of class Object) is (and must be) the class Class. Instances may optionally have programmer-accessible named instance variables.  (See the documentation on [classes](classes) for a more detailed description]).
* **BehavioralTrait:** A _Trait_ is a composable unit of behavior. [Traits](Traits) can be "used" by a class or by another _Trait_ with the effect of adding the methods defined (or used) by the _Trait_ to the method dictionary of the using class or of the using _Trait_.  A BehavioralTrait is a _Trait_ usable by any BehavioralTrait or by any Behavior (i.e., by any instance of the class BehavioralTrait, or by any instance of the class Behavior, or by any instance of any subclass of either the class BehavioralTrait or of the class Behavior.)
* **InstanceTrait:** An InstanceTrait is a _Trait_ usable by any InstanceTrait or by any Class (i.e, by any instance of the class ClassTrait. A ClassTrait is a _Trait_ usable by any ClassTrait or by any Metaclass (i.e., by any instance of the class ClassTrait or by any instance of  the class Metaclass.)
* **TraitTransformation:** A TraitTransformation acts a decorator of a _Trait_. It is used to exclude or rename one or more method selectors of the _Trait_ it decorates. TraitTransformations are defined by algebraic _Trait_ expressions at run time.
* **TraitComposition:** A TraitComposition combines one or more [Traits](Traits)(Traits) into a new _Trait_ that is the symmetric set difference of the combined [Traits](Traits)(Traits). TraitCompositions are defined by algebraic _Trait_ expressions at run time.
* **HostSystemObject:** A "host system object" is simply an instance of any CLR type which is not a formal part of the Essence# runtime system. One of the requirements for an Essence# class to _represent_ a CLR type (which may or may not be a "class" as the CLR defines that term) is that its instance type must be #HostSystemObject.
		
## Values adopted as-is from the CLR
In the case of certain"primitive" value types, Essence# adopts the native CLR types directly as the object state architecture of its objects.  That's true for null, false, true, characters, integer values and floating point values. Since the values of these CLR types aren't CLR "objects" (as the CLR defines that term,) it's impossible to "create instances" of them, not even in C#. Nevertheless, each such primitive CLR type is _represented_ by an Essence# class, which will have one of the following values as its instance architecture:
		
* **Nil:** A class whose instance architecture is #Nil governs the behavior (in Essence# code) of the value "null," which in Essence# syntax is written as **nil.** Nil (or "null") is technically a sentinel or metavalue whose meaning is "there is no value being referenced."
* **False:** A class whose instance architecture is #False governs the behavior (in Essence# code) of the value **false.**
* **True:** A class whose instance architecture is #True governs the behavior (in Essence# code) of the value **true.**
* **Char:** A class whose instance architecture is #Char governs the behavior (in Essence# code) of values of CLR type char (Unicode character values.)
* **SmallInteger:**	A class whose instance architecture is #SmallInteger governs the behavior (in Essence# code) of all integer values that aren't BigNums. The Essence# compiler always uses Int64 values for integer literals.
* **SinglePrecision:** A class whose instance architecture is #SinglePrecision governs the behavior (in Essence# code) of all IEEE 32-bit floating point values. 
* **DoublePrecision:** A class whose instance architecture is #DoublePrecision governs the behavior (in Essence# code) of all IEEE 64-bit floating point values. 
* **QuadPrecision:** A class whose instance architecture is #QuadPrecision governs the behavior (in Essence# code) of all CLR values of type Decimal (128-bit floating point values that use a proprietary format.) 
				
## Not yet implemented
Currently, there are no Essence# classes that implement the following object state architectures, nor any specific support in the runtime system to enable such implementation:

* **LargeInteger:** Support for large integers is currently in the development plan.  When implemented, there will be an Essence# class that can create and govern the behavior of BigNum values. Also, Int64 and UInt64 arithmetic operations will transparently overflow into BigNum values.
* **ScaledDecimal:** When implemented, the ScaledDecimal class will provide unlimited-precision rational numbers with a fixed decimal point.
_
The essence of OOP: It's all messages, all the time._
