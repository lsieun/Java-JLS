# a wildcard with a Java import statement

## con

### readable

The advantage of explicitly listing all imports is that I can tell at a glance **which class you meant to use**, which simply makes reading the code that much easier. If you're just doing a quick **one-off** thing, there's nothing explicitly wrong, but future maintainers will thank you for your clarity otherwise.

> one-off: limited to a single time, occasion, or instance

### 可能造成的问题

In short, the biggest problem is that your code can break when a class is added to a package you import. For example:

```java
import java.awt.*;
import java.util.*;

// ...

List list;
```

In Java 1.1, this was fine; List was found in java.awt and there was no conflict.

Now suppose you check in your perfectly working code, and a year later someone else brings it out to edit it, and is using Java 1.2.

Java 1.2 added an interface named List to java.util. BOOM! Conflict. The perfectly working code no longer works.

This is an EVIL language feature. There is NO reason that code should stop compiling just because a type is added to a package...

In addition, it makes it difficult for a reader to determine which "Foo" you're using.

### 3

- It helps to identify classname conflicts: two classes in different packages that have the same name. This can be masked with the * import.

- It makes dependencies explicit, so that anyone who has to read your code later knows what you meant to import and what you didn't mean to import.

- It can make some compilation faster because the compiler doesn't have to search the whole package to identify depdencies, though this is usually not a huge deal with modern compilers.

- The inconvenient aspects of explicit imports are minimized with modern IDEs. Most IDEs allow you to collapse the import section so it's not in the way, automatically populate imports when needed, and automatically identify unused imports to help clean them up.

Most places I've worked that use any significant amount of Java make explicit imports part of the coding standard. I sometimes still use * for quick prototyping and then expand the import lists (some IDEs will do this for you as well) when productizing the code.

## pro

### convenient

It is much more **convenient** and cleaner to use a single statement like

```java
import java.awt.*;
```

than to import a bunch of individual classes

```java
import java.awt.Panel;
import java.awt.Graphics;
import java.awt.Canvas;
...
```

**An import statement is intended to import a package, not a class**. It is much cleaner to import entire packages; the issues identified here (e.g. `java.sql.Date` vs `java.util.Date`) are easily remedied by other means, not really addressed by specific imports and certainly do not justify insanely pedantic imports on all classes. There is nothing more disconcerting than opening a source file and having to page through 100 import statements.


英语

- `justify`: to judge, regard, or treat as righteous 认为什么什么样是对的，我想起了这样一个场景：一个人做错了事，然后找理由为自己的不合理行为进行辩解。
- `insanely`: exhibiting a severely disordered state of mind 我想起了这样一个场景：有些人，为了自己的固有观念，当真理出现的时候，他们就像失去理智一般进行言语的攻击。
- `pedantic`: narrowly, stodgily(extremely old-fashioned), and often ostentatiously learned
- `ostentatious`: attracting or seeking to attract attention, admiration, or envy often by gaudiness or obviousness
- `gaudy`: marked by extravagance or sometimes tasteless showiness
- `extravagant`: exceeding the limits of reason or necessity 

**Doing specific imports makes refactoring more difficult**; if you remove/rename a class, you need to remove all of its specific imports. If you switch an implementation to a different class in the same package, you have to go fix the imports. While these extra steps can be automated, they are really productivity hits for no real gain.

There's really no rational justification for doing specific imports.

Here's how to deal with class conflicts:

```java
import java.sql.*;
import java.util.*;
import java.util.Date;
```

### Clean Code

It's not bad to use a wild card with a Java import statement.

In Clean Code, Robert C. Martin actually recommends using them to avoid long import lists.

Here is the recommendation:

```txt
J1: Avoid Long Import Lists by Using Wildcards

If you use two or more classes from a package, then import the whole package with

import package.*;

Long lists of imports are daunting to the reader. We don’t want to clutter up the tops of our modules with 80 lines of imports. Rather we want the imports to be a concise statement about which packages we collaborate with.

Specific imports are hard dependencies, whereas wildcard imports are not. If you specifically import a class, then that class must exist. But if you import a package with a wildcard, no particular classes need to exist. The import statement simply adds the package to the search path when hunting for names. So no true dependency is created by such imports, and they therefore serve to keep our modules less coupled.

There are times when the long list of specific imports can be useful. For example, if you are dealing with legacy code and you want to find out what classes you need to build mocks and stubs for, you can walk down the list of specific imports to find out the true qualified names of all those classes and then put the appropriate stubs in place. However, this use for specific imports is very rare. Furthermore, most modern IDEs will allow you to convert the wildcarded imports to a list of specific imports with a single command. So even in the legacy case it’s better to import wildcards.

Wildcard imports can sometimes cause name conflicts and ambiguities. Two classes with the same name, but in different packages, will need to be specifically imported, or at least specifically qualified when used. This can be a nuisance but is rare enough that using wildcard imports is still generally better than specific imports.
```

## Remark

> Although I would not be opposed to use explicit imports, I still prefer to use **star imports**. **They emphasize that the "unit of reuse" is the whole package, not its individual types**. The reasons others listed against star imports are weak, and in my experience using star imports has never caused any actual difficulties. – Rogério

这段话表述的方式是“它是这样，而不是那样”：the "unit of reuse" is the whole package, not its individual types

- There is no runtime impact, as compiler automatically replaces the * with concrete class names. If you decompile the .class file, you would never see import ...*.

- C# always uses * (implicitly) as you can only using package name. You can never specify the class name at all. Java introduces the feature after c#. (Java is so tricky in many aspects but it's beyond this topic).

- In Intellij Idea when you do "organize imports", it automatically replaces multiple imports of the same package with *. This is a mandantory feature as you can not turn it off (though you can increase the threshold).

## 对比

- **Performance**: No impact on performance as byte code is same. though it will lead to some compile overheads.

- **Compilation**: on my personal machine, Compiling a blank class without importing anything takes 100 ms but same class when import java.* takes 170 ms.
