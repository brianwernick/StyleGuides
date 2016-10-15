# Android/Kotlin style guidelines
###### Version 1.0.0

## 1 Project structure
The project should follow standard [Android project guidelines](https://developer.android.com/studio/projects/index.html)
however in short:
```
root/
  └─ app/
  └─ gradle/
  └─ lib/
  └─ miscFiles.txt
```

## 2 Package structure

### 2.1 Applications
The root structure should follow major source categories:
```
app/
  └─ data/
  └─ injection/
  └─ receiver/
  └─ service/
  └─ ui/
```

The `ui` category needs to be more granular based on the section and follow MVP organization.
This means a login screen will have its own package with `contract` and `platform` sub packages.
```
ui/
 └─ login/
    └─ contract/
    └─ platform/
```

Each category has sub structures to increase the granularity. e.g.
```
data/
  └─ webservice/
  └─ database/
```

### 2.2 Libraries
Similar to the Applications structure; the root structure should follow major source categories:
```
lib/
  └─ api/
  └─ data/
  └─ injection/
  └─ receiver/
  └─ service/
  └─ util/
```

Each category has sub structures to increase the granularity. e.g.
```
api/
  └─ account/
  └─ devices/
```

## 3 File naming

### 3.1 Class files
Class names should be written in [UpperCamelCase](http://en.wikipedia.org/wiki/CamelCase).

For classes that extend an Android component, the name of the class should end with the name of the component; for example:
* `SignInActivity`
* `SignInFragment`
* `ImageUploaderService`
* `ChangePasswordDialog`

### 3.1 Resources files

Resources file names are written in __lowercase_underscore__. e.g.
* `ic_launcher`
* `background_default`

#### 3.1.1 Drawables

##### 3.1.1.1 Icons
Icons should follow the naming scheme seen in [Material Icons](https://github.com/google/material-design-icons/) which
follows the format `ic_<Name>_<Color>_<Size>`; the exception being launcher images which should be named `ic_launcher`.
For example:

* `ic_link_black_24dp.xml`
* `ic_user_white_48dp.xml`


##### 3.1.1.2 Selector States
Assuming a selector state requires an image instead of an embedded shape, each images should be
named based on the main selector (`button_order` in the example) with the states as a suffix. e.g.

| State	       | Suffix       | Example                     |
|--------------|--------------|-----------------------------|
| Normal       | `_normal`    | `button_order_normal.9.png`    |
| Pressed      | `_pressed`   | `button_order_pressed.9.png`   |
| Focused      | `_focused`   | `button_order_focused.9.png`   |
| Disabled     | `_disabled`  | `button_order_disabled.9.png`  |
| Selected     | `_selected`  | `button_order_selected.9.png`  |


#### 3.1.2 Layout files
Layout file names should be prefixed with the section the source code will be used by _without_ prefixing
the Android component name. e.g.

| Component        | Class Name             | Layout Name           |
| ---------------- | ---------------------- | --------------------- |
| Activity         | `LoginActivity`        | `login.xml`           |
| Fragment         | `SignUpFragment`       | `sign_up.xml`         |
| Dialog           | `ChangePasswordDialog` | `change_password.xml` |
| AdapterView item | `PersonViewHolder`     | `person.xml`          |


#### 3.1.3 Menu files
Menu file names should match the parent layout naming, they should _not_ include `menu` as they are already
separated by the `R.menu.*` reference. e.g.

| Parent Layout      | Menu Name      |
| ------------------ | -------------- |
| `user_profile.xml` | `user_profile` |
| `person.xml`       | `person`       |


#### 3.1.4 Values files
Resource files in the values folder should be __plural__; i.e.:
* `strings.xml`
* `styles.xml`
* `colors.xml`
* `dimens.xml`
* `attrs.xml`


# 4 Code guidelines

## 4.1 XML style rules

### 4.1.1 Prefer self closing tags
When possible self closing tags should be used. e.g.

```xml
<TextView
	android:id="@+id/profileTextView"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" />
```

**Not**
```xml
<TextView
    android:id="@+id/profileTextView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
</TextView>
```

### 4.1.2 Resource naming
Resources should follow __lowercase_underscore__ naming, the exception being Ids, Styles and Themes (described below)

#### 4.1.2.1 IDs
IDs should have a suffix indicating the element or category type in  [LowerCamelCase](http://en.wikipedia.org/wiki/CamelCase). e.g.

| Element              | Suffix              |
| -------------------- | ------------------- |
| `TextView`           | `someTextView`      |
| `ImageView`          | `anImageView`       |
| `Button`             | `helpButton`        |   
| `Menu Item`          | `loginMenuItem`     |
| `Action Mode Menu`   | `artistActionMenu`  |
| `Context Menu`       | `personContextMenu` |


#### 4.1.2.2 Strings
String names start with a prefix that identifies the section they belong to. For example `registration_email_hint`
 or `registration_name_hint`. If a string __doesn't belong__ to any section then a simple name should be used. e.g.

| Name                    | Value                                   |
| ----------------------- | --------------------------------------- |
| `done`                  | `Done`                                  |
| `general_network_error` | `There was an unknown network error`    |


#### 4.1.2.3 Styles and Themes
Unlike the rest of resources, style names are written in [UpperCamelCase](http://en.wikipedia.org/wiki/CamelCase).

### 4.1.3 Attribute ordering
Attributes should follow the auto ordering that Android Studio enforces; in general this is

1. `id`
2. `style` and `theme`
3. `layout_width`, `layout_height`, and `layout_weight`
4. Other attributes sorted alphabetically
5. Non-`android` namespace items (e.g. `app`) sorted alphabetically
6. `tools` namespace items sorted alphabetically


## 4.2 Kotlin language rules

### 4.2.1 Always handle exceptions
While you may think that your code will never encounter this error condition or that it is not important to handle,
ignoring exceptions creates mines in your code for someone to eventually trip over. Every exception should be handled
by one of the following methods:

* Add an explicit `throws` to the method definition (e.g. `fun someErrorProneFunction() throws IOException`)
* Catch the exception and cancel future functionality (silently failures should be avoided)
* Catch the exception and use a default value instead

You should **Never** throw [Unchecked Exceptions](https://docs.oracle.com/javase/tutorial/essential/exceptions/runtime.html)


### 4.2.2 Catch generic Exceptions sparingly
In many cases there are good ways to handle specific issues (e.g. a `FileNotFoundException` should be caught to inform
the user that the file doesn't exist). If this is caught by a generic Exception then there is no good way to inform the
user. However there are some cases that we only care if the process fails, not the reason it failed; in these cases it
is alright to catch generic Exceptions.

For Example:

```kotlin
try {
    someComplicatedIOFunction()        // may throw IOException
    someComplicatedParsingFunction()   // may throw ParsingException
    someComplicatedSecurityFunction()  // may throw SecurityException
} catch (exception: Exception) {       // I'll just catch all exceptions (generic)
    handleError()                      // with one generic handler!
}
```

See the reason why and some alternatives [here](https://source.android.com/source/code-style.html#dont-catch-generic-exception)

### 4.2.3 Don't use finalizers
_We don't use finalizers. There are no guarantees as to when a finalizer will be called, or even that it will be called at all.
In most cases, you can do what you need from a finalizer with good exception handling. If you absolutely need it, define a
`close()` method (or the like) and document exactly when that method needs to be called. See `InputStream` for an example.
In this case it is appropriate but not required to print a short log message from the finalizer, as long as it is not expected
to flood the logs._ - ([Android code style guidelines](https://source.android.com/source/code-style.html#dont-use-finalizers))


### 4.2.4 Fully qualify imports
While excessive methods and imports can be stripped out with proguard, it is good practice to minimize the amount of
code included.  More information can be found [here](https://source.android.com/source/code-style.html#fully-qualify-imports)

For Example:  
an import should read `import foo.Bar` instead of `import foo.*`


## 4.3 Kotlin style rules

### 4.3.1 Fields definition and naming
Fields should be defined at the __top of the file__ and they should follow the naming rules listed below.

* private constants `private const val` are all caps (e.x. `MY_CONST`) and located in the [companion object](https://kotlinlang.org/docs/reference/object-declarations.html#companion-objects)
* public constants `public const val` are all caps (e.x. `MY_CONST`) and located in the [companion object](https://kotlinlang.org/docs/reference/object-declarations.html#companion-objects)
* all other fields should follow [LowerCamelCase](http://en.wikipedia.org/wiki/CamelCase) conventions

Example:

```kotlin
public class MyClass {
    companion object {
        private const val FOO = 1
        public const val SOME_CONSTANT = 42
    }

    private var privateVar: Int
    protected var protectedVar: Int
    public var publicVar: Int
}
```

### 4.3.3 Treat acronyms as words
| Good             | Bad              |
| ---------------- | ---------------- |
| `XmlHttpRequest` | `XMLHTTPRequest` |
| `getCustomerId`  | `getCustomerID`  |
| `String url`     | `String URL`     |
| `long id`        | `long ID`        |


### 4.3.4 Use spaces for indentation
Use __4 space__ indentations for blocks:

```kotlin
if (i == 1) {
    i++;
}
```

Use __8 space__ indentations for line wraps:
```kotlin
val instrument =
        someLongExpression(that, wouldNotFit, on, one, line)
```

### 4.3.5 Use standard brace style
Braces go on the same line as the code before them and are always required, except in the case of replacing
the ternary operator. e.g.

```kotlin
class MyClass {
    fun test() {
        if (something) {
            // ...
        } else if (somethingElse) {
            // ...
        } else {
            // ...
        }
    }

    //An example of an if...else replacement for a ternary
    fun ternaryFunction(someValue: String): Int {
        return if (someValue.isEmpty()) 0 else 1
    }
}
```

### 4.3.6 Limit variable scope
_The scope of local variables should be kept to a minimum (Effective Java Item 29). By doing so, you increase the readability
 and maintainability of your code and reduce the likelihood of error. Each variable should be declared in the innermost block
 that encloses all uses of the variable._

_Local variables should be declared at the point they are first used. Nearly every local variable declaration should contain an
initializer. If you don't yet have enough information to initialize a variable sensibly, you should postpone the declaration
until you do._ - ([Android code style guidelines](https://source.android.com/source/code-style.html#limit-variable-scope))


### 4.3.7 Logging guidelines
For simplicity [Timber](https://github.com/JakeWharton/timber) should be used for logging in place of the default
one provided by Android.

### 4.3.8 Class member ordering
There is no single correct solution for this but using a __logical__ and __consistent__ order will improve code
readability. It is recommendable to use the following order:

1. Constants
2. Fields
    1. Constants
    2. `@Inject`
    4. `@BindExtra`
    5. `@BindArgument`
    6. `@SaveState`
    7. Member Variables
3. Constructors
4. Override methods and callbacks (public or private)
5. Public methods
6. Private methods
7. Inner classes
8. Nested classes or interfaces

Example:

```kotlin
public class MainActivity: Activity {
    companion object {
        private const val ARG_FOO = "TAG"
    }

    @Inject //Dagger
    Prefs prefs

    @BindExtra //PocketKnife
    var userId: String?

    @SaveState //PocketKnife
    var currentPosition: Int = 0

    private var title: String?

    public fun setTitle(title: String?) {
    	this.title = title;
    }

    override public fun onCreate() {
        //...
    }

    private fun setUpView() {
        //...
    }

    //the `inner` keyword indicates the class is a child of `MainActivity`
    private inner class AnInnerClass {
        //...
    }

    //nested classes are static by default in kotlin
    class ANestedClass {
        //...
    }
}
```

If your class is extending and __Android component__ such as an Activity or a Fragment, it is a good practise to order the
override methods so that they __match the component's lifecycle__. For example, if you have an Activity that implements
`onCreate()`, `onDestroy()`, `onPause()` and `onResume()`, then the correct order is:

```kotlin
public class MainActivity: Activity {
    override public fun onCreate() {}

    override public fun onResume() {}

    override public fun onPause() {}

    override public fun onDestroy() {}
}
```

### 4.3.9 Parameter ordering in methods
When programming for Android, it is quite common to define methods that take a `Context`. If you are writing a method
like this, then the __Context__ must be the __first__ parameter.

The opposite case are __callback__ interfaces that should always be the __last__ parameter.

Examples:

```kotlin
// Context always go first
public fun loadUser(context: Context, userId: Int): User?

// Callbacks always go last
public fun loadUserAsync(context: Context, userId: Int, callback: UserCallback)
```

### 4.3.10 String constants, naming and values
Many elements of the Android SDK such as `SharedPreferences`, `Bundle` or `Intent` use a key-value pair approach so
it's very likely that even for a small app you end up having to write a lot of String constants.

When using one of these components, you __must__ define the keys as a `static final` fields and they should be prefixed as indicated below.

| Element            | Prefix            |
| -----------------  | ----------------- |
| SharedPreferences  | `PREF_`           |
| Bundle             | `BUNDLE_`         |
| Fragment Arguments | `ARGUMENT_`       |   
| Intent Extra       | `EXTRA_`          |
| Intent Action      | `ACTION_`         |

Note that the arguments of a Fragment - `Fragment.getArguments()` - are also a Bundle. However, because this is a quite common
use of Bundles, we define a different prefix for them.

Example:

```kotlin
companion object {
    // Note the value of the field is the same as the name to avoid duplication issues
    public const val PREF_EMAIL = "PREF_EMAIL"
    public const val BUNDLE_AGE = "BUNDLE_AGE"
    public const val ARGUMENT_USER_ID = "ARGUMENT_USER_ID"

    // Intent-related items use full package name as value
    public const val EXTRA_SURNAME = "com.myapp.extras.EXTRA_SURNAME"
    public const val ACTION_OPEN_USER = "com.myapp.action.ACTION_OPEN_USER"
}
```

### 4.3.11 Arguments in Fragments and Activities
When data is passed into an `Activity `or `Fragment` via `Intents` or a `Bundles`, the keys for the different values
 __must__ follow the rules described in the section above.

When an `Activity` or `Fragment` expect arguments, it should provide a `static public` method that facilitates the
creation of the `Fragment` or `Intent`.

In the case of Activities the method is usually called `getStartIntent()`

```kotlin
companion object {
    public fun getLocation(context: Context, user: User): Location {
    	val location = Location(user) //performs some work
    	return location
    }
}
```

For Fragments it's named `newInstance()` and it handles the creation of the Fragment with the right arguments.

```kotlin
companion object {
    public fun newInstance(user: User): UserFragment {
    	val fragment = UserFragment()

    	val args = Bundle()
    	args.putParcelable(ARGUMENT_USER, user)
    	fragment.setArguments(args)

    	return fragment
    }
}

```

### 4.3.12 Line length limit
Code lines should not exceed __160 characters__. If the line is longer than this limit there are usually two options to reduce its length:

* Extract a local variable or method (Preferable).
* Apply line-wrapping to divide a single line into multiple ones.

There are two __exceptions__ where is possible to have lines longer than 160:

* Lines that are not possible to split, e.g. long URLs in comments.
* `package` and `import` statements.


#### 4.3.12.1 Line-wrapping strategies
There isn't an exact formula that explains how to line-wrap and quite often different solutions are valid. However
there are a few rules that can be applied to common cases.

__Method chain case__

When multiple methods are chained in the same line - for example when using Builders - every call to a method should
go in its own line, breaking the line before the `.`

```kotlin
Picasso.with(context).load("http://ribot.co.uk/images/sexyjoe.jpg").into(imageView)
```

```kotlin
Picasso.with(context).apply {
    load("http://ribot.co.uk/images/sexyjoe.jpg")
    into(imageView)
}
```

__Long parameters case__

When a method has many parameters or its parameters are very long we should break the line after every comma `,`

```kotlin
loadPicture(context, "http://ribot.co.uk/images/sexyjoe.jpg", imageViewProfilePicture, clickListener, "Title of the picture");
```

```kotlin
loadPicture(context,
        "http://ribot.co.uk/images/sexyjoe.jpg",
        imageViewProfilePicture,
        clickListener,
        "Title of the picture");
```
