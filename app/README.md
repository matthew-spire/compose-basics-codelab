# Jetpack Compose Basics

## Before You Begin
- Jetpack Compose
    - Modern toolkit designed to simplify UI development
    - Combines a reactive programming model w/ the conciseness and ease of use of the Kotlin programming language
    - Fully declarative &rarr; Describe your UI by calling a series of functions that transform data into a UI hierarchy -> When the underlying data changes, the framework automatically re-executes these functions, updating the UI hierarchy for you
- Composable App
    - Made up of composable functions (often referred to as composables), i.e., just regular functions annotated with `@Composable`, which can call other functions
    - A function is all you need to create a new UI component
    - The annotation `@Composable` tells Compose to add special support to the function for updating and maintaining the UI over time
    - Compose lets you structure your code into small chunks &rarr; Easy to build up a library of UI elements used in your app, where each UI element is responsible for one part of the screen and can be edited independently

### What You Will Do
- Learn
    - What Compose is
    - How to build UIs with Compose
    - How to manage state in composable functions
    - How to create a performant list
    - How to add animations
    - How to style and them an app
- Build an app with an onboarding screen and a list of animated expanding items

## Starting a New Compose Project
- Understand how to create a new Compose project in Android Studio
- Use the "Empty Activity" w/ the Compose logo under "Phone and Tablet"

## Getting Started with Compose
- Understand the different classes and methods related to Compose that Android Studio has generated for you

### Composable Functions
- Composable function
    - Regular Kotlin function annotated with `@Composable` &rarr; Enables your function to call other `@Composable` functions within it
    - Will produce a piece of UI hierarchy
    - Some we define, others (e.g., `Text`) are provided by the library

### Compose in an Android App
- `MainActivity`
    - With Compose, an `Activity` remains the entry point to an Android app &rarr; `MainActivity` is launched when the user opens the app (as it is specified in the `AndroidManifest.xml` file)
    - Use `setContent` to define your layout, but instead of using XML (traditional View system), you call Composable functions within it
- `Theme`
    - Way to style Composable functions
- Android Studio Preview
    - Use by annotating any parameterless Composable function or functions with `@Preview` and build the project
- Classes related to Jetpack Compose
    - `androidx.compose.*` &rarr; Compiler and runtime classes
    - `androidx.compose.ui.*` &rarr; UI toolkit and libraries

## Tweaking the UI
- `Greeting` Background Color
    - Wrap the `Text` composable with a `Surface`
    - `Surface` takes a color, so use `MaterialTheme.colorScheme.primary` &rarr; `Surface` and `MaterialTheme` are concepts related to Material Design, which is a design system created by Google to help you create UIs and experiences
    - Components nested inside `Surface` will be drawn on top of that background color
- With the latest version of Android Studio, Flamingo, it no longer seems necessary to `Build & Refresh` to update the preview
- Note how the text color has changed
    - Done by the Material components &rarr; Built to make the experience better by taking care of common features that you probably want in the app (e.g., choosing an appropriate color for text)
    - Material is "opinionated" because it provides good defaults and patterns that are common to most apps
    - Material components in Compose are built on top of other foundational components (in `androidx.compose.foundation`) &rarr; Also accessible from your app components in case you need more flexibility
    - `Surface` understands that, when the background is set to the `primary` color, any text on top of it should use the `onPrimary` color, which is also defined in the theme

### Modifiers
- Most Compose UI elements (e.g., `Surface`, `Text`, etc.) accept an optional `modifier` parameter
- Modifiers tell a UI element how to lay out, display, or behave within its parent layout
- Example
    - The `padding` modifier will apply an amount of space around the element it decorates
    - Create a padding modifier with `Modifier.padding()`
    - Add padding to the `Text`
- There are dozens of modifiers which can be used to align, animate, lay out, make clickable or scrollable, transform, etc.
- List of Compose Modifiers: https://developer.android.com/jetpack/compose/modifiers-list?authuser=1

## Reusing Composables
- The more components you add to the UI, the more levels of nesting you create &rarr; Can affect readability if a function becomes really large
- By making small reusable components it is easy to build up a library of UI elements used in an app &rarr; Each one is responsible for one small part of the screen and can be edited independently
- Best practice (Modifier)
    - Your function should include a Modifier parameter that is assigned an empty Modifier by default
    - Forward this modifier to the first composable you call inside your function &rarr; The calling site can adapt layout instructions and behaviors from outside of your composable function
- Create a Composable called `MyApp` that includes the greeting
    - Lets us clean up the `onCreate` callback and the preview as we can now reuse the `MyApp` composable, avoiding code duplication

## Creating Columns and Rows
- The three basic standard layout elements in Compose are Column, Row, and Box &rarr; These are Composable functions that take Composable content, so you can place items inside
    - E.g., each child inside of a `Column` will be placed vertically
- Exercise
    - Try to change `Greeting` so that it shows a column with two text elements
    - Note that you might have to move the padding around

### Compose and Kotlin
- Composable functions can be used like any other function in Kotlin &rarr; Makes building UIs really powerful since you can add statements to influence how the UI will be displayed
    - E.g., you can use a `for` loop to add elements to the `Column`
- Without setting dimensions or adding any constraints to the size of the composables, each row takes the minimum space it can and the preview does the same thing
- Change the preview to emulate a common width of a small phone, 320dp, by adding the `widthDp` parameter to the `@Preview` annotation
- Modifiers are used extensively in Compose, so practice by making the columns fill the preview screen with a little padding between the edges and each other
    - Modifiers can have overloads so, for example, you can specify different ways to create a padding
    - To add multiple modifiers to an element, you simply chain them
    - Multiple ways to achieve the result

### Adding a Button
- Add a clickable element that expands the `Greeting` &rarr; Need to add the button first
- `Button` is a composable provided by the material3 package which takes a composable as the last argument
- Since trailing lambdas can be moved outside of the parentheses, you can add any content to the button as a child (e.g., a Text)
- Learn how to place a composable at the end of a row
    - No `alignEnd` modifier so, instead, you give some `weight` to the composable at the start &rarr; The `weight` modifier makes the element fill all available space making it "flexible", effectively pushing away the other elements that do not have a weight, which are called "inflexible"
        - `fillMaxWidth` modifier becomes redundant
    - Struggled here, so need to review

## State in Compose
- Other items to take care of before getting into how to make a button clickable and how to resize an item
- Need to store some value somewhere that indicates whether each item is expanded or not, i.e., the state of the item
- Note:
    - Compose apps transform data into UI by calling composable functions
    - If the data changes, Compose reexecutes these functions with the new data, creating an updated UI &rarr; Recomposition
    - Compose also looks at what data is needed by an individual composable so that it only needs to recompose components whose data has changed and skip recomposing those that are not affected
    - DO NOT RELY ON THE ORDERING OF THE CODE &rarr; Composable functions can execute frequently and in any order
- To add internal state to a composable, you can use the `mutableStateOf` function, which makes Compose recompose functions that read that `State`
    - `State` and `MutableState` are interfaces that hold some value and trigger UI updates (recompositions) whenever that value changes
    - Use `remember` to remember the mutable state and preserve state across recompositions &rarr; `remember` is used to **guard** against recomposition, so the state is not reset
    - Note: If you call the same composable from different parts of the screen you will create different UI elements, each with its own version of the state &rarr; **Think of internal state as a private variable in a class**
    - The composable function will automatically be "subscribed" to the state &rarr; If the state changes, composables that read these fields will be recomposed to display the updates

### Mutating State and Reacting to State Changes
- `Button` has a parameter called `onClick` but it does not take a value, it takes a function &rarr; Use to change the state
- You can define the action to take on click by assigning a lambda expression to it
- Example
    - Toggle the value of the expanded state and show a different text depending on the value
- Expand an item when requested
    - Add an additional variable that depends on the state &rarr; In this case you do not need to remember `extraPadding` against recomposition because it is doing a simple calculation
    - Add a new padding modifier to the column so that the padding is added to the bottom when "Show more" is clicked

## State Hoisting
- In Composable functions, state that is read or modified by multiple functions should live in a common ancestor &rarr; **state hoisting**
- Making state hoistable avoids duplicating state and introducing bugs, helps reuse composables, and makes composables substantially easier to test
- On the flip side, state that does not need to be controlled by a composable's parent should not be hoisted
- The **source of truth** belongs to whoever creates and controls that state
- Create an onboarding screen for the app
    - New composable called `OnboardingScreen` and also a new **preview**
        - Note that you can have multiple previews at the same time
    - `Column` can be configured to display its contents in the center of the screen (does not seem to be working)
    - `shouldShowOnboarding` is using a `by` keyword istead of the `=` &rarr; This is a property delegate that saves you from typing `.value` every time
    - When the button is clicked, `shouldShowOnboarding` is set to false, however you are not reading the state from anywhere yet
- Add the new onboarding screen to our app
    - Show it on launch and then hide it when the user presses "Continue"
        - In Compose **you do not hide UI elements** &rarr; Simply do not add them to the composition, so they are not added to the UI tree that Compose generates &rarr; Done with simple Kotlin logic
    - Need to share the state that we created in `OnboardingScreen` with the `MyApp` composable &rarr; Insteat of somehow sharing the value of the state with its parent, we **hoist** the state and move it to the common ancestor that needs access to it
    - Move the content of `MyApp` into a new composable called `Greetings` and adapt the preview to call the `Greetings` method instead
    - Add a preview for the new top level MyApp composable to test its behavior
    - Add the logic to show the different screens in `MyApp` and **hoist** the state
    - Also need to share `shouldShowOnboarding` w/ the onboarding screen but we are not going to pass it directly &rarr; Instead of letting `OnboardingScreen` mutate our state, it would be better to let it notify us when the user clicked on the "Continue" button
        - Pass events up by **passing callbacks down** &rarr; Callbacks are functions that are passed as arguments to other functions and get executed when the event occurs
        - Add a function parameter to the onboarding screen defined as `onContinueClicked: () -> Unit` so you can mutate the state from `MyApp`
        - By passing a function and not a state to `OnboardingScreen` we are making this composable more reusable and protecting the state from being mutated by other composables &rarr; Keeps things simple
    - Modify the onboarding preview to call the `OnboardingScreen`
        - Assigning `onContinueClicked` to an empty lambda expression means "do nothing", which is perfect for a preview

## Creating a Performan Lazy List
- Currently there are two greetings in a `Column` &rarr; Can it handle thousands of them?
- Change the default list value in the `Greetings` parameters to use another list constructor which allows us to set the list size and fill it with the value contained in its lambda &rarr; Creates 1000 greetings, even the ones that do not fit on the screen
    - Not performant and will probably freeze
- To display a scrollable column we use a `LazyColumn` &rarr; Renders only the visible items on screen, allowing performance gains when rendering a big list
    - Note: `LazyColumn` and `LazyRow` are equivalent to `RecyclerView` in Android Views
    - The `LazyColumn` API provides an `items` element within its scope, where individual item rendering logic is written
    - `LazyColumn` does not recycle its children like RecyclerView &rarr; Emits new Composables as you scroll through it and is still performant (as emitting Composables is relative cheap compared to instantiating Android Views)

## Persisting State
- Problem with the app &rarr; If you run the app on a device, click on the buttons and then rotate, the onboarding screen is shown again
- `remember` function works **only as long as the composable is key in the Composition**
-  When you rotate the screen, the whole activity is restarted so all state is lost &rarr; Also happens with any configuration change and on process death
- Solution
    - Instead of using `remember` you can use `rememberSaveable` &rarr; This will save each state surviving configuration changes (such as rotations) and process death
    - Replace the use of `remember` in `shouldShowOnboarding` with `rememberSaveable`

## Animating Your List
- Compose provides a number of ways to animate the UI
    - High-level APIs for simple animations
    - Low-level methods for full control and complex transitions
    - Documentation: https://developer.android.com/jetpack/compose/animation?authuser=1
- Animate the change in size that has already been implemented
    - Use a low-level API
    - Use the `animateDpAsState` composable &rarr; Returns a State object whose `value` will continuously be updated by the animation until it finishes and takes a "target value" whose type is `Dp`
    - Create an animated `extraPadding` that depends on the expanded state and use the property delegate (the `by` keyword)
    - Note: Expand item number 1, scroll away to number 20 and come back to 1, you will notice that 1 is back to the original size &rarr; You could save this data with `rememberSaveable`
    - `animateDpAsState` takes an optional `animationSpec` parameter that lets you customize the animation &rarr; Add a spring-based animation
    - Note: We are also making sure that padding is never negative, otherwise it could crash the app &rarr; Introduces a subtle animation bug that we will fix later
    - `spring` spec does not take any time-related parameters &rarr; Relies on physical properties (damping and stiffness) to make animations more natural
    - Any animation created with `animate*AsState` is interruptible &rarr; If the target value changes in the middle of the animation, `animate*AsState` restarts the animation and points to the new value
        - Interruptions look especially natural with spring-based animations
- Explore the different types of animations by trying out different parameters for `spring`, different specs (`tween`, `repeatable`), and different functions `animateColorAsState` etc.

## Styling and Theming Your App
- So far we have not styled any of the composables
- Look into what `ComposeBasicsCodelabTheme` and `MaterialTheme` are
- Open `ui/theme/Theme.kt`
    - `ComposeBasicsCodelabTheme` uses `MaterialTheme` in its implementation
- `MaterialTheme`
    - Composable function that reflects the styling principles from the Material design specification
    - Styling information cascades down to the components that are inside its `content`, which may read the information to style themselves
- Already using `ComposeBasicsCodelabTheme` in `MainActivity.kt`
    - `ComposeBasicsCodelabTheme` wraps `MaterialTheme` internally &rarr; `MyApp` is styled with the properties defined in the theme
    - For any descendant composable you can retrieve three properties of `MaterialTheme`: `colorScheme`, `typography`, and `shapes`
- Style the `Text` for the `name`
    - The `Text` composable sets a new `TextStyle`
    - You can create your own `TextStyle` or you can retrieve a theme-defined style by using `MaterialTheme.typography` (preferred)
    - `MaterialTheme.typography` provides access to the Material-defined text styles, such as `displayLarge`, `headlineMedium`, `titleSmall`, `bodyLarge`, `labelMedium`, etc.
- In general it is better to keep your colors, shapes, and font styles inside a `MaterialTheme`
    - E.g., dark mode would be hard to implement if you hard-code colors and would require a lot of error-prone work to fix
    - Sometimes you need to deviate slightly from the selection of colors and font styles &rarr; In these situations, it is better to base your color or style on an existing one &rarr; Modify a predefined style by using the `copy` function (make the number extra bold)
        - If it is necessary to change the font family or any other attribute of `headlineMedium`, you do not have to worry about small deviations

### Set Up a Dark Mode Preview
- Add an additional `@Preview` annotation to `DefaultPreview` with `UI_MODE_NIGHT_YES`

### Tweak Your App's Theme
- Everything related to the current them can be found in the files inside the `ui/theme` folder
    - E.g., the default colors that we have been using so far are defined in `Color.kt`
- Define new colors in `Color.kt` and assign the new colors to the `MaterialTheme`'s palette in `Theme.kt`
    - This will not cause the preview colors to change because, by default, the Preview uses the dynamic colors &rarr; See the logic for adding dynamic coloring in `Theme.kt` using the `dynamicColor` boolean parameter
    - Non-adaptive version of the color sceheme will occur on devices running API level 30 and below
- In `Theme.kt`, define the palette for dark colors
    - For some reason this is not reflecting when we run the app

## Finishing Touches

### Replace Button with an Icon
- Use the `IconButton` composable together with a child `Icon`
- Use `Icons.Filled.ExpandLess` and `Icons.Filled.ExpandMore`, which are available in the `material-icons-extended` artifact
- Add the following line to dependencies in the `app/build.gradle` file: `implementation "androidx.compose.material:material-icons-extended:$compose_version"`
- Modify padding to fix alignment
- Add a content description for accessibility

### Use String Resources - Done
- Content description for "Show more" and "Show less" should be present and can be added with a simple `if` statement &rarr; Hard-coded strings are a bad practice and you should get strings from the `strings.xml` file
    - Use "Extract string resource" on each string

### Showing More - Done
- "Composem ipsum" text appears and disappears, triggering a change in size of each card
    - Add a new `Text` to the Column inside `Greeting` that is displayed when the item is expanded
    - Remove the `extraPadding` and instead apply the `animateContentSize` modifier to the `Row` &rarr; Will automate the process of creating the animation, which would be hard to do manually. Also, it removes the need to `coerceAtLeast`

### Add elevation and shapes
- Can use the `shadow` modifier together with the `clip` modifier to achieve the card look
- A better way is to use the Material composable `Card`
    - The `Card`'s colors can be changed by calling `CardDefault.cardColors` and overriding the color you want to change