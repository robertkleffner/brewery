Specification
=============

The Brewery is a combined shell and programmable, distributed, document-oriented database, focusing on *well-typed* interactions between programs and users. Every document has a type, built from a set of core base types using features such as records, variants, tuples, lists, etc. The shell is stack-based, making scripts highly composable, although programs are still limited to returning just integers and taking only byte arrays as input. The Brewery comes with two higher-order statically typed scripting languages with full, global type inference: Barley and Gruit. It also comes with built-in compilers for two statically typed languages: Ale and Lager. The bundled compilers for these languages produce programs with *type certificates*, so that programs can be verified to run only on type-safe inputs.



Predefined Types
----------------

The Brewery supports a wide variety of types for convenient representation of information.

1. Base types
  + `Byte` - signed 8 bit number
  + `UByte` - unsigned 8 bit number
  + `Short` - signed 16 bit number
  + `UShort` - unsigned 16 bit number
  + `Int` - signed 32 bit number
  + `UInt` - unsigned 32 bit number
  + `Long` - signed 64 bit number
  + `ULong` - unsigned 64 bit number
  + `Single` - 32 bit floating point number
  + `Double` - 64 bit floating point number
  + `Bool` - a True or False value
2. Primitve constructors
  + `Function` - has a sequence of input types and a sequence of output types, and a possible set of side effects
  + `Record` - an unordered collection of heterogeneous types, with each type labelled by an identifier
  + `Variant` - contains one of a set of possible heterogeneous types, with each type labelled by an identifier
  + `List` - an ordered collection of homogeneously typed elements
  + `Set` - an unordered collection of homogeneously typed elements
  + `Tuple` - an ordered collection of heterogeneously typed elements
  + `Map` - an unordered collection of homogeneously typed elements, accessed by homogeneously typed key elements
3. System constructors
  + `File` - only the metadata of a document
  + `Document` - stores some type with additional File metadata
  + `Folder` - a collection of heterogeneously typed documents and other folders, where each document and folder is labelled by an identifier
  + `Encrypted` - contains some arbitrary encrypted data; the type information of the encrypted data becomes available upon decryption
4. Possible effects
  + `!Doc` - Document IO
  + `!Folder` - Folder IO
  + `!Vid` - Video IO
  + `!Snd` - Sound IO
  + `!Net` - Network IO
  + `!Wat` - Other device IO
  + `!Proc` - Starts or terminates another process
  + `!Blob` - Casts Blob to another type
  + `!Div` - May diverge
  + `!NDet` - Nondeterministic
  + `!Pure` - Absence of all other effects, reserved for pure 'mathematical' functions
5. Predefined aliases
  + `String` - alias for `[Byte]`
  + `Rune` - alias for `Int`
  + `Blob` - alias for `[UByte]`
6. Predefined algebraic types
  + `Option` - `type Option a = Some a | None`
  + `Result` - `type Result a b = OK a | Error b`

One of the key properties of The Brewery is the ability to set protection levels based on function effect types. Do you want all programs that may do Network IO to ask for user permission before being run? There's an option for the that. Do you want to be warned when a program may diverge? There's an option for that too. In sum, for each of the four side effects that programs and scripts may have, we provide settings to warn and restrict on such behavior.



Custom Types
------------

While the primitive set of types is clearly quite flexible and capable of representing data for many use cases, there is still a need for user defined types. For these, the Brewery provides standard algebraic data types from the ML family of languages. For instance, the Meme type may be defined as:

```
type Meme = Meme [Byte] (Option String)
```

We can then create a *type alias* for documents containing just one meme.

```
alias MemeDoc = Document Meme
```



Sharing Documents: The Global Type Tap
--------------------------------------

Astute readers may think of a problem with the aforementioned scenario: it's all well and good that I can have Meme types on *my* system, but how do I share this meme with my friends, colleagues, and worst enemies? Their breweries know nothing about the meme type! Enter the **Universal Type Tap**.

When anyone wants their specific type to be universally available, they can petition to have it added to the Universal Type Tap by sending a request to the Wort Committe, which oversees the definition and structure of the types available to all individual breweries. Breweries connected to the internet will periodically sync with Universal Type Tap to keep their local taps consistent with everyone else. The Wort Committee is also required to uphold a special promise: **once a type has been added to the Universal Type Tap, it will not be modified**.

### Namespacing

Of course, one can imagine that the names for types and their constructors will run out eventually, so the types can be petitioned to be included in a *namespace*. This concept is similar in nature to the DNS: there can be a google.com and a google.net which, in theory, may have nothing similar about them (although this is a poor example). Similarly, `tumblr.Meme` would be an entirely separate type from `_4chan.pol.Meme`. Entire namespaces themselves can be registered by a single entity (need to find rules on limiting this so boosted monkeys don't just swallow everything up).

Namespaces may also be arbitrarily nested. In practice, it is encouraged to keep namespaces short and small. Extremely commonly used types may, if the Wort Committee so decides, be added to the global namespace, and so no longer have a prefix. This will be extremely rarely done. Otherwise, every type added to the Global Type Tap must be nested in at least level of namespace.

### "I'm Just a Po' Boy..."

"... and I don't have the resources nor patience to submit a whole new type to the Wort Committee! I just want to send my friend this meme, do I really have to go through all this mumbo-jumbo just for him to be able to open it?"

Thankfully, no. You can always send your friend any of the basic types, so as long as your Meme can be *serialized* into some combination of the basic types, you can send it and they can receive it, and the type checker will be happy as a clown! In fact, for sending arbitary files, it is perfectly acceptable to send `Blob`s, which are an alias for `[UByte]`, a list of unsigned bytes. This is the most general mechanism, as The Brewery provides mechanisms for casting `Blob`s into arbitrary types. However, processes that do this are marked with an effect that says as much, which could trigger warnings or restrictions your friend may have active on their Brewery.



Organization of the System
--------------------------

Storage in the Brewery is divided into three portions: applications, type registry, and documents. Scripts are documents that can be run as if they were applications. Applications are, however, compiled and versioned, and can only be installed in the central application repository.

The document repository is structured like a common file system. The type registry is namespaced as described above. The application repository is namespaced similar to the type registry.



Primitive Operators
-------------------

The Brewery comes with a rich set of built-in operators for manipulating the system. Here's a basic subset:

+ `cd` - Consumes a `String` from the stack and navigates to that path if it exists in the current folder; effectively 'changes the current folder'
+ `cp` - Copies a document
+ `mv` - Moves a document
+ `mkdir` - Creates a new folder
+ `ls` - Gets the metadata of the files and folders of the current folder
+ `pwd` - Produces the name of the current folder
+ `rm` - Delets the specified documents or folders
+ `install` - Searches remotely for and installs the program with the given name
+ `uninstall` - Uninstalls the program with the given name
+ `barley` - Runs the Barley interpreter on a Barley script
+ `gruit` - Runs the Gruit interpreter on a Gruit script
+ `ale` - Runs the ale compiler with its specific arguments
+ `lager` - Runs the lager compiler with its specific arguments
+ `eq` - Checks whether the top two elements of the stack are equal
+ `neq` - Checks whether the top two elements of the stack are not equal
+ `apps?` - Lists the currently installed applications
+ `types?` - Lists the currently registered types
+ `meta` - Gives information about a document or folder
+ `about` - Accesses the documentation for the specified program
+ `sync` - Synchronizes the local tap with the Universal Type Tap


The Query Language
------------------

a basic query language useful for bringing whole collections of documents onto the stack, where other scripts and programs can access them without needing to perform IO side effects.